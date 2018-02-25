---
title: "A referencia-adatkészlet felvétele az Azure idő adatsorozat Insights környezet"
description: "Ez a cikk ismerteti a Azure idő adatsorozat Insights környezetben lévő adatok révén hivatkozás adatkészlet hozzáadása."
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: MicrosoftDocs/tsidocs
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 02/15/2018
ms.openlocfilehash: e0d11f253d5aa143ff636c4dc8dff7665a80360e
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Létrehozhat egy hivatkozási adatokat a idő adatsorozat Insights környezet az Azure portál használatával

Ez a cikk ismerteti a referencia-adatkészlet felvétele az Azure idő adatsorozat Insights környezetben. Referenciaadatok akkor hasznos, csatlakoztatása révén az értékeket a forrásadatokban.

A referencia-adatkészlet kiegészítheti az esemény forrásból származó események elemek gyűjteménye. Idő adatsorozat Insights érkező motor minden esemény az eseményforrás a megfelelő adatsor a csatlakozik a referencia-adatkészlet a. Ez a kibővített esemény ezután lekérdezhető. Ezt az összekapcsolást az elsődleges kulcs oszlop(ok) meghatározott a referencia-adatkészlet alapján történik.

Referenciaadatok visszamenőleges nincs tartományhoz csatlakoztatva. Ez azt jelenti, hogy csak a jelenlegi és jövőbeli érkező adatok egyező és referencia meghatározott időpontban csatlakozik, konfigurálása és feltöltése után.

## <a name="add-a-reference-data-set"></a>A referencia-adatkészlet hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a meglévő idő adatsorozat Insights környezetben. Kattintson a **összes erőforrás** a menüben, az Azure portál bal oldalán. Válassza ki az Azure Time Series Insights-környezetet.

3. Válassza ki a **áttekintése** lap. Keresse meg a **idő adatsorozat Insights explorer URL-cím** és a hivatkozás megnyitásához.  

   Tekintse meg a explorer ÁME környezetnek.

4. Bontsa ki a környezet választó a ÁME Explorer. Válassza ki az aktív környezetbe. A hivatkozás adatok ikonra a jobb felső részén jelölje ki a kezelőoldalt.

   ![Referenciaadatok hozzáadása](media/add-reference-data-set/add_reference_data.png)

5. Válassza ki a **+ Hozzáadás adatkészlet** gombra egy új adatkészlet hozzáadása megkezdéséhez.

   ![Adatkészlet hozzáadása](media/add-reference-data-set/add_data_set.png)

6. Az a **új hivatkozik adatkészlet** lapon, válassza ki az adatok formátumát: 
   - Válasszon **CSV** vesszővel tagolt adatok. Az első sort fejlécsorként a rendszer kezeli. 
   - Válasszon **JSON-tömb** a javascript object notation (JSON) formátumú adatok.

   ![Válassza ki az adatok formátuma.](media/add-reference-data-set/add_data.png)

7. Adja meg az adatokat, a két módszer egyikével:
   - Az adatok illessze be a szövegszerkesztőben. Ezt követően válassza **elemzési referenciaadatok** gombra.
   - Válassza ki **Choose File** adatok hozzáadása a helyi fájlból gombra. 

   Illessze be például a CSV-adatok: ![beillesztett CSV-adatok](media/add-reference-data-set/csv_data_pasted.png)

   Illessze be például a JSON-tömb adatokat: ![illessze be JSON-adatok](media/add-reference-data-set/json_data_pasted.png)

   Ha hiba történt az adatértékek elemzése során, a hibaüzenet jelenik meg a lap alján piros, mint `CSV parsing error, no rows extracted`.

8. Miután az adatok elemzése sikertelen, adatok rács látható oszlopainak és sorainak képviselő az adatok megjelenítése.  Tekintse át az adatrácson helyességét biztosításához.

   ![Referenciaadatok hozzáadása](media/add-reference-data-set/parse_data.png)

9. Minden oszlopban tekintheti meg az adattípust, feltételezve, hogy tekintse át, és szükség esetén változtassa meg az adattípust.  Válassza ki az adatok típusa szimbólum az oszlopfejléc:  **#**  dupla (numerikus adatokat), a **T |} F** a logikai érték, vagy **Abc** karakterláncot kell megadnia.

   ![Válassza ki az oszlopfejlécek adattípusokat.](media/add-reference-data-set/choose_datatypes.png)

10. Ha szükséges, nevezze át az oszlopfejlécek. A kulcs oszlopnév szükség az eseményforrás megfelelő tulajdonságának csatlakoztatása. Győződjön meg arról, hogy a hivatkozás adatok kulcsoszlop megfelel-e az esemény nevét a bejövő adatok, beleértve a kisbetű/nagybetű megkülönböztetése annak pontosan. A nem kulcs típusú oszlop neveket használnak, a bejövő adatok a megfelelő hivatkozást az adatértékek révén.

11. Kattintson a **adhat hozzá új sort** vagy **oszlop hozzáadása** hozzáadása több olyan hivatkozást adatértéket, igény szerint.

12. Adjon meg egy értéket a **a sorok szűrése...**  mező áttekintheti adott sorokra, igény szerint. A szűrő hasznos adatok megtekintésével, de nem lesz alkalmazva, az adatok feltöltésekor.
 
13. Az adatkészlet neve kitöltésével a **adatkészlet neve** fölött a program az adatrács mezőjét.

   ![Az adatkészlet nevét.](media/add-reference-data-set/name_reference_dataset.png)

14. Adja meg a **elsődleges kulcs** oszlop a következő adatkészletben, a legördülő fölött a program az adatrács kiválasztásával.

   ![Válassza ki a kulcs oszlopoknak.](media/add-reference-data-set/set_primary_key.png)

   Bejelölheti a  **+**  gombra kattintva adja hozzá egy másodlagos kulcs oszlop egy összetett elsődleges kulcs. Ha a kijelölés visszavonása van szüksége, az üres érték közül választhat a legördülő lista távolítsa el a másodlagos kulcsot.

15.  Töltse fel az adatokat, válassza ki a **sor feltöltése** gombra.

   ![Feltöltés](media/add-reference-data-set/upload_rows.png)

   Az oldal megerősíti, hogy a kész feltöltése és az üzenet megjelenítéséhez **sikeresen feltöltve a dataset**.

## <a name="next-steps"></a>További lépések
* [Referencia-adatok kezelése](time-series-insights-manage-reference-data-csharp.md) programozott módon.
* Az alkalmazásprogramozási felület (API) teljes leírását a [Referencia-adatok API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) dokumentum tartalmazza.
