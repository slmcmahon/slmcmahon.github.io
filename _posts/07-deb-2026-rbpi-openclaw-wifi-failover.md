---
layout: post
date: 2024-09-01
title: "Headless Raspberry Pi Wi‑Fi Failover with NetworkManager (with iPhone Hotspot Fallback)"
description: "How to preconfigure multiple Wi‑Fi networks on a headless Raspberry Pi and use NetworkManager autoconnect priorities to fail over to an iPhone hotspot when traveling."
---

# Headless Raspberry Pi Wi‑Fi Failover with NetworkManager (with iPhone Hotspot Fallback)

## Why I did this

I’m traveling soon, and I’m running **OpenClaw** on a **Raspberry Pi**. That’s great because it’s portable — I can bring my automation “home base” with me.

I _could_ leave the Pi plugged in at home, but:

- we sometimes have **power outages**, and
- if it goes down while I’m away, it’s harder to recover quickly.

So my plan is to travel with the Pi. It’s essentially a **headless box**: I plug it into power and connect it to Wi‑Fi, then manage it remotely.

The key requirement is convenience and resilience:

- I want the Pi to automatically connect to my **primary Wi‑Fi** when it’s available.
- When I’m at a new location, I want it to connect to a **travel Wi‑Fi** network.
- If neither is available, I want a reliable fallback to my **iPhone Personal Hotspot**.

This post explains exactly how to do that using **NetworkManager**.

---

## Hardware / environment used

This setup is intentionally simple and common:

- **Raspberry Pi** (modern model with Wi‑Fi; I’m using a Pi capable of running Linux + NetworkManager smoothly)
- **microSD card** (or SSD boot)
- **Power supply**
- Headless (no monitor/keyboard)
- **Raspberry Pi OS / Debian-based Linux**
- Wi‑Fi interface typically named **`wlan0`**

You’ll also want one of these remote-access methods:

- SSH (ideal)
- Tailscale/WireGuard (optional, but great when you move between networks)
- Any remote desktop solution you already trust

---

## The idea: NetworkManager autoconnect priorities

NetworkManager stores each Wi‑Fi as a **connection profile**.

Each profile can have:

- `connection.autoconnect=yes`
- `connection.autoconnect-priority=<number>`

When multiple known networks are available, NetworkManager typically chooses the one with the **highest priority number**.

We’ll create three profiles:

1. **Home Wi‑Fi** (highest priority)
2. **Travel Wi‑Fi** (medium priority)
3. **Phone hotspot** (lowest priority, but enabled)

---

## Step 1 — Confirm NetworkManager is running

```bash
systemctl is-active NetworkManager
```

If you see `active`, you’re good.

Also confirm your Wi‑Fi device name:

```bash
nmcli dev status
```

Look for something like `wlan0`.

---

## Step 2 — Create (or confirm) your Wi‑Fi profiles

If you’ve already connected to a network before, NetworkManager usually already created a profile.

List existing profiles:

```bash
nmcli connection show
```

### Create a new Wi‑Fi profile (even if you’re not currently on-site)

This is useful when you’re preparing for travel.

Example:

```bash
sudo nmcli connection add type wifi ifname wlan0 con-name "TRAVEL_WIFI" ssid "TRAVEL_WIFI"
sudo nmcli connection modify "TRAVEL_WIFI" wifi-sec.key-mgmt wpa-psk wifi-sec.psk "<PASSWORD>"
sudo nmcli connection modify "TRAVEL_WIFI" connection.autoconnect yes
```

If the SSID is hidden:

```bash
sudo nmcli connection modify "TRAVEL_WIFI" 802-11-wireless.hidden yes
```

Do the same for your hotspot profile if needed (use the SSID shown by your phone when the hotspot is enabled).

---

## Step 3 — Set autoconnect priorities

Pick numbers that make sense. Higher wins.

Example:

- Home Wi‑Fi = 30
- Travel Wi‑Fi = 20
- Hotspot = 10

```bash
sudo nmcli con modify "HOME_WIFI" connection.autoconnect yes
sudo nmcli con modify "HOME_WIFI" connection.autoconnect-priority 30

sudo nmcli con modify "TRAVEL_WIFI" connection.autoconnect yes
sudo nmcli con modify "TRAVEL_WIFI" connection.autoconnect-priority 20

sudo nmcli con modify "PHONE_HOTSPOT" connection.autoconnect yes
sudo nmcli con modify "PHONE_HOTSPOT" connection.autoconnect-priority 10
```

Verify:

```bash
nmcli -f NAME,TYPE,AUTOCONNECT,AUTOCONNECT-PRIORITY connection show
```

---

## Step 4 — Test failover safely (headless-friendly)

**Warning:** If you bring down your only active Wi‑Fi on a headless device, you can lock yourself out.

### Option A (recommended): timed rollback safety net

This schedules a reconnection attempt back to your primary network in 2 minutes.

```bash
( sleep 120; nmcli con up "HOME_WIFI" || true ) &
```

### Option B: force a switch to hotspot

1. Turn ON your phone’s hotspot and keep it nearby.
2. On the Pi:

```bash
sudo nmcli con down "HOME_WIFI"
sudo nmcli con up "PHONE_HOTSPOT"
```

Verify what’s active:

```bash
nmcli -t -f ACTIVE,NAME,DEVICE,TYPE con show | grep '^yes'
ip -4 addr show wlan0
```

Quick connectivity test:

```bash
ping -c 2 1.1.1.1
```

### Switch back

```bash
sudo nmcli con up "HOME_WIFI"
```

---

## Troubleshooting tips

- **SSID not found**: the network may be out of range, hidden, or the hotspot isn’t enabled.
- **Wrong password**: re-run the `nmcli connection modify ... wifi-sec.psk` command.
- **Priority doesn’t seem to apply**: confirm autoconnect is enabled and you’re not manually pinning a connection.

Useful commands:

```bash
nmcli -t -f ACTIVE,NAME,DEVICE con show
nmcli dev wifi list
journalctl -u NetworkManager --since "10 min ago"
```

---

## Security considerations (don’t skip this)

A hotspot fallback is convenient, but it changes your threat model: your Raspberry Pi is now routinely joining networks that may be used in crowded places.

### Key risks

- **Weak hotspot password / password sharing**: If someone joins your hotspot, they’re on the same LAN and can probe devices.
- **Local network attacks**: Any device on the hotspot network can attempt port scans and attacks against services listening on your Pi.
- **Auto-join “evil twin” networks**: If you reuse SSIDs (or use common SSID names), a malicious hotspot with the same name could trick devices into connecting.
- **Accidentally exposing admin UIs**: Many tools bind to `0.0.0.0` by default (all interfaces). That’s fine on a private LAN you control — less fine on a travel/hotspot network.

### Practical mitigations

- Use a **strong hotspot password** and don’t share it casually.
- For SSH: **disable password login** and use **SSH keys**.
- Prefer a **VPN overlay** (e.g., Tailscale/WireGuard) so you can manage the Pi securely regardless of what Wi‑Fi it’s on.
- Run a firewall and default to **deny inbound** except what you truly need.
- If you run web dashboards/admin tools, bind them to **localhost** unless there’s a strong reason not to.

---

## Closing thoughts

For travel + headless devices, this setup is a big quality-of-life improvement:

- plug in power
- the Pi picks the best known Wi‑Fi
- and if all else fails, you still have a hotspot lifeline

If you’re running something important (like automation, monitoring, or a personal assistant gateway) on a Raspberry Pi, Wi‑Fi failover is one of the best reliability upgrades you can do in under an hour.
