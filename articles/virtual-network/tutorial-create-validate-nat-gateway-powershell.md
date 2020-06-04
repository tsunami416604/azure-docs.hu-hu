---
title: 'Oktatóanyag: NAT-átjáró létrehozása és tesztelése – Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre NAT-átjárót a Azure PowerShell segítségével, és tesztelheti a NAT szolgáltatást
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: d674531a736e3d8f63f9d740758be8447df7d495
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343421"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Oktatóanyag: NAT-átjáró létrehozása a Azure PowerShell használatával és a NAT szolgáltatás tesztelése

Ebben az oktatóanyagban létrehoz egy NAT-átjárót az Azure-beli virtuális gépek kimenő kapcsolatának biztosításához. A NAT-átjáró teszteléséhez üzembe kell helyeznie egy forrás-és cél virtuális gépet. Tesztelje a NAT-átjárót úgy, hogy kimenő kapcsolatokat végez a nyilvános IP-címhez. Ezek a kapcsolatok a forrásról a cél virtuális gépre kerülnek. Ez az oktatóanyag az egyszerűség kedvéért két különböző virtuális hálózatban helyezi üzembe a forrás-és a célhelyet.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ezt az oktatóanyagot a Azure Cloud Shell használatával vagy a megfelelő parancsok helyi futtatásával végezheti el.  Ha még soha nem használta Azure Cloud Shellt, [Jelentkezzen be most](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myResourceGroupNAT** nevű erőforráscsoportot a **eastus2** helyen:


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az Internet eléréséhez szüksége lesz egy vagy több nyilvános IP-címre a NAT-átjáró számára. A [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) használatával hozzon létre egy **MYPUBLICIPSOURCE** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben. A parancs eredménye egy **$publicIPsource** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pips = 'myPublicIPsource'
$alm = 'Static'
$sku = 'Standard'

$publicIPsource = 
New-AzPublicIpAddress -Name $pips -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

 A [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) használatával hozzon létre egy **MYPUBLICIPPREFIXSOURCE** nevű nyilvános IP-előtag-erőforrást a **myResourceGroupNAT**-ben.  A parancs eredménye egy **$publicIPPrefixsource** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró erőforrásának létrehozása

Ez a szakasz részletesen ismerteti, hogyan hozhatja létre és konfigurálhatja a NAT-szolgáltatás következő összetevőit a NAT-átjáró erőforrásának használatával:
  - Egy nyilvános IP-címkészlet és egy nyilvános IP-előtag, amelyet a NAT-átjáró erőforrása lefordított kimenő folyamatokhoz használ.
  - Módosítsa az üresjárati időkorlátot az alapértelmezett 4 perctől 10 percre.

Hozzon létre egy globális Azure NAT-átjárót a [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway). A parancs eredménye létrehoz egy **myNATgateway** nevű átjáró-erőforrást, amely a nyilvános IP- **myPublicIPsource** és a nyilvános IP-előtag **myPublicIPprefixsource**használja. Az Üresjárati időkorlát 10 percre van beállítva.  A parancs eredménye egy **$natGateway** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

Ezen a ponton a NAT-átjáró működik, és az összes hiányzó beállítással konfigurálható, hogy a virtuális hálózat mely alhálózatai használják azt.

## <a name="prepare-the-source-for-outbound-traffic"></a>A forrás előkészítése a kimenő forgalomhoz

Végigvezeti a teljes tesztkörnyezet beállításán. A NAT-átjáró ellenőrzéséhez nyílt forráskódú eszközök használatával állítson be egy tesztet. Kezdjük a forrással, amely a korábban létrehozott NAT-átjárót fogja használni.

### <a name="configure-virtual-network-for-source"></a>Virtuális hálózat konfigurálása a forráshoz

Hozza létre a virtuális hálózatot, és rendelje hozzá az alhálózatot az átjáróhoz.

Hozzon létre egy **myVnetsource** nevű virtuális hálózatot egy **mySubnetsource** nevű alhálózattal a New [-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) használatával a **myResourceGroupNAT** [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)használatával. A virtuális hálózat IP-címe **192.168.0.0/16**. A virtuális hálózaton belüli alhálózat **192.168.0.0/24**.  A parancsok eredményét a rendszer **$subnetsource** és az **$vnetsource** későbbi használatra fogja tárolni.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$sbn = 'mySubnetsource'
$spfx = '192.168.0.0/24'
$vnm = 'myVnetsource'
$vpfx = '192.168.0.0/16'
$loc = 'eastus2'

$subnetsource = 
New-AzVirtualNetworkSubnetConfig -Name $sbn -AddressPrefix $spfx -NatGateway $natGateway

$vnetsource = 
New-AzVirtualNetwork -Name $vnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnet
```

A NAT szolgáltatás mostantól az összes internetes célhelyre irányuló kimenő forgalmat használja.  Nem szükséges konfigurálni a UDR.

A NAT-átjáró teszteléséhez létre kell hoznia egy forrásoldali virtuális gépet.  Egy nyilvános IP-cím típusú erőforrást egy példány szintű nyilvános IP-címként rendelünk hozzá a virtuális géphez a külvilágtól való hozzáféréshez. Ez a címe csak a teszthez való hozzáféréshez használatos.  Bemutatjuk, hogy a NAT szolgáltatás milyen elsőbbséget élvez a többi kimenő lehetőséggel szemben.

Azt is megteheti, hogy nyilvános IP-cím nélkül hozza létre ezt a virtuális gépet, és egy olyan virtuális gépet hoz létre, amelyet Jumpbox használ nyilvános IP-cím nélkül.

### <a name="create-public-ip-for-source-vm"></a>Nyilvános IP-cím létrehozása a forrásként szolgáló virtuális gép számára

Létrehozunk egy nyilvános IP-címet, amelyet a virtuális gép eléréséhez kíván használni.  A [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) használatával hozzon létre egy **MYPUBLICIPVM** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.  A parancs eredménye egy **$publicIpsourceVM** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Hozzon létre egy NSG, és tegye elérhetővé SSH-végpontot a virtuális géphez

Mivel a standard nyilvános IP-címek alapértelmezetten "biztonságosak", hozzunk létre egy NSG, amely engedélyezi a bejövő hozzáférést az SSH-hoz. A NAT szolgáltatás a flow irányát ismeri. Ez a NSG nem használható a kimenő forgalomhoz, ha a NAT-átjáró ugyanazon az alhálózaton van konfigurálva. A [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) használatával hozzon létre egy **myNSGsource**nevű NSG-erőforrást. A [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) használatával hozzon létre egy NSG-szabályt az **SSH** -hozzáféréshez a **myResourceGroupNAT**-ben. A parancs eredményét az **$nsgsource** nevű változó fogja tárolni későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$rnm = 'ssh'
$rdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$nsnm = 'myNSGsource'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$nsgsource = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-source-vm"></a>Hálózati adapter létrehozása forrásként szolgáló virtuális géphez

Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) nevű **myNicsource**. Ez a parancs a nyilvános IP-címet és a hálózati biztonsági csoportot rendeli hozzá. A parancs eredménye egy **$nicsource** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>Forrásként szolgáló virtuális gép létrehozása

#### <a name="create-ssh-key-pair"></a>SSH-kulcspár létrehozása

A rövid útmutató elvégzéséhez egy SSH-kulcspárra lesz szüksége. Ha már rendelkezésére áll egy SSH-kulcspár, kihagyhatja ezt a lépést.

Ssh-keygen használatával hozzon létre egy SSH-kulcspárt.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Az SSH-kulcspárok létrehozásáról, többek között a PuTTy használatáról az [SSH-kulcsok Windowsban való használatát](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) ismertető cikkben talál részletesebb információt.

Ha a Cloud Shell használatával hozza létre az SSH-kulcspárt, a rendszer a kulcspárt egy tároló rendszerképében tárolja. A [rendszer automatikusan létrehozza](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)ezt a Storage-fiókot. Ne törölje a Storage-fiókot vagy a fájlmegosztást a-ben, amíg a kulcsok beolvasása nem sikerült.

#### <a name="create-vm-configuration"></a>Virtuális gép konfigurációjának létrehozása

A PowerShellben a virtuális gépek létrehozáshoz egy konfigurációt kell létrehozni, amely a használni kívánt rendszerképpel, mérettel és hitelesítési beállításokkal egyező beállításokkal rendelkezik. A rendszer ezután ezzel a konfigurációval hozza létre a virtuális gépet.

Határozza meg az SSH hitelesítő adatokat, az operációs rendszer adatait és a virtuális gép méretét. Ebben a példában az SSH-kulcsot a ~/.ssh/id_rsa. pub tárolja.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmn = 'myVMsource'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigsource = 
New-AzVMConfig -VMName $vmn -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigsource -Linux -ComputerName $vmn -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigsource -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigsource -Id $nicsource.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigsource -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
A konfigurációs definíciók kombinálásával hozzon létre egy **myVMsource** nevű virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) a **myResourceGroupNAT**-ben.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Amíg a parancs azonnal visszatér, eltarthat néhány percig, amíg a virtuális gép üzembe kerül.

## <a name="prepare-destination-for-outbound-traffic"></a>Cél előkészítése a kimenő forgalomhoz

Most létrehozunk egy célhelyet a NAT szolgáltatás által lefordított kimenő forgalomhoz, hogy tesztelni lehessen.

### <a name="configure-virtual-network-for-destination"></a>Virtuális hálózat konfigurálása célhelyként

Létre kell hozni egy virtuális hálózatot, amelyben a cél virtuális gép lesz.  Ezek a parancsok ugyanazok a lépések, mint a forrásoldali virtuális gép esetén. Kis változtatások lettek hozzáadva a cél végpont számára.

Hozzon létre egy **myVnetdestination** nevű virtuális hálózatot egy **mySubnetdestination** nevű alhálózattal a New [-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) használatával a **myResourceGroupNAT** [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)használatával. A virtuális hálózat IP-címe **192.168.0.0/16**. A virtuális hálózaton belüli alhálózat **192.168.0.0/24**.  A parancsok eredményét a rendszer **$subnetdestination** és az **$vnetdestination** későbbi használatra fogja tárolni.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sbdn = 'mySubnetdestination'
$spfx = '192.168.0.0/24'
$vdn = 'myVnetdestination'
$vpfx = '192.168.0.0/16'

$subnetdestination = 
New-AzVirtualNetworkSubnetConfig -Name $sbdn -AddressPrefix $spfx

$vnetdestination = 
New-AzVirtualNetwork -Name $vdn -ResourceGroupName $rsg -Location $loc -AddressPrefix $vpfx -Subnet $subnetdestination
```

### <a name="create-public-ip-for-destination-vm"></a>Nyilvános IP-cím létrehozása a cél virtuális géphez

Létrehozunk egy nyilvános IP-címet, amely a cél virtuális gép elérésére szolgál.  A [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) használatával hozzon létre egy **MYPUBLICIPDESTINATIONVM** nevű nyilvános IP-cím-erőforrást a **myResourceGroupNAT**-ben.  A parancs eredménye egy **$publicIpdestinationVM** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>Hozzon létre egy NSG, és tegye elérhetővé SSH-és HTTP-végpontot a virtuális gépen

A standard nyilvános IP-címek alapértelmezés szerint "biztonságosak", egy NSG hozunk létre, amely lehetővé teszi az SSH bejövő hozzáférését. A [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) használatával hozzon létre egy **myNSGdestination**nevű NSG-erőforrást. A [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) használatával hozzon létre egy NSG-szabályt az **SSH-hozzáféréshez.**  A [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) használatával hozzon létre egy NSG-szabályt a **http-hozzáféréshez.** Mindkét szabály a **myResourceGroupNAT**-ben lesz létrehozva. A parancs eredménye egy **$nsgdestination** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$snm = 'ssh'
$sdsc = 'SSH access'
$acc = 'Allow'
$prt = 'Tcp'
$dir = 'Inbound'
$hnm = 'http'
$hdsc = 'HTTP access'
$nsnm = 'myNSGdestination'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $snm -Description $sdsc -Access $acc -Protocol $prt -Direction $dir -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 22

$httprule = 
New-AzNetworkSecurityRuleConfig -Name $hnm -Description $hdsc -Access $acc -Protocol $prt -Direction $dir -Priority 101 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80

$nsgdestination = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $nsnm -Location $loc -SecurityRules $sshrule,$httprule
```

### <a name="create-nic-for-destination-vm"></a>Hálózati adapter létrehozása a cél virtuális géphez

Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) nevű **myNicdestination**. Ez a parancs társítva lesz a nyilvános IP-címhez és a hálózati biztonsági csoporthoz. A parancs eredménye egy **$nicdestination** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>Cél virtuális gép létrehozása

#### <a name="create-vm-configuration"></a>Virtuális gép konfigurációjának létrehozása

A PowerShellben a virtuális gépek létrehozáshoz egy konfigurációt kell létrehozni, amely a használni kívánt rendszerképpel, mérettel és hitelesítési beállításokkal egyező beállításokkal rendelkezik. A rendszer ezután ezzel a konfigurációval hozza létre a virtuális gépet.

Határozza meg az SSH hitelesítő adatokat, az operációs rendszer adatait és a virtuális gép méretét. Ebben a példában az SSH-kulcsot a ~/.ssh/id_rsa. pub tárolja.

```azurepowershell-interactive
# Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$vmd = 'myVMdestination'
$vms = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$skus = '18.04-LTS'
$ver = 'latest'

$vmConfigdestination = New-AzVMConfig -VMName $vmd -VMSize $vms

Set-AzVMOperatingSystem -VM $vmConfigdestination -Linux -ComputerName $vmd -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfigdestination -PublisherName $pub -Offer $off -Skus $skus -Version $ver

Add-AzVMNetworkInterface -VM $vmConfigdestination -Id $nicdestination.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmConfigdestination -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
A konfigurációs definíciók kombinálásával hozzon létre egy **myVMdestination** nevű virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) a **myResourceGroupNAT**-ben.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Amíg a parancs azonnal visszatér, eltarthat néhány percig, amíg a virtuális gép üzembe kerül.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Webkiszolgáló és tesztelési tartalom előkészítése a célként megadott virtuális gépen

Először fel kell deríteni a cél virtuális gép IP-címét.  A virtuális gép nyilvános IP-címének lekéréséhez használja a [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy azt a következő lépésekben használhatja. Jelezze, hogy ez a cél virtuális gép.

### <a name="sign-in-to-destination-vm"></a>Bejelentkezés a cél virtuális gépre

Az SSH hitelesítő adatait az előző művelet során a Cloud Shell kell tárolnia.  Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) a böngészőben. Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre. 

```bash
ssh azureuser@<ip-address-destination>
```

Másolja ki és illessze be az alábbi parancsokat a bejelentkezés után.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Ezek a parancsok frissítik a virtuális gépet, telepítik az Nginx-et, és létrehozunk egy 100-es kilobájtos fájlt. Ezt a fájlt a rendszer a forrás virtuális gépről a NAT szolgáltatás használatával kéri le.

A célként megadott virtuális géppel zárjuk be az SSH-munkamenetet.

## <a name="prepare-test-on-source-vm"></a>Teszt előkészítése a forrás virtuális gépen

Először fel kell deríteni a forrás virtuális gép IP-címét.  A virtuális gép nyilvános IP-címének lekéréséhez használja a [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest). 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy azt a következő lépésekben használhatja. Jelezze, hogy ez a forrás virtuális gép.

### <a name="log-into-source-vm"></a>Bejelentkezés a forrás virtuális gépre

Az SSH hitelesítő adatait a rendszer a Cloud Shell tárolja. Nyisson meg egy új lapot a böngészőben [Azure Cloud Shellhoz](https://shell.azure.com) .  Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre. 

```bash
ssh azureuser@<ip-address-source>
```

Másolja és illessze be a következő parancsokat a NAT szolgáltatás tesztelésének előkészítéséhez.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Ezek a parancsok frissítik a virtuális gépet, telepítheti a Go-t, telepítheti a [Hey](https://github.com/rakyll/hey) alkalmazást a githubról, és frissítheti a rendszerhéj-környezetet.

Most már készen áll a NAT szolgáltatás tesztelésére.

## <a name="validate-nat-service"></a>NAT szolgáltatás ellenőrzése

Ha bejelentkezett a forrás virtuális gépre, a **curl** és a **Hey** használatával is létrehozhat KÉRÉSEKET a cél IP-címhez.

Az 100-kilobájtos fájl beolvasásához használja a curlt.  Cserélje le az **\<ip-address-destination>** alábbi példában a korábban másolt cél IP-címet.  A **--output** paraméter azt jelzi, hogy a beolvasott fájl el lesz vetve.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

A **Hey**használatával több kérelem is létrehozható. Ismét cserélje le **\<ip-address-destination>** a helyére a korábban másolt cél IP-címet.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Ezzel a paranccsal az 100-es kérések, 10 párhuzamosan, 30 másodperces időkorláttal fognak létrejönni. A TCP-kapcsolatok nem lesznek újra felhasználva.  Minden kérelem 100 KB-ot fog beolvasni.  A Futtatás végén a **Hey** jelentést készít arról, hogy milyen jól működik a NAT szolgáltatás.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) paranccsal távolíthatja el az erőforráscsoportot és a benne található összes erőforrást.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy NAT-átjárót, létrehozta a forrás és a cél virtuális gépet, majd tesztelte a NAT-átjárót.

Tekintse át a Azure Monitor mérőszámait a NAT szolgáltatás működésének megtekintéséhez. Problémák diagnosztizálása, például az elérhető SNAT-portok erőforrás-kimerülése.  A SNAT-portok erőforrás-kimerülése könnyen kezelhető további nyilvános IP-címek vagy nyilvános IP-előtag-erőforrások hozzáadásával vagy mindkettővel.

- Tudnivalók a [Virtual Network NAT](./nat-overview.md) -ról
- További információ a [NAT-átjáró erőforrásáról](./nat-gateway-resource.md).
- Rövid útmutató a [NAT-átjáró erőforrásának Azure CLI](./quickstart-create-nat-gateway-cli.md)-vel történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure PowerShell használatával](./quickstart-create-nat-gateway-powershell.md)történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure Portal használatával](./quickstart-create-nat-gateway-portal.md)történő üzembe helyezéséhez.

> [!div class="nextstepaction"]

