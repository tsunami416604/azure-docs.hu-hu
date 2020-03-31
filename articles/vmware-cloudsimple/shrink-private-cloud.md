---
title: Az Azure VMware-megoldás zsugorítása a CloudSimple private cloud szolgáltatásával
description: A CloudSimple private cloud zsugorításának ismertetése.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014266"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>CloudSimple magánfelhő zsugorítása

A CloudSimple rugalmasságot biztosít a privát felhő dinamikus zsugorításához.  A magánfelhő egy vagy több vSphere-fürtből áll. Minden fürt rendelkezhet 3–16 csomópontos csomópontokkal. A magánfelhő zsugorításakor eltávolít egy csomópontot a meglévő fürtből, vagy egy teljes fürtet töröl. 

## <a name="before-you-begin"></a>Előkészületek

A magánfelhő zsugorításához a következő feltételeknek kell teljesülniük.  A magánfelhő létrehozásakor létrehozott felügyeleti fürt (első fürt) nem törölhető.

* Egy vSphere-fürtnek három csomódból kell rendelkeznie.  A három csomót csak fürt nem zsugorítható.
* A teljes felhasznált tárterület nem haladhatja meg a fürt zsugorítása utáni teljes kapacitást.
* Ellenőrizze, hogy bármely elosztott erőforrás-ütemező (DRS) szabályok megakadályozzák vMotion egy virtuális gép.  Ha szabályok vannak jelen, tiltsa le vagy törölje a szabályokat.  A DRS-szabályok tartalmazzák a virtuális gépet az affinitási szabályok üzemeltetéséhez.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="shrink-a-private-cloud"></a>Magánfelhő zsugorítása

1. [A CloudSimple portál elérése.](access-cloudsimple-portal.md)

2. Nyissa meg az **Erőforrások** lapot.

3. Kattintson a zsugorítani kívánt magánfelhőre

4. Az összegzés lapon kattintson a **Zsugorítás gombra.**

    ![Magánfelhő zsugorítása](media/shrink-private-cloud.png)

5. Jelölje ki a zsugorítani vagy törölni kívánt fürtöt. 

    ![Magánfelhő zsugorítása – fürt kiválasztása](media/shrink-private-cloud-select-cluster.png)

6. Válassza **az Egy csomópont eltávolítása** vagy a Teljes fürt törlése **lehetőséget.** 

7. A fürt kapacitásának ellenőrzése

8. Kattintson a **Küldés** gombra a magánfelhő zsugorításához.

A magánfelhő zsugorítása elindul.  Figyelemmel kísérheti a tevékenységek előrehaladását.  A zsugorítási folyamat az adatoktól függően néhány órát is igénybe vehet, amelyet újra kell szinkronizálni a vSAN-on.

> [!NOTE]
> 1. Ha egy magánfelhő zsugorítása az adatközpont utolsó vagy egyetlen fürtjének törlésével, az adatközpont nem törlődik.
> 2. Ha a DRS-szabályok megsértése történik, csomópont nem lesz eltávolítva a fürtből, és a feladat leírása azt mutatja, hogy egy csomópont eltávolítása megsérti a DRS-szabályokat a fürtön.    


## <a name="next-steps"></a>További lépések

* [VMware rendszerű virtuális gépek felhasználása az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)
