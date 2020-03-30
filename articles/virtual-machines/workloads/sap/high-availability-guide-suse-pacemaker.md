---
title: Pacemaker beállítása az SLES-en az Azure-ban | Microsoft dokumentumok
description: Pacemaker beállítása az Azure-beli SUSE Linux Enterprise Server en
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/17/2020
ms.author: radeltch
ms.openlocfilehash: 9d3d0ddbd1282827f17cd82228fcf0f3fba3a60f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471982"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Pacemaker beállítása az Azure-beli SUSE Linux Enterprise Server en

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Két lehetőség van egy pacemaker-fürt beállítására az Azure-ban. Használhatja a kerítésügynök, amely gondoskodik a sikertelen csomópont újraindítása az Azure API-kon keresztül, vagy használhatja az SBD-eszköz.

Az SBD-eszköznek legalább egy további virtuális gépre van szüksége, amely iSCSI-tárolókiszolgálóként működik, és SBD-eszközt biztosít. Ezek az iSCSI-tárolókiszolgálók azonban megoszthatók más Pacemaker-fürtökkel. Az SBD-eszközök használatának előnye a gyorsabb feladatátvételi idő, és ha a helyszíni SBD-eszközöket használja, nincs szükség a pacemaker-fürt működésének módosítására. A Pacemaker-fürthöz legfeljebb három SBD-eszközt használhat annak engedélyezéséhez, hogy egy SBD-eszköz elérhetetlenné váljon, például az iSCSI-tárolókiszolgáló operációsrendszer-javítása során. Ha pacemakerenként egynél több SBD-eszközt szeretne használni, telepítsen több iSCSI-tárolókiszolgálót, és minden iSCSI-tárolókiszolgálóról egy SBD-t csatlakoztasson. Javasoljuk, hogy használjon egy SBD eszközt vagy hármat. A pacemaker nem lesz képes automatikusan lekeríteni egy fürtcsomópontot, ha csak két SBD-eszközt konfigurál, és az egyik nem érhető el. Ha azt szeretné, hogy egy iSCSI-tárolókiszolgáló nem sérült meg, három SBD-eszközt, és így három iSCSI-tárolókiszolgálót kell használnia.

Ha nem szeretne befektetni egy további virtuális gépbe, használhatja az Azure Fence-ügynököt is. A hátránya az, hogy a feladatátvétel 10–15 percet is igénybe vehet, ha egy erőforrás leállítása sikertelen, vagy a fürtcsomópontok nem tudnak kommunikálni, amelyek et többé kommunikálnak.

![Pacemaker az SLES áttekintése](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Linux-pacemaker fürtözött csomópontok és SBD-eszközök tervezésekor és telepítésekor elengedhetetlen a teljes fürtkonfiguráció általános megbízhatósága szempontjából, hogy az érintett virtuális gépek és az SBD-eszközt üzemeltető virtuális gép(ek) közötti útválasztás nem halad át más eszközökön, például [nva-kon.](https://azure.microsoft.com/solutions/network-appliances/) Ellenkező esetben az NVA-val kapcsolatos problémák és karbantartási események negatív hatással lehetnek a teljes fürtkonfiguráció stabilitására és megbízhatóságára. Az ilyen akadályok elkerülése érdekében ne határozza meg az NVA-k vagy [a felhasználó által definiált útválasztási szabályok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) útválasztási szabályait, amelyek a fürtözött csomópontok és az SBD-eszközök közötti forgalmat nva-kon és hasonló eszközökön keresztül irányítják a Linux-pacemaker fürtözött csomópontok és SBD-eszközök tervezése és üzembe helyezése során. 
>

## <a name="sbd-fencing"></a>SBD kerítés

Kövesse az alábbi lépéseket, ha SBD eszközt szeretne használni a kerítéshez.

### <a name="set-up-iscsi-target-servers"></a>ISCSI-tárolókiszolgálók beállítása

Először létre kell hoznia az iSCSI-tároló virtuális gépeket. Az iSCSI-tárolókiszolgálók több pacemaker-fürttel is megoszthatók.

1. Telepítsen új SLES 12 SP1 vagy újabb virtuális gépeket, és csatlakozzon hozzájuk ssh-n keresztül. A gépeknek nem kell nagynak lenniük. A virtuális gép mérete, mint a Standard_E2s_v3 vagy Standard_D2s_v3 elegendő. Győződjön meg arról, hogy a Prémium szintű tároló az operációs rendszer lemeze.

Futtassa a következő parancsokat az összes **iSCSI-tároló virtuális gépen.**

1. Frissítés SLES

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Előfordulhat, hogy újra kell indítania az operációs rendszert az operációs rendszer frissítése vagy frissítése után. 

1. Csomagok eltávolítása

   A targetcli és az SLES 12 SP3 ismert problémája elkerülése érdekében távolítsa el a következő csomagokat. Figyelmen kívül hagyhatja a nem található csomagok hibáit

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. ISCSI-tárolócsomagok telepítése

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Az iSCSI-tároló szolgáltatás engedélyezése

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>ISCSI-eszköz létrehozása iSCSI-tárolókiszolgálón

Futtassa a következő parancsokat az összes **iSCSI-tároló virtuális gépen** az SAP-rendszerek által használt fürtök iSCSI-lemezei létrehozásához. A következő példában több fürt SBD-eszközei jönnek létre. Bemutatja, hogyan használhat na egy iSCSI-tárolókiszolgálót több fürthöz. Az SBD-eszközök az operációs rendszer lemezére kerülnek. Győződjön meg róla, hogy van elég hely.

**`nfs`** az NFS-fürt azonosítására szolgál, az **ascsnw1** az **NW1,** **dbnw1** ASCS-fürt azonosítására szolgál az **NW1**, **nfs-0** és **nfs-1** adatbázis-fürt azonosítására az NFS-fürtcsomópontok állomásnevei, **Az nw1-xscs-0** és **az nw1-xscs-1** az **NW1** ASCS fürtcsomópontok állomásnevei, az **nw1-db-0** és az **nw1-db-1** pedig az adatbázis-fürtcsomópontok állomásnevei. Cserélje le őket a fürtcsomópontok állomásneveire és az SAP-rendszer biztonsági azonosítójára.

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

Ellenőrizheti, hogy minden megfelelően van-e beállítva a

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

### <a name="set-up-sbd-device"></a>SBD-eszköz beállítása

Csatlakozzon ahhoz az iSCSI-eszközhöz, amelyet a fürt utolsó lépésében hoztak létre.
Futtassa a következő parancsokat a létrehozni kívánt új fürt csomópontjain.
A következő elemek előtaggal vannak ellátva **az [A]** - az összes csomópontra vonatkozóan, **[1]** - csak az 1- es vagy **a [2]** csomópontra vonatkozik, és csak a 2-es csomópontra vonatkoznak.

1. **[A]** Csatlakozás az iSCSI-eszközökhöz

   Először engedélyezze az iSCSI- és SBD-szolgáltatásokat.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** A kezdeményező nevének módosítása az első csomóponton

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Módosítsa a fájl tartalmát úgy, hogy az megfeleljen az iSCSI-eszköz nek az iSCSI-tárolókiszolgálón , például az NFS-kiszolgálón történő létrehozásakor használt ACL-knak.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** A kezdeményező nevének módosítása a második csomóponton

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   A fájl tartalmának módosítása az iSCSI-tárolókiszolgálón az iSCSI-eszköz létrehozásakor használt ACL-knak megfelelően

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Az iSCSI-szolgáltatás újraindítása

   Most indítsa újra az iSCSI-szolgáltatást a módosítás alkalmazásához

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Csatlakoztassa az iSCSI-eszközöket. Az alábbi példában a 10.0.0.17 az iSCSI-tároló kiszolgáló IP-címe, a 3260 pedig az alapértelmezett port. <b>az iqn.2006-04.nfs.local:nfs</b> az alábbi első parancs futtatásakor felsorolt célnevek egyike (iscsiadm -m discovery).

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

   Győződjön meg arról, hogy az iSCSI-eszközök elérhetők, és jegyezze fel az eszköz nevét (a következő példában /dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Most olvassa be az iSCSI-eszközök azonosítóit.

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

   A parancs három eszközazonosítót sorol fel minden SBD eszközhöz. Javasoljuk, hogy a scsi-3-mal kezdődő azonosítót használja, a fenti példában a

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/byd/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/byd/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Az SBD eszköz létrehozása

   Az iSCSI-eszközök eszközazonosítójával hozza létre az új SBD-eszközöket az első fürtcsomóponton.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** Az SBD konfiguráció adaptálása

   Az SBD konfigurációs fájl megnyitása

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Módosítsa az SBD-eszköz tulajdonságát, engedélyezze a pacemaker-integrációt, és módosítsa az SBD indítási módját.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   A `softdog` konfigurációs fájl létrehozása

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Most töltse be a modult

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Fürt telepítése

A következő elemek előtaggal vannak ellátva **az [A]** - az összes csomópontra vonatkozóan, **[1]** - csak az 1- es vagy **a [2]** csomópontra vonatkozik, és csak a 2-es csomópontra vonatkoznak.

1. **[A]** Frissítés SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** Fürterőforrásokhoz szükséges összetevő telepítése

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** Telepítse az azure-lb összetevőt, amely fürterőforrásokhoz szükséges

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Ellenőrizze a csomag erőforrás-ügynökök verzióját, és győződjön meg arról, hogy a minimális verziókövetelmények teljesülnek:  
   > - Az SLES 12 SP4/SP5 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.018.a7fb5035-3.30.1.  
   > - Az SLES 15/15 SP1 esetében a verziónak legalább erőforrás-ügynököknek kell lennie-4.3.0184.6ee15eb2-4.13.1.  

1. **[A]** Az operációs rendszer konfigurálása

   Bizonyos esetekben a Pacemaker számos folyamatot hoz létre, és ezáltal kimeríti a folyamatok megengedett számát. Ebben az esetben a fürtcsomópontok közötti szívverés sikertelen lehet, és az erőforrások feladatátvételhez vezethet. Javasoljuk, hogy növelje a maximálisan engedélyezett folyamatokat a következő paraméter beállításával.

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

   Csökkentse a piszkos gyorsítótár méretét. További információ: [Alacsony írási teljesítmény nagy RAM memóriával rendelkező SLES 11/12 kiszolgálókon.](https://www.suse.com/support/kb/doc/?id=7010287)

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Felhő-netconfig-azure konfigurálása a HA-fürthöz

   Módosítsa a hálózati adapter konfigurációs fájlját az alábbiak szerint, hogy megakadályozza a felhőhálózati bővítmény eltávolítását a virtuális IP-címből (a Pacemakernek ellenőriznie kell a VIP-hozzárendelést). További információ: [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** SSH-hozzáférés engedélyezése

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** SSH-hozzáférés engedélyezése

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** SSH-hozzáférés engedélyezése

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Kerítésügynökök telepítése
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Ha a Suse Linux Enterprise Server for SAP 15, vegye figyelembe, hogy további modul aktiválása és további összetevő telepítése, amely az Azure Fence Agent használatának előfeltétele. A SUSE modulokról és bővítményekről a [Modulok és bővítmények ismertetése](https://www.suse.com/documentation/sles-15/singlehtml/art_modules/art_modules.html). Kövesse az utasításokat ordít az Azure Python SDK telepítéséhez. 

   Az Azure Python SDK telepítésével kapcsolatos alábbi utasítások csak az SAP **15-höz**készült Suse Enterprise Server kiszolgálóra vonatkoznak.  

    - Ha bring-your-own-subscription előfizetést használ, kövesse az alábbi  

    <pre><code>
    #Activate module PackageHub/15/x86_64
    sudo SUSEConnect -p PackageHub/15/x86_64
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

     - Ha használatalapú fizetéses előfizetést használ, kövesse az alábbi  

    <pre><code>#Activate module PackageHub/15/x86_64
    zypper ar https://download.opensuse.org/repositories/openSUSE:/Backports:/SLE-15/standard/ SLE15-PackageHub
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

1. **[A]** Állomásnév feloldása

   Használhatja a DNS-kiszolgálót, vagy módosíthatja az /etc/hosts kapcsolót az összes csomóponton. Ez a példa az /etc/hosts fájl használatát mutatja be.
   Cserélje le az IP-címet és az állomásnevet a következő parancsokban. Az /etc/hosts használatának előnye, hogy a fürt függetlenné válik a DNS-től, ami a hibák egyetlen pontja is lehet.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Szúrja be a következő sorokat az /etc/hosts könyvtárba. Az IP-cím és az állomásnév módosítása a környezetnek megfelelően   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Fürt telepítése

   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Csomópont hozzáadása a fürthöz

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** A hacluster jelszó módosítása ugyanarra a jelszóra

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Állítsa be a corosync beállításokat.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Adja hozzá a következő félkövér tartalmat a fájlhoz, ha az értékek nem léteznek vagy nem különböznek. Győződjön meg arról, hogy módosítsa a jogkivonatot 30000-re, hogy a memória megőrizhesse a karbantartást. További információt ebben a Linux vagy Windows [rendszerben olvasható cikkben][virtual-machines-linux-maintenance] talál. [Windows][virtual-machines-windows-maintenance]

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
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

   Ezután indítsa újra a corosync szolgáltatást

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Azure Fence ügynök STONITH-eszköz létrehozása

A STONITH-eszköz egy egyszerű szolgáltatás használatával engedélyezi a Microsoft Azure-t. Az egyszerű szolgáltatás létrehozásához kövesse az alábbi lépéseket.

1. Nyissa meg a következőt: <https://portal.azure.com>
1. Az Azure Active Directory panel megnyitása  
   Nyissa meg a Tulajdonságok felet, és írja le a címtárazonosítót. Ez a **bérlőazonosítója.**
1. Kattintson az Alkalmazásregisztrációk elemre
1. Kattintson az Új regisztráció gombra
1. Írjon be egy nevet, és válassza a "Csak ebben a szervezeti címtárban lévő fiókok" lehetőséget. 
2. Válassza a "Web" alkalmazástípust, adjon meg egy\/bejelentkezési URL-címet (például http: /localhost), és kattintson a Hozzáadás gombra  
   A bejelentkezési URL nem használatos, és bármely érvényes URL-cím lehet
1. Válassza a Tanúsítványok és titkos kulcsok lehetőséget, majd kattintson az Új ügyféltitok elemre.
1. Adjon meg egy új kulcsot, válassza a "Soha nem jár le" lehetőséget, és kattintson a Hozzáadás gombra.
1. Írja le az Érték. A szolgáltatásegyszerű **jelszóként** szolgál.
1. Válassza az Áttekintés lehetőséget. Írja le az alkalmazásazonosítót. A Szolgáltatásnév felhasználóneveként (az alábbi lépésekben**bejelentkezési azonosító)**

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Egyéni szerepkör létrehozása a kerítésügynök számára

Az egyszerű szolgáltatás nem rendelkezik az Azure-erőforrások eléréséhez alapértelmezés szerint nem rendelkezik engedéllyel. Meg kell adnia a Szolgáltatás egyszerű engedélyeket a fürt összes virtuális gépének indításához és leállításához (felszabadításához). Ha még nem hozták létre az egyéni szerepkört, létrehozhatja azt [a PowerShell](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-powershell#create-a-custom-role) vagy az [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-cli) használatával

Használja a következő tartalmat a bemeneti fájlhoz. Meg kell igazítani a tartalmat, hogy az előfizetések, azaz cserélje c276fc76-9cd4-44c9-99a7-4fd71546436e és e91d47c4-76f3-4271-a796-21b4ecfe3624 az azonosítót az előfizetés. Ha csak egy előfizetéssel rendelkezik, távolítsa el a második bejegyzést a AssignableScopes alkalmazásból.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action", 
    "Microsoft.Compute/virtualMachines/powerOff/action" 
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Az egyéni szerepkör hozzárendelése az egyszerű szolgáltatáshoz

Rendelje hozzá az egyéni szerepkör "Linux kerítésügynök szerepkör", amely az utolsó fejezetben létrehozott a szolgáltatásnév. Ne használja többé a Tulajdonos szerepkört!

1. odamegy[https://portal.azure.com](https://portal.azure.com)
1. A Minden erőforrás panel megnyitása
1. Az első fürtcsomópont virtuális gépének kiválasztása
1. Kattintson a Hozzáférés-vezérlés (IAM) elemre
1. Kattintson a Szerepkör-hozzárendelés hozzáadása gombra.
1. Válassza ki a "Linux kerítésügynök szerepkör" szerepkört
1. Adja meg a fent létrehozott alkalmazás nevét.
1. Kattintson a Save (Mentés) gombra.

Ismételje meg a fenti lépéseket a második fürtcsomóponthoz.

### <a name="1-create-the-stonith-devices"></a>**[1]** A STONITH eszközök létrehozása

Miután szerkesztette a virtuális gépek engedélyeit, konfigurálhatja a STONITH-eszközöket a fürtben.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Az SBD alapértelmezett pacemaker-konfigurációja

1. **[1]** Engedélyezze a STONITH eszköz használatát, és állítsa be a kerítés késleltetését

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

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Pacemaker konfiguráció az Azure ütemezett események

Az Azure [ütemezett eseményeket](https://docs.microsoft.com/azure/virtual-machines/linux/scheduled-events)kínál. Az ütemezett események meta-adatszolgáltatáson keresztül kerülnek rendelkezésre, és időt hagynak az alkalmazásszámára az olyan eseményekre való felkészülésre, mint a virtuális gépek leállítása, a virtuális gépek újratelepítése stb. Erőforrás-ügynök **[azure-események](https://github.com/ClusterLabs/resource-agents/pull/1161)** figyeli az ütemezett Azure-események. Események észlelése esetén az ügynök megpróbálja leállítani az összes erőforrást az érintett virtuális gépen, és áthelyezni őket a fürt egy másik csomópontjára. Ennek elérése érdekében további Pacemaker-erőforrásokat kell konfigurálni. 

1. **[A]** Győződjön meg arról, hogy az **azure-events** ügynök csomagja már telepítve van és naprakész. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Konfigurálja az erőforrásokat a Pacemaker ben. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Miután konfigurálta a Pacemaker-erőforrásokat az azure-events ügynökhöz, amikor a fürtet karbantartási módban helyezi el, vagy ki van helyezve a karbantartási módból, figyelmeztető üzenetekjelenhetnek meg, például:  
     FIGYELEM: cib-bootstrap-options: ismeretlen attribútum "hostName_ <strong>hostname</strong>"  
     FIGYELEM: cib-bootstrap-options: ismeretlen attribútum "azure-events_globalPullState"  
     FIGYELEM: cib-bootstrap-options: ismeretlen attribútum "hostName_ <strong>hostname</strong>"  
   > Ezek a figyelmeztető üzenetek figyelmen kívül hagyhatók.

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* [Magas rendelkezésre állás az NFS számára az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server rendszeren][sles-nfs-guide]
* [Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server SAP-alkalmazásokhoz szolgáltatásban][sles-guide]
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure virtuális gépeken, olvassa el [az SAP HANA magas rendelkezésre állását az Azure virtuális gépeken (VM-ek) című témakört.][sap-hana-ha]
