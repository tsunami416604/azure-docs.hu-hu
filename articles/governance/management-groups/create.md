---
title: Felügyeleti csoportok létrehozása az erőforrások rendszerezéséhez – Azure-szabályozás
description: Ismerje meg, hogyan hozhat létre Azure felügyeleti csoportokat több erőforrás kezeléséhez a portál, a Azure PowerShell és az Azure CLI használatával.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 34815089367512c4aa54f148c118a669625d0ea3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381588"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Felügyeleti csoportok létrehozása az erőforrások rendszerezéséhez és kezeléséhez

A felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférést, a szabályzatot és a megfelelőséget több előfizetés között. Hozza létre ezeket a tárolókat egy olyan hatékony és hatékony hierarchia létrehozásához, amely a [Azure Policy](../policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md)használható. A felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](overview.md).

A címtárban létrehozott első felügyeleti csoport akár 15 percet is igénybe vehet. Az Azure-ban a címtárhoz a felügyeleti csoportok szolgáltatás beállításához első alkalommal futó folyamatok futnak. A folyamat befejezésekor értesítést kap. További információ: [a felügyeleti csoportok kezdeti beállítása](./overview.md#initial-setup-of-management-groups).

## <a name="create-a-management-group"></a>Felügyeleti csoport létrehozása

A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoport írási engedélye hozzá lenne rendelve az adott felhasználóhoz. Ez az új felügyeleti csoport a legfelső szintű felügyeleti csoport gyermeke lesz, és a létrehozó a "tulajdonos" szerepkör-hozzárendelést kapja. A felügyeleti csoport szolgáltatás lehetővé teszi, hogy a szerepkör-hozzárendelések nem szükségesek a gyökérszintű szinten. A létrehozáskor egyetlen felhasználó sem férhet hozzá a gyökérszintű felügyeleti csoportjához. Ha el szeretné kerülni, hogy az Azure AD globális rendszergazdái megkeressék a felügyeleti csoportokat, lehetővé tesszük a kezdeti felügyeleti csoportok létrehozását a gyökérkönyvtárban.  
szintű.

A felügyeleti csoportot a portál, a [Resource Manager-sablon](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group), a PowerShell vagy az Azure CLI használatával hozhatja létre.

### <a name="create-in-portal"></a>Létrehozás a portálon

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).

1. Válassza **a minden szolgáltatás** > **kezelése + irányítás**lehetőséget.

1. **Cost Management és számlázás** kiválasztása

1. A Cost Management + számlázási felügyeleti csoportok lapon válassza a **Management groups**

1. Válassza a **+ felügyeleti csoport hozzáadása**lehetőséget.

   :::image type="content" source="./media/main.png" alt-text="A felügyeleti csoportok használatáról szóló oldal" border="false":::

1. Töltse ki a felügyeleti csoport azonosítója mezőt.

   - A **felügyeleti csoport azonosítója** a címtár egyedi azonosítója, amely a parancsok ezen a felügyeleti csoporton való elküldésére szolgál. Ez az azonosító nem szerkeszthető a létrehozás után, mivel az egész Azure-rendszeren a csoport azonosítására szolgál. A rendszer automatikusan létrehozza a [gyökérszintű felügyeleti csoportot](overview.md#root-management-group-for-each-directory) a Azure Active Directory azonosítójaként megadott azonosítóval. Az összes többi felügyeleti csoport esetében rendeljen hozzá egy egyedi azonosítót.
   - A megjelenítendő név mező a Azure Portal belül megjelenő név. A felügyeleti csoport létrehozásakor egy külön megjelenítendő név választható mező, amely bármikor módosítható  
     idő.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Beállítások ablaktábla új felügyeleti csoport létrehozásához" border="false":::

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

## <a name="next-steps"></a>További lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Felügyeleti csoportok létrehozása az Azure-erőforrások rendszerezéséhez](./create.md)
- [Felügyeleti csoportok módosítása, törlése és kezelése](./manage.md)
- [Felügyeleti csoportok áttekintése az Azure PowerShell Erőforrások moduljában](/powershell/module/az.resources#resources)
- [Felügyeleti csoportok áttekintése a REST API-ban](/rest/api/resources/managementgroups)
- [Felügyeleti csoportok áttekintése az Azure CLI-ben](/cli/azure/account/management-group)
