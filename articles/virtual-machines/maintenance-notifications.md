---
title: Karbantartási értesítések
description: Az Azure-ban futó virtuális gépek karbantartási értesítéseinek áttekintése.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 68159577cb31145be5063bb19af6db71ca1727bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115680"
---
# <a name="handling-planned-maintenance-notifications"></a>Tervezett karbantartási értesítések kezelése

Az Azure rendszeresen végez frissítéseket a virtuális gépeket futtató infrastruktúra megbízhatóságának, teljesítményének és biztonságának javítása érdekében. A frissítések olyan módosítások, mint az üzemeltetési környezet javítása vagy a hardver frissítése és leszerelése. A frissítések többsége a szolgáltatott virtuális gépekre gyakorolt hatás nélkül befejeződik. Vannak azonban olyan esetek, amikor a frissítések hatással vannak:

- Ha a karbantartás nem igényel újraindítást, az Azure a virtuális gép szüneteltetéséhez a virtuális gép szüneteltetéséhez használja a helyben történő áttelepítést. Ezek a karbantartási műveletek tartalék tartományonként alkalmazva vannak. A folyamat leáll, ha bármilyen figyelmeztető állapotjelzés érkezik.

- Ha a karbantartás újraindítást igényel, értesítést kap akarbantartás tervezett időpontjáról. Körülbelül 35 napos időablakot kap, ahol saját maga kezdheti el a karbantartást, amikor az Ön számára működik.


Az újraindítást igénylő tervezett karbantartás hullámokban van ütemezve. Minden hullám különböző hatókörrel (régiók).

- A hullám az ügyfelek értesítésével kezdődik. Alapértelmezés szerint a rendszer értesítést küld a szolgáltatásrendszergazdának és a társrendszergazdáknak. A [tevékenységnapló-értesítések](../service-health/alerts-activity-log-service-notifications.md)segítségével további címzetteket és üzenetküldési lehetőségeket, például e-maileket, SMS-eket és webhookokat adhat hozzá.  
- Az értesítés kihirdetése után egy *önkiszolgáló ablak* is elérhetővé válik. Ebben az ablakban lekérdezheti, hogy mely virtuális gépek érintettek, és a saját ütemezési igények alapján elkezdheti a karbantartást. Az önkiszolgáló ablak általában körülbelül 35 nap.
- Az önkiszolgáló ablak után megkezdődik az *ütemezett karbantartási időszak.* Egy bizonyos ponton ebben az ablakban az Azure ütemezi, és alkalmazza a szükséges karbantartást a virtuális gépre. 

A cél a két ablak, hogy elegendő időt a karbantartás elindításához és újraindításához a virtuális gép, miközben tudja, mikor az Azure automatikusan elindítja a karbantartást.


Az Azure Portalon, a PowerShell, a REST API és a CLI segítségével lekérdezheti a virtuális gépek karbantartási időszakait, és elindíthatja az önkiszolgáló karbantartást.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>Az önkiszolgáló időszakban kezdje el használni a karbantartást?  

Az alábbi irányelvek segítségével eldöntheti, hogy használja-e ezt a funkciót, és saját idejében kezdje el a karbantartást. 

> [!NOTE] 
> Előfordulhat, hogy az önkiszolgáló karbantartás nem érhető el az összes virtuális géphez. Annak megállapításához, hogy a proaktív újratelepítés elérhető-e a virtuális géphez, keresse meg a **Start now** a karbantartási állapot. Önkiszolgáló karbantartás jelenleg nem érhető el a felhőszolgáltatások (web/feldolgozó szerepkör) és a Service Fabric.


Az önkiszolgáló karbantartás nem ajánlott a **rendelkezésre állási készleteket**használó központi telepítésekhez. Az elérhetőségi csoportok már egyszerre csak egy frissítési tartományt frissítenek. 

- Hagyja, hogy az Azure indítsa el a karbantartást. Az újraindítást igénylő karbantartás esetén a karbantartás frissítési tartományonként történik. A frissítési tartományok nem feltétlenül kapják meg egymás után a karbantartást, és hogy 30 perces szünet van a frissítési tartományok között. 
- Ha egy ideiglenes elvesztése bizonyos kapacitás (1 frissítési tartomány) aggodalomra ad okot, a tartalékolási időszak alatt példányokat adhat hozzá. 
- Az újraindítást nem igénylő karbantartások esetén a frissítések a tartalék tartomány szintjén lesznek alkalmazva. 

**Ne** használjon önkiszolgáló karbantartást a következő esetekben: 
- Ha gyakran leállítja a virtuális gépeket, manuálisan, a DevTest Labs használatával, automatikus leállítás sal vagy ütemezés szerint, visszaállíthatja a karbantartási állapotot, és ezért további állásidőt okozhat.
- A rövid életű virtuális gépek, amelyekről tudja, törlődnek a karbantartási hullám vége előtt. 
- A helyi (rövid élettartamú) lemezen tárolt nagy állapotú munkaterhelések esetén, amelyeket a frissítés kor fenn kell tartani. 
- Azokban az esetekben, ahol gyakran átméretezi a virtuális gép, mivel visszaállíthatja a karbantartási állapot. 
- Ha olyan ütemezett eseményeket fogadott el, amelyek proaktív feladatátvételt vagy a munkaterhelés kecses leállítását teszik lehetővé, 15 perccel a karbantartás leállítása előtt

**Használja** az önkiszolgáló karbantartást, ha a virtuális gép megszakítás nélküli futtatását tervezi az ütemezett karbantartási fázisban, és a fent említett ellenjelzések egyike sem alkalmazható. 

A legjobb, ha önkiszolgáló karbantartást használ a következő esetekben:
- Pontos karbantartási időszakot kell közölnie a vezetőséggel vagy a végfelhasználóval. 
- A karbantartást egy adott dátumig kell elvégeznie. 
- A biztonságos helyreállítás biztosítása érdekében szabályoznia kell a karbantartás sorrendjét, például a többrétegű alkalmazást.
- Több mint 30 perc virtuális gép helyreállítási idő két frissítési tartományok (UDs) között van szükség. A frissítési tartományok közötti idő szabályozásához a virtuális gépek karbantartását egyszerre egy frissítési tartományban (UD) kell aktiválnia.


## <a name="faq"></a>GYIK


**K: Miért kell most újraindítani a virtuális gépeimet?**

**A.** Bár az Azure platformra vonatkozó frissítések és frissítések többsége nem befolyásolja a virtuális gépek rendelkezésre állását, vannak olyan esetek, amikor nem tudjuk elkerülni az Azure-ban üzemeltetett virtuális gépek újraindítását. Számos olyan változtatást halmoztunk fel, amelyek megkövetelik, hogy újraindítsuk a szervereinket, ami a virtuális gépek újraindítását eredményezi.

**K: Ha egy rendelkezésre állási csoport használatával követem a magas rendelkezésre állásra vonatkozó javaslatait, biztonságban vagyok?**

**A.** A rendelkezésre állási csoportban vagy a virtuálisgép-méretezési csoportokban üzembe helyezett virtuális gépek a tartományok frissítése (UD) fogalmával rendelkeznek. Karbantartás végrehajtásakor az Azure tiszteletben tartja az UD-megkötést, és nem indítja újra a különböző UD-ből (ugyanazon rendelkezésre állási csoporton belül) lévő virtuális gépeket.  Az Azure is vár legalább 30 percet, mielőtt a virtuális gépek következő csoportjába. 

A magas rendelkezésre állásról további információt az [Elérhetőség az Azure-ban elérhető virtuális gépekszámára című témakörben talál.](./linux/availability.md)

**K: Hogyan kaphatok értesítést a tervezett karbantartásról?**

**A.** A tervezett karbantartási hullám egy vagy több Azure-régió ütemezését állítja be. Nem sokkal később e-mailértesítést küld a rendszer az előfizetés rendszergazdáinak (előfizetésenként egy e-mail). Az értesítéshez további csatornák és címzettek konfigurálhatók a tevékenységnapló-riasztások használatával. Abban az esetben, ha egy virtuális gépet telepít egy olyan régióba, ahol a tervezett karbantartás már ütemezve van, nem kapja meg az értesítést, hanem ellenőriznie kell a virtuális gép karbantartási állapotát.

**K: Nem látom a tervezett karbantartás ra utaló jelét a portálon, a Powershellben vagy a CLI-ben. mi a baj?**

**A.** A tervezett karbantartással kapcsolatos információk csak a virtuális gépek, amelyek hatással lesznek a tervezett karbantartási hullám alatt érhető el. Más szóval, ha nem lát adatokat, lehet, hogy a karbantartási hullám már befejeződött (vagy nem indult el), vagy hogy a virtuális gép már üzemeltetve van egy frissített kiszolgálón.

**K: Van-e mód annak, hogy pontosan tudja, mikor lesz hatással a virtuális gépem?**

**A.** Az ütemezés beállításakor több napos időablakot határozunk meg. Az ablakon belüli kiszolgálók (és virtuális gépek) pontos szekvenálása azonban ismeretlen. Azok az ügyfelek, akik szeretnék tudni, hogy a virtuális gépek pontos idejét használhatja az [ütemezett események](./linux/scheduled-events.md) és lekérdezés a virtuális gépen belül, és kap egy 15 perces értesítést, mielőtt a virtuális gép újraindítása.

**K: Mennyi ideig tart újraindítani a virtuális gépemet?**

**A.**  A virtuális gép méretétől függően újraindítás akár néhány percet is igénybe vehet az önkiszolgáló karbantartási időszak alatt. Az Azure által kezdeményezett újraindítások az ütemezett karbantartási időszakban, az újraindítás általában körülbelül 25 percet vesz igénybe. Vegye figyelembe, hogy a felhőszolgáltatások (web/feldolgozó szerepkör), a virtuálisgép-méretezési készletek vagy a rendelkezésre állási csoportok használata esetén az ütemezett karbantartási időszakban 30 percet kap a virtuális gépek (UD) minden csoportja között.

**K: Mi a tapasztalat a virtuális gép méretezési készletek?**

**A.** A tervezett karbantartás már elérhető a virtuális gép méretezési készleteihez. Az önkiszolgáló karbantartás kezdeményezésével kapcsolatos utasításokért olvassa el [a virtuálisgép-méretezési készletek tervezett karbantartását](../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md) tartalmazó dokumentumot.

**K: Mi a tapasztalat a felhőszolgáltatások (web/feldolgozó szerepkör) és a szolgáltatásháló esetében?**

**A.** Bár ezeket a platformokat a tervezett karbantartás befolyásolja, az ezeket a platformokat használó ügyfelek biztonságosnak minősülnek, mivel egy adott időpontban csak az egyetlen frissítési tartományban (UD) lévő virtuális gépeket érinti. Önkiszolgáló karbantartás jelenleg nem érhető el a felhőszolgáltatások (web/feldolgozó szerepkör) és a Service Fabric.

**K: Nem jelennek meg karbantartási információk a virtuális gépeken. Mi romlott el?**

**A.** Számos oka lehet annak, hogy miért nem lát semmilyen karbantartási információt a virtuális gépeken:
1.  Microsoft belsőként megjelölt előfizetést használ.
2.  A virtuális gépek nincsenek ütemezve karbantartásra. Lehet, hogy a karbantartási hullám véget ért, törölt vagy módosított, hogy a virtuális gépek már nem érinti.
3.  Nem adja hozzá a **Karbantartás** oszlopot a virtuális gép listájának nézetéhez. Bár ezt az oszlopot hozzáadtuk az alapértelmezett nézethez, a nem alapértelmezett oszlopok megtekintésére konfigurált ügyfeleknek manuálisan hozzá kell adniuk a **Karbantartás** oszlopot a virtuális gép listanézetéhez.

**K: A virtuális gép a tervek szerint a karbantartás a második alkalommal. miért?**

**A.** Számos olyan használati eset van, amikor a virtuális gép karbantartásra ütemezve jelenik meg, miután már befejezte a karbantartás-újratelepítést:
1.  Lefújtuk a karbantartási hullámot, és újraindítottuk egy másik hasznos teherrel. Lehet, hogy hibás hasznos terhet észleltünk, és egyszerűen csak egy további hasznos adatot kell üzembe helyeznünk.
2.  A virtuális gép hardverhiba miatt egy másik csomópontra *lett javítva.*
3.  Kiválasztotta a virtuális gép leállítását (felszabadítását), és újraindítását.
4.  A virtuális **gép automatikus leállítása** be van kapcsolva.



## <a name="next-steps"></a>További lépések

A tervezett karbantartást az [Azure CLI,](maintenance-notifications-cli.md)az [Azure PowerShell](maintenance-notifications-powershell.md) vagy a [portál](maintenance-notifications-portal.md)használatával is kezelheti.

