---
title: Hogyan kell elvégezni az offline kiértékelést - Personalizer
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan használhatja az offline értékelést az alkalmazás hatékonyságának mérésére és a tanulási hurok elemzésére.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: diberry
ms.openlocfilehash: ce85c2d264b2b4849a4a36ed757150292fdf39f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622790"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>A tanulási ciklus elemzése offline értékeléssel

További információ az offline kiértékelés elvégzéséről és az eredmények megértéséről.

Az offline kiértékelések lehetővé teszik annak mérését, hogy a Personalizer mennyire hatékony az alkalmazás alapértelmezett viselkedéséhez képest, megtudhatja, hogy milyen funkciók járulnak hozzá leginkább a személyre szabáshoz, és automatikusan felfedezheti az új gépi tanulási értékeket.

További információ [az offline értékelésekről.](concepts-offline-evaluation.md)

## <a name="prerequisites"></a>Előfeltételek

* Konfigurált Personalizer hurok
* A Personalizer huroknak reprezentatív mennyiségű adatkal kell rendelkeznie - stadionként legalább 50 000 eseményt ajánlunk naplóiban az érdemi értékelési eredmények érdekében. Opcionálisan előfordulhat, hogy korábban exportált _tanulási szabályzatfájlokat_ is összehasonlíthat és tesztelhet ugyanabban a kiértékelésben.

## <a name="run-an-offline-evaluation"></a>Kapcsolat nélküli kiértékelés futtatása

1. Az [Azure Portalon](https://azure.microsoft.com/free/)keresse meg a Personalizer erőforrást.
1. Az Azure Portalon nyissa meg a **Kiértékelések szakaszt,** és válassza **a Kiértékelés létrehozása lehetőséget.**
    ![Az Azure Portalon nyissa meg a **Kiértékelések** szakaszt, és válassza a **Kiértékelés létrehozása**.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Állítsa be a következő értékeket:

    * Egy kiértékelési név.
    * Kezdő és záró dátum – ezek olyan dátumok, amelyek meghatározzák a kiértékelésben használandó adattartományt. Ezeknek az adatoknak meg kell jelen lenniük a naplókban, az [adatmegőrzési](how-to-settings.md) értékben megadottak szerint.
    * Optimalizálás Discovery meg **igen**.

    > [!div class="mx-imgBorder"]
    > ![Kapcsolat nélküli kiértékelési beállítások megadása](./media/offline-evaluation/create-an-evaluation-form.png)

1. Indítsa el az Értékelés gombot az **Ok**lehetőség kiválasztásával.

## <a name="review-the-evaluation-results"></a>Az értékelés eredményeinek áttekintése

Az értékelések futtatása hosszú időt vehet igénybe, a feldolgozandó adatok mennyiségétől, az összehasonlítandó tanulási szabályzatok számától és attól függően, hogy szükség van-e optimalizálásra.

Miután elkészült, kiválaszthatja az értékelést az értékelések listájából, majd válassza **az Alkalmazás pontszámának összehasonlítása más potenciális tanulási beállításokkal**lehetőséget. Akkor válassza ezt a funkciót, ha meg szeretné tekinteni, hogyan teljesít az aktuális tanulási szabályzat az új szabályzathoz képest.

1. Tekintse át a [tanulási irányelvek](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy)teljesítményét.

    > [!div class="mx-imgBorder"]
    > [![Értékelés eredményeinek áttekintése](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Válassza az **Alkalmaz** lehetőséget, ha azt a házirendet szeretné alkalmazni, amely az adatoknak leginkább a modellnek a legmegfelelőbb.

## <a name="next-steps"></a>További lépések

* További információ az [offline értékelések működéséről.](concepts-offline-evaluation.md)
