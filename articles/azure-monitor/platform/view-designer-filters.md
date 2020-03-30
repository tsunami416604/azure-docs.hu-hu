---
title: Szűrők az Azure Monitor nézetekben | Microsoft dokumentumok
description: Az Azure Monitor nézetben lévő szűrő lehetővé teszi a felhasználók számára, hogy egy adott tulajdonság értéke szerint szűrjék a nézetben lévő adatokat anélkül, hogy maga a nézetet módosítanák.  Ez a cikk azt ismerteti, hogy miként használhat szűrőt, és hogyan adhat hozzá egyet az egyéni nézethez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658574"
---
# <a name="filters-in-azure-monitor-views"></a>Szűrők az Azure Monitor nézetekben
Az Azure [Monitor nézetben](view-designer.md) lévő **szűrő** lehetővé teszi a felhasználók számára, hogy egy adott tulajdonság értéke szerint szűrjék a nézetben lévő adatokat anélkül, hogy maga a nézetet módosítanák.  Engedélyezheti például, hogy a nézet felhasználói csak egy adott számítógépről vagy számítógépcsoportról szűrjék a nézetet.  Egyetlen nézetben több szűrőt is létrehozhat, így a felhasználók több tulajdonság szerint szűrhetnek.  Ez a cikk azt ismerteti, hogy miként használhat szűrőt, és hogyan adhat hozzá egyet az egyéni nézethez.

## <a name="using-a-filter"></a>Szűrő használata
Kattintson a nézet tetején lévő dátumidő-tartományra a legördülő lista megnyitásához, ahol módosíthatja a nézet dátumidő-tartományát.

![Példa szűrése](media/view-designer-filters/filters-example-time.png)

Kattintson **+** a gombra a szűrő hozzáadásához a nézethez definiált egyéni szűrők használatával. Válasszon ki egy értéket a szűrőhöz a legördülő menüből, vagy írjon be egy értéket. Folytassa a szűrők hozzáadását **+** a lehetőségre kattintva. 


![Példa szűrése](media/view-designer-filters/filters-example-custom.png)

Ha eltávolítja egy szűrő összes értékét, akkor a szűrő a továbbiakban nem lesz alkalmazva.


## <a name="creating-a-filter"></a>Szűrő létrehozása

Hozzon létre egy szűrőt a **Szűrők** lapon [nézet szerkesztésekor.](view-designer.md)  A szűrő globális a nézetben, és a nézet minden részére vonatkozik.  

![Beállítások szűrése](media/view-designer-filters/filters-settings.png)

Az alábbi táblázat a szűrő beállításait ismerteti.

| Beállítás | Leírás |
|:---|:---|
| Mezőnév | A szűréshez használt mező neve.  Ennek a mezőnek meg kell egyeznie a Lekérdezés értékekhez mező összegző **mezőjével.** |
| Értékek lekérdezése | A felhasználó szűrőlegördülő menüjének feltöltéséhez futtatandó lekérdezés.  A lekérdezésnek [összegző](/azure/kusto/query/summarizeoperator) vagy [eltérő](/azure/kusto/query/distinctoperator) értéket kell használnia egy adott mező egyedi értékeinek megadásához, és meg kell egyeznie a **mezőnévvel.**  [A rendezés](/azure/kusto/query/sortoperator) segítségével rendezheti a felhasználó számára megjelenített értékeket. |
| Címke | A szűrőt támogató lekérdezésekben használt mező neve, amely a felhasználó számára is megjelenik. |

### <a name="examples"></a>Példák

Az alábbi táblázat néhány példát tartalmaz a gyakori szűrőkre.  

| Mezőnév | Értékek lekérdezése | Címke |
|:--|:--|:--|
| Computer   | A számítógép &#124; különálló &#124; számítógép asc szerint rendezve | Számítógépek |
| EventLevelName (Eseményszintneve) | Az esemény &#124; eltérő EventLevelName | Severity |
| Súlyossági szint | Syslog &#124; különböző severitylevel | Severity |
| SvcChangeType típus | ConfigurationChange &#124; különálló svcChangeType | Változástípusa |


## <a name="modify-view-queries"></a>Nézetlekérdezések módosítása

Ahhoz, hogy egy szűrő bármilyen hatással legyen, módosítania kell a nézetben lévő lekérdezéseket, hogy szűrje a kijelölt értékeket.  Ha nem módosítja a lekérdezéseket a nézetben, akkor a felhasználó által kiválasztott értékeknek nincs hatása.

A lekérdezésben a szűrőérték használatának szintaxisa a következő: 

    where ${filter name}  

Ha például a nézetben van egy olyan lekérdezés, amely eseményeket ad vissza, és _a Számítógépek_nevű szűrőt használja, használhatja a következő lekérdezést.

    Event | where ${Computers} | summarize count() by EventLevelName

Ha hozzáadott egy másik szűrőt, a Súlyosság, a következő lekérdezést használhatja mindkét szűrő használatához.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>További lépések
* További információ az egyéni nézethez hozzáadható [képi megjelenítési részekről.](view-designer-parts.md)
