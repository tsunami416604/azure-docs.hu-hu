---
title: A SUSE Linux Enterprise Server Azure virtuális gépeken az NFS magas rendelkezésre állású |} Microsoft Docs
description: A SUSE Linux Enterprise Server Azure virtuális gépeken az NFS magas rendelkezésre állás
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: sedusch
ms.openlocfilehash: b1a7b962d07b64aaa662aab937feed1782851a7b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>A SUSE Linux Enterprise Server Azure virtuális gépeken az NFS magas rendelkezésre állás

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

A cikkből megtudhatja, hogyan telepítse a virtuális gépeket, a virtuális gépet állíthat be, a fürt-keretrendszer telepítése és telepíteni a magas rendelkezésre állású NFS-kiszolgáló magas rendelkezésre állású SAP-rendszer a megosztott adatok tárolására használható.
Ez az útmutató ismerteti, hogyan állíthat be egy magas rendelkezésre állású NFS-kiszolgáló két SAP, NW1 és rendszereket NW2 által használt. A példában szereplő erőforrások (például virtuális gépek, virtuális hálózatok) nevei azt feltételezik, használja a [SAP fájl server sablon] [ template-file-server] erőforrás előtaggal rendelkező **termék**.

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

![SAP NetWeaver magas rendelkezésre állás – Áttekintés](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Az NFS-kiszolgáló minden SAP rendszer, amely használja az NFS-kiszolgáló egy dedikált virtuális állomásnevet és a virtuális IP-címet használ. Az Azure a terheléselosztó virtuális IP-cím szükséges. Az alábbi lista a terheléselosztó-konfiguráció látható.        

* Előtérbeli konfigurációja
  * IP-címet 10.0.0.4 NW1
  * IP-címet 10.0.0.5 NW2
* Háttérkonfiguráció
  * Az összes virtuális gépet, amely az NFS-fürt részét kell képezniük elsődleges hálózati illesztők csatlakozik
* Mintavételi portot
  * NW1 61000 port
  * NW2 61001 port
* Terheléselosztás szabályok
  * A NW1 2049 TCP
  * NW1 2049 UDP
  * A NW2 2049 TCP
  * NW2 2049 UDP

## <a name="set-up-a-highly-available-nfs-server"></a>Egy magas rendelkezésre állású NFS-kiszolgáló beállítása

Használhatja a egy Azure-sablont a githubból minden szükséges Azure-erőforrások, például a virtuális gépek telepítése, beállítása rendelkezésre állás és a terheléselosztóhoz, vagy manuálisan is telepítheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux keresztül Azure sablon telepítése

Az Azure piactéren SUSE Linux Enterprise Server SAP alkalmazások 12-es segítségével új virtuális gépek telepítése a kép tartalmazza.
Segítségével a gyorsindítási sablonok egyikét a githubon központi telepítése az összes szükséges erőforrásokat. A sablon telepíti, a virtuális gépek, a terheléselosztó hasonló adataival, a rendelkezésre állási csoport stb. Kövesse az alábbi lépéseket a sablon telepítéséhez:

1. Nyissa meg a [SAP fájl server sablon] [ template-file-server] az Azure-portálon   
1. Adja meg a következő paraméterek
   1. Erőforrás-előtag  
      Adja meg a használni kívánt előtagot. Az érték a telepített erőforrások esetén használatos előtagjaként.
   2. SAP rendszer száma adja meg ezt a fájlkiszolgálót használó SAP rendszerek számát. Ez telepíti a szükséges memóriamennyiség előtér-konfigurációk terheléselosztási szabályok, mintavételi portok lemezek stb.
   3. Operációs rendszer típusa  
      Válasszon egyet a Linux terjesztéseket. Ehhez a példához válassza ki a SLES 12 rendszert
   4. Rendszergazda felhasználónevét és a rendszergazdai jelszó  
      Új felhasználó jön létre, amely segítségével jelentkezzen be a gépre.
   5. Alhálózati azonosító  
      Az alhálózat, amelyhez a virtuális gépek csatlakoznia kell az azonosítója. Hagyja üresen, ha azt szeretné, hozzon létre egy új virtuális hálózatot, vagy jelölje ki az alhálózatot, a VPN- vagy Express Route virtuális hálózat a virtuális gép és a helyszíni hálózathoz csatlakozni. Az azonosító általában a következőképpen néz következő**&lt;előfizetés-azonosító&gt;**/resourceGroups/**&lt;erőforráscsoport-név&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;virtuálishálózat-név&gt;**/subnets/**&lt;alhálózat neve&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Manuális központi telepítése Linux Azure-portálon

Először az NFS-fürt a virtuális gépek létrehozásához. Ezután hozzon létre egy adott terheléselosztóhoz, és a virtuális gépek használata a háttérkészlet.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Egy rendelkezésre állási csoport létrehozása  
   Készlet maximális frissítési tartomány
1. 1 virtuális gép létrehozása   
   Legalább SLES4SAP 12 SP1, ebben a példában a SLES4SAP 12 SP1 saját kép https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES az SAP alkalmazások 12 SP1 (saját) használatos.  
   Válassza ki a rendelkezésre állási csoport korábban létrehozott  
1. 2. virtuális gép létrehozása   
   Legalább SLES4SAP 12 SP1, ebben a példában a SLES4SAP 12 SP1 saját kép https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1  
   SLES az SAP alkalmazások 12 SP1 (saját) használatos.  
   Válassza ki a rendelkezésre állási csoport korábban létrehozott  
1. Minden egyes SAP rendszerhez egy adatlemez hozzáadása mindkét virtuális gépei számára.
1. Hozzon létre egy terhelés-kiegyenlítő (belső)  
   1. Az előtérbeli IP-címek létrehozása
      1. IP-címet 10.0.0.4 NW1
         1. Nyissa meg a terheléselosztó előtérbeli IP-készlet kiválasztása és kattintson a Hozzáadás gombra
         1. Adja meg az új előtérbeli IP-címkészlet nevét (például **nw1-előtérbeli**)
         1. A hozzárendelés értékűre statikus, majd adja meg az IP-cím (például **10.0.0.4**)
         1. Kattintson az OK gombra         
      1. IP-címet 10.0.0.5 NW2
         * Ismételje meg a fenti NW2
   1. A háttér-címkészletek létrehozása
      1. Az összes virtuális gépet, amely az NFS-fürthöz NW1 részét kell képezniük elsődleges hálózati illesztők csatlakozik
         1. Nyissa meg a terheléselosztóhoz, válassza ki a háttérkészlet, kattintson a Hozzáadás gombra
         1. Adja meg az új háttérkészlet nevét (például **nw1-háttérrendszer**)
         1. Kattintson a Hozzáadás gombra a virtuális gépek
         1. Válassza ki a korábban létrehozott rendelkezésre állási csoport
         1. Válassza ki a virtuális gépek az NFS-fürt
         1. Kattintson az OK gombra
      1. Az összes virtuális gépet, amely az NFS-fürthöz NW2 részét kell képezniük elsődleges hálózati illesztők csatlakozik
         * Ismételje meg a fenti NW2 háttérkészlet létrehozásához
   1. Az állapotfigyelő mintavételt létrehozása
      1. NW1 61000 port
         1. Nyissa meg a terheléselosztóhoz, válassza ki a állapotteljesítmény, kattintson a Hozzáadás gombra
         1. Adja meg az új állapotmintáihoz nevét (például **nw1-hp**)
         1. Válassza ki a TCP protokoll, port 610**00**, időköz 5 és sérült küszöbérték 2
         1. Kattintson az OK gombra
      1. NW2 61001 port
         * Ismételje meg a fenti lépéseket, és hozzon létre egy állapotmintáihoz NW2
   1. Terheléselosztás szabályok
      1. A NW1 2049 TCP
         1. Nyissa meg a terheléselosztóhoz, válassza ki a terheléselosztási szabályok, kattintson a Hozzáadás gombra
         1. Adja meg az új terheléselosztási szabály nevét (például **nw1-lb-2049**)
         1. Válassza ki a front-end IP-címet, a háttérkészlet és a korábban létrehozott állapotmintáihoz (például **nw1-előtérbeli**)
         1. Tartsa protokoll **TCP**, írja be a port **2049**
         1. Növelje a 30 perc üresjárati időtúllépés
         1. **Ügyeljen arra, hogy a fix IP-Címek engedélyezése**
         1. Kattintson az OK gombra    
      1. NW1 2049 UDP
         * Ismételje meg a fenti lépéseket NW1 port 2049 és UDP
      1. A NW2 2049 TCP
         * Ismételje meg a fenti lépéseket a port 2049 és a TCP NW2
      1. NW2 2049 UDP
         * Ismételje meg a fenti lépéseket NW2 port 2049 és UDP

### <a name="create-pacemaker-cluster"></a>Támasztja fürt létrehozása

Kövesse a [támasztja a SUSE Linux Enterprise Server az Azure-ban beállítása](high-availability-guide-suse-pacemaker.md) az NFS-kiszolgáló alapszintű támasztja fürt létrehozásához.

### <a name="configure-nfs-server"></a>Configure NFS server

A következő elemek fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – csak érvényes csomópont 1 vagy **[2]** - csomópont 2 csak érvényes.

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
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]**  Engedélyezése NFS-kiszolgáló

   Hozza létre a legfelső szintű exportálási bejegyzést, indítsa el az NFS-kiszolgáló, és engedélyezze az automatikus indítás

   <pre><code>
   sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/

   sudo systemctl enable nfsserver
   sudo service nfsserver restart
   </code></pre>

1. **[A]**  Drbd összetevőinek telepítése

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Hozzon létre egy partíciót drbd eszközök számára

   Rendelkezésre álló adatok minden lemez felsorolása

   <pre><code>
   sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Példa a kimenetre
   
   ```
   lun0  lun1
   ```

   Minden adat lemez partíciók létrehozása

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]**  Létrehozása LVM konfigurációk

   Az összes elérhető partíció felsorolása

   <pre><code>
   ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Példa a kimenetre
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Minden partíció LVM köteteket hozhat létre

   <pre><code>
   sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]**  Drbd konfigurálása

   <pre><code>
   sudo vi /etc/drbd.conf
   </code></pre>

   Győződjön meg arról, hogy a drbd.conf fájl tartalmazza-e a következő két vonallá

   <pre><code>
   include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   A globális drbd konfigurációjának módosítása

   <pre><code>
   sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Adja hozzá a következő bejegyzéseket a kezelő és a nettó szakasz.

   <pre><code>
   global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             resync-rate 50M;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
        }
   }
   </code></pre>

1. **[A]**  Létrehozása az NFS drbd eszközök

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Helyezze be az új drbd eszköz és a kilépési konfiguráció

   <pre><code>
   resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Helyezze be az új drbd eszköz és a kilépési konfiguráció

   <pre><code>
   resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Hozzon létre a drbd eszközt, és indítsa el

   <pre><code>
   sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Kihagyása a kezdeti szinkronizálás

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Állítsa be az elsődleges csomópont

   <pre><code>
   sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Várjon, amíg az új drbd eszköz szinkronizált

   <pre><code>
   sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]**  Fájlrendszerek létrehozása a drbd eszközökön

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]**  Drbd maszkolt észlelés beállítása

   Ha drbd szinkronizálja az adatokat az egyik gazdagépről a másikra, úgynevezett maszkolt akkor fordulhat elő. A maszkolt olyan forgatókönyvekben, ahol mindkét fürtcsomóponton előléptetni a drbd eszköz az elsődleges és a hiba szinkronban. Előfordulhat, hogy nagyon ritkán helyzet, de továbbra is szeretné kezelni, és hárítsa el a maszkolt lassabbak, mint a lehetséges. Ezért fontos, ha egy maszkolt történt értesítést szeretne kapni.

   Olvasási [a hivatalos drbd dokumentáció](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) a felosztás agy értesítés beállítása.

   Akkor is automatikusan helyreállni a felosztás agy forgatókönyvhöz. További információkért olvassa el a [automatikus vegyes agy helyreállítási házirendek](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Fürt keretrendszer konfigurálása

1. **[1]**  Az SAP rendszer NW1 NFS drbd eszközök hozzáadása a fürt konfigurálása

   <pre><code>
   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=3 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=4 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=5 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=6 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=7 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=8 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> exportfs_<b>NW1</b>_sidsys \
     exportfs_<b>NW1</b>_sapmntsid exportfs_<b>NW1</b>_trans exportfs_<b>NW1</b>_ASCS \
     exportfs_<b>NW1</b>_ASCSERS exportfs_<b>NW1</b>_SCS exportfs_<b>NW1</b>_SCSERS \
     nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]**  Az SAP rendszer NW2 NFS drbd eszközök hozzáadása a fürt konfigurálása

   <pre><code>
   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true   
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=9 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=10 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=11 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=12 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=13 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=14 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=15 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=16 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> exportfs_<b>NW2</b>_sidsys \
     exportfs_<b>NW2</b>_sapmntsid exportfs_<b>NW2</b>_trans exportfs_<b>NW2</b>_ASCS \
     exportfs_<b>NW2</b>_ASCSERS exportfs_<b>NW2</b>_SCS exportfs_<b>NW2</b>_SCSERS \
     nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]**  Tiltsa le a karbantartási mód
   
   <pre><code>
   sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>További lépések
* [Az SAP ASC és az adatbázis telepítéséhez](high-availability-guide-suse.md)
* [Az Azure virtuális gépek tervezési és megvalósítási az SAP][planning-guide]
* [Az SAP Azure virtuális gépek telepítése][deployment-guide]
* [Az SAP Azure virtuális gépek adatbázis-kezelő telepítése][dbms-guide]
* Magas rendelkezésre állás és az Azure (nagy példány) az SAP HANA vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagy példányok) magas rendelkezésre állási és vészhelyreállítási helyreállítási Azure](hana-overview-high-availability-disaster-recovery.md).
* Magas rendelkezésre állás és az Azure virtuális gépeken az SAP HANA vész-helyreállítási terv létrehozásához, lásd: [magas rendelkezésre állás az SAP HANA Azure virtuális gépek (VM)][sap-hana-ha]
