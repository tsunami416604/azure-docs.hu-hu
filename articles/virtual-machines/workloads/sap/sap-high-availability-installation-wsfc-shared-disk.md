---
title: Telepítse az SAP NetWeaver magas rendelkezésre ÁLLÁS egy Windows feladatátvevő fürt és a egy SAP ASCS/SCS példányhoz az Azure-ban megosztott lemez |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti az SAP NetWeaver magas rendelkezésre ÁLLÁS egy Windows feladatátvevő fürt és a egy SAP ASCS/SCS példányhoz megosztott lemez.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1229b7f9e2a430a663a3e78bb457c03cf4a4a590
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480583"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>Telepítse az SAP NetWeaver magas rendelkezésre ÁLLÁS egy Windows feladatátvevő fürt és megosztott lemez egy SAP ASCS/SCS példányhoz az Azure-ban

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

Ez a cikk bemutatja, hogyan telepítése és konfigurálása egy magas rendelkezésre állású SAP-rendszerhez az Azure-ban a fürtszolgáltatás egy SAP ASCS/SCS példányhoz egy Windows Server feladatátvevő fürt és a fürt megosztott lemez használatával.

## <a name="prerequisites"></a>Előfeltételek

A telepítés megkezdése előtt tekintse át ezeket a dokumentumokat:

* [Architektúra-Útmutató: Egy Windows rendszerű feladatátvevő fürtre a fürt megosztott lemezt használ a fürt egy SAP ASCS/SCS példányhoz][sap-high-availability-guide-wsfc-shared-disk]

* [Az Azure-infrastruktúra előkészítése az SAP magas rendelkezésre ÁLLÁS Windows feladatátvevő fürt és megosztott lemez használatával egy SAP ASCS/SCS példányhoz][sap-high-availability-infrastructure-wsfc-shared-disk]

Ez a cikk az adatbázis-kezelő telepítés nem esik szó, mert mátrixok változhat az adatbázis-kezelő rendszert használja. Feltételezzük, hogy az adatbázis-kezelő magas rendelkezésre állású kérdéseket biztosított legyen az a Funkciók, amelyek támogatják a különböző DBMS-szállítók az Azure-hoz. Példák a következők: AlwaysOn vagy adatbázis-tükrözés az SQL Server és Oracle Data Guard az Oracle-adatbázisokat. A forgatókönyvben, ebben a cikkben használjuk hogy ne adjon sokkal nagyobb védelmet az adatbázis-kezelő a.

Nincsenek speciális megfontolásokat különböző DBMS-szolgáltatásokkal interakcióba egy fürtözött SAP ASCS vagy SCS-konfiguráció az Azure-ban.

> [!NOTE]
> A telepítési eljárások az SAP NetWeaver ABAP rendszerek, a Java-rendszerek és a ABAP + Java rendszerek majdnem azonosak. A legfontosabb különbség, hogy rendelkezik-e egy SAP ABAP-rendszer egyik ASCS-példány. Az SAP-Java-rendszer egyik SCS példányhoz rendelkezik. Az SAP ABAP + Java rendszer rendelkezik egy ASCS-példány és a egy, a feladatátvételi fürt ugyanazt a Microsoft csoportban futó SCS példányhoz. SAP NetWeaver-telepítés rengetegféle telepítési eltérések explicit módon szerepelnek. Akkor feltételezheti, hogy minden más részek azonosak.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Telepítse az SAP egy magas rendelkezésre állású ASCS/SCS példányhoz

> [!IMPORTANT]
> Győződjön meg arról, hogy ne helyezze el a lapozófájl az SIOS DataKeeper tükrözött kötetek. DataKeeper nem támogatja a tükrözött kötetek. Az Azure virtuális gép ideiglenes D meghajtó a lapozófájl az alapértelmezett hagyhatja. Ha még nem szerepel ott, a Windows lapon fájl áthelyezéséhez az Azure virtuális gép D meghajtó.
>
>

Ezeket a feladatokat egy magas rendelkezésre állású ASCS/SCS-példánnyal rendelkező SAP telepítése foglalja magában:

* Hozzon létre egy virtuális nevet a fürtözött SAP ASCS/SCS példányhoz.
* Telepítse az SAP első fürtcsomópontra.
* Módosításához az SAP ASCS/SCS-példány.
* Adja hozzá a mintavételi portot.
* Nyissa meg a Windows tűzfal mintavételi portot.

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Hozzon létre egy virtuális nevet a fürtözött SAP ASCS/SCS példányhoz

1. Hozzon létre egy DNS-bejegyzést a ASCS/SCS-példány virtuális állomás nevét a Windows DNS-kezelőben.

   > [!IMPORTANT]
   > Az ASCS/SCS-példány virtuális állomás nevét a hozzárendelt IP-cím ugyanaz, mint az Azure Load Balancer rendelt IP-cím kell lennie (\<SID\>- lb-ascs).  
   >
   >

   Az SAP ASCS/SCS virtuális állomás nevét (sap-pr1 – ascs) IP-címe megegyezik a Azure Load Balancer (pr1-lb-ascs) IP-címét.

   ![1. ábra: A DNS-bejegyzést az SAP ASCS/SCS-fürt virtuális nevét és a TCP/IP-cím megadása][sap-ha-guide-figure-3046]

   _**1. ábra:** A DNS-bejegyzést az SAP ASCS/SCS-fürt virtuális nevét és a TCP/IP-cím megadása_

2. A virtuális gazdagép neve rendelt IP-cím meghatározásához válassza **DNS-kezelő** > **tartomány**.

   ![2. ábra: Új virtuális nevét és a TCP/IP-cím az SAP ASCS/SCS-fürtkonfiguráció][sap-ha-guide-figure-3047]

   _**2. ábra:** Új virtuális nevét és a TCP/IP-cím az SAP ASCS/SCS-fürtkonfiguráció_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Telepítse az SAP első fürtcsomópontra

1. Hajtsa végre az első fürt csomópont lehetőség fürtcsomóponton rögzíti. Például az pr1-ascs-0 * gazdagép.
2. Tartsa meg az alapértelmezett portok az Azure belső load balancer-hez, válassza ki:

   * **ABAP rendszer**: **ASCS** példányok száma **00**
   * **Java-rendszer**: **SCS** példányok száma **01**
   * **ABAP + Java rendszer**: **ASCS** példányok száma **00** és **SCS** példányok száma **01**

   Az ABAP ASCS-példány és a Java SCS példányhoz 01 példány számok eltérő 00 használatához először módosítsa az Azure belső load balancer alapértelmezett terheléselosztási szabályok. További információkért lásd: [módosítsa a ASCS/SCS alapértelmezett terheléselosztási szabályok az Azure belső terheléselosztó][sap-ha-guide-8.9].

A standard szintű SAP telepítési dokumentációjának a következő néhány feladatot nem ismerteti.

> [!NOTE]
> Az SAP telepítési dokumentáció ismerteti, hogyan lehet ASCS/SCS fürt első csomópontjára telepítse.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Az SAP ASCS/SCS-példány módosításához

Először adja hozzá az új profil paramétert. A profil paraméter megakadályozza, hogy az SAP-munkafolyamatok és a sorba kiszolgáló közötti kapcsolat bezárása, amelyek hosszabb ideig inaktív. Hogy megemlíteni, hogy a probléma esetén [beállításjegyzék-bejegyzések hozzáadásához az SAP ASCS/SCS-példányának mindkét fürtcsomóponton][sap-ha-guide-8.11]. Az adott szakaszt is bevezettünk két változást, néhány alapszintű TCP/IP-kapcsolat paramétereit. A második lépésben kell állítania a sorba kiszolgáló küld egy `keep_alive` jelezze, hogy a kapcsolatok nem nyomja le az Azure belső load balancer üresjárati küszöbértéket.

A módosításához az SAP ASCS/SCS-példány:

1. Ez a profil paraméter hozzáadása az SAP ASCS/SCS példányhoz profil:

   ```
   enque/encni/set_so_keepalive = true
   ```
   Ebben a példában az elérési út:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Például, hogy az SAP SCS példányhoz profil és a megfelelő elérési út:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. A módosítások életbe léptetéséhez indítsa újra az SAP ASCS/SCS példányhoz.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Adja hozzá a mintavételi port

A belső load balancer mintavételi funkciót használja, hogy az Azure Load Balancerrel együttműködve a teljes fürtkonfiguráció. Az Azure belső terheléselosztó általában a bejövő munkaterhelés részt vevő virtuális gépek között egyenlően osztja el.

 Azonban ez nem fog működni az egyes fürtkonfigurációk mert csak egy példány aktív. A másik példány passzív, és a munkaterhelés nem tud fogadni. Egy mintavételi funkció segítségével, amikor az Azure belső terheléselosztó munkahelyi csak egy aktív példány számára. A mintavétel funkciókkal a belső terheléselosztó képes észlelni, mely példányok aktív, és csak a számítási feladatok példány majd célként.

A mintavételi port hozzáadása:

1. Ellenőrizze a jelenlegi **ProbePort** érték a következő PowerShell-parancs futtatásával:

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

   Hajtsa végre a parancsot a virtuális gépek egyikében a fürt konfigurációját.

2. A mintavételi port megadása. A mintavétel portszám: 0. A példánkban a mintavételi portot 62000 használjuk.

   ![3. ábra: A fürt konfigurációt a mintavételi portot kötelező alapértelmezés szerint 0][sap-ha-guide-figure-3048]

   _**3. ábra:** Az alapértelmezett fürt konfigurációt a mintavételi portot kötelező 0_

   A port számát az SAP az Azure Resource Manager-sablonokkal van meghatározva. A port számát, a PowerShell rendelhet hozzá.

   Egy új ProbePort érték beállítása az SAP- \<SID\> IP fürterőforrás, futtassa a következő PowerShell parancsfájlt a PowerShell változók a környezet frissítése:

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

   Miután tenné az SAP \<SID\> a fürt hálózatra, ellenőrizze, hogy **ProbePort** az új értékre van állítva.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```
   A szkript futtatása után a rendszer kéri, indítsa újra a SAP fürtcsoport a változások életbe lépjenek.

   ![4. ábra: A fürt port mintavételi, miután beállította az új érték][sap-ha-guide-figure-3049]

   _**4. ábra:** A fürt port mintavételi, miután beállította az új érték_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Nyissa meg a Windows tűzfal mintavételi port

Nyissa meg a Windows tűzfal a mintavételi port mindkét fürtcsomóponton. Windows tűzfal a mintavételi port megnyitásához használja a következő szkriptet. Frissítés a PowerShell változók környezete számára.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

**ProbePort** értékre van állítva **62000**. Most már hozzáférhet a fájlmegosztáshoz \\\ascsha-clsap\sapmnt más gazdagépekről, például ascsha-adatbázisok.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Az adatbázis-példány telepítése

Az adatbázis-példány telepítéséhez hajtsa végre a folyamatot, az SAP telepítési dokumentációjában leírt.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Telepítse a második fürtcsomópontra

A második fürt telepítéséhez kövesse a lépéseket, amelyek az SAP telepítési útmutatóban ismertetett.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Módosíthatja a az SAP on felhasználók Windows-példányok

A SAP SSZON Windows szolgáltatás indítási típusának módosítása **automatikus (Késleltetett indítás)** mindkét fürtcsomóponton.

![5. ábra: A szolgáltatás típusa az SAP SSZON példány módosíthatja a késleltetett automatikus][sap-ha-guide-figure-3050]

_**5. ábra:** A szolgáltatás típusa az SAP SSZON példány módosíthatja a késleltetett automatikus_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Az elsődleges alkalmazás SAP-kiszolgáló telepítése

Telepítse az elsődleges alkalmazás Server ATTRIBÚTUMKÉSZLETHEZ példányt \<SID\>-di-0 ÜGYFÉLCÍMEK üzemeltetésére kijelölt már a virtuális gépen. Nincsenek függőségek az Azure-ban. Nem tartoznak DataKeeper-specifikus beállítások.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> További SAP alkalmazáskiszolgáló telepítése

Telepítse az SAP további Application Server (AAS), amely már egy SAP-alkalmazáskiszolgáló-példány futtatására kijelölt összes virtuális gépet. Például az \<SID\>-di-1-re \<SID\>- di -&lt;n&gt;.

> [!NOTE]
> Ez befejezi a magas rendelkezésre állású SAP NetWeaver-rendszer telepítését. Ezután folytassa feladatátvétel tesztelése.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Az SAP ASCS/SCS-példány feladatátvételt és SIOS replikáció tesztelése
Akkor is könnyen teszteléséhez és a egy SAP ASCS/SCS-példány feladatátvételt és SIOS lemez replikációs figyelemmel követhető a Feladatátvevőfürt-kezelő és az SIOS DataKeeper felügyeleti és a konfigurációs eszközt.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Az SAP ASCS/SCS példányhoz egy fürtcsomóponton fut.

Az SAP PR1 fürtcsoport fut, a fürtcsomópont-t. Ha például a pr1-ascs-0. Rendelje hozzá a megosztott lemezmeghajtó S, amely az SAP PR1 fürt csoport része, fürtcsomópontra rögzíti. Az ASCS/SCS példányhoz használja is a lemezmeghajtó %s 

![6. ábra: A Feladatátvevőfürt-kezelő: Az SAP \<SID\> fürtcsoport fut egy csomóponton][sap-ha-guide-figure-5000]

_**6. ábra:** A Feladatátvevőfürt-kezelő: Az SAP \<SID\> fürtcsoport fut egy csomóponton_

Az SIOS DataKeeper felügyeleti és a konfigurációs eszközt láthatja, hogy a megosztott adatok a rendszer szinkron módon replikálja a fürtcsomópontra A forrás-kötet meghajtó S a céloldali kötet meghajtóról S fürtcsomópont b Ha például azt a rendszer replikálja a pr1-ascs-0 [10.0.0.40] pr1 – ascs-1 [10.0.0.41].

![7. ábra: Az SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról egy fürtcsomóponton B][sap-ha-guide-figure-5001]

_**7. ábra:** Az SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról egy fürtcsomóponton B_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Feladatátvételt egy csomópontról csomópontra B

1. Válasszon egyet az alábbi lehetőségek közül, az SAP feladatátvétel \<SID\> fürtcsoport fürtcsomópontról A b fürtcsomópontra
   - A Feladatátvevőfürt-kezelő  
   - A feladatátvevő fürt PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Indítsa újra a fürt csomópont A Windows vendég operációs rendszerből. Az SAP az automatikus feladatátvételt kezdeményez \<SID\> fürtcsoport egy csomópontról csomópontra B.  
3. Indítsa újra a fürt csomópont A az Azure Portalról. Az SAP az automatikus feladatátvételt kezdeményez \<SID\> fürtcsoport egy csomópontról csomópontra B.  
4. Indítsa újra a csomópont A fürthöz az Azure PowerShell használatával. Az SAP az automatikus feladatátvételt kezdeményez \<SID\> fürtcsoport egy csomópontról csomópontra B.

   Az SAP a feladatátvételt követően \<SID\> fürtcsoport fut, a fürtcsomópont B. Ha például jelenleg is fut a pr1-ascs-1.

   ![8. ábra: A Feladatátvevőfürt-kezelő, a SAP \<SID\> fürtcsoport B csomóponton fut.][sap-ha-guide-figure-5002]

   _**8. ábra**: A Feladatátvevőfürt-kezelő, a SAP \<SID\> fürtcsoport B csomóponton fut._

   A megosztott lemez már csatlakoztatva van a fürt csomópont b az SIOS DataKeeper van adatok meghajtóról forrás kötet S B fürtcsomóponton való replikálásához célmeghajtó kötet, S fürtcsomóponton A. Ha például replikálását végzi a pr1-ascs-1 [10.0.0.41] pr1 – ascs-0 [10.0.0.40].

   ![9. ábra: Az SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról B, a csomópont A fürthöz][sap-ha-guide-figure-5003]

   _**9. ábra:** Az SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról B, a csomópont A fürthöz_
