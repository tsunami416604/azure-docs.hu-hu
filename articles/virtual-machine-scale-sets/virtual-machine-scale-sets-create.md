---
title: "Hozzon létre egy Azure virtuálisgép-méretezési csoport |} Microsoft Docs"
description: "Létrehozhat és telepíthet a Linux vagy a Windows Azure virtuálisgép-méretezési beállítása az Azure parancssori felület, a PowerShell, a sablon vagy a Visual Studio."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.openlocfilehash: 32af01aa545c541688128a7ae6bbb82a0e046f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-deploy-a-virtual-machine-scale-set"></a>Létrehozhat és telepíthet egy virtuálisgép-méretezési csoport
Virtuálisgép-méretezési csoportok könnyebben akkor helyezheti üzembe és felügyelheti az azonos virtuális gépek készletként. Méretezési csoportok hyperscale alkalmazások jól méretezhető és testre szabható számítási rétegét adja meg, és a Windows platform-lemezképek, Linux platformon képek, egyéni lemezképek és bővítmények támogatják. Méretezési csoportok kapcsolatos további információkért lásd: [virtuálisgép-méretezési csoportok](virtual-machine-scale-sets-overview.md).

Ez az oktatóanyag bemutatja, hogyan hozzon létre egy virtuálisgép-méretezési csoport **nélkül** az Azure portál használatával. Az Azure-portál használatával kapcsolatos információkért lásd: [egy virtuálisgép-méretezési létrehozása az Azure-portálon beállítani](virtual-machine-scale-sets-portal-create.md).

>[!NOTE]
>Azure Resource Manager erőforrásokra vonatkozó további információkért lásd: [Azure Resource Manager és klasszikus üzembe helyezési](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Használata Azure CLI 2.0 vagy az Azure PowerShell méretezési készlet létrehozásához, akkor először be kell jelentkeznie az előfizetéséhez.

Telepítése, állítsa be, és jelentkezzen be az Azure-bA az Azure parancssori felület vagy a PowerShell használatával kapcsolatos további információkért lásd: [Ismerkedés az Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md) vagy [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/overview).

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Először hozzon létre egy erőforráscsoportot, amelyhez a virtuálisgép-méretezési csoport társítva van.

```azurecli
az group create --location westus2 --name MyResourceGroup1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name MyResourceGroup1
```

## <a name="create-from-azure-cli"></a>Az Azure parancssori felület létrehozása

Az Azure parancssori felület hozhat létre egy virtuálisgép-méretezési csatlakozhatnak beállítása. Ha nincs megadva alapértelmezett értékeket, az Ön megadták. Például, ha nem adja meg a virtuális hálózati adatok, a virtuális hálózat létrejötte meg. Ha nincs megadva a következő részekből, azok jön létre: 
- Terheléselosztó
- Virtuális hálózat
- A nyilvános IP-cím

Ha a virtuálisgép-méretezési csoport használni kívánt virtuális gép lemezképére, néhány lehetősége van:

- URN  
Egy erőforrás-azonosító:  
**Win2012R2Datacenter**

- URN alias  
A rövid nevét URN:  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**

- Egyéni erőforrás-azonosító  
Egy Azure-erőforrás elérési útja:  
**/Subscriptions/Subscription-GUID/resourceGroups/MyResourceGroup/Providers/Microsoft.COMPUTE/Images/MyImage**

- Webes erőforrás  
Egy HTTP URI elérési útja:  
**http://contoso.BLOB.Core.Windows.NET/vhds/osdiskimage.vhd**

>[!TIP]
>Az elérhető rendszerkép listájának kaphat `az vm image list`.

A virtuálisgép-méretezési csoport létrehozásához meg kell adnia a következőket:

- Erőforráscsoport 
- Név
- Operációsrendszer-lemezkép
- Hitelesítési adatok 
 
Az alábbi példa létrehoz egy alapszintű virtuálisgép-méretezési (ebben a lépésben eltarthat néhány percig).

```azurecli
az vmss create --resource-group MyResourceGroup1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

Ha a parancs most létrehozott beállítani a virtuálisgép-méretezési fog rendelkezni. Szükség lehet beolvasni a virtuális gép IP-címét, hogy azt is elérheti. Sok más információ a virtuális gép (beleértve az IP-cím) a következő paranccsal kérheti le. 

```azurecli
az vmss list-instance-connection-info --resource-group MyResourceGroup1 --name MyScaleSet
```

## <a name="create-from-powershell"></a>A PowerShell létrehozása

PowerShell bonyolultabb, mint az Azure parancssori felület használatával. Az Azure parancssori felület alapértelmezett beállításokat biztosít a hálózattal kapcsolatos erőforrások (például a terheléselosztók, IP-címek és virtuális hálózatok), miközben PowerShell viszont nem. A PowerShell-lel lemezkép hivatkozó egy kicsit bonyolultabb túl. A következő parancsmagok képekkel kaphat:

1. Get-AzureRMVMImagePublisher
2. Get-AzureRMVMImageOffer
3. Get-AzureRmVMImageSku

A parancsmagok munkahelyi átirányítható sorrendben. Példa mutatja be az összes lemezkép az beszerzése a **2 USA nyugati régiója** régióban, amelynek a neve, közzétevő, **microsoft** azt.

```powershell
Get-AzureRMVMImagePublisher -Location WestUS2 | Where-Object PublisherName -Like *microsoft* | Get-AzureRMVMImageOffer | Get-AzureRmVMImageSku | Select-Object PublisherName, Offer, Skus
```

```
PublisherName              Offer                    Skus
-------------              -----                    ----
microsoft-ads              linux-data-science-vm    linuxdsvm
microsoft-ads              standard-data-science-vm standard-data-science-vm
MicrosoftAzureSiteRecovery Process-Server           Windows-2012-R2-Datacenter
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Enterprise
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Standard
MicrosoftBizTalkServer     BizTalk-Server           2016-Developer
MicrosoftBizTalkServer     BizTalk-Server           2016-Enterprise
...
```

A munkafolyamat egy virtuálisgép-méretezési csoport létrehozásához a következőképpen történik:

1. Hozzon létre egy konfigurációs objektumot, amely tárolja az információkat a méretezési készlet.
2. Referencia az alap operációs rendszer lemezképét.
3. Adja meg az operációs rendszer beállításait: hitelesítés, a virtuális gép nevének előtagja és a felhasználó/fázis.
4. A hálózatkezelés konfigurálását.
5. A méretezési készlet létrehozása.

Ez a példa egy olyan számítógépen, amelyen Windows Server 2016 telepítve beállítása alapszintű két-példány méretezési hoz létre.

```powershell
# Resource group name from above
$rg = "MyResourceGroup1"
$location = "WestUS2"

# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location $location -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources

## Basics
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName $rg -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $subnet

## Load balancer
$publicIP = New-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName $rg -Location $location -AllocationMethod Static -DomainNameLabel "myuniquedomain"
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontend" -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
$probe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
$inboundNATRule1= New-AzureRmLoadBalancerRuleConfig -Name "webserver" -FrontendIpConfiguration $frontendIP -Protocol Tcp -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -Probe $probe -BackendAddressPool $backendPool
$inboundNATPool1 = New-AzureRmLoadBalancerInboundNatPoolConfig -Name "RDP" -FrontendIpConfigurationId $frontendIP.Id -Protocol TCP -FrontendPortRangeStart 53380 -FrontendPortRangeEnd 53390 -BackendPort 3389

New-AzureRmLoadBalancer -ResourceGroupName $rg -Name "LB1" -Location $location -FrontendIpConfiguration $frontendIP -LoadBalancingRule $inboundNATRule1 -InboundNatPool $inboundNATPool1 -BackendAddressPool $backendPool -Probe $probe

## IP address config
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ipaddress" -LoadBalancerBackendAddressPoolsId $backendPool.Id -SubnetId $vnet.Subnets[0].Id -LoadBalancerInboundNatPoolsId $inboundNATPool1.Id

# Attach the virtual network to the IP object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss -ResourceGroupName $rg -Name "MyScaleSet1" -VirtualMachineScaleSet $vmssConfig
```

### <a name="using-a-custom-virtual-machine-image"></a>Egyéni virtuálisgép-lemezkép használatával
A skála állítson be úgy a saját egyéni rendszerképét helyett egy virtuálisgép-lemezkép hivatkozik a gyűjteményből létrehozásakor a _Set-AzureRmVmssStorageProfile_ parancs néz ki:
```PowerShell
Set-AzureRmVmssStorageProfile -OsDiskCreateOption FromImage -ManagedDisk PremiumLRS -OsDiskCaching "None" -OsDiskOsType Linux -ImageReferenceId (Get-AzureRmImage -ImageName $VMImage -ResourceGroupName $rg).id
```

## <a name="create-from-a-template"></a>Létrehozás sablonból

A virtuálisgép-méretezési beállítása az Azure Resource Manager-sablon használatával telepítheti. Létrehozhat saját sablont, vagy használjon közül a [sablon tárház](https://azure.microsoft.com/resources/templates/?term=vmss). Ezeket a sablonokat is telepíthető közvetlenül az Azure-előfizetéshez.

>[!NOTE]
>Létrehozhat saját sablont, hozzon létre egy JSON-szövegfájlt. Hozzon létre és sablon testreszabása kapcsolatos általános információkért lásd: [Azure Resource Manager-sablonok](../azure-resource-manager/resource-group-authoring-templates.md).

Egy minta sablon [a Githubon](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set). Hozzon létre, és adott minta kapcsolatos további információkért lásd: [minimális életképes méretezési](.\virtual-machine-scale-sets-mvss-start.md).

## <a name="create-from-visual-studio"></a>A Visual Studio eszközből létrehozása

A Visual Studio hozzon létre egy Azure erőforráscsoport-projekt, és adja hozzá a virtuálisgép-méretezési sablon beállítása azt. Kiválaszthatja, hogy szeretné-e a Githubból vagy az Azure Web Application Gallery importálja azt. A központi telepítés PowerShell-parancsfájlt is létrejön. További információkért lásd: [egy virtuálisgép-méretezési létrehozása a Visual Studio beállítása](virtual-machine-scale-sets-vs-create.md).

## <a name="create-from-the-azure-portal"></a>Hozzon létre az Azure-portálon

Az Azure-portálon gyorsan létrehozhat egy méretezési kényelmes megoldást kínál. További információkért lásd: [egy virtuálisgép-méretezési létrehozása az Azure-portálon beállítani](virtual-machine-scale-sets-portal-create.md).

## <a name="next-steps"></a>Következő lépések

További információ [adatlemezek](virtual-machine-scale-sets-attached-disks.md).

Megtudhatja, hogyan [kezelheti az alkalmazásokat](virtual-machine-scale-sets-deploy-app.md).
