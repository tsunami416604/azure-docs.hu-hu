---
title: "Az SAP magas rendelkezésre ÁLLÁSÚ Windows feladatátvevő fürt és a megosztott lemez az SAP ASC/SCS segítségével az Azure-infrastruktúra előkészítése |} Microsoft Docs"
description: "Ismerje meg, hogyan készíti elő az Azure-infrastruktúra a SAP magas rendelkezésre ÁLLÁSÚ egy Windows feladatátvevő fürt és a megosztott lemez egy SAP ASC/SCS példány használatával."
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2547d40ac39bc8188511f6682911fa302cf3e8a5
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Az Azure-infrastruktúra a előkészítéséhez SAP magas rendelkezésre ÁLLÁSÚ segítségével egy Windows feladatátvevő fürt és a megosztott lemez számára SAP ASC/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

Ez a cikk ismerteti az Azure-infrastruktúra előkészítése telepítése és konfigurálása a magas rendelkezésre állású SAP rendszer a Windows feladatátvevő fürt használatával elvégzendő műveleteket a *megosztott fürtlemez* opcionálisan a fürtszolgáltatás egy SAP ASC példány.

## <a name="prerequisites"></a>Előfeltételek

A telepítés megkezdése előtt tekintse át az ebben a cikkben:

* [Architektúra-Útmutató: a Windows feladatátvevő fürt egy SAP ASC/SCS példány fürtön egy fürt megosztott lemez][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Az infrastruktúra előkészítése architekturális sablon 1
Az SAP Azure Resource Manager-sablonok segítségével egyszerűsítheti a szükséges erőforrások.

A háromrétegű sablonok az Azure Resource Manager is támogatja a magas rendelkezésre állású forgatókönyvek. Architekturális sablon 1 például két fürttel rendelkezik. Minden fürthöz egy SAP hibaérzékeny pontot SAP ASC/SCS és adatbázis-kezelő.

Itt található, ahol kaphat a példaforgatókönyv azt ismertetik, ez a cikk az Azure Resource Manager-sablonok:

* [Kép: Azure piactér](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Az Azure piactér kép Azure felügyelt lemezek használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Kép: egyéni](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Egyéni lemezkép által kezelt lemezek használata](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Az infrastruktúra előkészítése architekturális sablon 1:

- Az Azure portálon a a **paraméterek** ablaktáblán, a a **SYSTEMAVAILABILITY** mezőben válassza **magas rendelkezésre ÁLLÁSÚ**.

  ![1. ábra: Beállítása SAP magas rendelkezésre állású Azure Resource Manager-paraméterek][sap-ha-guide-figure-3000]

_**1. ábra:** beállítása SAP magas rendelkezésre állású Azure Resource Manager-paraméterek_


  A sablonok létrehozása:

  * **Virtuális gépek**:
    * SAP alkalmazáskiszolgáló virtuális gépek: \<SAPSystemSID\>- di -\<száma\>
    * A fürt virtuális gépek ASC/SCS: \<SAPSystemSID\>- ASC-\<száma\>
    * Adatbázis-kezelő fürt: \<SAPSystemSID\>- db-\<száma\>

  * **Hálózati kártya tartozó összes virtuális géphez társított IP-címekkel rendelkező**:
    * \<SAPSystemSID\>- nic-di -\<száma\>
    * \<SAPSystemSID\>- nic-ASC -\<száma\>
    * \<SAPSystemSID\>- nic-db -\<száma\>

  * **Az Azure storage-fiókok (csak a nem felügyelt lemezek)**:

  * **Rendelkezésre állási csoportok** esetében:
    * SAP alkalmazáskiszolgáló virtuális gépek: \<SAPSystemSID\>- avset-di
    * SAP ASC/SCS cluster virtuális gépek: \<SAPSystemSID\>- avset-ASC
    * Adatbázis-kezelő fürt virtuális gépek: \<SAPSystemSID\>- avset-adatbázis

  * **Az Azure belső terheléselosztó**:
    * Az összes port a ASC/SCS példányhoz, és az IP-cím \<SAPSystemSID\>- lb-ASC
    * Az összes port az SQL Server adatbázis-kezelő és az IP-cím \<SAPSystemSID\>- lb-adatbázis

  * **Hálózati biztonsági csoport**: \<SAPSystemSID\>- nsg-ASC-0  
    * Egy nyitott külső Remote Desktop Protocol (RDP) port az a \<SAPSystemSID\>virtuálisgép - ASC-0

> [!NOTE]
> Összes IP-címet a hálózati kártyák és az Azure belső terheléselosztók dinamikusak alapértelmezés szerint. Módosításukra statikus IP-címeket. Azt ismerteti, hogyan ehhez a cikk későbbi részében.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>A vállalati hálózati kapcsolat hibája (létesítmények közötti) éles környezetben használandó virtuális gépek telepítése
Éles rendszerek esetén SAP, az Azure virtuális gépek telepítése a [vállalati hálózati kapcsolat (létesítmények közötti)] [ planning-guide-2.2] Azure VPN Gateway vagy Azure ExpressRoute segítségével.

> [!NOTE]
> Az Azure Virtual Network-példányt is használhat. A virtuális hálózat és alhálózat már létrehozott és előkészítése.
>
>

1.  Az Azure portálon a a **paraméterek** ablaktáblán, a a **NEWOREXISTINGSUBNET** mezőben válassza **meglévő**.
2.  Az a **SUBNETID** mezőben adja meg a teljes karakterláncot a előkészített Azure hálózati alhálózati azonosító, ha azt tervezi, hogy az Azure virtuális gépek telepítése.
3.  Ahhoz, hogy az összes Azure-hálózat alhálózatok listája, a PowerShell parancsot:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  A **azonosító** mező értékét mutatja, az alhálózati azonosítóhoz.
4. Ahhoz, hogy minden alhálózati azonosító értékből álló lista, futtassa a PowerShell-parancsot:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  Az alhálózati azonosító így néz ki:

  ```
  /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
  ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>A tesztelési és bemutató csak felhőalapú SAP-példányok telepítése
Egy kizárólag felhőalapú üzembe helyezési modellben a magas rendelkezésre állású SAP rendszereket telepítheti központilag. Ez a központi telepítési típus elsősorban akkor hasznos, bemutató és tesztelési használatából adódó. Nem alkalmas a termelési használati eseteket.

- Az Azure portálon a a **paraméterek** ablaktáblán, a a **NEWOREXISTINGSUBNET** mezőben válassza **új**. Hagyja a **SUBNETID** mező üres.

  Az SAP Azure Resource Manager sablon automatikusan létrehozza az Azure-beli virtuális hálózat és az alhálózatot.

> [!NOTE]
> Is kell legalább egy dedikált virtuális gép az Azure virtuális hálózat ugyanazon Active Directory és a DNS-szolgáltatás telepítéséhez. A sablon nem hoz létre a virtuális gépek.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Az infrastruktúra előkészítése a 2. sablon architekturális.

A SAP Azure Resource Manager sablon segítségével leegyszerűsíti az SAP architekturális sablon 2 álló szükséges infrastruktúra központi telepítését.

Itt található, ahol kaphat Azure Resource Manager-sablonok a központi telepítési forgatókönyv:

* [Kép: Azure piactér](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Az Azure piactér lemezkép által kezelt lemezek használata](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Kép: egyéni](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Egyéni lemezkép által kezelt lemezek használata](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Az infrastruktúra előkészítése a 3. sablon architekturális.

Készítse elő az infrastruktúrát, és multi-SID SAP konfigurálása. Például hozzáadhat egy SAP ASC/SCS másodpéldányt be egy *meglévő* fürtkonfiguráció. További információkért lásd: [konfigurálása egy SAP ASC/SCS másodpéldányt egy létező fürtnek megfelelő konfiguráció egy SAP multi-SID konfigurációjának létrehozása az Azure Resource Manager][sap-ha-multi-sid-guide].

Ha szeretne létrehozni egy új multi-SID-fürtöt, használhatja a multi-SID [gyorsindítási sablonok a Githubon](https://github.com/Azure/azure-quickstart-templates).

Hozzon létre egy új multi-SID-fürtöt, az alábbi három sablonok kell telepítenie:

* [Asc/SCS sablon](#ASCS-SCS-template)
* [Adatbázis-sablon](#database-template)
* [Alkalmazássablon-kiszolgálók](#application-servers-template)

A következő szakaszok további információkat a sablonok és paramétereket, meg kell adnia a sablonokban rendelkezik.

### <a name="ASCS-SCS-template"></a>Asc/SCS sablon

A ASC/SCS sablon segítségével több ASC/SCS-példányt futtató Windows Server feladatátvevő fürt létrehozása két virtuális gépek telepíti.

A ASC/SCS multi-SID-sablon beállítása az a [ASC/SCS multi-SID sablon] [ sap-templates-3-tier-multisid-xscs-marketplace-image] vagy [ASC/SCS multi-SID-sablon által kezelt lemezek segítségével] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], adja meg a következő paraméterekkel:

  - **Erőforrás-előtag**: állítsa be az erőforrás előtagja, amelynek segítségével a telepítés során létrehozott összes erőforrás előtag. Mivel az erőforrások csak egy SAP-rendszer nem tartoznak, az előtag, az erőforrás nincs egy SAP rendszer biztonsági azonosítója.  Az előtag 3-6 karakter hosszúságú lehet.
  - **A verem típus**: válassza ki a verem az SAP rendszer. A verem típusától függően a Azure Load Balancer (ABAP vagy csak a Java) egy vagy két (ABAP + Java) magánhálózati IP-címek SAP rendszerenként rendelkezik.
  -  **Operációs rendszer típusa**: Jelölje ki az operációs rendszert a virtuális gépek.
  -  **SAP rendszer száma**: válassza ki a fürt telepíteni kívánt SAP-rendszerek számát.
  -  **Rendelkezésre állására**: válasszon **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazda felhasználónevét és a rendszergazdai jelszó**: hozzon létre egy új felhasználót, amely segítségével jelentkezzen be a gépet.
  -  **Új vagy meglévő alhálózati**: hozzon létre egy új virtuális hálózat és az alhálózatot, vagy használjon egy létező alhálózatot beállítása. Ha már van egy virtuális hálózatot, amely a helyszíni hálózathoz csatlakozik, válassza ki a **meglévő**.
  -  **Alhálózati azonosító**: az alhálózat, amelyhez a virtuális gépeket csatlakoztatni kell a azonosítóját. Jelölje ki az alhálózatot, a VPN- vagy ExpressRoute virtuális hálózat a virtuális gép és a helyszíni hálózathoz csatlakozni. Az azonosító általában néz ki:

   következő\<előfizetés-azonosító\>/resourceGroups/\<erőforráscsoport-név\>/providers/Microsoft.Network/virtualNetworks/\<virtuálishálózat-név\>/subnets/ \<alhálózat neve\>

A sablon egy Azure Load Balancer példánya, amely támogatja a több SAP rendszer központilag telepíti:

- A ASC példányok példányszámának 00, 10, 20 beállítást...
- A SCS példányok példányszámának 01, 11, 21 beállítást...
- A ASC sorba helyezni replikációs Server (SSZON) (csak Linux) példányok példányszámának 02, 12, 22 beállítást...
- A SCS SSZON (csak Linux) példányok példányszámának 03., 13, 23 beállítást...

A load balancer tartalmaz 1 VIP(s) (Linux 2), a ASC/SCS 1 x-VIP és SSZON (csak Linux esetén) 1 x virtuális.

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>SAP ASC/SCS portok
Az alábbi lista tartalmazza az összes terheléselosztási szabályok (ahol x egy SAP rendszer, például 1, 2, 3... száma):
- Minden SAP rendszerhez a Windows-specifikus portokat: 445-ös, 5985
- Asc portok (x0 száma): 32 x 0, 36 x 0, 39 x 0, 81-es x 0, 5 x 013, 5 x 014, 5 x 016
- SCS portok (x1 száma): 32 x 1, 33 x 1, 39 x 1, 81-es x 1, 5 x 113, 5 x 114, 5 x 116
- Linux (példányszámának x2) portok ASC SSZON: 33 x 2, 5 x 213, 5 x 214, 5 x 216
- Linux (példányszámának x3) portok SCS SSZON: 33 x 3, 5 x 313, 5 x 314, 5 x 316

A terheléselosztó a következő (Ha x az a szám az SAP-rendszer, például 1, 2, 3...) mintavételi portok használatára van konfigurálva:
- Asc/SCS belső terheléselosztó mintavételi portot betölteni: 620 x 0
- SSZON belső terheléselosztó mintavételi portot (csak Linux) betöltése: 621 x 2

### <a name="database-template"></a>Adatbázis-sablon

Az adatbázis-sablon telepít egy vagy két virtuális gépek, amelyek egy SAP rendszer relációs adatbázis-kezelő rendszerének (RDBMS) telepítéséhez. Például ha telepít egy ASC/SCS sablon öt SAP rendszerekhez, akkor kell telepíteni a sablon ötször.

Az adatbázis-multi-SID sablon beállítása a [adatbázis multi-SID sablon] [ sap-templates-3-tier-multisid-db-marketplace-image] vagy [adatbázis multi-SID-sablon által kezelt lemezek segítségével] [ sap-templates-3-tier-multisid-db-marketplace-image-md], adja meg a következő paraméterekkel:

  -  **SAP rendszerazonosító**: Adja meg a telepíteni kívánt SAP rendszer SAP rendszer Azonosítóját. Az azonosító előtagjaként szolgál a telepített erőforrások esetén.
  -  **Operációs rendszer típusa**: Jelölje ki az operációs rendszert a virtuális gépek.
  -  **DbType**: Adja meg, a fürtön telepíteni kívánt adatbázist. Válassza ki **SQL** Ha telepíti a Microsoft SQL Server. Válassza ki **HANA** Ha azt tervezi, hogy SAP HANA telepítse a virtuális gépeken. Győződjön meg arról, hogy bejelöli-e a megfelelő operációs rendszer típusa. Válassza ki **Windows** SQL, és válasszon egy Linux terjesztési Hana. Az Azure terheléselosztó, amely kapcsolódik a virtuális gépek a kijelölt adatbázis típusú támogatására van konfigurálva:
    * **SQL**: A betöltési terheléselosztó terheléselosztásához 1433-as portra. Győződjön meg arról, hogy ezen a porton az SQL Server AlwaysOn-beállítás.
    * **HANA**: A betöltési terheléselosztó terheléselosztásához 35015 és portot 35017. Ügyeljen arra, hogy telepítse SAP HANA példányszámának **50**.
    A load balancer 62550 mintavételi portot használja.
  -  **SAP mérete**: az új rendszer biztosít SAP adjon meg. Ha nem tudja, hogy hány SAP, a rendszer szükséges, kérje meg a SAP technológia Partner vagy a rendszer integráló.
  -  **Rendelkezésre állására**: válasszon **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazda felhasználónevét és a rendszergazdai jelszó**: hozzon létre egy új felhasználót, amely segítségével jelentkezzen be a gépet.
  -  **Alhálózati azonosító**: Adja meg az alhálózat ASC/SCS-sablon a telepítés során használt azonosító, vagy az alhálózat létrehozott azonosítója a ASC/SCS sablon központi telepítésének részeként.

### <a name="application-servers-template"></a>Alkalmazássablon-kiszolgálók

A kiszolgálók alkalmazássablon SAP alkalmazáskiszolgáló-példányok egy SAP-rendszerhez használható két vagy több virtuális gépek telepíti. Például ha telepít egy ASC/SCS sablon öt SAP rendszerekhez, akkor kell telepíteni a sablon ötször.

Az alkalmazás kiszolgálók multi-SID sablon beállítása a [kiszolgálók multi-SID alkalmazássablon] [ sap-templates-3-tier-multisid-apps-marketplace-image] vagy [kiszolgálók multi-SID alkalmazássablon által kezelt lemezeken] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], adja meg a következő paraméterekkel:

  -  **SAP rendszerazonosító**: Adja meg a telepíteni kívánt SAP rendszer SAP rendszer Azonosítóját. Az azonosító előtagjaként szolgál a telepített erőforrások esetén.
  -  **Operációs rendszer típusa**: Jelölje ki az operációs rendszert a virtuális gépek.
  -  **SAP mérete**: az új rendszer biztosít SAP száma. Ha nem tudja, hogy hány SAP, a rendszer szükséges, kérje meg a SAP technológia Partner vagy a rendszer integráló.
  -  **Rendelkezésre állására**: válasszon **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazda felhasználónevét és a rendszergazdai jelszó**: hozzon létre egy új felhasználót, amely segítségével jelentkezzen be a gépet.
  -  **Alhálózati azonosító**: Adja meg az alhálózat ASC/SCS-sablon a telepítés során használt azonosító, vagy az alhálózat létrehozott azonosítója a ASC/SCS sablon központi telepítésének részeként.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Azure-beli virtuális hálózat
A példánkban a cím az Azure Virtual Network példány 10.0.0.0/16. Nincs alhálózat, a 10.0.0.0/24 címtartományt meghívva egy alhálózatot. A virtuális hálózaton található virtuális gépek és a belső terheléselosztók vannak telepítve.

> [!IMPORTANT]
> Nem módosítja a vendég operációs rendszerében a hálózati beállításokat. Ez magában foglalja az IP-címek, a DNS-kiszolgálók és az alhálózatot. A hálózati beállítások konfigurálása az Azure-ban. A Dynamic Host Configuration Protocol (DHCP) szolgáltatás tölti ki a beállításokat.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS-IP-címek

A szükséges DNS-IP-címek beállításához kövesse az alábbi lépéseket:

1.  Az Azure portálon a a **DNS-kiszolgálók** ablaktáblában győződjön meg arról, hogy a virtuális hálózat **DNS-kiszolgálók** beállítás **egyéni DNS**.
2.  Válassza ki a beállítások alapján a hálózati rendelkezik. További információkért lásd a következőket:
    * [Vállalati hálózati kapcsolat (létesítmények közötti)][planning-guide-2.2]: hozzáadása a helyi DNS-kiszolgálók IP-címét.  
    Kiterjesztheti a helyi DNS-kiszolgálók az Azure-ban futó virtuális gépek számára. A forgatókönyv adhat hozzá a DNS szolgáltatást futtató Azure virtuális gépek IP-címét.
    * [Csak felhőalapú telepítési][planning-guide-2.1]: helyezze üzembe a virtuális hálózati példányt a DNS-kiszolgáló látja, hogy egy további virtuális gépet. Adja hozzá azt állította be az Azure virtuális gépek IP-címét, a DNS-szolgáltatás futtatásához.

    ![2. ábra: DNS-kiszolgálók konfigurálása az Azure-beli virtuális hálózathoz][sap-ha-guide-figure-3001]

    _**2. ábra:** DNS konfigurálása az Azure Virtual Network kiszolgálók_

  > [!NOTE]
  > Ha módosítja a DNS-kiszolgálók IP-címét, a módosítás alkalmazásához, és az új DNS-kiszolgálók propagálása Azure virtuális gépek újraindítására szeretné.
  >
  >

A fenti példában a DNS-szolgáltatás telepítve és konfigurálva van a Windows virtuális gépek:

| Virtuálisgép-szerepkör | Virtuális gép állomásneve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első DNS-kiszolgáló |domcontr-0 |PR1-nic-domcontr-0 |10.0.0.10 |
| Második DNS-kiszolgáló |domcontr-1 |PR1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Állomásnév és a statikus IP-címet a SAP ASC/SCS fürtözött példány és az adatbázis-kezelő fürtözött példány

A helyszíni telepítéshez szüksége ezek fenntartott állomásneve és IP-címek:

| Virtuális állomás neve szerepkör | Virtuális állomás neve | Virtuális statikus IP-cím |
| --- | --- | --- |
| SAP ASC/SCS első fürt virtuális host name (kezelő) |PR1-ASC-vir |10.0.0.42 |
| SAP ASC/SCS példány virtuális állomás neve |PR1-ASC-sap |10.0.0.43 |
| SAP DBMS második fürt virtuális állomásnevet (kezelő) |PR1-dbms-vir |10.0.0.32 |

A fürt létrehozásakor a virtuális gazdagép nevek pr1-ASC-vir és pr1-dbms-vir és a társított IP-címek kezelése egyrészt a fürt létrehozása. Ezzel kapcsolatos további információkért lásd: [fürtcsomópontok fürtkonfiguráció gyűjtése][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

Manuálisan létrehozhat a másik két virtuális állomásnevek, pr1-ASC-sap és pr1-adatbázis-kezelő – sap, és a társított IP-címek, a DNS-kiszolgálón. A fürtözött SAP ASC/SCS-példány és a fürtözött adatbázis-kezelő példány használja ezeket az erőforrásokat. Ezzel kapcsolatos további információkért lásd: [hozzon létre egy virtuális nevet egy fürtözött SAP ASC/SCS példány][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Állítsa be a statikus IP-címeket az SAP virtuális gépekhez
Miután telepítette a virtuális gépeket a fürtben használni, az összes virtuális gép statikus IP-címek be kell. Ehhez az Azure virtuális hálózat konfigurálása, és nem a vendég operációs rendszer.

1.  Válassza ki az Azure-portálon **erőforráscsoport** > **hálózati kártya** > **beállítások** > **IP-cím**.
2.  Az a **IP-címek** ablaktáblán, a **hozzárendelés**, jelölje be **statikus**. Az a **IP-cím** mezőbe írja be a használni kívánt IP-cím.

  > [!NOTE]
  > Ha módosítja a hálózati kártya IP-címét, indítsa újra az Azure virtuális gépeken, a módosítás alkalmazására van szükség.  
  >
  >

  ![3. ábra: Állítsa be a statikus IP-címek a hálózati kártyát. az egyes virtuális gépek][sap-ha-guide-figure-3002]

  _**3. ábra:** statikus IP-címek a hálózati kártyát. az egyes virtuális gépek beállítása_

  Ismételje meg ezt a lépést minden hálózati interfészen, hogy van, az összes virtuális gép, beleértve az Active Directory vagy a DNS-szolgáltatáshoz használni kívánt virtuális gépek.

A jelen példában vezetünk be a virtuális gépek és a statikus IP-címek:

| Virtuálisgép-szerepkör | Virtuális gép állomásneve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első SAP application server-példány |PR1-di-0 |PR1-nic-di-0 |10.0.0.50 |
| Második SAP Application Server-példány |PR1-di-1 |PR1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Utolsó SAP application server-példány |PR1-di-5 |PR1-nic-di-5 |10.0.0.55 |
| Első fürtcsomópontra ASC/SCS-példány |PR1-ASC-0 |PR1-nic-ASC-0 |10.0.0.40 |
| Második fürtcsomópont ASC/SCS-példány |PR1-ASC-1 |PR1-nic-ASC-1 |10.0.0.41 |
| Adatbázis-kezelő példány első fürtcsomópontra |PR1-db-0 |PR1-nic-db-0 |10.0.0.30 |
| Adatbázis-kezelő példány második fürtcsomópont |PR1-db-1 |PR1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Egy statikus IP-cím beállítása az Azure belső terheléselosztóhoz

Az SAP Azure Resource Manager sablonnal hoz létre Azure belső terheléselosztót a SAP ASC/SCS példány és az adatbázis-kezelő fürtön használt.

> [!IMPORTANT]
> A virtuális gazdagép neve a SAP ASC/SCS IP-címe megegyezik a SAP ASC/SCS belső terheléselosztó IP-címe: pr1-lb-ASC.
> A DBMS neve virtuális IP-címe megegyezik az IP-cím, az adatbázis-kezelő belső terheléselosztó: pr1-lb-adatbázis-kezelő.
>
>

Egy statikus IP-cím beállítása az Azure belső terheléselosztóhoz:

1.  A kezdeti telepítés beállítja a belső terheléselosztó IP-cím **dinamikus**. Az Azure portálon a a **IP-címek** ablaktáblán, a **hozzárendelés**, jelölje be **statikus**.
2.  Állítsa be az IP-cím, a belső terheléselosztó **pr1-lb-ASC** virtuális állomásnevének az SAP ASC/SCS példány IP-címre.
3.  Állítsa be az IP-cím, a belső terheléselosztó **pr1-lb-dbms** virtuális állomásnevének az adatbázis-kezelő példány IP-címre.

  ![4. ábra: Az SAP ASC/SCS példány belső terheléselosztót beállítani statikus IP-címek][sap-ha-guide-figure-3003]

  _**4. ábra:** SAP ASC/SCS-példány a belső terheléselosztó statikus IP-címek beállítása_

Ebben a példában két Azure belső terheléselosztók a statikus IP-címmel rendelkező vezetünk be:

| Az Azure belső terheléselosztási szerepköréhez | Az Azure belső terheléselosztó neve | Statikus IP-cím |
| --- | --- | --- |
| SAP ASC/SCS példány belső terheléselosztót |PR1-lb-ASC |10.0.0.43 |
| SAP DBMS belső terheléselosztó |PR1-lb-adatbázis-kezelő |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Alapértelmezett ASC/SCS terheléselosztási szabályok az Azure belső terheléselosztóhoz

A SAP Azure Resource Manager-sablon létrehozza a portok:
* Az alapértelmezett példányszámának 00, ABAP ASC példányok
* A Java SCS példány, az alapértelmezett példányszámának 01

A SAP ASC/SCS példányát telepítésekor kell használnia az alapértelmezett példányszámának 00 ABAP ASC-példány és az alapértelmezett példányszámának 01 a Java SCS-példányhoz.

Ezután hozzon létre a szükséges belső terheléselosztási végpontok a SAP NetWeaver portok.

Szükséges belső terheléselosztási végpontok, először hozzon létre a terheléselosztást a SAP NetWeaver ABAP ASC portok végpontok:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (példányszámának 00 példány ASC) (SSZON 10) |
| --- | --- | --- |
| Sorba helyezni server / *lbrule3200* |32\<InstanceNumber\> |3200 |
| ABAP üzenet server / *lbrule3600* |36\<InstanceNumber\> |3600 |
| Belső ABAP üzenet / *lbrule3900* |39\<InstanceNumber\> |3900 |
| Üzenet server HTTP / *Lbrule8100* |81-es\<InstanceNumber\> |8100 |
| SAP ASC HTTP szolgáltatás elindítása / *Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| SAP ASC HTTPS szolgáltatás elindítása / *Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| Sorba helyezni replikációs / *Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| SAP SSZON HTTP szolgáltatás elindítása *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| SAP SSZON HTTP szolgáltatás elindítása *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| Windows Rendszerfelügyeleti (webszolgáltatások WinRM) *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

**1. táblázat:** portszámokat SAP NetWeaver ABAP ASC példánya

Ezután hozzon létre a terheléselosztást a SAP NetWeaver Java SCS portok végpontok:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (SCS példány példányszámának 01) (SSZON 11) |
| --- | --- | --- |
| Sorba helyezni server / *lbrule3201* |32\<InstanceNumber\> |3201 |
| Átjárókiszolgáló / *lbrule3301* |33\<InstanceNumber\> |3301 |
| Java-üzenet server / *lbrule3900* |39\<InstanceNumber\> |3901 |
| Üzenet server HTTP / *Lbrule8101* |81-es\<InstanceNumber\> |8101 |
| SAP SCS HTTP szolgáltatás elindítása / *Lbrule50113* |5\<InstanceNumber\>13 |50113 |
| SAP SCS HTTPS szolgáltatás elindítása / *Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| Sorba helyezni replikációs / *Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| SAP SSZON HTTP szolgáltatás elindítása *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| SAP SSZON HTTP szolgáltatás elindítása *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| A Rendszerfelügyeleti webszolgáltatások *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

**2. táblázat:** portszámot az SAP NetWeaver Java SCS-példányok

![5. ábra: Az alapértelmezett ASC/SCS betöltése az Azure belső terheléselosztóhoz tartozó terheléselosztási szabályok][sap-ha-guide-figure-3004]

_**5. ábra:** alapértelmezett ASC/SCS terheléselosztási szabályok az Azure belső terheléselosztóhoz_

Állítsa be az IP-címét a load balancer pr1-lb-adatbázis-kezelő virtuális állomásnevének az adatbázis-kezelő példány IP-címre.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Módosítsa a ASC/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztóhoz

Ha a SAP ASC vagy SCS példányok eltérő számú használni kívánt, módosítania kell a nevek és értékek a portok alapértelmezett értékekhez.

1.  Válassza ki az Azure-portálon  **\<SID\>-lb-ASC terheléselosztó** > **terheléselosztási szabályok betöltése**.
2.  Minden terheléselosztási szabályok, amelyek az SAP ASC vagy SCS példányhoz tartozik ezek az értékek módosítása:

  * Név
  * Port
  * Háttér-port

  Például ha szeretné módosítani az alapértelmezett ASC példányszámának a 00 és 31, szeretné hajtsa végre a módosításokat minden porthoz az 1.

  Példa port frissítési *lbrule3200*.

  ![6. ábra: Módosítsa a ASC/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztóhoz][sap-ha-guide-figure-3005]

  _**6. ábra:** ASC/SCS alapértelmezett terheléselosztási az Azure belső terheléselosztóhoz tartozó szabályok módosítása_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Windows virtuális gépek felvételét a tartományba

Miután egy statikus IP-címet rendel a virtuális gépek, a virtuális gépek felvételét a tartományba.

![7. ábra: A virtuális gépek hozzáadása a tartományhoz][sap-ha-guide-figure-3006]

_**7. ábra:** felvesz egy virtuális gépet egy tartományhoz_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Adja hozzá a beállításjegyzék-bejegyzések az SAP ASC/SCS példány mindkét fürtcsomóponton

Az Azure terheléselosztó a belső terheléselosztók, hogy a kapcsolatok bezárása, amikor a kapcsolat üresjáratban a megadott ideig (üresjárati időkorlátot) időben rendelkezik. SAP munkafolyamatok párbeszédpanel példányok nyitott kapcsolatok az SAP sorba, amint az első sorba helyezni/created küldje kérelem küldésének kell feldolgozni. Ezek a kapcsolatok általában marad a meghatározott a munkahelyi folyamat, vagy a sorba helyezni folyamat újraindítja. Azonban ha a kapcsolat üresjáratban a beállított időn belül, az Azure belső terheléselosztó bezárja a kapcsolatokat. Ez nem probléma, mert a SAP munkahelyi folyamat újra létrehozza a kapcsolatot, a várólistára helyezés folyamatban, ha már nem létezik. Ezek a tevékenységek SAP folyamatok fejlesztői nyomait vannak dokumentálva, de ezeket a nyomkövetéseket a felesleges tartalmat nagy mennyiségű hoznak létre. Jó ötlet módosítása a TCP/IP `KeepAliveTime` és `KeepAliveInterval` mindkét fürtcsomóponton. Ezek a változások, a TCP/IP-paraméterek SAP profil paraméterekkel, a cikk későbbi részében leírt össze.

Mindkét fürtcsomópont az SAP ASC/SCS példány beállításjegyzék-bejegyzések hozzáadásához először adja hozzá a Windows beállításjegyzék-bejegyzések mindkét Windows fürtcsomópontokon az SAP ASC/SCS:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveTime` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Dokumentáció csatolása |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**3. táblázat:** módosítsa az első TCP/IP-paraméter

Ezt követően adja hozzá a Windows beállításjegyzék-bejegyzés mindkét Windows fürtcsomópontokon az SAP ASC/SCS:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveInterval` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Dokumentáció csatolása |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

**4. táblázat:** módosítása a második TCP/IP-paraméter

Alkalmazza a módosításokat, indítsa újra a kiszolgálót mindkét fürtcsomóponton.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Egy SAP ASC/SCS példány Windows Server feladatátvevő fürt beállítása

A Windows Server feladatátvevő fürt egy SAP ASC/SCS-példány beállítása magában foglalja a ezeket a feladatokat:

- Fürtözött konfigurációban a fürt csomópontjai gyűjtése.
- A fürt tanúsító fájlmegosztás beállítása.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Fürtözött konfigurációban a fürt csomópontjai gyűjtése

1.  A szerepkör hozzáadása és szolgáltatások varázsló vegye fel a Feladatátvételi fürtszolgáltatást mindkét fürtcsomóponton.
2.  A feladatátvevő fürt beállítása a Feladatátvevőfürt-kezelő használatával. A Feladatátvevőfürt-kezelőben válasszon **fürt létrehozása**, és adja meg csak az első (A csomópont) fürt nevét. A második csomópont ne adjon hozzá még; a második csomópont hozzáadása a egy későbbi lépésben.

  ![8. ábra: A kiszolgáló vagy a virtuális gép nevét, az első fürtcsomópont hozzáadása][sap-ha-guide-figure-3007]

  _**8. ábra:** adja hozzá az első fürtcsomópontra a kiszolgáló vagy a virtuális gép neve_

3.  Adja meg a fürt hálózati neve (virtuális állomás neve).

  ![9. ábra: Adja meg a fürt neve][sap-ha-guide-figure-3008]

  _**9. ábra:** adja meg a fürt neve_

4.  Miután létrehozta a fürthöz, futtassa a fürtellenőrzési tesztet.

  ![10. ábra: A fürt érvényesítése-ellenőrzés futtatása][sap-ha-guide-figure-3009]

  _**10. ábra:** a fürt érvényesítése-ellenőrzés futtatása_

  Ezen a ponton a folyamat lemezek kapcsolatos figyelmeztetéseket figyelmen kívül hagyhatja. Egy tanúsító fájlmegosztást és a SIOS megosztott lemezek később fogja hozzáadni. Ezen a ponton nem kell foglalkoznia a kvórum.

  ![11. ábra: Kvórumlemez nem található][sap-ha-guide-figure-3010]

  _**11. ábra:** kvórumlemez nem található_

  ![12. ábra: A core fürterőforrás kell egy új IP-cím][sap-ha-guide-figure-3011]

  _**12. ábra:** egy alapvető fürterőforrás kell egy új IP-cím_

5.  A core fürtszolgáltatás az IP-címének módosítása. A fürt nem indítható el, amíg nem módosítja az IP-cím a core fürtszolgáltatás, mert a kiszolgáló IP-címét a virtuális gép csomópontok egyikére. Az ehhez a **tulajdonságok** a core fürtszolgáltatás IP-erőforrás oldalán.

  Például azt kell rendelnie egy IP-cím (a példánkban 10.0.0.42) az a fürt virtuális állomás neve pr1-ASC-vir.

  ![13. ábra: A Tulajdonságok párbeszédpanelen az IP-címének módosítása][sap-ha-guide-figure-3012]

  _**13. ábra:** a a **tulajdonságok** párbeszédpanel változtassa meg az IP-cím_

  ![14. ábra: Az a fürt számára fenntartott IP-cím hozzárendelése][sap-ha-guide-figure-3013]

  _**14. ábra:** a fürt számára fenntartott IP-cím hozzárendelése_

6.  A fürt virtuális állomásnevet online állapotba.

  ![15. ábra: A fürtszolgáltatás core megfelelően működik, és, a megfelelő IP-címmel][sap-ha-guide-figure-3014]

  _**15. ábra:** core a fürtszolgáltatás megfelelően működik, és, a megfelelő IP-címmel_

7.  Adja hozzá a második fürtcsomópontokat.

  Most, hogy a core fürtszolgáltatás működik és elérhető, a második fürtcsomópont is hozzáadhat.

  ![Ábra a 16 a második csomópont hozzáadása][sap-ha-guide-figure-3015]

  _**16. ábra:** második csomópont hozzáadása_

8.  Adjon meg egy nevet, a második csomópont gazda esetében.

  ![17. ábra: Adja meg a második fürt csomópont állomásnév][sap-ha-guide-figure-3016]

  _**17. ábra:** adja meg a második fürt csomópont állomásnév_

  > [!IMPORTANT]
  > Ügyeljen arra, hogy a **minden megfelelő tároló felvétele a fürt** jelölőnégyzet *nem* kijelölt.  
  >
  >

  ![18. ábra: Jelölje be a jelölőnégyzetet][sap-ha-guide-figure-3017]

  _**18. ábra:** tegye *nem* jelölje be a jelölőnégyzetet_

  Kvórum és lemezek kapcsolatos figyelmeztetések figyelmen kívül hagyhatja. Akkor lesz a kvórum és megosztja a lemez később, a [telepítése SIOS DataKeeper Cluster Edition egy SAP ASC/SCS megosztás fürtlemez esetében][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

  ![19. ábra: A lemez kvórumával kapcsolatos figyelmeztetések mellőzése][sap-ha-guide-figure-3018]

  _**19. ábra:** a lemez kvórumával kapcsolatos figyelmeztetések mellőzése_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>A fürt tanúsító fájlmegosztás beállítása

Ezeket a feladatokat a fürt tanúsító fájlmegosztás beállítása foglal magában:

- Fájlmegosztás létrehozása.
- Állítsa be a fájl megosztási tanúsító kvórum a Feladatátvevőfürt-kezelőben.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Fájlmegosztás létrehozása

1.  Válassza ki a tanúsító fájlmegosztást egy kvórumlemez helyett. SIOS DataKeeper támogatja ezt a lehetőséget.

  A cikkben szereplő példákban az Active Directory vagy a DNS-kiszolgáló, az Azure-ban futó tanúsító fájlmegosztás van. A tanúsító fájlmegosztás neve domcontr-0. Volna konfigurálta a VPN-kapcsolat az Azure-ba (keresztül VPN-átjáró vagy Azure expressroute-ot), mert az Active Directory és a DNS szolgáltatás a helyszíni, és nem megfelelő, egy tanúsító fájlmegosztást futtatásához.

  > [!NOTE]
  > Ha az Active Directory vagy a DNS-szolgáltatás csak a helyszíni rendszeren fut, a tanúsító fájlmegosztás nem konfigurálja, hogy fut a helyszíni Active Directory vagy a DNS a Windows operációs rendszer. Azure és az Active Directory vagy a helyi DNS-beli fürtcsomópontok közötti hálózati késés előfordulhat, hogy, hogy túl nagy, és csatlakozási problémák miatt. Ne felejtse el a tanúsító fájlmegosztás beállítása megközelíti a fürtcsomópont futtató Azure virtuális géphez.  
  >
  >

  A kvórum meghajtó legalább 1024 MB szabad területre van szüksége. Azt javasoljuk, hogy a 2048 MB szabad lemezterületet a kvórum meghajtót.

2.  Adja hozzá a fürtnévobjektum.

  ![20. ábra: A megosztást a fürtnévobjektum vonatkozó engedélyek hozzárendelése][sap-ha-guide-figure-3019]

  _**20. ábra:** a megosztást a fürtnévobjektum vonatkozó engedélyek hozzárendelése_

  Győződjön meg arról, hogy az engedélyek tartalmazza-e a megosztáson található, a fürtnévobjektum (a példánkban pr1-ASC-vir$) az adatok módosítása a jogosultságokat.

3.  A fürtnévobjektum felvenni a listára, válassza ki **Hozzáadás**. Módosítsa a szűrőt, hogy ellenőrizze a számítógép-objektumok kívül 22. ábrán látható.

  ![21. ábra: Változás objektumtípusok számítógép felvétele][sap-ha-guide-figure-3020]

  _**21. ábra:** módosítás **objektumtípusok** közé tartoznak a számítógépek számára_

  ![22. ábra: Jelölje be a számítógépek][sap-ha-guide-figure-3021]

  _**22. ábra:** válassza ki a **számítógépek** jelölőnégyzetet_

4.  Adja meg a fürtnévobjektum, ahogy az ábra 21. A bejegyzést már létrejött, mert az engedélyek módosíthatja, ahogy az ábra 20.

5.  Válassza ki a **biztonsági** a megosztást, és majd lapján részletesebb a fürtnévobjektum engedélyeit.

  ![23. ábra: A fájl megosztási kvórum a fürt neve objektum biztonsági attribútumainak beállítása][sap-ha-guide-figure-3022]

  _**23. ábra:** a a fájl megosztási kvórum a fürt neve objektum biztonsági attribútumainak beállítása_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>Állítsa be a fájl megosztási tanúsító kvórum a Feladatátvevőfürt-kezelőben

1.  Nyissa meg a fürtkvórum beállítása varázsló konfigurálja.

  ![24. ábra: A konfigurálás fürtkvórum beállítása varázsló indítása][sap-ha-guide-figure-3023]

  _**24. ábra:** a konfigurálás fürtkvórum beállítása varázsló indítása_

2.  Az a **kvórumbeállítások kijelölése** lapon, hogy melyik **a kvórum tanúsítójának kijelölése**.

  ![25. ábra: Választhat kvórumkonfigurációinak][sap-ha-guide-figure-3024]

  _**25. ábra:** választhat kvórumkonfigurációinak_

3.  Az a **kvórum Tanúsítójának kijelölése** lapon, hogy melyik **konfigurálása egy tanúsító fájlmegosztást**.

  ![26. ábra: Válassza ki a tanúsító fájlmegosztás][sap-ha-guide-figure-3025]

  _**26. ábra:** válassza ki a tanúsító fájlmegosztás_

4.  Adja meg az UNC elérési útnak a fájlmegosztásra (a példánkban \\domcontr-0\FSW). A módosításokat végezhet listájának megtekintéséhez válasszon **következő**.

  ![27. ábra: A fájlmegosztási helyet a tanúsító fájlmegosztás meghatározása][sap-ha-guide-figure-3026]

  _**27. ábra:** határozza meg a fájlmegosztás helyét a tanúsító fájlmegosztás_

5.  Válassza ki a megfelelő módosításokat, majd válassza ki **következő**. Sikeresen konfigurálja újra a fürtkonfiguráció ahogy az ábra 28 kell:  

  ![28. ábra: Megerősítése, hogy a fürt már újra konfigurálni][sap-ha-guide-figure-3027]

  _**28. ábra:** , hogy a fürt már újra konfigurálni megerősítése_

Miután sikeresen telepítette a Windows feladatátvételi fürtszolgáltatás, módosítania néhány küszöbértékeket, azokat az Azure-ban feltételek feladatátvételi az észlelési igazításának lehetősége. A módosítandó paraméterei dokumentálva vannak [feladatátvevő fürt hálózati küszöbértékek hangolása][tuning-failover-cluster-network-thresholds]. Feltételezve, hogy a két virtuális gépek alkotó ASC/SCS Windows fürtkonfiguráció ugyanazon az alhálózaton, módosítsa ezeket az értékeket a következő paraméterekkel:

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Ezek a beállítások alapján történő teszteléskor az ügyfeleknek, és kínálnak a helyes biztonsági sérülése. -E elég rugalmas, de is biztosít, amely gyors elég tényleges hibaállapotok egy SAP-szoftver vagy a csomópont vagy virtuális gép a feladatátvételt.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Az SAP ASC/SCS fürtlemez-megosztás SIOS DataKeeper Cluster Edition telepítése

Most már rendelkezik egy működő Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban. Egy SAP ASC/SCS példányát telepítenie kell egy megosztott lemez erőforrás. Nem hozható létre a szükséges megosztott lemez erőforrások az Azure-ban. SIOS DataKeeper Cluster Edition egy külső megoldás, amely megosztott lemez erőforrások létrehozására használhatja.

Ezeket a feladatokat az SAP ASC/SCS megosztás fürtlemezt SIOS DataKeeper Cluster Edition telepítését foglalja magában:

- Adja hozzá a Microsoft .NET-keretrendszer 3.5-ös verzióját.
- Telepítse a SIOS DataKeeper.
- SIOS DataKeeper beállítása.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>Adja hozzá a .NET-keretrendszer 3.5-ös verzióját
.NET-keretrendszer 3.5 nincs aktiválva, vagy automatikusan Windows Server 2012 R2 rendszeren telepítve. SIOS DataKeeper használatához az összes olyan csomóponton, amelyen DataKeeper telepítenie kell a .NET, telepítenie kell a .NET-keretrendszer 3.5-ös verzióját az összes virtuális gépet a fürt a vendég operációs rendszeren.

Adja hozzá a .NET-keretrendszer 3.5 kétféleképpen történhet:

- A szerepkörök hozzáadása és szolgáltatások varázsló használata a Windows, ahogy az ábra 29:

  ![29. ábra: Telepítés .NET-keretrendszer 3.5-szerepkörök hozzáadása és szolgáltatások varázsló segítségével][sap-ha-guide-figure-3028]

  _**29. ábra:** telepítse .NET-keretrendszer 3.5-ös verzióját a szerepkörök hozzáadása és szolgáltatások varázsló segítségével_

  ![30. ábra: Telepítés folyamatjelző, a szerepkörök hozzáadása és szolgáltatások varázsló .NET-keretrendszer 3.5 telepítésekor][sap-ha-guide-figure-3029]

  _**30. ábra:** eszközterület-szerepkörök hozzáadása és szolgáltatások varázsló segítségével .NET-keretrendszer 3.5 telepítésekor a telepítési folyamat_

- Használja a dism.exe parancssori eszközt. Az ilyen típusú telepítést kell a Windows-telepítési adathordozón lévő SxS könyvtár eléréséhez. Egy rendszergazda jogú parancssorba írja be a parancsot:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a>SIOS DataKeeper telepítése

A fürt minden csomópontja SIOS DataKeeper Cluster Edition telepítését. Hozzon létre virtuális megosztott tárolási SIOS DataKeeper hozzon létre egy szinkronizált tükrözött, és ezután szimulálni a fürt megosztott tárhelye.

A SIOS szoftver telepítése előtt hozza létre a DataKeeperSvc tartományi felhasználót.

> [!NOTE]
> Adja hozzá a DataKeeperSvc tartományi felhasználót a helyi rendszergazdai csoporthoz mindkét fürtcsomóponton.
>
>

SIOS DataKeeper telepítése:

1.  Telepíti a SIOS mindkét fürtcsomóponton.

  ![SIOS telepítő][sap-ha-guide-figure-3030]

  ![31. ábra: A SIOS DataKeeper telepítés első oldalára][sap-ha-guide-figure-3031]

  _**31. ábra:** SIOS DataKeeper telepítésének első oldalára_

2.  A párbeszédpanelen válassza ki a **Igen**.

  ![32. ábra: DataKeeper arról tájékoztatja, hogy a szolgáltatás le lesz tiltva][sap-ha-guide-figure-3032]

  _**32. ábra:** DataKeeper arról tájékoztatja, hogy a szolgáltatás le lesz tiltva_

3.  A párbeszédpanelen, azt javasoljuk, hogy kiválassza **tartomány vagy a kiszolgáló fiók**.

  ![33. ábra: A SIOS DataKeeper felhasználó kiválasztása][sap-ha-guide-figure-3033]

  _**33. ábra:** SIOS DataKeeper a felhasználó kiválasztása_

4.  Adja meg a tartományi fiók felhasználói nevét és jelszavát, amelyet létrehozott SIOS DataKeeper.

  ![34. ábra: Adja meg a tartományi felhasználónevet és jelszót a SIOS DataKeeper telepítés][sap-ha-guide-figure-3034]

  _**34. ábra:** adja meg a tartományi felhasználónevet és jelszót a SIOS DataKeeper telepítés_

5.  Telepítse a SIOS DataKeeper példány licenckulcs, ahogy az ábra 35.

  ![35. ábra: Adja meg a SIOS DataKeeper licenckulcs][sap-ha-guide-figure-3035]

  _**35. ábra:** adja meg a SIOS DataKeeper licenckulcs_

6.  Amikor a rendszer kéri, indítsa újra a virtuális gép.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>SIOS DataKeeper beállítása

Miután telepítette a SIOS DataKeeper mindkét csomóponton, elindítja a konfigurációt. A konfigurációs célja a virtuális gépek mindegyikének csatlakozó további lemezek között szinkron replikálása rendelkezik.

1.  Indítsa el a DataKeeper kezelési és konfigurációs eszközt, és válassza **Connect kiszolgáló**.

  ![36. ábra: SIOS DataKeeper kezelési és konfigurációs eszköz][sap-ha-guide-figure-3036]

  _**36. ábra:** SIOS DataKeeper felügyelete és konfigurálása eszköz_

2.  Adja meg a nevét vagy IP-címét az első csomópontot a felügyeleti és konfigurációs eszköz számára, és egy második lépésben, a második csomópont kapcsolódnia kell.

  ![37. ábra: Helyezze be a nevet, vagy a TCP/IP-cím, az első csomópontot a felügyeleti és a konfigurációs eszközt kell csatlakoztatja, és egy második lépésben, a második csomópont][sap-ha-guide-figure-3037]

  _**37. ábra:** helyezze be a nevet vagy az első csomópontot a felügyeleti és a konfigurációs eszközt kell csatlakoztatja, és egy második lépésben, a második csomópont TCP/IP-címe_

3.  Hozzon létre a replikációs feladatot, a két csomópont között.

  ![38. ábra: A replikáció feladat létrehozása][sap-ha-guide-figure-3038]

  _**38. ábra:** replikációs feladat létrehozása_

  A varázsló végigvezeti egy fájlreplikálási feladat létrehozásának folyamatán.

4.  Adja meg a nevét, a replikációs feladat.

  ![39. ábra: Adja meg a nevét, a replikációs feladat][sap-ha-guide-figure-3039]

  _**39. ábra:** adja meg a nevét, a replikációs feladat_

  ![40. ábra: A csomópont, amely az aktuális adatforrás-csomópontnak kell lennie az alap adatok meghatározásához][sap-ha-guide-figure-3040]

  _**40. ábra:** a csomóponthoz, amely az aktuális adatforrás-csomópontnak kell lennie az alap adatok meghatározásához_

5.  Adja meg a nevét, az TCP/IP-cím és a célcsomóponton lemezkötetének.

  ![41. ábra: A nevét, a TCP/IP-cím és a aktuális célcsomóponton lemezkötetének megadása][sap-ha-guide-figure-3041]

  _**41. ábra:** a nevét, a TCP/IP-cím és az aktuális célcsomóponton lemezkötetének meghatározása_

6.  A tömörítési algoritmust határozza meg. Ebben a példában azt javasoljuk, hogy a tömörítés a replikálási adatfolyamból. Különösen abban az esetben az újraszinkronizálás a tömörítés a replikációs adatfolyam jelentősen csökkenti az újraszinkronizálás idő. Tömörítés a Processzor és memória szempontjából erőforrásokat, a virtuális gép használ. A tömörítési arány növekszik, ezért nem használt CPU-erőforrások mennyiségét. Ezt a beállítást később módosíthatja.

7.  Egy másik beállítást kell ellenőrizni, hogy a replikáció aszinkron vagy szinkron módon történik-e. Ha a SAP ASC/SCS konfigurációk, szinkron replikáció kell használnia.  

  ![42. ábra: A replikáció adatainak megadása][sap-ha-guide-figure-3042]

  _**42. ábra:** replikációs adatainak megadása_

8.  Határozza meg, hogy a kötetet, a replikációs feladat által replikált kell magát a Windows Server feladatátvevő fürt konfigurációjába, mint egy megosztott lemez számára. Az SAP ASC/SCS konfigurációs kiválasztása **Igen** , hogy a Windows fürtszolgáltatás látja a replikált kötet egy megosztott lemezt, amely egy fürt kötetként használhat.

  ![43. ábra: Válassza az Igen, a replikált kötet beállítása a fürt kötetként][sap-ha-guide-figure-3043]

  _**43. ábra:** válasszon **Igen** a replikált kötet beállítása a fürt kötetként_

  A kötet létrehozása után a DataKeeper felügyelete és konfigurálása eszköz mutatja, hogy a replikációs feladat aktív.

  ![44. ábra: DataKeeper szinkron tükrözés az SAP ASC/SCS megosztás lemez jelenleg aktív][sap-ha-guide-figure-3044]

  _**44. ábra:** DataKeeper szinkron tükrözés az SAP ASC/SCS a lemez megosztása a jelenleg aktív_

  Feladatátvevőfürt-kezelő most a lemez DataKeeper lemezként jeleníti meg, ahogy az ábra 45:

  ![45. ábra: A Feladatátvevőfürt-kezelő a lemez DataKeeper replikált jeleníti meg][sap-ha-guide-figure-3045]

  _**45. ábra:** Feladatátvevőfürt-kezelő jeleníti meg a lemezt, a replikált DataKeeper_

## <a name="next-steps"></a>Következő lépések

* [Telepítése SAP NetWeaver magas rendelkezésre ÁLLÁSÚ egy Windows feladatátvevő fürt és megosztott lemez egy SAP ASC/SCS példány][sap-high-availability-installation-wsfc-shared-disk]
