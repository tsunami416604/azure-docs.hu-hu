---
title: 'Az Azure Cosmos DB: SQL aszinkron Java API, SDK &-erőforrások'
description: Mindent megtudhat a SQL aszinkron Java API-t és az SDK kiadási dátum, kivezetési dátum és az Azure Cosmos DB SQL aszinkron Java SDK minden verziója közötti végzett módosításokat.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 3bee473adde9c4762cf400dff6190eb76a4cc8d7
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624691"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB aszinkron Java SDK az SQL API-hoz: Kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

Az SQL API aszinkron Java SDK azáltal, hogy az aszinkron műveletek támogatása az eltér az SQL API-t a Java SDK-t a [Netty könyvtár](https://netty.io/). A már meglévő [SQL API-t a Java SDK](sql-api-sdk-java.md) nem támogatja az aszinkron műveleteket. 

| |  |
|---|---|
| **SDK letöltése** | [Maven 3](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-dokumentáció** |[Java API dokumentációja](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Hozzájárul az SDK-t** | [GitHubon](https://github.com/Azure/azure-cosmosdb-java) | 
|**Első lépések** | [Az aszinkron Java SDK használatának első lépései](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Kódminta** | [GitHubon](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Teljesítménnyel kapcsolatos tippek**| [GitHub-információ](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimális támogatott futtatókörnyezet**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name250250"></a><a name="2.5.0"/>2.5.0
* Alapértelmezés szerint a TCP mód jelenleg be van kapcsolva
* A Cross Partition lekérdezési metrikái mostantól az összes partíciót visszaadja.
* A globális erős működés most már megfelelően működik
* Feladatátvétel olyan lekérdezések esetében, amelyek nem megfelelően újrapróbálkoznak a több főkiszolgálóval
* Függőségi ütközések biztonsági gyorsjavítások esetén

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Hibajavítás a hash v2-támogatáshoz

### <a name="a-name243243"></a><a name="2.4.3"/>2.4.3
* Hibajavítás az erőforrás-szivárgáshoz a következő ügyfélen: # Bezárás () ([github #88](https://github.com/Azure/azure-cosmosdb-java/issues/88)).

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* A további jogkivonat-támogatás hozzáadva a több partíciós lekérdezésekhez.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Kijavított néhány hibát közvetlen módban.
* Továbbfejlesztett naplózás közvetlen módban.
* Továbbfejlesztett kapcsolatok kezelése.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* A közvetlen mód kapcsolata már általánosan elérhető (GA). Közvetlen üzemmódú kapcsolatot használó minta: [Azure-cosmosdb-Java](https://github.com/Azure/azure-cosmosdb-java) GitHub-tárház.
* A QueryMetrics támogatása.
* Módosította a Java. util. Collectiont elfogadó API-kat, amelyek sorrendje fontos a Java. util. list elfogadásához. Most ConnectionPolicy # getPreferredLocations (), JsonSerialization és PartitionKey (.) fogadja el a listát.

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* Közvetlen módú kapcsolat támogatása.
* Módosította a Java. util. Collectiont elfogadó API-kat, amelyek sorrendje fontos a Java. util. list elfogadásához.
  Most ConnectionPolicy # getPreferredLocations (), JsonSerialization és PartitionKey (.) fogadja el a listát.
* Kijavított egy munkamenet-hibát a dokumentumok lekérdezéséhez az átjáró módban.
* Frissített függőségek (nettó 0.4.20 [GitHub-#79](https://github.com/Azure/azure-cosmosdb-java/issues/79), RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* A nagyon nagy lekérdezési válaszok kezelésére szolgáló javítások.
* Javítja az erőforrás-jogkivonat kezelését az ügyfél-példány létrehozásakor ([github #78](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* Frissített sérülékeny függőség Jackson-koordinátamezőket ([github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Erőforrás adatszivárgást hibája kijavítva.
* MultiPolygon támogatása
* Egyéni fejlécek RequestOptions támogatása.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2.
* Csomagolási hibája kijavítva.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Az írási újrapróbálkozási elérési út NPE hibája kijavítva.
* A végpont-kezelés NPE hibája kijavítva.
* Frissített sebezhető függőségek ([GitHub #68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Támogatás hozzáadva a hibaelhárítási Netty hálózati naplózás.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Többrégiós írási támogatása.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Támogatás hozzáadva a Proxy.
* Erőforrás-engedélyezési jogkivonat támogatása.
* Hiba javítva a nagyméretű partíciós kulcsok ([GitHub #63](https://github.com/Azure/azure-cosmosdb-java/issues/63)) kezelésére.
* Továbbfejlesztett dokumentációját.
* Az SDK átstrukturálása részletesebb modulokat.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Kijavított egy hibát a nem angol nyelvű területi beállításokhoz ([GitHub #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* A hozzáadott segédmetódusokat ütközés erőforrásban.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* A teljesítmény okai és licencelése ([GitHub #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)) miatt Jackson lecserélte a org. JSON-függőséget.
* Eltávolított elavult OfferV2 osztály.
* Átviteli sebesség tartalom ajánlat osztályhoz metódus hozzáadott leírójához.
* Bármelyik módszert a dokumentumok és az erőforrások egy jackson objektumot adja vissza a módosított org.json típusok visszaadása.
* Írja be a getObject(.) metódus az osztályok kiterjesztésének JsonSerializable módosult egy jackson ObjectNode adja vissza.
* getCollection(.) módszer gyűjtemény, ObjectNode adja vissza.
* A org.json.JSONObject arg. eltávolított JsonSerializable alosztályok konstruktorok
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) mostantól behúzás két szóközt használja.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2-es
* Egyedi Index szabályzat támogatása.
* Támogatás hozzáadva a válasz folytatási jogkivonat mérete a hírcsatorna beállításai korlátozza.
* A partíció-lekérdezés Adatbázisközi Partition Split támogatása.
* Kijavítva egy hiba a JSON-időbélyeg szerializálásakor ([GitHub #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Kijavítva a hiba az enumerálás a Json-szerializálást.
* Kijavítva egy hiba a 2 MB méretű dokumentumok kezelésében ([GitHub #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Függőség com. fasterxml. Jackson. Core: Jackson – a koordinátamezőket frissítése egy hiba miatt 2.9.5 ([Jackson-koordinátamezőket: GitHub #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Függőség rxjava – a 0.8.0.17-ra frissített extrák egy hiba miatt ([rxjava-extrák: GitHub #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* A metaadatok leírását frissíti, hogy a beágyazott dokumentáció a rest-tel pom-fájljába.
* Szintaxis fejlesztése ([github #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([GitHub #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Biztonsági-nyomás támogatása a lekérdezésben.
* A lekérdezés kulcstartományhoz partícióazonosító támogatása.
* Javítsa a nagyobb folytatási jogkivonat használatát a kérelem fejlécében (hibajavító GitHub #24).
* Annak biztosítása érdekében a JVM 4.1.22.Final frissített netty függőségi leállítja a főszálban befejeződése után.
* Javítsa ki a tokenu relace passing fő erőforrások olvasásakor elkerülése érdekében.
* További példák hozzá.
* További teljesítménymérési forgatókönyvek hozzá.
* Rögzített Java fejlécfájlok megfelelő java doc létrehozása céljából.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Általánosan elérhető SDK-val nem blokkoló i/o-használatával teljes körű támogatása a [Netty könyvtár](https://netty.io/) átjáró módban. 

## <a name="release-and-retirement-dates"></a>Kiadás és kivezetési dátuma
A Microsoft legalább értesítést küldenek **12 hónapig** kivonása egy SDK-t kiegyenlítse az a és újabb támogatott verzióra váltás előtt.

Új szolgáltatások és funkciók és optimalizálási lehetőségek csak hozzá az aktuális SDK-hoz. Ezért ajánlott, hogy mindig a legújabb SDK verzióra frissít, a lehető leghamarabb.

Cosmos DB-hez a kivont SDK használatával bármilyen kérelmet a rendszer elutasítja a szolgáltatás által.

<br/>

| Verzió | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [2.4.3](#2.4.3) |Mar 5, 2019|--- |
| [2.4.2](#2.4.2) |Mar 1, 2019|--- |
| [2.4.1](#2.4.1) |Február 20, 2019|--- |
| [2.4.0](#2.4.0) |Február 8., 2019|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |Február 4., 2019|--- |
| [2.3.1](#2.3.1) |Jan. január 15., 2019|--- |
| [2.3.0](#2.3.0) |2018. november 29.|--- |
| [2.2.2](#2.2.2) |2018. november 8.|--- |
| [2.2.1](#2.2.1) |2018. november 2.|--- |
| [2.2.0](#2.2.0) |2018. szeptember 22.|--- |
| [2.1.0](#2.1.0) |2018. szeptember 5.|--- |
| [2.0.1](#2.0.1) |2018. augusztus 16.|--- |
| [2.0.0](#2.0.0) |2018. június 20.|--- |
| [1.0.2](#1.0.2) |2018. május 18.|--- |
| [1.0.1](#1.0.1) |2018. április 20.|--- |
| [1.0.0](#1.0.0) |2018. február 27.|--- |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Cosmos DB kapcsolatos további információkért lásd: [a Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján.

