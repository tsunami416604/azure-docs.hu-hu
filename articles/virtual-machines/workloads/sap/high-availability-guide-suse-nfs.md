---
title: A SUSE Linux Enterprise Server Azure virtuális gépeken az NFS magas rendelkezésre állású |} A Microsoft Docs
description: Magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: da465fb9fe51b2be5ec90df1ac75c50271db87a9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992060"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken

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

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Ez a cikk ismerteti a virtuális gépek üzembe helyezése, konfigurálja a virtuális gépek, a fürt keretrendszer telepítése és a magas rendelkezésre állású SAP-rendszer a megosztott adatok tárolására szolgáló magas rendelkezésre állású NFS-kiszolgáló telepítése.
Ez az útmutató azt ismerteti, hogyan állítható be a magas rendelkezésre állású NFS-kiszolgáló két SAP-rendszereit, NW1 és NW2 által használt. Az erőforrások (például virtuális gépek, virtuális hálózatok) a példában nevei azt feltételezik, hogy használja a [SAP fájl kiszolgálói sablon] [ template-file-server] erőforrás előtaggal **prod**.

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
* [SUSE Linux Enterprise magas rendelkezésre állási bővítmény 12 SP3 ajánlott eljárások][sles-hae-guides]
  * Az NFS magas rendelkezésre állású tárolási DRBD és támasztja
* [SUSE Linux Enterprise Server SAP alkalmazások 12 SP3 ajánlott eljárások][sles-for-sap-bp]
* [SUSE magas rendelkezésre állású bővítmény 12 SP3 kibocsátási megjegyzései][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Áttekintés

Magas rendelkezésre állás, az SAP NetWeaver NFS-kiszolgáló szükséges. Az NFS-kiszolgáló egy külön fürtben lett konfigurálva, és több SAP-rendszerek által használható.

![SAP NetWeaver magas rendelkezésre állás – Áttekintés](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Az NFS-kiszolgáló minden SAP-rendszerhez, az NFS-kiszolgálót használó virtuális IP-címek és a egy dedikált virtuális állomásnevet használja. Az Azure-ban a terheléselosztó virtuális IP-cím szükséges. Az alábbi lista a terheléselosztó konfigurációját jeleníti meg.        

* Előtér-konfigurációjához
  * IP-címe 10.0.0.4 NW1
  * IP-cím 10.0.0.5 NW2
* Háttér-konfiguráció
  * Minden virtuális gépnek kell lennie a NFS-fürt elsődleges hálózati adaptere csatlakozik
* Mintavételi Port
  * NW1 61000 portja
  * NW2 61001 portja
* Terheléselosztás szabályok
  * 2049-es TCP NW1 használata
  * 2049-es UDP NW1
  * 2049-es TCP NW2 használata
  * 2049-es UDP NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Egy magas rendelkezésre állású NFS-kiszolgáló beállítása

Minden szükséges Azure-erőforrások, például a virtuális gépek üzembe helyezéséhez vagy használhatja az Azure-sablont a Githubból, rendelkezésre állási beállítása és terheléselosztó vagy manuálisan telepítheti az erőforrásokat.

### <a name="deploy-linux-via-azure-template"></a>Linux-n keresztül az Azure-sablon üzembe helyezése

Az Azure Marketplace-en SUSE Linux Enterprise Server SAP alkalmazások 12, amelyek új virtuális gépek telepítéséhez használhatja a kép tartalmazza.
Használhatja a gyorsindítási sablonok egyikét a Githubon üzembe helyezéséhez szükséges összes erőforrást. A sablon üzembe helyezi a virtuális gépek, a terheléselosztó, a rendelkezésre állási csoport stb. Kövesse az alábbi lépéseket a sablon üzembe helyezéséhez:

1. Nyissa meg a [SAP file server sablon] [ template-file-server] az Azure Portalon   
1. Adja meg a következő paraméterek
   1. Resource Prefix  
      Adja meg a használni kívánt előtagot. Az érték előtagjaként is szolgál az üzembe helyezett erőforrásokat.
   2. Az SAP-rendszer száma  
      Adja meg az SAP-rendszereit, hogy a fájlkiszolgáló fogja használni. Ez telepíti a szükséges méretű előtér-konfigurációk terheléselosztási szabályok, mintavételi port lemezek stb.
   3. Operációs rendszer típusa  
      Válasszon ki egy Linux-disztribúció. Ebben a példában válassza ki a SLES 12 rendszert
   4. Admin Username and Admin Password  
      Egy új felhasználót hoz létre, amely segítségével jelentkezzen be a számítógépen.
   5. Alhálózati azonosító  
      Ha azt szeretné, helyezheti üzembe a virtuális gép egy meglévő Vnetet, amelyekben egy meghatározott alhálózatot a virtuális gép hozzá kell rendelni, nevezze el a kívánt alhálózatot. Az azonosító általában néz ki: /subscriptions/**&lt;előfizetés-azonosító&gt;**/resourceGroups/**&lt;erőforráscsoport-név&gt;**/szolgáltatók/ Microsoft.Network/virtualNetworks/**&lt;virtuálishálózat-nevet&gt;**/subnets/**&lt;alhálózat neve&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Manuálisan üzembe helyezése Linux rendszerű Azure-portálon

Először a virtuális gépek az NFS-fürt létrehozásához. Ezt követően hozzon létre egy terheléselosztó, és a virtuális gépek használata a háttérkészletek.

1. Erőforráscsoport létrehozása
1. Virtuális hálózat létrehozása
1. Egy rendelkezésre állási csoport létrehozása  
   Maximális frissítési tartomány beállítása
1. Hozzon létre legalább a virtuális gép 1 használata SLES4SAP 12 SP3, ebben a példában a SLES4SAP 12 SP3 saját rendszerkép SLES For SAP alkalmazások 12 SP3 (saját) használatos  
   Válassza ki a korábban létrehozott rendelkezésre állási  
1. Hozzon létre legalább a virtuális gép 2 használata SLES4SAP 12 SP3, ebben a példában a SLES4SAP 12 SP3 saját kép  
   SLES For SAP alkalmazások 12 SP3 (saját) használatos.  
   Válassza ki a korábban létrehozott rendelkezésre állási  
1. Minden egyes SAP-rendszerhez egy adatlemez hozzá virtuális gépeket is.
1. Load Balancer létrehozása (belső)  
   1. Hozzon létre az előtérbeli IP-címek
      1. IP-címe 10.0.0.4 NW1
         1. Nyissa meg a terheléselosztó, válassza ki az előtérbeli IP-címkészlet, kattintson a Hozzáadás gombra
         1. Adja meg az új előtérbeli IP-címkészlet nevét (például **nw1-frontend**)
         1. A hozzárendelés Static értékre, és adja meg az IP-címet (például **10.0.0.4**)
         1. Kattintson az OK gombra
      1. IP-cím 10.0.0.5 NW2
         * Ismételje meg a fenti lépéseket NW2
   1. Hozzon létre a háttérkészletek
      1. Az NFS-fürtöt NW1 részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik
         1. Nyissa meg a terheléselosztó, válassza ki a háttérkészletek, és kattintson a Hozzáadás gombra
         1. Adja meg az új háttérkészlet nevét (például **nw1-háttérrendszer**)
         1. Kattintson a Hozzáadás gombra a virtuális gépek
         1. Válassza ki a korábban létrehozott rendelkezésre állási csoport
         1. Válassza ki a virtuális gépeket az NFS-fürt
         1. Kattintson az OK gombra
      1. Az NFS-fürtöt NW2 részének kell lennie az összes virtuális gépek elsődleges hálózati adaptere csatlakozik
         * Ismételje meg a fenti lépéseket, és hozzon létre egy háttérkészlet NW2 a
   1. Az állapotminták létrehozása
      1. NW1 61000 portja
         1. Nyissa meg a terheléselosztó, válassza ki az állapotmintákat, kattintson a Hozzáadás gombra
         1. Adja meg az új állapotadat-mintavétel nevét (például **nw1-hp**)
         1. Válassza ki a TCP protokoll, port 610**00**, időköz 5 és a nem kifogástalan állapot küszöbértéke 2
         1. Kattintson az OK gombra
      1. NW2 61001 portja
         * Ismételje meg a fenti lépéseket, és hozzon létre egy állapotmintát NW2
   1. Terheléselosztás szabályok
      1. 2049-es TCP NW1 használata
         1. Nyissa meg a terheléselosztó, terheléselosztási szabályok kiválasztása, és kattintson a Hozzáadás gombra
         1. Adja meg az új terheléselosztó-szabályt nevét (például **nw1-lb-2049**)
         1. Válassza ki az előtérbeli IP-cím, a háttérkészlet és a korábban létrehozott állapotadat-mintavétel (például **nw1-frontend**)
         1. Tartsa protokoll **TCP**, adja meg a port **2049**
         1. Üresjárati időkorlát akár 30 percig növelése
         1. **Ügyeljen arra, hogy Floating IP engedélyezése**
         1. Kattintson az OK gombra
      1. 2049-es UDP NW1
         * Ismételje meg a fenti lépéseket a 2049-es port- és UDP NW1
      1. 2049-es TCP NW2 használata
         * Ismételje meg a fenti lépéseket a 2049-es port és a TCP NW2
      1. 2049-es UDP NW2
         * Ismételje meg a fenti lépéseket a 2049-es port- és UDP NW2

### <a name="create-pacemaker-cluster"></a>Támasztja fürt létrehozása

Kövesse a [támasztja a SUSE Linux Enterprise Server az Azure-beli beállítása](high-availability-guide-suse-pacemaker.md) az NFS-kiszolgáló alapszintű támasztja fürt létrehozásához.

### <a name="configure-nfs-server"></a>NFS-kiszolgáló konfigurálása

A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – 1. csomópont csak érvényes vagy **: [2]** – 2. csomópont csak érvényes.

1. **[A]**  Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és a következő parancsokat az állomásnevet

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]**  Engedélyezése NFS-kiszolgáló

   A legfelső szintű NFS exportálási bejegyzés létrehozása

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]**  Drbd összetevőinek telepítése

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Hozzon létre egy partíciót a drbd eszközökhöz

   Rendelkezésre álló adatok minden lemez felsorolása

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Példa a kimenetre
   
   ```
   lun0  lun1
   ```

   Minden adatlemez partíciók létrehozása

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]**  Létrehozása LVM-konfigurációk

   Az összes rendelkezésre álló partíciók listázása

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Példa a kimenetre
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Minden partíciójának LVM kötetek létrehozása

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]**  Drbd konfigurálása

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Győződjön meg arról, hogy a drbd.conf fájl tartalmaz-e az alábbi két sort

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   A globális drbd konfigurációjának módosítása

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Adja hozzá a következő bejegyzéseket a kezelő és a nettó szakaszt.

   <pre><code>global {
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
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]**  Az NFS drbd eszközök létrehozása

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   A konfiguráció az új drbd eszköz és a kilépési beszúrása

   <pre><code>resource <b>NW1</b>-nfs {
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

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   A konfiguráció az új drbd eszköz és a kilépési beszúrása

   <pre><code>resource <b>NW2</b>-nfs {
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

   A drbd eszköz létrehozásához, majd indítsa el

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Kihagyása a kezdeti szinkronizálás

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Állítsa be az elsődleges csomópont

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Várjon, amíg a rendszer szinkronizálja az új drbd eszközök

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]**  Fájlrendszerek létrehozása a drbd eszközökön

   <pre><code>sudo mkfs.xfs /dev/drbd0
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

   Szinkronizálhatja az egyik gazdagépről a másikra adatait drbd használatakor egy úgynevezett maszkolt fordulhat elő. Egy maszkolt egy forgatókönyvet, ahol mindkét fürtcsomópont támogatni az drbd eszköz az elsődleges és a történt szinkronizálva. A ritka esetben lehet, de továbbra is szeretné kezelni, és hárítsa el a lehető leghamarabb maszkolt. Ezért fontos lehet szeretne értesítést kapni, amikor egy maszkolt történt.

   Olvasási [a hivatalos drbd dokumentáció](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) való felosztása agy értesítés beállítása.

   Akkor is split agy forgatókönyv automatikus helyreállításán. További információkért olvassa el [automatikus split agy helyreállítási házirendek](https://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurálja a fürt keretrendszer

1. **[1]**  NW1 SAP-rendszerhez, az NFS drbd eszközök hozzáadása a fürt konfigurálása

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

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
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]**  NW2 SAP-rendszerhez, az NFS drbd eszközök hozzáadása a fürt konfigurálása

   <pre><code># Enable maintenance mode
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
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]**  Tiltsa le a karbantartási mód
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>További lépések

* [Az SAP ASCS és adatbázisának telepítése](high-availability-guide-suse.md)
* [Az Azure virtuális gépek tervezése és megvalósítása SAP][planning-guide]
* [Az SAP az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* Magas rendelkezésre állást és az Azure-ban (nagyméretű példányok) SAP Hana vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md).
* Magas rendelkezésre állást és az Azure virtuális gépeken SAP Hana vész-helyreállítási terv létrehozásához, lásd: [magas rendelkezésre állás az SAP HANA Azure-beli virtuális gépeken (VM)][sap-hana-ha]
