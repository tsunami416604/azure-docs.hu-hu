---
title: "SAP NetWeaver magas rendelkezésre állású Windows feladatátvevő fürt és a fájlkiszolgáló-megosztáson található SAP ASC/SCS-példányok az Azure-on |} Microsoft Docs"
description: "SAP NetWeaver magas rendelkezésre állású Windows feladatátvevő fürt és a fájlkiszolgáló-megosztáson található SAP ASC/SCS-példányok"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 71296618-673b-4093-ab17-b7a80df6e9ac
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b7b403518c75c72b68957f94dcac750cd922f6bc
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="install-sap-netweaver-high-availability-on-a-windows-failover-cluster-and-file-share-for-sap-ascsscs-instances-on-azure"></a>SAP NetWeaver magas rendelkezésre állással telepített Windows feladatátvevő fürt és a fájlkiszolgáló-megosztáson található SAP ASC/SCS-példányok az Azure-on

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1596496]:https://launchpad.support.sap.com/#/notes/1596496

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

A cikkből megtudhatja, hogyan kell telepíteni és konfigurálni egy magas rendelkezésre állású SAP Azure, a Windows Server feladatátvételi fürt (WSFC) és Scale-Out File Server SAP ASC/SCS példányok fürtözéshez lehetőség.

## <a name="prerequisites"></a>Előfeltételek

A telepítés megkezdése előtt olvassa el a következő cikkeket:

* [Architektúra-Útmutató: Windows feladatátvevő fürt egy SAP ASC/SCS példány fürt fájlmegosztás használatával][sap-high-availability-guide-wsfc-file-share]

* [Készítse elő az Azure-infrastruktúra SAP magas rendelkezésre állású egy Windows feladatátvevő fürt és a fájlmegosztást a SAP ASC/SCS-példányok][sap-high-availability-infrastructure-wsfc-file-share]

A következő futtatható fájljainak és DLL-eket SAP lesz szüksége:
* SAP szoftver kiépítés Manager (SWPM) telepítési eszköz, verziószám: SPS21 vagy újabb.
* Töltse le a legfrissebb NTCLUST. Az új SAP KKT archív fürterőforrás DLL-Fájljában. Az új SAP fürt DLL-ek támogatása SAP ASC/SCS magas rendelkezésre állású fájlmegosztást a Windows Server feladatátvevő fürtön.

  Az új SAP-fürt erőforrás DLL kapcsolatos további információkért lásd: Ebben a blogban: [új SAP fürt erőforrás DLL!] [sap-blog-new-sap-cluster-resource-dll].

Az adatbázis felügyeleti rendszer (DBMS) telepítő nem azt ismertetik, mert beállítások változhatnak attól függően használja az adatbázis-kezelő. Azonban feltételezzük, hogy az adatbázis-kezelő magas rendelkezésre állású kérdéseket az adatbázis-kezelő számos különböző szállító támogató Azure-beli funkciókkal rendelkező tárgyalja. Ilyen funkciói közé tartoznak az AlwaysOn vagy az adatbázis-tükrözést az SQL Server és Oracle Data Guard az Oracle-adatbázisok. A forgatókönyvben a cikkben használjuk, azt nem további védelem bekapcsolása a az adatbázis-kezelő.

Nincsenek semmilyen külön odafigyelést különböző adatbázis-kezelő szolgáltatásokhoz fürtözött SAP ASC/SCS konfigurálása az Azure-ban az ilyen kommunikál.

> [!NOTE]
> A telepítési eljárásokat az SAP NetWeaver ABAP rendszerek, Java, és a ABAP + Java rendszerek csaknem azonosak. A legfontosabb különbség, hogy rendelkezik-e az SAP ABAP rendszer ASC egy példánya. Az SAP Java rendszer egy SCS példány van. Az SAP ABAP + Java rendszer rendelkezik egy ASC példány és egy SCS példány fut a Microsoft feladatátvevő fürt csoporton belül. Összes telepítési különbséget minden SAP NetWeaver telepítési verem explicit módon szerepelnek. Feltételezzük, hogy minden egyéb részeinek megegyeznek.  
>
>

## <a name="install-an-ascsscs-instance-on-an-ascsscs-cluster"></a>Egy ASC/SCS példányát telepítenie, ASC/SCS fürtökön

> [!IMPORTANT]
>
> Egy magas rendelkezésre állású fájlkiszolgáló-megosztás konfigurációja olyan beállítást az SAP SWPM telepítési eszköz által nem támogatott. Ezért néhány manuális bevezetését, egy SAP rendszer (például telepítési és a fürt egy SAP ASC/SCS példány és egy külön SAP globális állomás konfigurálása) telepítéséhez szükséges.  
>
> Nincs más telepítési lépéseket egy adatbázis-kezelő telepítésének (és a fürt) a változás-példány és az SAP alkalmazáskiszolgálók.
>

### <a name="install-an-ascsscs-instance-on-your-local-drive"></a>Egy ASC/SCS példányát telepítenie, a helyi meghajtón

SAP ASC/SCS példánya telepíthető *mindkét* a ASC/SCS fürt csomópontjait. Telepítse a helyi meghajtón. A fenti példában a helyi meghajtó a C:\\, de választhat bármely helyi meghajtón.  

A példány, a SAP SWPM telepítési eszköz telepítéséhez Ugrás:

**\<A termék >** > **\<DBMS >** > **telepítési** > **alkalmazáskiszolgáló ABAP**(vagy **Java**) > **rendszer elosztott** > **ASC/SCS példány**

> [!IMPORTANT]
> A fájlmegosztás forgatókönyvet az SAP SWPM telepítési eszköz által nem támogatott. Ön *nem használható* a következő telepítési elérési út:
>
> **\<A termék >** > **\<DBMS >** > **telepítési** > **alkalmazáskiszolgáló ABAP**(vagy **Java**) > **magas rendelkezésre állású rendszer** >...
>

### <a name="remove-sapmnt-and-create-an-saploc-file-share"></a>Távolítsa el a SAPMNT és SAPLOC fájlmegosztás létrehozása

SWMP egy SAPMNT helyi fájlmegosztás létrehozása a c:\\usr\\sap mappát.

Távolítsa el a SAPMNT fájlmegosztást a *mindkét* ASC/SCS fürtcsomópontok.

Hajtsa végre a következő PowerShell parancsfájlt:

```PowerShell
Remove-SmbShare sapmnt -ScopeName * -Force
 ```

Ha a SAPLOC megosztás nem létezik, hozzon létre egyet a *mindkét* ASC/SCS fürtcsomópontok.

Hajtsa végre a következő PowerShell parancsfájlt:

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

## <a name="prepare-an-sap-global-host-on-the-sofs-cluster"></a>A kibővíthető Fájlkiszolgáló fürtön egy SAP globális gazdagép előkészítése

Hozza létre a következő kötet és a fájlmegosztást a kibővíthető Fájlkiszolgáló fürtön:

* SAP GLOBALHOST fájl C:\ClusterStorage\Volume1\usr\sap\\<SID>\SYS\ struktúra kibővíthető Fájlkiszolgáló fürt megosztott kötete (CSV)

* SAPMNT fájlmegosztás

* A biztonság beállítása a SAPMNT fájlmegosztás és a teljes körű hozzáféréssel rendelkező mappába:
    * A \<TARTOMÁNYI > \SAP_\<SID > _GlobalAdmin felhasználói csoport
    * Az SAP ASC/SCS fürt csomópont számítógép-objektumok \<TARTOMÁNYI > \ClusterNode1$ és \<TARTOMÁNYI > \ClusterNode2$

A megosztott fürtkötetek tükrözött rugalmasságú létrehozására a kibővíthető Fájlkiszolgáló fürt csomópontjai egyik hajtható végre a következő PowerShell-parancsmagot:


```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR1 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```
SAPMNT létrehozásához, és állítsa be a mappa és a megosztáshoz biztonsági, hajtsa végre a következő PowerShell-parancsfájl a kibővíthető Fájlkiszolgáló fürt csomópontjai egyik:

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
## <a name="stop-ascsscs-instances-and-sap-services"></a>Asc/SCS példányok és az SAP-szolgáltatások leállítása

Hajtsa végre az alábbi lépéseket:
1. Állítsa le a SAP ASC/SCS példányok mindkét ASC/SCS fürtcsomópontokon.
2. SAP ASC/SCS Windows-szolgáltatások leállítása **SAP\<SID > _\<InstanceNumber >** mindkét fürtcsomóponton.

## <a name="move-the-sys-folder-to-the-sofs-cluster"></a>Helyezze át a \SYS\... a kibővíthető Fájlkiszolgáló fürt mappát

Hajtsa végre az alábbi lépéseket:
1. Másolja a SYS mappát (például C:\usr\sap\\<SID>\SYS) az egyik a ASC/SCS fürtcsomópontok a kibővíthető Fájlkiszolgáló fürthöz (például, hogy C:\ClusterStorage\Volume1\usr\sap\\<SID>\SYS).
2. Törölje a C:\usr\sap\\<SID>mindkét ASC/SCS fürtcsomópont \SYS mappát.

## <a name="update-the-cluster-security-setting-on-the-sap-ascsscs-cluster"></a>Az SAP ASC/SCS fürt a fürt biztonsági beállításainak frissítése

Hajtsa végre a következő PowerShell-parancsfájlt az SAP ASC/SCS fürtcsomópontok egyike:

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

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Hozzon létre egy virtuális nevet az SAP ASC/SCS fürtözött példány

Hozzon létre egy SAP ASC/SCS fürthálózat nevének (például **pr1-ASC [10.0.6.7]**) leírtak szerint [hozzon létre egy virtuális nevet az SAP ASC/SCS fürtözött példány] [ sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host] . 

## <a name="update-the-default-and-sap-ascsscs-instance-profile"></a>Az alapértelmezett és az SAP ASC/SCS példány profil frissítése

Az új SAP ASC/SCS virtuális állomás nevét, és globális állomásnév SAP, frissítenie kell az alapértelmezett és az SAP ASC/SCS példány profil \<SID >_ASC/SCS\<Nr >_<Host>.


| Régi értékek |  |
| --- | --- |
| SAP ASC/SCS állomásnév SAP globális állomás = | Asc-1 |
| SAP ASC/SCS példány profil neve | PR1_ASCS00_ascs-1 |

| Új értékek |  |
| --- | --- |
| SAP ASC/SCS állomásneve | **PR1-ASC** |
| SAP globális állomás | **sapglobal** |
| SAP ASC/SCS példány profil neve | PR1\_ASCS00\_**pr1-ASC** |

### <a name="update-sap-default-profile"></a>SAP alapértelmezett profil frissítése


| Paraméter neve | Paraméterérték |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| rdisp/mshost | **PR1-ASC** |
| célzó/serverhost | **PR1-ASC** |

### <a name="update-the-sap-ascsscs-instance-profile"></a>Az SAP ASC/SCS példány profil frissítése

| Paraméter neve | Paraméterérték |
| --- | --- |
| SAPGLOBALHOST | **sapglobal** |
| DIR_PROFILE | \\\sapglobal\sapmnt\PR1\SYS\profile |
| _PF | $(DIR_PROFILE) \PR1\_ASCS00_ pr1-ASC |
| Restart_Program_02 local$(_MS) pf=$(_PF) = | **Start**_Program_02 local$(_MS) pf=$(_PF) = |
| SAPLOCALHOST | **PR1-ASC** |
| Restart_Program_03 local$(_EN) pf=$(_PF) = | **Start**_Program_03 local$(_EN) pf=$(_PF) = |
| GW/netstat_once | **0** |
| célzó/encni/set_so_keepalive  | **Igaz** |
| szolgáltatás/ha_check_node | **1** |

> [!IMPORTANT]
>Használhatja a **frissítés-SAPASCSSCSProfile** PowerShell-parancsmag segítségével automatizálhatja a profil frissítéséhez.
>
>A PowerShell-parancsmag támogatja az SAP ABAP ASC és az SAP Java SCS példányok.
>

Másolás **SAPScripts.ps1** a helyi meghajtó a C:\tmp, és futtassa a következő PowerShell-parancsmagot:

```PowerShell
Import-Module C:\tmp\SAPScripts.ps1

Update-SAPASCSSCSProfile -PathToAscsScsInstanceProfile \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_ascs-1 -NewASCSHostName pr1-ascs -NewSAPGlobalHostName sapglobal -Verbose  
```

![1. ábra: SAPScripts.ps1 kimeneti][sap-ha-guide-figure-8012]

_**1. ábra**: SAPScripts.ps1 kimeneti_

## <a name="update-the-sidadm-user-environment-variable"></a>Frissítés a \<sid > adm felhasználói környezeti változó

1. Frissítés a \<sid > adm felhasználói környezet új GLOBALHOST UNC elérési utat a *mindkét* ASC/SCS fürtcsomópontok.
2. Jelentkezzen be \<sid > adm felhasználó, és indítsa el a Regedit.exe eszköz.
3. Nyissa meg a **HKEY_CURRENT_USER** > **környezet**, majd frissítse a változókat az új érték:

| Változó | Érték |
| --- | --- |
| RSEC_SSFS_DATAPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\data |
| RSEC_SSFS_KEYPATH | \\\\**sapglobal**\sapmnt\PR1\SYS\global\security\rsecssfs\key |
| SAPEXE | \\\\**sapglobal**\sapmnt\PR1\SYS\exe\uc\NTAMD64 |
| SAPLOCALHOST  | **PR1-ASC** |


## <a name="install-a-new-saprcdll-file"></a>Egy új saprc.dll fájl telepítéséhez:

1. Az SAP-fürterőforrás, amely támogatja a fájlmegosztás forgatókönyv új verzióját telepíti.

2. Töltse le a legfrissebb NTCLUST. Az SAP szolgáltatás piactérről csomag KKT.

3. Csomagolja ki NTCLUS. KKT ASC/SCS egyik fürtcsomópont, és futtassa a következő parancsot a parancssorba a következő új saprc.dll fájl telepítéséhez:

```
.\NTCLUST\insaprct.exe -yes -install
```

Az új saprc.dll fájl mindkét ASC/SCS fürtcsomóponton telepítve van.

További információkért lásd: [Megjegyzés 1596496 SAP - SAP erőforrástípus DLL-fájl frissítése a fürterőforrás-figyelő][1596496].

## <a name="create-a-sap-sid-cluster-group-network-name-and-ip"></a>Hozzon létre egy SAP <SID> csoport, a hálózati név és IP-fürt

Egy SAP létrehozásához \<SID > fürtcsoportban, egy ASC/SCS hálózati név és egy megfelelő IP-címet, futtassa a következő PowerShell parancsmagot:

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

## <a name="register-the-sap-start-service-on-both-nodes"></a>Az SAP start szolgáltatás mindkét csomópontján regisztrálása

Regisztrálja újra a SAP ASC/SCS start szolgáltatást úgy, hogy az új profil és a profil elérési útja mutasson.

Ez az Újraregisztrálás kell végrehajtani a *mindkét* ASC/SCS fürtcsomópontok.

A rendszergazda jogú parancssorból futtassa a következő parancsot:

```
C:\usr\sap\PR1\ASCS00\exe\sapstartsrv.exe -r -p \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_pr1-ascs -s PR1 -n 00 -U SAPCLUSTER\SAPServicePR1 -P mypasswd12 -e SAPCLUSTER\pr1adm
```

![2. ábra: SAP szolgáltatás újratelepítése][sap-ha-guide-figure-8013]

_**2. ábra**: telepítse újra a SAP-szolgáltatás_

Győződjön meg arról, hogy a paraméterek helyességét, majd válassza ki **manuális** , a **indítási típus**.

## <a name="stop-the-ascsscs-service"></a>A ASC/SCS szolgáltatás leállítása

Állítsa le a SAP ASC/SCS szolgáltatást SAP\<SID > _\<InstanceNumber > mindkét ASC SCS fürtcsomópontokon.

## <a name="create-a-new-sap-service-and-sap-instance-resources"></a>Hozzon létre egy új SAP-szolgáltatás és az SAP példány erőforrások

Az SAP SAP az erőforrások létrehozásának véglegesítéséhez\<SID > fürtcsoportot, és hozzon létre a következőket:

* SAP \<SID > \<InstanceNumber > szolgáltatás
* SAP \<SID > \<InstanceNumber > példány

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

## <a name="add-a-probe-port"></a>Adjon hozzá egy mintavételi portot

Egy SAP-erőforrás, az SAP-SID-IP mintavételi portot konfigurálja a PowerShell használatával. Hajtsa végre ezt a konfigurációt egy SAP ASC/SCS fürtcsomópontnak leírtak [ebben a cikkben][sap-high-availability-installation-wsfc-shared-disk-add-probe-port].

## <a name="install-an-ers-instance-on-both-cluster-nodes"></a>Egy SSZON példányát telepítenie, mindkét fürtcsomóponton

Sorba helyezni replikációs Server (SSZON) példánya telepíthető *mindkét* a ASC/SCS fürt csomópontjait. A SWPM menü kövesse a telepítési útvonalon:

**\<A termék >** > **\<DBMS >** > **telepítési** > **további SAP rendszer példányok**  >  **Sorba helyezni replikációs Server-példány**

## <a name="install-a-dbms-instance-and-sap-application-servers"></a>A DBMS példánya és SAP alkalmazás-kiszolgálók telepítése

Az SAP rendszer telepítésének véglegesítéséhez telepítése:
* A DBMS példánya.
* Egy elsődleges SAP-alkalmazáskiszolgáló.
* További SAP alkalmazáskiszolgáló.

## <a name="next-steps"></a>Következő lépések

* [Egy ASC/SCS példányát telepítenie, a megosztott lemez - magas rendelkezésre állású fájlmegosztás hivatalos SAP irányelvek a feladatátvevő fürtben][sap-official-ha-file-share-document]

* [Tárolóhelyek – közvetlen a Windows Server 2016][s2d-in-win-2016]

* [Kibővített fájlkiszolgáló alkalmazásadatokhoz – áttekintés][sofs-overview]

* [What's new in Windows Server 2016 tárolás][new-in-win-2016-storage]
