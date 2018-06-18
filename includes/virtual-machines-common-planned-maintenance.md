---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: e484dac645ff2e5867d2e652c389a9950e8bac12
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "30196440"
---
Azure rendszeres időközönként megbízhatóságát, teljesítményét és a virtuális gépek állomás infrastruktúra biztonságát javító frissítés. Ezen frissítések közé javítását szoftverösszetevőket. az üzemeltetési környezetben (például az operációs rendszer, hipervizor, és a gazdagépen rendszerbe állított különböző ügynökök), hálózati összetevők frissítése hardver leszerelése. A legtöbb, a frissítések a futtatott virtuális gépek számára gyakorolt hatás nélkül kerül sor. Vannak azonban esetekben, amikor frissítések ütközés:

- Lehetőség egy újraindítás nélküli frissítést, ha az Azure megőrzi az karbantartási memóriát használja a virtuális gép felfüggesztése, amíg frissül, a gazdagép vagy a virtuális gép áthelyezése egy már frissített gazdagépre regisztrálását.

- Ha karbantartási újraindítást igényel, kap értesítést, ha a karbantartási tervezett. Ezekben az esetekben akkor lesz is kell adni egy olyan időkeretet, ahol megkezdheti a karbantartási saját magának, megfelelő egyszerre.

Ez a lap ismerteti, hogyan Microsoft Azure-ban mindkét karbantartási típusú. Nem tervezett események (hosszabb idejű) kapcsolatos további információkért lásd: a virtuális gépek rendelkezésre állásának kezelése [Windows] (.. / articles/virtual-machines/windows/manage-availability.md) vagy [Linux](../articles/virtual-machines/linux/manage-availability.md).

A virtuális gépen futó alkalmazások jövőbeli frissítések információt tud gyűjteni az Azure metaadat-szolgáltatás használatával [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) vagy [Linux] (.. / articles/virtual-machines/linux/instance-metadata-service.md).

"" Útmutatót a tervezett karbantartások kezelésére, tekintse meg "Kezelési tervezett karbantartás értesítések" [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) vagy [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="memory-preserving-maintenance"></a>Megőrzi az karbantartási memória

Frissítések egy teljes számítógép újraindítása nem szükséges, ha memória megóvása karbantartási mechanizmusok használatával a virtuális gép gyakorolt hatást. A virtuális gép fel van függesztve, legfeljebb 30 másodpercig, a RAM memória megőrzi az üzemeltetési környezetben alkalmazza, a szükséges frissítések és javítások, vagy a virtuális gép gazdagépre helyezi át egy már frissített. A virtuális gép majd folytatja a működését, és a rendszer automatikusan szinkronizálja az óra, a virtuális gép. 

Virtuális gépek a rendelkezésre állási csoportokban a frissítés tartományai frissített egyszerre csak egy. Frissítési tartományok (UD) virtuális gépeinek szünetel, frissítése és majd folytatása előtt a következő UD a tervezett karbantartások helyezi át.

Egyes alkalmazások negatív hatással lehet az ilyen típusú frissítések. Valós idejű Eseményfeldolgozási, például a médiaadatfolyam vagy az átkódolás vagy a magas teljesítmény forgatókönyvek, hálózati végző alkalmazások nem úgy tervezték, hogy egy 30 másodperces szünet működését. <!-- sooooo, what should they do? --> Ha a virtuális gép alatt áthelyezése egy másik gazdagépet, bizonyos érzékeny alkalmazások és szolgáltatások előfordulhat, hogy teljesítménycsökkenést enyhe a vezet a virtuális gép szüneteltetési néhány perc múlva. 


## <a name="maintenance-requiring-a-reboot"></a>A rendszer újraindítását igénylik karbantartás

Ha virtuális gépeket újra kell indítani a tervezett karbantartáshoz, értesítés jelenik meg előre. Tervezett karbantartás két szakasza van: az önkiszolgáló ablakot, és egy ütemezett karbantartási időszaknál.

A **önkiszolgáló ablak** lehetővé teszi, hogy kezdeményezni a virtuális gépeken karbantartási. Ebben az időszakban lekérheti az állapotát tekintheti meg, és ellenőrizze az utolsó karbantartási kérelem eredménye összes virtuális Géphez.

Önkiszolgáló karbantartási indításakor a virtuális gép átkerül egy csomópontra, amely már frissítve van, és majd bekapcsolja azt vissza. A virtuális gép újraindul, mert az ideiglenes lemez, és dinamikus, virtuális hálózati interfészhez társított IP-címek frissülnek.

Önkiszolgáló karbantartási indítja el, és a folyamat során hiba történik, ha a művelet le van állítva, a virtuális gép nem frissül, és a tervezett karbantartások iterációs is a rendszer eltávolítja. A rendszer kapcsolatba lépni a később egy új ütemezéssel rendelkező, majd felkínálja egy új lehetőséget önkiszolgáló karbantartás. 

Az önkiszolgáló időszak elteltével a **ütemezett karbantartási időszaknál** kezdődik. Időablak során továbbra is kereshet a karbantartási időszak, de már nem lehet elindítani a karbantartási magát.

Információ a rendszer újraindítását igénylik karbantartási kezeléséről: "Kezelési tervezett karbantartás értesítések" a [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) vagy [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

## <a name="availability-considerations-during-planned-maintenance"></a>Tervezett karbantartás során a rendelkezésre állási lehetőségekért 

Ha úgy dönt, hogy a tervezett karbantartások megvárni, néhány szempontot kell figyelembe venni a legnagyobb rendelkezésre állást, a virtuális gépek fenntartásához. 

### <a name="paired-regions"></a>Párhuzamos régiók

Minden Azure-régió, egy másik ugyanazon a földrajzi régióját párosított, együtt egy regionális pár élnek. Tervezett karbantartás közben Azure csak frissíti a virtuális gépek régió pár egy régiót. Például ha az USA északi középső régiójában található virtuális gépeket frissíti, az Azure nem fogja frissíteni az USA déli középső régiójában található virtuális gépeket ugyanabban az időben. Azonban más régiók (például Észak-Európa) karbantarthatók ugyanabban az időben, mint az USA keleti régiója. Ismertetése régió párok működése segítségével jobban elosztása a virtuális gépek régiók. További információkért lásd: [az Azure-régió párok](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="availability-sets-and-scale-sets"></a>Rendelkezésre állási készletek és a méretezési csoportok

A munkaterhelés Azure virtuális gépeken való telepítésekor a virtuális gépek rendelkezésre állási készlet magas rendelkezésre állás, az alkalmazás belül is létrehozhat. Ez biztosítja, hogy szolgáltatáskimaradás vagy karbantartási események, legalább egy virtuális gép érhető el.

Egy rendelkezésre állási csoportot belül az egyes virtuális gépek legfeljebb 20 frissítési tartományok (UDs) vannak elosztva. Tervezett karbantartás közben csak egyetlen frissítési tartományi egy adott időpontban van hatással. Vegye figyelembe, hogy a frissítési tartományok befolyásolja sorrendjét nem feltétlenül fordulhat elő egymás után. 

Virtuálisgép-méretezési csoportok olyan Azure számítási erőforrás, amely lehetővé teszi, akkor helyezheti üzembe és felügyelheti az azonos virtuális gépek készletét, amely egyetlen erőforrásra. A méretezési automatikusan telepíti a frissítési tartományokon, például a virtuális gépek rendelkezésre állási csoportba. Csakúgy, mint a rendelkezésre állási csoportok esetében a méretezési csoportok csak egyetlen frissítési tartományi van hatással az adott időpontban.

A magas rendelkezésre állású virtuális gépek konfigurálásával kapcsolatos további információkért tekintse meg a virtuális gépek rendelkezésre állásának kezelése a [Windows](../articles/virtual-machines/windows/manage-availability.md) vagy [Linux](../articles/virtual-machines/linux/manage-availability.md).
