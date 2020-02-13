---
title: Azure Monitor a Designer és a munkafüzetek közötti átalakítási beállítások megjelenítése
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f04ebc1a4a53825709479ca3f1dc7ce1245fc67f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171552"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Azure Monitor a Designer és a munkafüzetek közötti átalakítási beállítások megjelenítése
A [tervező](view-designer.md) a Azure monitor egyik funkciója, amely lehetővé teszi, hogy egyéni nézeteket hozzon létre, amelyek segítségével megjelenítheti a log Analytics-munkaterületen lévő, diagramokkal, listákkal és időpontokkal kapcsolatos adatait. A rendszer fokozatosan lecseréli azokat a munkafüzetekkel, amelyek további funkciókat biztosítanak. Ez a cikk összehasonlítja a két és a munkafüzetek nézeteinek konvertálására szolgáló lehetőségek alapvető fogalmait.

## <a name="basic-workbook-designs"></a>Alapszintű munkafüzetek tervezése

A tervezőben rögzített statikus ábrázolási stílus szerepel, míg a munkafüzetek lehetővé teszik az adatmegjelenítést és az adatmegjelenítést. Az alábbi ábrán két példa látható arra, hogyan rendezheti a munkafüzeteket a nézetek konvertálásakor.

[Függőleges munkafüzet](view-designer-conversion-examples.md#vertical)
![függőlegesen](media/view-designer-conversion-options/view-designer-vertical.png)

A [Többlapos munkafüzet](view-designer-conversion-examples.md#tabbed)
![adattípus-terjesztés lap](media/view-designer-conversion-options/distribution-tab.png)
![adattípusok az idő lapon](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Csempe konvertálása
A tervező a teljes állapot ábrázolására és összegzésére használja az Áttekintés csempe funkciót. Ezek hét csempén jelennek meg, a számoktól a diagramig terjedően. A munkafüzetekben a felhasználók hasonló vizualizációkat hozhatnak létre, és rögzíthetik őket az áttekintő csempék eredeti stílusához hasonló módon. 

![Katalógus](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Irányítópult-átalakítás megtekintése
A Designer csempék általában két szakaszból állnak, egy vizualizációból és egy olyan listából, amely megfelel a vizualizációban lévő adatoknak, például a **fánk & lista** csempét.

![fánk](media/view-designer-conversion-options/donut-example.png)

A munkafüzetek esetében lehetővé tesszük a felhasználó számára, hogy lekérdezze a nézet egy vagy mindkét részét. A munkafüzetekbe való lekérdezések egyszerű kétlépéses folyamatnak vannak kialakítva. Először az adatok a lekérdezésből jönnek létre, a másodikban pedig vizualizációként jelennek meg az adatok.  A következő példa azt szemlélteti, hogyan hozza létre újra a nézetet a munkafüzetekbe:

![Konvertálás](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Következő lépések
- [Munkafüzetek & engedélyeinek elérése](view-designer-conversion-access.md)