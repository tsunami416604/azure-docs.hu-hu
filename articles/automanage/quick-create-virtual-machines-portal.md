---
title: Gyors útmutató – az Azure-beli automanage engedélyezése virtuális gépekhez a Azure Portal
description: Megtudhatja, hogyan engedélyezheti gyorsan a virtuális gépek autofelügyeletét egy új vagy meglévő virtuális gépen a Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: d66c19ce3a9786a5ca0f1390acb398c2a9cf502f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445800"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Gyors útmutató: az Azure automanage engedélyezése virtuális gépekhez a Azure Portal

Ismerkedjen meg az Azure automata felügyeletével a virtuális gépeken a Azure Portal használatával, amely lehetővé teszi az automanagement használatát egy új vagy meglévő virtuális gépen.


## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) .

> [!NOTE]
> Az ingyenes próbaverziós fiókok nem férnek hozzá az oktatóanyagban használt virtuális gépekhez. Váltson utólagos elszámolású előfizetésre.

> [!IMPORTANT]
> Rendelkeznie kell a **közreműködő** szerepkörrel, hogy engedélyezze az automanage-t egy meglévő automanage-fiók használatával. Ha új automanage-fiókkal engedélyezi az autofelügyeletet, a következő engedélyek szükségesek: **tulajdonosi** szerepkör vagy **közreműködő** a **felhasználói hozzáférés rendszergazdai** szerepköreivel együtt.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Virtuális gépek autokezelésének engedélyezése meglévő virtuális gépen

1. A keresési sávban keresse meg és válassza ki az **automanage – Azure virtuális gépek ajánlott eljárásait**.

2. Válassza az **Engedélyezés meglévő virtuális gépen**lehetőséget.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Engedélyezés meglévő virtuális gépen.":::

3. A **gépek kiválasztása** panelen:
    1. A virtuális gépek listájának szűrése az **előfizetés** és az **erőforráscsoport**alapján.
    1. Jelölje be a bevezetéshez használni kívánt virtuális gépek jelölőnégyzetét.
    1. Kattintson a **kiválasztás** gombra.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Engedélyezés meglévő virtuális gépen.":::

4. A **konfigurációs profil**területen kattintson a Tallózás gombra, és válassza a **profilok és beállítások módosítása**elemet.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Engedélyezés meglévő virtuális gépen.":::

5. A **konfigurációs profil és beállítások kiválasztása panelen** :
    1. Válasszon ki egy profilt a bal oldalon: *fejlesztési/tesztelési* teszt *, éles* üzemi környezet.
    1. Kattintson a **kiválasztás** gombra.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Engedélyezés meglévő virtuális gépen.":::

6. Kattintson az **Engedélyezés** gombra.


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>Új virtuális gépen lévő virtuális gépek autokezelésének engedélyezése

Egy új virtuális gép létrehozásához jelentkezzen be [a Azure Portalba, és](https://aka.ms/automanageportalnextstep) engedélyezze az autofelügyeletet.

1. Kövesse a rövid útmutató [– Windows rendszerű virtuális gép létrehozása a Azure Portal](..\virtual-machines\windows\quick-create-portal.md).

2. A virtuális gép üzembe helyezése után az üzembe helyezés állapota lapon a lenti javasolt **következő lépések** láthatók.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="Engedélyezés meglévő virtuális gépen.":::

3. A **következő lépések**szakaszban jelölje be a **virtuális gépekkel kapcsolatos ajánlott eljárások engedélyezése**jelölőnégyzetet.

4. Az automatikus **kezelés – Azure-beli virtuális gépek ajánlott eljárásai** lapon a **gépeket** automatikusan az újonnan létrehozott virtuális gép tölti fel.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="Engedélyezés meglévő virtuális gépen.":::

5. A **konfigurációs profil**területen kattintson a Tallózás gombra, és válassza a **profilok és beállítások módosítása**elemet.

6. A **konfigurációs profil és beállítások kiválasztása panelen** :
    1. Válasszon ki egy profilt a bal oldalon: *fejlesztési/tesztelési* teszt *, éles* üzemi környezet.
    1. Kattintson a **kiválasztás** gombra.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Engedélyezés meglévő virtuális gépen.":::

7. Kattintson az **Engedélyezés** gombra.

## <a name="disable-automanage-for-vms"></a>Virtuális gépek autokezelésének letiltása

Az automanagement szolgáltatás letiltásával gyorsan leállíthatja a virtuális gépek Azure-beli autofelügyeletét.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Engedélyezés meglévő virtuális gépen.":::

1. Nyissa meg az automatikus **kezelés – Azure virtuális gépekre vonatkozó ajánlott eljárásokat** tartalmazó oldalt, amely felsorolja az összes automatikusan felügyelt virtuális gépet.
1. Jelölje be a letiltani kívánt virtuális gép melletti jelölőnégyzetet.
1. Kattintson a **Automanagent letiltása** gombra.
1. A **Letiltás**előtt olvassa el figyelmesen az üzenetküldést az eredményül kapott előugró ablakban.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha létrehozott egy új erőforráscsoportot a virtuális gépek Azure-beli autokezelésének kipróbálásához, és már nincs szüksége rá, törölheti az erőforráscsoportot. A csoport törlésekor a virtuális gép és az erőforráscsoport összes erőforrása is törlődik.

Az Azure automanage alapértelmezett erőforráscsoportokat hoz létre az erőforrások tárolásához a alkalmazásban. A "DefaultResourceGroupRegionName" és a "AzureBackupRGRegionName" elnevezési konvencióval rendelkező erőforráscsoportok használatával törölje az összes erőforrást.

1. Válassza ki az **erőforráscsoportot**.
1. Az erőforráscsoport lapján válassza a **Törlés**lehetőséget.
1. Ha a rendszer kéri, erősítse meg az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.


## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban engedélyezte a virtuális gépek Azure-beli felügyeletét. 

Ismerje meg, hogyan hozhat létre és alkalmazhat testreszabott beállításokat, amikor engedélyezi az automatikus felügyeletet a virtuális gépen. 

> [!div class="nextstepaction"]
> [Azure-beli automatikus felügyelet virtuális gépekhez – egyéni konfigurációs profil](virtual-machines-custom-preferences.md)
