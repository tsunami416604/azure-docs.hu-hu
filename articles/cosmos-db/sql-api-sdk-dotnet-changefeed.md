---
title: Az Azure Cosmos DB .NET változáscsatorna processzor API, SDK kiadási megjegyzések
description: Tudjon meg mindent a Change Feed Processor API-ról és az SDK-ról, beleértve a kiadási dátumokat, a megszüntetési dátumokat és a .NET Change Feed Processor SDK egyes verziói között végrehajtott módosításokat.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/30/2019
ms.author: maquaran
ms.openlocfilehash: 5820778d46f5701b82bb289192350a9e13739d37
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619443"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET Change Feed Processzor SDK: Megjegyzések letöltése és kiadása

> [!div class="op_single_selector"]
>
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET módosítási hírcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK letöltés**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-dokumentáció**|[A Hírcsatorna-processzorkönyvtár API-jának hivatkozási dokumentációjának módosítása](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Bevezetés**|[A hírcsatorna-feldolgozó módosítása .NET SDK – első lépések](change-feed.md)|
|**Jelenlegi támogatott keretrendszer**| [Microsoft .NET-keretrendszer 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> Ha változáscsatorna-processzort használ, olvassa el a [.NET SDK](change-feed-processor.md)legújabb 3.x verzióját, amely az SDK-ba beépített változáscsatornát is beépített. 

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="v2-builds"></a>v2 épít

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Hozzáadott egy `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` új módszert `ICheckpointPartitionProcessorFactory`és a megfelelő nyilvános felület . Ez lehetővé teszi, hogy a `IPartitionProcessor` felület megvalósítása beépített ellenőrzőpont-mechanizmust használjon. Az új gyár hasonló `IPartitionProcessorFactory`a meglévőhez, azzal a különbséggel, hogy a `Create` módszer e `ILeaseCheckpointer` paramétert is átveszi.
* A két módszer közül `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` csak `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`az egyik használható, `ChangeFeedProcessorBuilder` vagy a , használható ugyanarra a példányra.

### <a name="228"></a><a name="2.2.8"/>2.2.8
* Stabilitás és diagnosztizálhatóság javítása:
  * Támogatás sal bővült az olvasási módosítási hírcsatorna hosszú ideig tartó észlelése. Ha a tulajdonság által megadott értéknél tovább tart, a `ChangeFeedProcessorOptions.ChangeFeedTimeout` következő lépéseket kell tenni:
    * A problémás partíción a módosítási hírcsatorna olvasására irányuló művelet megszakad.
    * A változáscsatorna-processzor példány a problémás bérlet tulajdonjogát csökkenti. Az eldobott bérlet et a következő bérletbeszerzési lépés során veszi késedeljük meg, amelyet ugyanaz vagy a különböző változáscsatorna-feldolgozó példány fog elvégezni. Ily módon, olvasás változás feed kezdődik újra.
    * Egy probléma az állapotfigyelőnek jelentve. Az alapértelmezett heath monitor elküldi az összes jelentett problémát a nyomkövetési naplóba.
  * Hozzáadott egy új `ChangeFeedProcessorOptions.ChangeFeedTimeout`köztulajdon: . A tulajdonság alapértelmezett értéke 10 min.
  * Hozzáadott egy új nyilvános `Monitoring.MonitoredOperation.ReadChangeFeed`felsoracska érték: . Ha az `HealthMonitoringRecord.Operation` értéke a `Monitoring.MonitoredOperation.ReadChangeFeed`, azt jelzi, hogy az állapotprobléma a változáscsatorna olvasásával kapcsolatos.

### <a name="227"></a><a name="2.2.7"/>2.2.7
* Továbbfejlesztett terheléselosztási stratégia forgatókönyvesetén, ha az összes címbérlet lejárati időköznél hosszabb időt vesz igénybe, például hálózati problémák miatt:
  * Ebben a forgatókönyvben a terheléselosztási algoritmus tévesen tekinti a bérletek lejártnak való, ami az aktív tulajdonosok tól származó bérletek ellopását okozza. Ez számos bérleti szerződés szükségtelen újraegyensúlyozását idézheti elő.
  * Ez a probléma megoldódott ebben a kiadásban azáltal, hogy elkerüli az ütközések újrapróbálkozását, miközben lejárt bérletet szerez be, amelyet a tulajdonos nem módosított, és a lejárt bérlet megszerzését a következő terheléselosztási iterációra állítja.

### <a name="226"></a><a name="2.2.6"/>2.2.6
* A megfigyelői kivételek jobb kezelése.
* Gazdagabb információk a megfigyelői hibákról:
  * Amikor egy megfigyelő takarásban van az Observer ProcessChangesAsync által okozott kivétel miatt, a CloseAsync megkapja a ChangeFeedObserverCloseReason.ObserverError paramétert.
  * Nyomkövetések a megfigyelő felhasználói kódján belüli hibák azonosítására.

### <a name="225"></a><a name="2.2.5"/>2.2.5
* A megosztott adatbázis-átviteli erőket használó gyűjtemények felosztásának támogatása hozzáadva.
  * Ez a kiadás kijavítja azt a problémát, amely a megosztott adatbázis-átviteli terhelést használó gyűjtemények felosztása során fordulhat elő, amikor az eredményeket partíció-újraegyensúlyozásra osztja, és kettő helyett csak egy gyermek partíciókulcs-tartomány jön létre. Ha ez történik, a Change Feed Processzor elakadanak a régi partíciókulcs-tartomány bérletének törlésében, és nem hoznak létre új címbérleteket. A probléma ebben a kiadásban kivan javítva.

### <a name="224"></a><a name="2.2.4"/>2.2.4
* Hozzáadott új tulajdonság ChangeFeedProcessorOptions.StartContinuation, hogy támogassa a kezdő változás hírcsatorna kérelem folytatási token. Ez csak akkor használatos, ha a címbérlet-gyűjtemény üres, vagy a bérlet nem rendelkezik ContinuationToken készlettel. A lease Collection, amelyek ContinuationToken készlet, a continuationtoken használatos, és ChangeFeedProcessorOptions.StartContinuation figyelmen kívül hagyja.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Hozzáadott támogatás az egyéni tároló használatával a folytatási jogkivonatok partíciónkénti megőrzéséhez.
  * Például egy egyéni bérlettároló lehet Az Azure Cosmos DB-címbérlet-gyűjtemény bármilyen egyéni módon particionált.
  * Az egyéni címbérlet-tárolók új bővíthetőségi pontot használhatnak ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) és ILeaseStoreManager nyilvános felületet.
  * Az ILeaseManager felületet több szerepkör-illesztővé vette át.
* Kisebb törésmódosítás: eltávolított changeFeedProcessorBuilder.WithLeaseManager(ILeaseManager) eltávolítási pont, használja a ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) kifejezést.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* Ez a kiadás kijavítja azt a problémát, amely a figyelt gyűjtemény felosztásának feldolgozása és a particionált címbérlet-gyűjtemény használata során jelentkezik. Osztott partíció bérletének feldolgozásakor előfordulhat, hogy az adott partíciónak megfelelő bérlet nem törlődik. A probléma ebben a kiadásban kivan javítva.

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Rögzített Estimator számítás több főkiszolgálós fiókokhoz és új munkamenettoken formátumhoz.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* A particionált címbérlet-gyűjtemények támogatása hozzáadva. A partíciókulcsot /id néven kell definiálni.
* Kisebb megszakítási módosítás: az IChangeFeedDocumentClient felület és a ChangeFeedDocumentClient osztály metódusai a RequestOptions és a CancellationToken paraméterekkel módosították. IChangeFeedDocumentClient egy speciális bővíthetőségi pont, amely lehetővé teszi, hogy egyéni végrehajtását a dokumentum kliens használni Change Feed processzor, pl. díszíteni DocumentClient és elfog minden hívást, hogy nem extra nyomon követése, hibakezelés, stb. Ezzel a frissítéssel az IChangeFeedDocumentClient programot megvalósító kódot módosítani kell, hogy új paramétereket tartalmazzon a megvalósításba.
* Kisebb diagnosztikai fejlesztések.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Hozzáadott új&lt;API, Task&lt;IReadOnlyList RemainingPartitionWork&gt; &gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Ezt fel lehet használni, hogy a becsült munka minden partícióra.
* Támogatja a Microsoft.Azure.DocumentDB SDK 2.0-s verziót. Microsoft.Azure.DocumentDB 2.0 vagy újabb szükséges.

### <a name="206"></a><a name="2.0.6"/>clickstream
* Hozzáadott ChangeFeedEventHost.HostName public property for compatibility with v1.

### <a name="205"></a><a name="2.0.5"/>2.0.5
* Javítottunk egy versenyállapotot, amely a partíció felosztása során jelentkezik. A versenyviszonyok a bérlet megszerzéséhez és azonnali elvesztéséhez vezethetnek a partíció felosztása során, és viszálykodást okozhatnak. A versenyhelyzettel kapcsolatos probléma ezzel a kiadással lett kijavítva.

### <a name="204"></a><a name="2.0.4"/>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"/>2.0.3-előzetes kiadás
* Az alábbi problémákat javítja:
  * Partíciófelosztás esetén a felosztás előtt módosított dokumentumok duplikált feldolgozása történhet.
  * A GetEstimatedRemainingWork API 0-t adott vissza, ha nem voltak bérletek a címbérlet-gyűjteményben.

* A következő kivételek nyilvánosak. Az IPartitionProcessor processzort megvalósító bővítmények elhajthatják ezeket a kivételeket.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-előzetes kiadás
* Kisebb API-módosítások:
  * Eltávolította a ChangeFeedProcessorOptions.IsAutoCheckpointEnabled-t, amely elavultként volt megjelölve.

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-előzetes kiadás
* Stabilitási fejlesztések:
  * A lízingáruház inicializálásának jobb kezelése. Ha a bérlettároló üres, a processzornak csak egy példánya inicializálhatja, a többiek várni fognak.
  * Stabilabb/hatékonyabb bérleti megújítás/kiadás. Egy partíció megújítása és felszabadítása egy partíció független a mások megújításától. A v1, hogy történt egymás után az összes partíciót.
* Új v2 API:
  * Építőminta a processzor rugalmas felépítéséhez: a ChangeFeedProcessorBuilder osztály.
    * A paraméterek bármilyen kombinációját eltudja venni.
    * A DocumentClient-példányt figyelheti és/vagy címbérlet-gyűjteményként (a v1-ben nem érhető el).
  * Az IChangeFeedObserver.ProcessChangesAsync mostantól törlési tokenre kerül.
  * IRemainingWorkEstimator - a fennmaradó munkabecslő a processzortól elkülönítve használható.
  * Új bővíthetőségi pontok:
    * IPartitionLoadBalancingStrategy - a partíciók egyéni terheléselosztása a processzor példányai között.
    * ILease, ILeaseManager - egyéni címbérlet-kezeléshez.
    * IPartitionProcessor - egyéni feldolgozási módosítások at a partíción.
* Naplózás – [liblog](https://github.com/damianh/LibLog) könyvtárat használ.
* 100%-kal visszafelé kompatibilis a v1 API-val.
* Új kódbázis.
* Kompatibilis az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21.1-es és újabb verzióival.

### <a name="v1-builds"></a>v1 épít

### <a name="133"></a><a name="1.3.3"/>1.3.3
* További naplózás hozzáadva.
* Javítottunk egy DocumentClient-szivárgást, amikor többször is meghívta a függőben lévő munka becslését.

### <a name="132"></a><a name="1.3.2"/>1.3.2
* Javítások a függőben lévő munka becslésében.

### <a name="131"></a><a name="1.3.1"/>1.3.1
* Stabilitási fejlesztések.
  * Javítsa ki a megszakított feladatok kezelését, amely egyes partíciókon leállított megfigyelőkhöz vezethet.
* Kézi ellenőrzőpontok támogatása.
* Kompatibilis az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21-es és újabb verzióival.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Támogatja a .NET Standard 2.0-s szabványt. A csomag most `netstandard2.0` `net451` már támogatja és keret monikers.
* Kompatibilis az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0-s és újabb verzióival.
* Kompatibilis az [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1-es és újabb verzióival.

### <a name="111"></a><a name="1.1.1"/>1.1.1
* Kijavít egy problémát a hátralévő munka becsült összegének kiszámításával kapcsolatban, amikor a hírcsatorna módosítása üres volt, vagy nem volt függőben munka.
* Kompatibilis az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2-es és újabb verzióival.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Hozzáadott egy módszert a módosító hírfolyamban feldolgozandó hátralévő munka becslésének megszerzésére.
* Kompatibilis az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2-es és újabb verzióival.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Kompatibilis az [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1-es és újabb verzióival.

## <a name="release--retirement-dates"></a>Kiadási & nyugdíjazási dátumok

A Microsoft legalább **12 hónappal** az SDK kicsomagolása előtt értesítést küld az újabb/támogatott verzióra való áttérés zökkenőmentessé.

Az új funkciók és funkciók és optimalizálások csak az aktuális SDK-hoz kerülnek hozzáadásra, ezért ajánlott mindig a lehető leghamarabb frissíteni a legújabb SDK-verzióra. 

A Cosmos DB-nek a rendszer egy kivisszavonult SDK-t használó kérését a szolgáltatás elutasítja.

<br/>

| Verzió | Megjelenési dátum | Nyugdíjazás dátuma |
| --- | --- | --- |
| [2.3.0](#2.3.0) |2020. április 2. |--- |
| [2.2.8](#2.2.8) |2019. október 28.October 28, 2019 |--- |
| [2.2.7](#2.2.7) |2019. május 14.May 14, 2019 |--- |
| [2.2.6](#2.2.6) |2019. január 29., 2019. január 29. |--- |
| [2.2.5](#2.2.5) |2018. december 13., |--- |
| [2.2.4](#2.2.4) |2018. november 29., 29., 2018. |--- |
| [2.2.3](#2.2.3) |2018. november 19., 2018. |--- |
| [2.2.2](#2.2.2) |2018. október 31. |--- |
| [2.2.1](#2.2.1) |2018. október 24.October 24, 2018 |--- |
| [1.3.3](#1.3.3) |2018. május 08.May 08, 2018 |--- |
| [1.3.2](#1.3.2) |2018. április 18.April 18, 2018 |--- |
| [1.3.1](#1.3.1) |2018. március 13. |--- |
| [1.2.0](#1.2.0) |2017. október 31. |--- |
| [1.1.1](#1.1.1) |2017. augusztus 29.August 29, 2017 |--- |
| [1.1.0](#1.1.0) |2017. augusztus 13.August 13, 2017 |--- |
| [1.0.0](#1.0.0) |2017. július 07.July 07, 2017 |--- |

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még

A Cosmos DB szolgáltatásról a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatáslapján olvashat bővebben.
