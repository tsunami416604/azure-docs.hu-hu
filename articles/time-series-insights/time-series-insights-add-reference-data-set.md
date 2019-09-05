---
title: Hivatkozási adathalmaz hozzáadása a Azure Time Series Insights-környezethez | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan adhat hozzá egy hivatkozási adatkészletet a Azure Time Series Insights-környezetében lévő adatbővítéshez.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: 5b48a41f025ef06c69e6de126e0a64ad359ce09a
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666367"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Hozzon létre egy hivatkozási adatkészletet a Time Series Insights-környezethez a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan adhat hozzá egy hivatkozási adatkészletet a Azure Time Series Insights-környezethez. A hivatkozási adatok hasznosak a forrásadatok összekapcsolásához az értékek kibővítéséhez.

A hivatkozási adatkészletek olyan elemek gyűjteményei, amelyek kibővítik az esemény forrásának eseményeit. Time Series Insights beáramlási motor az eseményforrás minden eseményét összekapcsolja a hivatkozási adathalmaz megfelelő adatsorával. Ez a kibővített esemény ezután lekérdezhető. Ez a csatlakozás a hivatkozási adatkészletben definiált elsődleges kulcs oszlop (ok) alapján történik.

A hivatkozási adathalmazok nem csatlakoznak visszamenőlegesen. Így a rendszer csak a jelenlegi és a jövőbeli bejövő adatok összeegyeztetését és a hivatkozási dátumhoz való csatlakozást követően csatlakozik, miután konfigurálta és feltöltötte.

## <a name="video"></a>Videó

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Tudnivalók a Time Series Insight hivatkozási adatmodelljéről.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Hivatkozási adathalmaz hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a meglévő Time Series Insights-környezetbe. A Azure Portal bal oldalán található menüben válassza az **összes erőforrás** lehetőséget. Válassza ki az Azure Time Series Insights-környezetet.

1. Válassza az **Áttekintés** lapot. Keresse meg a **Time Series Insights Explorer URL-címét** , és nyissa meg a hivatkozást.  

   Tekintse meg az ÁME-környezet explorerjét.

1. Bontsa ki a környezeti választót az ÁME Explorerben. Válassza ki az aktív környezetet. Kattintson a jobb felső sarokban található hivatkozási adatikonra az Explorer oldalon.

   [![Hivatkozási érték hozzáadása](media/add-reference-data-set/add-reference-data.png)](media/add-reference-data-set/add-reference-data.png#lightbox)

1. Az új adatkészlet hozzáadásának megkezdéséhez kattintson az **+ adatkészlet hozzáadása** gombra.

   [![Adathalmaz hozzáadása](media/add-reference-data-set/add-data-set.png)](media/add-reference-data-set/add-data-set.png#lightbox)

1. Az **új hivatkozási adatkészlet** lapon válassza ki az adatformátumot:
   - Vesszővel tagolt adatmennyiség esetén válassza a **CSV** lehetőséget. Az első sor fejléc sorként lesz kezelve.
   - Válassza a **JSON** -tömböt a JavaScript-objektumok jelölésére formázott adathoz.

   [![Válassza az adatformátum lehetőséget.](media/add-reference-data-set/add-data.png)](media/add-reference-data-set/add-data.png#lightbox)

1. Adja meg az adatmennyiséget a két módszer egyikének használatával:
   - Illessze be az adatfájlokat a szövegszerkesztőbe. Ezután válassza a **hivatkozási adatelemzés** gomb lehetőséget.
   - Kattintson a **Fájl választása** gombra, ha helyi szövegfájlból kívánja felvenni az adatforrást.

   Például illessze be a CSV-fájlokat: [![Beillesztett CSV-fájl](media/add-reference-data-set/csv-data-pasted.png)](media/add-reference-data-set/csv-data-pasted.png#lightbox)

   Például illessze be a JSON-tömböt: [![JSON-adattárolás beillesztése](media/add-reference-data-set/json-data-pasted.png)](media/add-reference-data-set/json-data-pasted.png#lightbox)

   Ha hiba történik az adatértékek elemzésekor, a hiba az oldal alján piros színnel jelenik meg, például `CSV parsing error, no rows extracted`:.

1. Az adatgyűjtés sikeres elemzése után egy adatrács jelenik meg, amely megjeleníti az adatoszlopokat és az azokat jelképező sorokat.  A pontosság érdekében tekintse át az adatrácsot.

   [![Hivatkozási érték hozzáadása](media/add-reference-data-set/parse-data.png)](media/add-reference-data-set/parse-data.png#lightbox)

1. Tekintse át az egyes oszlopokat a feltételezett adattípus megjelenítéséhez, és szükség esetén módosítsa az adattípust.  Válassza ki az adattípus szimbólumát az oszlop fejlécében **#** : dupla (numerikus), **T | F** logikai vagy **ABC** karakterlánchoz.

   [![Válassza az oszlopok fejlécek adattípusok elemét.](media/add-reference-data-set/choose-datatypes.png)](media/add-reference-data-set/choose-datatypes.png#lightbox)

1. Szükség esetén nevezze át az oszlopfejléceket. A kulcs oszlopának neve szükséges az eseményforrás megfelelő tulajdonságához való csatlakozáshoz. Győződjön meg arról, hogy a hivatkozási adatkulcs oszlopainak nevei pontosan egyeznek az esemény nevével a bejövő adataihoz, beleértve a kis-és nagybetűket is. A nem kulcsos oszlopnevek a beérkező adatoknak a megfelelő hivatkozási adatértékekkel való kiegészítésére szolgálnak.

1. Válassza **a sor hozzáadása** vagy az **oszlop hozzáadása** lehetőséget a további hivatkozási adatértékek igény szerinti hozzáadásához.

1. Írjon be egy értéket a **sorok...** mezőre, és szükség szerint tekintse át az adott sorokat. A szűrő hasznos az adatelemzéshez, de a rendszer nem alkalmazza az adatfeltöltés során.

1. Nevezze el az adathalmazt az adatrács fölötti **adatkészlet neve** mező kitöltésével.

    [![Nevezze el az adathalmazt.](media/add-reference-data-set/name-reference-dataset.png)](media/add-reference-data-set/name-reference-dataset.png#lightbox)

1. Az adatrács fölötti legördülő lista kiválasztásával adja meg az **elsődleges kulcs** oszlopát az adatkészletben.

    [![Válassza ki a kulcs oszlop (oka) t.](media/add-reference-data-set/set-primary-key.png)](media/add-reference-data-set/set-primary-key.png#lightbox)

    Kiválaszthatja a **+** gombot egy másodlagos kulcs oszlopának összetett elsődleges kulcsként való hozzáadásához. Ha vissza kell vonnia a kijelölést, válassza ki az üres értéket a legördülő menüből a másodlagos kulcs eltávolításához.

1. Az adatok feltöltéséhez kattintson a **sorok feltöltése** gombra.

    [![Feltöltése](media/add-reference-data-set/upload-rows.png)](media/add-reference-data-set/upload-rows.png#lightbox)

    Az oldal megerősíti a befejezett feltöltést, és megjeleníti az üzenet **sikeresen feltöltött**adatkészletét.

## <a name="next-steps"></a>További lépések

* [Referencia-adatok kezelése](time-series-insights-manage-reference-data-csharp.md) programozott módon.

* Az alkalmazásprogramozási felület (API) teljes leírását a [Referencia-adatok API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) dokumentum tartalmazza.
