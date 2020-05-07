---
title: Azure-infrastruktúra SAP ASCS/SCS és WSFC&megosztott lemezrel | Microsoft Docs
description: Ismerje meg, hogyan készítheti elő az Azure-infrastruktúrát az SAP-hez a Windows feladatátvevő fürt és az SAP ASCS/SCS-példány megosztott lemezének használatával.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f5e0eda72f39a70f02b596a8fd69728336eac333
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594814"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Az Azure-infrastruktúra előkészítése az SAP-hez a Windows feladatátvevő fürt és az SAP ASCS/SCS közös lemezének használatával

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID magas rendelkezésre állási konfiguráció)

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

Ez a cikk azokat a lépéseket ismerteti, amelyekkel előkészítheti az Azure-infrastruktúrát a magas rendelkezésre állású SAP-rendszer telepítésére és konfigurálására egy Windows feladatátvevő fürtön egy, a *fürt megosztott lemezének* használatával, egy SAP ASCS-példány fürtözésére szolgáló lehetőséggel.

## <a name="prerequisites"></a>Előfeltételek

A telepítés megkezdése előtt tekintse át a következő cikket:

* [Architektúra-útmutató: SAP ASCS/SCS-példány fürtözése Windows feladatátvevő fürtön fürt megosztott lemezének használatával][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Az infrastruktúra előkészítése az építészeti sablonhoz 1
Az SAP-hez készült Azure Resource Manager-sablonok megkönnyítik a szükséges erőforrások üzembe helyezését.

A Azure Resource Manager háromrétegű sablonjai a magas rendelkezésre állású forgatókönyveket is támogatják. Például az 1. építészeti sablon két fürttel rendelkezik. Mindegyik fürt egy SAP-meghibásodási pont az SAP ASCS/SCS és az adatbázis-kezelő számára.

Itt bemutathatja Azure Resource Manager sablonokat a jelen cikkben ismertetett példához:

* [Azure Marketplace-rendszerkép](https://github.com/Azure/azure-quickstart-templates/)  
* [Azure Marketplace-rendszerkép az Azure Managed Disks használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/)
* [Egyéni rendszerkép Managed Disks használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Az infrastruktúra előkészítése az 1. építészeti sablonhoz:

- A Azure Portal a **Paraméterek** ablaktábla **SYSTEMAVAILABILITY** mezőjében válassza a **Ha**lehetőséget.

  ![1. ábra: az SAP magas rendelkezésre állású Azure Resource Manager paramétereinek beállítása][sap-ha-guide-figure-3000]

_**1. ábra:** SAP magas rendelkezésre állású Azure Resource Manager paramétereinek beállítása_


  A sablonok létrehozása:

  * **Virtuális gépek**:
    * SAP Application Server Virtual Machines \<:\>SAPSystemSID-di\<-Number\>
    * ASCS/SCS-fürt virtuális gépei \<:\>SAPSystemSID-ASCS\<-Number\>
    * Adatbázis-kezelő \<fürt\>: SAPSystemSID-\<db-Number\>

  * **Hálózati kártyák az összes virtuális géphez, társított IP-címekkel**:
    * \<SAPSystemSID\>-NIC-di-\<Number\>
    * \<SAPSystemSID\>-NIC-ASCs-\<Number\>
    * \<SAPSystemSID\>-NIC-db-\<Number\>

  * **Azure Storage-fiókok (csak nem felügyelt lemezek esetén)**:

  * **Rendelkezésre állási csoportok** a következőhöz:
    * SAP Application Server virtuális gépek: \<SAPSystemSID\>-avset-di
    * SAP ASCS/SCS-fürt virtuális gépei \<:\>SAPSystemSID-avset-ASCS
    * Adatbázis-kezelő fürt virtuális \<gépei\>: SAPSystemSID-avset-db

  * **Azure belső terheléselosztó**:
    * A ASCS/SCS-példány összes portja és az IP \<-\>cím SAPSystemSID-LB-ASCS
    * Az SQL Server adatbázis-kezelő és az IP- \<SAPSystemSID\>-LB-db összes portjával

  * **Hálózati biztonsági csoport**: \<SAPSystemSID\>-NSG-ASCs-0  
    * Nyitott külső RDP protokoll (RDP) porttal a \<SAPSystemSID\>-ASCs-0 virtuális géphez

> [!NOTE]
> Alapértelmezés szerint a hálózati kártyák és az Azure belső terheléselosztó összes IP-címe dinamikus. Módosítsa statikus IP-címekre. Ezt a cikket a cikk későbbi részében ismertetjük.
>
>

## <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Virtuális gépek üzembe helyezése vállalati hálózati kapcsolattal (telephelyek közötti) az éles környezetben való használatra
Az éles környezetben futó SAP-rendszerek esetében az Azure VPN Gateway vagy az Azure ExpressRoute használatával helyezzen üzembe vállalati hálózati kapcsolattal rendelkező Azure-beli virtuális gépeket.

> [!NOTE]
> Használhatja az Azure Virtual Network-példányát. A virtuális hálózat és az alhálózat már létrehozva és előkészített állapotban van.
>
>

1. A Azure Portal a **Paraméterek** ablaktábla **NEWOREXISTINGSUBNET** mezőjében válassza a **meglévő**lehetőséget.
2. **A befoglalt mezőben adja** meg az előkészített Azure hálózati ALHÁLÓZAT-azonosító teljes karakterláncát, ahol az Azure-beli virtuális gépek üzembe helyezését tervezi.
3. Az összes Azure-beli hálózati alhálózat listájának lekéréséhez futtassa a következő PowerShell-parancsot:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Az **azonosító** mező az alhálózat azonosítójának értékét jeleníti meg.
4. Az összes alhálózati azonosító érték listájának lekéréséhez futtassa a következő PowerShell-parancsot:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   Az alhálózati azonosító a következőképpen néz ki:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Csak felhőalapú SAP-példányok üzembe helyezése teszteléshez és bemutatóhoz
A magas rendelkezésre állású SAP-rendszer üzembe helyezése csak felhőalapú üzemi modellben végezhető el. Az ilyen típusú központi telepítés elsősorban bemutató és tesztelési célú esetekben hasznos. Éles használati esetekhez nem alkalmas.

- A Azure Portal a **Paraméterek** ablaktábla **NEWOREXISTINGSUBNET** mezőjében válassza az **új**lehetőséget. Hagyja üresen a **deneti** mezőt.

  Az SAP Azure Resource Manager sablon automatikusan létrehozza az Azure-beli virtuális hálózatot és alhálózatot.

> [!NOTE]
> A Active Directory és a DNS szolgáltatáshoz is telepítenie kell legalább egy dedikált virtuális gépet ugyanabban az Azure Virtual Network-példányban. A sablon nem hozza létre ezeket a virtuális gépeket.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Az infrastruktúra előkészítése a 2. építészeti sablonhoz

Ezt a Azure Resource Manager-sablont használhatja az SAP-hez, hogy egyszerűbbé váljon a szükséges infrastruktúra-erőforrások üzembe helyezése az SAP architektúra 2. sablonjában.

A következő helyen kaphat Azure Resource Manager sablonokat ehhez a telepítési forgatókönyvhöz:

* [Azure Marketplace-rendszerkép](https://github.com/Azure/azure-quickstart-templates/)  
* [Azure Marketplace-rendszerkép Managed Disks használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/)
* [Egyéni rendszerkép Managed Disks használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Az infrastruktúra előkészítése a 3. építészeti sablonhoz

Előkészítheti az infrastruktúrát, és konfigurálhatja az SAP-t a többszörös SID-hez. Hozzáadhat például egy további SAP ASCS/SCS-példányt egy *meglévő* fürtkonfiguráció-konfigurációhoz. További információ: [további SAP ASCS/SCS-példány konfigurálása meglévő fürtkonfiguráció esetén a Azure Resource Manager SAP multi-SID konfigurációjának létrehozásához][sap-ha-multi-sid-guide].

Ha új, több SID-alapú fürtöt szeretne létrehozni, használhatja a több SID-alapú [Gyorsindítás-sablonokat a githubon](https://github.com/Azure/azure-quickstart-templates).

Új több SID-fürt létrehozásához a következő három sablont kell telepítenie:

* [ASCS/SCS-sablon](#ASCS-SCS-template)
* [Adatbázis-sablon](#database-template)
* [Application Server-sablon](#application-servers-template)

A következő szakaszokban további részleteket talál a sablonokban megadható sablonokról és paraméterekről.

### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>ASCS/SCS-sablon

A ASCS/SCS-sablon két virtuális gépet telepít, amelyek segítségével több ASCS/SCS-példányt üzemeltető Windows Server feladatátvevő fürt hozható létre.

A ASCS/SCS multi-SID sablon beállításához a [ASCS/SCS multi-SID sablonban][sap-templates-3-tier-multisid-xscs-marketplace-image] vagy a [ASCS/SCS multi-sid sablonban Managed Disks használatával][sap-templates-3-tier-multisid-xscs-marketplace-image-md]adja meg a következő paraméterek értékeit:

- **Erőforrás-előtag**: állítsa be az erőforrás-előtagot, amely az üzembe helyezés során létrehozott összes erőforrás előállítására szolgál. Mivel az erőforrások nem csak egy SAP-rendszerhez tartoznak, az erőforrás előtagja nem egy SAP-rendszer SID-azonosítója.  Az előtagnak három és hat karakter közöttinek kell lennie.
- **Verem típusa**: válassza ki az SAP-szolgáltatás halmozási típusát. A verem típusától függően Azure Load Balancer rendelkezik egy (ABAP vagy Java only) vagy két (ABAP + Java) magánhálózati IP-címmel SAP-rendszeren.
- **Operációs rendszer típusa**: válassza ki a virtuális gépek operációs rendszerét.
- **SAP-rendszerszám**: válassza ki a fürtben TELEPÍTENI kívánt SAP-rendszerek számát.
- **Rendszerszintű rendelkezésre állás**: válassza a **Ha**lehetőséget.
- **Rendszergazdai Felhasználónév és rendszergazdai jelszó**: hozzon létre egy új felhasználót, amely használható a gépre való bejelentkezéshez.
- **Új vagy meglévő alhálózat**: beállíthatja, hogy új virtuális hálózatot és alhálózatot hozzon létre, vagy meglévő alhálózatot használjon. Ha már van olyan virtuális hálózata, amely a helyszíni hálózathoz csatlakozik, válassza a **meglévő**lehetőséget.
- **Alhálózati azonosító**: Ha a virtuális gépet egy olyan meglévő VNet szeretné telepíteni, amelyben egy alhálózat van megadva, a virtuális gépet hozzá kell rendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában a következőképpen néz ki:

  /Subscriptions/\<előfizetés-\>azonosító\</resourceGroups/-erőforráscsoport\>neve\</Providers/Microsoft.Network/virtualNetworks/virtuális hálózat\>neve\</Subnets/alhálózat neve\>

A sablon egy Azure Load Balancer példányt telepít, amely több SAP-rendszert is támogat:

- A ASCS-példányok a 00, 10, 20 példányra vannak konfigurálva...
- Az SCS-példányok a következő példányhoz vannak konfigurálva: 01, 11, 21...
- A ASCS sorba helyezni replikációs kiszolgáló (ERS) (csak Linux) példányok a 02, 12, 22... számú példányra vannak konfigurálva...
- Az SCS ERS (csak Linux) példányok konfigurálva vannak a következő példányhoz: 03, 13, 23...

A terheléselosztó 1 virtuális IP-címet (2 Linux-t), 1x VIP-t tartalmaz a ASCS/SCS-hez, és 1x VIP for ERS (csak Linux).

#### <a name="sap-ascsscs-ports"></a><a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>SAP ASCS/SCS-portok
Az alábbi lista tartalmazza az összes terheléselosztási szabályt (ahol az x az SAP-rendszer száma, például 1, 2, 3...):
- Windows-specifikus portok minden SAP-rendszerhez: 445, 5985
- ASCS-portok (példányok száma X0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-portok (X1-példány száma): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS-portok Linuxon (példány száma x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS-portok Linux rendszeren (3. példány száma): 33x3, 5x313, 5x314, 5x316

A terheléselosztó a következő mintavételi portok használatára van konfigurálva (ahol az x az SAP-rendszer száma, például 1, 2, 3...):
- ASCS/SCS belső terheléselosztó mintavételi portja: 620x0
- ERS belső terheléselosztó mintavételi portja (csak Linux): 621x2

### <a name="database-template"></a><a name="database-template"></a>Adatbázis-sablon

Az adatbázis-sablon egy vagy két virtuális gépet telepít, amelyekkel egy SAP-rendszerhez a (z) RDBMS-kezelő rendszer telepíthető. Ha például öt SAP-rendszerhez telepít egy ASCS/SCS-sablont, akkor öt alkalommal kell telepítenie ezt a sablont.

Az adatbázis multi-SID sablonjának beállításához a Managed Disks használatával adja meg az adatbázis [multi-SID sablonjában][sap-templates-3-tier-multisid-db-marketplace-image] vagy az [adatbázis több SID-sablonjában][sap-templates-3-tier-multisid-db-marketplace-image-md]a következő paraméterek értékeit:

- **SAP rendszer azonosítója**: adja meg a TELEPÍTENI kívánt SAP-rendszer azonosítóját. A rendszer az azonosítót használja az üzembe helyezett erőforrások előtagjaként.
- **Operációs rendszer típusa**: válassza ki a virtuális gépek operációs rendszerét.
- **DbType**: válassza ki a fürtön telepíteni kívánt adatbázis típusát. Válassza az **SQL** lehetőséget, ha Microsoft SQL Server szeretné telepíteni. Válassza a **HANA** lehetőséget, ha SAP HANA telepítését tervezi a virtuális gépeken. Győződjön meg arról, hogy az operációs rendszer megfelelő típusát választotta. Válassza a **Windows** for SQL lehetőséget, és válasszon egy Linux-disztribúciót a HANA-hoz. A virtuális gépekhez csatlakozó Azure Load Balancer úgy van konfigurálva, hogy támogassa a kiválasztott adatbázis típusát:
  * **SQL**: a terheléselosztó terheléselosztási 1433-as portja. Ügyeljen arra, hogy ezt a portot használja a SQL Server AlwaysOn telepítéséhez.
  * **HANA**: a terheléselosztó terheléselosztási 35015 és 35017. Győződjön meg arról, hogy a **50**-as számú példánnyal telepíti a SAP HANA.
  A terheléselosztó a 62550-es mintavételi portot használja.
- **SAP-rendszerméret**: állítsa be az új rendszeren elérhető SAP-t. Ha nem biztos benne, hogy a rendszer hány SAP-t igényel, kérdezze meg az SAP-technológiai partnerét vagy rendszerintegrátorát.
- **Rendszerszintű rendelkezésre állás**: válassza a **Ha**lehetőséget.
- **Rendszergazdai Felhasználónév és rendszergazdai jelszó**: hozzon létre egy új felhasználót, amely használható a gépre való bejelentkezéshez.
- **Alhálózat azonosítója**: adja meg annak az alhálózatnak az azonosítóját, amelyet a ASCS/SCS sablon telepítése során használt, vagy annak az alhálózatnak az azonosítója, amelyet a ASCS/SCS-sablon központi telepítésének részeként hozott létre.

### <a name="application-servers-template"></a><a name="application-servers-template"></a>Application Server-sablon

Az alkalmazáskiszolgáló sablon két vagy több virtuális gépet telepít, amelyek SAP Application Server-példányként használhatók egy SAP-rendszerhez. Ha például öt SAP-rendszerhez telepít egy ASCS/SCS-sablont, akkor öt alkalommal kell telepítenie ezt a sablont.

Az alkalmazáskiszolgáló több SID-sablonjának beállításához a Managed Disks használatával adja meg az Application [Servers multi-SID sablon][sap-templates-3-tier-multisid-apps-marketplace-image] vagy [az alkalmazáskiszolgáló több SID-sablonját][sap-templates-3-tier-multisid-apps-marketplace-image-md], írja be a következő paraméterek értékeit:

  -  **SAP rendszer azonosítója**: adja meg a TELEPÍTENI kívánt SAP-rendszer azonosítóját. A rendszer az azonosítót használja az üzembe helyezett erőforrások előtagjaként.
  -  **Operációs rendszer típusa**: válassza ki a virtuális gépek operációs rendszerét.
  -  **SAP-rendszerméret**: az új rendszeren elérhető nedvek száma. Ha nem biztos benne, hogy a rendszer hány SAP-t igényel, kérdezze meg az SAP-technológiai partnerét vagy rendszerintegrátorát.
  -  **Rendszerszintű rendelkezésre állás**: válassza a **Ha**lehetőséget.
  -  **Rendszergazdai Felhasználónév és rendszergazdai jelszó**: hozzon létre egy új felhasználót, amely használható a gépre való bejelentkezéshez.
  -  **Alhálózat azonosítója**: adja meg annak az alhálózatnak az azonosítóját, amelyet a ASCS/SCS sablon telepítése során használt, vagy annak az alhálózatnak az azonosítója, amelyet a ASCS/SCS-sablon központi telepítésének részeként hozott létre.


## <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Azure-Virtual Network
A példánkban az Azure Virtual Network példány 10.0.0.0/16. Az alhálózat nevű alhálózat egy 10.0.0.0/24 címtartomány. Az összes virtuális gép és belső terheléselosztó üzembe helyezése ebben a virtuális hálózaton történik.

> [!IMPORTANT]
> Ne módosítsa a hálózati beállításokat a vendég operációs rendszeren belül. Ide tartoznak az IP-címek, a DNS-kiszolgálók és az alhálózatok. Konfigurálja az összes hálózati beállítást az Azure-ban. A Dynamic Host Configuration Protocol (DHCP) szolgáltatás propagálja a beállításokat.
>
>

## <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS IP-címek

A szükséges DNS IP-címek megadásához hajtsa végre a következő lépéseket:

1. A Azure Portal **DNS-kiszolgálók** ablaktáblájában győződjön meg arról, hogy a virtuális hálózati **DNS-kiszolgálók** beállítás **Egyéni DNS-** re van beállítva.
2. Válassza ki a beállításokat a hálózat típusa alapján. További információkért lásd a következőket:
   * Adja hozzá a helyszíni DNS-kiszolgálók IP-címeit.  
   A helyszíni DNS-kiszolgálókat az Azure-ban futó virtuális gépekre is kiterjesztheti. Ebben az esetben felveheti azon Azure-beli virtuális gépek IP-címeit, amelyeken a DNS szolgáltatást futtatja.
   * Az Azure-ban elkülönített virtuálisgép-példányok esetén: helyezzen üzembe egy további virtuális gépet ugyanabban a Virtual Network-példányban, amely DNS-kiszolgálóként szolgál. Adja hozzá a DNS-szolgáltatás futtatásához beállított Azure-beli virtuális gépek IP-címeit.

   ![2. ábra: az Azure-hoz készült DNS-kiszolgálók konfigurálása Virtual Network][sap-ha-guide-figure-3001]

   _**2. ábra:** DNS-kiszolgálók konfigurálása az Azure Virtual Network_

   > [!NOTE]
   > Ha megváltoztatja a DNS-kiszolgálók IP-címeit, újra kell indítania az Azure-beli virtuális gépeket a módosítás alkalmazásához és az új DNS-kiszolgálók propagálásához.
   >
   >

A példánkban a DNS szolgáltatás telepítve van és konfigurálva van ezeken a Windows-alapú virtuális gépeken:

| Virtuális gépi szerepkör | Virtuális gép gazdagépének neve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első DNS-kiszolgáló |domcontr – 0 |PR1-NIC-domcontr-0 |10.0.0.10 |
| Második DNS-kiszolgáló |domcontr – 1 |PR1-NIC-domcontr-1 |10.0.0.11 |

## <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Az SAP ASCS/SCS fürtözött példány és az adatbázis-kezelő fürtözött példány állomásneve és statikus IP-címei

Helyszíni központi telepítés esetén ezekre a fenntartott állomásnévekre és IP-címekre van szükség:

| Virtuális gazdagép neve szerepkör | Virtuális gazdagép neve | Virtuális statikus IP-cím |
| --- | --- | --- |
| SAP ASCS/SCS első fürt virtuális gazdagépének neve (fürtözési felügyelethez) |PR1-ASCs-VIR |10.0.0.42 |
| SAP-ASCS/SCS-példány virtuális gazdagépének neve |PR1-ASCs-SAP |10.0.0.43 |
| SAP adatbázis-kezelő második fürt virtuális gazdagépének neve (fürtszolgáltatás) |PR1 – adatbázis-kezelő – VIR |10.0.0.32 |

A fürt létrehozásakor hozza létre a PR1-ASCs-VIR és a PR1-adatbázis-VIR nevű virtuális gazdagép nevét, valamint a fürtöt kezelő társított IP-címeket. Ennek módjával kapcsolatos további információkért lásd: fürtcsomópontok [gyűjtése a fürt konfigurációjában][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

A DNS-kiszolgálón manuálisan is létrehozhatja a másik két virtuális gazdagép nevét, a PR1-ASCs-SAP és a PR1-adatbázis-SAP protokollt, valamint a hozzájuk tartozó IP-címeket. A fürtözött SAP ASCS/SCS-példány és a fürtözött adatbázis-kezelő példány ezeket az erőforrásokat használja. Ennek módjáról a következő témakörben talál további információt: [virtuális állomásnév létrehozása FÜRTÖZÖTT SAP ASCS/SCS-példányhoz][sap-ha-guide-9.1.1].

## <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Statikus IP-címek beállítása az SAP-beli virtuális gépekhez
Miután telepítette a virtuális gépeket a fürtben való használatra, statikus IP-címeket kell megadnia az összes virtuális géphez. Ezt megteheti az Azure Virtual Network konfigurációjában, nem pedig a vendég operációs rendszeren.

1. A Azure Portal válassza az **erőforráscsoport** > **hálózati kártya** > **beállításai** > **IP-cím**elemet.
2. Az **IP-címek** ablaktábla **hozzárendelés**területén válassza a **statikus**lehetőséget. Az **IP-cím** mezőbe írja be a használni kívánt IP-címet.

   > [!NOTE]
   > Ha módosítja a hálózati kártya IP-címét, a módosítás alkalmazásához újra kell indítania az Azure-beli virtuális gépeket.  
   >
   >

   ![3. ábra: az egyes virtuális gépek hálózati kártyájának statikus IP-címeinek beállítása][sap-ha-guide-figure-3002]

   _**3. ábra:** Statikus IP-címek beállítása az egyes virtuális gépek hálózati kártyája számára_

   Ismételje meg ezt a lépést minden hálózati adapter esetében, azaz minden virtuális gép esetében, beleértve a Active Directory vagy a DNS szolgáltatáshoz használni kívánt virtuális gépeket is.

A példánkban a következő virtuális gépek és statikus IP-címek vannak:

| Virtuális gépi szerepkör | Virtuális gép gazdagépének neve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első SAP Application Server-példány |PR1-di-0 |PR1-NIC-di-0 |10.0.0.50 |
| Második SAP Application Server-példány |PR1-di-1 |PR1-NIC-di-1 |10.0.0.51 |
| ... |... |... |... |
| Utolsó SAP Application Server-példány |PR1-di-5 |PR1-NIC-di-5 |10.0.0.55 |
| Első fürtcsomópont a ASCS/SCS-példányhoz |PR1-ASCs-0 |PR1-NIC-ASCs-0 |10.0.0.40 |
| Második fürtcsomópont a ASCS/SCS-példányhoz |PR1-ASCs-1 |PR1-NIC-ASCs-1 |10.0.0.41 |
| Első fürtcsomópont az adatbázis-kezelő példányhoz |PR1-db-0 |PR1-NIC-db-0 |10.0.0.30 |
| Második fürtcsomópont az adatbázis-kezelő példányhoz |PR1-db-1 |PR1-NIC-db-1 |10.0.0.31 |

## <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Statikus IP-cím beállítása az Azure belső terheléselosztó számára

Az SAP Azure Resource Manager sablon egy belső Azure Load balancert hoz létre, amely az SAP ASCS/SCS instance-fürthöz és az adatbázis-kezelő fürthöz használható.

> [!IMPORTANT]
> Az SAP ASCS/SCS virtuális állomásneve IP-címe megegyezik az SAP ASCS/SCS belső terheléselosztó IP-címével: PR1-LB-ASCS.
> Az adatbázis-kezelőrendszer virtuális nevének IP-címe megegyezik az adatbázis-kezelő belső terheléselosztó IP-címével: PR1-LB-adatbázis-kezelő.
>
>

Statikus IP-cím beállítása az Azure belső terheléselosztó számára:

1. A kezdeti telepítés a belső terheléselosztó IP-címét a **dinamikus**értékre állítja. A Azure Portal **IP-címek** ablaktáblájának **hozzárendelés**területén válassza a **statikus**lehetőséget.
2. Állítsa be a belső terheléselosztó **PR1-LB-ASCS** IP-címét az SAP ASCS/SCS-példány virtuális állomásneve IP-címére.
3. Állítsa be a belső terheléselosztó **PR1-LB-adatbázis IP-** címét az adatbázis-kezelő példány virtuális állomásneve IP-címére.

   ![4. ábra: statikus IP-címek beállítása a belső terheléselosztó számára az SAP ASCS/SCS-példányhoz][sap-ha-guide-figure-3003]

   _**4. ábra:** Statikus IP-címek beállítása a belső terheléselosztó számára az SAP ASCS/SCS-példányhoz_

A példánkban két Azure belső terheléselosztó van, amelyek rendelkeznek ezekkel a statikus IP-címekkel:

| Azure belső terheléselosztó szerepkör | Azure belső terheléselosztó neve | Statikus IP-cím |
| --- | --- | --- |
| SAP ASCS/SCS-példány belső terheléselosztó |PR1-LB – ASCs |10.0.0.43 |
| SAP adatbázis-kezelő belső terheléselosztó |PR1-LB – adatbázis-kezelő |10.0.0.33 |


## <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Alapértelmezett ASCS/SCS terheléselosztási szabályok az Azure belső terheléselosztó számára

Az SAP Azure Resource Manager sablon létrehozza a szükséges portokat:
* ABAP ASCS-példány, az alapértelmezett 00-as számú példánnyal
* Egy Java SCS-példány, amely az alapértelmezett 1. számú példánnyal rendelkezik

Az SAP-ASCS/SCS-példány telepítésekor a következő alapértelmezett példányt kell használnia a ABAP ASCS-példányához, valamint a Java SCS-példányhoz tartozó alapértelmezett 01-es számú példányt.

Ezután hozza létre a szükséges belső terheléselosztási végpontokat az SAP NetWeaver-portok számára.

A szükséges belső terheléselosztási végpontok létrehozásához először hozza létre ezeket a terheléselosztási végpontokat az SAP NetWeaver ABAP ASCS-portok számára:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Beton portok (a 00-as példánnyal rendelkező ASCS-példány esetében) (10-ESEK) |
| --- | --- | --- |
| Sorba helyezni-kiszolgáló/ *lbrule3200* |32\<példányszám\> |3200 |
| ABAP üzenetkezelő kiszolgáló/ *lbrule3600* |36\<példányszám\> |3600 |
| Belső ABAP üzenet/ *lbrule3900* |39\<példányszám\> |3900 |
| Message Server HTTP/ *Lbrule8100* |81\<példányszám\> |8100 |
| SAP Start Service ASCS HTTP/ *Lbrule50013* |5\<példányszám\>13 |50013 |
| SAP Start Service ASCS HTTPS/ *Lbrule50014* |5\<példányszám\>14 |50014 |
| Sorba helyezni-replikáció/ *Lbrule50016* |5\<példányszám\>16 |50016 |
| SAP Start Service ERS HTTP- *Lbrule51013* |5\<példányszám\>13 |51013 |
| SAP Start Service ERS HTTP- *Lbrule51014* |5\<példányszám\>14 |51014 |
| Rendszerfelügyeleti webszolgáltatások (WinRM) *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

**1. táblázat:** Az SAP NetWeaver ABAP ASCS-példányok portszámai

Ezután hozza létre ezeket a terheléselosztási végpontokat az SAP NetWeaver Java SCS-portok számára:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Beton portok (a 01-es példánnyal rendelkező SCS-példány esetében) (11-ESEK) |
| --- | --- | --- |
| Sorba helyezni-kiszolgáló/ *lbrule3201* |32\<példányszám\> |3201 |
| Átjárókiszolgáló/ *lbrule3301* |33\<példányszám\> |3301 |
| Java-üzenet kiszolgálója/ *lbrule3900* |39\<példányszám\> |3901 |
| Message Server HTTP/ *Lbrule8101* |81\<példányszám\> |8101 |
| SAP Start Service SCS HTTP/ *Lbrule50113* |5\<példányszám\>13 |50113 |
| SAP Start Service SCS HTTPS/ *Lbrule50114* |5\<példányszám\>14 |50114 |
| Sorba helyezni-replikáció/ *Lbrule50116* |5\<példányszám\>16 |50116 |
| SAP Start Service ERS HTTP- *Lbrule51113* |5\<példányszám\>13 |51113 |
| SAP Start Service ERS HTTP- *Lbrule51114* |5\<példányszám\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

**2. táblázat:** Az SAP NetWeaver Java SCS-példányok portszámai

![5. ábra: alapértelmezett ASCS/SCS terheléselosztási szabályok az Azure belső terheléselosztó számára][sap-ha-guide-figure-3004]

_**5. ábra:** Alapértelmezett ASCS/SCS terheléselosztási szabályok az Azure belső terheléselosztó számára_

Állítsa be a terheléselosztó PR1-LB-adatbázis IP-címét az adatbázis-kezelő példány virtuális állomásneve IP-címére.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Az Azure belső terheléselosztó ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása

Ha az SAP-ASCS vagy az SCS-példányokhoz eltérő számokat szeretne használni, akkor a portok nevét és értékeit az alapértelmezett értékekkel kell módosítania.

1. A Azure Portal válassza a ** \<SID\>-LB-ASCs terheléselosztó** > terheléselosztási**szabályok**elemet.
2. Az SAP-ASCS vagy az SCS-példányhoz tartozó összes terheléselosztási szabály esetében módosítsa a következő értékeket:

   * Name
   * Port
   * Háttérbeli port

   Ha például meg szeretné változtatni az alapértelmezett ASCS-példány számát 00 és 31 között, akkor az 1. táblázatban felsorolt összes porton módosítania kell a módosításokat.

   Az alábbi példa egy frissítést mutat be a port *lbrule3200*.

   ![6. ábra: a ASCS/SCS alapértelmezett terheléselosztási szabályok módosítása az Azure belső terheléselosztó számára][sap-ha-guide-figure-3005]

   _**6. ábra:** Az Azure belső terheléselosztó ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása_

## <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Windows rendszerű virtuális gépek hozzáadása a tartományhoz

Miután hozzárendelt egy statikus IP-címet a virtuális gépekhez, adja hozzá a virtuális gépeket a tartományhoz.

![7. ábra: virtuális gép hozzáadása tartományhoz][sap-ha-guide-figure-3006]

_**7. ábra:** Virtuális gép hozzáadása tartományhoz_

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Beállításjegyzék-bejegyzések hozzáadása az SAP ASCS/SCS-példányhoz tartozó fürtcsomópontok esetében

A Azure Load Balancer belső terheléselosztó zárja be a kapcsolatokat, ha a kapcsolatok meghatározott időtartamon belül üresjáratban vannak (Üresjárati időkorlát). A párbeszédpanelen lévő SAP-munkafolyamatok az első sorba helyezni/deüzenetsor-kérelem elküldését követően megnyitják az SAP sorba helyezni folyamathoz való kapcsolódást. Ezek a kapcsolatok általában mindaddig megmaradnak, amíg a munkahelyi folyamat vagy a sorba helyezni folyamat újraindul. Ha azonban a kapcsolat üresjáratban van egy meghatározott időtartamon belül, az Azure belső terheléselosztó kizárja a kapcsolatokat. Ez nem jelent problémát, mert az SAP munkafolyamata újra létrehozza a kapcsolatot a sorba helyezni folyamattal, ha már nem létezik. Ezek a tevékenységek dokumentálva vannak az SAP-folyamatok fejlesztői nyomkövetésében, de nagy mennyiségű extra tartalmat hoznak létre ezekben a nyomkövetésekben. Érdemes megváltoztatnia a TCP/IP-t `KeepAliveTime` és `KeepAliveInterval` a fürt csomópontjait is. Kombinálja ezeket a módosításokat a TCP/IP-paraméterekben az SAP-profil paramétereinek leírásával, a cikk későbbi részében leírtak szerint.

Ha az SAP ASCS/SCS-példányt mindkét fürtcsomóponton szeretné felvenni, először adja hozzá ezeket a Windows-beállításjegyzékbeli bejegyzéseket az SAP ASCS/SCS-hez készült Windows-fürtcsomópontokon:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveTime` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Hivatkozás a dokumentációra |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**3. táblázat:** Az első TCP/IP-paraméter módosítása

Ezt követően adja hozzá ezt a Windows beállításjegyzékbeli bejegyzést az SAP ASCS/SCS-hez készült Windows-fürtcsomópontok esetében:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveInterval` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Hivatkozás a dokumentációra |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**4. táblázat:** A második TCP/IP-paraméter módosítása

A módosítások alkalmazásához indítsa újra a fürtcsomópontok csomópontját.

## <a name="set-up-a-windows-server-failover-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Windows Server feladatátvevő fürt beállítása SAP ASCS/SCS-példányhoz

A Windows Server feladatátvevő fürtök SAP ASCS/SCS-példányhoz való beállítása a következő feladatokat foglalja magában:

- A fürtcsomópontok begyűjtése a fürt konfigurációjában.
- Fürtözött fájlmegosztás konfigurálása tanúsító.

### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>Fürt csomópontjainak összegyűjtése

1. A szerepkörök és szolgáltatások hozzáadása varázslóban adja hozzá a feladatátvételi fürtszolgáltatást a fürtcsomópontokon.
2. Állítsa be a feladatátvevő fürtöt Feladatátvevőfürt-kezelő használatával. A Feladatátvevőfürt-kezelő területen válassza a **fürt létrehozása**lehetőséget, majd adja hozzá a csak az első fürt nevét (a csomópont). Még ne adja hozzá a második csomópontot; egy későbbi lépésben hozzáadja a második csomópontot.

   ![8. ábra: az első fürtcsomópont kiszolgálójának vagy virtuális gépnek a nevének hozzáadása][sap-ha-guide-figure-3007]

   _**8. ábra:** Adja hozzá az első fürtcsomópont-kiszolgáló vagy virtuális gép nevét_

3. Adja meg a fürt hálózatnév (virtuális állomásnév) nevét.

   ![9. ábra: a fürt nevének megadása][sap-ha-guide-figure-3008]

   _**9. ábra:** Adja meg a fürt nevét_

4. A fürt létrehozása után futtasson egy fürt-ellenőrzési tesztet.

   ![10. ábra: a fürt érvényesítési ellenőrzésének futtatása][sap-ha-guide-figure-3009]

   _**10. ábra:** A fürt érvényesítési ellenőrzésének futtatása_

   A folyamat ezen pontján a lemezekkel kapcsolatos figyelmeztetések figyelmen kívül hagyhatók. Később a tanúsító fájlmegosztás és a SIOS megosztott lemezek is felvehetők. Ebben a szakaszban nem kell aggódnia a kvórumtal kapcsolatban.

   ![11. ábra: nem található kvórum lemez][sap-ha-guide-figure-3010]

   _**11. ábra:** Nem található kvórum lemez_

   ![12. ábra: egy alapvető fürterőforrás új IP-címmel kell rendelkeznie][sap-ha-guide-figure-3011]

   _**12. ábra:** Egy alapvető fürterőforrás új IP-címmel kell rendelkeznie_

5. Módosítsa az alapszintű fürtszolgáltatás IP-címét. A fürt addig nem indul el, amíg meg nem változtatja az alapvető fürtszolgáltatás IP-címét, mert a kiszolgáló IP-címe a virtuálisgép-csomópontok egyikére mutat. Ezt az alapszintű fürtszolgáltatás IP-erőforrásának **Tulajdonságok** lapján teheti meg.

   Például hozzá kell rendelni egy IP-címet (példánkban a 10.0.0.42) a fürt virtuális PR1-ASCs-VIR-hez.

   ![13. ábra: a Properties (Tulajdonságok) párbeszédpanelen módosítsa az IP-címet][sap-ha-guide-figure-3012]

   _**13. ábra:** A **Tulajdonságok** párbeszédpanelen módosítsa az IP-címet_

   ![14. ábra: a fürt számára fenntartott IP-cím kiosztása][sap-ha-guide-figure-3013]

   _**14. ábra:** A fürt számára fenntartott IP-cím kiosztása_

6. A fürt virtuális gazdagépének neve online állapotba hozása.

   ![15. ábra: a cluster Core szolgáltatás működik, és a megfelelő IP-címmel rendelkezik.][sap-ha-guide-figure-3014]

   _**15. ábra:** A cluster Core szolgáltatás működik, és a megfelelő IP-címmel rendelkezik._

7. Adja hozzá a második fürtcsomópont-csomópontot.

   Most, hogy az alapszintű fürtszolgáltatás működik, felveheti a második fürtcsomópont-csomópontot.

   ![16. ábra – a második fürtcsomópont hozzáadása][sap-ha-guide-figure-3015]

   _**16. ábra:** A második fürtcsomópont hozzáadása_

8. Adja meg a második fürtcsomópont-gazdagép nevét.

   ![17. ábra: adja meg a fürt második csomópontjának állomásnevét][sap-ha-guide-figure-3016]

   _**17. ábra:** Adja meg a fürt második csomópontjának állomásnevét_

   > [!IMPORTANT]
   > Ügyeljen arra, hogy az **összes megfelelő tároló hozzáadása a fürthöz** jelölőnégyzet *ne* legyen bejelölve.  
   >
   >

   ![18. ábra: ne jelölje be a jelölőnégyzetet][sap-ha-guide-figure-3017]

   _**18. ábra:** Ne *jelölje be* a jelölőnégyzetet_

   A kvórumtal és a lemezekkel kapcsolatos figyelmeztetések figyelmen kívül hagyhatók. Állítsa be a kvórumot, és ossza meg később a lemezt a következő témakörben leírtak szerint: [install SIOS DataKeeper cluster Edition for a SAP ASCS/SCS cluster Share Disk][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios].

   ![19. ábra: a lemez kvórumával kapcsolatos figyelmeztetések figyelmen kívül hagyása][sap-ha-guide-figure-3018]

   _**19. ábra:** A lemez kvórumával kapcsolatos figyelmeztetések figyelmen kívül hagyása_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Fürt fájlmegosztás-megosztásának konfigurálása

A fürt fájlmegosztás-megosztásának konfigurálása a következő feladatokat foglalja magában:

- Hozzon létre egy fájlmegosztást.
- A tanúsító fájlmegosztás Kvórumának beállítása Feladatátvevőfürt-kezelőban.

#### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Fájlmegosztás létrehozása

1. Jelöljön ki egy tanúsító fájlmegosztást a kvórum lemeze helyett. A SIOS DataKeeper támogatja ezt a beállítást.

   A jelen cikkben szereplő példákban a tanúsító fájlmegosztás az Azure-ban futó Active Directory vagy DNS-kiszolgálón található. A tanúsító fájlmegosztás neve domcontr-0. Mivel a VPN-kapcsolat az Azure-hoz (VPN Gateway vagy Azure ExpressRoute) lett konfigurálva, a Active Directory vagy a DNS szolgáltatás a helyszínen található, és nem alkalmas tanúsító fájlmegosztás futtatására.

   > [!NOTE]
   > Ha a Active Directory vagy a DNS szolgáltatás csak a helyszínen fut, ne konfigurálja a tanúsító fájlmegosztás a helyszínen futó Active Directory vagy DNS Windows operációs rendszeren. Az Azure-ban futó fürtcsomópontok és Active Directory vagy a helyszíni DNS közötti hálózati késés túl nagy lehet, és kapcsolódási problémákhoz vezethet. Ügyeljen arra, hogy a tanúsító fájlmegosztás olyan Azure-beli virtuális gépen legyen konfigurálva, amely a fürtcsomópont mellett fut.  
   >
   >

   A kvórum meghajtójának legalább 1 024 MB szabad területtel kell rendelkeznie. A kvórum meghajtójának 2 048 MB szabad területét javasoljuk.

2. Adja hozzá a fürt neve objektumot.

   ![20. ábra: a fürt nevének objektumához tartozó megosztási engedélyek kiosztása][sap-ha-guide-figure-3019]

   _**20. ábra:** Rendelje hozzá az engedélyeket a fürt neve objektum megosztásához_

   Győződjön meg arról, hogy az engedélyek tartalmazzák a hatóságot a fürt neve objektum (példánkban: PR1-ASCs-VIR $) megosztásában tárolt adatmódosításra.

3. A fürt neve objektum listához való hozzáadásához válassza a **Hozzáadás**lehetőséget. Módosítsa a szűrőt a számítógép-objektumok kereséséhez, a 22. ábrán láthatók mellett.

   ![21. ábra: objektumtípusok módosítása a számítógépek belefoglalásához][sap-ha-guide-figure-3020]

   _**21. ábra:** **Objektumok típusának** módosítása a számítógépek belefoglalásához_

   ![22. ábra: a számítógépek jelölőnégyzet bejelölése][sap-ha-guide-figure-3021]

   _**22. ábra:** Jelölje be a **számítógépek** jelölőnégyzetet_

4. Adja meg a fürt neve objektumot a 21. ábrán látható módon. Mivel a rekord már létrejött, módosíthatja az engedélyeket, ahogy az a 20. ábrán látható.

5. Válassza ki a megosztás **Biztonság** lapját, majd állítsa be részletesebben a fürt neve objektumra vonatkozó engedélyeket.

   ![23. ábra: a fürt neve objektum biztonsági attribútumainak beállítása a fájlmegosztás kvórumán][sap-ha-guide-figure-3022]

   _**23. ábra:** A fürt neve objektum biztonsági attribútumainak beállítása a fájlmegosztás kvórumán_

#### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>A tanúsító fájlmegosztás Kvórumának beállítása Feladatátvevőfürt-kezelő

1. Nyissa meg a kvórum beállításának konfigurálása varázslót.

   ![24. ábra: a fürt Kvórumának beállítása varázsló elindítása][sap-ha-guide-figure-3023]

   _**24. ábra:** A fürt Kvórumának beállítása varázsló elindítása_

2. A **kvórum konfigurációjának kiválasztása** lapon válassza **a tanúsító kvórum kijelölése**lehetőséget.

   ![25. ábra: választható kvórum-konfigurációk][sap-ha-guide-figure-3024]

   _**25. ábra:** Olyan kvórum-konfigurációk, amelyek közül választhat_

3. A **tanúsító kvórum kijelölése** lapon válassza a **tanúsító fájlmegosztás konfigurálása**lehetőséget.

   ![26. ábra: a tanúsító fájlmegosztás kiválasztása][sap-ha-guide-figure-3025]

   _**26. ábra:** Tanúsító fájlmegosztás kiválasztása_

4. Adja meg a fájlmegosztás UNC elérési útját (példánkban: \\domcontr-0\FSW). Ha meg szeretné tekinteni az elvégezhető módosítások listáját, válassza a **tovább**lehetőséget.

   ![27. ábra: a tanúsító megosztáshoz tartozó fájlmegosztás helyének meghatározása][sap-ha-guide-figure-3026]

   _**27. ábra:** A tanúsító megosztás fájlmegosztás helyének megadása_

5. Válassza ki a kívánt módosításokat, majd kattintson a **tovább**gombra. A fürt konfigurációjának újrakonfigurálását a 28. ábrán látható módon kell megadnia:  

   ![28. ábra: a fürt újrakonfigurálásának megerősítése][sap-ha-guide-figure-3027]

   _**28. ábra:** Erősítse meg, hogy újrakonfigurálta a fürtöt_

Miután sikeresen telepítette a Windows feladatátvevő fürtöt, módosítania kell néhány küszöbértéket, hogy a feladatátvételi észlelést a feltételekhez igazítsa az Azure-ban. A módosítandó paraméterek leírása a [feladatátvevő fürt hálózati küszöbértékének finomhangolása][tuning-failover-cluster-network-thresholds]című dokumentumban található. Feltételezve, hogy a ASCS/SCS-hez készült Windows-fürtöt alkotó két virtuális gép ugyanabban az alhálózatban található, módosítsa a következő paramétereket az értékekre:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Ezek a beállítások az ügyfelekkel lettek tesztelve, és jó kompromisszumot biztosítanak. Elég rugalmasak, de olyan feladatátvételt is biztosítanak, amely egy SAP-szoftver, illetve egy csomópont-vagy virtuálisgép-hiba esetén valós hibák esetén elég gyors.

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>A SIOS DataKeeper-fürt kiadásának telepítése az SAP ASCS/SCS-fürt megosztott lemezéhez

Most már rendelkezik egy működő Windows Server feladatátvételi fürtszolgáltatási konfigurációval az Azure-ban. Az SAP ASCS/SCS példány telepítéséhez megosztott lemezes erőforrásra van szükség. Nem hozható létre az Azure-ban szükséges megosztott lemez erőforrásai. A SIOS DataKeeper-fürt kiadása egy külső gyártótól származó megoldás, amely megosztott lemezes erőforrások létrehozására használható.

A SIOS DataKeeper-fürt kiadásának az SAP ASCS/SCS-fürt megosztott lemezre való telepítése a következő feladatokat foglalja magában:

- Adja hozzá a Microsoft .NET keretrendszert 3,5.
- Telepítse a SIOS DataKeeper.
- SIOS-DataKeeper beállítása.

### <a name="add-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>.NET-keretrendszer 3,5 hozzáadása
A .NET-keretrendszer 3,5 nem aktiválódik automatikusan, vagy nincs telepítve a Windows Server 2012 R2 rendszerre. Mivel a SIOS DataKeeper a .NET-et igényli minden olyan csomóponton, ahol a DataKeeper telepíti, telepítenie kell a .NET-keretrendszer 3,5-es verzióját a fürtben lévő összes virtuális gép vendég operációs rendszerén.

A .NET-keretrendszer 3,5 kétféleképpen adható hozzá:

- Használja a Windows szerepkörök és szolgáltatások hozzáadása varázslóját a 29. ábrán látható módon:

  ![29. ábra: a .NET-keretrendszer 3,5 telepítése a szerepkörök és szolgáltatások hozzáadása varázsló használatával][sap-ha-guide-figure-3028]

  _**29. ábra:** A .NET-keretrendszer 3,5-es telepítése a szerepkörök és szolgáltatások hozzáadása varázsló használatával_

  ![30. ábra: telepítési folyamatjelző sáv a .NET-keretrendszer 3,5-es verziójának a szerepkörök és szolgáltatások hozzáadása varázslóval történő telepítésekor][sap-ha-guide-figure-3029]

  _**30. ábra:** Telepítési folyamatjelző sáv a .NET-keretrendszer 3,5-es verziójának a szerepkörök és szolgáltatások hozzáadása varázsló használatával történő telepítésekor_

- Használja a DISM. exe parancssori eszközt. Ilyen típusú telepítés esetén el kell érnie a SxS könyvtárat a Windows telepítési adathordozóján. Írja be a következő parancsot egy rendszergazda jogú parancssorba:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>A SIOS DataKeeper telepítése

Telepítse a SIOS DataKeeper-fürt kiadását a fürt mindegyik csomópontján. Ha virtuális megosztott tárolót szeretne létrehozni a SIOS DataKeeper, hozzon létre egy szinkronizált tükröt, majd szimulálja a fürt megosztott tárolóját.

A SIOS szoftver telepítése előtt hozza létre a DataKeeperSvc tartományi felhasználót.

> [!NOTE]
> Adja hozzá a DataKeeperSvc tartományi felhasználót a helyi rendszergazda csoporthoz mindkét fürtcsomóponton.
>
>

A SIOS DataKeeper telepítése:

1. Telepítse a SIOS szoftvert mindkét fürtcsomóponton.

   ![SIOS-telepítő][sap-ha-guide-figure-3030]

   ![31. ábra: a SIOS DataKeeper-telepítésének első lapja][sap-ha-guide-figure-3031]

   _**31. ábra:** A SIOS-DataKeeper telepítésének első lapja_

2. A párbeszédpanelen válassza az **Igen**lehetőséget.

   ![32. ábra: a DataKeeper értesíti, hogy a szolgáltatás le lesz tiltva][sap-ha-guide-figure-3032]

   _**32. ábra:** A DataKeeper tájékoztatja, hogy a szolgáltatás le lesz tiltva_

3. Javasoljuk, hogy a párbeszédpanelen válassza a **tartomány vagy a kiszolgáló fiók**lehetőséget.

   ![33. ábra: felhasználó kiválasztása a SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**33. ábra:** Felhasználó kiválasztása a SIOS DataKeeper_

4. Adja meg a SIOS DataKeeper létrehozott tartományi fiók felhasználónevét és jelszavát.

   ![34. ábra: adja meg a SIOS-DataKeeper telepítésének tartományhoz tartozó felhasználónevét és jelszavát][sap-ha-guide-figure-3034]

   _**34. ábra:** Adja meg a SIOS-DataKeeper telepítésének tartományhoz tartozó felhasználónevét és jelszavát_

5. Telepítse a SIOS DataKeeper-példányának licenckulcs értékét az 35. ábrán látható módon.

   ![35. ábra: a SIOS DataKeeper-licenckulcs megadása][sap-ha-guide-figure-3035]

   _**35. ábra:** Adja meg a SIOS DataKeeper-licenckulcs_

6. Ha a rendszer kéri, indítsa újra a virtuális gépet.

### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>SIOS DataKeeper beállítása

Miután mindkét csomópontra telepítette a SIOS DataKeeper, indítsa el a konfigurációt. A konfiguráció célja, hogy szinkron adatreplikációt lehessen végrehajtani az egyes virtuális gépekhez csatolt további lemezek között.

1. Indítsa el a DataKeeper-kezelő és a konfigurációs eszközt, majd válassza a **kiszolgáló összekapcsolását**.

   ![36. ábra: SIOS DataKeeper-kezelő és-konfigurációs eszköz][sap-ha-guide-figure-3036]

   _**36. ábra:** SIOS DataKeeper-kezelő és-konfigurációs eszköz_

2. Adja meg az első csomópont nevét vagy TCP/IP-címét, amelyhez a felügyelet és a konfigurációs eszköz csatlakozni kíván, a második lépésben pedig a második csomópontot.

   ![37. ábra: az első csomópont nevének vagy TCP/IP-címének beszúrása a felügyeleti és konfigurációs eszköznek csatlakoznia kell a kiszolgálóhoz, a második lépésben pedig a második csomópontot.][sap-ha-guide-figure-3037]

   _**37. ábra:** Szúrja be az első csomópont nevét vagy TCP/IP-címét a felügyeleti és konfigurációs eszközhöz, és a második lépésben a második csomópontot._

3. Hozza létre a replikációs feladatot a két csomópont között.

   ![38. ábra: replikációs feladatok létrehozása][sap-ha-guide-figure-3038]

   _**38. ábra:** Replikációs feladatok létrehozása_

   A varázsló végigvezeti a replikációs feladatok létrehozásának folyamatán.

4. Adja meg a replikációs feladatoknak a nevét.

   ![39. ábra: a replikációs feladatok nevének megadása][sap-ha-guide-figure-3039]

   _**39. ábra:** A replikációs feladatok nevének megadása_

   ![40. ábra: a csomópont alapadatának megadása, amelynek az aktuális forrás-csomópontnak kell lennie.][sap-ha-guide-figure-3040]

   _**40. ábra:** Adja meg a csomópont alapadatait, amelyeknek az aktuális forrás-csomópontnak kell lennie._

5. Adja meg a cél csomópont nevét, TCP/IP-címét és a lemez kötetét.

   ![41. ábra: az aktuális cél csomópont nevének, TCP/IP-címének és lemezének megadása][sap-ha-guide-figure-3041]

   _**41. ábra:** Adja meg az aktuális cél csomópont nevét, TCP/IP-címét és a lemez kötetét._

6. Adja meg a tömörítési algoritmusokat. A példánkban azt javasoljuk, hogy tömörítse a replikálási adatfolyamot. Különösen újraszinkronizálási helyzetekben a replikálási adatfolyam tömörítése jelentősen csökkenti az újraszinkronizálás idejét. A tömörítés a virtuális gép processzor-és RAM-erőforrásait használja. A tömörítési sebesség növekszik, így a felhasznált CPU-erőforrások mennyisége. Ezt a beállítást később módosíthatja.

7. Egy másik beállításnak ellenőriznie kell, hogy a replikáció aszinkron módon vagy szinkronban történjen-e. Az SAP ASCS/SCS-konfigurációk védetté tételéhez szinkron replikálást kell használni.  

   ![42. ábra: a replikáció részleteinek meghatározása][sap-ha-guide-figure-3042]

   _**42. ábra:** Replikáció részleteinek megadása_

8. Annak megadása, hogy a replikálási feladatokkal replikált kötetet egy Windows Server feladatátvevő fürt konfigurációjának kell-e képviselnie megosztott lemezként. Az SAP ASCS/SCS konfiguráció esetében válassza az **Igen** lehetőséget, hogy a Windows-fürt a replikált kötetet megosztott lemezként látja, amelyet fürtözött kötetként használhat.

   ![43. ábra: válassza az Igen lehetőséget a replikált kötet fürtözött kötetként való beállításához.][sap-ha-guide-figure-3043]

   _**43. ábra:** Válassza az **Igen** lehetőséget a replikált kötet fürtözött kötetként való beállításához_

   A kötet létrehozása után a DataKeeper-kezelés és-konfiguráció eszköz azt mutatja, hogy a replikációs feladatok aktívak.

   ![44. ábra: az SAP ASCS/SCS-megosztási lemez DataKeeper szinkron tükrözése aktív][sap-ha-guide-figure-3044]

   _**44. ábra:** Az SAP ASCS/SCS-megosztási lemez DataKeeper szinkron tükrözése aktív_

   Feladatátvevőfürt-kezelő most a lemezt DataKeeper lemezként jeleníti meg, az 45. ábrán látható módon:

   ![45. ábra: az Feladatátvevőfürt-kezelő a replikált DataKeeper lemezt mutatja][sap-ha-guide-figure-3045]

   _**45. ábra:** Feladatátvevőfürt-kezelő megjeleníti a replikált DataKeeper lemezt_

## <a name="next-steps"></a>További lépések

* [Telepítse az SAP NetWeaver HA szolgáltatást egy Windows feladatátvevő fürt és egy SAP ASCS/SCS-példány megosztott lemezének használatával][sap-high-availability-installation-wsfc-shared-disk]
