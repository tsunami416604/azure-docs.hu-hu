---
title: Azure-beli IaaS virtuális gépek másodlagos Azure-régióba történő vészhelyreállításának beállítása
description: Ebből a rövid útmutatóból megismerheti az Azure-beli IaaS virtuális gépek Azure-régiók között történő vészhelyreállításának lépéseit az Azure Site Recovery szolgáltatás használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 10/28/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e5f6cbec75a9f652cf05c9608c412e00489288b9
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212930"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Azure-beli virtuális gépek másodlagos Azure-régióba történő vészhelyreállításának beállítása 

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás működőképes állapotban tartja az üzleti alkalmazásokat a tervezett és nem tervezett leállások idején, így segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A Site Recovery felügyeli és koordinálja a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

Ez a rövid útmutató egy Azure-beli virtuális gép másik Azure-régióba való replikálását ismerteti.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> A cikk az új felhasználóknak hivatott bemutatni az Azure Site Recovery használatát az alapértelmezett beállításokkal és minimális testreszabással. Ha többet szeretne megtudni a különféle testreszabható beállításokról, tekintse meg [az Azure-beli virtuális gépek replikálásának engedélyezését bemutató oktatóanyagot](azure-to-azure-tutorial-enable-replication.md).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com címen.

## <a name="enable-replication-for-the-azure-vm"></a>Azure-beli virtuális gép replikálásának engedélyezése

1. Az Azure Portal területén kattintson a **Virtuális gépek** elemre, és válassza ki a replikálni kívánt virtuális gépet.
2. A **Műveletek** területen kattintson a **Vészhelyreállítás** elemre.
3. A **Vészhelyreállítás konfigurálása** > **Célrégió** részben válassza ki a célrégiót, amelybe a replikálást végezni kívánja.
4. A jelen rövid útmutató esetében fogadja el a többi alapértelmezett beállítást.
5. Kattintson a **Replikáció engedélyezése** lehetőségre. Ekkor elindul a virtuális gép replikálásának engedélyezési feladata.

    ![replikáció engedélyezése](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>A beállítások ellenőrzése

A replikálási feladat befejeződése után ellenőrizheti a replikálás állapotát, módosíthatja a replikációs beállításokat, és tesztelheti az üzemelő példányt.

1. A Virtuális gép menüben kattintson a **Vészhelyreállítás** elemre.
2. Ellenőrizheti a replikálás állapotát, a létrehozott helyreállítási pontokat, valamint a forrás- és célrégiókat a térképen.

   ![A replikálás állapota](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az elsődleges régióban lévő virtuális gép replikálása leáll, ha letiltja a gép esetében a replikálást:

- A forrás replikálási beállításai automatikusan törlődnek.
- Emellett leáll a virtuális gép Site Recovery-számlázása is.

Állítsa le a replikálást a következő módon:

1. Válassza ki a virtuális gépet.
2. A **Vészhelyreállítás** területen kattintson **A replikálás letiltása** elemre.

   ![A replikálás letiltása](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy virtuális gépet replikált egy másodlagos régióba. Most megismerheti a további beállításokat, és megpróbálhatja Azure-beli virtuális gépek egy csoportját replikálni helyreállítási terv alapján.

> [!div class="nextstepaction"]
> [Azure-beli virtuális gépek vészhelyreállításának konfigurálása](azure-to-azure-tutorial-enable-replication.md)
