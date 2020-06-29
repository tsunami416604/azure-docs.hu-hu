---
title: Virtuális gépek automatikus leállításának konfigurálása a Azure Lab Servicesban
description: Ez a cikk azt ismerteti, hogyan konfigurálható a virtuális gépek automatikus leállítása a labor-fiókban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 783e3b310b3ad06f637453f0e1b11f6a78beec3a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445814"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>A virtuális gépek automatikus leállításának beállítása a kapcsolat bontási beállításához labor-fiók esetén
A távoli asztali kapcsolat leválasztása után engedélyezheti vagy letilthatja a Windows Lab virtuális gépek (sablon vagy tanuló) automatikus leállítását. Megadhatja azt is, hogy a labor szolgáltatásai mennyi ideig várjon a felhasználó újrakapcsolódására az automatikus leállítás előtt.

## <a name="enable-automatic-shutdown"></a>Automatikus leállítás engedélyezése

1. A **labor-fiók** lapon válassza a bal oldali menü **Labs-beállítások** elemét.
2. Válassza a **virtuális gépek automatikus leállítása a felhasználók leválasztásakor** lehetőséget.
3. Annak megadása, hogy a labor szolgáltatásai mennyi ideig várjon a felhasználó újrakapcsolódására a virtuális gépek automatikus leállítása előtt.

    ![Automatikus leállítás beállítása Lab-fiókban](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    Ez a beállítás a labor-fiókban létrehozott összes laborra vonatkozik. A tesztkörnyezet létrehozója (pedagógus) a tesztkörnyezet szintjén felülbírálhatja ezt a beállítást. A labor-fiókban a beállítás módosítása csak a módosítás után létrehozott laborokra lesz hatással.

    Ennek a beállításnak a letiltásához törölje a jelet a **virtuális gépek automatikus leállítására** szolgáló jelölőnégyzetből, ha a felhasználók ezen a lapon le vannak választva. 

## <a name="next-steps"></a>További lépések
Ez a cikk azt ismerteti, hogyan konfigurálható vagy bírálható el a tesztkörnyezet tulajdonosa a tesztkörnyezet szintjén [.](how-to-enable-shutdown-disconnect.md)
