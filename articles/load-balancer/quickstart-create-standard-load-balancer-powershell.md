---
title: 'Rövid útmutató: nyilvános Load Balancer létrehozása – Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre terheléselosztó használatával Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2020
ms.author: allensu
ms:custom: seodec18
ms.openlocfilehash: e11113f34e7dbb9d659944bd29e101cee009668b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133146"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-azure-powershell"></a>Gyors útmutató: nyilvános terheléselosztó létrehozása a virtuális gépek terheléselosztásához Azure PowerShell használatával

A Azure Load Balancer használatának első lépései a Azure PowerShell használatával nyilvános terheléselosztó és három virtuális gép létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Helyileg telepített Azure PowerShell vagy Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

* Elnevezett **myresourcegrouplb erőforráscsoportban**.
* A **eastus** helyen.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'

New-AzResourceGroup -Name $rg -Location $loc
```
---

# <a name="option-1-default-create-a-load-balancer-standard-sku"></a>[1. lehetőség (alapértelmezett): Load Balancer létrehozása (standard SKU)](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. 

A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használata a következőhöz:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myPublicIP**néven.
* A **myresourcegrouplb erőforráscsoportban**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

Ha egy nyilvános IP-címet szeretne létrehozni az 1. zónában, használja a következő parancsot:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Standard'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku -zone 1
```

## <a name="create-standard-load-balancer"></a>Standard Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtéri IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  * Háttérbeli IP-címkészlet, amelyben a frontend-készlet elküldi a terheléselosztási hálózati forgalmat.
  * Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  * Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-frontend-ip"></a>Előtérbeli IP-cím létrehozása

Előtér-IP-cím létrehozása a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Elnevezett **myFrontEnd**.
* Nyilvános IP- **myPublicIP**csatolva.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Háttérbeli címkészlet konfigurálása

Háttérbeli címkészlet létrehozása a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Elnevezett **myBackEndPool**.
* A virtuális gépek ehhez a háttér-készlethez csatlakoznak a hátralévő lépésekben.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe**.
* **TCP**protokoll.
* A **80**-es port figyelése.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom előtérbeli IP-konfigurációja.
* A háttérbeli IP-készlet a forgalom fogadásához.
* A szükséges forrás-és célport. 

Terheléselosztó-szabály létrehozása az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Elnevezett **: myhttprule**
* A 80-es **port** figyelése a frontend-készlet **myFrontEnd**.
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool** a 80-es **porton**keresztül. 
* A Health mintavételi **myHealthProbe**használata.
* **TCP**protokoll.
* Engedélyezze a kimenő forrás hálózati címfordítást (SNAT) a előtérbeli IP-cím használatával.


```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool
```
> [!NOTE]
> A fenti parancs a terheléselosztó háttér-készletében lévő erőforrások kimenő kapcsolatát teszi lehetővé. A kimenő kapcsolatok speciális konfigurálásához tekintse meg a **[kimenő kapcsolatokat az Azure-ban](load-balancer-outbound-connections.md)** , és **[konfigurálja a terheléselosztást és a kimenő szabályokat standard Load BALANCER az Azure CLI használatával](configure-load-balancer-outbound-cli.md)**.


### <a name="create-load-balancer-resource"></a>Terheléselosztó erőforrás létrehozása

Hozzon létre egy nyilvános Load Balancert a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Elnevezett **myLoadBalancer**
* A **eastus**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Standard'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

A virtuális gépek üzembe helyezése és a terheléselosztó tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Elnevezett **myVNet**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* **MyBackendSubnet**nevű alhálózat.
* Virtuális hálózat **10.0.0.0/16**.
* Alhálózat **10.0.0.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'

## Create subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Nyilvános IP-címek létrehozása a virtuális gépekhez

A virtuális gépek RDP-kapcsolaton keresztüli eléréséhez nyilvános IP-címekre van szükség a virtuális gépek számára. 

A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával szabványos nyilvános IP-címeket hozhat létre a következőhöz:

#### <a name="vm1"></a>VM1

* Elnevezett **myVMPubIP1**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A **eastus** helyen.
* **Szabványos** SKU.
* **Statikus** kiosztás az IP-címhez.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip1 = 'myVMPubIP1'
$sku = 'Standard'
$all = 'static'

$RdpPubIP1 = 
New-AzPublicIpAddress -Name $ip1 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

#### <a name="vm2"></a>VM2

* Elnevezett **myVMPubIP2**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A **eastus** helyen.
* **Szabványos** SKU.
* **Statikus** kiosztás az IP-címhez. 

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip2 = 'myVMPubIP2'
$sku = 'Standard'
$all = 'static'

$RdpPubIP2 = 
New-AzPublicIpAddress -Name $ip2 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

#### <a name="vm3"></a>VM3

* Elnevezett **myVMPubIP3**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A **eastus** helyen.
* **Szabványos** SKU.
* **Statikus** kiosztás az IP-címhez. 

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip3 = 'myVMPubIP3'
$sku = 'Standard'
$all = 'static'

$RdpPubIP2 = 
New-AzPublicIpAddress -Name $ip3 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Hálózati biztonságicsoport-szabály létrehozása a 3389-es porhoz

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Elnevezett **myNSGRuleRDP**.
* Az **RDP-engedélyezés**leírása.
* Hozzáférés **engedélyezése**.
* **TCP**protokoll.
* Irány **bejövő**.
* **1000**prioritás.
* Az **Internet**forrása.
* A **(*)** forrásport tartománya.
* A **(*)** célcím előtagja.
* **3389**-as célport.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleRDP'
$des = 'Allow RDP'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '1000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '3389'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Hálózati biztonságicsoport-szabály létrehozása a 80-as porhoz
Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Elnevezett **myNSGRuleHTTP**.
* A **http engedélyezésének**leírása.
* Hozzáférés **engedélyezése**.
* **TCP**protokoll.
* Irány **bejövő**.
* **2000**prioritás.
* Az **Internet**forrása.
* A **(*)** forrásport tartománya.
* A **(*)** célcím előtagja.
* **80**-as célport.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule2 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Elnevezett **myNSG**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hely **eastus**.
* Egy változóban tárolt előző lépésekben létrehozott biztonsági szabályokkal.

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1,$rule2
```

### <a name="create-network-interfaces"></a>Hálózati adapterek létrehozása

Hozzon létre három hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Elnevezett **myNicVM1**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$ip1 = 'myVMPubIP1'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM1 ##
$pub1 = 
Get-AzPublicIPAddress -Name $ip1 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -PublicIpAddress $pub1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Elnevezett **myNicVM2**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$ip2 = 'myVMPubIP2'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM2 ##
$pub2 = 
Get-AzPublicIPAddress -Name $ip2 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -PublicIpAddress $pub2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>3. VIRTUÁLIS GÉP

* Elnevezett **myNicVM3**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$ip3 = 'myVMPubIP3'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM3 ##
$pub3 = 
Get-AzPublicIPAddress -Name $ip3 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -PublicIpAddress $pub3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```
### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell
$cred = Get-Credential
```

Hozza létre a virtuális gépeket az alábbiakkal:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [Új – AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Elnevezett **myVM1**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM1**.
* A terheléselosztó **myLoadBalancer**csatolva.
* **1. zóna**.
* A **eastus** helyen.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '1'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```


#### <a name="vm2"></a>VM2

* Elnevezett **myVM2**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM2**.
* A terheléselosztó **myLoadBalancer**csatolva.
* **2. zóna**.
* A **eastus** helyen.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '2'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

#### <a name="vm3"></a>VM3

* Elnevezett **myVM3**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM3**.
* A terheléselosztó **myLoadBalancer**csatolva.
* **3. zóna**.
* A **eastus** helyen.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$zn = '3'
$loc = 'eastus'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Zone $zn -Location $loc -VM $vmConfig
```

A három virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe.

# <a name="option-2-create-a-load-balancer-basic-sku"></a>[2. lehetőség: terheléselosztó létrehozása (alapszintű SKU)](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott. További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A webalkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. 

A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használata a következőhöz:

* Hozzon létre egy szabványos, redundáns nyilvános IP-címet a **myPublicIP**néven.
* A **myresourcegrouplb erőforráscsoportban**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'
$sku = 'Basic'
$all = 'static'

$publicIp = 
New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $all -SKU $sku
```

## <a name="create-basic-load-balancer"></a>Alapszintű Load Balancer létrehozása

Ez a szakasz részletesen ismerteti a terheléselosztó következő összetevőinek létrehozását és konfigurálását:

  * Egy előtéri IP-készlet, amely a terheléselosztó bejövő hálózati forgalmát fogadja.
  * Háttérbeli IP-címkészlet, amelyben a frontend-készlet elküldi a terheléselosztási hálózati forgalmat.
  * Egy állapot-mintavétel, amely meghatározza a háttérbeli virtuálisgép-példányok állapotát.
  * Egy terheléselosztó-szabály, amely meghatározza, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között.

### <a name="create-frontend-ip"></a>Előtérbeli IP-cím létrehozása

Előtér-IP-cím létrehozása a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig):

* Elnevezett **myFrontEnd**.
* Nyilvános IP- **myPublicIP**csatolva.

```azurepowershell-interactive
## Variables for the commands ##
$fe = 'myFrontEnd'
$rg = 'MyResourceGroupLB'
$loc = 'eastus'
$pubIP = 'myPublicIP'

$publicIp = 
Get-AzPublicIpAddress -Name $pubIP -ResourceGroupName $rg

$feip = 
New-AzLoadBalancerFrontendIpConfig -Name $fe -PublicIpAddress $publicIp
```

### <a name="configure-back-end-address-pool"></a>Háttérbeli címkészlet konfigurálása

Háttérbeli címkészlet létrehozása a [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig): 

* Elnevezett **myBackEndPool**.
* A virtuális gépek ehhez a háttér-készlethez csatlakoznak a hátralévő lépésekben.

```azurepowershell-interactive
## Variable for the command ##
$be = 'myBackEndPool'

$bepool = 
New-AzLoadBalancerBackendAddressPoolConfig -Name $be
```

### <a name="create-the-health-probe"></a>Az állapotminta létrehozása

Az állapot-mintavétel ellenőrzi, hogy az összes virtuálisgép-példány képes-e hálózati forgalom küldésére. 

A rendszer eltávolít egy sikertelen mintavételi vizsgálatot tartalmazó virtuális gépet a terheléselosztó-ből. A rendszer visszaadja a virtuális gépet a terheléselosztó számára a hiba feloldásakor.

Állapot-mintavétel létrehozása az [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig):

* A virtuális gépek állapotának figyelése.
* Elnevezett **myHealthProbe**.
* **TCP**protokoll.
* A **80**-es port figyelése.

```azurepowershell-interactive
## Variables for the command ##
$hp = 'myHealthProbe'
$pro = 'http'
$port = '80'
$int = '360'
$cnt = '5'

$probe = 
New-AzLoadBalancerProbeConfig -Name $hp -Protocol $pro -Port $port -RequestPath / -IntervalInSeconds $int -ProbeCount $cnt
```

### <a name="create-the-load-balancer-rule"></a>A terheléselosztási szabály létrehozása

A terheléselosztó szabálya az alábbiakat határozza meg:

* A bejövő forgalom előtérbeli IP-konfigurációja.
* A háttérbeli IP-készlet a forgalom fogadásához.
* A szükséges forrás-és célport. 

Terheléselosztó-szabály létrehozása az [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig): 

* Elnevezett **: myhttprule**
* A 80-es **port** figyelése a frontend-készlet **myFrontEnd**.
* Elosztott terhelésű hálózati forgalom küldése a háttérbeli címkészlet **myBackEndPool** a 80-es **porton**keresztül. 
* A Health mintavételi **myHealthProbe**használata.
* **TCP**protokoll.
* Engedélyezze a kimenő forrás hálózati címfordítást (SNAT) a előtérbeli IP-cím használatával.


```azurepowershell-interactive
## Variables for the command ##
$lbr = 'myHTTPRule'
$pro = 'tcp'
$port = '80'

## $feip and $bePool are the variables from previous steps. ##

$rule = 
New-AzLoadBalancerRuleConfig -Name $lbr -Protocol $pro -Probe $probe -FrontendPort $port -BackendPort $port -FrontendIpConfiguration $feip -BackendAddressPool $bePool
```

### <a name="create-load-balancer-resource"></a>Terheléselosztó erőforrás létrehozása

Hozzon létre egy nyilvános Load Balancert a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer):

* Elnevezett **myLoadBalancer**
* A **eastus**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.

```azurepowershell-interactive
## Variables for the command ##
$lbn = 'myLoadBalancer'
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sku = 'Basic'

## $feip, $bepool, $probe, $rule are variables with configuration information from previous steps. ##

$lb = 
New-AzLoadBalancer -ResourceGroupName $rg -Name $lbn -SKU $sku -Location $loc -FrontendIpConfiguration $feip -BackendAddressPool $bepool -Probe $probe -LoadBalancingRule $rule
```

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

A virtuális gépek üzembe helyezése és a terheléselosztó tesztelése előtt hozza létre a támogató virtuális hálózati erőforrásokat.

### <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

* Elnevezett **myVNet**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* **MyBackendSubnet**nevű alhálózat.
* Virtuális hálózat **10.0.0.0/16**.
* Alhálózat **10.0.0.0/24**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$sub = 'myBackendSubnet'
$spfx = '10.0.0.0/24'
$vnm = 'myVNet'
$vpfx = '10.0.0.0/16'

## Create subnet config ##
$subnetConfig = 
New-AzVirtualNetworkSubnetConfig -Name $sub -AddressPrefix $spfx

## Create the virtual network ##
$vnet = 
New-AzVirtualNetwork -ResourceGroupName $rg -Location $loc -Name $vnm -AddressPrefix $vpfx -Subnet $subnetConfig
```
### <a name="create-public-ip-addresses-for-the-vms"></a>Nyilvános IP-címek létrehozása a virtuális gépekhez

A virtuális gépek RDP-kapcsolaton keresztüli eléréséhez nyilvános IP-címekre van szükség a virtuális gépek számára. 

A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) használatával szabványos nyilvános IP-címeket hozhat létre a következőhöz:

#### <a name="vm1"></a>VM1

* Elnevezett **myVMPubIP1**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A **eastus** helyen.
* **Szabványos** SKU.
* **Statikus** kiosztás az IP-címhez.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip1 = 'myVMPubIP1'
$sku = 'Basic'
$all = 'static'

$RdpPubIP1 = 
New-AzPublicIpAddress -Name $ip1 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

#### <a name="vm2"></a>VM2

* Elnevezett **myVMPubIP2**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A **eastus** helyen.
* **Szabványos** SKU.
* **Statikus** kiosztás az IP-címhez. 

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip2 = 'myVMPubIP2'
$sku = 'Basic'
$all = 'static'

$RdpPubIP2 = 
New-AzPublicIpAddress -Name $ip2 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

#### <a name="vm3"></a>VM3

* Elnevezett **myVMPubIP3**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A **eastus** helyen.
* **Szabványos** SKU.
* **Statikus** kiosztás az IP-címhez. 

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$ip3 = 'myVMPubIP3'
$sku = 'Basic'
$all = 'static'

$RdpPubIP2 = 
New-AzPublicIpAddress -Name $ip3 -ResourceGroupName $rg -Location $loc -SKU $sku -AllocationMethod $all
```

### <a name="create-network-security-group"></a>Hálózati biztonsági csoport létrehozása
Hozzon létre hálózati biztonsági csoportot a virtuális hálózat bejövő kapcsolatainak meghatározásához.

#### <a name="create-a-network-security-group-rule-for-port-3389"></a>Hálózati biztonságicsoport-szabály létrehozása a 3389-es porhoz

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Elnevezett **myNSGRuleRDP**.
* Az **RDP-engedélyezés**leírása.
* Hozzáférés **engedélyezése**.
* **TCP**protokoll.
* Irány **bejövő**.
* **1000**prioritás.
* Az **Internet**forrása.
* A **(*)** forrásport tartománya.
* A **(*)** célcím előtagja.
* **3389**-as célport.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleRDP'
$des = 'Allow RDP'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '1000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '3389'

$rule1 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group-rule-for-port-80"></a>Hálózati biztonságicsoport-szabály létrehozása a 80-as porhoz
Hozzon létre egy hálózati biztonsági csoportra vonatkozó szabályt a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig):

* Elnevezett **myNSGRuleHTTP**.
* A **http engedélyezésének**leírása.
* Hozzáférés **engedélyezése**.
* **TCP**protokoll.
* Irány **bejövő**.
* **2000**prioritás.
* Az **Internet**forrása.
* A **(*)** forrásport tartománya.
* A **(*)** célcím előtagja.
* **80**-as célport.

```azurepowershell-interactive
## Variables for command ##
$rnm = 'myNSGRuleHTTP'
$des = 'Allow HTTP'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '2000'
$spfx = 'Internet'
$spr = '*'
$dpfx = '*'
$dpr = '80'

$rule2 = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $des -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix $spfx -SourcePortRange $spr -DestinationAddressPrefix $dpfx -DestinationPortRange $dpr
```

#### <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Hozzon létre egy hálózati biztonsági csoportot a [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup):

* Elnevezett **myNSG**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hely **eastus**.
* Egy változóban tárolt előző lépésekben létrehozott biztonsági szabályokkal.

```azurepowershell
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nmn = 'myNSG'

## $rule1 and $rule2 are variables with configuration information from the previous steps. ##
$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rg -Location $loc -Name $nmn -SecurityRules $rule1,$rule2
```

### <a name="create-network-interfaces"></a>Hálózati adapterek létrehozása

Hozzon létre három hálózati adaptert a [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface):

#### <a name="vm-1"></a>VM 1

* Elnevezett **myNicVM1**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic1 = 'myNicVM1'
$vnt = 'myVNet'
$ip1 = 'myVMPubIP1'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM1 ##
$pub1 = 
Get-AzPublicIPAddress -Name $ip1 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM1 ##
$nicVM1 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic1 -PublicIpAddress $pub1 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-2"></a>VM 2

* Elnevezett **myNicVM2**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic2 = 'myNicVM2'
$vnt = 'myVNet'
$ip2 = 'myVMPubIP2'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM2 ##
$pub2 = 
Get-AzPublicIPAddress -Name $ip2 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM2 ##
$nicVM2 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic2 -PublicIpAddress $pub2 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

#### <a name="vm-3"></a>3. VIRTUÁLIS GÉP

* Elnevezett **myNicVM3**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A hely **eastus**.
* A virtuális hálózat **myVNet**.
* Az alhálózat **myBackendSubnet**.
* A hálózati biztonsági csoport **myNSG**.
* Csatolva a terheléselosztó **myLoadBalancer** a **myBackEndPool**-ben.

```azurepowershell-interactive
## Variables for command ##
$rg = 'myResourceGroupLB'
$loc = 'eastus'
$nic3 = 'myNicVM3'
$vnt = 'myVNet'
$ip3 = 'myVMPubIP3'
$lb = 'myLoadBalancer'
$ngn = 'myNSG'

## Command to get virtual network configuration. ##
$vnet = 
Get-AzVirtualNetwork -Name $vnt -ResourceGroupName $rg

## Command to get public ip address for VM3 ##
$pub3 = 
Get-AzPublicIPAddress -Name $ip3 -ResourceGroupName $rg

## Command to get load balancer configuration
$bepool = 
Get-AzLoadBalancer -Name $lb -ResourceGroupName $rg | Get-AzLoadBalancerBackendAddressPoolConfig

## Command to get network security group configuration ##
$nsg = 
Get-AzNetworkSecurityGroup -Name $ngn -ResourceGroupName $rg

## Command to create network interface for VM3 ##
$nicVM3 = 
New-AzNetworkInterface -ResourceGroupName $rg -Location $loc -Name $nic3 -PublicIpAddress $pub3 -LoadBalancerBackendAddressPool $bepool -NetworkSecurityGroup $nsg -Subnet $vnet.Subnets[0]
```

### <a name="create-availability-set-for-virtual-machines"></a>Rendelkezésre állási csoport létrehozása a virtuális gépekhez

A [New-AzAvailabilitySet](/powershell/module/az.compute/new-azvm) használatával hozzon létre egy rendelkezésre állási készletet a virtuális gépek számára:

* Elnevezett **myAvSet**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* A **eastus** helyen.

```azurepowershell-interactive
## Variables used for the command. ##
$rg = 'myResourceGroupLB'
$avs = 'myAvSet'
$loc = 'eastus'

New-AzAvailabilitySet -ResourceGroupName $rg -Name $avs -Location $loc
```

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell
$cred = Get-Credential
```

Hozza létre a virtuális gépeket az alábbiakkal:

* [New-AzVM](/powershell/module/az.compute/new-azvm)
* [Új – AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
* [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
* [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
* [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


#### <a name="vm1"></a>VM1

* Elnevezett **myVM1**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM1**.
* A terheléselosztó **myLoadBalancer**csatolva.
* A **eastus** helyen.
* Az **AlmyAvSeton** rendelkezésre állási csoportba.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM1'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM1 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM1.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```


#### <a name="vm2"></a>VM2

* Elnevezett **myVM2**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM2**.
* A terheléselosztó **myLoadBalancer**csatolva.
* A **eastus** helyen.
* Az **AlmyAvSeton** rendelkezésre állási csoportba.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM2'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM2 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM2.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

#### <a name="vm3"></a>VM3

* Elnevezett **myVM3**.
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.
* Csatolva a hálózati adapter **myNicVM3**.
* A terheléselosztó **myLoadBalancer**csatolva.
* A **eastus** helyen.
* Az **AlmyAvSeton** rendelkezésre állási csoportba.

```azurepowershell-interactive
## Variables used for command. ##
$rg = 'myResourceGroupLB'
$vm = 'myVM3'
$siz = 'Standard_DS1_v2'
$pub = 'MicrosoftWindowsServer'
$off = 'WindowsServer'
$sku = '2019-Datacenter'
$ver = 'latest'
$loc = 'eastus'
$avs = 'myAvSet'

## Create a virtual machine configuration. $cred and $nicVM3 are variables with configuration from the previous steps. ##

$vmConfig = 
New-AzVMConfig -VMName $vm -VMSize $siz | Set-AzVMOperatingSystem -Windows -ComputerName $vm -Credential $cred | Set-AzVMSourceImage -PublisherName $pub -Offer WindowsServer -Skus $sku -Version $ver | Add-AzVMNetworkInterface -Id $nicVM3.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig -AvailabilitySetName $avs
```

A három virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe.

---

### <a name="install-iis-with-a-custom-web-page"></a>Az IIS telepítése egyéni weboldallal

Telepítse az IIS-t egy egyéni weboldallal mindkét háttérbeli virtuális gépen a következőképpen:

1. Szerezze be a három virtuális gép nyilvános IP-címét a [Get-AzPublicIPAddress](/powershell/module/az.compute/get-azpublicipaddress)használatával.

   ```azurepowershell-interactive
   ## Variables for commands. ##
   $rg = 'myResourceGroupLB'
   $ip1 = 'myVMPubIP1'
   $ip2 = 'myVMPubIP2'
   $ip3 = 'myVMPubIP3'

   ## VM1 ##
   (Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ip1).IpAddress

   ## VM2 ##
   (Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ip2).IpAddress

   ## VM3 ##
   (Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ip3).IpAddress
   ```
   
2. Hozzon létre távoli asztali kapcsolatokat a **myVM1**, a **MyVM2**és a **myVM3** használatával a virtuális gépek nyilvános IP-címeivel.

3. Adja meg az egyes virtuális gépek hitelesítő adatait az RDP-munkamenet elindításához.

4. Indítsa el a Windows PowerShellt az egyes virtuális gépeken, és az alábbi parancsokkal telepítse az IIS-kiszolgálót, és frissítse az alapértelmezett htm-fájlt.

   ```powershell
   # Install IIS
   Install-WindowsFeature -name Web-Server -IncludeManagementTools

   # Remove default htm file
   remove-item  C:\inetpub\wwwroot\iisstart.htm
    
   #Add custom htm file
   Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from host " + $env:computername)
   ```

5. Az RDP-kapcsolatok lezárása a **myVM1**, a **MyVM2**és a **myVM3**.


## <a name="test-load-balancer"></a>Terheléselosztó tesztelése
A terheléselosztó nyilvános IP-címének lekéréséhez használja a [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):

* Elnevezett **myPublicIP**
* Az erőforráscsoport **myresourcegrouplb erőforráscsoportban**.

```azurepowershell-interactive
## Variables for command. ##
$rg = 'myResourceGroupLB'
$ipn = 'myPublicIP'

Get-AzPublicIPAddress -ResourceGroupName $rg -Name $ipn | select IpAddress
```

Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába.

![Terheléselosztó tesztelése](media/quickstart-create-basic-load-balancer-powershell/load-balancer-test.png)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolítható az erőforráscsoport, a terheléselosztó és az összes 

```azurepowershell-interactive
## Variable for command. ##
$rg = 'myResourceGroupLB'

Remove-AzResourceGroup -Name $rg
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban

* Létrehozott egy standard vagy alapszintű nyilvános Load balancert
* Csatlakoztatott virtuális gépek. 
* Konfigurálta a terheléselosztó forgalmi szabályát és az állapot-mintavételt.
* Tesztelte a terheléselosztó.

Ha többet szeretne megtudni a Azure Load Balancerről, folytassa a [mi Azure Load Balancer?](load-balancer-overview.md) és [Load Balancer a gyakori kérdések](load-balancer-faqs.md)című témakört.

További információ a [Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md).
