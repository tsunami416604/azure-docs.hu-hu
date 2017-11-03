---
title: "Csatlakozás PostgreSQL-hez készült Azure-adatbázishoz a Go nyelv használatával | Microsoft Docs"
description: "Ez a rövid útmutató egy Go programozási nyelven írt mintát biztosít, amellyel csatlakozhat a PostgreSQL-hez készült Azure-adatbázishoz, és adatokat is lekérdezhet róla."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 06/29/2017
ms.openlocfilehash: 1a581752e3803e9c9aba826b23db14a76080b4ec
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2017
---
# <a name="azure-database-for-postgresql-use-go-language-to-connect-and-query-data"></a>PostgreSQL-hez készült Azure-adatbázis: Csatlakozás és adatok lekérdezése a Go nyelv használatával
Ez a rövid útmutató azt ismerteti, hogyan lehet csatlakozni a PostgreSQL-hez készült Azure-adatbázishoz [Go](https://golang.org/) nyelven írt kóddal (golang). Bemutatjuk, hogy az SQL-utasítások használatával hogyan kérdezhetők le, illeszthetők be, frissíthetők és törölhetők az adatok az adatbázisban. A cikk feltételezi, hogy Ön ismeri a Go-t használó fejlesztéseket, de még járatlan a PostgreSQL-hez készült Azure-adatbázis használatában.

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutató az alábbi útmutatók valamelyikében létrehozott erőforrásokat használja kiindulópontként:
- [DB létrehozása – portál](quickstart-create-server-database-portal.md)
- [DB létrehozása – Azure CLI](quickstart-create-server-database-azure-cli.md)

## <a name="install-go-and-pq-connector"></a>A Go és a pq-összekötő telepítése
Telepítse a [Go](https://golang.org/doc/install)-t és a [Pure Go Postgres illesztőprogramot (pq)](https://github.com/lib/pq) a saját számítógépére. Attól függően, hogy a platform hajtsa végre a megfelelő lépéseket:

### <a name="windows"></a>Windows
1. [Töltse le](https://golang.org/dl/) és telepítse a Microsoft Windowshoz készült Go-t a [telepítési utasítások](https://golang.org/doc/install) szerint.
2. Nyissa meg a parancssort a Start menüből.
3. Egy mappa a projekthez, például, hogy a `mkdir  %USERPROFILE%\go\src\postgresqlgo`.
4. Nyissa meg a projektmappát (például `cd %USERPROFILE%\go\src\postgresqlgo`).
5. Úgy állítsa be a GOPATH környezeti változóját, hogy a forráskód könyvtárára mutasson. `set GOPATH=%USERPROFILE%\go`.
6. Telepítse a [Pure Go Postgres illesztőt (pq)](https://github.com/lib/pq) a `go get github.com/lib/pq` parancs futtatásával.

   Összefoglalva, telepítse a Go-t, majd futtassa ezeket a parancsokat a parancssorban:
   ```cmd
   mkdir  %USERPROFILE%\go\src\postgresqlgo
   cd %USERPROFILE%\go\src\postgresqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/lib/pq
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Indítsa el a Bash felületet. 
2. Telepítse a Go-t a `sudo apt-get install golang-go` parancs futtatásával.
3. Hozzon létre egy mappát a projekt számára a kezdőkönyvtárban (például `mkdir -p ~/go/src/postgresqlgo/`).
4. Nyissa meg a projektmappát (például `cd ~/go/src/postgresqlgo/`).
5. Úgy állítsa be a GOPATH környezeti változót, hogy egy érvényes forráskönyvtárra mutasson, például az aktuális kezdőkönyvtár Go mappájára. A bash felületen futtassa az `export GOPATH=~/go` parancsot, amellyel a Go könyvtárát GOPATH útvonalként adhatja meg az aktuális felületi munkamenetre.
6. Telepítse a [Pure Go Postgres illesztőt (pq)](https://github.com/lib/pq) a `go get github.com/lib/pq` parancs futtatásával.

   Összefoglalva, futtassa ezeket a bash-parancsokat:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

### <a name="apple-macos"></a>Apple macOS
1. Töltse le és telepítse a Go-t a platformjának megfelelő [telepítési utasítások](https://golang.org/doc/install) szerint. 
2. Indítsa el a Bash felületet. 
3. Hozzon létre egy mappát a projekt számára a kezdőkönyvtárban (például `mkdir -p ~/go/src/postgresqlgo/`).
4. Nyissa meg a projektmappát (például `cd ~/go/src/postgresqlgo/`).
5. Úgy állítsa be a GOPATH környezeti változót, hogy egy érvényes forráskönyvtárra mutasson, például az aktuális kezdőkönyvtár Go mappájára. A bash felületen futtassa az `export GOPATH=~/go` parancsot, amellyel a Go könyvtárát GOPATH útvonalként adhatja meg az aktuális felületi munkamenetre.
6. Telepítse a [Pure Go Postgres illesztőt (pq)](https://github.com/lib/pq) a `go get github.com/lib/pq` parancs futtatásával.

   Összefoglalva, telepítse a Go-t, majd futtassa ezeket a bash-parancsokat:
   ```bash
   mkdir -p ~/go/src/postgresqlgo/
   cd ~/go/src/postgresqlgo/
   export GOPATH=~/go/
   go get github.com/lib/pq
   ```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a PostgreSQL-hez készült Azure-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. Szüksége lesz a teljes kiszolgálónévre és a bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például **mypgserver-20170401**).
3. Kattintson a **mypgserver-20170401** kiszolgálónévre.
4. Válassza ki a kiszolgáló **Áttekintés** oldalát. Jegyezze fel a **Kiszolgálónevet** és a **Kiszolgáló-rendszergazdai bejelentkezési nevet**.
 ![PostgreSQL-hez készült Azure-adatbázis – Kiszolgáló-rendszergazdai bejelentkezés](./media/connect-go/1-connection-string.png)
5. Amennyiben elfelejtette a kiszolgálója bejelentkezési adatait, lépjen az **Áttekintés** oldalra, és keresse ki a kiszolgáló-rendszergazda bejelentkezési nevét. Szükség esetén kérjen új jelszót.

## <a name="build-and-run-go-code"></a>Go kód felépítése és futtatása 
1. Golang-kód írásához használhat egy egyszerű szövegszerkesztőt, ilyen például Microsoft Windows rendszeren a Jegyzettömb, Ubuntu rendszeren a [vi](http://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) vagy a [Nano](https://www.nano-editor.org/), macOS rendszeren pedig a TextEdit. Ha a funkciógazdagabb interaktív fejlesztési környezeteket (IDE-ket) részesít előnyben, próbálja ki a Jetbrains [Gogland](https://www.jetbrains.com/go/) a Microsoft [Visual Studio Code](https://code.visualstudio.com/) vagy az [Atom](https://atom.io/) eszközt.
2. Az alábbi szakaszokban található Golang-kódokat illessze be szövegfájlokba, és mentse a fájlokat a projektmappába \*.go kiterjesztéssel, például `%USERPROFILE%\go\src\postgresqlgo\createtable.go` (Windows) vagy `~/go/src/postgresqlgo/createtable.go` (Linux) elérési úton.
3. Keresse meg a `HOST`, a `DATABASE`, a `USER` és a `PASSWORD` állandót a kódban, és a példaértékeket cserélje le a saját értékeire.  
4. Nyissa meg a parancssort vagy a bash rendszerhéjat. Lépjen a projektmappára. Windows rendszer például a következővel: `cd %USERPROFILE%\go\src\postgresqlgo\`. Linuxon: `cd ~/go/src/postgresqlgo/`. A fentiekben említettek közül egyes IDE-környezetek hibakeresési és futásidejű képességeket biztosítanak anélkül, hogy rendszerhéjparancsokra lenne szükség.
5. A kód futtatásához írja be a `go run createtable.go` parancsot az alkalmazás lefordításához és futtatásához. 
6. Vagy a kód natív alkalmazásba való beépítéséhez írja be a `go build createtable.go` parancsot, majd indítsa el a `createtable.exe` fájlt az alkalmazás futtatásához.

## <a name="connect-and-create-a-table"></a>Csatlakozás és tábla létrehozása
A következő kód használatával csatlakozhat, és létrehozhat egy táblát a **CREATE TABLE** SQL-utasítással, majd az **INSERT INTO** SQL-utasításokkal sorokat adhat hozzá a táblához.

A kód importálja három csomagot: a [sql csomag](https://golang.org/pkg/database/sql/), a [pq csomag](http://godoc.org/github.com/lib/pq) illesztőprogramként PostgreSQL kiszolgálóval való kommunikációhoz és a [fmt csomag](https://golang.org/pkg/fmt/) nyomtatott bemeneti és a kimeneti a parancssorban.

A kód metódus meghívja [sql. Open()](http://godoc.org/github.com/lib/pq#Open) adatbázishoz való kapcsolódáshoz Azure PostgreSQL-adatbázishoz, és ellenőrzi a kapcsolat metódussal [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A kód többször is meghívja az [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) metódust, hogy különböző SQL-parancsokat futtasson. Minden alkalommal, amikor egy egyéni checkError() metódus ellenőrzi, ha hiba történt, és vészhelyzeti való kilépéshez, ha hiba történik.

Cserélje le a `HOST`, `DATABASE`, `USER` és `PASSWORD` paramétereket a saját értékeire. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

## <a name="read-data"></a>Adatok beolvasása
A következő kóddal csatlakozhat, és beolvashatja az adatokat a **SELECT** SQL-utasítással. 

A kód importálja három csomagot: a [sql csomag](https://golang.org/pkg/database/sql/), a [pq csomag](http://godoc.org/github.com/lib/pq) illesztőprogramként PostgreSQL kiszolgálóval való kommunikációhoz és a [fmt csomag](https://golang.org/pkg/fmt/) nyomtatott bemeneti és a kimeneti a parancssorban.

A kód metódus meghívja [sql. Open()](http://godoc.org/github.com/lib/pq#Open) adatbázishoz való kapcsolódáshoz Azure PostgreSQL-adatbázishoz, és ellenőrzi a kapcsolat metódussal [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A select lekérdezés futtatása metódus meghívásával [db. Query()](https://golang.org/pkg/database/sql/#DB.Query), és az eredményül kapott sor tartják típusú változó [sorok](https://golang.org/pkg/database/sql/#Rows). A kód beolvassa az aktuális sorban található oszlopok adatértékeit a [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) metódussal, majd a [rows.Next()](https://golang.org/pkg/database/sql/#Rows.Next) iterátor használatával ismétlődően fut a sorokon, amíg azok el nem fogynak. Az egyes sorok oszlopértékei megjelennek a konzolon. Minden alkalommal, amikor egy egyéni checkError() módszer segítségével ellenőrizze, ha hiba történt, és vészhelyzeti való kilépéshez, ha hiba történik.

Cserélje le a `HOST`, `DATABASE`, `USER` és `PASSWORD` paramétereket a saját értékeire. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

## <a name="update-data"></a>Adatok frissítése
A következő kódot használja a csatlakozáshoz, és frissíti a használatával végzett egy **frissítése** SQL-utasításban.

A kód három csomagot importál, az [sql package](https://golang.org/pkg/database/sql/) és a [pq package](http://godoc.org/github.com/lib/pq) csomagot illesztőprogramként a Postgres-kiszolgálóval való kommunikációhoz, illetve az [fmt package](https://golang.org/pkg/fmt/) csomagot a nyomtatott bemenetekhez és kimenetekhez a parancssoron.

A kód metódus meghívja [sql. Open()](http://godoc.org/github.com/lib/pq#Open) adatbázishoz való kapcsolódáshoz Azure PostgreSQL-adatbázishoz, és ellenőrzi a kapcsolat metódussal [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A kód meghívja az [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) metódust, hogy futtassa a táblát frissítő SQL-utasítást. Egy egyéni checkError() módszert ellenőrzése, ha hiba történt, és vészhelyzeti való kilépéshez, ha hiba történik.

Cserélje le a `HOST`, `DATABASE`, `USER` és `PASSWORD` paramétereket a saját értékeire. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

## <a name="delete-data"></a>Adat törlése
Az alábbi kód használatával csatlakozhat és végezheti el az adatok törlését egy **DELETE** SQL-utasítás segítségével. 

A kód három csomagot importál, az [sql package](https://golang.org/pkg/database/sql/) és a [pq package](http://godoc.org/github.com/lib/pq) csomagot illesztőprogramként a Postgres-kiszolgálóval való kommunikációhoz, illetve az [fmt package](https://golang.org/pkg/fmt/) csomagot a nyomtatott bemenetekhez és kimenetekhez a parancssoron.

A kód metódus meghívja [sql. Open()](http://godoc.org/github.com/lib/pq#Open) adatbázishoz való kapcsolódáshoz Azure PostgreSQL-adatbázishoz, és ellenőrzi a kapcsolat metódussal [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A kód hívások a [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) módszert az SQL-utasítást, amely egy sor törlése a táblázatból. Egy egyéni checkError() módszer segítségével ellenőrizze, hogy hiba történt, és pánikot való kilépéshez, ha hiba történik.

Cserélje le a `HOST`, `DATABASE`, `USER` és `PASSWORD` paramétereket a saját értékeire. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)
