---
title: Referenciaadatkészletek hozzáadása a környezethez – Azure Time Series Insights | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan adhat hozzá egy referencia-adatkészletet az Azure Time Series Insights-környezetben az adatok bővítéséhez.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18~~~~
ms.openlocfilehash: 79628ed44753577023464ef6208027e1b7996d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087246"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Hozzon létre egy referencia-adatkészletet a Time Series Insights-környezethez az Azure Portal használatával

Ez a cikk ismerteti, hogyan adhat hozzá egy referencia-adatkészletet az Azure Time Series Insights-környezetben. A referenciaadatok hasznosak a forrásadatokhoz való illesztéshez az értékek növelése érdekében.

A referencia-adatkészlet olyan elemek gyűjteménye, amelyek kiegészítik az eseményforrásból származó eseményeket. A Time Series Insights bejövő elemzések motorja az eseményforrásminden eseményét a referencia-adatkészlet megfelelő adatsorával egyesíti. Ez a kibővített esemény ezután lekérdezhető. Ez az illesztés a referencia-adatkészletben definiált elsődleges kulcs oszlop(ok)on alapul.

A referenciaadatok visszamenőleges en nem egyesülnek. Így csak a jelenlegi és a jövőbeli belső kapcsolat adatai egyeztetve vannak, és csatlakozott a referencia-dátum készlet, miután konfiguráltése és feltöltése.

## <a name="video"></a>Videó

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Ismerje meg a Time Series Insight referenciaadat-modelljét.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Referencia-adatkészlet hozzáadása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Keresse meg a meglévő Azure Time Series Insights-környezetben. Válassza az **Összes erőforrás az** Azure Portal bal oldalán található menüben. Válassza ki az Azure Time Series Insights-környezetet.

1. Válassza az **Áttekintés** lapot. Bontsa ki az **Essentials szakaszt** a lap tetején, és keresse meg a **Time Series Insights-kezelő URL-címét,** és nyissa meg a hivatkozást.  

   [![Essentials szakasz kibontása](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Tekintse meg a Time Series Insights környezet kezelőjének megtekintését.

1. Bontsa ki a környezetválasztót a Time Series Insights-kezelőben. Válassza ki az aktív környezetet. Válassza a referenciaadatok ikonját a felfedező lap jobb felső részén.

   [![Hivatkozási adatok hozzáadása](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Új **adatkészlet** hozzáadásának megkezdéséhez válassza a + Adatkészlet hozzáadása gombot.

   [![Adatkészlet hozzáadása](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Az **Új referencia-adatkészlet** lapon válassza ki az adatok formátumát:

   - Válassza a **CSV-t** a vesszővel tagolt adatokhoz. Az első sort a kezelés fejlécsorként kezeli.
   - Válassza a **JSON tömb** javascript objektumjelölés (JSON) formátumú adatait.

   [![Válassza ki az adatformátumot.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Adja meg az adatokat a következő két módszer egyikével:

   - Illessze be az adatokat a szövegszerkesztőbe. Ezután válassza **a Hivatkozási adatok kiválasztása** gombot.
   - A Helyi szövegfájlból származó adatok hozzáadásához válassza a **Fájl kiválasztása** gombot.

   Például CSV-adatok beillesztése: [ ![Beillesztett CSV-adatok](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Például jsontömbadatok beillesztése: [ ![JSON-adatok beillesztése](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Ha hiba történik az adatértékek elemzésével kapcsolatban, a hiba pirosan jelenik meg az oldal alján, például `CSV parsing error, no rows extracted`.

1. Az adatok sikeres elemzése után egy adatrács jelenik meg, amely az adatokat jelölő oszlopokat és sorokat jeleníti meg. A helyesség érdekében tekintse át az adatrácsot.

   [![Referenciaadatok áttekintése](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Tekintse át az egyes oszlopokat a feltételezett adattípus megértéséhez, és szükség esetén módosítsa az adattípust.  Válassza ki az adattípus szimbólumot az oszlopfejlécben: **#** dupla (numerikus adatok), **T| F** a logikai érték, vagy **az Abc** a karakterlánc.

   [![Válassza ki az adattípusokat az oszlopfejléceken.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Szükség esetén nevezze át az oszlopfejléceket. A kulcsoszlop neve szükséges, hogy csatlakozzon a megfelelő tulajdonság az eseményforrásban. 

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a referencia-adatkulcs oszlopnevei pontosan megegyeznek az esemény nevével a bejövő adatokkal, beleértve a kis- és nagybetűk megkülönböztetését is. A nem kulcsoszlopnevek a bejövő adatok és a megfelelő referenciaértékek kiegészítésére szolgálnak.

1. Írjon be egy értéket **a Sorok szűrése...** mezőbe, hogy szükség szerint áttekintsen bizonyos sorokat. A szűrő az adatok ellenőrzéséhez hasznos, de az adatok feltöltésekor nem kerül alkalmazásra.

1. Nevezze el az adatkészletet az adatrács feletti **Adatkészletnév** mező kitöltésével.

    [![Nevezze el az adatkészletet.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Adja meg az **elsődleges kulcs** oszlopot az adatkészletben az adatrács feletti legördülő menü kiválasztásával.

    [![Jelölje ki a kulcsoszlop(oka)t.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Nem kötelező)** Válassza **+** ki a gombot egy másodlagos kulcsoszlop összetett elsődleges kulcsként való hozzáadásához. Ha vissza kell vonnia a kijelölést, a másodlagos kulcs eltávolításához válassza ki az üres értéket a legördülő menüből.

1. Az adatok feltöltéséhez jelölje ki a **Sorok feltöltése** gombot.

    [![Sorok feltöltése és adatok megerősítése.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    A lap megerősíti a befejezett feltöltést, és megjeleníti a **sikeresen feltöltött adatkészletet.**

    > [!WARNING]
    > A referenciaadatkészletek között megosztott oszlopok vagy tulajdonságok **ismétlődő tulajdonságnév-feltöltési** hibát jelenítmeg. A hiba nem akadályozza meg a referenciaadatkészletek sikeres feltöltését. A duplikált tulajdonságnevet megosztó sorok kombinálásával távolítható el.

1. Válassza **a Sor hozzáadása**, Tömeges **importálási sorok**vagy Oszlop **hozzáadása** lehetőséget további referenciaadat-értékek hozzáadásához.

    [![Adjon hozzá egy sort, tömeges importálási sorokat vagy oszlop hozzáadása.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Minden olyan sor, amely egy másik sorral osztozik egy egyedi kulcson, oszlopait felülírja az utolsó hozzáadott sor, amely megosztja az egyedi kulcsot.

   > [!NOTE]
   > A hozzáadott soroknak **nem** kell *téglalap alakúaknak* lenniük - előfordulhat, hogy kevesebb, nagyobb vagy változó oszlopuk van a referencia-adatkészlet többi bejegyzésééből.

## <a name="next-steps"></a>További lépések

* [Referencia-adatok kezelése](time-series-insights-manage-reference-data-csharp.md) programozott módon.

* A teljes API-referencia, olvassa el [a Referenciaadatok API-dokumentum.](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)
