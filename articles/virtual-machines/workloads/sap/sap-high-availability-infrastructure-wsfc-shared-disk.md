---
title: Az SAP magas rendelkezésre ÁLLÁS Windows feladatátvevő fürt és megosztott lemez esetében az SAP ASCS/SCS használatával az Azure-infrastruktúra előkészítése |} A Microsoft Docs
description: Ismerje meg, hogyan készíti elő az Azure-infrastruktúra a SAP magas rendelkezésre ÁLLÁS az SAP ASCS/SCS példányhoz Windows feladatátvevő fürt és megosztott lemez használatával.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12decd07934b45c3f2e8b9b098af305303641176
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634778"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Készítse elő az Azure-infrastruktúra az SAP magas rendelkezésre ÁLLÁS segítségével egy Windows feladatátvevő fürt és megosztott lemez esetében az SAP ASCS/SCS

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP több biztonsági AZONOSÍTÓVAL magas rendelkezésre állású konfiguráció)

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

Ez a cikk ismerteti a lépéseket, az Azure-infrastruktúra előkészítése telepítése és konfigurálása egy magas rendelkezésre állású SAP-rendszerhez egy Windows feladatátvevő fürtön használatával egy *megosztott fürtlemez* lehetőségként a fürtszolgáltatás egy Az SAP ASCS-példány.

## <a name="prerequisites"></a>Előfeltételek

A telepítés megkezdése előtt tekintse át az ebben a cikkben:

* [Architektúra-Útmutató: az SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fürt megosztott lemez használatával][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Az infrastruktúra előkészítése az 1. a sablon architekturális.
Az SAP az Azure Resource Manager-sablonok segítségével egyszerűsítheti az üzembe helyezést, a szükséges erőforrásokat.

A háromrétegű sablonok az Azure Resource Manager támogatja a magas rendelkezésre állású forgatókönyveket is. 1. a sablon architekturális. például két fürt rendelkezik. Minden egyes fürt egy SAP rendszerkritikus meghibásodási pontot SAP ASCS/SCS-és adatbázis-kezelő rendszer.

Itt látható, ahol is igénybe az Azure Resource Manager-sablonok az ebben a cikkben ismertetünk. példa:

* [Az Azure Marketplace-lemezkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Az Azure Marketplace-en rendszerképet az Azure Managed Disks használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Felügyelt lemezek használatával egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Az infrastruktúra előkészítése architekturális sablon 1:

- Az Azure Portalon az a **paraméterek** ablaktáblán, a a **SYSTEMAVAILABILITY** jelölje ki **magas rendelkezésre ÁLLÁSÚ**.

  ![1. ábra: Állítsa be SAP magas rendelkezésre állású Azure Resource Manager-paraméterek][sap-ha-guide-figure-3000]

_**1. ábra:** beállítása SAP magas rendelkezésre állású Azure Resource Manager-paraméterek_


  A sablonok létrehozása:

  * **Virtuális gépek**:
    * SAP alkalmazáskiszolgáló virtuális gépek: \<SAPSystemSID\>- di -\<száma\>
    * A fürt ASCS/SCS virtuális gépek: \<SAPSystemSID\>– ascs-\<száma\>
    * Az adatbázis-kezelő fürt: \<SAPSystemSID\>- db-\<száma\>

  * **Hálózati kártya minden virtuális gép társított IP-címekkel rendelkező**:
    * \<SAPSystemSID\>- nic-di -\<száma\>
    * \<SAPSystemSID\>- nic-ascs -\<száma\>
    * \<SAPSystemSID\>- nic-db -\<száma\>

  * **Az Azure storage-fiókok (csak a nem felügyelt lemezek)**:

  * **Rendelkezésre állási csoportok** számára:
    * SAP alkalmazáskiszolgáló virtuális gépek: \<SAPSystemSID\>- avset-di
    * Az SAP ASCS/SCS virtuális gépek fürtözése: \<SAPSystemSID\>- avset – ascs
    * Az adatbázis-kezelő fürt virtuális gépek: \<SAPSystemSID\>- avset-db

  * **Az Azure belső terheléselosztó**:
    * Az ASCS/SCS példányhoz és IP-cím minden porttal \<SAPSystemSID\>- lb-ascs
    * Az összes port az SQL Server adatbázis-kezelő és az IP-cím \<SAPSystemSID\>- lb-db

  * **Hálózati biztonsági csoport**: \<SAPSystemSID\>- nsg-ascs-0  
    * Egy megnyitott külső távoli asztal protokoll (RDP) port, a \<SAPSystemSID\>virtuálisgép - ascs-0

> [!NOTE]
> A hálózati kártyák és az Azure belső terheléselosztók összes IP-címek dinamikusak alapértelmezés szerint. Statikus IP-címek módosítsa őket. A cikk későbbi részében ehhez ismertetünk.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> A vállalati hálózati kapcsolattal (létesítmények közötti) az éles környezetben használt virtuális gépek üzembe helyezése
Az SAP-rendszereit, üzembe helyezése az Azure-beli virtuális gépek [vállalati hálózati kapcsolat (létesítmények közötti)] [ planning-guide-2.2] Azure VPN Gateway vagy az Azure ExpressRoute használatával.

> [!NOTE]
> Az Azure Virtual Network-példányt is használhat. A virtuális hálózat és alhálózat már létrehozott és előkészített.
>
>

1.  Az Azure Portalon az a **paraméterek** ablaktáblán, a a **NEWOREXISTINGSUBNET** jelölje ki **meglévő**.
2.  Az a **SUBNETID** adjon hozzá a teljes karakterláncot, az előkészített Azure-beli hálózati alhálózati azonosító, ha azt tervezi, hogy az Azure-beli virtuális gépek üzembe helyezése.
3.  Az összes Azure-beli hálózati alhálózatok listájának lekéréséhez futtassa a következő PowerShell-paranccsal:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  A **azonosító** mező értékét mutatja a alhálózati azonosítóját.
4. Minden alhálózati azonosító értékek listájának lekéréséhez futtassa a PowerShell-parancsot:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  Az alhálózati azonosító így néz ki:

  ```
  /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
  ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> A tesztelési és bemutató csak felhőalapú SAP-példányok üzembe helyezése
A csak felhőalapú üzembe helyezési modell magas rendelkezésre állású SAP-rendszerrel is telepítheti. Az ilyen típusú központi telepítés elsősorban hasznos bemutató és tesztelés a használati esetek. Éles környezeti alkalmazási helyzetek esetén nem használhatók.

- Az Azure Portalon az a **paraméterek** ablaktáblán, a a **NEWOREXISTINGSUBNET** jelölje ki **új**. Hagyja a **SUBNETID** mezője üres.

  Az SAP az Azure Resource Manager-sablon automatikusan létrehozza az Azure virtuális hálózatot és alhálózatot.

> [!NOTE]
> Akkor is szükség van legalább egy dedikált virtuális gép az Azure Virtual Network ugyanazon Active Directory és DNS-szolgáltatás. A sablon nem hoz létre ezekre a virtuális gépekre.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>2. a sablon architekturális. az infrastruktúra előkészítése

Az SAP az Azure Resource Manager-sablonok segítségével az SAP architekturális sablon 2. a szükséges infrastruktúra-erőforrások üzembe helyezés egyszerűsítéséhez.

Itt látható, ahol megkapja a az Azure Resource Manager-sablonok a központi telepítési forgatókönyv:

* [Az Azure Marketplace-lemezkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Az Azure Piactéri lemezképhez Managed Disks használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Felügyelt lemezek használatával egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>3. a sablon architekturális. az infrastruktúra előkészítése

Az infrastruktúra előkészítése, és konfigurálja az SAP több biztonsági AZONOSÍTÓVAL. Például hozzáadhat egy további, az SAP ASCS/SCS-példányt egy *meglévő* fürtkonfiguráció. További információkért lásd: [konfigurálása egy további SAP ASCS/SCS példányhoz egy SAP több biztonsági AZONOSÍTÓVAL konfiguráció létrehozása az Azure Resource Manager-meglévő fürt konfigurálásának][sap-ha-multi-sid-guide].

Ha több biztonsági AZONOSÍTÓVAL fürtöt létrehozni, használhatja a több biztonsági AZONOSÍTÓVAL [gyorsindítási sablonok github](https://github.com/Azure/azure-quickstart-templates).

Több SID-vel fürtöt létrehozni, telepítenie kell az alábbi három sablonok:

* [ASCS/SCS-sablon](#ASCS-SCS-template)
* [Adatbázis-sablon](#database-template)
* [Alkalmazássablon-kiszolgálók](#application-servers-template)

Az alábbi szakaszok a sablonok és a paraméterek, amelyeket meg kell adnia a sablonok részletesebb információkat tartalmaz.

### <a name="ASCS-SCS-template"></a> ASCS/SCS-sablon

Az ASCS/SCS-sablon üzembe helyezi a két virtuális gépet, amelyek segítségével több ASCS/SCS-példányt üzemeltető Windows Server feladatátvevő fürt létrehozása.

Az ASCS/SCS több biztonsági AZONOSÍTÓVAL sablon, a beállítása a [ASCS/SCS több biztonsági AZONOSÍTÓVAL sablon] [ sap-templates-3-tier-multisid-xscs-marketplace-image] vagy [ASCS/SCS több biztonsági AZONOSÍTÓVAL sablon Managed Disks használatával] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], adja meg az értékeket az alábbi paraméterekkel:

  - **Erőforrás-előtag**: állítsa be az erőforrás-előtagja, amelynek az üzembe helyezés során létrehozott összes erőforrást előtagot használja. Az erőforrások csak egy SAP-rendszerhez tartozik, mert az előtag, az erőforrás nem áll a SID-egy SAP-rendszerhez.  Az előtag a 3-6 karakter közöttinek kell lennie.
  - **Írja be a verem**: válassza ki a verem az SAP-rendszerhez. A stack típusától függően az Azure Load Balancerhez tartozik (ABAP és Java csak) egy vagy két (ABAP + Java) magánhálózati IP-címek száma SAP-rendszerhez.
  -  **Operációs rendszer típusa**: válassza ki a virtuális gépek operációs rendszerének.
  -  **Az SAP-rendszer száma**: válassza ki az SAP-rendszereit, amelyek a fürtön telepíteni kívánt számát.
  -  **Rendszer rendelkezésre állását**: válasszon **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazdai felhasználónév és jelszó rendszergazdai**: hozzon létre egy új felhasználót, jelentkezzen be a gép is lehet.
  -  **Új vagy meglévő alhálózat**: állítsa be, hogy hozzon létre egy új virtuális hálózatot és alhálózatot, vagy egy meglévő alhálózatot használja. Ha a helyszíni hálózatához csatlakoztatott virtuális hálózat már rendelkezik, válassza ki a **meglévő**.
  -  **Alhálózati azonosító**: Ha azt szeretné, helyezheti üzembe a virtuális gép egy meglévő Vnetet, amelyekben egy meghatározott alhálózatot a virtuális gép hozzá kell rendelni, nevezze el a kívánt alhálózatot. Az azonosító általában néz ki:

   /Subscriptions/\<előfizetés-azonosító\>/resourceGroups/\<erőforráscsoport-név\>/providers/Microsoft.Network/virtualNetworks/\<virtuálishálózat-nevet\>/subnets/ \<alhálózat neve\>

A sablon üzembe helyez egy Azure Load Balancer példányt, amely támogatja a több SAP-rendszerek:

- Az ASCS példányok úgy vannak konfigurálva, a példány számát 00, 10, 20...
- Az SCS-példányok száma 01, 11., 21 konfigurált...
- Az ASCS sorba replikációs kiszolgáló (SSZON) (csak Linux) példányok úgy vannak konfigurálva, a példány a 02, 12, 22-es szám...
- Az SCS SSZON (csak Linux) példányok úgy vannak konfigurálva, a példányok száma 03., 13., 23...

A load balancer tartalmaz 1 VIP(s) (2. Linux esetén), ASCS/SCS virtuális 1 x IP és 1 x virtuális IP-cím a SSZON (csak Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> Az SAP ASCS/SCS-portok
Az alábbi lista tartalmazza az összes terheléselosztási szabályok (ahol x a az SAP-rendszerhez, például 1, 2, 3... száma):
- Minden SAP rendszere Windows-specifikus portokat: 445-ös, 5985
- ASCS portok (x0 száma): 32 x 0, 36 x 0, 39 x 0, 81-es x 0, 5 x 013, 5 x 014, 5 x 016
- SCS portok (x1 száma): 32 x 1, 33 x 1, 39 x 1, 81-es x 1, 5 x 113, 5 x 114, 5 x 116
- Linux (példányszámának x2) portok ASCS SSZON: 33 x 2, 5 x 213, 5 x 214, 5 x 216
- Linux (példányszámának x3) portok SCS SSZON: 33 x 3, 5 x 313, 5 x 314, 5 x 316

A terheléselosztó a következő mintavételi portokon (Ha x az a szám az SAP-rendszer, például 1, 2, 3...) használatára van konfigurálva:
- ASCS/SCS belső load balancer mintavételi port: 620 x 0
- SSZON belső load balancer mintavételi portot (csak Linux): 621 x 2

### <a name="database-template"></a> Adatbázis-sablon

Az adatbázis-sablon üzembe helyez egy vagy két virtuális gépek, amelyek egy SAP-rendszer relációsadatbázis-kezelő rendszerének (RDBMS) telepítéséhez használhatja. Például ha telepít egy ASCS/SCS-sablon az öt SAP-rendszereit, szüksége ötször a sablon üzembe helyezésére.

A beállítása az adatbázis több biztonsági AZONOSÍTÓVAL sablon, a [adatbázis több biztonsági AZONOSÍTÓVAL sablon] [ sap-templates-3-tier-multisid-db-marketplace-image] vagy [adatbázis több biztonsági AZONOSÍTÓVAL sablon Managed Disks használatával] [ sap-templates-3-tier-multisid-db-marketplace-image-md], adja meg az értékeket az alábbi paraméterekkel:

  -  **Rendszer-azonosító SAP**: Adja meg az SAP az SAP-rendszerhez, amelyet át szeretne telepíteni, rendszer-azonosító. Az azonosító az üzembe helyezett erőforrások előtagjaként is szolgál.
  -  **Operációs rendszer típusa**: válassza ki a virtuális gépek operációs rendszerének.
  -  **DbType**: válassza ki az adatbázis, amely a fürtön telepíteni kívánt típusát. Válassza ki **SQL** Ha Microsoft SQL Server telepítéséhez. Válassza ki **HANA** Ha azt tervezi, hogy az SAP HANA telepítése a virtuális gépeken. Győződjön meg arról, hogy bejelöli-e a megfelelő operációs rendszer típusa. Válassza ki **Windows** SQL és a Hana Linux-disztribúciók válassza. Az Azure Load Balancer, amely csatlakozik a virtuális gépek a kiválasztott adatbázis típusa támogatására van konfigurálva:
    * **SQL**: A load balancer terheléselosztás 1433-as portra. Ellenőrizze, hogy ezt a portot használja az SQL Server AlwaysOn beállítás.
    * **HANA**: A load balancer terheléselosztás 35015 és portok 35017. Mindenképpen telepítse az SAP HANA-példányok számát **50**.
    A terheléselosztó 62550 mintavételi portot használja.
  -  **SAP-rendszer mérete**: az új rendszer biztosít SAP számának megadása. Ha nem, hogy a rendszer hány SAP, kérje meg az SAP technológiai partnerek vagy rendszerintegrátor.
  -  **Rendszer rendelkezésre állását**: válasszon **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazdai felhasználónév és jelszó rendszergazdai**: hozzon létre egy új felhasználót, jelentkezzen be a gép is lehet.
  -  **Alhálózati azonosító**: Adja meg az alhálózatot, amelyet a ASCS/SCS-sablon üzembe helyezése során használt azonosítója vagy az alhálózatot, amelyet létrejött azonosítója a ASCS/SCS sablon központi telepítésének részeként.

### <a name="application-servers-template"></a> Alkalmazássablon-kiszolgálók

Az alkalmazássablon kiszolgálók SAP-alkalmazáskiszolgáló-példányok egy SAP-rendszerhez használható két vagy több virtuális gépeket helyez üzembe. Például ha telepít egy ASCS/SCS-sablon az öt SAP-rendszereit, szüksége ötször a sablon üzembe helyezésére.

A beállítása az kiszolgálók több biztonsági AZONOSÍTÓVAL sablon, a [alkalmazássablon kiszolgálók több biztonsági AZONOSÍTÓVAL] [ sap-templates-3-tier-multisid-apps-marketplace-image] vagy [kiszolgálók több biztonsági AZONOSÍTÓVAL alkalmazássablon Managed Diskshasználatával] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], adja meg az értékeket az alábbi paraméterekkel:

  -  **Rendszer-azonosító SAP**: Adja meg az SAP az SAP-rendszerhez, amelyet át szeretne telepíteni, rendszer-azonosító. Az azonosító az üzembe helyezett erőforrások előtagjaként is szolgál.
  -  **Operációs rendszer típusa**: válassza ki a virtuális gépek operációs rendszerének.
  -  **SAP-rendszer mérete**: az új rendszer biztosít SAP száma. Ha nem, hogy a rendszer hány SAP, kérje meg az SAP technológiai partnerek vagy rendszerintegrátor.
  -  **Rendszer rendelkezésre állását**: válasszon **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazdai felhasználónév és jelszó rendszergazdai**: hozzon létre egy új felhasználót, jelentkezzen be a gép is lehet.
  -  **Alhálózati azonosító**: Adja meg az alhálózatot, amelyet a ASCS/SCS-sablon üzembe helyezése során használt azonosítója vagy az alhálózatot, amelyet létrejött azonosítója a ASCS/SCS sablon központi telepítésének részeként.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Az Azure Virtual Network
Ebben a példában az Azure Virtual Network-példány a címtér a 10.0.0.0/16. Nincs több alhálózat-címtartománya a 10.0.0.0/24 alhálózat, nevű. A virtuális hálózatban lévő virtuális gépek és a belső terheléselosztók vannak telepítve.

> [!IMPORTANT]
> A vendég operációs rendszeren belül a hálózati beállításokat nem kell semmit módosítania. Ez magában foglalja az IP-címek, DNS-kiszolgálók és alhálózat. A hálózati beállítások konfigurálása az Azure-ban. A Dynamic Host Configuration Protocol (DHCP) szolgáltatás tölti ki a beállításokat.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP-címek

Állítsa be a szükséges DNS-IP-címek, hajtsa végre az alábbi lépéseket:

1.  Az Azure Portalon az a **DNS-kiszolgálók** ablaktáblán ellenőrizze, hogy a virtuális hálózat **DNS-kiszolgálók** beállítás **egyéni DNS**.
2.  Válassza ki a beállításokat, rendelkezik hálózat típusa alapján. További információkért lásd a következőket:
    * [Vállalati hálózati kapcsolat (létesítmények közötti)][planning-guide-2.2]: Adja hozzá a helyi DNS-kiszolgálók IP-címét.  
    Kiterjesztheti a helyszíni DNS-kiszolgálók Azure-ban futó virtuális gépekhez. Ebben az esetben a DNS-szolgáltatást futtató Azure virtuális gépek IP-címek is hozzáadhat.
    * [Kizárólag felhőalapú üzembe helyezés][planning-guide-2.1]: a virtuális hálózat példányt, amely egy DNS-kiszolgálót egy további virtuális gép üzembe helyezése. Adja hozzá az Azure virtuális gépeket, akár beállított IP-címét a DNS-szolgáltatás futtatásához.

    ![2. ábra: Azure virtuális hálózat DNS-kiszolgálók konfigurálása][sap-ha-guide-figure-3001]

    _**2. ábra:** DNS konfigurálása Azure Virtual Network kiszolgálók_

  > [!NOTE]
  > Ha módosítja a DNS-kiszolgálók IP-címek, meg kell indítania az Azure virtuális gépeket, a módosítás alkalmazása és az új DNS-kiszolgálók propagálása.
  >
  >

Ebben a példában a DNS-szolgáltatás telepítve és konfigurálva van a Windows virtuális gépeken:

| Virtuális gépi szerepkör | Virtuális gép állomásneve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első DNS-kiszolgáló |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Második DNS-kiszolgáló |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Állomásnév és az SAP ASCS/SCS fürtözött példányát és az adatbázis-kezelő fürtözött példány statikus IP-címek

A helyszíni telepítéshez szüksége ezen fenntartott neveket és IP-címek:

| Virtuális állomás neve szerepkör | Virtuális állomás neve | Virtuális statikus IP-cím |
| --- | --- | --- |
| Az SAP ASCS/SCS első fürt virtuális host name (kezelő) |pr1-ascs-vir |10.0.0.42 |
| Az SAP ASCS/SCS példányhoz virtuális állomás neve |PR1 – ascs-sap |10.0.0.43 |
| Az SAP DBMS második fürt virtuális állomás neve (kezelő) |pr1-dbms-vir |10.0.0.32 |

A fürt létrehozásakor hozzon létre a virtuális gazdagép nevek pr1 – ascs-vir és pr1 – dbms-vir és kezelheti a fürthöz társított IP-címeket. Ennek módjáról további információkért lásd: [fürtcsomópontok fürtkonfiguráció gyűjtése][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

A DNS-kiszolgálón manuálisan a másik két virtuális állomásnevek, pr1 – ascs-sap- és pr1 – dbms-sap és a társított IP-címeket is létrehozhat. A fürtözött SAP ASCS/SCS-példány és a fürtözött adatbázis-kezelő példány használja ezeket az erőforrásokat. Ennek módjáról további információkért lásd: [hozzon létre egy virtuális nevet a fürtözött SAP ASCS/SCS példányhoz][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Állítsa be a statikus IP-címeket, a SAP virtual Machines
A virtuális gépek használata a fürt üzembe helyezése, után minden virtuális gép statikus IP-címeket be kell. Ehhez az Azure Virtual Network konfigurációban, nem pedig a vendég operációs rendszer.

1.  Az Azure Portalon válassza ki a **erőforráscsoport** > **hálózati kártya** > **beállítások** > **IP-cím**.
2.  Az a **IP-címek** panel alatt **hozzárendelés**, jelölje be **statikus**. Az a **IP-cím** mezőbe írja be a használni kívánt IP-címet.

  > [!NOTE]
  > Ha módosítja a hálózati kártya IP-címét, indítsa újra az Azure virtuális gépeket, a módosítás alkalmazására van szükség.  
  >
  >

  ![3. ábra: A statikus IP-címeket a hálózati kártya minden virtuális gép beállítása][sap-ha-guide-figure-3002]

  _**3. ábra:** statikus IP-címeket a hálózati kártya minden virtuális gép beállítása_

  Ismételje meg ezt a lépést, amely van, minden virtuális gép, beleértve a virtuális gépeket, amelyek az Active Directory vagy a DNS szolgáltatás használni kívánt összes hálózati adapter.

Ebben a példában rendelkezünk ezen virtuális gépek és a statikus IP-címek:

| Virtuális gépi szerepkör | Virtuális gép állomásneve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első SAP alkalmazás server-példány |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Második SAP-alkalmazáskiszolgáló-példány |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Utolsó SAP alkalmazás server-példány |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Első fürtcsomópontra ASCS/SCS példányhoz |PR1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Második fürtcsomópontra ASCS/SCS példányhoz |PR1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| DBMS-példányra az első fürtcsomópontra |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Második fürtcsomópontra DBMS-példány |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Az Azure belső terheléselosztó statikus IP-cím beállítása

Az SAP az Azure Resource Manager-sablont hoz létre az Azure belső terheléselosztó az SAP ASCS/SCS példányhoz és az adatbázis-kezelő fürtön használt.

> [!IMPORTANT]
> Az IP-címe az SAP ASCS/SCS virtuális állomás neve megegyezik az SAP ASCS/SCS belső terheléselosztó IP-címe: pr1-lb-ascs rendszerbe fut be.
> A DBMS neve virtuális IP-címe megegyezik a DBMS belső terheléselosztó IP-címe: pr1-lb-adatbázis-kezelő.
>
>

Az Azure belső terheléselosztó statikus IP-címet beállítani:

1.  Az első üzembe helyezés beállítja a belső terheléselosztó IP-címe **dinamikus**. Az Azure Portalon az a **IP-címek** panel alatt **hozzárendelés**, jelölje be **statikus**.
2.  A belső terheléselosztó IP-cím beállítva **pr1-lb-ascs** az SAP ASCS/SCS-példány állomásneve, IP-címet.
3.  A belső terheléselosztó IP-cím beállítva **pr1-lb-dbms** virtuális állomás nevét a DBMS-példány IP-címet.

  ![4. ábra: Állítsa be a statikus IP-címeket, a belső terheléselosztó az SAP ASCS/SCS példányhoz][sap-ha-guide-figure-3003]

  _**4. ábra:** statikus IP-címek beállítása a belső terheléselosztó az SAP ASCS/SCS példányhoz_

Ebben a példában van két Azure belső terheléselosztó, amely a statikus IP-címet:

| Az Azure belső terheléselosztói szerepkör | Az Azure belső terheléselosztó neve | Statikus IP-cím |
| --- | --- | --- |
| Az SAP ASCS/SCS példány belső terheléselosztót |PR1-lb-ascs |10.0.0.43 |
| Az SAP DBMS belső load balancer |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Alapértelmezett ASCS/SCS-terheléselosztási szabályok az Azure belső terheléselosztó

Az SAP az Azure Resource Manager-sablont hoz létre a szükséges portokat:
* Az alapértelmezett példányszámának 00, ABAP ASCS példányok
* A Java SCS példányhoz, 01 alapértelmezett példány számát

Az SAP ASCS/SCS példányhoz telepítésekor kell használnia az alapértelmezett példányszámának 00 az ABAP ASCS-példány és az alapértelmezett példányszámának 01 a Java SCS példányhoz.

Ezután hozza létre a szükséges belső terheléselosztási végpont esetében az SAP NetWeaver-portokat.

Szükséges belső terheléselosztási végpont létrehozásához először hozza létre a terheléselosztási végpontok a SAP NetWeaver ABAP ASCS portok:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (ASCS példány példányszámának 00) (10 SSZON) |
| --- | --- | --- |
| Sorba server / *lbrule3200* |32\<példányszám\> |3200 |
| ABAP üzenetkiszolgáló / *lbrule3600* |36\<példányszám\> |3600 |
| Belső ABAP-üzenet / *lbrule3900* |39\<példányszám\> |3900 |
| Üzenetkiszolgáló HTTP / *Lbrule8100* |81-es\<példányszám\> |8100 |
| Az SAP ASCS HTTP szolgáltatás elindítása / *Lbrule50013* |5\<példányszám\>13 |50013 |
| Az SAP ascs rendszerbe fut be HTTPS szolgáltatás elindítása / *Lbrule50014* |5\<példányszám\>14 |50014 |
| Sorba replikációs / *Lbrule50016* |5\<példányszám\>16 |50016 |
| SAP on felhasználók HTTP szolgáltatás elindítása *Lbrule51013* |5\<példányszám\>13 |51013 |
| SAP on felhasználók HTTP szolgáltatás elindítása *Lbrule51014* |5\<példányszám\>14 |51014 |
| Windows Rendszerfelügyeleti (webszolgáltatások WinRM) *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

**1. táblázat:** portszámot az SAP NetWeaver ABAP ASCS példányok

Ezután hozzon létre a terheléselosztási végpontok a SAP NetWeaver Java SCS portok:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (SCS példányhoz az példányszámának 01) (SSZON-11) |
| --- | --- | --- |
| Sorba server / *lbrule3201* |32\<példányszám\> |3201 |
| Átjárókiszolgáló / *lbrule3301* |33\<példányszám\> |3301 |
| Java message kiszolgáló / *lbrule3900* |39\<példányszám\> |3901 |
| Üzenetkiszolgáló HTTP / *Lbrule8101* |81-es\<példányszám\> |8101 |
| SAP SCS HTTP szolgáltatás elindítása / *Lbrule50113* |5\<példányszám\>13 |50113 |
| SAP SCS HTTPS szolgáltatás elindítása / *Lbrule50114* |5\<példányszám\>14 |50114 |
| Sorba replikációs / *Lbrule50116* |5\<példányszám\>16 |50116 |
| SAP on felhasználók HTTP szolgáltatás elindítása *Lbrule51113* |5\<példányszám\>13 |51113 |
| SAP on felhasználók HTTP szolgáltatás elindítása *Lbrule51114* |5\<példányszám\>14 |51114 |
| A Rendszerfelügyeleti webszolgáltatások *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

**2. táblázat:** portszámot az SAP NetWeaver Java SCS-példányok

![5. ábra: Alapértelmezett ASCS/SCS-terheléselosztási szabályok az Azure belső terheléselosztó][sap-ha-guide-figure-3004]

_**5. ábra:** alapértelmezett ASCS/SCS-terheléselosztási szabályok az Azure belső terheléselosztó_

A load balancer pr1-lb-dbms IP-címét állítsa a virtuális gazdagép neve DBMS-példány IP-címet.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Az ASCS/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztó módosítása

Ha azt szeretné, az SAP ASCS vagy SCS példányokhoz használandó eltérő számú, módosítania kell neveket és értékeket a portok alapértelmezett értékek közül.

1.  Az Azure Portalon válassza ki a  **\<SID\>-lb-ascs terheléselosztó** > **terheléselosztási szabályok betöltése**.
2.  Az összes terheléselosztási szabályok, amelyek az SAP ASCS vagy SCS példányhoz tartozik módosítsa ezeket az értékeket:

  * Name (Név)
  * Port
  * Háttér-port

  Például ha szeretné módosítani az alapértelmezett ASCS példányszámának 00 31-ig, meg kell hajtsa végre a módosításokat az összes port az 1.

  Íme egy példa egy frissítést, a port *lbrule3200*.

  ![6. ábra: Módosítsa a ASCS/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztó][sap-ha-guide-figure-3005]

  _**6. ábra:** ASCS/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztó módosítása_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Windows virtuális gépek hozzáadása a tartományhoz

Miután statikus IP-címet rendel a virtuális gépek, virtuális gépeket adni a tartományhoz.

![7. ábra: Virtuális gép hozzáadása egy tartományhoz][sap-ha-guide-figure-3006]

_**7. ábra:** hozzáadása egy virtuális gépet egy tartományhoz_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adja hozzá a beállításjegyzék-bejegyzések az SAP ASCS/SCS-példányának mindkét fürtcsomóponton

Az Azure Load Balancer belső terheléselosztót, hogy bezárul kapcsolatok, ha a megadott kapcsolatok üresjárati idő (üresjárati időkorlátot) rendelkezik. A párbeszédpanelen példányok nyitott kapcsolatok az SAP sorbaállítása SAP munkafolyamatok feldolgozni, amint az első sorba/eltávolítása a sorból kérelem kell küldeni. Ezek a kapcsolatok általában marad megalapozott a work folyamat, vagy sorba folyamata újraindul. Azonban ha a kapcsolat egy meghatározott ideig tétlen, az Azure belső terheléselosztó bezárja a kapcsolatokat. Ez nem probléma, mivel az SAP rendellenesnek akkor újra létrehozza a kapcsolatot a sorba folyamatot, ha már nem létezik. Ezeket a tevékenységeket a fejlesztői nyomkövetések SAP folyamatok vannak dokumentálva, de ezeket a nyomkövetéseket a felesleges tartalmat nagy mennyiségű hoznak létre. Módosíthatja a TCP/IP-érdemes `KeepAliveTime` és `KeepAliveInterval` mindkét fürtcsomóponton. Ezeket a módosításokat a TCP/IP-paraméterek SAP profil paraméterek, a cikk későbbi részében leírt össze.

Az SAP ASCS/SCS-példányának mindkét fürtcsomóponton beállításjegyzék-bejegyzések hozzáadásához először adja hozzá a Windows beállításjegyzék-bejegyzések mindkét Windows fürtcsomópontokon az SAP ASCS/SCS:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveTime` |
| A változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Dokumentáció mutató hivatkozás |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**3. táblázat:** módosítsa az első TCP/IP-paraméter

Ezután adja hozzá a Windows-beállításjegyzékbeli bejegyzést mindkét Windows fürtcsomópontokon az SAP ASCS/SCS:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveInterval` |
| A változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Dokumentáció mutató hivatkozás |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**4. táblázat:** módosítsa a második TCP/IP-paraméter

A módosítások életbe léptetéséhez indítsa újra a mindkét fürtcsomóponton.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Az SAP ASCS/SCS példányhoz a Windows Server feladatátvevő fürt beállítása

Az SAP ASCS/SCS példányhoz egy Windows Server feladatátvevő fürt beállítása magában foglalja ezeket a feladatokat:

- Fürtözött konfigurációban a fürt csomópontjai összegyűjtése.
- A fürt tanúsító fájlmegosztás konfigurálása.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Fürtözött konfigurációban a fürt csomópontjai gyűjtése

1.  A szerepkör hozzáadása és szolgáltatások varázsló adja hozzá a Feladatátvételi fürtszolgáltatást mindkét fürtcsomóponton.
2.  Állítsa be a feladatátvevő fürt Feladatátvevőfürt-kezelő használatával. A Feladatátvevőfürt-kezelőben válassza **-fürt létrehozása**, majd adja hozzá a csak az első fürtöt (A csomópont) nevét. A második csomópont ne adjon hozzá még; a második csomópontot adhat egy későbbi lépésben.

  ![8. ábra: A kiszolgáló vagy virtuális gép nevére az első fürtcsomópont hozzáadása][sap-ha-guide-figure-3007]

  _**8. ábra:** adja hozzá az első fürtcsomópontra a kiszolgáló vagy virtuális gép neve_

3.  Adja meg a fürt hálózati neve (virtuális állomás neve).

  ![9. ábra: Adja meg a fürt neve][sap-ha-guide-figure-3008]

  _**9. ábra:** adja meg a fürt neve_

4.  Miután létrehozta a fürthöz, futtassa a fürtellenőrzési tesztet.

  ![10. ábra: A fürt-ellenőrzés futtatása][sap-ha-guide-figure-3009]

  _**10. ábra:** a fürt-ellenőrzés futtatása_

  A folyamat ezen a ponton lemezekkel kapcsolatos figyelmeztetéseket figyelmen kívül hagyhatja. Egy tanúsító fájlmegosztást és az SIOS megosztott lemezeket később fogja hozzáadni. Ebben a szakaszban nem kell foglalkoznia a kvóruma.

  ![11. ábra: Nincs kvórum lemez található][sap-ha-guide-figure-3010]

  _**11. ábra:** nincs kvórum lemez található_

  ![12. ábra: A fürt alapvető erőforrásai kell új IP-cím][sap-ha-guide-figure-3011]

  _**12. ábra:** fürt alapvető erőforrásai, szüksége van egy új IP-cím_

5.  Módosítsa a core fürtszolgáltatás IP-címét. A fürt nem indítható el, amíg nem módosítja a core fürtszolgáltatás IP-címét, mert a kiszolgáló IP-címét a virtuálisgép-csomópontok egyikét mutat. Ehhez a **tulajdonságok** lapján a core fürtszolgáltatás IP-erőforrást.

  Például azt kell rendelnie egy IP-címet (a példánkban 10.0.0.42) számára a fürt virtuális állomás neve pr1 – ascs-vir.

  ![13. ábra: A Tulajdonságok párbeszédpanelen módosítsa az IP-cím][sap-ha-guide-figure-3012]

  _**13. ábra:** a a **tulajdonságok** párbeszédpanelen módosítsa az IP-cím_

  ![14. ábra: A fürt számára fenntartott IP-cím hozzárendelése][sap-ha-guide-figure-3013]

  _**14. ábra:** a fürt számára fenntartott IP-cím hozzárendelése_

6.  A fürt virtuális állomás neve online állapotba.

  ![15. ábra: A fürt alapvető szolgáltatás működik, a megfelelő IP-címmel][sap-ha-guide-figure-3014]

  _**15. ábra:** a fürt alapvető szolgáltatás működik, a megfelelő IP-címmel_

7.  Adja hozzá a második fürtcsomópontra.

  Most, hogy a core fürtszolgáltatás helyezheti üzembe, a második fürtcsomópontra is hozzáadhat.

  ![16. ábra hozzáadása a második fürtcsomópontra][sap-ha-guide-figure-3015]

  _**16. ábra:** adja hozzá a második fürtcsomópontra_

8.  Adja meg a második fürt csomópont állomás nevét.

  ![17. ábra: Adja meg a második fürt állomásneve][sap-ha-guide-figure-3016]

  _**17. ábra:** adja meg a második fürt állomásneve_

  > [!IMPORTANT]
  > Ügyeljen arra, hogy a **minden megfelelő tároló felvétele a fürtbe** jelölőnégyzet *nem* kiválasztott.  
  >
  >

  ![18. ábra: Nem jelöli be a jelölőnégyzetet][sap-ha-guide-figure-3017]

  _**18. ábra:** tegye *nem* jelölje be a jelölőnégyzetet_

  Kvórum és a lemezek kapcsolatos figyelmeztetést figyelmen kívül hagyhatja. Fog a kvórum beállítása és a lemezt később, megoszthatja a leírtak szerint [telepítse az SIOS DataKeeper Cluster Edition esetében az SAP ASCS/SCS fürtlemez-megosztás][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

  ![19. ábra: A lemez kvórumával kapcsolatos figyelmeztetések mellőzése][sap-ha-guide-figure-3018]

  _**19. ábra:** a lemez kvórumával kapcsolatos figyelmeztetések mellőzése_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> A fürt tanúsító fájlmegosztás beállítása

Ezeket a feladatokat a fürt tanúsító fájlmegosztás konfigurálása foglalja magában:

- Fájlmegosztás létrehozása.
- A fájl megosztási tanúsító Kvórum beállítása a Feladatátvevőfürt-kezelőben.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Fájlmegosztás létrehozása

1.  Válassza ki a tanúsító fájlmegosztás helyett egy kvórumlemez. Az SIOS DataKeeper támogatja ezt a lehetőséget.

  A cikkben szereplő példák a tanúsító fájlmegosztás van az Active Directory vagy a DNS-kiszolgáló, amely az Azure-ban fut-e. Tanúsító fájlmegosztás neve domcontr – 0. Szeretné konfigurálta az Azure (VPN-átjáróval vagy Azure ExpressRoute) VPN-kapcsolat, mert az Active Directory és a DNS szolgáltatás a helyszínen, és nem megfelelő futtatásához egy tanúsító fájlmegosztást.

  > [!NOTE]
  > Ha az Active Directory és a DNS szolgáltatás csak a helyszínen fut, az Active Directory és DNS Windows operációs rendszeren, amelyen fut a helyi ne konfigurálja a tanúsító fájlmegosztás. Előfordulhat, hogy fut az Azure és az Active Directory vagy a helyszíni DNS fürtcsomópontok közötti hálózati késés túl nagy, és a kapcsolódási problémák miatt. Győződjön meg arról, tanúsító fájlmegosztás beállítása egy Azure virtuális gépen, amelyen fut a fürtcsomópont közelében.  
  >
  >

  A kvórum meghajtón legalább 1024 MB szabad terület van szüksége. Azt javasoljuk, hogy a 2048 MB-nyi szabad terület a kvórum.

2.  Adja hozzá a fürtnévobjektum.

  ![20. ábra: Rendelje hozzá a megosztást a fürtnévobjektum engedélyei][sap-ha-guide-figure-3019]

  _**20. ábra:** engedélyei a megosztást a fürtnévobjektum hozzárendelése_

  Győződjön meg arról, hogy az engedélyek tartalmazzák-e a szolgáltató módosítása a fürtnévobjektum (a példánkban pr1 – ascs-vir$) számára a megosztás adataihoz.

3.  Válassza ki a fürtnévobjektum hozzáadása a listához, **Hozzáadás**. Módosítsa a szűrőt, hogy ellenőrizze a számítógép-objektumok 22. ábra szereplő termékektől mellett.

  ![21. ábra: Változás Gyorsítótárazandó objektumtípusok közé tartoznak a számítógépek][sap-ha-guide-figure-3020]

  _**21. ábra:** módosítása **objektumtípusok** közé tartoznak a számítógépek,_

  ![22. ábra: Válassza ki a számítógép négyzet jelölését.][sap-ha-guide-figure-3021]

  _**22. ábra:** válassza ki a **számítógépek** jelölőnégyzetet_

4.  Írja be a fürt nevét, ahogyan a 21. ábra. A bejegyzést már létre van hozva, mert módosíthatja az engedélyeket, a 20. ábrán látható módon.

5.  Válassza ki a **biztonsági** lapján a megosztást, és állítsunk be részletesebb a fürtnévobjektum engedélyeit.

  ![23. ábra: A fájl megosztási kvórum a fürt neve objektum biztonsági attribútumainak beállítása][sap-ha-guide-figure-3022]

  _**23. ábra:** a fájl megosztási kvórum a fürt neve objektum biztonsági attribútumainak beállítása_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> A fájl megosztási tanúsító Kvórum beállítása a Feladatátvevőfürt-kezelő

1.  Nyissa meg a fürtkvórum beállítása varázsló konfigurálja.

  ![24. ábra: A konfigurálás fürtkvórum beállítása varázsló indítása][sap-ha-guide-figure-3023]

  _**24. ábra:** a konfigurálás fürtkvórum beállítása varázsló indítása_

2.  Az a **kvórumbeállítások kijelölése** lapra, jelölje be **a kvórum tanúsítójának kijelölése**.

  ![25. ábra: Kvórumkonfigurációkkal közül választhat][sap-ha-guide-figure-3024]

  _**25. ábra:** kvórumkonfigurációkkal közül választhat_

3.  Az a **kvórum Tanúsítójának kijelölése** lapra, jelölje be **konfigurálja egy tanúsító fájlmegosztást**.

  ![26. ábra: Válassza ki a tanúsító fájlmegosztás][sap-ha-guide-figure-3025]

  _**26. ábra:** tanúsító fájlmegosztás kiválasztása_

4.  Adja meg a fájlmegosztás UNC elérési útját (a példánkban a \\domcontr-0\FSW). A módosításokat végezhet listájának megtekintéséhez válasszon **tovább**.

  ![27. ábra: A tanúsító fájlmegosztás a fájlmegosztás helyét határozza meg.][sap-ha-guide-figure-3026]

  _**27. ábra:** határozza meg a fájlmegosztás helyét a tanúsító fájlmegosztás_

5.  Válassza ki a módosításokat, majd válassza ki **tovább**. Sikeresen konfigurálja újra a fürt konfigurációját a 28. ábrán látható módon kell megadnia:  

  ![28. ábra: Jóváhagyás, hogy a fürt már újra konfigurálni][sap-ha-guide-figure-3027]

  _**28. ábra:** , hogy a fürt már újra konfigurálni megerősítése_

Miután sikeresen telepítette a Windows feladatátvevő fürt, néhány küszöbértékek módosítani, ezért azok alkalmazkodnak a feladatátvétel az észlelési feltételek az Azure-ban szüksége. A módosítandó paraméterei dokumentálva vannak [finomhangolása a feladatátvevő fürt hálózati küszöbértékek][tuning-failover-cluster-network-thresholds]. Feltételezve, hogy a két virtuális gépet alkotó a Windows-fürt konfigurációját a ASCS/SCS ugyanazon az alhálózaton, ezeket az értékeket módosítsa a következő paraméterekkel:

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Ezek a beállítások tesztelt az ügyfelekkel, és egy jó biztonsági sérülés kínálnak. Elég rugalmas, azonban is biztosítanak, amely elég gyors valós hibaállapotok SAP-szoftvereket vagy egy csomópontot vagy Virtuálisgép-hiba a feladatátvételt.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Telepítse az SIOS DataKeeper Cluster Edition esetében az SAP ASCS/SCS fürtlemez-megosztás

Most már rendelkezik egy működő Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban. Az SAP ASCS/SCS példányhoz telepítéséhez, egy megosztott lemez erőforrást kell. Nem hozható létre a szükséges megosztott erőforrásokat az Azure-ban. Az SIOS DataKeeper Cluster Edition rendszer egy külső megoldás, amely a megosztott erőforrások létrehozására használhatja.

Ezek a feladatok telepítése az SIOS DataKeeper Cluster Edition a SAP ASCS/SCS fürtlemez-megosztás foglalja magában:

- Adja hozzá a Microsoft .NET-keretrendszer 3.5-ös verzióját.
- Telepítse az SIOS DataKeeper.
- Állítsa be az SIOS DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Adja hozzá a .NET-keretrendszer 3.5
.NET-keretrendszer 3.5 nem automatikusan aktiválva, vagy a Windows Server 2012 R2 rendszeren telepítve. Mivel az SIOS DataKeeper van szüksége lehet az összes csomóponton, amelyen DataKeeper telepíti a .NET, .NET-keretrendszer 3.5 kell telepítenie a fürt összes virtuális gép vendég operációs rendszeren.

.NET-keretrendszer 3.5 hozzáadandó két módja van:

- A szerepkörök hozzáadása és szolgáltatások varázsló használata a Windows, 29. ábrán látható módon:

  ![29. ábra: Telepítse .NET-keretrendszer 3.5-ös verzióját a adja hozzá szerepkörök és szolgáltatások varázsló használatával][sap-ha-guide-figure-3028]

  _**29. ábra:** telepítse .NET-keretrendszer 3.5-ös verzióját a adja hozzá szerepkörök és szolgáltatások varázsló használatával_

  ![30. ábra: Telepítési folyamatjelző, .NET-keretrendszer 3.5 telepítése a szerepkörök hozzáadása és a szolgáltatások varázsló használatával][sap-ha-guide-figure-3029]

  _**30. ábra:** telepítési folyamatjelző, .NET-keretrendszer 3.5 telepítése a szerepkörök hozzáadása és a szolgáltatások varázsló használatával_

- Használja a dism.exe parancssori eszközt. Az ilyen típusú telepítés kell a a Windows-telepítési adathordozón lévő SxS könyvtár eléréséhez. Egy rendszergazda jogú parancssorba írja be ezt a parancsot:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Telepítse az SIOS DataKeeper

Telepítse az SIOS DataKeeper Cluster Edition a fürt egyes csomópontjaihoz. Az SIOS DataKeeper virtuális megosztott tároló létrehozásához, hozzon létre egy szinkronizált tükrözött, és ezután szimulálja a fürt megosztott tárolójába.

Mielőtt telepíti az SIOS szoftver, hozzon létre a DataKeeperSvc tartományi felhasználó.

> [!NOTE]
> Adja hozzá a DataKeeperSvc tartományi felhasználót a helyi rendszergazdai csoporthoz mindkét fürtcsomóponton.
>
>

Az SIOS DataKeeper telepítése:

1.  Telepítse az SIOS szoftvert mindkét fürtcsomóponton.

  ![Az SIOS telepítő][sap-ha-guide-figure-3030]

  ![31. ábra: Az SIOS DataKeeper telepítési első oldalán][sap-ha-guide-figure-3031]

  _**31. ábra:** az SIOS DataKeeper telepítés első oldal_

2.  A párbeszédpanelen válassza ki a **Igen**.

  ![32. ábra: DataKeeper figyelmeztet, hogy a szolgáltatás le lesz tiltva][sap-ha-guide-figure-3032]

  _**32. ábra:** DataKeeper tájékoztatja, hogy a szolgáltatás le lesz tiltva_

3.  A párbeszédpanelen, azt javasoljuk, hogy bejelölte **tartomány vagy a kiszolgáló fiók**.

  ![33. ábra: Az SIOS DataKeeper felhasználó kiválasztása][sap-ha-guide-figure-3033]

  _**33. ábra:** az SIOS DataKeeper felhasználó kiválasztása_

4.  Adja meg a tartományi fiók felhasználói nevét és jelszavát, amelyet az SIOS DataKeeper létrehozott.

  ![34. ábra: Adja meg a tartomány felhasználónév és jelszó az SIOS DataKeeper telepítéséhez][sap-ha-guide-figure-3034]

  _**34. ábra:** az SIOS DataKeeper telepítését adja meg a tartomány felhasználónév és jelszó_

5.  Telepítse az SIOS DataKeeper példány a licenckulcs, 35. ábrán látható módon.

  ![35. ábra: Adja meg az SIOS DataKeeper licenckulcs][sap-ha-guide-figure-3035]

  _**35. ábra:** adja meg az SIOS DataKeeper licenckulcs_

6.  Amikor a rendszer kéri, indítsa újra a virtuális gépet.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Az SIOS DataKeeper beállítása

Miután telepítette az SIOS DataKeeper mindkét csomóponton, indítsa el a konfiguráció. Az a konfiguráció célja, hogy rendelkezik a további lemezek vannak csatolva a virtuális gépek mindegyike közötti szinkron adatreplikációt.

1.  Indítsa el a DataKeeper felügyeleti és a konfigurációs eszközt, és válassza ki **Kapcsolódás kiszolgálóhoz**.

  ![36. ábra: Az SIOS DataKeeper felügyeleti és a konfigurációs eszköz][sap-ha-guide-figure-3036]

  _**36. ábra:** az SIOS DataKeeper felügyeleti és a konfigurációs eszköz_

2.  Adja meg a nevét vagy a felügyeleti és a konfigurációs eszközt, és a egy második lépésben, a második csomópont csatlakozni az első fürtcsomópont TCP/IP-címét.

  ![37. ábra: Helyezze be a nevét, vagy az első felügyeleti csomópont TCP/IP-címét és a konfigurációs eszközt, és a egy második lépésben, a második csomópont csatlakoznia][sap-ha-guide-figure-3037]

  _**37. ábra:** helyezze be a nevét vagy a TCP/IP-címét a felügyeleti és a konfigurációs eszközt, és a egy második lépésben, a második csomópont csatlakozni az első fürtcsomópont_

3.  Hozzon létre a a két csomópont közötti replikálás feladatot.

  ![38. ábra: Replikáció feladat létrehozása][sap-ha-guide-figure-3038]

  _**38. ábra:** replikációs feladat létrehozása_

  A varázsló végigvezeti egy replikációs feladat létrehozásának folyamatán.

4.  Határozza meg a replikációs feladat nevét.

  ![39. ábra: Határozza meg a replikációs feladat nevét][sap-ha-guide-figure-3039]

  _**39. ábra:** határozza meg a replikációs feladat nevét_

  ![40. ábra: Az alapszintű adatok kell lennie az aktuális forráscsomópont csomópont meghatározása][sap-ha-guide-figure-3040]

  _**40. ábra:** a csomópont, amely lehet a jelenlegi forráscsomópont alapadatok megadása_

5.  Adja meg a nevét, a TCP/IP-cím és a cél uzlu lemezkötetet.

  ![41. ábra: A neve, TCP/IP-cím és az aktuális cél csomópont lemezkötetet megadása][sap-ha-guide-figure-3041]

  _**41. ábra:** neve, TCP/IP-címet, és az aktuális cél csomópont lemezkötetet meghatározása_

6.  A tömörítési algoritmust határozza meg. Ebben a példában azt javasoljuk, hogy a tömörítés a replikációs adatfolyam. Különösen abban az esetben az újraszinkronizálás a tömörítés a replikációs Stream jelentősen csökkenti az újraszinkronizálás idő. A tömörítés a virtuális gép Processzor- és Memóriakövetelményeknek erőforrásokat használja. A tömörítési aránya nő, ahogy nő a Processzor által használt erőforrások mennyisége. Ezt a beállítást később módosíthatja.

7.  Egy másik beállítást kell ellenőrizni az e a replikáció aszinkron vagy szinkron módon történik-e. Ha az SAP ASCS/SCS-konfigurációk, a szinkron replikáció kell használnia.  

  ![42. ábra: Replikáció adatainak megadása][sap-ha-guide-figure-3042]

  _**42. ábra:** replikálás részletei_

8.  Adja meg e kell-e a kötetet, amelyet a rendszer replikálja a replikálási feladat által jelölt a Windows Server feladatátvevő fürt konfigurációjába megosztott lemez. Válassza ki az SAP ASCS/SCS konfiguráció **Igen** úgy, hogy a Windows-fürt látja a replikált kötet megosztott fürtkötet, ezáltal az lemez.

  ![43. ábra: A replikált kötetet állítja be a fürt kötet az Igen lehetőséget.][sap-ha-guide-figure-3043]

  _**43. ábra:** kiválasztása **Igen** a replikált kötet beállítása a fürt kötetként_

  A kötet létrehozása után a DataKeeper felügyeleti és a konfigurációs eszköz azt mutatja, hogy a replikációs feladat aktív.

  ![44. ábra: DataKeeper szinkron tükrözés az SAP ASCS/SCS-megosztás lemez jelenleg aktív][sap-ha-guide-figure-3044]

  _**44. ábra:** DataKeeper szinkron tükrözés az SAP ASCS/SCS a lemez megosztása a jelenleg aktív_

  Feladatátvevőfürt-kezelő most már látható DataKeeper lemezként, a lemez, 45. ábrán látható módon:

  ![45. ábra: A Feladatátvevőfürt-kezelővel a lemez DataKeeper replikált jeleníti meg][sap-ha-guide-figure-3045]

  _**45. ábra:** Feladatátvevőfürt-kezelőben jeleníti meg a lemez a replikált DataKeeper_

## <a name="next-steps"></a>További lépések

* [Telepítse az SAP NetWeaver magas rendelkezésre ÁLLÁS Windows feladatátvevő fürt és megosztott lemez használatával egy SAP ASCS/SCS példányhoz][sap-high-availability-installation-wsfc-shared-disk]
