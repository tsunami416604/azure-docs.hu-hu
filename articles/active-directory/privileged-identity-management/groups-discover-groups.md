---
title: Szerepkörhöz hozzárendelhető csoportok kezelése Kiemelt jogosultságú hozzáférési csoportként – Azure AD | Microsoft Docs
description: A szerepkörhöz hozzárendelhető csoportok bevezetésének beleegyezik a Privileged Identity Management (PIM) jogosultsági szintű hozzáférési csoportokba való kezeléséhez.
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
ms.date: 08/17/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: a707c66f2562fe442c58ce6292e51c4e67a20ae2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869531"
---
# <a name="manage-privileged-access-groups-preview-in-privileged-identity-management"></a>Kiemelt jogosultságú hozzáférési csoportok (előzetes verzió) kezelése Privileged Identity Management

Azure Active Directory (Azure AD) esetében a szerepkör-hozzárendelések kezelésének egyszerűbbé tétele érdekében az Azure AD beépített szerepkörei a felhőalapú csoportokhoz rendelhetők. Az Azure AD-szerepkörök védelméhez és a hozzáférés biztonságossá tételéhez mostantól Privileged Identity Management (PIM) használatával kezelheti az igény szerinti hozzáférést a csoportok tagjaihoz vagy tulajdonosainak. Ahhoz, hogy egy Azure AD-szerepkörhöz hozzárendelhető csoportot rendszerjogosultságú hozzáférési csoportként kezelje Privileged Identity Managementban, a PIM felügyelete alá kell azt hoznia.

## <a name="identify-groups-to-manage"></a>Kezelendő csoportok azonosítása

Az Azure AD-ben létrehozhat egy szerepkörhöz hozzárendelhető csoportot, amely a [szerepkör-hozzárendelhető csoport létrehozása Azure Active Directoryban](../users-groups-roles/roles-groups-create-eligible.md)című témakörben leírtak szerint használható. A csoport tulajdonosának kell lennie, hogy az Privileged Identity Management felügyelete alá kerüljön.

1. [Jelentkezzen be az Azure ad-be](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazdai jogosultságokkal.
1. Válassza a **csoportok** lehetőséget, majd válassza ki a kezelni kívánt szerepkör-hozzárendelhető csoportot. Kereshet vagy szűrheti a listát.

    ![szerepkörhöz hozzárendelhető csoport keresése a PIM-ben való kezeléshez](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Nyissa meg a csoportot, és válassza a **privilegizált hozzáférés (előzetes verzió)** lehetőséget.

    ![A Privileged Identity Management felület megnyitása](./media/groups-discover-groups/groups-discover-groups.png)

1. Ha a csoportok még nem lettek bevezetve a PIM felügyelete alatt, válassza a **privilegizált hozzáférés engedélyezése** a felügyelethez lehetőséget. Ez a beleegyezés csak a csoport globális rendszergazdája vagy tulajdonosa számára adható meg.

    ![a csoport kezelésének beleegyezett Privileged Identity Management ha szükséges](./media/groups-discover-groups/consent-page.png)

1. A PIM-hozzárendelések kezelésének megkezdése.

    ![Hozzárendelések kezelése Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> A Kiemelt jogosultságú hozzáférési csoport kezelése után nem lehet kivenni a felügyelet alól. Ez megakadályozza, hogy egy másik rendszergazda eltávolítsa Privileged Identity Management beállításait.

## <a name="next-steps"></a>Következő lépések

- [Kiemelt jogosultságú hozzáférési csoport hozzárendeléseinek konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Emelt szintű hozzáférési csoportok kiosztása Privileged Identity Management](pim-resource-roles-assign-roles.md)
