---
title: "A vizsgálati eredmények, a Hyper-V replikáció az Azure Site Recovery a helyek közötti |} Microsoft Docs"
description: "Ez a cikk tájékoztatást ad azokról teljesítményének tesztelésekor a helyszíni helyszíni Hyper-V virtuális gépek replikálása az Azure Site Recovery segítségével."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 96ff404f-0d88-43fa-a00b-2dffde93d192
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: c221fe94c5301b0a36882d5ae1c57e523002ecc4
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="test-results-for-on-premises-to-on-premises-hyper-v-replication-with-site-recovery"></a>A vizsgálati eredmények, a helyszíni Hyper-V replikáció Site Recovery szolgáltatással történő helyszíni

A Microsoft Azure Site Recovery összehangolására és a virtuális gépek és fizikai kiszolgálók Azure-bA vagy másodlagos adatközpontba replikáció kezelésére használható. Ez a cikk ismerteti a vizsgálata teljesítmény azt hasonló a Hyper-V virtuális gépek replikálása két között helyszíni adatközpontokkal.

## <a name="test-goals"></a>Tesztelési célokat

A cél a vizsgálat volt, hogy vizsgálja meg, hogyan Azure Site Recovery stabil állapotot replikáció során hajtja végre. Ha virtuális gépek kezdeti replikáció befejeződött, és szinkronizál a változási különbözeteket stabil állapotot replikáció. Fontos stabil állapotot használatával, mert az állapota, amelyben a legtöbb virtuális gépek továbbra is, kivéve, ha nem várt leállások esetén teljesítményének méréséhez.

A VMM-kiszolgáló minden hely két helyszíni hely a teszttelepítés állt. Ez az üzembe helyezési teszt jelenti, az elsődleges hely és a másodlagos vagy tartalék helyként a fiókirodában működő irodát a központi iroda/fiók office központi telepítés, a jellemző.

## <a name="what-we-did"></a>Azt adta

Mi a Microsoft a teszt felelt meg az itt található:

1. A létrehozott virtuális gépek VMM-sablonokkal.
2. Virtuális gépek és a készítés referenciakonfiguráció teljesítménymutatók 12 óránál nagyobb elindult.
3. Létrehozott felhők elsődleges és a helyreállítási VMM-kiszolgálókon.
4. Az Azure Site Recovery, beleértve a forrás- és a helyreállítási felhő hozzárendelése konfigurált felhővédelmet.
5. Engedélyezve van a virtuális gépek védelmét, és lehetővé teszik a kezdeti replikálása befejeződik.
6. Néhány óra múlva a rendszer stabilizációs várt.
7. Rögzített teljesítménymutatók 12 óránál nagyobb, győződjön meg arról, hogy az összes virtuális gép ezen 12 óra ki maradt várt replikációs állapotban.
8. Az alapterv metrikák és a replikációs metrikák közötti különbözetet mérését.


## <a name="primary-server-performance"></a>Elsődleges kiszolgáló teljesítménye

* Hyper-V replika aszinkron módon állapotváltozásait, minimális tárolási terhelés mellett naplófájlt használ az elsődleges kiszolgálón.
* A Hyper-V replika használja az önálló karbantartott memóriagyorsítótár IOPS terhet nyomon követésére minimalizálása érdekében. Tárolja a memóriában VHDX ír, és akkor írja ki azokat a naplófájlba a naplót a helyreállítási helyen küldött időpont előtt. A kiürítési lemez is akkor történik, ha az írási műveletek egy előre meghatározott korlátot elérte.
* Az alábbi diagram megjeleníti a stabil állapotot IOPS terhelést a replikáció. Győződjön meg arról, hogy a terhelés miatt replikációs IOPS körülbelül 5 %, ami meglehetősen kicsi láthatja.

![Elsődleges eredmények](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

A Hyper-V replika használja a memória, lemez teljesítmény optimalizálása érdekében az elsődleges kiszolgálón. Az alábbi diagramon látható, terhet elsődleges a fürtben található összes kiszolgáló memóriájának marginális. A terhelés látható memória a Hyper-V kiszolgálón az összes telepített memória képest replikáció által használt memória aránya.

![Elsődleges eredmények](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

Hyper-V replika rendelkezik olyan minimális CPU-terhelés. Ahogy az a diagramon, replikációs terhelés 2-3 % a tartományban van.

![Elsődleges eredmények](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

## <a name="secondary-recovery-server-performance"></a>Másodlagos (helyreállítási) kiszolgáló teljesítménye

A Hyper-V replika a helyreállítási kiszolgálón a kis méretű memória segítségével optimalizálja a tárolási műveletek száma. A diagram a memóriahasználat a helyreállítási kiszolgálón foglalja össze. A terhelés látható memória a Hyper-V kiszolgálón az összes telepített memória képest replikáció által használt memória aránya.

![Másodlagos eredmények](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

A helyreállítási helyen i/o-műveletek mennyiségének feladata az elsődleges hely az írási műveletek számát. Most nézze meg a teljes i/o-műveletek a helyreállítási helyen összehasonlítva az összes i/o-művelet és az írási műveletek az elsődleges helyen. A diagramokon jelzik, hogy a teljes IOPS a helyreállítási helyen

* Körülbelül 1,5-szerese az írási IOPS az elsődleges.
* Körülbelül 37 %-a a teljes IOPS, az elsődleges helyen.

![Másodlagos eredmények](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![Másodlagos eredmények](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

## <a name="effect-on-network-utilization"></a>Hálózathasználat hatása

Az átlagos 275 MB / s a hálózati sávszélesség elleni egy meglévő sávszélesség 5 GB / s között az elsődleges és a másodlagos csomópont (tömörítés engedélyezése) használt.

![Hálózathasználat eredmények](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

## <a name="effect-on-vm-performance"></a>VM teljesítményre gyakorolt hatás

Egy fontos szempont, hogy a replikáció a termelési számítási feladatokhoz a virtuális gépeken futó hatását. Ha az elsődleges hely képes megfelelően lett beállítva a replikáció, akkor nem adható meg gyakorolt hatás a munkaterhelések. Követés mechanizmus a Hyper-V replika lightweight biztosítja, hogy a virtuális gépeken futó számítási feladatok nem érintettek stabil állapot replikáció során. Ezt mutatja be a következő diagramjait.

Ez a grafikon azt ábrázolja, IOPS végre a különböző terhelésekhez előtt futó virtuális gépek és a replikáció engedélyezése után. Azt is láthatja, hogy nincs-e a kettő közötti különbség.

![A replika hatás eredmények](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

A következő grafikon azt ábrázolja, az átviteli sebesség különböző terhelésekhez előtt futó virtuális gépek és a replikáció engedélyezése után. Azt is láthatja, hogy a replikáció nincs jelentős hatással van.

![Eredmények replika hatások](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

## <a name="conclusion"></a>Összegzés

Az eredmények szétválasztásához, hogy Azure Site Recovery szolgáltatásban alapján kialakulhat Hyper-V replika, méretezze át is, legalább egy nagy méretű fürt esetén.  Az Azure Site Recovery egyszerű üzembe helyezés, a replikáció, a kezelési és figyelési biztosít. Hyper-V replika a szükséges infrastruktúrát biztosít a sikeres replikáció méretezést. Az optimális központi telepítésének tervezéséhez javasoljuk, hogy letöltötte a [Hyper-V replika Capacity Planner](https://www.microsoft.com/download/details.aspx?id=39057).

## <a name="test-environment-details"></a>Környezet részletei

### <a name="primary-site"></a>Elsődleges hely

* Az elsődleges hely öt Hyper-V rendszert futtató 470 virtuális gépeket tartalmazó fürt rendelkezik.
* A virtuális gépek különböző alkalmazásokat és szolgáltatásokat futtathatnak, és mindegyik rendelkezik engedélyezett Azure Site Recovery általi védelmet.
* A fürt összes csomópontjára vonatkozóan tárolási egy iSCSI SAN által biztosított. Modell – Hitachi HUS130.
* Minden egyes fürtkiszolgálón rendelkezik négy hálózati kártyák (NIC) a egy kapcsolóport.
* Két, a hálózati kártyák egy iSCSI magánhálózati csatlakozik, és két külső vállalati hálózatra csatlakoznak. A külső hálózatokat egyik csak a fürtkommunikáció számára van fenntartva.

![Elsődleges hardverkövetelmények](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

| Kiszolgáló | RAM | Modell | Processzor | Processzorok száma | A HÁLÓZATI ADAPTER | Szoftver |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V fürtben lévő kiszolgálókat: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128ESTLAB-HOST25 rendelkezik 256 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |4 |I x 4 kapcsolóport |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM-kiszolgáló |2 | | |2 |1 Gbps |Windows Server 2012 adatbázis R2 (x 64) + VMM 2012 R2 |

### <a name="secondary-recovery-site"></a>Másodlagos (helyreállítási) helyet

* A másodlagos hely tartozik egy hat csomópontos feladatátvevő fürtöt.
* A fürt összes csomópontjára vonatkozóan tárolási egy iSCSI SAN által biztosított. Modell – Hitachi HUS130.

![Elsődleges hardver meghatározása](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

| Kiszolgáló | RAM | Modell | Processzor | Processzorok száma | A HÁLÓZATI ADAPTER | Szoftver |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V fürtben lévő kiszolgálókat: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 @ 2.30-as GHz |2 |I x 4 kapcsolóport |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST17 |128 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM-kiszolgáló |2 | | |2 |1 Gbps |Windows Server 2012 adatbázis R2 (x 64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Kiszolgáló-munkaterhelések

* Tesztelési célokra azt kivételezett gyakori vállalati forgatókönyvet a munkaterhelések.
* Használjuk [IOMeter](http://www.iometer.org) a munkaterhelés jellemző szimuláció táblázat foglalja össze.
* Minden IOMeter profilok írása a legrosszabb szimulálása véletlenszerű bájt írása minták munkaterhelések vannak állítva.

| Számítási feladat | I/o-méret (KB) | % Hozzáférés | % Olvasása | Függőben lévő i/o műveletek | I/o-minta |
| --- | --- | --- | --- | --- | --- |
| Fájlkiszolgáló |48163264 |60%20%5%5%10% |80%80%80%80%80% |88888 |Véletlenszerű 100 % |
| SQL Server (1. kötet) SQL Server (kötet 2) |864 |100%100% |70%0% |88 |szekvenciális 100 %-os random100 % |
| Exchange |32 |100% |67% |8 |100 %-os véletlenszerű |
| Munkaállomás/VDI |464 |66%34% |70%95% |11 |Mindkét véletlenszerű 100 %. |
| Webkiszolgáló-fájl |4864 |33%34%33% |95%95%95% |888 |Véletlenszerű 75 % |

### <a name="vm-configuration"></a>Virtuálisgép-konfiguráció

* az elsődleges fürtön 470 virtuális gépeket.
* Minden virtuális gépek VHDX-lemezzel.
* A táblázatban összefoglalt szolgáltatásokat futtató virtuális gépekhez. Az összes VMM-sablonok-ekkel hozta létre.

| Számítási feladat | Száma virtuális gépek | Minimális memória (GB) | Maximális memória (GB) | Logikai lemez mérete (GB) virtuális gépenként | Maximális IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Fájlkiszolgáló |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Webkiszolgáló |149 |.5 |1 |80 |6 |
| ÖSSZESEN |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>Webhely-helyreállítási beállításai

* Az Azure Site Recovery konfigurálása a helyszíni alkalmazások közötti adatszinkronizálás védelme
* A VMM-kiszolgáló a Hyper-V fürt kiszolgálók és virtuális gépeik tartalmazó konfigurált, négy felhők rendelkezik.

| Elsődleges VMM-felhő | A felhőben lévő védett virtuális gépek | Replikációs gyakoriság | A további helyreállítási pontok |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 perc |None |
| PrimaryCloudRpo30s |47 |30 másodperc |None |
| PrimaryCloudRpo30sArp1 |47 |30 másodperc |1 |
| PrimaryCloudRpo5m |235 |5 perc |None |

### <a name="performance-metrics"></a>Teljesítménymértékeket

A táblázat összefoglalja a metrikák és a központi telepítésben lévő volt mért számlálókat.

| Metrika | A számláló |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Rendelkezésre álló memória |\Memory\Available memória (MB) |
| IO |\PhysicalDisk (_Total) \Disk átvitel/mp |
| Virtuális gép olvasási művelet/mp (IOPS) |\Hyper-V virtuális tárolóeszköz (<VHD>) \Read művelet/mp |
| Virtuális gép (IOPS) írási művelet/mp |\Hyper-V virtuális tárolóeszköz (<VHD>) \Write műveletek/mp |
| Olvassa el az átviteli sebesség VM |\Hyper-V virtuális tárolóeszköz (<VHD>) \Read bájtok/s |
| Virtuális gép teljesítménye |\Hyper-V virtuális tárolóeszköz (<VHD>) \Write bájtok/s |

## <a name="next-steps"></a>Következő lépések

[A két helyszíni VMM-helyek közötti replikáció beállítása](site-recovery-vmm-to-vmm.md)
