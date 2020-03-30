---
title: Az Azure Monitor nézettervezője a munkafüzetek konvertálási beállításaihoz
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 7bfa831332451718c0c9c05023b90104d2b8b02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658710"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Az Azure Monitor nézettervezője a munkafüzetek konvertálási beállításaihoz
[A View designer](view-designer.md) az Azure Monitor egyik szolgáltatása, amely lehetővé teszi, hogy egyéni nézeteket hozzon létre, amelyek segítségével megjelenítheti az adatokat a Log Analytics-munkaterületen, diagramokkal, listákkal és ütemtervekkel. Ezeket fokozatosan megszüntetik, és olyan munkafüzetekre cserélik, amelyek további funkciókat biztosítanak. Ez a cikk a nézetek munkafüzetté alakításának alapvető fogalmait és lehetőségeit hasonlítja össze.

## <a name="basic-workbook-designs"></a>Alapvető munkafüzettervek

A nézettervező rögzített statikus ábrázolási stílussal rendelkezik, míg a munkafüzetek lehetővé teszik az adatok ábrázolásának és megjelenítésének szabadságát. Az alábbi képek két példát mutatnak be arra, hogyan rendezheti el a munkafüzeteket a nézetek konvertálásakor.

[Függőleges munkafüzet](view-designer-conversion-examples.md#vertical)
![függőleges](media/view-designer-conversion-options/view-designer-vertical.png)

[Többlapos munkafüzet](view-designer-conversion-examples.md#tabbed)
![Adattípus elosztása lap](media/view-designer-conversion-options/distribution-tab.png)
![Adattípusok idővel lap](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Csempe átalakítása
A nézettervező az áttekintő csempe funkciót használja a teljes állapot ábrázolására és összegzésére. Ezek hét csempén jelennek meg, a számoktól a diagramokig. A munkafüzetekben a felhasználók hasonló vizualizációkat hozhatnak létre, és az áttekintő csempék eredeti stílusához hasonlítanak. 

![Katalógus](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Irányítópult-átalakítás megtekintése
A nézettervezőcsempék általában két szakaszból állnak, egy vizualizációból és egy listából, amely megfelel a vizualizáció adatainak, például a **Donut & List** csempe.

![Gyűrű](media/view-designer-conversion-options/donut-example.png)

A munkafüzetek segítségével a felhasználó kiválaszthatja, hogy lekérdezi-e a nézet egyik vagy mindkét szakaszát. A munkafüzetekben lévő lekérdezések kialakítása egyszerű, kétlépésből álló folyamat. Először is, az adatok a lekérdezésből jönnek létre, másodszor pedig az adatok vizualizációként jelennek meg.  A munkafüzetekben a következő példa a nézet újbóli létrehozása:

![Konvertálás](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>További lépések
- [Munkafüzetek & engedélyek elérése](view-designer-conversion-access.md)