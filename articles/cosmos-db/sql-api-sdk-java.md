---
title: 'Azure Cosmos DB: SQL Java API, SDK & erőforrások'
description: Tudjon meg mindent az SQL Java API-ról és az SDK-ról, beleértve a kiadási dátumokat, a megszüntetési dátumokat és az Azure Cosmos DB SQL Java SDK egyes verziói között végrehajtott módosításokat.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 02/21/2020
ms.author: sngun
ms.openlocfilehash: 514982727509788918c159e07f8061962df32336
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77558928"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK SQL API-hoz: Kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Többi](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó - Java](sql-api-sdk-bulk-executor-java.md)

Az SQL API Java SDK támogatja a szinkron műveleteket. Az aszinkron támogatáshoz használja az [SQL API Async Java SDK-t.](sql-api-sdk-async-java.md) 

| |  |
|---|---|
|**SDK letöltése**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-dokumentáció**|[Java API referenciadokumentációja](/java/api/com.microsoft.azure.documentdb)|
|**Hozzájárulás az SDK-hoz**|[Github](https://github.com/Azure/azure-documentdb-java/)|
|**Első lépések**|[Ismerkedés a Java SDK-val](sql-api-java-get-started.md)|
|**Webalkalmazás oktatóanyaga**|[Webalkalmazás-fejlesztés az Azure Cosmos DB-vel](sql-api-java-application.md)|
|**Minimálisan támogatott futásidő**|[Java Fejlesztői készlet (JDK) 7+](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="247"></a><a name="2.4.7"/>2.4.7
* Javítja a kapcsolatkészlet időtúllépési problémája.
* Javítja az auth token frissítés belső újrapróbálkozások.

### <a name="246"></a><a name="2.4.6"/>2.4.6
* Frissítve a megfelelő ügyféloldali replika házirend-címke az databaseAccount-on, és az databaseAccount konfigurációja beolvasást eredményezett a gyorsítótárból.

### <a name="245"></a><a name="2.4.5"/>2.4.5
* Az érvénytelen partíciókulcs-tartományhibáj elkerülése, ha a felhasználó pkRangeId azonosítót ad meg.

### <a name="244"></a><a name="2.4.4"/>2.4.4
* Az optimalizált partíciókulcs-tartomány gyorsítótára frissül.
* Javítja azt a forgatókönyvet, amelyben az SDK nem szórakoztatja a partíciófelosztási emlékeztetőt a kiszolgálótól, és helytelen ügyféloldali útválasztási gyorsítótárak frissítését eredményezi.

### <a name="242"></a><a name="2.4.2"/>2.4.2
* Az optimalizált gyűjteménygyorsítótár frissül.

### <a name="241"></a><a name="2.4.1"/>2.4.1
* Hozzáadott támogatás a belső kivételüzenet kérésdiagnosztikai karakterláncból való lekéréséhez.

### <a name="240"></a><a name="2.4.0"/>2.4.0
* Bevezetett verzió api a PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Külön időmeghosszabbítási támogatás hozzáadva a közvetlen módhoz.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Null hibaüzenet használata a szolgáltatásból és a dokumentumügyfél-kivétel készítése.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* A szoftvercsatorna-kapcsolat javítása, a SoKeepAlive alapértelmezett értéke true hozzáadása.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Hozzáadott kérelem diagnosztikai karakterlánc támogatása.

### <a name="213"></a><a name="2.1.3"/>2.1.3
* Javítva a PartitionKey kivonatoló V2 hibája.

### <a name="212"></a><a name="2.1.2"/>2.1.2
* Az összetett indexek támogatása hozzáadva.
* Javítva a globális végpontkezelő ben a frissítés kényszerítéséhez.
* Rögzített hiba upserts az előfeltételek közvetlen módban.

### <a name="211"></a><a name="2.1.1"/>2.1.1
* Javítva a hiba az átjáró címgyorsítótárában.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Többrégiós írási támogatás hozzáadva a közvetlen módhoz.
* Hozzáadott támogatás kezelése IOExceptions for ServiceUnavailable kivételek, egy proxy.
* Javítva egy hiba az endpoint discovery retry házirendben.
* Javítva egy hiba, amely biztosítja, hogy a BaseDatabaseAccountConfigurationProvider ne legyen null mutatókivétel.
* Javítva egy hiba, amely biztosítja, hogy a QueryIterator ne ad vissza null értékeket.
* Javítva egy hiba, hogy biztosítsa a nagy PartitionKey engedélyezett

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Többrégiós írási támogatás hozzáadva az átjáró módhoz.

### <a name="1164"></a><a name="1.16.4"/>1.16.4
* Javítva egy hiba a Read partition Key tartományok egy lekérdezést.

### <a name="1163"></a><a name="1.16.3"/>1.16.3
* Hiba történt a folytatási token fejlécméretének DirectHttps módban történő beállításában.

### <a name="1162"></a><a name="1.16.2"/>1.16.2
* Hozzáadott streamelési feladatátvételtámogatása.
* Az egyéni metaadatok támogatása hozzáadva.
* Továbbfejlesztett munkamenet-kezelési logika.
* Javítva egy hiba a partíciókulcs-tartomány gyorsítótárában.
* Javítva egy NPE hiba közvetlen módban.

### <a name="1161"></a><a name="1.16.1"/>1.16.1
* Az egyedi index támogatása hozzáadva.
* A folytatási token méretének korlátozásához nyújtott támogatás a hírcsatorna-beállításokban.
* Javítva egy hiba a Json Szerializálás (időbélyeg) hibája.
* Javítva a Json Szerializálás (enum) hibája.
* A com.fasterxml.jackson.core:jackson-databind függősége 2.9.5-re frissítve.

### <a name="1160"></a><a name="1.16.0"/>1.16.0
* Továbbfejlesztett kapcsolatkészletezés közvetlen módban.
* Továbbfejlesztett előzetes betöltési fejlesztés a nem rendezett partíciók közötti lekérdezéshez.
* Továbbfejlesztett UUID-generáció.
* Továbbfejlesztett munkamenet-konzisztencialogika.
* A multipoligon támogatása hozzáadva.
* Hozzáadott támogatása partition key range statistics for Collection.
* Javítva egy hiba a többrégiós támogatásban.

### <a name="1150"></a><a name="1.15.0"/>1.15.0
* Továbbfejlesztett Json szerializálási teljesítmény.
* Ez az SDK-verzió az Azure Cosmos DB Emulator legújabb verzióját igényli, amely letölthető a alkalmazásból. https://aka.ms/cosmosdb-emulator

### <a name="1140"></a><a name="1.14.0"/>1.14.0
* Belső módosítások a Microsoft baráti könyvtáraiban.

### <a name="1130"></a><a name="1.13.0"/>1.13.0
* Kijavítottuk az egypartíciós kulcstartományok olvasásának egyik kiírása kori problémát.
* Kijavítottunk egy problémát a ResourceID elemzésben, amely a rövid nevű adatbázist érinti.
* Kijavítottunk egy problémát, amelyet a partíciókulcs kódolása okozott.

### <a name="1120"></a><a name="1.12.0"/>1.12.0
* Kritikus hibajavítások a partíciófelosztás során a feldolgozás kéréséhez.
* Kijavítottuk az Erős és a BoundedStaleness konzisztenciaszintekkel kapcsolatos problémát.

### <a name="1110"></a><a name="1.11.0"/>1.11.0
* A Konzisztens előtag nevű új konzisztenciaszint támogatása hozzáadva.
* Javítva egy hiba az olvasási gyűjteményben munkamenet módban.

### <a name="1100"></a><a name="1.10.0"/>1.10.0
* A particionált gyűjtemény támogatása 2500 RU/s-os alacsony, 100 RU/s-os lépésekben skálázható.
* Javítva egy hiba a natív kódösszeállításban, amely nullref kivételt okozhat egyes lekérdezésekben.

### <a name="196"></a><a name="1.9.6"/>1.9.6
* Kijavítottunk egy hibát a lekérdezési motor konfigurációjában, amely kivételeket okozhat az átjáró módban lévő lekérdezéseknél.
* Kijavítottunk néhány hibát a munkamenet-tárolóban, amelyek a gyűjtemény létrehozása után azonnal "Tulajdonos erőforrás nem található" kivételt okozhatnak a kérelmeknél.

### <a name="195"></a><a name="1.9.5"/>1.9.5
* Az összesítési lekérdezések támogatása hozzáadva (DARAB, MIN, MAX, SZUM és AVG). Lásd: [Aggregation támogatás](sql-query-aggregates.md).
* Hozzáadott támogatja a változás feed.
* A requestoptions.setPopulateQuotaInfo-n keresztül a gyűjteménykvóta-információk támogatása hozzáadva.
* A tárolt eljárásparancsfájlok requestoptions.setScriptLoggingEnabled szolgáltatáson keresztüli naplózásának támogatása hozzáadva.
* Kijavítottunk egy hibát, amely miatt a DirectHttps módban leadott lekérdezés nem válaszol, ha szabályozási hibák lépnek fel.
* Javítva egy hiba a munkamenet konzisztencia üzemmódjában.
* Kijavítottunk egy hibát, amely nullhivatkozásexceptiont okozhat a HttpContext környezetben, ha a kérelmek aránya magas.
* A DirectHttps mód jobb teljesítménye.

### <a name="194"></a><a name="1.9.4"/>1.9.4
* Egyszerű ügyfélpéldány-alapú proxytámogatás hozzáadva a ConnectionPolicy.setProxy() API-val.
* DocumentClient.close() API-t adott hozzá a DocumentClient példány megfelelő leállításához.
* Továbbfejlesztett lekérdezési teljesítmény közvetlen kapcsolatmódban a lekérdezési terv a natív szerelvény helyett az átjáró.
* Állítsa be FAIL_ON_UNKNOWN_PROPERTIES = false értéket, így a felhasználóknak nem kell meghatározniuk a JsonIgnoreProperties tulajdonságot a POJO-ban.
* Refactored fakitermelés használata SLF4J.
* Javítva néhány más hibákkonkonzisztencia olvasó.

### <a name="193"></a><a name="1.9.3"/>1.9.3
* Javítva egy hiba a kapcsolatkezelésben, hogy megakadályozza a kapcsolat szivárgását a közvetlen kapcsolati módban.
* Javítva egy hiba a TOP lekérdezésben, ahol nullreferencia kivételt okozhat.
* Jobb teljesítmény a belső gyorsítótárak hálózati hívásainak számának csökkentésével.
* Állapotkód, ActivityID és REQUEST URI hozzáadása a DocumentClientException programban a jobb hibaelhárítás érdekében.

### <a name="192"></a><a name="1.9.2"/>1.9.2
* Kijavítottunk egy problémát a kapcsolatkezelésben a stabilitás érdekében.

### <a name="191"></a><a name="1.9.1"/>1.9.1
* A BoundedStaleness konzisztenciaszint támogatása hozzáadva.
* A particionált gyűjtemények CRUD-műveleteinek közvetlen kapcsolattámogatása.
* Javítva egy hiba az SQL-t tartalmazó adatbázis lekérdezésekor.
* Javítva egy hiba a munkamenet-gyorsítótárban, ahol a munkamenet-token helytelenül állítható be.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* A keresztpartíciós párhuzamos lekérdezések támogatása hozzáadva.
* A TOP/ORDER BY lekérdezések támogatása hozzáadva a particionált gyűjtemények számára.
* Az erős konzisztencia támogatása hozzáadva.
* A névalapú kérelmek támogatása közvetlen kapcsolat használata esetén.
* Javítva, hogy az ActivityId konzisztens maradjon az összes kérelem újrapróbálkozásakor.
* Javítva a munkamenet-gyorsítótárral kapcsolatos hiba, amikor egy azonos nevű gyűjteményt hozott létre.
* Sokszög és LineString DataTypes hozzáadása, míg a gyűjtemény indexelési házirend geo-kerítés térbeli lekérdezések.
* Javítottuk a Java Doc for Java 1.8-as problémáit.

### <a name="181"></a><a name="1.8.1"/>1.8.1
* Javítva a PartitionKeyDefinitionMap egyik hibája az egypartíciós gyűjtemények gyorsítótárazásához, és nem történt meg további lehívási partíciókulcs-kérelmek.
* Javítva egy hiba, hogy ne próbálja meg újra, ha helytelen partíciókulcs-érték van megadva.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Hozzáadva a többrégiós adatbázisfiókok támogatása.
* A sávszélesség-szabályozási kérelmek automatikus újrapróbálkozásának támogatása a maximális újrapróbálkozási kísérletek testreszabására és a maximális újrapróbálkozási várakozási idő re testreszabására szolgáló beállításokkal.  Lásd: Újrapróbálkozási beállítások és ConnectionPolicy.getRetryOptions().
* Elavult IPartitionResolver alapú egyéni particionálási kód. Kérjük, használja a particionált gyűjtemények a nagyobb tárolási és átviteli.

### <a name="171"></a><a name="1.7.1"/>1.7.1
* Újrapróbálkozási házirend támogatása a sebességkorlátozáshoz.  

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Hozzáadott idő az élő (TTL) támogatása dokumentumok.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* [Particionált gyűjtemények](partition-data.md) és [a felhasználó által definiált teljesítményszintek.](performance-levels.md)

### <a name="151"></a><a name="1.5.1"/>1.5.1
* Javítva egy hiba a HashPartitionResolver-ben, hogy kivonatértékeket hozzon létre a kis-endian-ban, hogy összhangban legyen más SDK-kkal.

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Kivonatoló & tartomány partíciófeloldók, hogy segítse az alkalmazások több partíción átnyúló skálázás.

### <a name="140"></a><a name="1.4.0"/>1.4.0
* Valósítsa meg upsert. Új upsertXXX módszerek hozzá, hogy támogassa Upsert funkció.
* Azonosítóalapú útválasztás megvalósítása. Nincs nyilvános API-módosítás, minden belső módosítás.

### <a name="130"></a><a name="1.3.0"/>1.3.0
* A kiadás kimarad, hogy a verziószám a többi SDK-val egy vonalban jelenjen meg

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Támogatja a térinformatikai indexet
* Az összes erőforrás azonosítótulajdonságának ellenőrzése. Az erőforrások azonosítói nem tartalmazhatnak ?, /, #, karaktereket, \, és szóközökkel végződhetnek.
* Új fejléc "index átalakítási folyamat" a ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* V2 indexelési házirendet valósít meg

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Kiadási és nyugdíjazási dátumok
A Microsoft legalább **12 hónappal** az SDK kicsomagolása előtt értesítést küld az újabb/támogatott verzióra való áttérés zökkenőmentessé.

Az új funkciók és funkciók és optimalizálások csak az aktuális SDK-hoz kerülnek hozzáadásra, ezért javasoljuk, hogy mindig a lehető leghamarabb frissítsen a legújabb SDK-verzióra.

A Cosmos DB-nek a rendszer egy kivisszavonult SDK-t használó kérését a szolgáltatás elutasítja.

> [!WARNING]
> **2020. május 30-án**az SQL SDK java-i verziójának **1.x** verziója megszűnik.
> 
>

> [!WARNING]
> **2016. február 29-én**az SQL SDK Java-verziójának **az 1.0.0-s** verzió előtti összes verziója kivisszavonult.
> 
> 

<br/>

| Verzió | Megjelenési dátum | Nyugdíjazás dátuma |
| --- | --- | --- |
| [2.4.7](#2.4.7) |Február 20, 2020 |--- |
| [2.4.6](#2.4.6) |2020. január 24. |--- |
| [2.4.5](#2.4.5) |2019. november 10., 2019. |--- |
| [2.4.4](#2.4.4) |2019. október 24. |--- |
| [2.4.2](#2.4.2) |2019. szeptember 26. |--- |
| [2.4.1](#2.4.1) |2019. július 18., 2019. |--- |
| [2.4.0](#2.4.0) |2019. május 04.May 04, 2019 |--- |
| [2.3.0](#2.3.0) |2019. április 24. |--- |
| [2.2.3](#2.2.3) |2019. április 16. |--- |
| [2.2.2](#2.2.2) |2019. április 05. |--- |
| [2.2.0](#2.2.0) |Már 27, 2019 |--- |
| [2.1.3](#2.1.3) |2019. március 13. |--- |
| [2.1.2](#2.1.2) |2019. március 09. |--- |
| [2.1.1](#2.1.1) |2018. december 13., 2018. |--- |
| [2.1.0](#2.1.0) |2018. november 20. |--- |
| [2.0.0](#2.0.0) |2018. szeptember 21. |--- |
| [1.16.4](#1.16.4) |2018. szeptember 10. |2020. május 30. |
| [1.16.3](#1.16.3) |2018. szeptember 09. |2020. május 30. |
| [1.16.2](#1.16.2) |2018. június 29.June 29, 2018 |2020. május 30. |
| [1.16.1](#1.16.1) |2018. május 16.May 16, 2018 |2020. május 30. |
| [1.16.0](#1.16.0) |2018. március 15.March 15, 2018 |2020. május 30. |
| [1.15.0](#1.15.0) |2017. november 14.Nov 14, 2017 |2020. május 30. |
| [1.14.0](#1.14.0) |2017. október 28. |2020. május 30. |
| [1.13.0](#1.13.0) |2017. augusztus 25.August 25, 2017 |2020. május 30. |
| [1.12.0](#1.12.0) |2017. július 11. |2020. május 30. |
| [1.11.0](#1.11.0) |2017. május 10. |2020. május 30. |
| [1.10.0](#1.10.0) |2017. március 11.March 11, 2017 |2020. május 30. |
| [1.9.6](#1.9.6) |2017. február 21.February 21, 2017 |2020. május 30. |
| [1.9.5](#1.9.5) |2017. január 31., az a hét, aki nem tudom, hogy mi történt. |2020. május 30. |
| [1.9.4](#1.9.4) |2016. november 24. |2020. május 30. |
| [1.9.3](#1.9.3) |2016. október 30. |2020. május 30. |
| [1.9.2](#1.9.2) |2016. október 28. |2020. május 30. |
| [1.9.1](#1.9.1) |2016. október 26. |2020. május 30. |
| [1.9.0](#1.9.0) |2016. október 03. |2020. május 30. |
| [1.8.1](#1.8.1) |2016. június 30. |2020. május 30. |
| [1.8.0](#1.8.0) |2016. június 14. |2020. május 30. |
| [1.7.1](#1.7.1) |2016. április 15. |2020. május 30. |
| [1.7.0](#1.7.0) |2016. április 27. |2020. május 30. |
| [1.6.0](#1.6.0) |2016. március 29. |2020. május 30. |
| [1.5.1](#1.5.1) |2015. augusztus 25. |2020. május 30. |
| [1.5.0](#1.5.0) |2015. augusztus 25. |2020. május 30. |
| [1.4.0](#1.4.0) |2015. augusztus 25. |2020. május 30. |
| [1.3.0](#1.3.0) |2015. augusztus 25. |2020. május 30. |
| [1.2.0](#1.2.0) |2015. augusztus 05. |2020. május 30. |
| [1.1.0](#1.1.0) |2015. július 09. |2020. május 30. |
| 1.0.1 |2015. május 12. |2020. május 30. |
| [1.0.0](#1.0.0) |2015. augusztus 25. |2020. május 30. |
| 0.9.5-prelease |2015. március 09. |2016. február 29. |
| 0.9.4-prelease |2015. február 17. |2016. február 29. |
| 0.9.3-prelease |2015. augusztus 25. |2016. február 29. |
| 0.9.2-prelease |2014. január 19. |2016. február 29. |
| 0.9.1-prelease |2014. január 19. |2016. február 29. |
| 0.9.0-prelease |2014. december 10. |2016. február 29. |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB szolgáltatásról a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatáslapján olvashat bővebben.

