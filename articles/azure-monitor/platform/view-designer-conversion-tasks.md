---
title: Az Azure Monitor nézet tervezője a munkafüzetek átalakításának gyakori feladataihoz
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658744"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>A tervező és a munkafüzetek átalakításának gyakori feladatainak megtekintése
[A View designer](view-designer.md) az Azure Monitor egyik szolgáltatása, amely lehetővé teszi, hogy egyéni nézeteket hozzon létre, amelyek segítségével megjelenítheti az adatokat a Log Analytics-munkaterületen, diagramokkal, listákkal és ütemtervekkel. Ezeket fokozatosan megszüntetik, és olyan munkafüzetekre cserélik, amelyek további funkciókat biztosítanak. Ez a cikk a nézetek munkafüzetté alakításában gyakran előforduló feladatokat részletezi.


## <a name="quickstart-with-preset-view-designer-templates"></a>Gyorsútmutató előre beállított nézettervezői sablonokkal

A Log Analytics-munkaterületeken lévő munkafüzetek már rendelkeznek olyan sablonokkal, amelyek megfelelnek a nézettervező egyes nézeteinek. A **Designer-segédvonalak megtekintése kategóriában** válassza a **Tervezői áttűnési útmutató megtekintése** lehetőséget a beállítások megismeréséhez, vagy válasszon egy előre beállított sablont.

![Példa sablonok](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Időtartomány-szűrő engedélyezése
A nézettervező beépített alapértelmezett időtartomány-szűrővel rendelkezik, azonban a munkafüzetekben ez a beállítás alapértelmezés szerint nincs engedélyezve. A munkafüzetek lehetővé teszik a felhasználók számára, hogy saját időtartomány-szűrőket hozzanak létre, amelyek jobban alkalmazhatók az adatnaplóikra. A szűrő létrehozásának lépései az alábbiak:

Válassza a **Paraméterek hozzáadása** lehetőséget. Az alapértelmezett **stílus** beállítása *Pills*.

![Param hozzáadása](media/view-designer-conversion-tasks/add-param.png)

 Válassza a **Paraméter hozzáadása** gombot.

![Paraméter hozzáadása](media/view-designer-conversion-tasks/add-parameter.png)

Az oldalsáv **menüjében** a Paraméter név szövegmezőbe írja be a *TimeRange*parancsot. **Paramétertípus** beállítása *időtartomány-választóként*. Jelölje be a **Kötelező?** jelölőnégyzetet.

![Paraméter menü](media/view-designer-conversion-tasks/parameter-menu.png)

Mentse a paramétert az oldalsáv menü bal felső sarkába. A legördülő menüt alapértelmezés szerint *unset* néven hagyhatja, vagy kiválaszthat egy alapértelmezett **TimeRange** értéket, például *24 órát.* Válassza a **Kész szerkesztés lehetőséget.**

A paraméterek a lekérdezésekben is használhatók, {} ha kapcsos zárójeleket adnak hozzá a paraméter neve köré. A paraméterekkel kapcsolatos további részletek a [munkafüzetek paraméterekre vonatkozó dokumentációjában](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md)találhatók.

## <a name="updating-queries-with-the-timerange-parameter"></a>Lekérdezések frissítése a TimeRange paraméterrel

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>1. lehetőség: Válassza ki a TimeRange-t az Időtartomány legördülő menüből

![Idő paraméter](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>2. lehetőség: A naplólekérdezések frissítése

A lekérdezésben adja `| where TimeGenerated {TimeRange}` hozzá a sort: ahogy a következő példában:

Eredeti lekérdezés
```KQL
search * 
| summarize count() by Type
```

Frissített lekérdezés
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Listával együtt
A legtöbb nézettervezői nézet tartalmaz egy listát, és ezt a szabványos listát reprodukálhatja egy munkafüzetben.

![Mozaiklista](media/view-designer-conversion-tasks/tile-list.png)

Vizualizáció hozzáadásához kattintson a **Lekérdezés hozzáadása** elemre a cellabeállítások közül.

![Param hozzáadása](media/view-designer-conversion-tasks/add-param.png)

A Nézettervező olyan alapértelmezett lekérdezést alkalmaz, amely megegyezik az Eredeti példa szintaxisával. Ez a lekérdezés frissített űrlapra való módosításával frissíthető, akövetkező példában:

Eredeti lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Frissített lekérdezés
```KQL
search * 
| summarize Count = count() by Type
```

Ez létrehoz egy listát, amely a következőhöz hasonló:

![Példa lista](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Értékgörbék engedélyezése
A rácsok gyakori jellemzője, hogy értékgörbéket ad hozzá a különböző adatminták idővel való összegzéséhez. A View designer az **Értékgörbék engedélyezése** funkciót kínálja az összes listához, akárcsak a munkafüzetek. Ha a nézettervezőnek megfelelő értékgörbéket szeretne felvenni az adatokba, az alábbi példában látható módon egyesítse az adatokat az eredeti lekérdezéssel:

Eredeti lekérdezés
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Frissített lekérdezés
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Válassza **az Oszlopbeállítások lehetőséget.**
![Oszlopbeállítások](media/view-designer-conversion-tasks/column-settings.png)

Frissítse az **Oszlopmegjelenítő** legördülő menüt *Spark-területté.*
![Értékgörbék](media/view-designer-conversion-tasks/sparkline.png)

Mentse a beállításokat, és futtassa újra a lekérdezést, hogy frissítse a táblát, hogy egy értékgörbét is tartalmazzon.

Az eredményül kapott rács ![a következőhöz hasonlóan fog kinézni: Értékgörbe példa](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Speciális cellabeállítások
A nézettervező tükrözéséhez olyan feladatokat hajthat végre, mint a munkafüzetcellák méretének módosítása, vagy pinek és külső hivatkozások hozzáadása a naplókhoz.

A **Speciális beállítások** eléréséhez jelölje ki az egyes cellák alján található fogaskerék ikont.

![Speciális beállítások](media/view-designer-conversion-tasks/advanced-settings.png)

Ez megjelenik egy menü különböző lehetőségekkel:

![Speciális beállítások beállításai](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Pin és külső lekérdezésre mutató hivatkozás hozzáadásához jelölje be a megfelelő jelölőnégyzeteket. Ha címet szeretne hozzáadni a cellához, írja be a kívánt címet a **Diagram cím** szakaszába.

Alapértelmezés szerint minden munkafüzetcella úgy van beállítva, hogy a teljes oldalszélességet elvegye, de ezt úgy módosíthatja, hogy a cellát lefelé skálázhatja a **Speciális beállítások** menü **Stílus** lapján.

![Speciális beállítások stílusa](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>További paraméterek
Új paraméter létrehozásához válassza a **Paraméter hozzáadása** lehetőséget. 

Előfizetés kiválasztásához írja be az *Előfizetés* szót az oldalsó menü **Paraméternév** mezőjébe, és válassza az *Előfizetésválasztó* lehetőséget a **Paramétertípus** legördülő menüből.

![Előfizetés menü](media/view-designer-conversion-tasks/subscription-filter.png)

Erőforrás kijelöléséhez írja be az *Erőforrás* szót az oldalmenü **Paraméternév** mezőjébe, és válassza az *Erőforrás-választó* lehetőséget a **Paraméter típus** legördülő menüből.

![Erőforrás menü](media/view-designer-conversion-tasks/resource-filter.png)

Ez beilleszti a legördülő menüt, amely lehetővé teszi a különböző előfizetések és erőforrások elérését.

![Előfizetési erőforrás legördülő menü](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>További lépések
- [Csempekonverziók](view-designer-conversion-tiles.md)
