---
title: A Azure Cosmos DB .NET SDK használatakor felmerülő problémák diagnosztizálása és hibaelhárítása
description: A .NET SDK használatakor olyan szolgáltatásokat használhat, mint az ügyféloldali naplózás és más külső eszközök a Azure Cosmos DB problémák azonosításához, diagnosztizálásához és hibaelhárításához.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 51b37c43b94ad59090f32af0d57bbefaa57f30fa
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932555"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>A Azure Cosmos DB .NET SDK használatakor felmerülő problémák diagnosztizálása és hibaelhárítása
Ez a cikk általános problémákról, megkerülő megoldásokról, diagnosztikai lépésekről és eszközökről tartalmaz Azure Cosmos DB SQL API-fiókokkal rendelkező [.net SDK](sql-api-sdk-dotnet.md) -t használva.
A .NET SDK ügyféloldali logikai ábrázolást biztosít a Azure Cosmos DB SQL API eléréséhez. Ez a cikk azokat az eszközöket és módszereket ismerteti, amelyek segítségével a problémákba ütközik.

## <a name="checklist-for-troubleshooting-issues"></a>Ellenőrzőlista a hibaelhárítási problémákhoz:
Mielőtt éles környezetben áthelyezi az alkalmazást, vegye figyelembe a következő ellenőrzőlistát. Az ellenőrzőlista használatával több gyakori probléma is megjelenhet. A probléma megoldásához gyorsan is diagnosztizálhatja a problémát:

*   Használja a legújabb [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)-t. Az előzetes verziójú SDK-kat nem ajánlott éles környezetben használni. Ez megakadályozza a már kijavított ismert problémák elkerülését.
*   Tekintse át a [teljesítménnyel kapcsolatos tippeket](performance-tips.md), és kövesse a javasolt eljárásokat. Ez segít megakadályozni a skálázást, a késést és az egyéb teljesítménnyel kapcsolatos problémákat.
*   A probléma megoldásához engedélyezze az SDK-naplózást. A naplózás engedélyezése hatással lehet a teljesítményre, így csak hibaelhárítási problémák esetén ajánlott engedélyezni. A következő naplók engedélyezhetők:
    *   A [metrikák naplózása](monitor-accounts.md) a Azure Portal használatával. A portál metrikái a Azure Cosmos DB telemetria mutatják be, ami hasznos lehet annak megállapítására, hogy a probléma megfelel-e a Azure Cosmos DBnak, vagy az ügyfél oldaláról származik-e.
    *   Naplózza a [diagnosztikai karakterláncot](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) a pont műveleti válaszainak.
    *   Az [SQL-lekérdezési metrikák](sql-api-query-metrics.md) naplózása az összes lekérdezési válaszból 
    *   Az [SDK-naplózás]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) beállításának követése

Tekintse meg a jelen cikk [gyakori problémák és megoldások](#common-issues-workarounds) című szakaszát.

Ellenőrizze, hogy aktív-e a [GitHub-problémák szakasza](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) . Ellenőrizze, hogy a megkerülő megoldással kapcsolatos bármilyen hasonló probléma már be van-e jelölve. Ha nem talál megoldást, a GitHub-problémát is megteheti. A sürgős problémákhoz egy támogatási kullancsot is megnyithat.


## <a name="common-issues-workarounds"></a>Gyakori problémák és megkerülő megoldások

### <a name="general-suggestions"></a>Általános javaslatok
* Ha lehetséges, futtassa az alkalmazást ugyanabban az Azure-régióban, mint a Azure Cosmos DB-fiókját. 
* Az ügyfélszámítógépen lévő erőforrások hiánya miatt előfordulhat, hogy kapcsolati vagy rendelkezésre állási problémákba ütközik. Javasoljuk, hogy a CPU-kihasználtságot a Azure Cosmos DB-ügyfelet futtató csomópontokon figyelje, és ha nagy terhelésen futnak, fel kell skálázást.

### <a name="check-the-portal-metrics"></a>A portál metrikáinak megtekintése
A [portál metrikáinak](monitor-accounts.md) ellenőrzése segít meghatározni, hogy az ügyféloldali probléma-e, vagy hogy van-e probléma a szolgáltatással. Ha például a mérőszámok nagy arányban korlátozott kérelmeket tartalmaznak (a 429-as HTTP-állapotkód), ami azt jelenti, hogy a kérést a rendszer lekéri, akkor ellenőrizze, hogy a [Túl nagy a kérelmek aránya] . 

### <a name="request-timeouts"></a>Kérelmek időtúllépései
A RequestTimeout általában a Direct/TCP használatakor fordul elő, de az átjáró módban is történhet. Ezek a leggyakoribb ismert okok, és javaslatok a probléma megoldására.

* A CPU-kihasználtság magas, ami késést és/vagy kérelmek időtúllépését okozhatja. Az ügyfél vertikális felskálázást végez a gazdagépen, hogy több erőforrást biztosítson, vagy ha a terhelés több gépen is elosztható.
* A szoftvercsatorna/port rendelkezésre állása alacsony lehet. Az Azure-ban való futtatáskor a .NET SDK-t használó ügyfelek elérhetik az Azure SNAT (PAT) portjának kimerülését. Ha csökkenteni szeretné a probléma előfordulásának esélyét, használja a .NET SDK legújabb, 2. x vagy 3. x verzióját. Ez egy példa arra, hogy miért javasoljuk, hogy mindig futtassa a legújabb SDK-verziót.
* Több DocumentClient-példány létrehozása a kapcsolati és időtúllépési problémákhoz vezethet. Kövesse a [teljesítménnyel kapcsolatos tippeket](performance-tips.md), és használjon egyetlen DocumentClient-példányt egy teljes folyamaton belül.
* A felhasználók időnként emelt szintű késést vagy kérelmek időtúllépését láthatják, mert a gyűjtemények nem megfelelő módon vannak kiépítve, a háttér-szabályozási kérések, az ügyfél pedig belső próbálkozások nélkül próbálkozik a hívóval. Keresse meg a [portál metrikáit](monitor-accounts.md).
* A Azure Cosmos DB a teljes kiosztott átviteli sebességet egyenletesen osztja el a fizikai partíciók között. Tekintse meg a portál metrikáit, és ellenőrizze, hogy a számítási feladatok egy gyors [partíciós kulcson](partition-data.md)futnak-e. Ez azt eredményezi, hogy az összesített felhasznált átviteli sebesség (RU/s) úgy tűnik, hogy a kiépített RUs alatt legyenek, de egyetlen partíción felhasznált átviteli sebesség (RU/s) túllépi a kiosztott átviteli sebességet. 
* Emellett az 2,0 SDK a csatornák szemantikai feladását is hozzáadja a közvetlen/TCP-kapcsolatokhoz. Egy TCP-kapcsolatok egyszerre több kérelemhez használatosak. Ez két problémát eredményezhet bizonyos esetekben:
    * A magas fokú párhuzamosság a csatornán való kivezetéshez vezethet.
    * A nagyméretű kérelmek vagy válaszok a csatornán kívüli blokkolást okozhatnak a csatornán, és súlyosbítják a versengés mértékét, akár viszonylag alacsony fokú párhuzamosságtal is.
    * Ha az eset mindkét kategóriába tartozik (vagy ha a magas CPU-kihasználtság gyanúja van), akkor ezek lehetséges enyhítések:
        * Próbálja meg az alkalmazás felskálázását.
        * Emellett a [nyomkövetési figyelővel](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) is rögzítheti az SDK-naplókat, így további részleteket érhet el.

### <a name="connection-throttling"></a>Kapcsolatok szabályozása
A kapcsolatok szabályozása a gazdagép egy hálózati korlátja miatt fordulhat elő. A 2,0-as verzióban az Azure-ban futó ügyfelek az [Az Azure SNAT (PAT) portjának kimerülése]tudták elérni.

### <a name="snat"></a>Az Azure SNAT (PAT) portjának kimerülése

Ha az alkalmazás nyilvános IP-cím nélküli Azure-Virtual Machines van telepítve, alapértelmezés szerint az [Azure SNAT-portok](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) kapcsolatot létesít a virtuális gépen kívüli végpontokkal. A virtuális gépről a Azure Cosmos DB végpont számára engedélyezett kapcsolatok számát az [Azure SNAT konfigurációja](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)korlátozza.

 Az Azure SNAT-portok használata csak akkor történik meg, ha a virtuális gép magánhálózati IP-címmel rendelkezik, és a virtuális gép egy folyamata megpróbál csatlakozni egy nyilvános IP-címhez. Az Azure SNAT-korlátozás elkerülése érdekében két Áthidaló megoldás létezik:

* Adja hozzá Azure Cosmos DB szolgáltatási végpontját az Azure Virtual Machines Virtual Network alhálózatához. További információ: [Azure Virtual Network Service-végpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). 

    Ha a szolgáltatási végpont engedélyezve van, a rendszer a kérelmeket már nem küldi el a nyilvános IP-címről Azure Cosmos DB. Ehelyett a rendszer elküldi a virtuális hálózatot és az alhálózati identitást. Ez a változás akkor okozhat tűzfalat, ha csak a nyilvános IP-címek engedélyezettek. Ha tűzfalat használ, a szolgáltatás végpontjának engedélyezésekor [Virtual Network ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl)-EK használatával adjon hozzá egy alhálózatot a tűzfalhoz.
* Rendeljen hozzá egy nyilvános IP-címet az Azure-beli virtuális géphez.

### <a name="http-proxy"></a>HTTP-proxy
Ha HTTP-proxyt használ, győződjön meg arról, hogy az képes támogatni az SDK `ConnectionPolicy`-ban konfigurált kapcsolatok számát.
Ellenkező esetben a csatlakoztatási problémákkal szembesül.

### Túl nagy a kérelmek aránya<a name="request-rate-too-large"></a>
"A kérelmek aránya túl nagy" vagy a 429-es hibakód azt jelzi, hogy a kérések szabályozása folyamatban van, mert a felhasznált átviteli sebesség (RU/s) túllépte a kiosztott átviteli sebességet. Az SDK automatikusan újrapróbálkozik a kérelmekkel a megadott [újrapróbálkozási házirend](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)alapján. Ha ez a hiba gyakran előfordul, érdemes lehet növelni az átviteli sebességet a gyűjteményen. Tekintse [meg a portál metrikáit](use-metrics.md) , és ellenőrizze, hogy a 429-es hibát észlel-e. Tekintse át a [partíciós kulcsot](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) , és győződjön meg arról, hogy a tárolók és a kérelmek mennyiségének egyenletes eloszlását eredményezi. 

### <a name="slow-query-performance"></a>Lassú lekérdezési teljesítmény
A [lekérdezési metrikák](sql-api-query-metrics.md) segítenek meghatározni, hogy a lekérdezés hol tölti le a legtöbb időt. A lekérdezési mérőszámokból megtekintheti, hogy mennyi időt tölt a háttér és a-ügyfél.
* Ha a háttérbeli lekérdezés gyorsan visszatér, és nagy időt tölt az ügyfélen, ellenőrizze a terhelést a gépen. Valószínű, hogy nincs elegendő erőforrás, és az SDK arra vár, hogy az erőforrások elérhetők legyenek a válasz kezelésére.
* Ha a háttérbeli lekérdezés lassú, próbálkozzon [a lekérdezés optimalizálásával](optimize-cost-queries.md) , és tekintse meg az aktuális [indexelési házirendet](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Túl nagy a kérelmek aránya]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Az Azure SNAT (PAT) portjának kimerülése]: #snat
[Production check list]: #production-check-list


