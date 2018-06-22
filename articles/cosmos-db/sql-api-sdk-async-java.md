---
title: 'Azure Cosmos DB: SQL aszinkron Java API-t, az SDK és erőforrások |} Microsoft Docs'
description: Tudnivalók az SQL aszinkron Java API-t és SDK, beleértve a kiadási dátum, használatból való kivonást dátumok és az Azure Cosmos DB SQL aszinkron Java SDK verziói között végrehajtott módosításokat.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 06/20/2018
ms.author: sngun
ms.openlocfilehash: e4a3b3a482f56065c54525a4d9cd7971f50f5b2a
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300679"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB aszinkron Java SDK API-SQL: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítás adatcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

A SQL API aszinkron Java SDK abban tér el az SQL API Java SDK-támogatása az aszinkron műveletek megadásával a [Netty könyvtár](http://netty.io/). A már meglévő [SQL API Java SDK](sql-api-sdk-java.md) nem támogatja az aszinkron műveletek. 

<table>

<tr><td>**SDK letöltése**</td><td>[Maven 3](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**API-JÁNAK dokumentációja**</td><td>[Java API-referenciadokumentáció](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Hozzájárul az SDK**</td><td>[GitHubon](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Első lépések**</td><td>[Ismerkedés az aszinkron Java SDK-val](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**kódminta**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Teljesítménnyel kapcsolatos tippek**</td><td>[Github-fontos](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Minimális támogatott futásidejű**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Org.json függőségi helyébe jackson miatt a teljesítmény szempontjából és licencelési ([github #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Eltávolított elavult OfferV2 osztály.
* A hozzáadott elérőmetódust ajánlat osztályhoz átviteli tartalomhoz.
* Bármely dokumentum és az erőforrások vissza egy jackson objektumtípus megváltozott org.json típusok visszaadó metódus.
* Írja be a getObject(.) metódus osztályok kibővítése JsonSerializable megváltozott egy jackson ObjectNode vissza.
* getCollection(.) metódus módosult az gyűjtemény a ObjectNode vissza.
* A org.json.JSONObject arg. eltávolított JsonSerializable alosztályok konstruktorok
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) most behúzás két szóközt használja.
  
### <a name="a-name102102"></a><a name="1.0.2"/>amely az 1.0.2-esnél újabb
* Egyedi Index házirend támogatása.
* Támogatja a válasz folytatási jogkivonat mérete adatcsatorna beállítások korlátozza.
* A kereszt-Partíciólekérdezés partíció vegyes támogatása.
* Programhiba rögzített Json Timestamp típusú szerializálási ([github #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Programhiba parancs a következő felsorolás a Json-szerializálást.
* Rögzített 2MB méretű dokumentumok kezelése hiba ([github #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* A függőségi com.fasterxml.jackson.core:jackson-databind frissítve 2.9.5 programhiba okozza az ([jackson-databind: #1599 github](https://github.com/FasterXML/jackson-databind/issues/1599))
* Egy hiba miatt 0.8.0.17 frissített rxjava-kiegészítő funkciók függőség ([rxjava-kiegészítő funkciók: #30 github](https://github.com/davidmoten/rxjava-extras/issues/30)).
* A metaadatok leírását a dokumentáció a többi beágyazva frissített pom fájlt.
* Szintaxis fokozása ([github #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Biztonsági-nyomás támogatja a lekérdezést.
* Partícióazonosító legfontosabb tartomány a lekérdezés támogatása.
* Javítsa ki, hogy lehetővé tegyék a nagyobb folytatási kérelem fejléce (bugfix github #24).
* JVM biztosításához 4.1.22.Final frissített netty függőségi leállítja a fő szálnak befejeződése után.
* Javítsa ki a munkameneti jogkivonat átadása a fő erőforrások olvasásakor elkerülése érdekében.
* További példák hozzá.
* További összehasonlítási forgatókönyvek hozzá.
* Rögzített Java fejlécfájlok megfelelő java doc létrehozása céljából.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK-val nem blokkoló IO használatával végpont támogatása a [Netty könyvtár](http://netty.io/) átjáró módban. 

## <a name="release-and-retirement-dates"></a>Kiadás és a használatból való kivonást dátuma
Microsoft legalább értesítést küldenek **12 hónapon keresztül** SDK eltávolítása érdekében vagy újabb támogatott verzióra való áttérés előtt.

Az aktuális SDK csak hozzáadni a új szolgáltatások és funkciók és optimalizálási lehetőségeit. Ezért ajánlott, hogy mindig a legújabb SDK verzióra frissít lehető leghamarabb.

A Cosmos DB kivont SDK használatával fog kell elutasította a szolgáltatás.

<br/>

| Verzió | Kiadás dátuma | Kivezetési dátum |
| --- | --- | --- |
| [2.0.0](#2.0.0) |2018. június 20.|--- |
| [1.0.2](#1.0.2) |2018. május 18.|--- |
| [1.0.1](#1.0.1) |2018. április 20.|--- |
| [1.0.0](#1.0.0) |2018. február 27.|--- |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB kapcsolatos további információkért lásd: [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján.

