---
title: "Az Azure figyelő Metrikaböngésző |} Microsoft Docs"
description: "A figyelő Metrikaböngészőben Azure új szolgáltatásainak megismerése"
author: vgorbenko
manager: Victor.Mushkatin
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.openlocfilehash: 537dd6d64fe49093dd73d8040cde5a9153a7bd5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor-metrics-explorer"></a>Az Azure figyelő Metrikaböngésző

Ez az útmutató ismerteti a következő generációs Azure figyelő metrikák diagramkészítési élményt nyilvános előzetes verzió jelenleg. Az új felület megjelenítési diagramok többdimenziós metrikákat, és nincs dimenzió alapvető metrikák támogatja. Dolgozunk a különböző típusú, a több erőforrás-csoportok és az előfizetések metrikák átfedő diagramokat is. A többdimenziós metrikák diagramok dimenzió szűrők alkalmazása, valamint a csoportosítása szerint testre szabható. Bármelyik olyan diagram, beleértve a testreszabott diagramok irányítópultok rögzíthet.

Ha a régi felülethez nincs dimenzió alapvető metrikák támogató kapcsolatos információkat keres, tekintse át "Elérése a portálon metrikák" című szakaszban található a [Microsoft Azure metrikák áttekintése útmutató](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="what-is-azure-monitor-metrics-explorer"></a>Mi az Azure-figyelő Metrikaböngésző?

Az Azure figyelő Metrikaböngésző része egy a Microsoft Azure portál, amely lehetővé teszi, hogy a diagramok ábrázolásához, vizuálisan adatok a trendek és igényeiben jelentkező vizsgálja, és a metrikák értékek esik. Metrikaböngésző egy alapvető kiindulópont azon különböző teljesítmény- és az alkalmazások és az infrastruktúra Azure szolgáltatásban üzemeltetett vagy az Azure-figyelő szolgáltatás által figyelt elérhetőségével kapcsolatos problémákat vizsgál. 

## <a name="what-are-metrics-in-azure"></a>Mik az Azure-ban mérőszámok?

A Microsoft Azure-ban mérőszámok mért értékek és a gyűjtött és tárolt időbeli számok sorozatát. Nincsenek, standard (vagy "platform") metrikákat és egyéni metrikákra vonatkozóan egyaránt. A szabványos metrikák rendszer által biztosított az Azure platformon, saját magát. Standard metrikák tükrözze az Azure-erőforrások állapotadatok és használati statisztikáit. Mivel egyéni metrikák Azure a használó alkalmazások által küldött a [Application Insights API egyéni események](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Egyéni metrikákat az Application Insights-erőforrások együtt más alkalmazás adott mérőszámok vannak tárolva.

## <a name="what-are-multi-dimensional-metrics"></a>Mik azok a többdimenziós metrikák?

Azure-erőforrások számos többdimenziós metrikák most teszi közzé. A metrikák egy vagy több elnevezett dimenzió több sorozatát nyomon követéséhez. Például a "rendelkezésre álló lemezterület" metrika lehet a dimenzió néven "Meghajtó" értékek "C:", "D:", amely lehetővé teszi a megtekintése vagy rendelkezésre álló szabad lemezterület az összes meghajtó vagy az egyes meghajtók külön-külön. 

Az alábbi példában látható módon a "Hálózati átviteli" nevű elméleti metrika két adathalmaz. Az első adatkészlet nem dimenziója van. A második dataset két dimenzió, "IP-cím" és "Irány" az értékek láthatók:

### <a name="network-throughput"></a>Hálózati teljesítmény
(Ez a metrika rendelkezik nincs dimenzió)

 |időbélyeg        | Metrika értékét | 
   | ------------- |:-------------| 
   | 8/9/2017 8:14 | 1,331.8 kbit/s | 
   | 8/9/2017 8:15 | 1,141.4 kbit/s |
   | 8/9/2017 8:16 | 1,110.2 kbit/s |

Ez a metrika nem egydimenziós is csak egy alapszintű kérdésre adott válasz, például "Mi történt a hálózati átviteli sebesség egy adott időben?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Hálózati teljesítmény + két dimenzió ("IP-címe" és "Irány")

| időbélyeg          | "IP-címe" dimenzió | "Irány" dimenzió | Metrika értékét| 
   | ------------- |:-----------------|:------------------- |:-----------|  
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Irány "Send" =    | 646.5 kbit/s |
   | 8/9/2017 8:14 | IP = "192.168.5.2" | Irány "Kap" = | 420.1 kbit/s |
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Irány "Send" =    | 150.0 kbit/s | 
   | 8/9/2017 8:14 | IP = "10.24.2.15"  | Irány "Kap" = | 115,2 KB/s |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Irány "Send" =    | 515.2 kbit/s |
   | 8/9/2017 8:15 | IP = "192.168.5.2" | Irány "Kap" = | 371.1 kbit/s |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Irány "Send" =    | 155.0 kbit/s |
   | 8/9/2017 8:15 | IP = "10.24.2.15"  | Irány "Kap" = | 100.1 kbit/s |

Ez a metrika kérdésekre lehet például a "Mi történt az IP-címek hálózati átviteli?" és "mennyi adatot küldött és fogadott?" Többdimenziós metrikák lebonyolítására további elemzési és diagnosztikai érték nem egydimenziós metrikák képest. 

## <a name="how-do-i-create-a-new-chart"></a>Hogyan hozható létre új diagram?

   > [!NOTE]
   > Egyes szolgáltatásokat a régi metrikák felület még nem érhető el az új Metrikaböngésző. Közben az új felület előnézeti, továbbra is a régi (nem háromdimenziós) mérőszámok nézet használata Azure-figyelő. 

1. Nyissa meg az Azure-portálon
2. Keresse meg az új **figyelő** lapra, majd válassza ki **metrikák (előzetes verzió)**.

   ![Metrikák villámnézeti kép](./media/monitoring-metric-charts/001.png)

3. A **metrika választó** automatikusan nyitott meg lesz. A kapcsolódó metrikák megtekintéséhez listájából válasszon ki egy erőforrást. A listában megjelenő metrikák csak erőforrásokhoz.

   ![Metrikák villámnézeti kép](./media/monitoring-metric-charts/002.png)

   > [!NOTE]
   >Ha több Azure-előfizetéssel rendelkezik, az erőforrások közötti összes előfizetést a portál beállításait a kijelölt kimenő Metrikaböngésző ponttá előfizetések listával -> szűrő. Ha módosítani szeretné azt, kattintson a portál beállítások fogaskerék ikonra a képernyő felett, és válassza a melyik előfizetések fogják használni kívánt.

4. Néhány erőforrások típust (azaz a Storage-fiókok és a virtuális gépek), egy metrika kiválasztása előtt ki kell választani egy **Sub szolgáltatás**. Minden egyes sub szolgáltatás hordoz magában, ha a saját ítélt lényeges, csak a sub szolgáltatás, és nem más előfizetett szolgáltatások készlete.

   Például minden Azure Storage rendelkezik metrikáját subservices "Blobok", "Fájlok", "Várólisták" és "Tábla", amely a tárfiók összes részét képezik. Azonban a metrika "várólista-üzenetek száma" nem természetes bármely egyéb tároló-subservices a subservice "Várólista" és nem alkalmazható.

   ![Metrikák villámnézeti kép](./media/monitoring-metric-charts/003.png)

5. Jelöljön ki egy metrikát a listából. Ha tudja, hogy egy részleges név a mérőszám azt szeretné, megkezdheti a beírja azt az elérhető mérőszámok szűrt listájának megtekintéséhez:

   ![Metrikák villámnézeti kép](./media/monitoring-metric-charts/004.png)

6. Miután kiválasztott egy metrika, a diagram jelenik meg a kiválasztott metrika az alapértelmezett összesítés. Ezen a ponton csak kattintani távolabb a **metrikák választó** bezárásához. A diagram is váltani egy másik összesítő. Néhány metrikáihoz összesítési váltás lehetővé teszi annak meghatározását mely tekintse meg a diagram kívánt érték. Például megváltoztathatja az átlagos, minimális és maximális értékek között. 

7. A metrika hozzáadása ikonra kattintva ![metrika ikon](./media/monitoring-metric-charts/icon001.png) és lépéseket 3-6 ismétlődő adhat hozzá további metrikák ugyanezen a diagramon.

   > [!NOTE]
   > Általában nem szeretnénk metrikák különböző mértékegységben (azaz "ezredmásodperc" és "kilobájt") vagy jelentősen eltérő méretezési rendelkezzen egy diagram. Ehelyett érdemes lehet több diagram. Kattintson a diagram hozzáadása gombra a Metrikaböngészőben több diagram létrehozásához.

## <a name="how-do-i-apply-filters-to-the-charts"></a>Hogyan szűrők alkalmazása a diagramok?

A metrikák többdimenziósak megjelenítő diagramok a szűrőket alkalmazhat. Például a "Tranzakció száma" metrika-dimenziója van, ha "Választípus", amely jelzi, hogy a tranzakciók válaszát sikeres vagy sikertelen volt, majd szűrése e dimenzión volna tőzsdei diagram sort csak sikeres (vagy csak sikertelen) tranzakciók. 

### <a name="to-add-a-filter"></a>Szűrő hozzáadásához:

1. Kattintson a szűrő hozzáadása ikonra ![Szűrő ikon](./media/monitoring-metric-charts/icon002.png) a diagram felett

2. Válassza ki, melyik dimenzió (tulajdonság) szűrése

   ![metrika kép](./media/monitoring-metric-charts/006.png)

3. Válassza ki a mellékletet, amikor a diagram (Ez a példa bemutatja, a sikeres storage-tranzakció kiszűrése) ábrázolásához dimenzió értékektől:

   ![metrika kép](./media/monitoring-metric-charts/007.png)

4. A szűrőértékek kiválasztását követően kattintson a Tevékenységszűrő-választó, zárja be, hogy befejeződött. Most már a diagram azt ábrázolja, hogy hány storage-tranzakció nem sikerült:

   ![metrika kép](./media/monitoring-metric-charts/008.png)

5. 1-4 több szűrők alkalmazása az azonos diagramok lépéseket megismételve.

## <a name="how-do-i-segment-a-chart"></a>Hogyan szegmentálja a diagramot?

Ossza fel a metrika szembeni metrika összehasonlítása különböző részeit megjelenítéséhez dimenzió által és a külső dimenzió szegmensei azonosítja. 

### <a name="to-segment-a-chart"></a>A diagram szegmentálja:

1. Kattintson a csoportosítás hozzáadása ikonra  ![metrika kép](./media/monitoring-metric-charts/icon003.png) a diagram felett.
 
   > [!NOTE]
   > Több szűrőt, de csak egy csoportosítási bármely egyetlen diagram lehet.

2. Adja meg, amelyre át szeretné a diagram szegmentálni dimenzió: 

   ![metrika kép](./media/monitoring-metric-charts/010.png)

   Most már a diagram már helyesen jelenik meg több sort, egy a minden szegmensben, dimenzió:

   ![metrika kép](./media/monitoring-metric-charts/012.png)

3. Kattintson a távolabb a **csoportosítási választó** bezárásához.

   > [!NOTE]
   > Szűrés és ugyanaz a dimenzió szerinti csoportosítást használható elrejtheti a szegmenseket, amelyek az adott esetben nem számít, és könnyebben diagramok olvasható.

## <a name="how-do-i-pin-charts-to-dashboards"></a>Hogyan rögzítheti a diagramok irányítópultok?

Miután a diagramok, érdemes lehet hozzáadni az irányítópultokat, hogy megtekintse újra, valószínűleg más figyelési telemetriai adatokat a környezetében, vagy megoszthatja munkatársaival. 

PIN-kód irányítópultokhoz konfigurált diagram:

Miután a diagram, kattintson a a **diagram műveletek** menüben jobb felső sarkában a diagram, kattintson a **rögzítés az irányítópulton**.

   ![metrika kép](./media/monitoring-metric-charts/013.png)

## <a name="next-steps"></a>Következő lépések

  Olvasási [egyéni KPI-irányítópult létrehozása](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) további információt az ajánlott eljárások a metrikák végrehajthatóként irányítópultok létrehozásához.