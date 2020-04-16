---
title: Gépi tanulási algoritmus Cheat Sheet - tervező
titleSuffix: Azure Machine Learning
description: A nyomtatható Machine Learning algoritmus Cheat Sheet segítségével kiválaszthatja a megfelelő algoritmust a prediktív modell az Azure Machine Learning designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
ms.openlocfilehash: accc4766f25f5d58ba2f31c153fe1c5d23413170
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399085"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Gépi tanulási algoritmus cheat sheet az Azure Machine Learning tervező

Az **Azure Machine Learning algoritmus cheat sheet** segítségével kiválaszthatja a megfelelő algoritmust a tervező egy prediktív elemzési modell.

Az Azure Machine Learning a ***besorolási***, ***ajánlói rendszerekből,*** ***fürtözésből,*** ***anomáliadetektálásból,*** ***regresszióból***és ***szövegelemzési*** családokból származó algoritmusok nagy könyvtárával rendelkezik. Mindegyik egy másik típusú gépi tanulási probléma megoldására szolgál.

További útmutatásért lásd: [Algoritmusok kiválasztása](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Letöltés: Gépi tanulási algoritmus Cheat Sheet

**Töltse le a csaló lapot itt: [Machine Learning Algoritmus Cheat Sheet (11x17 in.)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Machine Learning algoritmus cheat sheet: Megtanulják, hogyan kell kiválasztani a Machine Learning algoritmus.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

Töltse le és nyomtassa ki a Machine Learning Algoritmus Cheat Sheet a bulvár méretben, hogy tartsa kéznél, és segítséget válasszon egy algoritmust.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Hogyan kell használni a Machine Learning algoritmus Cheat Sheet

A javaslatok kínált ebben az algoritmus cheat sheet hozzávetőleges szabályok-of-thumb. Néhány lehet hajlított, és néhány is kirívóan megsértik. Ez a csaláslap kiindulópontot javasol. Ne féljen fej-fej melletti versenyt futtatni az adatokon lévő számos algoritmus között. Egyszerűen nem helyettesítheti az egyes algoritmusok és az adatokat generáló rendszer alapelveinek megértését.

Minden gépi tanulási algoritmusnak saját stílusa vagy induktív elfogultsága van. Egy adott probléma esetén több algoritmus is megfelelő lehet, és egy algoritmus jobban illeszkedhet, mint mások. De ez nem mindig lehet tudni előre, melyik a legjobb illeszkedést. Az ilyen esetekben számos algoritmus szerepel együtt a csalólapon. A megfelelő stratégia az lenne, hogy megpróbál egy algoritmust, és ha az eredmények még nem kielégítő, próbálja meg a többit. 

Ha többet szeretne megtudni az Azure Machine Learning tervezőjének algoritmusairól, nyissa meg az [Algoritmus és a modulhivatkozás című részt.](algorithm-module-reference/module-reference.md)

## <a name="kinds-of-machine-learning"></a>Gépi tanulás típusai

A gépi tanulásnak három fő kategóriája van: *felügyelt tanulás*, felügyelet *nélküli tanulás*és *megerősítés .*

### <a name="supervised-learning"></a>Felügyelt tanulás

A felügyelt tanulásban minden adatpont címkével van ellátva, vagy egy érdeklődési körhöz vagy értékhez van társítva. Egy kategorikus címke például a kép "macska" vagy "kutya" ként való hozzárendelése. Az értékcímkére példa a használt autóhoz kapcsolódó eladási ár. A felügyelt tanulás célja, hogy számos ilyen címkével ellátott példát tanulmányozzon, majd előrejelzéseket készítsen a jövőbeli adatpontokról. Például az új fényképek azonosítása a megfelelő állattal, vagy pontos eladási árak hozzárendelése más használt autókhoz. Ez egy népszerű és hasznos típusú gépi tanulás.

### <a name="unsupervised-learning"></a>Felügyelet nélküli tanulás

Felügyelet nélküli tanulás, adatpontok nincs címkék társítva. Ehelyett a felügyelet nélküli tanulási algoritmus célja az adatok valamilyen módon való rendszerezése vagy a szerkezet leírása. Felügyelet nélküli tanulási csoportok adatok fürtökbe, mint a K-means nem, vagy megtalálja a különböző módon nézett összetett adatokat, hogy egyszerűbbnek tűnjön.

### <a name="reinforcement-learning"></a>Megerősítő tanulás

A megerősítés tanulás, az algoritmus kap, hogy válasszon egy műveletet válaszul minden adatpont. Ez egy közös megközelítés a robotika, ahol a készlet érzékelő mért egy adott időpontban egy adatpont, és az algoritmus kell választani a robot következő művelet. Ez is egy természetes illeszkedés ta- A tanulási algoritmus is kap egy jutalom jelet nem sokkal később, jelezve, hogy milyen jó volt a döntés. Ennek alapján a jel, az algoritmus módosítja a stratégiát annak érdekében, hogy elérjék a legmagasabb jutalmat. 

## <a name="next-steps"></a>További lépések

* További útmutatás az [algoritmusok kiválasztásához](how-to-select-algorithms.md)

* [Ismerje meg a stúdiót az Azure Machine Learningben és az Azure Portalon.](overview-what-is-azure-ml.md)

* [Oktatóanyag: Előrejelzési modell létrehozása az Azure Machine Learning tervezőjében.](tutorial-designer-automobile-price-train-score.md)

* [Ismerje meg a mélytanulást és a gépi tanulást.](concept-deep-learning-vs-machine-learning.md)
