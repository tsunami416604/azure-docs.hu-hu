---
title: SAP NetWeaver magas rendelkezésre állású Windows feladatátvevő fürt és a fájlkiszolgáló-megosztáson található SAP ASCS/SCS-példányok az Azure-ban |} A Microsoft Docs
description: SAP NetWeaver magas rendelkezésre állású Windows feladatátvevő fürt és a fájlkiszolgáló-megosztáson található az SAP ASCS/SCS példányhoz
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 71296618-673b-4093-ab17-b7a80df6e9ac
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4f9628be1d1f1d146ed0dbc5ebd9579f0512aeac
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997362"
---
# <a name="install-sap-netweaver-high-availability-on-a-windows-failover-cluster-and-file-share-for-sap-ascsscs-instances-on-azure"></a>SAP NetWeaver magas rendelkezésre állású telepítése a Windows feladatátvevő fürt és a fájlkiszolgáló-megosztáson található SAP ASCS/SCS-példányok az Azure-ban

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1596496]:https://launchpad.support.sap.com/#/notes/1596496

[sap-installation-guides]:http://service.sap.com/instguides

[sap-powershell-scrips]:https://github.com/Azure-Samples/sap-powershell

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[sap-blog-new-sap-cluster-resource-dll]:https://blogs.sap.com/2017/08/28/new-sap-cluster-resource-dll-is-available/
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]:sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP több biztonsági AZONOSÍTÓVAL magas rendelkezésre állású konfiguráció)


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

Ez a cikk bemutatja, hogyan telepítése és konfigurálása egy magas rendelkezésre állású SAP-rendszerhez az Azure-ban, a Windows Server feladatátvételi fürt (WSFC) és Scale-Out File Server SAP ASCS/SCS-példányok fürtszolgáltatásra vonatkozó beállítás.

## <a name="prerequisites"></a>Előfeltételek

A telepítés megkezdése előtt tekintse át a következő cikkeket:

* [Architektúra-Útmutató: A fájlmegosztás Windows feladatátvevő fürt a fürt egy SAP ASCS/SCS példányhoz][sap-high-availability-guide-wsfc-file-share]

* [Készítse elő az Azure-infrastruktúra SAP magas rendelkezésre állású egy Windows feladatátvevő fürt és -fájlmegosztást az SAP ASCS/SCS példányhoz][sap-high-availability-infrastructure-wsfc-file-share]

Az alábbi végrehajtható fájlok és DLL-eket SAP lesz szüksége:
* SAP Software kiépítés Manager (SWPM) telepítési eszköz verziójának SPS21 vagy újabb.
* Töltse le a legújabb NTCLUST. Az új SAP KKT archív fürterőforrás DLL-fájl. Az új SAP fürt DLL-ek támogatása az SAP ASCS/SCS magas rendelkezésre állású fájlmegosztást a Windows Server feladatátvételi fürtben.

  Az új SAP-fürt erőforrás DLL kapcsolatos további információkért tekintse meg ezt a blogot: [Új SAP fürt erőforrás DLL érhető el! ][sap-blog-new-sap-cluster-resource-dll].

Az adatbázis felügyeleti rendszer (DBMS) telepítő nem azt ismertetik, mert beállításnál használja az adatbázis-kezelő függően változhat. Azonban feltételezzük, hogy az adatbázis-kezelő magas rendelkezésre állású kérdéseket az a funkciók különböző DBMS-gyártók támogató Azure-ig. Ilyen funkciók például AlwaysOn vagy adatbázis-tükrözés az SQL Server és Oracle Data Guard az Oracle-adatbázisokat. Az ebben a cikkben használjuk esetben nagyobb védelmet, az adatbázis-kezelő nem adtuk hozzá.

Nincsenek speciális megfontolásokat különböző DBMS-szolgáltatásokkal interakcióba ilyen konfigurációtípusok fürtözött SAP ASCS/SCS az Azure-ban.

> [!NOTE]
> A telepítési eljárások az SAP NetWeaver ABAP rendszerek, a Java-rendszerek és a ABAP + Java rendszerek majdnem azonosak. A legfontosabb különbség, hogy rendelkezik-e egy SAP ABAP-rendszer egyik ASCS-példány. Az SAP-Java-rendszer egyik SCS példányhoz rendelkezik. Az SAP ABAP + Java rendszer rendelkezik egy ASCS-példány és a egy, a feladatátvételi fürt ugyanazt a Microsoft csoportban futó SCS példányhoz. SAP NetWeaver-telepítés rengetegféle telepítési eltérések explicit módon szerepelnek. Akkor feltételezheti, hogy minden más részek azonosak.  
>
>

## <a name="install-an-ascsscs-instance-on-an-ascsscs-cluster"></a>Az ASCS/SCS-fürtön ASCS/SCS-példány telepítése

> [!IMPORTANT]
>
> Egy megosztás konfiguráció egy magas rendelkezésre állású beállítás az SAP SWPM telepítési eszköz által nem támogatott. Ezért néhány manuális elfogadásához esetében az SAP-rendszer (például a telepítése és a fürt egy SAP ASCS/SCS példányhoz, és a egy különálló SAP globális állomás konfigurálása) telepítéséhez szükséges.  
>
> Az egyéb telepítési lépéseket telepítésének (és a fürt) adatbázis-kezelő nem változik-példány és az SAP-alkalmazáskiszolgálók.
>

### <a name="install-an-ascsscs-instance-on-your-local-drive"></a>Az ASCS/SCS példányhoz telepítése a helyi meghajtón

Telepítse az SAP ASCS/SCS példányhoz *mindkét* a ASCS/SCS-fürt csomópontja. Telepítheti a helyi meghajtón. Ebben a példában a helyi meghajtón a C:\\, de választhat bármely helyi meghajtón.  

A példány, az SAP SWPM telepítési eszközben telepítéséhez nyissa meg:

**\<A termék >** > **\<DBMS >** > **telepítési** > **alkalmazáskiszolgáló ABAP** () vagy **Java**) > **elosztott rendszer** > **ASCS/SCS példányhoz**

> [!IMPORTANT]
> A fájlmegosztás forgatókönyvet az SAP SWPM telepítési eszköz által nem támogatott. Ön *nem használható* a következő telepítési elérési út:
>
> **\<A termék >** > **\<DBMS >** > **telepítési** > **alkalmazáskiszolgáló ABAP** () vagy **Java**) > **magas rendelkezésre állású System** >...
>

### <a name="remove-sapmnt-and-create-an-saploc-file-share"></a>Távolítsa el a SAPMNT, és a egy SAPLOC fájlmegosztás létrehozása

Létrehozott egy SAPMNT helyi megosztás SWMP a C:\\usr\\sap mappát.

Távolítsa el a SAPMNT fájlmegosztást a *mindkét* ASCS/SCS fürtcsomópont.

Hajtsa végre a következő PowerShell-parancsfájlt:

```PowerShell
Remove-SmbShare sapmnt -ScopeName * -Force
 ```

Ha a SAPLOC megosztás nem létezik, hozzon létre egyet a *mindkét* ASCS/SCS fürtcsomópont.

Hajtsa végre a következő PowerShell-parancsfájlt:

```PowerShell
#Create SAPLOC share and set security
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"
$HostName = $env:computername
$SAPLocalAdminGroupName = "$HostName\SAP_LocalAdmin"
$SAPDisk = "C:"
$SAPusrSapPath = "$SAPDisk\usr\sap"

New-SmbShare -Name saploc -Path c:\usr\sap -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName , $SAPLocalAdminGroupName  
 ```

## <a name="prepare-an-sap-global-host-on-the-sofs-cluster"></a>Egy globális SAP-gazdagép az SOFS-fürthöz a előkészítése

A következő kötet és a fájlmegosztást létrehozni az SOFS-fürthöz:

* SAP GLOBALHOST fájl C:\ClusterStorage\Volume1\usr\sap\\<SID>\SYS\ struktúra az SOFS-fürt megosztott kötete (CSV)

* SAPMNT fájlmegosztás

* A fájlmegosztás SAPMNT és a mappában, a teljes körűen biztonsági be:
    * A \<tartomány > \SAP_\<SID > _GlobalAdmin felhasználói csoport
    * Az SAP ASCS/SCS csomópont számítógép-objektumok fürt \<tartomány > \ClusterNode1$ és \<tartomány > \ClusterNode2$

CSV-kötetet létrehozni, tükrözött rugalmassági beállítással, hajtsa végre a következő PowerShell-parancsmagot egy SOFS-fürt csomópont:


```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR1 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```
SAPMNT létrehozásához, és állítsa be a mappa és a fájlmegosztás biztonsági, hajtsa végre a következő PowerShell-parancsfájl egy SOFS-fürt csomópont:

```PowerShell
# Create SAPMNT on file share
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP ASCS/SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP ASCS/SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Create a SAPMNT file share and set share security
New-SmbShare -Name sapmnt -Path $UsrSAPFolder -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName, $ASCSClusterObjectNode1, $ASCSClusterObjectNode2 -ContinuouslyAvailable $false -CachingMode None -Verbose

# Get SAPMNT file share security settings
Get-SmbShareAccess sapmnt

# Set file and folder security
$Acl = Get-Acl $UsrSAPFolder

# Add a file security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add  a security object of the clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add a security object of the clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
 ```
## <a name="stop-ascsscs-instances-and-sap-services"></a>ASCS/SCS-példány és az SAP-szolgáltatások leállítása

Hajtsa végre az alábbi lépéseket:
1. Állítsa le az SAP ASCS/SCS-példányok mindkét ASCS/SCS-fürtcsomópontokon.
2. Az SAP ASCS/SCS Windows-szolgáltatások leállítása **SAP\<SID > _\<példányszám >** mindkét fürtcsomóponton.

## <a name="move-the-sys-folder-to-the-sofs-cluster"></a>Helyezze át a \SYS\... mappát az SOFS-fürthöz

Hajtsa végre az alábbi lépéseket:
1. Másolja a SYS mappát (például C:\usr\sap\\<SID>\SYS) ASCS/SCS egyik fürtcsomópont az SOFS-fürthöz (például, hogy C:\ClusterStorage\Volume1\usr\sap\\<SID>\SYS).
2. Törölje a C:\usr\sap\\<SID>mindkét ASCS/SCS fürtcsomópontok \SYS mappájában található.

## <a name="update-the-cluster-security-setting-on-the-sap-ascsscs-cluster"></a>Az SAP ASCS/SCS-fürt a fürt biztonsági beállításainak frissítése

Hajtsa végre a következő PowerShell-parancsfájlt az SAP ASCS/SCS fürtcsomópontok egyike:

```PowerShell
# Grant <DOMAIN>\SAP_<SID>_GlobalAdmin group access to the cluster

$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# Set full access for the <DOMAIN>\SAP_<SID>_GlobalAdmin group
Grant-ClusterAccess -User $SAPSIDGlobalAdminGroupName -Full

#Check security settings
Get-ClusterAccess
```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Hozzon létre egy virtuális nevet a fürtözött SAP ASCS/SCS példányhoz

Hozzon létre egy SAP ASCS/SCS fürthálózat nevének (például **pr1 – ascs [10.0.6.7]**) leírtak szerint [hozzon létre egy virtuális nevet a fürtözött SAP ASCS/SCS példányhoz] [ sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host] .

## <a name="update-the-default-and-sap-ascsscs-instance-profile"></a>Az alapértelmezett és az SAP ASCS/SCS példányhoz profil frissítése

Az új SAP ASCS/SCS virtuális állomás nevét és a globális állomásnév SAP, frissítenie kell az alapértelmezett és az SAP ASCS/SCS példányhoz profil \<SID >_ASCS/SCS\<Nr >_<Host>.


| Régi értékek |  |
| --- | --- |
| Az SAP ASCS/SCS-állomás neve = SAP globális gazdagép | ascs-1 |
| Az SAP ASCS/SCS példányhoz profil neve | PR1_ASCS00_ascs-1 |

| Új érték |  |
| --- | --- |
| SAP ASCS/SCS host name | **PR1 – ascs** |
| SAP globális gazdagép | **sapglobal** |
| Az SAP ASCS/SCS példányhoz profil neve | PR1\_ASCS00\_**pr1 – ascs** |

### <a name="update-sap-default-profile"></a>SAP alapértelmezett profil frissítése


| Paraméter neve | Paraméter értéke |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| rdisp/mshost | **PR1 – ascs** |
| enque/serverhost | **PR1 – ascs** |

### <a name="update-the-sap-ascsscs-instance-profile"></a>Az SAP ASCS/SCS példányhoz profil frissítése

| Paraméter neve | Paraméter értéke |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| DIR_PROFILE | \\\sapglobal\sapmnt\PR1\SYS\profile |
| _PF | $(DIR_PROFILE) \PR1\_ASCS00_ pr1 – ascs |
| Restart_Program_02 local$(_MS) pf=$(_PF) = | **Indítsa el**_Program_02 local$(_MS) pf=$(_PF) = |
| SAPLOCALHOST | **PR1 – ascs** |
| Restart_Program_03 local$(_EN) pf=$(_PF) = | **Indítsa el**_Program_03 local$(_EN) pf=$(_PF) = |
| az átjáró/netstat_once | **0** |
| enque/encni/set_so_keepalive  | **true** |
| service/ha_check_node | **1** |

> [!IMPORTANT]
>Használhatja a **Update-SAPASCSSCSProfile** PowerShell-parancsmag használatával automatizálhatja a profil frissítéséhez.
>
>A PowerShell-parancsmag támogatja a SAP ABAP ascs rendszerbe fut be- és a SAP Java SCS-példányok.
>

Másolás [ **SAPScripts.psm1** ] [ sap-powershell-scrips] a helyi meghajtó C:\tmp, és futtassa a következő PowerShell-parancsmagot:

```PowerShell
Import-Module C:\tmp\SAPScripts.psm1

Update-SAPASCSSCSProfile -PathToAscsScsInstanceProfile \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_ascs-1 -NewASCSHostName pr1-ascs -NewSAPGlobalHostName sapglobal -Verbose  
```

![1. ábra: SAPScripts.psm1 output][sap-ha-guide-figure-8012]

_**1. ábra**: SAPScripts.psm1 output_

## <a name="update-the-sidadm-user-environment-variable"></a>Frissítés a \<sid > adm felhasználói környezeti változó

1. Frissítés a \<sid > adm felhasználói környezet új GLOBALHOST UNC elérési utat a *mindkét* ASCS/SCS fürtcsomópont.
2. Jelentkezzen be \<sid > adm-felhasználó, és indítsa el a Regedit.exe eszközt.
3. Lépjen a **HKEY_CURRENT_USER** > **környezet**, és frissítse a változókat, az új érték:

| Változó | Érték |
| --- | --- |
| RSEC_SSFS_DATAPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\data |
| RSEC_SSFS_KEYPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\key |
| SAPEXE | \\\\**sapglobal**\sapmnt\PR1\SYS\exe\uc\NTAMD64 |
| SAPLOCALHOST  | **PR1 – ascs** |


## <a name="install-a-new-saprcdll-file"></a>Egy új saprc.dll fájl telepítése

1. A SAP-fürterőforrás, amely támogatja a fájlmegosztás a forgatókönyv új verziójának telepítése.

2. Töltse le a legújabb NTCLUST. Az SAP Service Marketplace-ről (KKT) csomagot.

3. NTCLUS kicsomagolása. Az ASCS/SCS egyik KKT fürtcsomóponton, és futtassa a következő parancsot a parancssorból az új saprc.dll fájl telepítéséhez:

```
.\NTCLUST\insaprct.exe -yes -install
```

Az új saprc.dll fájl mindkét ASCS/SCS fürtcsomóponton telepítve van.

További információkért lásd: [Megjegyzés 1596496 SAP - SAP erőforrástípus DLL-ek frissítése a fürterőforrás-figyelő][1596496].

## <a name="create-a-sap-sid-cluster-group-network-name-and-ip"></a>Hozzon létre egy SAP <SID> csoport, a hálózat nevét és IP-fürt

Egy SAP létrehozása \<SID > fürtcsoport egy ASCS/SCS hálózati név és egy megfelelő IP-címet, futtassa a következő PowerShell-parancsmagot:

```PowerShell
# Create SAP Cluster Group
$SAPSID = "PR1"
$SAPClusterGroupName = "SAP $SAPSID"
$SAPIPClusterResourceName = "SAP $SAPSID IP"
$SAPASCSNetworkName = "pr1-ascs"
$SAPASCSIPAddress = "10.0.6.7"
$SAPASCSSubnetMask = "255.255.255.0"

# Create an SAP ASCS instance virtual IP cluster resource
Add-ClusterGroup -Name $SAPClusterGroupName -Verbose

#Create an SAP ASCS virtual IP address
$SAPIPClusterResource = Add-ClusterResource -Name $SAPIPClusterResourceName -ResourceType "IP Address" -Group $SAPClusterGroupName -Verbose

# Set a static IP address
$param1 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,Address,$SAPASCSIPAddress
$param2 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,SubnetMask,$SAPASCSSubnetMask
$params = $param1,$param2
$params | Set-ClusterParameter

# Create a corresponding network name
$SAPNetworkNameClusterResourceName = $SAPASCSNetworkName
Add-ClusterResource -Name $SAPNetworkNameClusterResourceName -ResourceType "Network Name" -Group $SAPClusterGroupName -Verbose

# Set a network DNS name
$SAPNetworkNameClusterResource = Get-ClusterResource $SAPNetworkNameClusterResourceName
$SAPNetworkNameClusterResource | Set-ClusterParameter -Name Name -Value $SAPASCSNetworkName

#Check the updated values
$SAPNetworkNameClusterResource | Get-ClusterParameter

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPNetworkNameClusterResourceName -Dependency "[$SAPIPClusterResourceName]" -Verbose

#Start an SAP <SID> cluster group
Start-ClusterGroup -Name $SAPClusterGroupName -Verbose
```

## <a name="register-the-sap-start-service-on-both-nodes"></a>Az SAP kezdő szolgáltatás mindkét csomópontjának regisztrálása

Regisztrálja újra az SAP ASCS/SCS kezdő szolgáltatás átirányítása az új profil és a profil elérési útja.

Végre kell hajtani az újraregisztrálás a *mindkét* ASCS/SCS fürtcsomópont.

A rendszergazdai jogú parancssorban futtassa a következő parancsot:

```
C:\usr\sap\PR1\ASCS00\exe\sapstartsrv.exe -r -p \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_pr1-ascs -s PR1 -n 00 -U SAPCLUSTER\SAPServicePR1 -P mypasswd12 -e SAPCLUSTER\pr1adm
```

![2. ábra: SAP szolgáltatás újratelepítése][sap-ha-guide-figure-8013]

_**2. ábra**: SAP szolgáltatás újratelepítése_

Győződjön meg arról, hogy a paraméterek megfelelőek-e, és válassza **manuális** , a **indítási típus**.

## <a name="stop-the-ascsscs-service"></a>Az ASCS/SCS szolgáltatás leállítása

Állítsa le az SAP ASCS/SCS szolgáltatást SAP\<SID > _\<példányszám > mindkét ASCS/SCS fürtcsomópont.

## <a name="create-a-new-sap-service-and-sap-instance-resources"></a>Hozzon létre egy új SAP-szolgáltatás és az SAP-példány erőforrások

Az SAP az SAP-erőforrások létrehozásának véglegesítéséhez\<SID > fürtcsoportot, hozza létre a következő erőforrásokat:

* SAP \<SID > \<példányszám > szolgáltatás
* SAP \<SID > \<példányszám > példány

Futtassa a következő PowerShell-parancsmagot:

```PowerShell
$SAPSID = "PR1"
$SAPInstanceNumber = "00"
$SAPNetworkNameClusterResourceName = "pr1-ascs"

$SAPServiceName = "SAP$SAPSID"+ "_" + $SAPInstanceNumber

$SAPClusterGroupName = "SAP $SAPSID"
$SAPServiceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Service"

$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPServiceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Service" -SeparateMonitor -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name ServiceName -Value $SAPServiceName

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPNetworkNameClusterResourceName]" -Verbose

$SAPInstanceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Instance"

# Create SAP instance cluster resource
$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPInstanceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Resource" -SeparateMonitor -Verbose

#Set SAP instance cluster resource parameters
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystemName -Value $SAPSID -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystem -Value $SAPInstanceNumber -Verbose

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPServiceClusterResourceName]" -Verbose
```

## <a name="add-a-probe-port"></a>Adja hozzá a mintavételi port

Egy SAP-erőforrás, a SAP-SID-IP-mintavételi port konfigurálása a PowerShell használatával. Hajtsa végre ezt a konfigurációt az egyik az SAP ASCS/SCS fürtcsomópontok leírtak szerint [ebben a cikkben][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].

## <a name="install-an-ers-instance-on-both-cluster-nodes"></a>SSZON példányát telepítse mindkét fürtcsomóponton

Egy sorba replikációs kiszolgáló (SSZON) példány telepíthető *mindkét* a ASCS/SCS-fürt csomópontja. A SWPM menüben kövesse a telepítési útvonalon:

**\<A termék >** > **\<DBMS >** > **telepítési** > **további SAP-rendszerhez példányok**  >  **Sorba replikációs Server-példány**

## <a name="install-a-dbms-instance-and-sap-application-servers"></a>Egy adatbázis-kezelő-példány és az SAP-alkalmazáskiszolgálókhoz telepítése

Az SAP-rendszer telepítésének véglegesítéséhez telepítése:
* DBMS-példány.
* Egy elsődleges SAP-alkalmazáskiszolgáló.
* An additional SAP application server.

## <a name="next-steps"></a>További lépések

* [Nem megosztott lemez - hivatalos SAP-útmutató a magas rendelkezésre állású fájlmegosztást a feladatátvevő fürt telepítése egy ASCS/SCS példányhoz][sap-official-ha-file-share-document]

* [A Windows Server 2016 közvetlen tárolóhelyek szolgáltatása][s2d-in-win-2016]

* [Kibővített fájlkiszolgáló alkalmazásadatokhoz – áttekintés][sofs-overview]

* [What's new in Windows Server 2016 storage szolgáltatásában][new-in-win-2016-storage]
