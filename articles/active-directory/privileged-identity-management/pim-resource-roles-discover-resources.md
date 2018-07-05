---
title: Fedezze fel, és Azure-erőforrások kezelése a Privileged Identity Management használatával |} A Microsoft Docs
description: Ismerteti, hogyan lehet Azure-erőforrások védelme a PIM használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: aca218a33d148e9f53f405f9cda98a701a7443cc
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442525"
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>Fedezze fel, és Azure-erőforrások kezelése a Privileged Identity Management használatával

Ismerje meg, hogyan derítheti fel és kezelheti az Azure-erőforrásokat az Azure Active Directoryban (Azure AD) Privileged Identity Management (PIM) használatakor. Ez az információ akkor lehet hasznos, szervezetek, amelyek már használják a PIM védelme érdekében a rendszergazda erőforrásokat, és előfizetés-tulajdonosokat, akik a biztonságos éles erőforrásait.

Amikor először állítsa be a PIM az Azure-erőforrásokhoz, kell felderíteni, és válassza ki a PIM használatával védeni kívánt erőforrások. A PIM felügyelt erőforrások száma nincs korlátozva van. Azonban javasoljuk, hogy a kritikus fontosságú (éles) erőforrásokkal.

> [!NOTE]
> Csak keresése és kiválasztása az előfizetési erőforrások kezelése a PIM használatával. Ha Ön kezeli a PIM az előfizetéshez, az előfizetés gyermekerőforrásait is kezelheti.

## <a name="discover-resources"></a>Erőforrások észlelése

Az Azure Portalon nyissa meg a **Privileged Identity Management** ablaktáblán. A bal oldali menüben lévő a **kezelés** szakaszban jelölje be **Azure-erőforrások**.

![A "Privileged Identity Management – Azure-erőforrások" panel](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Ha az első alkalommal a PIM használata az Azure-erőforrásokhoz, először futtassa a felderítést található erőforrások kezeléséhez. Az a **derítsen fel erőforrásokat** panelen válassza a **derítsen fel erőforrásokat** gombra kattintva indítsa el a felderítési élményt.

![A "Derítsen fel erőforrásokat" panel](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Ha a szervezet egy másik erőforrás vagy directory rendszergazda van már egy Azure-erőforrás történő kezelésével a PIM, vagy ha egy erőforráshoz egy jogosult szerepkör-hozzárendelés, a lista nézet jeleníti meg az üzenet **aktiválja az erőforrások felderítését vagy egy jogosult szerepkör-hozzárendelés folytatja**. 

![A "Derítsen fel erőforrásokat" gombra a a "Privileged Identity Manager – Azure-erőforrások" panel](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Amikor kiválasztja a **derítsen fel erőforrásokat** gombra kattint, a felső menüben, vagy a panelen közepén, amelyek segítségével kezelheti az előfizetések listája jelenik meg. A PIM által már védett előfizetéseket, amelyek ki vannak emelve.

> [!NOTE]
> Megakadályozására, hogy egy másik erőforrás-rendszergazda, miközben a PIM-beállítások egy előfizetés beállítása után felügyelt, az előfizetés nem lehet a nem felügyelt.

![Az "Azure erőforrás - felderítés" panel](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Az a **erőforrás** oszlop, vigye az egérmutatót a PIM használatával védeni kívánt előfizetést. Ezután jelölje be a jelölőnégyzetet, bal oldalán található az erőforrás neve. Egyszerre több előfizetést is választhat.

![Az erőforrások listájához a az "Azure erőforrás - felderítés" panel](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Elindítja a bevezetési folyamatot, a felső menüben válassza a **Erőforráskezelés**.

![Az "Erőforrás kezelése" gombra a "Azure-erőforrások – észlelés" panel](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

A kijelölt erőforrások most már a PIM kezeli. Zárja be a felderítés képernyő jobb felső sarokban válassza **X**. A PIM-beállítások kezelését, és a hozzárendelése a tagok, a menü felső részén a **Privileged Identity Management – Azure-erőforrások** panelen válassza a **frissítése** gombra.

![A "Frissítés" gombot a felső menüben, a "Privileged Identity Management – Azure-erőforrások" panel](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>További lépések

- [Szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)
- [A PIM szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md)
