---
title: SAP HANA kibővített HSR-pacemaker SLES-sel az Azure virtuális gépek hibaelhárítása| Microsoft dokumentumok
description: Útmutató egy összetett SAP HANA kibővített, magas rendelkezésre állású konfiguráció ellenőrzéséhez és elhárításához az SAP HANA rendszerreplikáció (HSR) és az Azure virtuális gépeken futó SLES 12 SP3 pacemakere alapján
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: e93b3412785817050ac53030be9ff2172a678c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617119"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Az SAP HANA kibővített, magas rendelkezésre állású telepítésének ellenőrzése és hibaelhárítása az SLES 12 SP3-on 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Ez a cikk segít az Azure virtuális gépeken (VM-ek) futó SAP HANA horizontális felskálázás pacemaker-fürtkonfigurációjának ellenőrzésében. A fürt beállítása az SAP HANA rendszerreplikációval (HSR) és az SAPHanaSR-ScaleOut SUSE RPM csomaggal együtt történt. Minden vizsgálatot csak suse SLES 12 SP3-on végeztek. A cikk szakaszai különböző területeket fednek le, és mintaparancsokat és kivonatokat tartalmaznak a konfigurációs fájlokból. Ezeket a mintákat a fürt teljes beállításának ellenőrzésére és ellenőrzésére szolgáló módszerként javasoljuk.



## <a name="important-notes"></a>Fontos megjegyzések

Az SAP HANA horizontális felskálázás az SAP HANA rendszerreplikációval és pacemakerrel kombinálva végzett összes tesztelés csak az SAP HANA 2.0-val történt. Az operációs rendszer verziója a SUSE Linux Enterprise Server 12 SP3 SAP alkalmazásokhoz volt. A legutóbbi RPM-csomagot, a SUSE SAPHanaSR-ScaleOut-ját a Pacemaker-fürt beállításához használták.
A SUSE közzétette [a teljesítményre optimalizált beállítás részletes leírását.][sles-hana-scale-out-ha-paper]

Az SAP HANA horizontális felskálázás támogatott virtuálisgép-típusok esetében tekintse meg az [SAP HANA tanúsítvánnyal rendelkező IaaS-címtárban.][sap-hana-iaas-list]

Technikai probléma volt az SAP HANA horizontális felskálázkódás több alhálózattal és vNIC-vel kombinálva, és a HSR beállítása. Kötelező a legújabb SAP HANA 2.0-javítások használata, ahol ezt a problémát kijavították. A következő SAP HANA-verziók támogatottak: 

* rev2.00.024.04 vagy újabb 
* rev2.00.032 vagy újabb

Ha a SUSE támogatására van szüksége, kövesse ezt az [útmutatót][suse-pacemaker-support-log-files]. A cikkben leírtak szerint gyűjtse össze az SAP HANA magas rendelkezésre állású (HA) fürtjével kapcsolatos összes információt. A SUSE-támogatásnak szüksége van erre az információra a további elemzéshez.

A belső tesztelés során a fürt beállítása összezavarodott egy normál kecses virtuális gép leállítása az Azure Portalon keresztül. Ezért azt javasoljuk, hogy tesztelje a fürt feladatátvétel más módszerekkel. Használjon olyan módszereket, mint a kernel pánikkényszerítése, vagy a hálózatok leállítása vagy az **msl** erőforrás áttelepítése. A részleteket a következő szakaszokban találja. A feltételezés az, hogy a szokásos leállítás történik szándékkal. A szándékos leállítás legjobb példája a karbantartás. Lásd a [részleteket](#planned-maintenance)a Tervezett karbantartás .

A belső tesztelés során a fürt telepítése összezavarodott egy manuális SAP HANA-átvétel után, miközben a fürt karbantartási módban volt. Azt javasoljuk, hogy a fürt karbantartási mód befejezése előtt manuálisan kapcsolja vissza. Egy másik lehetőség, hogy a fürt karbantartási módba való bekerülése előtt elindít egy feladatátvételt. További információ: [Tervezett karbantartás](#planned-maintenance). A SUSE dokumentációja leírja, hogyan állíthatja alaphelyzetbe a fürtöt ily módon a **crm** paranccsal. De a korábban említett megközelítés volt robusztus belső vizsgálat során, és soha nem mutatott semmilyen váratlan mellékhatások.

A **crm áttelepítési** parancs használatakor győződjön meg arról, hogy törli a fürtkonfigurációt. Olyan helymegkötéseket ad hozzá, amelyekről esetleg nem tud. Ezek a megkötések hatással vannak a fürt viselkedésére. További részletek A [tervezett karbantartás](#planned-maintenance).



## <a name="test-system-description"></a>A vizsgálati rendszer leírása

 Az SAP HANA kibővített HA-ellenőrzés és -tanúsítás, a telepítő t használták. Két rendszerből állt, amelyek mindegyike három SAP HANA-csomópontból áll: egy főkiszolgálóból és két dolgozóból. Az alábbi táblázat a virtuális gépek nevét és a belső IP-címeket sorolja fel. Az összes ezt követő ellenőrzési mintát ezeken a virtuális gépeken végezték el. A virtuális gép nevek és IP-címek használatával a parancsmintákban jobban megértheti a parancsokat és azok kimeneteit:


| Csomópont típusa | a virtuális gép neve | IP-cím |
| --- | --- | --- |
| Fő csomópont az 1. | hso-hana-vm-s1-0 | 10.0.0.30 |
| 1-es feldolgozócsomópont az 1. | hso-hana-vm-s1-1 | 10.0.0.31 |
| 2. feldolgozócsomópont az 1. | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Fő csomópont a 2. | hso-hana-vm-s2-0 | 10.0.0.40 |
| 1-es feldolgozócsomópont a 2. | hso-hana-vm-s2-1 | 10.0.0.41 |
| 2. dolgozócsomópont a 2. | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Többségi gyártó csomópont | hso-hana-dm | 10.0.0.13 |
| SBD eszközkiszolgáló | hso-hana-sbd | 10.0.0.19 |
| | | |
| NFS-kiszolgáló 1 | hso-nfs-vm-0 | 10.0.0.15 |
| NFS-kiszolgáló 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Több alhálózat és virtuális adapterek

Az SAP HANA hálózati javaslatait követve három alhálózat jött létre egy Azure virtuális hálózaton belül. Az SAP HANA horizontális felskálázást az Azure-ban nem megosztott módban kell telepíteni. Ez azt jelenti, hogy minden csomópont helyi lemezköteteket használ a **/hana/data** és a **/hana/log kapcsolóhoz.** Mivel a csomópontok csak helyi lemezköteteket használnak, nem szükséges külön alhálózatot definiálni a tároláshoz:

- 10.0.2.0/24 az SAP HANA internode kommunikációhoz
- 10.0.1.0/24 az SAP HANA rendszer replikációjához (HSR)
- 10.0.0.0/24 minden másért

A több hálózat használatával kapcsolatos SAP HANA-konfigurációról az [SAP HANA global.ini](#sap-hana-globalini)című témakörben talál további információt.

A fürt minden virtuális gépe három virtuális hálózati adaptert, amelyek megfelelnek az alhálózatok száma. [Linuxos virtuális gép létrehozása az Azure-ban több hálózati csatolókártyával][azure-linux-multiple-nics] egy linuxos virtuális gép üzembe helyezésekor az Azure-beli potenciális útválasztási problémát ismerteti. Ez a konkrét útválasztási cikk csak több virtuális adapterek használatára vonatkozik. A problémát az SUSE alapértelmezés szerint megoldja az SLES 12 SP3 szervizcsomagban. További információ: [Multi-NIC with cloud-netconfig in EC2 and Azure][suse-cloud-netconfig].


Annak ellenőrzéséhez, hogy az SAP HANA megfelelően van-e konfigurálva több hálózat használatára, futtassa a következő parancsokat. Először ellenőrizze az operációs rendszer szintjén, hogy mindhárom belső IP-cím mindhárom alhálózat aktív.First check on the OS level that all three internal IP addresses for all three subnets are active. Ha az alhálózatokat különböző IP-címtartományokkal definiálta, a következő parancsokat kell módosítania:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

A következő mintakimenet a 2. Három különböző belső IP-címet láthat az eth0, eth1 és eth2 címekről:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Ezután ellenőrizze az SAP HANA portok a névkiszolgáló és a HSR. AZ SAP HANA-nak figyelnie kell a megfelelő alhálózatokat. Az SAP HANA-példány számától függően módosítania kell a parancsokat. A tesztrendszer esetében a példányszám **00**volt. A használt portok megmásának többféleképpen is megtudódnak. 

A következő SQL utasítás a példányazonosítót, a példányszámot és egyéb információkat adja vissza:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

A megfelelő portszámok megkereséséhez keresse meg például a HANA Studio-ban a **Konfiguráció** csoportban vagy egy SQL utasításon keresztül:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Az SAP szoftververemben használt összes port megkereséséhez, beleértve az SAP HANA-t is, keressen [az összes SAP-termék TCP/IP portjai között.][sap-list-port-numbers]

Az SAP HANA 2.0 tesztrendszerben a **00** példányszámát tekintve a névkiszolgáló portszáma **30001.** A HSR metaadat-kommunikáció portszáma **40002**. Az egyik lehetőség az, hogy jelentkezzen be egy munkavégző csomópont, majd ellenőrizze a fő csomópont szolgáltatások. Ebben a cikkben ellenőriztük a 2-es telepen a 2-es feldolgozócsomópontot, amint megpróbál csatlakozni a fő csomóponthoz a 2.

Ellenőrizze a névkiszolgáló portját:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Annak bizonyítására, hogy az internode kommunikáció a **10.0.2.0/24**alhálózatot használja, az eredménynek a következő mintakimenethez kell hasonlítania.
Csak a **10.0.2.0/24** alhálózaton keresztüli kapcsolat nak kell sikeresnek lennie:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Most ellenőrizze a HSR port **40002:**

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Annak bizonyítására, hogy a HSR-kommunikáció **a 10.0.1.0/24**alhálózatot használja, az eredménynek a következő mintakimenethez kell hasonlítania.
Csak a **10.0.1.0/24** alhálózaton keresztüli kapcsolat nak kell sikeresnek lennie:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync között


A **corosync** konfigurációs fájlnak helyesnek kell lennie a fürt minden csomópontján, beleértve a többségi gyártó csomópontot is. Ha egy csomópont fürtillesztése nem a várt módon működik, hozza létre vagy másolja **az /etc/corosync/corosync.conf** kapcsolót manuálisan az összes csomópontra, és indítsa újra a szolgáltatást. 

A **corosync.conf** tartalma a tesztrendszerből egy példa.

Az első szakasz **totem**, afürt [telepítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation)11. Figyelmen kívül hagyhatja a **mcastaddr**értékét. Csak tartsa meg a meglévő bejegyzést. A **token** és a **konszenzus** bejegyzéseit a [Microsoft Azure SAP HANA dokumentációjának][sles-pacemaker-ha-guide]megfelelően kell beállítani.

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

A második szakasz, **a naplózás**nem változott a megadott alapértelmezett értékekhez:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

A harmadik szakasz a **csomópontot mutatja.** Minden csomópont a klaszter meg kell jelenniük a **nodeid:**

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

Az utolsó szakaszban a **kvórumban**fontos, hogy helyesen állítsa be **expected_votes** értékét. A csomópontok számának kell lennie, beleértve a többségi gyártó csomópontot is. És az érték **two_node** kell **0**. Ne távolítsa el teljesen a bejegyzést. Csak állítsa az értéket **0- ra.**

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Indítsa újra a szolgáltatást **systemctl segítségével:**

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD eszköz

Az SBD-eszközök beállítása egy Azure virtuális gépen az [SBD-kerítés](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing)ben található.

Először ellenőrizze az SBD-kiszolgáló virtuális gépét, ha a fürt minden csomópontja acl-bejegyzéseket tartalmazza. Futtassa a következő parancsot az SBD-kiszolgáló virtuális gépén:


<pre><code>
targetcli ls
</code></pre>


A tesztrendszeren a parancs kimenete a következő mintához hasonlóan néz ki. Az **iqn.2006-04.hso-db-0.local:hso-db-0** típusú ACL-neveket a virtuális gépeken megfelelő kezdeményezőnévként kell megadni. Minden virtuális gépnek szüksége van egy másikra.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Ezután ellenőrizze, hogy a kezdeményező nevei az összes virtuális gépen eltérőek-e, és megfelelnek-e a korábban megjelenített bejegyzéseknek. Ez a példa az 1-es feldolgozócsomóponttól származik az 1.

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

A kimenet a következő mintához hasonlóan néz ki:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Ezután ellenőrizze, hogy a **felderítés** megfelelően működik-e. Futtassa a következő parancsot minden fürtcsomóponton az SBD-kiszolgáló virtuális gépének IP-címével:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

A kimenetnek a következő mintához hasonlóan kell kinéznie:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

A következő próbapont annak ellenőrzése, hogy a csomópont látja-e az SDB-eszközt. Ellenőrizze, hogy minden csomóponton, beleértve a többségi gyártó csomópont:

<pre><code>
lsscsi | grep dbhso
</code></pre>

A kimenetnek a következő mintához hasonlóan kell kinéznie. A nevek azonban eltérhetnek. Az eszköz neve is változhat a virtuális gép újraindítása után:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

A rendszer állapotától függően néha segít az iSCSI-szolgáltatások újraindításában a problémák megoldása érdekében. Ezután futtassa le a következő parancsokat:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Bármely csomópontról ellenőrizheti, hogy minden csomópont **tiszta-e.** Győződjön meg arról, hogy a megfelelő eszköznevet használja egy adott csomóponton:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

A kimenetnek a fürt minden csomópontján meg kell **jelennie:**

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Egy másik SBD-ellenőrzés az **sbd** parancs **kiírási** lehetősége. Ebben a minta parancs és kimenet a többségi gyártó csomópont, az eszköz neve **volt sdd**, nem **sdm:**

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

A kimenetnek az eszköz neve mellett minden csomóponton ugyanúgy kell kinéznie:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

Még egy ellenőrzés az SBD-hez az a lehetőség, hogy üzenetet küldjön egy másik csomópontnak. Ha üzenetet szeretne küldeni a 2-es feldolgozócsomópontnak a 2.

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

A cél virtuális gép oldalán, **hso-hana-vm-s2-2** ebben a példában a következő bejegyzést találja a **/var/log/messages-ben:**

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Ellenőrizze, hogy az **/etc/sysconfig/sbd** bejegyzések megfelelnek-e a Pacemaker beállítása az [Azure-ban a SUSE Linux Enterprise Server pacemakerének beállítása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing)című részben szereplő leírásnak. Ellenőrizze, hogy az **/etc/iscsi/iscsid.conf** indítási beállítása automatikus-e.

A következő bejegyzések fontosak az **/etc/sysconfig/sbd kapcsolóban.** Szükség esetén **igazítsa az azonosító** értéket:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Ellenőrizze az indítási beállítást az **/etc/iscsi/iscsid.conf fájlban.** A szükséges beállításnak a dokumentációban ismertetett következő **iscsiadm** paranccsal kellett volna történnie. Ellenőrizze és alakítsa manuálisan **vi,** ha ez más.

Ez a parancs beállítja az indítási viselkedést:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Legyen ez a bejegyzés az **/etc/iscsi/iscsid.conf könyvtárban**:

<pre><code>
node.startup = automatic
</code></pre>

A tesztelés és az ellenőrzés során a virtuális gép újraindítása után az SBD-eszköz bizonyos esetekben már nem volt látható. Eltérés volt az indítási beállítás és a YaST2 által mutatott adatok között. A beállítások ellenőrzéséhez tegye a következőket:

1. Indítsa el a YaST2-t.
2. Válassza a **Hálózati szolgáltatások** lehetőséget a bal oldalon.
3. Görgessen le a jobb oldalon az **iSCSI-kezdeményezőhöz,** és jelölje ki.
4. A **Szolgáltatás** lap következő képernyőjén láthatja a csomópont egyedi kezdeményezőnevét.
5. A kezdeményező neve felett győződjön meg arról, hogy a **Szolgáltatás indítása** érték a Rendszerindításkor értékre van **állítva.**
6. Ha nem, akkor állítsa a **Kézi** indítás helyett a **Rendszerindítás gombra.**
7. Ezután kapcsolja a felső lapot **csatlakoztatott célok ra.**
8. A **Csatlakoztatott célok** képernyőn a mintához hasonló sbd-eszköz bejegyzést kell látnia: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**.
9. Ellenőrizze, hogy az Indítás értéke **indításkor** **van-e** beállítva .
10. Ha nem, válassza a **Szerkesztés** és módosítás lehetőséget.
11. Mentse a módosításokat, és lépjen ki a YaST2-ből.



## <a name="pacemaker"></a>Pacemaker

Miután minden helyesen van beállítva, a következő parancsot futtathatja minden csomóponton a Pacemaker szolgáltatás állapotának ellenőrzéséhez:

<pre><code>
systemctl status pacemaker
</code></pre>

A kimenet tetején kell kinéznie a következő mintát. Fontos, hogy az **Aktív** utáni állapot **betöltöttként** és **aktívként (futásként)** jelenjen meg. **A Loaded** utáni állapotnak **engedélyezettként**kell jelennie.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Ha a beállítás még mindig le van **tiltva,** futtassa a következő parancsot:

<pre><code>
systemctl enable pacemaker
</code></pre>

A Pacemaker összes konfigurált erőforrásának megtekintéséhez futtassa a következő parancsot:

<pre><code>
crm status
</code></pre>

A kimenetnek a következő mintához hasonlóan kell kinéznie. Ez rendben van, hogy a **cln** és **msl** források jelennek meg megállt a többségi gyártó VM, **hso-hana-dm**. Nincs SAP HANA-telepítés a többségi gyártó csomóponton. Így a **cln** és **msl** erőforrások leállítottként jelennek meg. Fontos, hogy a 7-es virtuális gépek helyes teljes számát jelenítse **meg.** A fürt höz kapcsolódó összes virtuális gépnek **online**állapotúnak kell lennie. Az aktuális elsődleges főcsomópontot helyesen kell felismerni. Ebben a példában **ez hso-hana-vm-s1-0:**

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

A Pacemaker egyik fontos jellemzője a karbantartási mód. Ebben a módban módosításokat hajthat végre anélkül, hogy azonnali fürtműveletet váltana ki. Egy példa a virtuális gép újraindítása. Egy tipikus használati eset lenne tervezett operációs rendszer vagy az Azure-infrastruktúra karbantartása. Lásd [Tervezett karbantartás](#planned-maintenance). A Pacemaker karbantartási üzemmódba helyezheti a következő parancsot:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Amikor **crm állapotú**ellenőrzést észlel, a kimenetben azt veszi észre, hogy az összes erőforrás **nem felügyeltként**van megjelölve. Ebben az állapotban a fürt nem reagál semmilyen változásra, például az SAP HANA indítása vagy leállítása.
A következő minta a **crm status** parancs kimenetét mutatja, amíg a fürt karbantartási üzemmódban van:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


Ez a parancsminta bemutatja, hogyan lehet letenni a fürt karbantartási módját:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Egy másik **crm** parancs beszerzi a teljes fürtkonfigurációt egy szerkesztőbe, így szerkesztheti azt. A módosítások mentése után a fürt elindítja a megfelelő műveleteket:

<pre><code>
crm configure edit
</code></pre>

A fürt teljes konfigurációját a **crm show** beállítással tekintse meg:

<pre><code>
crm configure show
</code></pre>



A fürterőforrások meghibásodása után a **crm status** parancs a Sikertelen műveletek listáját jeleníti **meg.** Lásd ennek a kimenetnek a következő mintáját:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

Hibák után fürttisztítást kell végezni. Használja újra a **crm** parancsot, és a **parancsbeállítás-törléssel** megszabaduljon ezektől a sikertelen műveletbejegyzésektől. Nevezze el a megfelelő fürterőforrást a következőképpen:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

A parancsnak a következő példához hasonlóan kell visszaadnia a kimenetet:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Feladatátvétel vagy átvétel

A fontos [megjegyzések](#important-notes)ben tárgyalt módon nem szabad egy szabványos kecses leállítás a fürt feladatátvételi vagy SAP HANA HSR-átvételi tesztelése. Ehelyett azt javasoljuk, hogy indítsa el a kernel pánik, kényszerítse az erőforrás-áttelepítés, vagy esetleg állítsa le az összes hálózatot az operációs rendszer szintjén egy virtuális gép. Egy másik módszer a **crm \<\> node készenléti** parancs. Lásd a [SUSE-dokumentumot][sles-12-ha-paper]. 

A következő három mintaparancs kényszerítheti a fürt feladatátvételét:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

A [tervezett karbantartás](#planned-maintenance), egy jó módja annak, hogy figyelemmel kíséri a fürt tevékenységét futtatni **SAPHanaSR-showAttr** a **watch** command:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Ez is segít, hogy tekintse meg az SAP HANA fekvőállapot egy SAP Python-parancsfájlból érkező állapotát. A fürt telepítője ezt az állapotértéket keresi. Világossá válik, ha egy feldolgozói csomópont meghibásodása. Ha egy feldolgozó csomópont leáll, az SAP HANA nem azonnal a teljes horizontális felskálázási rendszer állapotát adja vissza. 

Vannak újrapróbálkozások a szükségtelen feladatátvételek elkerülése érdekében. A fürt csak akkor **reagál,** ha az állapot ok értékről 4-es értékre változik **,** **hiba**, **1-es**érték. Tehát helyes, ha az **SAPHanaSR-showAttr** kimenete egy offline **állapotú**virtuális gépről jelenít meg. De még nincs aktivitás az elsődleges és másodlagos váltásra. Nem fürttevékenység aktiválódik, amíg az SAP HANA nem ad vissza hibát.

Figyelheti az SAP HANA fekvőállapot állapotát felhasználó ** \<HANA SID\>adm** az SAP Python-parancsfájl az alábbiak szerint. Lehet, hogy alkalmazkodnia kell az útvonalhoz:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

A parancs kimenetének a következő mintához hasonlóan kell kinéznie. Az **Állomás állapota** oszlop és a teljes **állomásállapot** egyaránt fontos. A tényleges kimenet szélesebb, további oszlopokkal.
Annak érdekében, hogy a dokumentumban olvashatóbb legyen a kimeneti táblázat, a jobb oldali oszlopok többsége lekerült a rendszerből:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Van egy másik parancs az aktuális fürttevékenységek ellenőrzésére. Tekintse meg a következő parancsot és a kimeneti farkat az elsődleges hely fő csomópontjának leölése után. Az átmeneti műveletek listáját, például a korábbi másodlagos főcsomópont, **a hso-hana-vm-s2-0** **előléptetése,** mint az új elsődleges főkiszolgáló. Ha minden rendben van, és minden tevékenység befejeződött, az **átmenet összegzése** lista üresnek kell lennie.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Tervezett karbantartás 

Vannak különböző használati esetek, amikor a tervezett karbantartás. Az egyik kérdés az, hogy ez csak az infrastruktúra karbantartása, mint a változások az operációs rendszer szintjén és a lemez konfigurációját, vagy a HANA frissítés.
További információkat a SUSE-ból származó dokumentumokban talál, [például a Nulla állásidő vagy][sles-zero-downtime-paper] az [SAP HANA SR teljesítményoptimalizált forgatókönyvből.][sles-12-for-sap] Ezek a dokumentumok olyan mintákat is tartalmaznak, amelyek bemutatják az elsődleges manuális áttelepítését.

Intenzív belső tesztelést végeztek az infrastruktúra karbantartásának használati esetének ellenőrzésére. Az elsődleges áttelepítéssel kapcsolatos problémák elkerülése érdekében úgy döntöttünk, hogy mindig áttelepítjük az elsődleges állapotot, mielőtt egy fürtet karbantartási módba helyeznénk. Így nem szükséges, hogy a fürt felejtse el a korábbi helyzetet: melyik oldal volt az elsődleges, és melyik volt másodlagos.

E tekintetben két különböző helyzet van:

- A jelenlegi másodlagos karbantartás **tervezett karbantartása**. Ebben az esetben csak helyezze a fürt karbantartási módba, és végezze el a munkát a másodlagos anélkül, hogy a fürt.

- A jelenlegi elsődleges karbantartás **tervezett karbantartása**. Annak érdekében, hogy a felhasználók továbbra is dolgozhassanak a karbantartás során, ki kell kényszerítenie a feladatátvételt. Ezzel a megközelítéssel el kell indítania a fürt feladatátvételpacemaker, és nem csak az SAP HANA HSR szinten. A pacemaker beállítása automatikusan elindítja az SAP HANA átvétele. A feladatátvételt is el kell végeznie, mielőtt a fürtet karbantartási módba helyezne.

A jelenlegi másodlagos telephelykarbantartási eljárása a következő:

1. Helyezze a fürtöt karbantartási módba.
2. Végezze el a munkát a másodlagos helyszínen. 
3. A fürt karbantartási módjának leállítása.

A jelenlegi elsődleges telephely karbantartási eljárása összetettebb:

1. Manuálisan indítsa el a feladatátvételi vagy SAP HANA-átvétel egy pacemaker erőforrás-áttelepítés keresztül. Lásd a következő részleteket.
2. Sap HANA a korábbi elsődleges hely lesz leállítva a fürt telepítője.
3. Helyezze a fürtöt karbantartási módba.
4. A karbantartási munka elvégzése után regisztrálja az előző elsődleges, mint az új másodlagos hely.
5. Tisztítsa meg a fürtkonfigurációt. Lásd a következő részleteket.
6. A fürt karbantartási módjának leállítása.


Erőforrás áttelepítése bejegyzést ad a fürtkonfigurációhoz. Egy példa a feladatátvétel kényszerítése. A karbantartási mód befejezése előtt meg kell tisztítania ezeket a bejegyzéseket. Lásd a következő mintát.

Először kényszerítse ki a fürt feladatátvételt az **msl** erőforrás nak az aktuális másodlagos főcsomópontra való áttelepítésével. Ez a parancs figyelmeztetést ad a **mozgáskorlátozás** létrehozásáról:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Ellenőrizze a feladatátvételi folyamatot az **SAPHanaSR-showAttr**paranccsal. A fürt állapotának figyeléséhez nyisson meg egy dedikált rendszerhéj-ablakot, és indítsa el a parancsot az **órával:**

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

A kimenetnek meg kell jelennie a manuális feladatátvételt. A korábbi másodlagos **főcsomópontot előléptették**ebben a mintában **hso-hana-vm-s2-0**néven. A korábbi elsődleges hely **lss** leállt, **a** korábbi elsődleges főcsomópont 1-es értéke **hso-hana-vm-s1-0:** 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

A fürt feladatátvétele és az SAP HANA átvétele után helyezze a fürtkarbantartási üzemmódba a Pacemaker ben [leírtak](#pacemaker)szerint.

Az **SAPHanaSR-showAttr** és **crm állapot** parancs ad semmit az erőforrás-áttelepítés által létrehozott korlátozásokról. A megkötések láthatóvá tétele egyik lehetősége a fürt teljes erőforrás-konfigurációjának megjelenítése a következő paranccsal:

<pre><code>
crm configure show
</code></pre>

A fürtkonfiguráción belül talál egy új helymegkötést, amelyet a korábbi manuális erőforrás-áttelepítés okoz. Ez a példabejegyzés **a hely meghatározással kezdődik:**

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Sajnos ezek a korlátozások hatással lehetnek a fürt általános viselkedésére. Ezért kötelező újra eltávolítani őket, mielőtt az egész rendszert visszahoznád. Az **unmigrate** paranccsal a korábban létrehozott helymegkötések megtisztíthatók. Az elnevezés egy kicsit zavaros lehet. Nem próbálja meg az erőforrást visszatelepíteni az eredeti virtuális gépre, amelyről átlett telepítve. Csak eltávolítja a helymegkötéseket, és a megfelelő információkat is visszaadja a parancs futtatásakor:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

A karbantartási munka végén leállítja a fürt karbantartási módját a [Pacemaker](#pacemaker).



## <a name="hb_report-to-collect-log-files"></a>hb_report naplófájlok gyűjtésének

A Szívritmus-szabályozó fürtproblémáinak elemzéséhez hasznos, és a SUSE-támogatás is kéri a **hb_report** segédprogram futtatásához. Összegyűjti az összes fontos naplófájlt, amire szüksége van a történtek elemzéséhez. Ez a mintahívás egy kezdő és záró időpontot használ, ahol egy adott incidens történt. Lásd [még: Fontos megjegyzések:](#important-notes)

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

A parancs megmondja, hová tette a tömörített naplófájlokat:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Ezután kibonthatja az egyes fájlokat a szabványos **tar** paranccsal:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Ha megnézed a kibontott fájlokat, megtalálod az összes naplófájlt. Legtöbbjük külön könyvtárakba került a fürt minden csomópontjára vonatkozóan:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


A megadott időtartományon belül az aktuális főcsomópont **hso-hana-vm-s1-0** meghalt. Az eseményhez kapcsolódó bejegyzéseket a **journal.log fájlban**találja:

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Egy másik példa a pacemaker naplófájlja a másodlagos főkiszolgálón, amely az új elsődleges főkiszolgálóvá vált. Ez a részlet azt mutatja, hogy a megölt elsődleges főcsomópont állapota **offline**állapotú lett:

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


A következő részletek az SAP HANA **global.ini** fájlból származnak a fürt 2.000. Ebben a példában az SAP HANA internode kommunikáció és a HSR különböző hálózatainak használatával kapcsolatban a állomásnév-feloldási bejegyzések láthatók:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

A fürtmegoldás olyan böngészőfelületet biztosít, amely grafikus felhasználói felületet biztosít azoknak a felhasználóknak, akik a menüket és a grafikát részesítik előnyben, mint a rendszerhéj szintjén lévő összes parancsot.
A böngészőfelület használatához ** \<\> ** cserélje le a csomópontot egy tényleges SAP HANA-csomópontra a következő URL-ben. Ezután adja meg a fürt (felhasználói fürt) hitelesítő **adatait:**

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Ez a képernyőkép a fürt irányítópultját mutatja:


![Sólyomfürt irányítópultja](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Ez a példa a fürterőforrás-áttelepítés által okozott helykorlátokat mutatja be a [Tervezett karbantartás magyarázata szerint:](#planned-maintenance)


![Hawk lista korlátok](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Azt is feltöltheti a **hb_report** kimenet Hawk a **történelem**alatt , az alábbiak szerint jelenik meg. A naplófájlok gyűjtéséhez hb_report: 

![Hawk feltölteni hb_report kimenet](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Az **Előzménykezelőben**a hb_report **kimenetben** szereplő összes fürtáttűnát átveheti:

![Hawk átmenetek a hb_report kimenet](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Ez az utolsó képernyőkép egyetlen átmenet **Részletek** szakaszát jeleníti meg. A fürt egy elsődleges főcsomópont-összeomlásra, **hso-hana-vm-s1-0**csomópontra reagált. Ez most előmozdítása a másodlagos csomópont, mint az új mester, **hso-hana-vm-s2-0:**

![Hawk egyetlen átmenet](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>További lépések

Ez a hibaelhárítási útmutató ismerteti a magas rendelkezésre állású SAP HANA egy kibővített konfigurációban. Az adatbázis mellett egy másik fontos összetevő egy SAP-környezetben az SAP NetWeaver verem. Ismerje meg [az SAP NetWeaver magas rendelkezésre állását a SUSE Enterprise Linux Server t használó Azure virtuális gépeken.][sap-nw-ha-guide-sles]

