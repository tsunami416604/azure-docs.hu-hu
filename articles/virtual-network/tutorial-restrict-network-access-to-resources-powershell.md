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
ms.openlocfilehash: 28c95e1333b4641e50284a869135a9608dd3242f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>Hálózati hozzáférés korlátozása PaaS erőforrások virtuális hálózati szolgáltatás végpontokon PowerShell használatával

Virtuális hálózati szolgáltatási végpont lehetővé teszik a virtuális hálózati alhálózat az Azure szolgáltatás erőforrásokhoz való hálózati hozzáférés korlátozásához. Eltávolíthatja az internet-hozzáférés az erőforrásokhoz is. Végpontok adja meg a közvetlen kapcsolat a virtuális hálózat és a támogatott Azure-szolgáltatásokat, hogy lehetővé teszi a virtuális hálózat titkos címterület használata az Azure-szolgáltatások eléréséhez. Azure-erőforrások Szolgáltatásvégpontok keresztül mindig irányuló forgalom a Microsoft Azure hálózat marad. Ebből a cikkből megismerheti, hogyan:

* Hozzon létre egy virtuális hálózatot egyetlen alhálózattal
* Adjon hozzá egy alhálózatot és egy végpontot engedélyezése
* Hozzon létre egy Azure-erőforrás és a hálózati hozzáférés engedélyezése, hogy csak egy alhálózatból
* Virtuális gép (VM) telepítése minden egyes alhálózathoz
* Erősítse meg a hozzáférést egy erőforráshoz alhálózatból származó
* Győződjön meg arról, a hozzáférés megtagadva erőforrás alhálózat és az internetről

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához helyileg, ez a cikk igényel-e az Azure PowerShell modul verziója 5.4.1 vagy újabb. A telepített verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Virtuális hálózat létrehozása előtt hozzon létre egy erőforráscsoportot a virtuális hálózat, és ez a cikk létrehozott összes többi erőforrása van. Hozzon létre egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Az alábbi példa létrehoz egy erőforráscsoportot *myResourceGroup*: 

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

Az alhálózat-konfiguráció létrehozása [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). Az alábbi példa létrehoz egy alhálózati konfigurációt nevű alhálózat *nyilvános*:

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

## <a name="enable-a-service-endpoint"></a>A szolgáltatásvégpont engedélyezése 

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

## <a name="restrict-network-access-for-a-subnet"></a>Egy alhálózatot a hálózati hozzáférés korlátozása

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

Hozzon létre egy hálózati biztonsági csoport [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű *myNsgPrivate*.

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

## <a name="restrict-network-access-to-a-resource"></a>Egy erőforrás való hálózati hozzáférés korlátozása

Azure-végpontok engedélyezett szolgáltatások segítségével létrehozott erőforrások való hálózati hozzáférés korlátozása szükséges lépések szolgáltatásban függően változik. Az egyes szolgáltatások lépéseit minden egyes szolgáltatás dokumentációjában találhat. Ez a cikk hátralévő része az Azure Storage-fiók, mint például a hálózati hozzáférés korlátozása.

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

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban lévő

A tárfiók környezet létrehozása és a kulcs [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext). A környezet magában foglalja a tárfiók nevét és a fiók kulcsot:

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

A fájlmegosztás létrehozása [New-AzureStorageShare](/powershell/module/azure.storage/new-azurestorageshare):

$share = New-AzureStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>A storage-fiók összes hálózati hozzáférés megtagadása

Alapértelmezés szerint a storage-fiókok bármely hálózatban lévő ügyfelek hálózati kapcsolatokat fogadjon. A kijelölt hálózatokhoz való hozzáférés korlátozásához, módosítsa az alapértelmezett művelet *Megtagadás* rendelkező [frissítés-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset). Ha a hálózati hozzáférés megtagadva a tárfiók nem érhető el egyetlen hálózathoz sem.

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Egy alhálózatot a hálózati hozzáférés engedélyezése

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

Tesztelje a storage-fiók a hálózati hozzáférést, telepítenie kell a virtuális gépek minden egyes alhálózatot.

### <a name="create-the-first-virtual-machine"></a>Az első virtuális gép létrehozása

A virtuális gép létrehozása a *nyilvános* alhálózat [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). A következő parancs futtatásakor a rendszer kéri a hitelesítő adatokat. A megadott értékek a virtuális Gépet, a felhasználónév és jelszó van állítva. A `-AsJob` beállítás hoz létre a virtuális gép a háttérben, hogy továbbra is a következő lépéssel.

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

## <a name="confirm-access-to-storage-account"></a>Erősítse meg a tárfiók eléréséhez

Használjon [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vissza a nyilvános IP-címet a virtuális gépek. A következő példa a nyilvános IP-címét adja vissza a *myVmPrivate* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Cserélje le `<publicIpAddress>` az alábbi parancs a nyilvános IP-cím az előző parancs által visszaadott, és írja be a következő parancsot: 

```powershell
mstsc /v:<publicIpAddress>
```

A távoli asztal protokoll (RDP) fájl létrehozása és a számítógép letölti. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**. Adja meg a felhasználónevet és a virtuális gép létrehozásakor megadott jelszót. Válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, hogy a virtuális gép létrehozása után a megadott hitelesítő adatok megadása. Kattintson az **OK** gombra. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a figyelmeztetést kap, válassza ki a **Igen** vagy **Folytatás**, hogy a kapcsolat.

Az a *myVmPrivate* VM PowerShell-lel Z meghajtóra Azure fájlmegosztás hozzárendelését. Cserélje le a parancsok futtatásához `<storage-account-key>` és `<storage-account-name>` megadva, vagy olvassa be a értékeivel [hozzon létre egy tárfiókot](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```
PowerShell-kimenet visszaadása hasonló a következő egy példa a kimenetre:

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Az Azure-fájlmegosztáshoz sikeresen leképezve a Z meghajtóra.

Győződjön meg arról, hogy a virtuális gép rendelkezik-e a kimenő kapcsolat bármely nyilvános IP-címek:

```powershell
ping bing.com
```

Nem érkezett válasz, mert a társított hálózati biztonsági csoport kapni a *titkos* alhálózat nem engedélyezi a nyilvános IP-címek kívül a címek az Azure Storage szolgáltatás kimenő hozzáférést.

Zárja be a távoli asztali munkamenetet a *myVmPrivate* virtuális gép.

## <a name="confirm-access-is-denied-to-storage-account"></a>Erősítse meg a tárfiók a hozzáférés megtagadva

A nyilvános IP-cím az beszerzése a *myVmPublic* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Cserélje le `<publicIpAddress>` az alábbi parancs a nyilvános IP-cím az előző parancs által visszaadott, és írja be a következő parancsot: 

```powershell
mstsc /v:<publicIpAddress>
```

Az a *myVmPublic* VM, megpróbált Azure fájlmegosztás Z meghajtóra. Cserélje le a parancsok futtatásához `<storage-account-key>` és `<storage-account-name>` megadva, vagy olvassa be a értékeivel [hozzon létre egy tárfiókot](#create-a-storage-account).

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

A fájlmegosztás elérését, és érkezik egy `New-PSDrive : Access is denied` hiba. Hozzáférés megtagadva, mert a *myVmPublic* virtuális gép telepítve van a *nyilvános* alhálózat. A *nyilvános* alhálózati nincs engedélyezve az Azure Storage szolgáltatásvégpont, és a tárfiók csak a hálózati hozzáférés lehetővé teszi a *titkos* alhálózati, nem a *nyilvános*alhálózat.

Zárja be a távoli asztali munkamenetet a *myVmPublic* virtuális gép.

A számítógépről megpróbálja a fájlmegosztások tekintse meg a tárfiók a következő paranccsal:

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

Nincs hozzáférése, és érkezik egy *Get-azurestoragefile parancsnak: A távoli kiszolgáló hibát adott vissza: (403-as) tiltott. HTTP-állapotkód: 403 - HTTP-hibaüzenet: ehhez a kérelemhez nem jogosult a művelet elvégzéséhez* hiba, mert a számítógép nincs a a *titkos* alhálózata a *MyVirtualNetwork* virtuális hálózat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) eltávolítása az erőforráscsoport és az összes olyan erőforrást tartalmaz:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy végpontot a virtuális hálózati alhálózat engedélyezve van. Megtudta, hogy engedélyezhető-e a végpontok a több Azure-szolgáltatásokkal üzembe helyezett erőforrás. Létrehozott egy Azure Storage-fiók és a tárolási fiók csak egy virtuális hálózati alhálózat erőforrásainak korlátozott hálózati hozzáférést. Szolgáltatásvégpontok kapcsolatos további információkért lásd: [szolgáltatás végpontok áttekintése](virtual-network-service-endpoints-overview.md) és [alhálózatok kezelése](virtual-network-manage-subnet.md).

Ha több virtuális hálózat már rendelkezik fiókjába, érdemes lehet összekapcsolni két virtuális hálózatok, az egyes virtuális hálózati erőforrások is kommunikálhatnak egymással. Megtudhatja, hogyan: [virtuális hálózatok csatlakoztatása](tutorial-connect-virtual-networks-powershell.md).