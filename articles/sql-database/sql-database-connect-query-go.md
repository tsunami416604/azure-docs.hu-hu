---
title: Go használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs
description: Hozzon létre a Go használatával egy programot, amely csatlakozik egy Azure SQL Database-adatbázishoz, majd Transact-SQL-utasítások használatával kérdezze le és módosítsa az adatokat.
services: sql-database
author: David-Engel
manager: craigg
ms.reviewer: MightyPen
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: go
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: v-daveng
ms.openlocfilehash: 3585a47e0823a765bd59b28f4b399aed7c5fcae3
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791069"
---
# <a name="use-go-to-query-an-azure-sql-database"></a>Go használata Azure SQL Database-adatbázis lekérdezéséhez

Ez a rövid útmutató azt ismerteti, hogyan használható a [Go](https://godoc.org/github.com/denisenkom/go-mssqldb) egy Azure SQL Database-adatbázishoz való csatlakozáshoz. Emellett az adatok lekérdezéséhez és módosításához használatos Transact-SQL-utasításokat is bemutatja.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- A rövid útmutatóhoz használt számítógép nyilvános IP-címére vonatkozó [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

- Telepítette a Gót és az operációs rendszerének megfelelő kapcsolódó szoftvereket:

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

2. Lépjen az **SqlServerSample** könyvtárba, majd telepítse a Go SQL Server-illesztőjét.

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   go install github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>Mintaadatok létrehozása

1. Egy szövegszerkesztővel hozzon létre egy **CreateTestData.sql** nevű fájlt, és mentse az **SqlServerSample** mappába. Másolja ki és illessze be a következő T-SQL kódot a létrehozott fájlba. Ez a kód létrehoz egy sémát és egy táblát, és beszúr néhány sort.

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

2. Csatlakozzon az adatbázishoz az sqlcmd használatával, majd futtassa az SQL-szkriptet, hogy létrehozzon egy sémát és egy táblát, és beszúrjon néhány sort. Helyettesítse be a kiszolgáló és az adatbázis megfelelő adatait, valamint a felhasználónevet és a jelszót.

   ```bash
   sqlcmd -S your_server.database.windows.net -U your_username -P your_password -d your_database -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-sql-database"></a>Kód beszúrása SQL-adatbázis lekérdezéséhez

1. Hozzon létre egy **sample.go** nevű fájlt az **SqlServerSample** mappában.

2. Nyissa meg a fájlt, és cserélje le a tartalmát a következő kódra. Adja meg a kiszolgáló és az adatbázis megfelelő adatait, valamint a felhasználónevet és a jelszót. Ez a példa a GoLang Context metódusainak használatával gondoskodik róla, hogy aktív kapcsolat álljon fenn az adatbázis-kiszolgálóval.

   ```go
   package main

   import (
       _ "github.com/denisenkom/go-mssqldb"
       "database/sql"
       "context"
       "log"
       "fmt"
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
           log.Fatal("Error creating connection pool:", err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createId, err := CreateEmployee("Jake", "United States")
       fmt.Printf("Inserted ID: %d successfully.\n", createId)

       // Read employees
       count, err := ReadEmployees()
       fmt.Printf("Read %d rows successfully.\n", count)

       // Update from database
       updateId, err := UpdateEmployee("Jake", "Poland")
       fmt.Printf("Updated row with ID: %d successfully.\n", updateId)

       // Delete from database
       rows, err := DeleteEmployee("Jake")
       fmt.Printf("Deleted %d rows successfully.\n", rows)
   }

   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           log.Fatal("What?")
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name,@Location);")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))

       if err != nil {
           log.Fatal("Error inserting new row: " + err.Error())
           return -1, err
       }

       return result.LastInsertId()
   }

   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           log.Fatal("Error reading rows: " + err.Error())
           return -1, err
       }

       defer rows.Close()

       var count int = 0

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               log.Fatal("Error reading rows: " + err.Error())
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // Update an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name= @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           log.Fatal("Error updating row: " + err.Error())
           return -1, err
       }

       return result.LastInsertId()
   }

   // Delete an employee from database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           log.Fatal("Error pinging database: " + err.Error())
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name=@Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           fmt.Println("Error deleting row: " + err.Error())
           return -1, err
       }

       return result.RowsAffected()
   }
   ```

## <a name="run-the-code"></a>A kód futtatása

1. Futtassa az alábbi parancsokat a parancssorban:

   ```bash
   go run sample.go
   ```

2. Ellenőrizze a kimenetet:

   ```text
   Connected!
   Inserted ID: 4 successfully.
   ID: 1, Name: Jared, Location: Australia
   ID: 2, Name: Nikita, Location: India
   ID: 3, Name: Tom, Location: Germany
   ID: 4, Name: Jake, Location: United States
   Read 4 rows successfully.
   Updated row with ID: 4 successfully.
   Deleted 1 rows successfully.
   ```

## <a name="next-steps"></a>További lépések

- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [Go illesztő a Microsoft SQL Serverhez](https://github.com/denisenkom/go-mssqldb)
- [Problémák jelentése és kérdezés](https://github.com/denisenkom/go-mssqldb/issues)

