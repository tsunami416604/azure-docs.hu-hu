---
title: Az Azure Monitor munkafüzeteinek időparaméterei
description: Az összetett jelentések egyszerűsítése előre összeállított és egyéni paraméterezett munkafüzetekkel
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658013"
---
# <a name="workbook-time-parameters"></a>Munkafüzet időparaméterei

Az időparaméterek lehetővé teszik a felhasználók számára az elemzés időkörnyezetének beállítását, és szinte az összes jelentés használja őket. Viszonylag egyszerű beállítani és használni - lehetővé téve a szerzők számára, hogy meghatározzák a legördülő legördülő időtartományokat, beleértve az egyéni időtartományok lehetőségét is. 

## <a name="creating-a-time-parameter"></a>Időparaméter létrehozása
1. Kezdje üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben lévő hivatkozásokból.
3. Kattintson a kék _Add Parameter_ gombra.
4. Az új paraméter ablaktáblán, amely megjelenik adja meg a következőt:
    1. Paraméter neve:`TimeRange`
    2. Paraméter típusa:`Time range picker`
    3. Szükséges:`checked`
    4. Elérhető időtartományok: Utolsó óra, Utolsó 12 óra, Utolsó 24 óra, Utolsó 48 óra, Utolsó 3 nap, Utolsó 7 nap és Egyéni időtartomány-kijelölés engedélyezése
5. A paraméter létrehozásához válassza a "Mentés" lehetőséget az eszköztáron.

    ![Időtartomány-paraméter létrehozásáról kép](./media/workbooks-time/time-settings.png)

Így fog kinézni a munkafüzet olvasási módban.

![Időtartomány-paramétert ábrázoló kép olvasási módban](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Hivatkozás időparaméterre
### <a name="via-bindings"></a>Keresztül kötések
1. Vegyen fel egy lekérdezésvezérlőt a munkafüzetbe, és jelöljön ki egy Application Insights-erőforrást.
2. A legtöbb munkafüzetvezérlő támogatja _az Időtartomány_ hatókör-választót. Nyissa meg az _Időtartomány_ legördülő menüt, és válassza ki az `{TimeRange}` időcsengetési paraméterek csoportját az alján.
3. Ez köti az időtartomány paraméterét a diagram időtartományához. A mintalekérdezés időhatóköre most már 24 óra.
4. Lekérdezés futtatása az eredmények megtekintéséhez

    ![Kötéseken keresztül hivatkozott időtartomány-paramétert ábrázoló kép](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>A kql-ban
1. Vegyen fel egy lekérdezésvezérlőt a munkafüzetbe, és jelöljön ki egy Application Insights-erőforrást.
2. A KQL-ben adjon meg egy időhatókör-szűrőt a következő paraméter használatával:`| where timestamp {TimeRange}`
3. Ez kibővíti a `| where timestamp > ago(1d)`lekérdezés kiértékelési idejét , amely a paraméter időtartományának értéke.
4. Lekérdezés futtatása az eredmények megtekintéséhez

    ![KQL-ben hivatkozott időtartományt ábrázoló kép](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>Szövegben 
1. Szövegvezérlő hozzáadása a munkafüzethez.
2. A jelölés lefelé mezőbe írja be a`The chosen time range is {TimeRange:label}`
3. Válassza a _Kész szerkesztés lehetőséget_
4. A szövegvezérlő szöveget jelenít meg: _A kiválasztott időtartomány az elmúlt 24 óra_

## <a name="time-parameter-options"></a>Időparaméter-beállítások
| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Időtartomány-címke | Elmúlt 24 óra |
| `{TimeRange:label}` | Időtartomány-címke | Elmúlt 24 óra |
| `{TimeRange:value}` | Időtartomány értéke | > ezelőtt (1d) |
| `{TimeRange:query}` | Időtartomány-lekérdezés | > ezelőtt (1d) |
| `{TimeRange:start}` | Időtartomány kezdési időpontja | 2019.03.20. |
| `{TimeRange:end}` | Időtartomány befejezési ideje | 2019.03.21. |
| `{TimeRange:grain}` | Időtartomány-szemcséme | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Paraméterbeállítások használata lekérdezésben
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>További lépések

* [Ismerkedés a](workbooks-visualizations.md) munkafüzetekkel, számos gazdag vizualizációs lehetőséggel.
* [Szabályozhatja](workbooks-access-control.md) és megoszthatja a munkafüzet erőforrásaihoz való hozzáférést.
