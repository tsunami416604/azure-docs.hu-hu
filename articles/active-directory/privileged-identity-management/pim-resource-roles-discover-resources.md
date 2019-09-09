---
title: Azure-erőforrások felderítése a PIM-ben – Azure Active Directory | Microsoft Docs
description: Ismerje meg, hogyan derítheti fel az Azure-erőforrásokat Azure AD Privileged Identity Management (PIM) kezeléséhez.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1f1712ab7474747829c8744a39e74b78777668
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804164"
---
# <a name="discover-azure-resources-to-manage-in-pim"></a>A PIM-ban kezelendő Azure-erőforrások felderítése

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával javíthatja az Azure-erőforrások védelmét. Ez olyan szervezetek számára hasznos, amelyek már használják a PIM-t az Azure AD-szerepkörök védelmére, valamint a felügyeleti csoport és az előfizetések tulajdonosai számára, akik az éles erőforrásokat szeretnék biztosítani.

Amikor először állítja be a PIM-t az Azure-erőforrásokhoz, fel kell derítenie és ki kell választania a PIM-védelemmel ellátott erőforrásokat. A PIM használatával felügyelhető erőforrások száma nem korlátozható. Javasoljuk azonban, hogy a legjelentősebb (éles) erőforrásokkal kezdjen.

## <a name="discover-resources"></a>Erőforrások észlelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Kattintson az **Azure-erőforrások**elemre.

    Ha első alkalommal használja a PIM-t az Azure-erőforrásokhoz, megjelenik a Discover Resources (erőforrások felderítése) panel.

    ![Erőforrások ablaktábla észlelése az első alkalommal felmerülő erőforrások nélkül](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Ha a szervezet egy másik erőforrás-vagy címtár-rendszergazdája már kezeli az Azure-erőforrásokat a PIM-ban, megjelenik a jelenleg felügyelt erőforrások listája.

    ![A jelenleg felügyelt erőforrások listázása ablaktábla](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. A felderítési élmény elindításához kattintson az **erőforrások felderítése** elemre.

    ![A felderítés ablaktábla a felügyelhető erőforrások, például az előfizetések és a felügyeleti csoportok listázására szolgál.](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. A felderítés ablaktáblán használja az **Erőforrás-állapot szűrőt** , és **válassza az erőforrástípus lehetőséget** azon felügyeleti csoportok vagy előfizetések szűréséhez, amelyekhez írási engedéllyel rendelkezik. Valószínűleg **a legegyszerűbb, ha először** kezdi el.

    Csak a PIM használatával felügyelhető felügyeleti csoport vagy előfizetési erőforrások kereshetők és választhatók ki. Ha felügyeleti csoportot vagy előfizetést kezel a PIM-ben, akkor a gyermek erőforrásai is kezelhetők.

1. Vegyen fel egy pipát a felügyelni kívánt nem felügyelt erőforrások mellett.

1. Kattintson az **erőforrás kezelése** elemre a kiválasztott erőforrások kezelésének megkezdéséhez.

    > [!NOTE]
    > Ha egy felügyeleti csoport vagy előfizetés felügyelt értékre van beállítva, nem lehet felügyelet nélküli. Ez megakadályozza, hogy egy másik erőforrás-rendszergazda eltávolítsa a PIM-beállításokat.

    ![Felderítés ablaktábla kiválasztott erőforrással és az erőforrás kezelése lehetőség kiemelve](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Ha megjelenik egy üzenet, amely megerősíti a kiválasztott erőforrás előkészítését a felügyelethez, kattintson az **Igen**gombra.

    ![A kiválasztott erőforrások felügyeletre való bevezetését igazoló üzenet](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>További lépések

- [Az Azure erőforrás-szerepkör beállításainak konfigurálása a PIM-ben](pim-resource-roles-configure-role-settings.md)
- [Azure-beli erőforrás-szerepkörök kiosztása a PIM-ben](pim-resource-roles-assign-roles.md)
