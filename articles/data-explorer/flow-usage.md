---
title: Példák a Microsoft Azure Data Explorer Flow-összekötő (előzetes verzió) használatára
description: Ismerje meg a Microsoft Flow-összekötő használati példáit.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 1b9d593b0f0895e2ba75fae7ab7e78ea883c8907
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521687"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Példák a Microsoft Flow-összekötő (előzetes verzió) használatára

Az Azure Data Explorer flow-összekötő lehetővé teszi, hogy az Azure Data Explorer a [Microsoft Power Automate Flow-képességeivel](https://flow.microsoft.com/) automatikusan futtathassa a Kusto-lekérdezéseket és parancsokat egy ütemezett vagy aktivált feladat részeként. Ez a dokumentum számos gyakori Microsoft Flow-összekötő használati példát tartalmaz.

További információt a [Microsoft Flow-összekötő (előzetes verzió) című témakörben talál.](flow.md)

* [Microsoft Flow-összekötő és SQL](#microsoft-flow-connector-and-sql)
* [Adatok leküldése a Power BI-adatkészletbe](#push-data-to-power-bi-dataset)
* [Feltételes lekérdezések](#conditional-queries)
* [Több Azure Data Explorer-folyamatdiagram elküldése e-mailben](#email-multiple-azure-data-explorer-flow-charts)

## <a name="microsoft-flow-connector-and-sql"></a>Microsoft Flow-összekötő és SQL

A Microsoft Flow-összekötő segítségével lekérdezheti és összesítheti az adatokat egy SQL-adatbázisban.

> [!Note]
> Az SQL-beszúrás minden sorhoz külön-külön történik. Csak kis mennyiségű kimeneti adathoz használja a Microsoft Flow összekötőt. 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> A *Fürtnév* mezőbe írja be a fürt URL-címét.

## <a name="push-data-to-power-bi-dataset"></a>Adatok leküldése a Power BI-adatkészletbe

A Microsoft Flow-összekötő a Power BI-összekötővel együtt leküldéses adatokat Kusto-lekérdezések power BI streamelési adatkészletek.

1. Hozzon létre egy új Lekérdezés futtatása és lista eredmények művelet.
1. Válassza az **Új lépés** lehetőséget.
1. Válassza **a Művelet hozzáadása lehetőséget,** és keressen a Power BI-ra.
1. Válassza a **Power BI**lehetőséget.
1. Válassza **a Sorok hozzáadása adatkészlethez**lehetőséget. 

    ![Flow Power BI-csatlakozó](./media/flow-usage/flow-powerbiconnector.png)
1. Adja meg azt a **munkaterületet**, **az adatkészletet**és **a táblát,** amelyre a rendszer az adatokat lenyomja.
1. A dinamikus tartalom párbeszédpanelen adjon hozzá egy hasznos adat, amely tartalmazza az adatkészlet-sémát és a megfelelő Kusto lekérdezési eredményeket.

    ![Flow Power BI-mezők](./media/flow-usage/flow-powerbifields.png)

A Flow automatikusan alkalmazza a Power BI műveletet a Kusto lekérdezés eredménytáblájának minden sorára. 

![Flow Power BI művelet minden sorhoz](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>Feltételes lekérdezések

A Kusto-lekérdezések eredményei bemenetként vagy feltételekként használhatók a következő folyamatműveletekhez.

A következő példában lekérdezzük Kusto incidensek történt az utolsó napon. Minden egyes megoldott incidenshez egy tartalékidő-üzenetet küld a rendszer, és létrejön egy leküldéses értesítés.
Minden egyes incidens, amely még mindig aktív, Kusto kérdezi további információt a hasonló események. Ezt az információt e-mailben küldi el, és megnyit egy kapcsolódó TFS-feladatot.

Hasonló folyamat létrehozásához kövesse az alábbi utasításokat:

1. Hozzon létre egy új Lekérdezés futtatása és lista eredmények művelet.
1. Válassza az **Új lépés** lehetőséget.
1. Válassza **a Feltétel vezérlő lehetőséget.**
1. A dinamikus tartalomablakban válassza ki a következő műveletek feltételeként használni kívánt paramétert.
1. Válassza ki a *Kapcsolat* és *az Érték* típusát egy adott feltétel beállításához az adott paraméterhez.

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    A folyamat ezt a feltételt alkalmazza a lekérdezés eredménytáblájának minden sorára.
1. Adjon hozzá műveleteket, ha a feltétel igaz és hamis.

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

A Kusto lekérdezés eredményértékeit használhatja bemenetként a következő műveletekhez. Válassza ki az eredményértékeket a dinamikus tartalomablakból.
Az alábbi példában egy Tartalékidő – Üzenet küldése művelet és a Visual Studio – A Kusto-lekérdezésből adatokat tartalmazó új munkaelem-művelet létrehozása.

![Slack - Üzenet utáni művelet](./media/flow-usage/flow-slack.png)

![Visual Studio-művelet](./media/flow-usage/flow-visualstudio.png)

Ebben a példában, ha egy incidens még mindig aktív, lekérdezés Kusto újra információt kapni arról, hogyan incidensek ugyanabból a forrásból megoldódott a múltban.

![Folyamatfeltétel-lekérdezés](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> A *Fürtnév* mezőbe írja be a fürt URL-címét.

Jelenítse meg ezt az információt kördiagramként, és küldje el e-mailben a csapatnak.

![Flow-feltétel e-mail](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>Több Azure Data Explorer-folyamatdiagram elküldése e-mailben

1. Hozzon létre egy új folyamatot az ismétlődési eseményindítóval, és adja meg a Folyamat időközét és a gyakoriságot. 
1. Adjon hozzá egy új lépést egy vagy több Kusto - Lekérdezés futtatása és az eredményműveletek megjelenítése. 

    ![Több lekérdezés futtatása egy folyamatban](./media/flow-usage/flow-severalqueries.png)
1. Minden Egyes Kusto – Lekérdezés futtatása és az eredmény megjelenítése a következő mezőket határozza meg:
    * Fürt URL-címe
    * Adatbázis neve
    * Lekérdezés- és diagramtípus (HTML-táblázat, kördiagram, idődiagram, sávdiagram vagy egyéni érték megadása).

    ![Eredmények megjelenítése több melléklettel](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

1. E-mail küldése (v2) művelet hozzáadása: 
    1. A törzsszakaszban válassza a kódnézet ikonját.
    1. A **Törzs** mezőbe szúrja be a szükséges BodyHtml-kódot, hogy a lekérdezés láthatóvá tétele szerepeljen az e-mail törzsében.
    1. Ha mellékletet szeretne hozzáadni az e-mailhez, adja hozzá a melléklet nevét és a melléklet tartalmát.
    
    ![Több melléklet elküldése e-mailben](./media/flow-usage/flow-email-multiple-attachments.png)

    Az e-mail művelet létrehozásával kapcsolatos teljes körű útmutatásért olvassa el [a Kusto-lekérdezés eredményeinek e-mail ben.](flow.md#email-kusto-query-results) 

Eredmények:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="next-steps"></a>További lépések

Ismerje meg a [Microsoft Azure Explorer Logic App összekötő,](https://docs.microsoft.com/azure/kusto/tools/logicapps) amely egy másik módja a Kusto-lekérdezések és parancsok automatikus futtatásának részeként egy ütemezett vagy aktivált feladat.
