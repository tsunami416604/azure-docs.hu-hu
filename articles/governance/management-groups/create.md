---
title: Felügyeleti csoportok létrehozása az erőforrások rendszerezéséhez – Azure-szabályozás
description: Ismerje meg, hogyan hozhat létre Azure felügyeleti csoportokat több erőforrás kezeléséhez a portál, a Azure PowerShell és az Azure CLI használatával.
author: rthorn17
ms.service: governance
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 17154ea72a49ff48a482b1149eeecf0229fcccd7
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254688"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Felügyeleti csoportok létrehozása az erőforrás-szervezethez és-kezeléshez

A felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférést, a szabályzatot és a megfelelőséget több előfizetés között. Hozza létre ezeket a tárolókat egy olyan hatékony és hatékony hierarchia létrehozásához, amely a [Azure Policy](../policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md)használható. A felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](overview.md).

A címtárban létrehozott első felügyeleti csoport akár 15 percet is igénybe vehet. Az Azure-ban a címtárhoz a felügyeleti csoportok szolgáltatás beállításához első alkalommal futó folyamatok futnak. A folyamat befejezésekor értesítést kap.

## <a name="create-a-management-group"></a>Felügyeleti csoport létrehozása

A felügyeleti csoportot a portál, a PowerShell vagy az Azure CLI használatával hozhatja létre. Jelenleg nem használhat Resource Manager-sablonokat felügyeleti csoportok létrehozásához.

### <a name="create-in-portal"></a>Létrehozás a portálon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza **a minden szolgáltatás** > **felügyelet + irányítás**lehetőséget.

1. **Cost Management és számlázás** kiválasztása

1. A Cost Management + számlázási felügyeleti csoportok lapon válassza a **Management groups**

1. Válassza a **+ felügyeleti csoport hozzáadása**lehetőséget.

   ![A felügyeleti csoportok használatáról szóló oldal](./media/main.png)

1. Töltse ki a felügyeleti csoport azonosítója mezőt.

   - A **felügyeleti csoport azonosítója** a címtár egyedi azonosítója, amely a parancsok ezen a felügyeleti csoporton való elküldésére szolgál. Ez az azonosító nem szerkeszthető a létrehozás után, mivel az egész Azure-rendszeren a csoport azonosítására szolgál. A rendszer automatikusan létrehozza a [gyökérszintű felügyeleti csoportot](overview.md#root-management-group-for-each-directory) a Azure Active Directory azonosítójaként megadott azonosítóval. Az összes többi felügyeleti csoport esetében rendeljen hozzá egy egyedi azonosítót.
   - A megjelenítendő név mező a Azure Portal belül megjelenő név. A felügyeleti csoport létrehozásakor egy külön megjelenítendő név nem választható mező, és bármikor módosítható.  

   ![Beállítások ablaktábla új felügyeleti csoport létrehozásához](./media/create_context_menu.png)  

1. Kattintson a **Mentés** gombra.

### <a name="create-in-powershell"></a>Létrehozás a PowerShellben

A PowerShell esetében a [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) parancsmag használatával hozzon létre egy új felügyeleti csoportot.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

A **Csoportnév** egy egyedi azonosítót hoz létre. Ezt az azonosítót más parancsok használják a csoportra való hivatkozáshoz, és később nem módosíthatók.

Ha azt szeretné, hogy a felügyeleti csoport más nevet jelenítsen meg a Azure Portalon belül, adja hozzá a **DisplayName** paramétert. Ha például egy olyan felügyeleti csoportot szeretne létrehozni a contoso csoportnév-vel és a "contoso-csoport" megjelenített nevével, amely a következő parancsmagot használja:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Az előző példákban az új felügyeleti csoport a gyökérszintű felügyeleti csoport alatt jön létre. Másik felügyeleti csoport szülőként való megadásához használja a **ParentID** paramétert.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Létrehozás az Azure CLI-ben

Az Azure CLI esetében használja az az [Account Management-Group Create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) parancsot egy új felügyeleti csoport létrehozásához.

```azurecli-interactive
az account management-group create --name Contoso
```

A **név** a létrehozandó egyedi azonosító. Ezt az azonosítót más parancsok használják a csoportra való hivatkozáshoz, és később nem módosíthatók.

Ha azt szeretné, hogy a felügyeleti csoport más nevet jelenítsen meg a Azure Portalon belül, adja hozzá a **Display-Name** paramétert. Ha például egy olyan felügyeleti csoportot szeretne létrehozni a contoso csoportnév-vel és a "contoso Group" névvel, amely a következő parancsot használja:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Az előző példákban az új felügyeleti csoport a gyökérszintű felügyeleti csoport alatt jön létre. Másik felügyeleti csoport szülőként való megadásához használja a **szülő** paramétert, és adja meg a szülő csoport nevét.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Következő lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Felügyeleti csoportok létrehozása az Azure-erőforrások rendszerezéséhez](create.md)
- [Felügyeleti csoportok módosítása, törlése és kezelése](manage.md)
- [Felügyeleti csoportok áttekintése az Azure PowerShell Erőforrások moduljában](/powershell/module/az.resources#resources)
- [Felügyeleti csoportok áttekintése a REST API-ban](/rest/api/resources/managementgroups)
- [Felügyeleti csoportok áttekintése az Azure CLI-ben](/cli/azure/account/management-group)
