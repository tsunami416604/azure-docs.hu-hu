---
title: "Konfigurálja, hogy az mindig a rendelkezésre állási csoport figyelője – a Microsoft Azure |} Microsoft Docs"
description: "Rendelkezésre állási csoport figyelői konfigurálása az Azure Resource Manager modellt, a belső terheléselosztók használatával egy vagy több IP-címekkel rendelkező."
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: 14b39cde-311c-4ddf-98f3-8694e01a7d3b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/22/2017
ms.author: mikeray
ms.openlocfilehash: 74fa1e4c9cfa608a9a385f3dd82a0599fbcc421c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Egy vagy több Always On rendelkezésre állási csoport figyelői - erőforrás-kezelő konfigurálása
Ez a témakör bemutatja, hogyan:

* Hozzon létre egy belső elosztott terhelésű az SQL Server rendelkezésre állási csoportok PowerShell-parancsmagok használatával.
* További IP-címek hozzáadása a terheléselosztó több rendelkezésre állási csoporthoz. 

Egy rendelkezésre állási csoport figyelőjének egy virtuális hálózat neve, amely az ügyfelek kapcsolódnak az adatbázis eléréséhez. Az Azure virtuális gépeken a terheléselosztó a figyelő az IP-címet tartalmazza. A load balancer irányítja a forgalmat, hogy a mintavételi portot nem SQL Server példányához. Általában a rendelkezésre állási csoport belső terheléselosztót használja. Az Azure belső terheléselosztót rendelkezhet egy vagy több IP-címeket. Minden IP-cím egy adott mintavételi portot használja. Ez a dokumentum bemutatja, hogyan lehet PowerShell használatával egy terheléselosztó létrehozása vagy IP-címek hozzáadása a meglévő terheléselosztó az SQL Server rendelkezésre állási csoport. 

Több IP-címek kiosztása a belső terheléselosztók lehetőséget egy új Azure-ba, és csak érhető el a Resource Manager modellt. Ez a feladat befejezéséhez szükség lehet a Resource Manager modellt az Azure virtuális gépeken telepített SQL Server rendelkezésre állási csoport. Mindkét SQL Server virtuális gépek ugyanabban a rendelkezésre állási csoportba kell tartoznia. Használhatja a [Microsoft sablon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) automatikusan a rendelkezésre állási csoport létrehozásához az Azure Resource Manager. Ez a sablon automatikusan létrehozza a rendelkezésre állási csoport, beleértve a belső terheléselosztó meg. Ha kívánja, akkor [kézzel konfigurálásához az Always On rendelkezésre állási csoport](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Ez a témakör megköveteli, hogy a rendelkezésre állási csoportok már be van állítva.  

Kapcsolódó témakörök az alábbiak:

* [AlwaysOn rendelkezésre állási csoportok konfigurálása Azure virtuális gépen (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Egy VNet – VNet-kapcsolat beállítása az Azure Resource Manager és a PowerShell használatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>A Windows tűzfal konfigurálása
SQL Server-hozzáférés engedélyezése a Windows tűzfal konfigurálása. A tűzfalszabályok engedélyezése a portokat használja a figyelő mintavétel, valamint az SQL Server-példány TCP-kapcsolatot. Részletes útmutatásért lásd: [beállítani a Windows tűzfalat a hozzáféréshez](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Az SQL Server portja és a mintavételi portot a bejövő szabályt létrehozni.

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Mintaparancsfájl: A belső terheléselosztók létrehozása a PowerShell használatával
> [!NOTE]
> Ha létrehozta a rendelkezésre állási csoportban található, a [Microsoft sablon](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), a belső terheléselosztó már létre lett hozva. 
> 
> 

A következő PowerShell-parancsfájlt hoz létre a belső terheléselosztók, konfigurálja a terheléselosztási szabályok, és beállítja az IP-címet a terheléselosztóhoz. A parancsfájl futtatásához nyissa meg a Windows PowerShell ISE, és illessze be a parancsfájlt a parancssori panelbe. Használjon `Login-AzureRMAccount` próbál bejelentkezni a PowerShell. Ha több Azure-előfizetéssel rendelkezik, `Select-AzureRmSubscription ` az előfizetés beállításához. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

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

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($vm.NetworkProfile.NetworkInterfaces.Id.split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="Add-IP"></a>Mintaparancsfájl: IP-cím hozzáadása a meglévő terheléselosztó a PowerShell használatával
Egynél több rendelkezésre állási csoport, vegye fel egy további IP-címet a terheléselosztóhoz. Minden IP-cím a saját terheléselosztási szabály, a mintavételi portot és az első port szükséges.

Az előtér-portot használja a portot, amelyet az SQL Server-példány való csatlakozáskor használandó alkalmazásokat. Másik rendelkezésre állási csoportok IP-címet a azonos előtér-portot is használhat.

> [!NOTE]
> SQL Server rendelkezésre állási csoportok minden IP-cím szükséges egy adott mintavételi portot. Például ha egy IP-címet a terheléselosztóhoz 59999 mintavételi portot használ, nincs más IP-címeit, hogy a terheléselosztó mintavételi portot 59999 is használhat.

* A load balancer korlátok kapcsolatos információkért lásd: **terheléselosztó magánhálózati front-end IP-cím** alatt [hálózatkezelés korlátok - Azure Resource Manager](../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* További információ a rendelkezésre állási csoport korlátok: [korlátozások (rendelkezésre állási csoportok)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

A következő parancsfájl egy új IP-cím hozzáadása a meglévő terheléselosztó. A Példánynak a terheléselosztási előtér-portot figyelő-portot használja. Ezt a portot a portot, amelyet az SQL-kiszolgáló figyel a következőn lehet. Az alapértelmezett példány az SQL Server a port az 1433-as. A terheléselosztási szabály a rendelkezésre állási csoport hozni egy fix IP-cím (közvetlen kiszolgálói válasz), így a háttér-port nem azonos az előtér-port. A változók a környezet frissítése. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
```

## <a name="configure-the-listener"></a>A figyelő konfigurálása

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>A figyelő port beállítása az SQL Server Management Studio

1. Indítsa el az SQL Server Management Studio eszközt, és kapcsolódjon az elsődleges másodpéldány.

1. Navigáljon a **AlwaysOn magas rendelkezésre állás** | **rendelkezésre állási csoportok** | **rendelkezésre állási csoport figyelői**. 

1. Meg kell jelennie a figyelő nevét, amelyet a Feladatátvevőfürt-kezelőt hozott létre. Kattintson a jobb gombbal a figyelő nevét, és kattintson a **tulajdonságok**.

1. Az a **Port** mezőben adja meg a portszámot az elérhetőségi csoport figyelője az a korábban használt $EndpointPort használatával (az alapértelmezett 1433-as volt az), majd kattintson a **OK**.

## <a name="test-the-connection-to-the-listener"></a>Tesztelje a kapcsolatot a figyelő

A kapcsolat ellenőrzéséhez:

1. RDP egy SQL Server ugyanazon virtuális hálózatban, de nem tulajdonosa a replikát. Ez lehet a más SQL Server a fürtben.

1. Használjon **sqlcmd** segédprogram létrehozott kapcsolat ellenőrzéséhez. Például az alábbi parancsfájlt hoz létre egy **sqlcmd** kapcsolatot az elsődleges másodpéldány, a figyelő a Windows-hitelesítés használatával:
   
    ```
    sqlmd -S <listenerName> -E
    ```
   
    Ha a figyelő az alapértelmezettől eltérő portot használ (1433) portot, adja meg a portot a kapcsolati karakterláncban. A következő sqlcmd paranccsal például egy figyelő 1435 porton csatlakozik: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Az SQLCMD kapcsolat automatikusan csatlakozik bármely SQL Server-példányt az elsődleges replikát. 

> [!NOTE]
> Győződjön meg arról, hogy a megadott port meg nyitva a tűzfalon az mindkét SQL-kiszolgálók. A TCP-portot, amelyekkel egy bejövő forgalomra vonatkozó szabály mindkét kiszolgáló szükséges. Lásd: [hozzáadása vagy szerkesztése tűzfalszabály](http://technet.microsoft.com/library/cc753558.aspx) további információt. 
> 
> 

## <a name="guidelines-and-limitations"></a>Irányelvek és korlátozások
Vegye figyelembe a következő irányelveket a rendelkezésre állási csoport figyelőjének az Azure-ban a belső terheléselosztó:

* A belső terheléselosztót csak érhető el a figyelő a virtuális hálózaton belül.


## <a name="for-more-information"></a>További információ
További információkért lásd: [beállítása Always On rendelkezésre állási csoport az Azure virtuális gép manuálisan](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok
A következő PowerShell-parancsmagok használatával hozzon létre az Azure virtuális gépek belső terheléselosztót.

* [Új AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) új terheléselosztó létrehozása. 
* [Új AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) hoz létre egy terheléselosztó előtér-IP-konfigurációja. 
* [Új AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) hoz létre egy terheléselosztó szabály konfigurációját. 
* [Új AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) hoz létre egy háttér címkészlet beállítása a terheléselosztóhoz. 
* [Új AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) hoz létre egy terhelés-kiegyenlítő mintavételi konfigurációját.
* [Remove-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx) terheléselosztó eltávolítása egy Azure-erőforráscsoportot.
