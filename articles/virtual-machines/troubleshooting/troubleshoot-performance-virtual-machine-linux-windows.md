---
title: Az Azure virtuális gépek teljesítményének hibái Linuxon vagy Windowson
description: Ez a cikk ismerteti a virtuális gép (VM) általános teljesítményhibaelhárítás a szűk keresztmetszetek figyelése és megfigyelése révén, és lehetséges szervizelése az esetlegesen előforduló problémák.
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
ms.openlocfilehash: 176b0634fe2c7ee2f47162e439c4ea16bde77a8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772618"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Az Azure virtuális gépek teljesítményének hibái Linuxon vagy Windowson

Ez a cikk ismerteti a virtuális gép (VM) általános teljesítményhibaelhárítás a szűk keresztmetszetek figyelése és megfigyelése révén, és lehetséges szervizelése az esetlegesen előforduló problémák. A figyelés mellett használhatja a Perfinsights-ot is, amely az IO/CPU/Memory használatával kapcsolatos ajánlott eljárásokkal és az IO/CPU/Memory kulcsfontosságú szűk keresztmetszetekkel rendelkező jelentést is tartalmaz. Perfinsights érhető el a [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) és [a Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) virtuális gépek az Azure-ban.

Ez a cikk végigvezeti a figyelés használatával diagnosztizálni teljesítmény szűk keresztmetszetek.

## <a name="enabling-monitoring"></a>Figyelés engedélyezése

### <a name="azure-iaas-virtual-machine-monitoring"></a>Az Azure IAAS virtuálisgépek figyelése

A vendég virtuális gép figyeléséhez használja az Azure virtuális gép figyelése, amely figyelmezteti az egyes magas szintű erőforrás-feltételek. Annak ellenőrzéséhez, hogy engedélyezve van-e a virtuális gép diagnosztikája, olvassa el az [Azure Resource naplók áttekintése című témakört.](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) Ha a következőket látja, akkor valószínűleg nincs engedélyezve a diagnosztika:

![A figyelés nincs engedélyezve](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>Virtuálisgép-diagnosztika engedélyezése a Microsoft Azure Portalon keresztül

Virtuálisgép-diagnosztika engedélyezése:

1. Ugrás a virtuális gépre
2. Kattintson **a Diagnosztikai beállítások parancsra**
3. Jelölje ki a tárfiókot, és kattintson **a Vendégszintű figyelés engedélyezése**elemre.

   ![Kattintson a Beállítások, majd a Diagnosztika parancsra](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

A Diagnosztikai beállításokhoz használt tárfiókot az **Ügynök** lapon, a **Diagnosztikai beállítások**területen ellenőrizheti.

![Tárfiók ellenőrzése](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>Tárfiók-diagnosztika engedélyezése az Azure Portalon keresztül

A tárolás nagyon fontos szint, amikor egy virtuális gép I/O-teljesítményét kívánjuk elemezni az Azure-ban. A tárolással kapcsolatos metrikák hoz unk, hogy engedélyezze a diagnosztika, mint egy további lépés. Ez is engedélyezhető, ha csak a tárolással kapcsolatos számlálókat szeretnénk elemezni.

1. A virtuális gép kiválasztásával azonosítsa, hogy a virtuális gép melyik tárfiókot (vagy fiókokat) használja. Kattintson **a Beállítások**gombra, majd a **Lemezek gombra:**

   ![Kattintson a Beállítások, majd a Lemezek elemre](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. A portálon nyissa meg a virtuális gép tárfiókját (vagy fiókjait), és hajtsa végre a következő lépéseket:

   1. Kattintson a fenti lépéssel talált tárfiók áttekintése elemre.
   2. Az alapértelmezett mérőszámok jelennek meg. 

    ![Alapértelmezett mutatók](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Kattintson bármelyik metrikák, amely megmutatja egy másik panel több lehetőséget konfigurálására és hozzáadására metrikák.

   ![Mérőszámok hozzáadása](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

A beállítások konfigurálása:

1.  Válassza a **Metrika** lehetőséget.
2.  Válassza ki az **erőforrást** (tárfiókot).
3.  A **névtér kijelölése**
4.  Válassza a **Metrikus lehetőséget.**
5.  Az **összesítés** típusának kiválasztása
6.  Ezt a nézetet rögzítheti az irányítópulton.

## <a name="observing-bottlenecks"></a>A szűk keresztmetszetek megfigyelése

Miután átestünk a szükséges metrikák kezdeti beállítási folyamatán, és a virtuális gép és a kapcsolódó tárfiók diagnosztikáját engedélyező könyvelést követően áttérhetünk az elemzési fázisra.

### <a name="accessing-the-monitoring"></a>Hozzáférés a megfigyeléshez

Válassza ki a vizsgálni kívánt Azure virtuális gép, és válassza **a figyelés**lehetőséget.

![Figyelés kiválasztása](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>A megfigyelés idővonalai

Annak megállapításához, hogy vannak-e erőforrás-szűk keresztmetszetek, tekintse át az adatokat. Ha úgy találja, hogy a gép e szerint jól működik, de a rról számoltak be, hogy a teljesítmény a közelmúltban csökkent, tekintse át az adatok időtartományát, amely magában foglalja a teljesítménymetrikus adatokat a jelentett módosítás előtt, a probléma alatt és után.

### <a name="check-for-cpu-bottleneck"></a>Cpu-szűk keresztmetszetének ellenőrzése

![Cpu-szűk keresztmetszetek ellenőrzése](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. A grafikon szerkesztése.
2. Állítsa be az időtartományt.
3. Ezután hozzá kell adnia a számláló: CPU százalékos vendég operációs rendszer
4. Mentse el.

### <a name="cpu-observe-trends"></a>CPU megfigyelni trendek

Ha megnézzük a teljesítményproblémákat, legyen tisztában a trendekkel, és értse meg, ha azok hatással vannak Önre. A következő szakaszokban a figyelési grafikonok a portáltrendek megjelenítéséhez. Ezek akkor is hasznosak lehetnek, ha a különbség erőforrás-viselkedések ugyanazon időszakban történő kereszthivatkozásához. A grafikonok testreszabásához kattintson az [Azure Monitor adatplatformjára.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)

Spiking - Spiking lehet kapcsolatban egy ütemezett feladat / ismert esemény. Ha azonosítani tudja a feladatot, határozza meg, hogy a feladat a kívánt teljesítményszinten fut-e. Ha a teljesítmény elfogadható, előfordulhat, hogy nem kell növelnie az erőforrásokat.

Tüske és állandó – gyakran azt jelzi, egy új számítási feladatok. Ha ez nem egy felismert számítási feladatok, engedélyezze a figyelés a virtuális gép, hogy megtudja, milyen folyamat (vagy folyamatok) okozza a viselkedést. A folyamat felismerése után határozza meg, hogy a megnövekedett fogyasztást nem hatékony kód vagy normál fogyasztás okozza-e. Normál fogyasztás esetén döntse el, hogy a folyamat a kívánt teljesítményszinten működik-e.

Állandó – határozza meg, hogy a virtuális gép mindig ezen a szinten fut, vagy ha csak akkor fut ezen a szinten, mivel a diagnosztika engedélyezve lett. Ha igen, azonosítsa a problémát okozó folyamatot (vagy folyamatokat), és fontolja meg az erőforrás további hozzáadását.

Folyamatosan növekvő – A fogyasztás folyamatos növekedése gyakran vagy nem hatékony kód, vagy egy folyamat, amely több felhasználói munkaterhelést.

### <a name="high-cpu-utilization-remediation"></a>Magas CPU-kihasználtsági javítás

Ha az alkalmazás vagy a folyamat nem a megfelelő teljesítményszinten fut, és 95% + CPU-kihasználtsági állandót lát, az alábbi feladatok bármelyikét elvégezheti:

* Azonnali megkönnyebbülés - a virtuális gép méretének növelése több maggal
* Ismerje meg a problémát – keresse meg az alkalmazást/folyamatot, és ennek megfelelően hárítsa el a hibaelhárítást.

Ha megnövelte a virtuális gép, és a CPU továbbra is fut 95%, határozza meg, hogy ez a beállítás kínál jobb teljesítményt vagy nagyobb alkalmazás átviteli teljesítmény elfogadható szinten. Ha nem, hárítsa el az egyes alkalmazás\folyamat hibáit.

A Perfinsights for [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) segítségével elemezheti, hogy melyik folyamat hajtja a CPU-fogyasztást. 

## <a name="check-for-memory-bottleneck"></a>Memóriaszűk keresztmetszet ellenőrzése

A mérőszámok megtekintése:

1. Szakasz hozzáadása.
2. Csempének hozzáadása.
3. Nyisd meg a Galériát.
4. Jelölje ki a Memóriahasználat lehetőséget, és húzza a mutatót. Amikor a csempe dokkolva van, kattintson a jobb gombbal, és válassza a **6x4**parancsot.

### <a name="memory-observe-trends"></a>Memória megfigyelni trendek

A memóriahasználat megmutatja, hogy mennyi memóriát használ fel a virtuális gép. Ismerje meg a trend, és hogy térképek az idő, amikor megjelenik kérdéseket. Mindig több mint 100 MB szabad memóriával kell rendelkeznie.

Tüske és állandó/állandó állandó felhasználás – Előfordulhat, hogy a magas memóriakihasználtság nem okoz rossz teljesítményt, mivel egyes alkalmazások, például a relációs adatbázis-motorok nagy mennyiségű memóriát foglalnak le, és ez a kihasználtság nem jelentős. Ha azonban több memóriaéhes alkalmazás van, gyenge teljesítmény jelenhet meg a memóriaversengésből, ami vágást és lapozást/ lemezre cserélést okoz. Ez a gyenge teljesítmény gyakran észrevehető oka az alkalmazás teljesítményének hatása.

Folyamatosan növekvő fogyasztás - Egy lehetséges alkalmazás "felmelegedés", ez a fogyasztás gyakori az adatbázis-motorok induló. Ugyanakkor jelezheti egy alkalmazás memóriavesztését is. Azonosítsa az alkalmazást, és ismerje meg, hogy a viselkedés várható-e.

Page vagy Swap File Usage – Ellenőrizze, hogy a Windows lapozófájlt (a D:\) vagy a Linux Swap fájlon (amely en `/dev/sdb`található) használja-e, erősen használják-e. Ha ezeken a köteteken nincs semmi, kivéve ezeket a fájlokat, ellenőrizze, hogy nincs-e magas olvasási/írási műveletek ezeken a lemezeken. Ez a probléma alacsony memóriaállapotra utal.

### <a name="high-memory-utilization-remediation"></a>Nagy memóriakihasználtság immára

A nagy memóriakihasználtság feloldásához hajtsa végre az alábbi feladatok valamelyikét:

* Azonnali megkönnyebbülés vagy lap vagy fájlcsere használata – növelje a virtuális gép méretét egy több memóriával, majd a monitor.
* Ismerje meg a problémát – keresse meg az alkalmazásokat/folyamatokat, és hárítsa el a nagy fogyasztású memóriaalkalmazások azonosítását.
* Ha ismeri az alkalmazást, nézze meg, hogy a memóriafoglalás korlátozható-e.

Ha egy nagyobb virtuális gépre való frissítés után felfedezi, hogy 100%-ig folyamatosan folyamatosan növekszik, azonosítsa az alkalmazást/folyamatot és hibaelhárítást.

A Perfinsights for [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) segítségével elemezheti, hogy melyik folyamat hajtja a memóriafelhasználást. 

## <a name="check-for-disk-bottleneck"></a>Lemezszűk keresztmetszete

A virtuális gép tárolási alrendszerének ellenőrzéséhez ellenőrizze a diagnosztikát az Azure virtuális gép szintjén a virtuális gép diagnosztika számlálói, valamint a tárfiók diagnosztika használatával.

A virtuális gép specifikus hibaelhárítás, használhatja a Perfinsights [for Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux), amely segíthet elemezni, hogy melyik folyamat hajtja az I.-es. 

Vegye figyelembe, hogy a zónaredundáns és prémium szintű tárfiókok számlálói nem rendelkeznek. A számlálókkal kapcsolatos problémák esetén vess fel támogatási esetet.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>Tárfiók diagnosztikájának megtekintése a figyelésben

Az alábbi elemeken való munkához lépjen be a portálon lévő virtuális gép tárfiókjába:

![A tárfiók diagnosztikájának megtekintése a figyelésben](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. A figyelési grafikon szerkesztése.
2. Állítsa be az időtartományt.
3. Adja hozzá az alábbi lépésekben ismertetett számlálókat.
4. Mentse a módosításokat.

### <a name="disk-observe-trends-standard-storage-only"></a>A lemez betartja a trendeket (csak normál tárolásesetén)

A tárolási problémák azonosításához tekintse meg a teljesítmény metrikák a tárfiók diagnosztika és a virtuális gép diagnosztika.

Az alábbi ellenőrzéshez keresse meg a legfontosabb trendeket, amikor a problémák a probléma időtartományán belül jelentkeznek.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Az Azure-tárhely rendelkezésre állásának ellenőrzése – A tárfiók mérőszámának hozzáadása: rendelkezésre állás

Ha a rendelkezésre állás csökkenését látja, előfordulhat, hogy probléma van a platformmal, ellenőrizze az [Azure állapotát.](https://azure.microsoft.com/status/) Ha ott nem jelenik meg probléma, új támogatási kérelmet kell felvetnie.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Az Azure-táridő-időmegadás ellenőrzése – Adja hozzá a tárfiók metrikáit:

* ClientTimeOuterror (Ügyfélidő-hiba)
* ServerTimeOuthiba
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

A *TimeOutError metrikák értékei azt jelzik, hogy egy I/O-művelet túl sokáig tartott, és időtúlodott. A következő lépések megsegítik a lehetséges okok azonosítását.

Az AverageServerLatency növekedése a TimeOutErrors-nál platformprobléma lehet. Ebben a helyzetben új támogatási kérelmet kell felvetnie.

Az AverageE2ELatency az ügyfél késését jelöli. Ellenőrizze, hogy az alkalmazás hogyan hajtja végre az IOPS-t. Keressen egy növekedést vagy folyamatosan magas TotalRequests metrikát. Ez a metrika az IOPS-t jelöli. Ha a tárfiók vagy az egyetlen virtuális merevlemez korlátait kezdi meg, a késés a szabályozással függhet össze.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Az Azure storage-szabályozás ellenőrzése – A tárfiók metrikáinak hozzáadása: Szabályozási hiba

A sávszélesség-szabályozás értékei azt jelzik, hogy a rendszer a tárfiók szintjén van szabályozva, ami azt jelenti, hogy a fiók IOPS-korlátjának leválasztása. A **TotalRequests**metrika ellenőrzésével meghatározhatja, hogy eléri-e az I/P-küszöbértéket.

Vegye figyelembe, hogy minden virtuális merevlemez 500 IOPS-vagy 60 MBit-es korláttal rendelkezik, de a tárfiókonként 20000 IOPS összesített korlát köti.

Ezzel a metrikával nem tudja megmondani, hogy melyik blob okozza a szabályozást, és amelyek et érinti. Azonban vagy az IOPS vagy a tárfiók be- és kimenő forgalom korlátját.

Annak megállapításához, ha eléri az IOPS-korlátot, lépjen be a storage-fiók diagnosztikája, és ellenőrizze a TotalRequests, keresve, hogy ha közeledik 20 ezer TotalRequests. Azonosítsa a minta változását, hogy először látja-e a korlátot, vagy hogy ez a korlát egy adott időpontban történik-e.

A standard szintű tárban található új lemezajánlatok esetén az IOPS- és az átviteli-átviteli korlátok eltérőek lehetnek, de a standard tárfiók összesített korlátja 20000 IOPS(Prémium szintű storage fiók- vagy lemezszinten különböző korlátokkal rendelkezik). További információ a különböző szabványos tárolólemez-ajánlatokról és lemezenkénti korlátokról:

* [Méretezhetőségi és teljesítménycélok a Windows virtuálisgép-lemezeihez.](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)

#### <a name="references"></a>Referencia

* [Méretezhetőségi és teljesítménycélok a prémium szintű lapblobstorage-fiókokhoz](../../storage/blobs/scalability-targets-premium-page-blobs.md)

A tárfiók sávszélességét a tárfiók metrikák: TotalIngress és TotalEgress mérik. A redundancia típusától és a régióktól függően különböző sávszélesség-küszöbértékekkel rendelkezik.

* [Méretezhetőségi és teljesítménycélok a szabványos tárfiókokhoz](../../storage/common/scalability-targets-standard-account.md)

Ellenőrizze a TotalIngress és totalegress a be- és kimenő forgalom korlátok a tárfiók redundancia típusa és régió.

Ellenőrizze a virtuális géphez csatlakoztatott virtuális gépek átviteli korlátját. Adja hozzá a virtuális gép metrikái lemez olvasási és írási.

A standard szintű tároló új lemezkínálatai eltérő IOPS- és átviteli-átviteli korlátozásokkal rendelkeznek (az IOPS nem jelenik meg virtuális merevlemezenként). Tekintse meg az adatokat, hogy ha a virtuális merevlemez(ek) kombinált átviteli sebességű MB a virtuális gép szintjén a lemez olvasása és írása, majd optimalizálja a virtuális gép tárolási konfigurációját a méretezés i single VHD korlátok. További információ a különböző szabványos tárolólemez-ajánlatokról és lemezenkénti korlátokról:

* [Méretezhetőségi és teljesítménycélok a Windows virtuálisgép-lemezeihez.](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)

### <a name="high-disk-utilizationlatency-remediation"></a>Magas lemezkihasználtság/késés helyreállítása

Az ügyfélkésés csökkentése és a virtuális gép im-io optimalizálása a virtuális merevlemez-korlátokon túli méretezéshez

* [Az Io windowsos optimalizálása az Azure-ban](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [Az IO linuxos optimalizálása az Azure-ban](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>A szabályozás csökkentése

Ha a tárfiókok felső határát éri el, újra kiegyenlíti a virtuális gépek et a tárfiókok között. Tekintse meg az [Azure Storage méretezhetőségi és teljesítménycélok](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/)című részt.

### <a name="increase-throughput-and-reduce-latency"></a>Az átviteli képesség növelése és a késés csökkentése

Ha egy késésérzékeny alkalmazást, és nagy átviteli sebességű, telepítse át a virtuális gépek az Azure Premium storage a DS és a GS sorozat virtuális gép használatával.

Ezek a cikkek a konkrét forgatókönyveket ismertetik:

* [Áttelepítés Azure Premium Storage-ba](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [Az Azure Premium Storage használata az SQL Server rel](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége a cikk bármely pontján, forduljon az Azure szakértőihez [az MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/)

Másik lehetőségként nyújtson be egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
