---
title: Az Privileged Identity Management-Azure AD-ben kezelendő csoport azonosítása | Microsoft Docs
description: Bemutatjuk, hogyan lehet felügyelt szerepkörrel rendelkező csoportokat bevezetni a Privileged Identity Management (PIM) rendszerjogosultságú hozzáférési csoportjaiba.
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
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76f7b61b6fb065409a2fe4b2ed8f599f321cd1db
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542284"
---
# <a name="bring-a-privileged-access-groups-preview-into-privileged-identity-management"></a>Emelt szintű hozzáférési csoportok (előzetes verzió) beszerzése Privileged Identity Management

Azure Active Directory (Azure AD) esetében a szerepkör-hozzárendelések kezelésének egyszerűbbé tétele érdekében az Azure AD beépített szerepkörei a felhőalapú csoportokhoz rendelhetők. Az Azure AD-szerepkörök védelméhez és a hozzáférés biztonságossá tételéhez mostantól Privileged Identity Management (PIM) használatával kezelheti az igény szerinti hozzáférést a csoportok tagjaihoz vagy tulajdonosainak. Ahhoz, hogy egy Azure AD-szerepkörhöz hozzárendelhető csoportot rendszerjogosultságú hozzáférési csoportként kezelje Privileged Identity Managementban, a PIM felügyelete alá kell azt hoznia.

## <a name="identify-groups-to-manage"></a>Kezelendő csoportok azonosítása

Az Azure AD-ben létrehozhat egy szerepkörhöz hozzárendelhető csoportot, amely a [szerepkör-hozzárendelhető csoport létrehozása Azure Active Directoryban](../users-groups-roles/roles-groups-create-eligible.md)című témakörben leírtak szerint használható. A csoport tulajdonosának kell lennie, hogy a felügyelete alá kerüljön Privileged Identity Management.

1. [Jelentkezzen be az Azure ad-be](https://aad.portal.azure.com) a Kiemelt szerepkörű rendszergazda szerepkör engedélyeivel.
1. Válassza a **csoportok** lehetőséget, majd válassza ki a PIM-ban felügyelni kívánt szerepkörhöz hozzárendelhető csoportot. Kereshet és szűrheti a listát.

    ![szerepkörhöz hozzárendelhető csoport keresése a PIM-ben való kezeléshez](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Nyissa meg a csoportot, és válassza a **privilegizált hozzáférés (előzetes verzió)** lehetőséget.

    ![A Privileged Identity Management felület megnyitása](./media/groups-discover-groups/groups-discover-groups.png)

1. A PIM-hozzárendelések kezelésének megkezdése.

    ![Hozzárendelések kezelése Privileged Identity Management](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> A Kiemelt jogosultságú hozzáférési csoport kezelése után nem lehet kivenni a felügyelet alól. Ez megakadályozza, hogy egy másik erőforrás-rendszergazda eltávolítsa Privileged Identity Management beállításait.

## <a name="next-steps"></a>További lépések

- [Kiemelt jogosultságú hozzáférési csoport hozzárendeléseinek konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Emelt szintű hozzáférési csoportok kiosztása Privileged Identity Management](pim-resource-roles-assign-roles.md)
