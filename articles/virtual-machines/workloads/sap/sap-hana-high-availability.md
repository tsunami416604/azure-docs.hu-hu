---
title: SAP HANA replikációs az Azure virtuális gépek (VM) beállítása |} Microsoft Docs
description: Magas rendelkezésre állású SAP HANA az Azure virtuális gépek (VM) létrehozásához.
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/24/2018
ms.author: sedusch
ms.openlocfilehash: e3fb06309dabd7f66d5873e4c5faa48b468854f6
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Magas rendelkezésre állású SAP HANA az Azure virtuális gépek (VM)

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
[2388694]:https://launchpad.support.sap.com/#/notes/2388694

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

A helyszíni vagy HANA replikációs használhatja, vagy használja a megosztott tárolót SAP HANA a magas rendelkezésre állás érdekében.
Az egyetlen támogatott magas rendelkezésre állású függvény, amennyiben az Azure virtuális gépek HANA replikációs az Azure-on. SAP HANA-replikáció egy elsődleges csomópontot és legalább egy másodlagos csomópont áll. Az elsődleges csomóponton lévő adatok módosításait replikálva vannak a másodlagos csomópont szinkron vagy aszinkron módon.

A cikkből megtudhatja, hogyan telepítse a virtuális gépeket, a virtuális gépet állíthat be, a fürt-keretrendszer telepítése, telepítése és konfigurálása a SAP HANA replikációs.
A példa konfigurációkban telepítési parancsok 03 stb. száma, és HANA rendszer azonosító HN1 használatos.

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
* [SAP HANA SR teljesítmény optimalizált forgatókönyv] [ suse-hana-ha-guide] az útmutató helyszíni SAP HANA replikációs beállítása az összes szükséges információkat tartalmazza. Ez az útmutató használja kiindulópontként.

## <a name="overview"></a>Áttekintés

Magas rendelkezésre állás eléréséhez SAP HANA két virtuális gép telepítve van. Az adatok HANA rendszer replikáció segítségével replikálódnak.

![SAP HANA magas rendelkezésre állás – Áttekintés](./media/sap-hana-high-availability/ha-suse-hana.png)

A SAP HANA SR telepítő használatát egy dedikált virtuális állomásnév és a virtuális IP-címét. Az Azure a terheléselosztó virtuális IP-cím szükséges. Az alábbi lista a terheléselosztó-konfiguráció látható.

* Előtérbeli konfigurációja
  * IP-címet 10.0.0.13 hn1-adatbázis
* Háttérkonfiguráció
  * Az összes virtuális gépet, amely HANA replikációs részét kell képezniük elsődleges hálózati illesztők csatlakozik
* Mintavételi portot
  * Port 62503
* Terheléselosztás szabályok
  * 30313 TCP
  * 30315 TCP
  * 30317 TCP

## <a name="deploying-linux"></a>Linux telepítése

Az erőforrás-ügynök SAP Hana SUSE Linux Enterprise Server szerepel az SAP-alkalmazásokból.
Az Azure piactéren SUSE Linux Enterprise Server SAP alkalmazások 12-es segítségével új virtuális gépek telepítése a kép tartalmazza.

### <a name="deploy-with-template"></a>Üzembe helyezés sablon használatával
Segítségével a gyorsindítási sablonok egyikét a githubon központi telepítése az összes szükséges erőforrásokat. A sablon telepíti, a virtuális gépek, a terheléselosztó hasonló adataival, a rendelkezésre állási csoport stb. A sablon telepítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [adatbázis sablon] [ template-multisid-db] vagy a [sablon összevont] [ template-converged] az Azure portálon. 
   Az adatbázis-sablon csak a terheléselosztási szabályok adatbázis hoz létre, mivel az összevont sablont is létrehoz a terheléselosztási szabályok ASC/SCS és SSZON (csak Linux) példány. Ha azt tervezi, hogy az SAP NetWeaver alapú rendszert telepíti, és szeretné telepíteni az ASC/SCS-példányt az azonos gépeken, használja a [sablon összevont][template-converged].
1. Adja meg a következő paraméterek
    1. SAP azonosító  
       Adja meg a telepíteni kívánt SAP rendszer SAP rendszer Azonosítóját. Az azonosító az üzembe helyezett erőforrások előtagjaként használható lesz.
    1. A készlet típusa (csak akkor érvényes, az összevont sablon)   
       Az SAP NetWeaver verem típusának kiválasztása
    1. Operációs rendszer típusa  
       Válasszon egyet a Linux terjesztéseket. Ehhez a példához válassza ki a SLES 12 rendszert
    1. DB típusa  
       Válassza ki a HANA
    1. SAP mérete  
       Adja meg az új rendszer érintetlen SAP mennyisége. Ha nem tudja, hogy hány SAP, a rendszer szükséges, kérje meg, a SAP technológia Partner vagy a rendszer integráló
    1. Rendszer rendelkezésre állás  
       Válassza ki a magas rendelkezésre ÁLLÁSÚ
    1. Rendszergazda felhasználónevét és a rendszergazdai jelszó  
       Új felhasználó jön létre, amely segítségével jelentkezzen be a gépre.
    1. Új vagy meglévő alhálózati  
       Meghatározza, hogy egy új virtuális hálózat és alhálózat kell létrehozni, vagy használjon egy létező alhálózatot. Ha már van egy virtuális hálózatot, amely a helyszíni hálózathoz csatlakozik, válasszon a meglévő.
    1. Alhálózati azonosító  
    Az alhálózat, amelyhez a virtuális gépek csatlakoznia kell az azonosítója. A virtuális gép a helyszíni hálózathoz csatlakozni, válassza ki a VPN- vagy Express Route virtuális hálózat alhálózatában. Az azonosító általában a következőképpen néz következő`<subscription ID`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

### <a name="manual-deployment"></a>Manuális telepítése

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Egy rendelkezésre állási csoport létrehozása  
   Készlet maximális frissítési tartomány
1. Hozzon létre egy terhelés-kiegyenlítő (belső)  
   Válassza ki a virtuális hálózat létrehozása a második
1. 1 virtuális gép létrehozása  
   Legalább SLES4SAP 12 SP1, ebben a példában használjuk a SLES4SAP 12 SP2 kép https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   A SLES 12 SP2 SAP (prémium)  
   Válassza ki a rendelkezésre állási csoport korábban létrehozott  
1. 2. virtuális gép létrehozása  
   Legalább SLES4SAP 12 SP1, ebben a példában használjuk a SLES4SAP 12 SP1 saját kép https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   A SLES 12 SP2 SAP (prémium)  
   Válassza ki a rendelkezésre állási csoport korábban létrehozott  
1. Adatlemez hozzáadása
1. A load balancer konfigurálása
    1. Előtérbeli IP-készlet létrehozása
        1. Nyissa meg a terheléselosztó előtérbeli IP-készlet kiválasztása és kattintson a Hozzáadás gombra
        1. Adja meg a nevét, az új előtérbeli IP-címtartomány (például hana-előtér)
        1. A hozzárendelés értékűre statikus, majd adja meg az IP-cím (például **10.0.0.13**)
        1. Kattintson az OK gombra
        1. Az új előtérbeli IP-címkészlet létrehozása után jegyezze fel az IP-címe
    1. Háttér-készlet létrehozása
        1. Nyissa meg a terheléselosztóhoz, válassza ki a háttérkészlet, kattintson a Hozzáadás gombra
        1. Adja meg az új háttérkészlet (például hana-háttérrendszer)
        1. Kattintson a Hozzáadás gombra a virtuális gépek
        1. Válassza ki a korábban létrehozott rendelkezésre állási csoport
        1. Válassza ki a virtuális gépeket, a SAP HANA-fürt
        1. Kattintson az OK gombra
    1. Állapotminta létrehozása
        1. Nyissa meg a terheléselosztóhoz, válassza ki a állapotteljesítmény, kattintson a Hozzáadás gombra
        1. Adja meg az új állapotmintáihoz (például hana-hp)
        1. Válassza ki a TCP protokoll, port 625**03**, időköz 5 és sérült küszöbérték 2
        1. Kattintson az OK gombra
    1. SAP HANA 1.0: Terheléselosztási szabályok létrehozása
        1. Nyissa meg a terheléselosztóhoz, válassza ki a terheléselosztási szabályok, kattintson a Hozzáadás gombra
        1. Adja meg az új terheléselosztási szabály nevét (például hana-lb-3**03**15)
        1. Válassza ki a front-end IP-címet, a háttérkészlet és a állapotmintáihoz létrehozott korábbi (a példa hana-előtér)
        1. Tartsa a TCP protokoll, adja meg azt a portot 3**03**15
        1. Növelje a 30 perc üresjárati időtúllépés
        1. **Ügyeljen arra, hogy a fix IP-Címek engedélyezése**
        1. Kattintson az OK gombra
        1. Ismételje meg a fenti port 3**03**17
    1. SAP HANA 2.0: Terheléselosztási szabályok-adatbázisának létrehozása
        1. Nyissa meg a terheléselosztóhoz, válassza ki a terheléselosztási szabályok, kattintson a Hozzáadás gombra
        1. Adja meg az új terheléselosztási szabály nevét (például hana-lb-3**03**13)
        1. Válassza ki a front-end IP-címet, a háttérkészlet és a állapotmintáihoz létrehozott korábbi (a példa hana-előtér)
        1. Tartsa a TCP protokoll, adja meg azt a portot 3**03**13
        1. Növelje a 30 perc üresjárati időtúllépés
        1. **Ügyeljen arra, hogy a fix IP-Címek engedélyezése**
        1. Kattintson az OK gombra
        1. Ismételje meg a fenti port 3**03**14
    1. SAP HANA 2.0: Terheléselosztási szabályok első a bérlő adatbázis létrehozása
        1. Nyissa meg a terheléselosztóhoz, válassza ki a terheléselosztási szabályok, kattintson a Hozzáadás gombra
        1. Adja meg az új terheléselosztási szabály nevét (például hana-lb-3**03**40)
        1. Válassza ki a front-end IP-címet, a háttérkészlet és a állapotmintáihoz létrehozott korábbi (a példa hana-előtér)
        1. Tartsa a TCP protokoll, adja meg azt a portot 3**03**40
        1. Növelje a 30 perc üresjárati időtúllépés
        1. **Ügyeljen arra, hogy a fix IP-Címek engedélyezése**
        1. Kattintson az OK gombra
        1. Ismételje meg a fenti port 3**03**41-es és 3**03**42

SAP Hana a szükséges portok kapcsolatos további információkért olvassa el a fejezet [bérlői adatbázis-kapcsolatok](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) , a [SAP HANA-bérlő adatbázisok](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) útmutató vagy [SAP Megjegyzés 2388694] [2388694].


## <a name="create-pacemaker-cluster"></a>Támasztja fürt létrehozása

Kövesse a [támasztja a SUSE Linux Enterprise Server az Azure-ban beállítása](high-availability-guide-suse-pacemaker.md) HANA kiszolgáló alapszintű támasztja fürt létrehozásához. Az azonos támasztja fürthöz használható SAP HANA és az SAP NetWeaver (A) SCS.

## <a name="installing-sap-hana"></a>SAP HANA telepítése

A következő elemek fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – csak érvényes csomópont 1 vagy **[2]** – csak érvényes támasztja fürt csomópontján, 2.

1. **[A]**  Telepítő lemez elrendezése
    1. LVM  
       
       Általában javasoljuk LVM kötetek, amelyek adatokat tárolhatnak, és a naplófájlok. Az alábbi példák azt feltételezi, hogy, hogy a virtuális gépek rendelkeznek négy adatlemezt csatolni, amelynek használatával hozzon létre két köteteket.

       Minden elérhető lemez felsorolása
       
       <pre><code>
       ls /dev/disk/azure/scsi1/lun*
       </code></pre>
       
       Példa a kimenetre
       
       ```
       /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
       ```
       
       A használni kívánt összes lemez fizikai köteteket hozhat létre.    
       <pre><code>
       sudo pvcreate /dev/disk/azure/scsi1/lun0
       sudo pvcreate /dev/disk/azure/scsi1/lun1
       sudo pvcreate /dev/disk/azure/scsi1/lun2
       sudo pvcreate /dev/disk/azure/scsi1/lun3
       </code></pre>

       Az adatfájlok kötet csoport, a naplófájlok egy kötet csoport és egy SAP HANA a megosztott könyvtár létrehozása

       <pre><code>
       sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
       sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
       sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
       </code></pre>
       
       A logikai köteteket hozhat létre

       <pre><code>
       sudo lvcreate -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
       sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
       sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
       sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       </code></pre>
       
       A csatlakoztatási könyvtárak létrehozása, és másolja az összes logikai kötet UUID
       
       <pre><code>
       sudo mkdir -p /hana/data/<b>HN1</b>
       sudo mkdir -p /hana/log/<b>HN1</b>
       sudo mkdir -p /hana/shared/<b>HN1</b>
       # write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       sudo blkid
       </code></pre>
       
       A három logikai kötetek között az fstab bejegyzéseket létrehozni
       
       <pre><code>
       sudo vi /etc/fstab
       </code></pre>
       
       Ez a /etc/fstab sor beszúrása
       
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
       </code></pre>
       
       Csatlakoztassa az új köteteket
       
       <pre><code>
       sudo mount -a
       </code></pre>
    
    1. Egyszerű lemez  
       Bemutató rendszerekhez elhelyezhet egy lemezt a HANA adatainak és naplókönyvtárainak fájlokat. A következő parancsok /dev/disk/azure/scsi1/lun0 hozza létre a partíciót, és formázza xfs.

       <pre><code>
       sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
       sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
       
       # write down the ID of /dev/disk/azure/scsi1/lun0-part1
       sudo /sbin/blkid
       sudo vi /etc/fstab
       </code></pre>

       Ez a /etc/fstab sor beszúrása
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
       </code></pre>

       A célkönyvtár létrehozása, és csatlakoztassa a lemezt.

       <pre><code>
       sudo mkdir /hana
       sudo mount -a
       </code></pre>

1. **[A]**  Állomásnév telepítési állomások  
    DNS-kiszolgálót használjon, vagy módosítsa az/etc/hosts minden csomóponton. Ez a példa bemutatja, hogyan használható az/etc/hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnév
    ```bash
    sudo vi /etc/hosts
    ```
    Helyezze be a következő sorokat/etc/hosts. Az IP-cím és a környezet megfelelő állomásnév módosítása    
    
   <pre><code>
   <b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]**  Telepítése HANA magas rendelkezésre ÁLLÁSÚ csomagok  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

SAP HANA replikációs telepítéséhez kövesse a fejezet 4 az SAP HANA SR teljesítmény optimalizált forgatókönyv Útmutató: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/

1. **[A]**  Hdblcm HANA DVD-ről futtatni
    * Válassza ki a telepítés-1 >
    * Válassza ki a további összetevők telepítésének 1->
    * Adja meg a telepítési útvonalat [/ hana/megosztott]: -> adjon meg
    * Adja meg a helyi állomás nevét [.]: -> adjon meg
    * Végrehajtja a rendszer további gazdagépeket vehet fel? (i/n) [n]: -> adjon meg
    * Adja meg az SAP HANA-azonosító: <SID of HANA e.g. HN1>
    * Adja meg a [00] száma:   
  HANA példányszámának. 03 használja, ha használja az Azure-sablon vagy követni a manuális központi telepítése
    * Válassza ki az adatbázis-mód / Index [1] adja meg: -> adjon meg
    * Válassza ki a rendszer használati / adja meg az Index [4]:  
  A rendszer használati kiválasztása
    * Adja meg a helyet, az adatkötetek [/ hana/data/HN1]: -> adjon meg
    * Adja meg a naplózási kötetek [/ hana/napló/HN1] helyet: ENTER ->
    * Maximális memória kiosztása korlátozása? [n]: -> ENTER
    * Adja meg a tanúsítvány gazdagép neve a gazdagép "..." []: -> Adjon meg
    * Adja meg a SAP ügynök felhasználói (sapadm) jelszavát:
    * SAP ügynök felhasználói (sapadm) jelszó megerősítése:
    * Adja meg a rendszergazda (hdbadm) jelszavát:
    * Erősítse meg a rendszergazda (hdbadm) jelszavát:
    * Adja meg a rendszer rendszergazdai kezdőkönyvtár [/ usr/sap/HN1/home]: -> adjon meg
    * Adja meg a rendszer rendszergazdai bejelentkezési rendszerhéj [/ bin/sh]: -> adjon meg
    * Adja meg a rendszergazda felhasználói Azonosítóját [1001]: -> adjon meg
    * Adjon meg azonosító a felhasználói csoport (sapsys) [79]: -> adjon meg
    * Adja meg az adatbázis jelszó (rendszer):
    * Adatbázis (rendszer) felhasználói jelszó megerősítése:
    * Számítógép újraindítása után indítsa újra a rendszert? [n]: -> ENTER
    * Folytatja? (y/n):   
  Az összegzés, és írja be az y gombbal folytathatja

1. **[A]**  SAP állomás ügynökök frissítése  
  Töltse le a legfrissebb SAP a gazdagép ügynöke archív a a [SAP Softwarecenter] [ sap-swcenter] és az ügynökök frissítése a következő parancsot. Cserélje le az archívum mutasson a letöltött fájl elérési útja.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2.0-s replikációs konfigurálása

A következő elemek fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – csak érvényes csomópont 1 vagy **[2]** – csak érvényes támasztja fürt csomópontján, 2.

1. **[1]**  Bérlői adatbázis létrehozása

   Ha SAP HANA 2.0-s vagy MDC használ, a SAP NetWeaver rendszer bérlői adatbázis létrehozása. Cserélje le a biztonsági AZONOSÍTÓT az SAP rendszer NW1.

   Jelentkezzen be `<hanasid`> adm és hajtsa végre a következő parancsot

   <pre><code>
   hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Első csomóponton replikációs konfigurálása
   
   Jelentkezzen be `<hanasid`> adm és az adatbázisok biztonsági mentése

   <pre><code>
   hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Másolja át a nyilvános kulcsokra épülő infrastruktúra rendszerfájlok másodlagos

   <pre><code>
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Hozza létre az elsődleges helyet.

   <pre><code>
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Replikációs konfigurálja a második csomópont
    
    A második csomópontot, a rendszer replikálását indító regisztrálni. Jelentkezzen be `<hanasid`> adm, és futtassa a következő parancsot

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1.0-s replikációs konfigurálása

1. **[1]**  a szükséges felhasználók létrehozása

    Jelentkezzen be rendszergazdaként, és futtassa a következő parancsot. Ügyeljen arra, hogy félkövér karakterláncok (HANA rendszer azonosító HN1 és példány újrahasznosítása 03) cserélje le az értékeket a SAP HANA-telepítés.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. **[A]**  Keystore bejegyzés létrehozása
   
    Jelentkezzen be rendszergazdaként, és futtassa a következő parancsot, hogy hozzon létre egy új keystore.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>

1. **[1]**  Adatbázis biztonsági másolata

   Jelentkezzen be rendszergazdaként, és az adatbázisok biztonsági mentése

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Ha egy több-bérlős telepítést használ, is biztonsági másolatot a bérlő adatbázisról

   <pre><code>   
   hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Első csomóponton replikációs konfigurálása
    
    Jelentkezzen be `<hanasid`> adm és hozza létre az elsődleges hely.

    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>

1. **[2]**  Replikációs konfigurálja a másodlagos csomópont.

    Jelentkezzen be `<hanasid`> adm, és regisztrálja a másodlagos helyet.

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA-fürterőforrás létrehozása

   Először hozza létre a HANA-topológiát. A következő parancsokat egy támasztja csomópont.
   
   <pre><code>
   sudo crm configure property maintenance-mode=true

   # replace the bold string with your instance number and HANA system ID
   
   sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
     operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"
   
   sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
   </code></pre>
   
   Ezután hozzon létre a HANA erőforrásokat.
   
   <pre><code>
   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
      
   sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
   
   sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
   
   sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
     meta target-role="Started" is-managed="true" \
     operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10s" timeout="20s" \
     params ip="<b>10.0.0.13</b>"
   
   sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>
   
   sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 2000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  
   
   sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
   
   # Cleanup the HANA resources. The HANA resources might have failed because of a known issue.
   sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

   sudo crm configure property maintenance-mode=false
   </code></pre>

   Győződjön meg arról, hogy a fürt állapota rendben, és, hogy az összes erőforrás indulnak el. Nem fontos, melyik csomópontján, az erőforrások futnak.

   <pre><code>
   sudo crm_mon -r
   
   # Online: [ hn1-db-0 hn1-db-1 ]
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): Started hn1-db-0
   # rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
   # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
   #     Started: [ hn1-db-0 hn1-db-1 ]
   # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
   #     Masters: [ hn1-db-0 ]
   #     Slaves: [ hn1-db-1 ]
   # Resource Group: g_ip_HN1_HDB03
   #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
   #     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

### <a name="test-cluster-setup"></a>Teszt fürt beállítása
Ez a fejezet ismerteti, hogyan tesztelheti a telepítőt. Minden teszt feltételezi, hogy a legfelső szintű áll, és az SAP HANA-főkiszolgáló fut a virtuális gép hn1-db-0.

#### <a name="fencing-test"></a>Teszt kerítésépítés

A telepítő a kerítés ügynök hn1-db-0 csomópont a hálózati adapter letiltásával tesztelheti.

<pre><code>
sudo ifdown eth0
</code></pre>

A virtuális gép kell most beolvasása újraindult, vagy attól függően, hogy a fürt konfigurációját, leállt.
Ha a stonith-művelet kikapcsolva, le kell állítani a virtuális gép lesz, és az erőforrások települnek a futó virtuális gépre.

Után újra elindítani a virtuális gépet, a SAP HANA-erőforrás nem sikerül elindítani a másodlagos Ha AUTOMATED_REGISTER = "false". Ebben az esetben HANA-példány beállítása másodlagos, ez a parancs végrehajtásával:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Manuális feladatátvétel tesztelése

Tesztelheti egy kézi feladatátvételre hn1-db-0 csomópont a támasztja szolgáltatás leállításával.
<pre><code>
service pacemaker stop
</code></pre>

A feladatátvétel után újra elindíthatja a szolgáltatást. Ha AUTOMATED_REGISTER = "false", az az SAP HANA-erőforrás hn1-db-0 sikertelen lesz, mint a másodlagos elindításához. Ebben az esetben HANA-példány beállítása másodlagos, ez a parancs végrehajtásával:

<pre><code>
service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-migration"></a>Áttelepítés tesztelése

Az SAP HANA-főcsomópont áttelepítése a következő parancs végrehajtása
<pre><code>
crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
crm resource migrate g_ip_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Ha AUTOMATED_REGISTER = "false", ez a parancssorozat át kell telepíteni, a fő SAP HANA-csomópont és a csoport, amely tartalmazza a virtuális IP-címet hn1-db-1.
Az SAP HANA-erőforrás hn1-db-0 nem, mint a másodlagos indul el. Ebben az esetben HANA-példány beállítása másodlagos, ez a parancs végrehajtásával:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

Az áttelepítés hoz létre a helyre vonatkozó megszorítások, hogy újra kell törölni.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HN1</b>_HDB<b>03</b> g_ip_<b>HN1</b>_HDB<b>03</b> role=Started inf: <b>hn1-db-1</b>
</code></pre>

Is kell a másodlagos csomópont erőforrás állapotának karbantartása

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

## <a name="next-steps"></a>További lépések
* [Az Azure virtuális gépek tervezési és megvalósítási az SAP][planning-guide]
* [Az SAP Azure virtuális gépek telepítése][deployment-guide]
* [Az SAP Azure virtuális gépek adatbázis-kezelő telepítése][dbms-guide]
* Magas rendelkezésre állás és az Azure (nagy példány) az SAP HANA vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagy példányok) magas rendelkezésre állási és vészhelyreállítási helyreállítási Azure](hana-overview-high-availability-disaster-recovery.md). 
