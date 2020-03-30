---
title: 'Rövid útmutató: Table API node.js - Azure Cosmos DB'
description: Ez a gyors útmutató azt ismerteti, hogy hogyan használható az Azure Cosmos DB Table API profilalkalmazások létrehozására az Azure Portal és a Node.js használatával
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 08/06/2019
ms.author: sngun
ms.openlocfilehash: f317b7b5f3ab60f466054f2043027b13e8396abc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77212833"
---
# <a name="quickstart-build-a-table-api-app-with-nodejs-and-azure-cosmos-db"></a>Gyors útmutató: Table API-alkalmazás felépítése a Node.js és az Azure Cosmos DB használatával

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

Ebben a rövid útmutatóban hozzon létre egy Azure Cosmos DB Table API-fiókot, és a Data Explorer és a GitHubról klónozott Node.js alkalmazás használatával hozzon létre táblákat és entitásokat. Az Azure Cosmos DB egy többmodelles adatbázis-szolgáltatás, amely lehetővé teszi a dokumentumok, a tábla, a kulcsérték és a grafikonadatbázisok gyors létrehozását és lekérdezését globális terjesztési és horizontális méretezési képességekkel.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Vagy [próbálja ki az Azure Cosmos DB-t ingyenesen](https://azure.microsoft.com/try/cosmosdb/) Azure-előfizetés nélkül. Az [Azure Cosmos DB emulátort](https://aka.ms/cosmosdb-emulator) is `https://localhost:8081` használhatja `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`a kulcs URI-jával és a kulccsal.
- [Node.js 0.10.29+](https://nodejs.org/) .
- [Git](https://git-scm.com/downloads).

## <a name="create-a-database-account"></a>Adatbázisfiók létrehozása

> [!IMPORTANT] 
> Létre kell hoznia egy új Table API-fiókot, amely használható az általánosan elérhető Table API SDK-kkal. Az általánosan elérhető SDK-k nem támogatják az előzetes verzióban létrehozott Table API-fiókokat.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Tábla hozzáadása

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Mintaadatok hozzáadása

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Most pedig klónozunk egy Table-alkalmazást a GitHubról, beállítjuk a kapcsolati sztringet, majd futtatni fogjuk az alkalmazást. Látni fogja, mennyire egyszerű programozott módon dolgozni az adatokkal. 

1. Nyisson meg egy parancssort, hozzon létre egy git-samples nevű mappát, majd zárja be a parancssort.

    ```bash
    md "C:\git-samples"
    ```

2. Nyisson meg egy git terminálablakot, például a git bash eszközt, és a `cd` parancs használatával váltson az új mappára, ahol telepíteni szeretné a mintaalkalmazást.

    ```bash
    cd "C:\git-samples"
    ```

3. Futtassa a következő parancsot a mintatárház klónozásához. Ez a parancs másolatot hoz létre a mintaalkalmazásról az Ön számítógépén.

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-node-getting-started.git
    ```

## <a name="update-your-connection-string"></a>A kapcsolati sztring frissítése

Lépjen vissza az Azure Portalra a kapcsolati sztring adataiért, majd másolja be azokat az alkalmazásba. Ez lehetővé teszi az alkalmazás számára, hogy kommunikáljon az üzemeltetett adatbázissal. 

1. Az Azure Cosmos DB-fiókjában az [Azure Portalon](https://portal.azure.com/)válassza a **Kapcsolati karakterlánc**lehetőséget. 

    ![Tekintse meg a kapcsolati sztring szükséges adatait a Kapcsolati sztring ablaktáblán, és másolja őket](./media/create-table-nodejs/connection-string.png)

2. Másolja az ELSŐDLEGES KAPCSOLAT KARAKTERLÁNCot a jobb oldali másolásgombbal.

3. Nyissa meg az *app.config* fájlt, és illessze be az értéket a 3. 

    > [!IMPORTANT]
    > Ha a végpont a documents.azure.com címet használja, akkor előzetes fiókkal rendelkezik, és létre kell hoznia egy [új Table API-fiókot](#create-a-database-account), amely használható az általánosan elérhető Table API SDK-kkal.
    >

3. Mentse az *app.config* fájlt.

Ezzel frissítette az alkalmazást az összes olyan információval, amely az Azure Cosmos DB-vel való kommunikációhoz szükséges. 

## <a name="run-the-app"></a>Az alkalmazás futtatása

1. A git terminálablakában a `cd` paranccsal lépjen a storage-table-java-getting-started mappába.

    ```
    cd "C:\git-samples\storage-table-node-getting-started"
    ```

2. Futtassa a következő parancsot az [azure], [node-uuid], [nconf] és [async] modulok helyi telepítéséhez, valamint mentsen nekik egy bejegyzést a *package.json* fájlba.

   ```
   npm install azure-storage node-uuid async nconf --save
   ```

2. A git terminálablakában futtassa a következő parancsokat a Node.js alkalmazás futtatásához.

    ```
    node ./tableSample.js 
    ```

    A konzolablakban láthatja, hogy a rendszer táblaadatokat ad az Azure Cosmos DB új táblaadatbázisához.

    Most már visszatérhet az Adatkezelőhöz, és megtekintheti, lekérdezheti, módosíthatja és dolgozhat ezekkel az új adatokkal. 

## <a name="review-slas-in-the-azure-portal"></a>Tekintse át az SLA-kat az Azure Portalon

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Azure Cosmos DB-fiókot, hogyan hozhat létre egy táblát az Adatkezelő vel, és hogyan futtathatja a Node.js alkalmazást a táblaadatok hozzáadásához.  Most már le tudja kérdezni adatait a Table API segítségével.  

> [!div class="nextstepaction"]
> [Táblaadatok importálása a Table API-ba](table-import.md)
