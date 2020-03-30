---
title: Az Azure Monitor nézet tervezője a munkafüzetek csempekonverzióihoz
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658626"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Az Azure Monitor nézet tervezői csempekonverziói
[A View designer](view-designer.md) az Azure Monitor egyik szolgáltatása, amely lehetővé teszi, hogy egyéni nézeteket hozzon létre, amelyek segítségével megjelenítheti az adatokat a Log Analytics-munkaterületen, diagramokkal, listákkal és ütemtervekkel. Ezeket fokozatosan megszüntetik, és olyan munkafüzetekre cserélik, amelyek további funkciókat biztosítanak. Ez a cikk a különböző csempék munkafüzetté alakításának részleteit tartalmazza.

## <a name="donut--list-tile"></a>& fánklista csempéje

![Fánk lista](media/view-designer-conversion-tiles/donut-list.png)

A fánk & listacsempének újbóli létrehozása a munkafüzetekben két külön vizualizációt foglal magában. A fánk rész két lehetőség van.
Mindkét kezdetnél válassza **a Lekérdezés hozzáadása** lehetőséget, és illessze be az eredeti lekérdezést a nézettervezőből a cellába.

**1. lehetőség:** Válassza a **Kördiagram** lehetőséget a ![ **Képi megjelenítése konvizializációs** menüből](media/view-designer-conversion-tiles/pie-chart.png)

**2. lehetőség:** Válassza a **Megjelenítés** legördülő menü Lekérdezés `| render piechart` **beállítása lehetőséget,** és adja hozzá a lekérdezéshez:

 ![Képi megjelenítés menü](media/view-designer-conversion-tiles/set-by-query.png)

**Példa**

Eredeti lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Frissített lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

A lista létrehozásáról és az értékgörbék engedélyezéséről a [gyakori feladatokról](view-designer-conversion-tasks.md)szóló cikkben olvashat.

Az alábbi példa bemutatja, hogyan értelmezhető újra a fánk & listacsempét a munkafüzetekben:

![Fánklista munkafüzetek](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Vonaldiagram & listacsempével
![Vonaldiagram-lista](media/view-designer-conversion-tiles/line-list.png) 

A vonaldiagram rész újbóli létrehozásához frissítse a lekérdezést az alábbiak szerint:

Eredeti lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Frissített lekérdezés
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

A vonaldiagram megjelenítésének két lehetősége van

**1. lehetőség:** Válassza a **Vonaldiagram lehetőséget** a **Vizualizációs** legördülő listából:
 
 ![Vonaldiagram menü](media/view-designer-conversion-tiles/line-visualization.png)

**2. lehetőség:** Válassza a **Megjelenítés** legördülő menü Lekérdezés `| render linechart` **beállítása lehetőséget,** és adja hozzá a lekérdezéshez:

 ![Képi megjelenítés menü](media/view-designer-conversion-tiles/set-by-query.png)

**Példa**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

A lista létrehozásáról és az értékgörbék engedélyezéséről a [gyakori feladatokról](view-designer-conversion-tasks.md)szóló cikkben olvashat.

Az alábbi példa bemutatja, hogyan értelmezhető újra a sordiagram & a listacsempén:

![Sordiagramlista-munkafüzetek](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Szám & lista csempéje

 ![Mozaiklista](media/view-designer-conversion-tiles/tile-list-example.png)

A számcsempe esetében frissítse a lekérdezést a következőképpen:

Eredeti lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Frissített lekérdezés
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Módosítsa a Képi megjelenítése legördülő menüt **Mozaikra,** majd válassza **a Mozaikbeállítások lehetőséget.**
 ![Mozaikképi megjelenítés](media/view-designer-conversion-tiles/tile-visualization.png)

Hagyja üresen a **Cím** szakaszt, és válassza **a Balra**lehetőséget. Módosítsa a **Felhasználás oszlop értékét:** **megszámlálás**, **oszlopmegjelenítő** **pedig Nagy szám**értékre:

![Csempe beállításai](media/view-designer-conversion-tiles/tile-settings.png)

 
A lista létrehozásáról és az értékgörbék engedélyezéséről a [gyakori feladatokról](view-designer-conversion-tasks.md)szóló cikkben olvashat.

Az alábbi példa bemutatja, hogyan értelmezhető újra a lista& a munkafüzetekben:

![Számlista munkafüzetek](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Ütemterv és lista

 ![Idősor lista](media/view-designer-conversion-tiles/time-list.png)

Az ütemtervhez frissítse a lekérdezést az alábbiak szerint:

Eredeti lekérdezés
```KQL
search * 
| sort by TimeGenerated desc
```

Frissített lekérdezés
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

A lekérdezéssávdiagramként való megjelenítésének két lehetősége van:

**1. lehetőség:** **A Sávdiagram** kiválasztása a **Képi megjelenítése** legördülő menüből: ![Sávdiagram-megjelenítés](media/view-designer-conversion-tiles/bar-visualization.png)
 
**2. lehetőség:** Válassza a **Megjelenítés** legördülő menü Lekérdezés `| render barchart` **beállítása lehetőséget,** és adja hozzá a lekérdezéshez:

 ![Képi megjelenítés menü](media/view-designer-conversion-tiles/set-by-query.png)

 
A lista létrehozásáról és az értékgörbék engedélyezéséről a [gyakori feladatokról](view-designer-conversion-tasks.md)szóló cikkben olvashat.

Az alábbi példa bemutatja, hogyan értelmezhető újra az idővonal & a listacsempét a munkafüzetekben:

![Ütemtervlista-munkafüzetek](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>További lépések

- [A nézettervező és a munkafüzetek áttűnésének áttekintése](view-designer-conversion-overview.md)
