---
title: Azure VMware-megoldás törlése a CloudSimple private cloud szolgáltatásával
description: A CloudSimple private cloud törlésének ismertetése.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024755"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>CloudSimple privát felhő törlése

A CloudSimple rugalmasságot biztosít a magánfelhő törléséhez.  A magánfelhő egy vagy több vSphere-fürtből áll. Minden fürt rendelkezhet 3–16 csomópontos csomópontokkal. Ha töröl egy magánfelhőt, az összes fürt törlődik.

## <a name="before-you-begin"></a>Előkészületek

A magánfelhő törlése törli a teljes magánfelhőt.  A magánfelhő összes összetevője törlődik.  Ha meg szeretné tartani az adatokat, győződjön meg arról, hogy biztonsági másolatot tett az adatokról a helyszíni tárolóba vagy az Azure-tárolóba.

A magánfelhő összetevői a következők:

* CloudSimple csomópontok
* Virtuális gépek
* Virtuális helyi hálózatok vagy alhálózatok
* A magánfelhőn tárolt összes felhasználói adat
* A VIRTUÁLIS/ ALHÁLÓZAT összes tűzfalszabály-melléklete

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="delete-a-private-cloud"></a>Magánfelhő törlése

1. [A CloudSimple portál elérése.](access-cloudsimple-portal.md)

2. Nyissa meg az **Erőforrások** lapot.

3. Kattintson a törölni kívánt magánfelhőre

4. Az összesítő lapon kattintson a **Törlés gombra.**

    ![Magánfelhő törlése](media/delete-private-cloud.png)

5. A megerősítő lapon adja meg a magánfelhő nevét, és kattintson a **Törlés gombra.** 

    ![Privát felhő törlése - megerősítés](media/delete-private-cloud-confirm.png)

A magánfelhő törlésre van megjelölve.  A törlési folyamat három óra elteltével kezdődik, és törli a magánfelhőt.

> [!CAUTION]
> A csomópontokat a magánfelhő törlése után törölni kell.  A csomópontok mérése addig folytatódik, amíg a csomópontok törlődnek az előfizetésből.

## <a name="next-steps"></a>További lépések

* [Csomópontok törlése](delete-nodes.md)
