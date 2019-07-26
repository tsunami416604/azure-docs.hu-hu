---
title: 'A VMM-felhőkben lévő Hyper-V virtuális gépek replikálásának tesztelési eredményei egy másodlagos helyre a következővel: Azure Site Recovery | Microsoft Docs'
description: Ez a cikk a VMM-felhőkben lévő Hyper-V virtuális gépeknek a Azure Site Recovery használatával történő replikálására szolgáló teljesítménytesztek adatait ismerteti.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: a7413b2dcb24a42092eb2af9816b1d29a8306e19
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377226"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>A Hyper-V replikálásának tesztelési eredményei másodlagos helyre


Ez a cikk az System Center Virtual Machine Manager-(VMM-) felhőkben lévő Hyper-V virtuális gépek egy másodlagos adatközpontba történő replikálásának eredményeit ismerteti.

## <a name="test-goals"></a>Tesztelési célok

A tesztelés célja az volt, hogy megvizsgálják, hogyan végzi el a Site Recovery az állandó állapotú replikáció során.

- Az állandó állapotú replikáció akkor fordul elő, ha a virtuális gépek befejezték a kezdeti replikálást, és szinkronizálják a különbözeti változásokat.
- Fontos, hogy stabil állapottal mérje a teljesítményt, mert az az állapot, amelyben a legtöbb virtuális gép marad, kivéve, ha váratlan kimaradások történnek.
- A tesztelési üzembe helyezés két helyszíni helyből áll, amelyek mindegyike VMM-kiszolgálóval rendelkezik. Ez a tesztelési üzembe helyezés jellemző a központi iroda/fiókiroda üzembe helyezésére, az elsődleges helyként működő főkiszolgálóval és a fiókirodával a másodlagos vagy helyreállítási helyként.

## <a name="what-we-did"></a>Mi volt

A teszt pass:

1. VMM-sablonok használatával létrehozott virtuális gépek.
2. Elindította a virtuális gépeket, és 12 óra alatt rögzítette az alapkonfiguráció teljesítményének mérőszámait.
3. Az elsődleges és a helyreállítási VMM-kiszolgálókon felhőket hozott létre.
4. Site Recovery konfigurált replikáció, beleértve a forrás-és a helyreállítási felhők közötti leképezést is.
5. Engedélyezve van a virtuális gépek védelme, és lehetővé teszi számukra a kezdeti replikáció befejezését.
6. A rendszer stabilizációja pár órát várt.
7. 12 óra alatt rögzített teljesítmény-mérőszámok, ahol az összes virtuális gép a várt replikálási állapotban maradt az adott 12 órában.
8. Az alapteljesítmény-mérőszámok és a replikálási teljesítmény mérőszámai közötti különbözet mérhető.


## <a name="primary-server-performance"></a>Elsődleges kiszolgáló teljesítménye

* A Hyper-V replika (Site Recovery által használt) aszinkron módon követi nyomon a naplófájlok módosításait, az elsődleges kiszolgáló minimális tárolási terhelésével együtt.
* A Hyper-V replika saját karbantartású memória-gyorsítótárat használ a IOPS terhelés csökkentése érdekében. Az írásokat a memóriában tárolja a VHDX, és a naplófájlnak a helyreállítási helyre történő elküldése előtt kiüríti őket a naplófájlba. A lemez kiürítése akkor is megtörténik, ha az írás előre meghatározott korlátot talált.
* Az alábbi ábrán látható az állandó állapot IOPS terhelése a replikáláshoz. Láthatjuk, hogy a replikálás miatti IOPS-terhelés 5% körül van, ami nagyon alacsony.

  ![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744913.png)

A Hyper-V replika az elsődleges kiszolgáló memóriáját használja a lemezek teljesítményének optimalizálása érdekében. Ahogy az az alábbi ábrán is látható, a memória terhelése az elsődleges fürt összes kiszolgálóján marginális. A memória terhelése a replikáció által használt memória százalékos arányát mutatja, a Hyper-V kiszolgáló összes telepített memóriájának összehasonlítva.

![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744914.png)

A Hyper-V replika minimális CPU-terheléssel rendelkezik. Ahogy az ábrán is látható, a replikálási terhelés a 2-3%-os tartományba esik.

![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Másodlagos kiszolgáló teljesítménye

A Hyper-V replika kis mennyiségű memóriát használ a helyreállítási kiszolgálón a tárolási műveletek számának optimalizálása érdekében. A Graph összefoglalja a memória használatát a helyreállítási kiszolgálón. A memória terhelése a replikáció által használt memória százalékos arányát mutatja, a Hyper-V kiszolgáló összes telepített memóriájának összehasonlítva.

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744916.png)

A helyreállítási helyen lévő I/O-műveletek mennyisége az írási műveletek számának függvénye az elsődleges helyen. Tekintse meg a helyreállítási helyen lévő összes I/O-műveletet a teljes I/O-műveletekkel és az elsődleges helyen található írási műveletekkel összehasonlítva. A diagramok azt mutatják, hogy a helyreállítási hely teljes IOPS

* Körülbelül 1,5 alkalommal az írási IOPS az elsődlegesen.
* Az elsődleges hely teljes IOPS körülbelül 37%-a.

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744917.png)

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>A hálózat kihasználtságának hatása

A hálózati sávszélesség átlagosan 275 MB-os átlagát használták az elsődleges és a helyreállítási csomópontok (a tömörítést engedélyező) között, egy meglévő, 5 GB/s sávszélességgel.

![Eredmények hálózati kihasználtsága](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>A virtuális gép teljesítményére gyakorolt hatás

Fontos szempont, hogy a replikáció a virtuális gépeken futó éles munkaterhelésekre is hatással van. Ha az elsődleges hely megfelelően van kiépítve a replikáláshoz, akkor nem lesz hatással a munkaterhelésekre. A Hyper-V replika Lightweight követési mechanizmusa biztosítja, hogy a virtuális gépeken futó munkaterhelések ne legyenek hatással az állandósult állapotú replikáció során. Ezt a következő diagramok szemléltetik.

Ez a diagram a replikáció engedélyezése előtt és után a különböző számítási feladatokat futtató virtuális gépek által végrehajtott IOPS jeleníti meg. Azt is megfigyelheti, hogy a kettő között nincs különbség.

![Replika hatásának eredményei](./media/hyper-v-vmm-performance-results/IC744920.png)

A következő gráf a replikáció engedélyezése előtt és után a különböző számítási feladatokat futtató virtuális gépek átviteli sebességét jeleníti meg. Azt is megfigyelheti, hogy a replikációnak nincs jelentős hatása.

![Eredmények replikájának hatásai](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Összegzés

Az eredmények egyértelműen azt mutatják, hogy Site Recovery, a Hyper-V replikával párosítva, jól méretezhető a nagyméretű fürtök minimális terhelésével. Site Recovery egyszerű üzembe helyezést, replikálást, felügyeletet és figyelést tesz lehetővé. A Hyper-V replika biztosítja a szükséges infrastruktúrát a replikálás sikeres skálázásához. 

## <a name="test-environment-details"></a>Tesztkörnyezet részletei

### <a name="primary-site"></a>Elsődleges hely

* Az elsődleges hely olyan fürttel rendelkezik, amely öt Hyper-V-kiszolgálót tartalmaz, és 470 virtuális gépet futtat.
* A virtuális gépek eltérő munkaterheléseket futtatnak, és az összes Site Recovery védelem engedélyezve van.
* A fürtcsomópont tárolóját egy iSCSI SAN biztosítjuk. Model – Hitachi HUS130.
* Mindegyik fürthöz négy hálózati kártya (NIC) tartozik.
* A hálózati kártyák közül kettő egy iSCSI-magánhálózathoz csatlakozik, és kettő egy külső vállalati hálózathoz csatlakozik. Az egyik külső hálózat csak a fürt kommunikációja számára van fenntartva.

![Elsődleges hardverkövetelmények](./media/hyper-v-vmm-performance-results/IC744922.png)

| Kiszolgáló | RAM | Modell | Processzor | Processzorok száma | Hálózati adapter | Szoftver |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-kiszolgálók a fürtben: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB – a HOST25 256 |Dell™ PowerEdge™ R820 |Intel (r) Xeon (r) CPU E5 – 4620 0 \@ 2.20 GHz |4 |1 GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM-kiszolgáló |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Másodlagos hely

* A másodlagos hely hat csomópontos feladatátvevő fürttel rendelkezik.
* A fürtcsomópont tárolóját egy iSCSI SAN biztosítjuk. Model – Hitachi HUS130.

![Elsődleges hardver specifikációja](./media/hyper-v-vmm-performance-results/IC744923.png)

| Kiszolgáló | RAM | Modell | Processzor | Processzorok száma | Hálózati adapter | Szoftver |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-kiszolgálók a fürtben: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge™ R720 |Intel (r) Xeon (r) CPU E5 – 2630 0 \@ 2.30 GHz |2 |1 GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST17 |128 |Dell™ PowerEdge™ R820 |Intel (r) Xeon (r) CPU E5 – 4620 0 \@ 2.20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge™ R820 |Intel (r) Xeon (r) CPU E5 – 4620 0 \@ 2.20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM-kiszolgáló |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Kiszolgáló munkaterhelései

* Tesztelési célból a nagyvállalati felhasználói forgatókönyvekben gyakran használt számítási feladatokat választottuk ki.
* A [IOMeter](http://www.iometer.org) -t a szimulált táblázatban összefoglalt számítási feladatok jellemzővel használjuk.
* Az összes IOMeter-profil úgy van beállítva, hogy véletlenszerű bájtokat írjon, hogy szimulálja a számítási feladatokhoz szükséges legrosszabb írási mintákat.

| Számítási feladat | I/O-méret (KB) | %-Os hozzáférés | % Olvasás | Függőben lévő I/o | I/O-minta |
| --- | --- | --- | --- | --- | --- |
| Fájlkiszolgáló |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Minden 100% véletlenszerű |
| SQL Server (1. kötet)<br />SQL Server (2. kötet) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% véletlenszerű<br />100%-os szekvenciális |
| Exchange |32 |100% |67% |8 |100% véletlenszerű |
| Munkaállomás/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |100%-os véletlenszerű |
| Web File Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Minden 75% véletlenszerű |

### <a name="vm-configuration"></a>Virtuális gép konfigurációja

* 470 virtuális gépek az elsődleges fürtön.
* Minden VHDX lemezzel rendelkező virtuális gép.
* A táblázatban összefoglalt számítási feladatokat futtató virtuális gépek. Az összes VMM-sablonnal lett létrehozva.

| Számítási feladat | Virtuális gépek száma | Minimális RAM (GB) | Maximális memória (GB) | Logikai lemez mérete (GB) virtuális gépenként | Maximális IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Fájlkiszolgáló |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Webkiszolgáló |149 |.5 |1 |80 |6 |
| ÖSSZESEN |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Site Recovery beállítások

* Site Recovery a helyszíni környezet és a helyszíni védelem használatára lett konfigurálva
* A VMM-kiszolgálónak négy felhője van konfigurálva, amelyek a Hyper-V fürt kiszolgálóit és a virtuális gépeket tartalmazzák.

| Elsődleges VMM-felhő | Védett virtuális gépek | Replikálás gyakorisága | További helyreállítási pontok |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 perc |Nincsenek |
| PrimaryCloudRpo30s |47 |30 másodperc |Nincsenek |
| PrimaryCloudRpo30sArp1 |47 |30 másodperc |1 |
| PrimaryCloudRpo5m |235 |5 perc |Nincsenek |

### <a name="performance-metrics"></a>Teljesítmény-mérőszámok

A tábla összegzi az üzemelő példányban mért teljesítmény-mérőszámokat és számlálókat.

| Metrika | Számláló |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Available memory |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM read (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM write (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM read throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM write throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## Next steps

[Set up replication](hyper-v-vmm-disaster-recovery.md)\Processor(_Total)\` Processor Timeation of Hyper-V VMs in VMM clouds to a secondary site with Azure Site Recovery | Microsoft Docs
description: This article provides information about performance testing for replication of Hyper-V VMs in VMM clouds to a secondary site using Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi

---
# Test results for Hyper-V replication to a secondary site


This article provides the results of performance testing when replicating Hyper-V VMs in System Center Virtual Machine Manager (VMM) clouds, to a secondary datacenter.

## Test goals

The goal of testing was to examine how Site Recovery performs during steady state replication.

- Steady state replication occurs when VMs have completed initial replication, and are synchronizing delta changes.
- It’s important to measure performance using steady state, because it’s the state in which most VMs remain, unless unexpected outages occur.
- The test deployment consisted of two on-premises sites, with a VMM server in each site. This test deployment is typical of a head office/branch office deployment, with head office acting as the primary site, and the branch office as the secondary or recovery site.

## What we did

Here's what we did in the test pass:

1. Created VMs using VMM templates.
2. Started VMs, and captured baseline performance metrics over 12 hours.
3. Created clouds on the primary and recovery VMM servers.
4. Configured replication in Site Recovery, including mapping between source and recovery clouds.
5. Enabled protection for VMs, and allowed them to complete initial replication.
6. Waited a couple of hours for system stabilization.
7. Captured performance metrics over 12 hours, where all VMs remained in an expected replication state for those 12 hours.
8. Measured the delta between the baseline performance metrics, and the replication performance metrics.


## Primary server performance

* Hyper-V Replica (used by Site Recovery) asynchronously tracks changes to a log file, with minimum storage overhead on the primary server.
* Hyper-V Replica utilizes self-maintained memory cache to minimize IOPS overhead for tracking. It stores writes to the VHDX in memory, and flushes them into the log file before the time that the log is sent to the recovery site. A disk flush also happens if the writes hit a predetermined limit.
* The graph below shows the steady state IOPS overhead for replication. We can see that the IOPS overhead due to replication is around 5%, which is quite low.

  ![Primary results](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica uses memory on the primary server, to optimize disk performance. As shown in the following graph, memory overhead on all servers in the primary cluster is marginal. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Primary results](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica has minimum CPU overhead. As shown in the graph, replication overhead is in the range of 2-3%.

![Primary results](./media/hyper-v-vmm-performance-results/IC744915.png)

## Secondary server performance

Hyper-V Replica uses a small amount of memory on the recovery server, to optimize the number of storage operations. The graph summarizes the memory usage on the recovery server. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744916.png)

The amount of I/O operations on the recovery site is a function of the number of write operations on the primary site. Let’s look at the total I/O operations on the recovery site in comparison with the total I/O operations and write operations on the primary site. The graphs show that the total IOPS on the recovery site is

* Around 1.5 times the write IOPS on the primary.
* Around 37% of the total IOPS on the primary site.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secondary results](./media/hyper-v-vmm-performance-results/IC744918.png)

## Effect on network utilization

An average of 275 Mb per second of network bandwidth was used between the primary and recovery nodes (with compression enabled), against an existing bandwidth of 5 Gb per second.

![Results network utilization](./media/hyper-v-vmm-performance-results/IC744919.png)

## Effect on VM performance

An important consideration is the impact of replication on production workloads running on the virtual machines. If the primary site is adequately provisioned for replication, there shouldn’t be any impact on the workloads. Hyper-V Replica’s lightweight tracking mechanism ensures that workloads running in the virtual machines are not impacted during steady-state replication. This is illustrated in the following graphs.

This graph shows IOPS performed by virtual machines running different workloads, before and after replication was enabled. You can observe that there is no difference between the two.

![Replica effect results](./media/hyper-v-vmm-performance-results/IC744920.png)

The following graph shows the throughput of virtual machines running different workloads, before and after replication was enabled. You can observe that replication has no significant impact.

![Results replica effects](./media/hyper-v-vmm-performance-results/IC744921.png)

## Conclusion

The results clearly show that Site Recovery, coupled with Hyper-V Replica, scales well with minimum overhead for a large cluster. Site Recovery provides simple deployment, replication, management and monitoring. Hyper-V Replica provides the necessary infrastructure for successful replication scaling. 

## Test environment details

### Primary site

* The primary site has a cluster containing five Hyper-V servers, running 470 virtual machines.
* The VMs run different workloads, and all have Site Recovery protection enabled.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.
* Each cluster server has four network cards (NICs) of one Gbps each.
* Two of the network cards are connected to an iSCSI private network, and two are connected to an external enterprise network. One of the external networks is reserved for cluster communications only.

![Primary hardware requirements](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 has 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Secondary site

* The secondary site has a six-node failover cluster.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.

![Primary hardware specification](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Server workloads

* For test purposes we picked workloads commonly used in enterprise customer scenarios.
* We use [IOMeter](http://www.iometer.org) with the workload characteristic summarized in the table for simulation.
* All IOMeter profiles are set to write random bytes to simulate worst-case write patterns for workloads.

| Workload | I/O size (KB) | % Access | %Read | Outstanding I/Os | I/O pattern |
| --- | --- | --- | --- | --- | --- |
| File Server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |All 100% random |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% random<br />100% sequential |
| Exchange |32 |100% |67% |8 |100% random |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Both 100% random |
| Web File Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |All 75% random |

### VM configuration

* 470 VMs on the primary cluster.
* All VMs with VHDX disk.
* VMs running workloads summarized in the table. All were created with VMM templates.

| Workload | # VMs | Minimum RAM (GB) | Maximum RAM (GB) | Logical disk size (GB) per VM | Maximum IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Web server |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### Site Recovery settings

* Site Recovery was configured for on-premises to on-premises protection
* The VMM server has four clouds configured, containing the Hyper-V cluster servers and their VMs.

| Primary VMM cloud | Protected VMs | Replication frequency | Additional recovery points |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 mins |None |
| PrimaryCloudRpo30s |47 |30 secs |None |
| PrimaryCloudRpo30sArp1 |47 |30 secs |1 |
| PrimaryCloudRpo5m |235 |5 mins |None |

### Performance metrics

The table summarizes the performance metrics and counters that were measured in the deployment.

| Metric | Counter |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Elérhető memória |\Memory\Available MB-ban |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VIRTUÁLIS gépek olvasási (IOPS) műveletei (művelet/s) |\Hyper-V virtuális tárolóeszköz (\<VHD >) \Read művelet/mp |
| Virtuális gép írási (IOPS) műveletei (művelet/s) |\Hyper-V virtuális tárolóeszköz (\<VHD >) \Write művelet/S |
| Virtuális gép olvasási átviteli sebessége |\Hyper-V virtuális tárolóeszköz (VHD\<>) \Read sebessége (bájt/s) |
| Virtuális gép írási sebessége |\Hyper-V virtuális tárolóeszköz (VHD\<>) \Write sebessége (bájt/s) |

## <a name="next-steps"></a>További lépések

[Replikáció beállítása](hyper-v-vmm-disaster-recovery.md)
