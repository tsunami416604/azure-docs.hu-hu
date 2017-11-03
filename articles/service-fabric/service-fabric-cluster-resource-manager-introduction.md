---
title: "A Service Fabric fürt erőforrás-kezelő bemutatása |} Microsoft Docs"
description: "Bevezetés a Service Fabric fürt Resource Manager."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3e8cd4dc8e960e38ba0e4a9a195b2f61d9ec1924
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>A Service Fabric fürt erőforrás-kezelő bemutatása
Hagyományosan az informatikai rendszerek vagy online szolgáltatások kezelése azt jelentette, hogy ezeket a meghatározott szolgáltatásokat vagy rendszerek adott fizikai vagy virtuális gépek dedikálni. Szolgáltatások volt tervezett rétegek szerint. A "web" réteghez és egy "data" vagy "tároló" réteget lenne. Alkalmazások kellene egy üzenetkezelési réteget, ahol a kérelmek növelésére és csökkentésére, valamint a gyorsítótárazás dedikált gépek halmazát jelenti érkezett. Minden egyes réteg vagy típusú volt kijelölve, a konkrét gépek: az adatbázis lett kijelölve, a webkiszolgálók, a kevés néhány gépek. Ha egy munkaterhelés bizonyos típusú be volt a gépek futtatása túl gyakran használt adatok, majd további gépeket, hogy azonos konfigurációval hozzá adott réteg sablonbeállításait. Azonban nem minden munkaterhelések így könnyen sikerült terjeszthető ki – különösen az adatréteg rendelkező cserélje általában a gépek, a nagyobb gépek. Egyszerű. Ha egy gép sikertelen volt, a teljes alkalmazás a része alacsonyabb kapacitással futott, mindaddig, amíg a gép lehetett visszaállítani. Továbbra is meglehetősen egyszerű (Ha ez nem szükségszerűen szórakoztató).

Most már azonban a globális szolgáltatás és szoftver-architektúra megváltozott. Gyakori, hogy az alkalmazások alkalmazó kibővített kialakítást is. A tárolók vagy mikroszolgáltatások alkalmazásokat (vagy mindkettő) közös. Most csak néhány gépek továbbra is lehet, amíg azok nem futtatja a munkaterhelés csak egyetlen példányát. Ezek akkor is futtathatnak több különböző terhelésekhez egyszerre. Most már rendelkezik különböző szolgáltatások (nincs fel egy teljes számítógép alatt érkezett erőforrások), több tucatnyi akár több száz ezek a szolgáltatások különböző példányai. Minden elnevezett példány rendelkezik egy vagy több példány vagy a replikákat a magas rendelkezésre állású (HA). Attól függően, hogy ezeket a munkaterheléseket, és hogyan foglalt méretű több száz vagy ezer gépek előfordulhat magát. 

Hirtelen kezelése a környezet többé már nem így egyszerűen átjárófelügyeleti feladatokat lát el egy munkaterhelés-típusok néhány számítógép kezelése. A kiszolgálók virtuális, illetve neve nem lehet (a mindsets rendelkezik váltott [való szarvasmarha kedvtelésből](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) összes). Konfigurációs értéke kisebb a gépek és a további információk a szolgáltatásoknak. Hardver, amely egyetlen példány futhat a munkaterhelés nagyjából egy dolog of a múltban. Maguk szolgáltatások váltak kis elosztott rendszerek, amelyek több több kisebb kódrészletek hagyományos hardvereken.

Az alkalmazás már nincs elosztva a több rétegek monoliths sorozata, mert már számos további kombinációk kezelésére. Akik úgy dönt, hogy milyen típusú munkaterheléseket futtatható hardver, vagy hogy hány? Melyik munkaterhelések is ugyanazon a hardveren, és amelyek ütköznek? Amikor olyan gép lefelé hogyan tudja, hogy mi az, hogy a gép nem futott? Ki van feladata meggyőződött arról, hogy munkaterhelés újra futásának indításakor? Várjon a (virtuális?) gép térjen vissza, vagy hajtsa végre a munkaterhelések automatikusan áthelyezze a feladatokat más gépek és a futtatása során is garantálják? Az emberi beavatkozás szükséges? Mi a helyzet a frissítéseket az ebben a környezetben?

A fejlesztők és operátorok foglalkozó ebben a környezetben fogjuk összetettség kezelése segítségre van szüksége. Alkalmazási binge és elrejtése személyekkel összetettségét próbál valószínűleg nem a megfelelő választ, így Mi a teendő?

## <a name="introducing-orchestrators"></a>Orchestrators bemutatása
Egy "Orchestrator" egy adott szoftver, amely segítségével a rendszergazdák az ilyen típusú környezetben kezelése az általános kifejezés. Orchestrators az összetevőket, amelyek a kérelmek igénybe vehet, például "Szeretnék a saját környezetben futó szolgáltatás öt példánya." A környezeti felel meg a kívánt állapot, függetlenül attól, mi történik, hogy próbálja meg.

(Nem emberek) orchestrators, mi a művelet végrehajtása, és a gép meghibásodik, vagy egy munkaterhelés valamilyen nem várt okból leáll. A legtöbb orchestrators csupán foglalkoznak. Egyéb szolgáltatások rendelkeznek-e új frissítések kezelése, és a hálózatierőforrás-fogyasztás és irányítási foglalkozó központi telepítések kezelt. Minden orchestrators alapvetően kerül a konfiguráció a környezetben kívánt állapot fenntartása. Szeretne tudni az orchestrator mondja el, mi azt szeretné, és annak a gyakori emelő tegye. Az Aurora Mesos, Docker Datacenter/Docker Swarm, Kubernetes és a Service Fabric fölött példák összes orchestrators. Ezek orchestrators aktívan fejlesztenek éles környezetben valós munkaterhelések igényeinek. 

## <a name="orchestration-as-a-service"></a>Vezénylési szolgáltatásként
A fürt erőforrás-kezelő a rendszer összetevője, amely kezeli a Service Fabric vezénylési. A fürt Resource Manager feladat három részből oszlik:

1. Szabályok érvényesítése
2. A környezet optimalizálása
3. Útmutatás nyújtása a más folyamatokkal

Tekintse meg a fürt erőforrás-kezelő működése, a következő Microsoft Virtual Academy videót:<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=d4tka66yC_5706218965">
<img src="./media/service-fabric-cluster-resource-manager-introduction/ConceptsAndDemoVid.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="what-it-isnt"></a>Mi nem
A hagyományos N rétegből álló alkalmazások, mindig fennáll a [terheléselosztó](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Általában ez volt, a hálózati terheléselosztó (NLB) vagy egy alkalmazás Load Balancer (ALB) attól függően, hogy amennyiben ez volt a a hálózati vermet. Néhány terheléselosztók meg az F5 BigIP ajánlat például hardveralapú, mások olyan szoftveres például a Microsoft által a hálózati Terheléselosztás. Más környezetekben megjelenhet valami haproxy esetén, nginx, Istio vagy ügyféloldali ebben a szerepkörben. Ezek architektúrákban a terheléselosztás feladata, állapot nélküli munkaterheléseket fogadása (körülbelül) akkora munka. Terheléselosztás kapcsolatos olyan stratégiák változatos betölteni. Néhány terheléselosztók elküldése minden más hívási egy másik kiszolgálóra. Mások biztosítja, hogy a rögzítési munkamenet/tölcsérútvonalak. Speciális terheléselosztók tényleges betöltést becslés vagy a jelentés segítségével egy hívás a várt és jelenlegi gép terhelés alapján továbbítja.

Hálózati terheléselosztó vagy üzenet útválasztók próbált győződjön meg arról, hogy a webes/munkavégző réteg nagyjából kiegyensúlyozott maradt. Az adatréteghez tartozó terheléselosztási kapcsolatos olyan stratégiák különböző és az adatok tárolási mechanizmus a függő volt. Adatok horizontális gyorsítótárazáshoz, olyan felügyelt nézetek, a tárolt eljárások és az egyéb tároló-specifikus mechanizmusok az adatréteghez tartozó terheléselosztási hivatkozni.

Ezek stratégiák némelyike érdekes, amíg a Service Fabric fürt erőforrás-kezelő nincs semmi például egy hálózati terheléselosztó vagy a gyorsítótár. Hálózati terheléselosztó frontends frontends közötti forgalom elosztásával egyensúlyba kerüljön. A Service Fabric fürt erőforrás-kezelő rendelkezik a különböző stratégiát. Alapvetően, helyezi át a Service Fabric *szolgáltatások* ahol azok célszerű a legtöbb, a várt forgalom, és kövesse betöltése. Például, előfordulhat, hogy helyezze át szolgáltatások, amelyek jelenleg cold, mert a szolgáltatásokat, hogy vannak-e nem végez alkalmazásfejlesztőre csomópontok. A csomópontok cold lehet, mivel a jelen sikerült törölt vagy máshol áthelyezték. Másik példaként a fürt erőforrás-kezelő is helyezze át egy másik lapra a gép szolgáltatás. Lehet, hogy a gép van-e frissítve lesznek, és a rajta futó szolgáltatások túlterhelt egy csúcs az igények fogyasztás miatt. A szolgáltatás erőforrás-követelmények Alernatively, előfordulhat, hogy megnövelte. Ennek eredményeképpen nincs elegendő erőforrás ezen a gépen is fut a folytatáshoz. 

A fürt erőforrás-kezelő felelős körül szolgáltatások áthelyezésére, mert egy másik készlet egy hálózati terheléselosztó volna felfedése tartalmaz. Ennek az az oka hálózati terheléselosztók kézbesíthet hálózati forgalmat adott szolgáltatások már vannak, akkor is, ha az adott hely ideális nem fut a szolgáltatás. A Service Fabric fürt erőforrás-kezelő funkcióit használja annak biztosítására, hogy a fürtön hatékonyan ki van használva alapvetően különböző stratégiákat.

## <a name="next-steps"></a>Következő lépések
- Az architektúra és információk folyamat belül a fürt erőforrás-kezelő információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-architecture.md)
- A fürt erőforrás-kezelő rendelkezik a fürt leíró számos lehetőséget. Metrikák kapcsolatos további tudnivalókért tekintse meg a cikk a [leíró a Service Fabric-fürt](service-fabric-cluster-resource-manager-cluster-description.md)
- A szolgáltatások konfigurálásáról [szolgáltatások konfigurálásával kapcsolatos tudnivalók](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Adatok gyűjtése le hogyan kezeli a Service Fabric fürt erőforrás-kezelő a használati és a fürt teljes kapacitását. További információt a metrikák és konfigurálásukról kivételének [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md)
- A fürt erőforrás-kezelő szolgáltatás Hálófelügyeleti képességek működik. Ha szeretné tudni, hogy integrációról további információkat, olvassa el a [Ez a cikk](service-fabric-cluster-resource-manager-management-integration.md)
- Hogyan kezeli a fürt erőforrás-kezelő, és elosztja a terhelést a fürt kapcsolatos további tudnivalókért tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)
