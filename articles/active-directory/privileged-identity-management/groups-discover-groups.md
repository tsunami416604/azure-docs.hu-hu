---
title: Szerepkörhöz hozzárendelhető csoportok felderítése a PIM-ben – Azure AD | Microsoft Docs
description: Megtudhatja, hogyan derítheti fel a szerepkörhöz hozzárendelhető csoportokat, hogy kiemelt jogosultságú hozzáférési csoportként kezelje őket a Privileged Identity Management (PIM) szolgáltatásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b66a41f0f1aa48433dac77fca56d5e2877989a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506003"
---
# <a name="discover-privileged-access-groups-preview-to-manage-in-privileged-identity-management"></a>A felügyelni kívánt Kiemelt hozzáférési csoportok (előzetes verzió) felderítése Privileged Identity Management

Azure Active Directory (Azure AD) esetében a szerepkör-hozzárendelések kezelésének egyszerűbbé tétele érdekében az Azure AD beépített szerepkörei a felhőalapú csoportokhoz rendelhetők. Most már használhatja a Privileged Identity Management (PIM) lehetőséget a csoportok tagságának vagy tulajdonosi jogosultságának hozzárendelésére az Azure AD-szerepkörök védelméhez és a hozzáférés biztosításához. Ahhoz, hogy az Azure AD szerepkörhöz hozzárendelhető csoport felügyelhető legyen Kiemelt hozzáférési csoportként Privileged Identity Managementban, fel kell derítenie azt, és a PIM felügyelete alá kell tartoznia.

## <a name="discover-resources"></a>Erőforrások felfedezése

1. [Jelentkezzen be az Azure ad-be](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda szerepkör engedélyeivel.
1. Szerepkörhöz hozzárendelhető csoport létrehozása az Azure AD-ben. A csoport tulajdonosának kell lennie, hogy felderítse és felügyelje Privileged Identity Managementával.
1. Nyissa meg **Privileged Identity Management**.
1. Válassza a **privilegizált hozzáférés (előzetes verzió)** lehetőséget.

    ![Csoportok felderítése parancs az első alkalommal](./media/groups-discover-groups/groups-discover-groups.png)

1. Válassza a **csoportok felderítése**lehetőséget.
1. Keresés csoport neve alapján.
1. Válassza ki a csoportot, és válassza a **csoportok kezelése** lehetőséget, hogy a PIM felügyelete alá kerüljön.

    ![Olyan csoportok felderítése, amelyek nem rendelkeznek az első alkalommal tapasztalt erőforrásokkal](./media/groups-discover-groups/groups-bring-under-management.png)

    > [!NOTE]
    > A Kiemelt jogosultságú hozzáférési csoport kezelése után nem lehet kivenni a felügyelet alól. Ez megakadályozza, hogy egy másik erőforrás-rendszergazda eltávolítsa Privileged Identity Management beállításait.

1. Ha megjelenik egy üzenet, amely megerősíti a kiválasztott erőforrás bevezetését a felügyelethez, válassza az **Igen**lehetőséget.

## <a name="next-steps"></a>További lépések

- [Kiemelt jogosultságú hozzáférési csoport hozzárendeléseinek konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Emelt szintű hozzáférési csoportok kiosztása Privileged Identity Management](pim-resource-roles-assign-roles.md)
