---
title: A Windows virtuális gép felhasználó lehet hozzárendelve MSI Azure Resource Manager eléréséhez használja
description: Ez az oktatóanyag bemutatja, hogyan a folyamatot, amely a felhasználó hozzárendelt felügyelt szolgáltatás identitásának (MSI) használata a Windows virtuális gép, Azure Resource Manager eléréséhez.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: d80e0fc35b8c20bd61a78a68542f3311c1a40952
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>A felhasználó hozzárendelt felügyelt szolgáltatás identitásának (MSI) használja a Windows virtuális gép, Azure Resource Manager eléréséhez

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ez az oktatóanyag ismerteti, hogyan hozzon létre egy felhasználó lehet hozzárendelve identitás, rendelje hozzá a Windows virtuális gép (VM) és az Azure Resource Manager API eléréséhez majd használja az identitásukat. Felügyelt szolgáltatás-identitások Azure automatikusan kezeli. Az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatok beágyazása a kódot támogató szolgáltatások lehetővé teszik. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Windows rendszerű virtuális gép létrehozása 
> * Hozzon létre egy felhasználó lehet hozzárendelve identitás
> * Rendelje hozzá a felhasználó lehet hozzárendelve a Windows virtuális gép identitása
> * A felhasználó identitása hozzáférési jogot egy erőforráscsoportot az Azure Resource Manager 
> * Szereznie egy hozzáférési jogkivonatot használja a felhasználó identitását, és hívja az Azure Resource Manager használatával 
> * Erőforráscsoport tulajdonságainak olvasása

## <a name="prerequisites"></a>Előfeltételek

- Ha a felügyelt Szolgáltatásidentitás ismerik biztosan tekintse meg a [áttekintése](overview.md) szakasz. **Ne feledje el áttekinteni a [rendszer és a felhasználó közötti különbségek hozzárendelt identitások](overview.md#how-does-it-work)**.
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.
- Ebben az oktatóanyagban a szükséges erőforrások létrehozása és a szerepkör felügyeleti lépések végrehajtásához a fiókot kell a "Tulajdonos" engedélyeket a megfelelő hatókörben (saját előfizetés vagy az erőforrás csoport). Ha a szerepkör-hozzárendelés segítségre van szüksége, tekintse meg [Use Role-Based hozzáférés-vezérlés kezelése az Azure-előfizetés erőforrásokhoz való hozzáférés](/azure/role-based-access-control/role-assignments-portal).

PowerShell telepítéséhez és használatához helyileg választja, az oktatóanyag az Azure PowerShell 5.7-es vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

A következő példában az erőforráscsoport neve, *myResourceGroupVM* jön létre a *EastUS* régióban.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Az erőforráscsoport létrehozása után hozzon létre egy Windows virtuális Gépet.

A virtuális gép rendszergazdai fiókjának felhasználónevét és jelszavát állítsa be a [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal:

```azurepowershell-interactive
$cred = Get-Credential
```
Hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) parancsmaggal.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>Hozzon létre egy felhasználó lehet hozzárendelve identitás

A felhasználó identitása Azure-erőforrás különálló jön létre. Használja a [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity), az Azure létrehoz identitás, amely egy vagy több Azure szolgáltatáspéldány lehet hozzárendelni az Azure AD-bérlőben.

> [!IMPORTANT]
> A felhasználói identitások a különleges karakterek (pl. aláhúzásjel) nevében létrehozása jelenleg nem támogatott. Alfanumerikus karaktereket használja. Biztonsági frissítések ellenőrzése.  További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

A válasz a felhasználó identitása létrehozott, az alábbi példához hasonló részleteit tartalmazza. Megjegyzés: a `Id` érték a felhasználó identitását, a következő lépésben lesz használható:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Rendelje hozzá a felhasználó identitását egy Windows virtuális gépre

Egy felhasználó lehet hozzárendelve identitás több Azure-erőforrások az ügyfelek által használható. A következő parancsok segítségével a felhasználó identitásának rendelhet egy virtuális. Használja a `Id` tulajdonságot adott vissza az előző lépésben a `-IdentityID` paraméter.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Adja meg a felhasználói hozzáférés MSI rendelt egy erőforráscsoportot az Azure Resource Manager 

Felügyelt szolgáltatás identitásának (MSI) biztosít identitásokat tartalmaz, amelyek a kód segítségével való hitelesítésre szolgáló erőforrás API-k, támogatás az Azure AD-hitelesítés hozzáférési jogkivonatok igényelhetnek. Ebben az oktatóanyagban a kódot az Azure Resource Manager API-val érik el. 

A kódot az API eléréséhez, az identitás hozzáférést egy erőforráshoz az Azure Resource Manager szeretné. Ebben az esetben az erőforráscsoportot, amelyben a virtuális gép található. Frissítse az értéket a `<SUBSCRIPTION ID>` a környezetnek megfelelően.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

A válasz tartalmazza a létrehozott, az alábbi példához hasonló szerepkör-hozzárendelés részletei:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Szereznie egy hozzáférési jogkivonatot, a virtuális gép azonosítójának használatával, és hívja az erőforrás-kezelő használatával 

Az oktatóanyag a hátralévő fog működni a korábban létrehozott virtuális gépről.

1. Jelentkezzen be az Azure-portálon: [https://portal.azure.com](https://portal.azure.com)

2. A portálon lépjen a **virtuális gépek** és nyissa meg a Windows rendszerű virtuális gép és a a **áttekintése**, kattintson a **Connect**.

3. Adja meg a **felhasználónév** és **jelszó** a Windows virtuális gép létrehozásakor használt.

4. Most, hogy létrehozott egy **távoli asztali kapcsolat** nyissa meg a virtuális gép **PowerShell** a távoli munkamenet.

5. A PowerShell használatával `Invoke-WebRequest`, indítson egy lekérdezést a helyi MSI-végpont megszerezni egy hozzáférési jogkivonatot az Azure Resource Manager.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Erőforráscsoport tulajdonságainak olvasása

Használja a hozzáférési jogkivonat elérését az Azure Resource Manager és az erőforrás tulajdonságainak olvasása az előző lépésben beolvasott csoport rendelkeznek a hozzárendelt felhasználói identitás hozzáférést. Cserélje le <SUBSCRIPTION ID> az előfizetés-azonosítója a környezetben.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
A válasz az adott erőforráscsoport információkat tartalmaz, a következőhöz hasonló:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>További lépések

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](overview.md).
