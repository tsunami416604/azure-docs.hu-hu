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
ms.openlocfilehash: 747fb9a38cc0c27d162192f4f3ed928e8a968f27
ms.sourcegitcommit: abeefca6cd5ca01c3e0b281832212aceff08bf3e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "64993115"
---
Az Azure platform a megbízhatóságának, teljesítményének és a gazda-infrastruktúrát, a virtuális gépek biztonságának javítása érdekében rendszeres időközönként frissíti. Ezen frissítések köre, a javítás szoftverösszetevőket hardver leszerelésének hálózati összetevők frissítése, a üzemeltetési környezetben. Ezek a frissítések a legtöbb nincs hatással a futtatott virtuális gépek tartalmaz. Előfordulhatnak azonban olyan esetekben, amikor a frissítések hatással vannak, és az Azure úgy dönt, a frissítések legalább hatásos módszer:

- Ha egy nem rebootful frissítés lehetséges, a virtuális gép fel van függesztve, amíg frissül a gazdagépet, vagy az élő áttelepítése egy már frissített gazdagépre.

- Ha karbantartási újraindítás szükséges, egy értesítés, amikor a karbantartás tervezett kap. Az Azure ad egy olyan időkeretet, ahol elkezdheti a karbantartási feladattal saját maga is az Önnek legmegfelelőbb egyszerre is. Önkiszolgáló karbantartás alatt az időszak van általában négy hét, kivéve ha sürgős karbantartás céljából. Az Azure is fektet csökkentése érdekében az esetek, amikor a virtuális gépeket újra kell indítani a platform tervezett karbantartási technológiák terén. 

Ez az oldal ismerteti, hogyan végez az Azure-karbantartási mindkét típusú. (Kimaradások), nem tervezett eseményekkel kapcsolatos további információt talál a virtuális gépek rendelkezésre állásának kezelése [Windows](../articles/virtual-machines/windows/manage-availability.md) vagy [Linux](../articles/virtual-machines/linux/manage-availability.md).

Az ütemezett események a közeledő karbantartással kapcsolatban a virtuális gép értesítés kérheti [Windows](../articles/virtual-machines/windows/scheduled-events.md) vagy [Linux](../articles/virtual-machines/linux/scheduled-events.md).

"Útmutató" információ a tervezett karbantartás kezelése: "Tervezett karbantartásával kapcsolatos értesítések kezelése" a [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) vagy [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-not-requiring-a-reboot"></a>Karbantartási nem igényli a számítógép újraindítása

Legtöbb nullától eltérő hatás karbantartás, és nem igényel újraindítást célja a virtuális gép felfüggesztése kevesebb mint 10 másodperc. Az Azure úgy dönt, hogy a frissítési mechanizmust, amely legalább hatásos, az ügyfél virtuális gépeket. Bizonyos esetekben memória megőrző karbantartás mechanizmusokat használ, amely legfeljebb 30 másodpercig a virtuális gép felfüggesztése, és megőrzi a memóriát RAM-ban. A virtuális gép majd helyreáll, és a rendszeróra automatikusan szinkronizálódik. Az Azure egyre inkább az élő áttelepítés technológiák használatával, és millióiról karbantartási mechanizmus, amellyel csökkentheti a szünet időtartama memória javítása.  

Ezek nem rebootful karbantartási műveleteket tartalék tartomány által alkalmazott tartalék tartományt, és folyamatban le van állítva, ha bármilyen figyelmeztetés állapot jelek. 

Egyes alkalmazások hatással lehet az ilyen típusú frissítések. Abban az esetben a virtuális gép élő áttelepítése egy másik gazdagépre, néhány teljesítményérzékeny számítási feladatok esetleg teljesítménycsökkenést enyhe a néhány perc alatt a virtuális gép felfüggesztése vezető. Az ilyen alkalmazások milyen előnyei származhatnak ütemezett események [Windows](../articles/virtual-machines/windows/scheduled-events.md) vagy [Linux](../articles/virtual-machines/linux/scheduled-events.md) virtuális gép karbantartásának készítheti elő, és semmilyen hatást nem Azure-karbantartás során. Azure a karbantartási funkcióinak vezérléséhez például ultramagas érzékeny alkalmazások esetén is működik. 

## <a name="live-migration"></a>Az élő áttelepítés

Az élő áttelepítés nem rebootful művelet, amely a virtuális gép megőrzi a memória, és korlátozott eredményeket felfüggesztése vagy rögzítése, általában tartó legfeljebb 5 másodperc. Az összes infrastruktúra-szolgáltatás (IaaS) virtuális gépek G, M, N és H sorozat szereplőkkel jelenleg jogosultak az élő áttelepítés. Ez állapotnak felel meg a Azure készletünket üzembe helyezett IaaS virtuális több mint 90 %-át. 

A következő esetekben az Azure Fabric által kezdeményezett élő áttelepítés:
- Tervezett karbantartás
- Hardverhiba
- Foglalási optimalizálási lehetőségek

Az élő áttelepítés bizonyos tervezett karbantartás esetekben szolgáltatást veszi igénybe, és ütemezett események segítségével tudja Előrefizetéssel történik, amikor élő áttelepítési műveletek indítása.

Az élő áttelepítés is szolgál a virtuális gépek áthelyezése a hardvert, amely egy közelgő előre jelzett hiba észlelésekor a gépi tanulási algoritmusok engedményt, és optimalizálhatja a virtuális gép értékesítjük. A prediktív modellezés, hogy az észlelt példányait csökkentett teljesítményű hardverre kapcsolatos további információkért lásd a foglalkozó blogbejegyzést jogosult [javítása Azure virtuális gép rugalmasságot a prediktív gépi Tanulási és az élő áttelepítés](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure). Ügyfelek mindig az élő áttelepítés értesítést kap az az Azure Portalon, a figyelő a / Service Health-naplók, valamint az ütemezett események a folyamatban használt keresztül.

## <a name="maintenance-requiring-a-reboot"></a>A rendszer újraindítását igénylik karbantartás

Ritka esetben, ha a virtuális gépeket újra kell indítani a tervezett karbantartás, értesítést kap előre. Tervezett karbantartás két fázisból áll: az önkiszolgáló ablakot, és a egy ütemezett karbantartási időszaknál.

A **önkiszolgáló ablak** lehetővé teszi a virtuális gépeken a karbantartás indítását. Ebben az időszakban, amely általában négy héttel lekérdezheti, ha azok állapotát és az utolsó karbantartási kérelem eredményének ellenőrzése minden virtuális Géphez.

Ha önkiszolgáló karbantartást indítja el, a virtuális gép újratelepítése van egy már frissített csomópontoknak. A virtuális gép újraindul, mert az ideiglenes lemez elvesztését, és dinamikus, virtuális hálózati adapterhez társított IP-címek frissülnek.

Önkiszolgáló karbantartási indítja el, és a folyamat során hiba történik, ha a művelet le van állítva, a virtuális gép nem frissül, és ismételje meg az önkiszolgáló karbantartás lehetőséget kap. 

Az önkiszolgáló ablak megfelelt, ha a **ütemezett karbantartási időszaknál** kezdődik. Ezen időszakon továbbra is lekérdezhetik a karbantartási időszak, de nem indítható el a karbantartást saját magának.

Információk a rendszer újraindítását igénylik karbantartási kezelése: "Tervezett karbantartásával kapcsolatos értesítések kezelése" a [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) vagy [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Az ütemezett karbantartás során a rendelkezésre állási szempontok 

Ha úgy dönt, megvárni az ütemezett karbantartási időszaknál, néhány dolgot figyelembe venni a legmagasabb rendelkezésre állását a virtuális gépeket. 

#### <a name="paired-regions"></a>Párosított régiók

Minden egyes Azure-régió párban áll egy másik régióban a megegyező területen található, és együtt, győződjön meg arról, egy regionális párokból érdemes. Az ütemezett karbantartás az Azure csak pár régió egyetlen régión belüli virtuális gépek frissíti. Például amikor frissíti a virtuális gép az USA északi középső Régiója, az Azure nem frissítése USA déli középső Régiójában lévő összes virtuális gép egyszerre. Azonban más régiók (például Észak-Európa) karbantarthatók ugyanabban az időben, mint az USA keleti régiója. Ismertetése, hogyan működnek a régiópárok segítségével jobban elosztása a virtuális gépek régiók között elosztva. További információkért lásd: [Azure régiópárok](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>A rendelkezésre állási csoportok és a méretezési csoportok

Az Azure virtuális gépeken futó számítási feladatok üzembe helyezésekor, egy rendelkezésre állási csoportot az alkalmazás magas rendelkezésre állás biztosításához a virtuális gépeket hozhat létre. Ez biztosítja, hogy leállás vagy rebootful karbantartási események, legalább egy virtuális gép elérhető.

Egy rendelkezésre állási csoporton belül az egyes virtuális gépek akár 20 frissítési tartományig (frissítési) vannak elosztva. Az ütemezett karbantartás során a csak egyetlen frissítési tartomány egy adott időpontban frissül. Frissítési tartományok frissítése nem feltétlenül fordulhat elő, egymás után. 

Virtuálisgép-méretezési csoportok olyan számítási Azure-erőforrások, amellyel üzembe helyezése és azonos virtuális gépek egyetlen erőforrásként kezelheti. A méretezési automatikusan telepített frissítési tartományokban, például a virtuális gépet egy rendelkezésre állási csoportban. Csakúgy, mint a rendelkezésre állási csoportokkal, a méretezési csoportok csak egyetlen frissítési tartomány frissül egy adott időpontban az ütemezett karbantartás során.

A magas rendelkezésre állású virtuális gépek konfigurálásával kapcsolatos további információkért tekintse meg a virtuális gépek rendelkezésre állásának kezelése a [Windows](../articles/virtual-machines/windows/manage-availability.md) vagy [Linux](../articles/virtual-machines/linux/manage-availability.md).
