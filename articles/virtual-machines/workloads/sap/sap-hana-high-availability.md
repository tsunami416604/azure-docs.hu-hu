---
title: "Az Azure virtuális gépek (VM) SAP HANA magas rendelkezésre állású |} Microsoft Docs"
description: "Magas rendelkezésre állású SAP HANA az Azure virtuális gépek (VM) létrehozásához."
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: sedusch
ms.openlocfilehash: 5f6ef18e93b8f77162b3524f31cb632e1db38f80
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
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

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

A helyszíni vagy HANA replikációs használhatja, vagy használja a megosztott tárolót SAP HANA a magas rendelkezésre állás érdekében.
Sajnos jelenleg csak a támogatott HANA replikációs beállítása az Azure-on. SAP HANA-replikáció egy főcsomópont és legalább egy alárendelt csomópont áll. A fő csomóponton levő adatok módosításait replikálva vannak a alárendelt csomópontok szinkron vagy aszinkron módon.

A cikkből megtudhatja, hogyan telepítse a virtuális gépeket, a virtuális gépet állíthat be, a fürt-keretrendszer telepítése, telepítése és konfigurálása a SAP HANA replikációs.
A példa konfigurációkban telepítési parancsok 03 stb. száma, és HANA rendszer azonosító HDB használatos.

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

## <a name="deploying-linux"></a>Linux telepítése

Az erőforrás-ügynök SAP Hana SUSE Linux Enterprise Server szerepel az SAP-alkalmazásokból.
Az Azure piactéren SUSE Linux Enterprise Server SAP alkalmazások 12 a saját (Bring Your saját előfizetés), amely segítségével új virtuális gépek telepítése a kép tartalmazza.

### <a name="manual-deployment"></a>Manuális telepítése

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Két Storage-fiókok létrehozása
1. Egy rendelkezésre állási csoport létrehozása  
   Készlet maximális frissítési tartomány
1. Hozzon létre egy terhelés-kiegyenlítő (belső)  
   Válassza ki a fenti lépést a virtuális hálózat
1. 1 virtuális gép létrehozása  
   Legalább SLES4SAP 12 SP1, ebben a példában használjuk a SLES4SAP 12 SP1 saját kép https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES SAP alkalmazások 12 SP1 (saját)  
   Válassza ki a Tárfiók 1  
   A rendelkezésre állási csoport kiválasztása  
1. 2. virtuális gép létrehozása  
   Legalább SLES4SAP 12 SP1, ebben a példában használjuk a SLES4SAP 12 SP1 saját kép https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES SAP alkalmazások 12 SP1 (saját)  
   Válassza ki a Tárfiók 2   
   A rendelkezésre állási csoport kiválasztása  
1. Adatlemez hozzáadása
1. A load balancer konfigurálása
    1. Előtérbeli IP-készlet létrehozása
        1. Nyissa meg a terheléselosztó előtérbeli IP-készlet között, kattintson a Hozzáadás gombra
        1. Adja meg a nevét, az új előtérbeli IP-címtartomány (például hana-előtér)
        1. Kattintson az OK gombra
        1. Az új előtérbeli IP-címkészlet létrehozása után jegyezze fel az IP-címe
    1. Háttér-készlet létrehozása
        1. Nyissa meg a terheléselosztóhoz, válassza ki a háttérkészlet, kattintson a Hozzáadás gombra
        1. Adja meg az új háttérkészlet (például hana-háttérrendszer)
        1. Kattintson a Hozzáadás gombra a virtuális gépek
        1. Válassza ki a korábban létrehozott rendelkezésre állási csoport
        1. Válassza ki a virtuális gépeket, a SAP HANA-fürt
        1. Kattintson az OK gombra
    1. Hozzon létre egy állapotmintáihoz
        1. Nyissa meg a terheléselosztóhoz, válassza ki a állapotteljesítmény, kattintson a Hozzáadás gombra
        1. Adja meg az új állapotmintáihoz (például hana-hp)
        1. Válassza ki a TCP protokoll, port 625**03**, időköz 5 és sérült küszöbérték 2
        1. Kattintson az OK gombra
    1. Terheléselosztási szabályok létrehozása
        1. Nyissa meg a terheléselosztóhoz, válassza ki a terheléselosztási szabályok, kattintson a Hozzáadás gombra
        1. Adja meg az új terheléselosztási szabály nevét (például hana-lb-3**03**15)
        1. Válassza ki az előtérbeli IP-cím, a háttérkészlet és állapotfigyelő mintavételi, korábban létrehozott (például hana-előtér)
        1. Tartsa a TCP protokoll, adja meg azt a portot 3**03**15
        1. Növelje a 30 perc üresjárati időtúllépés
        1. **Ügyeljen arra, hogy a fix IP-Címek engedélyezése**
        1. Kattintson az OK gombra
        1. Ismételje meg a fenti port 3**03**17

### <a name="deploy-with-template"></a>Üzembe helyezés sablon használatával
Segítségével a gyorsindítási sablonok egyikét a githubon központi telepítése az összes szükséges erőforrásokat. A sablon telepíti, a virtuális gépek, a terheléselosztó hasonló adataival, a rendelkezésre állási csoport stb. Kövesse az alábbi lépéseket a sablon telepítéséhez:

1. Nyissa meg a [adatbázis sablon] [ template-multisid-db] vagy a [sablon összevont] [ template-converged] az Azure portál, az adatbázis-sablon csak hoz létre a terheléselosztási szabályok-adatbázis, mivel átszervezett is létrejön a terheléselosztási szabályok ASC/SCS és SSZON (csak Linux) példány. Ha azt tervezi, hogy az SAP NetWeaver alapú rendszert telepíti, és szeretné telepíteni az ASC/SCS-példányt az azonos gépeken, használja a [sablon összevont][template-converged].
1. Adja meg a következő paraméterek
    1. SAP azonosító  
       Adja meg a telepíteni kívánt SAP rendszer SAP rendszer Azonosítóját. Az azonosító az üzembe helyezett erőforrások előtagjaként lesz használható.
    1. A készlet típusa (csak akkor érvényes, az összevont sablon)  
       Az SAP NetWeaver verem típusának kiválasztása
    1. Operációs rendszer típusa  
       Válasszon egyet a Linux terjesztéseket. Ehhez a példához válassza ki a SLES 12 saját
    1. DB típusa  
       Válassza ki a HANA
    1. SAP mérete  
       Az új rendszer nyújtják SAP mennyisége. Ha nem tudja, a rendszer hány SAP, kérje meg a SAP technológia Partner vagy a rendszer integráló
    1. Rendszer rendelkezésre állás  
       Válassza ki a magas rendelkezésre ÁLLÁSÚ
    1. Rendszergazda felhasználónevét és a rendszergazdai jelszó  
       Új felhasználó jön létre, amely segítségével jelentkezzen be a gépre.
    1. Új vagy meglévő alhálózati  
       Meghatározza, hogy egy új virtuális hálózat és alhálózat kell létrehozni, vagy használjon egy létező alhálózatot. Ha már van egy virtuális hálózatot, amely a helyszíni hálózathoz csatlakozik, válasszon a meglévő.
    1. Alhálózati azonosító  
    Az alhálózat, amelyhez a virtuális gépek csatlakoznia kell az azonosítója. Jelölje ki az alhálózatot, a VPN- vagy Express Route virtuális hálózat a virtuális gép és a helyszíni hálózathoz csatlakozni. Az azonosító általában a következőképpen néz következő`<subscription ID`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

## <a name="setting-up-linux-ha"></a>Beállítása Linux magas rendelkezésre ÁLLÁSÚ

A következő elemek fűzve előtagként vagy [A] - alkalmazható az összes csomóponton [1] - 1 vagy [2] - csomópont 2 csak érvényes csomópont csak érvényes.

1. [A] az SAP - csak a saját használhatják a tárolóhelyekkel való regisztrálásához SLES SLES
1. [A] SLES az SAP saját csak - nyilvános felhő modul hozzá lesz adva
1. [A] SLES frissítése
    ```bash
    sudo zypper update

    ```

1. [1] ssh hozzáférés engedélyezése
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] ssh hozzáférés engedélyezése
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] ssh hozzáférés engedélyezése
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] magas rendelkezésre ÁLLÁSÚ-kiterjesztés telepítése
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] telepítő lemez elrendezése
    1. LVM  
    Általában javasoljuk LVM kötetek, amelyek adatokat tárolhatnak, és a naplófájlok. Az alábbi példa azt feltételezi, hogy, hogy a virtuális gépek rendelkeznek négy adatlemezt csatolni, amelynek használatával hozzon létre két köteteket.
        * A használni kívánt összes lemez fizikai köteteket hozhat létre.
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * Az adatfájlok kötet csoport, a naplófájlok egy kötet csoport és egy SAP HANA a megosztott könyvtár létrehozása
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * A logikai köteteket hozhat létre
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * A csatlakoztatási könyvtárak létrehozása, és másolja az összes logikai kötet UUID
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the ID of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * A három logikai kötetek között az fstab bejegyzéseket létrehozni
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    Ez a /etc/fstab sor beszúrása
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * Csatlakoztassa az új köteteket
    <pre><code>
    sudo mount -a
    </code></pre>
    1. Egyszerű lemez  
       A kis vagy bemutató rendszerek, elhelyezhet egy lemezt a HANA adatainak és naplókönyvtárainak fájlokat. A következő parancsok /dev/sdc hozza létre a partíciót, és formázza xfs.
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>Jegyezze fel a /dev/sdc1 azonosítója
    sudo/sbin/blkid sudo vi/etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] állomásnév telepítési állomások  
    DNS-kiszolgálót használjon, vagy módosítsa az/etc/hosts minden csomóponton. Ez a példa bemutatja, hogyan használható az/etc/hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnév
    ```bash
    sudo vi /etc/hosts
    ```
    Helyezze be a következő sorokat/etc/hosts. Az IP-cím és a környezet megfelelő állomásnév módosítása    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] fürt telepítése
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] csomópont hozzáadása fürthöz
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] módosítása hacluster jelszó ugyanazt a jelszót
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] egyéb átvitelt használ, és adja hozzá a csomópontlista corosync konfigurálása. Fürt egyébként nem működik.
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    Vegye fel a következő félkövér tartalmat a fájlba.
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of node 1 >
      }
      node {
        ring0_addr:     < ip address of node 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    Indítsa újra a corosync szolgáltatás

    ```bash
    sudo service corosync restart
    
    ```

1. [A] HANA magas rendelkezésre ÁLLÁSÚ csomagok telepítése  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>SAP HANA telepítése

4 fejezete kövesse a [SAP HANA SR teljesítmény optimalizált forgatókönyv útmutató] [ suse-hana-ha-guide] SAP HANA rendszer replikáció telepítését.

1. [A] hdblcm futtassa a HANA DVD-ről
    * Válassza ki a telepítés-1 >
    * Válassza ki a további összetevők telepítésének 1->
    * Adja meg a telepítési útvonalat [/ hana/megosztott]: -> adjon meg
    * Adja meg a helyi állomás nevét [.]: -> adjon meg
    * Végrehajtja a rendszer további gazdagépeket vehet fel? (i/n) [n]: -> adjon meg
    * Adja meg az SAP HANA-azonosító:<SID of HANA e.g. HDB>
    * Adja meg a [00] száma:   
  HANA példányszámának. 03 használja, ha követte a fenti példa vagy használja az Azure-sablon
    * Válassza ki az adatbázis-mód / Index [1] adja meg: -> adjon meg
    * Válassza ki a rendszer használati / adja meg az Index [4]:  
  A rendszer használati kiválasztása
    * Adja meg a helyet, az adatkötetek [/ hana/data/HDB]: -> adjon meg
    * Adja meg a naplózási kötetek [/ hana/napló/HDB] helyet: ENTER ->
    * Maximális memória kiosztása korlátozása? [n]: -> adjon meg
    * Adja meg a tanúsítvány gazdagép neve a gazdagép "..." []: -> Adjon meg
    * Adja meg a SAP ügynök felhasználói (sapadm) jelszavát:
    * SAP ügynök felhasználói (sapadm) jelszó megerősítése:
    * Adja meg a rendszergazda (hdbadm) jelszavát:
    * Erősítse meg a rendszergazda (hdbadm) jelszavát:
    * Adja meg a rendszer rendszergazdai kezdőkönyvtár [/ usr/sap/HDB/home]: -> adjon meg
    * Adja meg a rendszer rendszergazdai bejelentkezési rendszerhéj [/ bin/sh]: -> adjon meg
    * Adja meg a rendszergazda felhasználói Azonosítóját [1001]: -> adjon meg
    * Adjon meg azonosító a felhasználói csoport (sapsys) [79]: -> adjon meg
    * Adja meg az adatbázis jelszó (rendszer):
    * Adatbázis (rendszer) felhasználói jelszó megerősítése:
    * Számítógép újraindítása után indítsa újra a rendszert? [n]: -> adjon meg
    * Folytatja? (i/n):  
  Az összegzés, és írja be az y gombbal folytathatja
1. [A] frissítési SAP Gazdagépügynöke  
  Töltse le a legfrissebb SAP a gazdagép ügynöke archív a a [SAP Softwarecenter] [ sap-swcenter] és az ügynökök frissítése a következő parancsot. Cserélje le az archívum mutasson a letöltött fájl elérési útja.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] replikációs HANA létrehozása (a legfelső szintű)  
    A következő parancsot. Ügyeljen arra, hogy félkövér karakterláncok (HANA rendszer azonosító HDB és példány újrahasznosítása 03) cserélje le az értékeket a SAP HANA-telepítés.
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] létrehozása keystore bejegyzés (root)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] backup database (a legfelső szintű)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] váltson a sapsid felhasználó (pl. hdbadm), és az elsődleges hely létrehozásához.
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] váltson a sapsid felhasználó (pl. hdbadm), és a másodlagos hely létrehozásához.
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>Fürt keretrendszer konfigurálása

Az alapértelmezett beállítások módosítása

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-defaults.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Most azt betölteni a fájlt a fürthöz
sudo crm terhelés frissítés crm-defaults.txt konfigurálása
</pre>

### <a name="create-stonith-device"></a>STONITH eszköz létrehozása

A STONITH eszköz egy egyszerű szolgáltatást használ, szemben a Microsoft Azure engedélyezése. Kérjük, kövesse ezeket a lépéseket egy egyszerű szolgáltatásnév létrehozásához.

1. Ugrás a <https://portal.azure.com>
1. Nyissa meg az Azure Active Directory panelt  
   Nyissa meg tulajdonságait, és jegyezze fel a könyvtár-azonosító. Ez a **bérlői azonosító**.
1. Kattintson az alkalmazás-regisztráció
1. Kattintson az Add (Hozzáadás) parancsra
1. Adjon meg egy nevet, válassza ki a "Web app/API" alkalmazástípus, adja meg a bejelentkezési URL-címet (például http://localhost) és kattintson a Létrehozás gombra
1. A bejelentkezési URL-címet nem használja, és bármilyen érvényes URL-CÍMEK lehetnek
1. Válassza ki az új alkalmazást, és a beállítások lapon kattintson a kulcsok
1. Adja meg egy új kulcs leírását, válassza a "Soha nem jár le", és kattintson a Mentés gombra
1. Jegyezze fel az értéket. Használják a **jelszó** a szolgáltatás egyszerű
1. Jegyezze fel az alkalmazás azonosítóját. A felhasználónév használják (**bejelentkezési azonosító** az alábbi lépéseket a) a szolgáltatás egyszerű

A szolgáltatás egyszerű nincs engedélye a alapértelmezés szerint az Azure-erőforrások eléréséhez. Hozzá kell rendelnie a szolgáltatás egyszerű engedélyek indítása és leállítása (felszabadítása) a fürt összes virtuális gépet.

1. Ugrás a https://portal.azure.com
1. Nyissa meg az összes erőforrás panel
1. Válassza ki a virtuális gépet
1. Kattintson a hozzáférés-vezérlés (IAM)
1. Kattintson az Add (Hozzáadás) parancsra
1. Válassza ki a szerepkör tulajdonosa
1. Adja meg az előbb létrehozott alkalmazás nevét
1. Kattintson az OK gombra

Után szerkeszteni a virtuális gépek engedélyeit, beállíthatja a STONITH eszközök a fürtben.

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Most azt betölteni a fájlt a fürthöz
sudo crm terhelés frissítés crm-fencing.txt konfigurálása
</pre>

### <a name="create-sap-hana-resources"></a>SAP HANA-erőforrások létrehozása

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system ID
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Most azt betölteni a fájlt a fürthöz
sudo crm terhelés frissítés crm-saphanatop.txt konfigurálása
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Most azt betölteni a fájlt a fürthöz
sudo crm terhelés frissítés crm-saphana.txt konfigurálása
</pre>

### <a name="test-cluster-setup"></a>Teszt fürt beállítása
A következő fejezet ismerteti, hogyan tesztelheti a telepítőt. Minden teszt feltételezi, hogy a legfelső szintű áll, és az SAP HANA-főkiszolgáló fut a virtuális gép saphanavm1.

#### <a name="fencing-test"></a>Teszt kerítésépítés

A telepítő a kerítés ügynök tesztelheti a hálózati adapternek csomópont saphanavm1 letiltásával.

<pre><code>
sudo ifdown eth0
</code></pre>

A virtuális gép kell most beolvasása újraindult, vagy attól függően, hogy a fürt konfigurációját, leállt.
Ha a stonith-művelet kikapcsolva, a virtuális gép leáll, és az erőforrások települnek a futó virtuális gépre.

Után újra elindítani a virtuális gépet, a SAP HANA-erőforrás indulnak-e a másodlagos el Ha AUTOMATED_REGISTER = "false". Ebben az esetben kell HANA-példány beállítása a másodlagos, futtassa a következő parancsot:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Manuális feladatátvétel tesztelése

Manuális feladatátvétel tesztelheti a csomópont saphanavm1 a támasztja szolgáltatás leállításával.
<pre><code>
service pacemaker stop
</code></pre>

A feladatátvétel után újra elindíthatja a szolgáltatást. Az SAP HANA-erőforrás a saphanavm1 nem fog elindulni, mint a másodlagos Ha AUTOMATED_REGISTER = "false". Ebben az esetben kell HANA-példány beállítása a másodlagos, futtassa a következő parancsot:

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>Áttelepítés tesztelése

Az SAP HANA-főcsomópont áttelepítése a következő parancs végrehajtása
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

Ez át kell telepíteni a fő SAP HANA-csomópont és a virtuális IP-címet saphanavm2 tartalmazó csoport.
Az SAP HANA-erőforrás a saphanavm1 nem fog elindulni, mint a másodlagos Ha AUTOMATED_REGISTER = "false". Ebben az esetben kell HANA-példány beállítása a másodlagos, futtassa a következő parancsot:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

Az áttelepítés hoz létre a helyre vonatkozó megszorítások, hogy újra kell törölni.

<pre><code>
crm configure edited

# delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

Is kell a másodlagos csomópont erőforrás állapotának karbantartása

<pre><code>
# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>Következő lépések
* [Az Azure virtuális gépek tervezési és megvalósítási az SAP][planning-guide]
* [Az SAP Azure virtuális gépek telepítése][deployment-guide]
* [Az SAP Azure virtuális gépek adatbázis-kezelő telepítése][dbms-guide]
* Magas rendelkezésre állás és az Azure (nagy példány) az SAP HANA vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagy példányok) magas rendelkezésre állási és vészhelyreállítási helyreállítási Azure](hana-overview-high-availability-disaster-recovery.md). 
