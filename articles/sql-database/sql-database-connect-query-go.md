---
title: Go használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs
description: Hozzon létre a Go használatával egy programot, amely csatlakozik egy Azure SQL Database-adatbázishoz, majd Transact-SQL-utasítások használatával kérdezze le és módosítsa az adatokat.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: go
ms.topic: quickstart
author: David-Engel
ms.author: v-daveng
ms.reviewer: MightyPen
manager: craigg
ms.date: 12/07/2018
ms.openlocfilehash: 34b3ee54c48040eaa6f7b7569921678869baa84b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53092366"
---
# <a name="quickstart-use-go-to-query-an-azure-sql-database"></a>Rövid útmutató: Go használata Azure SQL Database-adatbázis lekérdezéséhez

Ez a rövid útmutató használatát mutatja be a [Go](https://godoc.org/github.com/denisenkom/go-mssqldb) programozási nyelv, az Azure SQL Database-adatbázishoz csatlakozhat, és futtasson Transact-SQL-utasításokat az adatok lekérdezéséhez és módosításához. [Nyissa meg](https://golang.org/) egy nyílt forráskódú programozási nyelv, amely megkönnyíti az egyszerű, megbízható és hatékony építhet ki.  

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- A [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal-firewall.md) a számítógép nyilvános IP-cím beállítva.

- A go és a telepített operációs rendszerhez kapcsolódó szoftvereket:

    - **MacOS**: Telepítse a Homebrew-t és a GoLangot. Lásd az [1.2. lépést](https://www.microsoft.com/sql-server/developer-get-started/go/mac/).
    - **Ubuntu**: Telepítse a GoLangot. Lásd az [1.2. lépést](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/).
    - **Windows**: Telepítse a GoLangot. Lásd az [1.2. lépést](https://www.microsoft.com/sql-server/developer-get-started/go/windows/).    

## <a name="sql-server-connection-information"></a>Az SQL-kiszolgáló kapcsolatadatai

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-go-project-and-dependencies"></a>Go-projekt és függőségek létrehozása

1. A terminálból hozza létre az **SqlServerSample** nevű új projektmappát. 

   ```bash
   mkdir SqlServerSample
   ```

2. Módosítsa a könyvtárat a **SqlServerSample** , és telepítse az SQL Server illesztőprogramját a Góhoz készült.

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   go install github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>Mintaadatok létrehozása

1. Tetszőleges szövegszerkesztőben hozzon létre egy nevű fájlt **CreateTestData.sql** a a **SqlServerSample** mappát. A fájlban másolás és beillesztés, az alábbi T-SQL kód, amely létrehoz egy sémát, tábla, és beszúr néhány sort.

   ```sql
   CREATE SCHEMA TestSchema;
   GO

   CREATE TABLE TestSchema.Employees (
     Id       INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
     Name     NVARCHAR(50),
     Location NVARCHAR(50)
   );
   GO

   INSERT INTO TestSchema.Employees (Name, Location) VALUES
     (N'Jared',  N'Australia'),
     (N'Nikita', N'India'),
     (N'Tom',    N'Germany');
   GO

   SELECT * FROM TestSchema.Employees;
   GO
   ```

2. Használat `sqlcmd` kapcsolódni az adatbázishoz, és az újonnan létrehozott SQL-szkript futtatásához. Helyettesítse be a kiszolgáló és az adatbázis megfelelő adatait, valamint a felhasználónevet és a jelszót.

   ```bash
   sqlcmd -S your_server.database.windows.net -U your_username -P your_password -d your_database -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-sql-database"></a>Kód beszúrása SQL-adatbázis lekérdezéséhez

1. Hozzon létre egy **sample.go** nevű fájlt az **SqlServerSample** mappában.

2. Nyissa meg a fájlt, és illessze be az alábbi kódot. Adja meg a kiszolgáló és az adatbázis megfelelő adatait, valamint a felhasználónevet és a jelszót. Ez a példa a GoLang Context metódusainak használatával győződjön meg arról, hogy az adatbázis-kiszolgáló aktív kapcsolat van.

   ```go
   package main

   import (
       _ "github.com/denisenkom/go-mssqldb"
       "database/sql"
       "context"
       "log"
       "fmt"
       "errors"
   )

   var db *sql.DB

   var server = "your_server.database.windows.net"
   var port = 1433
   var user = "your_username"
   var password = "your_password"
   var database = "your_database"

   func main() {
       // Build connection string
       connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s;",
           server, user, password, port, database)

       var err error

       // Create connection pool
       db, err = sql.Open("sqlserver", connString)
       if err != nil {
           log.Fatal("Error creating connection pool: ", err.Error())
       }
       ctx := context.Background()
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal(err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createID, err := CreateEmployee("Jake", "United States")
       if err != nil {
           log.Fatal("Error creating Employee: ", err.Error())
       }
       fmt.Printf("Inserted ID: %d successfully.\n", createID)

       // Read employees
       count, err := ReadEmployees()
       if err != nil {
           log.Fatal("Error reading Employees: ", err.Error())
       }
       fmt.Printf("Read %d row(s) successfully.\n", count)

       // Update from database
       updatedRows, err := UpdateEmployee("Jake", "Poland")
       if err != nil {
           log.Fatal("Error updating Employee: ", err.Error())
       }
       fmt.Printf("Updated %d row(s) successfully.\n", updatedRows)

       // Delete from database
       deletedRows, err := DeleteEmployee("Jake")
       if err != nil {
           log.Fatal("Error deleting Employee: ", err.Error())
       }
       fmt.Printf("Deleted %d row(s) successfully.\n", deletedRows)
   }

   // CreateEmployee inserts an employee record
   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           err = errors.New("CreateEmployee: db is null")
           return -1, err
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := "INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name, @Location); select convert(bigint, SCOPE_IDENTITY());"

       stmt, err := db.Prepare(tsql)
       if err != nil {
          return -1, err
       }
       defer stmt.Close()

       row := stmt.QueryRowContext(
           ctx,
           sql.Named("Name", name),
           sql.Named("Location", location))
       var newID int64
       err = row.Scan(&newID)
       if err != nil {
           return -1, err
       }

       return newID, nil
   }

   // ReadEmployees reads all employee records
   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           return -1, err
       }

       defer rows.Close()

       var count int

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // UpdateEmployee updates an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name = @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }

   // DeleteEmployee deletes an employee from the database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name = @Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }
   ```

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa a következő parancsot.

   ```bash
   go run sample.go
   ```

2. A kimenet ellenőrzése

   ```text
   Connected!
   Inserted ID: 4 successfully.
   ID: 1, Name: Jared, Location: Australia
   ID: 2, Name: Nikita, Location: India
   ID: 3, Name: Tom, Location: Germany
   ID: 4, Name: Jake, Location: United States
   Read 4 row(s) successfully.
   Updated 1 row(s) successfully.
   Deleted 1 row(s) successfully.
   ```

## <a name="next-steps"></a>További lépések

- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [Go illesztő a Microsoft SQL Server](https://github.com/denisenkom/go-mssqldb)
- [Problémák jelentése és kérdezés](https://github.com/denisenkom/go-mssqldb/issues)

