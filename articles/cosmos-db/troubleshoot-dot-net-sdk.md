---
title: Az Azure Cosmos DB .NET SDK használatakor felmerülő hibák diagnosztizálása és elhárítása
description: A .NET SDK használatakor olyan szolgáltatásokat használhat, mint az ügyféloldali naplózás és más külső eszközök a Azure Cosmos DB problémák azonosításához, diagnosztizálásához és hibaelhárításához.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 05/06/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c0f40b3c79c16046ef61e89cad72c714346d2674
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84672629"
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
A [portál metrikáinak](monitor-accounts.md) ellenőrzése segít meghatározni, hogy az ügyféloldali probléma-e, vagy hogy van-e probléma a szolgáltatással. Ha például a mérőszámok nagy arányban korlátozott kérelmeket tartalmaznak (a 429-as HTTP-állapotkód), ami azt jelenti, hogy a kérést a rendszer lekéri, akkor ellenőrizze, hogy a [kérelem sebessége túl nagy] . 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>Kérelmek időtúllépései
A RequestTimeout általában a Direct/TCP használatakor fordul elő, de az átjáró módban is történhet. Ezek a hibák a leggyakoribb ismert okok, és javaslatok a probléma megoldására.

* A CPU-kihasználtság magas, ami késést és/vagy kérelmek időtúllépését okozhatja. Az ügyfél vertikális felskálázást végez a gazdagépen, hogy több erőforrást biztosítson, vagy ha a terhelés több gépen is elosztható.
* A szoftvercsatorna/port rendelkezésre állása alacsony lehet. Az Azure-ban való futtatáskor a .NET SDK-t használó ügyfelek elérhetik az Azure SNAT (PAT) portjának kimerülését. Ha csökkenteni szeretné a probléma előfordulásának esélyét, használja a .NET SDK legújabb, 2. x vagy 3. x verzióját. Ez egy példa arra, hogy miért ajánlott mindig a legújabb SDK-verziót futtatni.
* Több DocumentClient-példány létrehozása a kapcsolati és időtúllépési problémákhoz vezethet. Kövesse a [teljesítménnyel kapcsolatos tippeket](performance-tips.md), és használjon egyetlen DocumentClient-példányt egy teljes folyamaton belül.
* A felhasználók időnként emelt szintű késést vagy kérelmek időtúllépését láthatják, mert a gyűjtemények nem megfelelő módon vannak kiépítve, a háttér-szabályozási kérelmek és az ügyfél belső újrapróbálkozások. Keresse meg a [portál metrikáit](monitor-accounts.md).
* A Azure Cosmos DB a teljes kiosztott átviteli sebességet egyenletesen osztja el a fizikai partíciók között. Tekintse meg a portál metrikáit, és ellenőrizze, hogy a számítási feladatok egy gyors [partíciós kulcson](partition-data.md)futnak-e. Ez azt eredményezi, hogy az összesített felhasznált átviteli sebesség (RU/s) úgy tűnik, hogy a kiépített RUs alatt legyenek, de egyetlen partíción felhasznált átviteli sebesség (RU/s) túllépi a kiosztott átviteli sebességet. 
* Emellett az 2,0 SDK a csatornák szemantikai feladását is hozzáadja a közvetlen/TCP-kapcsolatokhoz. Egy TCP-kapcsolatok egyszerre több kérelemhez használatosak. Ez két problémát eredményezhet bizonyos esetekben:
    * A magas fokú párhuzamosság a csatornán való kivezetéshez vezethet.
    * A nagyméretű kérelmek vagy válaszok a csatornán kívüli blokkolást okozhatnak a csatornán, és súlyosbítják a versengés mértékét, akár viszonylag alacsony fokú párhuzamosságtal is.
    * Ha az eset mindkét kategóriába tartozik (vagy ha a magas CPU-kihasználtság gyanúja van), akkor ezek lehetséges enyhítések:
        * Próbálja meg az alkalmazás felskálázását.
        * Emellett a [nyomkövetési figyelővel](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) is rögzítheti az SDK-naplókat, így további részleteket érhet el.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>Nagy hálózati késés
A hálózati késést a v2 SDK- [ban vagy a](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) v3 SDK-ban lévő diagnosztika [diagnosztikai karakterláncának](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) használatával lehet azonosítani.

Ha nincsenek [időtúllépések](#request-timeouts) , és a diagnosztika egyetlen kérést mutat be, ahol a nagy késés nyilvánvaló a és a közötti különbségnél `ResponseTime` `RequestStartTime` (>300 ezredmásodperc ebben a példában):

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

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Az Azure SNAT (PAT) portjának kimerülése

Ha az alkalmazás [nyilvános IP-cím nélküli Azure-Virtual Machines](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)van telepítve, alapértelmezés szerint az [Azure SNAT-portok](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) kapcsolatot létesít a virtuális gépen kívüli végpontokkal. A virtuális gépről a Azure Cosmos DB végpont számára engedélyezett kapcsolatok számát az [Azure SNAT konfigurációja](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)korlátozza. Ez a helyzet a kapcsolat szabályozásához, a kapcsolat bezárásához vagy a fent említett [kérelmek időtúllépéséhez](#request-timeouts)vezethet.

 Az Azure SNAT-portokat csak akkor használja a rendszer, ha a virtuális gép magánhálózati IP-címmel csatlakozik egy nyilvános IP-címhez. Az Azure SNAT-korlátozás elkerülése érdekében két Áthidaló megoldás létezik (ha már egyetlen ügyfél-példányt használ a teljes alkalmazásban):

* Adja hozzá Azure Cosmos DB szolgáltatási végpontját az Azure Virtual Machines Virtual Network alhálózatához. További információ: [Azure Virtual Network Service-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md). 

    Ha a szolgáltatási végpont engedélyezve van, a rendszer a kérelmeket már nem küldi el a nyilvános IP-címről Azure Cosmos DB. Ehelyett a rendszer elküldi a virtuális hálózatot és az alhálózati identitást. Ez a változás akkor okozhat tűzfalat, ha csak a nyilvános IP-címek engedélyezettek. Ha tűzfalat használ, a szolgáltatás végpontjának engedélyezésekor [Virtual Network ACL](../virtual-network/virtual-networks-acl.md)-EK használatával adjon hozzá egy alhálózatot a tűzfalhoz.
* Rendeljen hozzá egy [nyilvános IP-címet az Azure-beli virtuális géphez](../load-balancer/troubleshoot-outbound-connection.md#assignilpip).

### <a name="http-proxy"></a>HTTP-proxy
Ha HTTP-proxyt használ, győződjön meg arról, hogy az képes támogatni az SDK-ban konfigurált kapcsolatok számát `ConnectionPolicy` .
Ellenkező esetben a csatlakoztatási problémákkal szembesül.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>Túl nagy a kérelmek aránya
"A kérelmek aránya túl nagy" vagy a 429-es hibakód azt jelzi, hogy a kérések szabályozása folyamatban van, mert a felhasznált átviteli sebesség (RU/s) túllépte a [kiosztott átviteli sebességet](set-throughput.md). Az SDK automatikusan újrapróbálkozik a kérelmekkel a megadott [újrapróbálkozási házirend](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)alapján. Ha ez a hiba gyakran előfordul, érdemes lehet növelni az átviteli sebességet a gyűjteményen. Tekintse [meg a portál metrikáit](use-metrics.md) , és ellenőrizze, hogy a 429-es hibát észlel-e. Tekintse át a [partíciós kulcsot](partitioning-overview.md#choose-partitionkey) , és győződjön meg arról, hogy a tárolók és a kérelmek mennyiségének egyenletes eloszlását eredményezi. 

### <a name="slow-query-performance"></a>Lassú lekérdezési teljesítmény
A [lekérdezési metrikák](sql-api-query-metrics.md) segítenek meghatározni, hogy a lekérdezés hol tölti le a legtöbb időt. A lekérdezési mérőszámokból megtekintheti, hogy mennyi időt tölt a háttér és a-ügyfél.
* Ha a háttérbeli lekérdezés gyorsan visszatér, és nagy időt tölt az ügyfélen, ellenőrizze a terhelést a gépen. Valószínű, hogy nincs elegendő erőforrás, és az SDK arra vár, hogy az erőforrások elérhetők legyenek a válasz kezelésére.
* Ha a háttérbeli lekérdezés lassú, próbálkozzon [a lekérdezés optimalizálásával](optimize-cost-queries.md) , és tekintse meg az aktuális [indexelési házirendet](index-overview.md) 

### <a name="http-401-the-mac-signature-found-in-the-http-request-is-not-the-same-as-the-computed-signature"></a>HTTP 401: a HTTP-kérelemben található MAC-aláírás nem egyezik meg a számított aláírással
Ha a következő 401 hibaüzenetet kapta: "a HTTP-kérelemben található MAC-aláírás nem egyezik meg a számított aláírással." ezt a következő forgatókönyvek okozhatják.

1. A kulcsot elforgatták, és nem az [ajánlott eljárásokat](secure-access-to-data.md#key-rotation)követték. Általában ez a helyzet. Cosmos DB fiók kulcsának elforgatása eltarthat néhány másodperctől akár napokig is, az Cosmos DB fiók méretétől függően.
   1. 401 a MAC-aláírás röviddel a kulcs elforgatása után következik be, és végül leáll a módosítás nélkül. 
2. A kulcs helytelenül van konfigurálva az alkalmazásban, így a kulcs nem egyezik meg a fiókkal.
   1. 401 MAC-aláírási probléma konzisztens lesz, és az összes hívás esetén megtörténik
3. Létezik egy versenyhelyzet a tároló létrehozásával. Egy alkalmazás-példány megpróbál hozzáférni a tárolóhoz a tároló létrehozása után. A leggyakoribb példa erre, ha az alkalmazás fut, és a tároló törlődik, és az alkalmazás futása során ugyanazzal a névvel jön létre. Az SDK megpróbálja használni az új tárolót, de a tároló létrehozása még folyamatban van, így nem rendelkezik a kulcsokkal.
   1. 401 MAC-aláírási probléma röviddel a tároló létrehozása után következik be, és csak a tároló létrehozása után történik meg.
 
 ### <a name="http-error-400-the-size-of-the-request-headers-is-too-long"></a>400-es HTTP-hiba. A kérések fejlécének mérete túl hosszú.
 A fejléc mérete nagyra nőtt, és meghaladja a maximálisan megengedett méretet. A legújabb SDK-t mindig ajánlott használni. Ügyeljen arra, hogy legalább a [3. x](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md) vagy [2. x](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)verziót használja, amely hozzáadja a fejléc méretének nyomon követését a kivételt jelző üzenethez.

Okoz
 1. A munkamenet-jogkivonat túl nagyra nőtt. A munkamenet-jogkivonat nő, mint a partíciók számának növekedése a tárolóban.
 2. A folytatási token nagyra nőtt. A különböző lekérdezések a folytatási tokenek különböző méreteit fogják tartalmazni.
 3. Ezt a munkamenet-jogkivonat és a folytatási jogkivonat kombinációja okozza.

Megoldás:
   1. Kövesse a [teljesítménnyel kapcsolatos tippeket](performance-tips.md) , és alakítsa át az alkalmazást Direct + TCP-kapcsolatok módba. A Direct + TCP nem rendelkezik a fejléc méretére vonatkozó korlátozással, például a HTTP-vel, amely elkerüli ezt a problémát.
   2. Ha a munkamenet-token az OK, akkor ideiglenes megoldás az alkalmazás újraindítása. Az alkalmazás-példány újraindítása a munkamenet-token alaphelyzetbe állítását állítja vissza. Ha a kivételek az újraindítás után leállnak, akkor megerősíti, hogy a munkamenet-token oka. A rendszer végül a kivételt okozó méretre nő vissza.
   3. Ha az alkalmazás nem konvertálható a Direct + TCP értékre, és a munkamenet-token az OK, akkor a megoldás az ügyfél konzisztencia- [szintjének](consistency-levels.md)módosításával végezhető el. A munkamenet-jogkivonat csak a munkamenet-konzisztencia esetében használatos, amely a Cosmos DB alapértelmezett értéke. Bármely más konzisztencia-szint nem fogja használni a munkamenet-jogkivonatot. 
   4. Ha az alkalmazás nem alakítható át a Direct + TCP értékre, és a folytatási token az OK, akkor próbálja meg beállítani a ResponseContinuationTokenLimitInKb beállítást. A beállítás a v2-es vagy a v3-as QueryRequestOptions FeedOptions érhető el.

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Túl nagy a kérelmek aránya]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
