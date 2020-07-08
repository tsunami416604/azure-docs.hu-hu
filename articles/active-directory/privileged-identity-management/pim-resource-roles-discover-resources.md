---
title: Azure-erőforrások felderítése a PIM-beli kezeléshez – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan derítheti fel az Azure-erőforrásokat Azure AD Privileged Identity Management (PIM) kezeléséhez.
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
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4852f841fc9ac8ebea586d5e24967a1db20b8044
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743711"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>A Privileged Identity Management felügyelni kívánt Azure-erőforrások felderítése

Az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával javíthatja az Azure-erőforrások védelmét. Ez olyan szervezetek számára hasznos, amelyek már használják a Privileged Identity Management az Azure AD-szerepkörök védelmére, valamint a felügyeleti csoport és az előfizetések tulajdonosai számára, akik az éles erőforrásokat szeretnék biztosítani.

Amikor először állít be Privileged Identity Management az Azure-erőforrásokhoz, fel kell derítenie és ki kell választania az Privileged Identity Management védelemmel ellátni kívánt erőforrásokat. A Privileged Identity Management használatával felügyelhető erőforrások száma nincs korlátozva. Javasoljuk azonban, hogy a legjelentősebb (éles) erőforrásokkal kezdjen.

## <a name="discover-resources"></a>Erőforrások felfedezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. Válassza ki az **Azure-erőforrásokat**.

    Ha első alkalommal használja az Azure-erőforrások Privileged Identity Managementét, megjelenik a **Discover Resources (erőforrások felderítése** ) oldal.

    ![Erőforrások ablaktábla észlelése az első alkalommal felmerülő erőforrások nélkül](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Ha a szervezet egy másik rendszergazdája már felügyeli az Azure-erőforrásokat Privileged Identity Managementban, megjelenik a jelenleg kezelt erőforrások listája.

    ![A jelenleg felügyelt erőforrások listázása ablaktábla](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. A felderítési élmény elindításához válassza az **erőforrások felderítése** lehetőséget.

    ![A felderítés ablaktábla a felügyelhető erőforrások, például az előfizetések és a felügyeleti csoportok listázására szolgál.](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. A **felderítés** lapon használja az **Erőforrás-állapot szűrőt** , és **válassza az erőforrástípus lehetőséget** azon felügyeleti csoportok vagy előfizetések szűréséhez, amelyekhez írási engedéllyel rendelkezik. Valószínűleg **a legegyszerűbb, ha először** kezdi el.

    Csak a Privileged Identity Management használatával felügyelhető felügyeleti csoport vagy előfizetési erőforrások kereshetők és választhatók ki. Ha Privileged Identity Managementban felügyel egy felügyeleti csoportot vagy előfizetést, akkor a gyermek erőforrásai is kezelhetők.

1. Jelölje be a felügyelni kívánt nem felügyelt erőforrások melletti jelölőnégyzetet.

1. Válassza az **erőforrás kezelése** lehetőséget a kiválasztott erőforrások kezelésének megkezdéséhez.

    > [!NOTE]
    > A felügyeleti csoport vagy előfizetés kezelése után nem lehet felügyelni. Ez megakadályozza, hogy egy másik erőforrás-rendszergazda eltávolítsa Privileged Identity Management beállításait.

    ![Felderítés ablaktábla kiválasztott erőforrással és az erőforrás kezelése lehetőség kiemelve](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Ha megjelenik egy üzenet, amely megerősíti a kiválasztott erőforrás bevezetését a felügyelethez, válassza az **Igen**lehetőséget.

    ![A kiválasztott erőforrások felügyeletre való bevezetését igazoló üzenet](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>További lépések

- [Az Azure erőforrás-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Azure-beli erőforrás-szerepkörök kiosztása Privileged Identity Management](pim-resource-roles-assign-roles.md)
