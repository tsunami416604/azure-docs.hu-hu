---
title: 'Azure Cosmos DB: SQL Java API, SDK &-erőforrások'
description: Ismerkedjen meg az SQL Java API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a Azure Cosmos DB SQL Java SDK egyes verzióiban végrehajtott módosításokat.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: eb8110c953787d1ebf7f01037808ee77db2eb8b4
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934162"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK for SQL API: kibocsátási megjegyzések és erőforrások
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

Az SQL API Java SDK támogatja a szinkron műveleteket. Aszinkron támogatáshoz használja az [SQL API aszinkron Java SDK](sql-api-sdk-async-java.md)-t. 

| |  |
|---|---|
|**SDK letöltése**|[Maven 3](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-dokumentáció**|[Java API-referenciák dokumentációja](/java/api/com.microsoft.azure.documentdb)|
|**Közreműködés az SDK-val**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Első lépések**|[Ismerkedés a Java SDK-val](sql-api-java-get-started.md)|
|**Webalkalmazás-oktatóanyag**|[Webalkalmazás-fejlesztés Azure Cosmos DB](sql-api-java-application.md)|
|**Minimális támogatott futtatókörnyezet**|[Java Development Kit (JDK) 7 +](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name244244"></a><a name="2.4.4"/>2.4.4
* Az optimalizált partíciós kulcs tartományának gyorsítótár-frissítése.

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Az optimalizált gyűjtemények gyorsítótára frissül.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* A belső kivételi üzenet kérés diagnosztikai karakterláncból való beolvasásának támogatása hozzáadva.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* A PartitionKeyDefinition-on bevezetett API-verzió.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* A közvetlen üzemmódhoz külön időtúllépési támogatás lett hozzáadva.

### <a name="a-name223223"></a>2\.2.3 <a name="2.2.3"/>
* NULL hibaüzenetet fogyaszt a szolgáltatástól, és a dokumentum-ügyfél kivételt hoz létre.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Szoftvercsatorna-kapcsolat fejlesztése, SoKeepAlive hozzáadása alapértelmezett True (igaz).

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* A kérelem diagnosztikai karakterláncának támogatása hozzáadva.

### <a name="a-name213213"></a>2\.1.3 <a name="2.1.3"/>
* Rögzített hiba a PartitionKey for hash v2 esetében.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* Összetett indexek támogatása hozzáadva.
* Kijavítva a hiba a globális Endpoint Managerben a frissítés kényszerítéséhez.
* Rögzített hiba az előfeltételekkel rendelkező upsert közvetlen módban.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Kijavítva a hiba az átjáró címeinek gyorsítótárában.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* A többrégiós írási támogatás közvetlen üzemmódhoz lett hozzáadva.
* A ServiceUnavailable-kivételként kiváltott IOExceptions kezelése a proxytól.
* Kijavítva egy hiba a végpont-felderítési újrapróbálkozási házirendben.
* Javítva lett egy hiba, amely biztosítja, hogy a rendszer null mutató kivételeket ne dobjon a BaseDatabaseAccountConfigurationProvider.
* Kijavított egy hibát annak biztosítására, hogy a QueryIterator nem ad vissza null értéket.
* Kijavítva egy hiba a nagyméretű PartitionKey engedélyezése érdekében

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* A többrégiós írási támogatás az átjáró üzemmódhoz lett hozzáadva.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Hiba javítva a lekérdezés olvasási partíciós kulcsának tartományában.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Kijavítva egy hiba a folytatási jogkivonat fejlécének DirectHttps módban való beállításakor.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* A folyamatos átviteli feladatátvétel támogatása.
* Egyéni metaadatok támogatása.
* Továbbfejlesztett munkamenet-kezelési logika.
* Kijavítva egy hiba a partíciós kulcs tartományának gyorsítótárában.
* Rögzített NPE hiba közvetlen módban.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Új támogatás az egyedi indexekhez.
* A folytatási token méretének korlátozása a hírcsatornában – beállítások.
* Kijavítva egy hiba a JSON-Szerializálásban (timestamp).
* Kijavítva egy hiba a JSON-Szerializálásban (Enum).
* Függőség a com. fasterxml. Jackson. Core: Jackson-koordinátamezőket 2.9.5-re frissítve.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Továbbfejlesztett kapcsolatok készletezése közvetlen módban.
* Továbbfejlesztett visszahívási fejlesztések a nem OrderBy kereszt partíciós lekérdezésekhez.
* Továbbfejlesztett UUID-generáció.
* Továbbfejlesztett munkamenet-konzisztencia-logika.
* Többsokszöges támogatás hozzáadva.
* A partíciós kulcs tartományának statisztikáinak támogatása a gyűjteményhez.
* Kijavított egy hibát a többrégiós támogatásban.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Továbbfejlesztett JSON-szerializálási teljesítmény.
* Az SDK-verzióhoz a Azure Cosmos DB Emulator legújabb verziójára van szükség, amely letölthető a https://aka.ms/cosmosdb-emulator ról.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* A Microsoft barátok kódtárainak belső módosításai.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Kijavítva egy hiba az egyes partíciós kulcsok tartományának olvasásakor.
* Kijavított egy problémát a ResourceID-elemzésben, amely a rövid névvel rendelkező adatbázist érinti.
* A partíciós kulcs kódolása okozta a probléma kijavítását.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Kritikus hibajavítások kérelmek feldolgozásához a partíciók felosztásakor.
* Kijavított egy problémát az erős és a BoundedStaleness konzisztencia szintjével.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Egy új, ConsistentPrefix nevű konzisztencia-szint támogatása.
* Kijavítva egy hiba a gyűjtemény olvasásakor munkamenet módban.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Engedélyezve van a particionált gyűjtemény támogatása a 2 500 RU/s értékkel, a mérete pedig 100 RU/s.
* Kijavított egy hibát a natív szerelvényben, amely NullRef kivételt okozhat bizonyos lekérdezésekben.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Kijavított egy hibát a lekérdezési motor konfigurációjában, amely kivételeket okozhat a lekérdezésekhez az átjáró módban.
* A munkamenet-tárolóban kijavított néhány hiba, amely miatt előfordulhat, hogy a "tulajdonosi erőforrás nem található" kivételt a rendszer a gyűjtemény létrehozása után azonnal kéri.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Összesítő lekérdezések támogatása (DARABSZÁM, MIN., MAX., SUM és AVG). Lásd: [összesítési támogatás](sql-query-aggregates.md).
* A módosítási hírcsatorna támogatása.
* A gyűjtési kvóta adatainak támogatása a RequestOptions. setPopulateQuotaInfo használatával.
* A tárolt eljárás parancsfájl-naplózásának támogatása a RequestOptions. setScriptLoggingEnabled használatával.
* Kijavítva a hiba, ahol a DirectHttps módban végzett lekérdezés nem válaszol a szabályozási hibák esetén.
* Rögzített hiba a munkamenet-konzisztencia módban.
* Kijavítva a hiba, amely NullReferenceException okozhat a HttpContext, ha a kérések sebessége magas.
* A DirectHttps mód jobb teljesítménye.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* A ConnectionPolicy. setProxy () API-val egyszerű ügyfél-példány-alapú proxy-támogatás lett hozzáadva.
* Hozzáadta a DocumentClient. Bezárás () API-t a DocumentClient-példány megfelelő leállításához.
* A lekérdezési teljesítmény javítása közvetlen kapcsolati módban a lekérdezési tervnek az átjáró helyett a natív szerelvényből való származtatása révén.
* Állítsa be a FAIL_ON_UNKNOWN_PROPERTIES = FALSE értéket, hogy a felhasználóknak ne kelljen megadniuk a JsonIgnoreProperties a POJO.
* A naplózás újrabontása a SLF4J használatára.
* Javítva kell néhány más hibát a konzisztencia-olvasóban.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Kijavított egy hibát a kapcsolat kezelésében, hogy megakadályozza a kapcsolatok szivárgását a közvetlen kapcsolati módban.
* Kijavított egy hibát a felső lekérdezésben, amely NullReference kivételt okozhat.
* Jobb teljesítmény a belső gyorsítótárak hálózati hívásának számának csökkentésével.
* A jobb hibaelhárítás érdekében hozzáadott állapotkódot, tevékenységazonosító és kérelem-URI-t a DocumentClientException-ben.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Javítva lett egy probléma a stabilitási kapcsolatok kezelésében.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* A BoundedStaleness konzisztencia-szintjének támogatása.
* Közvetlen kapcsolat támogatása a particionált gyűjtemények esetében a SZIFILISZi műveletekhez.
* Hiba javítva egy adatbázis SQL-sel való lekérdezése során.
* Kijavított egy hibát a munkamenet-gyorsítótárban, ahol a munkamenet-jogkivonat helytelenül állítható be.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* További támogatás a több partíciós párhuzamos lekérdezésekhez.
* A TOP/ORDER szintű támogatás hozzáadva a particionált gyűjtemények lekérdezései alapján.
* Erős konzisztencia-támogatás hozzáadva.
* A név alapú kérelmek támogatása közvetlen kapcsolat használata esetén.
* Javítva, hogy a tevékenységazonosító az összes kérelem újrapróbálkozásakor konzisztens maradjon.
* A munkamenet-gyorsítótárhoz kapcsolódó hiba javítva, amikor létrehoz egy azonos nevű gyűjteményt.
* Sokszög-és LineString-adattípusok hozzáadása a gyűjtemények indexelési szabályzatának megadásakor a Geo-kerítés térbeli lekérdezésekhez.
* A Java doc-mel kapcsolatos kijavított problémák a Javához 1,8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Kijavított egy hibát a PartitionKeyDefinitionMap az egypartíciós gyűjtemények gyorsítótárazásához, és nem végez további lekéréses particionálást.
* Kijavítva a hiba, hogy a rendszer Ne próbálkozzon újra, amikor helytelen partíciós kulcs van megadva.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* A többrégiós adatbázis-fiókok támogatása hozzáadva.
* A maximális újrapróbálkozási kísérletek és a maximális újrapróbálkozási várakozási idő testreszabására szolgáló beállításokkal automatikusan újrapróbálkozhat a szabályozott kérelmekkel.  Lásd: RetryOptions és ConnectionPolicy. getRetryOptions ().
* Elavult IPartitionResolver-alapú egyéni particionálási kód. Használjon particionált gyűjteményeket a nagyobb tárterület és átviteli sebesség érdekében.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Ismételt újrapróbálkozási szabályzat támogatása a díjszabás korlátozásához.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* További élettartam (TTL) támogatása a dokumentumokhoz.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* A [particionált gyűjtemények](partition-data.md) és a [felhasználó által definiált teljesítményszint](performance-levels.md)implementálva.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Javítva lett egy hiba a HashPartitionResolver-ben, hogy a endian a többi SDK-val konzisztensek legyenek.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Adja hozzá a kivonatoló &-tartomány partíciójának feloldóit, hogy segítséget nyújtson a több partíción lévő alkalmazások horizontális felskálázásában.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Upsert implementálása. Új upsertXXX-metódusok lettek hozzáadva a Upsert szolgáltatás támogatásához.
* Az azonosító alapú útválasztás implementálása. Nincs nyilvános API-változás, az összes változás belső.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* A kiadás kihagyva, hogy a verziószáma más SDK-k használatával legyen összehangolva

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* A térinformatikai index támogatása
* Érvényesíti az összes erőforrás azonosító tulajdonságát. Az erőforrások azonosítói nem tartalmazhatják a következőket:?,/, #, \, karakter, vagy egy szóközzel kell végződnie.
* Hozzáadja az új "index átalakítási folyamat" fejlécet a ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* A v2 indexelési házirend megvalósítása

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Kiadási és nyugdíjazási dátumok
A Microsoft legalább **12 hónappal** korábban értesítést küld az SDK kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra.

Az új funkciók és funkciók és optimalizálás csak a jelenlegi SDK-hoz adódik hozzá, ezért azt javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb SDK-verzióra.

A szolgáltatás elutasítja a kivont SDK-val Cosmos DBre irányuló kéréseket.

> [!WARNING]
> A Javához készült SQL SDK összes verziójának **1. x** verziója **2020. május 30-** án megszűnik.
> 
>

> [!WARNING]
> A Javához készült SQL SDK összes verziója, a **1.0.0** verzió előtt **, 2016. február 29-** én megszűnt.
> 
> 

<br/>

| Verzió | Kiadás dátuma | Nyugdíjazás dátuma |
| --- | --- | --- |
| [2.1.3](#2.1.3) |Mar 13, 2018 |--- |
| [2.1.2](#2.1.2) |Mar 09, 2018 |--- |
| [2.1.1](#2.1.1) |Dec. 13, 2018 |--- |
| [2.1.0](#2.1.0) |November 20, 2018 |--- |
| [2.0.0](#2.0.0) |Szeptember 21., 2018 |--- |
| [1.16.4](#1.16.4) |Szeptember 10., 2018 |2020. május 30. |
| [1.16.3](#1.16.3) |Szeptember 9., 2018 |2020. május 30. |
| [1.16.2](#1.16.2) |2018. június 29. |2020. május 30. |
| [1.16.1](#1.16.1) |2018. május 16. |2020. május 30. |
| [1.16.0](#1.16.0) |Március 15., 2018 |2020. május 30. |
| [1.15.0](#1.15.0) |November 14., 2017 |2020. május 30. |
| [1.14.0](#1.14.0) |Október 28., 2017 |2020. május 30. |
| [1.13.0](#1.13.0) |Augusztus 25., 2017 |2020. május 30. |
| [1.12.0](#1.12.0) |2017. július 11. |2020. május 30. |
| [1.11.0](#1.11.0) |2017. május 10. |2020. május 30. |
| [1.10.0](#1.10.0) |2017. március 11. |2020. május 30. |
| [1.9.6](#1.9.6) |Február 21., 2017 |2020. május 30. |
| [1.9.5](#1.9.5) |2017. január 31. |2020. május 30. |
| [1.9.4](#1.9.4) |November 24., 2016 |2020. május 30. |
| [1.9.3](#1.9.3) |Október 30-ig 2016 |2020. május 30. |
| [1.9.2](#1.9.2) |Október 28., 2016 |2020. május 30. |
| [1.9.1](#1.9.1) |Október 26., 2016 |2020. május 30. |
| [1.9.0](#1.9.0) |Október 03., 2016 |2020. május 30. |
| [1.8.1](#1.8.1) |Június 30., 2016 |2020. május 30. |
| [1.8.0](#1.8.0) |Június 14., 2016 |2020. május 30. |
| [1.7.1](#1.7.1) |Április 30., 2016 |2020. május 30. |
| [1.7.0](#1.7.0) |Április 27., 2016 |2020. május 30. |
| [1.6.0](#1.6.0) |Március 29., 2016 |2020. május 30. |
| [1.5.1](#1.5.1) |December 31., 2015 |2020. május 30. |
| [1.5.0](#1.5.0) |December 04., 2015 |2020. május 30. |
| [1.4.0](#1.4.0) |Október 05.2015 |2020. május 30. |
| [1.3.0](#1.3.0) |Október 05.2015 |2020. május 30. |
| [1.2.0](#1.2.0) |Augusztus 05, 2015 |2020. május 30. |
| [1.1.0](#1.1.0) |Július 9., 2015 |2020. május 30. |
| 1.0.1 |2015. május 12. |2020. május 30. |
| [1.0.0](#1.0.0) |2015. április 7. |2020. május 30. |
| 0.9.5 – előbérlet |Mar 09, 2015 |Február 29., 2016 |
| 0.9.4 – előbérlet |Február 17., 2015 |Február 29., 2016 |
| 0.9.3 – előbérlet |2015. január 13. |Február 29., 2016 |
| 0.9.2 – előbérlet |December 19., 2014 |Február 29., 2016 |
| 0.9.1 – előbérlet |December 19., 2014 |Február 29., 2016 |
| 0.9.0 – előbérlet |2014. december 10. |Február 29., 2016 |

## <a name="faq"></a>Gyakori kérdések
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még:
További információ a Cosmos DBről: [Microsoft Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapja.

