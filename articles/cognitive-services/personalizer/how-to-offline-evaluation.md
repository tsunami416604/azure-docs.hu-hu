---
title: Offline próbaverzió végrehajtása – személyre szabás
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan használható az offline értékelés az alkalmazás hatékonyságának méréséhez és a tanulási hurok elemzéséhez.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: c2aec0db2d1f9865188f2749a0eeb765a14d04ed
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73952999"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>A tanulási hurok elemzése offline kiértékeléssel

Ismerje meg, hogyan végezheti el az offline kiértékelést, és megismerheti az eredményeket.

Az offline értékelések segítségével mérhetővé válik, hogy az alkalmazás alapértelmezett viselkedéséhez képest mennyire hatékony a személyre szabott funkció, hogy milyen funkciók járulnak hozzá leginkább a személyre szabáshoz, és automatikusan Fedezze fel az új gépi tanulási értékeket.

További információért olvassa el a [kapcsolat nélküli értékeléseket](concepts-offline-evaluation.md) ismertető témakört.


## <a name="prerequisites"></a>Előfeltételek

* Konfigurált személyre szabott hurok
* A személyre szabott huroknak reprezentatív mennyiségű adatnak kell lennie, és a naplókban legalább 50 000 eseményt javasolt kiértékelési eredményekre. Előfordulhat, hogy korábban már exportálta a _tanulási szabályzatok_ fájljait, amelyeket összevetheti és tesztelheti ugyanabban az értékelésben.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Új offline értékelés indításához szükséges lépések

1. A [Azure Portal](https://azure.microsoft.com/free/)keresse meg a személyre szabott erőforrást.
1. A Azure Portal lépjen az **értékelések** szakaszra, és válassza az **Értékelés létrehozása**elemet.
    ![a Azure Portalban nyissa meg a * * értékelések * * szakaszt, és válassza a * * Értékelés létrehozása * * elemet.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Konfigurálja a következő értékeket:

    * Értékelés neve
    * Kezdési és befejezési dátum – ezek a múltbeli dátumok, amelyek meghatározzák a kiértékelésben használandó adattartományt. Ezeknek az adatoknak szerepelniük kell a naplókban az [adatmegőrzési](how-to-settings.md) értékben megadott módon.
    * Az optimalizálási felderítés beállítása **Igen**

    ![Offline kiértékelési beállítások kiválasztása](./media/offline-evaluation/create-an-evaluation-form.png)

1. A kiértékelés elindításához kattintson **az OK gombra**. 

## <a name="results"></a>Results (Eredmények)

Az értékelések hosszú időt vehetnek igénybe a feldolgozandó adatmennyiségtől, az összehasonlítani kívánt tanulási szabályzatok számától és a kért optimalizálástól függően.

Ha elkészült, kiválaszthatja a kiértékelést az értékelések listájában. 

A tanulási szabályzatok összehasonlítása a következőket foglalja magában:

* **Online házirend**: a személyre szabott, jelenleg használt képzési szabályzat
* **Alapkonfiguráció: az**alkalmazás alapértelmezett értéke (a Rank-hívásokban elindított első művelet határozza meg),
* **Véletlenszerű házirend**: olyan képzeletbeli rangsorolási viselkedés, amely mindig véletlenszerű választ ad vissza a megadott műveletek közül.
* **Egyéni szabályzatok**: a próbaverzió indításakor feltöltött további képzési szabályzatok.
* **Optimalizált házirend**: Ha a kiértékelést az optimalizált szabályzat felderítésére szolgáló lehetőséggel indította el, a rendszer összehasonlítja azt is, és letöltheti vagy elvégezheti az online tanulási szabályzatot, amely az aktuálisat váltja fel.

![Offline kiértékelési beállítások eredményeinek diagramja](./media/offline-evaluation/evaluation-results.png)

A műveletek és a környezet [funkcióinak](concepts-features.md) hatékonysága.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, [hogyan működnek az offline értékelések](concepts-offline-evaluation.md).
