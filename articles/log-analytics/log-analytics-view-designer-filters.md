---
title: "Az Azure Naplóelemzés nézetekben szűrők |} Microsoft Docs"
description: "Naplóelemzési nézetben szűrő lehetővé teszi, hogy a felhasználók számára az adatok szűrése a nézetben által egy adott tulajdonság értéke maga a nézet módosítása nélkül.  Ez a cikk ismerteti, hogyan szűrőt, és vegyen fel egyet a egyéni nézetekben."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: bwren
ms.openlocfilehash: 5c2201292eb085dcc043e4257580c7971dbaffbd
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="filters-in-log-analytics-views"></a>A Naplóelemzési nézetekben szűrők
A **szűrő** a egy [Naplóelemzési megtekintése](log-analytics-view-designer.md) lehetővé teszi a felhasználóknak a nézetben szereplő adatok szűrését által egy adott tulajdonság értéke maga a nézet módosítása nélkül.  Így például csak egy adott számítógépről származó adatok a nézet szűréséhez a nézet felhasználók vagy számítógépek csoportja.  Létrehozhat több szűrő a felhasználók több tulajdonságra szűrés egyetlen nézetben.  Ez a cikk ismerteti, hogyan szűrőt, és vegyen fel egyet a egyéni nézetekben.

## <a name="using-a-filter"></a>Szűrő használata
Kattintson a **szűrő** a keresőablak nézet megnyitásához.  Ez lehetővé teszi, hogy egy időtartományt és szűrőket a nézet számára elérhető értékeinek kiválasztása.  Amikor kiválaszt egy szűrő, az elérhető értékek listáját jeleníti meg.  Válasszon egy vagy több érték, vagy írja be azokat. A nézet a megadott szűrési automatikusan frissül. 

Ha nem ad meg értéket a szűrő van kijelölve, ez a szűrő a nézet nem alkalmazza.  Ha eltávolítja az összes szűrő értékét, majd a szűrő már nem érvényesek.


![Szűrő – példa](media/log-analytics-view-designer/filters-example.png)


## <a name="creating-a-filter"></a>A szűrő létrehozása

A szűrő létrehozása a **szűrők** lapon mikor [nézet szerkesztése](log-analytics-view-designer.md).  A szűrő globális a nézet, és a nézetben mind részt vonatkozik.  

![Beállítások szűrése](media/log-analytics-view-designer/filters-settings.png)

Az alábbi táblázat bemutatja a szűrő beállításait.

| Beállítás | Leírás |
|:---|:---|
| Mező neve | A szűréshez használt mező neve.  Ennek egyeznie kell a summarize mezője **értékek lekérdezés**. |
| Lekérdezés értékek | Lekérdezés, amely a felhasználó szűrő legördülő feltöltéséhez futtassa.  Ez kell használnia, vagy [összefoglalója](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) vagy [különböző](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) egyedi értékeket jelentenek egy adott mező, és meg kell egyeznie a **mezőnév**.  Használhat [rendezési](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator) rendezheti az értékeket, a felhasználó számára megjelenített. |
| Címke | A támogatása a szűrési lekérdezések használatban van, és a felhasználók számára is látható mező nevét. |

### <a name="examples"></a>Példák

Az alábbi táblázatban néhány példa a közös szűrőket tartalmaz.  

| Mező neve | Lekérdezés értékek | Címke |
|:--|:--|:--|
| Computer   | Szívverés &#124; különálló számítógép &#124; Rendezze a számítógép asc | Számítógépek |
| EventLevelName | Esemény &#124; különböző EventLevelName | Súlyosság |
| Súlyossági szint | Syslog &#124; különböző súlyossági szint | Súlyosság |
| SvcChangeType | Konfigurációváltozás &#124; különböző svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Nézetű lekérdezések módosítása

Egy szűrőhöz hatástalan módosítania kell a nézet a kijelölt értékekre szűrhet lekérdezéseket.  Ha nem módosítja a nézetben lekérdezéseket a felhasználó kijelöli értékkel nincs hatással lesz.

Az egyik szűrőérték használatát a lekérdezés szintaxisa a következő: 

    where ${filter name}  

Például ha a nézet a beolvasása események lekérdezés tartozik, és számítógépeket nevezett szűrőt használja, használhatja a következő.

    Event | where ${Computers} | summarize count() by EventLevelName

Ha adott súlyossági nevű másik szűrőt, a következő lekérdezés segítségével mindkét szűrők használata.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Következő lépések
* További információ a [képi megjelenítés részek](log-analytics-view-designer-parts.md) adhat hozzá az egyéni nézetben.