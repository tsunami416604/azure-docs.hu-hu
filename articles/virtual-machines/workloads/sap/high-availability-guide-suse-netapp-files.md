---
title: Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server az Azure Files-NetApp |} A Microsoft Docs
description: Magas rendelkezésre állású útmutató az SAP NetWeaver SUSE Linux Enterprise Server, SAP-alkalmazások Azure NetApp fájljait a futó
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/015/2019
ms.author: radeltch
ms.openlocfilehash: 02a97852a8dc659071c3484126b921d6f7106562
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662370"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server az Azure NetApp Files SAP alkalmazások az Azure virtuális gépeken

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/en-us/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Ez a cikk ismerteti a virtuális gépek üzembe helyezése, konfigurálja a virtuális gépek, telepítse a fürt keretrendszert és egy magas rendelkezésre állású SAP NetWeaver 7.50 rendszert, használatával [Azure NetApp fájlokat (a nyilvános előzetes verzió)](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-introduction/).
A példa konfiguráció, telepítési parancsokat stb., a ASCS példány szám 02 00, a SSZON példányszámának 01, az elsődleges alkalmazáspéldány (szolgáltatói CÍMEI) pedig az alkalmazáspéldány (AAS) 03. Az SAP-rendszer azonosító QAS szolgál. 

Ez a cikk azt ismerteti, hogyan érhető el magas rendelkezésre állás az SAP NetWeaver alkalmazást az Azure Files-NetApp. Az adatbázis réteg nem ez a cikk részletesen ismertetett.

Először olvassa el az alábbi SAP-megjegyzések és tanulmányok:

* [Az Azure NetApp Files dokumentációja][anf-azure-doc] 
* SAP-Jegyzetnek [1928533], amely rendelkezik:  
  * SAP szoftver központi telepítése által támogatott Azure-beli Virtuálisgép-méretek
  * Azure-beli Virtuálisgép-méretek esetében fontos kapacitásadatok
  * Támogatott SAP-szoftverek és operációs rendszer (OS) és adatbázis-kombinációk
  * Szükséges SAP kernel verziója a Windows és Linux a Microsoft Azure
* SAP-Jegyzetnek [2015553] az Azure-beli SAP az SAP által támogatott szoftverek központi telepítéséhez szükséges előfeltételeket ismerteti.
* SAP-Jegyzetnek [2205917] ajánlott az operációs rendszer beállításait a SUSE Linux Enterprise Server SAP-alkalmazások
* SAP-Jegyzetnek [1944799] SUSE Linux Enterprise Server SAP HANA-irányelvek rendelkezik az SAP-alkalmazások
* SAP-Jegyzetnek [2178632] részletes jelentett az Azure-beli SAP-figyelési metrikákat kapcsolatos információkat tartalmaz.
* SAP-Jegyzetnek [2191498] rendelkezik a szükséges SAP gazdagép-ügynök verziója Linux az Azure-ban.
* SAP-Jegyzetnek [2243692] SAP linuxon az Azure-beli licenceléssel kapcsolatos információkat tartalmaz.
* SAP-Jegyzetnek [1984787] SUSE Linux Enterprise Server 12 vonatkozó általános információkat tartalmaz.
* SAP-Jegyzetnek [1999351] további információkat talál az Azure Enhanced Monitoring bővítményt az SAP rendelkezik.
* [Az SAP közösségi WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik az összes szükséges SAP-megjegyzések Linux rendszeren.
* [Az Azure virtuális gépek tervezése és megvalósítása az SAP, Linux rendszeren][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése, az SAP, Linux rendszeren][deployment-guide]
* [Az Azure Virtual Machines DBMS üzembe helyezése, az SAP, Linux rendszeren][dbms-guide]
* [SUSE SAP magas rendelkezésre ÁLLÁS ajánlott eljárások gyűjteménye] [ suse-ha-guide] a útmutatókat tartalmaz minden szükséges információt a beállítása Netweaver magas rendelkezésre ÁLLÁS és a helyszíni SAP HANA-Rendszerreplikálást. Ezek az útmutatók használja általános kiindulópontként. Sokkal részletesebb információkat biztosítanak.
* [SUSE magas rendelkezésre állású bővítmény 12 SP3 kibocsátási megjegyzései][suse-ha-12sp3-relnotes]
* [NetApp SAP alkalmazások az Azure Files-NetApp használatával a Microsoft Azure][anf-sap-applications-azure]

## <a name="overview"></a>Áttekintés

Magas availability(HA) SAP Netweaver központi szolgáltatásokhoz megosztott tárolóra van szükség.
Ennek érdekében javítottunk a SUSE Linux, amennyiben szükséges volt külön magas rendelkezésre állású NFS-fürt létrehozásához. 

Most Önön eléréséhez SAP Netweaver magas rendelkezésre ÁLLÁS megosztott tárolót üzembe helyezve az Azure Files-NetApp használatával lehetséges. A megosztott tároló nem kell az Azure NetApp Files használatával további [NFS fürt](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Magas rendelkezésre ÁLLÁS, az SAP Netweaver központi services(ASCS/SCS) támasztja továbbra is szükség van.


![SAP NetWeaver magas rendelkezésre állás – Áttekintés](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS, and the SAP HANA database use virtual hostname and virtual IP addresses. Az Azure-ban egy [terheléselosztó](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview) kell használnia a virtuális IP-címet. Az alábbi lista tartalmazza (A) konfigurációjának SCS és SSZON terheléselosztó.

### <a name="ascs"></a>(A)SCS

* Előtér-konfigurációjához
  * IP address 10.1.1.20
* Háttér-konfiguráció
  * (A) részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik SCS/SSZON fürt
* Mintavételi Port
  * Port 620<strong>&lt;nr&gt;</strong>
* Terheléselosztási szabályok
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Előtér-konfigurációjához
  * IP address 10.1.1.21
* Háttér-konfiguráció
  * (A) részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik SCS/SSZON fürt
* Mintavételi Port
  * Port 621<strong>&lt;nr&gt;</strong>
* Terheléselosztási szabályok
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Az Azure Files-NetApp infrastruktúra beállítása 

SAP NetWeaver az átvitel és a profil Directory megosztott tároló szükséges hozzá.  A Azure NetApp files-infrastruktúra telepítése előtt, ismerje meg az a [Azure NetApp fájlok dokumentáció][anf-azure-doc]. Ellenőrizze, hogy ha a kiválasztott Azure-régiót kínál Azure NetApp fájlokat. Az alábbi hivatkozásra a NetApp Azure fájlok az Azure-régióban rendelkezésre állását jeleníti meg: [Azure NetApp rendelkezésre állási fájlok az Azure-régió szerint][anf-avail-matrix].

Az Azure NetApp fájlok szolgáltatás jelenleg nyilvános előzetes verzióban több Azure-régióban. Azure NetApp fájlok üzembe helyezése előtt regisztráljon az Azure Files-NetApp előzetes verziójára, a következő a [regisztrálni Azure NetApp fájlok útmutatás][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>NetApp fájlokat az Azure-erőforrások üzembe helyezése  

A lépések azt feltételezik, hogy korábban már telepítettek [Azure Virtual Network](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview). Ne feledje, hogy a NetApp fájlokat az Azure-erőforrások és a virtuális gépek, ahol a NetApp fájlokat az Azure-erőforrások lesz csatlakoztatva kell telepíteni az azonos Azure virtuális hálózatban.  

1. A kérelem nem tette meg már, ha [regisztrálása a Azure NetApp Preview](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register).  

2. A NetApp fiók létrehozása a kiválasztott Azure-régióban, a következő a [NetApp fiók létrehozására vonatkozó utasításokat](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Állítsa be a következő Azure NetApp fájlok kapacitás-készletben a [való beállítása az Azure Files-NetApp kapacitás készlet](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Az SAP Netweaver-architektúra ebben a cikkben bemutatott egyetlen Azure NetApp fájlok kapacitás készletet, prémium szintű Termékváltozatot használja. Azure NetApp fájlok prémium Termékváltozatot javasoljuk az SAP Netweaver alkalmazás számítási feladatait az Azure-ban.  

4. Azure NetApp files alhálózat delegálása leírtak szerint a [utasításokat delegálása az Azure Files-NetApp alhálózat](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Az Azure Files-NetApp köteteket, a következő üzembe helyezheti a [kötet létrehozása a NetApp Azure-fájlok útmutatás](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-volumes). A köteteket a kijelölt NetApp Azure Files szolgáltatásban üzembe helyezheti [alhálózati](https://docs.microsoft.com/en-us/rest/api/virtualnetwork/subnets). Ne feledje, hogy a NetApp fájlokat az Azure-erőforrások és az Azure virtuális gépek azonos Azure virtuális hálózatban kell lennie. Például sapmnt<b>QAS</b>, usrsap<b>QAS</b>használatához és így tovább a kötetnevek és sapmnt<b>qas</b>, usrsap<b>qas</b>használatához és így tovább a filepaths vannak az Azure-beli használatra NetApp fájlok kötetek.  

   1. kötet sapmnt<b>QAS</b> (nfs://10.1.0.4/sapmnt<b>qas</b>)
   2. kötet usrsap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>)
   3. kötet usrsap<b>QAS</b>sys (nfs://10.1.0.5/usrsap<b>qas</b>sys)
   4. kötet usrsap<b>QAS</b>sszon (nfs://10.1.0.4/usrsap<b>qas</b>sszon)
   5. kötet trans (nfs://10.1.0.4/trans)
   6. kötet usrsap<b>QAS</b>szolgáltatói címei (nfs://10.1.0.5/usrsap<b>qas</b>szolgáltatói címei)
   7. kötet usrsap<b>QAS</b>rendszergazdájaként (nfs://10.1.0.4/usrsap<b>qas</b>aas)
   
Ebben a példában a NetApp fájlokat az Azure minden SAP Netweaver fájlrendszerekben bemutatásához, hogyan használható az Azure Files-NetApp használtuk. Az SAP fájlrendszereket, amelyeket nem kell csatlakoztatnia kell az NFS-n keresztül is telepíthető központilag [az Azure disk storage](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#premium-ssd) . Ebben a példában <b>-e</b> kell lennie az Azure Files-NetApp és <b>f-g</b> (azaz usr/sap/<b>QAS</b>/D<b>02</b>,usr/sap/<b>QAS </b>/D<b>03</b>), az Azure disk storage is telepíthető. 

### <a name="important-considerations"></a>Fontos szempontok

Az SAP Netweaver SUSE magas rendelkezésre állású architektúra az Azure NetApp fájlok kiválasztásakor vegye figyelembe az alábbi fontos szempontokat:

- A kapacitásérték-minimumot készlet használata 4 Tib-ra. A kapacitás a készlet méretét 4 Tib-ra többszörösének kell lennie.
- A minimális kötet 100 GiB
- NetApp Azure Files és az összes virtuális gép, amelyen az Azure Files-NetApp kötetek csatlakoztatva van az azonos Azure virtuális hálózatban kell lennie. [Virtuális hálózatok közötti társviszony](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview) Azure NetApp fájlok még nem támogatott.
- A kiválasztott virtuális hálózat egy alhálózat Azure NetApp Files, meghatalmazott kell rendelkeznie.
- NetApp Azure Files jelenleg csak NFSv3 támogatja 
- Biztosít a NetApp Azure Files [házirend exportálása](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): szabályozhatja az engedélyezett ügyfelektől, a hozzáférés típusa (Olvasás és írás, csak olvasható, stb.). 
- Azure NetApp fájlok funkció még nem ismeri a zóna. Azure NetApp fájlok szolgáltatás jelenleg nem telepített minden rendelkezésre állási zónák egy Azure-régióban. Vegye figyelembe a lehetséges késés hatással az egyes Azure-régióban. 

   > [!NOTE]
   > Vegye figyelembe, hogy az Azure Files-NetApp virtuális hálózati társviszony-létesítés még nem támogatja. Telepítse a virtuális gépek és az Azure NetApp fájlok ugyanabban a virtuális hálózatban.

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Manuálisan üzembe helyezése Linux rendszerű virtuális gépek Azure-portálon

Először meg kell az Azure Files-NetApp köteteket hozhat létre. A virtuális gépek üzembe helyezése. Ezt követően hozzon létre egy terheléselosztó, és a virtuális gépek használata a háttérkészletek.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Az ascs rendszerbe fut be egy rendelkezésre állási csoport létrehozása  
   Maximális frissítési tartomány beállítása
1. 1 virtuális gép létrehozása  
   Legalább SLES4SAP 12 SP3, ebben a példában a SLES4SAP 12 SP3 lemezképet használja  
   Válassza ki a rendelkezésre állási csoport ascs rendszerbe fut be a korábban létrehozott  
1. 2 virtuális gép létrehozása  
   Legalább SLES4SAP 12 SP3, ebben a példában a SLES4SAP 12 SP3 lemezképet használja  
   Válassza ki a rendelkezésre állási csoport ascs rendszerbe fut be a korábban létrehozott  
1. Az SAP alkalmazáspéldányok (szolgáltatói CÍMEI, AAS) egy rendelkezésre állási csoport létrehozása    
   Maximális frissítési tartomány beállítása
1. 3 virtuális gép létrehozása  
   Legalább SLES4SAP 12 SP3, ebben a példában a SLES4SAP 12 SP3 lemezképet használja  
   Válassza ki a rendelkezésre állási szolgáltatói CÍMEI vagy Rendszergazdájaként a korábban létrehozott   
1. 4 virtuális gép létrehozása  
   Legalább SLES4SAP 12 SP3, ebben a példában a SLES4SAP 12 SP3 lemezképet használja  
   Válassza ki a rendelkezésre állási szolgáltatói CÍMEI vagy Rendszergazdájaként a korábban létrehozott  

## <a name="setting-up-ascs"></a>(A) SCS beállítása

Ebben a példában a rendszer üzembe helyezett erőforrások manuálisan keresztül a [az Azure portal](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Az Azure Load Balancer telepítheti manuálisan az Azure Portalon keresztül

Először meg kell az Azure Files-NetApp köteteket hozhat létre. A virtuális gépek üzembe helyezése. Ezt követően hozzon létre egy terheléselosztó, és a virtuális gépek használata a háttérkészletek.

1. Load Balancer létrehozása (belső)  
   1. Hozzon létre az előtérbeli IP-címek
      1. Az ASCS 10.1.1.20 IP-címe
         1. Nyissa meg a terheléselosztó, válassza ki az előtérbeli IP-címkészlet, kattintson a Hozzáadás gombra
         1. Adja meg az új előtérbeli IP-címkészlet nevét (például **előtérbeli. QAS. ASCS**)
         1. A hozzárendelés Static értékre, és adja meg az IP-címet (például **10.1.1.20**)
         1. Kattintson az OK gombra
      1. Az ASCS SSZON 10.1.1.21 IP-címe
         * Ismételje meg a fenti lépéseket a SSZON IP-cím létrehozása "a" alatt (például **10.1.1.21** és **előtérbeli. QAS. SSZON**)
   1. Hozzon létre a háttérkészletek
      1. Az ASCS háttérkészlet létrehozása
         1. Nyissa meg a terheléselosztó, válassza ki a háttérkészletek, és kattintson a Hozzáadás gombra
         1. Adja meg az új háttérkészlet nevét (például **háttérrendszer. QAS**)
         1. Kattintson a Hozzáadás elemre egy virtuális gépet.
         1. Válassza ki a korábban létrehozott ascs rendszerbe fut be a rendelkezésre állási csoport 
         1. Válassza ki a virtuális gépek (A) SCS-fürt
         1. Kattintson az OK gombra
   1. Az állapotminták létrehozása
      1. Port 620**00** ASCS számára
         1. Nyissa meg a terheléselosztó, válassza ki az állapotmintákat, kattintson a Hozzáadás gombra
         1. Adja meg az új állapotadat-mintavétel nevét (például **állapotát. QAS. ASCS**)
         1. Válassza ki a TCP protokoll, port 620**00**, időköz 5 és a nem kifogástalan állapot küszöbértéke 2
         1. Kattintson az OK gombra
      1. Port 621**01** ASCS SSZON számára
            * Ismételje meg a fenti lépéseket a "c" hozzon létre egy állapotmintát az on-felhasználók számára (például 621**01** és **állapotát. QAS. SSZON**)
   1. Terheléselosztási szabályok
      1. 32**00** TCP ASCS használata
         1. Nyissa meg a terheléselosztó, terheléselosztási szabályok kiválasztása, és kattintson a Hozzáadás gombra
         1. Adja meg az új terheléselosztó-szabályt nevét (például **lb. QAS. ASCS.3200**)
         1. Válassza ki az előtérbeli IP-cím ascs rendszerbe fut be, a háttérkészlet, valamint a korábban létrehozott állapotadat-mintavétel (például **előtérbeli. QAS. ASCS**)
         1. Tartsa protokoll **TCP**, adja meg a port **3200**
         1. Üresjárati időkorlát akár 30 percig növelése
         1. **Ügyeljen arra, hogy Floating IP engedélyezése**
         1. Kattintson az OK gombra
      1. Az ASCS további portokat
         * Ismételje meg a fenti lépéseket a "d" portok 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14., 5**00**16 és a TCP az ASCS használata
      1. Az ASCS SSZON további portokat
         * Ismételje meg a fenti lépéseket a "d" portok 33**01**, 5**01**13, 5**01**14, 5**01**16 és a TCP az ASCS SSZON használata

> [!IMPORTANT]
> Ne engedélyezze a TCP időbélyegeket Azure Load Balancer mögé helyezett Azure virtuális gépeken. Sikertelen állapotadat-mintavételek engedélyezése TCP időbélyegek miatt. A paramétert **net.ipv4.tcp_timestamps** való **0**. További részletekért lásd: [Load Balancer állapot-mintavételei](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Támasztja fürt létrehozása

Kövesse a [támasztja a SUSE Linux Enterprise Server az Azure-beli beállítása](high-availability-guide-suse-pacemaker.md) hozzon létre egy alapszintű támasztja fürtről ehhez (A) SCS-kiszolgálón.

### <a name="installation"></a>Telepítés

A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – 1. csomópont csak érvényes vagy **: [2]** – 2. csomópont csak érvényes.

1. **[A]**  SUSE-összekötő telepítése

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Ne használjon gondolatjelek a fürtcsomópontok állomásnevét. Ellenkező esetben a fürt nem fog működni. Ez egy ismert korlátozás és SUSE dolgozik a javítást. A javítás, a javítás az sap-suse-felhő-összekötő csomag elérhető lesz.

   Győződjön meg arról, hogy telepítette-e a SAP SUSE-fürt összekötő új verziója. A régit sap_suse_cluster_connector byla volána és a egy újra nevezzük **sap-suse-fürtösszekötő**.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
      Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   Version        : 3.1.0-8.1
   Arch           : noarch
   Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
   Support Level  : Level 3
   Installed Size : 45.6 KiB
   Installed      : Yes
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.1.0-8.1.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]**  Frissítés SAP erőforrás ügynökök  
   
   Az erőforrás-ügynökök csomag javítást kell használnia az új konfigurációt, ebben a cikkben leírt. Ellenőrizheti, ha a javítás már telepítve van a következő paranccsal

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   A kimenet hasonlít kell lennie.

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Ha a grep parancs nem találja a IS_ERS paraméter, a javítás található telepítenie kell [a SUSE letöltési oldala](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]**  Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnevet

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver-telepítés előkészítése

1. **[A]**  a közös könyvtárak létrehozása

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]**  Autofs konfigurálása

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Fájl létrehozása

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > Azure NetApp Files jelenleg csak NFSv3 támogatja. Ne hagyja ki a nfsvers = 3 kapcsoló.
   
   Indítsa újra az új megosztások csatlakoztatására autofs
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]**  Fájl cseréje konfigurálása

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Indítsa újra az ügynököt, a módosítás aktiválása

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/SSZON telepítése

1. **[1]**  Egy virtuális IP- és állapot-mintavételi ASCS-példány létrehozása

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben, és elindulnak, hogy az összes erőforrás. Nem számít mely erőforrásokat futtató csomóponton.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #     rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]**  SAP NetWeaver ASCS telepítése  

   SAP NetWeaver ASCS telepítése egy virtuális állomásnevet, amely leképezi a terheléselosztó előtérbeli konfigurációját a ascs rendszerbe fut be, az IP-címe például használatával első csomópontjára legfelső szintű <b>anftstsapvh</b>, <b>10.1.1.20</b> és a példányok számát, amelyet a például használja a terheléselosztó a mintavétel <b>00</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni. SAPINST_USE_HOSTNAME paraméter használatával telepítse az SAP virtuális állomásnevet használja.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Ha a telepítés sikertelen lesz, hozzon létre egy almappát a usr/sap/**QAS**/ASCS**00**, próbálja meg a tulajdonos és a ASCS csoportja**00** mappát, majd próbálkozzon újra. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]**  Egy virtuális IP- és állapot-mintavételi SSZON-példány létrehozása

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>01</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben, és elindulnak, hogy az összes erőforrás. Nem számít mely erőforrásokat futtató csomóponton.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **a(z) [2]**  SAP NetWeaver SSZON telepítése

   SAP NetWeaver SSZON telepíti, a második csomópont használatával egy virtuális állomásnevet, amely leképezi a terheléselosztó előtérbeli konfigurációját a SSZON az IP-címét, például a legfelső szintű <b>anftstsapers</b>, <b>10.1.1.21</b> és a példányok számát, amelyet a például használja a terheléselosztó a mintavétel <b>01</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni. SAPINST_USE_HOSTNAME paraméter használatával telepítse az SAP virtuális állomásnevet használja.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Használjon SWPM SP 20 PL 05 vagy újabb verziója. Alacsonyabb verziók nincs beállítva az engedélyeket, és a telepítés sikertelen lesz.

   Ha a telepítés sikertelen lesz, hozzon létre egy almappát a usr/sap/**QAS**/ERS**01**, próbálja meg a tulajdonos és a SSZON csoportja**01** mappát, majd próbálkozzon újra.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]**  Adapt ASCS/SCS és SSZON példány profilok
 
   * ASCS/SCS-profil

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * SSZON profil

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]**  Életben tartási konfigurálása

   A SAP NetWeaver-kiszolgáló és az ASC/SCS közötti kommunikáció áthalad egy szoftveres terheléselosztót. A load balancer inaktív kapcsolatok leválasztása után konfigurálható időkorlát. Ennek megelőzéséhez szüksége egy paraméter az SAP NetWeaver ASCS/SCS-profilban és a Linux rendszer beállításait módosíthatja. Olvasási [SAP Megjegyzés 1410736] [ 1410736] további információt.

   Az ASCS/SCS profil paraméter célzó/encni/set_so_keepalive már lett adva az előző lépésben.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]**  a telepítés után az SAP-felhasználó konfigurálása

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]**  A ASCS és SSZON SAP-szolgáltatás hozzáadása a sapservice fájlhoz

   Az ASCS bejegyzést a második csomópontra történő szolgáltatást, és másolja a SSZON szolgáltatás bejegyzés az első fürtcsomópont hozzáadása.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]**  Az SAP-fürt erőforrásainak létrehozása

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben, és elindulnak, hogy az összes erőforrás. Nem számít mely erőforrásokat futtató csomóponton.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver alkalmazás kiszolgáló előkészítése 

Egyes adatbázisokhoz szükséges, hogy az adatbázis-példány telepítése alkalmazáskiszolgáló hajtja végre. Készítse elő az alkalmazás server virtuális gépek ezekben az esetekben használhatja őket.

A lépések Csengő azt feltételezik, hogy a kiszolgáló egy kiszolgálón telepíti a ASCS/SCS- és HANA-kiszolgálókról különböző. Ellenkező esetben néhány (pl. állomásnév-feloldás konfigurálása) az alábbi lépéseket nem szükséges.

A következő elemek van fűzve előtagként vagy **[A]** – szolgáltatói CÍMEI és a Rendszergazdájaként, alkalmazandó **[P]** – csak érvényes szolgáltatói CÍMEI vagy **[S]** – csak érvényes Rendszergazdájaként.


1. **[A]**  Az operációs rendszer konfigurálása

   A szabálytalan gyorsítótár méretének csökkentésére. További információkért lásd: [SLES 11/12 alacsony az írási teljesítmény nagy RAM-MAL rendelkező kiszolgálók](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]**  Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnevet

   ```bash
   sudo vi /etc/hosts
   ```

   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]**  a sapmnt könyvtár létrehozása

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]**  a szolgáltatói CÍMEI könyvtár létrehozása

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]**  A AAS könyvtár létrehozása

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]**  Szolgáltatói CÍMEI autofs konfigurálása

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Új fájl létrehozása

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/ursap<b>qas</b>pas
   </code></pre>

   Indítsa újra az új megosztások csatlakoztatására autofs

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]**  AAS autofs konfigurálása

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Új fájl létrehozása

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   Indítsa újra az új megosztások csatlakoztatására autofs

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]**  Fájl cseréje konfigurálása

   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Indítsa újra az ügynököt, a módosítás aktiválása

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Adatbázis telepítése

Ebben a példában SAP Netweavertől az SAP HANA telepítve van. Minden támogatott adatbázis használhatja a telepítéshez. Az Azure-beli SAP HANA telepítése További információkért lásd: [magas rendelkezésre állás az SAP HANA Azure-beli virtuális gépeken (VM)][sap-hana-ha]. Támogatott adatbázisok listájának megtekintéséhez lásd: [SAP Megjegyzés 1928533][1928533].

* Futtassa az SAP adatbázis-példány telepítése

   Telepítse az SAP NetWeaver-adatbázispéldány legfelső szintű használatával egy virtuális állomásnevet, amely a terheléselosztó előtérbeli konfigurációját az adatbázis IP-címére van leképezve.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver alkalmazás kiszolgáló telepítése

Kövesse az alábbi lépéseket egy SAP-alkalmazáskiszolgáló telepítése.

1. **[A]**  Előkészítése az alkalmazáskiszolgáló kövesse a fejezet a [SAP NetWeaver alkalmazás server előkészítési](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) fenti a kiszolgáló előkészítéséhez.

2. **[A]**  Alkalmazáskiszolgáló telepítése SAP NetWeaver elsődleges vagy a további SAP NetWeaver-alkalmazások kiszolgáló telepítése.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]**  Biztonságos frissítés SAP HANA-tároló

   Frissítse az SAP HANA biztonságos tároló, a SAP HANA-Rendszerreplikálást beállítása virtuális nevére mutasson.

   Futtassa a következő parancsot a tételek felsorolása
   <pre><code>
   hdbuserstore List
   </code></pre>

   Ez felsorolásban szerepelnie kell az összes bejegyzést, és kell kinéznie:
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   A kimenet mutatja, hogy a virtuális gépet, és nem a terheléselosztó IP-cím mutat-e alapértelmezett bejegyzését IP-címét. Ez a bejegyzés kell módosítani, hogy a terheléselosztó virtuális állomásnevét mutasson. Győződjön meg arról, hogy ugyanazt a portot használja (**30313** a fenti kimenetben) és az adatbázis nevét (**QAS** a fenti kimenetben)!

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Tesztelje a fürt beállítása

A következő tesztek elvégzése a vizsgálati esetek másolatát a [ajánlott eljárások útmutatók, SUSE][suse-ha-guide]. A felhasználók kényelme érdekében másolja őket. Mindig is olvassa el az ajánlott eljárások útmutatókat, és hajtsa végre, előfordulhat, hogy hozzáadott összes további vizsgálat.

1. Teszt HAGetFailoverConfig HACheckConfig és HACheckFailoverConfig

   Futtassa a következő parancsokat, \<sapsid > adm, ahol az ASCS-példány fut, a csomóponton. Ha a parancs sikertelen, és a sikertelen: Nincs elég memória, ez oka lehet a gazdanév található kötőjelek. Ez egy ismert probléma, és úgy lesz kijavítva, SUSE az sap-suse-fürtösszekötő csomagban.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. Az ASCS-példány manuális áttelepítésével kapcsolatban

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Futtassa az alábbi parancsokat rendszergazdaként az ASCS-példányát.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. Teszt HAFailoverToNode

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Futtassa a következő parancsokat, \<sapsid > adm ASCS-példányát.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Csomópont-összeomlás szimulálása 

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Az alábbi parancsot rendszergazdaként a csomóponton a ASCS-példányt futtató

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   SBD használatakor támasztja kell nem indul el automatikusan a leállítva csomóponton. Az állapot, miután elindult a csomópontot újra kell kinéznie.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   A következő parancsok használatával támasztja a leállítva csomóponton induljon el, a SBD üzenetek tiszta és tisztítsa meg a sikertelen erőforrásokat.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Manuális újraindítás ASCS példány tesztelése

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Hozzon létre egy sorba zárolási szerint, például egy felhasználó a tranzakció su01 szerkesztésre. Futtassa a következő parancsokat, < sapsid\>adm a csomóponton, hol futnak az ASCS-példány. A parancsok a ASCS példány leáll, és indítsa el újra. A sorba zárolást várhatóan ebben a tesztben elvész.

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Az ASCS-példány már le kell tiltani a támasztja

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Indítsa el az ASCS-példány újra ugyanazon a csomóponton.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   A sorba zárolási tranzakció su01 az elveszett eszköz kell lennie, és a háttér-kell vissza lett állítva. Erőforrás állapotának a vizsgálat után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Üzenet kiszolgálói folyamat leállítása

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Futtassa az alábbi parancsokat a folyamat az üzenet-kiszolgáló azonosításához, és állítsa le a legfelső szintű.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Ha Ön csak kill a üzenetkiszolgáló egyszer, által sapstart újraindul. Ha Ön kill Ez gyakran elegendő támasztja a rendszer végül helyezze át az ASCS-példány egy másik csomópontra. Futtassa az alábbi parancsokat rendszergazdaként a vizsgálat után az erőforrás állapotának ASCS és SSZON példány karbantartása.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Sorba kiszolgálói folyamat leállítása

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Futtassa az alábbi parancsokat rendszergazdaként azon a csomóponton hol futnak az ASCS-példány a sorba kiszolgáló leállítása.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   Az ASCS példányt kell azonnal átadja a feladatokat egy másik csomópontra. A SSZON példányt is feladatátvételt az ASCS-példány indítása után. Futtassa az alábbi parancsokat rendszergazdaként a vizsgálat után az erőforrás állapotának ASCS és SSZON példány karbantartása.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Sorba replikációs kiszolgálói folyamat leállítása

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Futtassa a következő parancsot rendszergazdaként azon a csomóponton a SSZON példányt futtató kiszolgáló a sorba replikációs kiszolgálói folyamat leállítása.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Csak a parancs futtatása után, sapstart újraindítja a folyamatot. Kell futtatnia, ha gyakran elegendő sapstart lesz indítsa újra a folyamatot, és az erőforrás egy leállított állapotba kerül. Futtassa az alábbi parancsokat rendszergazdaként a vizsgálat után az erőforrás állapotának SSZON példány karbantartása.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Erőforrás állapotának a vizsgálat után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Sorba sapstartsrv folyamat leállítása

   Erőforrás állapotának a vizsgálat megkezdése előtt:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Futtassa az alábbi parancsokat rendszergazdaként azon a csomóponton hol futnak az ascs rendszerbe fut be.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   A sapstartsrv folyamat mindig újra kell indítani az támasztja erőforrás ügynök. Erőforrás állapotának a vizsgálat után:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása SAP][planning-guide]
* [Az SAP az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* Megtudhatja, hogyan kell magas rendelkezésre állást és az SAP vész-helyreállítási terv létrehozása 
* Az Azure-ban (nagyméretű példányok), HANA lásd [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md).
* Magas rendelkezésre állást és az Azure virtuális gépeken SAP Hana vész-helyreállítási terv létrehozásához, lásd: [magas rendelkezésre állás az SAP HANA Azure-beli virtuális gépeken (VM)][sap-hana-ha]
