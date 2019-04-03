---
title: Always On rendelkezésre állási csoport figyelője – Microsoft Azure konfigurálása |} A Microsoft Docs
description: Belső terheléselosztó használata egy vagy több IP-címek az Azure Resource Manager modellel konfigurálja a rendelkezésre állási csoport figyelője.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.openlocfilehash: 5b647af7925ceb81c524deb0accf90f9e895080e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876989"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Egy vagy több Always On rendelkezésre állási csoport figyelője – erőforrás-kezelő konfigurálása
Ez a témakör bemutatja, hogyan lehet:

* Hozzon létre egy belső terheléselosztót a PowerShell-parancsmagok használatával az SQL Server rendelkezésre állási csoportok számára.
* Adja hozzá a további IP-címek terheléselosztóhoz egynél több rendelkezésre állási csoport. 

Egy rendelkezésre állási csoport figyelőjének egy virtuális hálózat neve, amelyhez az ügyfelek csatlakozni az adatbázis eléréséhez. Azure-beli virtuális gépeken terheléselosztó tárolja az IP-címet a figyelőhöz. A load balancer irányítja a forgalmat a mintavételi portot figyel az SQL Server példányához. Általában a rendelkezésre állási csoport egy belső terheléselosztót használ. Az Azure belső terheléselosztó egy vagy több IP-címek is üzemeltethet. Minden IP-cím egy adott mintavételi portot használja. Ez a dokumentum bemutatja, hogyan PowerShell használatával létrehozhat egy terheléselosztót, vagy IP-címek hozzáadása egy meglévő terheléselosztóhoz az SQL Server rendelkezésre állási csoportok számára. 

Több IP-cím hozzárendelése a belső terheléselosztó lehetővé teszi új Azure-ba, és csak a Resource Manager-modell érhető el. Ez a feladat végrehajtásához szüksége lehet Resource Manager-modellben az Azure virtuális gépeken telepített SQL Server rendelkezésre állási csoport. Az SQL Server virtuális gépeket is ugyanabban a rendelkezésre állási csoporthoz kell tartoznia. Használhatja a [Microsoft sablon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) automatikusan létrehozni a rendelkezésre állási csoportot az Azure Resource Manager. Ez a sablon automatikusan létrehozza a rendelkezésre állási csoport, beleértve a belső terheléselosztó az Ön számára. Ha szeretné, akkor [Always On rendelkezésre állási csoport manuális konfigurálása](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Ehhez a témakörhöz, hogy a rendelkezésre állási csoportok már konfigurálva vannak.  

Kapcsolódó témakörök a következők:

* [AlwaysOn rendelkezésre állási csoportok konfigurálása Azure-beli virutális gépen (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Virtuális hálózatok közötti kapcsolat beállítása az Azure Resource Manager és a PowerShell használatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>PowerShell-verziójának ellenőrzése

Ebben a cikkben szereplő példák az Azure PowerShell-modul 5.4.1-es használatával vizsgálják.

Ellenőrizze a PowerShell-modul 5.4.1 vagy újabb.

Lásd: [Azure PowerShell-modul telepítéséhez](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>A Windows tűzfal konfigurálása

Az SQL Server-hozzáférés engedélyezése a Windows tűzfal konfigurálásához. A tűzfalszabályok engedélyezik a portokat használja a figyelő mintavétel, valamint az SQL Server-példány TCP-kapcsolatot. Részletes útmutatásért lásd: [Windows tűzfal konfigurálása az hozzáféréshez](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Hozzon létre egy bejövő szabályt az SQL Server-portot és a mintavételi portot.

Ha Ön az Azure hálózati biztonsági csoporttal való hozzáférés korlátozása győződjön meg arról, hogy az engedélyezési szabályok közé tartoznak a háttér SQL Server virtuális gép IP-címek és a terheléselosztó nem fix IP-címek a rendelkezésre állási csoport figyelőjének és a fürt alapvető IP-címét, ha van ilyen.

## <a name="determine-the-load-balancer-sku-required"></a>Határozza meg a terheléselosztó Termékváltozat megadása kötelező

[Az Azure load balancer](../../../load-balancer/load-balancer-overview.md) 2 termékváltozatban érhető el: Alapszintű és Standard. A standard load balancer használata javasolt. Ha a virtuális gépeket egy rendelkezésre állási csoportban, az alapszintű load balancer számára engedélyezett. A standard load balancer szükséges, hogy az összes virtuális gép IP-címek szabványos IP-cím használatára.

Az aktuális [Microsoft sablon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) egy rendelkezésre állási csoportot használja az alapszintű load balancer alapszintű IP-címmel.

Ebben a cikkben szereplő példák a standard load balancer adja meg. A parancsfájl tartalmazza a példákban `-sku Standard`.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Alapszintű load balancer létrehozása, távolítsa el a `-sku Standard` a sorból, amely a terheléselosztó hoz létre. Példa:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Példa parancsfájl: Belső terheléselosztó létrehozása a PowerShell használatával

> [!NOTE]
> Ha létrehozta a rendelkezésre állási csoportnak a [Microsoft sablon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), a belső load balancer már létre lett hozva.

A következő PowerShell-parancsfájl egy belső terheléselosztót hoz létre, konfigurálja a terheléselosztási szabályok és állítja be az IP-címet a terheléselosztóhoz. A szkript futtatásához nyissa meg a Windows PowerShell ISE-ben, és illessze be a parancsfájlt a parancsfájl panelen. Használat `Connect-AzAccount` való bejelentkezés a Powershellbe. Ha több Azure-előfizetéssel rendelkezik, használja a `Select-AzSubscription` az előfizetés beállításához. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the front-end configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the back-end configuration

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzNetworkInterface -NetworkInterface $NIC
        start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a> Példa parancsfájl: IP-cím hozzáadása egy meglévő a terheléselosztóhoz a PowerShell-lel
Egynél több rendelkezésre állási csoportot használja, adja hozzá a további IP-címet a terheléselosztóhoz. Minden IP-cím a saját terheléselosztási szabály, mintavételi portot és előtérbeli port szükséges.

Az előtérbeli portot az SQL Server-példányhoz való csatlakozáshoz használt alkalmazások port. Különböző rendelkezésre állási csoportok számára az IP-címek is használja az előtér-portot.

> [!NOTE]
> Az SQL Server rendelkezésre állási csoportok minden IP-cím szükséges az adott mintavételi port. Például egy IP-címmel a terheléselosztón 59999 mintavételi portot használ, nincs más IP-címeket, hogy a terheléselosztó a mintavételi portot 59999 használhatja.

* Load balancer korlátok kapcsolatos információkért lásd: **privát előtéri IP-Címek száma terheléselosztónként** alatt [hálózatkezelési korlátok – Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Rendelkezésre állási csoport korlátok kapcsolatos információkért lásd: [korlátozások (rendelkezésre állási csoportok)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

A következő parancsfájl egy új IP-címet ad hozzá egy meglévő a terheléselosztóhoz. Az ILB figyelő-portot az előtérbeli portot terheléselosztási használja. Ezt a portot is lehet a port, amelyet az SQL Server figyel a következőn:. Az SQL Server alapértelmezett példánya a port a 1433-as. A terheléselosztási szabály egy rendelkezésre állási csoport egy nem fix IP-cím (közvetlen kiszolgálói válasz) szükséges, ezért a a háttér-port pedig ugyanaz, mint az előtérbeli portot. Frissítse a változókat, az adott környezetben. 

```powershell
# Connect-AzAccount
# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzLoadBalancer 

$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzLoadBalancer   
```

## <a name="configure-the-listener"></a>A figyelő konfigurálása

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>A figyelő portját állítsa az SQL Server Management Studióban

1. Indítsa el az SQL Server Management Studiót, és kapcsolódjon az elsődleges replika.

1. Navigáljon a **AlwaysOn magas rendelkezésre állás** | **rendelkezésre állási csoportok** | **rendelkezésre állási csoport figyelője**. 

1. Meg kell jelennie a figyelő nevét, amely a Feladatátvevőfürt-kezelő létrehozta. Kattintson a jobb gombbal a figyelő nevét, és kattintson a **tulajdonságok**.

1. Az a **Port** mezőben, adja meg a portszámot a rendelkezésre állási csoport figyelőjének a korábban használt $EndpointPort használatával (az alapértelmezett 1433-as volt az), majd kattintson a **OK**.

## <a name="test-the-connection-to-the-listener"></a>A figyelő a kapcsolat tesztelése

A kapcsolat teszteléséhez:

1. RDP-vel az azonos virtuális hálózatba, de a replika nem rendelkezik SQL-kiszolgálóra. Ez lehet a többi SQL Server a fürtben.

1. Használat **sqlcmd** segédprogram a kapcsolat teszteléséhez. Például hozza létre a következő parancsfájl egy **sqlcmd** kapcsolatot az elsődleges replika, a figyelő a Windows-hitelesítés használatával:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Ha a figyelő nem az alapértelmezett portot használ portot (1433), adja meg a portot a kapcsolati karakterláncban. A következő sqlcmd parancsot például egy figyelő 1435 porton csatlakozik: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Az SQLCMD-kapcsolatot automatikusan csatlakozik bármely SQL Server-példány az elsődleges replikát. 

> [!NOTE]
> Győződjön meg arról, hogy a megadott port nyitva a tűzfalon az SQL-kiszolgálók is. Mindkét kiszolgáló szükséges egy bejövő szabályt a TCP-portot, amelyet használhat. Lásd: [hozzáadása vagy szerkesztése tűzfalszabály](https://technet.microsoft.com/library/cc753558.aspx) további információt. 
> 
> 

## <a name="guidelines-and-limitations"></a>Irányelvek és korlátozások
Vegye figyelembe az alábbi irányelveket a rendelkezésre állási csoport figyelője az Azure-ban a belső terheléselosztó:

* A belső terheléselosztót csak érheti el, a figyelő az adott virtuális hálózaton belül.

* Ha Ön az Azure hálózati biztonsági csoporttal való hozzáférés korlátozása győződjön meg arról, hogy az engedélyezési szabályok közé tartoznak a háttér SQL Server virtuális gép IP-címek és a terheléselosztó nem fix IP-címek a rendelkezésre állási csoport figyelőjének és a fürt alapvető IP-címét, ha van ilyen.

## <a name="for-more-information"></a>További tudnivalók
További információkért lásd: [konfigurálása Always On rendelkezésre állási csoport Azure-beli virtuális gépen manuálisan](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok
A következő PowerShell-parancsmagok használatával hozzon létre egy Azure-beli virtuális gépek belső terheléselosztót.

* [Új AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) egy terheléselosztót hoz létre. 
* [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) creates a front-end IP configuration for a load balancer. 
* [New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) creates a rule configuration for a load balancer. 
* [New-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) creates a backend address pool configuration for a load balancer. 
* [Új AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) hoz létre egy mintavételi konfigurációt egy terheléselosztóhoz.
* [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) terheléselosztó távolít el egy Azure-erőforráscsoportot.
