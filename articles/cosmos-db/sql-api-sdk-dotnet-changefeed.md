---
title: Azure Cosmos DB .NET Change feed Processor API, SDK kibocsátási megjegyzései
description: Ismerkedjen meg a Change feed Processor API-val és az SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a .NET Change feed Processor SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: 679f3113cddbfe13370483f2678154f4dd1f8ab2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392063"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change feed Processor SDK: letöltési és kibocsátási megjegyzések

> [!div class="op_single_selector"]
>
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * Rest (/rest/api
> * [REST erőforrás-szolgáltató] (/rest/api
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK letöltése**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-dokumentáció**|[A hírcsatorna-feldolgozó függvénytár API-referenciájának módosítása – dokumentáció](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Első lépések**|[Ismerkedés a Change feed Processor .NET SDK-val](change-feed.md)|
|**Jelenleg támogatott keretrendszer**| [Microsoft .NET-keretrendszer 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET mag](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Ha a módosítási hírcsatorna-processzort használja, tekintse meg a [.net SDK](change-feed-processor.md)legújabb 3. x verzióját, amely az SDK-ba beépített hírcsatornát módosítja. 

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="v2-builds"></a>v2 – buildek

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Új metódus és a `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` hozzá tartozó nyilvános felület lett hozzáadva `ICheckpointPartitionProcessorFactory` . Ez lehetővé teszi az interfész megvalósítását a `IPartitionProcessor` beépített ellenőrzőpont-mechanizmus használatára. Az új gyár hasonló a meglévőhöz `IPartitionProcessorFactory` , azzal a különbséggel, hogy a `Create` metódus is a `ILeaseCheckpointer` paramétert is alkalmazza.
* Ugyanahhoz a példányhoz csak a két módszer egyikét lehet `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` használni `ChangeFeedProcessorBuilder` .

### <a name="228"></a><a name="2.2.8"></a>2.2.8
* A stabilitás és a diagnosztizálás fejlesztése:
  * Az olvasási változási csatorna hosszú idő alatt történő észlelésének támogatása. Ha hosszabb időt vesz igénybe, mint a tulajdonság által megadott érték `ChangeFeedProcessorOptions.ChangeFeedTimeout` , a következő lépéseket kell elvégeznie:
    * Megszakadt a módosítási hírcsatorna olvasási művelete a problémás partíción.
    * A hírcsatorna-feldolgozó példány módosítása elveszíti a problémás bérlet tulajdonjogát. Az eldobott bérlet a következő címbérleti beolvasási lépés során lesz felhasználva, amelyet az azonos vagy eltérő módosítási hírcsatorna-feldolgozó példány hajt végre. Így a változási hírcsatorna olvasása megkezdődik.
    * Probléma jelent meg az állapotfigyelő szolgáltatásban. Az alapértelmezett egészségügyi figyelő az összes jelentett problémát a nyomkövetési naplóba küldi.
  * Új nyilvános tulajdonság hozzáadva: `ChangeFeedProcessorOptions.ChangeFeedTimeout` . Ennek a tulajdonságnak az alapértelmezett értéke 10 perc.
  * Új nyilvános enumerálási érték lett hozzáadva: `Monitoring.MonitoredOperation.ReadChangeFeed` . Ha a értéke értékre `HealthMonitoringRecord.Operation` van állítva `Monitoring.MonitoredOperation.ReadChangeFeed` , akkor az állapot azt jelzi, hogy a változási csatorna olvasási kapcsolatban van.

### <a name="227"></a><a name="2.2.7"></a>2.2.7
* Jobb terheléselosztási stratégia, ha az összes bérlet beolvasása hosszabb időt vesz igénybe, mint a bérlet lejárati időtartama, például hálózati problémák miatt:
  * Ebben a forgatókönyvben olyan terheléselosztási algoritmust használ, amely hamisan megfontolni a bérletek lejártként való megadását, ami az aktív tulajdonosoktól érkező bérletek lopását Ez a sok bérlet szükségtelen újrakiegyensúlyozását eredményezheti.
  * Ez a probléma ebben a kiadásban kerül megjavításra azáltal, hogy az újrapróbálkozások elkerülésével megtörténik a lejárt bérlet beszerzése, amely nem változott, és elhalasztotta a lejárt bérlet beszerzését a következő terheléselosztási iterációra

### <a name="226"></a><a name="2.2.6"></a>2.2.6
* A megfigyelői kivételek jobb kezelése.
* Gazdagabb információk a megfigyelői hibákról:
  * Ha egy megfigyelőt a megfigyelő ProcessChangesAsync által kiváltott kivétel miatt lezártak, a CloseAsync a ChangeFeedObserverCloseReason. ObserverError értékre állítja az OK paramétert.
  * Nyomkövetéseket adott meg a megfigyelő felhasználói kódjában található hibák azonosításához.

### <a name="225"></a><a name="2.2.5"></a>2.2.5
* A megosztott adatbázis átviteli sebességét használó gyűjtemények felosztásának támogatása.
  * Ez a kiadás javít egy problémát, amely akkor fordulhat elő, ha a gyűjtemények felosztása a megosztott adatbázis átviteli sebességével történik, ha a felosztás a partíció újrakiegyensúlyozását eredményezi, és nem kettőt. Ha ez bekövetkezik, akkor a csatorna módosítása megakadhat, ha törli a régi partíciós kulcs tartományának bérletét, és nem hoz létre új bérleteket. A probléma ebben a kiadásban van kijavítva.

### <a name="224"></a><a name="2.2.4"></a>2.2.4
* Új ChangeFeedProcessorOptions. StartContinuation tulajdonság hozzáadva, amely támogatja a változási hírcsatorna megkezdését a kérelem folytatási jogkivonata alapján. Ezt csak akkor használja a rendszer, ha a címbérleti gyűjtemény üres, vagy a bérlet nem rendelkezik Continuationtoken argumentumot használja-készlettel. A Continuationtoken argumentumot használja készlettel rendelkező bérlet gyűjteményben lévő bérletek esetében a rendszer a Continuationtoken argumentumot használja használja, és a ChangeFeedProcessorOptions. StartContinuation figyelmen kívül hagyja.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* További támogatás az egyéni tároló használatával a folytatási tokenek partíción való megőrzéséhez.
  * Egy egyéni címbérleti tároló például lehet Azure Cosmos DB bármely egyéni módon particionált bérlet gyűjteménye.
  * Az egyéni címbérleti tárolók a ChangeFeedProcessorBuilder. WithLeaseStoreManager (ILeaseStoreManager) és a ILeaseStoreManager Public Interface új bővíthetőségi ponttal rendelkezhetnek.
  * A ILeaseManager felületet több szerepkör-interfészre is átalakítja.
* Másodlagos feltörési változás: eltávolította a ChangeFeedProcessorBuilder. WithLeaseManager (ILeaseManager) bővíthetőségi pontot, használja helyette a ChangeFeedProcessorBuilder. WithLeaseStoreManager (ILeaseStoreManager) metódust.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Ez a kiadás javít egy olyan problémát, amely a megfigyelt gyűjtemények és a particionált bérletek gyűjteményének feldolgozása során következik be. A felosztott partíciók bérletének feldolgozásakor előfordulhat, hogy a partíciónak megfelelő bérlet nem törölhető. A probléma ebben a kiadásban van kijavítva.

### <a name="221"></a><a name="2.2.1"></a>2.2.1
* A több főkiszolgálós fiókok és az új munkamenet-jogkivonat formátumának rögzített Kalkulátoros kiszámítása.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* A particionált címbérleti gyűjtemények támogatása megnövelve. A partíciós kulcsot/id. kell definiálni.
* Kisebb megszakítási változás: a IChangeFeedDocumentClient felület és a ChangeFeedDocumentClient osztály metódusai úgy lettek módosítva, hogy tartalmazzák a RequestOptions és a CancellationToken paramétereket. A IChangeFeedDocumentClient egy fejlett bővíthetőségi pont, amely lehetővé teszi, hogy a dokumentum-ügyfél egyéni implementációját a Change feed processzorral használja, például díszítse a DocumentClient, és feltartóztatja az összes hívást, hogy elvégezze a további nyomkövetést, hibakezelés stb. Ezzel a frissítéssel a IChangeFeedDocumentClient megvalósító kódot úgy kell módosítani, hogy új paramétereket tartalmazzon a megvalósításban.
* Kisebb diagnosztika fejlesztése.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Új API hozzáadása, feladat &lt; IReadOnlyList &lt; RemainingPartitionWork &gt; &gt; IRemainingWorkEstimator. GetEstimatedRemainingWorkPerPartitionAsync (). Ez az egyes partíciók becsült munkájának lekérésére használható.
* A Microsoft.Azure.DocumentDB SDK 2,0-es verziója. A Microsoft.Azure.DocumentDB 2,0-es vagy újabb verziójára van szükség.

### <a name="206"></a><a name="2.0.6"></a>clickstream
* A ChangeFeedEventHost. HostName nyilvános tulajdonsága a v1-vel való kompatibilitáshoz lett hozzáadva.

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* Rögzített egy versenyhelyzet, amely a partíció felosztása során következik be. A versenyhelyzet feltétele a bérlet megszerzését és a felosztáskor azonnal elveszítheti azt. A versenyhelyzet problémája ebben a kiadásban van kijavítva.

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"></a>2.0.3 – előzetes verzió
* Az alábbi problémákat javítja:
  * A felosztott partíciók megkettőzése esetén előfordulhat, hogy a felosztás előtt módosított dokumentumok többször is feldolgozva lettek.
  * A GetEstimatedRemainingWork API 0 értéket adott vissza, ha nem találhatók bérletek a címbérleti gyűjteményben.

* A következő kivételek nyilvánosak. A IPartitionProcessor megvalósító bővítmények kihasználhatják ezeket a kivételeket.
  * Microsoft.Azure.Documents. ChangeFeedProcessor. kivétellistájára. LeaseLostException. 
  * Microsoft.Azure.Documents. ChangeFeedProcessor. kivétellistájára. PartitionException. 
  * Microsoft.Azure.Documents. ChangeFeedProcessor. kivétellistájára. PartitionNotFoundException.
  * Microsoft.Azure.Documents. ChangeFeedProcessor. kivétellistájára. PartitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"></a>2.0.2 – előzetes verzió
* Másodlagos API-változások:
  * A rendszer eltávolította az elavultként megjelölt ChangeFeedProcessorOptions. IsAutoCheckpointEnabled.

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"></a>2.0.1 – előzetes verzió
* A stabilitás fejlesztése:
  * A bérleti tároló inicializálásának jobb kezelését. Ha a címbérlet-tároló üres, akkor a processzornak csak egy példánya inicializálható, a többi pedig megvárja.
  * Stabilabb/hatékony bérlet megújítása/kiadása. Egy adott bérlet megújítása és felszabadítása független a mások megújításával. A v1-ben, amely az összes partíció esetében szekvenciálisan lett végrehajtva.
* Új v2 API:
  * A processzor rugalmas felépítésének építő mintája: a ChangeFeedProcessorBuilder osztály.
    * A paraméterek tetszőleges kombinációját elvégezheti.
    * DocumentClient-példányt is igénybe vehet a figyelési és/vagy bérleti gyűjteményekhez (a v1-ben nem érhető el).
  * A IChangeFeedObserver. ProcessChangesAsync most a CancellationToken-t veszi igénybe.
  * IRemainingWorkEstimator – a fennmaradó munkahelyi kalkulátor külön is felhasználható a processzortól.
  * Új bővíthetőségi pontok:
    * IPartitionLoadBalancingStrategy – a partíciók a processzor példányai közötti egyéni terheléselosztása.
    * ILease, ILeaseManager – egyéni bérletek kezeléséhez.
    * IPartitionProcessor – a partíciók egyéni feldolgozásának módosítása.
* Naplózás – a [LibLog](https://github.com/damianh/LibLog) könyvtárat használja.
* 100% visszamenőlegesen kompatibilis a v1 API-val.
* Új kód alapja.
* Kompatibilis az [SQL .net SDK](sql-api-sdk-dotnet.md) 1.21.1 és újabb verzióival.

### <a name="v1-builds"></a>v1-buildek

### <a name="133"></a><a name="1.3.3"></a>1.3.3
* További naplózás lett hozzáadva.
* Rögzített DocumentClient-szivárgás a függőben lévő munkahelyi becslés többszöri meghívásakor.

### <a name="132"></a><a name="1.3.2"></a>1.3.2
* Javítások a függőben lévő munka becslésében.

### <a name="131"></a><a name="1.3.1"></a>1.3.1
* Stabilitási változások.
  * Javítsa a megszakított feladatok kezelésére vonatkozó problémát, amely a figyelők egyes partíciókban való leállítását eredményezheti.
* A manuális ellenőrzőpontok támogatása.
* Kompatibilis az [SQL .net SDK](sql-api-sdk-dotnet.md) 1,21-es vagy újabb verziójával.

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* A .NET Standard 2,0 támogatásának támogatása. A csomag mostantól támogatja `netstandard2.0` és a `net451` keretrendszer monikereit.
* Kompatibilis az [SQL .net SDK](sql-api-sdk-dotnet.md) 1.17.0 és újabb verzióival.
* Kompatibilis az [SQL .net Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1-es és újabb verzióival.

### <a name="111"></a><a name="1.1.1"></a>1.1.1
* Kijavít egy problémát a hátralévő munka becslésének kiszámításával, ha a változási csatorna üres volt, vagy nem volt függőben a munka.
* Kompatibilis az [SQL .net SDK](sql-api-sdk-dotnet.md) 1.13.2 és újabb verzióival.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Hozzáadott egy metódust a változási hírcsatornában feldolgozandó hátralévő munka becsült értékének beszerzéséhez.
* Kompatibilis az [SQL .net SDK](sql-api-sdk-dotnet.md) 1.13.2 és újabb verzióival.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK
* Kompatibilis az [SQL .net SDK](sql-api-sdk-dotnet.md) 1.14.1 és újabb verzióival.

## <a name="release--retirement-dates"></a>Kiadási & nyugdíjazási dátumok

A Microsoft legalább **12 hónappal** korábban értesítést küld az SDK kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra.

Az új funkciók és funkciók és optimalizálás csak a jelenlegi SDK-hoz adódik hozzá, ezért azt javasoljuk, hogy a lehető leghamarabb frissítsen a legújabb SDK-verzióra. 

A szolgáltatás elutasítja a kivont SDK-val Cosmos DBre irányuló kéréseket.

<br/>

| Verzió | Kiadás dátuma | Kivonás dátuma |
| --- | --- | --- |
| [2.3.0](#2.3.0) |2020. április 2. |--- |
| [2.2.8](#2.2.8) |Október 28., 2019 |--- |
| [2.2.7](#2.2.7) |Május 14., 2019 |--- |
| [2.2.6](#2.2.6) |2019. január 29. |--- |
| [2.2.5](#2.2.5) |December 13., 2018 |--- |
| [2.2.4](#2.2.4) |November 29., 2018 |--- |
| [2.2.3](#2.2.3) |November 19., 2018 |--- |
| [2.2.2](#2.2.2) |2018. október 31. |--- |
| [2.2.1](#2.2.1) |Október 24., 2018 |--- |
| [1.3.3](#1.3.3) |08. május 2018 |--- |
| [1.3.2](#1.3.2) |Április 18., 2018 |--- |
| [1.3.1](#1.3.1) |2018. március 13. |--- |
| [1.2.0](#1.2.0) |2017. október 31. |--- |
| [1.1.1](#1.1.1) |Augusztus 29., 2017 |--- |
| [1.1.0](#1.1.0) |Augusztus 13., 2017 |--- |
| [1.0.0](#1.0.0) |2017. július 7. |--- |

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még

További információ a Cosmos DBről: [Microsoft Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapja.
