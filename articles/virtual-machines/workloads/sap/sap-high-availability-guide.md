---
title: Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver |} A Microsoft Docs
description: Magas rendelkezésre állású útmutató az SAP NetWeaver az Azure Virtual machines szolgáltatásban
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
ms.openlocfilehash: a6b6728d7eaa263bb7e9da0f08a47ffe2f1e961a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009460"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver"></a>Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

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



Azure-beli virtuális gépek a megoldás olyan szervezeteknek, amelyek a szükséges számítási, tárolási és hálózati erőforrásokat, minimális és hosszadalmas beszerzési ciklusokat nélkül is. Használhatja az Azure Virtual Machines klasszikus alkalmazások, például ABAP SAP NetWeaver-alapú, a Java és a egy ABAP + Java-verem üzembe helyezése. Kiterjesztheti a megbízhatósággal és rendelkezésre állással, a helyszíni erőforrások nélkül. Azure-beli virtuális gépek közötti kapcsolatot, támogatja, így az Azure virtuális gépek integrálása a szervezet helyszíni tartományok, a magánfelhők és az SAP-rendszer rendszeren.

Ez a cikk ismerteti a lépéseket, amelyeket az Azure-ban magas rendelkezésre állású SAP-rendszerek telepítése az Azure Resource Manager-alapú üzemi modell használatával. Részletesen fő feladatok:

* Keresse meg a megfelelő SAP-megjegyzések és telepítési útmutatóit szerepel a [erőforrások] [ sap-ha-guide-2] szakaszban. Ez a cikk kiegészíti az SAP-dokumentáció, és SAP-megjegyzések, amelyek az elsődleges erőforrásokat, amelyek segítségével telepítse, és üzembe helyezése az SAP-szoftvereket az adott platformon.
* Ismerje meg az Azure Resource Manager-alapú üzemi modell és a klasszikus Azure üzemi modell közötti különbségekről.
* Ismerje meg a Windows Server feladatátvételi fürtszolgáltatási kvórummódok, ezért kiválaszthatja a megfelelő az Azure-alapú modell.
* További információ az Azure-szolgáltatások a Windows Server feladatátvételi fürtszolgáltatási megosztott tároló.
* Ismerje meg, hogyan védheti meg egyetlen – pont-az-hibát összetevői például fejlett üzleti alkalmazások fejlesztői (ABAP) SAP Central Services (ASCS) / SAP Central Services (SCS) és az adatbázis-kezelő rendszerek (DBMS) és a redundáns összetevők, például az SAP-alkalmazások Kiszolgáló, az Azure-ban.
* Egy részletes példa-telepítés és konfiguráció egy magas rendelkezésre állású SAP-rendszerhez egy Windows Server feladatátvételi fürtszolgáltatási fürt az Azure-ban, kövesse az Azure Resource Manager használatával.
* További információ a Windows Server feladatátvételi fürtszolgáltatási amelyhez nem szükséges egy helyszíni környezetben, de az Azure használatához szükséges további lépéseket.

Központi telepítése és konfigurálása, ez a cikk egyszerűsítése érdekében használjuk az SAP háromrétegű magas rendelkezésre állású Resource Manager-sablonok. A sablonok egy magas rendelkezésre állású SAP-rendszerhez szükséges a teljes infrastruktúra üzembe helyezésének automatizálása. Az infrastruktúra támogatja a SAP alkalmazás teljesítményének Standard (SAP) méretezés az SAP-rendszer is.

## <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Előfeltételek
A Kezdés előtt győződjön meg arról, hogy megfelel-e az előfeltételeknek, amely a következő szakaszok ismertetik. Emellett mindenképpen ellenőrizze a felsorolt összes erőforrást a [erőforrások] [ sap-ha-guide-2] szakaszban.

Ez a cikk az Azure Resource Manager-sablonokkal használjuk [háromrétegű SAP NetWeaver felügyelt lemezek használatával](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/). Sablonok hasznos áttekintéséért lásd: [SAP az Azure Resource Manager-sablonok](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Erőforrások
Ezek a cikkek az Azure-beli SAP-környezetekhez terjed ki:

* [Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver][planning-guide]
* [Az SAP NetWeaver számára az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP NetWeaver számára az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* [Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver (Ez az útmutató)][sap-ha-guide]

> [!NOTE]
> Amikor csak lehetséges, is biztosítunk egy hivatkozást a hivatkozó SAP telepítési útmutatójában (lásd a [SAP telepítési útmutatóinak][sap-installation-guides]). Az előfeltételeket és a telepítési folyamatra vonatkozó információkat célszerű figyelmesen olvassa el az SAP NetWeaver telepítési útmutatóit. Ez a cikk ismerteti, amelyet használhat az Azure Virtual Machines, az SAP NetWeaver-alapú rendszerek csak meghatározott feladatok.
>
>

A témakör az Azure-beli SAP kapcsolatos alábbi SAP-megjegyzések:

| Megjegyzés száma | Cím |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-ban: Támogatott termékek és méretezés |
| [2015553] |A Microsoft Azure-beli SAP: Támogatás előfeltételei |
| [1999351] |Továbbfejlesztett SAP az Azure Monitoring |
| [2178632] |Metrikák figyelése a Microsoft Azure-beli SAP-kulcs |
| [1999351] |A Windows-virtualizálás: Speciális figyelés |
| [2243692] |SAP DBMS-példány használatát az Azure prémium szintű SSD-tárolóval |

Tudjon meg többet a [korlátozások az Azure-előfizetések][azure-subscription-service-limits-subscription], beleértve az általános alapértelmezett korlátozások és a maximális korlátozások.

## <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Magas rendelkezésre állás az SAP az Azure Resource Managerrel és a klasszikus Azure üzemi modell
Az Azure Resource Manager és klasszikus Azure üzemi modell a következő területek különböznek meg:

- Erőforráscsoportok
- Azure belső load balancer függőség az Azure-erőforráscsoportot
- SAP több biztonsági AZONOSÍTÓVAL forgatókönyvek támogatása

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Erőforráscsoportok
Az Azure Resource Manager erőforráscsoportokat használhatja az alkalmazás-erőforrások kezelése az Azure-előfizetésében. Az integrált módszert egy erőforráscsoportba tartozó összes erőforrásnak rendelkeznie kell az azonos életciklus. Például egyszerre létrehozott összes erőforrást, és a egy időben törlés. További információk az [erőforráscsoportokról](../../../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azure belső load balancer függőség az Azure-erőforráscsoportot

A klasszikus Azure üzemi modellben az Azure belső terheléselosztó (az Azure Load Balancer szolgáltatás) és a felhőszolgáltatás közötti függőség van. Minden egyes belső load balancer kell egy felhőalapú szolgáltatás.

Az Azure Resource Managerben minden Azure-erőforrás át kívánja helyezni, az Azure-erőforráscsoportban, és ez érvényes az Azure Load Balancerhez is. Azonban nem kell rendelkeznie egy Azure-erőforráscsoportot az Azure load balancer száma, például egy Azure-erőforráscsoport több Azure Terheléselosztók is tartalmazhat. A környezet nem egyszerűbb és rugalmasabb. 

### <a name="support-for-sap-multi-sid-scenarios"></a>SAP több biztonsági AZONOSÍTÓVAL forgatókönyvek támogatása

Az Azure Resource Managerben, több példányt is telepíthet SAP rendszer azonosítója (SID) ASCS/SCS egy fürtben. Több SID-vel példányok miatt egyes Azure belső terheléselosztó több IP-címek támogatása is lehetséges.

A klasszikus Azure üzemi modellt használja, kövesse az ismertetett eljárások [SAP NetWeaver az Azure-ban: Az SAP ASCS/SCS-példányok fürtszolgáltatás Windows Server feladatátvételi fürtszolgáltatás használatával az Azure-ban az SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056).

> [!IMPORTANT]
> Javasoljuk, hogy az SAP-telepítések az Azure Resource Manager üzemi modell használata. Ez lehetővé teszi számos előnnyel jár, amelyek nem érhetők el a klasszikus üzemi modellben. További tudnivalók az Azure [üzembe helyezési modellek][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server feladatátvételi fürtszolgáltatás
A Windows Server feladatátvételi fürtszolgáltatási az alapja egy magas rendelkezésre állású SAP ASCS/SCS telepítés és a Windows DBMS.

Egy feladatátvevő fürt egy 1 + n különálló kiszolgálók csoportja, (csomópontok), amelyek együttműködése magasabb az alkalmazások és szolgáltatások rendelkezésre állását. Egy csomópont meghibásodása esetén a Windows Server feladatátvételi fürtszolgáltatási számítja ki, amely akkor fordulhat elő, miközben fenntartja az alkalmazások és szolgáltatások megfelelő fürt hibák száma. A Feladatátvételi fürtszolgáltatás eléréséhez különböző kvórummódok közül választhat.

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Kvórummódok
A Windows Server feladatátvételi fürtszolgáltatás használata esetén négy kvórummódok közül választhat:

* **Csomóponttöbbség**. A fürt minden csomópontján is szavazhat. A fürt működik csak a szavazatot, többségét, a több mint fele a szavazatokat. Azt javasoljuk, hogy ezt a lehetőséget olyan fürtök, amelyek rendelkeznek a csomópontok páratlan számú. Például egy hét csomópontos fürtben három csomópont visszaadhatja a feladatokat, és a fürt stills éri el a nagyobb részét, és továbbra is fusson.  
* **Csomópont- és lemeztöbbség**. Minden egyes csomópont és a egy kijelölt lemez a fürttároló, (a tanúsító lemez) mikor érhetők el, és a kommunikáció is szavazhat. A fürt működik a szavazatok többsége csak az azt jelenti, a több mint fele a szavazatokat. Ebben a módban van értelme páros szám a csomópontok fürtözött környezetben. Ha a csomópontok fele és a lemez online állapotban, a fürt kifogástalan állapotban marad.
* **Csomópont- és fájlmegosztás legtöbb**. Minden egyes csomópontra és a egy kijelölt fájlmegosztás (tanúsító fájlmegosztás) a rendszergazda által létrehozott értékelheti a hozzászólást, függetlenül attól, hogy a csomópontok és a fájlmegosztás érhetők el, és a kommunikáció. A fürt működik a szavazatok többsége csak az azt jelenti, a több mint fele a szavazatokat. Ebben a módban van értelme páros szám a csomópontok fürtözött környezetben. Hasonló, a csomópont- és lemeztöbbség módot, de a tanúsító fájlmegosztás helyett egy tanúsító lemezt használ. Ebben a módban könnyen megvalósítható, de ha a fájlmegosztás maga nem magas rendelkezésre állású, válhat a hibaérzékeny pont.
* **Nincs többség: Csak lemez**. A fürt kvóruma rendelkezik, ha egy csomópont elérhető, és a egy adott lemez a fürttároló folytatott kommunikáció. Csak a csomópontok is, hogy a lemez folytatott kommunikáció csatlakozhassanak a fürthöz. Azt javasoljuk, hogy nem használja ezt a módot.
 

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server feladatátvételi fürtszolgáltatás a helyszíni
1. ábra mutatja a két csomópontból álló fürtben. Ha a csomópontok meghiúsul, és egyaránt csomópontok vesznek fel, és fut, egy kvórumlemez, vagy a fájl közötti hálózati kapcsolat meghatározza, hogy melyik csomópont továbbra is biztosítja a fürt alkalmazásokat és szolgáltatásokat. A csomópont, amely a kvórum lemez vagy fájlmegosztás hozzáféréssel rendelkezik az a csomópont, biztosítja, hogy a szolgáltatások továbbra is.

Mivel ez a példa egy két csomópontos fürt használja, a csomópont- és fájlmegosztástöbbség kvórummód használjuk. A csomópont- és lemeztöbbség is érvényes. Éles környezetben azt javasoljuk, hogy egy kvórumlemez használja. Hálózati és tárolási rendszer technológia segítségével magas rendelkezésre állásúvá tenni.

![1. ábra: A Windows Server feladatátvételi fürtszolgáltatás konfigurációs az SAP ASCS/SCS az Azure-ban – példa][sap-ha-guide-figure-1000]

_**1. ábra:** A Windows Server feladatátvételi fürtszolgáltatás konfigurációs az SAP ASCS/SCS az Azure-ban – példa_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Megosztott tároló
1. ábra is mutatja egy két csomópontos megosztott tárolófürt. Egy helyi megosztott tároló fürtben a fürt összes csomópontja észleli a megosztott tároló. Zárolási mechanizmus sérülés elleni védi az adatokat. Minden csomópont képes észlelni, ha egy másik csomópont meghibásodik. Ha egy csomópont meghibásodik, a megmaradó csomópontra tulajdonjogot a tárolási erőforrások és szolgáltatások rendelkezésre állását biztosítja.

> [!NOTE]
> Az SQL Server nincs szükség megosztott lemezeinek magas rendelkezésre állás az egyes adatbázis-kezelő alkalmazások, például. SQL Server Always On replikálja a helyi lemez egy másik fürtcsomópontra, a helyi lemez egy fürtcsomópont adathoz és naplófájlhoz az adatbázis-kezelő. Ebben az esetben a Windows-fürt konfigurációját nem kell egy megosztott lemez.
>
>

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Hálózat és a névfeloldás
Ügyfélszámítógépek a fürt virtuális IP-címet és a egy virtuális állomás neve, a DNS-kiszolgáló által biztosított keresztül érhető el. A helyszíni csomópontok és a DNS-kiszolgáló képes kezelni a több IP-címet.

A hagyományos telepítés két vagy több hálózati kapcsolatot használhatja:

* A storage, dedikált kapcsolat
* A szívverés egy fürt belső hálózati kapcsolatot
* Egy nyilvános hálózatot használó ügyfelek számára a fürthöz való csatlakozáshoz

## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> A Windows Server feladatátvevő fürt az Azure-ban
Operációs rendszer nélküli vagy magánfelhő-telepítések képest, Azure Virtual Machines konfigurálása a Windows Server feladatátvételi fürtszolgáltatás további lépéseket igényel. Ha egy megosztott fürtlemezre hoz létre, több IP-címek és virtuális állomásnevek az SAP ASCS/SCS példányhoz be kell.

Ebben a cikkben bemutatjuk, főbb fogalmakat és a egy SAP central services magas rendelkezésre állású fürt az Azure-ban létrehozásához szükséges további lépéseket. Bemutatjuk, hogyan állítható be a külső eszköz az SIOS DataKeeper, és az Azure belső terheléselosztó konfigurálása. Ezek az eszközök segítségével a Windows feladatátvevő fürt létrehozása a tanúsító fájlmegosztás az Azure-ban.

![2. ábra: A Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban egy megosztott lemez nélkül][sap-ha-guide-figure-1001]

_**2. ábra:** A Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban egy megosztott lemez nélkül_

### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Az SIOS DataKeeper megosztott lemezt az Azure-ban
A magas rendelkezésre állású SAP ASCS/SCS példányhoz megosztott tárolót kell fürt. 2016 szeptemberétől Azure nem biztosít, amelyek segítségével hozzon létre egy megosztott tárolófürt megosztott tárolót. Harmadik féltől származó szoftverek az SIOS DataKeeper Cluster Edition használatával hozzon létre egy tükrözött tárfiókot, amely szimulálja a fürt megosztott tárolójába. Az SIOS megoldás biztosítja a valós idejű szinkron adatreplikációt. Ez a fürt megosztott erőforrás létrehozásának módját:

1. A virtuális gépek (VM) egy Windows-fürt konfigurációját mindegyike csatolása egy további lemezt.
2. Futtassa az SIOS DataKeeper Cluster Edition mindkét virtuális gép csomópontját.
3. Állítsa az SIOS DataKeeper Cluster Edition, hogy azt a tartalom a további csatlakoztatott lemez kötet a forrás virtuális gép a cél virtuális gép további csatlakoztatott lemez mennyiségét tükrözi. Az SIOS DataKeeper kivonatolja a forrás- és helyi köteteken, és ezután jeleníti meg őket a Windows Server feladatátvételi fürtszolgáltatás egy megosztott lemez.

További információk [az SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

![3. ábra: Az Azure-ban az SIOS DataKeeper Windows Server feladatátvételi fürtszolgáltatás konfigurációs][sap-ha-guide-figure-1002]

_**3. ábra:** Az Azure-ban az SIOS DataKeeper Windows Server feladatátvételi fürtszolgáltatás konfigurációs_

> [!NOTE]
> Magas rendelkezésre állás az egyes DBMS-termékek, például az SQL Server már nincs szüksége megosztott lemezeket. SQL Server Always On replikálja a helyi lemez egy másik fürtcsomópontra, a helyi lemez egy fürtcsomópont adathoz és naplófájlhoz az adatbázis-kezelő. Ebben az esetben a Windows-fürt konfigurációját nem kell egy megosztott lemez.
>
>

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> A névfeloldás az Azure-ban
Az Azure felhőalapú platformján nem teszi lehetővé a virtuális IP-címek, például a nem fix IP-címek konfigurálásához. Alternatív megoldásként elérni a fürt erőforrásai a felhőben egy virtuális IP-cím beállításához szüksége lesz.
Az Azure belső terheléselosztó az Azure Load Balancer szolgáltatás rendelkezik. A belső terheléselosztó az ügyfelek a fürt a fürt virtuális IP-címen keresztül érhető el.
Az erőforráscsoport, amely tartalmazza a fürtcsomópontok belső terheléselosztóinak kell. Ezt követően konfigurálja a portokat a belső terheléselosztó továbbítási szabályok a mintavétel az összes szükséges port.
Az ügyfelek csatlakozhatnak-n keresztül a virtuális gazdagép nevét. A DNS-kiszolgáló szünteti meg a fürt IP-címét, és a belső load balancer kezeli port továbbítja a fürt aktív csomópontja.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver az Azure infrastruktúra--szolgáltatásként (IaaS) a magas rendelkezésre állás
SAP alkalmazás magas rendelkezésre állás, mint például a SAP software-összetevők a következő összetevők védelmére kell:

* SAP alkalmazáskiszolgáló-példány
* Az SAP ASCS/SCS példányhoz
* Az adatbázis-kezelő kiszolgáló

Az SAP-összetevők magas rendelkezésre állású forgatókönyvekben védelméről további információ: [Azure Virtual Machines tervezési és megvalósítási az SAP NetWeaver számára][planning-guide-11].

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Magas rendelkezésre állású SAP-alkalmazáskiszolgáló
Általában nincs szükség egy adott magas rendelkezésre állású megoldás az SAP-alkalmazáskiszolgáló és a párbeszédpanel-példányokhoz. Magas rendelkezésre állás, a redundancia, és a különböző példányok az Azure Virtual Machines alkalmazáspéldányok párbeszédpanelen konfigurálja. Legalább két SAP alkalmazáspéldányok két példányt az Azure Virtual Machines telepítve kell lennie.

![4. ábra: Magas rendelkezésre állású SAP-alkalmazáskiszolgáló][sap-ha-guide-figure-2000]

_**4. ábra:** Magas rendelkezésre állású SAP-alkalmazáskiszolgáló_

SAP-alkalmazáskiszolgáló példányait üzemeltetni az azonos Azure rendelkezésre állási beállítása minden virtuális gépnek kell elhelyeznie. Az Azure rendelkezésre állási csoport biztosítja, hogy:

* Az összes virtuális gép az azonos frissítési tartomány részét képezik. A frissítési tartomány, például gondoskodik arról, hogy a virtuális gépek tervezett karbantartás leállások során egy időben nem frissítenek.
* Az összes virtuális gép az azonos tartalék tartomány részét képezik. Tartalék tartomány, például gondoskodik arról, hogy a virtuális gépek, hogy nem rendszerkritikus meghibásodási pontot hatással van az összes virtuális gépek rendelkezésre állásának vannak-e telepítve.

Ismerje meg, hogyan [virtuális gépek rendelkezésre állásának kezelése][virtual-machines-manage-availability].

Csak a nem felügyelt lemez: Mivel az Azure storage-fiók egy potenciálisan hibaérzékeny pont, fontos legalább két Azure storage-fiókok, amelyben oszlanak meg legalább két virtuális gép rendelkezik. Egy ideális beállítás minden SAP párbeszédpanel példányát futtató virtuális gép lemezei a egy másik tárfiók üzembe kívánja helyezni.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Magas rendelkezésre állású SAP ASCS/SCS példányhoz
5. ábra egy példát egy magas rendelkezésre állású SAP ASCS/SCS példányhoz.

![5. ábra: Magas rendelkezésre állású SAP ASCS/SCS példányhoz][sap-ha-guide-figure-2001]

_**5. ábra:** Magas rendelkezésre állású SAP ASCS/SCS példányhoz_

#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Az SAP ASCS/SCS példányhoz magas rendelkezésre állást biztosít a Windows Server feladatátvételi fürtszolgáltatási az Azure-ban
Operációs rendszer nélküli vagy magánfelhő-telepítések képest, Azure Virtual Machines konfigurálása a Windows Server feladatátvételi fürtszolgáltatás további lépéseket igényel. Egy Windows feladatátvevő fürtöt létrehozni, szüksége egy megosztott fürtlemezre, több IP-címek, több virtuális állomásnevek és az Azure belső terheléselosztó a fürtszolgáltatás egy SAP ASCS/SCS példányhoz. Bemutatjuk, ez a cikk későbbi részében részletesebben.

![6. ábra: A Windows Server feladatátvételi fürtszolgáltatási az SIOS DataKeeper használatával az Azure-beli SAP ASCS/SCS konfigurációhoz][sap-ha-guide-figure-1002]

_**6. ábra:** A Windows Server feladatátvételi fürtszolgáltatási az Azure-ban az SIOS DataKeeper SAP ASCS/SCS konfigurációhoz_

### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Magas rendelkezésre állású DBMS-példány
Az adatbázis-kezelő egy SAP-rendszerrel is szerepel egy egyetlen kapcsolattartási pontja. Kell egy magas rendelkezésre állású megoldás megvédheti azokat. 7. ábra bemutatja egy SQL Server Always On magas rendelkezésre állású megoldás az Azure-ban, a Windows Server feladatátvételi fürtszolgáltatási és az Azure belső terheléselosztó. SQL Server Always On replikálja a saját adatbázis-kezelő replikációs adathoz és naplófájlhoz az adatbázis-kezelő. Ebben az esetben, nem kell a fürt megosztott lemezzel, ami leegyszerűsíti az összes beállítást.

![7. ábra: Az SQL Server Always On a magas rendelkezésre állású SAP DBMS – példa][sap-ha-guide-figure-2003]

_**7. ábra:** Az SQL Server Always On a magas rendelkezésre állású SAP DBMS – példa_

Az Azure Resource Manager-alapú üzemi modell használatával az Azure-beli SQL Server fürtszolgáltatással kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [Always On rendelkezésre állási csoport konfigurálása Azure Virtual Machines szolgáltatásban manuálisan Resource Manager használatával] [virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Az Always On rendelkezésre állási csoporthoz az Azure belső terheléselosztó konfigurálása az Azure-ban] [virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Végpontok közötti magas rendelkezésre állású központi telepítési forgatókönyvei

### <a name="deployment-scenario-using-architectural-template-1"></a>Üzembe helyezési forgatókönyv architekturális sablon 1 használata

8. ábra szemlélteti, az SAP NetWeaver magas rendelkezésre állású architektúra az Azure-ban **egy** SAP-rendszerhez. Ebben a forgatókönyvben be van állítva a következő:

- Az SAP ASCS/SCS példányhoz egy dedikált fürt használható.
- A DBMS-példány egy dedikált fürt használható.
- SAP alkalmazáskiszolgáló-példányok saját dedikált virtuális gépek vannak üzembe helyezve.

![8. ábra: Magas rendelkezésre állású architektúra sablon 1, dedikált fürttel ASCS/SCS-és adatbázis-kezelő SAP][sap-ha-guide-figure-2004]

_**8. ábra:** Magas rendelkezésre állású architektúra sablon 1, SAP ASCS/SCS-és adatbázis-kezelő dedikált fürtök_

### <a name="deployment-scenario-using-architectural-template-2"></a>Üzembe helyezési forgatókönyv architekturális sablon 2 használatával

9. ábra szemlélteti, az SAP NetWeaver magas rendelkezésre állású architektúra az Azure-ban **egy** SAP-rendszerhez. Ebben a forgatókönyvben be van állítva a következő:

- Egy dedikált fürt használt **mindkét** az SAP ASCS/SCS-példány és az adatbázis-kezelő.
- SAP alkalmazáskiszolgáló-példányok saját dedikált virtuális gépek vannak üzembe helyezve.

![9. ábra: Magas rendelkezésre állású architektúra sablon 2, egy dedikált ASCS/SCS-fürtöt és a egy dedikált adatbázis-kezelő fürt SAP][sap-ha-guide-figure-2005]

_**9. ábra:** Magas rendelkezésre állású architektúra sablon 2, egy dedikált ASCS/SCS-fürtöt és a egy dedikált adatbázis-kezelő fürt SAP_

### <a name="deployment-scenario-using-architectural-template-3"></a>Üzembe helyezési forgatókönyv a 3. a sablon architekturális.

10. ábrán látható egy példa az SAP NetWeaver magas rendelkezésre állású architektúra az Azure-ban **két** rendszerek, SAP &lt;SID1&gt; és &lt;SID2&gt;. Ebben a forgatókönyvben be van állítva a következő:

- Egy dedikált fürt használt **mindkét** az SAP ASCS/SCS SID1 példány *és* az SAP ASCS/SCS SID2-példány (egy fürtben).
- Egy dedikált fürt DBMS SID1 szolgál, és egy másik dedikált fürtöt szolgál az adatbázis-kezelő SID2 (két fürtben).
- SAP alkalmazáskiszolgáló-példányok az SAP-rendszer SID1 rendelkezik a saját dedikált virtuális gépeken.
- SAP alkalmazáskiszolgáló-példányok az SAP-rendszer SID2 rendelkezik a saját dedikált virtuális gépeken.

![10. ábra: SAP magas rendelkezésre állású architektúra sablon 3, egy dedikált fürttel különböző ASCS/SCS példányhoz][sap-ha-guide-figure-6003]

_**10. ábra:** SAP magas rendelkezésre állású architektúra sablon 3, egy dedikált fürttel különböző ASCS/SCS példányhoz_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Az infrastruktúra előkészítése

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Az infrastruktúra előkészítése az 1. a sablon architekturális.
Az SAP az Azure Resource Manager-sablonok segítségével egyszerűsítheti az üzembe helyezést, a szükséges erőforrásokat.

A háromrétegű sablonok az Azure Resource Manager is támogatja a magas rendelkezésre állású forgatókönyvek, például architekturális sablon 1, amely két fürttel rendelkezik. Minden egyes fürt egy SAP rendszerkritikus meghibásodási pontot SAP ASCS/SCS-és adatbázis-kezelő rendszer.

Itt látható, ahol is igénybe az Azure Resource Manager-sablonok az ebben a cikkben ismertetünk. példa:

* [Az Azure Marketplace-lemezkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Felügyelt lemezek használata az Azure Marketplace-en kép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Felügyelt lemezek használatával egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Az infrastruktúra előkészítése architekturális sablon 1:

- Az Azure Portalon az a **paraméterek** panelen, a a **SYSTEMAVAILABILITY** jelölje ki **magas rendelkezésre ÁLLÁSÚ**.

  ![11. ábra: Magas rendelkezésre állás az SAP az Azure Resource Manager paraméterek beállítása][sap-ha-guide-figure-3000]

_**11. ábra:** Magas rendelkezésre állás az SAP az Azure Resource Manager paraméterek beállítása_


  A sablonok létrehozása:

  * **Virtuális gépek**:
    * SAP alkalmazáskiszolgáló virtuális gépek: <*SAPSystemSID*> - di - <*száma*>
    * A fürt virtuális gépek ASCS/SCS: <*SAPSystemSID*> – ascs - <*száma*>
    * Az adatbázis-kezelő fürt: <*SAPSystemSID*> - db - <*száma*>

  * **Hálózati kártya minden virtuális gép társított IP-címekkel rendelkező**:
    * <*SAPSystemSID*>-nic-di-<*Number*>
    * <*SAPSystemSID*>-nic-ascs-<*Number*>
    * <*SAPSystemSID*>-nic-db-<*Number*>

  * **Az Azure storage-fiókok (csak a nem felügyelt lemezek esetén)**

  * **Rendelkezésre állási csoportok** számára:
    * SAP alkalmazáskiszolgáló virtuális gépek: <*SAPSystemSID*> - avset - di
    * Az SAP ASCS/SCS virtuális gépek fürtözése: <*SAPSystemSID*> - avset – ascs
    * Az adatbázis-kezelő fürt virtuális gépek: <*SAPSystemSID*> - avset - adatbázis

  * **Az Azure belső terheléselosztó**:
    * Az ASCS/SCS példányhoz és IP-cím minden porttal <*SAPSystemSID*> - lb - ascs
    * Az SQL Server adatbázis-kezelő és az IP-cím minden porttal <*SAPSystemSID*> - lb - adatbázis

  * **Hálózati biztonsági csoport**: <*SAPSystemSID*> - nsg - ascs-0  
    * Egy megnyitott külső távoli asztal protokoll (RDP) port, a <*SAPSystemSID*> virtuális gép – ascs - 0

> [!NOTE]
> A hálózati kártyák és az Azure belső terheléselosztók összes IP-címek **dinamikus** alapértelmezés szerint. Módosítsa őket **statikus** IP-címeket. A cikk későbbi részében ehhez ismertetünk.
>
>

### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> A vállalati hálózati kapcsolattal (létesítmények közötti) az éles környezetben használt virtuális gépek üzembe helyezése
Az SAP-rendszereit, üzembe helyezése az Azure-beli virtuális gépek [vállalati hálózati kapcsolat (létesítmények közötti)] [ planning-guide-2.2] az Azure Site-to-Site VPN- vagy Azure ExpressRoute használatával.

> [!NOTE]
> Az Azure Virtual Network-példányt is használhat. A virtuális hálózat és alhálózat már létrehozott és előkészített.
>
>

1. Az Azure Portalon az a **paraméterek** panelen, a a **NEWOREXISTINGSUBNET** jelölje ki **meglévő**.
2. Az a **SUBNETID** adjon hozzá a teljes karakterláncot, az előkészített Azure-hálózatok SubnetID, ha azt tervezi, hogy az Azure-beli virtuális gépek üzembe helyezése.
3. Az összes Azure-beli hálózati alhálózatok listájának lekéréséhez futtassa a következő PowerShell-paranccsal:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   A **azonosító** mezőt jeleníti meg a **SUBNETID**.
4. Az összes listába **SUBNETID** értékeket, futtassa a következő PowerShell-parancsot:

   ```PowerShell
   (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   A **SUBNETID** kell kinéznie:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> A tesztelési és bemutató csak felhőalapú SAP-példányok üzembe helyezése
A csak felhőalapú üzembe helyezési modell magas rendelkezésre állású SAP-rendszerrel is telepítheti. Az ilyen típusú központi telepítés elsősorban hasznos bemutató és tesztelés a használati esetek. Éles környezeti alkalmazási helyzetek esetén nem használhatók.

- Az Azure Portalon az a **paraméterek** panelen, a a **NEWOREXISTINGSUBNET** jelölje ki **új**. Hagyja a **SUBNETID** mezője üres.

  Az SAP az Azure Resource Manager-sablon automatikusan létrehozza az Azure virtuális hálózatot és alhálózatot.

> [!NOTE]
> Akkor is szükség van legalább egy dedikált virtuális gép Active Directory és DNS Azure Virtual Network ugyanezen példányában. A sablon nem hoz létre ezekre a virtuális gépekre.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>2. a sablon architekturális. az infrastruktúra előkészítése

Az SAP az Azure Resource Manager-sablon segítségével egyszerűsítheti az üzembe helyezést a szükséges infrastruktúra-erőforrások SAP architekturális sablon 2.

Itt látható, ahol megkapja a az Azure Resource Manager-sablonok a központi telepítési forgatókönyv:

* [Az Azure Marketplace-lemezkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Felügyelt lemezek használata az Azure Marketplace-en kép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Felügyelt lemezek használatával egyéni rendszerkép](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>3. a sablon architekturális. az infrastruktúra előkészítése

Az infrastruktúra előkészítése, és konfigurálja az SAP **több biztonsági AZONOSÍTÓVAL**. Például hozzáadhat egy további, az SAP ASCS/SCS-példányt egy *meglévő* fürtkonfiguráció. További információkért lásd: [konfigurálása egy további SAP ASCS/SCS példányhoz be egy meglévő fürtkonfigurációhoz egy SAP több biztonsági AZONOSÍTÓVAL konfiguráció létrehozása az Azure Resource Manager][sap-ha-multi-sid-guide].

Ha több biztonsági AZONOSÍTÓVAL fürtöt létrehozni, használhatja a több biztonsági AZONOSÍTÓVAL [gyorsindítási sablonok github](https://github.com/Azure/azure-quickstart-templates).
Hozzon létre egy új több biztonsági AZONOSÍTÓVAL fürtöt, meg kell a következő három sablonok üzembe helyezése:

* [ASCS/SCS-sablon](#ASCS-SCS-template)
* [Adatbázis-sablon](#database-template)
* [Alkalmazássablon-kiszolgálók](#application-servers-template)

Az alábbi szakaszok a sablonok és a paraméterek, meg kell adnia a sablonok részletesebb információkat tartalmaz.

#### <a name="ASCS-SCS-template"></a> ASCS/SCS-sablon

Az ASCS/SCS-sablon üzembe helyezi a két virtuális gépet, amelyek segítségével több ASCS/SCS-példányt üzemeltető Windows Server feladatátvevő fürt létrehozása.

Az ASCS/SCS több biztonsági AZONOSÍTÓVAL sablon, a beállítása a [ASCS/SCS több biztonsági AZONOSÍTÓVAL sablon] [ sap-templates-3-tier-multisid-xscs-marketplace-image] vagy [ASCS/SCS több biztonsági AZONOSÍTÓVAL sablon felügyelt lemezek használatával] [ sap-templates-3-tier-multisid-xscs-marketplace-image-md], adja meg az értékeket az alábbi paraméterekkel:

  - **Erőforrás-előtag**.  Állítsa be az erőforrás-előtagja, amelynek az üzembe helyezés során létrehozott összes erőforrást előtagot használja. Az erőforrások csak egy SAP-rendszerhez tartozik, mert az előtag, az erőforrás nem áll a SID-egy SAP-rendszerhez.  Az előtag közé kell esnie **3-6 karakter**.
  - **Írja be a verem**. Válassza ki a verem az SAP-rendszerhez. Depending on the stack type, Azure Load Balancer has one (ABAP or Java only) or two (ABAP+Java) private IP addresses per SAP system.
  -  **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerének.
  -  **Az SAP-rendszer száma**. Válassza ki a fürt telepíteni szeretné az SAP-rendszerek számát.
  -  **Rendszer rendelkezésre állását**. Válassza ki **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazdai felhasználónév és jelszó rendszergazdai**. Hozzon létre egy új felhasználót, jelentkezzen be a gép is lehet.
  -  **Új vagy meglévő alhálózaton**. Állítsa be, hogy egy új virtuális hálózatot és alhálózatot kell létrehozni, vagy egy létező alhálózatot kell használnia. Ha a helyszíni hálózatához csatlakoztatott virtuális hálózat már rendelkezik, válassza ki a **meglévő**.
  -  **Alhálózati azonosító**. Ha azt szeretné, helyezheti üzembe a virtuális gép egy meglévő Vnetet, amelyekben egy meghatározott alhálózatot a virtuális gép hozzá kell rendelni, nevezze el a kívánt alhálózatot. Az azonosító általában néz ki: /subscriptions/ <*előfizetés-azonosító*> /resourceGroups/ <*erőforráscsoport-név*> /providers/Microsoft.Network/virtualNetworks/ < *virtuális hálózat neve*> /subnets/ <*alhálózat neve*>

A sablon üzembe helyez egy Azure Load Balancer példányt, amely támogatja a több SAP-rendszereinket.

- Az ASCS példányok úgy vannak konfigurálva, a példány számát 00, 10, 20...
- Az SCS-példányok száma 01, 11., 21 konfigurált...
- Az ASCS sorba replikációs kiszolgáló (SSZON) (csak Linux) példányok úgy vannak konfigurálva, a példány a 02, 12, 22-es szám...
- Az SCS SSZON (csak Linux) példányok úgy vannak konfigurálva, a példányok száma 03., 13., 23...

A load balancer tartalmaz (2. Linux esetén) 1 VIP(s), ASCS/SCS virtuális 1 x IP és 1 x VIP SSZON (csak Linux) számára.

Az alábbi lista tartalmazza az összes terheléselosztási szabályok (ahol x a az SAP-rendszerhez, például 1, 2, 3... száma):
- Windows-specifikus portok minden SAP rendszere esetében: 445, 5985
- ASCS portok (x0 száma): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-portok (x1 száma): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS SSZON portok (példányszámának x2) Linux rendszeren: 33x2, 5x213, 5x214, 5x216
- SCS SSZON portok (példányszámának x3) Linux rendszeren: 33x3, 5x313, 5x314, 5x316

A terheléselosztó a következő mintavételi portokon (Ha x az a szám az SAP-rendszer, például 1, 2, 3...) használatára van konfigurálva:
- ASCS/SCS belső load balancer mintavételi port: 620x0
- SSZON belső load balancer mintavételi portot (csak Linux): 621x2

#### <a name="database-template"></a> Adatbázis-sablon

Az adatbázis-sablon üzembe helyez egy vagy két virtuális gépek, amelyek egy SAP-rendszer relációsadatbázis-kezelő rendszerének (RDBMS) telepítéséhez használhatja. Például ha telepít egy ASCS/SCS-sablon az öt SAP-rendszereit, szüksége ötször a sablon üzembe helyezésére.

A beállítása az adatbázis több biztonsági AZONOSÍTÓVAL sablon, a [adatbázis több biztonsági AZONOSÍTÓVAL sablon] [ sap-templates-3-tier-multisid-db-marketplace-image] vagy [felügyelt lemezek használatával több biztonsági AZONOSÍTÓVAL adatbázissablon] [ sap-templates-3-tier-multisid-db-marketplace-image-md], adja meg az értékeket az alábbi paraméterekkel:

- **Rendszer-azonosító SAP**. Adja meg az SAP az SAP-rendszer telepíteni kívánt rendszer-azonosító. Az azonosító az üzembe helyezett erőforrások használható előtagjaként.
- **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerének.
- **DbType**. Válassza ki az adatbázist a fürtön telepíteni kívánt típusát. Válassza ki **SQL** Ha Microsoft SQL Server telepítéséhez. Válassza ki **HANA** Ha azt tervezi, hogy az SAP HANA telepítése a virtuális gépeken. Ügyeljen arra, hogy válassza ki a megfelelő operációs rendszer típusa: válassza ki **Windows** SQL és a Hana Linux-disztribúciók válassza. Az Azure Load Balancer virtuális gépekhez csatlakoztatott lesz konfigurálva a kijelölt adatbázis típusát támogatja:
  * **SQL**. A load balancer terheléselosztás 1433-as porton lesz. Ellenőrizze, hogy ezt a portot használja az SQL Server Always On beállítás.
  * **HANA**. A load balancer fog terheléselosztás 35015 és 35017 portot. Mindenképpen telepítse az SAP HANA-példányok számát **50**.
  A load balancer mintavételi port 62550 fogja használni.
- **SAP-rendszer mérete**. Az új rendszer biztosít SAP számának megadása. Ha nem arra van szükség a rendszer hány SAP, kérje meg az SAP technológiai partnerek vagy rendszerintegrátor.
- **Rendszer rendelkezésre állását**. Válassza ki **magas rendelkezésre ÁLLÁSÚ**.
- **Rendszergazdai felhasználónév és jelszó rendszergazdai**. Hozzon létre egy új felhasználót, jelentkezzen be a gép is lehet.
- **Alhálózati azonosító**. Adja meg az alhálózatot, amelyet a ASCS/SCS-sablon üzembe helyezése során használt azonosítója vagy az alhálózatot, amelyet létrejött azonosítója a ASCS/SCS sablon központi telepítésének részeként.

#### <a name="application-servers-template"></a> Alkalmazássablon-kiszolgálók

Az alkalmazássablon kiszolgálók SAP-alkalmazáskiszolgáló-példányok egy SAP-rendszerhez használható két vagy több virtuális gépeket helyez üzembe. Például ha telepít egy ASCS/SCS-sablon az öt SAP-rendszereit, szüksége ötször a sablon üzembe helyezésére.

A beállítása az kiszolgálók több biztonsági AZONOSÍTÓVAL sablon, a [alkalmazássablon kiszolgálók több biztonsági AZONOSÍTÓVAL] [ sap-templates-3-tier-multisid-apps-marketplace-image] vagy [kiszolgálók több biztonsági AZONOSÍTÓVAL alkalmazássablonfelügyeltlemezekhasználatával] [ sap-templates-3-tier-multisid-apps-marketplace-image-md], adja meg az értékeket az alábbi paraméterekkel:

  -  **Rendszer-azonosító SAP**. Adja meg az SAP az SAP-rendszer telepíteni kívánt rendszer-azonosító. Az azonosító az üzembe helyezett erőforrások használható előtagjaként.
  -  **Operációs rendszer típusa**. Válassza ki a virtuális gépek operációs rendszerének.
  -  **SAP-rendszer mérete**. Az új rendszer biztosít SAP száma. Ha nem arra van szükség a rendszer hány SAP, kérje meg az SAP technológiai partnerek vagy rendszerintegrátor.
  -  **Rendszer rendelkezésre állását**. Válassza ki **magas rendelkezésre ÁLLÁSÚ**.
  -  **Rendszergazdai felhasználónév és jelszó rendszergazdai**. Hozzon létre egy új felhasználót, jelentkezzen be a gép is lehet.
  -  **Alhálózati azonosító**. Adja meg az alhálózatot, amelyet a ASCS/SCS-sablon üzembe helyezése során használt azonosítója vagy az alhálózatot, amelyet létrejött azonosítója a ASCS/SCS sablon központi telepítésének részeként.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Az Azure virtual network
Ebben a példában az Azure virtuális hálózat címtartománya 10.0.0.0/16. Van egy nevű alhálózat **alhálózati**, a 10.0.0.0/24 címtartományt. A virtuális hálózatban lévő virtuális gépek és a belső terheléselosztók vannak telepítve.

> [!IMPORTANT]
> A vendég operációs rendszeren belül a hálózati beállításokat nem kell semmit módosítania. Ez magában foglalja az IP-címek, DNS-kiszolgálók és alhálózat. A hálózati beállítások konfigurálása az Azure-ban. The Dynamic Host Configuration Protocol (DHCP) service propagates your settings.
>
>

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP-címek

Állítsa be a szükséges DNS-IP-címek, hajtsa végre a következő lépéseket.

1. Az Azure Portalon az a **DNS-kiszolgálók** panelen ellenőrizze, hogy a virtuális hálózat **DNS-kiszolgálók** beállítás **egyéni DNS**.
2. Válassza ki a beállításokat, rendelkezik hálózat típusa alapján. További információkért lásd a következőket:
   * [Vállalati hálózati kapcsolat (létesítmények közötti)][planning-guide-2.2]: Adja hozzá a helyi DNS-kiszolgálók IP-címét.  
   Kiterjesztheti a helyszíni DNS-kiszolgálók Azure-ban futó virtuális gépekhez. Ebben az esetben a DNS-szolgáltatást futtató Azure virtuális gépek IP-címek is hozzáadhat.
   * A virtuális gépek üzembe helyezése az Azure-ban logikailag: A virtuális hálózat példányt, amely egy DNS-kiszolgálót egy további virtuális gép üzembe helyezése. Adja hozzá az Azure virtuális gépeket, amelyek a DNS-szolgáltatás futtatásához beállította az IP-címét.

   ![12. ábra: Azure virtuális hálózat DNS-kiszolgálók konfigurálása][sap-ha-guide-figure-3001]

   _**12. ábra:** Azure virtuális hálózat DNS-kiszolgálók konfigurálása_

   > [!NOTE]
   > Ha módosítja a DNS-kiszolgálók IP-címek, meg kell indítania az Azure virtuális gépeket, a módosítás alkalmazása és az új DNS-kiszolgálók propagálása.
   >
   >

Ebben a példában a DNS-szolgáltatás telepítve és konfigurálva van a Windows virtuális gépeken:

| Virtuális gépi szerepkör | Virtuális gép állomásneve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első DNS-kiszolgáló |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Second DNS server |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Állomásnév és az SAP ASCS/SCS fürtözött példányát és az adatbázis-kezelő fürtözött példány statikus IP-címek

A helyszíni telepítéshez szüksége ezen fenntartott neveket és IP-címek:

| Virtuális állomás neve szerepkör | Virtuális állomás neve | Virtuális statikus IP-cím |
| --- | --- | --- |
| Az SAP ASCS/SCS első fürt virtuális host name (kezelő) |pr1-ascs-vir |10.0.0.42 |
| Az SAP ASCS/SCS példányhoz virtuális állomás neve |pr1-ascs-sap |10.0.0.43 |
| Az SAP DBMS második fürt virtuális állomás neve (kezelő) |pr1-dbms-vir |10.0.0.32 |

A fürt létrehozásakor meg létrehozni a virtuális állomásnevek **pr1 – ascs-vir** és **pr1 – dbms-vir** és kezelheti a fürthöz társított IP-címeket. Ennek módjáról további információkért lásd: [fürtcsomópontok fürtkonfiguráció gyűjtése][sap-ha-guide-8.12.1].

A másik két virtuális állomásnevek, manuálisan is létrehozhat **pr1 – ascs-sap** és **pr1 – dbms-sap**, és a társított IP-címek, a DNS-kiszolgálón. A fürtözött SAP ASCS/SCS-példány és a fürtözött adatbázis-kezelő példány használja ezeket az erőforrásokat. Ennek módjáról további információkért lásd: [hozzon létre egy virtuális nevet a fürtözött SAP ASCS/SCS példányhoz][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Állítsa be a statikus IP-címeket, a SAP virtual Machines
A virtuális gépek használata a fürt üzembe helyezése, után minden virtuális gép statikus IP-címeket be kell. Ehhez az Azure Virtual Network konfigurációban, nem pedig a vendég operációs rendszer.

1. Az Azure Portalon válassza ki a **erőforráscsoport** > **hálózati kártya** > **beállítások** > **IP-cím**.
2. Az a **IP-címek** panel alatt **hozzárendelés**válassza **statikus**. Az a **IP-cím** mezőbe írja be a használni kívánt IP-címet.

   > [!NOTE]
   > Ha módosítja a hálózati kártya IP-címét, indítsa újra az Azure virtuális gépeket, a módosítás alkalmazására van szükség.  
   >
   >

   ![13. ábra: Statikus IP-címeket a hálózati kártya minden virtuális gép beállítása][sap-ha-guide-figure-3002]

   _**13. ábra:** Statikus IP-címeket a hálózati kártya minden virtuális gép beállítása_

   Ismételje meg ezt a lépést, amely van, minden virtuális gép, beleértve a virtuális gépeket, amelyek az Active Directory és DNS szolgáltatás használni kívánt összes hálózati adapter.

Ebben a példában rendelkezünk ezen virtuális gépek és a statikus IP-címek:

| Virtuális gépi szerepkör | Virtuális gép állomásneve | Hálózati kártya neve | Statikus IP-cím |
| --- | --- | --- | --- |
| Első SAP-alkalmazáskiszolgáló-példány |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Második SAP-alkalmazáskiszolgáló-példány |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Utolsó SAP-alkalmazáskiszolgáló-példány |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Első fürtcsomópontra ASCS/SCS példányhoz |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Második fürtcsomópontra ASCS/SCS példányhoz |PR1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| DBMS-példányra az első fürtcsomópontra |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Második fürtcsomópontra DBMS-példány |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Az Azure belső terheléselosztó statikus IP-cím beállítása

Az SAP az Azure Resource Manager-sablont hoz létre az Azure belső terheléselosztó az SAP ASCS/SCS példányhoz és az adatbázis-kezelő fürtön használt.

> [!IMPORTANT]
> Az IP-címe az SAP ASCS/SCS virtuális állomás neve megegyezik az SAP ASCS/SCS belső terheléselosztó IP-címe: **pr1-lb-ascs**.
> A DBMS neve virtuális IP-címe megegyezik a DBMS belső terheléselosztó IP-címe: **pr1-lb-dbms**.
>
>

Az Azure belső terheléselosztó statikus IP-címet beállítani:

1. Az első üzembe helyezés beállítja a belső terheléselosztó IP-címe **dinamikus**. Az Azure Portalon az a **IP-címek** panel alatt **hozzárendelés**, jelölje be **statikus**.
2. A belső terheléselosztó IP-cím beállítva **pr1-lb-ascs** az SAP ASCS/SCS-példány állomásneve, IP-címet.
3. A belső terheléselosztó IP-cím beállítva **pr1-lb-dbms** virtuális állomás nevét a DBMS-példány IP-címet.

   ![14. ábra: Állítsa be a statikus IP-címeket a belső terheléselosztó az SAP ASCS/SCS példányhoz][sap-ha-guide-figure-3003]

   _**14. ábra:** Állítsa be a statikus IP-címeket a belső terheléselosztó az SAP ASCS/SCS példányhoz_

Ebben a példában van két Azure belső terheléselosztó, amely a statikus IP-címet:

| Az Azure belső terheléselosztói szerepkör | Az Azure belső terheléselosztó neve | Statikus IP-cím |
| --- | --- | --- |
| Az SAP ASCS/SCS példány belső terheléselosztót |PR1-lb-ascs |10.0.0.43 |
| Az SAP DBMS belső load balancer |pr1-lb-dbms |10.0.0.33 |


### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Alapértelmezett ASCS/SCS-terheléselosztási szabályok az Azure belső terheléselosztó

Az SAP az Azure Resource Manager-sablont hoz létre a szükséges portokat:
* ABAP ASCS példányok, az alapértelmezett példányszámának **00**
* A Java SCS példányhoz, az alapértelmezett példány számmal **01**

Az SAP ASCS/SCS példányhoz telepítésekor kell használnia az alapértelmezett példányszámának **00** az ABAP ASCS-példány és az alapértelmezett példányszámának **01** a Java SCS példányhoz.

Ezután hozza létre a szükséges belső terheléselosztási végpont esetében az SAP NetWeaver-portokat.

Szükséges belső terheléselosztási végpont létrehozásához először hozza létre a terheléselosztási végpontok a SAP NetWeaver ABAP ASCS portok:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (ASCS példány példányszámának 00) (10 SSZON) |
| --- | --- | --- |
| Enqueue Server / *lbrule3200* |32 <*példányszám*> |3200 |
| ABAP Message Server / *lbrule3600* |36 <*példányszám*> |3600 |
| Internal ABAP Message / *lbrule3900* |39 <*példányszám*> |3900 |
| A kiszolgáló HTTP-üzenet / *Lbrule8100* |81-es <*példányszám*> |8100 |
| SAP Start Service ASCS HTTP / *Lbrule50013* |5 <*példányszám*> 13 |50013 |
| SAP Start Service ASCS HTTPS / *Lbrule50014* |5 <*példányszám*> 14 |50014 |
| Sorba replikációs / *Lbrule50016* |5 <*példányszám*> 16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5 <*példányszám*> 13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5 <*példányszám*> 14 |51014 |
| Erőforrás-kezelő Win *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

_**1. táblázat:** Az SAP NetWeaver ABAP ASCS példányainak portszámok_

Ezután hozzon létre a terheléselosztási végpontok a SAP NetWeaver Java SCS portok:

| Szolgáltatás/terheléselosztási szabály neve | Alapértelmezett portszámok | Konkrét portok (SCS példányhoz az példányszámának 01) (SSZON-11) |
| --- | --- | --- |
| Sorba Server / *lbrule3201* |32 <*példányszám*> |3201 |
| Átjárókiszolgáló / *lbrule3301* |33 <*példányszám*> |3301 |
| Java Message kiszolgáló / *lbrule3900* |39 <*példányszám*> |3901 |
| Message Server HTTP / *Lbrule8101* |81-es <*példányszám*> |8101 |
| SAP Start Service SCS HTTP / *Lbrule50113* |5 <*példányszám*> 13 |50113 |
| SAP Start Service SCS HTTPS / *Lbrule50114* |5 <*példányszám*> 14 |50114 |
| Sorba replikációs / *Lbrule50116* |5 <*példányszám*> 16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5 <*példányszám*> 13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5 <*példányszám*> 14 |51114 |
| Erőforrás-kezelő Win *Lbrule5985* | |5985 |
| Fájlmegosztás *Lbrule445* | |445 |

_**2. táblázat:** Az SAP NetWeaver Java SCS példányainak portszámok_

![15. ábra: Alapértelmezett ASCS/SCS-terheléselosztási szabályok az Azure belső terheléselosztó][sap-ha-guide-figure-3004]

_**15. ábra:** Alapértelmezett ASCS/SCS-terheléselosztási szabályok az Azure belső terheléselosztó_

A terheléselosztó IP-cím beállítva **pr1-lb-dbms** virtuális állomás nevét a DBMS-példány IP-címet.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Az ASCS/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztó módosítása

Ha azt szeretné, az SAP ASCS vagy SCS példányokhoz használandó eltérő számú, módosítania kell neveket és értékeket a portok alapértelmezett értékek közül.

1. Az Azure Portalon válassza ki a  **< *SID*> - lb - ascs terheléselosztó** > **terheléselosztási szabályok betöltése**.
2. Az összes terheléselosztási szabályok, amelyek az SAP ASCS vagy SCS példányhoz tartozik módosítsa ezeket az értékeket:

   * Name (Név)
   * Port
   * Háttér-port

   Például ha szeretné módosítani az alapértelmezett ASCS példányszámának 00 31-ig, meg kell hajtsa végre a módosításokat az összes port az 1.

   Íme egy példa egy frissítést, a port *lbrule3200*.

   ![16. ábra: Az ASCS/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztó módosítása][sap-ha-guide-figure-3005]

   _**16. ábra:** Az ASCS/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztó módosítása_

### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Windows virtuális gépek hozzáadása a tartományhoz

Miután statikus IP-címet rendel a virtuális gépek, virtuális gépeket adni a tartományhoz.

![17. ábra: Adjon hozzá egy virtuális gépet egy tartományhoz][sap-ha-guide-figure-3006]

_**17. ábra:** Adjon hozzá egy virtuális gépet egy tartományhoz_

### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Adja hozzá a beállításjegyzék-bejegyzések az SAP ASCS/SCS-példányának mindkét fürtcsomóponton

Az Azure Load Balancer belső terheléselosztót, hogy bezárul kapcsolatok, ha a megadott kapcsolatok üresjárati idő (üresjárati időkorlátot) rendelkezik. A párbeszédpanelen példányok nyitott kapcsolatok az SAP sorbaállítása SAP munkafolyamatok feldolgozni, amint az első sorba/eltávolítása a sorból kérelem kell küldeni. Ezek a kapcsolatok általában marad megalapozott a work folyamat, vagy sorba folyamata újraindul. Azonban ha a kapcsolat egy meghatározott ideig tétlen, az Azure belső terheléselosztó bezárja a kapcsolatokat. Ez nem probléma, mivel az SAP rendellenesnek akkor újra létrehozza a kapcsolatot a sorba folyamatot, ha már nem létezik. Ezeket a tevékenységeket a fejlesztői nyomkövetések SAP folyamatok vannak dokumentálva, de ezeket a nyomkövetéseket a felesleges tartalmat nagy mennyiségű hoznak létre. Módosíthatja a TCP/IP-érdemes `KeepAliveTime` és `KeepAliveInterval` mindkét fürtcsomóponton. Ezeket a módosításokat a TCP/IP-paraméterek SAP profil paraméterek, a cikk későbbi részében leírt össze.

Az SAP ASCS/SCS-példányának mindkét fürtcsomóponton beállításjegyzék-bejegyzések hozzáadásához először adja hozzá a Windows beállításjegyzék-bejegyzések mindkét Windows fürtcsomópontokon az SAP ASCS/SCS:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveTime` |
| A változó típusa |REG_DWORD (Decimal) |
| Érték |120000 |
| Dokumentáció mutató hivatkozás |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

_**3. táblázat:** Módosítsa az első TCP/IP-paraméter_

Ezután adja hozzá a Windows beállításjegyzék-bejegyzések mindkét Windows fürtcsomópontokon az SAP ASCS/SCS:

| Útvonal | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Változó neve |`KeepAliveInterval` |
| A változó típusa |REG_DWORD (Decimal) |
| Érték |120000 |
| Dokumentáció mutató hivatkozás |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

_**4. táblázat:** Módosítsa a második TCP/IP-paraméter_

**A módosítások életbe léptetéséhez indítsa újra a mindkét fürtcsomópont**.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Fürt létrehozása a Windows Server feladatátvételi fürtszolgáltatási egy SAP ASCS/SCS példányhoz

Az SAP ASCS/SCS példányhoz egy Windows Server feladatátvételi fürtszolgáltatási fürt beállítása magában foglalja ezeket a feladatokat:

- Fürtözött konfigurációban a fürt csomópontjai gyűjtése
- A fürt tanúsító fájlmegosztás beállítása

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Fürtözött konfigurációban a fürt csomópontjai gyűjtése

1. A szerepkör hozzáadása és szolgáltatások varázsló adja hozzá a Feladatátvételi fürtszolgáltatást mindkét fürtcsomóponton.
2. Állítsa be a feladatátvevő fürt Feladatátvevőfürt-kezelő használatával. A Feladatátvevőfürt-kezelőben válassza **-fürt létrehozása**, majd adja hozzá a csak az első fürt,. a csomópont neve A második csomópont ne adjon hozzá még; a második csomópont egy későbbi lépésben fogja hozzáadni.

   ![18. ábra: Adja hozzá az első fürtcsomópontra a kiszolgáló vagy virtuális gép neve][sap-ha-guide-figure-3007]

   _**18. ábra:** Adja hozzá az első fürtcsomópontra a kiszolgáló vagy virtuális gép neve_

3. Adja meg a fürt hálózati neve (virtuális állomás neve).

   ![19. ábra: Adja meg a fürt neve][sap-ha-guide-figure-3008]

   _**19. ábra:** Adja meg a fürt neve_

4. Miután létrehozta a fürthöz, futtassa a fürtellenőrzési tesztet.

   ![20. ábra: A fürt-ellenőrzés futtatása][sap-ha-guide-figure-3009]

   _**20. ábra:** A fürt-ellenőrzés futtatása_

   A folyamat ezen a ponton lemezekkel kapcsolatos figyelmeztetéseket figyelmen kívül hagyhatja. Egy tanúsító fájlmegosztást és az SIOS megosztott lemezeket később fogja hozzáadni. Ebben a szakaszban nem kell foglalkoznia a kvóruma.

   ![21. ábra: Nincs kvórum lemez található][sap-ha-guide-figure-3010]

   _**21. ábra:** Nincs kvórum lemez található_

   ![22. ábra: Core fürterőforrás kell új IP-cím][sap-ha-guide-figure-3011]

   _**22. ábra:** Core fürterőforrás kell új IP-cím_

5. Módosítsa a core fürtszolgáltatás IP-címét. A fürt nem indítható el, amíg nem módosítja a core fürtszolgáltatás IP-címét, mert a kiszolgáló IP-címét a virtuálisgép-csomópontok egyikét mutat. Ehhez a **tulajdonságok** lapján a core fürtszolgáltatás IP-erőforrást.

   Például kell hozzárendelni az IP-cím (ebben a példában **10.0.0.42**) a fürt virtuális állomás neve **pr1 – ascs-vir**.

   ![23. ábra: A Tulajdonságok párbeszédpanelen módosítsa az IP-cím][sap-ha-guide-figure-3012]

   _**23. ábra:** Az a **tulajdonságok** párbeszédpanelen módosítsa az IP-cím_

   ![24. ábra: A fürt számára fenntartott IP-cím hozzárendelése][sap-ha-guide-figure-3013]

   _**24. ábra:** A fürt számára fenntartott IP-cím hozzárendelése_

6. A fürt virtuális állomás neve online állapotba.

   ![25. ábra: Core szolgáltatás működik és fut, és a megfelelő IP-cím][sap-ha-guide-figure-3014]

   _**25. ábra:** Core szolgáltatás működik és fut, és a megfelelő IP-cím_

7. Adja hozzá a második fürtcsomópontra.

   Most, hogy a core fürtszolgáltatás helyezheti üzembe, a második fürtcsomópontra is hozzáadhat.

   ![26. ábra: Adja hozzá a második fürtcsomópontra][sap-ha-guide-figure-3015]

   _**26. ábra:** Adja hozzá a második fürtcsomópontra_

8. Adja meg a második fürt csomópont állomás nevét.

   ![27. ábra: Adja meg a második fürt állomásneve][sap-ha-guide-figure-3016]

   _**27. ábra:** Adja meg a második fürt állomásneve_

   > [!IMPORTANT]
   > Ügyeljen arra, hogy a **minden megfelelő tároló felvétele a fürtbe** jelölőnégyzet **nem** kiválasztott.  
   >
   >

   ![28. ábra: Jelölje be a jelölőnégyzetet][sap-ha-guide-figure-3017]

   _**28. ábra:** Tegye **nem** jelölje be a jelölőnégyzetet_

   Kvórum és a lemezek kapcsolatos figyelmeztetést figyelmen kívül hagyhatja. Fog a kvórum beállítása és a lemezt később, megoszthatja a leírtak szerint [telepítése az SIOS DataKeeper Cluster Edition a SAP ASCS/SCS fürtlemez-megosztás][sap-ha-guide-8.12.3].

   ![29. ábra: A lemez kvórumával kapcsolatos figyelmeztetések mellőzése][sap-ha-guide-figure-3018]

   _**29. ábra:** A lemez kvórumával kapcsolatos figyelmeztetések mellőzése_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> A fürt tanúsító fájlmegosztás beállítása

Ezeket a feladatokat a fürt tanúsító fájlmegosztás konfigurálása foglalja magában:

- Fájlmegosztás létrehozása
- A fájl megosztási tanúsító Kvórum beállítása a Feladatátvevőfürt-kezelő

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Fájlmegosztás létrehozása

1. Válassza ki a tanúsító fájlmegosztás helyett egy kvórumlemez. Az SIOS DataKeeper támogatja ezt a lehetőséget.

   A cikkben szereplő példák a tanúsító fájlmegosztás van, az Azure-ban fut-e az Active Directory és DNS-kiszolgálón. Tanúsító fájlmegosztás neve **domcontr-0**. Mivel lenne konfigurálva az Azure (Site-to-Site VPN- vagy Azure ExpressRoute) VPN-kapcsolat, az Active Directory és DNS szolgáltatás a helyszíni, és nem megfelelő futtatni egy tanúsító ossza meg.

   > [!NOTE]
   > Ha az Active Directory és DNS-szolgáltatás csak a helyszínen fut, az Active Directory és DNS Windows operációs rendszeren, amelyen fut a helyi ne konfigurálja a tanúsító fájlmegosztás. Előfordulhat, hogy az Azure és az Active Directory és a DNS a helyszínen futó fürtcsomópontok közötti hálózati késés túl nagy, és a kapcsolódási problémák miatt. Győződjön meg arról, tanúsító fájlmegosztás beállítása egy Azure virtuális gépen, amelyen fut a fürtcsomópont közelében.  
   >
   >

   A kvórum meghajtón legalább 1024 MB szabad terület van szüksége. Azt javasoljuk, hogy a 2048 MB-nyi szabad terület a kvórum.

2. Adja hozzá a fürtnévobjektum.

   ![30. ábra: Rendelje hozzá a megosztást a fürtnévobjektum engedélyei][sap-ha-guide-figure-3019]

   _**30. ábra:** Rendelje hozzá a megosztást a fürtnévobjektum engedélyei_

   Győződjön meg, hogy az engedélyek tartalmazzák-e a szolgáltató módosítása a fürtobjektum nevének a megosztás adataihoz (a példánkban a **pr1 – ascs-vir$**).

3. Válassza ki a fürtnévobjektum hozzáadása a listához, **Hozzáadás**. Módosítsa a szűrőt, hogy ellenőrizze a számítógép-objektumok 31. ábra szereplő termékektől mellett.

   ![31. ábra: Módosítsa a Gyorsítótárazandó objektumtípusok közé tartoznak a számítógépek][sap-ha-guide-figure-3020]

   _**31. ábra:** Módosítsa a Gyorsítótárazandó objektumtípusok közé tartoznak a számítógépek_

   ![32. ábra: Válassza ki a számítógép négyzet jelölését.][sap-ha-guide-figure-3021]

   _**32. ábra:** Válassza ki a **számítógépek** jelölőnégyzetet_

4. Írja be a fürt nevét, ahogyan a 31. ábra. A bejegyzést már létre van hozva, mert módosíthatja az engedélyeket, 30. ábrán látható módon.

5. Válassza ki a **biztonsági** lapján a megosztást, és állítsunk be részletesebb a fürtnévobjektum engedélyeit.

   ![33. ábra: A fájl megosztási kvórum a fürt neve objektum biztonsági attribútumainak beállítása][sap-ha-guide-figure-3022]

   _**33. ábra:** A fájl megosztási kvórum a fürt neve objektum biztonsági attribútumainak beállítása_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> A fájl megosztási tanúsító Kvórum beállítása a Feladatátvevőfürt-kezelő

1. Nyissa meg a fürtkvórum beállítása varázsló konfigurálja.

   ![34. ábra: A konfigurálás fürtkvórum beállítása varázsló indítása][sap-ha-guide-figure-3023]

   _**34. ábra:** A konfigurálás fürtkvórum beállítása varázsló indítása_

2. Az a **kvórumkonfiguráció kiválasztása** lapra, jelölje be **a kvórum tanúsítójának kijelölése**.

   ![35. ábra: A kvórumkonfiguráció közül választhat][sap-ha-guide-figure-3024]

   _**35. ábra:** A kvórumkonfiguráció közül választhat_

3. Az a **kvórum Tanúsítójának kijelölése** lapra, jelölje be **konfigurálja egy tanúsító fájlmegosztást**.

   ![36. ábra: Válassza ki a tanúsító fájlmegosztás][sap-ha-guide-figure-3025]

   _**36. ábra:** Válassza ki a tanúsító fájlmegosztás_

4. Adja meg a fájlmegosztás UNC elérési útját (a példánkban a \\domcontr-0\FSW). A módosításokat végezhet listájának megtekintéséhez válasszon **tovább**.

   ![37. ábra: Adja meg a fájlmegosztás helyét a tanúsító fájlmegosztás][sap-ha-guide-figure-3026]

   _**37. ábra:** Adja meg a fájlmegosztás helyét a tanúsító fájlmegosztás_

5. Válassza ki a módosításokat, majd válassza ki **tovább**. Sikeresen konfigurálja újra a fürt konfigurációját, 38. ábrán látható módon kell.  

   ![38. ábra: Jóváhagyás, hogy a fürt már újra konfigurálni][sap-ha-guide-figure-3027]

   _**38. ábra:** Jóváhagyás, hogy a fürt már újra konfigurálni_

A Windows feladatátvevő fürt sikeres telepítés után módosításokat kell tenni bizonyos küszöbértékek igazíthatja a feladatátvétel az észlelési feltételek az Azure-ban. Módosítani a paraméterei dokumentálva vannak ebben a blogbejegyzésben: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Feltételezve, hogy a két virtuális gépet hozhat létre a Windows-fürt konfigurációját ASCS/SCS ugyanazon az alhálózaton találhatók, a következő paramétereket kell módosítani ezeket az értékeket:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Ezek a beállítások az ügyfelekkel való tesztelését és egy jó biztonsági sérülés elég rugalmas az egyik oldalon megadott. Másrészről ezeket a beállításokat is biztosít gyors elegendő valós hibaállapotok-feladatátvétel az SAP-szoftver vagy csomópontot vagy Virtuálisgép-hiba. 

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Telepítse az SIOS DataKeeper Cluster Edition esetében az SAP ASCS/SCS fürtlemez-megosztás

Most már rendelkezik egy működő Windows Server feladatátvételi fürtszolgáltatás konfigurációs az Azure-ban. De, egy SAP ASCS/SCS példányhoz telepítéséhez, egy megosztott lemez erőforrást kell. Nem hozható létre a szükséges megosztott erőforrásokat az Azure-ban. Az SIOS DataKeeper Cluster Edition egy olyan külső megoldás, megosztott erőforrások létrehozására használhatja.

Ezek a feladatok telepítése az SIOS DataKeeper Cluster Edition a SAP ASCS/SCS fürtlemez-megosztás foglalja magában:

- A .NET-keretrendszer 3.5-ös hozzáadása
- Az SIOS DataKeeper telepítése
- Az SIOS DataKeeper beállítása

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Adja hozzá a .NET-keretrendszer 3.5.
A Microsoft .NET-keretrendszer 3.5-ös verzióját nem automatikusan aktiválva, vagy a Windows Server 2012 R2 rendszeren telepített. Az SIOS DataKeeper használatához a .NET-keretrendszer kell az összes csomóponton, amelyet telepíteni DataKeeper, telepítenie kell a .NET-keretrendszer 3.5 a fürt összes virtuális gép vendég operációs rendszeren.

A .NET-keretrendszer 3.5 hozzáadandó két módja van:

- Használja a szerepkörök hozzáadása és a szolgáltatások varázsló Windows 39. ábrán látható módon.

  ![39. ábra: A .NET-keretrendszer 3.5 telepítése a szerepkörök hozzáadása és a szolgáltatások varázsló használatával][sap-ha-guide-figure-3028]

  _**39. ábra:** A .NET-keretrendszer 3.5 telepítése a szerepkörök hozzáadása és a szolgáltatások varázsló használatával_

  ![40. ábra: Telepítési folyamatjelző, amikor telepíti a .NET-keretrendszer 3.5-szerepkörök hozzáadása és szolgáltatások varázsló segítségével][sap-ha-guide-figure-3029]

  _**40. ábra:** Telepítési folyamatjelző, amikor telepíti a .NET-keretrendszer 3.5-szerepkörök hozzáadása és szolgáltatások varázsló segítségével_

- A parancssori eszköz a dism.exe használata szükséges. Az ilyen típusú telepítés kell a a Windows-telepítési adathordozón lévő SxS könyvtár eléréséhez. Egy rendszergazda jogú parancssort írja be:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Telepítse az SIOS DataKeeper

Telepítse az SIOS DataKeeper Cluster Edition a fürt egyes csomópontjaihoz. Az SIOS DataKeeper virtuális megosztott tároló létrehozásához, hozzon létre egy szinkronizált tükrözött, és ezután szimulálja a fürt megosztott tárolójába.

Mielőtt a SIOS szoftver telepítése, létre kell hoznia a tartományi felhasználó **DataKeeperSvc**.

> [!NOTE]
> Adja hozzá a **DataKeeperSvc** felhasználót, hogy a **helyi rendszergazdai** csoport mindkét fürtcsomóponton.
>
>

Az SIOS DataKeeper telepítése:

1. Telepítse az SIOS szoftvert mindkét fürtcsomóponton.

   ![Az SIOS telepítő][sap-ha-guide-figure-3030]

   ![41. ábra: Az SIOS DataKeeper telepítés első oldal][sap-ha-guide-figure-3031]

   _**41. ábra:** Az SIOS DataKeeper telepítés első oldal_

2. A 42. ábra megjelenő párbeszédpanelen jelölje ki a **Igen**.

   ![42. ábra: DataKeeper tájékoztatja, hogy a szolgáltatás le lesz tiltva][sap-ha-guide-figure-3032]

   _**42. ábra:** DataKeeper tájékoztatja, hogy a szolgáltatás le lesz tiltva_

3. A párbeszédpanelen látható 43. ábra, azt javasoljuk, hogy bejelölte **tartomány vagy a kiszolgáló fiók**.

   ![43. ábra: Az SIOS DataKeeper felhasználó kiválasztása][sap-ha-guide-figure-3033]

   _**43. ábra:** Az SIOS DataKeeper felhasználó kiválasztása_

4. Adja meg a tartományi fiók felhasználói nevét és az SIOS DataKeeper létrehozott jelszavakat.

   ![44. ábra: Adja meg a tartományi felhasználónevet és jelszót az SIOS DataKeeper telepítése][sap-ha-guide-figure-3034]

   _**44. ábra:** Adja meg a tartományi felhasználónevet és jelszót az SIOS DataKeeper telepítése_

5. Telepítse az SIOS DataKeeper példány a licenckulcs, 45. ábrán látható módon.

   ![45. ábra: Adja meg az SIOS DataKeeper licenckulcs][sap-ha-guide-figure-3035]

   _**45. ábra:** Adja meg az SIOS DataKeeper licenckulcs_

6. Amikor a rendszer kéri, indítsa újra a virtuális gépet.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Az SIOS DataKeeper beállítása

Miután telepítette az SIOS DataKeeper mindkét csomóponton, indítsa el a konfigurációs kell. A konfiguráció az a célja, hogy van csatolva a virtuális gépek mindegyike a további lemezek közötti szinkron adatreplikációt.

1. Indítsa el a DataKeeper felügyeleti és a konfigurációs eszközt, és válassza ki **Kapcsolódás kiszolgálóhoz**. (A 46. ábra ezt a beállítást a pirossal bekarikázva.)

   ![46. ábra: Az SIOS DataKeeper felügyeleti és a konfigurációs eszköz][sap-ha-guide-figure-3036]

   _**46. ábra:** Az SIOS DataKeeper felügyeleti és a konfigurációs eszköz_

2. Adja meg a nevét vagy a felügyeleti és a konfigurációs eszközt, és a egy második lépésben, a második csomópont csatlakozni az első fürtcsomópont TCP/IP-címét.

   ![47. ábra: A nevét, vagy a TCP/IP-címét az első csomópontjára a felügyeleti és a konfigurációs eszközt, és a egy második lépésben, a második csomópont kell csatlakozniuk.][sap-ha-guide-figure-3037]

   _**47. ábra:** A nevét, vagy a TCP/IP-címét az első csomópontjára a felügyeleti és a konfigurációs eszközt, és a egy második lépésben, a második csomópont kell csatlakozniuk._

3. Hozzon létre a a két csomópont közötti replikálás feladatot.

   ![48. ábra: Replikációs feladat létrehozása][sap-ha-guide-figure-3038]

   _**48. ábra:** Replikációs feladat létrehozása_

   A varázsló végigvezeti egy replikációs feladat létrehozásának folyamatán.
4. Adja meg a nevét, a TCP/IP-cím és a forráscsomóponton lemezek mennyisége.

   ![49. ábra: Határozza meg a replikációs feladat nevét][sap-ha-guide-figure-3039]

   _**49. ábra:** Határozza meg a replikációs feladat nevét_

   ![50. ábra: A csomópont, amely lehet a jelenlegi forráscsomópont alapadatok megadása][sap-ha-guide-figure-3040]

   _**50. ábra:** A csomópont, amely lehet a jelenlegi forráscsomópont alapadatok megadása_

5. Adja meg a nevét, a TCP/IP-cím és a cél uzlu lemezkötetet.

   ![51. ábra: A csomópont, amely lehet a jelenlegi célcsomópont alapadatok megadása][sap-ha-guide-figure-3041]

   _**51. ábra:** A csomópont, amely lehet a jelenlegi célcsomópont alapadatok megadása_

6. A tömörítési algoritmust határozza meg. Ebben a példában azt javasoljuk, hogy a tömörítés a replikációs adatfolyam. Különösen abban az esetben az újraszinkronizálás a tömörítés a replikációs Stream jelentősen csökkenti az újraszinkronizálás idő. Vegye figyelembe, hogy a tömörítés a Processzor és memória erőforrások a virtuális gép használja. A tömörítési aránya nő, mivel így nem használt Processzor-erőforrások mennyiségét. Emellett módosíthatja ezt a beállítást később.

7. Egy másik beállítást kell ellenőrizni az e a replikáció aszinkron vagy szinkron módon történik-e. *Ha az SAP ASCS/SCS-konfigurációk, a szinkron replikáció kell használnia*.  

   ![52. ábra: Replikálás részletei][sap-ha-guide-figure-3042]

   _**52. ábra:** Replikálás részletei_

8. Adja meg e kell-e a kötetet, amelyet a rendszer replikálja a replikálási feladat által jelölt a Windows Server feladatátvételi fürtszolgáltatási fürtkonfiguráció megosztott lemez. Válassza ki az SAP ASCS/SCS konfiguráció **Igen** úgy, hogy a Windows-fürt látja a replikált kötet megosztott fürtkötet, ezáltal az lemez.

   ![53. ábra: Válassza az Igen lehetőséget a replikált kötetet állítja be a fürt kötet][sap-ha-guide-figure-3043]

   _**53. ábra:** Válassza ki **Igen** a replikált kötet beállítása a fürt kötetként_

   A kötet létrehozása után a DataKeeper felügyeleti és a konfigurációs eszköz azt mutatja, hogy a replikációs feladat aktív.

   ![54. ábra: DataKeeper szinkron tükrözés az SAP ASCS/SCS-megosztás lemez jelenleg aktív][sap-ha-guide-figure-3044]

   _**54. ábra:** DataKeeper szinkron tükrözés az SAP ASCS/SCS-megosztás lemez jelenleg aktív_

   Ahogy az 55. ábra a Feladatátvevőfürt-kezelő most DataKeeper lemezként, a lemez jeleníti meg.

   ![55. ábra: A Feladatátvevőfürt-kezelő jeleníti meg a lemezt, hogy DataKeeper replikált][sap-ha-guide-figure-3045]

   _**55. ábra:** A Feladatátvevőfürt-kezelő jeleníti meg a lemezt, hogy DataKeeper replikált_

## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Az SAP NetWeaver rendszer telepítése

Az adatbázis-kezelő a telepítő nem ismertetünk, mert mátrixok változhat az adatbázis-kezelő rendszert használja. Azonban feltételezzük, hogy az adatbázis-kezelő magas rendelkezésre állású kérdéseket az a Funkciók, a különböző DBMS-szállítók támogatja az Azure-ig. Például mindig bekapcsolva vagy adatbázis-tükrözést az SQL Server és Oracle Data Guard az Oracle-adatbázisokat. Az ebben a cikkben használjuk esetben nagyobb védelmet, az adatbázis-kezelő nem adtuk hozzá.

Nincsenek speciális megfontolásokat különböző DBMS-szolgáltatásokkal interakcióba ilyen konfigurációtípusok fürtözött SAP ASCS/SCS az Azure-ban.

> [!NOTE]
> A telepítési eljárások az SAP NetWeaver ABAP rendszerek, a Java-rendszerek és a ABAP + Java rendszerek majdnem azonosak. A legfontosabb különbség, hogy rendelkezik-e egy SAP ABAP-rendszer egyik ASCS-példány. Az SAP-Java-rendszer egyik SCS példányhoz rendelkezik. Az SAP ABAP + Java rendszer rendelkezik egy ASCS-példány és a egy, a feladatátvételi fürt ugyanazt a Microsoft csoportban futó SCS példányhoz. SAP NetWeaver-telepítés rengetegféle telepítési eltérések explicit módon szerepelnek. Akkor feltételezheti, hogy minden más részek azonosak.  
>
>

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Telepítse az SAP egy magas rendelkezésre állású ASCS/SCS példányhoz

> [!IMPORTANT]
> Győződjön meg arról, hogy ne helyezze el a lapozófájl DataKeeper tükrözött kötetek. DataKeeper nem támogatja a tükrözött kötetek. Az Azure virtuális gép ideiglenes D meghajtó a lapozófájl az alapértelmezett hagyhatja. Ha még nem szerepel ott, a Windows lapon fájl áthelyezéséhez az Azure virtuális gép a D: meghajtó.
>
>

Ezeket a feladatokat egy magas rendelkezésre állású ASCS/SCS-példánnyal rendelkező SAP telepítése foglalja magában:

- Virtuális állomásnevet a fürtözött SAP ASCS/SCS-példány létrehozása
- Az SAP első fürtcsomópontra telepítése
- Az SAP-profil ASCS/SCS-példány módosítása
- A mintavételi port hozzáadása
- A Windows tűzfal mintavételi port megnyitása

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Hozzon létre egy virtuális nevet a fürtözött SAP ASCS/SCS példányhoz

1. Hozzon létre egy DNS-bejegyzést a ASCS/SCS-példány virtuális állomás nevét a Windows DNS-kezelőben.

   > [!IMPORTANT]
   > Az ASCS/SCS-példány virtuális állomás nevét a hozzárendelt IP-cím ugyanaz, mint az Azure Load Balancer rendelt IP-cím kell lennie (**<*SID*> - lb - ascs**).  
   >
   >

   Az SAP ASCS/SCS virtuális állomásnév IP-címét (**pr1 – ascs-sap**) ugyanaz, mint az Azure Load Balancer IP-címét (**pr1-lb-ascs**).

   ![56. ábra: A DNS-bejegyzést az SAP ASCS/SCS-fürt virtuális nevét és a TCP/IP-cím megadása][sap-ha-guide-figure-3046]

   _**56. ábra:** A DNS-bejegyzést az SAP ASCS/SCS-fürt virtuális nevét és a TCP/IP-cím megadása_

2. A virtuális gazdagép neve rendelt IP-cím meghatározásához válassza **DNS-kezelő** > **tartomány**.

   ![57. ábra: Új virtuális nevét és a TCP/IP-cím az SAP ASCS/SCS-fürtkonfiguráció][sap-ha-guide-figure-3047]

   _**57. ábra:** Új virtuális nevét és a TCP/IP-cím az SAP ASCS/SCS-fürtkonfiguráció_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Telepítse az SAP első fürtcsomópontra

1. Hajtsa végre az első fürt csomópont lehetőség fürtcsomóponton rögzíti. Például a **pr1-ascs-0** gazdagép.
2. Tartsa meg az alapértelmezett portok az Azure belső load balancer-hez, válassza ki:

   * **ABAP rendszer**: **ASCS** példányok száma **00**
   * **Java-rendszer**: **SCS** példányok száma **01**
   * **ABAP + Java rendszer**: **ASCS** példányok száma **00** és **SCS** példányok száma **01**

   Az ABAP ASCS-példány és a Java SCS példányhoz 01 példány számok eltérő 00 használatához először módosítani szeretné az Azure belső load balancer alapértelmezett terheléselosztási szabályokat és ismertetett [az ASC/SCS alapértelmezett vonatkozó terheléselosztási szabályok módosítása az Azure belső terheléselosztó][sap-ha-guide-8.9].

A standard szintű SAP telepítési dokumentációjának a következő néhány feladatot nem ismerteti.

> [!NOTE]
> Az SAP telepítési dokumentáció ismerteti, hogyan lehet ASCS/SCS fürt első csomópontjára telepítse.
>
>

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Az SAP ASCS/SCS-példány módosításához

Adjon hozzá egy új profil paramétert kell. A profil paraméter megakadályozza, hogy az SAP-munkafolyamatok és a sorba kiszolgáló közötti kapcsolat bezárása, amelyek hosszabb ideig inaktív. A probléma esetén említettük [beállításjegyzék-bejegyzések hozzáadásához az SAP ASCS/SCS-példányának mindkét fürtcsomóponton][sap-ha-guide-8.11]. A szakasz emellett bevezettük két változást néhány alapszintű TCP/IP-kapcsolat paramétereit a. A második lépésben kell állítania a sorba kiszolgáló küld egy `keep_alive` jelezze, hogy a kapcsolatok nem nyomja le az Azure belső load balancer üresjárati küszöbértéket.

A módosításához az SAP ASCS/SCS-példány:

1. Ez a profil paraméter hozzáadása az SAP ASCS/SCS példányhoz profil:

   ```
   enque/encni/set_so_keepalive = true
   ```
   Ebben a példában az elérési út:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Például, hogy az SAP SCS példányhoz profil és a megfelelő elérési út:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. A módosítások életbe léptetéséhez indítsa újra az SAP ASCS /SCS példányt.

#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Adja hozzá a mintavételi port

A belső load balancer mintavételi funkciót használja, hogy az Azure Load Balancerrel együttműködve a teljes fürtkonfiguráció. Az Azure belső terheléselosztó általában a bejövő munkaterhelés részt vevő virtuális gépek között egyenlően osztja el. Azonban ez nem fog működni az egyes fürtkonfigurációk mert csak egy példány aktív. A másik példány passzív, és a munkaterhelés nem tud fogadni. Egy mintavételi funkció segítségével, amikor az Azure belső terheléselosztó munkahelyi csak egy aktív példány számára. A mintavétel funkciókkal a belső terheléselosztó képes észlelni, mely példányok aktív, és csak a számítási feladatok példány majd célként.

A mintavételi port hozzáadása:

1. Ellenőrizze a jelenlegi **ProbePort** beállítása a következő PowerShell-parancs futtatásával. Hajtsa végre a virtuális gépek egyikében, az a fürt konfigurációját.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. A mintavételi port megadása. Az alapértelmezett mintavételi portszám **0**. Ebben a példában a mintavételi portot használjuk **62000**.

   ![58. ábra: A fürt konfigurációt a mintavételi portot kötelező alapértelmezés szerint 0][sap-ha-guide-figure-3048]

   _**58. ábra:** Az alapértelmezett fürt konfigurációt a mintavételi portot kötelező 0_

   A port számát az SAP az Azure Resource Manager-sablonokkal van meghatározva. A port számát, a PowerShell rendelhet hozzá.

   Új ProbePort értéket beállítani a **SAP <*SID*> IP** fürterőforrás, futtassa a következő PowerShell-parancsfájlt. Frissítés a PowerShell változók környezete számára. A szkript futtatása után a rendszer kérni fogja, indítsa újra a SAP fürtcsoport a változások életbe lépjenek.

   ```PowerShell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Miután tenné a **SAP <*SID*>** a fürt hálózatra, ellenőrizze, hogy **ProbePort** az új értékre van állítva.

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![59. ábra: A fürt port mintavételi, miután beállította az új érték][sap-ha-guide-figure-3049]

   _**59. ábra:** A fürt port mintavételi, miután beállította az új érték_

#### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Nyissa meg a Windows tűzfal mintavételi port

Meg kell nyitnia a Windows tűzfal a mintavételi port mindkét fürtcsomóponton. Windows tűzfal a mintavételi port megnyitásához használja a következő szkriptet. Frissítés a PowerShell változók környezete számára.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

A **ProbePort** értékre van állítva **62000**. Most akkor is hozzáférhet a fájlmegosztáshoz  **\\\ascsha-clsap\sapmnt** más gazdagépekről, például, mint a **ascsha-adatbázisok**.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Az adatbázis-példány telepítése

Az adatbázis-példány telepítéséhez kövesse a SAP telepítési dokumentációban ismertetett folyamatot.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Telepítse a második fürtcsomópontra

A második fürt telepítéséhez kövesse a lépéseket az SAP telepítési útmutatóban.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Módosíthatja a az SAP on felhasználók Windows-példányok

A SAP SSZON Windows szolgáltatás indítási típusának módosítása **automatikus (Késleltetett indítás)** mindkét fürtcsomóponton.

![60. ábra: A szolgáltatás típusa az SAP SSZON példány módosíthatja a késleltetett automatikus][sap-ha-guide-figure-3050]

_**60. ábra:** A szolgáltatás típusa az SAP SSZON példány módosíthatja a késleltetett automatikus_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Az elsődleges alkalmazás SAP-kiszolgáló telepítése

Telepítse az elsődleges alkalmazás Server ATTRIBÚTUMKÉSZLETHEZ példányt <*SID*> - di - 0 ÜGYFÉLCÍMEK üzemeltetésére kijelölt már a virtuális gépen. Nincsenek függőségek az Azure-ban vagy DataKeeper-specifikus beállítások.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> További SAP alkalmazáskiszolgáló telepítése

Telepítse az SAP további Application Server (AAS), amely már egy SAP-alkalmazáskiszolgáló-példány futtatására kijelölt összes virtuális gépet. Ha például a <*SID*> - di - 1, <*SID*> - di -&lt;n&gt;.

> [!NOTE]
> Ez befejezi a magas rendelkezésre állású SAP NetWeaver-rendszer telepítését. Ezután folytassa feladatátvétel tesztelése.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Az SAP ASCS/SCS-példány feladatátvételt és SIOS replikáció tesztelése
Akkor is könnyen teszteléséhez és a egy SAP ASCS/SCS-példány feladatátvételt és SIOS lemez replikációs figyelemmel követhető a Feladatátvevőfürt-kezelő és az SIOS DataKeeper felügyeleti és a konfigurációs eszközt.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Az SAP ASCS/SCS példányhoz egy fürtcsomóponton fut.

A **SAP PR1** fürtcsoport fut, a fürtcsomópont-t. Ha például a **pr1-ascs-0**. Rendelje hozzá a megosztott lemezmeghajtó S, amely részét képezi, a **SAP PR1** fürtcsoport, és az ASC/SCS példányhoz használja, a fürt csomópont-t.

![61. ábra: A Feladatátvevőfürt-kezelő: Az SAP < SID > fürtcsoport fut egy fürtcsomóponton][sap-ha-guide-figure-5000]

_**61. ábra:** A Feladatátvevőfürt-kezelő: Az SAP <*SID*> fürtcsoport fut egy csomóponton_

Az SIOS DataKeeper felügyeleti és a konfigurációs eszközt láthatja, hogy a megosztott adatok a rendszer szinkron módon replikálja a fürtcsomópontra A forrás-kötet meghajtó S a céloldali kötet meghajtóról S fürtcsomópont b Például, hogy a rendszer replikálja a **pr1-ascs-0 [10.0.0.40]** való **pr1-ascs-1 [10.0.0.41]**.

![62. ábra: Az SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról egy fürtcsomóponton B][sap-ha-guide-figure-5001]

_**62. ábra:** Az SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról egy fürtcsomóponton B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Feladatátvételt egy csomópontról csomópontra B

1. Válasszon egyet az alábbi lehetőségek közül, az SAP feladatátvétel <*SID*> fürtcsoport fürtcsomópontról A b fürtcsomópontra
   - Használja a Feladatátvevőfürt-kezelőben  
   - A feladatátvevő fürt PowerShell-lel

   ```PowerShell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Indítsa újra a fürt csomópont A Windows vendég operációs rendszerből (Ez elindítja az SAP az automatikus feladatátvételt <*SID*> fürtcsoport egy csomópontról csomópontra B).  
3. Indítsa újra a fürt csomópont A az Azure Portal (Ez elindítja az SAP az automatikus feladatátvételt <*SID*> fürtcsoport egy csomópontról csomópontra B).  
4. Indítsa újra a csomópont A fürthöz az Azure PowerShell használatával (Ez elindítja az SAP az automatikus feladatátvételt <*SID*> fürtcsoport egy csomópontról csomópontra B).

   Az SAP a feladatátvételt követően <*SID*> fürtcsoport fut, a fürtcsomópont B. Ha például jelenleg is fut a **pr1-ascs-1**.

   ![63. ábra: A Feladatátvevőfürt-kezelő, a SAP < SID > fürtcsoport fut B fürtcsomóponton][sap-ha-guide-figure-5002]

   _**63. ábra**: A Feladatátvevőfürt-kezelő, a SAP <*SID*> fürtcsoport B csomóponton fut._

   A megosztott lemez már csatlakoztatva van a fürt csomópont b az SIOS DataKeeper van adatok meghajtóról forrás kötet S B fürtcsomóponton való replikálásához célmeghajtó kötet, S fürtcsomóponton A. A replikáló például **pr1-ascs-1 [10.0.0.41]** való **pr1-ascs-0 [10.0.0.40]**.

   ![64. ábra: Az SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról B, a csomópont A fürthöz][sap-ha-guide-figure-5003]

   _**64. ábra:** Az SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról B, a csomópont A fürthöz_
