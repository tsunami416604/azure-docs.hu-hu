---
title: Az Azure-erőforrások rendszerezése felügyeleti csoportok létrehozása
description: Ismerje meg, hogyan kezelhet több olyan erőforrást az Azure felügyeleti csoportok létrehozása.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2018
ms.author: rithorn
ms.openlocfilehash: c2d4317bcbf70a0cebf6ab1915968eeb9ef8b4c6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992626"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Erőforrás-szervezet és felügyeleti felügyeleti csoportok létrehozása

Felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférési házirend és megfelelőség több előfizetést is. Hozzon létre hozhat létre egy leghatékonyabb hierarchia-használható tárolók [Azure Policy](../policy/overview.md) és [Azure szerepkör alapú hozzáférés-vezérlés](../../role-based-access-control/overview.md). A felügyeleti csoport további információkért lásd: [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](overview.md).

Az első felügyeleti csoport a címtárban létrehozott akár 15 percet is igénybe vehet. Nincsenek olyan folyamatokat, amelyek a felügyeleti csoportok szolgáltatás beállítása Azure-ban a címtár első bejelentkezésekor futtatni. Amikor elkészült a folyamat értesítést kap.

## <a name="create-a-management-group"></a>A felügyeleti csoport létrehozása

A felügyeleti csoport a portálon, a PowerShell vagy az Azure CLI használatával hozhat létre. Jelenleg a felügyeleti csoportok létrehozása a Resource Manager-sablonok nem használhat.

### <a name="create-in-portal"></a>A portál létrehozása

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. A fő oldalon válassza ki a **új felügyeleti csoport**.

   ![Fő csoport](./media/main.png)

1. Adja meg a felügyeleti csoport azonosító mező.

   - A **felügyeleti csoport azonosítója** directory egyedi azonosítója, amellyel küldje el a felügyeleti csoportra parancsokat. Ez az azonosító adatok nem módosíthatók létrehozása után, mivel a Azure rendszerben ez a csoport azonosításához használt.
   - A megjelenített név mezőjének Ez a név jelenik meg az Azure Portalon. Egy külön megjelenített név mező kitöltése nem kötelező, ha a felügyeleti csoportot, és bármikor módosítható.  

   ![Létrehozás](./media/create_context_menu.png)  

1. Kattintson a **Mentés** gombra.

### <a name="create-in-powershell"></a>A PowerShell létrehozása

PowerShell-lel, belül a Add-AzureRmManagementGroups parancsmagok használata:

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso'
```

A **GroupName** létrehozott egyedi azonosítója. Ezt az Azonosítót ehhez a csoporthoz hivatkozhat más parancsok használja, és azt később már nem módosítható.

Ha a felügyeleti csoport egy másik nevet az Azure Portalon megjelenítendő szeretett volna, kell felvenni a **DisplayName** karakterlánc paraméterrel. Például ha a felügyeleti csoport létrehozása a Contoso GroupName és "Contoso csoport" megjelenített neve, használja a következő parancsmagot:

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId 'ContosoTenant'
```

Használja a **ParentId** paramétert a felügyeleti csoportot létrehozni egy másik felügyeleti csoportban.

### <a name="create-in-azure-cli"></a>Hozzon létre az Azure CLI-ben

Azure CLI-t, akkor használhat a az fiók felügyeleti-group create paranccsal.

```azurecli-interactive
az account management-group create --group-name 'Contoso'
```

## <a name="next-steps"></a>További lépések

Felügyeleti csoportok kapcsolatos további információkért lásd:

- [Az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](overview.md)
- [Módosítása, törlése és a felügyeleti csoportok kezelése](manage.md)
- [Az Azure Powershell-modul telepítése](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Tekintse át a REST API-specifikációja](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Az Azure CLI-bővítmény telepítése](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)