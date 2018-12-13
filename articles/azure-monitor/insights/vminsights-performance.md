---
title: Diagram – a virtuális gépek (előzetes verzió) és az Azure Monitor teljesítmény hogyan |} A Microsoft Docs
description: Teljesítmény funkciója az Azure monitor-beli virtuális gépek, amelyek automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Ez a cikk részletesen hogyan használható a különböző forgatókönyveket.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: 419160ea8a91672d9c948c57b9c3ffd70180ab66
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187721"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms-preview"></a>Hogyan diagram teljesítmény és az Azure Monitor-beli virtuális gépek (előzetes verzió)
Az Azure Monitor-beli virtuális gépek több fő teljesítménymutatók (KPI-k) segítségével eldöntheti, milyen jól a virtuális gép működik-e a cél teljesítménydiagramok készletét tartalmazza. A diagramok megjelenítése az erőforrás-használatot egy időszakon belül, így azonosíthatja a szűk keresztmetszeteket, a rendellenességeket, vagy váltson át az egyes gépek megtekintése a kiválasztott metrika alapján erőforrás-használat listázása egy perspektíva. Bár vannak számos elemet kell figyelembe venni, amikor foglalkoznak, teljesítmény, a virtuális gépek az Azure Monitor összpontosít az operációs rendszer keresztül a processzor, memória, hálózati adapterek és lemezek, ahogy. Teljesítmény egészíti ki a állapotát a figyelési funkció, és tegye elérhetővé a problémákat, amelyek jelzik, hogy egy lehetséges rendszer összetevő hibája, támogatási finomhangolása és optimalizálási hatékonyság elérése érdekében, vagy támogatja a kapacitástervezés segít.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Az Azure Monitor több virtuális gépre kiterjedő szempont
Az Azure Monitor a teljesítmény funkció erőforráscsoportok, előfizetések vagy a környezetében telepített összes figyelt virtuális gép több virtuális gép nézetét jeleníti meg.  Az Azure Monitor eléréséhez hajtsa végre az alábbi lépéseket. 

1. Az Azure Portalon válassza ki a **figyelő**. 
2. Válasszon **(előzetes verzió) virtuális gépek** a a **megoldások** szakaszban.
3. Válassza ki a **teljesítmény** fülre.

![Virtuális gép insights teljesítményének felső N lista megtekintése](./media/vminsights-performance/vminsights-performance-aggview-01.png)

Az a **felső N diagramok** lapon, ha egynél több Log Analytics-munkaterületen, válassza ki a munkaterületet a megoldás engedélyezve van a **munkaterület** választó, amely a lap tetején. A **csoport** választóval adja vissza az előfizetések, erőforráscsoportok, [számítógépcsoportok](../../azure-monitor/platform/computer-groups.md), és a virtuális gép méretezési csoportok, amelyek segítségével tovább szűrheti a kijelölt munkaterülethez kapcsolódó számítógépek a diagramok ezen az oldalon, és más oldalain megjelenő eredményeket. A kijelölés csak a teljesítmény funkció vonatkozik, és nem vállalunk pénzügyi vagy a térképen.  

Alapértelmezés szerint a diagramok megjelenítése az elmúlt 24 órában. Használatával a **TimeRange** választó, lekérdezheti a múltbéli tartományok megjelenítése, hogyan teljesítmény kikeresi az elmúlt 30 napig.   

Az öt kapacitás kihasználtsága diagramok az oldalon látható a következők:

* CPU-használat % - a legmagasabb átlagos processzorhasználat az első öt gépek jeleníti meg. 
* Rendelkezésre álló memória – bemutatja a leggyakoribb öt gépek a rendelkezésre álló memória legkisebb átlagos mennyisége 
* Logikai lemez lefoglalt terület % - mutatja, hogy a legmagasabb átlagos lemezterület öt leggyakoribb gépekkel % használt összes lemez-kötetek között 
* Bájt küldve-forgalom – küldött bájtok legmagasabb átlagosan öt gépek jeleníti meg. 
* Bájt Receive-forgalom – küldött bájtok legmagasabb átlagosan öt gépek jeleníti meg. 

Az öt diagramok bármelyike jobb felső sarkában kattint nyílik **felső N lista** megtekintése.  Itt láthatja az erőforrás-használat, a teljesítmény metrika szerint egyéni virtuális Gépet a listanézet és mely gépre legmagasabb ráta.  

![A kiválasztott teljesítmény-mérőszám a felső N listanézet](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

A virtuális gép kattintva a **tulajdonságok** ablaktábla ki van bontva, például a rendszer-információkat az operációs rendszer, az Azure-beli Virtuálisgép-használat tulajdonságok által jelentett kijelölt elem tulajdonságainak megjelenítéséhez a jobb oldalon. Kattintson a beállítások alatt a **Gyorshivatkozások** szakasz átirányítja Önt az adott szolgáltatás közvetlenül a kijelölt virtuális gépről.  

![Virtuális gép tulajdonságait tartalmazó ablaktáblán](./media/vminsights-performance/vminsights-properties-pane-01.png)

Váltson a **összesített diagramok** átlagos vagy percentilisei mértékek szerint szűrt fülre kattintva megtekintheti a teljesítmény-mérőszámon.  

![Virtuális gép insights összesített teljesítmény nézet](./media/vminsights-performance/vminsights-performance-aggview-02.png)

A következő kapacitás kihasználtsága diagramok áll rendelkezésre:

* CPU-használat % - alapértelmezés szerint az átlagos és felső 95. percentilis megjelenítése 
* Rendelkezésre álló memória – alapértelmezett érték az átlagos, felső 5. és a 10th PERCENTILIS megjelenítése 
* Logikai lemez lefoglalt terület % – alapértelmezés szerint az átlagos és a 95. percentilis megjelenítése 
* Bájt küldve-forgalom – alapértelmezett értékeket megjelenítő küldött bájtok átlagos száma 
* Bájt Receive-forgalom – alapértelmezett értékeket megjelenítő fogadott bájtok átlagos száma

A diagramok időtartományban részletességét is kiválasztásával módosíthatja **átlagos**, **Min**, **maximális**, **szükséges idő az 50**,  **90**, és **95** PERCENTILIS-választójában jelenítse.   

Megtekintheti az egyes virtuális gép erőforrás-használat lista nézetben, és mely gépre ráta a legnagyobb kihasználtságú, jelölje be a **felső N lista** fülre.  A **felső N lista** oldal bemutatja az első 20 gépek szerint a 95. percentilis a metrika a leginkább kihasznált szerint rendezve *CPU-kihasználtság (%)*.  Kiválasztásával megtekintheti a további gépek **terhelés további**, és az eredmények bontsa ki az első 500 gépek megjelenítéséhez. 

>[!NOTE]
>A lista nem jeleníthető meg több mint 500 gépek egyszerre.  
>

![Felső N lista lap példa](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

A lista egy adott virtuális gépen az eredmények szűréséhez, adja meg a számítógép nevére, a a **Keresés név alapján** szövegmezőbe.  

Ha szeretné inkább megtekintheti a különböző teljesítmény-mérőszám, a kihasználtság a a **metrika** legördülő listában válassza ki **rendelkezésre álló memória**, **logikai lemez lefoglalt terület %**,  **Fogadott bájt/s hálózat**, vagy **hálózati küldött bájt/s** és a lista frissíti ennek a mutatónak a hatókörön belüli használat megjelenítéséhez.  

Megnyílik egy virtuális gépet a listából a **tulajdonságok** jobb oldalán található a panelen, és innen kiválaszthatja **teljesítmény részletei**.  A **virtuális gépek részletei** lap megnyitásakor, és a virtuális Gépeket, hasonló élményt nyújt az Azure virtuális gép közvetlenül a virtuális gép Insights teljesítménye elérésekor hatókörét.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Közvetlenül az Azure virtuális teljesítmény megtekintése
Elérése közvetlenül a virtuális gépről, hajtsa végre a következő.

1. Az Azure Portalon válassza ki a **virtuális gépek**. 
2. A listából válassza ki a virtuális gép és a a **figyelés** szakaszban válasszon **Insights (előzetes verzió)**.  
3. Válassza ki a **teljesítmény** fülre. 

Ezen a lapon nem csupán teljesítménydiagramok kihasználtságát, de is egy táblázat látható az egyes logikai lemezek felderítése, a kapacitás kihasználtságát, tartalmazza, és minden mérték által átlagos száma.  

A következő kapacitás kihasználtsága diagramok áll rendelkezésre:

* CPU-használat % - alapértelmezés szerint az átlagos és felső 95. percentilis megjelenítése 
* Rendelkezésre álló memória – alapértelmezett érték az átlagos, felső 5. és a 10th PERCENTILIS megjelenítése 
* Logikai lemez lefoglalt terület % – alapértelmezés szerint az átlagos és a 95. percentilis megjelenítése 
* Logikai lemez IOPS - alapértelmezés szerint az átlagos és a 95. percentilis megjelenítése
* Logikai lemez MB/s - alapértelmezett értékeket, az átlagos és a 95. percentilis megjelenítése
* Maximális logikai lemez használt % – alapértelmezés szerint az átlagos és a 95. percentilis megjelenítése
* Bájt küldve-forgalom – alapértelmezett értékeket megjelenítő küldött bájtok átlagos száma 
* Bájt Receive-forgalom – alapértelmezett értékeket megjelenítő fogadott bájtok átlagos száma

![Virtuális gép insights teljesítmény közvetlenül a virtuális gép megtekintése](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="alerting-and-alert-management"></a>Riasztások és a riasztások kezelése 
Előre konfigurált riasztási szabályok engedélyezve van az Azure Monitor részeként a virtuális gépek teljesítmény-mérőszámok nem tartalmazzák. Bár vannak [állapotriasztások](vminsights-health.md#alerting-and-alert-management) teljesítményproblémákat észlel az Azure virtuális gép, például a magas CPU-kihasználtság, kevés a rendelkezésre álló memória, lemez I/O, kevés a szabad lemezterület, stb., a megfelelő ezek állapotriasztások csak lesznek alkalmazva a virtuális gépekhez csatlakoztatva a virtuális gépek az Azure Monitor engedélyezve a Log Analytics munkaterületén. 

Azonban előfordulhat, hogy csak gyűjtjük és tároljuk a teljesítmény-mérőszámokat a Log Analytics-munkaterületen van szüksége egy részét. Ha a monitorozási stratégia elemzés vagy riasztás, amely tartalmazza a egyéb teljesítménymutatóiról annak érdekében, hogy hatékonyan értékeli a kapacitás vagy a virtuális gép állapotát, vagy van szüksége rugalmasságra, adja meg a saját riasztási feltételek vagy a logikai van szükség, akkor az konfigurálása [megvizsgálhatjuk a teljesítményszámlálókat gyűjteményét](../../azure-monitor/platform/data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json) a Log Analyticsben és definiálhat [naplóriasztások](../../azure-monitor/platform/alerts-log.md?toc=/azure/azure-monitor/toc.json). Amíg a Log Analytics lehetővé teszi, hogy más típusú adatokat tartalmazó összetett elemzéseket végezhet, és adja meg a hosszabb adatmegőrzési tendenciája, metrikák támogatására, másrészt könnyen használható, és képes a közel valós idejű feldolgozásához. Által gyűjtött a [Azure diagnosztikai ügynök](../../virtual-machines/windows/monitor.md) és az így kisebb késéssel és a egy alacsonyabb költségek, a riasztások létrehozása az Azure Monitor metrikák adattárban tárolt.

Tekintse át a áttekintése [gyűjteménye, metrikák és naplók az Azure Monitor szolgáltatással](../../azure-monitor/platform/data-collection.md?toc=/azure/azure-monitor/toc.json) így jobban megismerheti az alapvető különbség, és egyéb szempontok gyűjtemény további metrikák és a riasztási szabályok konfigurálása előtt.  

## <a name="next-steps"></a>További lépések
Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [a virtuális gépek állapotának megtekintése az Azure Monitor](vminsights-health.md), vagy a felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md). 