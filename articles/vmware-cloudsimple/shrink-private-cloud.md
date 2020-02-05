---
title: Az Azure VMware Solutions (AVS) privát felhő csökkentése
description: Útmutató az AVS Private-felhők összezsugorodása érdekében.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014266"
---
# <a name="shrink-an-avs-private-cloud"></a>AVS privát felhő zsugorítása

Az AVS rugalmasságot biztosít az AVS-alapú privát felhő dinamikus csökkentése érdekében. Az AVS Private Cloud egy vagy több vSphere-fürtből áll. Minden fürthöz 3 – 16 csomópont tartozhat. Egy AVS-beli privát felhő zsugorításakor eltávolít egy csomópontot a meglévő fürtből, vagy törölhet egy teljes fürtöt. 

## <a name="before-you-begin"></a>Előzetes teendők

Az AVS Private-felhő csökkentése előtt a következő feltételeknek kell teljesülniük. A felügyeleti fürt (az első fürt) az AVS Private Cloud létrehozásakor jön létre. Nem törölhető.

* A vSphere-fürtnek három csomóponttal kell rendelkeznie. Csak három csomóponttal rendelkező fürt nem lehet összezsugorodni.
* A felhasznált tárterület teljes mérete nem haladhatja meg a fürt zsugorodása utáni teljes kapacitást.
* Ellenőrizze, hogy az elosztott erőforrás-ütemező (DRS) szabályai meggátolják-e a virtuális gépek vMotion. Ha szabályok vannak jelen, tiltsa le vagy törölje a szabályokat. A DRS-szabályok közé tartozik a virtuális gép az affinitási szabályok üzemeltetéséhez.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="shrinking-an-avs-private-cloud"></a>Az AVS Private Cloud csökkentése

1. [Hozzáférés az AVS-portálhoz](access-cloudsimple-portal.md).

2. Nyissa meg az **erőforrások** lapot.

3. Kattintson a zsugorodni kívánt AVS Private-felhőre

4. Az összefoglalás lapon kattintson a **zsugorodás**elemre.

    ![Az AVS Private Cloud zsugorítása](media/shrink-private-cloud.png)

5. Válassza ki a lekicsinyíteni vagy törölni kívánt fürtöt. 

    ![Az AVS Private Cloud zsugorítása – fürt kiválasztása](media/shrink-private-cloud-select-cluster.png)

6. Válassza az **egyetlen csomópont eltávolítása** vagy **a teljes fürt törlése**lehetőséget. 

7. A fürt kapacitásának ellenőrzése

8. Kattintson a **Submit (elküldés** ) elemre az AVS Private Cloud kicsinyítéséhez.

Az AVS Private Cloud lekicsinyítése megkezdődik. Nyomon követheti a feladatok előrehaladását. A zsugorodó folyamat néhány órát is igénybe vehet az adattól függően, amelyet újra kell szinkronizálni a vSAN-on.

> [!NOTE]
> 1. Ha az adatközpontban az utolsó vagy az egyetlen fürt törlésével csökkenti a privát felhőt, az adatközpont nem lesz törölve.
> 2. Ha bármely DRS-szabály megsértése történik, a rendszer nem távolítja el a csomópontot a fürtből, és a feladat leírása azt mutatja, hogy a csomópontok eltávolítása megsérti a fürt DRS-szabályait.    


## <a name="next-steps"></a>Következő lépések

* [VMware virtuális gépek használata az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ az [AVS Private felhőkről](cloudsimple-private-cloud.md)
