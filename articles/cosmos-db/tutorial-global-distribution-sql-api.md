---
title: 'Oktatóanyag: Azure Cosmos DB globális terjesztési oktatóanyag az SQL API-hoz'
description: 'Oktatóanyag: Ismerje meg, hogyan állíthatja be Azure Cosmos DB globális terjesztést az SQL API-val a .NET, a Java, a Python és számos más SDK használatával'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: ee648efde22e6bbef045b9d89b8d016e2e489f20
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872970"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Oktatóanyag: Azure Cosmos DB globális terjesztés beállítása az SQL API használatával

Ebben a cikkben bemutatjuk, hogyan használhatja a Azure Portalt Azure Cosmos DB globális terjesztés beállításához, majd az SQL API használatával való kapcsolódáshoz.

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása az [SQL API](sql-api-introduction.md) -k használatával

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a><a id="preferred-locations"></a>Csatlakozás egy előnyben részesített régióhoz az SQL API használatával

A [globális terjesztés](distribute-data-globally.md) kihasználása érdekében az ügyfélalkalmazások megadhatják a preferált régiók sorrendbe rendezett listáját a dokumentumokkal kapcsolatos műveletek elvégzéséhez. Ezt a kapcsolódási szabályzat beállításával lehet megtenni. Az SQL SDK az Azure Cosmos DB-fiók konfigurációja, az aktuális régiónkénti rendelkezésre állás és a megadott preferencialista alapján fogja kiválasztani az optimális végpontot az írási és olvasási műveletek végrehajtásához.

A preferencialista meghatározására akkor kerül sor, amikor kapcsolatot inicializál az SQL SDK-k használatával. Az SDK-k egy „PreferredLocations” nevű választható paramétert is elfogadnak, amely az Azure-régiók rendezett listája.

Az SDK automatikusan elküldi az írásokat az aktuális írási régióba.

Az olvasásokat a PreferredLocations lista első elérhető régiójába küldi. Ha a kérelem meghiúsul, az ügyfél továbbadja a listát a következő régiónak, és így tovább.

Az SDK-k csak a PreferredLocations listában szereplő régiókból próbálnak meg olvasni. Így ha például az adatbázisfiók négy régióban érhető el, de az ügyfél csak két olvasási (nem írási) régiót ad meg a PreferredLocations listában, akkor a PreferredLocations listán nem szereplő olvasási régiók nem szolgálnak ki olvasásokat. Ha a PreferredLocations listán szereplő olvasási régiók nem érhetők el, az olvasásokat is az írási régió szolgálja ki.

Az alkalmazás az SDK 1.8-as vagy újabb verzióiban elérhető WriteEndpoint és a ReadEndpoint tulajdonságok megtekintésével ellenőrizni tudja az SDK által kiválasztott aktuális írási és olvasási végpontot.

Ha a PreferredLocations tulajdonság nincs beállítva, a kérelmek az aktuális írási régióból lesznek teljesítve.

## <a name="net-sdk"></a>.NET SDK
Az SDK a kód módosítása nélkül használható. Ebben az esetben az SDK automatikusan átirányítja az olvasásokat és az írásokat az aktuális írási régióba.

A .NET SDK 1.8-as és újabb verzióiban a DocumentClient konstruktor ConnectionPolicy paramétere rendelkezik egy Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations nevű tulajdonsággal. Ez a tulajdonság egy `<string>` típusú gyűjtemény, amelynek egy listát kell tartalmaznia a régiónevekkel. A sztringértékek formázása [Az Azure régiói][regions] oldalon található Régiónév oszlop alapján történik, és az első karakter előtt, illetve az utolsó karakter után nincs szóköz.

Az aktuális írási és olvasási végpontokat a DocumentClient.WriteEndpoint és a DocumentClient.ReadEndpoint tulajdonság tartalmazza.

> [!NOTE]
> A végpontok URL-címei nem tekinthetők hosszú élettartamú állandóknak. A szolgáltatás bármikor frissítheti őket. Az SDK automatikusan kezeli ezt a módosítást.
>
>

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

## <a name="nodejsjavascript"></a>Node. js/JavaScript

> [!NOTE]
> A végpontok URL-címei nem tekinthetők hosszú élettartamú állandóknak. A szolgáltatás bármikor frissítheti őket. Az SDK automatikusan kezeli ezt a módosítást.
>
>

Alább látható egy példa a Node. js-hez vagy a Javascripthez.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Python SDK

A következő kód bemutatja, hogyan állíthatja be az előnyben részesített helyszíneket a Python SDK használatával:

```python

connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v4-sdk"></a>Java v4 SDK

A következő kód bemutatja, hogyan állíthatja be az előnyben részesített helyszíneket a Java SDK használatával:

### <a id="java4-preferred-locations"></a>
#### <a name="async"></a>[Aszinkron](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) aszinkron API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TutorialGlobalDistributionPreferredLocationAsync)]

#### <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=TutorialGlobalDistributionPreferredLocationSync)]

--- 

## <a name="rest"></a>REST
Ha az adatbázisfiók több régióban is elérhetővé vált, az ügyfelek lekérdezhetik a rendelkezésre állását egy GET kéréssel, amely a következő URI-ra vonatkozik.

    https://{databaseaccount}.documents.azure.com/

A szolgáltatás visszaadja a régiók listáját és a replikák régiókhoz tartozó Azure Cosmos-DB végpont URI-jait. A válasz az aktuális írási régiót is tartalmazni fogja. Az ügyfél ezután a következő módon választhatja ki a megfelelő végpontot a további REST API-kérésekhez.

Példaválasz

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Az összes PUT, POST és DELETE kérelemnek a megadott írási URI-ra kell érkeznie
* A beolvasás és az egyéb írásvédett kérelmek (például a lekérdezések) az ügyfél tetszőleges végpontján léphetnek fel

A csak olvasási régiókba érkező írási kérelmek a 403-as HTTP-hibakóddal („Tiltott”) hiúsulnak meg.

Ha az írási régió az ügyfél kezdeti felderítési fázisa után változik, az előző írási régióba való későbbi írások sikertelenek lesznek a 403-as HTTP-hibakód miatt ("tiltott"). Az ügyfélnek ilyenkor ismét le kell kérnie a régiók listáját a GET metódussal a frissített írási régió beszerzéséhez.

Ezzel el is végezte az oktatóanyagot. Ha meg szeretné ismerni, hogyan kezelheti a globálisan replikált fiók konzisztenciáját, olvassa el a [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md) című cikket. További információ a globális adatbázis-replikáció működéséről az Azure Cosmos DB szolgáltatásban: [Globális adatterjesztés az Azure Cosmos DB-vel](distribute-data-globally.md).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása az SQL API-k használatával

Továbbléphet a következő oktatóanyagra, amelyből megtudhatja, hogyan fejleszthet helyileg az Azure Cosmos DB helyi emulátorával.

> [!div class="nextstepaction"]
> [Helyi fejlesztés az emulátorral](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

