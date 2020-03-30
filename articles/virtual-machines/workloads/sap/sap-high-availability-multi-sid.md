---
title: Hozzon létre egy SAP multi-SID konfigurációt az Azure-ban | Microsoft dokumentumok
description: Útmutató a magas rendelkezésre állású SAP NetWeaver multi-SID konfigurációhoz Windows virtuális gépeken
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7938f7db22f004a0bf6cdf2e22dc8e103896719
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617401"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>SAP NetWeaver többSID-konfiguráció létrehozása

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
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

2016 szeptemberében a Microsoft kiadott egy olyan funkciót, amelyen egy Azure belső terheléselosztó használatával több virtuális IP-címet is kezelhet. Ez a funkció már létezik az Azure külső terheléselosztójában.

Ha SAP-telepítéssel rendelkezik, belső terheléselosztóval létrehozhat egy Windows-fürtkonfigurációt az SAP ASCS/SCS számára, ahogy azt a [windowsos virtuális gépeken futó SAP NetWeaver útmutatója is dokumentálja.][sap-ha-guide]

Ez a cikk arra összpontosít, hogyan helyezhető át egyetlen ASCS/SCS-telepítésről egy SAP többSID-konfigurációra további SAP ASCS/SCS fürtözött példányok telepítésével egy meglévő Windows Server feladatátvételi fürtözési (WSFC) fürtbe. Ha ez a folyamat befejeződött, konfigurált egy SAP több SID-fürtöt.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
Már konfigurált egy WSFC-fürtöt, amely egy SAP ASCS/SCS-példányhoz használatos, ahogy azt a [windowsos virtuális gépeken elérhető SAP NetWeaver útmutatója][sap-ha-guide] ismerteti, és ahogy az az ábrán látható.

![Magas rendelkezésre állású SAP ASCS/SCS-példány][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Célarchitektúra

A cél több SAP ABAP ASCS vagy SAP Java SCS fürtözött példány telepítése ugyanabban a WSFC-fürtben, amint az itt látható:

![Több SAP ASCS/SCS fürtözött példány az Azure-ban][sap-ha-guide-figure-6002]

> [!NOTE]
>Az egyes Azure belső terheléselosztók privát előtér-IP-k száma korlátozva van.
>
>Az SAP ASCS/SCS-példányok maximális száma egy WSFC-fürtben megegyezik az egyes Azure belső terheléselosztók privát előtér-IP-kiszolgálóinak maximális számával.
>

A terheléselosztó-korlátokról a Hálózati [korlátok: Azure Resource Manager][networking-limits-azure-resource-manager]című témakörben talál további információt a "Privát előtér-IP-cím terheléselosztásonként" című témakörben.

A teljes táj két magas rendelkezésre állású SAP-rendszerrel a következőkre vallana:

![SAP magas rendelkezésre állású, több SID-s telepítés két SAP rendszer SID-vel][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> A beállításnak a következő feltételeknek kell megfelelnie:
> - Az SAP ASCS/SCS-példányok nak ugyanazt a WSFC-fürtöt kell megosztaniuk.
> - Minden DBMS-biztonsági kiszolgálónak saját dedikált WSFC-fürttel kell rendelkeznie.
> - Az egy SAP-rendszer biztonsági azonosítójához tartozó SAP-alkalmazáskiszolgálóknak saját dedikált virtuális gépekkel kell rendelkezniük.


## <a name="prepare-the-infrastructure"></a>Az infrastruktúra előkészítése
Az infrastruktúra előkészítéséhez telepíthet egy további SAP ASCS/SCS-példányt a következő paraméterekkel:

| Paraméter neve | Érték |
| --- | --- |
| SAP ASCS/SCS-biztonsági rendszer |pr1-lb-ascs |
| SAP DBMS belső terheléselosztó | PR5 |
| SAP virtuális állomás neve | pr5-nedv-cl |
| SAP ASCS/SCS virtuális állomás IP-címe (további Azure terheléselosztó IP-címe) | 10.0.0.50 |
| SAP ASCS/SCS-példány száma | 50 |
| ILB-mintavételi port további SAP ASCS/SCS-példányhoz | 62350 |

> [!NOTE]
> AZ SAP ASCS/SCS fürtpéldányok esetében minden IP-címhez egyedi mintavételi port szükséges. Ha például egy Azure belső terheléselosztó egyik IP-címe a 62300-as mintavételi portot használja, a terheléselosztó más IP-címe nem használhatja a 62300-as mintavételi portot.
>
>A mi céljainkra, mivel a 62300-as szondaport már le van foglalva, a 62350-es szondaportot használjuk.

További SAP ASCS/SCS-példányokat telepíthet a meglévő WSFC-fürtbe két csomón:

| Virtuális gép szerepkör | Virtuálisgép-állomás neve | Statikus IP-cím |
| --- | --- | --- |
| 1. fürtcsomópont AZ ASCS/SCS-példányhoz |pr1-ascs-0 |10.0.0.10 |
| 2. fürtcsomópont AZ ASCS/SCS-példányhoz |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Virtuális állomásnév létrehozása a fürtözött SAP ASCS/SCS-példányhoz a DNS-kiszolgálón

Az ASCS/SCS-példány virtuális állomásnevéhez dns-bejegyzést hozhat létre a következő paraméterek használatával:

| Új SAP ASCS/SCS virtuális állomásnév | Társított IP-cím |
| --- | --- |
|pr5-nedv-cl |10.0.0.50 |

Az új állomásnév és IP-cím a DNS-kezelőben jelenik meg, ahogy az a következő képernyőképen látható:

![A DNS-kezelő lista az új SAP ASCS/SCS fürt virtuális nevéhez és TCP/IP-címéhez megadott DNS-bejegyzést kiemelve][sap-ha-guide-figure-6004]

A DNS-bejegyzések létrehozásának eljárását részletesen ismerteti a [Windows virtuális gépeken található, magas rendelkezésre állású SAP NetWeaver][sap-ha-guide-9.1.1]fő útmutatója is.

> [!NOTE]
> A további ASCS/SCS-példány virtuális állomásnevéhez rendelt új IP-címnek meg kell egyeznie az SAP Azure terheléselosztóhoz rendelt új IP-címmel.
>
>A mi forgatókönyvünkben az IP-cím 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>IP-cím hozzáadása egy meglévő Azure belső terheléselosztóhoz a PowerShell használatával

Ha egynél több SAP ASCS/SCS-példányt szeretne létrehozni ugyanabban a WSFC-fürtben, a PowerShell használatával adjon hozzá egy IP-címet egy meglévő Azure belső terheléselosztóhoz. Minden IP-cím hez saját terheléselosztási szabályok, mintavételi port, előtér-IP-készlet és háttérkészlet szükséges.

A következő parancsfájl új IP-címet ad hozzá egy meglévő terheléselosztóhoz. Frissítse a PowerShell-változókat a környezetben. A parancsfájl létrehozza az összes SZÜKSÉGES terheléselosztási szabályt az összes SAP ASCS/SCS porthoz.

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
A parancsfájl futtatása után az eredmények megjelennek az Azure Portalon, ahogy az a következő képernyőképen látható:

![Új előtér-IP-készlet az Azure Portalon][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Lemezek hozzáadása fürtgépekhez és a SIOS-fürtmegosztási lemez konfigurálása

Minden további SAP ASCS/SCS-példányhoz hozzá kell adnia egy új fürtmegosztási lemezt. Windows Server 2012 R2 esetén a jelenleg használt WSFC-fürtmegosztási lemez a SIOS DataKeeper szoftvermegoldás.

Tegye a következőket:
1. Adjon hozzá egy azonos méretű (csíkozott) lemezt vagy lemezeket az egyes fürtcsomópontokhoz, és formázza azokat.
2. Konfigurálja a tárolóreplikációt a SIOS DataKeeper segítségével.

Ez az eljárás feltételezi, hogy már telepítette a SIOS DataKeeper-t a WSFC fürtgépeken. Ha telepítette, most konfigurálnia kell a replikációt a gépek között. A folyamatot részletesen ismertetjük a [windowsos virtuális gépeken található, magas rendelkezésre állású SAP NetWeaver fő útmutatójában.][sap-ha-guide-8.12.3.3]  

![DataKeeper szinkron tükrözés az új SAP ASCS/SCS megosztási lemezhez][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Virtuális gépek telepítése SAP-alkalmazáskiszolgálókhoz és DBMS-fürthöz

A második SAP-rendszer infrastruktúra-előkészítésének befejezéséhez tegye a következőket:

1. Telepítse a dedikált virtuális gépeket az SAP-alkalmazáskiszolgálókhoz, és helyezze őket a saját dedikált rendelkezésre állási csoportba.
2. Telepítse a dedikált virtuális gépeket a DBMS-fürthöz, és helyezze őket a saját dedikált rendelkezésre állási csoportba.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>A második SAP SID2 NetWeaver rendszer telepítése

A második SAP SID2-rendszer telepítésének teljes folyamatát a [windowsos virtuális gépeken elérhető SAP NetWeaver][sap-ha-guide-9]fő útmutatója ismerteti.

A magas szintű eljárás a következő:

1. [Telepítse az SAP első fürtcsomópontját.][sap-ha-guide-9.1.2]  
 Ebben a lépésben az SAP-t magas rendelkezésre állású ASCS/SCS-példánysal telepíti a **MEGLÉVŐ WSFC fürtcsomópont 1.**

2. [Módosítsa az ASCS/SCS-példány SAP-profilját.][sap-ha-guide-9.1.3]

3. [Mintavételi port konfigurálása][sap-ha-guide-9.1.4].  
 Ebben a lépésben egy SAP-fürterőforrás SAP-SID2-IP-mintavételi port a PowerShell használatával konfigurálása. Hajtsa végre ezt a konfigurációt az SAP ASCS/SCS fürtcsomópontok egyikén.

4. [Az adatbázispéldány telepítése] [sap-ha-guide-9.2].  
 Ebben a lépésben a DBMS telepítése egy dedikált WSFC-fürtre.

5. [Telepítse a második fürtcsomópontot] [sap-ha-guide-9.3].  
 Ebben a lépésben az SAP-t magas rendelkezésre állású ASCS/SCS-példánysal telepíti a meglévő WSFC fürtcsomópont 2.

6. Nyissa meg a Windows tűzfal portjait az SAP ASCS/SCS-példányhoz és a ProbePorthoz.  
 Az SAP ASCS/SCS-példányokhoz használt mindkét fürtcsomóponton megnyitja az SAP ASCS/SCS által használt összes Windows tűzfalport. Ezek a portok a [windowsos virtuális gépeken elérhető SAP NetWeaver útmutatójában][sap-ha-guide-8.8]találhatók.  
 Nyissa meg az Azure belső terheléselosztó mintavételi portját is, amely a forgatókönyvünkben 62350.

7. [Módosítsa az SAP ERS Windows szolgáltatáspéldány indítási típusát.][sap-ha-guide-9.4]

8. [Telepítse az SAP elsődleges alkalmazáskiszolgálót][sap-ha-guide-9.5] az új dedikált virtuális gépre.

9. [Telepítse az SAP további alkalmazáskiszolgálót][sap-ha-guide-9.6] az új dedikált virtuális gépre.

10. [Tesztelje az SAP ASCS/SCS-példány feladatátvételét és a SIOS replikációt.][sap-ha-guide-10]

## <a name="next-steps"></a>További lépések

- [Hálózati korlátok: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Több VIP az Azure Load Balancer számára][load-balancer-multivip-overview]
- [Útmutató a magas rendelkezésre állású SAP NetWeaver windowsos virtuális gépeken][sap-ha-guide]
