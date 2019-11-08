---
title: Az Azure VMware-megoldás csökkentése a CloudSimple privát felhővel
description: Útmutatás a CloudSimple-alapú privát felhők lekicsinyítéséhez.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ae2f87a3719853f4a91cb8ba801be6d578597d3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825687"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>CloudSimple privát felhő zsugorítása

A CloudSimple rugalmasságot biztosít a privát felhők dinamikus összezsugorodása érdekében.  A privát felhő egy vagy több vSphere-fürtből áll. Minden fürthöz 3 – 16 csomópont tartozhat. Egy privát felhő zsugorításakor eltávolít egy csomópontot a meglévő fürtből, vagy törölhet egy teljes fürtöt. 

## <a name="before-you-begin"></a>Előkészületek

A privát felhő zsugorodása után a következő feltételeknek kell teljesülniük.  A privát felhő létrehozásakor létrehozott felügyeleti fürt (első fürt) nem törölhető.

* A vSphere-fürtnek három csomóponttal kell rendelkeznie.  Csak három csomóponttal rendelkező fürt nem lehet összezsugorodni.
* A felhasznált tárterület teljes mérete nem haladhatja meg a fürt zsugorodása utáni teljes kapacitást. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="shrink-a-private-cloud"></a>Magánfelhő zsugorítása

1. [Nyissa meg a CloudSimple portált](access-cloudsimple-portal.md).

2. Nyissa meg az **erőforrások** lapot.

3. Kattintson a használni kívánt privát felhőre

4. Az összefoglalás lapon kattintson a **zsugorodás**elemre.

    ![Privát felhő zsugorítása](media/shrink-private-cloud.png)

5. Válassza ki a lekicsinyíteni vagy törölni kívánt fürtöt. 

    ![Privát felhő zsugorítása – fürt kiválasztása](media/shrink-private-cloud-select-cluster.png)

6. Válassza az **egyetlen csomópont eltávolítása** vagy **a teljes fürt törlése**lehetőséget. 

7. A fürt kapacitásának ellenőrzése

8. Kattintson a **Submit (Küldés** ) gombra a privát felhő összezsugorodása érdekében.

A privát felhő zsugorodása megkezdődik.  Nyomon követheti a feladatok előrehaladását.  A zsugorodó folyamat néhány órát is igénybe vehet az adattól függően, amelyet újra kell szinkronizálni a vSAN-on.

> [!NOTE]
> Ha az adatközpontban az utolsó vagy az egyetlen fürt törlésével csökkenti a privát felhőt, az adatközpont nem lesz törölve.  


## <a name="next-steps"></a>További lépések

* [VMware virtuális gépek használata az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* További információ a [privát felhőkről](cloudsimple-private-cloud.md)
