---
title: Teszteredmények Hyper-V virtuális gépek replikálása másodlagos helyre az Azure Site Recovery VMM-felhőkben |} A Microsoft Docs
description: Ez a cikk ismerteti a Teljesítménytesztelés replikálás Hyper-V virtuális gépek egy másodlagos helyre az Azure Site Recovery VMM-felhőkben.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 4e6884bdcbc0d9921186ec9ff11a701b707faeef
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921297"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Egy másodlagos helyre Hyper-V-replikáció terhelésiteszt-eredményei

Ez a cikk a Teljesítménytesztelés, amikor a System Center Virtual Machine Manager (VMM) felhőkben, a Hyper-V virtuális gépek replikálása másodlagos adatközpontba eredményeit.

## <a name="test-goals"></a>Tesztelési célok

A cél tesztelése az volt, vizsgálja meg, hogyan hajtja végre a Site Recovery stabil állapotú replikáció során.

- Stabil állapotú replikáció esetén a virtuális gépek kezdeti replikációja befejeződött, és szinkronizál a változásokat.
- Fontos használata a stabil állapotot teljesítmény mérésére, a legtöbb virtuális gépek mely állapot, mert maradnak, kivéve, ha váratlan leállások esetén.
- A tesztelési környezet foglalnak el két helyszíni hely, az egyes helyek VMM-kiszolgálóval. Ez az üzembe helyezési teszt jellemzően a fő office/office ágán, a központi iroda és az elsődleges hely és a fiókiroda a másodlagos vagy helyreállítási helyként.

## <a name="what-we-did"></a>Helyeztünk

Íme, mi a Microsoft a teszt felelt meg:

1. A VMM-sablonok használatával létrehozott virtuális gépre.
2. Virtuális gépek elindult, és a teljesítmény-mérőszámok rögzített 12 órán át.
3. Létrehozott felhőket, az elsődleges és helyreállítási VMM-kiszolgálókon.
4. A Site Recoveryben, beleértve a forrás és a helyreállítási közötti megfeleltetés felhők beállított replikációs.
5. Engedélyezve van a virtuális gépek védelmét, és a kezdeti replikációjának befejezéséhez engedélyezett őket.
6. Néhány óra múlva a rendszer stabilizálása várta.
7. Rögzített teljesítmény-mérőszámok 12 órán át, ahol minden virtuális gép maradt várt replikációs állapotban ezeket 12 órán át.
8. A teljesítmény-mérőszámok és a replikációs teljesítmény-mérőszámok között a különbözeti mérni.


## <a name="primary-server-performance"></a>Elsődleges kiszolgáló teljesítménye

* Hyper-V replika (Site Recovery által használt) aszinkron módon naplófájlt használ, a minimális storage többletterhelést az elsődleges kiszolgálón nyomon követi a módosításokat.
* Hyper-V replika nyomon követésére terhelés csökkentése érdekében a IOPS önálló karbantartott gyorsítótárban használja. Tárolja a vhdx-fájlt a memóriában ír, és a napló a helyreállítási helyre küldendő ideje előtt a naplófájlba kiüríti azokat. Flush lemez is történik, ha az írási műveletek egy előre meghatározott korlát.
* Az alábbi diagram bemutatja IOPS többletterhelést egyenletes állapotú replikáció. Láthatjuk, hogy terhelés miatt a replikáció az IOPS érték körülbelül 5 %-át, amely meglehetősen kicsi.

  ![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V replika az elsődleges kiszolgálón memóriát használ lemez teljesítmény optimalizálása érdekében. A következő diagramon látható, a memória többletterhelést az elsődleges fürt összes kiszolgálóján marginális. A memória terhelés látható-e a replikáció, a Hyper-V-kiszolgálón az összes telepített memória képest által használt memória aránya.

![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V replika a Processzor terhelését a minimális rendelkezik. A grafikonon látható, replikációs terhelést a 2-3 % hatótávolságán belül van.

![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Másodlagos kiszolgáló teljesítménye

Hyper-V replika kisebb mennyiségű memóriát használ a helyreállítási kiszolgálón optimalizálhatja a tárolási műveletek száma. A grafikon a memóriahasználat, a helyreállítási kiszolgálón foglalja össze. A memória terhelés látható-e a replikáció, a Hyper-V-kiszolgálón az összes telepített memória képest által használt memória aránya.

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744916.png)

A helyreállítási helyre i/o-műveletek mennyiségét, akkor a függvény az elsődleges hely az írási műveletek számát. Nézzük tekintse meg a teljes i/o-műveletek a helyreállítási helyre összehasonlítva a teljes i/o-műveletek írása és az elsődleges helyen. A gráfok mutatják, hogy a teljes iops-t, a helyreállítási helyre

* Körülbelül 1,5-szerese az írási iops-t az elsődleges.
* Az elsődleges helyen lévő teljes iops-érték körülbelül 37 %-át.

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744917.png)

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>A hálózathasználat hatása

275 Mb-os hálózati sávszélesség másodpercenként átlagosan másodpercenként 5 GB-os egy meglévő sávszélesség elleni használták (a tömörítés engedélyezése), az elsődleges és helyreállítási csomópontok között.

![Eredmények hálózati kihasználtság](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>A virtuális gép teljesítményére hatással

Egy fontos szempont, a replikáció a virtuális gépeken futó éles számítási feladatok hatását. Ha az elsődleges hely van fizikaimemória-replikációhoz, nem lehetnek hatással a számítási feladatok. Hyper-V replika egyszerűsített követési mechanizmus biztosítja, hogy a virtuális gépeken futó számítási feladatok nem érinti őket egyenletes állapotú replikáció. Ezt mutatja be a következő diagramok.

Ez a diagram látható IOPS különböző számítási feladatokhoz, előtt futó virtuális gépek és a replikáció engedélyezése után. Azt is megfigyelheti, hogy nincs különbség a kettő között.

![Replika érvénybe eredmények](./media/hyper-v-vmm-performance-results/IC744920.png)

A következő diagram bemutatja az átviteli sebességet, mielőtt különböző számítási feladatokhoz, futó virtuális gépek és a replikáció engedélyezése után. Azt is megfigyelheti, hogy a replikáció nem jelentős befolyásolja.

![Eredmények replika hatások](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Összegzés

Az eredmények egyértelműen bemutatják, hogy a Site Recovery, Hyper-V replika, így jól, legalább egy nagy fürt esetén méretezze át. Egyszerű telepítés, a replikáció, a felügyeleti és monitorozási a Site recoveryben. Hyper-V replika a szükséges infrastruktúrát biztosít a sikeres replikálás méretezése. 

## <a name="test-environment-details"></a>Tesztelési környezet részletei

### <a name="primary-site"></a>Elsődleges hely

* Az elsődleges hely öt Hyper-V kiszolgálók, a 470 virtual machines szolgáltatásban futó tartalmazó fürt rendelkezik.
* A virtuális gépek különböző számítási feladatok futtatásához, és az összes Site Recovery általi védelem engedélyezve van.
* Egy iSCSI SAN által biztosított tárolási a fürt összes csomópontjára vonatkozóan. Modell – Hitachi HUS130.
* Minden egyes fürtkiszolgálón négy hálózati kártyák (NIC) egy gbps rendelkezik.
* A hálózati kártyák közül kettő az iSCSI-magánhálózati csatlakozik, és két külső vállalati hálózathoz csatlakozik. A külső hálózatok közül csak a fürtkommunikáció számára van fenntartva.

![Elsődleges hardverkövetelmények](./media/hyper-v-vmm-performance-results/IC744922.png)

| Kiszolgáló | RAM | Modell | Processzor | Processzorok száma | Hálózati adapter | Szoftver |
| --- | --- | --- | --- | --- | --- | --- |
| A Hyper-V fürtben lévő kiszolgálók: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128ESTLAB-HOST25 256 rendelkezik |A Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |4 |GB/s x 4 I |A Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM-kiszolgáló |2 | | |2 |1 Gbps |A Windows Server 2012 adatbázis R2 (x 64) és a VMM 2012 R2-ben |

### <a name="secondary-site"></a>Másodlagos hely

* A másodlagos hely rendelkezik, egy hat csomópontos feladatátvevő fürtöt.
* Egy iSCSI SAN által biztosított tárolási a fürt összes csomópontjára vonatkozóan. Modell – Hitachi HUS130.

![Elsődleges hardver-specifikáció](./media/hyper-v-vmm-performance-results/IC744923.png)

| Kiszolgáló | RAM | Modell | Processzor | Processzorok száma | Hálózati adapter | Szoftver |
| --- | --- | --- | --- | --- | --- | --- |
| A Hyper-V fürtben lévő kiszolgálók: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |A Dell™ PowerEdge™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 @ 2.30-as GHz |2 |GB/s x 4 I |A Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST17 |128 |A Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |4 | |A Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST24 |256 |A Dell™ PowerEdge™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 @ 2,20 GHz |2 | |A Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM-kiszolgáló |2 | | |2 |1 Gbps |A Windows Server 2012 adatbázis R2 (x 64) és a VMM 2012 R2-ben |

### <a name="server-workloads"></a>Server számítási feladatok

* Tesztelési célokra azt követi a számítási feladatok gyakran használt vállalati forgatókönyvet.
* Használjuk a [IOMeter](http://www.iometer.org) a munkaterhelés jellemző a szimuláció táblázat foglalja össze.
* Összes IOMeter profil beállítása az írási szimulálásához legrosszabb véletlenszerű bájt írási számítási feladatok esetében.

| Számítási feladat | I/o-mérete (KB) | A(z) % hozzáférés | % Olvasási | Függőben lévő i/o műveletek | I/o-minta |
| --- | --- | --- | --- | --- | --- |
| Fájlkiszolgáló |48163264 |60%20%5%5%10% |80%80%80%80%80% |88888 |Minden 100 %-os véletlenszerű |
| Az SQL Server (1. kötet) SQL Server (2. kötet) |864 |100%100% |70%0% |88 |100 %-os random100 % szekvenciális |
| Exchange |32 |100% |67% |8 |100 %-os véletlenszerű |
| Munkaállomás/VDI |464 |66%34% |70%95% |11 |Mindkét 100 %-os véletlenszerű |
| Web File Server |4864 |33%34%33% |95%95%95% |888 |Az összes 75 %-os véletlenszerű |

### <a name="vm-configuration"></a>Virtuálisgép-konfiguráció

* Az elsődleges fürtön 470 virtuális gépeket.
* A vhdx az összes virtuális gépet.
* A táblázatban összefoglalt számítási feladatokat futtató virtuális gépeket. Az összes VMM-sablonok-ekkel hozta létre.

| Számítási feladat | Száma virtuális gépek | Minimális memória (GB) | Maximális memória (GB) | Logikai lemez mérete (GB) virtuális gépenként | Maximális iops-érték |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Fájlkiszolgáló |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Webkiszolgáló |149 |.5 |1 |80 |6 |
| ÖSSZES |470 | | |96.83 TB |4108 |

### <a name="site-recovery-settings"></a>A Site Recovery beállításait

* A Site Recovery a helyszíni védelem helyszíni konfigurálása
* A VMM-kiszolgálót a Hyper-V fürt kiszolgálók és a virtuális gépeket tartalmazó konfigurált, négy felhők rendelkezik.

| Elsődleges VMM-felhőben | Védett virtuális gépek | Replikációs gyakoriság | További helyreállítási pontok |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 perc |None |
| PrimaryCloudRpo30s |47 |30 másodperc |None |
| PrimaryCloudRpo30sArp1 |47 |30 másodperc |1 |
| PrimaryCloudRpo5m |235 |5 perc |None |

### <a name="performance-metrics"></a>Teljesítmény-mérőszámok

A táblázat összefoglalja a teljesítmény-mérőszámok és a központi telepítésben lévő is mért számlálókat.

| Metrika | Számláló |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Elérhető memória |\Memory\Available (MB) |
| IO |\PhysicalDisk(_Total)\Disk Transfers/sec |
| Virtuális gép olvasási művelet/mp (IOPS) |\Hyper-V virtuális tárolóeszköz (<VHD>) \Read művelet/mp |
| Virtuális gép (IOPS) írási művelet/mp |\Hyper-V virtuális tárolóeszköz (<VHD>) \Write művelet/mp |
| Virtuális gép olvasási teljesítménye |\Hyper-V virtuális tárolóeszköz (<VHD>) \Read bájt/mp |
| Virtuális gép a lemezírás teljesítménye |\Hyper-V virtuális tárolóeszköz (<VHD>) \Write bájt/mp |

## <a name="next-steps"></a>További lépések

[A replikáció beállítása](hyper-v-vmm-disaster-recovery.md)
