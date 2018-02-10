---
title: "Az Azure PowerShell szerepköralapú hozzáférés-vezérlés (RBAC) kezelése |} Microsoft Docs"
description: "Hogyan kezelheti az Azure PowerShell, beleértve a szerepköröket, a szerepkörök hozzárendelése és a szerepkör-hozzárendelések törlése RBAC."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: fa364c8f43d38bfa40c2d08cc1fe2d2d46629217
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Szerepköralapú hozzáférés-vezérlés kezelése az Azure PowerShell-lel
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)

Szerepköralapú hozzáférés-vezérlést (RBAC) az Azure portálon, és az Azure erőforrás-kezelési API segítségével hozzáférésének kezelése az előfizetéshez a minden részletre kiterjedő szinten. Ez a szolgáltatás egyes szerepkörök hozzárendelése el egy adott hatókörhöz szerint engedélyezheti a hozzáférést az Active Directory felhasználók, csoportok vagy szolgáltatásnevekről.

PowerShell kezeléséhez RBAC használata előtt kell a következő előfeltételek teljesülését:

* Az Azure PowerShell 0.8.8 verzió vagy újabb. Telepítse a legújabb verziót, és társítsa azt az Azure-előfizetése, [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).
* Az Azure Resource Manager parancsmagok. Telepítse a [Azure Resource Manager parancsmagjainak](/powershell/azure/overview) a PowerShellben.

## <a name="list-roles"></a>Lista szerepkörök
### <a name="list-all-available-roles"></a>Elérhető szerepkörök felsorolása
A lista RBAC szerepkörökhöz kiosztására használható, és a műveletek, amelyhez azokat hozzáférést, a vizsgálandó használó `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![Az RBAC PowerShell-Get AzureRmRoleDefinition – képernyőkép](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Egy szerepkör lista műveletek
Kilistázhatja az adott szerepkörhöz műveletek `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![Az RBAC PowerShell-Get AzureRmRoleDefinition egy adott szerepkör esetében – képernyőkép](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Lásd a kinek van hozzáférése:
A listában a Szerepalapú hozzáférés-hozzárendelések használja `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Szerepkör-hozzárendelések listáját egy adott hatókörhöz
A megadott előfizetés, a erőforráscsoport vagy az erőforrás hozzáférés-hozzárendelések tekintheti meg. Például az összes aktív hozzárendelése egy erőforráscsoport, használja a `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![Az RBAC - Get-AzureRmRoleAssignment erőforráscsoport - PowerShell képernyőképe](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>A felhasználóhoz rendelt lista szerepkörök
Kilistázhatja az adott felhasználóhoz rendelt összes szerepkör és a szerepköröket, amelyek a csoportok, amelyhez a felhasználó tartozik `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![Az RBAC PowerShell-Get AzureRmRoleAssignment egy felhasználó számára – képernyőkép](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Lista klasszikus szolgáltatás-rendszergazda és a szerepkör-hozzárendelések társfelügyeletű
Lista access-hozzárendelések a hagyományos előfizetés rendszergazdai és coadministrators, használja:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Hozzáférés biztosítása
### <a name="search-for-object-ids"></a>Objektumazonosítók keresése
A szerepkör hozzárendeléséhez kell azonosítani az objektum (felhasználó, csoport vagy alkalmazás) és a hatókör.

Ha az előfizetés-azonosító nem tudja, megtalálja a a **előfizetések** panel az Azure portálon. Az előfizetés-azonosító lekérdezése, lásd: [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) az MSDN Webhelyén.

Az objektum azonosítója az Azure AD-csoport, amelyet:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Az objektum azonosítója az Azure AD szolgáltatás egyszerű vagy az alkalmazás használatához:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Az alkalmazást az előfizetési hatókört szerepkör hozzárendelése
Az előfizetés hatókörből alkalmazáshoz való hozzáférés biztosításához használja:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![Az RBAC PowerShell új AzureRmRoleAssignment – képernyőkép](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>A szerepkör hozzárendelése egy felhasználóhoz a erőforrás csoport hatóköre:
Hozzáférés biztosítása a felhasználók a erőforrás hatókörét, használja:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![Az RBAC PowerShell új AzureRmRoleAssignment – képernyőkép](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>A szerepkör hozzárendelése a erőforrás hatókörben csoporthoz
Hozzáférés biztosítása a erőforrás hatókörben egy csoportot, használja:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![Az RBAC PowerShell új AzureRmRoleAssignment – képernyőkép](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Megszünteti a hozzáférést
A felhasználók, csoportok és alkalmazások elérésének eltávolításához használja:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![Az RBAC PowerShell-Remove AzureRmRoleAssignment – képernyőkép](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása
Egy egyéni biztonsági szerepkört hozhat létre a ```New-AzureRmRoleDefinition``` parancsot. Kétféleképpen a szerkezetének kialakítása a szerepkörre, PSRoleDefinitionObject vagy egy JSON-sablon használatával. 

## <a name="get-actions-for-a-resource-provider"></a>Egy erőforrás-szolgáltató műveleteinek beolvasása
Ha teljesen új egyéni szerepkörök hoz létre, fontos tudni, hogy az erőforrás-szolgáltató az összes lehetséges műveletet.
Használja a ```Get-AzureRMProviderOperation``` parancs használatával beszerezheti az ezeket az információkat.
Például ha ellenőrizni kívánja az elérhető műveletek a virtuális gép használja ezt a parancsot:

```
Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize
```

### <a name="create-role-with-psroledefinitionobject"></a>PSRoleDefinitionObject szerepkör létrehozása
Amikor a PowerShell segítségével hozzon létre egy egyéni biztonsági szerepkört, új, vagy próbálkozzon a [beépített szerepkörök](role-based-access-built-in-roles.md) kiindulási pontként. A jelen szakaszban ismertetett példa egy beépített szerepkör kezdődik, és majd testreszabása további engedélyekkel. Szerkessze a attribútumokat kíván hozzáadni a *műveletek*, *notActions*, vagy *hatókörök* , és mentse a módosításokat egy új szerepkörként.

Az alábbi példa kezdődik-e a *virtuális gép közreműködő* szerepkör és a által használt egyéni szerepkör létrehozása, amely nevű *virtuális gépet üzemeltető*. Az új szerepkör hozzáférést biztosít az összes olvasási műveletek a *Microsoft.Compute*, *Microsoft.Storage*, és *Microsoft.Network* erőforrás szolgáltatók és biztosít hozzáférést indítása , indítsa újra, és a virtuális gépek figyelése. Az egyéni biztonsági szerepkört is használható két előfizetésekhez.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![Az RBAC PowerShell-Get AzureRmRoleDefinition – képernyőkép](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>A JSON-sablon szerepkör létrehozása
A JSON-sablon az egyéni szerepkör használható legyen a forrás-definíció. Az alábbi példa létrehoz egy egyéni biztonsági szerepkört, amely olvasási hozzáférést biztosít a tárolási és számítási erőforrásokat, hozzáférés támogatásához, és adja a szerepkört két előfizetések. Hozzon létre egy új fájlt `C:\CustomRoles\customrole1.json` a következő példa a. A azonosítóját kell beállítani. `null` a kezdeti szerepkör létrehozásakor egy új ID automatikusan létrejön. 

```
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
Adja hozzá a szerepkört az előfizetés, futtassa a következő PowerShell-parancsot:
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Egyéni szerepkör módosítása
Hasonló egyéni szerepkör létrehozása, módosíthatja egy meglévő egyéni szerepkört a PSRoleDefinitionObject vagy egy JSON-sablon használatával.

### <a name="modify-role-with-psroledefinitionobject"></a>A PSRoleDefinitionObject szerepkör módosítása
Szeretné módosítani egy egyéni biztonsági szerepkört, először használja a `Get-AzureRmRoleDefinition` parancs beolvasása a szerepkör-definíció. A szerepkör-definíció, végezze el a szükséges módosításokat. Végül a `Set-AzureRmRoleDefinition` parancs menteni a módosított szerepkör-definíció.

A következő példakóddal felveheti a `Microsoft.Insights/diagnosticSettings/*` művelet a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![Az RBAC PowerShell-Set AzureRmRoleDefinition – képernyőkép](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

A következő példa egy Azure-előfizetés hozzáadása a hozzárendelhető hatókörök a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört.

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![Az RBAC PowerShell-Set AzureRmRoleDefinition – képernyőkép](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>Szerepkör JSON-sablon módosítása
Az előző JSON-sablon használatával könnyen módosíthatja egy meglévő egyéni szerepkör hozzáadásához vagy eltávolításához műveletek. A JSON-sablont módosítani, és adja hozzá az olvasási művelet a hálózatkezeléshez, a következő példában látható módon. A sablon szerepel a definíciók összesítve nem érvényesek egy meglévő definíciójához, ami azt jelenti, hogy a szerepkör pontosan úgy adja meg, ha a sablon megjelenik-e. Is módosítania az azonosítót tartalmazó mezőt, azonosító: a szerepkör. Ha nem tudja biztosan az értéket nem, akkor használhatja a `Get-AzureRmRoleDefinition` parancsmagot, hogy megkapja ezt az információt.

```
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

A meglévő szerepkör frissítéséhez futtassa a következő PowerShell-parancsot:
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Egyéni szerepkör törléséhez
Egyéni szerepkör törléséhez használja a `Remove-AzureRmRoleDefinition` parancsot.

A következő példában eltávolítjuk a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört.

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![Az RBAC PowerShell-Remove AzureRmRoleDefinition – képernyőkép](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Egyéni szerepkörök listája
A szerepkörök, amelyek rendelhető hozzá hatókör kilistázhatja a `Get-AzureRmRoleDefinition` parancsot.

Az alábbi példa felsorolja az összes szerepkör, amely a kijelölt előfizetés kiosztására használható.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![Az RBAC PowerShell-Get AzureRmRoleDefinition – képernyőkép](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

A következő példában a *virtuális gépet üzemeltető* egyéni szerepkör nem érhető el a *Production4* előfizetés, mert az adott előfizetéshez nem szerepel a **AssignableScopes** a szerepkör.

![Az RBAC PowerShell-Get AzureRmRoleDefinition – képernyőkép](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Lásd még
* [Using Azure PowerShell használata az Azure Resource Manager eszközzel](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

