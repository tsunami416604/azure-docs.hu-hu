---
title: 'Rövid útmutató: NAT-átjáró létrehozása – Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre NAT-átjárót az Azure PowerShell használatával
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 1d5f8d6e0b2499bbecd32e7cb3fda2cd2cad4d19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202228"
---
# <a name="quickstart-create-a-nat-gateway-using-azure-powershell"></a>Rövid útmutató: NAT-átjáró létrehozása az Azure PowerShell használatával

Ez a rövid útmutató bemutatja, hogyan használhatja az Azure virtuális hálózati NAT-szolgáltatást. NAT-átjárót hoz létre, hogy kimenő kapcsolatot biztosítson egy virtuális gép számára az Azure-ban. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ezt az oktatóanyagot az Azure Cloud Shell használatával befejezheti, vagy helyileg futtathatja a parancsokat.  Ha még nem használta az Azure Cloud Shellt, [jelentkezzen be most.](https://shell.azure.com)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest)segítségével. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy **myResourceGroupNAT** nevű erőforráscsoportot az **eastus2** helyen:

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>A NAT-átjáró létrehozása

A NAT-átjáró nyilvános IP-beállításai a következők:

* **Nyilvános IP-címek**
* **Nyilvános IP-előtagok**

Mindkettő használható NAT átjáróval.

Ehhez a forgatókönyvhöz egy nyilvános IP-címet és egy nyilvános IP-előtagot adunk hozzá.

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az internet eléréséhez egy vagy több nyilvános IP-címre van szükség a NAT-átjáróhoz. A [New-AzPublicIpAddress használatával](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) hozzon létre egy **myPublicIP** nevű nyilvános IP-címerőforrást a **myResourceGroupNAT-ban.** A parancs eredménye egy változóban lesz **tárolva, $publicIP** későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

A [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) segítségével hozzon létre egy **myPublicIPprefix** nevű nyilvános IP-előtagot a **myResourceGroupNAT-ban.**  A parancs eredménye egy **$publicIPPrefix** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró-erőforrás létrehozása

Ez a szakasz részletezi, hogyan hozhat létre és konfigurálhat a NAT szolgáltatás következő összetevőit a NAT átjáróerőforrás használatával:
  - Nyilvános IP-készlet és nyilvános IP-előtag a NAT átjáró erőforrás által lefordított kimenő folyamatokhoz.
  - Módosítsa az alapjárati időoutot az alapértelmezett 4 percről 10 percre.

Hozzon létre egy globális Azure NAT-átjárót a [New-AzNatGateway segítségével.](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway) Ennek a parancsnak az eredménye egy **myNATgateway** nevű átjáró-erőforrást hoz létre, amely a nyilvános IP-címet használja **myPublicIP** és a nyilvános IP-előtag **myPublicIPprefix**. Az tétlen időjárat 10 percre van állítva.  A parancs eredménye egy **$natGateway** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

Ezen a ponton a NAT-átjáró működőképes, és csak azt kell konfigurálni, hogy a virtuális hálózat mely alhálózatai használják azt.

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

Hozza létre a virtuális hálózatot, és társítsa az alhálózatot az átjáróhoz.

Hozzon létre egy **myVnet** nevű virtuális hálózatot **egy mySubnet** nevű alhálózattal a [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) használatával a **myResourceGroup** ban a [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)használatával. A virtuális hálózat IP-címterülete **192.168.0.0/16**. A virtuális hálózaton belüli **alhálózat: 192.168.0.0/24**.  A parancsok eredményét a **$subnet** és **$vnet** nevű változók tárolják későbbi használatra.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

Az internetes célállomásokra irányuló összes kimenő forgalom most már a NAT szolgáltatást használja.  Nem szükséges az UDR konfigurálása.

## <a name="create-a-vm-to-use-the-nat-service"></a>Virtuális gép létrehozása a NAT szolgáltatás használatához

Most hozzon létre egy virtuális gép a NAT szolgáltatás használatához.  Ez a virtuális gép egy nyilvános IP-cím ként használható példányszintű nyilvános IP-cím, amely lehetővé teszi a virtuális gép eléréséhez.  A NAT-szolgáltatás áramlásirány-függő, és lecseréli az alhálózat alapértelmezett internetes célját. A virtuális gép nyilvános IP-címe nem lesz használva a kimenő kapcsolatokhoz.

### <a name="create-public-ip-for-source-vm"></a>Nyilvános IP létrehozása a forrás virtuális géphez

Létrehozunk egy nyilvános IP-t a virtuális gép eléréséhez.  A [New-AzPublicIpAddress használatával](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) hozzon létre egy **myResourceGroupNAT** nevű nyilvános IP-címerőforrást. **myResourceGroupNAT**  A parancs eredménye egy **$publicIpVM** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>NSG létrehozása és SSH-végpont elérhetővé

A szabványos nyilvános IP-címek "alapértelmezés szerint biztonságosak", létre kell hoznunk egy NSG-t, hogy lehetővé tegyék az ssh bejövő hozzáférését. A [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) segítségével hozzon létre egy **myNSG**nevű NSG-erőforrást. A [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) segítségével hozzon létre egy NSG-szabályt **az ssh** nevű SSH-hozzáféréshez a **myResourceGroupNAT-ban.**  A parancs eredménye egy **$nsg** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Hálózati adapter létrehozása virtuális géphez

Hozzon létre egy hálózati adaptert a **MyNic**nevű [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) kapcsolattal. Ez a parancs a nyilvános IP-címet és a hálózati biztonsági csoportot társítja. A parancs eredménye egy **$nic** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Virtuális gép létrehozása

#### <a name="create-ssh-key-pair"></a>SSH-kulcspár létrehozása

A rövid útmutató elvégzéséhez egy SSH-kulcspárra lesz szüksége. Ha már rendelkezésére áll egy SSH-kulcspár, kihagyhatja ezt a lépést.

SSH-keygen használatával sSH kulcspárt hozhat létre.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Az SSH-kulcspárok létrehozásáról, többek között a PuTTy használatáról az [SSH-kulcsok Windowsban való használatát](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) ismertető cikkben talál részletesebb információt.

Ha az SSH-kulcspárt a Cloud Shell használatával hozza létre, a kulcspár egy tárolólemezképtárolja. Ez [a tárfiók automatikusan létrejön.](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage) Ne törölje a tárfiókot vagy a fájlmegosztást, amíg a kulcsok be olvasása után.

#### <a name="create-vm-configuration"></a>Virtuálisgép-konfiguráció létrehozása

Virtuális gép létrehozásához a PowerShellben hozzon létre egy konfigurációt, amely a lemezkép használatára, méretére és hitelesítési beállításaira vonatkozó beállításokat tartalmaz. A konfiguráció a virtuális gép létrehozásához használható.

Határozza meg az SSH hitelesítő adatokat, az operációs rendszer adatait és a virtuális gép méretét. Ebben a példában az SSH-kulcs a ~/.ssh/id_rsa.pub ban van tárolva.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
A konfigurációs definíciók kombinálásával hozzon létre egy **myVM** nevű virtuális gépet [a New-AzVM szolgáltatással](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) a **myResourceGroupNAT**szolgáltatásban.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Várja meg, amíg a virtuális gép előkészíti az üzembe helyezést, majd folytassa a többi lépéssel.

## <a name="discover-the-ip-address-of-the-vm"></a>A virtuális gép IP-címének felfedezése

Először is fel kell derítenünk a létrehozott virtuális gép IP-címét. A virtuális gép nyilvános IP-címének leigazolásához használja a [Get-AzPublicIpAddress címet.](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest) 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy jegyzettömbbe, így használhatja a virtuális gép eléréséhez.

### <a name="sign-in-to-vm"></a>Bejelentkezés a virtuális gépbe

Az SSH hitelesítő adatokat az előző műveletből származó Felhőrendszerhéjban kell tárolni.  Nyisson meg egy [Azure Cloud Shellt](https://shell.azure.com) a böngészőjében. Használja az előző lépésben beolvasott IP-címet az SSH-hoz a virtuális géphez.

```bash
ssh azureuser@<ip-address-destination>
```

Most már készen áll a NAT szolgáltatás használatára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [Eltávolítás-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) paranccsal eltávolíthatja az erőforráscsoportot és a benne lévő összes erőforrást.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy NAT-átjárót és egy virtuális gépet a használatához. 

Tekintse át a metrikákat az Azure Monitorban a NAT-szolgáltatás működésének megtekintéséhez. Diagnosztizálja az olyan problémákat, mint például a rendelkezésre álló SNAT-portok erőforrás-kimerülése.  Az SNAT-portok erőforrás-kimerülése további nyilvános IP-címerőforrások vagy nyilvános IP-előtag-erőforrások vagy mindkettő hozzáadásával történik.


- Learn about [Azure Virtual Network NAT](./nat-overview.md)
- További információ a [NAT átjáró-erőforrásról.](./nat-gateway-resource.md)
- Rövid útmutató a [NAT átjáróerőforrás Azure CLI használatával történő](./quickstart-create-nat-gateway-cli.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT átjáróerőforrás Azure PowerShell használatával történő](./quickstart-create-nat-gateway-powershell.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT-átjáró-erőforrás Azure Portalon való](./quickstart-create-nat-gateway-portal.md)telepítéséhez.
> [!div class="nextstepaction"]


