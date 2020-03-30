---
title: Az Ugrás lekérdezéshez használata
description: Hozzon létre a Go használatával egy programot, amely csatlakozik egy Azure SQL-adatbázishoz, majd Transact-SQL-utasítások használatával kérdezze le és módosítsa az adatokat.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: go
ms.topic: quickstart
author: David-Engel
ms.author: craigg
ms.reviewer: MightyPen
ms.date: 02/12/2019
ms.openlocfilehash: 9b85b1bfb8935b5e311bb7d9503c17261a210127
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73827085"
---
# <a name="quickstart-use-golang-to-query-an-azure-sql-database"></a>Rövid útmutató: A Golang használatával azure SQL-adatbázis lekérdezése

Ebben a rövid útmutatóban a [Golang](https://godoc.org/github.com/denisenkom/go-mssqldb) programozási nyelvet fogja használni egy Azure SQL-adatbázishoz való csatlakozáshoz. Ezután a Transact-SQL utasításokat futtatva lekérdezi és módosítja az adatokat. [A Golang](https://golang.org/) egy nyílt forráskódú programozási nyelv, amely megkönnyíti az egyszerű, megbízható és hatékony szoftverek készítését.  

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Azure SQL-adatbázis. Az alábbi rövid útmutatók egyikével hozhat létre, majd konfigurálhat egy adatbázist az Azure SQL Database-ben:

  || Önálló adatbázis | Felügyelt példány |
  |:--- |:--- |:---|
  | Létrehozás| [Portál](sql-database-single-database-get-started.md) | [Portál](sql-database-managed-instance-get-started.md) |
  || [parancssori felület](scripts/sql-database-create-and-configure-database-cli.md) | [parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [Powershell](scripts/sql-database-create-and-configure-database-powershell.md) | [Powershell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Konfigurálás | [Kiszolgálószintű IP-tűzfal szabály](sql-database-server-level-firewall-rule.md)| [Kapcsolódás virtuális gépről](sql-database-managed-instance-configure-vm.md)|
  |||[Helyszíni kapcsolat](sql-database-managed-instance-configure-p2s.md)
  |Adatok betöltése|A kalandworks betöltve egy rövid útmutató|[Széles világbeli importőrök visszaállítása](sql-database-managed-instance-get-started-restore.md)
  |||Kalandorok visszaállítása vagy importálása [a BACPAC-fájlból](sql-database-import.md) a [GitHubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > A cikkben szereplő parancsfájlok a Kalandorbolt-adatbázis használatára íródnak. Felügyelt példány esetén vagy importálnia kell a Kalandorbolt-adatbázist egy példányadatbázisba, vagy módosítania kell a cikkparancsfájljait a Wide World Importers adatbázis használatához.

- Golang és a kapcsolódó szoftver az operációs rendszer telepítve:

  - **MacOS**: Telepítse a Homebrew és a Golang alkalmazást. Lásd az [1.2. lépést](https://www.microsoft.com/sql-server/developer-get-started/go/mac/).
  - **Ubuntu**: Telepítse golang. Lásd az [1.2. lépést](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/).
  - **Windows**: Telepítse Golang. Lásd az [1.2. lépést](https://www.microsoft.com/sql-server/developer-get-started/go/windows/).

## <a name="get-sql-server-connection-information"></a>SQL-kiszolgálókapcsolati adatok beszerezése

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információk beszerezése. A közelgő eljárásokhoz szüksége lesz a teljesen minősített kiszolgáló- vagy állomásnévre, az adatbázis nevére és bejelentkezési adataira.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Keresse meg az **SQL-adatbázisok** vagy az **SQL felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a **kiszolgáló neve** melletti teljesen minősített kiszolgálónevet egyetlen adatbázishoz, vagy a teljesen minősített kiszolgálónevet a felügyelt példány **gazdagépe** mellett. A kiszolgáló vagy az állomásnév másolásához mutasson rá, és válassza a **Másolás ikont.**

## <a name="create-golang-project-and-dependencies"></a>Golang-projekt és függőségek létrehozása

1. A terminálból hozza létre az **SqlServerSample** nevű új projektmappát. 

   ```bash
   mkdir SqlServerSample
   ```

2. Nyissa meg az **SqlServerSample alkalmazást,** és telepítse az SQL Server illesztőprogramját az Go alkalmazáshoz.

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   go install github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>Mintaadatok létrehozása

1. Szövegszerkesztőben hozzon létre egy **CreateTestData.sql** nevű fájlt az **SqlServerSample** mappában. Illessze be a fájlba ezt a T-SQL kódot, amely sémát, táblát és néhány sort szúr be.

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

2. Az `sqlcmd` adatbázishoz való csatlakozáshoz és az újonnan létrehozott SQL-parancsfájl futtatásához használható. Helyettesítse be a kiszolgáló és az adatbázis megfelelő adatait, valamint a felhasználónevet és a jelszót.

   ```bash
   sqlcmd -S <your_server>.database.windows.net -U <your_username> -P <your_password> -d <your_database> -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-sql-database"></a>Kód beszúrása SQL-adatbázis lekérdezéséhez

1. Hozzon létre egy **sample.go** nevű fájlt az **SqlServerSample** mappában.

2. Illessze be a fájlba ezt a kódot. Adja hozzá a kiszolgáló, az adatbázis, a felhasználónév és a jelszó értékeit. Ez a példa a Golang [környezet metódusok segítségével](https://golang.org/pkg/context/) győződjön meg arról, hogy van egy aktív adatbázis-kiszolgáló kapcsolat.

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

   var server = "<your_server.database.windows.net>"
   var port = 1433
   var user = "<your_username>"
   var password = "<your_password>"
   var database = "<your_database>"

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

2. Ellenőrizze a kimenetet.

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

- [Az első Azure SQL-adatbázis megtervezése](sql-database-design-first-database.md)
- [Golang illesztőprogram a Microsoft SQL Server rendszerhez](https://github.com/denisenkom/go-mssqldb)
- [Problémák jelentése és kérdezés](https://github.com/denisenkom/go-mssqldb/issues)

