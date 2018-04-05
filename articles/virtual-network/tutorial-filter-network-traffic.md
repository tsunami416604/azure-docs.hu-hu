---
title: Hálózati forgalmat - az Azure PowerShell |} Microsoft Docs
description: Ebből a cikkből megismerheti, hogyan alhálózat, a hálózati biztonsági csoport, a PowerShell használatával a hálózati forgalom szűrésére.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 53406150cfc2ec4229ecd9cf3356ad03d60f8e7e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Hálózati forgalom szűrésére egy hálózati biztonsági csoporttal PowerShell használatával

Szűrheti a hálózati forgalom a bejövő és kimenő virtuális hálózati alhálózat a hálózati biztonsági csoport. Hálózati biztonsági csoportok tartalmazzák a szabályokat, amelyek IP-cím, port és protokoll által a hálózati forgalom szűrésére. Egy alhálózaton üzembe helyezett erőforrás biztonsági szabályok érvényesek. Ebből a cikkből megismerheti, hogyan:

> [!div class="checklist"]
> * A hálózati biztonsági csoport és a biztonsági szabályok létrehozása
> * Hozzon létre egy virtuális hálózatot, és rendelje hozzá egy alhálózatot a hálózati biztonsági csoport
> * Virtuális gépek (VM) telepítése egy alhálózatba
> * Forgalomszűrők tesztelése

Tetszés szerint ez a cikk segítségével befejezheti a [Azure CLI](tutorial-filter-network-traffic-cli.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha PowerShell telepítéséhez és használatához helyileg, ez a cikk igényel-e az Azure PowerShell modul verziója 5.4.1 vagy újabb. A telepített verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hálózati biztonsági csoport biztonsági szabályokat tartalmazza. Biztonsági szabályok adja meg a forrás és cél. Források és célok lehetnek biztonsági csoportok.

### <a name="create-application-security-groups"></a>Biztonsági csoportok létrehozása

Először hozzon létre egy erőforráscsoportot, ebben a cikkben létrehozott összes erőforrás [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). A következő példa egy erőforrás csoportot hoz létre a *eastus* helye: 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Az alkalmazás biztonsági csoport létrehozása [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup). Az alkalmazáscsoport biztonsági lehetővé teszi csoport kiszolgálókat a hasonló Portszűrési követelmények. Az alábbi példakód létrehozza a két alkalmazás biztonsági csoportok.

```azurepowershell-interactive
$webAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Szabályok létrehozása

A biztonsági szabály létrehozása [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). Az alábbi példa létrehoz egy szabályt, amely lehetővé teszi, hogy a forgalom az internetről bejövő a *myWebServers* alkalmazáscsoport biztonsági 80-as és 443-as portokon keresztül:

```azurepowershell-interactive
$webRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

Ebben a cikkben RDP (3389-es port) kommunikál az internettel a *myAsgMgmtServers* virtuális gép. Éles környezetben, a 3389-es porton csatlakozik az internethez, hogy csökkenjen helyett javasolt, hogy csatlakozni használatával kezelni kívánt Azure-erőforrások egy [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [titkos](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hálózati kapcsolat.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoport [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). Az alábbi példakód létrehozza a hálózati biztonsági csoport nevű *myNsg*: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Az alábbi példa létrehoz egy virtuális nevű *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Az alhálózat-konfiguráció létrehozása [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig), majd írja be az alhálózat beállítása a virtuális hálózathoz [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). A következő példakóddal felveheti nevű alhálózat *mySubnet* a virtuális hálózat és társult a *myNsg* hálózati biztonsági csoportra, és azt:

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A virtuális gépek létrehozását, mielőtt beolvasni az alhálózat virtuális hálózat objektumot [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Hozzon létre egy nyilvános IP-cím az egyes virtuális gépek a [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmMgmt


Hozzon létre két hálózati adapterrel rendelkező [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface), és egy nyilvános IP-címet rendel a hálózati illesztőt. Az alábbi példakód létrehozza a hálózati adaptert, társult a *myVmWeb* nyilvános IP-címet, és lehetővé teszi az tagja a *myAsgWebServers* alkalmazáscsoport biztonsági:

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

Az alábbi példakód létrehozza a hálózati adaptert, társult a *myVmMgmt* nyilvános IP-címet, és lehetővé teszi az tagja a *myAsgMgmtServers* alkalmazáscsoport biztonsági:

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Hozzon létre két virtuális gépek a virtuális hálózat, hogy ellenőrizhesse a forgalom egy későbbi lépésben szűrést. 

Hozzon létre egy Virtuálisgép-konfiguráció [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), majd hozza létre a virtuális Géphez a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példakód létrehozza a virtuális gép, egy webkiszolgáló erre a célra. A `-AsJob` beállítás hoz létre a virtuális Gépet a háttérben, így továbbra is a következő lépés: 

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzureRmVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $webNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Hozzon létre egy virtuális Gépet egy felügyeleti kiszolgálója:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzureRmVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

A virtuális gép létrehozásához néhány percet vesz igénybe. Nem folytathatja a következő lépés a virtuális gép létrehozása Azure befejezéséig.

## <a name="test-traffic-filters"></a>Forgalomszűrők tesztelése

Használjon [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) vissza a nyilvános IP-címet a virtuális gépek. A következő példa a nyilvános IP-címét adja vissza a *myVmMgmt* VM:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

A távoli asztali munkamenetet a következővel létrehozásához használja a következő parancsot a *myVmMgmt* VM a helyi számítógépről. Cserélje le `<publicIpAddress>` az előző parancs által visszaadott IP-címét.

```
mstsc /v:<publicIpAddress>
```

Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza ki a **Connect**.

Adja meg a felhasználónevet és a virtuális gép létrehozásakor a megadott jelszó (válassza ki szeretne **több lehetőséget**, majd **használjon más fiókot**, hogy a virtuális gép létrehozása után a megadott hitelesítő adatok megadása), Válassza ki **OK**. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza ki **Igen** gombra a kapcsolat. 
   
A csatlakozás sikeres, mert a 3389-es port használata engedélyezett az internetről bejövő a *myAsgMgmtServers* biztonsági csoport, amely a hálózati adapter csatlakozik a *myVmMgmt* virtuális gép.

Távoli asztali kapcsolat létrehozása az alábbi parancs segítségével a *myVmWeb* VM, az a *myVmMgmt* rendelkező virtuális gépet, a következő parancsot, a PowerShell:

``` 
mstsc /v:myvmWeb
```

A csatlakozás sikeres, mert az egyes hálózati biztonsági csoportok alapértelmezett biztonsági szabály lehetővé teszi, hogy a forgalmat a virtuális hálózaton belül az összes IP-címek között az összes porton keresztül. A távoli asztali kapcsolat nem hozható létre a *myVmWeb* VM az internetről, mert a biztonsági szabály az a *myAsgWebServers* port nem engedélyezi az internetről bejövő 3389-es.

Az alábbi parancs segítségével a Microsoft IIS-t telepíteni a *myVmWeb* PowerShell virtuális gép:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Az IIS telepítésének befejezése után válassza le a *myVmWeb* visszatérhet a virtuális Gépet a *myVmMgmt* VM távoli asztali kapcsolat. Az IIS üdvözlőképernyőn megtekintéséhez, nyisson meg egy böngészőprogramot, és keresse meg a http://myVmWeb.

Válassza le a *myVmMgmt* virtuális gép.

A számítógépen, adja meg a következő parancsot a Powershellből beolvasása a nyilvános IP-címe a *myVmWeb* kiszolgáló:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Annak ellenőrzéséhez, hogy hozzá tud férni a *myVmWeb* webkiszolgáló, az Azure-on kívüli, nyisson meg egy böngészőprogramot, a számítógépen, és keresse meg a `http://<public-ip-address-from-previous-step>`. A csatlakozás sikeres, mert a 80-as porton az internetről bejövő engedélyezett a *myAsgWebServers* biztonsági csoport, amely a hálózati adapter csatlakozik a *myVmWeb* virtuális gép.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) eltávolítása az erőforráscsoport és az összes olyan erőforrást tartalmaz:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy hálózati biztonsági csoportot hozott létre, és azt társított virtuális hálózati alhálózat. Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoport – áttekintés](security-overview.md) és [a hálózati biztonsági csoportok kezelése](virtual-network-manage-nsg-arm-ps.md).

Alapértelmezés szerint az alhálózatok közötti forgalmat Azure útvonalak. Ehelyett választhatja, hogy egy virtuális Gépet, mint egy tűzfal keresztül alhálózatok közötti forgalom például. Megtudhatja, hogyan kell hoznia egy útválasztási táblázatot, hogy a következő cikket.

> [!div class="nextstepaction"]
> [Hozzon létre egy útválasztási táblázatot](./tutorial-create-route-table-portal.md)