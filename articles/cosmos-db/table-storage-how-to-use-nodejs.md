---
title: Azure Table storage vagy a Node.js-ből a Azure Cosmos DB használatával |} Microsoft Docs
description: Strukturált adat tárolása a felhőben Azure Table storage vagy Azure Cosmos DB használatával.
services: cosmos-db
documentationcenter: nodejs
author: SnehaGunda
manager: kfile
ms.assetid: fc2e33d2-c5da-4861-8503-53fdc25750de
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 3f1908a6c2d129da44e0719b2cf69cf09baef356
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-azure-table-storage-from-nodejs"></a>Node.js-ből a Azure Table storage használata
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan hajthat végre az Azure Storage Table szolgáltatás vagy az Azure Cosmos DB használata Node.js-alkalmazásokban gyakori forgatókönyvek.

## <a name="create-an-azure-service-account"></a>Az Azure szolgáltatás-fiók létrehozása

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB tábla API-fiók létrehozása

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage"></a>Állítsa be az alkalmazását Azure Storage eléréséhez
Azure Storage- vagy Azure Cosmos DB használatához szüksége az Azure Storage szolgáltatás SDK a Node.js, amely tartalmaz, a felhasználók kényelme érdekében szalagtár szerepel, amely a többi tárolási szolgáltatásokkal kommunikálni.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>A csomag telepítése a csomópont Package Manager (NPM) használatával
1. Használjon például egy parancssori felületet **PowerShell** (Windows), **Terminálszolgáltatások** (Mac), vagy **Bash** (Unix) eszközt, és lépjen abba a mappába, amelyben létrehozta az alkalmazást.
2. Típus **npm telepítése azure-tároló** a parancsablakban. A parancs kimenetében a következőhöz hasonló.

       azure-storage@0.5.0 node_modules\azure-storage
       +-- extend@1.2.1
       +-- xmlbuilder@0.4.3
       +-- mime@1.2.11
       +-- node-uuid@1.4.3
       +-- validator@3.22.2
       +-- underscore@1.4.4
       +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
       +-- xml2js@0.2.7 (sax@0.5.2)
       +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
3. Manuálisan futtatható a **ls** parancs futtatásával ellenőrizze, hogy egy **node_modules** mappa hozták létre. A mappában megtalálja az **azure-tároló** csomagot, amely tartalmazza a könyvtárak kell érniük a tárhelyet.

### <a name="import-the-package"></a>A csomag importálása
Adja hozzá a következő kódot a felső részén a **server.js** fájl az alkalmazásban:

```nodejs
var azure = require('azure-storage');
```

## <a name="add-an-azure-storage-connection"></a>Az Azure Storage-kapcsolat hozzáadása
Az Azure modul olvassa be a környezeti változók AZURE_STORAGE_ACCOUNT és AZURE_STORAGE_ACCESS_KEY vagy AZURE_STORAGE_CONNECTION_STRING az Azure Storage-fiók eléréséhez szükséges információkat. Ha ezek a környezeti változók nem, meg kell adnia a fiókadatokat, meghívásakor **TableService**. Például az alábbi kód létrehoz egy **TableService** objektum:

```nodejs
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

## <a name="add-an-azure-comsos-db-connection"></a>Egy Azure Comsos adatbázis-kapcsolat hozzáadása
Egy Azure Cosmos adatbázis-kapcsolat hozzáadása, hozzon létre egy **TableService** objektumra, és adja meg a fiók nevét, az elsődleges kulcs és a végpont. Ezeket az értékeket a másolhatja **beállítások** > **kapcsolati karakterlánc** Cosmos DB fiókja az Azure portálon. Példa:

```nodejs
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```  

## <a name="create-a-table"></a>Tábla létrehozása
Az alábbi kód létrehoz egy **TableService** objektumot, és hozzon létre egy új táblát használja. 

```nodejs
var tableSvc = azure.createTableService();
```

A hívás **createTableIfNotExists** új táblázat létrehozása a megadott névvel, ha még nem létezik. Az alábbi példában a "mytable" nevű, ha még nem létezik új táblázat létrehozása:

```nodejs
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

A `result.created` van `true` Ha új tábla létrehozása, és `false` Ha a tábla már létezik. A `response` a kérelmekre vonatkozó információkat tartalmaz.

### <a name="filters"></a>Szűrők
Választható szűrni használatával végrehajtott műveletek alkalmazása **TableService**. Műveletek szűrésének lehetnek a naplózási, automatikus újrapróbálkozások stb. A metódus aláírása megvalósító objektumok szűrők a következők:

```nodejs
function handle (requestOptions, next)
```

Ezután a előfeldolgozása kérelmet a beállítások, a metódust kell hívni **következő**, átadja a következő aláírással rendelkező visszahívás:

```nodejs
function (returnObject, finalCallback, next)
```

A visszahívási, és feldolgozás után a **returnObject** (válasza a kérés a kiszolgáló), a visszahívás vagy kell meghívni **következő** rá más szűrők folytatni vagy egyszerűen Invoke **finalCallback** egyébként pedig a szolgáltatás hívás befejezéséhez.

Két szűrőket, amelyek megvalósítják az újrapróbálkozási logika érhetők el az Azure SDK for Node.js, a **ExponentialRetryPolicyFilter** és **LinearRetryPolicyFilter**. A következő létrehoz egy **TableService** objektum, amely használja a **ExponentialRetryPolicyFilter**:

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Egy entitás hozzáadásához először létre kell hoznia egy objektum, amely meghatározza az entitás tulajdonságai. Összes entitás tartalmaznia kell egy **PartitionKey** és **RowKey**, amelyeket az entitáshoz tartozó egyedi azonosítóként.

* **PartitionKey** -meghatározza a partíció, amely az entitás tárolja.
* **RowKey** – egyedi módon azonosítja az entitást a partíción belül.

Mindkét **PartitionKey** és **RowKey** karakterlánc-értékekkel kell lennie. További információkért lásd: [ismertetése a Table szolgáltatás adatmodell](http://msdn.microsoft.com/library/azure/dd179338.aspx).

A következő példában látható egy entitás meghatározásának. Vegye figyelembe, hogy **dueDate** van definiálva, egyfajta **Edm.DateTime**. A típus megadása nem kötelező, és típusok vannak következtetni rá, ha nincs megadva.

```nodejs
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> Szerepel továbbá egy **időbélyeg** mezőjét az egyes rekordokhoz, amely az Azure-ban van beállítva, ha entitás beszúrni vagy frissíteni.
>
>

Használhatja a **entityGenerator** entitások létrehozásához. Az alábbi példakód létrehozza a azonos feladat entitás használatával a **entityGenerator**.

```nodejs
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Egy entitás hozzáadása a táblához, adja át a forrásentitás-objektum a **insertEntity** metódust.

```nodejs
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

Ha a művelet sikeres, `result` tartalmazza a [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) a beszúrt rekordok és `response` működésével kapcsolatos adatokat tartalmazza.

Példa egy válasz:

```nodejs
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Alapértelmezés szerint **insertEntity** nem ad vissza a beszúrt entitás részeként a `response` információkat. Ha más műveleteket végez ehhez az entitáshoz tervez, vagy gyorsítótárazza az információkat, azok hasznos lehet akkor adja vissza részeként a `result`. Ehhez engedélyezésével **echoContent** az alábbiak szerint:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`
>
>

## <a name="update-an-entity"></a>Egy entitás frissítése
Több módszer érhető el frissíteni a meglévő entitás áll rendelkezésre:

* **replaceEntity** -cserélje ki egy meglévő entitás frissíti.
* **mergeEntity** -meglévő entitás frissíti a meglévő entitás új tulajdonságértékek egyesíti.
* **insertOrReplaceEntity** -cserélje ki egy meglévő entitás frissíti. Ha nem entitás létezik, a program beszúrja egy újat.
* **insertOrMergeEntity** -meglévő entitás frissíti a meglévő tulajdonságértékek új egyesíti. Ha nem entitás létezik, a program beszúrja egy újat.

A következő példa bemutatja, hogy egy entitás használatával frissítése **replaceEntity**:

```nodejs
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> Alapértelmezés szerint egy entitás frissítése nem ellenőrizze, hogy ha az adatok frissítése során korábban módosították egy másik folyamat. Támogatja a párhuzamos frissítések:
>
> 1. Az ETag a frissítendő objektum beolvasása. Ennek részeként adja vissza a `response` entitás kapcsolatos műveletek és kérhető `response['.metadata'].etag`.
> 2. Entitás frissítési művelet végrehajtásakor adja hozzá az új entitást korábban lekért ETag adatok. Példa:
>
>       entity2 [.metadata] .etag = currentEtag;
> 3. A frissítési műveletet végrehajtani. Ha az entitást az ETag érték, például az alkalmazás egy másik példánya lekért óta módosították egy `error` ad vissza, amely meghatározza, hogy a kérelemben megadott frissítés feltétel nem teljesült.
>
>

A **replaceEntity** és **mergeEntity**, ha a frissítendő entitás nem létezik, akkor a frissítési művelet sikertelen; ezért, ha szeretné tárolni, függetlenül attól, hogy egy entitás azt már létezik, használjon **insertOrReplaceEntity** vagy **insertOrMergeEntity**.

A `result` sikeres frissítési műveleteket tartalmaz a **Etag** a frissített entitás.

## <a name="work-with-groups-of-entities"></a>Az entitások csoportok használata
Egyes esetekben érdemes elküldeni a több műveletei együtt egy kötegelt atomi feldolgozása a kiszolgáló biztosításához. Hajthatja végre, hogy, használja a **TableBatch** osztály létrehozza, és használja a **executeBatch** metódusában **TableService** a kötegelt műveletek végrehajtásához.

 A következő példa bemutatja, hogy egy kötegelt két entitása elküldése:

```nodejs
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

A sikeres kötegelt műveleteket `result` a kötegben lévő egyes műveletek kapcsolatos tudnivalókat tartalmazza.

### <a name="work-with-batched-operations"></a>A kötegelt műveletek használata
Egy kötegelt hozzáadni megtekintésével műveletek vizsgálhatja meg a `operations` tulajdonság. Az alábbi eljárások segítségével műveletek használata:

* **Törölje a jelet** -egy köteg az összes művelet törli.
* **getOperations** -művelet lekérdezi a kötegből.
* **hasOperations** -igaz értéket ad vissza, ha a köteg műveleteket tartalmaz.
* **removeOperations** -művelet eltávolítja.
* **méret** -műveletek számát adja vissza a kötegben.

## <a name="retrieve-an-entity-by-key"></a>Egy entitás kulcs letöltése
Egy adott entitás alapján vissza a **PartitionKey** és **RowKey**, használja a **retrieveEntity** metódust.

```nodejs
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

Ez a művelet végeztével befejeződik, `result` az entitást tartalmaz.

## <a name="query-a-set-of-entities"></a>Az entitások készletének lekérdezése
Ha egy táblából, használja a **TableQuery** objektumot egy lekérdezési kifejezésben, a következő záradék használatával kialakításához:

* **Válassza ki** – a lekérdezés által visszaadott mezőket.
* **Ha** -a where záradékban.

  * **és** - `and` where feltétel.
  * **vagy** - `or` where feltétel.
* **felső** -beolvasási elemek száma.

A következő példa a felső öt cikkeket a PartitionKey "hometasks" visszaadó alkot.

```nodejs
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

Mivel **válasszon** nem használ, akkor minden mező ad vissza. A lekérdezés egy táblázaton végrehajtásához használja **queryEntities**. Az alábbi példában a lekérdezés a "mytable" ad vissza entitásokat.

```nodejs
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

Ha sikeres, `result.entries` , amelyek megfelelnek a lekérdezés entitások tömbjét tartalmazza. Ha a lekérdezés nem tudta ad vissza az összes entitásokat `result.continuationToken` nem -*null* és a harmadik paraméter használható **queryEntities** további eredmények beolvasásához. A kezdeti lekérdezés használata *null* a harmadik paraméter.

### <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése
A lekérdezés egy táblához pár mezők kérhetnek le egy entitás.
Ez csökkenti a sávszélesség, és javíthatja a lekérdezési teljesítményt, különösen olyan nagyméretű entitásokat. Használja a **válasszon** záradék, és adja át a mezők nevének meghatározása való visszatéréshez. Például a következő lekérdezés csak adja vissza a **leírás** és **dueDate** mezőket.

```nodejs
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Entitás törlése
A partíció- és sorfejlécek kulcsokkal entitás törölheti. Ebben a példában a **task1** objektum tartalmazza a **RowKey** és **PartitionKey** értékek az entitás törlése. Az objektum átadott, majd a **deleteEntity** metódust.

```nodejs
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> Érdemes lehet ETag-EK elemek, törlésekor annak érdekében, hogy az elem nem módosították-e egy másik folyamat. Lásd: [frissíthető entitás](#update-an-entity) információ az ETag-EK használatával.
>
>

## <a name="delete-a-table"></a>Tábla törlése
Az alábbi kód törölhető egy tábla a tárfiókból.

```nodejs
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Ha biztos benne, hogy a tábla létezik, **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Használjon tokeneket folytatása
Ha nagy mennyiségű eredmények táblák kérdez le, keresse meg folytatási jogkivonatokat. Nagy mennyiségű adat lehet használható a lekérdezésben, akkor előfordulhat, hogy nem ismeri fel, ha ismeri fel, ha jelen a folytatási kód nem hoz létre.

A **eredmények** objektumot adott vissza entitások beállítása lekérdezése során egy `continuationToken` tulajdonságot, ha ilyen tokent. Majd ezzel a lekérdezés végrehajtása során továbbra is a partícióazonosító és a tábla entitás közötti áthelyezése közben.

Lekérdezésekor, megadhat egy `continuationToken` paraméter a lekérdezés-példányt és a visszahívási függvény között:

```nodejs
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Ha nézze meg a `continuationToken` objektum található tulajdonságok például `nextPartitionKey`, `nextRowKey` és `targetLocation`, amelyek segítségével az eredményeket iterációt.

## <a name="work-with-shared-access-signatures"></a>Megosztott hozzáférési aláírásokkal működik
Közös hozzáférésű jogosultságkód (SAS), amelyek egy biztonságos táblák részletes hozzáférést biztosítanak a Tárfiók neve vagy a kulcsok megadása nélkül. SAS gyakran használják korlátozott hozzáférést biztosít az adatok, például egy mobil alkalmazás lekérdezést rögzíti.

A megbízható alkalmazások, például egy felhőalapú szolgáltatás létrehoz egy SAS használatával a **generateSharedAccessSignature** , a **TableService**, és átadja egy nem megbízható vagy félig megbízható alkalmazás, például egy mobilalkalmazást. A biztonsági Társítások létrejön egy házirendet, amely a kezdő és záró dátumát, amely alatt a SAS érvénytelen ismerteti, valamint a hozzáférési szintet a biztonsági Társítások jogosult kap.

Az alábbi példa létrehoz egy új megosztott elérési házirendet, amely lehetővé teszi a lekérdezés ("r") a tábla SAS jogosult, és 100 perc létrehozása után lejár.

```nodejs
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

Vegye figyelembe, hogy meg kell adni az állomás információit, akkor szükség, ha a biztonsági Társítások jogosult megpróbál hozzáférni a táblában.

Az ügyfélalkalmazás majd használja a biztonsági Társításait **TableServiceWithSAS** a táblázaton műveletek végrehajtásához. A következő példa a tábla kapcsolódik, és lekérdezést hajt végre.

```nodejs
var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

Mivel a SAS létre lett hozva, csak a lekérdezés hozzáférést, ha próbál beszúrni, frissíteni vagy entitások törlésére hibát ad vissza.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák
Egy hozzáférés-vezérlési lista (ACL) segítségével állítsa be a hozzáférési házirend tartozó SAS korlátozására. Ez akkor hasznos, ha azt szeretné, hogy lehetővé teszi több ügyfelek a táblának az elérésére, de különböző hozzáférési házirendek biztosít az egyes ügyfelek számára.

Hozzáférés-vezérlési Listában hozzáférési házirendeket, tömbje segítségével minden házirendhez társított azonosítójú van megvalósítva. Az alábbi példa meghatározza, hogy két házirend, egy "felhasználó1", és egy "felhasználó2":

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Az alábbi példa lekérdezi az aktuális hozzáférés-vezérlési listája a **hometasks** tábla, és hozzáadja az új házirendek **setTableAcl**. Ez a megközelítés lehetővé teszi, hogy:

```nodejs
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

A hozzáférés-vezérlési lista beállítása után is létrehozhat egy SAS-azonosító egy házirend alapján. Az alábbi példa létrehoz egy új SAS-kód "felhasználó2":

```nodejs
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket.

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
* [Az Azure Storage SDK for Node.js](https://github.com/Azure/azure-storage-node) GitHub tárházából.
* [Azure Node.js-fejlesztőknek](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest)
* [Node.js-webalkalmazás létrehozása az Azure-ban](../app-service/app-service-web-get-started-nodejs.md)
* [Hozza létre és telepítheti az Azure Cloud Service a Node.js-alkalmazás](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (a Windows PowerShell használatával)
