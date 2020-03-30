---
title: A PaaS-erőforrások hálózati hozzáférésének korlátozása – Azure PowerShell
description: Ebben a cikkben megtudhatja, hogyan korlátozhatja és korlátozhatja a hálózati hozzáférést az Azure-erőforrásokhoz, például az Azure Storage-hoz és az Azure SQL Database-hez az Azure PowerShell használatával a virtuális hálózati szolgáltatás végpontjaival.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1d0cf65bb39dbda2b7451c50629ff8949c5507cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185542"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>A PaaS-erőforrások hálózati hozzáférésének korlátozása virtuális hálózati szolgáltatás-végpontokkal a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Virtuális hálózati szolgáltatásvégpontokkal egy adott virtuális hálózati alhálózatra korlátozható az egyes Azure-szolgáltatási erőforrásokhoz való hálózati hozzáférés. Emellett teljesen le is tiltható az internetes hozzáférés az erőforrásokhoz. A szolgáltatásvégpontok közvetlen csatlakozást biztosítanak a virtuális hálózat és a támogatott Azure-szolgáltatások között, így lehetővé teszik a virtuális hálózat magáncímterének használatát az Azure-szolgáltatások eléréséhez. A szolgáltatásvégpontokon keresztül az Azure-erőforrások felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán marad. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Virtuális hálózat létrehozása egyetlen alhálózattal
* Alhálózat hozzáadása és szolgáltatásvégpont engedélyezése
* Azure-erőforrás létrehozása és hálózati hozzáférés engedélyezése az erőforráshoz egyetlen alhálózatról
* Virtuális gép (VM) üzembe helyezése az egyes alhálózatokon
* Erőforráshoz való alhálózati hozzáférés ellenőrzése
* Erőforráshoz való alhálózati és internetes hozzáférés letiltásának ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a PowerShellt, ez a cikk az Azure PowerShell-modul 1.0.0-s vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Virtuális hálózat létrehozása előtt létre kell hoznia egy erőforráscsoportot a virtuális hálózathoz és a cikkben létrehozott összes többi erőforráshoz. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)segítségével. A következő példa létrehoz egy *myResourceGroup*nevű erőforráscsoportot: 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). A következő példa létrehoz egy *myVirtualNetwork* nevű virtuális hálózatot a *10.0.0.0/16*címelőtaggal.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Hozzon létre egy alhálózati konfigurációt a [New-AzVirtualSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)segítségével. A következő példa egy *Nyilvános*nevű alhálózat alhálózati konfigurációját hozza létre:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Hozza létre az alhálózatot a virtuális hálózatban úgy, hogy a [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)segítségével beírja az alhálózati konfigurációt a virtuális hálózatba:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése

A szolgáltatásvégpontok csak a szolgáltatás végpontokat támogató szolgáltatások engedélyezheti. A [Get-AzVirtualNetworkAvailableEndpointService szolgáltatással az Azure-ban](/powershell/module/az.network/get-azvirtualnetworkavailableendpointservice)elérhető, végpontbarát szolgáltatások megtekintése. A következő példa az *eastus* régióban elérhető szolgáltatás-végpont-kompatibilis szolgáltatások listáját adja vissza. A visszaadott szolgáltatások listája idővel növekedni fog, ahogy egyre több Azure-szolgáltatás válik szolgáltatásvégpont-engedélyezve.

```azurepowershell-interactive
Get-AzVirtualNetworkAvailableEndpointService -Location eastus | Select Name
```

Hozzon létre egy további alhálózatot a virtuális hálózatban. Ebben a példában egy *Privát* nevű alhálózat jön létre a Microsoft szolgáltatásvégpontjával.Storage: *Microsoft.Storage* 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Alhálózat hálózati hozzáférésének korlátozása

Hozzon létre hálózati biztonsági csoport biztonsági szabályokat [a New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)segítségével. A következő szabály lehetővé teszi a kimenő hozzáférést az Azure Storage-szolgáltatáshoz rendelt nyilvános IP-címekhez: 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
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
  -Priority 110 `
  -Protocol * `
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
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Erőforráshoz való hálózati hozzáférés korlátozása

A szolgáltatásvégpontok használatára képes Azure-szolgáltatásokkal létrehozott erőforrásokhoz való hálózati hozzáférés korlátozásának lépései szolgáltatásonként eltérőek. Az egyes szolgáltatásokhoz szükséges lépéseket az adott szolgáltatások dokumentációja tartalmazza. Ez a cikk további lépések az Azure Storage-fiók hálózati hozzáférésének korlátozására, példaként tartalmazza.

### <a name="create-a-storage-account"></a>Create a storage account

Hozzon létre egy Azure-tárfiókot a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)segítségével. Cserélje `<replace-with-your-unique-storage-account-name>` le egy olyan névre, amely az összes Azure-helyen egyedi, 3–24 karakter hosszú, csak számokat és kisbetűket használva.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

A tárfiók létrehozása után olvassa be a tárfiók kulcsát egy változóba a [Get-AzStorageAccountKey segítségével:](/powershell/module/az.storage/get-azstorageaccountkey)

```azurepowershell-interactive
$storageAcctKey = (Get-AzStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

A kulcs segítségével fájlmegosztást hozhat létre egy későbbi lépésben. Adja `$storageAcctKey` meg és jegyezze fel az értéket, mivel manuálisan is meg kell adnia egy későbbi lépésben, amikor a fájlmegosztást egy virtuális gép meghajtójára rendeli hozzá.

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban

Hozzon létre egy környezetet a tárfiókhoz és a kulcshoz a [New-AzStorageContext segítségével.](/powershell/module/az.storage/new-AzStoragecontext) A környezet beágyazi a tárfiók nevét és a fiókkulcsot:

```azurepowershell-interactive
$storageContext = New-AzStorageContext $storageAcctName $storageAcctKey
```

Fájlmegosztás létrehozása a [New-AzStorageShare szolgáltatással:](/powershell/module/az.storage/new-azstorageshare)

$share = New-AzStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Tárfiók hoz való összes hálózati hozzáférés megtagadása

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező hálózati kapcsolatokat elfogadnak. A kijelölt hálózatokhoz való hozzáférés korlátozásához módosítsa az alapértelmezett műveletet *Megtagadás* az [Update-AzStorageAccountNetworkRuleSet értékre.](/powershell/module/az.storage/update-azstorageaccountnetworkruleset) Ha a hálózati hozzáférés le van tiltva, a tárfiók egyetlen hálózatról sem érhető el.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Hálózati hozzáférés engedélyezése alhálózatról

A létrehozott virtuális hálózat beolvasása a [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) segítségével, majd a magánalhálózati objektum beolvasása egy változóba a [Get-AzVirtualNetworkSubnetConfig segítségével:](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzVirtualNetworkSubnetConfig `
  -Name "Private"
```

Az [Add-AzStorageAccountNetworkRule szolgáltatással](/powershell/module/az.network/add-aznetworksecurityruleconfig)hálózati hozzáférést engedélyezhet a tárfiókhoz a *Magánhálózati* alhálózatról.

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Tárfiókhoz való hálózati hozzáférés teszteléséhez helyezzen üzembe egy virtuális gépet minden alhálózaton.

### <a name="create-the-first-virtual-machine"></a>Az első virtuális gép létrehozása

Hozzon létre egy virtuális gépet a *Nyilvános* alhálózatban a [New-AzVM](/powershell/module/az.compute/new-azvm)segítségével. A következő parancs futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszót. Az `-AsJob` kapcsoló a háttérben hozza létre a virtuális gépet, így Ön eközben folytathatja a következő lépéssel.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

A következő példakimenethez hasonló kimenet et adja vissza a rendszer:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM     
```

### <a name="create-the-second-virtual-machine"></a>A második virtuális gép létrehozása

Hozzon létre egy virtuális gépet a *privát* alhálózatban:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Néhány percet vesz igénybe, amíg az Azure létrehozza a virtuális gép. Ne folytassa a következő lépéssel, amíg az Azure befejezi a virtuális gép létrehozását, és vissza nem adja a kimenetet a PowerShellnek.

## <a name="confirm-access-to-storage-account"></a>Tárfiókhoz való hozzáférés ellenőrzése

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

A *myVmPrivate* virtuális gépen a PowerShell-lel képezze le az Azure-fájlmegosztást a Z meghajtóra. A következő parancsok futtatása `<storage-account-key>` `<storage-account-name>` előtt cserélje le és cserélje ki a [Storage-fiók létrehozása](#create-a-storage-account)című területen megadott vagy beolvasott értékeket.

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

A PowerShell a következő példához hasonló kimenetet ad vissza:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Az Azure-fájlmegosztás sikeresen le lett képezve a Z meghajtóra.

Győződjön meg arról, hogy a virtuális gép nem rendelkezik kimenő kapcsolattal más nyilvános IP-címekhez:

```powershell
ping bing.com
```

Nem kap választ, mert a *Magánjellegű* alhálózathoz rendelt hálózati biztonsági csoport nem engedélyezi a kimenő hozzáférést olyan nyilvános IP-címekhez, amelyek nem az Azure Storage szolgáltatáshoz rendelt címek.

Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="confirm-access-is-denied-to-storage-account"></a>Tárfiókhoz való hozzáférés letiltásának ellenőrzése

A *myVmPublic* vm nyilvános IP-címének beszerezése:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A következő parancsban cserélje le a `<publicIpAddress>` értékét az előző parancs által visszaadott nyilvános IP-címre, majd írja be a következő parancsot: 

```powershell
mstsc /v:<publicIpAddress>
```

A *myVmPublic* virtuális gépen próbálja meg leképezni az Azure-fájlmegosztást a Z meghajtóra. A következő parancsok futtatása `<storage-account-key>` `<storage-account-name>` előtt cserélje le és cserélje ki a [Storage-fiók létrehozása](#create-a-storage-account)című területen megadott vagy beolvasott értékeket.

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

A megosztáshoz való hozzáférés megtagadva, és `New-PSDrive : Access is denied` hibaüzenet jelenik meg. A hozzáférést a rendszer megtagadja, mert a *myVmPublic* virtuális gép a *Nyilvános* alhálózaton van üzembe helyezve. A *Nyilvános* alhálózat nem rendelkezik az Azure Storage-hoz engedélyezett szolgáltatásvégponttal, és a tárfiók kizárólag a *Magánjellegű* alhálózatról engedélyezi a hozzáférést, a *Nyilvános* alhálózatról nem.

Zárja be a távoli asztali munkamenetet a *myVmPublic* virtuális géppel.

A következő paranccsal próbálja meg megtekinteni a tárfiók fájlmegosztásait a számítógépen:

```powershell-interactive
Get-AzStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

A hozzáférés megtagadva, és *get-AzStorageFile fájl jelenik meg: A távoli kiszolgáló a következő hibaüzenetet adta vissza: (403) Tiltott. HTTP-állapotkód: 403 – HTTP-hibaüzenet: Ez a kérés nem jogosult a művelet végrehajtására,* mert a számítógép nem a *MyVirtualNetwork* virtuális hálózat *privát* alhálózatában található.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) segítségével eltávolíthatja az erőforráscsoportot és az összes benne lévő erőforrást:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben engedélyezte a szolgáltatásvégpontot egy virtuális hálózati alhálózathoz. Megismerte, hogy az Azure-szolgáltatásokkal üzembe helyezett erőforrásokhoz szolgáltatásvégpontok engedélyezhetők. Létrehozott egy Azure Storage-fiókot, és egy adott virtuális hálózati alhálózat erőforrásaira korlátozta a fiók felé irányuló hálózati hozzáférést. További információkat a szolgáltatásvégpontokról a [szolgáltatásvégpontok áttekintését](virtual-network-service-endpoints-overview.md) és az [alhálózatok kezelését](virtual-network-manage-subnet.md) ismertető cikkekben olvashat.

Ha több virtuális hálózat található a fiókjában, érdemes lehet összekapcsolni két virtuális hálózatot, hogy az egyes virtuális hálózatokban található erőforrások kommunikálhassanak egymással. Ebből, hogy miként, olvassa el [a Virtuális hálózatok csatlakoztatása ..](tutorial-connect-virtual-networks-powershell.md)
