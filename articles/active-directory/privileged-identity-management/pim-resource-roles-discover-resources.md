---
title: Privileged Identity Management az Azure-erőforrások - felderítése és felügyelete az Azure-erőforrások |} Microsoft Docs
description: Ismerteti, hogyan védi az Azure-erőforrások.
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
ms.openlocfilehash: 78650e47ec92aa144e4ccc8c57f309240bf31ee3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="discover-and-manage-azure-resources"></a>Felderítése és felügyelete az Azure-erőforrások

Ha a munkahelyén már működik a Azure AD PIM védelme érdekében a rendszergazdák a könyvtárban, vagy Ön egy szeretnék éles erőforrások biztonságba helyezése az előfizetés tulajdonosa a megfelelő helyen most.

Amikor először engedélyezi az Azure-erőforrások PIM, kell felderíteni, és válassza ki azokat az erőforrásokat a PIM védelméhez. A PIM kezelhető erőforrások száma nincs korlátozva, de javasoljuk, hogy először a kritikus fontosságú (éles) erőforrások kezdve.

> [!Note]
> Csak előfizetéshez kapcsolódó erőforrásokat keres, és választja. Válassza a PIM az előfizetés kezelése, valamint az összes gyermekerőforrásait felügyeletét teszi lehetővé.

## <a name="discover-resources"></a>Erőforrások felderítéséhez

Navigáljon az Azure AD PIM, és válassza ki az Azure-erőforrások kezelése szakaszában a bal oldali navigációs menü.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

Ha az első alkalommal PIM használata az Azure-erőforrások lesz szüksége található erőforrások kezeléséhez felderítés végrehajtásakor.
A "Erőforrások felderítéséhez" gombra a képernyő indítsa el a feltárási folyamata közepére.

![](media/azure-pim-resource-rbac/aadpim_first_run_discovery.png)

Ha a szervezet egy másik erőforrás vagy a könyvtár rendszergazda már kezeli egy Azure-erőforrás PIM, vagy ha az erőforrás jogosult szerepkör-hozzárendelés, a lista nézet fogja tartalmazni az üzenet: "erőforrásokat derítse fel vagy egy erre jogosult szerepkör aktiválásához hozzárendelés folytatja". 

![](media/azure-pim-resource-rbac/aadpim_discover_eligible_not_active.png)

A gomb műveletsávon, vagy a képernyő felderítési erőforrások közepén választja, helyezésekor itt látható egy felügyeleti az elérhető előfizetések listájából. Ekkor megjelenik a kijelölt előfizetéseket azt jelzi, PIM védi azokat.

> [!Note]
> Megakadályozhatja, hogy egy másik erőforrás-rendszergazda a PIM-beállítások eltávolítása előfizetés felügyelt nem lehet nem felügyelt.

![](media/azure-pim-resource-rbac/aadpim_discovery_some_selected.png)

Szeretné védeni a PIM, és jelölje be a sor bal előfizetés mutasson. Egyszerre több előfizetéssel is ki.

![](media/azure-pim-resource-rbac/aadpim_discovery_all_selected.png)

Kezdeményezheti a bevezetési folyamat kijelölésével az a "Erőforrás kezelése" gombra a képernyő tetején.

![](media/azure-pim-resource-rbac/aadpim_discovery_click_manage.png)

A kijelölt erőforrások most PIM kezeli. Zárja be a felderítés képernyő, az "X" használatával az oldal jobb felső sarokban, és kattintson a frissítés megkezdéséhez kezelése PIM-beállítások és hozzárendelése tagok kezelése Azure erőforrások képernyő felső sávon.

![](media/azure-pim-resource-rbac/aadpim_discovery_resources_refresh.png)

## <a name="next-steps"></a>További lépések

[Felhasználóiszerep-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)

[A PIM szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md)
