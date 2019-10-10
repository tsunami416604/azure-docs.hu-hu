---
title: Általános teljesítmény-hibaelhárítás Linux vagy Windows rendszerű Azure-beli virtuális gépekhez
description: Ez a cikk a virtuális gép (VM) általános teljesítményének hibaelhárítását ismerteti a szűk keresztmetszetek figyelésével és megfigyelésével, valamint az esetlegesen felmerülő problémák megoldásával kapcsolatos esetleges szervizelést.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: fc8cc4834997033203376cd33670cc907e2911e7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170295"
---
# <a name="generic-performance-troubleshooting-for-azure-virtual-machine-running-linux-or-windows"></a>Általános, teljesítménnyel kapcsolatos hibaelhárítás Linux vagy Windows rendszeren futó Azure virtuális gépekhez

Ez a cikk a virtuális gép (VM) általános teljesítményének hibaelhárítását ismerteti a szűk keresztmetszetek figyelésével és megfigyelésével, valamint az esetlegesen felmerülő problémák megoldásával kapcsolatos esetleges szervizelést.

## <a name="enabling-monitoring"></a>Figyelés engedélyezése

### <a name="azure-iaas-virtual-machine-monitoring"></a>Azure IAAS-beli virtuális gépek figyelése

A vendég virtuális gép figyeléséhez használja az Azure-beli virtuális gépek figyelését, amely riasztást küld az egyes magas szintű erőforrás-feltételekről. Ellenőrizze, hogy engedélyezve van-e a virtuálisgép-diagnosztika, lásd: [Azure-erőforrások naplói – áttekintés](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview#collecting-resource-logs). Ha a következő jelenik meg, akkor valószínűleg nincs engedélyezve a diagnosztika:

![A figyelés nincs engedélyezve](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>A VM Diagnostics engedélyezése a Microsoft Azure Portal

A VM-diagnosztika engedélyezéséhez nyissa meg a virtuális gépet, kattintson a **Beállítások**, majd a **diagnosztika**elemre.

![Kattintson a beállítások, majd a diagnosztika elemre.](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)
 
### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>A Storage-fiók diagnosztika engedélyezése Azure Portal

Először határozza meg, hogy melyik Storage-fiókot (vagy-fiókokat) használja a virtuális gép a virtuális gép kiválasztásával. Kattintson a **Beállítások**, majd a **lemezek**elemre:

![Kattintson a beállítások, majd a lemezek elemre.](media/troubleshoot-performance-virtual-machine-linux-windows/3-storage-disks-disks-selection.png)

A portálon lépjen a virtuális gép Storage-fiókjához (vagy fiókjaihoz), és végezze el a következő lépéseket:

![BLOB metrikáinak kiválasztása](media/troubleshoot-performance-virtual-machine-linux-windows/4-select-blob-metrics.png)
 
1. Válassza **a minden beállítás**lehetőséget.
2. Diagnosztika bekapcsolása.
3. Válassza a  **blob* -metrikák** lehetőséget, és állítsa be az adatmegőrzést **30** napra.
4. Mentse a módosításokat.

## <a name="observing-bottlenecks"></a>Szűk keresztmetszetek megfigyelése

### <a name="accessing-the-monitoring"></a>A figyelés elérése

Válassza ki a vizsgálni kívánt Azure-beli virtuális gépet, és válassza a **figyelés**lehetőséget.

![Figyelés kiválasztása](media/troubleshoot-performance-virtual-machine-linux-windows/5-observe-monitoring.png)
 
### <a name="timelines-of-observation"></a>Megfigyelési határidők

Annak megállapításához, hogy van-e erőforrás-szűk keresztmetszete, tekintse át az adatait. Ha úgy találja, hogy a számítógépe nem működik megfelelően, de a rendszer azt jelezte, hogy a teljesítmény a közelmúltban csökkent, tekintse át a teljesítménnyel kapcsolatos mérőszámok adatait tartalmazó időtartományt a jelentett változás előtt és után.

### <a name="check-for-cpu-bottleneck"></a>A CPU szűk keresztmetszetének keresése

![A CPU szűk keresztmetszetének keresése](media/troubleshoot-performance-virtual-machine-linux-windows/6-cpu-bottleneck-time-range.png)

1. Szerkessze a diagramot.
2. Állítsa be az időtartományt.
3. Ezután fel kell vennie a következőt: CPU százalék vendég operációs rendszer
4. Mentse el.

### <a name="cpu-observe-trends"></a>A CPU megfigyelésének trendjei

Ha teljesítménnyel kapcsolatos problémákat tapasztal, vegye figyelembe a trendeket, és Ismerje meg, ha azok érintik Önt. A következő fejezetekben a portálon a figyelési grafikonokat használjuk a trendek megjelenítéséhez. Emellett hasznosak lehetnek az erőforrás-viselkedésre vonatkozó kereszthivatkozások egyazon időszakban. A diagramok testreszabásához kattintson [Azure monitor adatplatformra](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform).

Beszúró – a beszúró folyamat egy ütemezett feladathoz/ismert eseményhez kapcsolódhat. Ha azonosítani tudja a feladatot, állapítsa meg, hogy a feladat a szükséges teljesítményszint szerint fut-e. Ha a teljesítmény elfogadható, előfordulhat, hogy nem kell megemelnie az erőforrásokat.

Tüskén vagy állandó – gyakran egy új munkaterhelést jelez. Ha nem ismeri fel a munkaterhelést, a virtuális gépen engedélyezze a figyelést, hogy megtudja, milyen folyamat (vagy folyamatok) okozza a működést. A folyamat felismerése után állapítsa meg, hogy a megnövekedett felhasználás a nem hatékony kód vagy a normál fogyasztás okozta-e. Normál fogyasztás esetén döntse el, hogy a folyamat a szükséges teljesítményszint szerint működik-e.

Konstans – megállapítja, hogy a virtuális gép mindig ezen a szinten fut-e, vagy csak ezen a szinten fut, mivel a diagnosztika engedélyezve van. Ha igen, azonosítsa a problémát okozó folyamatot (vagy folyamatokat), és vegye fontolóra az erőforrás további részét.

Folyamatosan növekvő – A felhasználás állandó növekedése gyakran vagy nem hatékony kód, vagy egy folyamat, amely több felhasználói munkaterhelést vesz igénybe.

### <a name="high-cpu-utilization-remediation"></a>Magas CPU-kihasználtság szervizelése

Ha az alkalmazás vagy folyamat nem a megfelelő teljesítményszint fut, és a 95% + CPU-kihasználtsági állandót látja, akkor a következő feladatok egyikét hajthatja végre:

* Azonnali használatra – növelje a virtuális gép méretét több maggal rendelkező méretre
* A probléma megismerése – az alkalmazás/folyamat és a hibakeresés megkeresése ennek megfelelően.

Ha megnövelte a virtuális gépet, és a processzor továbbra is 95%-ot futtat, állapítsa meg, hogy ez a beállítás a jobb teljesítményt vagy magasabb szintű alkalmazások átviteli sebességét kínálja elfogadható szintre. Ha nem, akkor hárítsa el az egyes application\process.

## <a name="check-for-memory-bottleneck"></a>Memória szűk keresztmetszetének keresése

A metrikák megtekintése:

1. Szakasz hozzáadása.
2. Csempe hozzáadása
3. Nyissa meg a gyűjteményt.
4. Válassza ki a memóriahasználat és a húzás elemet. Ha a csempe dokkolva van, kattintson a jobb gombbal, és válassza a **6x4**lehetőséget.

### <a name="memory-observe-trends"></a>Memória megfigyelésének trendjei

A memóriahasználat megmutatja, hogy mennyi memóriát használ a virtuális gép. Ismerje meg a trendet, és azt, hogy a probléma mikor jelenik meg. Mindig több mint 100 MB szabad memóriával kell rendelkeznie.

Spike és állandó/állandó fogyasztás – a nagy memória kihasználtsága nem lehet a rossz teljesítmény oka, mivel egyes alkalmazások, például a viszonyítási adatbázisok nagy mennyiségű memóriát foglalnak le, és ez a kihasználtság nem jelent jelentős értéket. Ha azonban több memóriára éhes alkalmazás van, előfordulhat, hogy gyenge teljesítményt tapasztal a memóriában, így a vágás és a lapozás/csere lemezre történik. Ez a gyenge teljesítmény gyakran észlelhető az alkalmazás teljesítményére gyakorolt hatás miatt.

Folyamatosan növekvő fogyasztás – egy lehetséges "felmelegedés" alkalmazás, ez a fogyasztás gyakori az adatbázis-hajtóműveknél. Azonban egy adott alkalmazásban memóriavesztés is lehet. Azonosítsa az alkalmazást, és Ismerje meg, hogy a működés várható-e.

Oldal-vagy lapozófájl-használat – győződjön meg arról, hogy a Windows lapozófájlt használja (a D: \) vagy a Linux swap-fájl (`/dev/sdb`) helyen található-e. Ha ezekkel a kötetekkel nem rendelkezik ezekkel a fájlokkal, akkor ellenőrizze, hogy vannak-e magas olvasási/írási műveletek a lemezeken. Ez a probléma kevés memóriát jelez.

### <a name="high-memory-utilization-remediation"></a>Nagy memória-kihasználtság szervizelése

A magas memóriahasználat feloldásához hajtsa végre a következő feladatok bármelyikét:

* Azonnali támogatás vagy lapozófájl használata esetén – növelje a virtuális gép méretét több memóriával, majd figyelje a figyelést.
* A probléma ismertetése – alkalmazások/folyamat és hibakeresés a nagy mennyiségű memóriabeli alkalmazások azonosításához.
* Ha ismeri az alkalmazást, tekintse meg a memória kiosztásának korlátját.

Ha egy nagyobb virtuális gépre való frissítés után kiderül, hogy továbbra is állandó a 100%-os folyamatos növekedés, azonosítsa az alkalmazást/folyamatot és a hibakeresést.

## <a name="check-for-disk-bottleneck"></a>A lemez szűk keresztmetszetének keresése

A virtuális gép tárolási alrendszerének ellenőrzéséhez tekintse meg a diagnosztika az Azure-beli virtuálisgép-szinten a virtuális gép diagnosztika és a Storage-fiók diagnosztika használatával.

Ne feledje, hogy a zónák redundáns és Premium Storage fiókjaihoz nem tartoznak számlálók. Az ezekkel a számlálókkal kapcsolatos problémák esetén egy támogatási esetet kell megnövelni.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>A Storage-fiók diagnosztika megtekintése a figyelésben

Az alábbi elemek működéséhez lépjen a virtuális gép Storage-fiókjába a portálon:

![A Storage-fiók diagnosztika megtekintése a Figyelésben](media/troubleshoot-performance-virtual-machine-linux-windows/7-virtual-machine-storage-account.png)

1. Szerkessze a figyelési gráfot.
2. Állítsa be az időtartományt.
3. Adja hozzá az alábbi lépésekben ismertetett számlálókat.
4. Mentse a módosításokat.

### <a name="disk-observe-trends-standard-storage-only"></a>A lemezek megfigyelésének trendjei (csak standard Storage)

A tárterülettel kapcsolatos problémák azonosításához tekintse meg a Storage-fiók diagnosztika és a virtuálisgép-diagnosztika teljesítmény-mérőszámait.

Az alábbi ellenőrzéseknél keresse meg a legfontosabb trendeket, amikor a probléma időtartományán belül előforduló problémák történnek.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Az Azure Storage rendelkezésre állásának keresése – a Storage-fiók metrikájának hozzáadása: rendelkezésre állás

Ha a rendelkezésre állás csökkenését tapasztalja, akkor a platformmal kapcsolatos probléma lehet az [Azure állapotának](https://azure.microsoft.com/status/)vizsgálatával. Ha nem jelenik meg probléma, adjon meg egy új támogatási kérelmet.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Az Azure Storage időtúllépésének keresése – a Storage-fiók metrikáinak hozzáadása:

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* Averageserverlatency értéket mutatnak
* TotalRequests

A * TimeOutError mérőszámokban szereplő értékek azt jelzik, hogy egy IO-művelet túl sokáig tartott, és időtúllépés történt. A következő lépések elvégzése segít azonosítani a lehetséges okokat.

A Averageserverlatency értéket mutatnak egy időben nő a TimeOutErrors. Ebben a helyzetben új támogatási kérést kell létrehoznia.

A AverageE2ELatency az ügyfél késését jelöli. Ellenőrizze, hogy az alkalmazás hogyan végzi el a IOPS. Keressen egy növekedést vagy állandóan magas TotalRequests mérőszámot. Ez a metrika a IOPS jelöli. Ha elindítja a Storage-fiók vagy egy virtuális merevlemez korlátait, a késés a szabályozáshoz köthető.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Az Azure Storage szabályozásának keresése – a Storage-fiók metrikáinak hozzáadása: ThrottlingError

A szabályozás értéke azt jelzi, hogy a tárolási fiók szintjén szabályozza a szabályozást, ami azt jelenti, hogy a fiók IOPS korlátja. A metrika **TotalRequests**ellenőrzésével meghatározhatja, hogy a IOPs küszöbértéket éri-e el.

Vegye figyelembe, hogy a virtuális merevlemezek 500 IOPS vagy 60 MBit-os korláttal rendelkeznek, de a IOPS 20000-os összesített korlátja köti őket.

Ezzel a metrikával nem állapítható meg, hogy melyik blob okozza a szabályozást, és melyeket érinti. Azonban a Storage-fiók IOPS vagy bejövő/kimenő korlátját is megkeresi.

Ha meg szeretné állapítani, hogy az IOPS-korlátot használja-e, lépjen be a Storage-fiók diagnosztikát, és ellenőrizze a TotalRequests, és nézze meg, hogy közeledik-e a 20000 TotalRequests. Azonosíthatja a minta változását, függetlenül attól, hogy a korlátot az első alkalommal látja-e, vagy hogy a korlát egy adott időpontban történik.

#### <a name="references"></a>Tudástár

* [A virtuálisgép-lemezek skálázhatósági céljai](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)

A Storage-fiók sávszélességét a Storage-fiók Metrikái határozzák meg: TotalIngress és TotalEgress. A sávszélesség a redundancia és a régiók típusától függően eltérő küszöbértékekkel rendelkezik.

* [A Blobok, várólisták, táblák és fájlok skálázhatósági céljai](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-blobs-queues-tables-and-files)

A TotalIngress és a TotalEgress a Storage-fiók redundancia-típusa és-régiója esetében a bejövő és a kimenő forgalomra vonatkozó korlátok között tájékozódhat.

A virtuális géphez csatlakoztatott virtuális merevlemezek átviteli sebességére vonatkozó korlátok ellenőrzéséhez. Adja hozzá a virtuális gép metrikai lemezét olvasás és írás.

A virtuális merevlemezek legfeljebb 60 MB/s-ot tudnak támogatni (a IOPS nem teszik közzé VHD-n). Tekintse meg az adatait, és ellenőrizze, hogy a virtuális merevlemezen a VHD (k) kombinált átviteli sebessége (i) a lemez olvasása és írása paranccsal, majd a virtuális gép tárolási konfigurációjának optimalizálása a múltbeli egyszeri VHD-korlátok skálázására.

### <a name="high-disk-utilizationlatency-remediation"></a>Nagy lemezterület-kihasználtság/késés szervizelése

Csökkentse az ügyfelek késését, és optimalizálja a virtuális gépek IO-jét a korábbi VHD-korlátok méretezéséhez

* [A Windows rendszerhez készült IO optimalizálása az Azure-ban](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Az IO for Linux optimalizálása az Azure-ban](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>A szabályozás csökkentése

Ha a Storage-fiókok felső korlátait sújtja, a virtuális merevlemezek átkerülnek a Storage-fiókok között. Tekintse át az [Azure Storage skálázhatósági és teljesítményi célkitűzéseit](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/).

### <a name="increase-throughput-and-reduce-latency"></a>Az átviteli sebesség növelése és a késés csökkentése

Ha késésre érzékeny alkalmazásra van szüksége, és magas átviteli sebességet igényel, telepítse át a virtuális merevlemezeket az Azure Premium Storage-ba a DS és a GS sorozatú virtuális gép használatával.

Ezek a cikkek a konkrét forgatókönyveket tárgyalják:

* [Áttelepítés Azure Premium Storage-ba](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Az Azure Premium Storage használata a SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>Következő lépések

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/).

Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.
