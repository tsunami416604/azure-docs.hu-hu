---
title: "Az SQL API-t Azure Cosmos DB globális telepítési útmutató |} Microsoft Docs"
description: "Útmutató: Azure Cosmos DB globális terjesztési az SQL API-val beállítása."
services: cosmos-db
keywords: "Globális terjesztési"
documentationcenter: 
author: rafats
manager: jhubbard
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: 0cee55673c8abca29b7e389fa4fd62a48566904b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Hogyan lehet beállítani az Azure Cosmos DB globális terjesztési az SQL API-val

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Ebben a cikkben megmutatjuk, hogyan használható az Azure-portálon Azure Cosmos DB globális terjesztési beállításához, és csatlakozzon az SQL API-val.

Ez a cikk ismerteti a következő feladatokat: 

> [!div class="checklist"]
> * Az Azure portál használatával globális terjesztési konfigurálása
> * Globális terjesztési használatával konfigurálja a [SQL API-k](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>A preferált régió, az SQL API-val való kapcsolódás

Kihasználása érdekében [globális terjesztési](distribute-data-globally.md), ügyfélalkalmazások is adja meg a dokumentum műveletek végrehajtásához használandó régiók rendezett beállítások listáját. Ezt megteheti a kapcsolat házirend beállításával. Az Azure Cosmos DB-fiók konfigurációja, az aktuális területi rendelkezésre állás és a megadott beállításokat szabályozó lista alapján, a legoptimálisabb végpont választja ki az SQL SDK írási és olvasási műveletek.

Ez a beállítás lista van megadva, az SQL SDK-k használata a kapcsolat inicializálása közben. Az SDK-k elfogadása "PreferredLocations" nem kötelező paraméter, amely egy Azure-régiók rendezett listáját.

Az SDK automatikusan elküld minden írási műveleteket ad ki az aktuális írási régió.

Az összes olvasási kapnak a PreferredLocations lista első rendelkezésre álló terület. A kérés nem teljesíthető, ha az ügyfél lefelé a listában, a következő régióban sikertelen, és így tovább.

Az SDK-k csak megpróbálja beolvasni a régió van megadva a PreferredLocations. Így például ha az adatbázis-fiókot négy régiókban, de az ügyfél PreferredLocations csak két read(non-write) régió meghatározza, majd nincs olvasási szolgáltató kívül a nem megadott PreferredLocations olvasási régió. Az olvasási régió van megadva a PreferredLocations a nem érhetők el, ha olvasási szolgáltató írási régió kívül.

Az alkalmazás a jelenlegi írási végpont ellenőrizheti és olvassa el a két tulajdonság, WriteEndpoint és ReadEndpoint, elérhető, a SDK 1.8-as verzióját és az újabb ellenőrzésével az SDK által választott végpont.

A PreferredLocations tulajdonsága nincs beállítva, ha minden kérésnél szolgáltató aktuális írási régióban.

## <a name="net-sdk"></a>.NET SDK
Az SDK kód módosítások nélkül használható. Az SDK ebben az esetben automatikusan arra utasítja, mind az Olvasás, és az aktuális írási területen írja.

A .NET SDK 1,8 és újabb verziójában a ConnectionPolicy paramétert a DocumentClient konstruktor Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations nevű tulajdonsággal rendelkezik. Ez a tulajdonság nem gyűjtemény típusú `<string>` és tartalmaznia kell a régió neveinek listáját. A karakterlánc-értékek az a régió nevét oszloponként formázott a [Azure-régiókat] [ regions] , szóközök nélkül előtt vagy után az első lap, és az utolsó karakter kulcsattribútumokkal.

Az aktuális írási és olvasási végpontok találhatók DocumentClient.WriteEndpoint és DocumentClient.ReadEndpoint kulcsattribútumokkal.

> [!NOTE]
> Az URL-címeket a végpontok nem hosszú élettartamú állandók kell tekinteni. A szolgáltatás ezek bármikor előfordulhat, hogy frissíteni. Az SDK kezeli automatikusan ezt a módosítást.
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

## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS és a Python SDK-k
Az SDK kód módosítások nélkül használható. Ebben az esetben az SDK automatikusan átirányítja a mind az Olvasás, mind az írás az aktuális írási régióban.

A 1,8 és minden SDK újabb verziója a ConnectionPolicy paramétert a DocumentClient konstruktor új tulajdonság neve DocumentClient.ConnectionPolicy.PreferredLocations. A paraméter karakterláncok tömbje, amely régió neveinek listáját. A nevek a régió nevét oszloponként vannak formázva a [Azure-régiókat] [ regions] lap. A kényelem objektumban AzureDocuments.Regions is használhatja az előre definiált állandók

Az aktuális írási és olvasási végpontok találhatók DocumentClient.getWriteEndpoint és DocumentClient.getReadEndpoint kulcsattribútumokkal.

> [!NOTE]
> Az URL-címeket a végpontok nem hosszú élettartamú állandók kell tekinteni. A szolgáltatás ezek bármikor előfordulhat, hogy frissíteni. Az SDK automatikusan kezeli ezt a módosítást.
>
>

Az alábbiakban egy példa van NodeJS/Javascript. Python és a Java a rendszer ugyanazt a sablont követi.

```java
// Creating a ConnectionPolicy object
var connectionPolicy = new DocumentBase.ConnectionPolicy();

// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);
```

## <a name="rest"></a>REST
Az adatbázisfiók elérhetővé tett több régióba, ha az ügyfelek lekérhetnek által egy GET kérelem végrehajtása a következő URI-CÍMÉN elérhetőségét.

    https://{databaseaccount}.documents.azure.com/

A szolgáltatás régiók és a replikák számára a megfelelő Azure Cosmos DB végpont URI-azonosítók listáját adja vissza. Az aktuális írási terület jelzik a válaszban. Az ügyfél kiválaszthatja a megfelelő végpont minden további REST API-kérelmek az alábbiak szerint.

Példa egy válasz

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


* Minden PUT, POST és DELETE kérelmek be kell lépnie a jelzett írási URI
* Minden lekérdezi és egyéb olvasási kérések (például a lekérdezések) lehet, hogy nyissa meg az az ügyfél által választott bármely végponthoz

Az írási kérelmek írásvédett régiók sikertelen lesz, és HTTP-hibakódot 403 ("tiltott").

Ha az írási régió változik, az ügyfél kezdeti felderítés fázis után, a későbbi írási műveleteket ad ki az előző írási régió sikertelen lesz, és HTTP-hibakódot 403 ("tiltott"). Az ügyfél ezután SZEREZHETI be újra a frissített írási régió beolvasandó régiók listáját.

Ez azt, hogy ez az oktatóanyag befejezése. Megismerheti a globális replikált fiókja konzisztencia kezeléséhez olvasásával [Azure Cosmos DB-ben konzisztenciaszintek](consistency-levels.md). És hogyan globális adatbázis-replikációval kapcsolatos további információk az Azure Cosmos Adatbázisba működik, a következő témakörben: [adatok globálisan Azure Cosmos DB terjesztése](distribute-data-globally.md).

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Az Azure portál használatával globális terjesztési konfigurálása
> * Az SQL API-kkal globális terjesztési konfigurálása

Most már folytathatja a következő oktatóanyag megtudhatja, hogyan fejleszthet, helyileg emulátorral Azure Cosmos DB helyi.

> [!div class="nextstepaction"]
> [Helyileg emulátorral fejlesztése](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

