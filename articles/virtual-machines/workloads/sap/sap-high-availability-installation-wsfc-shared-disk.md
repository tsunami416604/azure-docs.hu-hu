---
title: SAP NetWeaver HA telepítése Windows feladatátvevő fürtön és megosztott lemezen az Azure-beli SAP ASCS/SCS-példányhoz | Microsoft Docs
description: Ismerje meg, hogyan telepítheti az SAP NetWeaver HA szolgáltatást egy Windows feladatátvevő fürtön, illetve egy SAP ASCS/SCS-példány megosztott lemezén.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e50733c843dfd21e35572f00fc6690e1e84aba97
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617357"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>SAP NetWeaver HA telepítése Windows feladatátvevő fürtön és megosztott lemezen az Azure-beli SAP ASCS/SCS-példányhoz

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

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
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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

Ez a cikk azt ismerteti, hogyan telepíthet és konfigurálhat egy magas rendelkezésre állású SAP-rendszert az Azure-ban egy olyan Windows Server feladatátvevő fürt és fürtözött megosztott lemez használatával, amely egy SAP ASCS/SCS-példány fürtözésére szolgál.

## <a name="prerequisites"></a>Előfeltételek

A telepítés megkezdése előtt tekintse át a következő dokumentumokat:

* [Architektúra-útmutató: SAP ASCS/SCS-példány fürtözése Windows feladatátvevő fürtön fürt megosztott lemezének használatával][sap-high-availability-guide-wsfc-shared-disk]

* [Készítse elő az Azure-infrastruktúrát az SAP-hez a Windows feladatátvevő fürt és az SAP ASCS/SCS-példány megosztott lemezének használatával][sap-high-availability-infrastructure-wsfc-shared-disk]

Ebben a cikkben nem írja le az adatbázis-kezelői konfigurációt, mert a beállítások a használt adatbázis-kezelő rendszertől függően változnak. Feltételezzük, hogy az adatbázis-kezelők magas rendelkezésre állással kapcsolatos problémái az Azure-t használó különböző adatbázis-kezelők által támogatott funkciókkal foglalkoznak. Ilyenek például a AlwaysOn vagy az adatbázis-tükrözés a SQL Server és az Oracle-alapú adatvédelemhez az Oracle-adatbázisokhoz. Az ebben a cikkben használt forgatókönyvben nem adunk hozzá további védelmet az adatbázis-kezelő szolgáltatáshoz.

Nincsenek különleges megfontolások, ha a különböző adatbázis-kezelő szolgáltatások egy fürtözött SAP-ASCS vagy SCS-konfigurációval működnek az Azure-ban.

> [!NOTE]
> Az SAP NetWeaver ABAP rendszerek, a Java-rendszerek és a ABAP + Java rendszerek telepítési eljárásai csaknem azonosak. A legfontosabb különbség az, hogy egy SAP ABAP rendszernek van egy ASCS-példánya. Az SAP Java-rendszeren egy SCS-példány van. Az SAP ABAP + Java rendszer egy ASCS-példánnyal és egy olyan SCS-példánnyal rendelkezik, amely ugyanabban a Microsoft feladatátvételi fürtben fut. A rendszer explicit módon megemlíti az egyes SAP NetWeaver telepítési verem telepítési különbségeit. Feltételezzük, hogy minden más rész azonos.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Az SAP telepítése magas rendelkezésre állású ASCS/SCS-példánnyal

> [!IMPORTANT]
> Ügyeljen arra, hogy a lapozófájlt a SIOS DataKeeper tükrözött köteteken helyezze el. A DataKeeper nem támogatja a tükrözött köteteket. A lapozófájlt egy Azure-beli virtuális gép ideiglenes meghajtójába helyezheti el, amely az alapértelmezett. Ha még nem tette meg, helyezze át a Windows-lapozófájlt az Azure-beli virtuális gép D meghajtóján.
>
>

A magas rendelkezésre állású ASCS/SCS-példánnyal rendelkező SAP telepítése a következő feladatokat foglalja magában:

* Hozzon létre egy virtuális állomásnevet a fürtözött SAP ASCS/SCS-példányhoz.
* Telepítse az SAP első fürtcsomópont-csomópontját.
* Módosítsa az ASCS/SCS-példány SAP-profilját.
* Vegyen fel egy mintavételi portot.
* Nyissa meg a Windows tűzfal mintavételi portját.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Virtuális állomásnév létrehozása a fürtözött SAP ASCS/SCS-példányhoz

1. A Windows DNS-kezelőben hozzon létre egy DNS-bejegyzést a ASCS/SCS-példány virtuális állomásnevét.

   > [!IMPORTANT]
   > A ASCS/SCS-példány virtuális állomásnevét hozzárendelt IP-címnek meg kell egyeznie a Azure Load Balancerhoz rendelt IP-címmel (\<SID\>-LB-ASCS).  
   >
   >

   A virtuális SAP ASCS/SCS-állomásnév (PR1-ASCS-SAP) IP-címe megegyezik a Azure Load Balancer IP-címével (PR1-LB-ASCS).

   ![1\. ábra: az SAP ASCS/SCS-fürt virtuális neve és a TCP/IP-cím DNS-bejegyzésének megadása][sap-ha-guide-figure-3046]

   _**1. ábra:** Adja meg a DNS-bejegyzést az SAP ASCS/SCS-fürt virtuális neve és TCP/IP-címe számára._

2. A virtuális gazdagép neveként hozzárendelt IP-cím megadásához válassza a **DNS-kezelő** > **tartomány**lehetőséget.

   ![2\. ábra: új virtuális név és TCP/IP-cím az SAP ASCS/SCS-fürt konfigurációjához][sap-ha-guide-figure-3047]

   _**2. ábra:** Új virtuális név és TCP/IP-cím az SAP ASCS/SCS-fürt konfigurációjához_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Az SAP első fürtcsomópont telepítése

1. Hajtsa végre az A fürt első csomópontjának beállítását a csomóponton. Például a PR1-ASCs-0 * gazdagépen.
2. Az Azure belső terheléselosztó alapértelmezett portjainak megtartásához válassza a következő lehetőséget:

   * **ABAP-System**: **ASCS** -példány száma **00**
   * **Java-System**: **SCS** -példány száma **01**
   * **ABAP + Java System**: **ASCS** -példány száma **00** és **SCS** -példány száma **01**

   Ha a 00-as vagy a Java SCS-példányhoz tartozó ASCS-példányok esetében a 00-nál nem több példányt szeretne használni, először módosítsa az Azure belső terheléselosztó alapértelmezett terheléselosztási szabályait. További információ: a [ASCS/SCS alapértelmezett terheléselosztási szabályok módosítása az Azure belső terheléselosztó számára][sap-ha-guide-8.9].

A következő néhány feladat nem szerepel a szabványos SAP-telepítési dokumentációban.

> [!NOTE]
> Az SAP telepítési dokumentációja leírja, hogyan telepítheti az első ASCS/SCS fürtcsomópont-csomópontot.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Az ASCS/SCS-példány SAP-profiljának módosítása

Először adjon hozzá egy új profil paramétert. A profil paraméter megakadályozza a kapcsolódást az SAP-munkafolyamatok és a sorba helyezni-kiszolgáló között, ha túl sokáig tétlenek. Megemlítjük a problémát a [beállításjegyzékbeli bejegyzések hozzáadása az SAP ASCS/SCS-példányhoz tartozó fürtcsomópontok esetében][sap-ha-guide-8.11]című témakörben. Ebben a szakaszban két módosítást is bevezetünk néhány Alapszintű TCP/IP-kapcsolódási paraméterre. Egy második lépésben úgy kell beállítania a sorba helyezni-kiszolgálót, hogy `keep_alive` jelet küldjön, hogy a kapcsolatok ne elérjenek az Azure belső terheléselosztó üresjárati küszöbértékét.

Az ASCS/SCS-példány SAP-profiljának módosítása:

1. Adja hozzá ezt a profil paramétert az SAP ASCS/SCS instance profilhoz:

   ```
   enque/encni/set_so_keepalive = true
   ```
   A példánkban az elérési út a következőket eredményezi:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Például az SAP SCS-példány profiljához és a megfelelő elérési úthoz:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. A módosítások alkalmazásához indítsa újra az SAP ASCS/SCS-példányt.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Mintavételi Port hozzáadása

A belső terheléselosztó mintavételi funkciójának használatával biztosíthatja, hogy a teljes fürtkonfiguráció működjön a Azure Load Balancer. Az Azure belső terheléselosztó általában a bejövő munkaterheléseket a részt vevő virtuális gépek között egyenlően osztja el.

 Ez azonban nem fog működni bizonyos fürtkonfiguráció esetén, mert csak egy példány aktív. A másik példány passzív, és nem fogadja el a számítási feladatok egyikét sem. A mintavételi funkciók segítenek abban az esetben, ha az Azure belső terheléselosztó csak aktív példányhoz rendeli a munkát. A mintavétel funkcióval a belső terheléselosztó képes érzékelni, hogy mely példányok aktívak, és csak a számítási feladattal rendelkező példányt célozzák meg.

Mintavételi Port hozzáadása:

1. A következő PowerShell-parancs futtatásával keresse meg az aktuális **ProbePort** értéket:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Futtassa a parancsot a fürt konfigurációjának egyik virtuális gépről.

2. Adjon meg egy mintavételi portot. A mintavétel alapértelmezett portszáma 0. A példánkban a 62000-es mintavételi portot használjuk.

   ![3\. ábra: a fürt konfigurációjának mintavételi portja alapértelmezés szerint 0.][sap-ha-guide-figure-3048]

   _**3. ábra:** Az alapértelmezett fürtkonfiguráció-mintavételi port a 0_

   A portszám az SAP Azure Resource Manager-sablonokban van meghatározva. A portszám a PowerShellben is hozzárendelhető.

   Ha új ProbePort értéket szeretne beállítani az SAP \<SID\> IP-fürterőforrás számára, futtassa a következő PowerShell-parancsfájlt a környezet PowerShell-változóinak frissítéséhez:

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure internal load balancer

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

   Miután az SAP \<SID\> a fürt szerepkörét online állapotba helyezte, ellenőrizze, hogy a **ProbePort** az új értékre van-e állítva.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   A szkript futtatása után a rendszer felszólítja, hogy indítsa újra az SAP-fürtöt a módosítások aktiválásához.

   ![4\. ábra: a fürt portjának mintavétele az új érték beállítása után][sap-ha-guide-figure-3049]

   _**4. ábra:** A fürt portjának mintavétele az új érték beállítása után_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>A Windows tűzfal mintavételi portjának megnyitása

Nyisson meg egy Windows tűzfal mintavételi portot mindkét fürtcsomópontokon. A következő parancsfájl használatával nyisson meg egy Windows tűzfal mintavételi portot. Frissítse a környezet PowerShell-változóit.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

A **ProbePort** értéke **62000**. Most már hozzáférhet a fájlmegosztás \\\ascsha-clsap\sapmnt más gazdagépekről, például a következőről: ascsha-adattervezők.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Az adatbázis-példány telepítése

Az adatbázis-példány telepítéséhez kövesse az SAP telepítési dokumentációjában leírt eljárást.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>A második fürtcsomópont telepítése

A második fürt telepítéséhez kövesse az SAP telepítési útmutatójában ismertetett lépéseket.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Az SAP ERS Windows-szolgáltatás példányának indítási típusának módosítása

Módosítsa az SAP ERS Windows szolgáltatás indítási típusát **automatikus (Késleltetett indítás)** értékre mindkét fürtcsomóponton.

![5\. ábra: a szolgáltatás típusának módosítása az SAP ERS-példányhoz automatikus késleltetve][sap-ha-guide-figure-3050]

_**5. ábra:** Az SAP ERS-példány szolgáltatás típusának megváltoztatása késleltetett automatikusra_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Az SAP Primary Application Server telepítése

Telepítse az elsődleges alkalmazáskiszolgáló (PAS) példányát \<SID\>-di-0 értéket azon a virtuális gépen, amelyet a PAS számára jelölt ki. Nincsenek függőségek az Azure-ban. Nincs DataKeeper-specifikus beállítás.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Az SAP további alkalmazáskiszolgáló telepítése

Telepítsen egy SAP további alkalmazáskiszolgáló (AAS) szolgáltatást minden olyan virtuális gépre, amelyet az SAP Application Server-példány üzemeltetésére jelölt ki. Például \<SID\>-di-1 \<SID\>-di-&lt;n&gt;.

> [!NOTE]
> Ez befejezi a magas rendelkezésre állású SAP NetWeaver rendszer telepítését. Ezután folytassa a feladatátvételi teszttel.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Az SAP-ASCS/SCS-példány feladatátvételi és SIOS-replikálásának tesztelése
A Feladatátvevőfürt-kezelő és a SIOS DataKeeper-kezelő és konfigurációs eszköz használatával egyszerűen tesztelheti és figyelheti az SAP ASCS/SCS-példányok feladatátvételét és SIOS.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>SAP ASCS/SCS-példány fut az A fürtcsomóponton

Az SAP PR1-fürt az A fürtcsomóponton fut. Például: PR1-ASCs-0. Rendelje hozzá a (z) "a" csomóponthoz az SAP PR1 részét képező megosztott lemezmeghajtókat. A ASCS/SCS-példány a következő lemezmeghajtókat is használja. 

![6\. ábra: Feladatátvevőfürt-kezelő: az SAP \<SID\> a fürt csomópontja az A fürtcsomóponton fut][sap-ha-guide-figure-5000]

_**6. ábra:** Feladatátvevőfürt-kezelő: az SAP \<SID\> a fürt csomópontja az A fürtcsomóponton fut_

A SIOS DataKeeper-kezelés és-konfigurálás eszközben láthatja, hogy a megosztott lemez adatainak szinkron módon replikálódnak a (z) "A" fürt " Például a rendszer replikálja a PR1-ASCs-0 [10.0.0.40] típusról a PR1-ASCs-1 [10.0.0.41] értékre.

![7\. ábra: a SIOS DataKeeper-ben replikálja a helyi kötetet a fürt csomópontból a B csomópontba][sap-ha-guide-figure-5001]

_**7. ábra:** A SIOS DataKeeper-ben replikálja a helyi kötetet a fürt csomópontból a B csomópontba._

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Feladatátvétel az A csomópontról a B csomópontra

1. Válassza ki az alábbi lehetőségek egyikét az SAP \<SID\>-fürt feladatátvételének elindításához a (z) "A" fürt "a" csomópontról a B fürtre:
   - Feladatátvevőfürt-kezelő  
   - Feladatátvevő fürt PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Indítsa újra az A fürtcsomópont-csomópontot a Windows vendég operációs rendszeren. Ez automatikusan feladatátvételt kezdeményez az SAP \<SID\> fürtcsomóponton az A csomópontról a B csomópontra.  
3. Indítsa újra az A fürtcsomópont-csomópontot a Azure Portal. Ez automatikusan feladatátvételt kezdeményez az SAP \<SID\> fürtcsomóponton az A csomópontról a B csomópontra.  
4. Indítsa újra az A fürtcsomópont Azure PowerShell használatával. Ez automatikusan feladatátvételt kezdeményez az SAP \<SID\> fürtcsomóponton az A csomópontról a B csomópontra.

   A feladatátvételt követően az SAP \<SID\> a fürt a B csomóponton fut. Például a PR1-ASCs-1 számítógépen fut.

   ![8\. ábra: a Feladatátvevőfürt-kezelő az SAP \<SID\> fürterőforrás a B fürtcsomóponton fut.][sap-ha-guide-figure-5002]

   _**8. ábra**: a FELADATÁTVEVŐFÜRT-kezelő az SAP \<SID\> fürterőforrás a B fürtcsomóponton fut._

   A megosztott lemez most már csatolva van a B csomóponthoz. a SIOS DataKeeper a B csomóponton lévő forrás kötet-meghajtóról származó adatok replikálását célozza meg a (z) "A" fürt "A" csomópontjára. A replikálás például a PR1-ASCs-1 [10.0.0.41] verzióról a PR1-ASCs-0 [10.0.0.40] verzióra.

   ![9\. ábra: a SIOS DataKeeper a B csomópontról replikálja a helyi kötetet a "A" fürt "A" csomópontjára][sap-ha-guide-figure-5003]

   _**9. ábra:** A SIOS DataKeeper replikálja a B fürtcsomópont helyi kötetét az A fürt csomópontjának_
