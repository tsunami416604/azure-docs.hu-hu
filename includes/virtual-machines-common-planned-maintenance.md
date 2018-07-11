---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/05/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: abf10177f8ce86309043da92d1f2b690775b6d89
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37909912"
---
Az Azure rendszeresen végez frissítéseket a virtuális gépeket futtató infrastruktúra megbízhatóságának, teljesítményének és biztonságának javítása érdekében. Ezek a frissítések a futtatókörnyezet szoftverösszetevőinek (például az operációs rendszer, a hipervizor és a gazdagépen telepített különböző ügynökök) javítását, a hálózati összetevők frissítését és a hardverek leszerelését takarják. Ezek a frissítések a legtöbb annak hatását a futtatott virtuális gépek működésére. Előfordulhatnak azonban olyan esetekben, amikor frissítések hatással:

- Ha lehetőség egy újraindítás nélküli frissítését, az Azure karbantartás megőrzése memóriát használ a virtuális gép felfüggesztése, amíg a gazdagép frissítése vagy a virtuális gép áthelyezése egy már frissített gazdagépre hozhassanak.

- Ha karbantartási újraindítás szükséges, egy értesítés, amikor a karbantartás tervezett kap. Ezekben az esetekben meg fogjuk is kell adni egy olyan időkeretet, ahol elkezdheti a karbantartási feladattal saját maga is az Önnek legmegfelelőbb egyszerre.

Ez az oldal ismerteti, hogyan végez a Microsoft Azure-karbantartási mindkét típusú. Nem tervezett események (leállások) kapcsolatos további információkért lásd: a virtuális gépek rendelkezésre állásának kezelése a [Windows] (.. /articles/virtual-machines/windows/manage-availability.md) vagy [Linux](../articles/virtual-machines/linux/manage-availability.md).

Egy virtuális gépen futó alkalmazások közelgő frissítésével kapcsolatos információkat gyűjthet a az Azure Metadata Service használatával [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) vagy [Linux] (.. /articles/virtual-machines/linux/instance-metadata-service.md).

"Útmutató" információ a tervezett karbantartás kezelése: "Tervezett karbantartásával kapcsolatos értesítések kezelése" a [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) vagy [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="memory-preserving-maintenance"></a>Karbantartás megőrzése memória

Frissítések teljes újraindítása nem szükséges, ha memória megőrző karbantartás mechanizmus segítségével korlátozhatják a virtuális géphez. A virtuális gép fel van függesztve, akár 30 másodperc, megőrizve a memóriát RAM-ban, amíg az üzemeltetési környezet a szükséges frissítéseket és javításokat vonatkozik, vagy egy már frissített gazdagépre helyezi át a virtuális gép számára. A virtuális gép majd helyreáll, és a virtuális gép órája a rendszer automatikusan szinkronizálja. 

Ezek nem rebootful karbantartási műveleteket tartalék tartomány által alkalmazott tartalék tartományt, és folyamatban le van állítva, ha bármilyen figyelmeztetés állapot jelek.

Egyes alkalmazások hatással lehet az ilyen típusú frissítések. Az alkalmazásokat, amelyek hajtsa végre a valós idejű esemény feldolgozására, például a médiatartalmak streamelése vagy átkódolása, vagy nagy átviteli sebességű hálózati forgatókönyvek, nem úgy tervezték, hogy a 30 másodperces szünet iktatható működését. <!-- sooooo, what should they do? --> Abban az esetben, ha a virtuális gép áthelyezik egy másik gazdagépre, néhány teljesítményérzékeny számítási feladatok előfordulhat, hogy teljesítménycsökkenést enyhe az a virtuális gép felfüggesztése vezető néhány perc múlva. 


## <a name="maintenance-requiring-a-reboot"></a>A rendszer újraindítását igénylik karbantartás

Ha a virtuális gépeket újra kell indítani a tervezett karbantartás, értesítést kap az előzetesen. Tervezett karbantartás két fázisból áll: az önkiszolgáló ablakot, és a egy ütemezett karbantartási időszaknál.

A **önkiszolgáló ablak** kezdeményezheti a karbantartást, a virtuális gépeken teszi lehetővé. Ez idő alatt lekérdezheti, ha azok állapotát és az utolsó karbantartási kérelem eredményének ellenőrzése minden virtuális Géphez.

Amikor az önkiszolgáló karbantartás, a virtuális gép átkerül már frissítették, és majd használja újra a csomópontot. A virtuális gép újraindul, mert az ideiglenes lemez elvesztését, és dinamikus, virtuális hálózati adapterhez társított IP-címek frissülnek.

Önkiszolgáló karbantartási indítja el, és a folyamat során hiba történik, ha a művelet le van állítva, a virtuális gép nem frissül, és ismételje meg az önkiszolgáló karbantartás lehetőséget kap. 

Az önkiszolgáló ablak megfelelt, ha a **ütemezett karbantartási időszaknál** kezdődik. Ezen időszakon továbbra is lekérdezheti a karbantartási időszak, de már nem kell saját kezűleg elindíthatja a karbantartási.

Információk a rendszer újraindítását igénylik karbantartási kezelése: "Tervezett karbantartásával kapcsolatos értesítések kezelése" a [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) vagy [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Az ütemezett karbantartás során a rendelkezésre állási szempontok 

Ha úgy dönt, megvárni az ütemezett karbantartási időszaknál, néhány dolgot figyelembe venni a legmagasabb rendelkezésre állását a virtuális gépeket. 

#### <a name="paired-regions"></a>Párosított régiók

Minden egyes Azure-régió párban áll egy másik régióban a megegyező területen található, együtt, győződjön meg arról, egy regionális párokból érdemes. Ütemezett karbantartás során Azure-régió párban egyetlen régión belüli virtuális gépek csak frissíti. Például ha az USA északi középső régiójában található virtuális gépeket frissíti, az Azure nem fogja frissíteni az USA déli középső régiójában található virtuális gépeket ugyanabban az időben. Azonban más régiók (például Észak-Európa) karbantarthatók ugyanabban az időben, mint az USA keleti régiója. Ismertetése, hogyan működnek a régiópárok segítségével jobban elosztása a virtuális gépek régiók között elosztva. További információkért lásd: [Azure régiópárok](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>A rendelkezésre állási csoportok és a méretezési csoportok

Az Azure virtuális gépeken futó számítási feladatok üzembe helyezésekor, egy rendelkezésre állási csoportot az alkalmazás magas rendelkezésre állás biztosításához a virtuális gépeket hozhat létre. Ez biztosítja, hogy leállás vagy rebootful karbantartási események, legalább egy virtuális gép elérhető.

Egy rendelkezésre állási csoporton belül az egyes virtuális gépek akár 20 frissítési tartományig (frissítési) vannak elosztva. Az ütemezett karbantartás során csak egyetlen frissítési tartomány kihatással van egy adott időpontban. Vegye figyelembe, hogy befolyásolja a frissítési tartományok sem feltétlenül történik egymás után. 

Virtuálisgép-méretezési csoportok olyan számítási Azure-erőforrások, amellyel üzembe helyezése és azonos virtuális gépek egyetlen erőforrásként kezelheti. A méretezési automatikusan telepített frissítési tartományokban, például a virtuális gépet egy rendelkezésre állási csoportban. Csakúgy, mint a rendelkezésre állási csoportokkal, a méretezési csoportok csak egyetlen frissítési tartomány kihatással van a tervezett karbantartás során az adott időpontban.

A magas rendelkezésre állású virtuális gépek konfigurálásával kapcsolatos további információkért tekintse meg a virtuális gépek rendelkezésre állásának kezelése a [Windows](../articles/virtual-machines/windows/manage-availability.md) vagy [Linux](../articles/virtual-machines/linux/manage-availability.md).
