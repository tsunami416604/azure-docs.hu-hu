---
title: PaaS erőforrások – az Azure PowerShell való hálózati hozzáférés korlátozása |} Microsoft Docs
description: Ebből a cikkből megismerheti, hogyan korlátozására és a hálózati hozzáférés korlátozása Azure-erőforrások, például az Azure Storage és az Azure SQL Database, a virtuális hálózati szolgáltatás végpontok Azure PowerShell használatával.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
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
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: b3977e045751165947243c67291e81b998b5fcb5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Hálózati hozzáférés korlátozása PaaS erőforrások virtuális hálózati szolgáltatás végpontokon PowerShell használatával

Virtuális hálózati szolgáltatásvégpontokkal egy adott virtuális hálózati alhálózatra korlátozható az egyes Azure-szolgáltatási erőforrásokhoz való hálózati hozzáférés. Emellett teljesen le is tiltható az internetes hozzáférés az erőforrásokhoz. A szolgáltatásvégpontok közvetlen csatlakozást biztosítanak a virtuális hálózat és a támogatott Azure-szolgáltatások között, így lehetővé teszik a virtuális hálózat magáncímterének használatát az Azure-szolgáltatások eléréséhez. A szolgáltatásvégpontokon keresztül az Azure-erőforrások felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán marad. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Virtuális hálózat létrehozása egyetlen alhálózattal
* Alhálózat hozzáadása és szolgáltatásvégpont engedélyezése
* Azure-erőforrás létrehozása és hálózati hozzáférés engedélyezése az erőforráshoz egyetlen alhálózatról
* Virtuális gép (VM) üzembe helyezése az egyes alhálózatokon
* Erőforráshoz való alhálózati hozzáférés ellenőrzése
* Erőforráshoz való alhálózati és internetes hozzáférés letiltásának ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához helyileg, ez a cikk igényel-e az Azure PowerShell modul verziója 5.4.1 vagy újabb. A telepített verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Virtuális hálózat létrehozása előtt hozzon létre egy erőforráscsoportot a virtuális hálózat, és ez a cikk létrehozott összes többi erőforrása van. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroup*: 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Az alábbi példa létrehoz egy virtuális hálózatot nevű *myVirtualNetwork* a címelőtaggal rendelkező *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

A [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) parancs használatával hozzon létre egy alhálózati konfigurációt. Az alábbi példa létrehoz egy alhálózati konfigurációt nevű alhálózat *nyilvános*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Hozza létre az alhálózatot a virtuális hálózatban az alhálózat beállítása a virtuális hálózathoz írásával [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork):

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése 

Csak a végpontok támogató szolgáltatások Szolgáltatásvégpontok engedélyezheti. Tekintse meg az Azure-beli hely, a szolgáltatási végpont engedélyezve van az elérhető szolgáltatások [Get-AzureRmVirtualNetworkAvailableEndpointService](/powershell/module/azurerm.network/get-azurermvirtualnetworkavailableendpointservice). Az alábbi példa elérhető szolgáltatás-végpont-kompatibilis szolgáltatások listáját adja vissza a *eastus* régióban. A szolgáltatások visszaadott tájékoztatás idővel bővülni fog, több Azure-szolgáltatások szolgáltatási végpont engedélyezve lesz.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkAvailableEndpointService -Location eastus | Select Name
``` 

Hozzon létre egy további alhálózati a virtuális hálózat. Ebben a példában egy alhálózat nevű *titkos* hozza létre a szolgáltatásvégpont *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Alhálózat hálózati hozzáférésének korlátozása

Hozzon létre a hálózati biztonsági csoport biztonsági szabályai a [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). A következő szabály lehetővé teszi, hogy a nyilvános IP-címek az Azure Storage szolgáltatás kimenő hozzáférést: 

```azurepowershell-interactive
$rule1 = New-AzureRmNetworkSecurityRuleConfig `
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

A következő szabály megtagadja a hozzáférést minden nyilvános IP-cím. A korábbi szabály felülbírálja a szabályt, a magasabb prioritású, amely lehetővé teszi a hozzáférést a nyilvános IP-címek az Azure Storage miatt.

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
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

A következő szabály lehetővé teszi, hogy a távoli asztal protokoll (RDP) forgalmat bárhonnan alhálózathoz bejövő. Távoli asztali kapcsolatokhoz, hogy a hálózati hozzáférést egy erőforráshoz egy későbbi lépésben ellenőrizheti az alhálózat számára engedélyezett.

```azurepowershell-interactive
$rule3 = New-AzureRmNetworkSecurityRuleConfig `
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

Hozzon létre egy hálózati biztonsági csoportot a [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) paranccsal. Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

A hálózati biztonsági csoportra, és társítsa a *titkos* alhálózat [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) majd írja be az alhálózat beállítása a virtuális hálózathoz. Az alábbi példa társítja a *myNsgPrivate* hálózati biztonsági csoportra, és a *titkos* alhálózati:

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>Erőforráshoz való hálózati hozzáférés korlátozása

A szolgáltatásvégpontok használatára képes Azure-szolgáltatásokkal létrehozott erőforrásokhoz való hálózati hozzáférés korlátozásának lépései szolgáltatásonként eltérőek. Az egyes szolgáltatásokhoz szükséges lépéseket az adott szolgáltatások dokumentációja tartalmazza. Ez a cikk hátralévő része az Azure Storage-fiók, mint például a hálózati hozzáférés korlátozása.

### <a name="create-a-storage-account"></a>Create a storage account

Az Azure storage-fiók létrehozása [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Cserélje le `<replace-with-your-unique-storage-account-name>` , minden Azure helyszínen, 3 – 24 karakter hosszúságúnak, egyedi névvel használatával csak számokat és kisbetűket tartalmazhatnak.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzureRmStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

A tárfiók létrehozása után a tárfiók kulcsa olvasson be egy változó, [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

A kulcs használatával hozzon létre egy fájlmegosztást egy későbbi lépésben. Adja meg `$storageAcctKey` és Megjegyzés: az érték is kell kézzel beírnia egy későbbi lépésben a fájlmegosztás leképezése egy virtuális gép meghajtóra.

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban

A tárfiók környezet létrehozása és a kulcs [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext). A környezet magában foglalja a tárfiók nevét és a fiók kulcsot:

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

A fájlmegosztás létrehozása [New-AzureStorageShare](/powershell/module/azure.storage/new-azurestorageshare):

$share = New-AzureStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>A storage-fiók összes hálózati hozzáférés megtagadása

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező hálózati kapcsolatokat elfogadnak. A kijelölt hálózatokhoz való hozzáférés korlátozásához, módosítsa az alapértelmezett művelet *Megtagadás* rendelkező [frissítés-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset). Ha a hálózati hozzáférés megtagadva a tárfiók nem érhető el egyetlen hálózathoz sem.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Hálózati hozzáférés engedélyezése alhálózatról

A létrehozott virtuális hálózathoz beolvasása [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) majd lekérheti a titkos alhálózati objektum alhálózatát a egy változóba [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzureRmVirtualNetworkSubnetConfig `
  -Name "Private"
```

A tárolási fiók a hálózati hozzáférés engedélyezése a *titkos* alhálózat [Add-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig).

```azurepowershell-interactive
Add-AzureRmStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Tárfiókhoz való hálózati hozzáférés teszteléséhez helyezzen üzembe egy virtuális gépet minden alhálózaton.

### <a name="create-the-first-virtual-machine"></a>Az első virtuális gép létrehozása

A virtuális gép létrehozása a *nyilvános* alhálózat [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). A következő parancs futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszót. Az `-AsJob` kapcsoló a háttérben hozza létre a virtuális gépet, így Ön eközben folytathatja a következő lépéssel.

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

A következő egy példa a kimenetre hasonló kimenetet visszaadja:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-virtual-machine"></a>A második virtuális gép létrehozása

A virtuális gép létrehozása a *titkos* alhálózati:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

A virtuális gép létrehozása Azure néhány percet vesz igénybe. Ne folytassa a következő lépéssel mindaddig, amíg az Azure létrehozza a virtuális Gépet, és a kimenetet visszaadja a PowerShell. 

## <a name="confirm-access-to-storage-account"></a>Tárfiókhoz való hozzáférés ellenőrzése

Egy virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot. A következő példa a nyilvános IP-címét adja vissza a *myVmPrivate* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A következő parancsban cserélje le a `<publicIpAddress>` értékét az előző parancs által visszaadott nyilvános IP-címre, majd írja be a következő parancsot: 

```powershell
mstsc /v:<publicIpAddress>
```

A rendszer létrehoz és letölt a számítógépre egy Remote Desktop Protocol (.rdp) fájlt. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához a **További lehetőségek**, majd a **Másik fiók használata** lehetőségre kell kattintania. Kattintson az **OK** gombra. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához.

A *myVmPrivate* virtuális gépen a PowerShell-lel képezze le az Azure-fájlmegosztást a Z meghajtóra. Cserélje le a parancsok futtatásához `<storage-account-key>` és `<storage-account-name>` megadva, vagy olvassa be a értékeivel [hozzon létre egy tárfiókot](#create-a-storage-account).

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

Győződjön meg arról, hogy a virtuális gép rendelkezik-e a kimenő kapcsolat bármely nyilvános IP-címek:

```powershell
ping bing.com
```

Nem kap választ, mert a *Magánjellegű* alhálózathoz rendelt hálózati biztonsági csoport nem engedélyezi a kimenő hozzáférést olyan nyilvános IP-címekhez, amelyek nem az Azure Storage szolgáltatáshoz rendelt címek.

Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="confirm-access-is-denied-to-storage-account"></a>Tárfiókhoz való hozzáférés letiltásának ellenőrzése

A nyilvános IP-cím az beszerzése a *myVmPublic* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A következő parancsban cserélje le a `<publicIpAddress>` értékét az előző parancs által visszaadott nyilvános IP-címre, majd írja be a következő parancsot: 

```powershell
mstsc /v:<publicIpAddress>
```

Az a *myVmPublic* VM, megpróbált Azure fájlmegosztás Z meghajtóra. Cserélje le a parancsok futtatásához `<storage-account-key>` és `<storage-account-name>` megadva, vagy olvassa be a értékeivel [hozzon létre egy tárfiókot](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

A fájlmegosztás elérését, és érkezik egy `New-PSDrive : Access is denied` hiba. A hozzáférést a rendszer megtagadja, mert a *myVmPublic* virtuális gép a *Nyilvános* alhálózaton van üzembe helyezve. A *Nyilvános* alhálózat nem rendelkezik az Azure Storage-hoz engedélyezett szolgáltatásvégponttal, és a tárfiók kizárólag a *Magánjellegű* alhálózatról engedélyezi a hozzáférést, a *Nyilvános* alhálózatról nem.

Zárja be a távoli asztali munkamenetet a *myVmPublic* virtuális géppel.

A számítógépről megpróbálja a fájlmegosztások tekintse meg a tárfiók a következő paranccsal:

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Nincs hozzáférése, és érkezik egy *Get-azurestoragefile parancsnak: A távoli kiszolgáló hibát adott vissza: (403-as) tiltott. HTTP-állapotkód: 403 - HTTP-hibaüzenet: ehhez a kérelemhez nem jogosult a művelet elvégzéséhez* hiba, mert a számítógép nincs a a *titkos* alhálózata a *MyVirtualNetwork* virtuális hálózat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy végpontot a virtuális hálózati alhálózat engedélyezve van. Megismerte, hogy az Azure-szolgáltatásokkal üzembe helyezett erőforrásokhoz szolgáltatásvégpontok engedélyezhetők. Létrehozott egy Azure Storage-fiókot, és egy adott virtuális hálózati alhálózat erőforrásaira korlátozta a fiók felé irányuló hálózati hozzáférést. További információkat a szolgáltatásvégpontokról a [szolgáltatásvégpontok áttekintését](virtual-network-service-endpoints-overview.md) és az [alhálózatok kezelését](virtual-network-manage-subnet.md) ismertető cikkekben olvashat.

Ha több virtuális hálózat található a fiókjában, érdemes lehet összekapcsolni két virtuális hálózatot, hogy az egyes virtuális hálózatokban található erőforrások kommunikálhassanak egymással. Megtudhatja, hogyan: [virtuális hálózatok csatlakoztatása](tutorial-connect-virtual-networks-powershell.md).
