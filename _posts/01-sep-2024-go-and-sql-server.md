Go - Using MS SQL Server
--

The following are very simple examples of how to read/write data to SQL Server with the Go programming language

### Read

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/denisenkom/go-mssqldb"
    "log"
)

var (
    server   = "your_server.database.windows.net"
    port     = 1433
    user     = "your_username"
    password = "your_password"
    database = "your_database"
)

// Define a struct to represent a record
type Record struct {
    Column1 string
    Column2 string
}

func main() {
    // Build the connection string
    connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s",
        server, user, password, port, database)

    // Open the connection
    db, err := sql.Open("sqlserver", connString)
    if err != nil {
        log.Fatal("Error creating connection pool: ", err.Error())
    }
    defer db.Close()

    // Ping the database to verify the connection
    err = db.Ping()
    if err != nil {
        log.Fatal("Error pinging database: ", err.Error())
    }

    fmt.Println("Successfully connected to the database!")

    // Query the database
    query := "SELECT TOP 10 column1, column2 FROM your_table"
    rows, err := db.Query(query)
    if err != nil {
        log.Fatal("Error running query: ", err.Error())
    }
    defer rows.Close()

    // Iterate through the result set and store the data in a slice of structs
    var records []Record
    for rows.Next() {
        var record Record
        err := rows.Scan(&record.Column1, &record.Column2)
        if err != nil {
            log.Fatal("Error scanning row: ", err.Error())
        }
        records = append(records, record)
    }

    // Check for errors after iterating through the result set
    err = rows.Err()
    if err != nil {
        log.Fatal("Error iterating through rows: ", err.Error())
    }

    // Print the records
    for _, record := range records {
        fmt.Printf("Column1: %s, Column2: %s\n", record.Column1, record.Column2)
    }
}
```

### Write

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/denisenkom/go-mssqldb"
    "log"
)

var (
    server   = "your_server.database.windows.net"
    port     = 1433
    user     = "your_username"
    password = "your_password"
    database = "your_database"
)

// Define a struct to represent a record
type Record struct {
    Column1 string
    Column2 string
}

func main() {
    // Build the connection string
    connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s",
        server, user, password, port, database)

    // Open the connection
    db, err := sql.Open("sqlserver", connString)
    if err != nil {
        log.Fatal("Error creating connection pool: ", err.Error())
    }
    defer db.Close()

    // Ping the database to verify the connection
    err = db.Ping()
    if err != nil {
        log.Fatal("Error pinging database: ", err.Error())
    }

    fmt.Println("Successfully connected to the database!")

    // Create a record to insert
    record := Record{
        Column1: "value1",
        Column2: "value2",
    }

    // Insert data into the database
    insertQuery := "INSERT INTO your_table (column1, column2) VALUES (@p1, @p2)"
    _, err = db.Exec(insertQuery, record.Column1, record.Column2)
    if err != nil {
        log.Fatal("Error executing insert query: ", err.Error())
    }

    fmt.Println("Data inserted successfully!")
}
```