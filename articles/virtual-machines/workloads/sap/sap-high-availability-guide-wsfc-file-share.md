---
title: Az SAP ASCS/SCS példányhoz Windows feladatátvevő fürtre a fürt az Azure-beli fájlmegosztás használatával |} A Microsoft Docs
description: Útmutató az SAP ASCS/SCS példányhoz Windows feladatátvevő fürtre a fürt az Azure-beli fájlmegosztás használatával.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6c45ca62c1b734dcbcf854fdea4aa44e603d3908
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55693824"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

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


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 

# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Az SAP ASCS/SCS példányhoz Windows feladatátvevő fürtre a fürt az Azure-beli fájlmegosztás használatával

> ![Windows][Logo_Windows] Windows
>

A Windows Server feladatátvételi fürtszolgáltatás az alapja egy magas rendelkezésre állású SAP ASCS/SCS telepítés és a Windows DBMS.

Egy feladatátvevő fürt egy 1 + n különálló kiszolgálók csoportja, (csomópontok), amelyek együttműködése magasabb az alkalmazások és szolgáltatások rendelkezésre állását. Egy csomópont meghibásodása esetén a Windows Server feladatátvételi fürtszolgáltatás a hibák, amelyek fordulhat elő, és továbbra is biztosít az alkalmazások és szolgáltatások megfelelő fürt számítja ki. A Feladatátvételi fürtszolgáltatás eléréséhez különböző kvórummódok közül választhat.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené az ebben a cikkben leírt feladatok, tekintse át az ebben a cikkben:

* [Az Azure virtuális gépek magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver számára][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Fürtszolgáltatás SAP ASCS/SCS-példányok egy fájlmegosztás használatával támogatott SAP NetWeaver 7.40 (és újabb), az SAP-Kernel 7.49 (és újabb).
>


## <a name="windows-server-failover-clustering-in-azure"></a>A Windows Server feladatátvevő fürt az Azure-ban

Operációs rendszer nélküli vagy magánfelhő-telepítések képest, Azure Virtual Machines konfigurálása a Windows Server feladatátvételi fürtszolgáltatás további lépéseket igényel. Ha egy fürtöt hoz létre, több IP-címek és virtuális állomásnevek az SAP ASCS/SCS példányhoz be kell.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>A névfeloldás az Azure és a fürt virtuális állomás neve

Az Azure felhőalapú platformján nem teszi lehetővé a virtuális IP-címek, például a nem fix IP-címek konfigurálásához. Alternatív megoldásként elérni a fürt erőforrásai a felhőben egy virtuális IP-cím beállításához szüksége lesz. 

Az Azure Load Balancer szolgáltatás biztosít egy *belső load balancer* az Azure-hoz. A belső terheléselosztó az ügyfelek a fürt a fürt virtuális IP-címen keresztül érhető el. 

Helyezze üzembe a belső terheléselosztó, amely tartalmazza a fürt csomópontjai az erőforráscsoportban. Ezt követően konfigurálja a belső terheléselosztó portok továbbítási szabályok a mintavétel használatával minden szükséges port. Az ügyfelek csatlakozhatnak-n keresztül a virtuális gazdagép nevét. A DNS-kiszolgáló szünteti meg a fürt IP-címét. A belső terheléselosztó porttovábbítást a fürt az aktív csomópontra végzi.

![1. ábra: A Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban egy megosztott lemez nélkül][sap-ha-guide-figure-1001]

_**1. ábra:** A Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban anélkül, hogy egy megosztott lemez_

## <a name="sap-ascsscs-ha-with-file-share"></a>Az SAP ASCS/SCS magas rendelkezésre ÁLLÁS fájlmegosztást

SAP fejlesztett új megközelítést, és a fürt megosztott lemezeinek, az SAP ASCS/SCS példányhoz fürtszolgáltatás a Windows feladatátvevő fürt helyett. Ahelyett, hogy a fürt megosztott lemezeket, SMB-fájlmegosztás segítségével SAP globális gazdagép fájlok üzembe helyezése.

> [!NOTE]
> SMB-fájlmegosztást a fürt megosztott lemezt használ a fürtszolgáltatás SAP ASCS/SCS-példányok alternatívája.  
>

Ez az architektúra olyan egyedi a következő módon:

* SAP central services (a saját fájl struktúra és az üzenet és a sorba folyamatok) elkülönülnek az SAP globális gazdagép fájlokat.
* Az SAP central services egy SAP ASCS/SCS példányhoz futni.
* Az SAP ASCS/SCS példányhoz fürtözve van, és segítségével érhetőek el a \<ASCS/SCS virtuális állomásnevet\> virtuális állomás neve.
* SAP globális fájlok az SMB-fájlmegosztás kerülnek, és használatával elért a \<SAP globális gazdagép\> állomásnév: \\\\&lt;SAP global host&gt;\sapmnt\\&lt;SID&gt;\SYS\...
* Az SAP ASCS/SCS példányhoz egy helyi lemez mindkét fürtcsomóponton telepítve van.
* A \<ASCS/SCS virtuális állomásnevet\> hálózati neve eltér &lt;SAP globális gazdagép&gt;.

![2. ábra: Az SAP ASCS/SCS magas rendelkezésre ÁLLÁS architektúra az SMB-fájlmegosztás][sap-ha-guide-figure-8004]

_**2. ábra:** Új SAP ASCS/SCS magas rendelkezésre ÁLLÁS architektúra az SMB-fájlmegosztásra_

SMB-fájlmegosztásra vonatkozó Előfeltételek:

* SMB 3.0-s (vagy újabb) protokollt.
* Lehetővé teszi állíthatók be az Active Directory hozzáférés-vezérlési listák (ACL) az Active Directory felhasználói csoportok számára, és a `computer$` számítógép-objektumot.
* A fájlmegosztás magas rendelkezésre ÁLLÁSÚ engedélyezve kell lennie:
    * Fájlok tárolására használt lemezek nem lehet olyan hibaérzékeny pont.
    * Kiszolgáló vagy virtuális gépek üzemszünete nem okoz állásidőt a fájlmegosztáson.

Az SAP \<SID\> fürtszerepkör nem tartalmazza a fürt megosztott lemezeket vagy általános fájlmegosztás fürt erőforrást.


![3. ábra: SAP \<SID\> szerepkör erőforrásokat egy fájlmegosztást a fürt][sap-ha-guide-figure-8005]

_**3. ábra:** SAP &lt;SID&gt; szerepkör erőforrásokat egy fájlmegosztást a fürt_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Kibővíthető fájlmegosztások a közvetlen tárolóhelyek szolgáltatással az Azure-ban, mivel egy SAPMNT fájlmegosztás

Használhatja egy kibővített fájlmegosztást üzemeltethet, és az SAP globális gazdagép fájlok védelmét. Egy kibővített fájlmegosztást SAPMNT fájl megosztási szolgáltatás magas rendelkezésre állást is kínál.

![4. ábra: SAP globális gazdagép fájlok védelmére szolgáló kibővített fájlkiszolgáló-megosztás][sap-ha-guide-figure-8006]

_**4. ábra:** Egy kibővített fájlmegosztást SAP globális gazdagép fájlok védelmére szolgáló_

> [!IMPORTANT]
> Kibővíthető fájlmegosztások teljes mértékben támogatottak, a Microsoft Azure-felhőben, és a helyszíni környezetben.
>

Egy kibővített fájlmegosztást egy magas rendelkezésre állású, vízszintesen méretezhető SAPMNT fájlmegosztás kínál.

A közvetlen tárolóhelyek használatos megosztott lemez egy kibővített fájlmegosztást. Használhatja a közvetlen tárolóhelyek helyi tárterülettel rendelkező kiszolgálók használata magas rendelkezésre állású és méretezhető tárolási hozhat létre. Megosztott tároló, amely egy kibővített fájlmegosztást szolgál, az SAP globális gazdagép-fájlok, például, nem egy meghibásodási pont.

> [!IMPORTANT]
>Ha Ön *nem* beállításához vész-helyreállítási terv egy kibővített fájlmegosztást megoldásként az Azure-ban magas rendelkezésre állású fájlmegosztás használatát javasoljuk.
>

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>Kibővített fájlmegosztásokat az Azure-beli SAP előfeltételei

Egy kibővített fájlmegosztást használ, a rendszer a következő követelményeknek kell megfelelnie:

* Legalább két fürtcsomópont kibővített fájlmegosztáshoz.
* Minden egyes csomópont legalább két helyi lemezzel kell rendelkeznie.
* Teljesítmény okból kell használnia *rugalmasság tükrözés*:
    * Kétutas tükrözés két fürt csomópontjai a kibővített fájlmegosztáshoz.
    * Háromutas tükrözés három (vagy több) fürtcsomópont a kibővített fájlmegosztáshoz.
* Azt javasoljuk, hogy a kibővített fájlmegosztáshoz, a háromirányú tükrözés három (vagy több) fürtcsomópont.
    Ez a beállítás nagyobb méretezhetőséget is jelent, és további tárolási rugalmasságot, mint a kibővített fájlkiszolgáló fájlmegosztás beállítása két fürt csomópontjai és kétirányú tükrözés kínál.
* Prémium szintű lemezeket kell használnia.
* Azt javasoljuk, hogy az Azure Managed Disks szolgáltatást.
* Azt javasoljuk, hogy a kötetek Resilient File System (ReFS) használatával formázhatja.
    * További információkért lásd: [SAP Megjegyzés 1869038 – az SAP támogatása az ReFs fájlrendszer] [ 1869038] és a [kiválasztása a fájlrendszer] [ planning-volumes-s2d-choosing-filesystem] fejezete az a cikk tervezési kötetek a közvetlen tárolóhelyek.
    * Győződjön meg, hogy telepítse [összegző frissítés a Microsoft KB4025334][kb4025334].
* DS vagy DSv2 sorozatú Azure-beli Virtuálisgép-méretek is használhatja.
* A közvetlen tárolóhelyek lemez szinkronizálási van szüksége, virtuális gépek közötti megfelelő minőségű hálózati teljesítmény használja egy VM-típus, amely rendelkezik legalább egy "nagy" hálózati sávszélesség.
    További információkért lásd: a [DSv2-sorozat] [ dv2-series] és [DS sorozatú] [ ds-series] előírásoknak.
* Azt javasoljuk, hogy a néhány a tárolókészletben lefoglalt kapacitás foglalása. Néhány nem lefoglalt kapacitás és a tárolókészlet lehetővé teszi a kötetek hely a "helyi javításnak" Ha egy meghajtó meghibásodik. Ez javítja az adatok biztonságát és teljesítményét.  További információkért lásd: [kötet méretének kiválasztása][choosing-the-size-of-volumes-s2d].
* Azure virtuális gépek kibővített fájlkiszolgáló megosztást a saját Azure rendelkezésre állási csoportban kell telepíteni.
* Nem kell a kibővített fájlkiszolgáló megosztást hálózat nevét, az Azure belső terheléselosztó konfigurálása például az \<SAP globális gazdagép\>. Ebben az esetben a \<ASCS/SCS virtuális állomás neve\> SAP ASCS/SCS-példány, vagy az adatbázis-kezelő számára. Egy kibővített fájlmegosztást elvégzi a horizontális felskálázást a terhelés több fürt összes csomópontján. \<SAP globális gazdagép\> helyi IP-címet használja, az összes fürtcsomópont.


> [!IMPORTANT]
> Nem lehet átnevezni a SAPMNT fájlmegosztás, amely \<SAP globális gazdagép\>. SAP támogatja a csak a megosztás neve "sapmnt."
>
> További információkért lásd: [SAP-Jegyzetnek 2492395 - tudja módosítani a megosztási név sapmnt?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Konfigurálja az SAP ASCS/SCS-példányok és a egy kibővített fájlkiszolgáló két fürtben megosztása

Telepíthet egy fürtben, a saját SAP az SAP ASCS/SCS példányok \<SID\> fürtszerepkört. Ebben az esetben konfigurálnia a kibővített fájlmegosztáshoz egy másik fürtre, a fürt egy másik szerepkört.

> [!IMPORTANT]
>Ebben a forgatókönyvben az SAP ASCS/SCS példányhoz van konfigurálva, a SAP globális gazdagép eléréséhez UNC elérési úttal \\ \\ &lt;SAP globális gazdagép&gt;\sapmnt\\&lt;SID&gt;\SYS\.
>

![5. ábra: Az SAP ASCS/SCS példányhoz, és a egy kibővített fájlmegosztást két fürtben üzembe helyezett][sap-ha-guide-figure-8007]

_**5. ábra:** Az SAP ASCS/SCS példányhoz, és a egy kibővített fájlmegosztást két fürtben üzembe helyezett_

> [!IMPORTANT]
> Az Azure-felhő SAP és a kibővített fájlkiszolgáló megosztások telepíteni kell a saját Azure rendelkezésre állási használt minden egyes fürt állítson be. Ez biztosítja, hogy a fürt virtuális gépein az alapul szolgáló Azure-infrastruktúra között elosztott elhelyezését.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Az SIOS DataKeeper, a fürt megosztott lemezeinek általános fájlmegosztás


> [!IMPORTANT]
> Azt javasoljuk, hogy egy magas rendelkezésre állású fájlmegosztást a kibővített fájlkiszolgáló megosztást megoldás.
>
> Ha azt tervezi, a magas rendelkezésre állású fájlmegosztás vész-helyreállítási is beállíthat, a fürt megosztott lemezekhez egy általános fájlmegosztás és SISO DataKeeper kell használnia.
>

Általános fájlmegosztás egy másik lehetőség egy magas rendelkezésre állású fájlmegosztás eléréséhez.

Ebben az esetben a fürt megosztott lemez egy külső SIOS megoldás is használhatja.

## <a name="next-steps"></a>További lépések

* [Készítse elő az Azure-infrastruktúra az SAP magas rendelkezésre ÁLLÁS az SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürt és a fájlmegosztást segítségével][sap-high-availability-infrastructure-wsfc-file-share]
* [Telepítse az SAP NetWeaver magas rendelkezésre ÁLLÁS Windows feladatátvevő fürt és a fájlkiszolgáló-megosztáson található egy SAP ASCS/SCS példányhoz][sap-high-availability-installation-wsfc-shared-disk]
* [Fájlkiszolgálók üzembe helyezése két csomópontot a közvetlen tárolóhelyek kibővített UPD tárolás az Azure-ban][deploy-sofs-s2d-in-azure]
* [A Windows Server 2016 közvetlen tárolóhelyek szolgáltatása][s2d-in-win-2016]
* [Részletes bemutatása: A közvetlen tárolóhelyek a kötetek][deep-dive-volumes-in-s2d]
