---
title: Az SAP több biztonsági AZONOSÍTÓVAL konfiguráció létrehozása az Azure-ban |} A Microsoft Docs
description: Útmutató a magas rendelkezésre állású SAP NetWeaver több biztonsági AZONOSÍTÓVAL konfigurálása a Windows virtuális gépek
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fe9b70d74e326166afae366becc47fbcc8b2ea56
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58846695"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Az SAP NetWeaver több biztonsági AZONOSÍTÓVAL konfigurációjának létrehozása

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

2016 szeptemberétől a Microsoft nyilvánosan egyik funkciója, ahol több virtuális IP-címek kezelheti az Azure belső terheléselosztó használatával. Ez a funkció már szerepel az Azure külső load balancert.

Ha rendelkezik egy SAP üzemelő példányt, segítségével belső terheléselosztó létrehozása a Windows-fürt konfigurációját az SAP ASCS/SCS, leírtak szerint a [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken] [ sap-ha-guide].

Ez a cikk foglalkozik, az SAP több biztonsági AZONOSÍTÓVAL konfigurációjához további SAP ASCS/SCS fürtözött példányok telepít egy meglévő Windows Server feladatátvételi fürtszolgáltatási (WSFC) fürthöz való áthelyezéséhez egy egyetlen ASCS/SCS-telepítésből. Ez a folyamat befejezése után fog egy SAP több biztonsági AZONOSÍTÓVAL fürthöz konfigurált.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
Már konfigurált egy WSFC-fürt, amely egy SAP ASCS/SCS példányhoz, szolgál az a [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken] [ sap-ha-guide] és ezen az ábrán látható módon.

![Magas rendelkezésre állású SAP ASCS/SCS példányhoz][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Cél-architektúra

A célja, hogy több SAP ABAP ASCS telepítse, vagy SAP Java SCS fürtözött példányok WSFC fürtön, ábrákkal szemléltetett itt:

![Több SAP ASCS/SCS fürtözött példány az Azure-ban][sap-ha-guide-figure-6002]

> [!NOTE]
>Minden egyes Azure belső terheléselosztó magánhálózati előtérbeli IP-címek száma korlátozva van.
>
>Egy WSFC-fürt SAP ASCS/SCS-példányok maximális száma megegyezik a minden Azure belső terheléselosztó magánhálózati előtérbeli IP-címek maximális számát.
>

Load balancer korlátok kapcsolatos további információkért lásd: "privát előtéri IP-Címek száma load balancer" a [hálózati korlátai: Az Azure Resource Manager][networking-limits-azure-resource-manager].

A teljes fekvő két magas rendelkezésre állású SAP-rendszerek a következő lenne:

![SAP magas rendelkezésre állású több SID-vel telepítő két SAP-rendszerrel SID-k][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> A telepítő a következő feltételeknek kell megfelelniük:
> - Az SAP ASCS/SCS-példányokat kell osztania a WSFC-fürtön.
> - Minden egyes adatbázis-kezelő SID rendelkeznie kell a saját dedikált WSFC-fürtre.
> - SAP-alkalmazáskiszolgálókhoz, amely egy SAP-rendszerhez SID tartozik a saját dedikált virtuális gépet kell tartalmaznia.


## <a name="prepare-the-infrastructure"></a>Az infrastruktúra előkészítése
Az infrastruktúra előkészítése, egy további SAP ASCS/SCS-példányt is telepíthet a következő paraméterekkel:

| Paraméter neve | Érték |
| --- | --- |
| SAP ASCS/SCS SID |PR1-lb-ascs |
| Az SAP DBMS belső load balancer | PR5 |
| SAP virtuális állomás neve | pr5-sap-cl |
| Az SAP ASCS/SCS virtuális gazdagép IP-cím (további Azure load balancer IP-cím) | 10.0.0.50 |
| Az SAP ASCS/SCS-példányok száma | 50 |
| ILB-mintavétel port további SAP ASCS/SCS példányhoz | 62350 |

> [!NOTE]
> Az SAP ASCS/SCS példányok minden IP-cím szükséges egyedi mintavételi port. Például egy, az Azure belső terheléselosztó IP-cím 62300 mintavételi portot használ, nincs más IP-címet a terheléselosztó a mintavételi portot 62300 használhatja.
>
>A jelen 62300 mintavételi port már foglalt, mert használjuk 62350 mintavételi portot.

További SAP ASCS/SCS-példányok a meglévő WSFC-fürtben két csomópont telepítése:

| Virtuális gépi szerepkör | Virtuális gép állomásneve | Statikus IP-cím |
| --- | --- | --- |
| 1-től fürtcsomópont ASCS/SCS példányhoz |pr1-ascs-0 |10.0.0.10 |
| 2. fürtcsomópont ASCS/SCS példányhoz |PR1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Hozzon létre egy virtuális nevet a fürtözött SAP ASCS/SCS példányhoz a DNS-kiszolgálón

Létrehozhat egy DNS-bejegyzést a ASCS/SCS-példány virtuális állomás nevét a következő paraméterek használatával:

| Új SAP ASCS/SCS virtuális állomás neve | Társított IP-cím |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

Az új állomás neve és IP-cím jelenik meg a DNS-kezelőben az alábbi képernyőképen látható módon:

![A megadott DNS-bejegyzés esetében az új SAP ASCS/SCS kiemelés DNS-kezelő lista fürt virtuális neve és a TCP/IP-cím][sap-ha-guide-figure-6004]

DNS-bejegyzés létrehozása is ismerteti részletesen ismertetjük a fő [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken][sap-ha-guide-9.1.1].

> [!NOTE]
> Az új IP-címet rendel a további példányának ASCS/SCS virtuális állomás neve ugyanaz, mint az SAP az Azure load balancer rendelt új IP-címet kell lennie.
>
>Ebben az esetben az IP-cím 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>IP-cím hozzáadása egy meglévő Azure belső terheléselosztó a PowerShell használatával

Szeretne létrehozni egynél több SAP ASCS/SCS példányhoz a WSFC-fürtön, a PowerShell használatával ad hozzá egy meglévő Azure belső terheléselosztó IP-címet. Minden IP-cím a saját terheléselosztási szabályok, a mintavételi portot, az előtérbeli IP-címkészlet és a háttérkészlet igényel.

A következő parancsfájl egy új IP-címet ad hozzá egy meglévő a terheléselosztóhoz. Frissítés a PowerShell változók környezete számára. A parancsfájl létrehozza az összes szükséges terheléselosztási szabályok minden SAP ASCS/SCS-porthoz.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Miután a parancsfájl lefutott, az eredményeket az Azure Portalon jelennek meg az alábbi képernyőképen látható módon:

![Új előtérbeli IP-címkészlet az Azure Portalon][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Lemezek hozzáadása fürthöz gépekre, és konfigurálja az SIOS fürtlemez-megosztás

Hozzá kell adnia egy új megosztást fürtlemez minden további SAP ASCS/SCS példányhoz. A Windows Server 2012 R2 a WSFC megosztás fürtlemez jelenleg használatban az SIOS DataKeeper szoftvert megoldás.

Tegye a következőket:
1. Egy további lemezt vagy (amely a stripe-kell) azonos méretű lemezek hozzáadása a fürt csomópontok, és formázhatja őket.
2. Az SIOS DataKeeper tárreplikáció konfigurálja.

Ez az eljárás feltételezi, hogy már telepítette az SIOS DataKeeper a WSFC-fürt gépeken. Ha már telepítette azt, a gépek közötti replikáció most konfigurálnia kell. A folyamat fő részletes leírása a [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken][sap-ha-guide-8.12.3.3].  

![A szinkron tükrözés, az új SAP ASCS/SCS a lemez megosztása a DataKeeper][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>SAP-alkalmazáskiszolgálók és az adatbázis-kezelő fürt a virtuális gépek üzembe helyezése

Az infrastruktúra előkészítése a második SAP-rendszer végrehajtásához tegye a következőket:

1. Az SAP-alkalmazáskiszolgálókhoz dedikált virtuális gépek üzembe helyezése, és azokat a saját dedikált rendelkezésre állási csoportban.
2. Az adatbázis-kezelő fürt számára dedikált virtuális gépek üzembe helyezése, és azokat a saját dedikált rendelkezésre állási csoportban.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>A második SAP NetWeaver-SID2 rendszer telepítése

A teljes folyamat egy második SAP SID2-rendszer telepítését a fő leírt [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken][sap-ha-guide-9].

A magas szintű eljárás a következőképpen történik:

1. [Telepítse az SAP első fürtcsomópontra][sap-ha-guide-9.1.2].  
 Ebben a lépésben telepíti az SAP egy magas rendelkezésre állású ASCS/SCS-példányt a **létező WSFC-fürt csomópontja 1**.

2. [Az SAP ASCS/SCS-példány módosításához][sap-ha-guide-9.1.3].

3. [A mintavételi port konfigurálása][sap-ha-guide-9.1.4].  
 Ebben a lépésben PowerShell-lel konfigurál egy SAP-erőforrás SAP-SID2-IP mintavételi portot. Hajtsa végre ezt a konfigurációt az SAP ASCS/SCS fürtcsomópontok egyike.

4. [Az adatbázis-példány telepítése] [sap-magas rendelkezésre állás-guide-9.2].  
 Ebben a lépésben telepíti az adatbázis-kezelő egy dedikált WSFC-fürtön.

5. [A második fürtcsomópontra install] [sap-magas rendelkezésre állás-guide-9.3].  
 Ebben a lépésben telepíti, ha egy magas rendelkezésre állású ASCS/SCS példánya a létező WSFC-fürt csomópont 2 SAP.

6. Nyissa meg a Windows tűzfal portjainak az SAP ASCS/SCS példányhoz és ProbePort.  
 Mindkét fürtcsomópont által használt SAP ASCS/SCS-példányok, az SAP ASCS/SCS használt összes Windows tűzfal portok nyitja meg. Ezeket a portokat szerepelnek a [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken][sap-ha-guide-8.8].  
 A belső Azure load balancer mintavételi portot, amely 62350 ebben az esetben is megnyithatja.

7. [Módosíthatja a az SAP on felhasználók Windows-példányok][sap-ha-guide-9.4].

8. [Az SAP elsődleges alkalmazáskiszolgáló telepítése] [ sap-ha-guide-9.5] az új dedikált virtuális Gépet.

9. [További SAP alkalmazáskiszolgáló telepítése] [ sap-ha-guide-9.6] az új dedikált virtuális Gépet.

10. [Tesztelje az SAP ASCS/SCS-példány feladatátvevő és SIOS replikációs][sap-ha-guide-10].

## <a name="next-steps"></a>További lépések

- [Hálózatkezelési korlátok: Az Azure Resource Manager][networking-limits-azure-resource-manager]
- [Több virtuális IP-címek az Azure Load Balancer][load-balancer-multivip-overview]
- [Útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken][sap-ha-guide]
