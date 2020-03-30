---
title: Az Azure VM vész-helyreállítási szolgáltatásának beállítása egy másodlagos régióba az Azure Site Recovery szolgáltatással
description: Az Azure Site Recovery szolgáltatás használatával gyorsan beállíthatja a vészhelyreállítást egy másik Azure-régióban egy Azure-beli virtuális géphez.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: e26c2a1f24a88dc979f4ec68de65afc618740c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371864"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Rövid útmutató: Vészhelyreállítás beállítása egy másodlagos Azure-régióba egy Azure virtuális géphez

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás hozzájárul az üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégiához azáltal, hogy az üzleti alkalmazásokat online állapotban tartja a tervezett és nem tervezett leállások során. A Site Recovery kezeli és vezényli a helyszíni gépek és az Azure virtuális gépek (VM) vészutáni helyreállítását, beleértve a replikációt, a feladatátvételt és a helyreállítást.

Ez a rövid útmutató ismerteti, hogyan állíthatja be a vész-helyreállítási egy Azure-beli virtuális gép replikálásával egy másodlagos Azure-régióban. Általában az alapértelmezett beállítások a replikáció engedélyezésére szolgálnak.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag végrehajtásához egy Azure-előfizetésre és egy virtuális gépre van szükség.

- Ha nem rendelkezik aktív előfizetéssel rendelkező Azure-fiókkal, [létrehozhat egy fiókot ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Legalább 1 GB RAM-mal rendelkező virtuális gép használata ajánlott. [További információ](/azure/virtual-machines/windows/quick-create-portal) a virtuális gép létrehozásáról.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="enable-replication-for-the-azure-vm"></a>Azure-beli virtuális gép replikálásának engedélyezése

A következő lépések lehetővé teszik a virtuális gép replikációját egy másodlagos helyre.

1. Az Azure Portalon, a **Home** > **Virtual Machines** menüben válassza ki a virtuális gépet replikálni.
1. A **műveletekben** válassza a **Vészhelyreállítás**lehetőséget.
1. Az **alapok** > **célrégióban**válassza ki a célrégiót.
1. A replikációs beállítások megtekintéséhez válassza a **Véleményezés + Replikáció indítása**lehetőséget. Ha módosítania kell az alapértelmezett beállításokat, válassza **a Speciális beállítások lehetőséget.**
1. A virtuális gép replikációját lehetővé tévő feladat elindításához válassza **a Replikáció indítása**lehetőséget.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Engedélyezze a replikációt.":::

## <a name="verify-settings"></a>A beállítások ellenőrzése

A replikációs feladat befejezése után ellenőrizheti a replikáció állapotát, módosíthatja a replikációs beállításokat, és tesztelheti a központi telepítést.

1. Az Azure Portal menüben válassza **a Virtuális gépek,** és válassza ki a replikált virtuális gépet.
1. A **műveletekben** válassza a **Vészhelyreállítás**lehetőséget.
1. Ha meg szeretné tekinteni a replikáció részleteit az **Áttekintés** lehetőséget **essentials**. További részletek az **Állapot és állapot**, **Feladatátvételi készültség**és az Infrastruktúra **nézet** térkép.

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Replikáció állapota.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A virtuális gép replikációjának leállításához le kell tiltania a replikációt:

- A forrás replikálási beállításai automatikusan törlődnek.
- A virtuális gépre telepített Site Recovery bővítmény replikáció közben nem törlődik.
- Site Recovery számlázása a virtuális gép leáll.

A replikáció letiltásához tegye a következőket:

1. Az Azure Portal menüben válassza **a Virtuális gépek,** és válassza ki a replikált virtuális gépet.
1. A **műveletekben** válassza a **Vészhelyreállítás**lehetőséget.
1. Az **Áttekintés területen**válassza a **Replikáció letiltása**lehetőséget.
1. A Site Recovery bővítmény eltávolításához nyissa meg a virtuális gép > **Beállításbővítményeit.** **Settings**

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="A replikáció letiltása.":::

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy virtuális gépet replikált egy másodlagos régióba. Ezután állítsa be a replikációt több Azure-beli virtuális géphez.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása az Azure-beli virtuális gépekhez](azure-to-azure-tutorial-enable-replication.md)
