---
title: "Egy SAP multi-SID-konfiguráció létrehozása az Azure-ban |} Microsoft Docs"
description: "Útmutató a magas rendelkezésre állású SAP NetWeaver multi-SID-konfigurációs Windows virtuális gépek"
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b48df78df9f53ac7bf0804f55a8d36a2fe2f86b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Egy SAP NetWeaver multi-SID-konfiguráció létrehozása

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

2016 szeptemberétől a Microsoft, amely egy szolgáltatás, ahol több virtuális IP-cím kezelheti az Azure belső terheléselosztó használatával. Ez a funkció már létezik az Azure külső terheléselosztó.

Ha egy SAP üzemelő példányt, segítségével egy belső elosztott terhelésű létrehozása a Windows-fürt konfigurációjába a SAP ASC/SCS, ahogy a [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken] [ sap-ha-guide].

Ez a cikk a egyetlen ASC/SCS telepítés áthelyezése egy SAP multi-SID-konfiguráció további SAP ASC/SCS fürtözött példány telepítésével, a meglévő Windows Server feladatátvételi fürtszolgáltatási (WSFC) fürt összpontosít. Ez a folyamat befejezése után fog konfigurálta az SAP multi-SID-fürtöt.


## <a name="prerequisites"></a>Előfeltételek
Már konfigurálta a WSFC-fürt, amely egy SAP ASC/SCS példány szolgál a leírtaknak megfelelően a [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken] [ sap-ha-guide] és ezen a diagramon látható módon.

![Magas rendelkezésre állású SAP ASC/SCS példány][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Tároló-architektúra

A cél az, hogy több SAP ABAP ASC telepítéséhez, vagy SAP Java SCS fürtözött példánya a azonos WSFC-fürtre, ábrákkal szemléltetett itt:

![Több SAP ASC/SCS fürtözött példánya, az Azure-ban][sap-ha-guide-figure-6002]

> [!NOTE]
>Személyes előtér-IP-címek minden Azure belső terheléselosztóhoz száma korlátozva van.
>
>Egy WSFC-fürtön SAP ASC/SCS példányok maximális száma megegyezik a titkos előtér-IP-címek minden Azure belső terheléselosztóhoz maximális számát.
>

Terheléselosztó korlátok kapcsolatos további információkért lásd: "magánhálózati front-end IP-cím terheléselosztó" a [hálózatkezelés korlátok: Azure Resource Manager][networking-limits-azure-resource-manager].

A teljes fekvő két magas rendelkezésre állású SAP rendszerrel néz ki:

![SAP magas rendelkezésre állású multi-SID telepítése két SAP rendszer SID-k][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> A telepítő a következő feltételeknek kell megfelelniük:
> - A SAP ASC/SCS-példány kell osztozik ugyanazon a WSFC-fürtre.
> - Minden adatbázis-kezelő SID rendelkeznie kell a saját dedikált WSFC-fürtre.
> - Egy SAP rendszer SID tartozó SAP alkalmazáskiszolgálók rendelkeznie kell a saját dedikált virtuális gépek.


## <a name="prepare-the-infrastructure"></a>Az infrastruktúra előkészítése
Készítse elő az infrastruktúrát, egy további SAP ASC/SCS példányát telepítheti a következő paraméterekkel:

| Paraméter neve | Érték |
| --- | --- |
| SAP ASC/SCS SID |PR1-lb-ASC |
| SAP DBMS belső terheléselosztó | PR5 |
| SAP virtuális állomás neve | pr5-sap-cl |
| SAP ASC/SCS virtuális gazdagép IP-címe (további Azure load balancer IP-címe) | 10.0.0.50 |
| SAP ASC/SCS példányszámának | 50 |
| ILB mintavételi portot további SAP ASC/SCS-példány | 62350 |

> [!NOTE]
> Az SAP ASC/SCS példányokat a minden IP-cím csak egy egyedi mintavételi portot. Például egy IP-cím, egy Azure belső terheléselosztón 62300 mintavételi portot használ, ha nincs más IP-címet a terheléselosztóhoz mintavételi portot 62300 is használhat.
>
>A célokra mintavételi portot 62300 már foglalt, mert használjuk 62350 mintavételi portot.

SAP ASC/SCS további példányokat is telepíthet a létező WSFC-fürtön két csomóponttal rendelkező:

| Virtuálisgép-szerepkör | Virtuális gép állomásneve | Statikus IP-cím |
| --- | --- | --- |
| 1. fürtcsomópont ASC/SCS-példány |PR1-ASC-0 |10.0.0.10 |
| 2. fürtcsomópont ASC/SCS-példány |PR1-ASC-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Hozzon létre egy virtuális nevet az SAP ASC/SCS fürtözött példány a DNS-kiszolgálón

A következő paraméterek használatával létrehozhat egy DNS-bejegyzést a ASC/SCS példányának virtuális állomás neve:

| Új SAP ASC/SCS virtuális állomás neve | Társított IP-cím |
| --- | --- | --- |
|pr5-sap-cl |10.0.0.50 |

Az új állomás neve és IP-cím jelennek meg a DNS-kezelőben, az alábbi képernyőfelvételen látható módon:

![A megadott DNS-bejegyzés esetében az új SAP ASC/SCS kiemelése DNS-kezelő lista fürt virtuális nevét és a TCP/IP-cím][sap-ha-guide-figure-6004]

A DNS-bejegyzés létrehozása is fő részletes leírását lásd [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken][sap-ha-guide-9.1.1].

> [!NOTE]
> Új IP-címet rendel a virtuális gazdagép további ASC/SCS példány nevét az új IP-címként a SAP Azure load balancer rendelt azonosnak kell lennie.
>
>A mi esetünkben az IP-cím 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>IP-cím hozzáadása egy meglévő Azure belső terheléselosztót a PowerShell használatával

SAP ASC/SCS egynél több példány létrehozásához az ugyanazon a WSFC-fürtre, a PowerShell használatával IP-cím hozzáadása egy meglévő Azure belső terheléselosztót. Minden IP-címet igényel a saját terheléselosztási szabályok, a mintavételi portot, az előtér-IP-készlet és a háttér-készlet.

A következő parancsfájl egy új IP-cím hozzáadása a meglévő terheléselosztó. A környezet PowerShell változói frissítése. A parancsfájl minden SAP ASC/SCS port valamennyi szükséges terheléselosztási szabályokat hoz létre.

```powershell

# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzureRmLoadBalancer

# Get new updated configuration
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzureRmLoadBalancer

# Get new updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        #start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzureRmLoadBalancer

Write-Host "Succesfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Miután a parancsfájl lefutott, az eredményeket az Azure-portálon jelennek meg az alábbi képernyőfelvételen látható módon:

![Az Azure portálon található új előtér-IP-készlet][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Lemezek hozzáadása a fürtbeli gépeken, és konfigurálja a SIOS fürtlemez-megosztás

Hozzá kell adnia egy új megosztás fürtlemez minden további SAP ASC/SCS-példányhoz. A Windows Server 2012 R2, a WSFC megosztás fürtlemez jelenleg használatban, a SIOS DataKeeper szoftveres megoldás.

Tegye a következőket:
1. Az egyes fürtcsomópontokon egy további lemezt vagy lemezeket (amely kell paritásos) azonos méretűnek hozzáadása, és formázni őket.
2. Storage replikáció konfigurálását a SIOS DataKeeper.

Ez az eljárás feltételezi, hogy még telepítette SIOS DataKeeper a WSFC-fürt gépeken. Ha már telepítette azt, a gépek közötti replikáció most konfigurálnia kell. A folyamat fő részletes leírását lásd [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken][sap-ha-guide-8.12.3.3].  

![A szinkron tükrözés az új SAP ASC/SCS a lemez megosztása a DataKeeper][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Virtuális gépek SAP alkalmazáskiszolgálókra és az adatbázis-kezelő fürt üzembe helyezése

Az infrastruktúra előkészítése, a második SAP rendszer befejezéséhez tegye a következőket:

1. Az SAP alkalmazáskiszolgálók dedikált virtuális gépek telepítése, és azokat a saját dedikált rendelkezésre állási csoportban.
2. Adatbázis-kezelő fürt dedikált virtuális gépek telepítése, és azokat a saját dedikált rendelkezésre állási csoportban.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>A második SAP SID2 NetWeaver rendszer telepítése

A teljes folyamat egy második SAP SID2 rendszer telepítését a fő ismertetett [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken][sap-ha-guide-9].

A magas szintű eljárás a következőképpen történik:

1. [Az SAP első fürtcsomópontra telepíteni][sap-ha-guide-9.1.2].  
 Ebben a lépésben telepíti az SAP, ha a magas rendelkezésre állású ASC/SCS példánya a **létező WSFC-fürt csomópont 1**.

2. [Az SAP módosításához ASC/SCS példány][sap-ha-guide-9.1.3].

3. [Konfigurálja a mintavételi portot][sap-ha-guide-9.1.4].  
 Ebben a lépésben a PowerShell használatával konfigurál egy SAP-erőforrás SAP-SID2-IP mintavételi portot. Hajtsa végre ezt a konfigurációt a SAP ASC/SCS fürtcsomópontok egyike.

4. [Az adatbázis-példány telepítése] [sap-magas rendelkezésre állású-útmutató-9.2].  
 Ebben a lépésben kívánja telepíteni az adatbázis-kezelő egy dedikált WSFC-fürtre.

5. [A második csomópont telepítés] [sap-magas rendelkezésre állású-útmutató-9.3].  
 Ebben a lépésben telepíti, a magas rendelkezésre állású ASC/SCS példánya a létező WSFC-fürt csomópont 2 SAP.

6. Nyissa meg a Windows tűzfal portjait a SAP ASC/SCS példányhoz, és ProbePort.  
 Mindkét fürtcsomóponton SAP ASC/SCS-példányok használt az összes Windows tűzfal portjainak SAP ASC/SCS használt nyitja meg. Ezek a portok láthatók a [útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken][sap-ha-guide-8.8].  
 Az Azure belső terheléselosztási terheléselosztó mintavételi portot, amely 62350 a mi esetünkben is megnyithatja.

7. [Módosítsa az indítási típus az SAP SSZON Windows szolgáltatás példányának][sap-ha-guide-9.4].

8. [Az SAP elsődleges alkalmazáskiszolgáló telepítése] [ sap-ha-guide-9.5] az új dedikált virtuális gép.

9. [Az SAP további alkalmazáskiszolgáló telepítése] [ sap-ha-guide-9.6] az új dedikált virtuális gép.

10. [Az SAP ASC/SCS-példány feladatátvevő és SIOS replikációs teszteléséhez][sap-ha-guide-10].

## <a name="next-steps"></a>Következő lépések

- [Hálózatkezelés korlátok: az Azure Resource Manager][networking-limits-azure-resource-manager]
- [Több virtuális IP-címek az Azure terheléselosztó][load-balancer-multivip-overview]
- [Útmutató a magas rendelkezésre állású SAP NetWeaver Windows virtuális gépeken][sap-ha-guide]
