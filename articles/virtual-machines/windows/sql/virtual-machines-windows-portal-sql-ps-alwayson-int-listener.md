---
title: Rendelkezésre állási csoport figyelők konfigurálása & Load Balancer (PowerShell)
description: Konfigurálja a rendelkezésre állási csoport figyelőit a Azure Resource Manager modellen egy belső terheléselosztó használatával egy vagy több IP-címmel.
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
ms.openlocfilehash: cabfc84d2bc0c9d08a457e67c0182d7550f04ceb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80668888"
---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manager"></a>Egy vagy több always on rendelkezésre állási csoport figyelők konfigurálása – Resource Manager
Ez a témakör a következőket mutatja be:

* Hozzon létre belső terheléselosztó SQL Server rendelkezésre állási csoportok számára a PowerShell-parancsmagok használatával.
* További IP-címeket adhat hozzá egy terheléselosztó számára egynél több rendelkezésre állási csoport számára. 

A rendelkezésre állási csoport figyelője olyan virtuális hálózat neve, amelyet az ügyfelek az adatbázis-hozzáféréshez csatlakoznak. Az Azure-beli virtuális gépeken a terheléselosztó a figyelő IP-címét tárolja. A terheléselosztó átirányítja a forgalmat a mintavételi portot figyelő SQL Server példányára. A rendelkezésre állási csoport általában belső terheléselosztó használatával működik. Egy belső Azure Load Balancer egy vagy több IP-címet képes tárolni. Minden IP-cím egy adott mintavételi portot használ. Ebből a dokumentumból megtudhatja, hogyan használható a PowerShell egy terheléselosztó létrehozásához, illetve IP-címek egy meglévő terheléselosztó SQL Server rendelkezésre állási csoportok számára való hozzáadásához. 

Több IP-cím a belső terheléselosztó számára való hozzárendelésének lehetősége új az Azure-hoz, és csak a Resource Manager-modellben érhető el. A feladat végrehajtásához rendelkeznie kell egy, az Azure Virtual Machines szolgáltatásban üzembe helyezett SQL Server rendelkezésre állási csoport Resource Manager-modellben. Mindkét SQL Server virtuális gépnek ugyanahhoz a rendelkezésre állási csoporthoz kell tartoznia. A rendelkezésre állási csoport automatikus létrehozásához használhatja a [Microsoft-sablont](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) Azure Resource Manager. Ez a sablon automatikusan létrehozza a rendelkezésre állási csoportot, beleértve az Ön számára a belső terheléselosztó. Ha szeretné, [manuálisan is konfigurálhatja az Always On rendelkezésre állási csoportot](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Ehhez a témakörhöz a rendelkezésre állási csoportok konfigurálása szükséges.  

A kapcsolódó témakörök a következők:

* [AlwaysOn rendelkezésre állási csoportok konfigurálása Azure-beli virtuális gépen (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Virtuális hálózatok közötti kapcsolat létrehozása az Azure Resource Manager és a PowerShell használatával](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

[!INCLUDE [Start your PowerShell session](../../../../includes/sql-vm-powershell.md)]

## <a name="verify-powershell-version"></a>PowerShell-verzió ellenőrzése

A cikkben szereplő példák a Azure PowerShell modul 5.4.1-es verziójának használatával lettek tesztelve.

Győződjön meg arról, hogy a PowerShell-modul 5.4.1 vagy újabb.

Lásd: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="configure-the-windows-firewall"></a>A Windows tűzfal konfigurálása

Konfigurálja a Windows tűzfalat a SQL Server hozzáférésének engedélyezéséhez. A tűzfalszabályok lehetővé teszik, hogy a portok TCP-kapcsolatai a SQL Server-példány és a figyelő mintavétele által használhatók legyenek. Részletes útmutatásért lásd: [Windows tűzfal konfigurálása az adatbázismotor eléréséhez](https://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Hozzon létre egy bejövő szabályt a SQL Server portjához és a mintavételi porthoz.

Ha egy Azure hálózati biztonsági csoporttal korlátozza a hozzáférést, győződjön meg arról, hogy az engedélyezési szabályok közé tartozik a háttérrendszer SQL Server VM IP-címe, valamint az AG-figyelő terheléselosztási IP-címei és a fürt alapvető IP-címe, ha van ilyen.

## <a name="determine-the-load-balancer-sku-required"></a>Határozza meg a terheléselosztó SKU-jának követelményét

Az [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) 2 SKU-ban érhető el: alapszintű & standard. A standard Load Balancer használata ajánlott. Ha a virtuális gépek rendelkezésre állási csoportba tartoznak, az alapszintű Load Balancer használata engedélyezett. Ha a virtuális gépek rendelkezésre állási zónában találhatók, a standard Load Balancer szükséges. A standard Load Balancer megköveteli, hogy minden virtuális gép IP-címe standard IP-címet használjon.

Egy rendelkezésre állási csoport aktuális [Microsoft-sablonja](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) egy alapszintű Load balancert használ alapszintű IP-címekkel.

   > [!NOTE]
   > Ha standard Load balancert és Azure Storage-t használ a Felhőbeli tanúsító számára, konfigurálnia kell egy [szolgáltatási végpontot](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network) . 


A cikkben szereplő példák a standard Load balancert határozzák meg. A példákban a parancsfájl tartalmaz `-sku Standard`.

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe -sku Standard
```

Alapszintű terheléselosztó létrehozásához távolítsa `-sku Standard` el a terheléselosztó által létrehozott sorból. Például:

```powershell
$ILB= New-AzLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe
```

## <a name="example-script-create-an-internal-load-balancer-with-powershell"></a>Példa a parancsfájlra: belső terheléselosztó létrehozása a PowerShell-lel

> [!NOTE]
> Ha a rendelkezésre állási csoportot a [Microsoft-sablonnal](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)hozta létre, a belső terheléselosztó már létrejött.

A következő PowerShell-szkript létrehoz egy belső terheléselosztó-t, konfigurálja a terheléselosztási szabályokat, és beállítja a terheléselosztó IP-címét. A parancsfájl futtatásához nyissa meg Windows PowerShell integrált parancsprogram-kezelési környezet, majd illessze be a szkriptet a szkript ablaktáblába. A `Connect-AzAccount` paranccsal jelentkezhet be a powershellbe. Ha több Azure-előfizetéssel rendelkezik `Select-AzSubscription` , az előfizetés beállításához használja a következőt:. 

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

## <a name="example-script-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a><a name="Add-IP"></a>Példa parancsfájl: IP-cím hozzáadása meglévő terheléselosztó számára PowerShell-lel
Ha egynél több rendelkezésre állási csoportot szeretne használni, adjon hozzá egy további IP-címet a terheléselosztó számára. Minden IP-címnek szüksége van saját terheléselosztási szabályra, mintavételi portra és elülső portra.

Az előtér-port az a port, amelyet az alkalmazások a SQL Server-példányhoz való kapcsolódáshoz használnak. A különböző rendelkezésre állási csoportok IP-címei ugyanazt az előtér-portot használhatják.

> [!NOTE]
> SQL Server rendelkezésre állási csoportok esetében minden IP-címnek egy adott mintavételi portra van szüksége. Ha például egy terheléselosztó egyik IP-címe a 59999-es mintavételi portot használja, a terheléselosztó más IP-címei nem használhatják az 59999-es mintavételi portot.

* A terheléselosztó korlátaival kapcsolatos információkért lásd: **magánhálózati előtérbeli IP-cím/** terheléselosztó a [hálózati korlátok között – Azure Resource Manager](../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* További információ a rendelkezésre állási csoport korlátairól: [korlátozások (rendelkezésre állási csoportok)](https://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

A következő parancsfájl egy új IP-címet helyez el egy meglévő terheléselosztó számára. A ILB a figyelő portot használja a terheléselosztási előtér-porthoz. Ez a port lehet a SQL Server figyelő port. SQL Server alapértelmezett példányai esetében a port 1433. Egy rendelkezésre állási csoport terheléselosztási szabálya egy lebegőpontos IP-címet (közvetlen kiszolgáló-visszaküldést) igényel, így a háttér-port megegyezik az előtér-porttal. Frissítse a környezet változóit. 

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

## <a name="set-the-listener-port-in-sql-server-management-studio"></a>A figyelő portjának beállítása SQL Server Management Studio

1. Indítsa el SQL Server Management Studio és kapcsolódjon az elsődleges replikához.

1. Navigáljon a **magas rendelkezésre** | állási rendelkezésre állási**csoportok** | **rendelkezésre állási csoportjának figyelők**AlwaysOn. 

1. Ekkor megjelenik a Feladatátvevőfürt-kezelőban létrehozott figyelő neve. Kattintson a jobb gombbal a figyelő nevére, és kattintson a **Tulajdonságok**elemre.

1. A **port** mezőben adja meg a rendelkezésre állási csoport figyelő portszámát a korábban használt $EndpointPort használatával (1433 volt az alapértelmezett), majd kattintson az **OK**gombra.

## <a name="test-the-connection-to-the-listener"></a>A figyelővel létesített kapcsolatok tesztelése

A kapcsolódás tesztelése:

1. Az RDP-t egy olyan SQL Serverra, amely ugyanabban a virtuális hálózatban található, de nem tulajdonosa a replikának. Ez lehet a fürt más SQL Server.

1. A **Sqlcmd** segédprogram használatával tesztelheti a kapcsolódást. Az alábbi szkript például egy **Sqlcmd** kapcsolatot létesít az elsődleges replikával a figyelőn keresztül a Windows-hitelesítéssel:
   
    ```
    sqlcmd -S <listenerName> -E
    ```
   
    Ha a figyelő nem az alapértelmezett portot (1433) használja, akkor a portot a csatlakozási karakterláncban kell megadni. Például a következő Sqlcmd-parancs csatlakozik egy figyelőhöz a 1435-as porton: 
   
    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

Az SQLCMD-kapcsolat automatikusan csatlakozik a SQL Server-példányhoz, amely az elsődleges replikát tárolja. 

> [!NOTE]
> Győződjön meg arról, hogy a megadott port meg van nyitva a tűzfalon mindkét SQL Server-kiszolgálón. Mindkét kiszolgálónak szüksége van egy bejövő szabályra a használt TCP-porthoz. További információért lásd: [Tűzfalszabályok hozzáadása vagy szerkesztése](https://technet.microsoft.com/library/cc753558.aspx) . 
> 
> 

## <a name="guidelines-and-limitations"></a>Irányelvek és korlátozások
Vegye figyelembe a következő irányelveket a rendelkezésre állási csoport figyelője az Azure-ban belső terheléselosztó használatával:

* Belső terheléselosztó esetén a figyelő csak ugyanazon a virtuális hálózaton belül érhető el.

* Ha egy Azure hálózati biztonsági csoporttal korlátozza a hozzáférést, győződjön meg arról, hogy az engedélyezési szabályok közé tartozik a háttérrendszer SQL Server VM IP-címe, valamint az AG-figyelő terheléselosztási IP-címei és a fürt alapvető IP-címe, ha van ilyen.

* Hozzon létre egy szolgáltatási végpontot, ha standard Load balancert használ a Felhőbeli tanúsító Azure Storage szolgáltatással. További információ: [hozzáférés engedélyezése virtuális hálózatról](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

## <a name="for-more-information"></a>További tudnivalók
További információ: [Always On rendelkezésre állási csoport konfigurálása az Azure-beli virtuális gépen manuálisan](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok
Az alábbi PowerShell-parancsmagokkal hozzon létre egy belső Load balancert az Azure Virtual Machines szolgáltatáshoz.

* A [New-AzLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) létrehoz egy Load balancert. 
* A [New-AzLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) létrehoz egy ELŐTÉR-IP-konfigurációt a terheléselosztó számára. 
* A [New-AzLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) létrehoz egy szabálykészlet-konfigurációt a terheléselosztó számára. 
* A [New-AzLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) létrehoz egy háttér-címkészlet konfigurációját a terheléselosztó számára. 
* A [New-AzLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) létrehoz egy mintavételi konfigurációt a terheléselosztó számára.
* A [Remove-AzLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) eltávolítja a Load balancert egy Azure-erőforráscsoporthoz.
