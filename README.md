# About

GoSQLite3 provides both low-level and high-level wrappers for accessing the SQLite3 database from Go.
It started as simple extension of the API defined by Russ Cox in his wrapper which has since been
extended with a number of high-level conveniences.


## Installation

We support goinstall which can be invoked using the following command-line:

	goinstall -dashboard=true github.com/kuroneko/gosqlite3


## Usage

Example:

    package main

    import(
      "github.com/kuroneko/gosqlite3"
      "log"
      "fmt"
    )

    func main(){
      // Define the schema.
      tables := map[string]sqlite3.Table{
        "dishes" : sqlite3.Table{ "dishes", "id INTEGER PRIMARY KEY, text VARCHAR(56)" },
        "ingredients" : sqlite3.Table{ "ingredients", "id INTEGER PRIMARY KEY, text VARCHAR(56)" },
      }

      // open the database or create if it doesn't exist.
      db, err := sqlite3.Open("test.db")
      if err != nil{ log.Panic(err) }
      setupTables(db, tables)
      seedDishes(db)

      // Show the content of the dishes table.
      db.Execute("SELECT * from dishes", func(st *sqlite3.Statement, values ...interface{}) {
        log.Printf("%v: %v, %v: %v\n", sqlite3.ResultColumn(0).Name(st), sqlite3.ResultColumn(0).Value(st), st.ColumnName(1), st.Column(1))
      })

    }


    func setupTables(db *sqlite3.Database, tables map[string]sqlite3.Table) {
      for key, table := range tables {
        fmt.Printf("Recreating table: %s\n", key)
        table.Drop(db)
        table.Create(db)
        if c, _ := table.Rows(db); c != 0 {
          log.Fatalf("%v already contains data", table.Name)
        }
      }
    }

    func seedDishes(db *sqlite3.Database){
      db.Execute("INSERT INTO dishes values (NULL, 'Tartiflette')")
      db.Execute("INSERT INTO dishes values (NULL, 'Coq au Vin')")
    }


Please read the tests for examples of how to perform queries and handle results.
Documentation will follow in due course.


## License

TBD


## Contributors

Chris Collins
Eleanor McHugh
