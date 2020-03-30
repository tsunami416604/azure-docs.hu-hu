---
title: Rendelkezésre állási csoport figyelőinek konfigurálása & terheléselosztó (PowerShell)
description: Konfigurálja a rendelkezésre állási csoport figyelői az Azure Resource Manager modell használatával egy belső terheléselosztó egy vagy több IP-címeket.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/06/2019
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: f7d14da6c7436120e013c979b108f61b82640d13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647883"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Egy vagy több Mindig rendelkezésre állási csoportfigyelőjének konfigurálása – Erőforrás-kezelő
Ez a témakör bemutatja, hogyan:

* Hozzon létre egy belső terheléselosztót az SQL Server rendelkezésre állási csoportjaihoz PowerShell-parancsmagok használatával.
* Adjon hozzá további IP-címeket egy terheléselosztóhoz egynél több rendelkezésre állási csoporthoz. 

A rendelkezésre állási csoport figyelőegy virtuális hálózati név, amelyhez az ügyfelek adatbázis-hozzáféréshez csatlakoznak. Az Azure virtuális gépeken egy terheléselosztó rendelkezik a figyelő IP-címét. A terheléselosztó a forgalmat a mintavételi porton figyelő SQL Server példányához irányítja. Általában egy rendelkezésre állási csoport belső terheléselosztót használ. Egy Azure belső terheléselosztó egy vagy több IP-címet is üzemeltethet. Minden IP-cím egy adott mintavételi portot használ. Ez a dokumentum bemutatja, hogyan hozhat létre a PowerShell t, és hogyan adhat hozzá IP-címeket az SQL Server rendelkezésre állási csoportjaihoz meglévő terheléselosztóhoz. 

Az a képesség, hogy több IP-cím eket rendeljen egy belső terheléselosztóhoz, új az Azure-ban, és csak az Erőforrás-kezelő modellben érhető el. A feladat végrehajtásához egy SQL Server rendelkezésre állási csoport üzembe helyezéséhez az Azure virtuális gépek en Resource Manager modellben. Mindkét SQL Server virtuális gépnek ugyanahhoz a rendelkezésre állási csoporthoz kell tartoznia. A [Microsoft-sablon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) segítségével automatikusan létrehozhatja a rendelkezésre állási csoportot az Azure Resource Managerben. Ez a sablon automatikusan létrehozza a rendelkezésre állási csoportot, beleértve a belső terheléselosztót is. Ha szeretné, [manuálisan is konfigurálhatja az Mindig bekapcsolva állási csoportot.](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

Ez a témakör megköveteli, hogy a rendelkezésre állási csoportok már konfigurálva vannak.  

Kapcsolódó témák a következők:

* [AlwaysOn rendelkezésre állási csoportok konfigurálása az Azure virtuális gépben (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Virtuális hálózatok közötti kapcsolat létrehozása az Azure Resource Manager és a PowerShell használatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>A PowerShell-verzió ellenőrzése

Ebben a cikkben szereplő példák at az Azure PowerShell-modul 5.4.1-es verziójával teszteljük.

Ellenőrizze, hogy a PowerShell-modul 5.4.1-es vagy újabb.

Lásd: [Az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps)

## <a name="configure-the-windows-firewall"></a>A Windows tűzfal konfigurálása

Konfigurálja úgy a Windows tűzfalat, hogy engedélyezze az SQL Server elérését. A tűzfalszabályok engedélyezik a TCP-kapcsolatokat az SQL Server-példány és a figyelő mintavétel által használt portokhoz. A részletes útmutatásról a [Windows tűzfal konfigurálása az adatbázis-kezelő motorokhoz című témakörben talál.](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1) Hozzon létre egy bejövő szabályt az SQL Server-porthoz és a mintavételi porthoz.

Ha korlátozza a hozzáférést egy Azure Network Security Group, győződjön meg arról, hogy az engedélyezési szabályok tartalmazzák a háttér-SQL Server virtuális gép IP-címek, és a terheléselosztó lebegő IP-címek az AG figyelő és a fürt alapvető IP-cím, ha alkalmazható.

## <a name="determine-the-load-balancer-sku-required"></a>Határozza meg a szükséges terheléselosztó termékváltozatát

[Az Azure terheléselosztó](../../../load-balancer/load-balancer-overview.md) 2 termékkészletben érhető el: alapszintű & standard. A szabványos terheléselosztó használata ajánlott. Ha a virtuális gépek egy rendelkezésre állási csoportban vannak, az alapszintű terheléselosztó megengedett. A standard terheléselosztó megköveteli, hogy minden virtuálisgép IP-cím szabványos IP-címeket használjon.

Az aktuális [Microsoft-sablon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) egy rendelkezésre állási csoporthoz egy alapvető TERHELÉSelosztót használ alapvető IP-címekkel.

Ebben a cikkben szereplő példák szabványos terheléselosztót határoznak meg. A példákban a `-sku Standard`parancsfájl tartalmazza a .

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Alapterhelés-elosztó létrehozásához távolítsa `-sku Standard` el a terheléselosztót létrehozó vonalról. Példa:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Példa parancsfájl: Hozzon létre egy belső terheléselosztót a PowerShell segítségével

> [!NOTE]
> Ha a [Microsoft-sablonnal](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)hozta létre a rendelkezésre állási csoportot, a belső terheléselosztó már létrejött.

A következő PowerShell-parancsfájl létrehoz egy belső terheléselosztót, konfigurálja a terheléselosztási szabályokat, és beállítja a terheléselosztó IP-címét. A parancsfájl futtatásához nyissa meg a Windows PowerShell ISE-t, és illessze be a parancsfájlt a Parancsfájl ablaktáblába. A `Connect-AzAccount` PowerShellbe való bejelentkezéshez használható. Ha több Azure-előfizetéssel `Select-AzSubscription` rendelkezik, az előfizetés beállításához használhatja. 

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

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a>Példa parancsfájl: IP-cím hozzáadása egy meglévő terheléselosztóhoz a PowerShell segítségével
Egynél több rendelkezésre állási csoport használatához adjon hozzá egy további IP-címet a terheléselosztóhoz. Minden IP-címhez saját terheléselosztási szabályra, mintavételi portra és elülső portra van szükség.

Az előtér-port az a port, amelyet az alkalmazások az SQL Server-példányhoz való csatlakozáshoz használnak. A különböző rendelkezésre állási csoportok IP-címei ugyanazt az előtér-portot használhatják.

> [!NOTE]
> Az SQL Server rendelkezésre állási csoportjaihoz minden IP-címhez egy adott mintavételi port szükséges. Ha például egy terheléselosztó egyik IP-címe az 59999-es mintavételi portot használja, akkor az adott terheléselosztómás IP-címei nem használhatják az 59999-es mintavételi portot.

* A terheléselosztó-korlátokról a **Privát előtér-IP/terheléselosztó** [a Hálózati korlátok – Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)című témakörben talál.
* A rendelkezésre állási csoport korlátairól a [Korlátozások (elérhetőségi csoportok)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG)című témakörben talál további információt.

A következő parancsfájl új IP-címet ad hozzá egy meglévő terheléselosztóhoz. Az ILB a figyelőportot használja a terheléselosztási előtér-porthoz. Ez a port lehet az a port, amelyet az SQL Server figyel. Az SQL Server alapértelmezett példányai esetén a port 1433. Egy rendelkezésre állási csoport terheléselosztási szabálya lebegő IP-címet (közvetlen kiszolgáló-visszatérést) igényel, így a háttérport megegyezik az előtér-portéval. Frissítse a környezet változóit. 

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

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>A figyelőport beállítása az SQL Server Management Studio-ban

1. Indítsa el az SQL Server Management Studio alkalmazást, és csatlakozzon az elsődleges kópiához.

1. Keresse meg **az AlwaysOn magas rendelkezésre állási** | **rendelkezésre állási csoportok** | **rendelkezésre állási csoportfigyelőit.** 

1. Most látnia kell a feladatátvevői fürtkezelőben létrehozott figyelőnevet. Kattintson a jobb gombbal a figyelő nevére, és válassza a **Tulajdonságok parancsot.**

1. A **Port** mezőben adja meg a rendelkezésre állási csoportfigyelő portszámát a korábban használt $EndpointPort (1433 volt az alapértelmezett), majd kattintson az **OK**gombra.

## <a name="test-the-connection-to-the-listener"></a>A figyelővel való kapcsolat tesztelése

A kapcsolat tesztelése:

1. RDP egy SQL Server, amely ugyanabban a virtuális hálózatban, de nem a replika. Ez lehet a fürt másik SQL Server kiszolgálója.

1. A kapcsolat teszteléséhez használja az **sqlcmd** segédprogramot. A következő parancsfájl például **sqlcmd** kapcsolatot hoz létre az elsődleges kópiával a figyelőn keresztül a Windows-hitelesítéssel:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Ha a figyelő nem az alapértelmezett portot (1433) használja, adja meg a portot a kapcsolati karakterláncban. Például a következő sqlcmd parancs csatlakozik egy figyelőhöz az 1435-ös porton: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Az SQLCMD-kapcsolat automatikusan csatlakozik az elsődleges replikát az SQL Server bármelyik példányához. 

> [!NOTE]
> Győződjön meg arról, hogy a megadott port mindkét SQL-kiszolgáló tűzfalán meg van nyitva. Mindkét kiszolgálónak bejövő szabályra van szüksége a használt TCP-porthoz. További információt [a Tűzfalszabály hozzáadása és szerkesztése](https://technet.microsoft.com/library/cc753558.aspx) című témakörben talál. 
> 
> 

## <a name="guidelines-and-limitations"></a>Irányelvek és korlátozások
Vegye figyelembe az alábbi irányelveket a rendelkezésre állási csoport figyelő az Azure-ban belső terheléselosztó használatával:

* Egy belső terheléselosztó, csak a figyelő ugyanabból a virtuális hálózatból érhető el.

* Ha korlátozza a hozzáférést egy Azure Network Security Group, győződjön meg arról, hogy az engedélyezési szabályok tartalmazzák a háttér-SQL Server virtuális gép IP-címek, és a terheléselosztó lebegő IP-címek az AG figyelő és a fürt alapvető IP-cím, ha alkalmazható.

## <a name="for-more-information"></a>További tudnivalók
További információ: [A Mindig rendelkezésre állási csoport konfigurálása az Azure virtuális gép manuálisan.](virtual-machines-windows-portal-sql-availability-group-tutorial.md)

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok
A következő PowerShell-parancsmagok használatával hozzon létre egy belső terheléselosztót az Azure virtuális gépekhez.

* [A New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) terheléselosztót hoz létre. 
* [A New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) előtér-IP-konfigurációt hoz létre a terheléselosztószámára. 
* [A New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) szabálykonfigurációt hoz létre egy terheléselosztóhoz. 
* [A New-AzLoadBalancerBackendAddressPoolig](https://msdn.microsoft.com/library/mt603791.aspx) háttércímkészlet-konfigurációt hoz létre egy terheléselosztószámára. 
* [A New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) egy terheléselosztó mintavételi konfigurációját hozza létre.
* [Az Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) eltávolítja a terheléselosztót egy Azure-erőforráscsoportból.
