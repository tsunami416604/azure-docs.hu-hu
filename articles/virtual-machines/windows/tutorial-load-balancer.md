---
title: Oktatóanyag – Windows rendszerű virtuális gépek terheléselosztása az Azure-ban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre terheléselosztót az Azure PowerShell-lel egy magas rendelkezésre állású és biztonságos alkalmazáshoz három Windows rendszerű virtuális gépen keresztül
services: virtual-machines-windows
documentationcenter: virtual-machines
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 5ed59695ffadcfd69e7d72100729f7f88adc60a9
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54423391"
---
# <a name="tutorial-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application-with-azure-powershell"></a>Oktatóanyag: Terheléselosztó Windows virtuális gépek az Azure-ban magas rendelkezésre állású alkalmazás létrehozása az Azure PowerShell használatával
A terheléselosztás magasabb szintű rendelkezésre állást biztosít, mivel a bejövő kérelmeket több virtuális gép között osztja szét. Ebben az oktatóanyagban megismerkedhet az Azure Load Balancer különböző összetevőivel, amelyek elosztják a forgalmat, és gondoskodnak a magas rendelkezésre állásról. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Azure-terheléselosztó létrehozása
> * Terheléselosztó állapotmintájának létrehozása
> * Terheléselosztó forgalmára vonatkozó szabályok létrehozása
> * Alapszintű IIS-oldal létrehozása egyéni szkriptbővítménnyel
> * Virtuális gépek létrehozása és terheléselosztóhoz csatolása
> * Terheléselosztó megtekintése működés közben
> * Virtuális gépek hozzáadása a terheléselosztóhoz és eltávolításuk a terheléselosztóból

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.


## <a name="azure-load-balancer-overview"></a>Az Azure-terheléselosztók áttekintése
Az Azure-terheléselosztók 4. rétegbeli (TCP, UDP) terheléselosztók, amelyek magas rendelkezésre állást biztosítanak azáltal, hogy a bejövő forgalmat elosztják az ép virtuális gépek között. A terheléselosztó állapotmintája ez egyes virtuális gépek adott portjait monitorozza, és csak a működő virtuális gépekre terjeszt forgalmat.

Meg kell adnia egy előtérbeli IP-konfigurációt, amely egy vagy több nyilvános IP-címet tartalmaz. Ez az előtérbeli IP-konfiguráció lehetővé teszi, hogy a terheléselosztó és az alkalmazások elérhetők legyenek az interneten keresztül. 

A virtuális gépek a virtuális hálózati adapterkártyájuk (NIC) segítségével csatlakoznak a terheléselosztóhoz. Annak érdekében, hogy a forgalmat el lehessen osztani a virtuális gépek között, egy háttércímkészlet tartalmazza a terheléselosztóhoz csatlakoztatott NIC-k IP-címét.

A forgalom szabályozásához terheléselosztási szabályokat kell megadnia a virtuális gépekhez rendelt adott portokhoz és protokollokhoz.


## <a name="create-azure-load-balancer"></a>Azure-terheléselosztó létrehozása
Ez a szakasz részletesen ismerteti a terheléselosztó egyes összetevőinek létrehozását és konfigurálását. Mielőtt létrehozhatna egy terheléselosztót, létre kell hoznia egy erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal. A következő példa létrehoz egy *myResourceGroupLoadBalancer* nevű erőforráscsoportot az *EastUs* helyen:

```azurepowershell-interactive
New-AzureRmResourceGroup `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS"
```

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet
Az alkalmazás internetes eléréséhez a terheléselosztónak nyilvános IP-címmel kell rendelkeznie. Hozzon létre egy nyilvános IP-címet a [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) paranccsal. Az alábbi példában létrejön egy *myPublicIP* nevű nyilvános IP-cím a *myResourceGroupLoadBalancer* erőforráscsoportban:

```azurepowershell-interactive
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -AllocationMethod "Static" `
  -Name "myPublicIP"
```

### <a name="create-a-load-balancer"></a>Load Balancer létrehozása
Hozzon létre egy előtérbeli IP-címkészletet a [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) paranccsal. Az alábbi példában létrejön egy *myFrontEndPool* nevű előtérbeli IP-címkészlet, amely a *myPublicIP* címhez kötődik: 

```azurepowershell-interactive
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
```

Hozzon létre egy háttércímkészletet a [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) paranccsal. A virtuális gépek ehhez a háttérkészlethez lesznek csatolva a hátralévő lépések során. A következő példában egy *myBackEndPool* nevű háttércímkészlet jön létre:

```azurepowershell-interactive
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
```

Hozza létre a terheléselosztót a [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) paranccsal. Az alábbi példában egy *myLoadBalancer* nevű terheléselosztó jön létre az előző lépésekben létrehozott elő- és háttérbeli IP-címkészletek használatával:

```azurepowershell-interactive
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myLoadBalancer" `
  -Location "EastUS" `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Állapotminta létrehozása
Ahhoz, hogy a terheléselosztó monitorozhassa az alkalmazás állapotát, állapotmintát kell használni. Az állapotminta dinamikusan hozzáadja a virtuális gépeket a terheléselosztó rotációjához vagy eltávolítja belőle azokat, az állapotellenőrzésekre adott válaszuk alapján. Alapértelmezés szerint a virtuális gépeket a rendszer akkor távolítja el a terheléselosztó elosztásából, ha egy 15 másodperces intervallumon belül két, egymást követő hiba jelentkezik. Az állapotmintákat az alkalmazás egy protokollja vagy adott állapotellenőrzési oldala alapján lehet létrehozni. 

Az alábbi példában egy TCP-mintavétel jön létre. Egyéni HTTP-mintavételt is létrehozhat részletesebb állapotellenőrzések elvégzéséhez. Egyéni HTTP-mintavétel használatakor létre kell hoznia az állapotellenőrzési oldalt, például: *healthcheck.aspx*. A mintavételnek a **HTTP 200 OK** választ kell visszaadnia ahhoz, hogy a terheléselosztó a gazdagépet a rotációban tartsa.

TCP-állapotminta létrehozásához használja az [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) parancsot. Az alábbi példában egy *myHealthProbe* nevű állapotmintát hozunk létre, amely a *80*-as *TCP*-porton lévő virtuális gépeket monitorozza:

```azurepowershell-interactive
Add-AzureRmLoadBalancerProbeConfig `
  -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

Az állapotminta alkalmazásához frissítse a terheléselosztót a [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) paranccsal:

```azurepowershell-interactive
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. Meg kell határoznia az előtérbeli IP-konfigurációt a bejövő forgalomhoz és a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás- és célportot. Ahhoz, hogy csak a megfelelő állapotú virtuális gépek fogadhassanak forgalmat, adja meg a használandó állapotmintát is.

Hozzon létre egy terheléselosztási szabályt az [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) paranccsal. Az alábbi példa létrehoz egy *myLoadBalancerRule* nevű terheléselosztási szabályt, és elosztja a *80*-as *TCP*-port forgalmát:

```azurepowershell-interactive
$probe = Get-AzureRmLoadBalancerProbeConfig -LoadBalancer $lb -Name "myHealthProbe"

Add-AzureRmLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe $probe
```

Frissítse a terheléselosztót a [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) paranccsal:

```azurepowershell-interactive
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása
Mielőtt üzembe helyezne néhány virtuális gépet és tesztelné az elosztót, hozza létre a támogató virtuális hálózati erőforrásokat. További információt a virtuális hálózatokról az [Azure-beli virtuális hálózatok kezelésével](tutorial-virtual-network.md) foglalkozó oktatóanyagban talál.

### <a name="create-network-resources"></a>Hálózati erőforrások létrehozása
Hozzon létre egy virtuális hálózatot a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) paranccsal. Az alábbi példa létrehoz egy *mySubnet* hálózattal rendelkező *myVNet* nevű virtuális hálózatot:

```azurepowershell-interactive
# Create subnet config
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create the virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Location "EastUS" `
  -Name "myVnet" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Virtuális hálózati adapterek (NIC-k) a [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) paranccsal hozhatók létre. Az alábbi példa három virtuális NIC-t hoz létre. (Egy virtuális NIC-t minden virtuális géphez, amelyet létre fog hozni az alkalmazáshoz a következő lépések során). Bármikor létrehozhat további virtuális NIC-ket és virtuális gépeket, és hozzáadhatja őket a terheléselosztóhoz:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -Name myVM$i `
     -Location "EastUS" `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```


## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása
Az alkalmazás magas rendelkezésre állásának növeléséhez helyezze a virtuális gépeket egy rendelkezésre állási csoportba.

Hozzon létre egy rendelkezésre állási csoportot a [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) paranccsal. Az alábbi példa egy *myAvailabilitySet* nevű rendelkezésre állási csoportot hoz létre:

```azurepowershell-interactive
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myAvailabilitySet" `
  -Location "EastUS" `
  -Sku aligned `
  -PlatformFaultDomainCount 2 `
  -PlatformUpdateDomainCount 2
```

A [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) paranccsal állítsa be a virtuális gépek rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most már létrehozhatja a virtuális gépeket a [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) paranccsal. Az alábbi példa három virtuális gépet hoz létre, illetve a szükséges virtuális hálózati összetevőket, ha azok még nem léteznek:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupLoadBalancer" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -OpenPorts 80 `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred `
        -AsJob
}
```

Az `-AsJob` paraméter háttérfeladatként létrehozza a virtuális gépet, így használni tudja a PowerShell-parancssorokat. A háttérfeladatok részleteit a `Job` parancsmaggal tekintheti meg. A három virtuális gép létrehozása és konfigurálása néhány percet vesz igénybe.


### <a name="install-iis-with-custom-script-extension"></a>IIS telepítése egyéni szkriptbővítménnyel
A [Windows rendszerű virtuális gép testreszabásával](tutorial-automate-vm-deployment.md) foglalkozó korábbi oktatóanyagból megtudhatta, hogyan automatizálható egy virtuális gép testreszabása a Windows rendszerhez készült egyéni szkriptbővítménnyel. Ugyanezzel a módszerrel telepítheti és konfigurálhatja az IIS-t a virtuális gépeken.

A [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) paranccsal telepítse az egyéni szkriptbővítményt. A bővítmény a `powershell Add-WindowsFeature Web-Server` parancs futtatásával telepíti az IIS-webkiszolgálót, majd a *Default.htm* lapot frissítve megjeleníti a virtuális gép eszköznevét:

```azurepowershell-interactive
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
     -ResourceGroupName "myResourceGroupLoadBalancer" `
     -ExtensionName "IIS" `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.8 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location EastUS
}
```

## <a name="test-load-balancer"></a>Terheléselosztó tesztelése
Kérje le a terheléselosztó nyilvános IP-címét a [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) paranccsal. A következő példa a korábban létrehozott *myPublicIP* IP-címét kéri le:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
  -ResourceGroupName "myResourceGroupLoadBalancer" `
  -Name "myPublicIP" | select IpAddress
```

A nyilvános IP-címet beírhatja egy böngészőbe. Ekkor megjelenik a webhely, amely tartalmazza annak a virtuális gépnek az eszköznevét, amelyhez a terheléselosztó forgalmat irányított, ahogy az alábbi példában is látható:

![IIS-webhely futtatása](./media/tutorial-load-balancer/running-iis-website.png)

Annak megtekintéséhez, hogyan osztja el a terheléselosztó az alkalmazást futtató három virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését.


## <a name="add-and-remove-vms"></a>Virtuális gépek hozzáadása és eltávolítása
Előfordulhat, hogy karbantartás kell végeznie az alkalmazást futtató virtuális gépeken (például operációsrendszer-frissítést kell telepítenie). Az alkalmazás megnövekedett forgalmának kezeléséhez szükség lehet további virtuális gépek hozzáadására. Ez a szakasz bemutatja, hogyan távolíthat el egy virtuális gépet a terheléselosztóból, vagy hogyan adhat hozzá virtuális gépeket.

### <a name="remove-a-vm-from-the-load-balancer"></a>Virtuális gép eltávolítása a terheléselosztóból
Kérje le az NIC-t a [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) paranccsal, majd a virtuális NIC *LoadBalancerBackendAddressPools* tulajdonságát állítsa *$null* értékre. Végül frissítse a virtuális NIC-t:

```azurepowershell-interactive
$nic = Get-AzureRmNetworkInterface `
    -ResourceGroupName "myResourceGroupLoadBalancer" `
    -Name "myVM2"
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Annak megtekintéséhez, hogyan osztja el a terheléselosztó az alkalmazást futtató megmaradt kettő virtuális gép között a forgalmat, kényszerítheti a webböngésző frissítését. Most már elvégezheti a virtuális gépen a szükséges karbantartást, például telepítheti az operációs rendszer frissítéseit, vagy újraindíthatja a virtuális gépet.

### <a name="add-a-vm-to-the-load-balancer"></a>Virtuális gép hozzáadása a terheléselosztóhoz
A virtuális gép karbantartásának elvégzése után, vagy ha bővítenie kell a kapacitást, állítsa a virtuális NIC *LoadBalancerBackendAddressPools* tulajdonságát [Get-AzureRMLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) értékről *BackendAddressPool* értékre:

A terheléselosztó lekérése:

```azurepowershell-interactive
$lb = Get-AzureRMLoadBalancer `
    -ResourceGroupName myResourceGroupLoadBalancer `
    -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy terheléselosztót, majd virtuális gépeket csatolt hozzá. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure-terheléselosztó létrehozása
> * Terheléselosztó állapotmintájának létrehozása
> * Terheléselosztó forgalmára vonatkozó szabályok létrehozása
> * Alapszintű IIS-oldal létrehozása egyéni szkriptbővítménnyel
> * Virtuális gépek létrehozása és terheléselosztóhoz csatolása
> * Terheléselosztó megtekintése működés közben
> * Virtuális gépek hozzáadása a terheléselosztóhoz és eltávolításuk a terheléselosztóból

A következő oktatóanyag a virtuális gépek hálózatkezelését mutatja be.

> [!div class="nextstepaction"]
> [Virtuális gépek és virtuális hálózatok kezelése](./tutorial-virtual-network.md)
