---
title: Felderítése és felügyelete az Azure-erőforrások Privileged Identity Management használatával |} Microsoft Docs
description: Azure-erőforrások védelmét PIM ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: 51a10ea164e8bd7650ad2823281d9ed6a4c91915
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="discover-and-manage-azure-resources-by-using-privileged-identity-management"></a>Felderítése és felügyelete az Azure-erőforrások Privileged Identity Management használatával

Megtudhatja, hogyan felderítése és felügyelete az Azure-erőforrások, az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatakor. Ez az információ akkor lehet hasznos, a szervezeteknek, amelyek már a PIM használatával védi a rendszergazda-erőforrásokhoz, és előfizetésnél tulajdonos, aki szeretné éles erőforrások biztonságossá tételére.

Amikor először állítsa be a PIM az Azure-erőforrások, kell felderíteni, és válassza ki azokat az erőforrásokat a PIM védelméhez. A PIM kezelhető erőforrások száma nincs korlátozva van. Azt javasoljuk azonban a legfontosabb (éles) erőforrások kezdve.

> [!NOTE]
> Csak akkor jelölje ki az előfizetéshez kapcsolódó erőforrásokat PIM segítségével történő kezeléséhez. Ha Ön kezeli a PIM előfizetésre, az előfizetés alsóbb szintű erőforrásai is kezelheti.

## <a name="discover-resources"></a>Erőforrások felderítéséhez

Az Azure-portálon lépjen a **Privileged Identity Management** ablaktáblán. A bal oldali menüben az a **kezelése** szakaszban jelölje be **Azure-erőforrások**.

![A "Privileged Identity Management - Azure-erőforrások" ablak](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Ha ez az első alkalommal használja a PIM az Azure-erőforrások, először futtassa a felderítést található erőforrások kezeléséhez. Az a **erőforrások felderítéséhez** ablaktáblán válassza előbb a **erőforrások felderítéséhez** gombra kattintva indítsa el a feltárási folyamata.

![A "Erőforrások felderítéséhez" ablak](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Ha a szervezet egy másik erőforrás vagy a könyvtár rendszergazda már kezeli egy Azure-erőforrás PIM használatával, vagy ha az erőforrás jogosult szerepkör-hozzárendelés, a lista nézet megjeleníti az üzenet **erőforrásokat derítse fel, vagy aktiválni egy a folytatáshoz jogosult szerepkör-hozzárendelés**. 

![A "Erőforrások felderítéséhez" gombra a "Privileged Identity Manager - Azure-erőforrások" ablak](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

Ha bejelöli a **erőforrások felderítéséhez** gombra, a felső menüben vagy közepén a ablaktáblán megjelenik-e az előfizetések, amelyek segítségével kezelheti listája. Kiemelt előfizetések PIM által már védett.

> [!NOTE]
> A megakadályozására, hogy egy másik erőforrás rendszergazda PIM beállítások előfizetés beállítása felügyelt, az előfizetés nem felügyelt nem lehet.

![Az "Azure-erőforrások - felderítési" ablak](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Az a **erőforrás** oszlop, vigye az egérmutatót a PIM védeni kívánt előfizetést. Ezután jelölje be a nevétől balra található erőforrás számára. Egyszerre több előfizetéssel is ki.

![Az "Azure-erőforrások - felderítési" lévő erőforrások listája ablaktábla](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

A bevezetési folyamatának elindításához a felső menüben válassza a **erőforrás kezelése**.

![A "Erőforrás kezelése" gombra az "Azure-erőforrások - felderítési" a panelen](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

A kijelölt erőforrások most PIM kezeli. Zárja be a felderítés képernyő jobb felső sarokban válassza **X**. Megkezdéséhez kezelése PIM-beállítások és hozzárendelése tagjait, a menü felső részén a **Privileged Identity Management - Azure-erőforrások** ablaktáblán válassza előbb a **frissítése** gombra.

![A "Frissítés" gombra a felső menüben, a "Privileged Identity Management - Azure-erőforrások" ablak](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>További lépések

- [Szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)
- [A PIM szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md)
