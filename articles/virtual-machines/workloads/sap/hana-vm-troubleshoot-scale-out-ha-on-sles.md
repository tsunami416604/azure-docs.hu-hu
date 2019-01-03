---
title: Az SAP HANA 2.0 kibővített HSR-támasztja beállításának SLES 12 SP3 az Azure-beli virtuális gépek hibaelhárítása |} A Microsoft Docs
description: Útmutató a ellenőrzése és a egy összetett SAP HANA kibővített magas rendelkezésre állású konfigurációban SAP HANA rendszer replikációs (HSR) és az Azure virtual machines szolgáltatásban futó SLES 12 SP3 támasztja alapján hibaelhárítása
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: fb4fed2aa6b80ceb37dde1205996a16f0c30bdd4
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994714"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Győződjön meg arról, és az SAP HANA kibővített magas rendelkezésre állású telepítés SLES 12 SP3 hibaelhárítása 

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


Ez a cikk segít az SAP HANA horizontális felskálázás az Azure-beli virtuális gépek (VM) futtató támasztja fürtkonfigurációban ellenőrzése. A fürt beállítása az SAP HANA rendszer replikációs (HSR) együtt lehetett megtenni, és a SUSE RPM csomag SAPHanaSR – horizontális Felskálázás. Az összes teszt csak SUSE SLES 12 SP3 elkészült. A cikk szakaszok különböző területekre terjed ki, és Példaparancsok és excerpts konfigurációs fájlokból. Javasoljuk, hogy ezek a minták ellenőrizze-e, és ellenőrizze az egész fürt beállítása módszerként.



## <a name="important-notes"></a>Fontos megjegyzések

SAP HANA horizontális felskálázás az SAP HANA-Rendszerreplikálást és támasztja együtt az összes tesztelése megtörtént az SAP HANA 2.0-val csak. Az operációs rendszer verzióját az SAP-alkalmazások SUSE Linux Enterprise Server 12 SP3 volt. A legfrissebb RPM-csomagot, SAPHanaSR – horizontális Felskálázás, a SUSE, az támasztja fürt beállításához használt.
Közzétett SUSE egy [részletes leírása a teljesítményre optimalizált telepítő][sles-hana-scale-out-ha-paper].

Horizontális felskálázás az SAP HANA által támogatott virtuális gépek típusainak, ellenőrizze a [SAP HANA-tanúsítvánnyal rendelkező IaaS directory][sap-hana-iaas-list].

Az SAP HANA horizontális felskálázás több alhálózatok és a Vnic és HSR beállításával együtt egy technikai probléma történt. A kötelező használja a legújabb javításokat a SAP HANA 2.0 ahol probléma lett kijavítva. Az alábbi SAP HANA-verziók támogatottak: 

* rev2.00.024.04 vagy újabb 
* rev2.00.032 vagy újabb

SUSE támogatásra van szüksége, kövesse ezt [útmutató][suse-pacemaker-support-log-files]. Az SAP HANA magas rendelkezésre állású (HA) fürttel kapcsolatos összes információt gyűjteni, a cikkben leírtak szerint. SUSE IT-részlege ezeket az adatokat további elemzés céljából.

Belső tesztjei során a fürt beállítása okozhatott, a normál virtuális gép leállításának az Azure Portalon keresztül. Ezért azt javasoljuk, hogy más módokon fürt-feladatátvétel tesztelése. Módszerekkel például újraindítás kernelpánikot, vagy állítsa le a hálózatok vagy áttelepíteni a **msl** erőforrás. Az alábbi szakaszok a részletek megtekintéséhez. Feltételezzük, hogy a standard szintű leállás történik szándékát. A legjobb példa egy szándékos leállás van a karbantartás céljából. A részleteket a [tervezett karbantartás](#planned-maintenance).

Emellett belső tesztjei során a fürt beállítása zavart okozhatott egy manuális SAP HANA átvétele után közben a fürt karbantartási módban. Azt javasoljuk, hogy váltson át azt vissza újra manuálisan a fürt karbantartási mód leállítása előtt. Egy másik lehetőség, hogy a feladatátvétel előtt, a fürt karbantartási módba. További információkért lásd: [tervezett karbantartás](#planned-maintenance). A SUSE dokumentáció azt ismerteti, hogyan segítségével visszaállíthatja a fürt ezzel a módszerrel a **crm** parancsot. Azonban az említett módszer korábban volt belső tesztjei során a nagy teljesítményű, és soha nem kimutatta, minden olyan váratlan mellékhatással.

Ha a **crm áttelepítése** parancshoz, ügyeljen arra, hogy a fürt konfiguráció tisztítása. Hozzáadja a helyre vonatkozó megkötések, amelyek esetleg nem is tud. Ezek a korlátozások hatással lehet a fürt működését. További részletek az [tervezett karbantartás](#planned-maintenance).



## <a name="test-system-description"></a>Tesztelje a rendszer leírása

 Az SAP HANA kibővített magas rendelkezésre ÁLLÁS ellenőrzése és a minősítési egy beállítás lett megadva. Azt a két rendszer három csomóponttal SAP HANA minden állt: egy főkiszolgálóval és két feldolgozó. A következő tábla listák virtuális gép nevét és a belső IP-címek. Hajtsa végre az összes ellenőrzés mintát elkészült ezeken a virtuális gépeken. A parancs minták ezen virtuális gépek nevét és IP-címek segítségével jobban megérthetők a parancsok és a kibocsátásukra:


| Csomópont típusa | a virtuális gép neve | IP-cím |
| --- | --- | --- |
| 1 helyet a fő csomópont | hso-hana-virtuálisgép-s1-0 | 10.0.0.30 |
| 1 1 helyet a munkavégző csomópont | hso-hana-virtuálisgép-s1-1 | 10.0.0.31 |
| A helyen 1 2 feldolgozó csomóponton | hso-hana-virtuálisgép-s1 – 2 | 10.0.0.32 |
| | | |
| A főcsomópont 2 helyen található | hso-hana-virtuálisgép-s2-0 | 10.0.0.40 |
| 1, 2 helyen található munkavégző csomópont | hso-hana-virtuálisgép-s2-1 | 10.0.0.41 |
| A hely 2 2 feldolgozó csomóponton | hso-hana-virtuálisgép-s2-2  | 10.0.0.42 |
| | | |
| Csomóponttöbbséget használó készítői | hso-hana-dm | 10.0.0.13 |
| SBD eszköz kiszolgáló | hso-hana-sbd | 10.0.0.19 |
| | | |
| NFS-kiszolgálószolgáltatás 1 | hso-nfs-vm-0 | 10.0.0.15 címet |
| NFS-kiszolgáló 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Alhálózatok és a rendszer

SAP HANA-hálózatokra vonatkozó javaslatok, az alábbi három alhálózatot létrehozott egy Azure virtuális hálózaton belül. SAP HANA horizontális felskálázás az Azure-ban telepíthetők a makrókban módban van. Azt jelenti, hogy minden csomópont használ a helyi köteteket **/hana/adatok** és **/hana/log**. Mivel a csomópontok csak a helyi köteteken, nem elengedhetetlen egy külön alhálózatot tárolási meghatározásához:

- SAP HANA-csomópontok kommunikációhoz 10.0.2.0/24
- az SAP HANA-Rendszerreplikálást (HSR) 10.0.1.0/24
- minden más 10.0.0.0/24

Több hálózat használatát az SAP HANA-konfigurációval kapcsolatos további információkért lásd: [SAP HANA global.ini](#sap-hana-globalini).

A fürt minden virtuális gép rendelkezik három Vnic, amelyek megfelelnek az alhálózatok számát. [Hogyan hozhat létre Linux rendszerű virtuális gép az Azure-ban több hálózati kártyák] [ azure-linux-multiple-nics] ismerteti az Azure útválasztási problémát észlelt potenciális, Linux rendszerű virtuális gép üzembe helyezésekor. A megadott útválasztási cikk vonatkozik, csak a több virtuális hálózati adapter használatát. A probléma az SLES 12 SP3 alapértelmezés szerint SUSE kiküszöbölni. További információkért lásd: [a felhő-netconfig EC2 és az Azure a több hálózati][suse-cloud-netconfig].


Győződjön meg arról, hogy az SAP HANA megfelelően van konfigurálva több hálózat használatához, futtassa a következő parancsokat. Először ellenőrizze, hogy az összes három belső IP-címek mindhárom alhálózat aktívak az operációs rendszer szintjén. Ha másik IP-címtartományok az alhálózat definiált, a parancsok alkalmazkodni rendelkezik:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Az alábbi kimeneti példa származik, a második munkavégző csomópont 2 helyen található. Három különböző belső IP-címeket a eth0 eth1 és eth2 tekintheti meg:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Ezután ellenőrizze a kiszolgáló nevét és a HSR a SAP HANA-portokat. SAP HANA figyelnie kell a megfelelő alhálózatokat. SAP HANA-példányok számától függően kell igazítani a parancsokat. A példány számát volt a tesztrendszerhez **00**. Ismerje meg, milyen portokat használják a különböző módja van. 

A következő SQL-utasítást a Példányazonosító példányszámának és egyéb információkat adja vissza:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

A megfelelő portszámokat megkereséséhez megtalálhatja, például, HANA Studio alatt **konfigurációs** vagy egy SQL-utasítás segítségével:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

A SAP software kifejlesztése, amilyen az SAP HANA minden használt port megkereséséhez keressen [TCP/IP-port minden SAP-termék][sap-list-port-numbers].

A példány számát adott **00** a SAP HANA 2.0 teszt rendszeren, a port számát a névkiszolgáló van **30001**. A portszám HSR-metaadatok kommunikációhoz **40002**. Az egyik lehetőség, hogy jelentkezzen be a munkavégző csomópont, és ezután ellenőrizze a fő csomópont szolgáltatásokat. Ebben a cikkben azt ellenőrizni a munkavégző csomópont 2 helyen szeretne csatlakozni a fő csomóponttal, a hely 2 2.

Ellenőrizze a nevet kiszolgáló portja:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Hogy igazolja, hogy a csomópontok kommunikációs alhálózatot használ **10.0.2.0/24**, az eredmény az alábbi kimeneti példa hasonlóan kell kinéznie.
Csak a keresztül alhálózati kapcsolat **10.0.2.0/24** sikeres legyen:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Most ellenőrizze a HSR-portra **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Hogy igazolja, hogy a HSR-kommunikáció alhálózatot használ **10.0.1.0/24**, az eredmény az alábbi kimeneti példa hasonlóan kell kinéznie.
Csak a keresztül alhálózati kapcsolat **10.0.1.0/24** sikeres legyen:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


A **corosync** csak akkor lehet helyes, a fürtben, beleértve a csomóponttöbbség maker minden csomóponton rendelkezik a konfigurációs fájl. Ha a fürthöz való csatlakozást, a csomópont nem a várt módon működik, hozzon létre, vagy másolja **/etc/corosync/corosync.conf** manuálisan be minden csomópontján, és indítsa újra a szolgáltatást. 

Tartalmának **corosync.conf** a vizsgálatot a rendszer egy példa.

Az első szakasz **totem**leírtak szerint [telepítési fürt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation), 11. lépés. Az érték figyelmen kívül hagyhatja **mcastaddr**. Csak tartani a meglévő bejegyzést. A bejegyzéseket **token** és **konszenzus** kell állítani a következők szerint [a Microsoft Azure az SAP HANA-dokumentáció][sles-pacemaker-ha-guide].

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

A második szakasz **naplózás**, nem módosult az adott alapértelmezett értéket:

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

A harmadik szakasz azt mutatja be a **csomópontlista**. A fürt minden csomópontján kell jelenik meg a **nodeid**:

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

Az utolsó szakaszban **kvórum**, fontos, hogy értékét **expected_votes** megfelelően. Többek között a csomóponttöbbség maker csomópontok kell lennie. És az értéke **two_node** kell lennie **0**. A bejegyzés ne távolítsa el teljesen. Csak állítsa az értékét **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Indítsa újra a szolgáltatást keresztül **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD eszköz

Hogyan állítható be egy Azure-beli virtuális gépen egy SBD eszköz leírt [SBD szintaxiskiemeléshez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing).

Ha a fürt minden csomópontján az ACL-bejegyzések először ellenőrizze a virtuális gép SBD kiszolgálón. Futtassa a következő parancsot a virtuális gép SBD kiszolgálón:


<pre><code>
targetcli ls
</code></pre>


A tesztgépen a parancs kimenete az alábbi mintához hasonlóan néz ki. ACL-neveket például **iqn.2006-04.hso-db-0.local:hso-db-0** , meg kell adni a megfelelő kezdeményező nevét, a virtuális gépeken. Minden virtuális Gépnek szüksége van, egy másikat.

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

Ezután ellenőrizze, hogy a kezdeményező nevek az összes virtuális gép különböző, és felel meg a korábban bemutatott bejegyzések. Ebben a példában az 1 1 helyet a munkavégző csomópont van:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

A kimenet az alábbi mintához hasonlóan néz ki:

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

Ezután ellenőrizze, hogy a **felderítési** megfelelően működik-e. Futtassa a következő parancsot minden egyes fürtcsomóponton SBD kiszolgáló virtuális gép IP-cím használatával:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

A kimenet az alábbi mintához hasonlóan kell kinéznie:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

A következő megvalósíthatósági példában, hogy ellenőrizze, hogy a csomópont a SDB eszköz látja-e. Ellenőrizze azt minden csomóponton, beleértve a csomóponttöbbség készítő:

<pre><code>
lsscsi | grep dbhso
</code></pre>

A kimenetnek az alábbi mintához hasonlóan kell kinéznie. Azonban a nevei eltérőek lehetnek. Az eszköz nevét is módosíthatja a előfordulhat, hogy a virtuális gép újraindítása után:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

A rendszer állapotától függően néha segít a problémák megoldása az iSCSI-szolgáltatások újraindítása. Ezután futtassa le a következő parancsokat:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Bármely olyan csomópontról ellenőrizheti, ha vannak-e az összes csomópont **törölje a jelet**. Győződjön meg arról, hogy egy adott csomóponton használja a megfelelő eszköz neve:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Meg kell jelennie a kimeneti **törölje a jelet** a fürt minden csomópontján:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Egy másik SBD ellenőrizze-e a **memóriakép** lehetőséget a **sbd** parancsot. A mintául szolgáló parancs és a csomóponttöbbség maker kimenete az eszköznév lett **sdd**, nem **sdm**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

A kimenet az eszköznév, szereplőkkel kell kinéznie minden csomóponton ugyanaz:

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

Egy további ellenőrzési SBD a arra, hogy egy másik csomópontra való üzenetküldéshez. Egy üzenet küldéséhez 2 helyen 2 feldolgozó csomópont, futtassa a következő parancsot a munkavégző csomóponton 1 2 helyen található:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

A cél virtuális gép oldalán **hso-hana-virtuálisgép-s2-2** ebben a példában találja a következő bejegyzést **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Ellenőrizze, hogy a bejegyzések **/etc/sysconfig/sbd** felel meg a leírását [támasztja a SUSE Linux Enterprise Server az Azure-beli beállítása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing). Ellenőrizze, hogy az indítási beállítása **/etc/iscsi/iscsid.conf** automatikusra van állítva.

A következő bejegyzések fontosak az **/etc/sysconfig/sbd**. Alkalmazkodjon a **azonosító** értékét, ha szükséges:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Ellenőrizze az indítási beállítások **/etc/iscsi/iscsid.conf**. A kötelező beállítás azért kell történt, az alábbi **iscsiadm** parancs, a dokumentációjában leírt. Ellenőrizze és alkalmazkodnak hozzá kézzel a **vi** Ha eltérő.

Ez a parancs beállítja az indítási viselkedést:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Győződjön meg arról, a tételben szereplő **/etc/iscsi/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Tesztelés és a egy virtuális gép az újraindítás után az ellenőrzés során a SBD eszköz nem jelenik többé meg bizonyos esetekben. Hiba történt az indítási beállítások és YaST2 mutatott közötti eltérést. Ellenőrizze a beállításokat, hajtsa végre az alábbi lépéseket:

1. Indítsa el a YaST2.
2. Válassza ki **hálózati szolgáltatások** bal oldalán.
3. Görgessen le, jobb oldalán **iSCSI-kezdeményező** , és jelölje ki.
4. A következő képernyőn alatt a **szolgáltatás** lapon egyedi kezdeményező neve a csomópont megjelenik.
5. A kezdeményező neve felett ügyeljen arra, hogy a **szolgáltatás indítása** értéke **amikor rendszerindítását**.
6. Ha nem érhető el, majd beállíthatja azt a **amikor rendszerindítását** helyett **manuálisan**.
7. Ezután váltson a felső lapon **csatlakoztatott tárolók**.
8. Az a **csatlakoztatott tárolók** képernyőn egy bejegyzés az SBD eszköz példához hasonlóan kell megjelennie: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**.
9. Ellenőrizze, hogy a **indítási** értéke **az rendszerindításkor**.
10. Ha nem, válassza a **szerkesztése** módosítsa azt.
11. A módosítások mentéséhez és a kilépéshez YaST2.



## <a name="pacemaker"></a>Támasztja

Miután mindent megfelelően van beállítva, a minden csomóponton a támasztja szolgáltatás állapotának ellenőrzéséhez futtathatja a következő parancsot:

<pre><code>
systemctl status pacemaker
</code></pre>

Felső részén a kimenet az alábbi mintához hasonlóan kell kinéznie. Fontos, amely után az állapot **aktív** jelenik meg, mint **betöltött** és **aktív (fut)**. Miután az állapot **Loaded** láthatónak kell lennie **engedélyezve**.

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

Ha a beállítás értéke továbbra is **le van tiltva**, futtassa a következő parancsot:

<pre><code>
systemctl enable pacemaker
</code></pre>

Az összes konfigurált erőforrások támasztja megtekintéséhez futtassa a következő parancsot:

<pre><code>
crm status
</code></pre>

A kimenetnek az alábbi mintához hasonlóan kell kinéznie. Azt már rendben, amely a **cln** és **msl** erőforrások jelennek meg, a legtöbb maker VM, leállította **hso-hana-dm**. Nincs a legtöbb maker csomóponton SAP HANA-telepítés nélkül. Ezért a **cln** és **msl** erőforrások látható módon leállt. Fontos, hogy látható-e a megfelelő teljes száma, a virtuális gépek **7**. A fürt részét képező összes virtuális gép szerepelnie kell a állapotú **Online**. A jelenlegi elsődleges főcsomóponton helyesen ismeri fel. Ebben a példában van **hso-hana-virtuálisgép-s1-0**:

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

Egyik fontos szolgáltatása támasztja karbantartási módban. Ebben a módban azonnali fürt művelet szikrák nélkül végezhet módosításokat. Ilyen például, a virtuális gép újraindítását. Egy tipikus használati eset lenne tervezett operációs rendszer vagy az Azure infrastruktúra-karbantartási teendők. Lásd: [tervezett karbantartás](#planned-maintenance). A következő paranccsal karbantartás módba helyezni támasztja:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Az ellenőrzés **crm állapot**, megfigyelheti, hogy a kimenet megjelölt összes erőforrás **nem felügyelt**. Ebben az állapotban a fürt indítása vagy leállítása az SAP HANA hasonló módosításokat nem reagál.
A következő minta bemutatja a kimenetét a **crm állapot** parancsot, miközben a fürt karbantartási módban van:

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


Ez a parancs a minta bemutatja, hogyan a fürt karbantartási mód befejezéséhez:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Egy másik **crm** parancs lekéri a teljes fürtkonfiguráció-szerkesztő, így azokat. Miután menti a módosításokat, a fürt elindítja a megfelelő műveleteket:

<pre><code>
crm configure edit
</code></pre>

Tekintse meg a teljes fürtkonfigurációnak, használja a **crm show** lehetőséget:

<pre><code>
crm configure show
</code></pre>



Fürterőforrások, hiba után a **crm állapot** parancs listája látható **sikertelen műveletek**. Tekintse meg a kimenet a következő mintát:


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

Szükség egy fürt karbantartási hiba után. Használja a **crm** újra parancsot, és a parancssori kapcsolóval **karbantartása** távolíthatja el ezeket nem sikerült a művelet bejegyzéseket. Fürt megfelelő erőforrása nevezze a következőképpen:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

A parancs a következő mintához hasonló kimenetnek kell visszaadnia:

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



## <a name="failover-or-takeover"></a>Feladatátvétel vagy átvétele

Az [fontos megjegyzések](#important-notes), a feladatátvevő fürt vagy az SAP HANA HSR átvétele teszteléséhez ne használja a standard szabályos leállítást. Ehelyett azt javasoljuk, hogy kernelpánikot aktiválása, kényszerítheti az erőforrás-migrálás, vagy esetleg állítsa le a virtuális gép operációs rendszer szint minden hálózatból elérhető. Egy másik módszer a **crm \<csomópont\> készenléti** parancsot. Tekintse meg a [SUSE dokumentum][sles-12-ha-paper]. 

Az alábbi három Példaparancsok kényszerítheti a feladatátvevő fürt:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Leírtak szerint [tervezett karbantartás](#planned-maintenance), egy jó módszer a fürt tevékenységek figyelését, hogy futtassa **SAPHanaSR-showAttr** az a **watch** parancsot:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Emellett elősegíti, tekintse meg az SAP HANA fekvő állapot származó egy SAP Python-szkriptet. A fürt beállítása a keresi az állapot értékét. Akkor válik törlése, amikor egy feldolgozó csomópont leáll. Ha egy feldolgozó csomópont leáll, az SAP HANA nem ad vissza hibát a rendszer állapotát, az egész kibővített azonnal. 

Nincsenek az egyes újrapróbálkozások szükségtelen feladatátvételek elkerüléséhez. A fürt viselkedését abban az esetben csak akkor, ha az állapot változik **Ok**, visszatérési érték **4**, az **hiba**, visszatérési érték **1**. Így helyes Ha kimenete **SAPHanaSR-showAttr** jeleníti meg a állapotú virtuális gép **offline**. De még a váltás nincs tevékenység az elsődleges és másodlagos. Nincs fürt tevékenység lekéri indul el, mindaddig, amíg az SAP HANA nem ad vissza hibát.

Nyomon követheti az SAP HANA fekvő állapot felhasználóként  **\<HANA SID\>adm** SAP Python-szkript a következő meghívásával. Szükség lehet a alkalmazkodjon az elérési út:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Ez a parancs kimenete az alábbi mintához hasonlóan kell kinéznie. A **gazdagép állapota** oszlop és az **teljes állomás állapota** egyaránt fontosak. A tényleges kimenete szélesebb körű, további oszlopokat.
Ahhoz, hogy a kimeneti tábla olvashatóbb, ez a dokumentum belül, a rendszer eltávolította a legtöbb oszlopot a jobb oldalon:

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


Van egy másik parancs aktuális fürt tevékenységek ellenőrzéséhez. Tekintse meg a következő parancsot, és a kimeneti végéről után az elsődleges hely a fő csomóponttal lett állítva. Átmenet műveletekhez, például a listája látható **előléptetése** a korábbi másodlagos fő csomóponttal, **hso-hana-virtuálisgép-s2-0**, az új elsődleges Master. Ha mindent rendben, és az összes tevékenység befejeződtek, ez **átmenet összefoglalás** listája tartalmaz üresnek kell lenniük.

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

Esetén, a tervezett karbantartás, nincsenek különböző használati helyzetekhez. Egy kérdésre, hogy csak az infrastruktúra-karbantartási például a módosítások az operációs rendszer szintjén és a lemezkonfiguráció vagy egy HANA frissítés.
Például a SUSE-dokumentumokban további információt is megtalálhatja [felé üzemszünet] [ sles-zero-downtime-paper] vagy [SAP HANA SR teljesítmény optimalizált forgatókönyv] [ sles-12-for-sap]. Ezeket a dokumentumokat a minták azt mutatják be egy elsődleges manuális áttelepítésével is tartalmazhatnak.

Az erős belső tesztjei azért volt szükség, ellenőrizze az infrastruktúra-karbantartási használati eset. Bármely az elsődleges áttelepítésével kapcsolatos problémák elkerülése érdekében azt úgy döntött, hogy mindig egy elsődleges előtt egy fürt karbantartási módba. Ezzel a módszerrel nem elengedhetetlen, hogy a fürt elfelejtette kapcsolatos korábbi helyzet: mely side nem elsődleges és másodlagos lett.

Ebben a tekintetben van két különböző helyzetekben:

- **Tervezett karbantartás az aktuális másodlagos**. Ebben az esetben csak a fürt karbantartás módba helyezni, illetve használhat a munkát a másodlagos anélkül, hogy befolyásolná a fürtöt.

- **Tervezett karbantartás az aktuális elsődleges**. Hogy a felhasználók továbbra is működik a karbantartás során, akkor kell feladatátvételt kényszerít ki egy. Ezzel a módszerrel a fürt feladatátvétele támasztja és nem az SAP HANA HSR szintje indít el kell. A támasztja a telepítő automatikusan elindítja az SAP HANA átvételt. Emellett szüksége ehhez a feladatátvétel előtt, a fürt karbantartási módba.

A jelenlegi másodlagos helyen karbantartási eljárást a következőképpen történik:

1. A fürt karbantartás módba helyezni.
2. A másodlagos helyen a munka elvégzéséhez. 
3. A fürt karbantartási mód befejezéséhez.

A jelenlegi elsődleges helyen karbantartási eljárást a következő összetettebb:

1. Egy feladatátvételi vagy SAP HANA átvétel keresztül támasztja erőforrás-migrálás aktiválása manuálisan. Az alábbi részleteket.
2. A korábbi elsődleges hely az SAP HANA lekérdezi állítsa le a fürt beállítása szerint.
3. A fürt karbantartás módba helyezni.
4. Miután a karbantartási munkát, regisztrálja az előző elsődleges az új másodlagos hely.
5. A Törlés a fürt konfigurációját. Az alábbi részleteket.
6. A fürt karbantartási mód befejezéséhez.


Erőforrás áttelepítése bejegyzés hozzáadása a fürt konfigurációját. Például a rendszer feladatátvételt kényszerít ki. Ezek a bejegyzések karbantartási mód leállítása előtt törölni kell. Tekintse meg a következő mintát.

Először feladatátvételt kényszerít ki egy fürt áttelepítése révén a **msl** erőforrás a jelenlegi másodlagos fő csomóponttal. Ez a parancs figyelmeztetést küld, amely egy **megkötés áthelyezése** lett létrehozva:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Ellenőrizze a parancs használatával a feladatátvételi folyamat **SAPHanaSR-showAttr**. A fürt állapotának figyelése, nyisson meg egy dedikált rendszerhéj ablakot, és indítsa el a parancsot a **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

A kimenet a manuális feladatátvételt kell megjelennie. A korábbi másodlagos fő csomóponttal lett **előléptetett**, ebben a példában **hso-hana-virtuálisgép-s2-0**. A korábbi elsődleges helyén leállt, **licencszolgáltatóval** érték **1** a korábbi elsődleges főcsomópont **hso-hana-virtuálisgép-s1-0**: 

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

A feladatátvevő fürt és az SAP HANA átvétele után a fürt karbantartás módba helyezni leírtak szerint [támasztja](#pacemaker).

A parancsok **SAPHanaSR-showAttr** és **crm állapot** használatbavételben a korlátokat, az erőforrás-migrálás által létrehozott nem jelzik. Ezek a korlátozások láthatóvá az egyik lehetőség, hogy megjelenítése a teljes fürt erőforrás konfigurációját a következő paranccsal:

<pre><code>
crm configure show
</code></pre>

A fürtkonfiguráció között keresse meg egy új földrajzi megszorítás, a korábbi manuális erőforrás-migrálás okozta. Ez a példa a bejegyzés kezdődik **hely cli -**:

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Sajnos az ilyen megkötések hatással lehet az általános fürt működést. Ezért kötelező távolítsa el őket újból a teljes rendszer biztonsági mentése állapotba helyezése előtt. Az a **unmigrate** parancs, lehetséges, a helyre vonatkozó megkötések előtt létrehozott karbantartása. A naming egy kicsit zavaró lehet. Azt nem próbálja meg az erőforrás telepítheti vissza az eredeti virtuális gép áttelepítése történt. Ez csupán eltávolítja a helyre vonatkozó megkötések, és is visszaküldi a megfelelő információkat, a parancs futtatásakor:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

A karbantartási munkát végén, a fürt karbantartási mód leállítása, ahogyan [támasztja](#pacemaker).



## <a name="hbreport-to-collect-log-files"></a>naplófájlokat gyűjthet hb_report

Támasztja fürt problémák elemzéséhez, a hasznos és futtatásához SUSE-támogatást is kért legyen a **hb_report** segédprogramot. Összegyűjti az összes fontos naplófájlokat kell elemeznie, mi történt. Ez a minta-hívás egy kezdő és záró idő használ, ahol az egy adott esemény történt. További tájékoztatás [fontos megjegyzések](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

A parancs arra kéri, ahová a tömörített naplófájlokat:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Bontsa ki az egyes fájlokat, a standard keresztül **tar** parancsot:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Ha megnézi a kibontott fájlokat, keresse meg a naplófájlokat. Ezek többsége fel külön könyvtárakban a fürt minden csomópontján:

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


Az időtartomány lett megadva, a jelenlegi fő csomóponttal belül **hso-hana-virtuálisgép-s1-0** következtében leállt. Ez az esemény vonatkozó bejegyzést talál a **journal.log**:

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

Egy másik példa, a támasztja naplófájlt a másodlagos főkiszolgálón, és az új elsődleges főkiszolgáló vált. A cikkből szerint jeleníti meg, hogy az a leállítva elsődleges főcsomópont lett állítva: **offline**:

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


A következő excerpts vannak az SAP HANA-ból **global.ini** 2 helyhez cluster-fájlt. Ez a példa bemutatja a hostname különböző hálózatokon az SAP HANA-csomópontok kommunikációs és a HSR-névfeloldási bejegyzéseket:

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



## <a name="hawk"></a>HAWK

A megoldást kínál a felhasználók számára, hogy az összes parancs a rendszerhéj szintjén menük és a grafikai inkább grafikus felhasználói Felülettel egy böngésző felületet biztosít.
A böngésző felület használatához cserélje le a **\<csomópont\>** egy tényleges SAP HANA csomóponttal, a következő URL-címben. Adja meg a fürt hitelesítő adatait (felhasználói **fürt**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Ezen a képernyőfelvételen a fürt irányítópultja:


![HAWK fürt irányítópultja](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Ez a példa bemutatja a fürtáttelepítés erőforrás okozta, a helyre vonatkozó megkötések [tervezett karbantartás](#planned-maintenance):


![HAWK lista megkötései](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Emellett feltölthet a **hb_report** Hawk alatt a kimenetet **előzmények**, látható módon. Lásd: [naplófájlokat gyűjthet hb_report](#hbreport-to-collect-log-files): 

![HAWK feltöltési hb_report kimenet](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Az a **előzmények**, majd végigjárni a szereplő összes fürt átmenet a **hb_report** kimenete:

![HAWK átmenetek hb_report kimenet](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

A végső képernyőfelvételen a **részletek** egyetlen átmenet szakaszában. A fürt reagált egy elsődleges fő csomópont-összeomlás, a csomópont **hso-hana-virtuálisgép-s1-0**. Mostantól az új Master, a másodlagos csomópontra való előléptetése **hso-hana-virtuálisgép-s2-0**:

![Egyetlen átmenet HAWK](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>További lépések

Ez a hibaelhárítási útmutató az SAP Hana magas rendelkezésre állású kibővített konfigurációban ismerteti. Az adatbázis mellett egy másik fontos egy olyan SAP-rendszeren összetevője az SAP NetWeaver-verem. Ismerje meg [magas rendelkezésre állás az SAP NetWeaver SUSE Enterprise Linux Server használó Azure virtuális gépeken][sap-nw-ha-guide-sles].

