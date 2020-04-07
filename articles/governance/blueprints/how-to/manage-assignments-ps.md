---
title: Hozzárendelések kezelése a PowerShell használatával
description: Ismerje meg, hogyan kezelheti a blueprint-hozzárendelések a hivatalos Azure Blueprints PowerShell-modul, az Az.Blueprint.
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: 0868e5e207202511c1981a930870bfdc68a77a8f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677431"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Hozzárendelések kezelése a PowerShell használatával

Egy tervezet-hozzárendelés az **Az.Blueprint** Azure PowerShell-modulhasználatával kezelhető. A modul támogatja a hozzárendelések lehívását, létrehozását, frissítését és eltávolítását. A modul a meglévő tervezetdefiníciók részleteit is lehívhatja. Ez a cikk bemutatja, hogyan kell telepíteni a modult, és indítsa el annak használatát.

## <a name="add-the-azblueprint-module"></a>Az Az.Blueprint modul hozzáadása

Ahhoz, hogy az Azure PowerShell kezelni a tervezet-hozzárendelések, a modulhozzá kell adni. Ez a modul használható a helyileg telepített PowerShell, [az Azure Cloud Shell,](https://shell.azure.com)vagy az [Azure PowerShell Docker-lemezkép.](https://hub.docker.com/r/azuresdk/azure-powershell/)

### <a name="base-requirements"></a>Alapszintű követelmények

Az Azure Blueprints modulhoz a következő szoftver szükséges:

- Azure PowerShell 1.5.0 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1-es vagy újabb verzió. Ha nincs telepítve vagy frissítve, kövesse [ezeket az utasításokat](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>A modul telepítése

Az Azure Blueprints modul powershell az **Az.Blueprint.**

1. Egy **felügyeleti** PowerShell-parancssorból futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Ha az **Az.Accounts** már telepítve van, `-AllowClobber` szükség lehet a telepítés kényszerítéséhez.

1. Annak ellenőrzése, hogy a modul importálása megtörtént-e, és a megfelelő verziójú-e (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Tervdefiníciók beszereznie

A hozzárendelések munkaelső lépése gyakran egy tervezetdefinícióra való hivatkozás.
A `Get-AzBlueprint` parancsmag egy vagy több tervezetdefiníciót kap. A parancsmag tervezetdefiníciókat kaphat le `-ManagementGroupId {mgId}` egy felügyeleti `-SubscriptionId {subId}`csoportból vagy egy előfizetésből. A **Name** paraméter lead egy tervezetdefiníciót, de a **ManagementGroupId** vagy **a SubscriptionId**szolgáltatással kell használni. **Verzió** használható **a név,** hogy pontosabban mely tervezet definíciója ad vissza. A **Verzió**helyett `-LatestPublished` a kapcsoló megragadja a legutóbb közzétett verziót.

A következő `Get-AzBlueprint` példa a "101-blueprints-definition-subscription" nevű tervezetdefiníció összes verzióját egy `{subId}`adott előfizetésből kapja, amely a következő:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

A több verzióval rendelkező tervezetdefiníció példakimenete így néz ki:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

A [tervezet paraméterei](../concepts/parameters.md#blueprint-parameters) a tervezet definíciója bővíthető, hogy több információt.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Tervrajzok-hozzárendelések beszereznie

Ha a tervrajz-hozzárendelés már létezik, a `Get-AzBlueprintAssignment` parancsmaggal hivatkozást kaphat rá. A parancsmag **a SubscriptionId** és **a Name választható** paramétereket veszi fel. Ha **a SubscriptionId** nincs megadva, a jelenlegi előfizetési környezet et használja a program.

A következő `Get-AzBlueprintAssignment` példa egyetlen "Hozzárendelés-zárolás-erőforrás-csoportok" nevű társterv-hozzárendelés levételét használja egy adott előfizetésből, amely a következő: `{subId}`

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

A példa kimenetegy tervezet hozzárendelés így néz ki:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Tervhozzárendelések létrehozása

Ha a tervrajz-hozzárendelés még nem létezik, `New-AzBlueprintAssignment` létrehozhatja azt a parancsmaggal. Ez a parancsmag a következő paramétereket használja:

- **Név** [kötelező]
  - Megadja a tervrajz-hozzárendelés nevét.
  - Egyedinek kell lennie, és még nem létezik a **SubscriptionId-ban**
- **Tervrajz** [kötelező]
  - Megadja a hozzárendelni hezrendelt terv definícióját.
  - A `Get-AzBlueprint` referenciaobjektum lefoglalásának használata
- **Hely** [kötelező]
  - Megadja a rendszeráltal hozzárendelt felügyelt identitás és előfizetés-telepítési objektum régióját, amelyben létre kell hozni
- **Előfizetés** (nem kötelező)
  - Megadja azt az előfizetést, amelyhez a hozzárendelés telepítve van
  - Ha nincs megadva, az aktuális előfizetési környezet alapértelmezései
- **Zárolás** (nem kötelező)
  - Az üzembe helyezett erőforrásokhoz használandó [tervezeterőforrás-zárolást](../concepts/resource-locking.md) határozza meg.
  - Támogatott beállítások: _Nincs_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Ha nincs megadva, alapértelmezés _szerint_ nincs
- **SystemAssignedIdentity** (nem kötelező)
  - Bejelölésével rendszeráltal hozzárendelt felügyelt identitást hozhat létre a hozzárendeléshez, és üzembe szeretné helyezni az erőforrásokat.
  - Az "identity" paraméterkészlet alapértelmezett beállítása
  - Nem használható a **UserAssignedIdentity-vel**
- **UserAssignedIdentity** (nem kötelező)
  - Megadja a hozzárendeléshez és az erőforrások üzembe helyezéséhez használandó, felhasználó által hozzárendelt felügyelt identitást.
  - Az "identity" paraméterkészlet része
  - Nem használható a **SystemAssignedIdentity-val**
- **Paraméter** (nem kötelező)
  - Kulcs-/értékpárok [kivonattáblázata](/powershell/module/microsoft.powershell.core/about/about_hash_tables) a [tervezet hozzárendelésének dinamikus paramétereinek](../concepts/parameters.md#dynamic-parameters) beállításához
  - A dinamikus paraméterek alapértelmezett értéke a definíció **alapértelmezettértéke**
  - Ha egy paraméter nincs megadva, és nincs **defaultValue értéke,** a paraméter nem választható.

    > [!NOTE]
    > **A paraméter** nem támogatja a secureStrings-t.

- **ResourceGroupParameter** (nem kötelező)
  - Erőforráscsoport-összetevők [kivonattáblája](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - Minden erőforráscsoport-összetevő helyőrzőkulcs-/értékpárral rendelkezik az adott erőforráscsoport-összetevő **név** és **hely** dinamikus beállításához.
  - Ha egy erőforráscsoport-paraméter nincs megadva, és nincs **defaultValue érték,** az erőforráscsoport paraméter nem választható.
- **AssignmentFile** (nem kötelező)
  - Tervhozzárendelés JSON-fájlábrázolásának elérési útja
  - Ez a paraméter egy PowerShell-paraméterkészlet része, amely csak **a Name**, **Blueprint**és **SubscriptionId**, valamint a közös paramétereket tartalmazza.

### <a name="example-1-provide-parameters"></a>1. példa: Paraméterek megadása

A következő példa a "my-blueprint" blueprint definition beolvasásának "1.1" verziójának új `Get-AzBlueprint`hozzárendelését hozza létre, a felügyelt identitás- és hozzárendelési objektum helyét "westus2"-re állítja, zárolja az erőforrásokat az _AllResourcesReadOnly_szolgáltatással, és beállítja a **paraméter** és a **ResourceGroupParameter** kivonattábláit az adott `{subId}`előfizetésen:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

A példa kimenete egy tervezet hozzárendelés létrehozásához így néz ki:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>2. példa: JSON-hozzárendelés-definíciós fájl használata

A következő példa közel ugyanazt a hozzárendelést hozza létre, mint az [1.](#example-1-provide-parameters)
Ahelyett, hogy paramétereket adna át a parancsmagnak, a példa egy JSON-hozzárendelés-definíciós fájl és az **AssignmentFile** paraméter használatát mutatja. Ezenkívül a **kizártPrincipals** tulajdonság **a zárolások**részeként van konfigurálva. Nincs PowerShell-paraméter a **kizárt principalokhoz,** és a tulajdonság csak a JSON-hozzárendelés-definíciós fájlon keresztül állítható be.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

Egy példa a JSON-hozzárendelés-definíciós fájl egy felhasználó által hozzárendelt felügyelt identitás, lásd a kérelem törzse [a példa: Hozzárendelés a felhasználó által hozzárendelt felügyelt identitás](/rest/api/blueprints/assignments/createorupdate#examples) rest API-t.

## <a name="update-blueprint-assignments"></a>Tervhozzárendelések frissítése

Néha szükség van egy már létrehozott tervezethozzárendelés frissítésére. A `Set-AzBlueprintAssignment` parancsmag kezeli ezt a műveletet. A parancsmag a `New-AzBlueprintAssignment` parancsmag legtöbb paraméterét veszi igénybe, lehetővé téve a hozzárendelésen beállított minden frissítését. A kivételek a _név_, _blueprint_és _subscriptionid_. Csak a megadott értékek frissülnek.

Ha tudni szeretné, hogy mi történik a hozzárendelések frissítésekor, olvassa el [a hozzárendelések frissítésének szabályait.](./update-existing-assignments.md#rules-for-updating-assignments)

- **Név** [kötelező]
  - Megadja a frissítendő tervrajz-hozzárendelés nevét.
  - A frissíthető hozzárendelés megkeresésére szolgál, nem pedig a hozzárendelés módosítására
- **Tervrajz** [kötelező]
  - Megadja a tervezet hozzárendelésének tervrajz-definícióját.
  - A `Get-AzBlueprint` referenciaobjektum lefoglalásának használata
  - A frissíthető hozzárendelés megkeresésére szolgál, nem pedig a hozzárendelés módosítására
- **Hely** (nem kötelező)
  - Megadja a rendszeráltal hozzárendelt felügyelt identitás és előfizetés-telepítési objektum régióját, amelyben létre kell hozni
- **Előfizetés** (nem kötelező)
  - Megadja azt az előfizetést, amelyhez a hozzárendelés telepítve van
  - Ha nincs megadva, az aktuális előfizetési környezet alapértelmezései
  - A frissíthető hozzárendelés megkeresésére szolgál, nem pedig a hozzárendelés módosítására
- **Zárolás** (nem kötelező)
  - Az üzembe helyezett erőforrásokhoz használandó [tervezeterőforrás-zárolást](../concepts/resource-locking.md) határozza meg.
  - Támogatott beállítások: _Nincs_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (nem kötelező)
  - Bejelölésével rendszeráltal hozzárendelt felügyelt identitást hozhat létre a hozzárendeléshez, és üzembe szeretné helyezni az erőforrásokat.
  - Az "identity" paraméterkészlet alapértelmezett beállítása
  - Nem használható a **UserAssignedIdentity-vel**
- **UserAssignedIdentity** (nem kötelező)
  - Megadja a hozzárendeléshez és az erőforrások üzembe helyezéséhez használandó, felhasználó által hozzárendelt felügyelt identitást.
  - Az "identity" paraméterkészlet része
  - Nem használható a **SystemAssignedIdentity-val**
- **Paraméter** (nem kötelező)
  - Kulcs-/értékpárok [kivonattáblázata](/powershell/module/microsoft.powershell.core/about/about_hash_tables) a [tervezet hozzárendelésének dinamikus paramétereinek](../concepts/parameters.md#dynamic-parameters) beállításához
  - A dinamikus paraméterek alapértelmezett értéke a definíció **alapértelmezettértéke**
  - Ha egy paraméter nincs megadva, és nincs **defaultValue értéke,** a paraméter nem választható.

    > [!NOTE]
    > **A paraméter** nem támogatja a secureStrings-t.

- **ResourceGroupParameter** (nem kötelező)
  - Erőforráscsoport-összetevők [kivonattáblája](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - Minden erőforráscsoport-összetevő helyőrzőkulcs-/értékpárral rendelkezik az adott erőforráscsoport-összetevő **név** és **hely** dinamikus beállításához.
  - Ha egy erőforráscsoport-paraméter nincs megadva, és nincs **defaultValue érték,** az erőforráscsoport paraméter nem választható.

A következő példa frissíti a zárolási mód módosításával `Get-AzBlueprint` a "saját tervrajz" tervdefiníció "1.1" verziójának hozzárendelését:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

A példa kimenete egy tervezet hozzárendelés létrehozásához így néz ki:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Tervrajzok hozzárendelésének eltávolítása

Amikor egy tervrajz-hozzárendelés eltávolításának ideje `Remove-AzBlueprintAssignment` van, a parancsmag kezeli ezt a műveletet. A parancsmag **a Name** vagy **az InputObject objektumot** veszi igénybe az eltávolítani kívánt tervezethozzárendelés meghatározásához. **A SubscriptionId** _szükséges,_ és minden esetben meg kell adni.

A következő példa lehív egy `Get-AzBlueprintAssignment` meglévő tervezet-hozzárendelést, majd `{subId}`eltávolítja azt az adott előfizetésből:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Példa a végpontok között

Az összes lépést egyesítve a következő példa leveszi a tervezet definícióját, majd létrehoz, `{subId}`frissít, és eltávolítja a tervezet-hozzárendelést az adott előfizetésben, amely a következőként jelenik meg:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>További lépések

- További információ a [tervterv életciklusáról.](../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../concepts/resource-locking.md)
- Az [általános hibaelhárítással](../troubleshoot/general.md)kapcsolatos tervtervezet ekhozzárendelése során fellépő problémák megoldása.