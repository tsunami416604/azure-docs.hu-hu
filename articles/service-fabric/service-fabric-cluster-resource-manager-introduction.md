---
title: A Service Fabric fürterőforrás-kezelőjének bemutatása
description: Ismerje meg a Service Fabric fürterőforrás-kezelő, az alkalmazás szolgáltatásaivezésének kezelésének egyik módja.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: da9205f5d95eaf1b4dc655ee727ab8a4fe90893d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563326"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>A Service Fabric-fürt erőforrás-kezelőjének bemutatása
Az informatikai rendszerek vagy online szolgáltatások hagyományos kezelése azt jelentette, hogy meghatározott fizikai vagy virtuális gépeket az adott szolgáltatásoknak vagy rendszereknek kell eltartaniuk. A szolgáltatások a szintek ként voltak megtervezve. Lenne egy "webes" szint és egy "adat" vagy "tárolási" szint. Az alkalmazások rendelkeznek egy üzenetkezelési szinttel, ahol a kérelmek be- és kiáramlanak, valamint a gyorsítótárazásra dedikált gépek készlete. Minden egyes szint vagy típusú számítási feladatok külön gépek dedikált hozzá: az adatbázis kapott egy pár gép dedikált hozzá, a webkiszolgálók néhány. Ha egy adott típusú számítási feladatok miatt a gépek volt túl forró, majd hozzáadott több gép ugyanazzal a konfigurációval, hogy a szint. Azonban nem minden számítási feladatok lehet horizontálisan olyan könnyen - különösen az adatréteg általában a gépek helyett nagyobb gépek. Nem nehéz. Ha egy gép meghibásodott, a teljes alkalmazás azon része alacsonyabb kapacitással futott, amíg a gép vissza nem állítható. Még mindig meglehetősen egyszerű (ha nem feltétlenül szórakoztató).

Most azonban a világ a szolgáltatás és a szoftver architektúra megváltozott. Sokkal gyakoribb, hogy az alkalmazások egy kibővített kialakítást fogadtak el. Alkalmazások létrehozása tárolók vagy mikroszolgáltatások (vagy mindkettő) gyakori. Most, bár lehet, hogy még csak néhány gép, ők nem fut csak egy példányban a számítási feladatok. Előfordulhat, hogy egyszerre több különböző számítási feladatot is futtatnak. Most már több tucat különböző típusú szolgáltatások (egyik sem fogyaszt egy teljes gép értékű források), talán több száz különböző példányai ezeket a szolgáltatásokat. Minden elnevezett példány rendelkezik egy vagy több példányvagy replikák magas rendelkezésre állású (HA). A számítási feladatok méretétől és a ttól függően, hogy mennyire vannak elfoglalva, több száz vagy több ezer géppel találhatja magát. 

A környezet hirtelen kezelése nem olyan egyszerű, mint néhány, az egyes típusú számítási feladatoknak szentelt gép kezelése. A szerverek virtuálisak, és már nincs nevük (a háziállatokról a [szarvasmarhákra](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) váltottak). A konfiguráció kevésbé a gépekről szól, és inkább magukról a szolgáltatásokról. Hardver, amely elkötelezett egyetlen példánya a munkaterhelés nagyrészt a múlté. Maguk a szolgáltatások kis elosztott rendszerekké váltak, amelyek több kisebb áruhardverre is kiterjednek.

Mivel az alkalmazás már nem monolitok sorozata több rétegben elosztva, most már sokkal több kombinációval kell foglalkoznia. Ki dönti el, hogy milyen típusú számítási feladatok futtathatók, milyen hardveren, vagy hány? Mely munkaterhelések működnek jól ugyanazon a hardveren, és milyen ütközések? Ha egy gép leáll, honnan tudod, mi futott ott azon a gépen? Ki a felelős annak biztosításáért, hogy a munkaterhelés újra elinduljon? Megvárja, amíg a (virtuális?) gép visszatér, vagy a számítási feladatok automatikusan átadják a feladatokat más gépeknek, és továbbra is futnak? Szükség van-e emberi beavatkozásra? Mi a helyzet a frissítéseket ebben a környezetben?

Mivel a fejlesztők és az üzemeltetők foglalkozó ebben a környezetben, mi lesz szüksége nk segítséget kezelni ezt a komplexitást. A bérleti mértéktelen, és megpróbálja elrejteni a komplexitás az emberek valószínűleg nem a helyes válasz, akkor mit tegyünk?

## <a name="introducing-orchestrators"></a>Bemutatkozik a vezénylők
Az "Orchestrator" az általános kifejezés egy szoftver, amely segít a rendszergazdáknak kezelni az ilyen típusú környezetek. Orchestrators az összetevők, hogy vegye a kérelmek, mint a "Szeretnék öt példányban ez a szolgáltatás fut az én környezetben." Megpróbálják, hogy a környezet megfeleljen a kívánt állapot, nem számít, mi történik.

Orchestrators (nem emberek) azok, amelyek lépéseket, ha egy gép meghibásodik, vagy a számítási feladatok leáll valamilyen váratlan okból. A legtöbb zenekari nem csak a kudarcokkal foglalkozik. Az általuk rendelkező egyéb funkciók az új telepítések kezelése, a frissítések kezelése, valamint az erőforrás-felhasználás és a szabályozás kezelése. Minden orchestrators alapvetően a környezetben kívánt konfigurációs állapot fenntartásáról szól. Azt akarod, hogy képes legyen megmondani egy orchestrator, mit akar, és ez nem a nehéz emelés. Az Aurora a Mesos, a Docker Datacenter/Docker Swarm, a Kubernetes és a Service Fabric mellett az orchestrators példái. Ezeket a vezénylőket aktívan fejlesztik, hogy megfeleljenek az éles környezetekben a valós munkaterhelések igényeinek. 

## <a name="orchestration-as-a-service"></a>Vezénylés szolgáltatásként
A fürterőforrás-kezelő az a rendszerösszetevő, amely a Service Fabric vezénylését kezeli. A fürterőforrás-kezelő feladata három részre van bontva:

1. Szabályok érvényesítése
2. A környezet optimalizálása
3. Segítség más folyamatokban

### <a name="what-it-isnt"></a>Mi az, ami nem?
A hagyományos N szintű alkalmazásokban mindig van [terheléselosztó.](https://en.wikipedia.org/wiki/Load_balancing_(computing)) Ez általában egy hálózati terheléselosztó (NLB) vagy egy application load balancer (ALB) volt attól függően, hogy hol ült a hálózati veremben. Egyes terheléselosztók hardveralapúak, mint például az F5 BigIP ajánlata, mások szoftveralapúak, például a Microsoft hálózati terheléselosztása. Más környezetekben előfordulhat, hogy ebben a szerepkörben a HAProxy, nginx, Istio vagy Envoy. Ezekben az architektúrákban a terheléselosztás feladata annak biztosítása, hogy az állapot nélküli munkaterhelések (nagyjából) azonos mennyiségű munkát kapjanak. A terhelés kiegyensúlyozására vonatkozó stratégiák változatosak voltak. Egyes kiegyensúlyozók minden egyes hívást egy másik szerverre küldenek. Mások biztosított session pinning / stickiness. A fejlettebb kiegyensúlyozók a tényleges terhelésbecslést vagy a jelentéskészítést használják a hívás irányításához a várható költség és az aktuális gépterhelés alapján.

A hálózati kiegyensúlyozók vagy üzenetútválasztók igyekeztek biztosítani, hogy a webes/feldolgozói szint nagyjából kiegyensúlyozott maradjon. Az adatszint kiegyensúlyozására szolgáló stratégiák eltérőek voltak, és az adattárolási mechanizmustól függtek. Az adatréteg kiegyensúlyozása az adat-szabályozási, gyorsítótárazási, felügyelt nézetek, tárolt eljárások és egyéb üzletspecifikus mechanizmusok alapján.

Míg néhány ilyen stratégiák érdekes, a Service Fabric fürterőforrás-kezelő nem más, mint egy hálózati terheléselosztó vagy a gyorsítótár. A hálózati terheléselosztó kiegyensúlyozza az előtér-rendszereket az előtér-rendszerek közötti forgalom szétosztásával. A Service Fabric fürterőforrás-kezelő egy másik stratégia. Alapvetően a Service Fabric áthelyezi a *szolgáltatásokat* oda, ahol a legtöbb értelme van, és a forgalom vagy a terhelés követésére számít. Előfordulhat például, hogy áthelyezi a szolgáltatásokat olyan csomópontokra, amelyek jelenleg hidegek, mert az ott lévő szolgáltatások nem végeznek sok munkát. A csomópontok hidegek lehetnek, mivel a jelen lévő szolgáltatásokat törölték vagy máshová helyezték át. Egy másik példa, hogy a fürterőforrás-kezelő is áthelyezhet egy szolgáltatást a gépről. Lehet, hogy a gép hamarosan frissíteni, vagy túlterhelt miatt egy tüske a fogyasztás a rajta futó szolgáltatások. Másik lehetőségként a szolgáltatás erőforrás-szükséglete növekedhet. Ennek eredményeképpen nincs elegendő erőforrás ezen a számítógépen a futtatás folytatásához. 

Mivel a fürterőforrás-kezelő felelős a szolgáltatások áthelyezéséért, más szolgáltatáskészletet tartalmaz, mint amit a hálózati terheléselosztóban találna. Ennek az az oka, hogy a hálózati terheléselosztók hálózati forgalmat szállítanak oda, ahol a szolgáltatások már vannak, még akkor is, ha az adott hely nem ideális a szolgáltatás futtatásához. A Service Fabric fürterőforrás-kezelő alapvetően eltérő stratégiákat alkalmaz annak biztosítására, hogy a fürt erőforrásait hatékonyan használják ki.

## <a name="next-steps"></a>További lépések
- A fürterőforrás-kezelőarchitalitan belüli architektúráról és információáramlásról a [cikkben](service-fabric-cluster-resource-manager-architecture.md) olvashat.
- A fürterőforrás-kezelő számos lehetőséget kínál a fürt leírására. Ha többet szeretne megtudni a metrikákról, olvassa el ezt a cikket [a Service Fabric-fürt leírásáról](service-fabric-cluster-resource-manager-cluster-description.md)
- A szolgáltatások konfigurálásával kapcsolatos további [tudnivalókért](service-fabric-cluster-resource-manager-configure-services.md)
- Metrikák, amelyek a Service Fabric fürterőforrás-kezelő kezeli a fürt ben a fogyasztás és a kapacitás. Ha többet szeretne megtudni a metrikákról és azok konfigurálásáról, olvassa el [ezt a cikket](service-fabric-cluster-resource-manager-metrics.md)
- A fürterőforrás-kezelő együttműködik a Service Fabric felügyeleti képességeivel. Ha többet szeretne megtudni az integrációról, olvassa el [ezt a cikket](service-fabric-cluster-resource-manager-management-integration.md)
- Ha meg szeretné tudni, hogy a fürterőforrás-kezelő hogyan kezeli és egyensúlyozza ki a terhelést a fürtben, olvassa el a [terhelés elosztásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
