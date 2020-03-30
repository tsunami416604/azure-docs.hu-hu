---
title: Példák a Microsoft Azure Data Explorer Flow-összekötő (előzetes verzió) használatára
description: Ismerje meg a Microsoft Flow-összekötő használati példáit.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 759ea9800a0ed051fed887adfb10b06f7a53b72f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529203"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>Példák a Microsoft Flow-összekötő (előzetes verzió) használatára

Az Azure Data Explorer flow-összekötő lehetővé teszi, hogy az Azure Data Explorer a [Microsoft Power Automate Flow-képességeivel](https://flow.microsoft.com/) automatikusan futtathassa a Kusto-lekérdezéseket és parancsokat egy ütemezett vagy aktivált feladat részeként. Ez a dokumentum számos gyakori Microsoft Flow-összekötő használati példát tartalmaz.

További információt a [Microsoft Flow-összekötő (előzetes verzió) című témakörben talál.](flow.md)

* [Microsoft Flow-összekötő és SQL](#microsoft-flow-connector-and-sql)
* [Adatok leküldése a Power BI-adatkészletbe](#push-data-to-power-bi-dataset)
* [Feltételes lekérdezések](#conditional-queries)
* [Több Azure Data Explorer-folyamatdiagram elküldése e-mailben](#email-multiple-azure-data-explorer-flow-charts)
* [Másik e-mail küldése különböző partnereknek](#send-a-different-email-to-different-contacts)
* [Egyéni HTML-táblázat létrehozása](#create-a-custom-html-table)

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

1. Hozzon létre egy új folyamatot az "Ismétlődés" eseményindítóval, és adja meg a Folyamat időközét és a gyakoriságot. 
1. Adjon hozzá egy új lépést egy vagy több Kusto - Lekérdezés futtatása és az eredményműveletek megjelenítése. 

    ![Több lekérdezés futtatása egy folyamatban](./media/flow-usage/flow-severalqueries.png)
1. Minden Egyes Kusto – Lekérdezés futtatása és az eredmény megjelenítése a következő mezőket határozza meg:
    * Fürt URL-címe (a *Fürtnév* mezőben)
    * Adatbázis neve
    * Lekérdezés és diagramtípusa (HTML-táblázat/ kördiagram/ idődiagram/ sávdiagram/ Egyéni érték megadása).

    ![Eredmények megjelenítése több melléklettel](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

    > [!IMPORTANT]
    > A *Fürtnév* mezőkben adja meg a fürt URL-címét.

1. E-mail küldése művelet hozzáadása. 
    * A *Törzs* mezőbe szúrja be a szükséges törzset, hogy a lekérdezés láthatóvá tétele szerepeljen az e-mail törzsében.
    * Ha mellékletet szeretne hozzáadni az e-mailhez, adja hozzá a melléklet nevét és a melléklet tartalmát.
    
    ![Több melléklet elküldése e-mailben](./media/flow-usage/flow-emailmultipleattachments.png)

Eredmények:

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="send-a-different-email-to-different-contacts"></a>Másik e-mail küldése különböző partnereknek

A Microsoft Flow-val különböző testreszabott e-maileket küldhet különböző partnereknek. Az e-mail címek és az e-mail tartalma egy Kusto lekérdezés eredménye.

Példa:

![Dinamikus e-mail Kusto-lekérdezéssel](./media/flow-usage/flow-dynamicemailkusto.png)

> [!IMPORTANT]
> A *Fürtnév* mezőbe írja be a fürt URL-címét.

![Dinamikus e-mail a folyamatműveletben](./media/flow-usage/flow-dynamicemail.png)

## <a name="create-a-custom-html-table"></a>Egyéni HTML-táblázat létrehozása

A Microsoft Flow segítségével egyéni HTML-elemeket, például egyéni HTML-táblázatokat hozhat létre és használhat.

A következő példa bemutatja, hogyan hozhat létre egyéni HTML-táblázatot. A HTML-táblázat sorait naplószint szerint színezi (ugyanaz, mint az Azure Data Explorerben).

Hasonló folyamat létrehozásához kövesse az alábbi utasításokat:

1. Hozzon létre egy új Kusto - Lekérdezés futtatása és lista eredmények művelet.

    ![HTML-táblázat eredményeinek listázása](./media/flow-usage/flow-listresultforhtmltable.png)

> [!IMPORTANT]
> A *Fürtnév* mezőbe írja be a fürt URL-címét.

1. A lekérdezés eredményének körbejárva, és hozza létre a HTML-tábla törzsét: 
    1. Ha a HTML-karakterláncot tartó változót szeretné létrehozni, válassza az **Új lépés** lehetőséget
    1. Válassza **a Művelet hozzáadása lehetőséget,** és keressen változókat. 
    1. Válassza a **Változók - Inicializálása változó**. 
    1. Karakterlánc-változó inicializálása a következőképpen:

    ![Változó inicializálása](./media/flow-usage/flow-initializevariable.png)

1. Hurok az eredmények:
    1. Válassza az **Új lépés** lehetőséget.
    1. Válassza a **Művelet hozzáadása** lehetőséget.
    1. Változók keresése. 
    1. Válassza a **Változók - Hozzáfűzés karakterlánc-változóhoz lehetőséget.** 
    1. Válassza ki az inicializált változónevet, és hozza létre a HTML-táblasorokat a lekérdezés eredményével. 
    A lekérdezés eredményének kiválasztásakor az alkalmazás automatikusan hozzáadódik.

    Az alábbi példában `if` a kifejezés határozza meg az egyes sorok stílusát:

    ```if(equals(items('Apply_to_each')?['Level'], 'Warning'), 'Yellow', if(equals(items('Apply_to_each')?['Level'], 'Error'), 'red', 'white'))```

    [![](./media/flow-usage/flow-createhtmltableloopcontent.png "Create HTML table loop content")](./media/flow-usage/flow-createhtmltableloopcontent.png#lightbox)

1. A teljes HTML-tartalom létrehozása: 
    1. Új művelet hozzáadása az Alkalmazás az egyes műveletekhez kívül. 
    A következő példában a művelet használt küldése egy e-mailt.
    1. Definiálja a HTML-táblázatot az előző lépésekben szereplő változó segítségével. 
    1. Ha e-mailt küld, válassza **a Speciális beállítások megjelenítése** lehetőséget, és az Is HTML csoportban válassza az **Igen**lehetőséget.

    ![Egyéni HTML-táblázat e-mail címe](./media/flow-usage/flow-customhtmltablemail.png)

Eredmény:

![Egyéni HTML-táblázat e-mail eredménye](./media/flow-usage/flow-customhtmltableresult.png)

## <a name="next-steps"></a>További lépések

Ismerje meg a [Microsoft Azure Explorer Logic App összekötő,](https://docs.microsoft.com/azure/kusto/tools/logicapps) amely egy másik módja a Kusto-lekérdezések és parancsok automatikus futtatásának részeként egy ütemezett vagy aktivált feladat.
