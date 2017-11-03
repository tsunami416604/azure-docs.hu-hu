---
title: "Csatlakozás a MySQL-hez készült Azure-adatbázishoz a Go használatával | Microsoft Docs"
description: "Ez a rövid útmutató több Go-mintakódot biztosít, amelyekkel csatlakozhat a MySQL-hez készült Azure-adatbázishoz, illetve adatokat kérdezhet le róla."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.custom: mvc
ms.devlang: go
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 1f18a35a3c22ecdc379bdffa1ecacb931c62a59d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-go-language-to-connect-and-query-data"></a>MySQL-hez készült Azure-adatbázis: Csatlakozás és adatok lekérdezése a Go használatával
A gyors üzembe helyezés bemutatja, hogyan kell csatlakoztatni egy Azure-adatbázis a MySQL a Windows, az Ubuntu Linux és az Apple macOS platformok írt kód használatával a [Ugrás](https://golang.org/) nyelv. Azt is bemutatja, hogyan lehet SQL-utasítások használatával adatokat lekérdezni, beszúrni, frissíteni és törölni az adatbázisban. Ez a témakör azt feltételezi, hogy ismeri a fejlesztési Ugrás használatával, és, hogy még nem ismeri a MySQL az Azure-adatbázis használata.

## <a name="prerequisites"></a>Előfeltételek
Ebben a rövid útmutatóban a következő útmutatók valamelyikében létrehozott erőforrásokat használunk kiindulási pontként:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-go-and-mysql-connector"></a>A Go és a MySQL-összekötő telepítése
Telepítse [lépjen](https://golang.org/doc/install) és a [Ugrás-sql-illesztőprogramját MySQL](https://github.com/go-sql-driver/mysql#installation) a saját számítógépén. Attól függően, hogy a platform kövesse a megfelelő szakaszt:

### <a name="windows"></a>Windows
1. [Töltse le](https://golang.org/dl/) és telepítse a Microsoft Windowshoz készült Go-t a [telepítési utasítások](https://golang.org/doc/install) szerint.
2. Nyissa meg a parancssort a Start menüből.
3. Hozzon létre egy mappát a projekt számára, például `mkdir  %USERPROFILE%\go\src\mysqlgo`.
4. Nyissa meg a projektmappát (például `cd %USERPROFILE%\go\src\mysqlgo`).
5. Úgy állítsa be a GOPATH környezeti változóját, hogy a forráskód könyvtárára mutasson. `set GOPATH=%USERPROFILE%\go`.
6. Telepítse a [go-sql-driver for mysql](https://github.com/go-sql-driver/mysql#installation) illesztőt a `go get github.com/go-sql-driver/mysql` parancs futtatásával.

   Összefoglalva, telepítse a Go-t, majd futtassa ezeket a parancsokat a parancssorban:
   ```cmd
   mkdir  %USERPROFILE%\go\src\mysqlgo
   cd %USERPROFILE%\go\src\mysqlgo
   set GOPATH=%USERPROFILE%\go
   go get github.com/go-sql-driver/mysql
   ```

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Indítsa el a Bash felületet. 
2. Telepítse a Go-t a `sudo apt-get install golang-go` parancs futtatásával.
3. Hozzon létre egy mappát a projekt számára a kezdőkönyvtárban (például `mkdir -p ~/go/src/mysqlgo/`).
4. Nyissa meg a projektmappát (például `cd ~/go/src/mysqlgo/`).
5. Úgy állítsa be a GOPATH környezeti változót, hogy egy érvényes forráskönyvtárra mutasson, például az aktuális kezdőkönyvtár Go mappájára. A rendszerhéjakba futni `export GOPATH=~/go` nyissa meg a könyvtár hozzáadja a GOPATH rendszerhéj aktuális munkamenet.
6. Telepítse a [go-sql-driver for mysql](https://github.com/go-sql-driver/mysql#installation) illesztőt a `go get github.com/go-sql-driver/mysql` parancs futtatásával.

   Összefoglalva, futtassa ezeket a bash-parancsokat:
   ```bash
   sudo apt-get install golang-go
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

### <a name="apple-macos"></a>Apple macOS
1. Töltse le és nyissa meg a következők szerint telepítse a [telepítési utasításokat](https://golang.org/doc/install) a platformhoz megfelelő. 
2. Indítsa el a Bash felületet.
3. Hozzon létre egy mappát a projekt számára a kezdőkönyvtárban (például `mkdir -p ~/go/src/mysqlgo/`).
4. Nyissa meg a projektmappát (például `cd ~/go/src/mysqlgo/`).
5. Úgy állítsa be a GOPATH környezeti változót, hogy egy érvényes forráskönyvtárra mutasson, például az aktuális kezdőkönyvtár Go mappájára. A rendszerhéjakba futni `export GOPATH=~/go` nyissa meg a könyvtár hozzáadja a GOPATH rendszerhéj aktuális munkamenet.
6. Telepítse a [go-sql-driver for mysql](https://github.com/go-sql-driver/mysql#installation) illesztőt a `go get github.com/go-sql-driver/mysql` parancs futtatásával.

   Összefoglalva, telepítse a Go-t, majd futtassa ezeket a bash-parancsokat:
   ```bash
   mkdir -p ~/go/src/mysqlgo/
   cd ~/go/src/mysqlgo/
   export GOPATH=~/go/
   go get github.com/go-sql-driver/mysql
   ```

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Ehhez szükség lesz a teljes kiszolgálónévre és bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Az Azure-portálon a bal oldali menüben kattintson a **összes erőforrás** majd keresse meg a kiszolgáló rendelkezik gyűrött (például **myserver4demo**).
3. Kattintson a **myserver4demo** kiszolgálónévre.
4. Válassza ki a kiszolgálót **tulajdonságok** lapon, majd jegyezze fel a **kiszolgálónév** és **kiszolgálói rendszergazda bejelentkezési név**.
 ![MySQL-hez készült Azure-adatbázis – Kiszolgáló-rendszergazdai bejelentkezés](./media/connect-go/1_server-properties-name-login.png)
5. Ha elfelejti a kiszolgálói bejelentkezési adatok, navigáljon a **áttekintése** lapon megtekintéséhez a rendszergazdai bejelentkezési nevet, és ha szükséges a jelszó alaphelyzetbe állítása.
   

## <a name="build-and-run-go-code"></a>Go kód felépítése és futtatása 
1. Golang-kód írásához használhat egy egyszerű szövegszerkesztőt, ilyen például Microsoft Windows rendszeren a Jegyzettömb, Ubuntu rendszeren a [vi](http://manpages.ubuntu.com/manpages/xenial/man1/nvi.1.html#contenttoc5) vagy a [Nano](https://www.nano-editor.org/), macOS rendszeren pedig a TextEdit. Ha inkább egy gazdagabb interaktív fejlesztési környezeti (IDE), próbálja [Gogland](https://www.jetbrains.com/go/) által Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) Microsoft vagy [Atom](https://atom.io/).
2. Illessze be a Go kódot az alábbi szakaszok a szövegfájlok, és mentse azokat a projekt mappába kiterjesztésű \*.go (például a Windows útvonalhoz `%USERPROFILE%\go\src\mysqlgo\createtable.go` vagy Linux elérési `~/go/src/mysqlgo/createtable.go`).
3. Keresse meg a `HOST`, `DATABASE`, `USER`, és `PASSWORD` állandók a kódot, majd cserélje le a példában értékeket a saját értékekkel. 
4. Futtassa a parancssorból vagy a Bash rendszerhéjat. Lépjen a projektmappára. Windows rendszer például a következővel: `cd %USERPROFILE%\go\src\mysqlgo\`. Linuxon: `cd ~/go/src/mysqlgo/`.  A fentiekben említettek közül egyes IDE-szerkesztők hibakeresési és futásidejű képességeket biztosítanak anélkül, hogy rendszerhéjparancsokra lenne szükség.
5. Futtassa a kódot a `go run createtable.go` parancs beírásával az alkalmazás fordításához és futtatásához. 
6. Vagy a kód natív alkalmazásba való beépítéséhez írja be a `go build createtable.go` parancsot, majd indítsa el a `createtable.exe` fájlt az alkalmazás futtatásához.

## <a name="connect-create-table-and-insert-data"></a>Csatlakozás, táblák létrehozása és adatok beszúrása
A következő kód segítségével csatlakozzon a kiszolgálóhoz, hozzon létre egy táblát, és az adatok betöltése a egy **BESZÚRÁSA** SQL-utasításban. 

A kód három csomagot importál: az [sql package](https://golang.org/pkg/database/sql/) és a [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) csomagot illesztőként a MySQL-hez készült Azure adatbázissal való kommunikációhoz, illetve az [fmt package](https://golang.org/pkg/fmt/) csomagot a nyomtatott bemenetekhez és kimenetekhez a parancssoron.

A kód metódus meghívja [sql. Open()](http://go-database-sql.org/accessing.html) adatbázishoz való kapcsolódáshoz Azure MySQL, és ellenőrzi a kapcsolati módszer használatával [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A kód többször is meghívja az [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) metódust, hogy különböző DDL-parancsokat futtasson. A kódot is használ [Prepare()](http://go-database-sql.org/prepared.html) és Exec() három sor beszúrása eltérő paraméterekkel rendelkező előkészített utasítás futtatásához. Minden alkalommal, amikor, egy egyéni checkError() metódus ellenőrzése, ha hiba történt, és kilép vészhelyzeti használatos.

Cserélje le a `host`, `database`, `user` és `password` állandókat a saját értékeire. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "myserver4demo.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@myserver4demo"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed).")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table.")

    // Insert some data into table.
    sqlStatement, err := db.Prepare("INSERT INTO inventory (name, quantity) VALUES (?, ?);")
    res, err := sqlStatement.Exec("banana", 150)
    checkError(err)
    rowCount, err := res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("orange", 154)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)

    res, err = sqlStatement.Exec("apple", 100)
    checkError(err)
    rowCount, err = res.RowsAffected()
    fmt.Printf("Inserted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}

```

## <a name="read-data"></a>Adatok olvasása
Az alábbi kód segítségével csatlakozzon, és az adatok olvasása a egy **válasszon** SQL-utasításban. 

A kód három csomagot importál: az [sql package](https://golang.org/pkg/database/sql/) és a [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) csomagot illesztőként a MySQL-hez készült Azure adatbázissal való kommunikációhoz, illetve az [fmt package](https://golang.org/pkg/fmt/) csomagot a nyomtatott bemenetekhez és kimenetekhez a parancssoron.

A kód meghívja az [sql.Open()](http://go-database-sql.org/accessing.html) metódust a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz, majd a [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) metódussal ellenőrzi a kapcsolatot. A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A kód meghívja a [Query()](https://golang.org/pkg/database/sql/#DB.Query) metódust a kiválasztott parancs futtatásához. Ezután a [Next()](https://golang.org/pkg/database/sql/#Rows.Next) metódust futtatja az eredmények közötti iterációhoz, a [Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) használatával elemzi az oszlopértékeket, és menti az értéket a változókba. A rendszer minden alkalommal egyéni checkError() metódust használ a hibák ellenőrzéséhez és a kilépéshez.

Cserélje le a `host`, `database`, `user` és `password` állandókat a saját értékeire. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "myserver4demo.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@myserver4demo"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Variables for printing column data when scanned.
    var (
        id       int
        name     string
        quantity int
    )

    // Read some data from the table.
    rows, err := db.Query("SELECT id, name, quantity from inventory;")
    checkError(err)
    defer rows.Close()
    fmt.Println("Reading data:")
    for rows.Next() {
        err := rows.Scan(&id, &name, &quantity)
        checkError(err)
        fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
    }
    err = rows.Err()
    checkError(err)
    fmt.Println("Done.")
}
```

## <a name="update-data"></a>Adatok frissítése
A következő kód használatával csatlakozhat, és frissítheti az adatokat az **UPDATE** SQL-utasítással. 

A kód három csomagot importál: az [sql package](https://golang.org/pkg/database/sql/) és a [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) csomagot illesztőként a MySQL-hez készült Azure adatbázissal való kommunikációhoz, illetve az [fmt package](https://golang.org/pkg/fmt/) csomagot a nyomtatott bemenetekhez és kimenetekhez a parancssoron.

A kód meghívja az [sql.Open()](http://go-database-sql.org/accessing.html) metódust a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz, majd a [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) metódussal ellenőrzi a kapcsolatot. A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A kód meghívja az [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) metódust a frissítési parancs futtatásához. A rendszer minden alkalommal egyéni checkError() metódust használ a hibák ellenőrzéséhez és a kilépéshez.

Cserélje le a `host`, `database`, `user` és `password` állandókat a saját értékeire. 

```Go
package main

import (
    "database/sql"
    "fmt"

    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "myserver4demo.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@myserver4demo"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("UPDATE inventory SET quantity = ? WHERE name = ?", 200, "banana")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Deleted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
```

## <a name="delete-data"></a>Adat törlése
A következő kód használatával csatlakozhat, és eltávolíthatja az adatokat a **DELETE** SQL-utasítással. 

A kód három csomagot importál: az [sql package](https://golang.org/pkg/database/sql/) és a [go sql driver for mysql](https://github.com/go-sql-driver/mysql#installation) csomagot illesztőként a MySQL-hez készült Azure adatbázissal való kommunikációhoz, illetve az [fmt package](https://golang.org/pkg/fmt/) csomagot a nyomtatott bemenetekhez és kimenetekhez a parancssoron.

A kód meghívja az [sql.Open()](http://go-database-sql.org/accessing.html) metódust a MySQL-hez készült Azure-adatbázishoz való csatlakozáshoz, majd a [db.Ping()](https://golang.org/pkg/database/sql/#DB.Ping) metódussal ellenőrzi a kapcsolatot. A rendszer a folyamat során [adatbázis-leírót](https://golang.org/pkg/database/sql/#DB) használ, amely az adatbázis-kiszolgáló kapcsolatkészletét tárolja. A kód meghívja az [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) metódust a törlési parancs futtatásához. A rendszer minden alkalommal egyéni checkError() metódust használ a hibák ellenőrzéséhez és a kilépéshez.

Cserélje le a `host`, `database`, `user` és `password` állandókat a saját értékeire. 

```Go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/go-sql-driver/mysql"
)

const (
    host     = "myserver4demo.mysql.database.azure.com"
    database = "quickstartdb"
    user     = "myadmin@myserver4demo"
    password = "yourpassword"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true", user, password, host, database)

    // Initialize connection object.
    db, err := sql.Open("mysql", connectionString)
    checkError(err)
    defer db.Close()

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database.")

    // Modify some data in table.
    rows, err := db.Exec("DELETE FROM inventory WHERE name = ?", "orange")
    checkError(err)
    rowCount, err := rows.RowsAffected()
    fmt.Printf("Deleted %d row(s) of data.\n", rowCount)
    fmt.Println("Done.")
}
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./concepts-migrate-import-export.md)
