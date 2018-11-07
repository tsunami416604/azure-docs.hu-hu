---
title: 'Az Azure Cosmos DB: SQL aszinkron Java API, SDK és -erőforrások |} A Microsoft Docs'
description: Mindent megtudhat a SQL aszinkron Java API-t és az SDK kiadási dátum, kivezetési dátum és az Azure Cosmos DB SQL aszinkron Java SDK minden verziója közötti végzett módosításokat.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/02/2018
ms.author: moderakh
ms.openlocfilehash: 80beeda607c83bc2a583c140779766952efcc568
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252870"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Az Azure Cosmos DB aszinkron Java SDK SQL API-hoz: kibocsátási megjegyzések és erőforrások
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
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

Az SQL API aszinkron Java SDK azáltal, hogy az aszinkron műveletek támogatása az eltér az SQL API-t a Java SDK-t a [Netty könyvtár](http://netty.io/). A már meglévő [SQL API-t a Java SDK](sql-api-sdk-java.md) nem támogatja az aszinkron műveleteket. 

<table>

<tr><td>**SDK letöltése**</td><td>[Maven 3](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**API-dokumentáció**</td><td>[Java API dokumentációja](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client?view=azure-java-stable)</td></tr>

<tr><td>**Hozzájárul az SDK-t**</td><td>[GitHubon](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Első lépések**</td><td>[Az aszinkron Java SDK használatának első lépései](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**Kódminta**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Teljesítménnyel kapcsolatos tippek**</td><td>[Github információs](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Minimális támogatott futtatókörnyezet**</td><td>[JDK 8](https://aka.ms/azure-jdks)</td></tr>
</table></br>

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Az írási újrapróbálkozási elérési út NPE hibája kijavítva.
* A végpont-kezelés NPE hibája kijavítva.
* Frissítve a sebezhető függőségek ([github #68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Támogatás hozzáadva a hibaelhárítási Netty hálózati naplózás.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Többrégiós írási támogatása.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Támogatás hozzáadva a Proxy.
* Erőforrás-engedélyezési jogkivonat támogatása.
* Kijavítva a hiba a nagyméretű partíciókulcsok kezelését ([github #63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Továbbfejlesztett dokumentációját.
* Az SDK átstrukturálása részletesebb modulokat.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Kijavítva a hiba nem angol nyelvű területi beállításokhoz ([github #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* A hozzáadott segédmetódusokat ütközés erőforrásban.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Jackson miatt a teljesítmény javítása érdekében és licencelési org.json függőségi lecserélve ([github #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
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
* Kijavítva a hiba, a Json-szerializálás timestamp ([github #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Kijavítva a hiba az enumerálás a Json-szerializálást.
* Kijavítva a hiba a 2MB méretű dokumentumok kezelése ([github #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Függőségi com.fasterxml.jackson.core:jackson-databind 2.9.5 egy hiba miatt frissített ([jackson-databind: github #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Egy hiba miatt 0.8.0.17 frissített rxjava – kiegészítő funkciók a függőségi ([rxjava – kiegészítő funkciók: github #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* A metaadatok leírását frissíti, hogy a beágyazott dokumentáció a rest-tel pom-fájljába.
* Szintaxis fokozása ([github #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Biztonsági-nyomás támogatása a lekérdezésben.
* A lekérdezés kulcstartományhoz partícióazonosító támogatása.
* Javítsa ki, hogy nagyobb folytatási kérés fejlécében (#24 bugfix github).
* Annak biztosítása érdekében a JVM 4.1.22.Final frissített netty függőségi leállítja a főszálban befejeződése után.
* Javítsa ki a tokenu relace passing fő erőforrások olvasásakor elkerülése érdekében.
* További példák hozzá.
* További teljesítménymérési forgatókönyvek hozzá.
* Rögzített Java fejlécfájlok megfelelő java doc létrehozása céljából.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Általánosan elérhető SDK-val nem blokkoló i/o-használatával teljes körű támogatása a [Netty könyvtár](http://netty.io/) átjáró módban. 

## <a name="release-and-retirement-dates"></a>Kiadás és kivezetési dátuma
A Microsoft legalább értesítést küldenek **12 hónapig** kivonása egy SDK-t kiegyenlítse az a és újabb támogatott verzióra váltás előtt.

Új szolgáltatások és funkciók és optimalizálási lehetőségek csak hozzá az aktuális SDK-hoz. Ezért ajánlott, hogy mindig a legújabb SDK verzióra frissít, a lehető leghamarabb.

Cosmos DB-hez a kivont SDK használatával bármilyen kérelmet a rendszer elutasítja a szolgáltatás által.

<br/>

| Verzió | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [2.2.1](#2.1.0) |2018. november 2.|--- |
| [2.2.0](#2.2.1) |2018. szeptember 22.|--- |
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

