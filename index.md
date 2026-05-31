---
layout: default
title: Home
---

<style>
.page-content .wrapper {
  max-width: min(75vw, 1600px);
}

.home-layout {
  display: grid;
  grid-template-columns: minmax(360px, 38%) minmax(0, 1fr);
  gap: 2rem;
  align-items: start;
}

.home-toc {
  position: sticky;
  top: 1rem;
  padding-right: 1rem;
  border-right: 1px solid #e8e8e8;
}

.home-toc ul {
  margin-left: 1rem;
  padding-left: 0;
}

.home-toc li {
  margin-bottom: 0.35rem;
  white-space: nowrap;
}

.home-bio {
  min-width: 0;
}

@media (max-width: 900px) {
  .page-content .wrapper {
    max-width: 94vw;
  }

  .home-layout {
    grid-template-columns: 1fr;
  }

  .home-toc {
    position: static;
    border-right: 0;
    border-bottom: 1px solid #e8e8e8;
    padding-right: 0;
    padding-bottom: 1rem;
    margin-bottom: 1rem;
  }

  .home-toc li {
    white-space: normal;
  }
}
</style>

<div class="home-layout" markdown="1">
<div class="home-toc" markdown="1">

### Posts

{% assign ordered_posts = site.posts | reverse %}
{% for post in ordered_posts %}
- [{{ post.title }}]({{ post.url | relative_url }}) ({{ post.date | date: "%Y-%m-%d" }})
{% endfor %}

</div>

<div class="home-bio" markdown="1">

Hi, I'm [Stephen](mailto:stephen+blog@slmcmahon.com), a software architect, programmer, and pragmatic enterprise problem solver. For more than three decades, I've built and sustained software systems through every wave of change in corporate IT, from legacy platforms to modern cloud-native architectures. My foundation is Microsoft .NET, but I also enjoy working in Python and Go when the problem calls for it.

Today, my focus is helping my organization shift to AI-first software delivery and sharing what I learn with the casual readers of my content. I strive to design spec-driven development workflows where precise specifications, token discipline, and clean context are treated as first-class engineering assets. I see generative AI as a precision engine, not a shortcut: when architecture and intent are explicit, AI becomes a high-velocity execution partner that can scale output without scaling technical debt.

My core philosophy is simple: the spec is the code. If we can't explain architecture clearly enough for an AI to execute, we probably don't understand it well enough ourselves. Tools, languages, and frameworks matter, but durable systems are built on deterministic design, strong observability, and robust telemetry.

Beyond coding, I'm deeply interested in philosophy, especially Eastern thought, and how it shapes perspective on life, work, and relationships. I use Gemini as a sounding board for technical ideas and broader questions alike. I don't claim to have all the answers, but I care deeply about learning, teaching, and sharing what works.

Originally from East Texas and now based in Bogota, Colombia, I spend my time solving technical puzzles, simplifying complex systems, and helping teams build better software with clarity and intent.

You can also find me on [LinkedIn](https://www.linkedin.com/in/slmcmahon/) and [GitHub](https://github.com/slmcmahon). 

---

### Some of my favorite internet resources:

- [Farnam Street Blog](https://fs.blog/blog) - There are few places on the internet where i have spent more time than fs.blog. If you want to take a trip down a rabbit hole of intellectual stimulation, then this is a good starting point. Some of my favorites from there are:
  - [Mastering Success: Navigating Within Your Circle of Competence](https://fs.blog/circle-of-competence/)
  - [Turning Pro: The Difference Between Amateurs and Professionals](https://fs.blog/amateurs-professionals/)
  - [Mental Models: The Best Way to Make Intelligent Decisions](https://fs.blog/mental-models/)
- [Youtube: Intellectual Deep Web](https://www.youtube.com/@IntellectualDeepWeb) - Lectures by Some of the World's Greatest Minds.
- [Youtube: Academy of Ideas](https://www.youtube.com/@academyofideas) - Free Minds for a Free Society
- [Youtube: Einzelgänger](https://www.youtube.com/@Einzelg%C3%A4nger) - Einzelgänger explores a wide range of topics and ideas, with a dose of "as I see it," out of curiosity and creative urge.
- [Youtube: Kurzgesagt – In a Nutshell](https://www.youtube.com/@kurzgesagt) - Animation videos explaining things with optimistic nihilism since 12,013.
- [Morning Dew by Alvin Ashcraft](https://www.alvinashcraft.com/) - This is a great site for a daily dose of technical content. I subscribe to the [RSS feed](https://www.alvinashcraft.com/feed/) via the [Vienna](https://www.vienna-rss.com/) RSS reader and I can honestly say that I have never experienced a day where I did not find something new or interesting here.

</div>
</div>
