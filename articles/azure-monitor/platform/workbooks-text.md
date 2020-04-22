---
title: Az Azure Monitor tankönyvek szövegparaméterei
description: Az előre összeállított és egyéni paraméterezett munkafüzetekkel egyszerűsítheti az összetett jelentéseket. További információ a munkafüzet szövegparamétereiről.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c804cc8942a40e2f30c980636194daa82e0fb0e8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687325"
---
# <a name="workbook-text-parameters"></a>Munkafüzet szövegparaméterei

A Szövegdoboz paraméterei egyszerű módot kínálnak a munkafüzet-felhasználók szöveges bevitelének összegyűjtésére. Akkor használatosak, ha nem célszerű legördülő menüt használni a bemenetek összegyűjtéséhez (például egy tetszőleges küszöbértéket vagy általános szűrőket). A munkafüzetek lehetővé teszik a szerzők számára, hogy a szövegdoboz alapértelmezett értékét lekérdezésből szerezzék be. Ez lehetővé teszi az érdekes forgatókönyvek, például az alapértelmezett küszöbérték beállítása a metrika p95 alapján.

A szövegdobozok gyakori használata más munkafüzetvezérlők által használt belső változók. Ez úgy történik, hogy kihasználva a lekérdezés az alapértelmezett értékeket, és így a bemeneti vezérlő láthatatlan olvasási módban. Előfordulhat például, hogy egy felhasználó egy képletből (nem egy felhasználóból) szeretne egy küszöbértéket, majd a küszöbértéket a későbbi lekérdezésekben használni.

## <a name="creating-a-text-parameter"></a>Szövegparaméter létrehozása
1. Kezdje üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben lévő hivatkozásokból.
3. Kattintson a kék _Add Parameter_ gombra.
4. Az új paraméter ablaktáblán, amely megjelenik adja meg a következőt:
    1. Paraméter neve:`SlowRequestThreshold`
    2. Paraméter típusa:`Text`
    3. Szükséges:`checked`
    4. Alapértelmezett érték beszereznie a lekérdezésből:`unchecked`
5. A paraméter létrehozásához válassza a "Mentés" lehetőséget az eszköztáron.

    ![Szövegparaméter létrehozását bemutató kép](./media/workbooks-text/text-create.png)

Így fog kinézni a munkafüzet olvasási módban.

![Szövegparamétert ábrázoló kép olvasási módban](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Hivatkozás szöveges paraméterre
1. Vegyen fel egy lekérdezésvezérlőt a `Add query` munkafüzetbe a kék hivatkozás kiválasztásával és egy Application Insights-erőforrás kiválasztásával.
2. A KQL mezőben adja hozzá ezt a kódrészletet:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Ha a szöveges paramétert 500-as értékkel, a lekérdezésvezérlővel együtt használja, akkor az alábbi lekérdezést hatékonyan futtatja:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Lekérdezés futtatása az eredmények megtekintéséhez

    ![KQL-ben hivatkozott szövegparamétert ábrázoló kép](./media/workbooks-text/text-reference.png)

> [!NOTE]
> A fenti példában egy egész értéket `{SlowRequestThreshold}` jelöl. Ha egy karakterláncot, mint `{ComputerName}` akkor módosítania kell a Kusto `"{ComputerName}"` lekérdezést, hogy adjunk idézőjelek annak érdekében, hogy a paraméter mező egy accept input idézőjelek nélkül.

## <a name="setting-default-values"></a>Alapértelmezett értékek beállítása
1. Kezdje üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben lévő hivatkozásokból.
3. Kattintson a kék _Add Parameter_ gombra.
4. Az új paraméter ablaktáblán, amely megjelenik adja meg a következőt:
    1. Paraméter neve:`SlowRequestThreshold`
    2. Paraméter típusa:`Text`
    3. Szükséges:`checked`
    4. Alapértelmezett érték beszereznie a lekérdezésből:`checked`
5. A KQL mezőben adja hozzá ezt a kódrészletet:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Ez a lekérdezés a szövegmező alapértelmezett értékét az alkalmazás összes kérésének 95.
6. Lekérdezés futtatása az eredmény megtekintéséhez
7. A paraméter létrehozásához válassza a "Mentés" lehetőséget az eszköztáron.

    ![A KQL alapértelmezett értékkel rendelkező szöveges paramétert megjelenítő kép](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Bár ez a példa az Application Insights-adatoklekérdezésére szolgál, a megközelítés bármely naplóalapú adatforráshoz használható – Log Analytics, Azure Resource Graph stb.

## <a name="next-steps"></a>További lépések

* [Ismerkedés a](workbooks-visualizations.md) munkafüzetekkel, számos gazdag vizualizációs lehetőséggel.
* [Szabályozhatja](workbooks-access-control.md) és megoszthatja a munkafüzet erőforrásaihoz való hozzáférést.
