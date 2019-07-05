---
title: Azure VMware-megoldás által CloudSimple Magánfelhő zsugorítása
description: Ismerteti, hogyan lehet CloudSimple Magánfelhő zsugorítani.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544516"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>CloudSimple Magánfelhő zsugorítása

CloudSimple rugalmasságot biztosít, és dinamikusan csökkentheti a Magánfelhő.  Magánfelhő egy vagy több vSphere-fürtöket tartalmaz. Ha mindegyik fürthöz is 3 – 16 csomóponttal rendelkezik. Magánfelhő zsugorítását, ha eltávolít egy csomópontot a meglévő fürtből, vagy törli a teljes fürtöt. 

## <a name="before-you-begin"></a>Előkészületek

A következő feltételeknek kell teljesülnie kell zsugorítás magánfelhő.  Felügyeleti fürt (első saját fürt) létrehozott Magánfelhő létrehozásakor nem lehet törölni.

* Egy vSphere-fürtöt három csomóponttal kell rendelkeznie.  Mindössze három csomóponttal rendelkező fürt nem sikerült zsugorítani.
* Teljes felhasznált tárterület után a fürt zsugorítás nem haladhatja meg a teljes kapacitás. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="shrink-a-private-cloud"></a>Magánfelhő zsugorítása 

1. [A CloudSimple portál eléréséhez](access-cloudsimple-portal.md).

2. Nyissa meg a **erőforrások** lapot.

3. Kattintson a Magánfelhő szeretné csökkenteni a

4. Az összefoglalás lapon kattintson a **zsugorítani**.

    ![Magánfelhő zsugorítása](media/shrink-private-cloud.png)

5. Válassza ki a fürt csökkenhessen vagy törölni kívánt. 

    ![Magánfelhő - válassza ki a fürt zsugorítása](media/shrink-private-cloud-select-cluster.png)

6. Válassza ki **eltávolít egy csomópontot** vagy **az egész fürt törlése**. 

7. A fürt kapacitásának ellenőrzése

8. Kattintson a **küldés** zsugorítására a Magánfelhő.

A privát felhő zsugorítás elindul.  A folyamat előrehaladását a feladatok figyelheti.  A Zsugorítás folyamat, amelynek kell lennie resynced a vsan-hoz, az adatoktól függően több órát is igénybe vehet.

## <a name="next-steps"></a>További lépések

* [Az Azure-ban a VMware virtuális gépeket felhasználására](quickstart-create-vmware-virtual-machine.md)
* Tudjon meg többet [Magánfelhők létrehozása](cloudsimple-private-cloud.md)