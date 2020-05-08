---
title: Node. js-oktatóanyag a Azure Cosmos DB SQL API-hoz
description: Node.js oktatóanyag, amely az Azure Cosmos DB SQL API-val való csatlakoztatását és lekérdezését írja le
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
ms.openlocfilehash: ef493b6b21eb0ba0ad6d22a21e4e205a9fecacb6
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858065"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Oktatóanyag: Node. js-konzol alkalmazás létrehozása a JavaScript SDK-val Azure Cosmos DB SQL API-adat kezeléséhez

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Fejlesztőként rendelkezhet olyan alkalmazásokkal, amelyek NoSQL-dokumentumokat használnak. A dokumentumok tárolására és elérésére a Azure Cosmos DBban SQL API-fiók használható. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Node. js-konzol alkalmazást Azure Cosmos DB erőforrások létrehozásához és lekérdezéséhez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre és kapcsolódjon egy Azure Cosmos DB-fiókhoz.
> * Az alkalmazás beállítása.
> * Hozzon létre egy adatbázist.
> * Hozzon létre egy tárolót.
> * Elemek hozzáadása a tárolóhoz.
> * Alapszintű műveleteket hajthat végre az elemeken, a tárolón és az adatbázison.

## <a name="prerequisites"></a>Előfeltételek 

Győződjön meg róla, hogy rendelkezik az alábbi erőforrásokkal:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node. js](https://nodejs.org/) v 6.0.0 vagy újabb.

## <a name="create-azure-cosmos-db-account"></a>Azure Cosmos DB fiók létrehozása

Hozzunk létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa [A Node.js-alkalmazás beállítása](#SetupNode) című lépéssel. Ha az Azure Cosmos DB Emulatort használja, kövesse az [Azure Cosmos DB Emulatornál](local-emulator.md) leírt lépéseket az emulátor beállításához, majd ugorjon előre [A Node.js-alkalmazás beállítása](#SetupNode) című lépésre. 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>A Node.js-alkalmazás beállítása

Mielőtt elkezdi a kód írását az alkalmazás létrehozásához, létrehozhatja az alkalmazás keretrendszerét. A következő lépésekkel állíthatja be a Node. js-alkalmazást, amely a keretrendszer kódjával rendelkezik:

1. Nyissa meg kedvenc terminálját.
2. Keresse meg azt a mappát vagy könyvtárat, ahova a Node.js-alkalmazást menteni szeretné.
3. Hozzon létre üres JavaScript-fájlokat a következő parancsokkal:

   * Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux/OS X:
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. Hozzon létre és inicializáljon egy `package.json` fájlt. Használja az alábbi parancsot:
   * ```npm init -y```

5. Telepítse az @azure/cosmos modult az npm segítségével. Használja az alábbi parancsot:
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>Az alkalmazás konfigurációjának beállítása

Most, hogy az alkalmazás már létezik, meg kell győződnie arról, hogy tud beszélni Azure Cosmos DB. Néhány konfigurációs beállítás frissítésével az alábbi lépésekben látható módon beállíthatja, hogy az alkalmazás beszéljen a Azure Cosmos DB:

1. Nyissa meg a *config. js* fájlt a kedvenc szövegszerkesztőben.

1. Másolja és illessze be a következő kódrészletet a *config. js* fájlba, és állítsa be `endpoint` a `key` TULAJDONSÁGOKAT és a Azure Cosmos db végponti URI-t és az elsődleges kulcsot. Az adatbázis, a tárolók nevei a **feladatok** és **elemek**értékre vannak beállítva. Az alkalmazáshoz használni kívánt partíciós kulcs **/category**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   A végpont és a kulcs részleteit a [Azure Portal](https://portal.azure.com) **kulcsok** ablaktábláján találja.

   ![Kulcsok lekérése az Azure Portalról képernyőkép][keys]

A JavaScript SDK az általános feltételek *tárolóját* és az *elemeket*használja. A tároló lehet egy gyűjtemény, gráf vagy tábla. Az elem lehet egy dokumentum, él/csúcspont vagy sor, és ez jelöli a tárolóban lévő tartalmakat. Az előző kódrészletben a `module.exports = config;` kód a konfigurációs objektum exportálására szolgál, így hivatkozhat rá az *app. js* fájlon belül.

## <a name="create-a-database-and-a-container"></a>Adatbázis és tároló létrehozása

1. Nyissa meg a *databaseContext. js* fájlt a kedvenc szövegszerkesztőben.

1. Másolja és illessze be a következő kódot a *databaseContext. js* fájlba. Ez a kód olyan függvényt határoz meg, amely létrehozza a "feladatok", az "Items" adatbázist és a tárolót, ha még nem léteznek az Azure Cosmos-fiókban:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   Az adatbázis a tárolók között particionált elemek logikai tárolója. Adatbázis létrehozásához használja az **adatbázisok** osztály vagy `createIfNotExists` a Create függvényét. Egy tároló olyan elemekből áll, amelyek az SQL API esetében JSON-dokumentumok. Hozzon létre egy tárolót a `createIfNotExists` **tárolók** osztály vagy a Create függvény használatával. A tároló létrehozása után tárolhatja és lekérdezheti az adattárolást.

   > [!WARNING]
   > A tárolók létrehozása díjszabási következményekkel jár. Látogasson el az [árképzési oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/) , és tudja, mire számíthat.

## <a name="import-the-configuration"></a>A konfiguráció importálása

1. Nyissa meg az *app. js* fájlt a kedvenc szövegszerkesztőben.

1. Másolja és illessze be az alábbi kódot a `@azure/cosmos` modul, a konfiguráció és az előző lépésekben megadott databaseContext importálásához. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Kapcsolódás az Azure Cosmos-fiókhoz

Az *app. js* fájlban másolja és illessze be a következő kódot, hogy a korábban mentett végpontot és kulcsot használja egy új CosmosClient objektum létrehozásához.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> Ha csatlakozik a **Cosmos db emulátorhoz**, tiltsa le a TLS-ellenőrzést a csomóponti folyamathoz:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Most, hogy rendelkezik az Azure Cosmos DB-ügyfél elindításához szükséges kóddal, vessünk egy pillantást az Azure Cosmos DB-erőforrások használatára.

## <a name="query-items"></a><a id="QueryItem"></a>Lekérdezési elemek

Azure Cosmos DB támogatja az egyes tárolókban tárolt JSON-elemek részletes lekérdezéseit. A következő mintakód egy lekérdezést mutat be, amelyet a tároló elemein futtathat. Az elemeket az `Items` osztály lekérdezési funkciójával kérdezheti le. Adja hozzá a következő kódot az *app. js* fájlhoz az Azure Cosmos-fiókból származó elemek lekérdezéséhez:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Elemek létrehozása

Egy tétel a `Items` osztály Create függvényével hozható létre. Az SQL API használatakor az elemek dokumentumokként vannak kiképezve, amelyek felhasználó által definiált (tetszőleges) JSON-tartalmak. Ebben az oktatóanyagban egy új elem jön létre a feladatok adatbázisán belül.

1. Az app. js fájlban adja meg az elem definícióját:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Adja hozzá a következő kódot a korábban definiált elem létrehozásához:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Egy tétel frissítése

Azure Cosmos DB támogatja az elemek tartalmának cseréjét. Másolja és illessze be az alábbi kódot az *app. js* fájlba. Ez a kód beolvas egy elemet a tárolóból, és frissíti a *isComplete* mezőt igaz értékre.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Elem törlése

Az Azure Cosmos DB támogatja a JSON-elemek törlését. Az alábbi kód azt mutatja be, hogyan kérhető le egy adott tétel az azonosítója alapján, és hogyan törölhető. Másolja és illessze be a következő kódot az *app. js* fájlba:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>A Node.js-alkalmazás futtatása

Összességében a kódnak így kell kinéznie:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot:

```bash 
node app.js
```

Meg kell jelennie az első lépések alkalmazás kimenetének. A kimenetnek meg kell egyeznie az alábbi példaszöveggel.

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>A Node. js-oktatóanyagban szereplő teljes megoldás beszerzése

Ha nincs ideje az oktatóanyag lépéseinek végrehajtására, vagy csak szeretné letölteni a kódot, a [GitHubon](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ) beszerezheti azt.

A cikkben szereplő összes kódot tartalmazó első lépések megoldás futtatásához a következőkre lesz szüksége:

* Egy [Azure Cosmos db-fiók][create-account].
* A GitHubon elérhető [Kezdeti lépések](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) megoldás.

Telepítse a projekt függőségeit a NPM-on keresztül. Használja az alábbi parancsot:

* ```npm install``` 

Ezután a ```config.js``` fájlban frissítse a config. Endpoint és a config. Key értékeket a [3. lépés: az alkalmazás konfigurációjának beállítása](#Config)című témakörben leírtak szerint.  

Majd a terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szükség, törölheti az erőforráscsoportot, Azure Cosmos DB fiókot és az összes kapcsolódó erőforrást. Ehhez válassza ki a Azure Cosmos DB fiókhoz használt erőforráscsoportot, válassza a **Törlés**lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Cosmos DB fiók figyelése](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
