---
title: Hálózati forgalom szűrése – oktatóanyag – Azure PowerShell | Microsoft Docs
description: Eben az oktatóanyagban megismerheti, hogyan szűrheti a hálózati forgalmat alhálózatokra egy hálózati biztonsági csoport és a PowerShell használatával.
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
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 8e04ed7ad16b673597b62d947c3f782ee0d27c7c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-powershell"></a>Oktatóanyag: Hálózati forgalom szűrése hálózati biztonsági csoportokkal a PowerShell használatával

A virtuális hálózatok alhálózatainak bejövő vagy kimenő hálózati forgalmát hálózati biztonsági csoport segítségével szűrheti. A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak, amelyek IP-cím, port és protokoll szerint szűrik a hálózati forgalmat. A biztonsági szabályok az egyes alhálózatokban üzembe helyezett erőforrásokra vonatkoznak. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hálózati biztonsági csoport és biztonsági szabályok létrehozása
> * Virtuális hálózat létrehozása és hálózati biztonsági csoport hozzárendelése egy alhálózathoz
> * Virtuális gépek üzembe helyezése egy alhálózaton
> * Forgalomszűrők tesztelése

Igény szerint az oktatóanyag az [Azure CLI](tutorial-filter-network-traffic-cli.md) használatával is elvégezhető.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral. 

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

A hálózati biztonsági csoportok biztonsági szabályokat tartalmaznak. A biztonsági szabályok egy forrást és egy célt határoznak meg. A források és célok lehetnek alkalmazásbiztonsági csoportok.

### <a name="create-application-security-groups"></a>Alkalmazásbiztonsági csoportok létrehozása

Először is hozzon létre egy erőforráscsoportot a jelen oktatóanyag során létrehozott összes erőforrás számára a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. A következő példa létrehoz egy erőforráscsoportot az *eastus* helyen: 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Hozzon létre egy alkalmazásbiztonsági csoportot a [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup) paranccsal. Az alkalmazásbiztonsági csoportok lehetővé teszik, hogy csoportokba rendezze a hasonló portszűrési követelményekkel rendelkező kiszolgálókat. Az alábbi példa két alkalmazásbiztonsági csoportot hoz létre.

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

### <a name="create-security-rules"></a>Biztonsági szabályok létrehozása

Hozzon létre egy biztonsági szabályt a [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) paranccsal. Az alábbi példa egy olyan szabályt hoz létre, amely engedélyezi az internetről a *myWebServers* alkalmazásbiztonsági csoportba bejövő forgalmat a 80-as és 443-as porton keresztül.

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

Ebben az oktatóanyagban az RDP (3389-es port) közvetlenül az internetre csatlakozik a *myAsgMgmtServers* virtuális gép esetében. Éles környezet esetében a 3389-es port közvetlenül az internetre való csatlakoztatása helyett javasolt [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [privát](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) hálózati kapcsolat használatával csatlakozni a kezelni kívánt Azure-erőforrásokhoz.

### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) paranccsal. Az alábbi példa egy *myNsg* nevű hálózati biztonsági csoportot hoz létre: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Az alábbi példa egy *myVirtualNetwork* virtuális hálózatot hoz létre:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Hozzon létre egy alhálózati konfigurációt a [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) paranccsal, majd írja az alhálózati konfigurációt a virtuális hálózatba a [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork) paranccsal Az alábbi példa egy *mySubnet* nevű alhálózatot ad hozzá a virtuális hálózathoz, és hozzárendeli a *myNsg* hálózati biztonsági csoportot.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A virtuális gépek létrehozása előtt kérje le a virtuálishálózat-objektumot az alhálózattal együtt a [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) paranccsal.

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Hozzon létre egy nyilvános IP-címet minden virtuális gép esetében a [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) paranccsal.

$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmMgmt


Hozzon létre két hálózati adaptert a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) paranccsal, és rendeljen hozzájuk egy nyilvános IP-címet. Az alábbi példa létrehoz egy hálózati adaptert, hozzárendeli a *myVmWeb* nyilvános IP-címet, és felveszi azt a *myAsgWebServers* alkalmazásbiztonsági csoport tagjai közé:

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

Az alábbi példa létrehoz egy hálózati adaptert, hozzárendeli a *myVmMgmt* nyilvános IP-címet, és felveszi azt a *myAsgMgmtServers* alkalmazásbiztonsági csoport tagjai közé:

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Hozzon létre két virtuális gépet a virtuális hálózatban, hogy érvényesíthesse majd a fogalom szűrését egy később lépésben. 

Hozzon létre egy virtuálisgép-konfigurációt a [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) paranccsal, majd hozza létre a virtuális gépet a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal. Az alábbi példa egy virtuális gépet hoz létre, amely webkiszolgálóként fog szolgálni. Az `-AsJob` kapcsoló a háttérben hozza létre a virtuális gépet, így Ön eközben folytathatja a következő lépéssel. 

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

Hozzon létre egy felügyeleti kiszolgálóként szolgáló virtuális gépet:

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

A virtuális gép létrehozása néhány percet vesz igénybe. Ne ugorjon a következő lépésre, amíg az Azure be nem fejezte a virtuális gép létrehozását.

## <a name="test-traffic-filters"></a>Forgalomszűrők tesztelése

Egy virtuális gép nyilvános IP-címének visszaadásához használja a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) parancsot. Az alábbi példa a *myVmMgmt* virtuális gép nyilvános IP-címét adja vissza:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Az alábbi parancs használatával hozzon létre egy távoli asztali munkamenetet a *myVmMgmt* virtuális géppel a helyszíni számítógépéről. Cserélje ki a `<publicIpAddress>` kifejezést az előző parancs által visszaadott nyilvános IP-címre.

```
mstsc /v:<publicIpAddress>
```

Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót (előfordulhat, hogy a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához ki kell választania a **További lehetőségek**, majd a **Másik fiók használata** elemet), majd válassza az **OK** gombot. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** lehetőséget a csatlakozás folytatásához. 
   
A kapcsolat sikeresen létrejön, mert a 3389-es porton engedélyezve van az internetről a *myAsgMgmtServers* alkalmazásbiztonsági csoportba (amelyben a *myVmMgmt* virtuális géphez csatlakoztatott hálózati adapter található) bejövő forgalom.

Az alábbi parancs PowerShellben történő használatával hozzon létre egy távoli asztali kapcsolatot a *myVmWeb* virtuális géppel a *myVmMgmt* virtuális gépről:

``` 
mstsc /v:myvmWeb
```

A kapcsolat sikeresen létrejön, mert az egyes hálózati biztonsági csoportokon belüli alapértelmezett biztonsági szabály minden porton keresztül engedélyezi a forgalmat a virtuális hálózaton belüli összes IP-cím között. Nem hozhat létre távoli asztali kapcsolatot a *myVmWeb* virtuális géppel az internetről, mert a *myAsgWebServers* biztonsági szabálya nem engedélyezi az internetről bejövő forgalmat a 3389-es porton keresztül.

Az alábbi parancs PowerShellben történő használatával telepítse a Microsoft IIS-t a *myVmWeb* virtuális gépre:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Az IIS telepítésének befejezése után csatlakozzon le a *myVmWeb* virtuális gépről. Így csak a *myVmMgmt* virtuális géppel létesített távoli asztali kapcsolat marad. Az IIS üdvözlőképernyőjének megtekintéséhez nyisson meg egy webböngészőt, és lépjen a következő helyre: http://myVmWeb.

Csatlakozzon le a *myVmMgmt* virtuális gépről.

A számítógépén írja be az alábbi parancsot a PowerShellben a *myVmWeb* kiszolgáló nyilvános IP-címének lekéréséhez.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Annak megerősítéséhez, hogy eléri a *myVmWeb* webkiszolgálót az Azure-on kívülről is, nyisson meg egy webböngészőt a számítógépen, és lépjen a következő helyre: `http://<public-ip-address-from-previous-step>`. A kapcsolat sikeresen létrejön, mert a 80-as porton engedélyezve van az internetről a *myAsgWebServers* alkalmazásbiztonsági csoportba (amelyben a *myVmWeb* virtuális géphez csatlakoztatott hálózati adapter található) bejövő forgalom.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy hálózati biztonsági csoportot, és hozzárendelte azt egy virtuális hálózat alhálózatához. A hálózati biztonsági csoportokkal kapcsolatos további információ: [Hálózati biztonsági csoportok áttekintése](virtual-network-manage-nsg-arm-ps.md) és ](security-overview.md)Hálózati biztonsági csoportok kezelése[.

Az Azure alapértelmezés szerint irányítja a forgalmat az alhálózatok között. Ehelyett lehetősége van arra, hogy egy virtuális gépen keresztül irányítsa a forgalmat az alhálózatok között, amely így például tűzfalként is szolgálhat. Ha meg szeretné ismerni, hogyan hozhat létre útválasztási táblázatot, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Útválasztási táblázat létrehozása](./tutorial-create-route-table-portal.md)