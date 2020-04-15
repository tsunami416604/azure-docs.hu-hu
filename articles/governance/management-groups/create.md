---
title: Felügyeleti csoportok létrehozása az erőforrások rendszerezéséhez - Azure Governance
description: Ismerje meg, hogyan hozhat létre Azure felügyeleti csoportokat több erőforrás kezeléséhez a portál, az Azure PowerShell és az Azure CLI használatával.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 34815089367512c4aa54f148c118a669625d0ea3
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381588"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Felügyeleti csoportok létrehozása az erőforrások rendszerezéséhez és kezeléséhez

A felügyeleti csoportok olyan tárolók, amelyek segítségével több előfizetésben kezelheti a hozzáférést, a szabályzatot és a megfelelőséget. Ezeket a tárolókat hozza létre az [Azure Policy](../policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérlésekkel](../../role-based-access-control/overview.md)használható hatékony és eredményes hierarchia létrehozásához. A felügyeleti csoportokról az [Erőforrások rendszerezése az Azure felügyeleti csoportokkal](overview.md)című témakörben talál további információt.

A címtárban létrehozott első felügyeleti csoport létrehozása akár 15 percet is igénybe vehet. Vannak olyan folyamatok, amelyek első alkalommal futnak a felügyeleti csoportok szolgáltatás beállításához az Azure-ban a címtárban. Értesítést kap, ha a folyamat befejeződött. További információt [a felügyeleti csoportok kezdeti beállításában talál.](./overview.md#initial-setup-of-management-groups)

## <a name="create-a-management-group"></a>Felügyeleti csoport létrehozása

A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot a felügyeleti csoport írási engedélye nélkül, amely az adott felhasználóhoz van rendelve. Ez az új felügyeleti csoport a gyökérfelügyeleti csoport gyermeke lesz, és a létrehozó "Tulajdonos" szerepkör-hozzárendelést kap. A felügyeleticsoport-szolgáltatás lehetővé teszi ezt a képességet, így a szerepkör-hozzárendelések nem szükségesek a gyökér szinten. Egyetlen felhasználó sem férhet hozzá a gyökérfelügyeleti csoporthoz létrehozáskor. Annak elkerülése érdekében, hogy az Azure AD globális rendszergazdáinak megtalálása a felügyeleti csoportok használatának megkezdéséhez akadályokat gördítsen elé, lehetővé tesszük a kezdeti felügyeleti csoportok létrehozását a legfelső szinten  
Szinten.

A felügyeleti csoportot a portál, a [Resource Manager-sablon](../../azure-resource-manager/templates/deploy-to-tenant.md#create-management-group), a PowerShell vagy az Azure CLI használatával hozhatja létre.

### <a name="create-in-portal"></a>Létrehozás a portálon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Válassza az **Összes szolgáltatás** > **kezelése + cégirányítás lehetőséget.**

1. **Költségkezelés + számlázás** kiválasztása

1. A Költségkezelés + Számlázás – Felügyeleti csoportok lapon válassza a **Felügyeleti csoportok** lehetőséget.

1. Válassza a **+ Felügyeleti csoport hozzáadása lehetőséget.**

   :::image type="content" source="./media/main.png" alt-text="Lap a felügyeleti csoportokkal való együttműködéshez" border="false":::

1. Töltse ki a felügyeleti csoport azonosító mezőjét.

   - A **felügyeleti csoport azonosítója** az a könyvtáregyedi azonosító, amely a felügyeleti csoport parancsainak elküldésére szolgál. Ez az azonosító létrehozása után nem szerkeszthető, mivel az Azure-rendszerben használják a csoport azonosítására. A [gyökérfelügyeleti csoport](overview.md#root-management-group-for-each-directory) automatikusan létrejön egy azonosítót, amely az Azure Active Directory-azonosító. Az összes többi felügyeleti csoporthoz rendeljen egyedi azonosítót.
   - A megjelenítendő név mező az Azure Portalon megjelenő név. A külön megjelenítendő név nem kötelező mező a felügyeleti csoport létrehozásakor, és bármely  
     Idő.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Beállítások ablaktábla új felügyeleti csoport létrehozásához" border="false":::

1. Kattintson a **Mentés** gombra.

### <a name="create-in-powershell"></a>Létrehozás a PowerShellben

A PowerShell esetében a [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) parancsmag használatával hozzon létre egy új felügyeleti csoportot.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

A **Csoportnév** egy létrehozás alatt álló egyedi azonosító. Ezt az azonosítót más parancsok is használják a csoportra való hivatkozáshoz, és később nem módosítható.

Ha azt szeretné, hogy a felügyeleti csoport egy másik nevet jelenítsen meg az Azure Portalon belül, adja hozzá a **DisplayName** paramétert. Ha például a Contoso GroupName nevű felügyeleti csoportgal és a "Contoso Group" megjelenítendő nevével szeretne felügyeleti csoportot létrehozni, használja a következő parancsmast:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

Az előző példákban az új felügyeleti csoport jön létre a gyökér felügyeleti csoport alatt. Ha szülőként másik felügyeleti csoportot szeretne megadni, használja a **ParentId paramétert.**

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Létrehozás az Azure CLI-ben

Az Azure CLI, használja az [az-fiók felügyeleti csoport létrehozása](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) parancs egy új felügyeleti csoport létrehozásához.

```azurecli-interactive
az account management-group create --name Contoso
```

A **név** egy létrehozás alatt lévő egyedi azonosító. Ezt az azonosítót más parancsok is használják a csoportra való hivatkozáshoz, és később nem módosítható.

Ha azt szeretné, hogy a felügyeleti csoport egy másik nevet jelenítsen meg az Azure Portalon belül, adja hozzá a **megjelenítendő név** paramétert. Ha például a Contoso GroupName nevű felügyeleti csoporttal és a "Contoso Group" megjelenítendő nevével szeretne felügyeleti csoportot létrehozni, használja a következő parancsot:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

Az előző példákban az új felügyeleti csoport jön létre a gyökér felügyeleti csoport alatt. Ha szülőként másik felügyeleti csoportot szeretne **parent** megadni, használja a szülőparamétert, és adja meg a szülőcsoport nevét.

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
