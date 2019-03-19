---
title: A Service Fabric fürterőforrás-kezelő bemutatása |} A Microsoft Docs
description: Bevezetés a Service Fabric fürt Resource Manager.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: cfab735b-923d-4246-a2a8-220d4f4e0c64
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 75aa960ff060d74d0a579b475e4334402992b3c3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903360"
---
# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>A Service Fabric fürterőforrás-kezelő bemutatása
Hagyományosan a IT-rendszereit és online szolgáltatások kezelésére hivatott adott fizikai vagy virtuális gépek dedikálni ezeket adott szolgáltatások vagy a rendszer. Szolgáltatások rétegek szerint lettek tervezésnek. A "webes" réteg és a egy "adatok" vagy "tároló" réteget lenne. Alkalmazások kellene egy üzenetkezelési szinttel, ahol kérelmek érkezett be és ki, valamint dedikált gyorsítótár-csoportokon. Minden szintet vagy a számítási feladat volt dedikált, meghatározott gépek: az adatbázis van kijelölve, a webkiszolgálók egy néhány néhány gépek. Ha egy adott típusú számítási feladatok miatt az volt a gépek futtassa túl gyakori elérésű, majd a további gépek ugyanazt a konfigurációt az adott réteg hozzáadott. Azonban nem minden munkaterhelésről így könnyen sikerült terjeszthető ki – különösen az adatréteg-cserélje általában a nagyobb gépek rendelkező gépek. Egyszerű. Ha egy gép elindítása nem sikerült, mindaddig, amíg a gép sikerült visszaállítani a kisebb kapacitással futott, hogy a teljes alkalmazás részét. Továbbra is könnyen (Ha ez nem feltétlenül szórakoztató).

Most azonban a szolgáltatás és szoftver-architektúra a világ megváltozott. Jelenleg egyre gyakoribb, hogy alkalmazásokat elfogadott kibővített kialakítást. A tárolókkal és mikroszolgáltatásokkal alkalmazásokat (vagy mindkettő) szokás. Most, előfordulhat, hogy továbbra is csak néhány olyan gépekkel rendelkezik, amíg azok nem futtat egy számítási feladat csak egyetlen példánya. Akkor esetleg még fut több különböző számítási feladatok egyszerre. Most már több tucatnyi különféle szolgáltatások (nincs erőforrásokat egy teljes gép adatmintát használ), esetleg több száz különböző példányai ezeket a szolgáltatásokat. Minden elnevezett példány rendelkezik egy vagy több példány vagy a replikákat a magas rendelkezésre állású (HA). Azokat a munkaterheléseket, és hogyan elfoglaltak méretét függően akár több száz vagy ezer gépre előfordulhat saját magának. 

Hirtelen kezelése a környezetben nem így egyszerű, mintha egyetlen típusú számítási feladatok számára kijelölt néhány gépek kezeléséhez. A kiszolgálók virtuális, és többé nem kell a nevek (vált a mindsets [szarvasmarha a kisállatok](https://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) Elvégre). Konfigurációs érték kisebb, a gépek és a szolgáltatások kapcsolatos további információk. Egy számítási feladatot egyetlen példánya dedikált hardver nagymértékben egy a múlté. Szolgáltatások kis méretű elosztott rendszerek, amelyek a hagyományos hardvereken több kisebb darabokra váltak.

Mivel az alkalmazás már nem helyezkednek el több szinten kódtömbök sorozata, most már számos további kombinációk kezelésére. Akik úgy dönt, hogy milyen típusú munkaterheléseket is hardveren futtatott mely, illetve hány? Mely számítási feladatok ugyanazon a hardveren való jól működik, és amelyek ütköznek? Ha egy gép lefelé Honnan tudja, mi volt hiba fut ezen a gépen futó? Aki felelős gondoskodik róla, hogy az adott számítási feladat van újra elindult? Várjon a (virtuális?) gép ismét elérhető nem lesz, vagy hajtsa végre a számítási feladatok automatikusan átadja a feladatokat más gépek és tarthatja fut? Az emberi beavatkozás szükséges? Mi a helyzet a frissítéseket ebben a környezetben?

A fejlesztők és üzemeltetők foglalkozó ebben a környezetben fogjuk ezt a komplexitást kezelése segítségre van szüksége. A felvételi binge és bonyolultságának személyekkel próbál valószínűleg nem a helyes válasz, így Mi a teendő?

## <a name="introducing-orchestrators"></a>Vezénylők bemutatása
"Orchestrator" az általános kifejezés egy adott szoftver, amellyel a rendszergazdák az ilyen típusú környezetek kezeléséhez. Vezénylők az összetevőket, amelyek a kérelmek igénybe vehet, például a "Saját környezetben fut ez a szolgáltatás öt példányban szeretnék kapni." Hogy a környezet felel meg a kívánt állapotra, függetlenül attól, hogy mi történik, próbálja meg.

Vezénylők (nem az emberek) olyan milyen művelet végrehajtása, ha egy gép meghibásodik, vagy a munkaterhelés váratlan valamiért leáll. A legtöbb vezénylők több, mint foglalkoznak. Egyéb funkciókkal rendelkeznek kezelt új központi telepítéseknél, frissítések kezelése, és erőforrás-használat és a cégirányítási foglalkoznak. Összes vezénylővel támogatást készül a alapvetően néhány konfiguráció a környezet állapotának karbantartása. Szeretné tudni egy orchestrator mondja el, mit, szeretnének, és rendelkezik, tegye a feladatát. Mesos, a Docker Datacenter vagy Docker Swarm, Kubernetes és a Service Fabric felett Sarki fény a vezénylők összes példái. Ezek vezénylők folyamatosan fejlesztenek az éles környezetben valós munkaterhelések igényeinek. 

## <a name="orchestration-as-a-service"></a>Vezénylési szolgáltatásként
A fürterőforrás-kezelő a rendszer összetevő, amely kezeli a Service Fabric vezénylési. A fürt Resource Manager-feladat oszlik három részből áll:

1. Érvényesítési szabályok
2. A környezet optimalizálása
3. Útmutatás nyújtása a más folyamatokkal

### <a name="what-it-isnt"></a>Mit nem
A hagyományos N szintű alkalmazás esetében mindig egy [terheléselosztó](https://en.wikipedia.org/wiki/Load_balancing_(computing)). Általában ez volt a hálózati terheléselosztó (NLB) vagy egy alkalmazás Load Balancer (ALB) attól függően, hol rövidítéssel lévő a hálózati vermet. Néhány terheléselosztók például F5 BigIP ajánlat hardveralapú, mások például a Microsoft szoftveralapú a hálózati Terheléselosztás. Más környezetekben előfordulhat, hogy valami hasonló HAProxy, nginx-et, Istio vagy az Envoy ebben a szerepkörben. Az architektúrák a terheléselosztás feladata, állapot nélküli munkaterhelés kap (körülbelül) munkahelyi ugyanannyi. Terheléselosztás stratégiák változatos betölteni. Néhány terheléselosztók küld el minden más hívási egy másik kiszolgálóra. Mások a megadott munkamenet rögzítés tartós használatát. Speciális terheléselosztók tényleges betöltést-becslésére vagy a jelentéskészítés segítségével egy hívás a várt költségeket és a gép aktuális terhelés alapján továbbítja.

Hálózati terheléselosztók vagy üzenet útválasztók próbált meg, győződjön meg arról, hogy a webes/feldolgozói réteg nagyjából elosztott terhelésű maradt. Az adatréteg terheléselosztási stratégiák eltérő, és az adatok tárolási mechanizmus a függő voltak. Az adatok horizontális skálázása a gyorsítótárazás, felügyelt nézetek, a tárolt eljárások és az egyéb tároló-specifikus mechanizmusok támaszkodva az adatréteg terheléselosztást.

Bár ezek a stratégiák néhány érdekes, a Service Fabric fürterőforrás-kezelő nincs semmi például a hálózati terheléselosztás vagy a gyorsítótár. Hálózati terheléselosztó előtérrendszer osztja szét a forgalmat több előtérrendszer elosztja. A Service Fabric fürterőforrás-kezelő más stratégiával rendelkezik. Alapvetően, áthelyezi a Service Fabric *szolgáltatások* jelentéssel bírnak a legtöbb, a várt forgalom ahol, vagy hajtsa végre a betöltés. Például, előfordulhat, hogy helyezze át szolgáltatások, amelyek jelenleg offline, mert a szolgáltatásokat, hogy vannak-e nem végeznek sok munkát csomópontok. A csomópontok hideg lehet, mivel a szolgáltatások, amelyek a jelen volt törölték vagy áthelyezték máshol. Másik példaként a fürterőforrás-kezelő forrásadatok egy gép egy szolgáltatás is helyezheti át. Például a gép van frissítve lesznek, vagy egy megnövekedett felhasználás miatt a rajta futó szolgáltatások által túlterhelt. Másik lehetőségként a szolgáltatás erőforrás-igényű mértékben nőtt. Ennek eredményeképpen nincs elegendő erőforrás folytatja annak futtatását a számítógépen. 

Szolgáltatások köré áthelyezése a fürterőforrás-kezelő feladata, mert egy másik szolgáltatáskészlet találtak volna a hálózati terheléselosztó képest tartalmazza. Ennek oka az, hálózati terheléselosztókkal továbbítására hálózati forgalom hol szolgáltatások már vannak, még akkor is, ha erre a helyre nem fut a szolgáltatás ideális. A Service Fabric fürterőforrás-kezelő annak biztosítására, hogy a fürt erőforrásainak hatékony felhasználtuk alapvetően különböző stratégiákat használ.

## <a name="next-steps"></a>További lépések
- Az architektúra és információk folyamat belül a fürterőforrás-kezelő kapcsolatos információkért tekintse meg [Ez a cikk](service-fabric-cluster-resource-manager-architecture.md)
- A fürterőforrás-kezelő leíró a fürt számos lehetőség áll. További információk a metrikákról megkereséséhez tekintse meg a cikk a [leíró, Service Fabric-fürt](service-fabric-cluster-resource-manager-cluster-description.md)
- További információt a szolgáltatások konfigurálása [megismerheti a szolgáltatások konfigurálása](service-fabric-cluster-resource-manager-configure-services.md)
- Metrikák, hogyan kezeli a Service Fabric-fürt erőforrás-kezelő a használat és a kapacitás a fürtben. További információ a metrikák és a konfigurálásukról kivétel [Ez a cikk](service-fabric-cluster-resource-manager-metrics.md)
- A fürterőforrás-kezelő működik együtt a Service Fabric felügyeleti képességeket. További információ arról, hogy az integrációs, olvassa el [Ez a cikk](service-fabric-cluster-resource-manager-management-integration.md)
- Ismerje meg hogyan a fürterőforrás-kezelő felügyeli, és elosztja a terhelést a fürtben, tekintse meg a cikk a [terheléselosztás](service-fabric-cluster-resource-manager-balancing.md)
