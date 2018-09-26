---
title: Az SAP HANA 2.0 kibővített HSR-támasztja beállításának SLES 12 SP3 az Azure-beli virtuális gépek hibaelhárítása |} A Microsoft Docs
description: Útmutató a ellenőrzése és a egy összetett SAP HANA SAP HANA rendszer replikációs (HSR) és az Azure virtual machines szolgáltatásban futó SLES 12 SP3 támasztja alapján kibővített magas rendelkezésre állású konfigurációs hibáinak elhárítása
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
ms.openlocfilehash: 6c0d6397246e8b8db1d59c26229e37a722d49f48
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184862"
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


Ez a cikk írásának keresztül segít ellenőrizni a horizontális felskálázás az Azure virtuális gépeken futó SAP HANA támasztja fürtkonfigurációban. A fürt beállítása az SAP HANA rendszer replikációs (HSR) együtt lehetett megtenni, és a SUSE RPM csomag SAPHanaSR – horizontális Felskálázás. Az összes teszt csak SUSE SLES 12 SP3 elkészült. Számos különböző területekre terjed ki, és Példaparancsok és a konfigurációs fájlok excerpts több szakaszra. Ezek a minták használata akkor javasolt, győződjön meg arról, és ellenőrizze az egész fürt beállítása módszerként.



## <a name="important-notes"></a>Fontos megjegyzések

SAP HANA horizontális felskálázás az SAP HANA-Rendszerreplikálást és támasztja együtt az összes tesztelése megtörtént az SAP HANA 2.0-val csak. Az operációs rendszer verziója lett SUSE Linux Enterprise Server 12 SP3 SAP-alkalmazások számára. Emellett a legújabb RPM csomag SAPHanaSR – horizontális Felskálázás SUSE a támasztja fürt beállításához használt.
SUSE közzé a teljesítményre optimalizált beállítás, amely is található részletes leírását [Itt][sles-hana-scale-out-ha-paper]

Virtuális gép esetében, amelyek támogatottak az SAP HANA horizontális felskálázás, ellenőrizze a [SAP HANA-tanúsítvánnyal rendelkező IaaS-könyvtár][sap-hana-iaas-list]

Az SAP HANA horizontális felskálázás több alhálózatok és a Vnic és HSR beállításával együtt egy technikai probléma történt. A kötelező használja az SAP HANA 2.0 legújabb javításokat, ahol probléma van rögzített. Az alábbi SAP HANA-verziók támogatottak: 

**rev2.00.024.04 vagy újabb & rev2.00.032 vagy újabb verziója.**

Abban az esetben meg kell adni a helyzetet, amely a SUSE-támogatása szükséges kövesse ezt [útmutató][suse-pacemaker-support-log-files]. Az SAP HANA magas rendelkezésre ÁLLÁS fürttel kapcsolatos összes információ összegyűjtése, a cikkben leírtak szerint. SUSE IT-részlege ezeket az adatokat további elemzés céljából.

Belső tesztelése során ez történt, hogy a fürt beállítása a normál virtuális gép leállításának az Azure Portalon keresztül okozhatott. Ezért azt javasoljuk, hogy egy fürt feladatátvételi teszt más módszerekkel. Módszerekkel például kernelpánikot kényszerítése vagy állítsa le a hálózatok áttelepítése a **msl** erőforrás (lásd az alábbi szakaszokban szereplő részletek). Feltételezzük, hogy a standard szintű leállás történik szándékát. A legjobb példa egy szándékos leállás karbantartási (a részleteket a a tervezett karbantartással kapcsolatos szakaszt).

Belső tesztelése során ez történt, hogy a fürt beállítása zavart okozhatott egy manuális SAP HANA átvétele után a fürt közben karbantartási módban. Azt javasoljuk, hogy kapcsolja vissza manuálisan újra, a fürt üzemmódját a karbantartás befejezése előtt. Egy másik lehetőség, hogy feladatátvétel előtt a fürt karbantartási módba (lásd a további részletekért tervezett karbantartásának ismertetése). A SUSE dokumentáció azt ismerteti, hogyan alaphelyzetbe állíthatja a fürt ebben a tekintetben a crm paranccsal. Azonban az említett módszer előtt belső tesztelése során, majd soha többé nem kell robusztus tűnt kimutatta, minden olyan váratlan mellékhatással.

Amikor a parancs a crm használatával át, ne hagyja a fürtkonfiguráció karbantartása. Hozzáadja a helyre vonatkozó megkötések, amelyek esetleg nem is tud. Ezek a korlátozások hatással vannak a fürt működését (lásd a szakasz a tervezett karbantartással kapcsolatos további részletekért).



## <a name="test-system-description"></a>Tesztelje a rendszer leírása

SAP HANA kibővített magas rendelkezésre ÁLLÁS ellenőrzése és a egy beállítás lett megadva hitelesítő álló SAP HANA három csomóponttal, amelyek a két rendszer egyes – egy főkiszolgálóval és két feldolgozó. Íme a Virtuálisgép-neveknek és belső IP-címek listáját. Az összes ellenőrzés minta további le elkészült ezeken a virtuális gépeken. Ezek egy virtuális gép neve és IP címeket parancs minták jobb megértése érdekében a parancsok és a kibocsátásukra segítségül.


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

SAP HANA-hálózatokra vonatkozó javaslatok, az alábbi három alhálózatot létrehozott egy Azure virtuális hálózaton belül. Nem megosztott üzemmódban, ami azt jelenti, hogy minden csomópont használ-e a helyi köteteket telepítve van az SAP HANA horizontális felskálázás az Azure-ban **/hana/adatok** és **/hana/log**. Csak a helyi lemez-kötetek használata miatt, akkor nem kell tárolási egy külön alhálózatot adja meg:

- az SAP HANA-csomópontok közötti kommunikáció 10.0.2.0/24
- az SAP HANA-Rendszerreplikálást HSR 10.0.1.0/24
- minden más 10.0.0.0/24

SAP HANA-konfiguráció használatához kapcsolódó információ több hálózat: a szakasz **global.ini** kicsit lentebb.

Három Vnic alhálózatok számát a fürtben lévő összes virtuális Géphez tartozó rendelkezik. [Ez] [ azure-linux-multiple-nics] cikk ismerteti a lehetséges útválasztási problémát észlelt az Azure-ban, Linux rendszerű virtuális gép üzembe helyezésekor. A megadott útválasztási témakör csak a több virtuális hálózati adapter használatát vonatkozik. A probléma az SLES 12 SP3 alapértelmezés szerint SUSE kiküszöbölni. A SUSE kezelésükről szóló cikket ebben a témakörben található [Itt][suse-cloud-netconfig].


Alapszintű ellenőrzés győződjön meg arról, ha az SAP HANA megfelelően van konfigurálva több hálózat használatát, mint csak az alábbi parancsok futtatásával. Első lépéseként egyszerűen ellenőrizni, hogy az összes három belső IP-címek mindhárom alhálózat aktívak az operációs rendszer szintjén. Abban az esetben másik IP-címtartományok az alhálózat definiált kell igazítani a parancsokat:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Íme egy 2 helyen található, a második munkavégző csomópont mintakimenete. Három különböző belső IP-címeket a eth0 eth1 és eth2 tekintheti meg:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Második lépéseként SAP HANA portokat a névkiszolgáló és a HSR ellenőrzése. SAP HANA figyelnie kell a megfelelő alhálózatokat. SAP HANA-példányok számától függően kell igazítani a parancsokat. A példány számát volt a tesztrendszerhez **00**. Döntse el, milyen portokat használják a különböző módja van. 

Alább láthat egy SQL-utasítást, amely Példányazonosító és többek között a példányok számát adja vissza:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

A megfelelő portszámokat megkereséséhez megtalálhatja, például, HANA Studio alatt "**konfigurációs**" vagy egy SQL-utasítás segítségével:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Minden port, a SAP software kifejlesztése, amilyen az SAP HANA használja, amely keresést [Itt][sap-list-port-numbers].

A példány számát adott **00** az SAP HANA 2.0 teszt rendszerben a portnak a számát a névkiszolgáló van **30001**. A portszám HSR meta-data kommunikációhoz **40002**. Az egyik lehetőség, hogy jelentkezzen be a munkavégző csomópont, és ezután ellenőrizze a fő csomópont szolgáltatásokat. Itt az ellenőrzés végzett 2 helyen szeretne csatlakozni a fő csomóponttal, a hely 2 2 feldolgozó csomóponton.

Ellenőrizze a névkiszolgáló-port:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Az eredmény így kell kinéznie a igazolja, hogy a csomópontok közötti kommunikáció alhálózatot használja az alábbi kimeneti példa **10.0.2.0/24**.
Alhálózat csak a csatlakozás **10.0.2.0/24** sikeres legyen:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Most ellenőrizze a HSR-port **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Az eredmény így kell kinéznie a igazolja, hogy a HSR-kommunikáció alhálózatot használ az alábbi kimeneti példa **10.0.1.0/24**.
Alhálózat csak a csatlakozás **10.0.1.0/24** sikeres legyen:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


A corosync konfigurációs fájl nem lehet helyes-e a fürtben, beleértve a csomóponttöbbség maker minden csomóponton. Abban az esetben, ha a fürthöz való csatlakozást, a csomópont nem a várt módon működik, hozzon létre és/vagy másolása **/etc/corosync/corosync.conf** manuálisan/minden csomópontján, és indítsa újra a szolgáltatást.

A következő tartalmától **corosync.conf** példaként a teszt rendszerből.

Első szakasz **totem** ez leírtak szerint [dokumentáció] [ sles-pacemaker-ha-guide] (szakasz fürttelepítés – 11. lépés). Az érték figyelmen kívül hagyhatja **mcastaddr**. Csak tartani a meglévő bejegyzést. A bejegyzéseket **token** és **konszenzus** kell beállítani a Microsoft Azure az SAP HANA dokumentációját, amely annak megfelelően [Itt][sles-pacemaker-ha-guide]

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

A második szakasz **naplózás** nem módosult az adott alapértelmezett értéket:

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

A harmadik szakasz azt mutatja be a **csomópontlista**. A fürt minden csomópontján kell jelenik meg a csomópont-azonosító:

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

Hogyan állítható be egy Azure-beli virtuális gépen egy SBD eszköz ismertető dokumentációban ismertetett [Itt] [ sles-pacemaker-ha-guide] (szakasz sbd szintaxiskiemeléshez).

Először is ellenőrizze, hogy a virtuális gép SBD kiszolgálón keresse meg, ha a fürt minden csomópontján az ACL-bejegyzések. Futtassa a következő parancsot a virtuális gép SBD kiszolgálón:


<pre><code>
targetcli ls
</code></pre>


A tesztgépen a parancs kimenete az alábbi minta hasonlított. Az ACL-neveket például **iqn.2006-04.hso-db-0.local:hso-db-0** , meg kell adni a megfelelő kezdeményező neve a virtuális gépeken. Minden virtuális Gépnek szüksége van, egy másikat.

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

Ezt követően ellenőrizze, hogy a kezdeményező nevek az összes virtuális gép különböző, és megfelel a fenti bejegyzések. Íme egy példa a munkavégző csomópont 1 1 helyet meg:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

A kimenet az alábbi minta hasonlított:

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

Ezután ellenőrizze, hogy ha a **felderítési** megfelelően működik-e, és futtassa az alábbi parancsot minden egyes fürtcsomóponton SBD kiszolgáló virtuális gép IP-cím használatával:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

A kimenetnek az alábbi minta hasonlóan kell kinéznie:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Tovább megvalósíthatósági példában, hogy ellenőrizze, hogy a csomópont a SDB eszköz látja-e. Ellenőrizze azt minden csomóponton, beleértve a csomóponttöbbség készítő:

<pre><code>
lsscsi | grep dbhso
</code></pre>

A kimenetnek az alábbi minta hasonlóan kell kinéznie. Ne feledje, hogy a nevek eltérőek lehetnek (eszköznév megváltozhatnak virtuális gép újraindítása után):

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

A rendszer állapotától függően, előfordulhat, hogy néha segít a hiba elhárításához nyújthatnak segítséget az iscsi-szolgáltatások újraindítása. Ezután futtassa le a következő parancsokat:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Bármely olyan csomópontról ellenőrizheti, ha vannak-e az összes csomópont **törölje a jelet**. Csak figyelje, hogy a megfelelő eszköz a nevet használja egy adott csomóponton:

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


Egy másik SBD ellenőrizze-e a **memóriakép** a sbd parancs lehetőséget. Íme egy mintául szolgáló parancs és a legtöbb maker csomópontból, ahol az eszköz neve nem volt kimeneti **sdm** , de **sdd**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

A kimenet (szereplőkkel az eszköznév) kell kinéznie minden csomóponton ugyanaz:

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

Egy további ellenőrzési SBD a arra, hogy egy másik csomópontra való üzenetküldéshez. Futtassa a következő parancsot a munkavégző csomóponton 1 2 helyen található ahhoz, hogy az üzenet küldése a munkavégző csomópont 2 2 helyen található:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

A cél virtuális gép ügyféloldali - lett **hso-hana-virtuálisgép-s2-2** ebben a példában – keresse meg a következő bejegyzést **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Ha ellenőrizni bejegyzései **/etc/sysconfig/sbd** felel meg a leírását az [dokumentáció] [ sles-pacemaker-ha-guide] (szakasz sbd szintaxiskiemeléshez). Ellenőrizze, hogy az indítási beállítása **/etc/iscsi/iscsid.conf** automatikusra van állítva.

A fontos bejegyzések **/etc/sysconfig/sbd** (alkalmazkodjon az azonosító értéke, szükség esetén):

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Ellenőrizze, hogy egy másik cikk az indítási beállítása a **/etc/iscsi/iscsid.conf**. A kötelező beállítás szerint kell azért történt a **iscsiadm** parancs látható az alábbiakban, amely a dokumentációban ismertetett. Érdemes a, és talán alkalmazkodnak hozzá kézzel a **vi** abban az esetben nem egyeznek.

Parancsot az indítási viselkedést:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

A bejegyzés **/etc/iscsi/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Tesztelés és ellenőrzés során előfordulások történt, amikor egy virtuális gép az újraindítás után az SBD eszköz nem volt látható többé. Hiba történt az indítási beállítást, és bemutatta, milyen yast2 közötti eltérést. Ellenőrizze a beállításokat, hajtsa végre a következő lépéseket követve:

1. Indítsa el a yast2
2. Válassza ki **hálózati szolgáltatások** a bal oldalon
3. Görgessen le, jobb oldalán **iSCSI-kezdeményező** , és jelölje ki
4. A következő képernyőn alatt a **szolgáltatás** lapon kell megjelennie az egyedi kezdeményező neve a csomópont
5. A kezdeményező neve felett ügyeljen arra, hogy a **szolgáltatás indítása** értéke **rendszerindítás során**
6. Ha nem ez a helyzet, majd beállíthatja azt a **amikor rendszerindítását** helyett **manuálisan**
7. Ezután váltson a felső lapon **csatlakoztatott célok**
8. A cél csatlakoztatott képernyőn megjelenik egy bejegyzés az SBD eszköz, például a minta: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**
9. Ellenőrizze, hogy ha a kezdeti értéke "**onboot**"
10. Ha nem, válassza a **szerkesztése** és igény szerint
11. A módosítások mentéséhez és bezárásához yast2



## <a name="pacemaker"></a>Támasztja

Miután mindent megfelelően van beállítva, a minden csomóponton a támasztja szolgáltatás állapotának ellenőrzéséhez futtathatja a következő parancsot:

<pre><code>
systemctl status pacemaker
</code></pre>

A felső részén a kimenet az alábbi minta hasonlóan kell kinéznie. Fontos, amely után az állapot **aktív** jelenik meg, mint **betöltött** és **aktív (fut)**. Az állapot "Betöltött" láthatónak kell lennie Miután **engedélyezve**.

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

Abban az esetben, ha a beállítás értéke továbbra is **le van tiltva**, futtassa a következő parancsot:

<pre><code>
systemctl enable pacemaker
</code></pre>

Az összes konfigurált erőforrások támasztja megtekintéséhez futtassa a következő parancsot:

<pre><code>
crm status
</code></pre>

A kimenetnek az alábbi minta hasonlóan kell kinéznie. Rendben, hogy a cln és msl erőforrások jelennek meg, a legtöbb virtuális gép maker leállította (**hso-hana-dm**). Nincs a legtöbb maker csomóponton SAP HANA-telepítés nélkül. Ezért a **cln** és **msl** erőforrások látható módon leállt. Fontos, hogy látható-e a megfelelő virtuális gépek teljes száma (**7**). Minden virtuális gép, a fürt részét képező szerepelnie kell a állapotú **Online**. Megfelelően kell ismernie a jelenlegi elsődleges fő csomóponttal (ebben a példában ez **hso-hana-virtuálisgép-s1-0**).

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

Támasztja egyik fontos szolgáltatása, hogy karbantartás módba helyezni. Ez a mód lehetővé teszi, hogy végzett módosítások (például a virtuális gép újraindítása) azonnali fürt művelet szikrák nélkül. Egy tipikus használati eset lenne tervezett operációs rendszer vagy az Azure infrastruktúra-karbantartási (is című külön tervezett karbantartásának ismertetése). A következő paranccsal karbantartás módba helyezni támasztja:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

A ellenőrzésekor **crm állapot**, megfigyelheti, hogy a kimenet megjelölt összes erőforrás **nem felügyelt**. Ebben az állapotban a fürt nem reagál a módosításokat, mint az SAP HANA indítása/leállítása.
Íme egy minta kimenete a **crm állapot** parancsot, miközben a fürt karbantartási módban van:

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


És az alábbi parancs példában bemutatjuk, hogyan a fürt karbantartási mód befejezéséhez:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Egy másik crm parancs lehetővé teszi, hogy a teljes fürtkonfigurációnak szerkesztése lehetőséget egy szerkesztőben történő első. A módosítások mentése után a fürt elindítja a megfelelő műveleteket:

<pre><code>
crm configure edit
</code></pre>

Egyszerűen tekintse meg a teljes fürtkonfigurációnak, használja a crm **megjelenítése** lehetőséget:

<pre><code>
crm configure show
</code></pre>



Fürterőforrások hiba után, történik, amely a **crm állapot** parancs listája látható **sikertelen műveletek**. Ez a kimenet az alábbi mintát talál:


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

Szükség egy fürt karbantartási hiba után. Csak a crm-parancs használata újra, és használja a parancssori kapcsolót **karbantartása** távolíthatja el ezeket a sikertelen művelet bejegyzést a megfelelő elnevezési fürterőforrás a lent látható módon:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

A parancs, amely hasonlít az alábbi minta kimenet kell visszaadnia:

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



## <a name="failover--takeover"></a>Feladatátvétel / átvétel

Már az első szakaszban ismertetett módon a fontos megjegyzések, nem használjon standard szabályos leállítást, a fürt a feladatátvételi teszt vagy SAP HANA HSR átvételt. Ehelyett azt javasoljuk, hogy indít, például kernelpánikot vagy kényszerítése egy erőforrás-migrálás, vagy esetleg állítsa le a virtuális gépek operációs rendszer szintjén minden hálózatból. Egy másik módszer az lenne a **crm \<csomópont\> készenléti** parancsot. További tájékoztatás a SUSE-dokumentumban található [Itt][sles-12-ha-paper]. Alább láthat feladatátvételt kényszerít ki egy fürt három minta parancsok:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Is tervezett karbantartásának ismertetése a szakaszban leírt, egy jó módszer a fürt tevékenységek figyeléséhez futtatásához **SAPHanaSR-showAttr** az a **watch** parancsot:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Emellett segít és tekintse meg az SAP HANA fekvő állapot származó egy SAP python-szkriptet. Az állapot értéke a típust, amelyet a fürt beállítása keres. Ha egy feldolgozó csomópont leáll mértékegységeként világossá válik. Ha egy feldolgozó csomópont leáll, az SAP HANA nem azonnal ad vissza hibát a rendszer állapotát, az egész kibővített. Nincsenek az egyes újrapróbálkozások szükségtelen feladatátvételek elkerüléséhez. Csak akkor, ha a állapota Ok (visszatérési érték 4) származó hiba (visszatérési érték 1) a fürt reagál. Ezért helyes, ha kimenete **SAPHanaSR-showAttr** bemutatja egy virtuális gép állapota **offline** , de még a Váltás az elsődleges és másodlagos nincs tevékenység. Nincs fürt tevékenység lekéri indul el, mindaddig, amíg az SAP HANA nem ad vissza hibát.

Nyomon követheti az SAP HANA fekvő állapot felhasználóként \<HANA SID\>adm meghívásával az SAP python szkriptet (Előfordulhat, hogy rendelkezik az elérési út alkalmazkodni) a következő módon:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Ez a parancs kimenete az alábbi minta hasonlóan kell kinéznie. Fontos a **gazdagép állapota** oszlop, valamint a **teljes állomás állapota**. A tényleges kimenet valójában a szélesebb körű rendelkező további oszlopok.
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


Van egy másik parancs aktuális fürt tevékenységek ellenőrzéséhez. Lásd az alábbi a parancs, és a kimenet a következő végéről után az elsődleges hely a fő csomóponttal lett állítva. Átmenet műveletekhez, például a listája látható **előléptetése** a korábbi másodlagos fő csomóponttal (**hso-hana-virtuálisgép-s2-0**) az új elsődleges Master. Ha mindent rendben, és az összes tevékenység befejezett ezen a listán szereplő **átmenet összefoglalás** lehet üres.

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

Esetén, a tervezett karbantartás, nincsenek különböző használati helyzetekhez. Egy kérdésre, például akkor, ha csak az infrastruktúra-karbantartási például az operációs rendszer szintjén, és a lemezkonfiguráció vagy a verziófrissítésre HANA módosításokat.
Például a SUSE-dokumentumokban további információt is megtalálhatja [Itt] [ sles-zero-downtime-paper] vagy [egy másik Itt][sles-12-for-sap]. Ezeket a dokumentumokat is minták elsődleges manuális áttelepítésével.

Az erős belső tesztjei azért volt szükség, ellenőrizze az infrastruktúra-karbantartási használati eset. Bármilyen típusú az elsődleges áttelepítésével kapcsolatos probléma elkerülése érdekében a döntés született mindig áttelepíteni az elsődleges fürt karbantartási módba előtt. Ezzel a módszerrel nem elengedhetetlen, hogy a fürt elfelejtette kapcsolatos korábbi helyzet (melyik oldal lett elsődleges és melyik oldal másodlagos volt).

Ebben a tekintetben van két különböző helyzetekben:

1. A jelenlegi másodlagos tervezett karbantartás. 
   Ebben az esetben csak a fürt karbantartás módba helyezni, illetve használhat a munkát a másodlagos anélkül, hogy befolyásolná a fürt

2. Tervezett karbantartás az aktuális elsődleges. 
   Ahhoz, hogy a felhasználóknak, hogy folytassa a munkát a karbantartás során, akkor kell feladatátvételt kényszerít ki egy. Ezt a módszert használja a fürt feladatátvétele támasztja és nem az SAP HANA HSR szint indít el kell. A támasztja a telepítő automatikusan elindítja az SAP HANA átvételt. Ezenkívül fontos ehhez a feladatátvétel előtt a fürt karbantartási módba.

A jelenlegi másodlagos helyen karbantartási eljárást szeretné, az alábbi lépéseket:

1. A fürt karbantartási módba
2. A másodlagos helyen a munka elvégzéséhez 
3. A fürt karbantartási mód befejezéséhez

A jelenlegi elsődleges helyen karbantartási eljárást a következő összetettebb:

1. Manuális feladatátvétel / SAP HANA átvétel támasztja erőforrás-migrálás keresztül (lásd lejjebb)
2. A korábbi elsődleges hely az SAP HANA első leállásából a fürt beállítása
3. A fürt karbantartási módba
4. A karbantartási munkát, miután regisztrálja a korábbi elsődleges az új másodlagos hely
5. Távolítsa el a fürtkonfiguráció (lásd lejjebb)
6. A fürt karbantartási mód befejezéséhez


Egy erőforrás (például a feladatátvételt kényszerít ki egy) áttelepítése bejegyzés hozzáadása a fürt konfigurációját. Ezek a bejegyzések a karbantartási mód megszüntetése előtt törölni kell. Íme egy példa:

Első lépés, hogy feladatátvételt kényszerít ki egy fürt telepítse át az msl erőforrás a jelenlegi másodlagos fő csomóponttal. Az alábbi parancsot, hogy létrejött-e egy "áthelyezési korlátozás" figyelmeztetést.

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


A feladatátvételi folyamat parancs használatával ellenőrizze **SAPHanaSR-showAttr**. Nyisson meg egy dedikált rendszerhéj ablakot, és indítsa el a parancsot a mi segít a fürt állapotának figyelésére **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

A kimenet tükröznie kell azt a manuális feladatátvételt. A korábbi másodlagos fő csomóponttal lett **előléptetett** (ebben a példában **hso-hana-virtuálisgép-s2-0**) és a korábbi elsődleges hely le lett állítva (**licencszolgáltatóval** érték **1** a korábbi elsődleges főcsomópont **hso-hana-virtuálisgép-s1-0**): 

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

A feladatátvevő fürt és az SAP HANA átvétel után helyezze a fürt a támasztja szakaszban leírtak szerint karbantartási módba.

A parancsok **SAPHanaSR-showAttr** vagy **crm állapot** használatbavételben a korlátokat, az erőforrás-migrálás által létrehozott nem jelzik. Ezek a korlátozások láthatóvá az egyik lehetőség, hogy megjelenítése a teljes fürt erőforrás konfigurációját a következő paranccsal:

<pre><code>
crm configure show
</code></pre>

A fürtkonfiguráció között keresse meg egy új földrajzi megszorítás, a korábbi manuális erőforrás-migrálás okozta. Íme egy példa (bejegyzést: kezdje **hely cli -**):

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Sajnos az ilyen megkötések előfordulhat, hogy hatással vannak a teljes fürt viselkedését. Ezért kötelező történő visszaállítását a teljes rendszer biztonsági mentése előtt távolítsa el őket. Az a **unmigrate** lehetséges karbantartása a helyre vonatkozó megkötések, mielőtt létrehozó parancsot. A naming egy kicsit zavaró lehet. Ez nem jelenti azt, hogy, akkor próbálja meg az erőforrás telepítheti vissza az eredeti virtuális gép át lett. Ez csupán eltávolítja a helyre vonatkozó megkötések, és is ad vissza a megfelelő információkat, ha a következő parancs futtatásával:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

A karbantartási munkát végén, a fürt karbantartási mód leállítása a támasztja szakaszban látható módon.



## <a name="hbreport-to-collect-log-files"></a>naplófájlokat gyűjthet hb_report

Támasztja fürt problémák elemzéséhez, a hasznos és futtatásához SUSE-támogatást is kért legyen a **hb_report** segédprogramot. Összegyűjti az összes fontos logfiles, amelyek lehetővé teszik a Mi történt az elemzés. Itt látható egy minta-hívás használatával egy kezdő és záró időpontot, ahol az egy adott esemény történt (is lásd: első szakasz kapcsolatos fontos megjegyzések):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

A parancs Önt meg, ahová a tömörített naplófájlokat:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Bontsa ki az egyes fájlokat, a standard keresztül **tar** parancsot:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

A kibontott fájlokat megnézzük a naplófájlok találja. Ezek többsége fel külön könyvtárakban a fürt minden csomópontján:

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


Időtartományban, amelyet a rendszer az aktuális fő csomópont megadott **hso-hana-virtuálisgép-s1-0** következtében leállt. Az a **journal.log** megtalálhatja az eseményhez kapcsolódó bejegyzések:

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

Egy másik példa, a támasztja naplófájlt a másodlagos főkiszolgálón, és az új elsődleges főkiszolgáló vált. Íme egy olyan, amely bemutatja, hogy az a leállítva elsődleges főcsomópont lett állítva: **offline**.

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


Alább láthat a 2. fürt hely SAP HANA global.ini fájlból excerpts megjelenítése a különböző hálózatokról az SAP HANA-csomópontok közötti kommunikáció és a HSR-állomásnév névfeloldási bejegyzéseket, például:

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

A fürt megoldás is biztosít egy böngésző illesztőt, amely nagyszerű grafikus felhasználói felület kínál a személyeket, akik szívesebben menük és a grafikai képest rendszerhéj szinten lévő összes parancs.
A böngésző felület használatához az alábbi URL-címet is, és cserélje le **\<csomópont\>** egy tényleges SAP HANA-csomóponthoz, és adja meg a fürt hitelesítő adatait (felhasználói **hacluster**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Az alábbi képernyőfelvételen a fürt irányítópultja:


![HAWK fürt irányítópultja](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


A második képernyőképen az látható egy példa a helyre vonatkozó megkötések miatt erőforrás fürtáttelepítés a tervezett karbantartási szakaszban leírtak szerint:


![HAWK lista megkötései](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Egy másik nagyszerű szolgáltatása feltöltéséhez lehetősége egy **hb_report** kimeneti (című **hb_report**) a **HAWK** alatt **előzmények** , a következő képernyőfelvételen látható:

![HAWK feltöltési hb_report kimenet](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

A **előzmények** majd lehetővé teszi, hogy tartalmazza az összes fürt átmenetek keresztül a **hb_report** kimenete:

![HAWK tekintse meg az átmenetek belül hb_report kimenete](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Az előző képernyőképen látható egyetlen átmenet, amely bemutatja, hogy a fürt egy elsődleges fő csomópont-összeomlás a reakciót részletek szakaszának (csomópont **hso-hana-virtuálisgép-s1-0**) és a másodlagos csomópont most elősegítő új fő (**hso-hana-virtuálisgép-s2-0**):

![Egyetlen átmenet HAWK pillantást](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>További lépések

A hibaelhárítás az útmutató olyan magas rendelkezésre állás az SAP Hana-hoz kibővített konfigurációban kapcsolatban. Amellett, hogy az adatbázis egy SAP-rendszeren belüli másik fontos összetevő az SAP NetWeaver stack. Ezután olvasson magas rendelkezésre állás az SAP NetWeaver SUSE Enterprise Linux Server használata Azure virtuális gépeken [ez] [ sap-nw-ha-guide-sles] cikk.

