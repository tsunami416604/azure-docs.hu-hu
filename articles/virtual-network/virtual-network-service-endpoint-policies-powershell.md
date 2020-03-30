---
title: Adatok kiszivárgásának korlátozása az Azure Storage-ra – Azure PowerShell
description: Ebben a cikkben megtudhatja, hogyan korlátozhatja és korlátozhatja a virtuális hálózati adatok kiszivárgását az Azure Storage-erőforrásokra az Azure PowerShell használatával a virtuális hálózati szolgáltatás végpontszabályzataival.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 673431e2ddfc9a641bb1c640891daac79350cb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78253025"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Az Azure Storage-fiókokba történő adatkiszivárgás kezelése virtuális hálózati szolgáltatásvégpont-szabályzatokkal az Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A virtuális hálózati szolgáltatás végpontszabályzatai lehetővé teszik, hogy az Azure Storage-fiókok hozzáférés-vezérlését a szolgáltatásvégpontokon belül egy virtuális hálózaton keresztül alkalmazza. Ez a számítási feladatok védelmének kulcsa, a tárfiókok engedélyezettek kezelésének és az adatok kiszivárgásának helye.
Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Hozzon létre egy virtuális hálózatot.
* Adjon hozzá egy alhálózatot, és engedélyezze a szolgáltatásvégpontot az Azure Storage számára.
* Hozzon létre két Azure Storage-fiókot, és engedélyezze a hálózati hozzáférést a fent létrehozott alhálózatból.
* Hozzon létre egy szolgáltatásvégpont-szabályzatot, amely csak az egyik tárfiókhoz engedélyezi a hozzáférést.
* Virtuális gép (VM) üzembe helyezése az alhálózatra.
* Hozzáférés megerősítése az engedélyezett tárfiókhoz az alhálózatból.
* Ellenőrizze, hogy a hozzáférés megtagadva a nem engedélyezett tárfiókhoz az alhálózatból.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez a cikk az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózathoz és a cikkben létrehozott összes többi erőforráshoz. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)segítségével. A következő példa létrehoz egy *myResourceGroup*nevű erőforráscsoportot: 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példa létrehoz egy *myVirtualNetwork* nevű virtuális hálózatot a *10.0.0.0/16*címelőtaggal.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése

Hozzon létre egy alhálózatot a virtuális hálózatban. Ebben a példában egy *Privát* nevű alhálózat jön létre a Microsoft szolgáltatásvégpontjával.Storage: *Microsoft.Storage* 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>Az alhálózat hálózati hozzáférésének korlátozása

Hozzon létre hálózati biztonsági csoport biztonsági szabályokat [a New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)segítségével. A következő szabály lehetővé teszi a kimenő hozzáférést az Azure Storage-szolgáltatáshoz rendelt nyilvános IP-címekhez: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

A következő szabály megtagadja a hozzáférést az összes nyilvános IP-címhez. Az előző szabály felülbírálja ezt a szabályt, a magasabb prioritás miatt, amely lehetővé teszi a hozzáférést az Azure Storage nyilvános IP-címeihez.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

A következő szabály lehetővé teszi, hogy a Távoli asztali protokoll (RDP) forgalma bárhonnan befelé irányuló az alhálózatba. A távoli asztali kapcsolatok engedélyezettek az alhálózathoz, így egy későbbi lépésben ellenőrizheti az erőforráshoz való hálózati hozzáférést.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup csoporttal.](/powershell/module/az.network/new-aznetworksecuritygroup) A következő példa létrehoz egy *myNsgPrivate*nevű hálózati biztonsági csoportot.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

Társítsa a hálózati biztonsági csoportot a *Privát* alhálózathoz a [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) segítségével, majd írja az alhálózati konfigurációt a virtuális hálózatba. A következő példa a *myNsgPrivate* hálózati biztonsági csoportot társítja a *privát* alhálózathoz:

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Az Azure Storage-fiókok hálózati hozzáférése

A szolgáltatásvégpontok használatára képes Azure-szolgáltatásokkal létrehozott erőforrásokhoz való hálózati hozzáférés korlátozásának lépései szolgáltatásonként eltérőek. Az egyes szolgáltatásokhoz szükséges lépéseket az adott szolgáltatások dokumentációja tartalmazza. Ez a cikk további lépések az Azure Storage-fiók hálózati hozzáférésének korlátozására, példaként tartalmazza.

### <a name="create-two-storage-accounts"></a>Két tárfiók létrehozása

Hozzon létre egy Azure-tárfiókot a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)segítségével.

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

A tárfiók létrehozása után olvassa be a tárfiók kulcsát egy változóba a [Get-AzStorageAccountKey segítségével:](/powershell/module/az.storage/get-azstorageaccountkey)

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

A kulcs segítségével fájlmegosztást hozhat létre egy későbbi lépésben. Adja `$storageAcctKey` meg és jegyezze fel az értéket, mivel manuálisan is meg kell adnia egy későbbi lépésben, amikor a fájlmegosztást egy virtuális gép meghajtójára rendeli hozzá.

Most ismételje meg a fenti lépéseket egy második tárfiók létrehozásához.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

A tárfiók kulcsának lekérése ebből a fiókból a fájlmegosztás későbbi létrehozásához.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>Fájlmegosztás létrehozása az egyes tárfiókban

Hozzon létre egy környezetet a tárfiókhoz és a kulcshoz a [New-AzStorageContext segítségével.](/powershell/module/az.storage/new-AzStoragecontext) A környezet beágyazi a tárfiók nevét és a fiókkulcsot:

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

Fájlmegosztás létrehozása a [New-AzStorageShare szolgáltatással:](/powershell/module/az.storage/new-azstorageshare)

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>Tárfiókok hoz való összes hálózati hozzáférés megtagadása

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező hálózati kapcsolatokat elfogadnak. A kijelölt hálózatokhoz való hozzáférés korlátozásához módosítsa az alapértelmezett műveletet *Megtagadás* az [Update-AzStorageAccountNetworkRuleSet értékre.](/powershell/module/az.storage/update-azstorageaccountnetworkruleset) Ha a hálózati hozzáférés le van tiltva, a tárfiók egyetlen hálózatról sem érhető el.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>Hálózati hozzáférés engedélyezése csak a virtuális hálózat alhálózatáról

A létrehozott virtuális hálózat beolvasása a [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) segítségével, majd a magánalhálózati objektum beolvasása egy változóba a [Get-AzVirtualNetworkSubnetConfig segítségével:](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

Az [Add-AzStorageAccountNetworkRule szolgáltatással](/powershell/module/az.network/add-aznetworksecurityruleconfig)hálózati hozzáférést engedélyezhet a tárfiókhoz a *Magánhálózati* alhálózatról.

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Házirend alkalmazása az érvényes tárfiókhoz való hozzáférés engedélyezéséhez

Győződjön meg arról, hogy a virtuális hálózat felhasználói csak az Azure Storage-fiókok biztonságos és engedélyezett, létrehozhat egy szolgáltatás végpontszabályzat az engedélyezett tárfiókok listáját a definícióban. Ez a házirend ezután a virtuális hálózati alhálózatra vonatkozik, amely szolgáltatásvégpontokon keresztül csatlakozik a tárolóhoz.

### <a name="create-a-service-endpoint-policy"></a>Szolgáltatásvégpont-házirend létrehozása

Ez a szakasz létrehozza a házirend-definíciót a szolgáltatásvégponton keresztüli hozzáféréshez engedélyezett erőforrások listájával

Az első (engedélyezett) tárfiók erőforrás-azonosítójának lekérése 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

A házirend-definíció létrehozása a fenti erőforrás engedélyezéséhez

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

A szolgáltatásvégpont-házirend létrehozása a fent létrehozott házirend-definícióval

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>A szolgáltatásvégpont-házirend társítása a virtuális hálózat alhálózatához

A szolgáltatásvégpont-szabályzat létrehozása után társítja azt a cél alhálózathoz az Azure Storage szolgáltatásvégpont-konfigurációjával.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Hozzáférés-korlátozás ellenőrzése az Azure Storage-fiókokhoz

### <a name="deploy-the-virtual-machine"></a>A virtuális gép üzembe helyezése

A hálózati hozzáférés tesztelése egy tárfiókhoz, telepítsen egy virtuális gép az alhálózatban.

Hozzon létre egy virtuális gépet a *Privát* alhálózatban a [New-AzVM](/powershell/module/az.compute/new-azvm)segítségével. A következő parancs futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszót. Az `-AsJob` kapcsoló a háttérben hozza létre a virtuális gépet, így Ön eközben folytathatja a következő lépéssel.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

A következő példakimenethez hasonló kimenet et adja vissza a rendszer:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>Hozzáférés megerősítése az *engedélyezett* tárfiókhoz

A [Get-AzPublicIpAddress használatával](/powershell/module/az.network/get-azpublicipaddress) adja vissza a virtuális gép nyilvános IP-címét. A következő példa a *myVmPrivate* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A következő parancsban cserélje le a `<publicIpAddress>` értékét az előző parancs által visszaadott nyilvános IP-címre, majd írja be a következő parancsot:

```powershell
mstsc /v:<publicIpAddress>
```

A rendszer létrehoz és letölt a számítógépre egy Remote Desktop Protocol (.rdp) fájlt. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. Válassza **az OK gombot.** A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.

A *myVmPrivate* virtuális gép, az Azure-fájlmegosztás leképezése az engedélyezett tárfiók a PowerShell használatával Z meghajtóra. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

A PowerShell a következő példához hasonló kimenetet ad vissza:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Az Azure-fájlmegosztás sikeresen le lett képezve a Z meghajtóra.

Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>Annak ellenőrzése, hogy a hozzáférés *megtagadva* a nem engedélyezett tárfiókhoz

Ugyanazon a *myVmPrivate* virtuális gép, próbálja meg leképezni az Azure-fájlmegosztást az X meghajtóra. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

A megosztáshoz való hozzáférés megtagadva, és `New-PSDrive : Access is denied` hibaüzenet jelenik meg. A hozzáférés megtagadva, mert a tárfiók *notallowedaccount* nem szerepel az engedélyezett erőforrások listájában a szolgáltatás végpontházirend. 

Zárja be a távoli asztali munkamenetet a *myVmPublic* virtuális géppel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) segítségével eltávolíthatja az erőforráscsoportot és az összes benne lévő erőforrást:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy szolgáltatásvégpont-szabályzatot alkalmazott egy Azure virtuális hálózati szolgáltatás végponton keresztül az Azure Storage-ba. Ön hozta létre az Azure Storage-fiókok at, és korlátozott hálózati hozzáférést csak bizonyos tárfiókok (és így megtagadta mások) egy virtuális hálózati alhálózat. Ha többet szeretne tudni a szolgáltatásvégpont-szabályzatokról, olvassa el [a Szolgáltatásvégpontok házirendjei – áttekintés című témakört.](virtual-network-service-endpoint-policies-overview.md)
