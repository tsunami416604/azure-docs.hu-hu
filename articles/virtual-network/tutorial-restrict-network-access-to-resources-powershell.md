---
title: PaaS-erőforrásokhoz – Azure PowerShell-lel való hálózati hozzáférés korlátozása |} A Microsoft Docs
description: Ebben a cikkben megismerheti, hogyan korlátozható az Azure-erőforrások, például az Azure Storage és Azure SQL Database-hálózati hozzáférés korlátozása a virtuális hálózati Szolgáltatásvégpontok Azure PowerShell-lel.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606114"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása a PowerShell-lel virtuális hálózati Szolgáltatásvégpontok

Virtuális hálózati szolgáltatásvégpontokkal egy adott virtuális hálózati alhálózatra korlátozható az egyes Azure-szolgáltatási erőforrásokhoz való hálózati hozzáférés. Emellett teljesen le is tiltható az internetes hozzáférés az erőforrásokhoz. A szolgáltatásvégpontok közvetlen csatlakozást biztosítanak a virtuális hálózat és a támogatott Azure-szolgáltatások között, így lehetővé teszik a virtuális hálózat magáncímterének használatát az Azure-szolgáltatások eléréséhez. A szolgáltatásvégpontokon keresztül az Azure-erőforrások felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán marad. Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Virtuális hálózat létrehozása egyetlen alhálózattal
* Alhálózat hozzáadása és szolgáltatásvégpont engedélyezése
* Azure-erőforrás létrehozása és hálózati hozzáférés engedélyezése az erőforráshoz egyetlen alhálózatról
* Virtuális gép (VM) üzembe helyezése az egyes alhálózatokon
* Erőforráshoz való alhálózati hozzáférés ellenőrzése
* Erőforráshoz való alhálózati és internetes hozzáférés letiltásának ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Előtt egy virtuális hálózatot hoz létre, akkor hozzon létre egy erőforráscsoportot a virtuális hálózatot és más ebben a cikkben létrehozott összes erőforrást. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. A következő példában létrehozunk egy erőforráscsoportot, nevű *myResourceGroup*: 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. A következő példában létrehozunk egy nevű virtuális hálózatot *myVirtualNetwork* a címelőtaggal rendelkező *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

A [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) parancs használatával hozzon létre egy alhálózati konfigurációt. A következő példában létrehozunk egy alhálózati konfigurációt nevű alhálózat *nyilvános*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Hozza létre az alhálózatot a virtuális hálózatban, ha az alhálózati konfigurációt a virtuális hálózatba a [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork):

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése 

Engedélyezheti a szolgáltatásvégpontokat csak a szolgáltatások, amelyek támogatják a Szolgáltatásvégpontok. Szolgáltatási végpont engedélyezve van elérhető szolgáltatások megtekintése az Azure-helyen [Get-AzureRmVirtualNetworkAvailableEndpointService](/powershell/module/azurerm.network/get-azurermvirtualnetworkavailableendpointservice). Az alábbi példa az elérhető szolgáltatások service-végpont engedélyezve van egy listáját adja vissza a *eastus* régióban. Vissza a szolgáltatások listájába idővel növekszik, ahogy további Azure-szolgáltatások válnak a szolgáltatásvégpont engedélyezve van.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkAvailableEndpointService -Location eastus | Select Name
``` 

Hozzon létre egy további alhálózatot a virtuális hálózatban. Ebben a példában egy alhálózat neve *privát* jön létre a szolgáltatásvégponttal *Microsoft.Storage*: 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>Alhálózat hálózati hozzáférésének korlátozása

Hozzon létre a hálózati biztonsági csoport biztonsági szabályai a [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). A következő szabály lehetővé teszi, hogy a kimenő hozzáférést az Azure Storage szolgáltatáshoz rendelt nyilvános IP-címek: 

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

A következő szabály megtagadja a hozzáférést az összes nyilvános IP-címet. Az előző szabályban, a magasabb prioritású, amely lehetővé teszi a hozzáférést a nyilvános IP-címek az Azure Storage miatt ez a szabály felülbírálja.

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

A következő szabály lehetővé teszi, hogy a távoli asztal protokoll (RDP) forgalmat bárhonnan az alhálózatra bejövő. Távoli asztali kapcsolatok engedélyezettek az alhálózathoz, így ellenőrizheti, hogy egy későbbi lépésben erőforráshoz való hálózati hozzáférés.

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

Hozzon létre egy hálózati biztonsági csoportot a [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) paranccsal. A következő példában létrehozunk egy hálózati biztonsági csoport nevű *myNsgPrivate*.

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

A hálózati biztonsági csoport társítása a *privát* alhálózat [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) majd írja az alhálózati konfigurációt a virtuális hálózathoz. Az alábbi példa hozzárendeli a *myNsgPrivate* hálózati biztonsági csoportot, amely a *privát* alhálózat:

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

A szolgáltatásvégpontok használatára képes Azure-szolgáltatásokkal létrehozott erőforrásokhoz való hálózati hozzáférés korlátozásának lépései szolgáltatásonként eltérőek. Az egyes szolgáltatásokhoz szükséges lépéseket az adott szolgáltatások dokumentációja tartalmazza. Ez a cikk további része az Azure Storage-fiókot, például a hálózati hozzáférés korlátozásához szükséges lépéseket tartalmazza.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Hozzon létre egy Azure storage-fiókkal [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Cserélje le `<replace-with-your-unique-storage-account-name>` , egyedi el az összes Azure-helyen 3 – 24 karakter közötti hosszúságú, melynek neve használatával csak számokból és kisbetűkből állhat.

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzureRmStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

A tárfiók létrehozása után lekérni a kulcsot, a storage-fiók és egy változóba [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey):

```azurepowershell-interactive
$storageAcctKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

A kulcs segítségével hozzon létre fájlmegosztást egy későbbi lépésben. Adja meg `$storageAcctKey` , és jegyezze fel az érték szerint is kell manuálisan adja meg azt egy későbbi lépésben a fájlmegosztás a virtuális gép meghajtó leképezése során.

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban

Hozzon létre egy környezetet a tárfiókhoz és kulcsát az [New-azurestoragecontext parancsmaggal kapcsolatos](/powershell/module/azure.storage/new-azurestoragecontext). A környezet magában foglalja a tárfiók nevét és kulcsát:

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

A fájlmegosztás létrehozása [New-AzureStorageShare](/powershell/module/azure.storage/new-azurestorageshare):

$share = New-AzureStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>Storage-fiók összes hálózati hozzáférés megtagadása

Alapértelmezés szerint a tárfiókok bármely hálózatban lévő ügyféltől érkező hálózati kapcsolatokat elfogadnak. Kijelölt hálózatok való hozzáférés korlátozására, módosítsa az alapértelmezett művelet *Megtagadás* a [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset). Hálózati hozzáférés megtagadva, miután a tárfiók nem érhető egyetlen hálózathoz sem.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Hálózati hozzáférés engedélyezése alhálózatról

A létrehozott virtuális hálózat beolvasása [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) és majd kérje le a privát alhálózati objektumot egy változóban az [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig):

```azurepowershell-interactive
$privateSubnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzureRmVirtualNetworkSubnetConfig `
  -Name "Private"
```

Hálózati hozzáférés engedélyezése a storage-fiókjában az *privát* alhálózat [Add-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig).

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

Az alábbi példa kimenetében hasonló kimenetet ad vissza:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-virtual-machine"></a>A második virtuális gép létrehozása

A virtuális gép létrehozása a *privát* alhálózat:

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

A virtuális gép létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépés az Azure befejezte a virtuális gép létrehozását és a kimenetet visszaadja a PowerShell. 

## <a name="confirm-access-to-storage-account"></a>Tárfiókhoz való hozzáférés ellenőrzése

Egy virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot. Az alábbi példában a nyilvános IP-címét adja vissza a *myVmPrivate* virtuális Géphez:

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

A *myVmPrivate* virtuális gépen a PowerShell-lel képezze le az Azure-fájlmegosztást a Z meghajtóra. Az alábbi parancsok futtatása, előtt cserélje le a `<storage-account-key>` és `<storage-account-name>` a megadott vagy lekért értékekkel [hozzon létre egy tárfiókot](#create-a-storage-account).

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

Győződjön meg arról, hogy a virtuális gép nem rendelkezik kimenő kapcsolattal bármely nyilvános IP-címek:

```powershell
ping bing.com
```

Nem kap választ, mert a *Magánjellegű* alhálózathoz rendelt hálózati biztonsági csoport nem engedélyezi a kimenő hozzáférést olyan nyilvános IP-címekhez, amelyek nem az Azure Storage szolgáltatáshoz rendelt címek.

Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="confirm-access-is-denied-to-storage-account"></a>Tárfiókhoz való hozzáférés letiltásának ellenőrzése

Nyilvános IP-címének lekéréséhez a *myVmPublic* virtuális Géphez:

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

Az a *myVmPublic* VM, próbál képezze le az Azure-fájlmegosztást a Z meghajtóra. Az alábbi parancsok futtatása, előtt cserélje le a `<storage-account-key>` és `<storage-account-name>` a megadott vagy lekért értékekkel [hozzon létre egy tárfiókot](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

A megosztás elérése megtagadva, és a egy `New-PSDrive : Access is denied` hiba. A hozzáférést a rendszer megtagadja, mert a *myVmPublic* virtuális gép a *Nyilvános* alhálózaton van üzembe helyezve. A *Nyilvános* alhálózat nem rendelkezik az Azure Storage-hoz engedélyezett szolgáltatásvégponttal, és a tárfiók kizárólag a *Magánjellegű* alhálózatról engedélyezi a hozzáférést, a *Nyilvános* alhálózatról nem.

Zárja be a távoli asztali munkamenetet a *myVmPublic* virtuális géppel.

A számítógépről kísérlet megtekintéséhez a fájlmegosztásokat a tárfiókban, a következő paranccsal:

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

A hozzáférés megtagadva, és a egy *Get-AzureStorageFile: A távoli kiszolgáló hibát adott vissza: (403) tiltott. HTTP-állapotkód: 403 – HTTP-hibaüzenetet: A kérelem nem jogosult e művelet elvégzéséhez* hiba, mert a számítógép nem szerepel a következőben a *privát* alhálózatának a *MyVirtualNetwork* virtuális hálózat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben engedélyezett egy szolgáltatásvégpontot egy virtuális hálózat alhálózatához. Megismerte, hogy az Azure-szolgáltatásokkal üzembe helyezett erőforrásokhoz szolgáltatásvégpontok engedélyezhetők. Létrehozott egy Azure Storage-fiókot, és egy adott virtuális hálózati alhálózat erőforrásaira korlátozta a fiók felé irányuló hálózati hozzáférést. További információkat a szolgáltatásvégpontokról a [szolgáltatásvégpontok áttekintését](virtual-network-service-endpoints-overview.md) és az [alhálózatok kezelését](virtual-network-manage-subnet.md) ismertető cikkekben olvashat.

Ha több virtuális hálózat található a fiókjában, érdemes lehet összekapcsolni két virtuális hálózatot, hogy az egyes virtuális hálózatokban található erőforrások kommunikálhassanak egymással. További információ [virtuális hálózatok összekapcsolása](tutorial-connect-virtual-networks-powershell.md).
