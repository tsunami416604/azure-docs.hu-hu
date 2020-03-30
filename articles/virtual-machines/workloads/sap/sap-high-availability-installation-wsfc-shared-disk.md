---
title: Telepítse az SAP NetWeaver HA-t egy Windows feladatátvételi fürtre és megosztott lemezre egy SAP ASCS/SCS-példányhoz az Azure-ban | Microsoft dokumentumok
description: Megtudhatja, hogyan telepítheti az SAP NetWeaver HA-t egy Windows feladatátvevő fürtre és egy SAP ASCS/SCS-példány megosztott lemezére.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279831"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Telepítse az SAP NetWeaver HA-t egy Windows feladatátvételi fürtre és megosztott lemezre egy SAP ASCS/SCS-példányhoz az Azure-ban

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

Ez a cikk azt ismerteti, hogyan telepíthet és konfigurálhat egy magas rendelkezésre állású SAP-rendszert az Azure-ban egy Windows Server feladatátvevő fürt és fürt megosztott lemez használatával egy SAP ASCS/SCS-példány fürtözéséhez.

## <a name="prerequisites"></a>Előfeltételek

A telepítés megkezdése előtt tekintse át az alábbi dokumentumokat:

* [Architektúra-útmutató: SAP ASCS/SCS-példány fürtje windowsos feladatátvevő fürtön egy megosztott fürt használatával][sap-high-availability-guide-wsfc-shared-disk]

* [Az Azure-infrastruktúra előkészítése az SAP HA számára egy Windows feladatátvételi fürt és egy SAP ASCS/SCS-példány megosztott lemezének használatával][sap-high-availability-infrastructure-wsfc-shared-disk]

Ebben a cikkben nem ismertetjük a DBMS beállítását, mivel a beállítások a használt DBMS-rendszertől függően változnak. Feltételezzük, hogy a dbms magas rendelkezésre állású aggályait a különböző DBMS-szállítók által az Azure-hoz támogatott funkciókkal kezelik. Ilyenek például az AlwaysOn vagy az adatbázis-tükrözés az SQL Server és az Oracle Data Guard for Oracle adatbázisok számára. Az ebben a cikkben használt forgatókönyvben nem adunk hozzá további védelmet a DBMS-hez.

Nincsenek speciális szempontok, ha a különböző DBMS-szolgáltatások kommunikálnak egy fürtözött SAP ASCS vagy SCS konfiguráció az Azure-ban.

> [!NOTE]
> Az SAP NetWeaver ABAP rendszerek, Java rendszerek és ABAP+Java rendszerek telepítési eljárásai majdnem azonosak. A legjelentősebb különbség az, hogy egy SAP ABAP rendszer rendelkezik egy ASCS-példány. Az SAP Java rendszer egy SCS-példányt rendelkezik. Az SAP ABAP+Java rendszer egy ASCS-és egy SCS-példányt futtat ugyanabban a Microsoft feladatátvevő fürtcsoportban. Az egyes SAP NetWeaver telepítési veremtelepítési különbségek et kifejezetten megemlíti. Feltételezheti, hogy minden más rész ugyanaz.  
>
>

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Telepítse az SAP-t magas rendelkezésre állású ASCS/SCS-példányokkal

> [!IMPORTANT]
> Ügyeljen arra, hogy ne helyezze el a lapfájlt a SIOS DataKeeper tükrözött köteteken. A DataKeeper nem támogatja a tükrözött köteteket. A lapozófájlt az Azure virtuális gép D ideiglenes meghajtóján hagyhatja, amely az alapértelmezett. Ha még nincs ott, helyezze át a Windows-lapfájlt az Azure virtuális gép D-meghajtójára.
>
>

Az SAP telepítése magas rendelkezésre állású ASCS/SCS-példányokkal a következő feladatokat foglalja magában:

* Hozzon létre egy virtuális állomás nevet a fürtözött SAP ASCS/SCS-példány.
* Telepítse az SAP első fürtcsomópontját.
* Módosítsa az ASCS/SCS-példány SAP-profilját.
* Mintavételi port hozzáadása.
* Nyissa meg a Windows tűzfal mintavételi portját.

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Virtuális állomásnév létrehozása a fürtözött SAP ASCS/SCS-példányhoz

1. A Windows DNS-kezelőben hozzon létre egy DNS-bejegyzést az ASCS/SCS-példány virtuális állomásnevéhez.

   > [!IMPORTANT]
   > Az ASCS/SCS-példány virtuális állomásnevéhez rendelt IP-címnek meg kell egyeznie az Azure Load\<Balancerhez rendelt IP-címmel ( SID\>-lb-ascs).  
   >
   >

   A virtuális SAP ASCS/SCS állomásnév (pr1-ascs-sap) IP-címe megegyezik az Azure Load Balancer (pr1-lb-ascs) IP-címével.

   ![1. ábra: Az SAP ASCS/SCS fürt virtuális nevéhez és TCP/IP-címéhez tartozó DNS-bejegyzés megadása][sap-ha-guide-figure-3046]

   _**1. ábra:** Az SAP ASCS/SCS fürt virtuális nevének és A TCP/IP-címnek a DNS-bejegyzésének megadása_

2. A virtuális állomásnévhez rendelt IP-cím meghatározásához válassza a **DNS-kezelői** > **tartomány**lehetőséget.

   ![2. ábra: Új virtuális név és TCP/IP-cím az SAP ASCS/SCS fürtkonfigurációhoz][sap-ha-guide-figure-3047]

   _**2. ábra:** Új virtuális név és TCP/IP-cím az SAP ASCS/SCS fürtkonfigurációhoz_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Az SAP első fürtcsomópontjának telepítése

1. Az első fürtcsomópont-beállítás végrehajtása az A fürtcsomóponton. Például a pr1-ascs-0*host.
2. Az Azure belső terheléselosztó alapértelmezett portjainak megtartásához válassza a következőket:

   * **ABAP rendszer**: **00-as SZÁMÚ ASCS-példány** **00**
   * **Java rendszer**: **SCS** példány száma **01**
   * **ABAP+Java rendszer**: **00-as SZÁMÚ ASCS-példány** és **01-es** **00** **SCS-példányszám**

   Ha 00-tól eltérő példányszámokat szeretne használni az ABAP ASCS-példányhoz és a 01-hez a Java SCS-példányhoz, először módosítsa az Azure belső terheléselosztó alapértelmezett terheléselosztási szabályait. További információ: [Az ASCS/SCS alapértelmezett terheléselosztási szabályainak módosítása az Azure belső terheléselosztóhoz][sap-ha-guide-8.9]című témakörben olvashat.

A következő néhány feladat nem ismertetésa a szabványos SAP telepítési dokumentációban.

> [!NOTE]
> Az SAP telepítési dokumentációja leírja, hogyan kell telepíteni az első ASCS/SCS fürtcsomópontot.
>
>

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Az ASCS/SCS-példány SAP-profiljának módosítása

Először adjon hozzá egy új profilparamétert. A profilparaméter megakadályozza az SAP munkafolyamatok és a várólistára helyezett kiszolgáló közötti kapcsolatok bezárását, ha túl sokáig üresjáratban vannak. Megemlítjük a problémát az [SAP ASCS/SCS-példány mindkét fürtcsomópontjának rendszerleíró bejegyzéseinek hozzáadása című részben.][sap-ha-guide-8.11] Ebben a szakaszban két módosítást is bevezetünk néhány alapvető TCP/IP kapcsolati paraméterben. A második lépésben be kell állítania a `keep_alive` várólistára helyezett kiszolgálót, hogy jelet küldjön, hogy a kapcsolatok ne érjék el az Azure belső terheléselosztó tétlen küszöbértékét.

Az ASCS/SCS-példány SAP-profiljának módosítása:

1. Adja hozzá ezt a profilparamétert az SAP ASCS/SCS-példányprofilhoz:

   ```
   enque/encni/set_so_keepalive = true
   ```
   A mi példánkban az út a következő:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Például az SAP SCS-példány profil és a megfelelő elérési út:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. A módosítások alkalmazásához indítsa újra az SAP ASCS/SCS-példányt.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Mintavételi port hozzáadása

A belső terheléselosztó mintavételi funkciójával a teljes fürtkonfiguráció együttműködik az Azure Load Balancer használatával. Az Azure belső terheléselosztó általában egyenlően osztja el a bejövő számítási feladatokat a részt vevő virtuális gépek között.

 Ez azonban egyes fürtkonfigurációkban nem működik, mert csak egy példány aktív. A másik példány passzív, és nem tudja elfogadni a számítási feladatok egyikét sem. A mintavételi funkció segít, ha az Azure belső terheléselosztó csak egy aktív példányhoz rendeli hozzá a munkát. A mintavételi funkcióval a belső terheléselosztó észleli, hogy mely példányok aktívak, és csak a számítási feladattal rendelkező példányt célozza meg.

Mintavételi port hozzáadása:

1. Ellenőrizze az aktuális **ProbePort-értéket** a következő PowerShell-parancs futtatásával:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   A parancs végrehajtása a fürtkonfiguráció egyik virtuális gépén belül.

2. Mintavételi port definiálása. A mintavételi port alapértelmezett száma 0. Példánkban a 62000-es szondaportot használjuk.

   ![3. ábra: A fürtkonfiguráció mintavételi portja alapértelmezés szerint 0][sap-ha-guide-figure-3048]

   _**3. ábra:** A fürt konfigurációs mintavételi portjának alapértelmezett portja 0_

   A portszám az SAP Azure Resource Manager-sablonokban van definiálva. A portszámát a PowerShellben rendelheti hozzá.

   Új ProbePort-érték beállításához \<az\> SAP SID IP-fürterőforráshoz futtassa a következő PowerShell-parancsfájlt a környezetpowerShell-változóinak frissítéséhez:

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

   Miután online \<állapotba hozta az SAP SID-fürt\> szerepkört, ellenőrizze, hogy a **ProbePort** értéke az új értékre van-e beállítva.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   A parancsfájl futtatása után a rendszer kéri, hogy indítsa újra az SAP-fürtcsoportot a módosítások aktiválásához.

   ![4. ábra: A fürtport vizsgálata az új érték beállítása után][sap-ha-guide-figure-3049]

   _**4. ábra:** A fürtport vizsgálata az új érték beállítása után_

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>A Windows tűzfal mintavételi portjának megnyitása

Nyisson meg egy Windows tűzfal mintavételi portját mindkét fürtcsomóponton. A következő parancsfájl segítségével nyissa meg a Windows tűzfal mintavételi portját. Frissítse a PowerShell-változókat a környezetben.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**A ProbePort** **62000-re**van állítva. Most már más állomásokról, például ascsha-dbas-ból is elérheti a fájlmegosztást\ascsha-clsap\sapmnt. \\

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Az adatbázispéldány telepítése

Az adatbázis-példány telepítéséhez kövesse az SAP telepítési dokumentációjában ismertetett folyamatot.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>A második fürtcsomópont telepítése

A második fürt telepítéséhez kövesse az SAP telepítési útmutatójában ismertetett lépéseket.

## <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Az SAP ERS Windows szolgáltatáspéldány indítási típusának módosítása

Módosítsa az SAP ERS Windows-szolgáltatás indítási típusát **automatikusra (késleltetett indítás)** mindkét fürtcsomóponton.

![5. ábra: Módosítsa az SAP ERS-példány szolgáltatástípusát késleltetett automatikusra][sap-ha-guide-figure-3050]

_**5. ábra:** Módosítsa az SAP ERS-példány szolgáltatástípusát késleltetett automatikusra_

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Az SAP elsődleges alkalmazáskiszolgáló telepítése

Telepítse az elsődleges alkalmazáskiszolgáló \<(PAS) SID-di-0\>példányát arra a virtuális gépre, amelyet a PAS üzemeltetésére kijelölt. Nincsenek függőségek az Azure-tól. Nincsenek DataKeeper-specifikus beállítások.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Az SAP additional Application Server telepítése

Telepítsen egy SAP additional Application Server (AAS) rendszert az összes olyan virtuális gépen, amelyet egy SAP Application Server-példány üzemeltetésére kijelölt. Például a \<\>SID \<-di-1\>és SID&lt;&gt;-di- n között.

> [!NOTE]
> Ezzel befejezi a telepítést a magas rendelkezésre állású SAP NetWeaver rendszer. Ezután folytassa a feladatátvételi tesztelést.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Az SAP ASCS/SCS-példány feladatátvételének és a SIOS-replikációnak a tesztelése
Az SAP ASCS/SCS-példány feladatátvétele és a SIOS-lemezreplikáció tesztelése és figyelése a Feladatátvevő fürtkezelő és a SIOS DataKeeper Management and Configuration eszközzel könnyen ellenőrizhető és figyelhető.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>Az SAP ASCS/SCS-példány az A fürtcsomóponton fut

Az SAP PR1 fürtcsoport az A fürtcsomóponton fut. Például a pr1-ascs-0-n. Rendelje hozzá az SAP PR1 fürtcsoport részét képező S megosztott lemezmeghajtót az A fürtcsomóponthoz. Az ASCS/SCS példány az S lemezmeghajtót is használja. 

![6. ábra: Feladatátvevő \<fürtkezelő: Az SAP SID-fürtcsoport\> az A fürtcsomóponton fut][sap-ha-guide-figure-5000]

_**6. ábra:** Feladatátvevőfürt-kezelő: \<\> Az SAP SID-fürtcsoport az A fürtcsomóponton fut_

A SIOS DataKeeper Management and Configuration eszközben láthatja, hogy a megosztott lemezadatok szinkron módon replikálódnak az A fürtsen lévő S forráskötet-meghajtóról a B fürtcsomópont S célkötet-meghajtójára. Például a pr1-ascs-0 -ról [10.0.0.40] pr1-ascs-1 -re replikálódik [10.0.0.41].

![7. ábra: A SIOS DataKeeper alkalmazásban replikálja a helyi kötetet az A fürtcsomópontról a B fürtcsomópontra][sap-ha-guide-figure-5001]

_**7. ábra:** A SIOS DataKeeper alkalmazásban replikálja a helyi kötetet az A fürtcsomópontról a B fürtcsomópontra_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>Feladatátvétel az A csomópontról a B csomópontba

1. Az alábbi lehetőségek közül választhat az SAP \<\> SID-fürtcsoport feladatátvételének kezdeményezéséhez az A fürtből a B fürtcsomópontba:
   - Feladatátvevő fürtkezelő  
   - Feladatátvevő fürt PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Indítsa újra az A fürtcsomópontot a Windows vendég operációs rendszeren belül. Ez elindítja az SAP \<SID-fürtcsoport\> automatikus feladatátvételét az A csomópontról a B csomópontba.  
3. Indítsa újra az A fürtcsomópontot az Azure Portalról. Ez elindítja az SAP \<SID-fürtcsoport\> automatikus feladatátvételét az A csomópontról a B csomópontba.  
4. Indítsa újra az A fürtcsomópontot az Azure PowerShell használatával. Ez elindítja az SAP \<SID-fürtcsoport\> automatikus feladatátvételét az A csomópontról a B csomópontba.

   Feladatátvétel után \<az\> SAP SID-fürtcsoport a B fürtcsomóponton fut. Például pr1-ascs-1-en fut.

   ![8. ábra: A feladatátvevőfürt-kezelőben az SAP \<SID-fürtcsoport\> a B fürtcsomóponton fut][sap-ha-guide-figure-5002]

   _**8. ábra:** A feladatátvevőfürt-kezelőben az SAP \<SID-fürtcsoport\> a B fürtcsomóponton fut_

   A megosztott lemez most már csatlakoztatva van a B fürtcsomópontra. Például a pr1-ascs-1 [10.0.0.41] pr1-ascs-0 [10.0.0.40] között replikál.

   ![9. ábra: A SIOS DataKeeper replikálja a helyi kötetet a B fürtcsomópontról az A fürtcsomópontra][sap-ha-guide-figure-5003]

   _**9. ábra:** A SIOS DataKeeper replikálja a helyi kötetet a B fürtcsomópontról az A fürtcsomópontra_
