---
title: Az Azure virtuális gépek magas rendelkezésre állás a SAP NetWeaver a SUSE Linux Enterprise Server SAP alkalmazásokhoz |} Microsoft Docs
description: Magas rendelkezésre állású útmutatója az SAP NetWeaver a SUSE Linux Enterprise Server SAP-alkalmazásokból
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
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
ms.openlocfilehash: f1d2725237d2cf059450ce7e2c1600b24d17f35c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Magas rendelkezésre állás a SAP NetWeaver a SUSE Linux Enterprise Server Azure virtuális gépeken az SAP-alkalmazásokból

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

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Ez a cikk ismerteti, hogyan telepítse a virtuális gépeket, a virtuális gépet állíthat be, a fürt-keretrendszer telepítése és magas rendelkezésre állású SAP NetWeaver 7.50 rendszert telepíteni.
A példa konfigurációkban telepítési parancsok stb. Asc példányszámának 00, SSZON példány szám 02, és az SAP rendszer azonosító NW1 használatos. A példában szereplő erőforrások (például virtuális gépek, virtuális hálózatok) nevei azt feltételezik, használja a [sablon összevont] [ template-converged] SAP rendszer azonosító NW1 az erőforrások létrehozásához.

Olvassa el a következő SAP megjegyzések és által írt cikkeket először

* SAP Megjegyzés [1928533], amelynek van:
  * Az SAP szoftver központi telepítése támogatott Azure Virtuálisgép-méretek listáját
  * Az Azure Virtuálisgép-méretek fontos készletkapacitás információival
  * Támogatott SAP szoftver, és az operációs rendszer és az adatbázis kombinációját
  * A Windows és a Microsoft Azure Linux szükséges SAP kernel verziója

* SAP Megjegyzés [2015553] a SAP-támogatott SAP szoftverek központi telepítése az Azure-ban szükséges előfeltételeket ismerteti.
* SAP Megjegyzés [2205917] javasolt a SUSE Linux Enterprise Server operációs rendszer beállításait az SAP-alkalmazásokból
* SAP Megjegyzés [1944799] SUSE Linux Enterprise Server SAP HANA-irányelvek rendelkezik az SAP-alkalmazásokból
* SAP Megjegyzés [2178632] tartalmaz részletes információkat az Azure-ban SAP jelentett összes figyelési metrikákat.
* SAP Megjegyzés [2191498] vannak a szükséges SAP gazdagép-ügynök verziója Linux az Azure-ban.
* SAP Megjegyzés [2243692] SAP Azure Linux licenceléssel kapcsolatos információt tartalmaz.
* SAP Megjegyzés [1984787] SUSE Linux Enterprise Server 12 vonatkozó általános információkat tartalmaz.
* SAP Megjegyzés [1999351] további információkat talál az Azure fokozott Figyelőbővítmény az SAP rendelkezik.
* [SAP közösségi WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) rendelkezik az összes szükséges SAP megjegyzések Linux.
* [Azure virtuális gépek tervezési és megvalósítási az SAP Linux rendszeren][planning-guide]
* [Az Azure virtuális gépek telepítése az SAP, Linux (Ez a cikk)][deployment-guide]
* [Az SAP Linux Azure virtuális gépek DBMS-telepítés][dbms-guide]
* [SAP HANA SR teljesítményre optimalizált forgatókönyv][suse-hana-ha-guide]  
  Az útmutató a helyszíni SAP HANA replikációs beállítása az összes szükséges információkat tartalmazza. Ez az útmutató használja kiindulópontként.
* [Magas rendelkezésre álló NFS tár DRBD és támasztja] [ suse-drbd-guide] az útmutató a magas rendelkezésre állású NFS-kiszolgáló beállítása az összes szükséges információkat tartalmazza. Ez az útmutató használja kiindulópontként.


## <a name="overview"></a>Áttekintés

Magas rendelkezésre állás eléréséhez SAP NetWeaver az NFS-kiszolgáló szükséges. Az NFS-kiszolgáló egy külön fürtben lett konfigurálva, és több SAP-rendszerek által használható.

![SAP NetWeaver magas rendelkezésre állás – Áttekintés](./media/high-availability-guide-suse/img_001.png)

Az NFS-kiszolgáló, a SAP NetWeaver ASC, a SAP NetWeaver SCS, a SAP NetWeaver SSZON és az SAP HANA-adatbázisból virtuális állomásnév és a virtuális IP-címek használata. Az Azure a terheléselosztó virtuális IP-cím szükséges. Az alábbi listában láthatók (A) konfigurációjának SCS és SSZON belső terheléselosztót.

### <a name="ascs"></a>(A)SCS

* Előtérbeli konfigurációja
  * IP-cím 10.0.0.7
* Háttérkonfiguráció
  * Az összes virtuális gépet, amely a (A) részét kell képezniük elsődleges hálózati illesztők csatlakozik SCS/SSZON fürt
* Mintavételi portot
  * Port 620**&lt;nr&gt;**
* Terheléselosztás szabályok
  * 32**&lt;nr&gt;** TCP
  * 36**&lt;nr&gt;** TCP
  * 39**&lt;nr&gt;** TCP
  * 81**&lt;nr&gt;** TCP
  * 5**&lt;nr&gt;**13 TCP
  * 5**&lt;nr&gt;**14 TCP
  * 5**&lt;nr&gt;**16 TCP

### <a name="ers"></a>ERS

* Előtérbeli konfigurációja
  * IP-cím 10.0.0.8
* Háttérkonfiguráció
  * Az összes virtuális gépet, amely a (A) részét kell képezniük elsődleges hálózati illesztők csatlakozik SCS/SSZON fürt
* Mintavételi portot
  * Port 621**&lt;nr&gt;**
* Terheléselosztás szabályok
  * 33**&lt;nr&gt;** TCP
  * 5**&lt;nr&gt;**13 TCP
  * 5**&lt;nr&gt;**14 TCP
  * 5**&lt;nr&gt;**16 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>Egy magas rendelkezésre állású NFS-kiszolgáló beállítása

SAP NetWeaver az átvitel és a profil directory megosztott tárolót igényel. Olvasási [a SUSE Linux Enterprise Server Azure virtuális gépeken az NFS magas rendelkezésre állású] [ nfs-ha] hogyan SAP NetWeaver az NFS-kiszolgáló beállítása.

## <a name="setting-up-ascs"></a>(A) SCS beállítása

Használhatja a egy Azure-sablont a githubból minden szükséges Azure-erőforrások, például a virtuális gépek telepítése, beállítása rendelkezésre állás és a terheléselosztóhoz, vagy manuálisan is telepítheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux keresztül Azure sablon telepítése

Az Azure piactéren SUSE Linux Enterprise Server SAP alkalmazások 12-es segítségével új virtuális gépek telepítése a kép tartalmazza. A Piactéri lemezképhez SAP NetWeaver erőforrás ügynökön tartalmazza.

Segítségével a gyorsindítási sablonok egyikét a githubon központi telepítése az összes szükséges erőforrásokat. A sablon telepíti, a virtuális gépek, a terheléselosztó hasonló adataival, a rendelkezésre állási csoport stb. Kövesse az alábbi lépéseket a sablon telepítéséhez:

1. Nyissa meg a [ASC/SCS Multi SID sablon] [ template-multisid-xscs] vagy a [sablon összevont] [ template-converged] az Azure-portál a SCS/ASC sablon csak hoz létre a terheléselosztási szabályok SAP NetWeaver ASC/SCS és SSZON példányok (csak Linux), míg az átszervezett is létrejön a terheléselosztási szabályok-adatbázis (például a Microsoft SQL Server vagy az SAP HANA). Ha azt tervezi, hogy az SAP NetWeaver alapú rendszert telepíti, és szeretné telepíteni az adatbázis ugyanazon a gépen, használja a [sablon összevont][template-converged].
1. Adja meg a következő paraméterek
   1. Erőforrás-előtag (csak a sablon ASC/SCS Multi SID)  
      Adja meg a használni kívánt előtagot. Az érték a telepített erőforrások esetén használatos előtagjaként.
   3. SAP Rendszerazonosító (csak az átszervezett sablon)  
      Adja meg a telepíteni kívánt SAP rendszer SAP rendszer Azonosítóját. Az azonosító előtagjaként szolgál a telepített erőforrások esetén.
   4. A készlet típusa  
      Az SAP NetWeaver verem típusának kiválasztása
   5. Operációs rendszer típusa  
      Válasszon egyet a Linux terjesztéseket. Ehhez a példához válassza ki a SLES 12 saját
   6. DB típusa  
      Válassza ki a HANA
   7. SAP mérete  
      Az új rendszer biztosít SAP mennyisége. Ha nem tudja, hogy hány SAP, a rendszer szükséges, kérje meg, a SAP technológia Partner vagy a rendszer integráló
   8. Rendszer rendelkezésre állás  
      Válassza ki a magas rendelkezésre ÁLLÁSÚ
   9. Rendszergazda felhasználónevét és a rendszergazdai jelszó  
      Új felhasználó jön létre, amely segítségével jelentkezzen be a gépre.
   10. Alhálózati azonosító  
   Az alhálózat, amelyhez a virtuális gépek csatlakoznia kell az azonosítója.  Hagyja üresen, ha azt szeretné, hozzon létre egy új virtuális hálózatot, vagy válasszon ugyanazon az alhálózaton, amely a használni vagy létrehozni az NFS-kiszolgáló központi telepítésének részeként. Az azonosító általában a következőképpen néz következő**&lt;előfizetés-azonosító&gt;**/resourceGroups/**&lt;erőforráscsoport-név&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;virtuálishálózat-név&gt;**/subnets/**&lt;alhálózat neve&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Manuális központi telepítése Linux Azure-portálon

Először az NFS-fürt a virtuális gépek létrehozásához. Ezután hozzon létre egy adott terheléselosztóhoz, és a virtuális gépek használata a háttérkészlet.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Egy rendelkezésre állási csoport létrehozása  
   Készlet maximális frissítési tartomány
1. 1 virtuális gép létrehozása   
   Legalább SLES4SAP 12 SP1 verziójára, ez a példa a SLES4SAP 12 SP1 kép https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   Az SAP alkalmazások 12 SP1 SLES szolgál.  
   Válassza ki a rendelkezésre állási csoport korábban létrehozott  
1. 2. virtuális gép létrehozása   
   Legalább SLES4SAP 12 SP1 verziójára, ez a példa a SLES4SAP 12 SP1 kép https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   Az SAP alkalmazások 12 SP1 SLES szolgál.  
   Válassza ki a rendelkezésre állási csoport korábban létrehozott  
1. Legalább egy adatlemezt mindkét virtuális gépek hozzáadása  
   Az adatlemezek használjáka/usr/sap/`<SAPSID`> könyvtár
1. Hozzon létre egy terhelés-kiegyenlítő (belső)  
   1. Az előtérbeli IP-címek létrehozása
      1. A ASC 10.0.0.7 IP-címe
         1. Nyissa meg a terheléselosztó előtérbeli IP-készlet kiválasztása és kattintson a Hozzáadás gombra
         1. Adja meg az új előtérbeli IP-címkészlet nevét (például **nw1-ASC-előtérbeli**)
         1. A hozzárendelés értékűre statikus, majd adja meg az IP-cím (például **10.0.0.7**)
         1. Kattintson az OK gombra
      1. A ASC SSZON 10.0.0.8 átjáró IP-címe
         * Ismételje meg a fenti lépéseket, és hozzon létre egy IP-címet a SSZON (például **10.0.0.8** és **nw1-aers-háttérrendszer**)
   1. A háttér-címkészletek létrehozása
      1. A ASC háttérkészlet létrehozása
         1. Nyissa meg a terheléselosztóhoz, válassza ki a háttérkészlet, kattintson a Hozzáadás gombra
         1. Adja meg az új háttérkészlet nevét (például **nw1-ASC-háttérrendszer**)
         1. Kattintson a Hozzáadás gombra a virtuális gép.
         1. Válassza ki a korábban létrehozott rendelkezésre állási csoport
         1. Válassza ki a virtuális gépek (a) SCS fürt
         1. Kattintson az OK gombra
      1. A ASC SSZON háttérkészlet létrehozása
         * Ismételje meg a háttér-készlet létrehozása a SSZON a fent leírt lépésekkel (például **nw1-aers-háttérrendszer**)
   1. Az állapotfigyelő mintavételt létrehozása
      1. Port 620**00** ASC számára
         1. Nyissa meg a terheléselosztóhoz, válassza ki a állapotteljesítmény, kattintson a Hozzáadás gombra
         1. Adja meg az új állapotmintáihoz nevét (például **nw1-ASC-hp**)
         1. Válassza ki a TCP protokoll, port 620**00**, időköz 5 és sérült küszöbérték 2
         1. Kattintson az OK gombra
      1. Port 621**02** ASC on felhasználók számára
         * Ismételje meg a fenti lépéseket, és hozzon létre egy állapotmintáihoz a SSZON (például 621**02** és **nw1-aers-hp**)
   1. Terheléselosztás szabályok
      1. 32**00** ASC a TCP
         1. Nyissa meg a terheléselosztóhoz, válassza ki a terheléselosztási szabályok, kattintson a Hozzáadás gombra
         1. Adja meg az új terheléselosztási szabály nevét (például **nw1-lb-3200**)
         1. Válassza ki a front-end IP-címet, a háttérkészlet és a korábban létrehozott állapotmintáihoz (például **nw1-ASC-előtérbeli**)
         1. Tartsa protokoll **TCP**, írja be a port **3200**
         1. Növelje a 30 perc üresjárati időtúllépés
         1. **Ügyeljen arra, hogy a fix IP-Címek engedélyezése**
         1. Kattintson az OK gombra    
      1. További portokat a ASC
         * Ismételje meg a fenti portok, 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14., 5**00**16 és az ASC a TCP
      1. További portokat a ASC SSZON
         * Ismételje meg a fenti portok 33**02**, 5**02**13, 5**02**14, 5**02**16 és az ASC SSZON a TCP

### <a name="create-pacemaker-cluster"></a>Támasztja fürt létrehozása

Kövesse a [támasztja a SUSE Linux Enterprise Server az Azure-ban beállítása](high-availability-guide-suse-pacemaker.md) alapvető létrehozásához támasztja fürt ehhez (A) SCS kiszolgáló.

### <a name="installation"></a>Telepítés

A következő elemek fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – csak érvényes csomópont 1 vagy **[2]** - csomópont 2 csak érvényes.

1. **[A]**  SUSE-összekötő telepítése
   
   <pre><code>
   sudo zypper install sap_suse_cluster_connector
   </code></pre>

1. **[A]**  Frissítés SAP erőforrás ügynökök  
   
   Az erőforrás-ügynökök csomag javítást kell használnia az új konfigurációt, ebben a cikkben ismertetett. Ellenőrizheti, ha a javítás már telepítve van a következő paranccsal

   <pre><code>
   sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   A kimeneti hasonlónak kell lennie.

   <pre><code>
   &lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Ha a grep parancs nem található a IS_ERS paraméter, szeretné-e meg a javítás [a SUSE letöltési oldala](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code>
   # example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]**  Állomásnév beállítása   

   DNS-kiszolgálót használjon, vagy módosítsa az/etc/hosts minden csomóponton. Ez a példa bemutatja, hogyan használható az/etc/hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnév

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Helyezze be a következő sorokat/etc/hosts. Az IP-cím és a környezet megfelelő állomásnév módosítása   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.11 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.12 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver telepítésének előkészítése

1. **[A]**  a megosztott könyvtárak létrehozása

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

   Indítsa újra az új fájlmegosztások csatlakoztatása autofs

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]**  Fájl FELCSERÉLÉSE konfigurálása

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


### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASC/SSZON telepítése

1. **[1]**  Hozzon létre egy virtuális IP- és állapot-mintavételi ASC-példány

   <pre><code>
   sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.11</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben, és, hogy az összes erőforrás indulnak el. Nem fontos, melyik csomópontján, az erőforrások futnak.

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

1. **[1]**  SAP NetWeaver ASC telepítése  

   SAP NetWeaver ASC telepítse a legfelső szintű használatával egy virtuális állomásnevet, amely a terheléselosztó előtér-konfiguráció a ASC IP-címe például az első csomóponton <b>nw1-ASC</b>, <b>10.0.0.11</b> és a példány számát, amelyet például használ a load balancer mintavétel <b>00</b>.

   Használhatja a sapinst paraméter SAPINST_REMOTE_ACCESS_USER sapinst való kapcsolódáshoz nem legfelső szintű felhasználó engedélyezése.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Ha a telepítés nem sikerül hozzon létre egy almappát a/usr/sap/**NW1**/ASCS**00**, próbálja meg a tulajdonos és az ASC csoportja**00** mappa, és próbálkozzon újra.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]**  Hozzon létre egy virtuális IP- és állapot-mintavételi SSZON-példány

   <pre><code>
   sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.12</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_NW1_ASCS,nc_NW1_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>
 
   Győződjön meg arról, hogy a fürt állapota rendben, és, hogy az összes erőforrás indulnak el. Nem fontos, melyik csomópontján, az erőforrások futnak.

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

1. **[2]**  SAP NetWeaver SSZON telepítése  

   SAP NetWeaver SSZON telepítése a második csomópont használatával egy virtuális állomásnevet, amely a terheléselosztó előtér-konfiguráció a SSZON IP-címe például a legfelső szintű <b>nw1-aers</b>, <b>10.0.0.12</b> és a példány számát, amelyet például használ a load balancer mintavétel <b>02</b>.

   Használhatja a sapinst paraméter SAPINST_REMOTE_ACCESS_USER sapinst való kapcsolódáshoz nem legfelső szintű felhasználó engedélyezése.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > SWPM SP 20 PL 05 vagy újabb verzióját használja. Alacsonyabb verzió nincs beállítva az engedélyeket, és a telepítés sikertelen lesz.

   Ha a telepítés nem sikerül hozzon létre egy almappát a/usr/sap/**NW1**/ERS**02**, próbálja meg a tulajdonos és a SSZON csoportja**02** mappa, és próbálkozzon újra.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>
   > 

1. **[1]**  Adaptálása a ASC/SCS és SSZON példány profilok
 
   * Asc/SCS profil

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


1. **[A]**  Keep-Alive konfigurálása

   Az SAP NetWeaver alkalmazáskiszolgáló és az ASC/SCS közötti kommunikáció áthalad szoftveres terheléselosztóként üzemeljen. A load balancer inaktív kapcsolatok leválasztása után konfigurálható időtúllépés. Ennek megelőzése szüksége egy paramétert az SAP NetWeaver ASC/SCS profil és a Linux rendszer beállításainak módosítására. Olvasási [SAP Megjegyzés 1410736] [ 1410736] további információt.
   
   A ASC/SCS profil paraméter célzó/encni/set_so_keepalive már felvették az előző lépésben.

   <pre><code> 
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  Az SAP felhasználók konfigurálásához, a telepítés után
 
   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm   
   </code></pre>

1. **[1]**  A ASC és SSZON SAP-szolgáltatás hozzáadása a sapservice fájlhoz

   Adja hozzá a ASC bejegyzés a második csomópontra történő szolgáltatást, és másolja a SSZON szolgáltatás bejegyzés az első csomópontot.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]**  Az SAP fürterőforrások létrehozása

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

   Győződjön meg arról, hogy a fürt állapota rendben, és, hogy az összes erőforrás indulnak el. Nem fontos, melyik csomópontján, az erőforrások futnak.

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

Néhány adatbázis szükséges, hogy az adatbázis-példány telepítése az alkalmazáskiszolgáló végrehajtása. Felkészülés az alkalmazás kiszolgáló virtuális gépek ezekben az esetekben használhatja őket.

A lépések alábbi azt feltételezik, hogy az alkalmazáskiszolgáló egy kiszolgálón telepíti a ASC/SCS és HANA-kiszolgálókról különböző. Ellenkező esetben egyes (például a gazdagép névfeloldásához konfigurálása) az alábbi lépéseket nem szükséges.

1. A telepítő állomásnév

   DNS-kiszolgálót használjon, vagy módosítsa az/etc/hosts minden csomóponton. Ez a példa bemutatja, hogyan használható az/etc/hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnév
   ```bash
   sudo vi /etc/hosts
   ```
   Helyezze be a következő sorokat/etc/hosts. Az IP-cím és a környezet megfelelő állomásnév módosítása    
    
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.11 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.12 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.8 nw1-di-0</b>
   <b>10.0.0.7 nw1-di-1</b>
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

   Indítsa újra az új fájlmegosztások csatlakoztatása autofs

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

## <a name="install-database"></a>A telepítési adatbázis

Ebben a példában a SAP NetWeaver az SAP HANA telepítve van. Minden támogatott adatbázis is használhatja a telepítéshez. Az Azure-ban SAP HANA telepítéséről további információkért lásd: [magas rendelkezésre állás az SAP HANA Azure virtuális gépek (VM)][sap-hana-ha]. A támogatott adatbázisok listájának megtekintéséhez lásd: [SAP Megjegyzés 1928533][1928533].

1. Futtassa az SAP-adatbázis-példány telepítése

   A SAP NetWeaver adatbázis-példány telepítését a legfelső szintű használatával egy virtuális állomásnevet, amely a terheléselosztó előtér-konfiguráció az adatbázis IP-címe például <b>nw1-db</b> és <b>10.0.0.13</b>.

   Használhatja a sapinst paraméter SAPINST_REMOTE_ACCESS_USER sapinst való kapcsolódáshoz nem legfelső szintű felhasználó engedélyezése.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver alkalmazáskiszolgáló telepítése

Kövesse az alábbi lépéseket az SAP-alkalmazáskiszolgáló telepítése. 

1. Alkalmazás-kiszolgáló előkészítése

Kövesse a fejezet a [SAP NetWeaver application server előkészítő](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) fenti a kiszolgáló előkészítéséhez.

1. SAP NetWeaver alkalmazáskiszolgáló telepítése

   Elsődleges vagy további SAP NetWeaver alkalmazások kiszolgáló telepítése.

   Használhatja a sapinst paraméter SAPINST_REMOTE_ACCESS_USER sapinst való kapcsolódáshoz nem legfelső szintű felhasználó engedélyezése.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Biztonságos tár SAP HANA frissítése

   Frissítse a SAP HANA biztonságos tár úgy, hogy a virtuális nevét a replikációs SAP HANA-telepítő mutasson.

   A következő parancsot a tételek felsorolása
   <pre><code>
   hdbuserstore List
   </code></pre>

   Ez az összes bejegyzés szerepelnie kell, és hasonlóan kell kinéznie
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: HN1
   </code></pre>

   A kimeneti jeleníti meg, hogy a virtuális gép nem pedig a terheléselosztó IP-cím mutat-e alapértelmezett bejegyzését IP-címét. Ez a bejegyzés kell módosítható úgy, hogy a terheléselosztó virtuális állomásnév mutasson. Ügyeljen arra, hogy ugyanazt a portot használja (**30313** a fenti kimenetben)!

   <pre><code>
   su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>További lépések
* [Az Azure virtuális gépek tervezési és megvalósítási az SAP][planning-guide]
* [Az SAP Azure virtuális gépek telepítése][deployment-guide]
* [Az SAP Azure virtuális gépek adatbázis-kezelő telepítése][dbms-guide]
* Magas rendelkezésre állás és az Azure (nagy példány) az SAP HANA vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagy példányok) magas rendelkezésre állási és vészhelyreállítási helyreállítási Azure](hana-overview-high-availability-disaster-recovery.md).
* Magas rendelkezésre állás és az Azure virtuális gépeken az SAP HANA vész-helyreállítási terv létrehozásához, lásd: [magas rendelkezésre állás az SAP HANA Azure virtuális gépek (VM)][sap-hana-ha]
