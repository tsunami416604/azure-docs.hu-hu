---
title: Offline értékelés – személyre szabás
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan elemezheti tanulási ciklusát offline kiértékeléssel
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: diberry
ms.openlocfilehash: a157b36ad2b4f5a7f2e50a9609d8acd621efd5a8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884487"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>A tanulási hurok elemzése offline kiértékeléssel

Ismerje meg, hogyan végezheti el az offline kiértékelést, és megismerheti az eredményeket.

Az offline értékelések segítségével mérhetővé válik, hogy az alkalmazás alapértelmezett viselkedéséhez képest mennyire hatékony a személyre szabott funkció, hogy milyen funkciók járulnak hozzá leginkább a személyre szabáshoz, és automatikusan Fedezze fel az új gépi tanulási beállításokat.

További információért olvassa el a [kapcsolat nélküli értékeléseket](concepts-offline-evaluation.md) ismertető témakört.


## <a name="prerequisites"></a>Előfeltételek

* Konfigurált személyre szabott hurok
* A személyre szabott huroknak reprezentatív mennyiségű adatnak kell lennie, és a naplókban legalább 50 000 eseményt javasolt kiértékelési eredményekre. Előfordulhat, hogy korábban már exportálta a _tanulási szabályzatok_ fájljait, amelyeket összevetheti és tesztelheti ugyanabban az értékelésben.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Új offline értékelés indításához szükséges lépések

1. A [Azure Portal](https://azure.microsoft.com/free/)keresse meg a személyre szabott erőforrást.
1. A Azure Portal lépjen az **értékelések** szakaszra, és válassza az **Értékelés létrehozása**elemet.
    ![A Azure Portal nyissa meg a * * értékelések * * szakaszt, és válassza a * * Create reértékelés * * elemet.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Válassza ki a következő beállításokat:

    * Értékelés neve
    * Kezdési és befejezési dátum – ezek a múltbeli dátumok, amelyek meghatározzák a kiértékelésben használandó adattartományt. Ezeknek az adatoknak szerepelniük kell a naplókban az [adatmegőrzési](how-to-settings.md) beállításban megadott módon.
    * Az optimalizálási felderítés beállítása **Igen**

    ![Offline kiértékelési beállítások kiválasztása](./media/offline-evaluation/create-an-evaluation-form.png)

1. A kiértékelés elindításához kattintson **az OK gombra**. 

## <a name="results"></a>Results (Eredmények)

Az értékelések hosszú időt vehetnek igénybe a feldolgozandó adatmennyiségtől, az összehasonlítani kívánt tanulási szabályzatok számától és a kért optimalizálástól függően.

Ha elkészült, kiválaszthatja a kiértékelést az értékelések listájában. 

A tanulási szabályzatok összehasonlítása a következőket foglalja magában:

* **Online házirend**: A személyre szabott alkalmazásban használt aktuális tanulási szabályzat
* **Alapterv**: Az alkalmazás alapértelmezett értéke (amelyet a Rank-hívásokban elindított első művelet határoz meg),
* **Véletlenszerű házirend**: Egy képzeletbeli rangsorolási viselkedés, amely mindig véletlenszerű választ ad vissza a megadott műveletek közül.
* **Egyéni szabályzatok**: A próbaverzió indításakor feltöltött további képzési szabályzatok.
* **Optimalizált szabályzat**: Ha a kiértékelést az optimalizált szabályzat felderítésére szolgáló lehetőséggel indította el, azt a rendszer összehasonlítja, és letöltheti, vagy elvégezheti az online tanulási szabályzatot, amely az aktuálisat váltja fel.

![Offline kiértékelési beállítások eredményeinek diagramja](./media/offline-evaluation/evaluation-results.png)

A műveletek és a környezet [funkcióinak](concepts-features.md) hatékonysága.

## <a name="next-steps"></a>További lépések

* Ismerje meg, [hogyan működnek az offline értékelések](concepts-offline-evaluation.md).
