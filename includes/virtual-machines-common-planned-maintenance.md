---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155924"
---
Azure rendszeres időközönként frissíti a megbízhatóságának, teljesítményének és a gazda-infrastruktúrát, a virtuális gépek biztonságának javítása érdekében platformja. Ezek a frissítések célját címtartományok a javítás az üzemeltetési környezetben frissítése hálózati összetevők vagy hardver leszerelése szoftverösszetevőket. 

Frissítések ritkán érintik az üzemeltetett virtuális gépeket. Frissítések egy hatása, ha az Azure úgy dönt, a frissítések legalább hatásos módszer:

- A frissítés nem igényel újraindítást, ha a virtuális gép fel van függesztve, amíg frissül a gazdagépet, vagy a virtuális gép élő áttelepített egy már frissített gazdagépre.

- Karbantartási újraindítás szükséges, ha, értesítést kap a tervezett karbantartás. Az Azure is biztosít egy olyan időkeretet, ahol elkezdheti a karbantartási saját maga is az Önnek legmegfelelőbb egyszerre. Az önkiszolgáló – karbantartási időszak van általában 30 nap, kivéve, ha a karbantartási sürgős. Azure fektet technológiákban esetek, amelyben platform tervezett karbantartási szükséges újra kell indítani a virtuális gépek számának csökkentése érdekében. 

Ez az oldal ismerteti, hogyan végez az Azure-karbantartási mindkét típusú. Nem tervezett események (leállások) kapcsolatos további információkért lásd: [a Windows virtuális gépek rendelkezésre állásának kezelése](../articles/virtual-machines/windows/manage-availability.md) vagy a megfelelő számú cikk [Linux](../articles/virtual-machines/linux/manage-availability.md).

Egy virtuális gépen, beszerezheti a közeledő karbantartással kapcsolatban küldött [használata a Windows ütemezett események](../articles/virtual-machines/windows/scheduled-events.md) vagy [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Tervezett karbantartás kezelésével kapcsolatos utasításokért lásd: [tervezett karbantartásával kapcsolatos értesítések Linux kezelése](../articles/virtual-machines/linux/maintenance-notifications.md) vagy a megfelelő számú cikk [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Karbantartás, és nem igényel újraindítást

Ahogyan korábban hangsúlyoztuk, a legtöbb platformon frissítés ügyfél virtuális gépei nem befolyásolják. A következmények nélkül frissítése nem lehetséges, ha az Azure úgy dönt, a frissítési mechanizmust, amely legalább impactful ügyfél virtuális gépekhez. 

A legtöbb nullától eltérő hatás karbantartási kevesebb mint 10 másodpercet a virtuális gép felfüggesztése. Bizonyos esetekben az Azure memóriamegőrző karbantartási mechanizmusokat használ. Ezek a mechanizmusok a virtuális gép felfüggesztése legfeljebb 30 másodpercet, és őrizze meg a memóriát RAM-ban. A virtuális gép majd helyreáll, és a rendszeróra automatikusan szinkronizálódik. 

A memóriamegőrző karbantartási Azure virtuális gépek több mint 90 %-os működik. G, M, N és H-sorozat nem működik. Az Azure egyre élő áttelepítés technológiákat használ, és javítja a karbantartási mechanizmusok memóriamegőrző csökkentése érdekében a szünet időtartama.  

E karbantartási műveletek, amelyek nem igényelnek újraindítást egyszerre alkalmazott egy tartalék tartományban. Leáll, ha bármilyen figyelmeztetés állapot jelek kapnak. 

Az ilyen típusú frissítések hatással lehetnek egyes alkalmazások. Ha a virtuális gép élő áttelepített egy másik gazdagépre, az egyes bizalmas számítási feladatokhoz enyhe teljesítménycsökkenés előfordulhat, hogy a néhány perc alatt a virtuális gép felfüggesztése vezető megjelenítése. Virtuális gép karbantartásának készítheti elő, és hatást Azure karbantartás során, próbálja meg [használata a Windows ütemezett események](../articles/virtual-machines/windows/scheduled-events.md) vagy [Linux](../articles/virtual-machines/linux/scheduled-events.md) az ilyen alkalmazásokhoz. Azure karbantartási-vezérlés funkcióival ezek érzékeny alkalmazások esetén működik. 

### <a name="live-migration"></a>Az élő áttelepítés

Egy művelet, amely nincs szükség újraindításra és a memória, amely megőrzi a virtuális gép élő áttelepítés. Azt eredményezi, a felfüggesztés vagy rögzíteni, általában tartó legfeljebb 5 másodperc. Kivéve G M, N és a H-sorozat, az összes infrastruktúra-szolgáltatás (IaaS) virtuális gépek jogosultak az élő áttelepítés. Jogosult virtuális gépek több mint 90 %-a az Azure készletünket üzembe helyezett IaaS virtuális gépek jelentik. 

Az Azure-platform élő áttelepítés indítása a következő esetekben:
- Tervezett karbantartás
- Hardverhiba
- Foglalási optimalizálási lehetőségek

Néhány tervezett karbantartási helyzetekre élő migrálás használata, és használhatja az ütemezett eseményekről előre tudni, amikor élő indul el áttelepítési műveleteket.

Az élő áttelepítés is használható áthelyezi a virtuális gépeket, amikor az Azure Machine Learning-algoritmusok előrejelzésére egy közelgő hardverhiba vagy ha azt szeretné, optimalizálhatja a virtuális gép értékesítjük. Prediktív modellezés, hogy az észlelt példányait csökkentett teljesítményű hardverre kapcsolatos további információkért lásd: [javítása Azure virtuális gép rugalmasságát prediktív gépi tanulással és az élő áttelepítés](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Ha használja ezeket a szolgáltatásokat az Azure Portalon, a figyelő és a Service Health naplókat, valamint ütemezett események hasonlóan az élő áttelepítés értesítések jelennek meg.

## <a name="maintenance-that-requires-a-reboot"></a>Karbantartási, amely újraindítást igényel

A ritka eset, ahol virtuális gépeket újra kell indítani a tervezett karbantartás értesítést is előre. Tervezett karbantartás két fázisból áll: az önkiszolgáló fázis és a egy ütemezett karbantartás.

Során a *önkiszolgáló fázis*, általában négy héttel tartó, a karbantartás megkezdése a virtuális gépeken. Az önkiszolgáló részeként lekérdezheti, ha minden virtuális gép állapotát és az utolsó karbantartási kérelem eredményét.

Ha önkiszolgáló karbantartást indítja el, a virtuális gép újratelepítése van egy már frissített csomópontoknak. A virtuális gép újraindul, mert az ideiglenes lemez elvesztését, és a dinamikus IP-címek a virtuális hálózati adapterhez társított frissülnek.

Ha hiba merül fel, önkiszolgáló karbantartási, a művelet leállítja a virtuális gép nem frissül, és ismételje meg az önkiszolgáló karbantartás lehetőséget kap. 

Amikor befejeződik az önkiszolgáló fázis, a *ütemezett karbantartás* kezdődik. Ebben a fázisban továbbra is lekérdezheti az karbantartás számára, de saját magának a karbantartás nem lehet elindítani.

Karbantartási újraindítását igénylő kezeléséről további információkért lásd: [tervezett karbantartásával kapcsolatos értesítések Linux kezelése](../articles/virtual-machines/linux/maintenance-notifications.md) vagy a megfelelő számú cikk [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Az ütemezett karbantartás során a rendelkezésre állási szempontok 

Ha úgy dönt, megvárni az ütemezett karbantartás, nincsenek néhány dolgot, érdemes lehet a lehető legmagasabb rendelkezésre állásának a virtuális gépek fenntartásához. 

#### <a name="paired-regions"></a>Párosított régiók

Minden egyes Azure-régió párban áll egy másik az ugyanazon földrajzi környék belül. Együtt akkor győződjön meg arról, régió párban. Az ütemezett karbantartás során az Azure csak a futó virtuális gépeket a régiópárra az egyetlen régióban frissíti. Például frissítésekor a virtuális gép az USA északi középső Régiója, az Azure nem frissíti USA déli középső Régiójában lévő összes virtuális gép egyszerre. Azonban más régiók (például Észak-Európa) karbantarthatók ugyanabban az időben, mint az USA keleti régiója. Ismertetése, hogyan működnek a régiópárok segítségével jobban elosztása a virtuális gépek régiók között elosztva. További információkért lásd: [Azure régiópárok](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>A rendelkezésre állási csoportok és a méretezési csoportok

Az Azure virtuális gépeken futó számítási feladatok üzembe helyezésekor, a virtuális gépeket hozhat létre egy *rendelkezésre állási csoport* az alkalmazás magas rendelkezésre állást biztosít. Rendelkezésre állási csoportokat használ, biztosíthatja, hogy az újraindítást igénylő egy szolgáltatáskimaradás vagy a karbantartási események során legalább egy virtuális gép érhető el.

Egy rendelkezésre állási csoporton belül az egyes virtuális gépek akár 20 frissítési tartományig (frissítési) vannak elosztva. Ütemezett karbantartás során az UD csak egy adott időpontban frissül. Frissítési tartománnyal nem feltétlenül egymás után kell frissíteni. 

Virtuális gép *méretezési csoportok* az Azure-beli számítási erőforrások, amelyek segítségével üzembe helyezése és azonos virtuális gépek egyetlen erőforrásként kezelheti. A méretezési automatikusan különböző frissítési tartománnyal, például a virtuális gépet egy rendelkezésre állási csoportban van telepítve. Mint rendelkezésre állási csoportokkal, használhatja a méretezési csoportokat, ha csak egy UD frissül egy adott időpontban az ütemezett karbantartás során.

A magas rendelkezésre állású virtuális gépek beállításával kapcsolatos további információkért lásd: [számára a Windows virtuális gépek rendelkezésre állásának kezelése](../articles/virtual-machines/windows/manage-availability.md) vagy a megfelelő számú cikk [Linux](../articles/virtual-machines/linux/manage-availability.md).
