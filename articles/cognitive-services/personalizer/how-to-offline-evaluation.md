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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a8a75601daf36ca21ea56a5930219d7d467f0c85
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69557817"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Tanulási hurok elemzése offline kiértékeléssel


Ismerje meg, hogyan végezheti el az offline kiértékelést, és megismerheti az eredményeket.

Az offline értékelések segítségével mérhetővé válik, hogy az alkalmazás alapértelmezett viselkedéséhez képest mennyire hatékony a személyre szabott funkció, hogy milyen funkciók járulnak hozzá leginkább a személyre szabáshoz, és automatikusan Fedezze fel az új gépi tanulási beállításokat.

További információért olvassa el a [kapcsolat nélküli értékeléseket](concepts-offline-evaluation.md) ismertető témakört.


## <a name="prerequisites"></a>Előfeltételek

1. Meg kell adni egy személyre szabott hurok konfigurálását
1. A személyre szabott huroknak reprezentatív mennyiségű adatnak kell lennie, de a naplókban legalább 50 000 eseményt recommmend az értelmes értékelés eredményeire.

Előfordulhat, hogy korábban már exportálta a _tanulási szabályzatok_ fájljait, amelyeket összevetheti és tesztelheti ugyanabban az értékelésben.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Új offline értékelés indításához szükséges lépések

1. Keresse meg a személyre szabási hurok erőforrását a Azure Portal.
1. Navigáljon a "kiértékelés" szakaszhoz.
1. Kattintson az új próbaverzióra
1. Válassza ki az offline értékelés kezdési és befejezési dátumát. Ezek a múltbeli dátumok, amelyek meghatározzák a kiértékelésben használandó adatok tartományát. Ezeknek az adatoknak szerepelniük kell a naplókban az adatmegőrzési beállításban megadott módon. [](how-to-settings.md)
1. Igény szerint feltöltheti saját képzési szabályzatát. 
1. Itt adhatja meg, hogy a testre szabott tanulási szabályzatot az adott időszakban megfigyelt felhasználói viselkedés alapján kell-e létrehozni.
1. A kiértékelés elindítása

## <a name="results"></a>Results (Eredmények)

Az értékelések hosszú időt vehetnek igénybe a feldolgozandó adatmennyiségtől, az összehasonlítani kívánt tanulási szabályzatok számától és a kért optimalizálástól függően.

Ha elkészült, a következő eredményeket láthatja:

1. A tanulási szabályzatok összehasonlítása, beleértve a következőket:
    * **Online házirend**: A személyre szabott alkalmazásban használt aktuális tanulási szabályzat
    * **Alapterv**: Az alkalmazás alapértelmezett értéke (amelyet a Rank-hívásokban elindított első művelet határoz meg),
    * **Véletlenszerű házirend**: Egy képzeletbeli rangsorolási viselkedés, amely mindig véletlenszerű választ ad vissza a megadott műveletek közül.
    * **Egyéni szabályzatok**: A próbaverzió indításakor feltöltött további képzési szabályzatok.
    * **Optimalizált szabályzat**: Ha a kiértékelést az optimalizált szabályzat felderítésére szolgáló lehetőséggel indította el, azt a rendszer összehasonlítja, és letöltheti, vagy elvégezheti az online tanulási szabályzatot, amely az aktuálisat váltja fel.

1. A műveletek [](concepts-features.md) és a környezet funkcióinak hatékonysága.


## <a name="more-information"></a>További információ

* Ismerje meg, [hogyan működnek az offline értékelések](concepts-offline-evaluation.md).
