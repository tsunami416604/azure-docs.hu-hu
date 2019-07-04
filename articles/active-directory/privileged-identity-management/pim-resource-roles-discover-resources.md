---
title: Fedezze fel az Azure-erőforrások kezelése a PIM - Azure Active Directory |} A Microsoft Docs
description: Ismerje meg, hogyan deríthetők fel az Azure-erőforrások kezelése az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b9ca4862f129b2da23a1d1ad8bb0b1bd0a5078f
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476273"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>Fedezze fel a PIM kezelése Azure-erőforrások

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használ, növelheti az Azure-erőforrások védelmét. Ez hasznos szervezeteknek, amelyek már védelme érdekében az Azure AD-szerepköröket a PIM használatát, valamint a felügyeleti csoport és az előfizetések tulajdonosai akik a biztonságos éles erőforrásait.

Először állítsa be a PIM az Azure-erőforrásokhoz, amikor kell felderíteni, és válassza ki az erőforrásokat a PIM védelméhez. A PIM felügyelt erőforrások száma nincs korlátozva van. Azonban javasoljuk, hogy a kritikus fontosságú (éles) erőforrásokkal.

## <a name="discover-resources"></a>Erőforrások észlelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure-erőforrások**.

    Ha az első alkalommal a PIM használata az Azure-erőforrásokhoz, látni fogja a felderítési erőforrások panelen.

    ![Nincsenek erőforrások az első alkalommal élmény felsorolt rendelkező erőforrások panelen felderítése](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Ha a szervezet egy másik erőforrás vagy a könyvtár rendszergazda már van PIM az Azure-erőforrások kezeléséhez, látni fogja a jelenleg kezelt erőforrások listáját.

    ![Jelenleg kezelt erőforrások panelen listázott erőforrások felderítése](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Kattintson a **derítsen fel erőforrásokat** a felfedezési elindításához.

    ![Felderítési panel, amelyek például az előfizetések és a felügyeleti csoportok kezelhetők erőforrások listázása](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. A felderítés panelen használja **erőforrás állapota szerinti szűrő** és **erőforrás típusának kiválasztása** szűrése a felügyeleti csoportok vagy előfizetések rendelkezik írási engedéllyel. A legegyszerűbb valószínűleg a kezdéshez **összes** kezdetben.

    Csak keresse meg és válassza a felügyeleti csoportba vagy előfizetésbe erőforrások a PIM használata kezelheti. Ha Ön kezeli a felügyeleti csoport vagy egy előfizetés az PIM-ben, a gyermekerőforrásait is kezelheti.

1. Adja hozzá a kezelni kívánt bármilyen nem felügyelt erőforrások melletti jelölőnégyzet bejelölésekor.

1. Kattintson a **Erőforráskezelés** a kijelölt erőforrások kezelésének megkezdéséhez.

    > [!NOTE]
    > Miután a felügyeleti csoportba vagy előfizetésbe kezelt van beállítva, nem lehet a nem felügyelt. Ez megakadályozza, hogy egy másik erőforrás-rendszergazda a PIM beállításainak eltávolítása.

    ![Felderítési panelen a kiválasztott erőforrás és a kezelés erőforrás opció kiemelésével](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Ha a kiválasztott erőforrás-kezelés bevezetésének megerősítéséhez üzenet jelenik meg, kattintson a **Igen**.

    ![Felügyeletre kijelölt erőforrás előkészítése megerősítő üzenet](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>További lépések

- [A PIM Azure szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)
- [Rendelje hozzá a PIM az Azure-erőforrásszerepkörök](pim-resource-roles-assign-roles.md)
