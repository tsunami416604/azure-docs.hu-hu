---
title: "Az SAP magas rendelkezésre ÁLLÁSÚ Windows feladatátvevő fürt és a fájlmegosztás SAP (A) SCS-példány használata az Azure infrastruktúra előkészítése |} Microsoft Docs"
description: "Az SAP magas rendelkezésre ÁLLÁSÚ Windows feladatátvevő fürt és a fájlmegosztás SAP (A) SCS-példány használata az Azure infrastruktúra előkészítése"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 2ce38add-1078-4bb9-a1da-6f407a9bc910
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2468b5d0996fee5e0106d0d314c16654558e9f4
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="azure-infrastructure-preparation-for-sap-ha-using-windows-failover-cluster-and-file-share-for-sap-ascs-instance"></a>Az SAP magas rendelkezésre ÁLLÁSÚ Windows feladatátvevő fürt és a fájlmegosztás SAP (A) SCS-példány használata az Azure infrastruktúra előkészítése

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[ms-blog-s2d-in-azure]:https://blogs.technet.microsoft.com/filecab/2016/05/05/s2dazuretp5/
[arm-sofs-s2d-managed-disks]:https://github.com/robotechredmond/301-storage-spaces-direct-md
[arm-sofs-s2d-non-managed-disks]:https://github.com/Azure/azure-quickstart-templates/tree/master/301-storage-spaces-direct
[deploy-cloud-witness]:https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness

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
[sap-high-availability-infrastructure-wsfc-shared-disk-azure-network]:sap-high-availability-infrastructure-wsfc-shared-disk.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a
[sap-high-availability-infrastructure-wsfc-shared-disk-dns-ip]:sap-high-availability-infrastructure-wsfc-shared-disk.md#b22d7b3b-4343-40ff-a319-097e13f62f9e
[sap-ascs-high-availability-multi-sid-wsfc-set-static-ip]:sap-high-availability-infrastructure-wsfc-shared-disk.md#84c019fe-8c58-4dac-9e54-173efd4b2c30
[sap-high-availability-infrastructure-wsfc-shared-disk-set-static-ip-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e
[sap-high-availability-infrastructure-wsfc-shared-disk-default-ascs-ilb-rules]:sap-high-availability-infrastructure-wsfc-shared-disk.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-high-availability-infrastructure-wsfc-shared-disk-change-ascs-ilb-rules]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-add-win-domain]:sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c
[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md

[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f



[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


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

Ez a dokumentum leíró van az Azure-infrastruktúra előkészítő lépések, telepítése és konfigurálása a magas rendelkezésre álló SAP rendszer a szükséges **Windows feladatátvételi fürt (WSFC)**használatával **méretezési kimenő fájlmegosztás** , egy a fürtszolgáltatás SAP (A) SCS példány lehetőséget.

## <a name="prerequisite"></a>Előfeltétel

Mindenképpen tekintse át ezeket a dokumentumokat a telepítés megkezdése előtt:

* [Architektúra-útmutató - fürtszolgáltatás (A) SCS példány SAP a **Windows feladatátvevő fürt** használatával **fájlmegosztás**][sap-high-availability-guide-wsfc-shared-disk]


## <a name="host-names-and-ip-addresses"></a>Állomásnevet és IP-címek

| Virtuális állomás neve szerepkör | Virtuális állomás neve | Statikus IP-cím | Rendelkezésre állási csoport |
| --- | --- | --- | --- |
| SCS fürt első fürt csomópont (A) | Asc-1 | 10.0.6.4 | Asc-szerint |
| Második fürt csomópont (A) SCS fürt | Asc-2 | 10.0.6.5 | Asc-szerint |
| Fürt hálózati név |Asc – cl | 10.0.6.6 | n.a |
| SAP PR1 ASC a fürt hálózati neve |PR1-ASC | 10.0.6.7 | n.a |


**1. táblázat:** (A) SCS fürt

| SAP &lt;SID&gt; | SAP (A) SCS példányszámának |
| --- | --- |
| PR1 | 00 |

**2. táblázat:** SAP (A) SCS példány részletei


| Virtuális állomás neve szerepkör | Virtuális állomás neve | Statikus IP-cím | Rendelkezésre állási csoport |
| --- | --- | --- | --- |
| Első fürtcsomópontra | Kibővíthető fájlkiszolgáló-1 | 10.0.6.10 | Kibővíthető fájlkiszolgáló-, |
| Második fürtcsomópont | Kibővíthető fájlkiszolgáló-2 | 10.0.6.11 | Kibővíthető fájlkiszolgáló-, |
| Harmadik fürtcsomópont | Kibővíthető fájlkiszolgáló-3 | 10.0.6.12 | Kibővíthető fájlkiszolgáló-, |
| Fürt hálózati név | Kibővíthető fájlkiszolgáló-cl | 10.0.6.13 | n.a |
| SAP globális állomásneve | sapglobal | A fürt összes csomópontján IP-címek használata | n.a |

**3. táblázat:** kibővíthető Fájlkiszolgáló fürt


## <a name="deploy-vms-for-sap-ascs-cluster-dbms-cluster-and-sap-application-servers"></a>Virtuális gépek telepítése SAP (A) SCS fürt, az adatbázis-kezelő fürt és az SAP alkalmazáskiszolgálók

Azure-infrastruktúra előkészítéséhez kövesse az alábbi lépéseket:
* [Az infrastruktúra előkészítése architekturális sablon 1, 2 és 3][sap-high-availability-infrastructure-wsfc-shared-disk]

* [Azure-beli virtuális hálózat][sap-high-availability-infrastructure-wsfc-shared-disk-azure-network]

* [DNS-IP-címek][sap-high-availability-infrastructure-wsfc-shared-disk-dns-ip]

* [Állítsa be a statikus IP-címeket az SAP virtuális gépekhez][sap-ascs-high-availability-multi-sid-wsfc-set-static-ip]

* [Egy statikus IP-cím beállítása az Azure belső terheléselosztóhoz][sap-high-availability-infrastructure-wsfc-shared-disk-set-static-ip-ilb]

* [Alapértelmezett ASC/SCS terheléselosztási szabályok az Azure belső terheléselosztóhoz][sap-high-availability-infrastructure-wsfc-shared-disk-default-ascs-ilb-rules]

* [Módosítsa a ASC/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztóhoz][sap-high-availability-infrastructure-wsfc-shared-disk-change-ascs-ilb-rules]

*  [Windows virtuális gépek felvétele a tartomány hozzáadása beállításjegyzék-bejegyzések az SAP ASC/SCS példány mindkét fürtcsomóponton][sap-high-availability-infrastructure-wsfc-shared-disk-add-win-domain]

* Windows Server 2016 használata javasolt konfigurálása [Azure felhőbe tanúsító][deploy-cloud-witness]


## <a name="deploy-scale-out-file-server-manually"></a>Kibővíthető fájlkiszolgáló telepítése manuálisan 

Manuálisan kibővíthető Fájlkiszolgáló fürtök telepítése a blogbejegyzésben leírtaknak [közvetlen tárolóhelyek az Azure-ban][ms-blog-s2d-in-azure]:  


```PowerShell
# Set on Execution Policy  ALL cluster nodes!
Set-ExecutionPolicy Unrestricted

# Defines SOFS cluster nodes
$nodes = ("sofs-1", "sofs-2", "sofs-3")

# Add cluster and SOFS features
Invoke-Command $nodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools -Verbose}

# Test cluster
Test-Cluster -node $nodes -Verbose

#Install cluster
$ClusterNetworkName = "sofs-cl"
$ClusterIP = "10.0.6.13"
New-Cluster -Name $ClusterNetworkName -Node $nodes –NoStorage –StaticAddress $ClusterIP -Verbose

# Set Azure Quorum
Set-ClusterQuorum –CloudWitness –AccountName gorcloudwitness -AccessKey <YourAzureStorageAccessKey>

# Enable Storage Spaces Direct S2D
Enable-ClusterS2D

# Create SOFS with SAP Global Host Name
# SAPGlobalHostName
$SAPGlobalHostName = "sapglobal"
Add-ClusterScaleOutFileServerRole -Name $SAPGlobalHostName
```

## <a name="deploy-scale-out-file-server-automatically"></a>Automatikus központi telepítés kibővíthető fájlkiszolgáló

Akkor is, **automatizálásához** Azure Resource Manager-sablonok használatával egy meglévő virtuális hálózat és az Active Directory környezetben kibővíthető Fájlkiszolgáló telepítése:

> [!IMPORTANT]
>Javasoljuk, hogy a 3 (vagy több fürt) és 3 irányú tükrözés kibővíthető Fájlkiszolgáló-csomópont.
>
>Ezért a kibővíthető Fájlkiszolgáló Resource Manager-sablon felhasználói felületén meg kell adnia a virtuális gépek száma.
>

### <a name="using-managed-disks"></a>Felügyelt lemezekkel

Kibővíthető fájlkiszolgálóval (sofs-sel) közvetlen tárolóhelyek (S2D) és az Azure Managed lemezek telepítése Azure Resource Manager-sablon érhető el a [Github][arm-sofs-s2d-managed-disks].

Felügyelt lemezek használata ajánlott.

![1. ábra: Felhasználói felület képernyő felügyelt lemezzel SOFS Resource Manager-sablon][sap-ha-guide-figure-8010]

_**1. ábra:** SOFS Resource Manager sablon felügyelt lemezzel rendelkező felhasználói felület képernyője_

Virtuális gépek száma legalább 2, a lemez száma legalább 2 + 1 késztartalék lemezt = 3, SAP globális gazdagép hálózati neve **sapglobalhost** és fájlmegosztás **sapmnt**.

### <a name="using-non-managed-disks"></a>Nem kezelt lemezekkel

A kibővített fájlkiszolgálóval (sofs-sel) a tárolóhelyek közvetlen (S2D) és Azure Non-Managed lemezek telepítése Azure Resource Manager-sablon érhető [Github][arm-sofs-s2d-non-managed-disks].

![2. ábra: Felhasználói felület képernyő nélkül felügyelt lemezek SOFS Azure Resource Manager-sablon][sap-ha-guide-figure-8011]

_**2. ábra:** SOFS Azure Resource Manager sablon nélkül felügyelt lemezek felhasználói felület képernyője_

Ügyeljen arra, hogy válasszon **prémium szintű Storage** tárolási típusként. Egyéb beállítások ugyanazok, mint a felügyelt lemezek.

## <a name="next-steps"></a>Következő lépések

* [SAP NetWeaver magas rendelkezésre ÁLLÁSÚ telepítés a Windows feladatátvevő fürt és a fájlmegosztás SCS példány SAP (A)][sap-high-availability-installation-wsfc-file-share]
