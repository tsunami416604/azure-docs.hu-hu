---
title: Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server SAP-alkalmazások |} A Microsoft Docs
description: Magas rendelkezésre állású útmutató az SAP NetWeaver SUSE Linux Enterprise Server a SAP-alkalmazások
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
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
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: 935b501964435e80172ef3e147f777bf47119b48
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285122"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server az Azure virtuális gépeken SAP-alkalmazások

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Ez a cikk ismerteti a virtuális gépek üzembe helyezése, konfigurálja a virtuális gépek, telepítse a fürt keretrendszert és egy magas rendelkezésre állású SAP NetWeaver 7.50 rendszer telepítéséhez.
A példa konfigurációk esetén a telepítési parancsokat stb. ASCS példányszámának 00, SSZON példányok száma 02, és az SAP-rendszer azonosító NW1 szolgál. Az erőforrások (például virtuális gépek, virtuális hálózatok) a példában nevei azt feltételezik, hogy használja a [sablon összevont] [ template-converged] SAP-rendszerrel azonosító NW1 erőforrások létrehozásához.

Olvassa el először a következő SAP-megjegyzések és tanulmányok

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
* [Az Azure virtuális gépek üzembe helyezése, az SAP, Linux (Ez a cikk)][deployment-guide]
* [Az Azure Virtual Machines DBMS üzembe helyezése, az SAP, Linux rendszeren][dbms-guide]
* [SUSE SAP magas rendelkezésre ÁLLÁS ajánlott eljárások gyűjteménye] [ suse-ha-guide] a útmutatókat tartalmaz minden szükséges információját, állítsa be a Netweaver magas rendelkezésre ÁLLÁS és a helyszíni SAP HANA-Rendszerreplikálást. Használata esetén ez végigvezeti a általános kiindulópontként használja. Sokkal részletesebb információkat biztosítanak.
* [Magas rendelkezésre álló NFS-tár DRBD és támasztja] [ suse-drbd-guide] útmutató a magas rendelkezésre állású NFS-kiszolgáló beállítása minden szükséges információját tartalmazza. Ez az útmutató használja kiindulópontként.


## <a name="overview"></a>Áttekintés

Magas rendelkezésre állás, az SAP NetWeaver NFS-kiszolgáló szükséges. Az NFS-kiszolgáló egy külön fürtben lett konfigurálva, és több SAP-rendszerek által használható.

![SAP NetWeaver magas rendelkezésre állás – Áttekintés](./media/high-availability-guide-suse/img_001.png)

Az NFS-kiszolgáló, az SAP NetWeaver ascs rendszerbe fut be, az SAP NetWeaver SCS, az SAP NetWeaver SSZON és az SAP HANA-adatbázis virtuális állomásnevet és a virtuális IP-címek használata. Az Azure-ban a terheléselosztó virtuális IP-cím szükséges. Az alábbi lista tartalmazza (A) konfigurációjának SCS és SSZON terheléselosztó.

### <a name="ascs"></a>(A)SCS

* Előtér-konfigurációjához
  * IP-cím 10.0.0.7
* Háttér-konfiguráció
  * (A) részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik SCS/SSZON fürt
* Mintavételi Port
  * Port 620**&lt;nr&gt;**
* Terheléselosztás szabályok
  * 32**&lt;nr&gt;**  TCP
  * 36**&lt;nr&gt;**  TCP
  * 39**&lt;nr&gt;**  TCP
  * 81-es**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

### <a name="ers"></a>ERS

* Előtér-konfigurációjához
  * IP-cím 10.0.0.8
* Háttér-konfiguráció
  * (A) részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik SCS/SSZON fürt
* Mintavételi Port
  * Port 621**&lt;nr&gt;**
* Terheléselosztás szabályok
  * 33**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>Egy magas rendelkezésre állású NFS-kiszolgáló beállítása

SAP NetWeaver az átvitel és a profil könyvtár egy megosztott tároló szükséges hozzá. Olvasási [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken] [ nfs-ha] az NFS-kiszolgáló beállítása az SAP NetWeaver számára.

## <a name="setting-up-ascs"></a>(A) SCS beállítása

Használhatja a sablont a githubból az Azure minden szükséges Azure-erőforrás, beleértve a virtuális gépek üzembe helyezéséhez, rendelkezésre állás beállítása és a terheléselosztó vagy manuálisan telepítheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux-n keresztül az Azure-sablon üzembe helyezése

Az Azure Marketplace-en SUSE Linux Enterprise Server SAP alkalmazások 12, amelyek új virtuális gépek telepítéséhez használhatja a kép tartalmazza. A Piactéri rendszerkép tartalmazza az erőforrás-ügynököt az SAP NetWeaver számára.

Használhatja a gyorsindítási sablonok egyikét a githubon üzembe helyezéséhez szükséges összes erőforrást. A sablon üzembe helyezi a virtuális gépek, a terheléselosztó, a rendelkezésre állási csoport stb. Kövesse az alábbi lépéseket a sablon üzembe helyezéséhez:

1. Nyissa meg a [ASCS/SCS több SID sablon] [ template-multisid-xscs] vagy a [sablon összevont] [ template-converged] csak az ASC/SCS-sablont hoz létre az Azure Portalon a terheléselosztási szabályok az SAP NetWeaver ASCS/SCS és SSZON példányok (csak Linux), mivel a konvergens sablon is létrehoz a terheléselosztási szabályok (például a Microsoft SQL Server vagy az SAP HANA) adatbázis. Ha azt tervezi, hogy az SAP NetWeaver-alapú rendszert telepíti, és az adatbázis telepítésével az azonos gépeken is érdemes a [sablon összevont][template-converged].
1. Adja meg a következő paraméterek
   1. Erőforrás-előtag (csak a sablon ASCS/SCS több SID)  
      Adja meg a használni kívánt előtagot. Az érték előtagjaként is szolgál az üzembe helyezett erőforrásokat.
   3. Az SAP-rendszer-azonosító (csak konvergált sablon)  
      Adja meg az SAP az SAP-rendszer telepíteni kívánt rendszer-azonosító. Az azonosító az üzembe helyezett erőforrások előtagjaként is szolgál.
   4. A készlet típusa  
      Az SAP NetWeaver stack típusának kiválasztása
   5. Operációs rendszer típusa  
      Válasszon ki egy Linux-disztribúció. Ebben a példában válassza ki a SLES 12 saját
   6. Adatbázis típusa  
      Válassza ki a HANA
   7. Az SAP-rendszer mérete  
      Az új rendszer biztosít SAP mennyisége. Ha nem, hogy a rendszer hány SAP, kérje meg, a SAP technológiai partnerek vagy a rendszerintegrátor
   8. Rendszer rendelkezésre állását  
      Válassza ki a magas rendelkezésre ÁLLÁS
   9. Rendszergazdai felhasználónév és a rendszergazdai jelszó  
      Egy új felhasználót hoz létre, amely segítségével jelentkezzen be a számítógépen.
   10. Alhálózati azonosító  
   Az alhálózat, amelyhez a virtuális gépek kapcsolódnia kell a azonosítója.  Hagyja üresen, ha azt szeretné, hozzon létre egy új virtuális hálózatot, vagy válassza ki a használt vagy létrehozott ugyanazon az alhálózaton az NFS-kiszolgáló központi telepítésének részeként. Az azonosító általában néz ki: /subscriptions/**&lt;előfizetés-azonosító&gt;**/resourceGroups/**&lt;erőforráscsoport-név&gt;**/szolgáltatók/ Microsoft.Network/virtualNetworks/**&lt;virtuálishálózat-nevet&gt;**/subnets/**&lt;alhálózat neve&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Manuálisan üzembe helyezése Linux rendszerű Azure-portálon

Először a virtuális gépek az NFS-fürt létrehozásához. Ezt követően hozzon létre egy terheléselosztó, és a virtuális gépek használata a háttérkészletek.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Egy rendelkezésre állási csoport létrehozása  
   Maximális frissítési tartomány beállítása
1. 1 virtuális gép létrehozása   
   Legalább SLES4SAP 12 SP1, az ebben a példában a SLES4SAP 12 SP1 kép https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES For SAP alkalmazások 12 SP1 esetén használatos.  
   Válassza ki a korábban létrehozott rendelkezésre állási  
1. 2 virtuális gép létrehozása   
   Legalább SLES4SAP 12 SP1, az ebben a példában a SLES4SAP 12 SP1 kép https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES For SAP alkalmazások 12 SP1 esetén használatos.  
   Válassza ki a korábban létrehozott rendelkezésre állási  
1. Mindkét virtuális gép legalább egy adatlemez hozzáadása  
   Az adatlemezek használtusr/sap/`<SAPSID`> könyvtár
1. Load Balancer létrehozása (belső)  
   1. Hozzon létre az előtérbeli IP-címek
      1. Az ASCS 10.0.0.7 IP-címe
         1. Nyissa meg a terheléselosztó, válassza ki az előtérbeli IP-címkészlet, kattintson a Hozzáadás gombra
         1. Adja meg az új előtérbeli IP-címkészlet nevét (például **nw1 – ascs-frontend**)
         1. A hozzárendelés Static értékre, és adja meg az IP-címet (például **10.0.0.7**)
         1. Kattintson az OK gombra
      1. Az ASCS SSZON 10.0.0.8 átjáró IP-címe
         * Ismételje meg a fenti IP-cím létrehozása a SSZON lépéseket (például **10.0.0.8** és **nw1-aers-háttérrendszer**)
   1. Hozzon létre a háttérkészletek
      1. Az ASCS háttérkészlet létrehozása
         1. Nyissa meg a terheléselosztó, válassza ki a háttérkészletek, és kattintson a Hozzáadás gombra
         1. Adja meg az új háttérkészlet nevét (például **nw1 – ascs-háttérrendszer**)
         1. Kattintson a Hozzáadás elemre egy virtuális gépet.
         1. Válassza ki a korábban létrehozott rendelkezésre állási csoport
         1. Válassza ki a virtuális gépek (A) SCS-fürt
         1. Kattintson az OK gombra
      1. Az ASCS SSZON háttérkészlet létrehozása
         * Ismételje meg a fenti lépéseket, és hozzon létre egy háttérkészlet on a felhasználók számára (például **nw1-aers-háttérrendszer**)
   1. Az állapotminták létrehozása
      1. Port 620**00** ASCS számára
         1. Nyissa meg a terheléselosztó, válassza ki az állapotmintákat, kattintson a Hozzáadás gombra
         1. Adja meg az új állapotadat-mintavétel nevét (például **a hp ascs nw1**)
         1. Válassza ki a TCP protokoll, port 620**00**, időköz 5 és a nem kifogástalan állapot küszöbértéke 2
         1. Kattintson az OK gombra
      1. Port 621**02** ASCS SSZON számára
         * Ismételje meg a fenti lépéseket, és hozzon létre egy állapotmintát az on-felhasználók számára (például 621**02** és **a hp aers nw1**)
   1. Terheléselosztás szabályok
      1. 32**00** TCP ASCS használata
         1. Nyissa meg a terheléselosztó, terheléselosztási szabályok kiválasztása, és kattintson a Hozzáadás gombra
         1. Adja meg az új terheléselosztó-szabályt nevét (például **nw1-lb-3200**)
         1. Válassza ki az előtérbeli IP-cím, a háttérkészlet és a korábban létrehozott állapotadat-mintavétel (például **nw1 – ascs-frontend**)
         1. Tartsa protokoll **TCP**, adja meg a port **3200**
         1. Üresjárati időkorlát akár 30 percig növelése
         1. **Ügyeljen arra, hogy Floating IP engedélyezése**
         1. Kattintson az OK gombra    
      1. Az ASCS további portokat
         * Ismételje meg a fenti lépéseket a portokhoz 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14., 5**00**16 és a TCP az ASCS használata
      1. Az ASCS SSZON további portokat
         * Ismételje meg a fenti lépéseket a portokhoz 33**02**, 5**02**13, 5**02**14, 5**02**16 és a TCP az ASCS SSZON használata

### <a name="create-pacemaker-cluster"></a>Támasztja fürt létrehozása

Kövesse a [támasztja a SUSE Linux Enterprise Server az Azure-beli beállítása](high-availability-guide-suse-pacemaker.md) hozzon létre egy alapszintű támasztja fürtről ehhez (A) SCS-kiszolgálón.

### <a name="installation"></a>Telepítés

A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – 1. csomópont csak érvényes vagy **: [2]** – 2. csomópont csak érvényes.

1. **[A]**  SUSE-összekötő telepítése
   
   <pre><code>
   sudo zypper install sap-suse-cluster-connector
   </code></pre>

   Győződjön meg arról, hogy telepítette-e a SAP SUSE-fürt összekötő új verziója. A régit sap_suse_cluster_connector byla volána és a egy újra nevezzük **sap-suse-fürtösszekötő**.

   <pre><code>
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

1. **[A]**  Frissítés SAP erőforrás ügynökök  
   
   Az erőforrás-ügynökök csomag javítást kell használnia az új konfigurációt, ebben a cikkben leírt. Ellenőrizheti, ha a javítás már telepítve van a következő paranccsal

   <pre><code>
   sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   A kimenet hasonlít kell lennie.

   <pre><code>
   &lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Ha a grep parancs nem találja a IS_ERS paraméter, a javítás található telepítenie kell [a SUSE letöltési oldala](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code>
   # example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]**  Állomásnév-feloldás beállítása   

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnevet

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver-telepítés előkészítése

1. **[A]**  a közös könyvtárak létrehozása

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]**  Autofs konfigurálása
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Fájl létrehozása

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   /usr/sap/<b>NW1</b>/ASCS<b>00</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCS
   /usr/sap/<b>NW1</b>/ERS<b>02</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS
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

   <pre><code>
   sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/SSZON telepítése

1. **[1]**  Egy virtuális IP- és állapot-mintavételi ASCS-példány létrehozása

   <pre><code>
   sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben, és elindulnak, hogy az összes erőforrás. Nem számít mely erőforrásokat futtató csomóponton.

   <pre><code>
   sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]**  SAP NetWeaver ASCS telepítése  

   SAP NetWeaver ASCS telepítése egy virtuális állomásnevet, amely leképezi a terheléselosztó előtérbeli konfigurációját a ascs rendszerbe fut be, az IP-címe például használatával első csomópontjára legfelső szintű <b>nw1 – ascs</b>, <b>10.0.0.7</b> és a példányok száma, amelyek például használja a terheléselosztó a mintavétel <b>00</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Ha a telepítés sikertelen lesz, hozzon létre egy almappát a usr/sap/**NW1**/ASCS**00**, próbálja meg a tulajdonos és a ASCS csoportja**00** mappát, majd próbálkozzon újra.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]**  Egy virtuális IP- és állapot-mintavételi SSZON-példány létrehozása

   <pre><code>
   sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_NW1_ASCS,nc_NW1_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>
 
   Győződjön meg arról, hogy a fürt állapota rendben, és elindulnak, hogy az összes erőforrás. Nem számít mely erőforrásokat futtató csomóponton.

   <pre><code>
   sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **a(z) [2]**  SAP NetWeaver SSZON telepítése  

   SAP NetWeaver SSZON telepíti, a második csomópont használatával egy virtuális állomásnevet, amely leképezi a terheléselosztó előtérbeli konfigurációját a SSZON az IP-címét, például a legfelső szintű <b>nw1-aers</b>, <b>10.0.0.8</b> és a példányok száma, amelyek például használja a terheléselosztó a mintavétel <b>02</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Használjon SWPM SP 20 PL 05 vagy újabb verziója. Alacsonyabb verziók nincs beállítva az engedélyeket, és a telepítés sikertelen lesz.

   Ha a telepítés sikertelen lesz, hozzon létre egy almappát a usr/sap/**NW1**/ERS**02**, próbálja meg a tulajdonos és a SSZON csoportja**02** mappát, majd próbálkozzon újra.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>
   > 

1. **[1]**  Adapt ASCS/SCS és SSZON példány profilok
 
   * ASCS/SCS-profil

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
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
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   </code></pre>


1. **[A]**  Életben tartási konfigurálása

   A SAP NetWeaver-kiszolgáló és az ASC/SCS közötti kommunikáció áthalad egy szoftveres terheléselosztót. A load balancer inaktív kapcsolatok leválasztása után konfigurálható időkorlát. Ennek megelőzéséhez szüksége egy paraméter az SAP NetWeaver ASCS/SCS-profilban és a Linux rendszer beállításait módosíthatja. Olvasási [SAP Megjegyzés 1410736] [ 1410736] további információt.
   
   Az ASCS/SCS profil paraméter célzó/encni/set_so_keepalive már lett adva az előző lépésben.

   <pre><code> 
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  a telepítés után az SAP-felhasználó konfigurálása
 
   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm   
   </code></pre>

1. **[1]**  A ASCS és SSZON SAP-szolgáltatás hozzáadása a sapservice fájlhoz

   Az ASCS bejegyzést a második csomópontra történő szolgáltatást, és másolja a SSZON szolgáltatás bejegyzés az első fürtcsomópont hozzáadása.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]**  Az SAP-fürt erőforrásainak létrehozása

   <pre><code>
   sudo crm configure property maintenance-mode="true"   
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben, és elindulnak, hogy az összes erőforrás. Nem számít mely erőforrásokat futtató csomóponton.

   <pre><code>
   sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver alkalmazás kiszolgáló előkészítése

Egyes adatbázisokhoz szükséges, hogy az adatbázis-példány telepítése alkalmazáskiszolgáló hajtja végre. Készítse elő az alkalmazás server virtuális gépek ezekben az esetekben használhatja őket.

A lépések Csengő azt feltételezik, hogy a kiszolgáló egy kiszolgálón telepíti a ASCS/SCS- és HANA-kiszolgálókról különböző. Ellenkező esetben néhány (pl. állomásnév-feloldás konfigurálása) az alábbi lépéseket nem szükséges.

1. Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnevet
   ```bash
   sudo vi /etc/hosts
   ```
   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév    
    
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. A sapmnt könyvtár létrehozása

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Autofs konfigurálása
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Új fájl létrehozása

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Indítsa újra az új megosztások csatlakoztatására autofs

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Lapozófájl konfigurálása
 
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

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Adatbázis telepítése

Ebben a példában SAP Netweavertől az SAP HANA telepítve van. Minden támogatott adatbázis használhatja a telepítéshez. Az Azure-beli SAP HANA telepítése További információkért lásd: [magas rendelkezésre állás az SAP HANA Azure-beli virtuális gépeken (VM)][sap-hana-ha]. Támogatott adatbázisok listájának megtekintéséhez lásd: [SAP Megjegyzés 1928533][1928533].

1. Futtassa az SAP adatbázis-példány telepítése

   Gyökér szintű használatával egy virtuális állomásnevet, amely leképezi a terheléselosztó előtérbeli konfigurációját az adatbázis IP-címét, például az SAP NetWeaver-adatbázispéldány telepítése <b>nw1-db</b> és <b>10.0.0.13</b>.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver alkalmazás kiszolgáló telepítése

Kövesse az alábbi lépéseket egy SAP-alkalmazáskiszolgáló telepítése. 

1. Alkalmazás-kiszolgáló előkészítése

Kövesse a lépéseket a fejezetben [SAP NetWeaver alkalmazás kiszolgáló előkészítése](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) fenti a kiszolgáló előkészítéséhez.

1. SAP NetWeaver-alkalmazáskiszolgáló telepítése

   Elsődleges vagy a további SAP NetWeaver alkalmazást kiszolgáló telepítése.

   A sapinst paraméter SAPINST_REMOTE_ACCESS_USER használhatja, hogy a nem gyökér felhasználó sapinst csatlakozni.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA a biztonságos tár frissítése

   Frissítse az SAP HANA biztonságos tároló, a SAP HANA-Rendszerreplikálást beállítása virtuális nevére mutasson.

   Futtassa a következő parancsot a tételek felsorolása
   <pre><code>
   hdbuserstore List
   </code></pre>

   Ez felsorolásban szerepelnie kell az összes bejegyzést, és kell kinéznie:
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: HN1
   </code></pre>

   A kimenet mutatja, hogy a virtuális gépet, és nem a terheléselosztó IP-cím mutat-e alapértelmezett bejegyzését IP-címét. Ez a bejegyzés kell módosítani, hogy a terheléselosztó virtuális állomásnevét mutasson. Győződjön meg arról, hogy ugyanazt a portot használja (**30313** a fenti kimenetben)!

   <pre><code>
   su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>További lépések
* [Az Azure virtuális gépek tervezése és megvalósítása SAP][planning-guide]
* [Az SAP az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* Magas rendelkezésre állást és az Azure-ban (nagyméretű példányok) SAP Hana vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md).
* Magas rendelkezésre állást és az Azure virtuális gépeken SAP Hana vész-helyreállítási terv létrehozásához, lásd: [magas rendelkezésre állás az SAP HANA Azure-beli virtuális gépeken (VM)][sap-hana-ha]
