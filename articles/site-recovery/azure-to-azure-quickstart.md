---
title: Azure-beli IaaS virtuális gépek másodlagos Azure-régióba történő vészhelyreállításának beállítása
description: Ebből a rövid útmutatóból megismerheti az Azure-beli IaaS virtuális gépek Azure-régiók között történő vészhelyreállításának lépéseit az Azure Site Recovery szolgáltatás használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d23d16d1315eeaf224a5291641ab088212fcdc77
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146949"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Azure-beli virtuális gépek másodlagos Azure-régióba történő vészhelyreállításának beállítása        

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás működőképes állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez a rövid útmutató azt ismerteti, hogyan állítható be a vész-helyreállítás egy Azure-beli virtuális gépen egy másik Azure-régióba való replikálással.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> Ez a cikk az új felhasználóknak szóló rövid útmutató. A legegyszerűbb elérési utat használja az alapértelmezett beállításokkal és a minimális testreszabással.  A részletes útmutatóért tekintse [](azure-to-azure-tutorial-enable-replication.md)át az oktatóanyagot.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="enable-replication-for-the-azure-vm"></a>Azure-beli virtuális gép replikálásának engedélyezése

1. Az Azure Portal területén kattintson a **Virtuális gépek** elemre, és válassza ki a replikálni kívánt virtuális gépet.
2. A **Műveletek** területen kattintson a **Vészhelyreállítás** elemre.
3. A **Vészhelyreállítás konfigurálása** > **Célrégió** részben válassza ki a célrégiót, amelybe a replikálást végezni kívánja.
4. A jelen rövid útmutató esetében fogadja el a többi alapértelmezett beállítást.
5. Kattintson a **Replikáció engedélyezése** lehetőségre. Ekkor elindul a virtuális gép replikálásának engedélyezési feladata.

    ![replikáció engedélyezése](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>A beállítások ellenőrzése

A replikálási feladat befejeződése után ellenőrizheti a replikálás állapotát, módosíthatja a replikációs beállításokat, és tesztelheti az üzemelő példányt.

1. A **Műveletek** területen kattintson a **Vészhelyreállítás** elemre.
2. Ellenőrizheti a replikálás állapotát, a létrehozott helyreállítási pontokat, valamint a forrás- és célrégiókat a térképen.

   ![A replikálás állapota](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az elsődleges régióban lévő virtuális gép replikálása leáll, ha letiltja a gép esetében a replikálást:

- A forrás replikálási beállításai automatikusan törlődnek. A virtuális gépre a replikálás részeként telepített Site Recovery bővítmény nem törlődik, és manuálisan el kell távolítani. 
- A virtuális gép Site Recovery számlázása leáll.

A replikálás leállítása a következőképpen

1. Válassza ki a virtuális gépet.
2. A **Vészhelyreállítás** területen kattintson **A replikálás letiltása** elemre.

   ![A replikálás letiltása](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy virtuális gépet replikált egy másodlagos régióba. Most próbálkozzon egy helyreállítási terv használatával több Azure-beli virtuális gép replikálásával.

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek vészhelyreállításának konfigurálása](azure-to-azure-tutorial-enable-replication.md)
