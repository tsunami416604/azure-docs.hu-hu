---
title: Az Azure Cosmos DB .NET SDK használatakor felmerülő hibák diagnosztizálása és elhárítása
description: A .NET SDK használatakor az ügyféloldali naplózás és más külső eszközök használatával azonosíthatja, diagnosztizálhatja és elháríthatja az Azure Cosmos DB-val kapcsolatos problémákat.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137954"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Az Azure Cosmos DB .NET SDK használatakor felmerülő hibák diagnosztizálása és elhárítása
Ez [a](sql-api-sdk-dotnet.md) cikk a .
A .NET SDK ügyféloldali logikai ábrázolást biztosít az Azure Cosmos DB SQL API eléréséhez. Ez a cikk azokat az eszközöket és módszereket ismerteti, amelyek segítenek megoldani a problémákat.

## <a name="checklist-for-troubleshooting-issues"></a>Ellenőrzőlista a hibaelhárításhoz:
Vegye figyelembe az alábbi ellenőrzőlistát, mielőtt áthelyezi az alkalmazást éles környezetbe. Az ellenőrzőlista használata számos gyakori problémát megelőzhet. A probléma előfordulása esetén is gyorsan diagnosztizálhatja a következőket:

*    Használja a legújabb [SDK](sql-api-sdk-dotnet-standard.md). Az Előzetes SDK-k nem használhatók éles környezetben. Ez megakadályozza, hogy a már kijavított ismert problémákat találja el.
*    Tekintse át a [teljesítményre vonatkozó tippeket,](performance-tips.md)és kövesse a javasolt eljárásokat. Ez segít megelőzni a skálázást, a késést és más teljesítményproblémákat.
*    Az SDK-naplózás engedélyezésével elháríthatja a problémát. A naplózás engedélyezése hatással lehet a teljesítményre, ezért a legjobb, ha csak a problémák elhárításakor engedélyezi. A következő naplókat engedélyezheti:
    *    [Metrikák naplózása](monitor-accounts.md) az Azure Portal használatával. A portálmetrikák az Azure Cosmos DB telemetriai adatokat jelenítik meg, amely hasznos annak megállapításához, hogy a probléma megfelel-e az Azure Cosmos DB-nek, vagy az ügyféloldalról származik.Portal metrics show the Azure Cosmos DB telemettry, which is helpful to determine if the issue corresponds to Azure Cosmos DB or if it's from the client side.
    *    Naplózza a [diagnosztikai karakterláncot](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) a V2 SDK-ban vagy [a nanikta](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) V3 SDK-ban a pontművelet-válaszokból.
    *    Az [SQL-lekérdezési metrikák naplózása](sql-api-query-metrics.md) az összes lekérdezési válaszból 
    *    Kövesse az [SDK-naplózás]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) beállítását

Tekintse meg a cikk [Gyakori problémák és kerülő megoldások](#common-issues-workarounds) című szakaszát.

Ellenőrizze a [GitHub-problémák szakasz,](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) amely aktívan figyelt. Ellenőrizze, hogy van-e már benyújtani hasonló probléma a kerülő megoldással kapcsolatban. Ha nem talált megoldást, majd nyújtson be egy GitHub-problémát. Megnyithatja a támogatási kullancs sürgős kérdéseket.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Gyakori hibák és áthidaló megoldásaik

### <a name="general-suggestions"></a>Általános javaslatok
* Az alkalmazást az Azure Cosmos DB-fiókjával azonos Azure-régióban futtathatja, amikor csak lehetséges. 
* Előfordulhat, hogy az ügyfélgépen lévő erőforrások hiánya miatt kapcsolódási/rendelkezésre állási problémák merülnek fel. Azt javasoljuk, hogy az Azure Cosmos DB-ügyfelet futtató csomópontokon figyelheti a CPU-használatot, és nagy terhelés mellett történő felskálázást és felskálázást.

### <a name="check-the-portal-metrics"></a>A portál metrikáinak ellenőrzése
A [portál metrikáinak](monitor-accounts.md) ellenőrzése segít meghatározni, hogy ügyféloldali probléma-e, vagy ha probléma van a szolgáltatással. Például ha a metrikák nagy arányú korlátozott kérelmeket tartalmaznak(HTTP-állapotkód 429), ami azt jelenti, hogy a kérelem szabályozása van, majd ellenőrizze a [kérelem aránya túl nagy] szakasz. 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Időeltetéskérések kérése
RequestTimeout általában közvetlen/TCP használatakor történik, de átjáró módban is előfordulhat. Ezek a hibák a leggyakoribb ismert okok, és javaslatokat, hogyan lehet megoldani a problémát.

* A processzorkihasználtság magas, ami késést és/vagy időkérést okoz. Az ügyfél felskálázhatja a gazdagép, hogy több erőforrást, vagy a terhelés több gép között osztható konosztálása.
* A Socket / Port elérhetősége alacsony lehet. Amikor az Azure-ban fut, a .NET SDK-t használó ügyfelek megüt az Azure SNAT (PAT) port kimerültség. A probléma alkalmazásának esélyének csökkentése érdekében használja a .NET SDK legújabb 2.x vagy 3.x verzióját. Ez egy példa arra, hogy miért ajánlott mindig futtatni a legújabb SDK-verziót.
* Több DocumentClient-példány létrehozása kapcsolati versengéshez és időtúllépési problémákhoz vezethet. Kövesse a [teljesítménytippeket,](performance-tips.md)és használjon egyetlen DocumentClient példányt a teljes folyamatban.
* A felhasználók néha emelt szintű késést látnak, vagy időtúlhívásokat kérnek, mert a gyűjtemények nem megfelelően vannak kiépítve, a háttérrendszer-szabályozási kérelmek, és az ügyfél belsőleg újrapróbálkozik. Ellenőrizze a [portál metrikáit.](monitor-accounts.md)
* Az Azure Cosmos DB a teljes kiosztott átviteli forgalmat egyenletesen osztja el a fizikai partíciók között. Ellenőrizze a portál metrikáit, hogy a munkaterhelés [gyorspartíció-kulcsba](partition-data.md)ütközik-e. Ez azt eredményezi, hogy az összesített felhasznált átviteli -átbocsátás (RU/s) a kiosztott vt-k alatt jelenik meg, de egy partíció által felhasznált átviteli -át (RU/s) meghaladja a kiosztott átviteli. 
* Emellett a 2.0 SDK csatornaszemantikát ad hozzá a közvetlen/TCP-kapcsolatokhoz. Egy TCP-kapcsolat egyszerre több kérelemhez használatos. Ez két konkrét esetben két kérdést eredményezhet:
    * A magas szintű egyidejűség vezethet viszálykodás a csatornán.
    * A nagy kérések vagy válaszok a csatorna vezetékközbeni blokkolásához vezethetnek, és súlyosbíthatják a versengést, még viszonylag alacsony egyidejűség mellett is.
    * Ha az eset a két kategória valamelyikébe tartozik (vagy ha nagy CPU-kihasználtság gyanúja merül fel), ezek lehetséges megoldások:
        * Próbálja meg fel-ki-ki méretezni az alkalmazást.
        * Emellett az SDK-naplók a [Trace Listener](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) en keresztül rögzíthetők további részletekért.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Nagy hálózati késés
A nagy hálózati késés a V2 SDK [diagnosztikai karakterláncával](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) vagy a V3 SDK [diagnosztikájával](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) azonosítható.

Ha nincs [időtúltöltés,](#request-timeouts) és a diagnosztika egyetlen olyan kérelmeket `ResponseTime` `RequestStartTime`jelenít meg, ahol a nagy késés nyilvánvaló a különbség és a , a ) (>300 ezredmásodperc ebben a példában):

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

Ennek a késésnek több oka is lehet:

* Az alkalmazás nem fut ugyanabban a régióban, mint az Azure Cosmos DB-fiók.
* A [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) vagy [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) konfigurációja helytelen, és megpróbál csatlakozni egy másik régióhoz, ahol az alkalmazás jelenleg fut.
* Előfordulhat, hogy a nagy forgalmú forgalom miatt szűk keresztmetszet van a hálózati kapcsolaton. Ha az alkalmazás Azure virtuális gépeken fut, lehetséges megoldások:
    * Fontolja meg egy [olyan virtuális gép használatát, amelyen engedélyezve van a gyorsított hálózatkezelés.](../virtual-network/create-vm-accelerated-networking-powershell.md)
    * Gyorsított hálózatkezelés engedélyezése [meglévő virtuális gépen.](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)
    * Fontolja meg egy [magasabb kategóriás virtuális gép](../virtual-machines/windows/sizes.md)használatát.

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Az Azure SNAT (PAT) port kimerülése

Ha az alkalmazás [nyilvános IP-cím nélküli Azure virtuális gépeken](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)van telepítve, alapértelmezés szerint [az Azure SNAT-portok](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) létesítenek kapcsolatokat a virtuális gépen kívüli bármely végponthoz. A virtuális gép és az Azure Cosmos DB-végpont között engedélyezett kapcsolatok számát az [Azure SNAT-konfiguráció](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)korlátozza. Ez a helyzet kapcsolatszabályozáshoz, kapcsolatlezárásához vagy a fent említett [kérelem időtúlteljesítéséhez](#request-timeouts)vezethet.

 Az Azure SNAT-portok csak akkor használatosak, ha a virtuális gép privát IP-címmel rendelkezik, és nyilvános IP-címhez csatlakozik. Az Azure SNAT-korlátozás elkerülése érdekében két kerülő megoldás létezik (feltéve, hogy már egyetlen ügyfélpéldányt használ a teljes alkalmazásban):

* Adja hozzá az Azure Cosmos DB szolgáltatásvégpontját az Azure virtuális gépek virtuális hálózatának alhálózatához. További információ: [Azure Virtual Network service endpoints](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Ha a szolgáltatás végpontja engedélyezve van, a kérelmek et már nem küldi el a nyilvános IP-cím ről az Azure Cosmos DB. Ehelyett a virtuális hálózat és az alhálózati identitás küldése. Ez a módosítás tűzfal-leejtést eredményezhet, ha csak nyilvános IP-k engedélyezettek. Ha tűzfalat használ, a szolgáltatásvégpont engedélyezésekor vegyen fel egy alhálózatot a tűzfalhoz a [virtuális hálózati abeil-ek](../virtual-network/virtual-networks-acl.md)használatával.
* Rendeljen hozzá egy [nyilvános IP-címet az Azure virtuális géphez.](../load-balancer/load-balancer-outbound-connections.md#assignilpip)

### <a name="http-proxy"></a>HTTP-proxy
Http-proxy használata esetén győződjön meg arról, hogy az támogatja `ConnectionPolicy`az SDK-ban konfigurált kapcsolatok számát.
Ellenkező esetben csatlakozási problémákkal kell szembenéznie.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>A kérelem aránya túl nagy
A "kérelemaránya túl nagy" vagy a 429-es hibakód azt jelzi, hogy a kérelmek szabályozás alatt állnak, mivel a felhasznált átviteli sebesség (RU/s) túllépte a [kiosztott átviteli sebességet.](set-throughput.md) Az SDK automatikusan újrapróbálkozik a kérelmeket a megadott [újrapróbálkozási házirend](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)alapján. Ha gyakran előfordul ez a hiba, fontolja meg a gyűjtemény átviteli igényének növelését. Ellenőrizze a [portál metrikáit,](use-metrics.md) hogy ha 429 hibát észlel. Tekintse át a [partíciókulcsot,](partitioning-overview.md#choose-partitionkey) és győződjön meg arról, hogy a tárolási és kérelemkötet egyenletes eloszlását eredményezi. 

### <a name="slow-query-performance"></a>Lassú lekérdezési teljesítmény
A [lekérdezési metrikák segítenek](sql-api-query-metrics.md) meghatározni, hogy a lekérdezés az idő nagy részében hol van eltöltve. A lekérdezési metrikákból láthatja, hogy mennyi van a háttérrendszerés az ügyfél számára.
* Ha a háttérlekérdezés gyorsan visszatér, és nagy időt tölt az ügyfélen, ellenőrizze a terhelést a számítógépen. Valószínű, hogy nincs elég erőforrás, és az SDK arra vár, hogy az erőforrások elérhetők legyenek a válasz kezeléséhez.
* Ha a háttérlekérdezés lassú, próbálja meg [optimalizálni a lekérdezést,](optimize-cost-queries.md) és megvizsgálni az aktuális [indexelési házirendet](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[A kérelem aránya túl nagy]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


