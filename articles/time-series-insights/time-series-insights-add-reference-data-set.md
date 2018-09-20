---
title: Referencia-adatkészlet hozzáadása Azure Time Series Insights-környezete
description: Ez a cikk ismerteti, mivel megvédi az adatokat Azure Time Series Insights-környezete egy referencia-adatkészlet hozzáadása.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/15/2018
ms.openlocfilehash: 45520a556d109158987dd47e33a1efb52a1cd356
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364737"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>A Time Series Insights-környezethez az Azure portal használatával egy referencia-adatkészlet létrehozása

Ez a cikk ismerteti a referencia-adatkészlet hozzáadása Azure Time Series Insights-környezethez. Referenciaadatok akkor hasznos, mivel megvédi az értékeket a forrásadatokban csatlakoztatása.

Referencia-adatkészlet egy bővítésével kiolvassa az eseményeket az eseményforrás gyűjteménye. Time Series Insights bejövő forgalmat kezelő motorja a referencia-adatkészlet minden eseményt az eseményforrás a megfelelő adatok sor csatlakozik. Ez a kibővített esemény ezután lekérdezhető. A csatolás a referencia-adatkészlet meghatározott elsődleges kulcs oszlopoknak alapul.

Referenciaadatok visszamenőlegesen nem csatlakozik tartományhoz. Ez azt jelenti, hogy csak a jelenlegi és jövőbeli bejövő adatainak matched és referencia meghatározott időpontban csatlakozik, konfigurálása és feltöltése után.

## <a name="video"></a>Videó: 

### <a name="in-this-video-we-cover-time-series-insights-reference-data-modelbr"></a>Ez a videó ismerteti Time Series Insight-hivatkozás adatmodellt.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Referencia-adatkészlet hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a meglévő Time Series Insights-környezetbe. Kattintson a **összes erőforrás** az Azure portal bal oldali menüben. Válassza ki az Azure Time Series Insights-környezetet.

3. Válassza ki a **áttekintése** lapot. Keresse meg a **Time Series Insights explorer URL-cím** és a hivatkozás megnyitásához.  

   Tekintse meg a explorer TSI-környezete számára.

4. Bontsa ki a környezet választó a TSI Explorerben. Válassza ki az aktív környezetet. Kattintson a jobb felső sarokban a hivatkozás adatok ikonra az adatkezelő oldal.

   ![Referencia-adatok hozzáadása](media/add-reference-data-set/add_reference_data.png)

5. Válassza ki a **+ adatkészlet hozzáadása** gombra kattintva kezdje el hozzáadni az új adatkészletet.

   ![Adatkészlet hozzáadása](media/add-reference-data-set/add_data_set.png)

6. Az a **új referencia-adatkészlete** lapon a adatok formátumát: 
   - Válasszon **CSV** vesszővel tagolt adatok. Az első sor a fejlécsor számít. 
   - Válasszon **JSON-tömböt** JavaScript object notation (JSON) formátumú adatok.

   ![Válassza ki az adatok formátuma.](media/add-reference-data-set/add_data.png)

7. Adja meg az adatokat, a két módszer egyikével:
   - Illessze be az adatokat a szövegszerkesztőben. Ezután válassza ki **referenciaadatok elemzése** gombra.
   - Válassza ki **fájl kiválasztása** gombra kattintva adhat hozzá adatokat egy helyi szöveges fájlból. 

   Illessze be például a CSV-adatokat: ![beillesztett CSV-adatokat](media/add-reference-data-set/csv_data_pasted.png)

   Illessze be például a JSON-tömb adatok: ![illessze be a JSON-adatok](media/add-reference-data-set/json_data_pasted.png)

   Az adatértékek elemzése hiba történik, ha a hiba vörös színnel jelenik meg a lap alján például `CSV parsing error, no rows extracted`.

8. Miután sikeresen elemzi az adatokat, egy adatrács látható oszlopainak és sorainak jelölő az adatok megjelenítése.  Tekintse át az adatrácsban annak biztosítása érdekében helyességét.

   ![Referencia-adatok hozzáadása](media/add-reference-data-set/parse_data.png)

9. Tekintse át az adattípus feltételezi, hogy minden oszlop, és ha szükséges, módosítsa az adattípust.  Válassza ki az adat típusa szimbólum az oszlop fejlécére: **#** double (numerikus adatot), a **T |} F** Boolean, vagy **Abc** karakterlánc.

   ![Válassza ki az oszlopfejlécek adattípusokat.](media/add-reference-data-set/choose_datatypes.png)

10. Nevezze át az oszlopfejléceket, ha szükséges. Az elsődlegeskulcs-oszlop neve szükség a megfelelő tulajdonság az eseményforrás csatlakoztatása. Győződjön meg arról, hogy a hivatkozás az elsődlegeskulcs-oszlop nevének annak pontosan egyeznie az esemény nevét, a bejövő adatokat, beleértve a kisbetű/nagybetű megkülönböztetése. Nem kulcs oszlop nevét használják, mivel megvédi a bejövő adatok a megfelelő hivatkozás adatok értékekkel.

11. Kattintson a **adjon hozzá egy sort** vagy **oszlop hozzáadása** hozzáadása további referencia adatértékek, igény szerint.

12. Adjon meg egy értéket a **a sorok szűrése...**  mezőt, tekintse át az adott sorokat, igény szerint. A szűrő hasznos, tekintse át az adatokat, de a rendszer nem alkalmazza az adatok feltöltése közben.
 
13. Az adatkészlet neve kitöltésével a **adatkészlet neve** mezőt az adatrácsban felett.

   ![Az adatkészlet nevét.](media/add-reference-data-set/name_reference_dataset.png)

14. Adja meg a **elsődleges kulcs** az adatkészlet kiválasztásával a legördülő fent az adatrácsban oszlopa.

   ![Válassza ki a fő oszlopoknak.](media/add-reference-data-set/set_primary_key.png)

   Választhatja a **+** gombra kattintva adhat hozzá egy másodlagos kulcs oszlop egy összetett elsődleges kulcsként. A kijelölés visszavonása van szüksége, ha eltávolítja a másodlagos kulcsot a legördülő listából válassza ki az üres érték.

15.  Töltse fel az adatokat, válassza a **sorok feltöltése** gombra.

   ![Feltöltés](media/add-reference-data-set/upload_rows.png)

   Az oldal megerősíti, hogy a befejezett feltöltése és az üzenet megjelenítéséhez **sikeresen feltöltve az adatkészlet**.

## <a name="next-steps"></a>További lépések
* [Referencia-adatok kezelése](time-series-insights-manage-reference-data-csharp.md) programozott módon.
* Az alkalmazásprogramozási felület (API) teljes leírását a [Referencia-adatok API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) dokumentum tartalmazza.
