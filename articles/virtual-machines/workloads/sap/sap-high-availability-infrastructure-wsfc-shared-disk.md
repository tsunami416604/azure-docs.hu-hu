---
title: Azure-infrastruktúra SAP ASCS/SCS-hez WSFC&megosztott lemezzel | Microsoft dokumentumok
description: Ismerje meg, hogyan készítheti elő az Azure-infrastruktúrát az SAP HA-hoz egy Windows feladatátvételi fürt és egy SAP ASCS/SCS-példány megosztott lemez használatával.
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
ms.openlocfilehash: 8a49bc979923bf52d099e30615910c5bdb0601b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279857"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Az Azure-infrastruktúra előkészítése az SAP HA számára egy Windows feladatátvételi fürt és az SAP ASCS/SCS megosztott lemezhasználatával

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID magas rendelkezésre állású konfiguráció)

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

Ez a cikk ismerteti azokat a lépéseket, amelyeket az Azure-infrastruktúra előkészítéséhez egy windowsos feladatátvevő fürtön egy windowsos feladatátvételi fürtön való telepítéséhez és konfigurálásához egy *fürtmegosztott lemez* használatával az SAP ASCS-példány fürtözésének lehetőségként való előkészítése során.

## <a name="prerequisites"></a>Előfeltételek

A telepítés megkezdése előtt tekintse át a következő cikket:

* [Architektúra-útmutató: SAP ASCS/SCS-példány fürtje windowsos feladatátvevő fürtön egy megosztott fürt használatával][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Az infrastruktúra előkészítése az 1.
Az Sap-hoz készült Azure Resource Manager-sablonok megkönnyítik a szükséges erőforrások üzembe helyezését.

Az Azure Resource Manager háromrétegű sablonjai is támogatják a magas rendelkezésre állású forgatókönyveket. Az 1-es architekturális sablon például két fürttel rendelkezik. Minden fürt egy SAP egyetlen meghibásodási pont az SAP ASCS/SCS és a DBMS.

Itt szerezheti be az Azure Resource Manager-sablonokat a cikkben leírt példaforgatókönyvhöz:

* [Azure Piactér lemezképe](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Az Azure Piactér lemezképe az Azure felügyelt lemezei használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Egyéni kép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Egyéni lemezkép a Felügyelt lemezek használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Az 1.

- Az Azure Portal **paraméterek ablaktáblájának** **RENDSZERELÉRHETŐSÉG** e-mezőben válassza a **HA**lehetőséget.

  ![1. ábra: Sap magas rendelkezésre állású Azure Resource Manager-paraméterek beállítása][sap-ha-guide-figure-3000]

_**1. ábra:** Sap magas rendelkezésre állású Azure Resource Manager-paramétereinek beállítása_


  A sablonok a következőket hozzák létre:

  * **Virtuális gépek**:
    * SAP Application Server \<virtuális gépek: SAPSystemSID\>-di-\<Szám\>
    * ASCS/SCS fürt virtuális \<gépei: SAPSystemSID\>-ascs-\<Szám\>
    * DBMS-fürt: \<SAPSystemSID\>\<-db- Szám\>

  * **Hálózati kártyák az összes virtuális géphez, ip-címekkel:**
    * \<SAPSystemSID\>-nic-di-\<szám\>
    * \<SAPSystemSID\>-nic-ascs-\<Szám\>
    * \<SAPSystemSID\>-nic-db-\<Szám\>

  * **Azure storage-fiókok (csak nem felügyelt lemezekesetén)**:

  * **Elérhetőségi csoportok:**
    * SAP Application Server \<virtuális gépek: SAPSystemSID\>-avset-di
    * SAP ASCS/SCS fürt \<virtuális gépei: SAPSystemSID\>-avset-ascs
    * DBMS fürt virtuális \<gépei: SAPSystemSID\>-avset-db

  * **Az Azure belső terheléselosztója:**
    * Az ASCS/SCS-példány és az \<SAPSystemSID\>-lb-ascs IP-cím összes portjával
    * Az SQL Server DBMS és AZ \<SAPSystemSID\>-lb-db IP-cím összes portjával

  * **Hálózati biztonsági** \<csoport :\>SAPSystemSID -nsg-ascs-0  
    * AZ \<SAPSystemSID\>-ascs-0 virtuális géphez nyílt külső távoli asztali protokoll (RDP) porttal

> [!NOTE]
> A hálózati kártyák és az Azure belső terheléselosztók összes IP-címe alapértelmezés szerint dinamikus. Módosítsa őket statikus IP-címekre. Ezt a cikk későbbi részében ismertetjük.
>
>

## <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>Vállalati hálózati kapcsolattal (telephelyek közötti) rendelkező virtuális gépek üzembe helyezése éles környezetben való használatra
Éles SAP-rendszerek esetén üzembe helyezheti az Azure virtuális gépeit vállalati hálózati kapcsolattal az Azure VPN Gateway vagy az Azure ExpressRoute használatával.

> [!NOTE]
> Használhatja az Azure virtuális hálózati példányt. A virtuális hálózat és az alhálózat már létrejött és előkészített.
>
>

1. Az Azure Portal **paraméterek ablaktáblájának** **NEWOREXISTINGSUBNET** mezőjében válassza a **meglévő**lehetőséget.
2. A **SUBNETID** mezőben adja hozzá az előkészített Azure hálózati alhálózati azonosító teljes karakterláncát, ahol az Azure virtuális gépeit szeretné telepíteni.
3. Az Azure hálózati alhálózatainak listájának lefelvételéhez futtassa ezt a PowerShell-parancsot:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Az **azonosító** mező az alhálózati azonosító értékét mutatja.
4. Az összes alhálózati azonosító érték listájának lefelvételéhez futtassa ezt a PowerShell-parancsot:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   Az alhálózati azonosító a következőképpen néz ki:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>Csak felhőalapú SAP-példányok üzembe helyezése teszteléshez és bemutatóhoz
A magas rendelkezésre állású SAP-rendszert csak felhőalapú üzembe helyezési modellben telepítheti. Ez a fajta központi telepítés elsősorban a bemutató és a tesztelési használati esetek. Nem alkalmas gyártási használati esetekre.

- Az Azure Portal **paraméterek ablaktáblájának** **NEWOREXISTINGSUBNET** mezőjében válassza az **Új**lehetőséget. Hagyja üresen a **SUBNETID mezőt.**

  Az SAP Azure Resource Manager sablon automatikusan létrehozza az Azure virtuális hálózatot és alhálózatot.

> [!NOTE]
> Emellett legalább egy dedikált virtuális gépet kell telepítenie az Active Directory és a DNS-szolgáltatás számára ugyanabban az Azure virtuális hálózati példányban. A sablon nem hozza létre ezeket a virtuális gépeket.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Az infrastruktúra előkészítése a 2.

Ezt az Azure Resource Manager-sablont az SAP-hoz használhatja a szükséges infrastruktúra-erőforrások üzembe helyezésének egyszerűsítéséhez az SAP 2-es architekturális sablonhoz.

Itt szerezheti be az Azure Resource Manager-sablonokat ehhez a telepítési forgatókönyvhöz:

* [Azure Piactér lemezképe](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Piactér lemezkép a felügyelt lemezek használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Egyéni kép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Egyéni lemezkép a Felügyelt lemezek használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Az infrastruktúra előkészítése a 3- as építészeti sablonhoz

Előkészítheti az infrastruktúrát, és konfigurálhatja az SAP-t a több-SID-hez. Például hozzáadhat egy további SAP ASCS/SCS-példányt egy *meglévő* fürtkonfigurációhoz. További információ: [Egy további SAP ASCS/SCS-példány konfigurálása egy meglévő fürtkonfigurációhoz SAP többSID-konfiguráció létrehozásához az Azure Resource Managerben.][sap-ha-multi-sid-guide]

Ha új, több SID-fürtöt szeretne létrehozni, használhatja a [githubon a több SID-s gyorsindítási sablonokat.](https://github.com/Azure/azure-quickstart-templates)

Új, több SID-fürt létrehozásához a következő három sablont kell telepítenie:

* [ASCS/SCS sablon](#ASCS-SCS-template)
* [Adatbázissablon](#database-template)
* [Alkalmazáskiszolgálók sablonja](#application-servers-template)

A következő szakaszok további részleteket tartalmaznak a sablonokban megadandó sablonokról és paraméterekről.

### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a>ASCS/SCS sablon

Az ASCS/SCS sablon két virtuális gépet telepít, amelyek segítségével több ASCS/SCS-példányt tartalmazó Windows Server feladatátvevő fürtet hozhat létre.

Az ASCS/SCS multi-SID sablon beállításához az [ASCS/SCS multi-SID vagy][sap-templates-3-tier-multisid-xscs-marketplace-image] [ASCS/SCS multi-SID sablonban a Felügyelt lemezek használatával][sap-templates-3-tier-multisid-xscs-marketplace-image-md]adja meg a következő paraméterek értékeit:

- **Erőforrás-előtag**: Állítsa be az erőforrás-előtagot, amely a központi telepítés során létrehozott összes erőforrás előtagra szolgál. Mivel az erőforrások nem csak egy SAP-rendszerhez tartoznak, az erőforrás előtagja nem egy SAP-rendszer sid-je.  Az előtagnak három és hat karakter között kell lennie.
- **Verem típusa**: Válassza ki az SAP-rendszer veremtípusát. A verem típusától függően az Azure Load Balancer rendelkezik egy (Csak ABAP vagy Java) vagy két (ABAP+Java) privát IP-címSAP-rendszerenként.
- **OS Type**: Válassza ki a virtuális gépek operációs rendszerét.
- **SAP-rendszerszáma:** Válassza ki a fürtben telepíteni kívánt SAP-rendszerek számát.
- **A rendszer elérhetősége:** Válassza a **HA**lehetőséget.
- **Rendszergazdai felhasználónév és rendszergazdai jelszó:** Hozzon létre egy új felhasználót, amely segítségével bejelentkezhet a gépre.
- **Új vagy meglévő alhálózat:** Beállíthatja, hogy új virtuális hálózatot és alhálózatot hozzon-e létre, vagy meglévő alhálózatot használjon. Ha már rendelkezik olyan virtuális hálózattal, amely a helyszíni hálózathoz csatlakozik, válassza a **meglévő**lehetőséget.
- **Alhálózati azonosító:** Ha a virtuális gép egy meglévő virtuális hálózatba szeretné telepíteni, ahol a virtuális gép hez definiált alhálózat ot kell hozzárendelni, nevezze el az adott alhálózat azonosítóját. Az azonosító általában így néz ki:

  /subscription/\<subscription id\>/resourceGroups/\<\>erőforráscsoport neve /providers/Microsoft.Network/virtualNetworks/\<virtuális hálózat neve\>/subnets/\<alhálózat neve\>

A sablon egy Azure Load Balancer-példányt telepít, amely több SAP-rendszert támogat:

- Az ASCS-példányok a 00, 10, 20...
- Az SCS-példányok a 01, 11, 21...
- Az ASCS enqueue replikációs kiszolgáló (ERS) (csak Linux) példányai a 02, 12, 22 számú példányra vannak konfigurálva...
- Az SCS ERS (csak Linux) példányok a 03, 13, 23...

A terheléselosztó 1 VIP(s) (2 Linux), 1x VIP ASCS/SCS és 1x VIP for ERS (csak Linux) tartalmazza.

#### <a name="sap-ascsscs-ports"></a><a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a>SAP ASCS/SCS portok
Az alábbi lista tartalmazza az összes terheléselosztási szabályt (ahol x az SAP-rendszer száma, például 1, 2, 3...):
- Windows-specifikus portok minden SAP rendszerhez: 445, 5985
- ASCS-portok (példányszám x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-portok (példányszám x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS portok Linuxon (példányszám x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS portok Linuxon (példányszám x3): 33x3, 5x313, 5x314, 5x316

A terheléselosztó a következő mintavételi portok használatára van konfigurálva (ahol x az SAP-rendszer száma, például 1, 2, 3...):
- ASCS/SCS belső terheléselosztó szondaport: 620x0
- ERS belső terheléselosztó szondaport (csak Linux esetén): 621x2

### <a name="database-template"></a><a name="database-template"></a>Adatbázissablon

Az adatbázissablon egy vagy két virtuális gépet telepít, amelyek segítségével telepítheti a relációs adatbázis-kezelő rendszert (RDBMS) egy SAP-rendszerhez. Ha például öt SAP-rendszerhez telepít egy ASCS/SCS-sablont, akkor ezt a sablont ötször kell telepítenie.

Az adatbázis több-SID sablonjának beállításához az [adatbázis több-SID vagy][sap-templates-3-tier-multisid-db-marketplace-image] [adatbázis több-SID sablonjában a Felügyelt lemezek használatával][sap-templates-3-tier-multisid-db-marketplace-image-md]adja meg a következő paraméterek értékeit:

- **Sap-rendszerazonosító:** Adja meg a telepíteni kívánt SAP-rendszer SAP-rendszerazonosítóját. Az azonosító az üzembe helyezett erőforrások előtagjaként használatos.
- **Os Type**: Válassza ki a virtuális gépek operációs rendszerét.
- **Dbtype**: Válassza ki a fürtre telepíteni kívánt adatbázis típusát. Válassza az **SQL** lehetőséget, ha telepíteni szeretné a Microsoft SQL Server kiszolgálót. Válassza ki **a HANA,** ha azt tervezi, hogy telepítse az SAP HANA a virtuális gépeken. Győződjön meg arról, hogy a megfelelő operációsrendszer-típust válassza ki. Válassza ki a **Windows** SQL-hez, és válasszon egy Linux-disztribúciót a HANA-hoz. A virtuális gépekhez csatlakoztatott Azure Load Balancer a kiválasztott adatbázistípus támogatására van konfigurálva:
  * **SQL**: A terheléselosztó 1433-as terheléselosztó portja. Győződjön meg arról, hogy ezt a portot használja az SQL Server AlwaysOn telepítéséhez.
  * **HANA**: A terheléselosztó terheléselosztási portok 35015 és 35017. Győződjön meg arról, hogy az SAP HANA-t **az 50-es**példányszámmal telepíti.
  A terheléselosztó a 62550-es szondaportot használja.
- **Sap rendszer mérete:** Állítsa be az új rendszer által biztosított SAPS számát. Ha nem biztos abban, hogy hány SAP-t igényel a rendszer, kérdezze meg az SAP technológiai partnervagy a rendszerintegrátor.
- **A rendszer elérhetősége:** Válassza a **HA**lehetőséget.
- **Rendszergazdai felhasználónév és rendszergazdai jelszó:** Hozzon létre egy új felhasználót, amely segítségével bejelentkezhet a gépre.
- **Alhálózati azonosító:** Adja meg az ASCS/SCS sablon telepítése során használt alhálózat azonosítóját, vagy az ASCS/SCS sablon központi telepítésének részeként létrehozott alhálózat azonosítóját.

### <a name="application-servers-template"></a><a name="application-servers-template"></a>Alkalmazáskiszolgálók sablonja

Az alkalmazáskiszolgálók sablon két vagy több virtuális gépet telepít, amelyek SAP Application Server-példányként használhatók egy SAP-rendszerhez. Ha például öt SAP-rendszerhez telepít egy ASCS/SCS-sablont, akkor ezt a sablont ötször kell telepítenie.

Az alkalmazáskiszolgálók többSID-sablonjának beállításához az [alkalmazáskiszolgálók több SID-sablonjában][sap-templates-3-tier-multisid-apps-marketplace-image] vagy [több SID-sablonban a Felügyelt lemezek használatával][sap-templates-3-tier-multisid-apps-marketplace-image-md]adja meg a következő paraméterek értékeit:

  -  **Sap-rendszerazonosító:** Adja meg a telepíteni kívánt SAP-rendszer SAP-rendszerazonosítóját. Az azonosító az üzembe helyezett erőforrások előtagjaként használatos.
  -  **Os Type**: Válassza ki a virtuális gépek operációs rendszerét.
  -  **Sap rendszer mérete:** Az új rendszer által biztosított SAPS-ek száma. Ha nem biztos abban, hogy hány SAP-t igényel a rendszer, kérdezze meg az SAP technológiai partnervagy a rendszerintegrátor.
  -  **A rendszer elérhetősége:** Válassza a **HA**lehetőséget.
  -  **Rendszergazdai felhasználónév és rendszergazdai jelszó:** Hozzon létre egy új felhasználót, amely segítségével bejelentkezhet a gépre.
  -  **Alhálózati azonosító:** Adja meg az ASCS/SCS sablon telepítése során használt alhálózat azonosítóját, vagy az ASCS/SCS sablon központi telepítésének részeként létrehozott alhálózat azonosítóját.


## <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>Azure virtuális hálózat
A példában az Azure Virtual Network-példány címtere 10.0.0.0/16. Van egy alhálózat nevű Alhálózat, amelynek címtartománya 10.0.0.0/24. Az összes virtuális gép és belső terheléselosztó telepítve van ebben a virtuális hálózatban.

> [!IMPORTANT]
> Ne módosítsa a hálózati beállításokat a vendég operációs rendszeren belül. Ide tartoznak az IP-címek, a DNS-kiszolgálók és az alhálózatok. Konfigurálja az összes hálózati beállítást az Azure-ban. A DHCP (Dynamic Host Configuration Protocol) szolgáltatás propagálja a beállításokat.
>
>

## <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>DNS-IP-címek

A szükséges DNS-IP-címek beállításához hajtsa végre az alábbi lépéseket:

1. Az Azure Portal **DNS-kiszolgálók** ablaktábláján győződjön meg arról, hogy a virtuális hálózati **DNS-kiszolgálók** beállítása **Egyéni DNS**.
2. Válassza ki a beállításokat a hálózat típusa alapján. További információkért lásd a következőket:
   * Adja hozzá a helyszíni DNS-kiszolgálók IP-címét.  
   A helyszíni DNS-kiszolgálókat kiterjesztheti az Azure-ban futó virtuális gépekre. Ebben a forgatókönyvben hozzáadhatja azoknak az Azure virtuális gépeknek az IP-címeit, amelyeken a DNS-szolgáltatást futtatja.
   * Az Azure-ban elkülönített virtuálisgép-telepítések esetén: Telepítsen egy további virtuális gépet ugyanabban a virtuális hálózati példányban, amely DNS-kiszolgálóként szolgál. Adja hozzá a DNS-szolgáltatás futtatásához beállított Azure virtuális gépek IP-címét.

   ![2. ábra: DNS-kiszolgálók konfigurálása az Azure virtuális hálózathoz][sap-ha-guide-figure-3001]

   _**2. ábra:** DNS-kiszolgálók konfigurálása az Azure virtuális hálózathoz_

   > [!NOTE]
   > Ha módosítja a DNS-kiszolgálók IP-címét, újra kell indítania az Azure virtuális gépeket a módosítás alkalmazásához és az új DNS-kiszolgálók terjesztéséhez.
   >
   >

A példánkban a DNS-szolgáltatás telepítve és konfigurálva van ezeken a Windows virtuális gépeken:

| Virtuális gép szerepkör | Virtuálisgép-állomás neve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első DNS-kiszolgáló |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Második DNS-kiszolgáló |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>Állomásnevek és statikus IP-címek az SAP ASCS/SCS fürtözött példányhoz és az DBMS fürtözött példányhoz

A helyszíni telepítéshez ezekre a fenntartott állomásnevekre és IP-címekre van szükség:

| Virtuális állomásnév szerepkör | Virtuális állomás neve | Virtuális statikus IP-cím |
| --- | --- | --- |
| SAP ASCS/SCS első fürtvirtuális állomásneve (fürtkezeléshez) |pr1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS-példány virtuális állomásneve |pr1-ascs-nedv |10.0.0.43 |
| SAP DBMS második fürt virtuális állomásneve (fürtkezelés) |pr1-dbms-vir |10.0.0.32 |

A fürt létrehozásakor hozza létre a virtuális állomás neveket pr1-ascs-vir és pr1-dbms-vir és a kapcsolódó IP-címeket, amelyek kezelik a fürt maga. Ennek módjáról a [Fürtcsomópontok gyűjtése fürtkonfigurációban][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]című témakörben talál további információt.

A másik két virtuális állomásnevet, a pr1-ascs-sap és pr1-dbms-sap-t és a kapcsolódó IP-címeket manuálisan is létrehozhatja a DNS-kiszolgálón. A fürtözött SAP ASCS/SCS-példány és a fürtözött DBMS-példány ezeket az erőforrásokat használja. Ennek módjáról a [Fürtözött SAP ASCS/SCS-példány virtuális állomásnevének létrehozása][sap-ha-guide-9.1.1]című témakörben talál.

## <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>Statikus IP-címek beállítása az SAP virtuális gépekhez
Miután üzembe helyezte a fürtben használt virtuális gépeket, statikus IP-címeket kell beállítania az összes virtuális géphez. Ehhez az Azure virtuális hálózat konfiguráció, és nem a vendég operációs rendszer.

1. Az Azure portalon válassza az **Erőforráscsoport** >  >  > **hálózatikártya-beállítások****IP-címét**lehetőséget.**Settings**
2. Az **IP-címek** ablaktáblán a **Hozzárendelés**csoportban válassza a **Statikus**lehetőséget. Az **IP-cím** mezőbe írja be a használni kívánt IP-címet.

   > [!NOTE]
   > Ha módosítja a hálózati kártya IP-címét, újra kell indítania az Azure virtuális gépeket a módosítás alkalmazásához.  
   >
   >

   ![3. ábra: Statikus IP-címek beállítása az egyes virtuális gépek hálózati kártyáihoz][sap-ha-guide-figure-3002]

   _**3. ábra:** Statikus IP-címek beállítása az egyes virtuális gépek hálózati kártyáihoz_

   Ismételje meg ezt a lépést az összes hálózati csatoló esetében, azaz az összes virtuális gépen, beleértve az Active Directory hoz vagy a DNS-szolgáltatáshoz használni kívánt virtuális gépeket is.

A példánkban ezek a virtuális gépek és statikus IP-címek:

| Virtuális gép szerepkör | Virtuálisgép-állomás neve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első SAP-alkalmazáskiszolgáló-példány |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Második SAP-alkalmazáskiszolgáló példány |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Utolsó SAP-alkalmazáskiszolgáló-példány |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Az ASCS/SCS-példány első fürtcsomópontja |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Második fürtcsomópont AZ ASCS/SCS-példányhoz |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| A DBMS-példány első fürtcsomópontja |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| A DBMS-példány második fürtcsomópontja |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>Statikus IP-cím beállítása az Azure belső terheléselosztójának

Az SAP Azure Resource Manager-sablon létrehoz egy Azure belső terheléselosztót, amely az SAP ASCS/SCS-példányfürthöz és a DBMS-fürthöz használatos.

> [!IMPORTANT]
> Az SAP ASCS/SCS virtuális állomásnevének IP-címe megegyezik az SAP ASCS/SCS belső terheléselosztó: pr1-lb-ascs IP-címével.
> A DBMS virtuális nevének IP-címe megegyezik a DBMS belső terheléselosztójának IP-címével: pr1-lb-dbms.
>
>

Statikus IP-cím beállítása az Azure belső terheléselosztóhoz:

1. A kezdeti központi telepítés a belső terheléselosztó IP-címét **dinamikusra állítja.** Az Azure Portalon az **IP-címek** ablaktáblán a **Hozzárendelés**csoportban válassza a **Statikus**lehetőséget.
2. Állítsa be a belső terheléselosztó **pr1-lb-ascs** IP-címét az SAP ASCS/SCS-példány virtuális állomásnevének IP-címére.
3. Állítsa be a belső terheléselosztó **pr1-lb-dbms** IP-címét a DBMS-példány virtuális állomásnevének IP-címére.

   ![4. ábra: Statikus IP-címek beállítása az SAP ASCS/SCS-példány belső terheléselosztójának][sap-ha-guide-figure-3003]

   _**4. ábra:** Statikus IP-címek beállítása az SAP ASCS/SCS-példány belső terheléselosztójának_

A példában két Azure belső terheléselosztóval rendelkezik, amelyek rendelkeznek ezekkel a statikus IP-címekkel:

| Az Azure belső terheléselosztó szerepköre | Az Azure belső terheléselosztójának neve | Statikus IP-cím |
| --- | --- | --- |
| SAP ASCS/SCS-példány belső terheléselosztója |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS belső terheléselosztó |pr1-lb-dbms |10.0.0.33 |


## <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>Alapértelmezett ASCS/SCS terheléselosztási szabályok az Azure belső terheléselosztóhoz

Az SAP Azure Resource Manager sablon létrehozza a szükséges portokat:
* ABAP ASCS-példány, 00 alapértelmezett példányszámmal
* Java SCS-példány, 01-es alapértelmezett példányszámmal

Az SAP ASCS/SCS-példány telepítésekor az ABAP ASCS-példány 00-as számú példányát és a Java SCS-példány 01-es számú példányát kell használnia.

Ezután hozza létre a szükséges belső terheléselosztási végpontokat az SAP NetWeaver portokhoz.

A szükséges belső terheléselosztási végpontok létrehozásához először hozza létre ezeket a terheléselosztási végpontokat az SAP NetWeaver ABAP ASCS portokhoz:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (ASCS-példány 00-s példányszámmal) (ERS 10-el) |
| --- | --- | --- |
| Várólistára helyezett kiszolgáló / *lbrule3200* |32\<Példányszám\> |3200 |
| ABAP üzenetszerver / *lbrule3600* |36\<Példányszám\> |3600 |
| Belső ABAP üzenet / *lbrule3900* |39\<Példányszám\> |3900 |
| Üzenetkiszolgáló HTTP / *Lbrule8100* |81\<Példányszám\> |8100 |
| SAP start szolgáltatás ASCS HTTP / *Lbrule50013* |5\<Példányszám\>13 |50013 |
| SAP start szolgáltatás ASCS HTTPS / *Lbrule50014* |5\<Példányszám\>14 |50014 |
| Várólistára helyezett replikáció / *Lbrule50016* |5\<Példányszám 16\> |50016 |
| SAP start szolgáltatás ERS HTTP *Lbrule51013* |5\<Példányszám\>13 |51013 |
| SAP start szolgáltatás ERS HTTP *Lbrule51014* |5\<Példányszám\>14 |51014 |
| Windows távfelügyeleti (WinRM) *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

**1.** Az SAP NetWeaver ABAP ASCS-példányok portszámai

Ezután hozza létre ezeket a terheléselosztási végpontokat az SAP NetWeaver Java SCS portokhoz:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Betonportok (SCS-példány 01-es példányszámmal) (ERS 11-es sel) |
| --- | --- | --- |
| Várólistára helyezett kiszolgáló / *lbrule3201* |32\<Példányszám\> |3201 |
| Átjárókiszolgáló / *lbrule3301* |33\<Példányszám\> |3301 |
| Java üzenet szerver / *lbrule3900* |39\<Példányszám\> |3901 |
| Üzenetkiszolgáló HTTP / *Lbrule8101* |81\<Példányszám\> |8101 |
| SAP start szolgáltatás SCS HTTP / *Lbrule50113* |5\<Példányszám\>13 |50113 |
| SAP start szolgáltatás SCS HTTPS / *Lbrule50114* |5\<Példányszám\>14 |50114 |
| Várólistára helyezett replikáció / *Lbrule50116* |5\<Példányszám 16\> |50116 |
| SAP start szolgáltatás ERS HTTP *Lbrule51113* |5\<Példányszám\>13 |51113 |
| SAP start szolgáltatás ERS HTTP *Lbrule51114* |5\<Példányszám\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

**2.** Az SAP NetWeaver Java SCS-példányok portszámai

![5. ábra: Alapértelmezett ASCS/SCS terheléselosztási szabályok az Azure belső terheléselosztóhoz][sap-ha-guide-figure-3004]

_**5. ábra:** Alapértelmezett ASCS/SCS terheléselosztási szabályok az Azure belső terheléselosztóhoz_

Állítsa be a pr1-lb-dbms terheléselosztó IP-címét a DBMS-példány virtuális állomásnevének IP-címére.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>Az ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása az Azure belső terheléselosztójára vonatkozóan

Ha az SAP ASCS- vagy SCS-példányokhoz különböző számokat szeretne használni, módosítania kell a portok nevét és értékeit az alapértelmezett értékekről.

1. Az Azure Portalon válassza >  ** \<a\>SID -lb-ascs terheléselosztási**szabályok**lehetőséget.**
2. Az SAP ASCS- vagy SCS-példányhoz tartozó összes terheléselosztási szabály esetében módosítsa ezeket az értékeket:

   * Név
   * Port
   * Háttérport

   Ha például az alapértelmezett ASCS-példányszámot 00-ról 31-re szeretné módosítani, az 1.

   Íme egy példa az *lbrule3200*port frissítésére.

   ![6. ábra: Az ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása az Azure belső terheléselosztóhoz][sap-ha-guide-figure-3005]

   _**6. ábra:** Az ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása az Azure belső terheléselosztójára vonatkozóan_

## <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>Windows virtuális gépek hozzáadása a tartományhoz

Miután statikus IP-címet rendelt a virtuális gépekhez, adja hozzá a virtuális gépeket a tartományhoz.

![7. ábra: Virtuális gép hozzáadása tartományhoz][sap-ha-guide-figure-3006]

_**7. ábra:** Virtuális gép hozzáadása tartományhoz_

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>Rendszerleíró bejegyzések hozzáadása az SAP ASCS/SCS-példány mindkét fürtcsomópontján

Az Azure Load Balancer rendelkezik egy belső terheléselosztóval, amely bezárja a kapcsolatokat, ha a kapcsolatok egy meghatározott ideig (egy tétlen időtúltöltés) tétlenek. A párbeszédpéldányokban lévő SAP-munkafolyamatok megnyitják az SAP-várólistás folyamattal létesített kapcsolatokat, amint az első enqueue/dequeue kérelmet el kell küldeni. Ezek a kapcsolatok általában a munkafolyamat vagy a várólistán lévő folyamat újraindításáig maradnak létre. Azonban ha a kapcsolat egy meghatározott ideig tétlen, az Azure belső terheléselosztó bezárja a kapcsolatokat. Ez nem probléma, mert az SAP munkafolyamat újra létrehozza a kapcsolatot a várólistára folyamat, ha már nem létezik. Ezeket a tevékenységeket az SAP-folyamatok fejlesztői nyomai dokumentálják, de nagy mennyiségű extra tartalmat hoznak létre ezekben a nyomkövetésekben. Célszerű módosítani a TCP/IP protokollt `KeepAliveTime` `KeepAliveInterval` és mindkét fürtcsomóponton. Kombinálja ezeket a változásokat a TCP/IP paraméterek et az SAP profil paraméterekkel, amelyeket a cikk későbbi részében ismertetett.

Az SAP ASCS/SCS-példány mindkét fürtcsomópontjának rendszerleíró bejegyzéseinek hozzáadásához először adja hozzá ezeket a Windows rendszerleíró bejegyzéseket az SAP ASCS/SCS mindkét Windows-fürtcsomópontján:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Paraméterek |
| --- | --- |
| Változó neve |`KeepAliveTime` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Hivatkozás a dokumentációra |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**3.** Az első TCP/IP paraméter módosítása

Ezután adja hozzá ezt a Windows rendszerleíró bejegyzést az SAP ASCS/SCS mindkét Windows fürtcsomópontján:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Paraméterek |
| --- | --- |
| Változó neve |`KeepAliveInterval` |
| Változó típusa |REG_DWORD (decimális) |
| Érték |120000 |
| Hivatkozás a dokumentációra |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**4.** A második TCP/IP paraméter módosítása

A módosítások alkalmazásához indítsa újra mindkét fürtcsomópontot.

## <a name="set-up-a-windows-server-failover-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>Windows Server feladatátvevő fürt beállítása SAP ASCS/SCS-példányhoz

Windows Server feladatátvevő fürt beállítása SAP ASCS/SCS-példányhoz a következő feladatokat foglalja magában:

- Gyűjtse össze a fürtcsomópontokat egy fürtkonfigurációban.
- Konfigurálja a fürtfájl megosztásának tanúsítóját.

### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>A fürtcsomópontok gyűjtése fürtkonfigurációban

1. A Szerepkör és szolgáltatások hozzáadása varázslóban adja hozzá a feladatátvevő fürtözést mindkét fürtcsomóponthoz.
2. Állítsa be a feladatátvevő fürtöt a Feladatátvevő fürtkezelővel. A Feladatátvevőfürt-kezelőben válassza **a Fürt létrehozása**lehetőséget, majd csak az első fürt (A csomópont) nevét adja hozzá. Még ne adja hozzá a második csomópontot; egy későbbi lépésben adja hozzá a második csomópontot.

   ![8. ábra: Az első fürtcsomópont kiszolgáló- vagy virtuálisgép-nevének hozzáadása][sap-ha-guide-figure-3007]

   _**8. ábra:** Az első fürtcsomópont kiszolgáló- vagy virtuálisgép-nevének hozzáadása_

3. Adja meg a fürt hálózati nevét (virtuális állomásnevét).

   ![9. ábra: Adja meg a fürt nevét][sap-ha-guide-figure-3008]

   _**9. ábra:** Adja meg a fürt nevét_

4. A fürt létrehozása után futtasson fürtérvényesítési tesztet.

   ![10. ábra: A fürtérvényesítési ellenőrzés futtatása][sap-ha-guide-figure-3009]

   _**10. ábra:** A fürtérvényesítési ellenőrzés futtatása_

   A folyamat ezen pontján figyelmen kívül hagyhatja a lemezekkel kapcsolatos figyelmeztetéseket. Később hozzá kell adnia egy fájlmegosztási tanúsítót és a SIOS megosztott lemezeket. Ebben a szakaszban nem kell aggódnia a kvórum miatt.

   ![11. ábra: Nem található kvórumlemez][sap-ha-guide-figure-3010]

   _**11. ábra:** Nem található kvórumlemez_

   ![12. ábra: A fürtközponti alaperőforrásnak új IP-címre van szüksége][sap-ha-guide-figure-3011]

   _**12. ábra:** A központi fürterőforrásnak új IP-címre van szüksége_

5. Módosítsa a központi fürtszolgáltatás IP-címét. A fürt nem indítható el, amíg nem módosítja a központi fürtszolgáltatás IP-címét, mert a kiszolgáló IP-címe a virtuális gép egyik csomópontjára mutat. Ehhez a fő fürtszolgáltatás IP-erőforrásának **Tulajdonságok** lapján.

   Például hozzá kell rendelnünk egy IP-címet (példában 10.0.0.42) a fürt virtuális állomásnevéhez pr1-ascs-vir.

   ![13. ábra: A Tulajdonságok párbeszédpanelen módosítsa az IP-címet][sap-ha-guide-figure-3012]

   _**13. ábra:** A **Tulajdonságok** párbeszédpanelen módosítsa az IP-címet_

   ![14. ábra: A fürt számára fenntartott IP-cím hozzárendelése][sap-ha-guide-figure-3013]

   _**14. ábra:** A fürt számára fenntartott IP-cím hozzárendelése_

6. A fürt virtuális állomásnevének online állapotba hozása.

   ![15. ábra: A fürt alapszolgáltatása működik, a megfelelő IP-címmel][sap-ha-guide-figure-3014]

   _**15. ábra:** A fürt alapszolgáltatása működik, a megfelelő IP-címmel_

7. Adja hozzá a második fürtcsomópontot.

   Most, hogy a fő fürtszolgáltatás működik, hozzáadhatja a második fürtcsomópontot.

   ![16. ábra A második fürtcsomópont hozzáadása][sap-ha-guide-figure-3015]

   _**16. ábra:** A második fürtcsomópont hozzáadása_

8. Adja meg a második fürtcsomópont-állomás nevét.

   ![17. ábra: Adja meg a második fürtcsomópont állomásnevét][sap-ha-guide-figure-3016]

   _**17. ábra:** Adja meg a második fürtcsomópont állomásnevét_

   > [!IMPORTANT]
   > Győződjön meg arról, hogy az **Összes jogosult tároló hozzáadása a fürthöz** jelölőnégyzet *nincs* bejelölve.  
   >
   >

   ![18. ábra: Ne jelölje be a jelölőnégyzetet][sap-ha-guide-figure-3017]

   _**18. ábra:** *Ne* jelölje be a jelölőnégyzetet_

   Figyelmen kívül hagyhatja a kvórumra és a lemezekre vonatkozó figyelmeztetéseket. A kvórumot később fogja beállítani, és a lemezt később fogja megosztani, ahogy azt a [SIOS DataKeeper Cluster Edition telepítése sap ASCS/SCS fürtmegosztási lemezhez][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]című témakör ismerteti.

   ![19. ábra: A lemezkvórumra vonatkozó figyelmeztetések figyelmen kívül hagyása][sap-ha-guide-figure-3018]

   _**19. ábra:** A lemezkvórumra vonatkozó figyelmeztetések figyelmen kívül hagyása_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>Fürtfájlmegosztás tanúsítójának konfigurálása

A fürtfájlmegosztás tanúsítójának konfigurálása a következő feladatokat foglalja magában:

- Hozzon létre egy fájlmegosztást.
- Állítsa be a fájlmegosztási tanúsító kvórumát a feladatátvevői fürtkezelőben.

#### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>Fájlmegosztás létrehozása

1. Kvórumlemez helyett jelöljön ki egy tanúsító fájlmegosztást. A SIOS DataKeeper támogatja ezt a lehetőséget.

   Ebben a cikkben a példákban a fájlmegosztás tanúsító az Azure-ban futó Active Directory vagy DNS-kiszolgálón található. Az aktamegosztási tanú neve domcontr-0. Mivel vpn-kapcsolatot konfigurált volna az Azure-ral (VPN-átjárón vagy Azure ExpressRoute-on keresztül), az Active Directory vagy a DNS-szolgáltatás helyszíni, és nem alkalmas fájlmegosztási tanúsító futtatására.

   > [!NOTE]
   > Ha az Active Directory vagy a DNS-szolgáltatás csak a helyszínen fut, ne konfigurálja a fájlmegosztás tanúsítóját a helyszíni Active Directory vagy DNS Windows operációs rendszeren. Az Azure-ban és az Active Directoryban vagy a helyszíni DNS-ben futó fürtcsomópontok közötti hálózati késés túl nagy lehet, és kapcsolódási problémákat okozhat. Ügyeljen arra, hogy konfigurálja a fájlmegosztás tanúsító egy Azure virtuális gépen, amely a fürtcsomópont közelében fut.  
   >
   >

   A kvórummeghajtónak legalább 1024 MB szabad helyre van szüksége. Javasoljuk, hogy 2048 MB szabad terület a kvórummeghajtó.

2. Adja hozzá a fürtnév-objektumot.

   ![20. ábra: A fürtnév-objektum megosztására vonatkozó engedélyek hozzárendelése][sap-ha-guide-figure-3019]

   _**20. ábra:** A fürtnév-objektum megosztására vonatkozó engedélyek hozzárendelése_

   Győződjön meg arról, hogy az engedélyek tartalmazzák a fürtnév-objektum megosztásának adatait (példánkban pr1-ascs-vir$) adatok módosítására szolgáló jogosultságot.

3. Ha hozzá szeretné adni a fürtnév-objektumot a listához, válassza a **Hozzáadás gombot.** Módosítsa a szűrőt a számítógép-objektumok ellenőrzésére a 22.

   ![21. ábra: Objektumtípusok módosítása számítógépekre][sap-ha-guide-figure-3020]

   _**21. ábra:** **Objektumtípusok** módosítása számítógépekre_

   ![22. ábra: Jelölje be a Számítógépek jelölőnégyzetet][sap-ha-guide-figure-3021]

   _**22. ábra:** Jelölje be a **Számítógépek** jelölőnégyzetet._

4. Írja be a fürtnév-objektumot a 21. Mivel a rekord már létrejött, módosíthatja az engedélyeket, ahogy az a 20.

5. Válassza a megosztás **Biztonság** lapját, majd adja meg a fürtnév-objektum részletesebb engedélyeit.

   ![23. ábra: A fürtnév-objektum biztonsági attribútumainak beállítása a fájlmegosztáskvórumban][sap-ha-guide-figure-3022]

   _**23. ábra:** A fürtnév-objektum biztonsági attribútumainak beállítása a fájlmegosztáskvórumban_

#### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>A fájlmegosztási tanúsító kvórumának beállítása a Feladatátvevői fürtkezelőben

1. Nyissa meg a Kvórum beállítása varázslót.

   ![24. ábra: A Fürtkvórum beállítása varázsló indítása][sap-ha-guide-figure-3023]

   _**24. ábra:** A Fürtkvórum beállítása varázsló indítása_

2. A **Kvórum konfigurációs beállításainak kiválasztása** lapon válassza **a Kvórumtanúsító kiválasztása**lehetőséget.

   ![25. ábra: A kvórum konfigurációk közül választhat][sap-ha-guide-figure-3024]

   _**25. ábra:** Kvórum konfigurációk közül választhat_

3. A **Kvórum tanúsító kiválasztása** lapon válassza **a Fájlmegosztási tanúsító konfigurálása**lehetőséget.

   ![26. ábra: Válassza ki a tanúsító fájlmegosztást][sap-ha-guide-figure-3025]

   _**26. ábra:** A tanúsító fájlmegosztás kiválasztása_

4. Adja meg a fájlmegosztás UNC elérési \\útját (példánkban: domcontr-0\FSW). A módosítások listájának megtekintéséhez válassza a **Tovább gombot.**

   ![27. ábra: A tanúsító megosztás fájlmegosztási helyének meghatározása][sap-ha-guide-figure-3026]

   _**27. ábra:** A tanúsító megosztás fájlmegosztási helyének meghatározása_

5. Jelölje ki a kívánt módosításokat, majd kattintson a **Tovább gombra.** A fürtkonfigurációt a 28.  

   ![28. ábra: Megerősítés a fürt újrakonfigurálása][sap-ha-guide-figure-3027]

   _**28. ábra:** A fürt újrakonfigurálásának megerősítése_

Miután sikeresen telepítette a Windows feladatátvevő fürtöt, módosítania kell néhány küszöbértéket, hogy azok alkalmazkodjanak a feladatátvételi észleléshez az Azure-beli feltételekhez. A módosítandó paramétereket a [Hangolási szolgáltatásfürt hálózati küszöbértékei dokumentálják.][tuning-failover-cluster-network-thresholds] Feltételezve, hogy az ASCS/SCS Windows-fürtkonfigurációját kiálló két virtuális gép ugyanabban az alhálózatban van, módosítsa a következő paramétereket a következő értékekre:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Ezeket a beállításokat az ügyfelek tesztelték, és jó kompromisszumot kínálnak. Elég rugalmasak, de olyan feladatátvételt is biztosítanak, amely elég gyors az SAP-szoftver, illetve egy csomópont vagy virtuális gép meghibásodása esetén, valós hibafeltételek esetén.

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>SIOS DataKeeper Cluster Edition telepítése az SAP ASCS/SCS fürtmegosztási lemezre

Most már van egy működő Windows Server feladatátvételi fürtözési konfigurációja az Azure-ban. Sap ASCS/SCS-példány telepítéséhez megosztott lemezerőforrásra van szükség. Az Azure-ban nem hozhat létre a szükséges megosztott lemezerőforrásokat. A SIOS DataKeeper Cluster Edition egy külső gyártótól származó megoldás, amelynek segítségével megosztott lemezerőforrásokat hozhat létre.

A SIOS DataKeeper Cluster Edition telepítése az SAP ASCS/SCS fürtmegosztási lemezhez a következő feladatokat foglalja magában:

- Microsoft .NET Framework 3.5 hozzáadása.
- Telepítse a SIOS Datakeeper alkalmazást.
- Állítsa be a SIOS DataKeeper-t.

### <a name="add-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>.NET Framework 3.5 hozzáadása
A . Mivel a SIOS DataKeeper használatához a .NET szükséges a DataKeeper telepítéséhez szükséges összes csomóponton, telepítenie kell a .NET Framework 3.5 rendszert a fürt összes virtuális gépének vendég operációs rendszerére.

A .NET Framework 3.5 kétféleképpen adhat hozzá:

- Használja a Szerepkörök és szolgáltatások hozzáadása varázslót a Windows rendszerben, a29.

  ![29. ábra: A .NET Framework 3.5 telepítése a Szerepkörök és szolgáltatások hozzáadása varázslóval][sap-ha-guide-figure-3028]

  _**29. ábra:** A .NET Framework 3.5 telepítése a Szerepkörök és szolgáltatások hozzáadása varázslóval_

  ![30. ábra: A .NET Framework 3.5 telepítésekor a telepítés folyamatjelzője a Szerepkörök és szolgáltatások hozzáadása varázslóval][sap-ha-guide-figure-3029]

  _**30. ábra:** A telepítés folyamatjelző je a .NET Framework 3.5 telepítésekor a Szerepkörök és szolgáltatások hozzáadása varázslóval_

- Használja a dism.exe parancssori eszközt. Az ilyen típusú telepítéshez hozzá kell férnie a Windows telepítési adathordozójának SxS könyvtárához. Egy rendszergazda jogú parancssorba írja be ezt a parancsot:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a>SIOS DataKeeper telepítése

Telepítse a SIOS DataKeeper Cluster Edition-t a fürt minden csomópontjára. Virtuális megosztott tároló létrehozásához a SIOS DataKeeper segítségével hozzon létre egy szinkronizált tükröt, majd szimulálja a fürt megosztott tárolóját.

A SIOS szoftver telepítése előtt hozza létre a DataKeeperSvc tartományfelhasználót.

> [!NOTE]
> Adja hozzá a DataKeeperSvc tartományfelhasználót a helyi rendszergazda csoporthoz mindkét fürtcsomóponton.
>
>

A SIOS DataKeeper telepítése:

1. Telepítse a SIOS-szoftvert mindkét fürtcsomópontra.

   ![SIOS telepítő][sap-ha-guide-figure-3030]

   ![31. ábra: A SIOS DataKeeper telepítésének első oldala][sap-ha-guide-figure-3031]

   _**31. ábra:** A SIOS DataKeeper telepítésének első oldala_

2. A párbeszédpanelen válassza az **Igen**lehetőséget.

   ![32. ábra: A DataKeeper tájékoztatja, hogy egy szolgáltatás le lesz tiltva][sap-ha-guide-figure-3032]

   _**32. ábra:** A DataKeeper tájékoztatja, hogy egy szolgáltatás le lesz tiltva_

3. A párbeszédpanelen azt javasoljuk, hogy válassza a **Tartomány vagy kiszolgáló fiókot.**

   ![33. ábra: A SIOS DataKeeper felhasználói kiválasztása][sap-ha-guide-figure-3033]

   _**33. ábra:** A SIOS DataKeeper felhasználóválasztása_

4. Adja meg a SIOS DataKeeper számára létrehozott tartományi fiók felhasználónevét és jelszavát.

   ![34. ábra: Adja meg a SIOS DataKeeper telepítéséhez megadott tartomány-felhasználónevet és -jelszót][sap-ha-guide-figure-3034]

   _**34. ábra:** Adja meg a SIOS DataKeeper telepítéséhez megadott tartományfelhasználónevet és jelszót_

5. Telepítse a SIOS DataKeeper példány licenckulcsát a 35.

   ![35. ábra: Adja meg a SIOS DataKeeper licenckulcsát][sap-ha-guide-figure-3035]

   _**35. ábra:** Adja meg a SIOS DataKeeper licenckulcsát_

6. Amikor a rendszer kéri, indítsa újra a virtuális gépet.

### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>A SIOS DataKeeper beállítása

Miután mindkét csomópontra telepítette a SIOS DataKeeper-t, indítsa el a konfigurációt. A konfiguráció célja, hogy szinkron adatreplikációt az egyes virtuális gépekhez csatlakoztatott további lemezek között.

1. Indítsa el a DataKeeper Management and Configuration eszközt, majd válassza **a Kiszolgáló csatlakoztatása**lehetőséget.

   ![36. ábra: A SIOS DataKeeper felügyeleti és konfigurációs eszköze][sap-ha-guide-figure-3036]

   _**36. ábra:** SIOS DataKeeper felügyeleti és konfigurációs eszköz_

2. Adja meg annak az első csomópontnak a nevét vagy TCP/IP-címét, amelyhez a Felügyeleti és konfiguráció eszköznek csatlakoznia kell, és egy második lépésben a második csomóponthoz.

   ![37. ábra: Adja meg annak az első csomópontnak a nevét vagy TCP/IP-címét, amelyhez a Felügyeleti és konfiguráció eszköznek csatlakoznia kell, és egy második lépésben a második csomóponthoz][sap-ha-guide-figure-3037]

   _**37. ábra:** A Felügyeleti és konfigurációs eszköz első csomópontjának nevét vagy TCP/IP-címét szúrja be, és egy második lépésben a második csomópontot._

3. Hozza létre a replikációs feladatot a két csomópont között.

   ![38. ábra: Replikációs feladat létrehozása][sap-ha-guide-figure-3038]

   _**38. ábra:** Replikációs feladat létrehozása_

   A varázsló végigvezeti a replikációs feladat létrehozásának folyamatán.

4. Adja meg a replikációs feladat nevét.

   ![39. ábra: A replikációs feladat nevének meghatározása][sap-ha-guide-figure-3039]

   _**39. ábra:** A replikációs feladat nevének megadása_

   ![40. ábra: Adja meg a csomópont alapadatait, amely nek az aktuális forráscsomópontnak kell lennie.][sap-ha-guide-figure-3040]

   _**40. ábra:** Adja meg a csomópont alapadatait, amely nek az aktuális forráscsomópontnak kell lennie_

5. Adja meg a célcsomópont nevét, TCP/IP-címét és lemezkötetét.

   ![41. ábra: Adja meg az aktuális célcsomópont nevét, TCP/IP-címét és lemezkötetét.][sap-ha-guide-figure-3041]

   _**41. ábra:** Adja meg az aktuális célcsomópont nevét, TCP/IP-címét és lemezkötetét_

6. Adja meg a tömörítési algoritmusokat. A példában azt javasoljuk, hogy tömörítse a replikációs adatfolyamot. Különösen az újraszinkronizálási helyzetekben a replikációs adatfolyam tömörítése jelentősen csökkenti az újraszinkronizálási időt. A tömörítés a virtuális gép PROCESSZOR- és RAM-erőforrásait használja. A tömörítési sebesség növekedésével a felhasznált CPU-erőforrások mennyisége is növekszik. Ezt a beállítást később is módosíthatja.

7. Egy másik beállítás, amelyet ellenőriznie kell, hogy a replikáció aszinkron vagy szinkron módon történik-e. Az SAP ASCS/SCS-konfigurációk védelme esetén szinkron replikációt kell használnia.  

   ![42. ábra: Replikációs adatok meghatározása][sap-ha-guide-figure-3042]

   _**42. ábra:** Replikációs adatok megadása_

8. Adja meg, hogy a replikációs feladat által replikált kötet et megosztott lemezként kell-e a Windows Server feladatátvevő fürt konfigurációjában ábrázolni. Az SAP ASCS/SCS konfiguráció esetében válassza az **Igen** lehetőséget, hogy a Windows-fürt a replikált kötetet megosztott lemezként lássa, amelyet fürtkötetként használhat.

   ![43. ábra: Válassza az Igen lehetőséget a replikált kötet fürtkötetként való beállításához][sap-ha-guide-figure-3043]

   _**43. ábra:** Válassza az **Igen** lehetőséget a replikált kötet fürtkötetként való beállításához_

   A kötet létrehozása után a DataKeeper-kezelés és konfiguráció eszköz azt mutatja, hogy a replikációs feladat aktív.

   ![44. ábra: A DataKeeper szinkron tükrözése az SAP ASCS/SCS megosztási lemezhez aktív][sap-ha-guide-figure-3044]

   _**44. ábra:** Az SAP ASCS/SCS megosztási lemez DataKeeper szinkron tükrözése aktív_

   A feladatátvevőfürt-kezelő mostantól datakeeper lemezként jeleníti meg a lemezt, ahogy az a 45.

   ![45. ábra: A feladatátvevőfürt-kezelő a DataKeeper által replikált lemezt jeleníti meg][sap-ha-guide-figure-3045]

   _**45. ábra:** A feladatátvevőfürt-kezelő a DataKeeper által replikált lemezt jeleníti meg_

## <a name="next-steps"></a>További lépések

* [Az SAP NetWeaver HA telepítése Windows feladatátvételi fürt és megosztott lemez használatával SAP ASCS/SCS-példányhoz][sap-high-availability-installation-wsfc-shared-disk]
