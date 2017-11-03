---
title: "SAP NetWeaver magas rendelkezésre ÁLLÁSÚ telepítés Windows feladatátvevő fürtön, és a megosztott lemez SAP (A) SCS példány az Azure-on |} Microsoft Docs"
description: "SAP NetWeaver magas rendelkezésre ÁLLÁSÚ telepítés a Windows feladatátvételi fürtszolgáltatás és megosztott lemez SCS példány SAP (A)"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 938ab6be6b2ba9e1403919cb62f68c65f114c067
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="sap-netweaver-ha-installation-on-windows-failover-cluster-and-shared-disk-for-sap-ascs-instance-on-azure"></a>SAP NetWeaver magas rendelkezésre ÁLLÁSÚ telepítés a Windows feladatátvételi fürtszolgáltatás és megosztott lemez SAP (A) SCS példány az Azure-on

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

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
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

Ez a dokumentum van leíró telepítése és konfigurálása az Azure, magas rendelkezésre álló SAP rendszer **Windows feladatátvételi fürt (WSFC)** és **megosztott fürtlemez** SAP (A) SCS fürtözéshez példány.

## <a name="prerequisites"></a>Előfeltételek

Mindenképpen tekintse át ezeket a dokumentumokat a telepítés megkezdése előtt:

* [A megosztott lemez architektúra-útmutató - SAP (A) SCS példány fürtszolgáltatás a Windows feladatátvevő fürt fürt használatával][sap-high-availability-guide-wsfc-shared-disk]

* [Az SAP magas rendelkezésre ÁLLÁSÚ Windows feladatátvételi fürtszolgáltatás és megosztott lemez SAP (A) SCS-példány használata az Azure infrastruktúra előkészítése][sap-high-availability-infrastructure-wsfc-shared-disk]

Azt írja le az adatbázis-kezelő telepítő mert beállítások eltérők lehetnek, attól függően, hogy az adatbázis-kezelő rendszert használ. Azonban feltételezzük, hogy az adatbázis-kezelő magas rendelkezésre állású kérdéseket a különböző DBMS forgalmazójával támogatja az Azure-funkciókkal rendelkező tárgyalja. Például mindig bekapcsolva vagy adatbázis-tükrözést az SQL Server és Oracle Data Guard az Oracle-adatbázisok. A forgatókönyvben a cikkben használjuk, azt nem további védelem bekapcsolása a az adatbázis-kezelő.

Nincsenek semmilyen külön odafigyelést különböző adatbázis-kezelő szolgáltatásokat fürtözött SAP ASC/SCS konfigurálása az Azure-ban az ilyen kommunikál.

> [!NOTE]
> A telepítési eljárásokat az SAP NetWeaver ABAP rendszerek, Java, és a ABAP + Java rendszerek csaknem azonosak. A legfontosabb különbség, hogy rendelkezik-e az SAP ABAP rendszer ASC egy példánya. Az SAP Java rendszer egy SCS példány van. Az SAP ABAP + Java rendszer rendelkezik egy ASC példány és egy SCS példány fut a Microsoft feladatátvevő fürt csoporton belül. Összes telepítési különbséget minden SAP NetWeaver telepítési verem explicit módon szerepelnek. Feltételezzük, hogy minden egyéb részeinek megegyeznek.  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Telepítse a SAP, ha a magas rendelkezésre állású ASC/SCS példánya

> [!IMPORTANT]
> Győződjön meg arról, hogy nem helyezhető el a lapozófájl DataKeeper tükrözött köteteken. DataKeeper nem támogatja a tükrözött kötetek. A lapozófájl az ideiglenes meghajtón D egy Azure virtuális gép, az alapértelmezett hagyhatja. Ha még nincs hiba, a Windows lapozófájlja áthelyezése az Azure virtuális gép D meghajtó.
>
>

Ezeket a feladatokat, ha a magas rendelkezésre állású ASC/SCS példánya SAP telepítése foglal magában:

* Egy virtuális nevet az SAP ASC/SCS fürtözött példány létrehozása
* Az SAP első fürtcsomópontra telepítése
* Az SAP-profilnak ASC/SCS-példány módosítása
* A mintavétel a port hozzáadása
* A Windows tűzfal mintavételi port megnyitása

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Hozzon létre egy virtuális nevet a fürtözött SAP ASC/SCS-példány

1.  A Windows DNS-kezelőben hozzon létre egy DNS-bejegyzést a ASC/SCS példányának virtuális állomás neve.

  > [!IMPORTANT]
  > Az IP-cím, amikor hozzárendeli a ASC/SCS példányának virtuális állomás neve lehet ugyanaz, mint az Azure Load Balancer rendelt IP-cím (**<*SID*> - lb - ASC **).  
  >
  >

  Az SAP ASC/SCS állomásnév virtuális IP-címe (**pr1-ASC-sap**) ugyanaz, mint az IP-cím az Azure Load Balancer (**pr1-lb-ASC**).

  ![1. ábra: A DNS-bejegyzést a SAP ASC/SCS fürt virtuális nevét és a TCP/IP-cím megadása][sap-ha-guide-figure-3046]

  _**1. ábra:** a DNS-bejegyzést a SAP ASC/SCS fürt virtuális nevét és a TCP/IP-cím megadása_

2.  A virtuális állomásneve rendelt IP-cím megadásához válassza ki a **DNS-kezelő** > **tartomány**.

  ![2. ábra: Új virtuális nevét és TCP/IP-cím SAP ASC/SCS fürtnek megfelelő konfiguráció][sap-ha-guide-figure-3047]

  _**2. ábra:** új virtuális nevét és a TCP/IP-címtartományok SAP ASC/SCS fürtnek megfelelő konfiguráció_

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>Az SAP első fürtcsomópontra telepítése

1.  Az első fürt csomópont lehetőség fürtcsomóponton A. végrehajtása Ha például a **pr1-ASC-0** állomás.
2.  Az alapértelmezett portok az Azure belső terheléselosztóhoz, jelölje be:

  * **ABAP rendszer**: **ASC** szám példány **00**
  * **Java-rendszer**: **SCS** szám példány **01**
  * **ABAP + Java rendszer**: **ASC** szám példány **00** és **SCS** szám példány **01**

  A ABAP ASC példányhoz, és 01 használandó példány 00 eltérő számú portok a Java SCS példány, először módosítania kell az Azure belső alapértelmezett terheléselosztási szabályok, leírt [ASC/SCS alapértelmezett terheléselosztási szabályok az Azure-beli módosítása belső terheléselosztó] [sap-magas rendelkezésre állású-útmutató-8.9].

A következő néhány feladatot a szabványos SAP-dokumentáció nem ismerteti.

> [!NOTE]
> Az SAP-dokumentáció ASC/SCS fürt első csomópontjára telepítését ismerteti.
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>Az SAP-profil ASC/SCS-példány módosítása

Akkor hozzon létre egy új profil paraméter. A profil paraméter megakadályozza, hogy az SAP-munkafolyamatok és a sorba helyezni kiszolgáló közötti kapcsolat bezárása, ha túl sokáig üresjáratban. Jelenleg a probléma esetén [beállításjegyzékbeli bejegyzések hozzáadása az SAP ASC/SCS példány mindkét fürtcsomóponton] említett [sap-magas rendelkezésre állású-útmutató-8.11]. A szakaszt azt is vezette be két módosítások néhány alapvető TCP/IP-kapcsolat paraméterekhez. Egy második lépésben be kell állítani a sorba helyezni kiszolgáló küld egy `keep_alive` jelezze, hogy a kapcsolatok nem elérte az Azure belső elosztott terhelésű üresjárati küszöbértéket.

Az SAP-profil ASC/SCS-példány módosítása:

1.  Ez a profil paraméter hozzáadása az SAP ASC/SCS példány profilhoz:

  ```
  enque/encni/set_so_keepalive = true
  ```
  A fenti példában az elérési út:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  Például, hogy a SAP SCS példány profil és a megfelelő elérési út:

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  A módosítások alkalmazásához, indítsa újra az SAP ASC /SCS példányt.

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>Adjon hozzá egy mintavételi portot

A belső terheléselosztó mintavételi funkció segítségével a teljes fürt konfigurációs Azure terheléselosztó dolgozni. Az Azure belső terheléselosztó általában a bejövő terhelés részt vevő virtuális gépek között egyenlően osztja el. Azonban ez nem fog működni az egyes fürtkonfigurációk mert csak egy példány aktív. A többi példány passzív, és a munkaterhelés nem tudja elfogadni. A mintavételi funkció segít, amikor az Azure belső terheléselosztó munkahelyi csak egy aktív példány számára. A mintavétel-szolgáltatásával a belső terheléselosztó előfordulások aktív, és ezután célpéldányának csak a munkaterheléssel képes észlelni.

A mintavételi portot hozzáadása:

1.  Ellenőrizze az aktuális **ProbePort** beállítása a következő PowerShell-parancs futtatásával:

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

   A fürtkonfiguráció le a virtuális gépek egyik végrehajtást.

2.  A mintavételi portot határozza meg. Az alapértelmezett mintavételi portszám **0**. A jelen példában használjuk a mintavételi portot **62000**.

  ![3. ábra: A fürt konfigurációs mintavételi portot pedig 0 alapértelmezés szerint][sap-ha-guide-figure-3048]

  _**3. ábra:** fürt konfigurációs mintavételi törölve: 0_

  A portszám SAP Azure Resource Manager-sablonok van meghatározva. A port számát a PowerShellben rendelhet hozzá.

  Egy új ProbePort értéket az a  **SAP <*SID*> IP ** fürterőforrás, futtassa a következő PowerShell parancsfájlt a PowerShell változók a környezet frissítése:

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

  Miután kapcsolása a  **SAP <*SID*> ** fürtön a szerepkör hálózatra, ellenőrizze, hogy **ProbePort** az új értékre van beállítva.

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```
  A parancsfájl futtatása után a rendszer kérni fogja újraindítja a SAP fürtcsoport a változások életbe lépjenek.

  ![4. ábra: A fürt port mintavételi, az új érték beállítása után][sap-ha-guide-figure-3049]

  _**4. ábra:** az új érték beállítása után, mintavételi modulja a fürt port_

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>Nyissa meg a Windows tűzfal mintavételi portot

Nyissa meg a Windows tűzfal mintavételi portot mindkét fürtcsomóponton kell. A következő parancsfájl segítségével nyissa meg a Windows tűzfal mintavételi portot. A környezet PowerShell változói frissítése.

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

A **ProbePort** értéke **62000**. Most már hozzáférhet a fájlmegosztáshoz  **\\\ascsha-clsap\sapmnt** a többi, például mert a **ascsha-dbas**.

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>Az adatbázis-példány telepítése

Az adatbázispéldány fölött telepítéséhez kövesse a SAP dokumentáció ismertetett folyamatot.

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>A második fürt csomópont telepítése

A második fürt telepítéséhez kövesse az SAP telepítési útmutatóban.

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>Módosítsa a SAP SSZON Windows szolgáltatáspéldány indítási típusa

A SAP SSZON Windows szolgáltatás indítási típusának módosítása **automatikus (Késleltetett indítás)** mindkét fürtcsomóponton.

![5. ábra: Az SAP SSZON példány szolgáltatás típusának módosítása késleltetett automatikusra][sap-ha-guide-figure-3050]

_**5. ábra:** az SAP SSZON példányt késleltetett automatikus módosíthatja a szolgáltatás típusa_

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>Az SAP elsődleges alkalmazáskiszolgáló telepítése

Az elsődleges alkalmazás kiszolgáló (szolgáltatói)-példány telepítése <*SID*> - di - 0-fiókjához kijelölt üzemeltetéséhez a szolgáltatói CÍMEK a virtuális gépen. Nincsenek függőségek a Azure vagy DataKeeper-specifikus beállításokat.

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>Az SAP további alkalmazáskiszolgáló telepítése

Telepítse az SAP további Application Server (AAS) üzemeltetésére SAP Application Server-példány már a kijelölt virtuális gépek. Például a <*SID*> - di - 1 a <*SID*> - di -&lt;n&gt;.

> [!NOTE]
> Ez befejezi a magas rendelkezésre állású SAP NetWeaver rendszer telepítését. A következő folytatásához feladatátvételi tesztelése.
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>Az SAP ASC/SCS-példány feladatátvevő és SIOS replikációs teszteléséhez
Akkor is könnyen a tesztelése egy SAP ASC/SCS-példány feladatátvevő és SIOS lemez replikációs Feladatátvevőfürt-kezelő és a SIOS DataKeeper kezelési és konfigurációs eszköz használatával.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>SAP ASC/SCS-példány fut A fürtcsomópont

A **SAP PR1** fürtcsoport fürtcsomóponton A. fut. Például a **pr1-ASC-0**. Rendelje hozzá a megosztott lemezmeghajtó S, amely része a a **SAP PR1** fürtcsoportot, és az ASC/SCS-példány használja, a fürt csomópont azonosítójához.

![6. ábra: Feladatátvevőfürt-kezelő: < SID > a SAP fürtcsoport A csomóponton fut.][sap-ha-guide-figure-5000]

_**6. ábra:** Feladatátvevőfürt-kezelő: az SAP <*SID*> fürtcsoport A csomóponton fut._

A SIOS DataKeeper felügyelete és konfigurálása eszköz megtekintheti, hogy a megosztott lemez adatainak rendszer szinkron módon replikálja a csomóponton A forrás-kötet meghajtó S a kötet célmeghajtó S fürtcsomópontra a b kiszolgálóra. Például, hogy a rendszer replikálja a **pr1-ASC-0 [10.0.0.40]** való **pr1-ASC-1 [10.0.0.41]**.

![7. ábra: SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról A B-fürt csomópontjának][sap-ha-guide-figure-5001]

_**7. ábra:** SIOS DataKeeper replikálja a helyi kötet fürtcsomópontról A B-fürt csomópontjának_

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>B csomópont-csomópont A feladatátvétel

1.  Válasszon egyet ezek közül a SAP a feladatátvétel kezdeményezése <*SID*> fürtcsoport fürtcsomópontról A b-fürt csomópontjának
  - Feladatátvevőfürt-kezelővel  
  - Feladatátvevő fürt PowerShell használata

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  Indítsa újra a fürt csomópont A a Windows vendég operációs rendszerben (a SAP, az automatikus feladatátvételt kezdeményez <*SID*> fürtcsoport csomópontból A csomópont B).  
3.  Indítsa újra a fürt csomópont A Azure-portálról (egy, a SAP automatikus feladatátvételt kezdeményez <*SID*> fürtcsoport csomópontból A csomópont B).  
4.  Indítsa újra a fürt csomópont A Azure PowerShell használatával (ez elindít egy automatikus feladatátvétel, a SAP <*SID*> fürtcsoport csomópontból A csomópont B).

  Feladatátvétel után a SAP <*SID*> fürtcsoport fürtcsomóponton b fut. Futó például **pr1-ASC-1**.

  ![8. ábra: A Feladatátvevőfürt-kezelő, a SAP < SID > fürtcsoport van fürtcsomóponton futó B][sap-ha-guide-figure-5002]

  _**8. ábra**: A Feladatátvevőfürt-kezelő, a SAP <*SID*> fürtcsoport fürtcsomóponton B fut._

  A megosztott lemez már csatlakoztatva van a fürt csomópont b SIOS DataKeeper replikálódik adatok forrás kötet meghajtóról S fürtcsomóponton B célmeghajtó kötet, S fürtcsomóponton A. A replikáló például **pr1-ASC-1 [10.0.0.41]** való **pr1-ASC-0 [10.0.0.40]**.

  ![9. ábra: SIOS DataKeeper replikálja a helyi kötet fürtcsomópontból B csomópont A fürt][sap-ha-guide-figure-5003]

  _**9. ábra:** SIOS DataKeeper replikálja a helyi kötet fürtcsomópontból B csomópont A fürt_
