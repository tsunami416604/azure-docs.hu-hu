---
title: Az Azure-erőforrások – Azure Cégirányítási rendszerezése felügyeleti csoportok létrehozása
description: Ismerje meg, hogyan kezelhet több olyan erőforrást, a portál, az Azure PowerShell-lel és az Azure CLI használatával az Azure felügyeleti csoportok létrehozása.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: a89df98224634c08c84cb059eb58e64e3c7febf7
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801266"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Erőforrás-szervezet és felügyeleti felügyeleti csoportok létrehozása

Felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférési házirend és megfelelőség több előfizetést is. Hozzon létre hozhat létre egy leghatékonyabb hierarchia-használható tárolók [Azure Policy](../policy/overview.md) és [Azure szerepkör alapú hozzáférés-vezérlés](../../role-based-access-control/overview.md). A felügyeleti csoport további információkért lásd: [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](overview.md).

Az első felügyeleti csoport a címtárban létrehozott akár 15 percet is igénybe vehet. Nincsenek olyan folyamatokat, amelyek a felügyeleti csoportok szolgáltatás beállítása Azure-ban a címtár első bejelentkezésekor futtatni. Amikor elkészült a folyamat értesítést kap.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="create-a-management-group"></a>A felügyeleti csoport létrehozása

A felügyeleti csoport a portálon, a PowerShell vagy az Azure CLI használatával hozhat létre. Jelenleg a felügyeleti csoportok létrehozása a Resource Manager-sablonok nem használhat.

### <a name="create-in-portal"></a>A portál létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. A fő oldalon válassza ki a **új felügyeleti csoport**.

   ![A felügyeleti csoportok kezelése lap](./media/main.png)

1. Adja meg a felügyeleti csoport azonosító mező.

   - A **felügyeleti csoport azonosítója** directory egyedi azonosítója, amellyel küldje el a felügyeleti csoportra parancsokat. Ez az azonosító adatok nem módosíthatók létrehozása után, mivel a Azure rendszerben ez a csoport azonosításához használt.
   - A megjelenített név mezőjének Ez a név jelenik meg az Azure Portalon. Egy külön megjelenített név mező kitöltése nem kötelező, ha a felügyeleti csoportot, és bármikor módosítható.  

   ![Beállítások ablaktábla az új felügyeleti csoport létrehozása](./media/create_context_menu.png)  

1. Kattintson a **Mentés** gombra.

### <a name="create-in-powershell"></a>A PowerShell létrehozása

Belül a PowerShell a New-AzManagementGroup parancsmagot használja:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

A **GroupName** létrehozott egyedi azonosítója. Ezt az Azonosítót ehhez a csoporthoz hivatkozhat más parancsok használja, és azt később már nem módosítható.

Ha a felügyeleti csoport egy másik nevet az Azure Portalon megjelenítendő szeretett volna, kell felvenni a **DisplayName** karakterlánc paraméterrel. Például ha a felügyeleti csoport létrehozása a Contoso GroupName és "Contoso csoport" megjelenített neve, használja a következő parancsmagot:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId '/providers/Microsoft.Management/managementGroups/ContosoTenant'
```

Használja a **ParentId** paramétert a felügyeleti csoportot létrehozni egy másik felügyeleti csoportban.

### <a name="create-in-azure-cli"></a>Hozzon létre az Azure CLI-ben

Azure CLI-t, akkor használhat a az fiók felügyeleti-group create paranccsal.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

## <a name="next-steps"></a>További lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Felügyeleti csoportok létrehozása az Azure-erőforrások rendszerezéséhez](create.md)
- [Felügyeleti csoportok módosítása, törlése és kezelése](manage.md)
- [Felügyeleti csoportok áttekintése az Azure PowerShell Erőforrások moduljában](/powershell/module/az.resources#resources)
- [Felügyeleti csoportok áttekintése a REST API-ban](/rest/api/resources/managementgroups)
- [Felügyeleti csoportok áttekintése az Azure CLI-ben](/cli/azure/account/management-group)