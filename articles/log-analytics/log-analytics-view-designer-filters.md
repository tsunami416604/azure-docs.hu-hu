---
title: Szűrők az Azure Log Analytics nézetei |} A Microsoft Docs
description: A Log Analytics-nézetet a szűrő lehetővé teszi a felhasználóknak az adatok nézetben található szűrés egy adott tulajdonságának értéke maga a nézet módosítása nélkül.  Ez a cikk bemutatja, hogyan használhatja egy szűrőt, és adjon hozzá egyet, egyéni nézet.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: dcdc84f100ce534ea517f0201b0c090c3059a318
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47160929"
---
# <a name="filters-in-log-analytics-views"></a>A Log Analytics-nézetekben szűrők
A **szűrő** a egy [megtekintése a Log Analytics](log-analytics-view-designer.md) lehetővé teszi a felhasználóknak az adatok nézetben található szűrés egy adott tulajdonságának értéke maga a nézet módosítása nélkül.  Például az adatok csak egy adott számítógép a nézet szűréséhez a nézet felhasználókat vagy számítógépeket sikerült engedélyezése.  Az, hogy a felhasználók több tulajdonság szerinti szűréshez használandó egyetlen nézetben több szűrőt is létrehozhat.  Ez a cikk bemutatja, hogyan használhatja egy szűrőt, és adjon hozzá egyet, egyéni nézet.

## <a name="using-a-filter"></a>A szűrők használatával
Kattintson egy nézetre annak megnyitásához, ahol módosíthatja az adatok időintervallumát a nézet legördülő tetején az adatok időtartományt.

![Példa szűrése](media/log-analytics-view-designer/filters-example-time.png)

Kattintson a **+** használatával egyéni szűrőket a nézet szűrő hozzáadásához. Vagy válassza ki a szűrő egy értéket a legördülő listából, vagy írjon be egy értéket. Szűrők hozzáadása gombra kattintva folytassa a **+**. 


![Példa szűrése](media/log-analytics-view-designer/filters-example-custom.png)

Ha eltávolítja az összes szűrő értékeit, majd a szűrő már nem lépnek érvénybe.


## <a name="creating-a-filter"></a>Szűrő létrehozása

A szűrő létrehozása a **szűrők** lapon mikor [szerkesztési nézet](log-analytics-view-designer.md).  A szűrő a nézet globális, és a nézetben minden részére vonatkozik.  

![Szűrőbeállítások](media/log-analytics-view-designer/filters-settings.png)

A következő táblázat ismerteti az egy szűrő beállításait.

| Beállítás | Leírás |
|:---|:---|
| Mezőnév | A szűréshez használt mező neve.  Ennek egyeznie kell a summarize mezője **értékek lekérdezése**. |
| Értékek lekérdezése | A lekérdezés futtatása a felhasználói szűrő legördülő lista feltöltéséhez.  Ezt kell használnia, vagy [összefoglalója](/azure/kusto/query/summarizeoperator) vagy [különböző](/azure/kusto/query/distinctoperator) egyedi értékeket jelentenek a mezőt, és meg kell egyeznie a **mezőnév**.  Használhat [rendezési](/azure/kusto/query/sortoperator) rendezheti az értékeket, a felhasználó számára megjelenített. |
| Címke | Lekérdezések támogatása a szűrő van használatban, és a felhasználó számára is megjelenik a mező nevét. |

### <a name="examples"></a>Példák

Az alábbi táblázatban néhány példa a gyakran alkalmazott szűrőket tartalmaz.  

| Mezőnév | Értékek lekérdezése | Címke |
|:--|:--|:--|
| Computer   | Szívverés &#124; distinct Computer &#124; számítógép növekvő rendezés | Számítógépek |
| EventLevelName | Esemény &#124; különböző Error | Severity |
| SeverityLevel | Syslog &#124; különböző err | Severity |
| SvcChangeType | Konfigurációváltozás &#124; különböző svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Módosítsa a nézet lekérdezéseit

Szűrők csak lépnek érvénybe módosítania kell a nézetben a kiválasztott értékek szűrés lekérdezéseket.  Ha nem módosítja a nézet a lekérdezésekre majd minden olyan értéket, a felhasználó nem lesz hatása.

A szűrő értékét a lekérdezés szintaxisa: 

    where ${filter name}  

Például ha a nézet rendelkezik egy lekérdezést a értéket ad vissza eseményeket, és számítógépek nevű szűrőt használ, használhatja a következő.

    Event | where ${Computers} | summarize count() by EventLevelName

Ha adott súlyossági nevű másik szűrőt, a következő lekérdezés segítségével mindkét szűrők használata.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>További lépések
* Tudjon meg többet a [Vizualizáció részek](log-analytics-view-designer-parts.md) adhat hozzá az egyéni nézet.