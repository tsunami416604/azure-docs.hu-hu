---
title: A PowerShell-lel hozzárendelések kezelése
description: Útmutató a PowerShell-modullal a hivatalos Azure tervek, Az.Blueprint tervezet-hozzárendelések kezeléséhez.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d8eacffe4b792eda5d81051f6aa65caa3292c896
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785690"
---
# <a name="how-to-manage-assignments-with-powershell"></a>A PowerShell-lel hozzárendelések kezelése

A tervezet-hozzárendelést segítségével felügyelhetők a **Az.Blueprint** Azure PowerShell-modul. A modul támogatja a beolvasása, létrehozása, frissítése és -hozzárendelés eltávolítása. A modul is beolvasni a részleteket a meglévő tervezetdefiníciók. Ez a cikk bemutatja, hogyan telepíti a modult, és használatának megkezdéséhez.

## <a name="add-the-azblueprint-module"></a>A Az.Blueprint modul hozzáadása

Ahhoz, hogy a tervezet-hozzárendelések kezeléséhez az Azure PowerShell-lel, hozzá kell adni a modult. Ez a modul használható a helyileg telepített PowerShell [Azure Cloud Shell](https://shell.azure.com), vagy a [Azure PowerShell Docker-rendszerkép](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Alapszintű követelmények

Az Azure-tervek modul a következő szoftverek szükségesek:

- Az Azure PowerShell 1.5.0 vagy újabb verziója. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1-es vagy újabb verzió. Ha nincs telepítve vagy frissítve, kövesse [ezeket az utasításokat](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>A modul telepítése

A tervrajzok a modul PowerShell **Az.Blueprint**.

1. Az egy **felügyeleti** PowerShell-parancssorban futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Ha **Az.Accounts** van telepítve, szükség lehet használni `-AllowClobber` telepítésének kényszerítéséhez.

1. Ellenőrizze, hogy a modul importálása, és a megfelelő verziójú (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Tervezetdefiníciók beolvasása

Az első lépés, de az hozzárendelés gyakran van egy tervezetdefiníció-hivatkozás beszerzése.
A `Get-AzBlueprint` parancsmag lekérdezi egy vagy több tervezetdefiníciók. A parancsmag a felügyeleti csoport beszerezheti a tervezetdefiníciók `-ManagementGroupId {mgId}` vagy az előfizetés `-SubscriptionId {subId}`. A **neve** paraméter lekérdezi a tervezetdefiníciót, azonban együtt kell használni **ManagementGroupId** vagy **SubscriptionId**. **Verzió** használható **neve** kell egyértelműbben melyik tervezetdefiníció adja vissza. Helyett **verzió**, a kapcsoló `-LatestPublished` Markoló a legutóbb közzétett verziót.

Az alábbi példában `Get-AzBlueprint` beolvasni egy nevű tervezetdefiníció összes verzióját a(z) 101-tervek-definition-előfizetés "az egy adott előfizetés-kiszolgálókként `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

A példa a kimenetre többféle verzióját tartalmazó tervrajz-definíció a következőhöz hasonló:

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

A [paraméterek tervezetet](../concepts/parameters.md#blueprint-parameters) a tervezet a definíció bővíthetők ki további információval.

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

## <a name="get-blueprint-assignments"></a>Tervezet-hozzárendelések beolvasása

Ha a tervezet-hozzárendelés már létezik, beszerezheti azt egy hivatkozást a `Get-AzBlueprintAssignment` parancsmagot. A parancsmagnál **SubscriptionId** és **neve** nem kötelező paraméterek. Ha **SubscriptionId** nincs megadva, az aktuális előfizetési környezetet használja.

Az alábbi példában `Get-AzBlueprintAssignment` beolvasni egy egyetlen tervezet-hozzárendelést egy adott előfizetés-kiszolgálókként "Hozzárendelés-lock-resource-groups" nevű `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

A példa a kimenetre a tervezet-hozzárendelés a következőhöz hasonló:

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

## <a name="create-blueprint-assignments"></a>Tervezet-hozzárendelés létrehozása

Ha a tervezet-hozzárendelést még nem létezik, létrehozhatja a a `New-AzBlueprintAssignment` parancsmagot. Ez a parancsmag paraméterei a következők:

- **Név** [kötelező]
  - A tervezet-hozzárendelés neve
  - Kell egyedinek lennie, és már nem létezik a **előfizetés-azonosító**
- **Tervrajz** [kötelező]
  - Adja meg a tervezetdefiníció hozzárendelése
  - Használat `Get-AzBlueprint` a referencia-objektum
- **Hely** [kötelező]
  - Adja meg a régiót, a rendszer által hozzárendelt felügyelt identitás- és előfizetés központi telepítési objektum kell létrehozni
- **Előfizetés** (nem kötelező)
  - Megadja azt az előfizetést, a hozzárendelés van telepítve
  - Ha nincs megadva, az alapértelmezett az aktuális előfizetési környezetet
- **Zárolási** (nem kötelező)
  - Meghatározza a [tervezetet erőforrás zárolása](../concepts/resource-locking.md) használandó üzembe helyezett erőforrások
  - Támogatott beállítások: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Ha nincs megadva, az alapértelmezett _None_
- **SystemAssignedIdentity** (optional)
  - Válassza ki a rendszer által hozzárendelt felügyelt identitás a hozzárendelés létrehozásához, és az erőforrások üzembe helyezése
  - A "identity" paraméter a készlet alapértelmezett
  - Nem használható **UserAssignedIdentity**
- **UserAssignedIdentity** (nem kötelező)
  - Adja meg a felhasználó által hozzárendelt felügyelt identitás használata a hozzárendeléshez és az erőforrások üzembe helyezése
  - Része az "identity" paraméter beállítása
  - Nem használható **SystemAssignedIdentity**
- **A paraméter** (nem kötelező)
  - A [kivonattábla](/powershell/module/microsoft.powershell.core/about/about_hash_tables) kulcs/érték párok beállítás [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters) a tervezet-hozzárendelés
  - A dinamikus paraméter alapértelmezett értéke a **defaultValue** definíciójában
  - Ha a paraméter nincs megadva, és nem rendelkezik **defaultValue**, a paraméter nem kötelező

    > [!NOTE]
    > **A paraméter** secureStrings nem támogatja.

- **ResourceGroupParameter** (nem kötelező)
  - A [kivonattábla](/powershell/module/microsoft.powershell.core/about/about_hash_tables) az erőforrás-összetevők csoport
  - Minden egyes erőforrás-csoport összetevő helyőrző lesz a kulcs/érték párok dinamikusan állítja **neve** és/vagy **hely** az adott erőforrás-csoport összetevő
  - Ha egy erőforráscsoport-paramétere nincs megadva, és nem rendelkezik **defaultValue**, az erőforráscsoport-paramétere nem választható

A következő példában létrehozunk egy új hozzárendelést a "saját tervezet" tervezetdefiníció a mavenből "1.1" verziójának `Get-AzBlueprint`, felügyelt identitás- és hozzárendelési objektum helye 'westus2' állítja, az erőforrásokat zárolja  _AllResourcesReadOnly_, és beállítja a kivonattáblák mindkét **paraméter** és **ResourceGroupParameter** a megadott előfizetés-kiszolgálókként `{subId}`:

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

A példa a kimenetre a tervezet-hozzárendelés létrehozása a következőhöz hasonló:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>Tervezet-hozzárendelések frissítése

Néha érdekében fontos, hogy már létre van hozva a tervezet-hozzárendelés frissítése. A `Set-AzBlueprintAssignment` parancsmag kezeli ezt a műveletet. A parancsmag ugyanazokat a paramétereket a legtöbb vesz igénybe, amely a `New-AzBlueprintAssignment` parancsmag használható, amely lehetővé teszi, amelyeket frissíteni kell a hozzárendelés lett beállítva. Ennek a következők a kivételeket a _neve_, _tervezet_, és _SubscriptionId_. Csak a megadott értékek frissülnek.

Mi történik, ha a tervezet-hozzárendelés frissítése ismertetése: [hozzárendeléseinek frissítése folyamatban van a szabályok](./update-existing-assignments.md#rules-for-updating-assignments).

- **Név** [kötelező]
  - A frissíteni tervezet-hozzárendelés neve
  - Keresse meg a hozzárendelés frissíteni, és nem az megváltoztatására használt
- **Tervrajz** [kötelező]
  - Adja meg a tervezet-hozzárendelés a tervezetdefiníciót
  - Használat `Get-AzBlueprint` a referencia-objektum
  - Keresse meg a hozzárendelés frissíteni, és nem az megváltoztatására használt
- **Hely** (nem kötelező)
  - Adja meg a régiót, a rendszer által hozzárendelt felügyelt identitás- és előfizetés központi telepítési objektum kell létrehozni
- **Előfizetés** (nem kötelező)
  - Megadja azt az előfizetést, a hozzárendelés van telepítve
  - Ha nincs megadva, az alapértelmezett az aktuális előfizetési környezetet
  - Keresse meg a hozzárendelés frissíteni, és nem az megváltoztatására használt
- **Zárolási** (nem kötelező)
  - Meghatározza a [tervezetet erőforrás zárolása](../concepts/resource-locking.md) használandó üzembe helyezett erőforrások
  - Támogatott beállítások: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (optional)
  - Válassza ki a rendszer által hozzárendelt felügyelt identitás a hozzárendelés létrehozásához, és az erőforrások üzembe helyezése
  - A "identity" paraméter a készlet alapértelmezett
  - Nem használható **UserAssignedIdentity**
- **UserAssignedIdentity** (nem kötelező)
  - Adja meg a felhasználó által hozzárendelt felügyelt identitás használata a hozzárendeléshez és az erőforrások üzembe helyezése
  - Része az "identity" paraméter beállítása
  - Nem használható **SystemAssignedIdentity**
- **A paraméter** (nem kötelező)
  - A [kivonattábla](/powershell/module/microsoft.powershell.core/about/about_hash_tables) kulcs/érték párok beállítás [dinamikus paraméterek](../concepts/parameters.md#dynamic-parameters) a tervezet-hozzárendelés
  - A dinamikus paraméter alapértelmezett értéke a **defaultValue** definíciójában
  - Ha a paraméter nincs megadva, és nem rendelkezik **defaultValue**, a paraméter nem kötelező

    > [!NOTE]
    > **A paraméter** secureStrings nem támogatja.

- **ResourceGroupParameter** (nem kötelező)
  - A [kivonattábla](/powershell/module/microsoft.powershell.core/about/about_hash_tables) az erőforrás-összetevők csoport
  - Minden egyes erőforrás-csoport összetevő helyőrző lesz a kulcs/érték párok dinamikusan állítja **neve** és/vagy **hely** az adott erőforrás-csoport összetevő
  - Ha egy erőforráscsoport-paramétere nincs megadva, és nem rendelkezik **defaultValue**, az erőforráscsoport-paramétere nem választható

Az alábbi példa frissíti a hozzárendelés a "saját tervezet" tervezetdefiníció a mavenből "1.1" verziójának `Get-AzBlueprint` úgy módosítja a zárolási mód:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

A példa a kimenetre a tervezet-hozzárendelés létrehozása a következőhöz hasonló:

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

## <a name="remove-blueprint-assignments"></a>Tervezet-hozzárendelések eltávolítása

Amikor egy tervezet-hozzárendelés el kell távolítani, a `Remove-AzBlueprintAssignment` parancsmag kezeli ezt a műveletet. A parancsmag lép vagy **neve** vagy **InputObject** határozza meg, melyik tervezetet hozzárendelés eltávolításához. **SubscriptionId** van _szükséges_ és minden esetben meg kell adni.

Az alábbi példa lekéri a meglévő tervezet-hozzárendelést `Get-AzBlueprintAssignment` , és eltávolítja a megadott előfizetés-kiszolgálókként `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Teljes körű példakód

Összevonhatja a lépéseket, az alábbi példa lekérdezi a tervezetdefiníciót, majd hoz létre, frissít és eltávolítja a tervezet-hozzárendelést a megadott előfizetés-kiszolgálókként `{subId}`:

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
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

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).