---
title: Az Azure-erőforrások rendszerezése felügyeleti csoportok létrehozása a |} A Microsoft Docs
description: Ismerje meg, hogyan kezelhet több olyan erőforrást az Azure felügyeleti csoportok létrehozása.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2018
ms.author: rithorn
ms.openlocfilehash: b35803de2cb503418d4373fe3429b81ec5474de4
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358689"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Erőforrás-szervezet és felügyeleti felügyeleti csoportok létrehozása

Felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférési házirend és megfelelőség több előfizetést is. Hozzon létre hozhat létre egy leghatékonyabb hierarchia-használható tárolók [Azure Policy](../azure-policy/azure-policy-introduction.md) és [Azure szerepkör alapú hozzáférés-vezérlés](../role-based-access-control/overview.md). A felügyeleti csoport további információkért lásd: [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok ](management-groups-overview.md).

Az első felügyeleti csoport a címtárban létrehozott akár 15 percet is igénybe vehet. Nincsenek olyan folyamatokat, amelyek a felügyeleti csoportok szolgáltatás beállítása Azure-ban a címtár első bejelentkezésekor futtatni. Amikor elkészült a folyamat értesítést kap.  

## <a name="create-a-management-group"></a>A felügyeleti csoport létrehozása

A felügyeleti csoport a portálon, a PowerShell vagy az Azure CLI használatával hozhat létre.

### <a name="create-in-portal"></a>A portál létrehozása

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.
3. A fő oldalon válassza ki a **új felügyeleti csoportban.**

    ![Fő csoport](media/management-groups/main.png)
4.  Adja meg a felügyeleti csoport azonosító mező.
    - A **felügyeleti csoport azonosítója** directory egyedi azonosítója, amellyel küldje el a felügyeleti csoportra parancsokat. Ez az azonosító adatok nem módosíthatók létrehozása után, mivel a Azure rendszerben ez a csoport azonosításához használt.
    - A megjelenített név mezőjének Ez a név jelenik meg az Azure Portalon. Egy külön megjelenített név mező kitöltése nem kötelező, ha a felügyeleti csoportot, és bármikor módosítható.  

    ![Létrehozás](media/management-groups/create_context_menu.png)  
5.  Válassza ki **mentése**

### <a name="create-in-powershell"></a>A PowerShell létrehozása

PowerShell-lel, belül a Add-AzureRmManagementGroups parancsmagok használata:

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso
```

A **GroupName** létrehozott egyedi azonosítója. Ezt az Azonosítót ehhez a csoporthoz hivatkozhat más parancsok használja, és azt később már nem módosítható.

Ha a felügyeleti csoport egy másik nevet az Azure Portalon megjelenítendő szeretett volna, kell felvenni a **DisplayName** karakterlánc paraméterrel. Például ha a felügyeleti csoport létrehozása a Contoso GroupName és "Contoso csoport" megjelenített neve, használja a következő parancsmagot:

```azurepowershell-interactive
C:\> New-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
```

Használja a **ParentId** paramétert a felügyeleti csoportot létrehozni egy másik felügyeleti csoportban.  

### <a name="create-in-azure-cli"></a>Hozzon létre az Azure CLI-ben

Azure CLI-t, akkor használhat a az fiók felügyeleti-group create paranccsal.

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
```

---

## <a name="next-steps"></a>További lépések 
Felügyeleti csoportok kapcsolatos további információkért lásd: 
- [Az erőforrások rendszerezéséhez az Azure felügyeleti csoportok ](management-groups-overview.md)
- [Módosítása, törlése és a felügyeleti csoportok kezelése](management-groups-manage.md)
- [Az Azure Powershell-modul telepítése](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Tekintse át a REST API-specifikációja](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Az Azure CLI-bővítmény telepítése](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
