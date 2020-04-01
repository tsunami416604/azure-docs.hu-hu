---
title: 'Oktatóanyag: NAT-átjáró létrehozása és tesztelése – Azure PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre NAT-átjárót az Azure PowerShell használatával, és hogyan tesztelhatheti a NAT-szolgáltatást
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to test a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 61cda5e61d14c4eeaf2d88483603707598b1c911
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202229"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell-and-test-the-nat-service"></a>Oktatóanyag: NAT-átjáró létrehozása az Azure PowerShell használatával és a NAT-szolgáltatás tesztelése

Ebben az oktatóanyagban egy NAT-átjárót hoz létre, amely kimenő kapcsolatot biztosít az Azure-beli virtuális gépek számára. A NAT-átjáró teszteléséhez telepítsen egy forrás- és célvirtuális gépet. A NAT-átjárót nyilvános IP-címhez való kimenő kapcsolatokkal tesztelheti. Ezek a kapcsolatok a forrástól a cél virtuális gépig érkeznek. Ez az oktatóanyag az egyszerűség kedvéért két különböző virtuális hálózatban telepíti a forrást és a célt.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ezt az oktatóanyagot az Azure Cloud Shell használatával befejezheti, vagy helyileg futtathatja a megfelelő parancsokat.  Ha még soha nem használta az Azure Cloud Shellt, [most jelentkezzen be.](https://shell.azure.com)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](https://docs.microsoft.com/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy **myResourceGroupNAT** nevű erőforráscsoportot az **eastus2** helyen:


```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>A NAT-átjáró létrehozása

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

Az internet eléréséhez egy vagy több nyilvános IP-címre van szükség a NAT-átjáróhoz. A [New-AzPublicIpAddress használatával](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) hozzon létre egy **myPublicIPsource** nevű nyilvános IP-címerőforrást a **myResourceGroupNAT-ban.** A parancs eredménye egy **$publicIPsource** nevű változóban lesz tárolva későbbi használatra.

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

 A [New-AzPublicIpPrefix](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest) segítségével hozzon létre egy nyilvános IP-előtag-erőforrást, amelynek neve **myPublicIPprefixsource** a **myResourceGroupNAT-ban.**  A parancs eredménye egy **$publicIPPrefixsource** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$prips = 'mypublicIPprefixsource'

$publicIPPrefixsource = 
New-AzPublicIpPrefix -Name $prips -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró-erőforrás létrehozása

Ez a szakasz részletezi, hogyan hozhat létre és konfigurálhat a NAT szolgáltatás következő összetevőit a NAT átjáróerőforrás használatával:
  - Nyilvános IP-készlet és nyilvános IP-előtag a NAT átjáró erőforrás által lefordított kimenő folyamatokhoz.
  - Módosítsa az alapjárati időoutot az alapértelmezett 4 percről 10 percre.

Hozzon létre egy globális Azure NAT-átjárót a [New-AzNatGateway segítségével.](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway) Ennek a parancsnak az eredménye egy **myNATgateway** nevű átjáró-erőforrást hoz létre, amely a nyilvános IP-címet használja **myPublicIPsource** és a nyilvános IP-előtag **myPublicIPprefixsource**. Az tétlen időjárat 10 percre van állítva.  A parancs eredménye egy **$natGateway** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$nnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $nnm -ResourceGroupName $rsg -PublicIpAddress $publicIPsource -PublicIpPrefix $publicIPPrefixsource -Location $loc -Sku $sku -IdleTimeoutInMinutes 10      
  ```

Ezen a ponton a NAT-átjáró működőképes, és csak azt kell konfigurálni, hogy a virtuális hálózat mely alhálózatai használják azt.

## <a name="prepare-the-source-for-outbound-traffic"></a>A forrás előkészítése a kimenő forgalomra

Végigvezetjük a teljes tesztkörnyezet beállításán. A NAT-átjáró ellenőrzéséhez nyílt forráskódú eszközökkel állíthat be tesztet. Kezdjük a forrással, amely a korábban létrehozott NAT átjárót fogja használni.

### <a name="configure-virtual-network-for-source"></a>Virtuális hálózat konfigurálása forráshoz

Hozza létre a virtuális hálózatot, és társítsa az alhálózatot az átjáróhoz.

Hozzon létre egy **myVnetsource** nevű virtuális hálózatot **egy mySubnetsource** nevű alhálózattal a [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) használatával a **myResourceGroupNAT-ban** a [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)használatával. A virtuális hálózat IP-címterülete **192.168.0.0/16**. A virtuális hálózaton belüli **alhálózat: 192.168.0.0/24**.  A parancsok eredményét a **$subnetsource** és **$vnetsource** nevű változók tárolják későbbi használatra.

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

Az internetes célállomásokra irányuló összes kimenő forgalom most már a NAT szolgáltatást használja.  Nem szükséges az UDR konfigurálása.

Mielőtt tesztelhetnénk a NAT-átjárót, létre kell hoznunk egy forrás virtuális gép.  A virtuális gép kívülről való eléréséhez egy nyilvános IP-címerőforrást rendelünk hozzá példányszintű nyilvános IP-címként. Ez a cím csak a teszthez való hozzáféréshez használható.  Bemutatjuk, hogy a NAT szolgáltatás hogyan élvez elsőbbséget a többi kimenő beállítással szemben.

A virtuális gépet nyilvános IP-cím nélkül is létrehozhatja, és létrehozhat egy másik virtuális gépet, amelyet nyilvános IP-cím nélkül használhat gyakorlatként.

### <a name="create-public-ip-for-source-vm"></a>Nyilvános IP létrehozása a forrás virtuális géphez

Létrehozunk egy nyilvános IP-t a virtuális gép eléréséhez.  A [New-AzPublicIpAddress használatával](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) hozzon létre egy **myResourceGroupNAT** nevű nyilvános IP-címerőforrást. **myResourceGroupNAT**  A parancs eredménye egy **$publicIpsourceVM** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pipvm = 'myPublicIpsourceVM'
$alm = 'Static'

$publicIpsourceVM = 
New-AzPublicIpAddress -Name $pipvm -ResourceGroupName $rsg -AllocationMethod $alm -Location $loc -sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>NSG létrehozása és SSH-végpont elérhetővé

Mivel a szabványos nyilvános IP-címek "alapértelmezés szerint biztonságosak", létrehozunk egy NSG-t, amely lehetővé teszi az ssh bejövő hozzáférését. A NAT szolgáltatás áramlásirány-tudatos. Ez az NSG nem lesz használható a kimenő, ha nat átjáró ugyanazon az alhálózaton van konfigurálva. A [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) segítségével hozzon létre egy **myNSGsource nevű NSG-erőforrást.** A [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) segítségével hozzon létre egy NSG-szabályt **az ssh** nevű SSH-hozzáféréshez a **myResourceGroupNAT-ban.** A parancs eredménye **a $nsgsource** nevű változóban lesz tárolva későbbi használatra.

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

### <a name="create-nic-for-source-vm"></a>Hálózati adapter létrehozása a forrásvirtuális géphez

Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) nevű **myNicsource**. Ez a parancs a nyilvános IP-címet és a hálózati biztonsági csoportot fogja társítani. A parancs eredménye egy **$nicsource** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nin = 'myNicsource'

$nicsource = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nin -NetworkSecurityGroupID $nsgsource.Id -PublicIPAddressID $publicIPVMsource.Id -SubnetID $vnetsource.Subnets[0].Id -Location $loc
```

### <a name="create-a-source-vm"></a>Forrásvirtuális gép létrehozása

#### <a name="create-ssh-key-pair"></a>SSH-kulcspár létrehozása

A rövid útmutató elvégzéséhez egy SSH-kulcspárra lesz szüksége. Ha már rendelkezésére áll egy SSH-kulcspár, kihagyhatja ezt a lépést.

SSH-keygen használatával sSH kulcspárt hozhat létre.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Az SSH-kulcspárok létrehozásáról, többek között a PuTTy használatáról az [SSH-kulcsok Windowsban való használatát](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) ismertető cikkben talál részletesebb információt.

Ha az SSH-kulcspárt a Cloud Shell használatával hozza létre, a kulcspár egy tárolólemezképtárolja. Ez [a tárfiók automatikusan létrejön.](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage) Ne törölje a tárfiókot vagy a fájlmegosztást, amíg a kulcsok be olvasása után.

#### <a name="create-vm-configuration"></a>Virtuálisgép-konfiguráció létrehozása

A PowerShellben a virtuális gépek létrehozáshoz egy konfigurációt kell létrehozni, amely a használni kívánt rendszerképpel, mérettel és hitelesítési beállításokkal egyező beállításokkal rendelkezik. A rendszer ezután ezzel a konfigurációval hozza létre a virtuális gépet.

Határozza meg az SSH hitelesítő adatokat, az operációs rendszer adatait és a virtuális gép méretét. Ebben a példában az SSH-kulcs a ~/.ssh/id_rsa.pub ban van tárolva.

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
A konfigurációdefiníciók kombinálásával hozzon létre egy **myVMsource** nevű virtuális gépet [a New-AzVM-mel](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) a **myResourceGroupNAT-ban.**

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigsource
```

Bár a parancs azonnal visszatér, eltarthat néhány percig a virtuális gép üzembe helyezése.

## <a name="prepare-destination-for-outbound-traffic"></a>Úti cél előkészítése a kimenő forgalomra

Most hozzon létre egy céla a kimenő forgalom által lefordított NAT szolgáltatás lehetővé teszi, hogy tesztelje azt.

### <a name="configure-virtual-network-for-destination"></a>Virtuális hálózat konfigurálása a célhoz

Létre kell hoznunk egy virtuális hálózatot, ahol a cél virtuális gép lesz.  Ezek a parancsok ugyanazokat a lépéseket, mint a forrás virtuális gép. A célvégpont felfedéséhez kisebb módosítások lettek hozzáadva.

Hozzon létre egy **myVnetdestination** nevű virtuális hálózatot egy **mySubnetdestination** nevű alhálózattal a [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) használatával a **myResourceGroupNAT-ban** a [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest)használatával. A virtuális hálózat IP-címterülete **192.168.0.0/16**. A virtuális hálózaton belüli **alhálózat: 192.168.0.0/24**.  A parancsok eredményét a **$subnetdestination** és **$vnetdestination** nevű változók tárolják későbbi használatra.

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

### <a name="create-public-ip-for-destination-vm"></a>Nyilvános IP létrehozása a célvirtuális géphez

Létrehozunk egy nyilvános IP-t a cél virtuális gép eléréséhez.  A [New-AzPublicIpAddress használatával](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) hozzon létre egy **myPublicIPdestinationVM** nevű nyilvános IP-címerőforrást a **myResourceGroupNAT-ban.**  A parancs eredménye egy **$publicIpdestinationVM** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$all = 'Static'
$pipd = 'myPublicIPdestinationVM'

$publicIpdestinationVM = 
New-AzPublicIpAddress -Name $pipd -ResourceGroupName $rsg -AllocationMethod $all -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-and-http-endpoint-for-vm"></a>NSG létrehozása és SSH- és HTTP-végpont nak a létrehozása a virtuális gép számára

A szabványos nyilvános IP-címek "alapértelmezés szerint biztonságosak", létrehozunk egy NSG-t, amely lehetővé teszi az ssh bejövő hozzáférését. A [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest) segítségével hozzon létre egy **myNSGdestination nevű NSG-erőforrást.** A [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) segítségével hozzon létre egy NSG-szabályt **az ssh**nevű SSH-hozzáféréshez.  A [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest) segítségével hozzon létre egy NSG-szabályt a HTTP access számára http nevű **néven.** Mindkét szabály a **myResourceGroupNAT-ban**jön létre. A parancs eredménye egy **$nsgdestination** nevű változóban lesz tárolva későbbi használatra.

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

Hozzon létre egy hálózati adaptert a [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) nevű **myNicdestination**. Ez a parancs a nyilvános IP-címhez és a hálózati biztonsági csoporthoz lesz társítva. A parancs eredménye egy **$nicdestination** nevű változóban lesz tárolva későbbi használatra.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$nnm = 'myNicdestination'

$nicdestination = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nnm -NetworkSecurityGroupID $nsgdestination.Id -PublicIPAddressID $publicIPdestinationVM.Id -SubnetID $vnetdestination.Subnets[0].Id -Location $loc
```

### <a name="create-a-destination-vm"></a>Célvirtuális gép létrehozása

#### <a name="create-vm-configuration"></a>Virtuálisgép-konfiguráció létrehozása

A PowerShellben a virtuális gépek létrehozáshoz egy konfigurációt kell létrehozni, amely a használni kívánt rendszerképpel, mérettel és hitelesítési beállításokkal egyező beállításokkal rendelkezik. A rendszer ezután ezzel a konfigurációval hozza létre a virtuális gépet.

Határozza meg az SSH hitelesítő adatokat, az operációs rendszer adatait és a virtuális gép méretét. Ebben a példában az SSH-kulcs a ~/.ssh/id_rsa.pub ban van tárolva.

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
A konfigurációdefiníciók kombinálásával hozzon létre egy **myVMdestination** nevű virtuális gépet a [New-AzVM szolgáltatással](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) a **myResourceGroupNAT-ban.**

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmConfigdestination
```

Bár a parancs azonnal visszatér, eltarthat néhány percig a virtuális gép üzembe helyezése.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Webkiszolgáló előkészítése és a hasznos adatszolgáltatás tesztelése a célvirtuális gépen

Először is fel kell derítenünk a cél virtuális gép IP-címét.  A virtuális gép nyilvános IP-címének leigazolásához használja a [Get-AzPublicIpAddress címet.](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest) 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPdestinationVM'
  
Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy jegyzettömbbe, hogy a következő lépésekben használhassa. Azt jelzi, hogy ez a cél virtuális gép.

### <a name="sign-in-to-destination-vm"></a>Bejelentkezés a célvirtuális gépre

Az SSH hitelesítő adatokat az előző műveletből származó Felhőrendszerhéjban kell tárolni.  Nyisson meg egy [Azure Cloud Shellt](https://shell.azure.com) a böngészőjében. Használja az előző lépésben beolvasott IP-címet az SSH-hoz a virtuális géphez. 

```bash
ssh azureuser@<ip-address-destination>
```

A bejelentkezés után másolja és illessze be a következő parancsokat.  

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

Ezek a parancsok frissítik a virtuális gépet, telepítik a nginx-et, és létrehoznak egy 100 KBytes fájlt. Ez a fájl a NAT szolgáltatás használatával a forrásvirtuális gépről lesz beolvasva.

Zárja be az SSH-munkamenetet a cél virtuális géppel.

## <a name="prepare-test-on-source-vm"></a>Teszt előkészítése a forrás virtuális gépen

Először is meg kell, hogy felfedezzék az IP-címét a forrás virtuális gép.  A virtuális gép nyilvános IP-címének leigazolásához használja a [Get-AzPublicIpAddress címet.](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest) 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$pipn = 'myPublicIPsourceVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $pipn | select IpAddress
``` 

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy jegyzettömbbe, hogy a következő lépésekben használhassa. Azt jelzi, hogy ez a forrás virtuális gép.

### <a name="log-into-source-vm"></a>Bejelentkezés a forrás virtuális gépbe

Az SSH hitelesítő adatok ismét a Cloud Shellben tárolódnak. Nyisson meg egy új lapot az [Azure Cloud Shell](https://shell.azure.com) böngészőjében.  Használja az előző lépésben beolvasott IP-címet az SSH-hoz a virtuális géphez. 

```bash
ssh azureuser@<ip-address-source>
```

Másolja és illessze be a következő parancsokat a NAT-szolgáltatás tesztelésének előkészítéséhez.

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

Ezek a parancsok frissítik a virtuális gépet, telepítik az ugrást, telepítik [a hé-t](https://github.com/rakyll/hey) a GitHubról, és frissítik a rendszerhéj-környezetet.

Most már készen áll a NAT szolgáltatás tesztelésére.

## <a name="validate-nat-service"></a>NAT-szolgáltatás ellenőrzése

A forrásvirtuális gépbe bejelentkezve a **curl** és **a hey** segítségével kérelmeket hozhat létre a cél IP-címére.

A göndörség segítségével olvassa be a 100 kbytes fájlt.  Cserélje ** \<** le az alábbi példában megadott ip-cím-cél>a korábban másolt cél IP-címre.  A **--output** paraméter azt jelzi, hogy a beolvasott fájl el lesz vetve.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Azt is létrehozhat egy sor kérelmet a **hey**. Ismét cserélje le ** \<az ip-address-destination>** a korábban másolt cél IP-címre.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Ez a parancs 100 kérelmet hoz létre, 10-et egyidejűleg, 30 másodperces időhosszabbítással. A TCP-kapcsolat nem lesz újra használatban.  Minden kérelem 100 kbyte-ot fog lekérni.  Végén a futás, **hé** jelentést néhány statisztikát arról, hogy milyen jól a NAT szolgáltatás nem.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [Eltávolítás-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) paranccsal eltávolíthatja az erőforráscsoportot és a benne lévő összes erőforrást.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy NAT-átjárót, létrehozott egy forrás- és célvirtuális gép, majd tesztelte a NAT-átjárót.

Tekintse át a metrikákat az Azure Monitorban a NAT-szolgáltatás működésének megtekintéséhez. Diagnosztizálja az olyan problémákat, mint például a rendelkezésre álló SNAT-portok erőforrás-kimerülése.  Az SNAT-portok erőforrás-kimerülése könnyen kezelhető további nyilvános IP-címerőforrások vagy nyilvános IP-előtag-erőforrások vagy mindkettő hozzáadásával.

- További információ a [virtuális hálózati hálózati hálózati kapcsolatról](./nat-overview.md)
- További információ a [NAT átjáró-erőforrásról.](./nat-gateway-resource.md)
- Rövid útmutató a [NAT átjáróerőforrás Azure CLI használatával történő](./quickstart-create-nat-gateway-cli.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT átjáróerőforrás Azure PowerShell használatával történő](./quickstart-create-nat-gateway-powershell.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT-átjáró-erőforrás Azure Portalon való](./quickstart-create-nat-gateway-portal.md)telepítéséhez.

> [!div class="nextstepaction"]

