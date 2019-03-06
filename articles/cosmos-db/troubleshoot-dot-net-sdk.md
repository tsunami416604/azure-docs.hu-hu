---
title: Problémák diagnosztizálása és hibaelhárítása az Azure Cosmos DB .NET SDK használata
description: Szolgáltatások használata, mint az ügyféloldali naplózás és egyéb külső eszközöket, azonosítása, diagnosztizálása és Azure Cosmos DB-hibák elhárításához, ha a .NET SDK használatával.
author: j82w
ms.service: cosmos-db
ms.date: 01/19/2019
ms.author: jawilley
ms.devlang: c#
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7f969ab6059140ec32c9c5bf5045c546602a3c15
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411514"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Problémák diagnosztizálása és hibaelhárítása az Azure Cosmos DB .NET SDK használata
Ez a cikk ismerteti gyakori problémák, megoldások, diagnosztikai lépések és eszközök használata esetén a [.NET SDK-val](sql-api-sdk-dotnet.md) az Azure Cosmos DB SQL API-fiókok.
A .NET SDK-t biztosít ügyféloldali logikai leképezést az Azure Cosmos DB SQL API eléréséhez. Ez a cikk ismerteti az eszközökre és megközelítési segítséget, ha bármilyen problémába.

## <a name="checklist-for-troubleshooting-issues"></a>Ellenőrzőlista a problémák elhárítása:
Mielőtt az alkalmazás éles környezetbe helyezünk, vegye figyelembe a következő ellenőrzőlista. Az ellenőrzőlista használatával megakadályozza, hogy számos gyakori problémákat tapasztalhatja. Probléma esetén is gyorsan diagnosztizálhatja:

*   A legújabb [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md). Előzetes verzió SDK-k nem használható éles üzemi környezetek részei. Ez megakadályozza, hogy már kijavított ismert problémákat.
*   Tekintse át a [teljesítménnyel kapcsolatos tippek](performance-tips.md), és kövesse az ajánlott eljárásokat. Ez segít a skálázás, a késés és a teljesítménnyel kapcsolatos egyéb problémák megelőzése.
*   Probléma háríthatja el az SDK naplózásának engedélyezése. A naplózás engedélyezése hatással lehet a teljesítmény, a legjobb, az engedélyezéshez, csak akkor, ha a problémák elhárítása. Engedélyezheti a következő naplók kapcsolódnak:
    *   [Bejelentkezés metrikus](monitor-accounts.md) az Azure portal használatával. Portál mérőszámai megjelenítése az Azure Cosmos DB telemetriai adatokat, ami hasznos lehet meghatározni, ha a probléma felel meg az Azure Cosmos DB, vagy ha az ügyfél oldaláról.
    *   Napló a [diagnosztikai karakterlánc](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) a pont művelet válaszok.
    *   Napló a [SQL-lekérdezés metrikák](sql-api-query-metrics.md) , a lekérdezésre adott válasz 
    *   Kövesse a telepítés a [SDK naplózása]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

Vessen egy pillantást a [gyakori problémák és megoldások](#common-issues-workarounds) szakasz ebben a cikkben.

Ellenőrizze a [GitHub-problémák szakasz](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) , amely aktív figyelés alatt. Ellenőrizze, hogy ha bármilyen hasonló probléma áthidaló már be. Ha nem talál megoldást, majd a fájl egy GitHub-problémát. Megnyithat egy támogatási osztásjelek sürgős problémák esetén.


## <a name="common-issues-workarounds"></a>Gyakori problémák és megoldások

### <a name="general-suggestions"></a>Általános javaslatok
* Az alkalmazás futtatása az Azure Cosmos DB-fiókot, amikor csak lehetséges, az azonos Azure-régióban. 
* Kapcsolat/rendelkezésre állási problémák miatt megadtuk az ügyfélgépen erőforrások hiánya. Javasoljuk, hogy a CPU-kihasználtság, az Azure Cosmos DB-ügyfelet futtató csomópontot a figyelés, és a méretezés fel/az időkorlátot, ha nagy terhelés futnak.

### <a name="check-the-portal-metrics"></a>Ellenőrizze a portál mérőszámai
Ellenőrzi a [portál mérőszámai](monitor-accounts.md) segít meghatározni, ha egy ügyféloldali hiba történt, vagy ha a probléma a szolgáltatással. Például ha a metrikák tartalmaznak (HTTP-állapotkódot 429-es) sebessége korlátozott kérelmek gyakorisága a kérelem szabályozott első, ami azt jelenti, majd ellenőrizze a [Kérelmek száma túl nagy] szakaszban. 

### <a name="request-timeouts"></a>Kérelem időtúllépése
RequestTimeout általában akkor fordul elő, közvetlen/TCP használatakor, de Gateway módban történik is. Ezek a gyakori ismert okairól, és javaslatokat a probléma elhárításához.

* Processzorhasználat túl magas, amely fog késést okozhat és/vagy a kérelem időtúllépése. Az ügyfél is skálázza fel a gazdagépen gépet adjon meg további erőforrások, vagy a terhelés több gépet szét lehetnek osztva.
* A szoftvercsatorna / lehet, hogy a Port elérhetősége alacsony. A 2.0-s verzió előtt kiadott .NET SDK-k használatakor az Azure-ban futó ügyfelek sikerült eléri a [Az Azure SNAT (PAT) portfogyás]. Ez miért javasoljuk, hogy minden esetben az SDK legújabb verzióját futtató példát.
* Több DocumentClient-példány létrehozása kapcsolat versengés és időtúllépési problémák vezethet. Kövesse a [teljesítménnyel kapcsolatos tippek](performance-tips.md), és a egy teljes folyamat egyetlen DocumentClient-példányt használja.
* Felhasználók néha tekintse meg az emelt szintű várakozási ideje vagy a kérelem időtúllépése, mert azok gyűjtemények nem megfelelően kiépített szabályozza a kéréseket a háttér- és az ügyfél belsőleg anélkül, hogy a hívó számára felszínre hozza a újrapróbálkozik. Ellenőrizze a [portál mérőszámai](monitor-accounts.md).
* Az Azure Cosmos DB a teljes kiosztott átviteli sebesség egyenletesen elosztja fizikai partíciók között. Ellenőrizze a portál mérőszámai megtekintheti, ha a munkaterhelés léptek fel a gyors [partíciókulcs](partition-data.md). Ennek következtében az összesített felhasznált átviteli sebesség (RU/s) a fenntartott egységek alapján kell megjelenítendő, de egy egyetlen partíció felhasznált átviteli sebesség (RU/s) meghaladja a kiosztott átviteli sebesség. 
* Ezenkívül a 2.0-s SDK csatorna szemantika ad közvetlen/TCP-kapcsolatok. Egyszerre több kérést egy TCP-kapcsolat használható. Bizonyos esetekben a két problémák vezet:
    * Egyidejűségi magas fokú versengés vezethet a csatornán.
    * Nagyméretű kérelmek és válaszok vezethet a head-az-blokkolása a csatornán, és elmérgesítik azokat a versengés, egyidejűségi viszonylag kis mértékű mellett is.
    * Ha az eset esik, a két kategória egyikében (vagy ha a magas CPU-kihasználtság gyanús), ezek a lehetséges megoldások:
        * Próbálja meg az alkalmazás mentése és kimenő skálázhatja.
        * Ezenkívül SDK-naplókat rögzíthetők [Hívásláncfigyelő](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) további információért.

### <a name="connection-throttling"></a>Kapcsolat-szabályozás
Kapcsolat szabályozás feltehetően egy kapcsolathoz megadott korlátot gazdagépen. 2.0-s vissza az Azure-ban futó ügyfelek találati sikerült a [Az Azure SNAT (PAT) portfogyás].

### <a name="snat"></a>Az Azure SNAT (PAT) portfogyás

Ha az alkalmazás van üzembe helyezve az Azure Virtual machines szolgáltatásban egy nyilvános IP-cím nélkül alapértelmezés szerint [Azure SNAT portok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) minden végpontot a virtuális Gépen kívüli kapcsolatokat hozhat létre. A virtuális gépről az Azure Cosmos DB végpont engedélyezett kapcsolatainak számát korlátozza a [Azure SNAT konfigurációs](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Az Azure SNAT portok szolgálnak, csak akkor lehetséges, ha a virtuális gép magánhálózati IP-címmel rendelkezik, és a egy folyamatot, a virtuális gépről megpróbál csatlakozni a nyilvános IP-címet. Nincsenek Azure SNAT korlátozás elkerülése érdekében a két lehetséges megoldások:

* Az Azure Cosmos DB-szolgáltatásvégpont hozzáadása az Azure Virtual Machines virtuális hálózat alhálózatának. További információkért lásd: [Azure virtuális hálózati Szolgáltatásvégpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Ha a szolgáltatásvégpont engedélyezve van, a már nem küld a nyilvános IP-cím az Azure Cosmos DB. Ehelyett a virtuális hálózat és alhálózat identitást kapnak. Ez a változás tűzfal csepp eredményezhet, ha csak nyilvános IP-címek használata engedélyezett. Ha a szolgáltatásvégpont engedélyezésekor, tűzfalat használ, adjon hozzá egy alhálózatot a tűzfal használatával [virtuális hálózati hozzáférés-vezérlési listák](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Az Azure virtuális gép nyilvános IP-cím hozzárendelése.

### <a name="http-proxy"></a>A HTTP-proxy
Ha egy HTTP-proxyt használ, ellenőrizze, hogy tudja támogatni az SDK-ban konfigurált kapcsolatok száma `ConnectionPolicy`.
Ellenkező esetben között kapcsolódási problémák.

### Kérelmek száma túl nagy<a name="request-rate-too-large"></a>
"Kérelem túl nagy sebesség" vagy a 429-es hibakód azt jelzi, hogy a kérelmek van szabályozva, mert a felhasznált átviteli sebesség (RU/s) túllépte a kiosztott átviteli sebesség. Az SDK automatikusan újrapróbálkozik a kérelmet a megadott alapján [újrapróbálkozási szabályzat](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet). Ha a hiba gyakran kap, érdemes megfontolni az átviteli sebességet a gyűjteményben. Ellenőrizze a [portál metrikák](use-metrics.md) megtekintheti, ha azért kapta 429 hibákat. Tekintse át a [partíciókulcs](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) azt eredményezi, tárolási és a kérés a kötet az egyenletes eloszlás biztosítása érdekében. 

### <a name="slow-query-performance"></a>Lassú lekérdezések teljesítménye
A [metrikák lekérdezése](sql-api-query-metrics.md) segít meghatározni, ha a lekérdezés a legtöbbször tölti. A lekérdezés mérőszámokat, láthatja, hogy mennyi folyamatban van, az ügyfél a háttér-vs töltött.
* Ha a háttér-lekérdezés gyorsan adja vissza, és az ügyfél nagy időt tölt ellenőrizze a gép terhelése. Valószínű, hogy nem áll elegendő erőforrás, és az SDK-t arra vár, hogy erőforrásokat lehessen elérni a válasz kezelésére.
* Ha a háttér-lekérdezés lassú próbálja [a lekérdezés optimalizálása](optimize-cost-queries.md) és az aktuális [indexelési szabályzat](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Kérelmek száma túl nagy]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Az Azure SNAT (PAT) portfogyás]: #snat
[Production check list]: #production-check-list


