---
title: Erőforrások áthelyezése régiók között a PowerShell használatával az Azure-erőforrás-mozgató szolgáltatásban
description: Ismerje meg, hogy miként helyezhetők át erőforrások a régiók között a PowerShell használatával az Azure-beli erőforrás-Mozgatóban.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 3236e0a95c6a4b4f57ac38ed067011c3d6848b5a
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670544"
---
# <a name="move-resources-across-regions-in-powershell"></a>Erőforrások áthelyezése a régiók között a PowerShellben

Ismerje meg, hogyan helyezhet át Azure-erőforrásokat egy másik régióba a PowerShell használatával az Azure-beli erőforrás-mozgató alkalmazásban. 

> [!NOTE]
> Az Azure-beli erőforrás-mozgató szolgáltatás jelenleg előzetes verzióban érhető el.



## <a name="before-you-start"></a>Előkészületek

- Az Azure-előfizetésnek hozzáféréssel kell rendelkeznie az erőforrás-Mozgatóhoz, és rendelkeznie kell [tulajdonosi](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) vagy [felhasználói hozzáférés-rendszergazdai](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) jogosultságokkal az előfizetéshez.
- Az erőforrás-mozgató nem tudja követni a módosításokat és a frissítéseket, ezért az erőforrások áthelyezése előtt végezze el a szükséges módosításokat.
- Ha az erőforrásokat a PowerShell-lel helyezi át, jelenleg nem szerkesztheti a célcsoportok beállításait. Módosítsa ezeket a beállításokat közvetlenül a portálon.
- Ha erőforrásokat ad hozzá egy áthelyezési gyűjteményhez, a rendszer egy másik régióba helyezi át őket, az áthelyezéssel kapcsolatos metaadatokat egy erre a célra létrehozott erőforráscsoport tárolja. Jelenleg ez az erőforráscsoport az USA 2. keleti régiójában vagy Észak-Európában található. Az Azure-erőforrások bármely nyilvános régió között áthelyezhetők az ezen régiók egyikében található metaadatok használatával.

## <a name="sample-values"></a>Mintavételezési értékek

Ezeket az értékeket használjuk a parancsfájl-példákban:

**Beállítás** | **Érték** 
--- | ---
Előfizetés azonosítója | előfizetés-azonosító
Erőforrás-mozgató szolgáltatás helye | USA 2. keleti régiója
Metaadat-erőforráscsoport | RegionMoveRG-CentralUS-westcentralus
Metaadat-erőforráscsoport helye | USA 2. keleti régiója
Gyűjtemény nevének áthelyezése | MoveCollection-CentralUS-westcentralus
Forrásoldali régió |  centralus
Forrás erőforráscsoport | PSDemoRM
Cél régió | USA nyugati középső régiója
Cél erőforráscsoport | PSDemoRMtgt
Áthelyezni kívánt virtuális gép | PSDemoVM

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-előfizetésbe.

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>Az erőforráscsoport létrehozása a metaadatokhoz

Hozzon létre egy erőforráscsoportot a gyűjtemény metaadatainak és konfigurációs adatainak tárolásához.

```azurepowershell-interactive
# Sign in to an Azure subscription
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**Várt kimenet**:

![Az erőforráscsoport létrehozása után kimeneti szöveg](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>Az erőforrás-szolgáltató regisztrálása

Regisztrálja a Microsoft. Migrál erőforrás-szolgáltatót, hogy létre lehessen hozni a MoveCollection-erőforrást.

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>Az áthelyezési gyűjtemény létrehozása

A MoveCollection objektum az áthelyezni kívánt erőforrásokra vonatkozó metaadatokat és konfigurációs adatokat tárolja.

- Ahhoz, hogy a MoveCollection objektum hozzáférjen ahhoz az előfizetéshez, amelyben az Azure-erőforrás-mozgató szolgáltatás található, szüksége van egy [rendszerhez rendelt felügyelt identitásra](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (korábbi nevén felügyelt szolgáltatás azonosítása (MSI)), amelyet az előfizetés megbízhatónak tekint.
- Az identitás hozzá van rendelve a Közreműködőhöz vagy a felhasználói hozzáférés rendszergazdai szerepköréhez a forrás-előfizetéshez.
- Ha szerepkört szeretne hozzárendelni az identitáshoz, akkor az alábbi engedélyekkel kell rendelkeznie az előfizetésben (például a tulajdonos vagy a felhasználói hozzáférés rendszergazdája).
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft. Authorization/roleAssignments/delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus " -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**Várt kimenet**:

![Az áthelyezési gyűjtemény létrehozása után kimeneti szöveg](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>Felügyelt identitás kiosztása 

Állítsa be az előfizetés-azonosítót, kérje le a MoveCollection objektum identitását, és rendelje hozzá az Azure-szerepköröket.


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -Name "MoveCollection-centralus-westcentralus "

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"

```

## <a name="add-resources-to-the-move-collection"></a>Erőforrások hozzáadása az áthelyezési gyűjteményhez

Kérje le az áthelyezni kívánt forrás-erőforrás AZONOSÍTÓját. Ezután adja hozzá az áthelyezési gyűjteményhez.

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**Várt kimenet**

![Kimeneti szöveg az erőforrás-azonosító lekérése után](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “ Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**Várt kimenet** 
 ![ Az erőforrás hozzáadása utáni kimeneti szöveg](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>Függőségek feloldása

Érvényesítse a hozzáadott erőforrásokat, és oldja meg a függőségeket más erőforrásokon.

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**Kimenet, ha függőségek vannak**

Ha az áthelyezési gyűjteményben lévő erőforrás függőségekkel rendelkezik más erőforrásokon, a rendszer hibás üzenetet ad ki.

![A függőségek ellenőrzése utáni kimeneti szöveg](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>Függőségek lekérése

Ha az áthelyezni kívánt erőforrás függőségekkel rendelkezik más erőforrásokon, lekérheti a hiányzó függőségekkel kapcsolatos információkat.

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**Várt kimenet**

![A függőségek beolvasása után kimeneti szöveg](./media/move-region-powershell/missing-dependencies.png)

Ebben a példában két függőséget azonosít a rendszer a hiányzó módon:

- Forrásoldali erőforráscsoport: PSDemoRM
- Hálózati adapter a virtuális gépen: PSDemoVM62

## <a name="add-dependencies-to-collection"></a>Függőségek hozzáadása a gyűjteményhez


A lekért erőforrás-azonosítók használatával azonosíthatja a hiányzó erőforrások nevét, és hozzáadhatja őket az áthelyezési gyűjteményhez.

### <a name="add-the-nic"></a>A hálózati adapter hozzáadása

Kérje le a hálózati adapter nevét és típusát, majd adja hozzá a gyűjteményhez.

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/ <subscription-id> /resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”

```

**Várt kimenet**

![Kimeneti szöveg a hálózati adapter beolvasása után](./media/move-region-powershell/output-retrieve-nic.png)

Most adja hozzá a hálózati adaptert az áthelyezési gyűjteményhez. Ez a példa ugyanazt a nevet adja a célként megadott hálózati adapternek. Tartsa meg a beállításokat és a Case konzisztens beállítást.

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**Várt kimenet**

![Kimeneti szöveg, miután hozzáadta a hálózati adaptert a gyűjteményhez](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>A forrás erőforráscsoport hozzáadása

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**Várt kimenet**

![Az erőforráscsoport a gyűjteményhez való hozzáadása után kimeneti szöveg](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>Függőségek ismételt ellenőrzését

A hiányzó függőségek esetében próbálkozzon újra.

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**Kimenet**

További Hiányzó függőségek vannak.

![A függőségek újbóli ellenőrzése utáni kimeneti szöveg](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>További függőségek beolvasása

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Várt kimenet**


![További függőségek beolvasása után kimeneti szöveg](./media/move-region-powershell/additional-missing-dependencies.png)

Ebben a példában három további függőséget azonosítottak a hiányzó módon:

- Nyilvános IP-cím: psdemovm-IP
- Azure-beli virtuális hálózat: psdemorm-vnet
- Hálózati biztonsági csoport (NSG): psdemovm-NSG

## <a name="add-additional-dependencies"></a>További függőségek hozzáadása

1. [Kövesse az utasításokat](#add-dependencies-to-collection) , és adja hozzá ezeket az erőforrásokat az áthelyezési gyűjteményhez.
2. Az erőforrások hozzáadása után ellenőrizze újra az összes függőség hozzáadását.


## <a name="prepare-and-move-the-source-resource-group"></a>A forrásoldali erőforráscsoport előkészítése és áthelyezése

A forrás régióban a mozgatás előtt előkészíti az erőforrásokat. Az előkészítési folyamat az áthelyezett erőforrásoktól függ. Előfordulhat például, hogy az állapot nélküli erőforrások előkészítése előkészíti és exportál egy Azure Resource Manager (ARM) sablont. Az állapot-nyilvántartó erőforrások esetében előfordulhat, hogy a replikáció leáll. 

A forrás erőforrásainak előkészítése előtt elő kell készítenie és át kell helyeznie a forrás erőforráscsoportot.

### <a name="prepare"></a>Előkészítés

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”
```

**Várt kimenet**

![Kimeneti szöveg a forrás erőforráscsoport előkészítése után](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>Áthelyezés kezdeményezése

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”

**Expected output**

![Output text after initiating move of the source resource group](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**Várt kimenet**

![Kimeneti szöveg a forrásoldali erőforráscsoport véglegesítése után](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>Erőforrások előkészítése

Miután a forrás erőforráscsoportot áthelyezte a célhelyre, kérje le a gyűjteményben lévő erőforrások listáját, és készüljön fel a mozgatásra.

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “ <subscription-id> “ -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**Várt kimenet**

![Kimeneti szöveg a gyűjtemény erőforrásainak beolvasása után](./media/move-region-powershell/collection-resources.png)

Most készítse elő az erőforrásokat.

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
Azt is megteheti, hogy az erőforrás-azonosító használatával előkészíti és áthelyezi az erőforrásokat a név helyett:

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**Várt kimenet**

![Kimeneti szöveg a gyűjtemény erőforrásainak előkészítése után](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>Erőforrás-áthelyezés kezdeményezése

Az erőforrások előkészítése után indítsa el az áthelyezést.

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Várt kimenet** 
 ![ Kimeneti szöveg az erőforrás-áthelyezés kezdeményezése után](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>Áthelyezés elvetése vagy véglegesítés végrehajtása

A kezdeti áthelyezés után eldöntheti, hogy véglegesíteni kívánja-e az áthelyezést, vagy elveti azt. 

- **Elvetés**: Ha teszteli a tesztet, elkerülheti az áthelyezést, és nem szeretné ténylegesen áthelyezni a forrás erőforrást. Az áthelyezés elvetése visszaadja az erőforrást a *függőben lévő áthelyezés indításának*állapotára. Ezután kezdeményezheti az áthelyezést, ha szükséges.
- **Véglegesítés**: a véglegesítés befejezi az áthelyezést a célként megadott régióba. A véglegesítést követően a forrás erőforrás a *delete Source függőben*állapotba kerül, és eldöntheti, hogy szeretné-e törölni.

### <a name="discard"></a>Elvetés

Az áthelyezés elvetéséhez:

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Várt kimenet** 
 ![ Kimeneti szöveg az áthelyezés elvetése után](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Véglegesítés

Az áthelyezés véglegesíte:

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Várt kimenet**

![Kimeneti szöveg az áthelyezés véglegesítése után](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>Forrás erőforrásainak törlése

Miután elvégezte az áthelyezést, és ellenőrzi, hogy az erőforrások a megcélzott régióban megfelelően működnek-e, a [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources)a PowerShell vagy az [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources) [használatával](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)törölheti a forrás erőforrásait.

## <a name="next-steps"></a>Következő lépések

[Ismerje meg, hogyan](remove-move-resources.md) távolíthat el erőforrásokat egy áthelyezési gyűjteményből.