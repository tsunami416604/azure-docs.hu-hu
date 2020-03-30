---
title: Teljesítménydiagram az Azure Monitor szolgáltatással a virtuális gépekhez
description: A teljesítmény az Azure Monitor virtuális gépekhez szolgáltatása, amely automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Ez a cikk részletesen ismerteti, hogyan kell használni a különböző forgatókönyvek.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: a50ba39777e6a9d3d609e584c0c7d872f2a65f35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283718"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>Teljesítménydiagram az Azure Monitor szolgáltatással a virtuális gépekhez

Az Azure Monitor virtuális gépekhez tartalmaz egy teljesítménydiagramok, amelyek célja több fő teljesítménymutatók (KPI-k) segítségével határozza meg, hogy milyen jól teljesít a virtuális gép. A diagramok egy adott időszakon keresztül mutatják az erőforrás-kihasználtságot, így azonosíthatja a szűk keresztmetszeteket, anomáliákat, vagy átválthat egy perspektivikus nézetre, amely felsorolja az egyes gépeket az erőforrás-kihasználtság megtekintéséhez a kiválasztott metrika alapján. Bár számos olyan elem van, amelyet figyelembe kell venni a teljesítmény kezelése során, az Azure Monitor virtuális gépek figyeli a processzorhoz, a memóriához, a hálózati adapterhez és a lemezkihasználtsághoz kapcsolódó kulcsfontosságú operációsrendszer-teljesítménymutatókat. A teljesítmény kiegészíti az állapotfigyelési funkciót, és segít feltárni azokat a problémákat, amelyek a rendszerösszetevő esetleges meghibásodását jelzik, a hatékonyság elérése érdekében támogatja a hangolást és az optimalizálást, vagy támogatja a kapacitástervezést.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Többvirtuális gépes perspektíva az Azure Monitorból

Az Azure Monitor, a Teljesítmény funkció az összes figyelt virtuális gépek között telepített munkacsoportok az előfizetések vagy a környezetben. Az Azure Monitor ból való hozzáféréshez hajtsa végre a következő lépéseket. 

1. Az Azure Portalon válassza a **Figyelő**lehetőséget. 
2. Válassza a **Virtuális gépek lehetőséget** a **Megoldások** szakaszban.
3. Válassza a **Teljesítmény** lapot.

![VM-betekintésteljesítmény top N-lista nézet](media/vminsights-performance/vminsights-performance-aggview-01.png)

A **Felső N diagramok** lapon, ha egynél több Log Analytics-munkaterülettel rendelkezik, válassza ki a megoldással engedélyezett munkaterületet a lap tetején található Munkaterület-választóból. **Workspace** A **Csoportválasztó** a kijelölt munkaterülethez kapcsolódó előfizetéseket, erőforráscsoportokat, [számítógépcsoportokat](../platform/computer-groups.md)és virtuálisgép-méretezési csoportokat ad vissza, amelyek segítségével tovább szűrheti az ezen a lapon és a többi oldalon megjelenő diagramokon megjelenő eredményeket. A kijelölés csak a Teljesítmény funkcióra vonatkozik, és nem kerül át az Állapot vagy a Térkép alkalmazásba.  

Alapértelmezés szerint a diagramok az elmúlt 24 órát jelenítik meg. A **TimeRange** választó használatával lekérdezheti a legfeljebb 30 napos előzményidőtartományokat, hogy megmutassa, hogyan nézett ki a teljesítmény a múltban.

Az oldalon látható öt kapacitáskihasználtsági diagram a következő:

* CPU-kihasználtság % - a legmagasabb átlagos processzorkihasználtsú öt gépet mutatja 
* Rendelkezésre álló memória - az első öt gépet mutatja, amelyek a legalacsonyabb átlagos rendelkezésre álló memóriával 
* Használt logikai lemezterület – az összes lemezköteten a legnagyobb átlagos lemezterülettel rendelkező öt gép megjelenítése % 
* Küldött bájtok aránya – az első öt gépet jeleníti meg, ahol a legmagasabb az elküldött bájtok átlaga 
* Bájt fogadási arány – az első öt gépet jeleníti meg, ahol a legmagasabb a fogadott bájtok átlaga 

Ha az öt diagram egyikének jobb felső sarkában lévő pin ikonra kattint, a kiválasztott diagramot az utoljára megtekintett Azure-irányítópultra rögzítheti.  Az irányítópultról átméretezheti és áthelyezheti a diagramot. Ha kiválasztja a diagramot az irányítópultról, átirányítja az Azure Monitor virtuális gépekhez, és betölti a megfelelő hatókört és nézetet.  

Az öt diagram bármelyikén a pin ikontól balra található ikonra kattintva megnyílik a **Felső N lista** nézet.  Itt láthatja az erőforrás-kihasználtságot az adott teljesítménymetrika egyéni virtuális gép egy listanézetben, és melyik gép a legmagasabb trend.  

![Felső N lista nézet a kiválasztott teljesítménymérőhez](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Ha a virtuális gépre kattint, a **tulajdonságok** ablaktábla ki van bontva a jobb oldalon a kiválasztott elem tulajdonságainak megjelenítéséhez, például az operációs rendszer által jelentett rendszeradatok, az Azure virtuális gép tulajdonságai stb. A **Gyorshivatkozások** szakasz egyik beállítására kattintva közvetlenül a kiválasztott virtuális gépről irányítja át az adott funkcióra.  

![A Virtuális gép tulajdonságai ablaktábla](./media/vminsights-performance/vminsights-properties-pane-01.png)

Váltson az **Összesített diagramok** lapra az átlagos vagy százalékos mérték szerint szűrt teljesítménymutatók megtekintéséhez.  

![Virtuális gép elemzési adatai – Teljesítményösszesítés nézet](./media/vminsights-performance/vminsights-performance-aggview-02.png)

A következő kapacitáskihasználtsági diagramok állnak rendelkezésre:

* CPU-kihasználtság % - az átlagos és a felső 95 százalékos értéket megjelenítő alapértelmezett értékek 
* Rendelkezésre álló memória - az átlagos, a top 5. 
* Használt logikai lemezterület – az átlagot és a 95. 
* Elküldött bájtok díja – az elküldött átlagos bájtok alapértelmezett értéke 
* Fogadott bájtok – a beérkezett átlagos bájtok alapértelmezett értékeinek megjelenítése

A diagramok részletességét az időtartományon belül is módosíthatja, ha a percentilis választóban az **Átlag**, **Min**, **Max**, **50th**, **90th**és **95 értéket** választja.

Ha meg szeretné tekinteni az egyes virtuális gépek erőforrás-kihasználtságát egy listanézetben, és meg szeretné tekinteni, hogy melyik számítógép a legnagyobb kihasználtsítással, válassza a **Felső N lista** lapot.  A **Felső N lista** oldalon látható a top 20 gép a leggyakrabban használt 95 percentilis a metrikus *CPU-kihasználtság %*.  Több gépet láthat, ha kiválasztja az **Egyebek betöltése**lehetőséget, és az eredmények kibővülnek az első 500 gép megjelenítéséhez. 

>[!NOTE]
>A lista egyszerre legfeljebb 500 gépet tud megjeleníteni.  
>

![Példa a legnépszerűbb N-lista oldalára](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Ha a listában szereplő adott virtuális gépen lévő találatokat szeretné szűrni, írja be a számítógép nevét a **Keresés név szerint** mezőbe.  

Ha inkább egy másik teljesítménymérőszámból szeretné megtekinteni a kihasználtságot, a **Metrika** legördülő listából válassza a **Rendelkezésre álló memória**, **a Használt logikai lemezterület %**, a hálózati fogadott **bájt/s**vagy a **hálózati küldött bájt/s** lehetőséget, és a listafrissítéseket az adott mutatóhoz tartozó kihasználtsági hatókör megjelenítéséhez.  

Ha egy virtuális gépet választ a listából, megnyílik a **tulajdonságok** panel a lap jobb oldalán, és innen kiválaszthatja a **Teljesítmény részletei**lehetőséget.  A **virtuális gép részletei** lap megnyílik, és hatóköre, hogy a virtuális gép, hasonló tapasztalattal, amikor a virtuális gép Insights-teljesítmény közvetlenül az Azure virtuális gép elérésekor.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Teljesítmény megtekintése közvetlenül egy Azure virtuális gépről

Ha közvetlenül virtuális gépről szeretne hozzáférni, hajtsa végre a következő lépéseket.

1. Az Azure Portalon válassza a **Virtuális gépek**lehetőséget. 
2. A listából válasszon egy virtuális gép, és a **figyelési** szakaszban válassza **insights.**  
3. Válassza a **Teljesítmény** lapot. 

Ez az oldal nem csak a teljesítménykihasználási diagramokat tartalmazza, hanem egy táblázatot is, amely minden egyes felfedezett logikai lemezhez, kapacitásához, kihasználtságához és az egyes mértékekkel átlaghoz viszonyítva jeleníti meg.  

A következő kapacitáskihasználtsági diagramok állnak rendelkezésre:

* CPU-kihasználtság % - az átlagos és a felső 95 százalékos értéket megjelenítő alapértelmezett értékek 
* Rendelkezésre álló memória - az átlagos, a top 5. 
* Használt logikai lemezterület – az átlagot és a 95. 
* Logikai lemez IOPS - alapértelmezett mutató az átlagos és 95 percentilis
* Logikai lemez MB/s – az átlagot és a 95.
* Maximális logikai lemez használt % - az átlagot és a 95.
* Elküldött bájtok díja – az elküldött átlagos bájtok alapértelmezett értéke 
* Fogadott bájtok – a beérkezett átlagos bájtok alapértelmezett értékeinek megjelenítése

Ha bármelyik diagram jobb felső sarkában lévő pin ikonra kattint, a kijelölt diagramot az utoljára megtekintett Azure-irányítópultra rögzíti. Az irányítópultról átméretezheti és áthelyezheti a diagramot. A diagram kiválasztása az irányítópultról átirányítja önt az Azure Monitor virtuális gépekhez, és betölti a virtuális gép teljesítményrészletességi nézetét.  

![Virtuálisgép-elemzési teljesítmény közvetlenül a Virtuálisgép nézetből](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Teljesítmény megtekintése közvetlenül az Azure virtuálisgép-méretezési csoportból

Közvetlenül egy Azure virtuálisgép-méretezési csoportból való hozzáféréshez hajtsa végre a következő lépéseket.

1. Az Azure Portalon válassza a **Virtuálisgép-méretezési csoportok lehetőséget.**
2. A listából válasszon egy virtuális gép, és a **figyelési** szakaszban válassza **insights** a **Teljesítmény** lap megtekintéséhez.

Ez a lap betölti az Azure Monitor teljesítménynézetét, amely a kijelölt méretezési készletre van kitéve. Ez lehetővé teszi, hogy a top N példányok a méretezési csoportban a figyelt metrikák készletében, tekintse meg az összesített teljesítményt a méretezési csoportban, és tekintse meg a kiválasztott metrikák trendjeit az egyes példányok n a méretezési csoportban. Ha kiválaszt egy példányt a listanézetből, betöltheti a térképet, vagy részletes teljesítménynézetbe navigálhat az adott példányhoz.

Ha bármelyik diagram jobb felső sarkában lévő pin ikonra kattint, a kijelölt diagramot az utoljára megtekintett Azure-irányítópultra rögzíti. Az irányítópultról átméretezheti és áthelyezheti a diagramot. A diagram kiválasztása az irányítópultról átirányítja önt az Azure Monitor virtuális gépekhez, és betölti a virtuális gép teljesítményrészletességi nézetét.  

![Virtuálisgép-elemzési teljesítmény közvetlenül a virtuálisgép-méretezési csoport nézetéből](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>Egy adott példány részletes teljesítménynézetét is elérheti a méretezési csoport példányai nézetből. Keresse meg a **Példányok lapot** a **Beállítások** szakaszban, és válassza az **Elemzések**lehetőséget.



## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [használhatja az](vminsights-workbooks.md) Azure Monitor szolgáltatás a virtuális gépekhez, a munkafüzetek használatával a teljesítmény és a hálózati metrikák további elemzéséhez.  

- A felderített alkalmazásfüggőségekről az [Azure-figyelő szolgáltatás virtuális gépek leképezésének megtekintése.](vminsights-maps.md)
