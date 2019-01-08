---
title: A SUSE Linux Enterprise Server az Azure-ban támasztja beállítása |} A Microsoft Docs
description: A SUSE Linux Enterprise Server az Azure-ban támasztja beállítása
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
ms.openlocfilehash: 00e50d60ed7f13a2f28338891b39b1b55b7adbfa
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074376"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>A SUSE Linux Enterprise Server az Azure-ban támasztja beállítása

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#memory-preserving-maintenance
[virtual-machines-windows-maintenance]:../../windows/maintenance-and-updates.md#memory-preserving-maintenance
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Az Azure-ban támasztja fürt létrehozása két módon lehet. Használhat egy szintaxiskiemeléshez ügynököt, amely gondoskodik az Azure API-kon keresztül sikertelen csomópont újraindítása, vagy egy SBD eszközt is használhat.

A SBD eszköz, amely az iSCSI-tárolókiszolgáló funkcionál, és a egy SBD eszközt, legalább egy további virtuális gép igényel. Ezek iSCSI-célkiszolgálókhoz azonban lehetnek más támasztja fürtökkel osztva. Azt az előnyt SBD eszközt használ, gyorsabb feladatátvételt, valamint SBD a helyszíni eszközök, használatakor nem szükséges a támasztja fürt működési módját a módosításokat. Támasztja fürt legfeljebb három SBD eszközöket használhatja, hogy egy SBD eszköz már nem érhető el, például az iSCSI-tárolókiszolgáló javítás az operációs rendszer alatt. Ha egynél több SBD készülékenként támasztja használni kívánt, ügyeljen arra, hogy több iSCSI cél kiszolgáló telepítése és a egy SBD csatlakozzon az egyes iSCSI-tárolókiszolgáló. Egy SBD eszközzel vagy a három használatát javasoljuk. Támasztja nem lesz képes automatikusan fence egy fürt csomópontja, ha csak két SBD eszközt konfigurálja, és egyik nem érhető el. Ha meg szeretné tudni időkorlát, amikor egy iSCSI-tárolókiszolgáló nem működik, akkor három SBD eszközöket, és ezért három iSCSI-célkiszolgálókhoz.

Ha nem szeretné, hogy egy további virtuális gép be, használhatja az Azure időkorlát ügynök. A hátránya az, hogy a feladatátvétel eltarthat 10 – 15 perc között, ha egy erőforrás leállítása sikertelen, vagy a fürt csomópontjai nem tud kommunikálni amely egymáshoz többé.

![Támasztja a SLES áttekintése](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Tervezési és üzembe helyezése Linux támasztja fürtözött csomópontok és SBD eszközöket, amikor ez elengedhetetlen az általános megbízhatóságot nyújt, amely a virtuális gépek közötti útválasztást az érintett, és nem továbbítja a SBD őket üzemeltető virtuális gép teljes fürtkonfiguráció bármely más eszközök, például [nva-k](https://azure.microsoft.com/solutions/network-appliances/). Ellenkező esetben problémákat és az nva-t a karbantartási események negatív hatással lehet a stabilitás és megbízhatóság teljes fürtkonfiguráció. Annak érdekében, hogy ezeket az akadályokat, nem határoznak meg Nva-útválasztási szabályok vagy [felhasználói meghatározott útválasztási szabályok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) a fürtözött csomópontok és SBD eszközöknek az nva-k és hasonló eszközök tervezésekor és üzembe helyezése Linux közötti forgalom irányítása Fürtözött támasztja csomópontok és SBD eszközök. 
>

## <a name="sbd-fencing"></a>SBD szintaxiskiemeléshez

Ha szeretne egy SBD eszköz használata az elkerítés, kövesse az alábbi lépéseket.

### <a name="set-up-iscsi-target-servers"></a>ISCSI-célkiszolgálókhoz beállítása

Először az iSCSI cél virtuális gépek létrehozásához. iSCSI-célkiszolgálókhoz megoszthatók több támasztja fürtökkel.

1. Új SLES 12 SP1 vagy újabb virtuális gépek üzembe helyezéséhez és csatlakozáshoz keresztül ssh. A gépeket nem kell nagy. A virtuális gép méretét, Standard_E2s_v3 vagy Standard_D2s_v3 nem elegendő. Ellenőrizze, hogy a Premium storage az operációsrendszer-lemez használatára.

Futtassa az alábbi parancsokat az összes **iSCSI cél virtuális gépek**.

1. SLES frissítése

   <pre><code>sudo zypper update
   </code></pre>

1. Csomagok eltávolítása

   SLES 12 SP3 és targetcli egy ismert probléma elkerülése érdekében távolítsa el a következő csomagok. Hibák, amelyek nem találhatók csomagok figyelmen kívül hagyhatja

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. ISCSI cél csomagok telepítése

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Az iSCSI-tároló szolgáltatás engedélyezése

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Az iSCSI-tárolókiszolgáló iSCSI-eszköz létrehozása

Futtassa az alábbi parancsokat az összes **iSCSI cél virtuális gépek** a fürtök, az SAP-rendszerek által használt iSCSI-lemezek létrehozásához. A következő példában több fürt SBD eszközök jönnek létre. Ez bemutatja, hogyan szeretné használni egy iSCSI-tárolókiszolgáló több fürthöz. Az operációsrendszer-lemez a SBD eszközök kerülnek. Győződjön meg arról, hogy rendelkezik-e elegendő lemezterület.

**az NFS** azonosítja az NFS-fürt **ascsnw1** azonosítja az ASCS fürt **NW1**, **dbnw1** azonosítja az adatbázis fürtben **NW1**, **nfs-0** és **nfs-1** NFS fürtcsomópont a gazdanevek vannak **nw1-xscs-0** és  **nw1-xscs-1** , a gazdanevek vannak a **NW1** ASCS fürt csomópontjai, és **nw1-db-0** és **nw1-db-1** az adatbázis a gazdanevek vannak fürtcsomópontok. Cserélje le őket a fürtcsomópontok állomásnevét, és az SAP-rendszerhez biztonsági azonosítója.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Ellenőrizheti, hogy ha mindent beállításának ellenőrzése a

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>SBD eszköz beállítása

Az iSCSI-eszközt a fürtből az előző lépésben létrehozott csatlakozni.
Futtassa az alábbi parancsokat a létrehozni kívánt új fürt csomópontjain.
A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – 1. csomópont csak érvényes vagy **: [2]** – 2. csomópont csak érvényes.

1. **[A]**  Csatlakozhat az iSCSI-eszközök

   Először engedélyezze az iSCSI és SBD szolgáltatásokat.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Első csomópontjára kezdeményező nevének módosítása

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Módosítsa a megfelelő ACL-ek az iSCSI-eszközt a az iSCSI-tárolókiszolgáló, például az NFS-kiszolgáló létrehozásakor használt fájl tartalmát.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **a(z) [2]**  Kezdeményező a második csomópont nevének módosítása

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   A megfelelő ACL-ek az iSCSI-tárolókiszolgáló az iSCSI-eszközt létrehozásakor használt a fájl tartalmának módosítása

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]**  Az iSCSI-szolgáltatás újraindítása

   Most indítsa újra az iSCSI szolgáltatást, a módosítás alkalmazása

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Az iSCSI-eszközök csatlakoztatása. Az alábbi példában 10.0.0.17 az iSCSI-tárolókiszolgáló IP-címe pedig 3260-as az alapértelmezett portot. <b>IQN.2006-04.nfs.local:nfs</b> egyike a cél nevét, amely szerepel a listán (iscsiadm -m felderítési) alatt az első parancs futtatásakor.

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Győződjön meg arról, hogy az iSCSI-eszközök érhetők el, és jegyezze fel az eszköz neve (a következő példa/dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Az iSCSI-eszközök azonosítóinak most lekéréséhez.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   A parancs minden SBD eszközhöz három eszközazonosítókat listája. Javasoljuk, hogy kezdetű scsi-3, ez a fenti példában az azonosító használata

   * **/dev/Disk/by-ID/SCSI-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/Disk/by-ID/SCSI-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]**  A SBD eszköz létrehozása

   Az eszköz azonosítója, az iSCSI-eszközök használatával hozzon létre az új SBD eszközök az első fürtcsomópontra.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]**  Alkalmazkodnak a SBD config

   Nyissa meg a SBD konfigurációs fájlt

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   A tulajdonság az SBD eszköz módosítása, támasztja integrációjának engedélyezése és SBD indítási módját módosítani.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   A softdog konfigurációs fájl létrehozása

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Töltse be a modul

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Fürt telepítése

A következő elemek van fűzve előtagként vagy **[A]** – az összes csomópont alkalmazandó **[1]** – 1. csomópont csak érvényes vagy **: [2]** – 2. csomópont csak érvényes.

1. **[A]**  SLES frissítése

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]**  Az operációs rendszer konfigurálása

   Bizonyos esetekben támasztja sok folyamatot hoz létre, és ezáltal kimerítik a folyamatok engedélyezett számát. Ebben az esetben a fürt csomópontok közötti szívverést előfordulhat, hogy sikertelen, és az erőforrások feladatátvételi vezethet. Javasoljuk, hogy növelje a maximálisan megengedett folyamatok a következő paraméter beállításával.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   A szabálytalan gyorsítótár méretének csökkentésére. További információkért lásd: [SLES 11/12 alacsony az írási teljesítmény nagy RAM-MAL rendelkező kiszolgálók](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[1]**  Ssh hozzáférés engedélyezése

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **a(z) [2]**  Ssh hozzáférés engedélyezése

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys
   
   sudo ssh-keygen

   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]**  Ssh hozzáférés engedélyezése

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Ügynökök telepítése időkorlátja
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

1. **[A]**  Állomásnév-feloldás beállítása

   DNS-kiszolgálót használjon, vagy módosítsa a Hosts az összes csomópontra. Ez a példa bemutatja, hogyan használhatja a Hosts fájlt.
   Cserélje le az IP-cím és az állomásnevet, az alábbi parancsokban. A Hosts használatával előnye, hogy a fürt független a DNS, amely túl lehet egyetlen pont, a hibák válik.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Helyezze be a következő sorokat Hosts. Módosítsa az IP-cím és a környezet megfelelő állomásnév   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]**  Fürt telepítése

   <pre><code>sudo ha-cluster-init
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Network address to bind to (e.g.: 192.168.1.0) [10.0.0.0] <b>Press ENTER</b>
   # Multicast address (e.g.: 239.x.x.x) [239.232.97.43] <b>Press ENTER</b>
   # Multicast port [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **a(z) [2]**  Csomópont hozzáadása a fürthöz

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]**  Ugyanazt a jelszót hacluster jelszó módosítása

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]**  Más átviteli használhatja, és adja hozzá a csomópontlista corosync konfigurálása. Fürt egyébként nem működik.

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Adja hozzá a következő félkövér tartalmat a fájlhoz, ha az értékek nem ott vagy eltérő. Ellenőrizze, hogy módosítsa a jogkivonatot, hogy a karbantartás megőrzése memória 30000. További információkért lásd: [Ez a cikk a Linux-] [ virtual-machines-linux-maintenance] vagy [Windows][virtual-machines-windows-maintenance]. Ügyeljen arra, hogy a paraméter mcastaddr eltávolítása.

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     <b>transport:      udpu</b>
     # remove parameter mcastaddr
     <b># mcastaddr: IP</b>
   } 
   <b>nodelist {
     node {
      # IP address of <b>prod-cl1-0</b>
      ring0_addr:10.0.0.6
     }
     node {
      # IP address of <b>prod-cl1-1</b>
      ring0_addr:10.0.0.7
     } 
   }</b>
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Indítsa újra az corosync

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Azure időkorlát ügynök STONITH eszköz létrehozása

A STONITH eszköz hitelesítéséhez, szemben a Microsoft Azure egy egyszerű szolgáltatást használja. Kövesse az alábbi lépéseket egy szolgáltatásnév létrehozásához.

1. Lépjen a < https\://portal.azure.com >
1. Nyissa meg az Azure Active Directory panel  
   Lépjen a Tulajdonságok részhez, és jegyezze fel a címtár-azonosító. Ez a **bérlőazonosító**.
1. Kattintson az alkalmazásregisztrációk
1. Kattintson az Add (Hozzáadás) parancsra
1. Adjon meg egy nevet, válassza ki a "Web app/API" alkalmazástípus, adja meg a bejelentkezési URL-címet (például http\://localhost), és kattintson a Létrehozás gombra
1. A bejelentkezési URL-címet nem használja, és bármilyen érvényes URL-cím lehet
1. Válassza ki az új alkalmazást, és a beállítások lapon kattintson a kulcsok
1. Adja meg egy új kulcs leírását, válassza a "Soha nem jár le", és kattintson a Mentés gombra
1. Jegyezze fel az értéket. Használatban van a **jelszó** a Szolgáltatásnévhez
1. Jegyezze fel az alkalmazás azonosítóját. A felhasználónév használatban van (**bejelentkezési azonosító** az alábbi lépéseket a) a szolgáltatásnév

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Az időkorlát-ügynökhöz tartozó egyéni szerepkör létrehozása

Az egyszerű szolgáltatás nem rendelkezik engedélyekkel alapértelmezés szerint az Azure-erőforrások eléréséhez. Elindíthatja és leállíthatja a szolgáltatásnév-engedélyt kell (szabadítsa fel) a fürt összes virtuális gépet. Ha nem hozott már létre az egyéni szerepkör, létrehozhat használatával [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#create-a-custom-role) vagy [Azure CLI-vel](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli#create-a-custom-role)

A bemeneti fájl használja az alábbi tartalommal. Szeretne az előfizetések a tartalmat, amely alkalmazkodik c276fc76-9cd4-44c9-99a7-4fd71546436e és e91d47c4-76f3-4271-a796-21b4ecfe3624 cserélje le az előfizetés azonosítóját. Ha több előfizetéssel rendelkezik, távolítsa el a második bejegyzés AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="1-assign-the-custom-role-to-the-service-principal"></a>**[1]**  Az egyéni szerepkör hozzárendelése a Szolgáltatásnévhez

Rendelje hozzá az egyéni szerepkör "Linux időkorlát ügynök szerepkör", amely az előző fejezetben a szolgáltatásnév sikeresen létrehozva. A tulajdonosi szerepkör ne használjon többé!

1. Lépjen a https\://portal.azure.com
1. Nyissa meg az összes erőforrás panelen
1. Válassza ki a virtuális gépet, az első fürtcsomópontra
1. Kattintson a hozzáférés-vezérlés (IAM)
1. Kattintson a szerepkör-hozzárendelés hozzáadása
1. A "Linux időkorlát ügynök szerepkör" szerepkör kiválasztása
1. Adja meg a fent létrehozott alkalmazás neve
1. Kattintson a Save (Mentés) gombra.

Ismételje meg a fenti lépéseket a második fürtcsomópontra.

### <a name="1-create-the-stonith-devices"></a>**[1]**  A STONITH eszközök létrehozása

Miután szerkesztette az engedélyek a virtuális gépek, konfigurálhatja úgy a STONITH eszközöket a fürtben.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>SBD támasztja alapértelmezett konfigurációja

1. **[1]**  STONITH eszköz használatának engedélyezése, és állítsa be az időkorlát késleltetés

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása SAP][planning-guide]
* [Az SAP az Azure virtuális gépek üzembe helyezése][deployment-guide]
* [Az SAP az Azure Virtual Machines DBMS üzembe helyezése][dbms-guide]
* [Magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken][sles-nfs-guide]
* [Magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server az Azure virtuális gépeken SAP-alkalmazások][sles-guide]
* Magas rendelkezésre állást és az Azure virtuális gépeken SAP Hana vész-helyreállítási terv létrehozásához, lásd: [magas rendelkezésre állás az SAP HANA Azure-beli virtuális gépeken (VM)][sap-hana-ha]
