---
title: Az Azure Monitor munkafüzetek erőforrás-paraméterei
description: Az összetett jelentések egyszerűsítése előre összeállított és egyéni paraméterezett munkafüzetekkel
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: cc2cde7932f783f63ee2783f0589ce4f88f248a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658102"
---
# <a name="workbook-resource-parameters"></a>Munkafüzet erőforrás-paraméterei

Az erőforrás-paraméterek lehetővé teszik az erőforrások kiválasztását a munkafüzetekben. Ez akkor hasznos, ha beállítja azt a hatókört, amelyből az adatokat be szeretné szerezni. Egy példa, amely lehetővé teszi a felhasználók számára, hogy válassza ki a virtuális gépek, amelyek a diagramok később fogja használni az adatok bemutatásakor.

Az erőforrás-választók értékei származhatnak a munkafüzet környezetéből, statikus listából vagy az Azure Resource Graph lekérdezésekből.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Erőforrás-paraméter (munkafüzet-erőforrások) létrehozása
1. Kezdje üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben lévő hivatkozásokból.
3. Kattintson a kék _Add Parameter_ gombra.
4. Az új paraméter ablaktáblán, amely megjelenik adja meg a következőt:
    1. Paraméter neve:`Applications`
    2. Paraméter típusa:`Resource picker`
    3. Szükséges:`checked`
    4. Több kijelölés engedélyezése:`checked`
5. Adatok beszerezése innen:`Workbook Resources`
6. Csak erőforrástípusokat tartalmaz:`Application Insights`
7. A paraméter létrehozásához válassza a "Mentés" lehetőséget az eszköztáron.

![Erőforrás-paraméter létrehozását bemutató kép munkafüzet-erőforrások használatával](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Erőforrás-paraméter létrehozása (Azure Resource Graph)
1. Kezdje üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben lévő hivatkozásokból.
3. Kattintson a kék _Add Parameter_ gombra.
4. Az új paraméter ablaktáblán, amely megjelenik adja meg a következőt:
    1. Paraméter neve:`Applications`
    2. Paraméter típusa:`Resource picker`
    3. Szükséges:`checked`
    4. Több kijelölés engedélyezése:`checked`
5. Adatok beszerezése innen:`Query`
    1. Lekérdezés típusa:`Azure Resource Graph`
    2. Előfizetések:`Use default subscriptions`
    3. A lekérdezés vezérlőben adja hozzá ezt a kódrészletet
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. A paraméter létrehozásához válassza a "Mentés" lehetőséget az eszköztáron.

![Erőforrás-paraméter létrehozásáról az Azure Resource Graph használatával](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Az Azure Resource Graph még nem érhető el minden felhőben. Győződjön meg arról, hogy támogatja a célfelhőben, ha ezt a módszert választja.

[Az Azure Resource Graph dokumentációja](https://docs.microsoft.com/azure/governance/resource-graph/overview)

## <a name="creating-a-resource-parameter--json-list"></a>Erőforrás-paraméter létrehozása (JSON-lista)
1. Kezdje üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben lévő hivatkozásokból.
3. Kattintson a kék _Add Parameter_ gombra.
4. Az új paraméter ablaktáblán, amely megjelenik adja meg a következőt:
    1. Paraméter neve:`Applications`
    2. Paraméter típusa:`Resource picker`
    3. Szükséges:`checked`
    4. Több kijelölés engedélyezése:`checked`
5. Adatok beszerezése innen:`JSON`
    1. A tartalomvezérlőben adja hozzá ezt a json-kódrészletet
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Nyomja meg a kék _Frissítés_ gombot.
6. Tetszés `Include only resource types` szerint állítsa be az _Application Insights_
7. A paraméter létrehozásához válassza a "Mentés" lehetőséget az eszköztáron.

## <a name="referencing-a-resource-parameter"></a>Hivatkozás erőforrásparaméterre
1. Vegyen fel egy lekérdezésvezérlőt a munkafüzetbe, és jelöljön ki egy Application Insights-erőforrást.
2. Az _Application Insights_ legördülő segítségével a paramétert a vezérlőhöz kötje. Ezzel beállítja a lekérdezés hatókörét a paraméter által futásidőben visszaadott erőforrásokra.
4. A KQL vezérlőben adja hozzá ezt a kódrészletet
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Futtassa a lekérdezést az eredmények megtekintéséhez. 

![Lekérdezésvezérlőben hivatkozott erőforrásparamétert ábrázoló kép](./media/workbooks-resources/resource-reference.png)

> Ez a megközelítés erőforrások kötéséhez használható más vezérlők, például metrikák.

## <a name="resource-parameter-options"></a>Erőforrás-paraméter beállításai
| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `{Applications}` | A kijelölt erőforrásazonosító | _/subscriptions/<sub-id>/resourceGroups/<erőforráscsoport>/providers/<erőforrás-típusú>/acmeauthentication_ |
| `{Applications:label}` | A kijelölt erőforrás címkéje | `acmefrontend` |
| `{Applications:value}` | A kijelölt erőforrás értéke | _"/subscriptions/<alazonosító>/resourceGroups/<erőforráscsoport>/providers/<erőforrás-típusú>/acmeauthentication"_ |
| `{Applications:name}` | A kijelölt erőforrás neve | `acmefrontend` |
| `{Applications:resourceGroup}` | A kijelölt erőforrás erőforráscsoportja | `acmegroup` |
| `{Applications:resourceType}` | A kijelölt erőforrás típusa | _microsoft.insights/components_ |
| `{Applications:subscription}` | A kijelölt erőforrás előfizetése |  |
| `{Applications:grid}` | Az erőforrás tulajdonságait megjelenítő rács. Hasznos a szövegterületen való rendereléshez hibakeresés közben  |  |

## <a name="next-steps"></a>További lépések

* [Ismerkedés a](workbooks-visualizations.md) munkafüzetekkel, számos gazdag vizualizációs lehetőséggel.
* [Szabályozhatja](workbooks-access-control.md) és megoszthatja a munkafüzet erőforrásaihoz való hozzáférést.
