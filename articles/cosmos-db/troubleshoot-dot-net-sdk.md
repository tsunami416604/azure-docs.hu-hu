---
title: Az Azure Cosmos DB .NET SDK használatakor felmerülő hibák diagnosztizálása és elhárítása
description: A .NET SDK használatakor olyan szolgáltatásokat használhat, mint az ügyféloldali naplózás és más külső eszközök a Azure Cosmos DB problémák azonosításához, diagnosztizálásához és hibaelhárításához.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 09/12/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 581c8fcad62c40555a90b7455a260259f3a09212
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91802413"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Az Azure Cosmos DB .NET SDK használatakor felmerülő hibák diagnosztizálása és elhárítása

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

Ez a cikk általános problémákról, megkerülő megoldásokról, diagnosztikai lépésekről és eszközökről tartalmaz Azure Cosmos DB SQL API-fiókokkal rendelkező [.net SDK](sql-api-sdk-dotnet.md) -t használva.
A .NET SDK ügyféloldali logikai ábrázolást biztosít a Azure Cosmos DB SQL API eléréséhez. Ez a cikk azokat az eszközöket és módszereket ismerteti, amelyek segítenek megoldani a problémákat.

## <a name="checklist-for-troubleshooting-issues"></a>Ellenőrzőlista hibaelhárítási problémákhoz

Mielőtt éles környezetben áthelyezi az alkalmazást, vegye figyelembe a következő ellenőrzőlistát. Az ellenőrzőlista használatával több gyakori probléma is megjelenhet. A probléma megoldásához gyorsan is diagnosztizálhatja a problémát:

*    Használja a legújabb [SDK](sql-api-sdk-dotnet-standard.md)-t. Az előzetes verziójú SDK-kat nem ajánlott éles környezetben használni. Ez megakadályozza a már kijavított ismert problémák elkerülését.
*    Tekintse át a [teljesítménnyel kapcsolatos tippeket](performance-tips.md), és kövesse a javasolt eljárásokat. Ez segít megakadályozni a skálázást, a késést és az egyéb teljesítménnyel kapcsolatos problémákat.
*    A probléma megoldásához engedélyezze az SDK-naplózást. A naplózás engedélyezése hatással lehet a teljesítményre, így csak hibaelhárítási problémák esetén ajánlott engedélyezni. A következő naplók engedélyezhetők:
*    A [metrikák naplózása](monitor-accounts.md) a Azure Portal használatával. A portál metrikái a Azure Cosmos DB telemetria mutatják be, ami hasznos lehet annak megállapítására, hogy a probléma megfelel-e a Azure Cosmos DBnak, vagy az ügyfél oldaláról származik-e.
*    Naplózza a [diagnosztikai karakterláncot](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) a v2 SDK-ban vagy [DIAGNOSZTIKÁT](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) a v3 SDK-ban a pont műveleti válaszai közül.
*    Az [SQL-lekérdezési metrikák](sql-api-query-metrics.md) naplózása az összes lekérdezési válaszból 
*    Az [SDK-naplózás]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) beállításának követése

Tekintse meg a jelen cikk [gyakori problémák és megoldások](#common-issues-workarounds) című szakaszát.

Ellenőrizze, hogy aktív-e a [GitHub-problémák szakasza](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) . Ellenőrizze, hogy a megkerülő megoldással kapcsolatos bármilyen hasonló probléma már be van-e jelölve. Ha nem talál megoldást, a GitHub-problémát is megteheti. A sürgős problémákhoz egy támogatási kullancsot is megnyithat.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Gyakori hibák és áthidaló megoldásaik

### <a name="general-suggestions"></a>Általános javaslatok
* Ha lehetséges, futtassa az alkalmazást ugyanabban az Azure-régióban, mint a Azure Cosmos DB-fiókját. 
* Az ügyfélszámítógépen lévő erőforrások hiánya miatt előfordulhat, hogy kapcsolati vagy rendelkezésre állási problémákba ütközik. Javasoljuk, hogy a CPU-kihasználtságot a Azure Cosmos DB-ügyfelet futtató csomópontokon figyelje, és ha nagy terhelésen futnak, fel kell skálázást.

### <a name="check-the-portal-metrics"></a>A portál metrikáinak megtekintése
A [portál metrikáinak](monitor-accounts.md) ellenőrzése segít meghatározni, hogy az ügyféloldali probléma-e, vagy hogy van-e probléma a szolgáltatással. Ha például a mérőszámok nagy arányban korlátozott kérelmeket tartalmaznak (a 429-as HTTP-állapotkód), ami azt jelenti, hogy a kérést a rendszer lekéri, akkor ellenőrizze, hogy a [kérelem sebessége túl nagy](troubleshoot-request-rate-too-large.md) . 

## <a name="common-error-status-codes"></a>Gyakori hibák állapotkódok <a id="error-codes"></a>

| Állapotkód | Leírás | 
|----------|-------------|
| 400 | Hibás kérés (a hibaüzenettől függ)| 
| 401 | [Nincs engedélyezve](troubleshoot-unauthorized.md) | 
| 404 | [Az erőforrás nem található](troubleshoot-not-found.md) |
| 408 | [A kérelem időkorlátja lejárt](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | Ütközési hiba az, amikor egy meglévő erőforrás elvégezte egy írási művelethez megadott erőforrás AZONOSÍTÓját. A probléma megoldásához használjon másik azonosítót az erőforráshoz, mivel az AZONOSÍTÓnak egyedinek kell lennie az összes olyan dokumentumon belül, amelynek a partíciós kulcs értéke azonos. |
| 410 | Megszűnt kivételek (átmeneti hiba, amely nem sértheti az SLA-t) |
| 412 | Az előfeltétel meghibásodása esetén a művelet olyan eTag adott meg, amely eltér a kiszolgálón elérhető verziótól. Optimista egyidejűségi hiba történt. Az erőforrás legfrissebb verziójának beolvasása és a kérés eTagjének frissítése után próbálkozzon újra a kéréssel.
| 413 | [A kérelem entitása túl nagy](concepts-limits.md#per-item-limits) |
| 429 | [Túl sok kérelem](troubleshoot-request-rate-too-large.md) |
| 449 | Átmeneti hiba, amely csak írási műveletekben fordul elő, és biztonságos az újrapróbálkozáshoz |
| 500 | A művelet váratlan szolgáltatási hiba miatt nem sikerült. Vegye fel a kapcsolatot az ügyfélszolgálattal. Lásd: [Azure-támogatási probléma](https://aka.ms/azure-support)bejelentése. |
| 503 | [A szolgáltatás nem érhető el](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Az Azure SNAT (PAT) portjának kimerülése

Ha az alkalmazás [nyilvános IP-cím nélküli Azure-Virtual Machines](../load-balancer/load-balancer-outbound-connections.md)van telepítve, alapértelmezés szerint az [Azure SNAT-portok](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) kapcsolatot létesít a virtuális gépen kívüli végpontokkal. A virtuális gépről a Azure Cosmos DB végpont számára engedélyezett kapcsolatok számát az [Azure SNAT konfigurációja](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)korlátozza. Ez a helyzet a kapcsolat szabályozásához, a kapcsolat bezárásához vagy a fent említett [kérelmek időtúllépéséhez](troubleshoot-dot-net-sdk-request-timeout.md)vezethet.

 Az Azure SNAT-portokat csak akkor használja a rendszer, ha a virtuális gép magánhálózati IP-címmel csatlakozik egy nyilvános IP-címhez. Az Azure SNAT-korlátozás elkerülése érdekében két Áthidaló megoldás létezik (ha már egyetlen ügyfél-példányt használ a teljes alkalmazásban):

* Adja hozzá Azure Cosmos DB szolgáltatási végpontját az Azure Virtual Machines Virtual Network alhálózatához. További információ: [Azure Virtual Network Service-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Ha a szolgáltatási végpont engedélyezve van, a rendszer a kérelmeket már nem küldi el a nyilvános IP-címről Azure Cosmos DB. Ehelyett a rendszer elküldi a virtuális hálózatot és az alhálózati identitást. Ez a változás akkor okozhat tűzfalat, ha csak a nyilvános IP-címek engedélyezettek. Ha tűzfalat használ, a szolgáltatás végpontjának engedélyezésekor [Virtual Network ACL](../virtual-network/virtual-networks-acl.md)-EK használatával adjon hozzá egy alhálózatot a tűzfalhoz.
* Rendeljen hozzá egy [nyilvános IP-címet az Azure-beli virtuális géphez](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Nagy hálózati késés
A hálózati késést a v2 SDK- [ban vagy a](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) v3 SDK-ban lévő diagnosztika [diagnosztikai karakterláncának](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet&preserve-view=true) használatával lehet azonosítani.

Ha nincsenek [időtúllépések](troubleshoot-dot-net-sdk-request-timeout.md) , és a diagnosztika egyetlen kérést mutat be, ahol a nagy késés nyilvánvaló a és a közötti különbségnél `ResponseTime` `RequestStartTime` (>300 ezredmásodperc ebben a példában):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Ennek a késésnek több oka lehet:

* Az alkalmazás nem ugyanabban a régióban fut, mint a Azure Cosmos DB fiókja.
* A [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) vagy a [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) konfigurációja helytelen, és megpróbál csatlakozni egy másik régióhoz, ahol az alkalmazás jelenleg fut.
* A nagy adatforgalom miatt a hálózati adapter szűk keresztmetszetet jelenthet. Ha az alkalmazás az Azure Virtual Machineson fut, lehetséges a megkerülő megoldások:
    * A [gyorsított hálózatkezelést használó virtuális gépeket](../virtual-network/create-vm-accelerated-networking-powershell.md)érdemes lehet használni.
    * [Gyorsított hálózatkezelés engedélyezése egy meglévő virtuális gépen](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).
    * Érdemes lehet [magasabb végpontú virtuális gépet](../virtual-machines/windows/sizes.md)használni.

### <a name="common-query-issues"></a>Gyakori lekérdezési problémák

A [lekérdezési metrikák](sql-api-query-metrics.md) segítenek meghatározni, hogy a lekérdezés hol tölti le a legtöbb időt. A lekérdezési mérőszámokból megtekintheti, hogy mennyi időt tölt a háttér és a-ügyfél. További információ a [lekérdezések teljesítményének hibaelhárításáról](troubleshoot-query-performance.md).

* Ha a háttérbeli lekérdezés gyorsan visszatér, és nagy időt tölt az ügyfélen, ellenőrizze a terhelést a gépen. Valószínű, hogy nincs elegendő erőforrás, és az SDK arra vár, hogy az erőforrások elérhetők legyenek a válasz kezelésére.
* Ha a háttérbeli lekérdezés lassú, próbálja meg [optimalizálni a lekérdezést](troubleshoot-query-performance.md) , és tekintse meg az aktuális [indexelési házirendet](index-overview.md) .

    > [!NOTE]
    > A jobb teljesítmény érdekében javasoljuk a Windows 64 bites gazdagépek feldolgozását. Az SQL SDK tartalmaz egy natív ServiceInterop.dll a lekérdezések helyi elemzéséhez és optimalizálásához. A ServiceInterop.dll csak a Windows x64 platformon támogatott. Linux és egyéb nem támogatott platformok esetén, ahol a ServiceInterop.dll nem érhető el, az átjáróra további hálózati hívás történik az optimalizált lekérdezés beszerzéséhez.

Ha a következő hibába ütközik: `Unable to load DLL 'Microsoft.Azure.Cosmos.ServiceInterop.dll' or one of its dependencies:` és Windows rendszert használ, frissítsen a legújabb Windows-verzióra.

## <a name="next-steps"></a>További lépések

* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md) teljesítményével kapcsolatos irányelvek ismertetése
* Tudnivalók a [reaktor-alapú Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) -k használatáról

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
