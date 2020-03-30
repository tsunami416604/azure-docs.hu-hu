---
title: Konzolalkalmazás az Azure Cosmos DB MongoDB és Golang SDK API-jával
description: Bemutatja a Golang-kód minta segítségével csatlakozhat, és lekérdezésaz Azure Cosmos DB API-mongoDB használatával.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: c717a8d5baa57ce780fbbc0d25e67c2509ca86fc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75441954"
---
# <a name="quickstart-build-a-console-app-using-azure-cosmos-dbs-api-for-mongodb-and-golang-sdk"></a>Rövid útmutató: Hozzon létre egy konzolalkalmazást az Azure Cosmos DB MongoDB és Golang SDK API-jával

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Gyorsan hozhat létre és kérdezhet le dokumentumokat, kulcs-érték és gráf-adatbázisokat, amelyek mindegyike a Cosmos DB központi magjában lévő globális terjesztési és horizontális méretezési képességek előnyeit élvezi.

Ez a rövid útmutató bemutatja, hogyan vehet egy [meglévő, Golangban](https://golang.org/) írt MongoDB-alkalmazást, és hogyan csatlakoztathatja azt a Cosmos-adatbázishoz az Azure Cosmos DB MongoDB-hoz való API-jával.

Más szóval a Golang alkalmazás csak azt tudja, hogy mongoDB ügyféllel csatlakozik. Az alkalmazás számára átlátható, hogy az adatok egy Cosmos-adatbázisban vannak tárolva.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) mielőtt elkezdené. 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- [Lépjen a ](https://golang.org/dl/) oldalra a [Go](https://golang.org/) nyelvre vonatkozó általános ismertetőért.
- EGY IDE – [GoLand](https://www.jetbrains.com/go/) mellett Jetbrains, [Látási Műterem Kód](https://code.visualstudio.com/) mellett Mikroszkóp, vagy [Atom](https://atom.io/). Ebben a tutorial, én vagyok a GoLand.

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

Az [mgo](https://labix.org/mgo) driver egy [MongoDB](https://www.mongodb.com/) driver a [Go nyelvhez,](https://golang.org/) amely gazdag és jól tesztelt funkciókat valósít meg egy nagyon egyszerű API alatt a standard Go idiómákat követően.

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

### <a name="connecting-the-go-app-to-cosmos-db"></a>A Go alkalmazás csatlakoztatása a Cosmos DB-hez

Az Azure Cosmos DB MongoDB API-ja támogatja az SSL-kompatibilis kapcsolatot. A csatlakozáshoz meg kell határoznia a **DialServer** függvényt az [mgo-ban. DialInfo](https://godoc.org/gopkg.in/mgo.v2#DialInfo), és használja a [tls.* Tárcsázási* ](https://golang.org/pkg/crypto/tls#Dial) funkció a kapcsolat végrehajtásához.

A következő Golang-kódrészlet összeköti a Go alkalmazást az Azure Cosmos DB MongoDB API-jával. A *DialInfo* osztály tartalmazza a munkamenet létrehozásának lehetőségeit.

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

A Cosmos DB támogatja az egyes gyűjteményekben tárolt adatok gazdag lekérdezéseit. Az alábbi mintakód egy olyan lekérdezést mutat be, amelyet a gyűjteményben található dokumentumokra vonatkozóan futtathat le.

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

Cosmos DB támogatja a dokumentumok törlését.

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

1. A Golang alkalmazásban győződjön meg arról, hogy a GOPATH (a **Fájl,** **Beállítások**, **Go**, **GOPATH**területen érhető el) tartalmazza azt a helyet, ahol a gopkg telepítve volt, ami alapértelmezés szerint USERPROFILE\go. 
2. Tegye megjegyzésbe a dokumentumot törlő sorokat (103–107. sor), hogy az alkalmazás futtatása után megtekinthesse a dokumentumot.
3. A Golang alkalmazásban kattintson a **Futtatás**gombra, majd a **"Build main.go and run" parancsra.**

    Az alkalmazás befejeződik, és megjeleníti a létrehozott dokumentum leírását a [Dokumentum létrehozása](#create-document) területen.
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![Golang az alkalmazás kimenetét mutatja](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>A dokumentum ellenőrzése az Adatkezelőben

Lépjen vissza az Azure portálon, ha az Adatkezelőben szeretné megjeleníteni a dokumentumot.

1. Kattintson a bal oldali navigációs menü **Adatkezelő (előzetes verzió)** pontjára, bontsa ki a **golang-coach**, **package** csomópontokat, majd kattintson a **Dokumentumok** elemre. A **Dokumentumok** lapon kattintson a \_id-re a dokumentum megjelenítéséhez a jobb oldali ablaktáblán. 

    ![Az újonnan létrehozott dokumentum megjelenítve az Adatkezelőben](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. Ezután dolgozzon a dokumentumban, majd kattintson a **Frissítés** elemre a mentéshez. Törölheti is a dokumentumot, vagy létrehozhat új dokumentumokat és lekérdezéseket is.

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Cosmos-fiókot, és hogyan futtathatja a Golang-alkalmazást. Most már importálhat további adatokat a Cosmos-adatbázisba. 

> [!div class="nextstepaction"]
> [MongoDB adatok importálása az Azure Cosmos DB-be](mongodb-migrate.md)
