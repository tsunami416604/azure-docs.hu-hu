---
title: Node.js tutorial for the SQL API for Azure Cosmos DB
description: Node.js oktatóanyag, amely az Azure Cosmos DB SQL API-val való csatlakoztatását és lekérdezését írja le
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 212dd243842a8bdacc8a77241f456795ef508d9e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731686"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Oktatóanyag: Node.js konzolalkalmazás létrehozása a JavaScript SDK-val az Azure Cosmos DB SQL API-adatok kezeléséhez

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Fejlesztőként előfordulhat, hogy noSQL dokumentumadatokat használó alkalmazások at használ. Az Azure Cosmos DB-ben egy SQL API-fiók használatával tárolhatja és érheti el a dokumentumadatokat. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Node.js konzolalkalmazást az Azure Cosmos DB-erőforrások létrehozásához és lekérdezéséhez.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy Azure Cosmos DB-fiókot, és csatlakozzon hozzá.
> * Állítsa be az alkalmazást.
> * Hozzon létre egy adatbázist.
> * Hozzon létre egy tárolót.
> * Elemek hozzáadása a tárolóhoz.
> * Alapvető műveletek végrehajtása az elemeken, tárolókon és adatbázisokon.

## <a name="prerequisites"></a>Előfeltételek 

Győződjön meg róla, hogy rendelkezik az alábbi erőforrásokkal:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 vagy újabb.

## <a name="create-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzunk létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa [A Node.js-alkalmazás beállítása](#SetupNode) című lépéssel. Ha az Azure Cosmos DB Emulatort használja, kövesse az [Azure Cosmos DB Emulatornál](local-emulator.md) leírt lépéseket az emulátor beállításához, majd ugorjon előre [A Node.js-alkalmazás beállítása](#SetupNode) című lépésre. 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>A Node.js-alkalmazás beállítása

Mielőtt elkezdené írni a kódot az alkalmazás létrehozásához, létrehozhatja az alkalmazás keretrendszerét. A következő lépésekkel állítsa be a keretkódot tartalmazó Node.js alkalmazást:

1. Nyissa meg kedvenc terminálját.
2. Keresse meg azt a mappát vagy könyvtárat, ahova a Node.js-alkalmazást menteni szeretné.
3. Üres JavaScript-fájlok létrehozása a következő parancsokkal:

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

4. Fájl létrehozása és `package.json` inicializálása. Használja az alábbi parancsot:
   * ```npm init -y```

5. Telepítse az @azure/cosmos modult az npm segítségével. Használja az alábbi parancsot:
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>Az alkalmazás konfigurációjának beállítása

Most, hogy az alkalmazás létezik, győződjön meg arról, hogy tud beszélni az Azure Cosmos DB.Now that your app exists, you need to make sure it can talk to Azure Cosmos DB. Néhány konfigurációs beállítás frissítésével, ahogy az a következő lépésekben is látható, beállíthatja, hogy az alkalmazás beszéljen az Azure Cosmos DB-vel:

1. Nyissa meg a *config.js* fájlt a kedvenc szövegszerkesztőjében.

1. Másolja és illessze be a következő kódrészletet a *config.js* fájlba, és állítsa be a tulajdonságokat `endpoint` és `key` az Azure Cosmos DB-végpont URI-ját és az elsődleges kulcsot. Az adatbázis, a tárolónevek **beállítása Feladatok** és **Elemek**. Az alkalmazáshoz használt partíciókulcs a **/category**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   A végpontot és a legfontosabb részleteket az [Azure Portal](https://portal.azure.com) **Keys** ablaktáblájában találja.

   ![Kulcsok lekérése az Azure Portalról képernyőkép][keys]

A JavaScript SDK az általános kifejezések *tárolóját* és *elemét*használja. A tároló lehet egy gyűjtemény, gráf vagy tábla. Az elem lehet egy dokumentum, él/csúcspont vagy sor, és ez jelöli a tárolóban lévő tartalmakat. Az előző kódrészletben a `module.exports = config;` kód a konfigurációs objektum exportálására szolgál, így az *app.js* fájlban hivatkozhat rá.

## <a name="create-a-database-and-a-container"></a>Adatbázis és tároló létrehozása

1. Nyissa meg az *databaseContext.js* fájlt a kedvenc szövegszerkesztőjében.

1. Másolja a következő kódot a *databaseContext.js* fájlba. Ez a kód egy olyan függvényt határoz meg, amely létrehozza a "Feladatok", "Elemek" adatbázist és a tárolót, ha azok még nem léteznek az Azure Cosmos-fiókban:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   Az adatbázis a tárolók között particionált elemek logikai tárolója. Az adatbázist az `createIfNotExists` **Adatbázisok** osztály vagy létrehozás i vagy létrehozási funkciójával hozhatja létre. A tároló olyan elemekből áll, amelyek az SQL API esetében JSON-dokumentumok. Hozzon létre egy tárolót a `createIfNotExists` vagy hozzon létre függvényt a **Tárolók** osztályból. A tároló létrehozása után tárolhatja és lekérdezheti az adatokat.

   > [!WARNING]
   > Egy tároló létrehozása árképzési következményekkel jár. Látogass el [az árképzési oldalunkra,](https://azure.microsoft.com/pricing/details/cosmos-db/) hogy tudd, mire számíthatsz.

## <a name="import-the-configuration"></a>A konfiguráció importálása

1. Nyissa meg az *app.js* fájlt a kedvenc szövegszerkesztőjében.

1. Másolja és illessze be `@azure/cosmos` az alábbi kódot az előző lépésekben megadott modul, konfiguráció és adatbáziskörnyezet importálásához. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Csatlakozás az Azure Cosmos-fiókhoz

Az *app.js* fájlban másolja és illessze be a következő kódot a korábban mentett végpont és kulcs használatához egy új CosmosClient objektum létrehozásához.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> Ha a **Cosmos DB emulátorhoz**csatlakozik, tiltsa le a TLS-ellenőrzést a csomópontfolyamathoz:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Most, hogy rendelkezik az Azure Cosmos DB-ügyfél elindításához szükséges kóddal, vessünk egy pillantást az Azure Cosmos DB-erőforrások használatára.

## <a name="query-items"></a><a id="QueryItem"></a>Elemek lekérdezése

Az Azure Cosmos DB támogatja az egyes tárolókban tárolt JSON-elemek gazdag lekérdezéseit. A következő mintakód egy lekérdezést mutat be, amely futtatható a tárolóban lévő elemeken. Az elemeket az `Items` osztály lekérdezési függvényével kérdezheti le. Adja hozzá a következő kódot az *app.js* fájlhoz az Azure Cosmos-fiókelemeinek lekérdezéséhez:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Elem létrehozása

Egy elem az `Items` osztály létrehozási funkciójával hozható létre. Az SQL API használatakor az elemek kivetítése dokumentumként történik, amelyek a felhasználó által definiált (tetszőleges) JSON-tartalom. Ebben az oktatóanyagban új elemet hoz létre a feladat-adatbázisban.

1. Az app.js fájlban adja meg az elemdefiníciót:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Adja hozzá a következő kódot a korábban definiált elem létrehozásához:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Elem frissítése

Az Azure Cosmos DB támogatja az elemek tartalmának cseréjét. Másolja és illessze be a következő kódot az *app.js* fájlba. Ez a kód lead egy elemet a tárolóból, és az *isComplete* mezőt igaz értékre frissíti.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Elem törlése

Az Azure Cosmos DB támogatja a JSON-elemek törlését. A következő kód bemutatja, hogyan szerezhet be egy elemet az azonosítója szerint, és hogyan törölheti azt. Másolja és illessze be a következő kódot az *app.js* fájlba:

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

A cikkben szereplő összes kódot tartalmazó első lépések megoldás futtatásához a következőkre lesz szükség:

* Egy [Azure Cosmos DB-fiók.][create-account]
* A GitHubon elérhető [Kezdeti lépések](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) megoldás.

Telepítse a projekt függőségeit npm-en keresztül. Használja az alábbi parancsot:

* ```npm install``` 

Ezután a ```config.js``` fájlban frissítse a config.endpoint és config.key értékeket a [3.](#Config)  

Majd a terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szükség, törölheti az erőforráscsoportot, az Azure Cosmos DB-fiókot és az összes kapcsolódó erőforrást. Ehhez válassza ki az Azure Cosmos DB-fiókhoz használt erőforráscsoportot, válassza a **Törlés**lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Cosmos DB-fiók figyelése](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
