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
ms.openlocfilehash: b06cae5bcb07831ba79b805802a7851c2b6ad2b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467951"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Azure-beli virtuális gépek másodlagos Azure-régióba történő vészhelyreállításának beállítása        

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás működőképes állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez a rövid útmutató azt ismerteti, hogyan állítható be a vész-helyreállítás egy Azure-beli virtuális gépen egy másik Azure-régióba való replikálással.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> Ez a cikk az új felhasználóknak szóló rövid útmutató. A legegyszerűbb elérési utat használja az alapértelmezett beállításokkal és a minimális testreszabással.  A részletes útmutatóért tekintse át az [oktatóanyagot](azure-to-azure-tutorial-enable-replication.md).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="enable-replication-for-the-azure-vm"></a>Azure-beli virtuális gép replikálásának engedélyezése

1. A Azure Portal menüben válassza a **virtuális gépek**lehetőséget, vagy keresse meg és válassza ki a *virtuális gépeket* bármely oldalon. Válassza ki a replikálni kívánt virtuális gépet.
2. A **Műveletek** részen válassza a **Vészhelyreállítás** elemet.
3. A **Vészhelyreállítás konfigurálása** > **Célrégió** részben válassza ki a célrégiót, amelybe a replikálást végezni kívánja.
4. A jelen rövid útmutató esetében fogadja el a többi alapértelmezett beállítást.
5. Válassza a **felülvizsgálat + replikáció indítása**lehetőséget. Ezután válassza a **replikálás indítása** lehetőséget a virtuális gép replikálásának engedélyezéséhez.

    ![replikáció engedélyezése](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>A beállítások ellenőrzése

A replikálási feladat befejeződése után ellenőrizheti a replikálás állapotát, módosíthatja a replikációs beállításokat, és tesztelheti az üzemelő példányt.

1. A Azure Portal menüben válassza a **virtuális gépek**lehetőséget, vagy keresse meg és válassza ki a *virtuális gépeket* bármely oldalon. Válassza ki az ellenőrizni kívánt virtuális gépet.
2. A **Műveletek** részen válassza a **Vészhelyreállítás** elemet.

   Ellenőrizheti a replikálás állapotát, a létrehozott helyreállítási pontokat, valamint a forrás- és célrégiókat a térképen.

   ![A replikálás állapota](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az elsődleges régióban lévő virtuális gép replikálása leáll, ha letiltja a gép esetében a replikálást:

- A forrás replikálási beállításai automatikusan törlődnek. A virtuális gépre a replikálás részeként telepített Site Recovery bővítmény nem törlődik, és manuálisan el kell távolítani. 
- A virtuális gép Site Recovery számlázása leáll.

A replikálás leállítása a következőképpen

1. A Azure Portal menüben válassza a **virtuális gépek**lehetőséget, vagy keresse meg és válassza ki a *virtuális gépeket* bármely oldalon. Válassza ki a módosítani kívánt virtuális gépet.
2. A vész- **helyreállítás**területen válassza a **replikáció letiltása**lehetőséget.

   ![A replikálás letiltása](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy virtuális gépet replikált egy másodlagos régióba. Most próbálkozzon egy helyreállítási terv használatával több Azure-beli virtuális gép replikálásával.

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek vészhelyreállításának konfigurálása](azure-to-azure-tutorial-enable-replication.md)
