---
title: Azure Cosmos DB API a MongoDB és Golang-SDK-Konzolalkalmazás létrehozása
description: Egy használatával csatlakoznak és kérnek a mongodb-hez készült Azure Cosmos DB API használatával Golang-kódmintát mutat be.
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.custom: quickstart
ms.openlocfilehash: 7fb1e547f58f2297378b0c95cdea67182a416e04
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53791608"
---
# <a name="build-a-console-app-using-azure-cosmos-dbs-api-for-mongodb-and-golang-sdk"></a>Azure Cosmos DB API a MongoDB és Golang-SDK-Konzolalkalmazás létrehozása

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Gyors létrehozása és lekérdezése, a dokumentum, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike globális elosztási és horizontális skálázhatósági képességeket Cosmos DB középpontjában.

Ez a rövid útmutató azt ismerteti, hogyan nyelven írt meglévő MongoDB-alkalmazások is [Golang](https://golang.org/) , és csatlakoztassa a Cosmos-adatbázis az Azure Cosmos DB API használatával a mongodb-hez.

Más szóval a Golang-alkalmazás csak tudja, hogy azt csatlakozás MongoDB-ügyfél használatával. Nem látja, az alkalmazást, hogy az adatok egy Cosmos-adatbázis tárolja.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt. 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- [Lépjen a ](https://golang.org/dl/) oldalra a [Go](https://golang.org/) nyelvre vonatkozó általános ismertetőért.
- Egy IDE – [GoLand](https://www.jetbrains.com/go/) jetbrains-től, [Visual Studio Code](https://code.visualstudio.com/) Microsoft, vagy [Atom](https://atom.io/). Ebben az oktatóanyagban GoLand használok.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Klónozza a mintaalkalmazást, és telepítse a szükséges csomagokat.

1. Hozza létre a CosmosDBSample mappát a GOROOT\src mappában, amely alapértelmezés szerint a C:\Go\ mappa.
2. Futtassa a következő parancsot egy git bash git terminálablakot használva a minta-tárház klónozásához a CosmosDBSample mappába. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-golang-getting-started.git
    ```
3.  Futtassa a következő parancsot az mgo csomag lekéréséhez. 

    ```
    go get gopkg.in/mgo.v2
    ```

A [mgo](https://labix.org/mgo) illesztőprogram egy [MongoDB](https://www.mongodb.com/) illesztőprogramját a [Go nyelvhez](https://golang.org/) , amely megvalósítja a funkciók mellett egy nagyon egyszerű API-t a következő standard Go egy hatékony és alaposan tesztelt kiválasztása nyelv szintaxisát.

<a id="connection-string"></a>

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba.

1. Kattintson az **Első lépések** elemre a bal oldali navigációs menüben, majd az **Egyéb** elemre a Go-alkalmazáshoz szükséges kapcsolati sztring adatainak megtekintéséhez.

2. A Goglangban nyissa meg a main.go fájlt a GOROOT\CosmosDBSample könyvtárban, és frissítse a kapcsolati sztring adatait az Azure portálról az alábbi képernyőfelvételen látható módon, az alábbi kódsorok használatával. 

    Az adatbázisnév az Azure-portálon a kapcsolati sztring ablaktábla **Host** (Gazdgép) értékének előtagja. Az alábbi képen látható fiók esetében az adatbázisnév golang-coach.

    ```go
    Database: "The prefix of the Host value in the Azure portal",
    Username: "The Username in the Azure portal",
    Password: "The Password in the Azure portal",
    ```

    ![Az Azure-portál Első lépések ablaktábláján az Egyéb lap megjeleníti a kapcsolati sztring adatait](./media/create-mongodb-golang/cosmos-db-golang-connection-string.png)

3. Mentse a main.go fájlt.

## <a name="review-the-code"></a>A kód áttekintése

Ez a lépés nem kötelező. Ha meg szeretné ismerni, hogyan jönnek létre az adatbázis erőforrásai a kódban, tekintse át a következő kódrészleteket. Egyéb esetben áttérhet [Az alkalmazás futtatása](#run-the-app) szakaszra. 

Az alábbi kódrészletek mind a main.go fájlból származnak.

### <a name="connecting-the-go-app-to-cosmos-db"></a>Az alkalmazás csatlakoztatása a Cosmos DB

Az Azure Cosmos DB MongoDB API-támogatja az SSL-kapcsolatot. Szeretne csatlakozni, meg kell a **DialServer** függvényével [mgo. DialInfo](https://godoc.org/gopkg.in/mgo.v2#DialInfo), és győződjön meg arról, használja a [tls. *Tárcsázás* ](https://golang.org/pkg/crypto/tls#Dial) függvényt a csatlakozás végrehajtására.

A következő Golang kódrészlet a Go-alkalmazást az Azure Cosmos DB API a mongodb-hez csatlakozik. A *DialInfo* osztály munkamenetet beállításokat tartalmaz.

```go
// DialInfo holds options for establishing a session.
dialInfo := &mgo.DialInfo{
    Addrs:    []string{"golang-couch.documents.azure.com:10255"}, // Get HOST + PORT
    Timeout:  60 * time.Second,
    Database: "database", // It can be anything
    Username: "username", // Username
    Password: "Azure database connect password from Azure Portal", // PASSWORD
    DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
        return tls.Dial("tcp", addr.String(), &tls.Config{})
    },
}

// Create a session which maintains a pool of socket connections
// to Cosmos database (using Azure Cosmos DB's API for MongoDB).
session, err := mgo.DialWithInfo(dialInfo)

if err != nil {
    fmt.Printf("Can't connect, go error %v\n", err)
    os.Exit(1)
}

defer session.Close()

// SetSafe changes the session safety mode.
// If the safe parameter is nil, the session is put in unsafe mode, 
// and writes become fire-and-forget,
// without error checking. The unsafe mode is faster since operations won't hold on waiting for a confirmation.
// 
session.SetSafe(&mgo.Safe{})
```

A **mgo.Dial()** metódust akkor használatos, amikor nincs SSL-kapcsolat. Az SSL-kapcsolathoz a **mgo. DialWithInfo()** metódusra van szükség.

A munkamenet-objektum létrehozása a **DialWIthInfo{}** objektum használatával történik. A munkamenet létrehozását követően a következő kódrészlet használatával érhető el a gyűjtemény:

```go
collection := session.DB("database").C("package")
```

<a id="create-document"></a>

### <a name="create-a-document"></a>Dokumentum létrehozása

```go
// Model
type Package struct {
    Id bson.ObjectId  `bson:"_id,omitempty"`
    FullName      string
    Description   string
    StarsCount    int
    ForksCount    int
    LastUpdatedBy string
}

// insert Document in collection
err = collection.Insert(&Package{
    FullName:"react",
    Description:"A framework for building native apps with React.",
    ForksCount: 11392,
    StarsCount:48794,
    LastUpdatedBy:"shergin",

})

if err != nil {
    log.Fatal("Problem inserting data: ", err)
    return
}
```

### <a name="query-or-read-a-document"></a>Dokumentum lekérdezése vagy olvasása

A cosmos DB támogatja az egyes gyűjteményekben tárolt adatok végzett részletes lekérdezéseket. Az alábbi mintakód egy olyan lekérdezést mutat be, amelyet a gyűjteményben található dokumentumokra vonatkozóan futtathat le.

```go
// Get a Document from the collection
result := Package{}
err = collection.Find(bson.M{"fullname": "react"}).One(&result)
if err != nil {
    log.Fatal("Error finding record: ", err)
    return
}

fmt.Println("Description:", result.Description)
```


### <a name="update-a-document"></a>Dokumentum frissítése

```go
// Update a document
updateQuery := bson.M{"_id": result.Id}
change := bson.M{"$set": bson.M{"fullname": "react-native"}}
err = collection.Update(updateQuery, change)
if err != nil {
    log.Fatal("Error updating record: ", err)
    return
}
```

### <a name="delete-a-document"></a>Dokumentum törlése

A cosmos DB támogatja a dokumentumok törlését.

```go
// Delete a document
query := bson.M{"_id": result.Id}
err = collection.Remove(query)
if err != nil {
   log.Fatal("Error deleting record: ", err)
   return
}
```
    
## <a name="run-the-app"></a>Az alkalmazás futtatása

1. A Golang, győződjön meg arról, hogy a GOPATH (alatt elérhető **fájl**, **beállítások**, **Go**, **GOPATH**) változó tartalmazza a gopkg volt helyét Alapértelmezés szerint telepítve van, vagyis userprofile\go könyvtár. 
2. Tegye megjegyzésbe a dokumentumot törlő sorokat (103–107. sor), hogy az alkalmazás futtatása után megtekinthesse a dokumentumot.
3. A Golang, kattintson **futtatása**, és kattintson a **main.go létrehozása és futtatása**.

    Az alkalmazás befejeződik, és megjeleníti a létrehozott dokumentum leírását a [Dokumentum létrehozása](#create-document) területen.
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![A kimenet az alkalmazás látható Golang](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>A dokumentum ellenőrzése az Adatkezelőben

Lépjen vissza az Azure portálon, ha az Adatkezelőben szeretné megjeleníteni a dokumentumot.

1. Kattintson a bal oldali navigációs menü **Adatkezelő (előzetes verzió)** pontjára, bontsa ki a **golang-coach**, **package** csomópontokat, majd kattintson a **Dokumentumok** elemre. A **Dokumentumok** lapon kattintson a \_id-re a dokumentum megjelenítéséhez a jobb oldali ablaktáblán. 

    ![Az újonnan létrehozott dokumentum megjelenítve az Adatkezelőben](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. Ezután dolgozzon a dokumentumban, majd kattintson a **Frissítés** elemre a mentéshez. Törölheti is a dokumentumot, vagy létrehozhat új dokumentumokat és lekérdezéseket is.

## <a name="review-slas-in-the-azure-portal"></a>Az SLA-k áttekintése az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban bemutattuk hogyan hozzon létre egy Cosmos-fiókot, és futtathatja a Golang-alkalmazást. További adatok már importálhat a Cosmos database. 

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
