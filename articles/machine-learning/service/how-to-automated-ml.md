---
title: Mi az automatikus Machine Learning – az Azure Machine Learning szolgáltatás
description: Ismerje meg hogyan Azure Machine Learning szolgáltatás automatikusan algoritmus kiválasztása az Ön számára, és hozzon létre egy modellt, hogy mentse a paramétereket, és az Ön által megadott feltételeknek, válassza ki a legjobb algoritmus a modell használatával ideje.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 4b9356c7cba14cf5bd112c7d7ae1aab2842fb9d1
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53010892"
---
# <a name="what-is-automated-machine-learning"></a>Mi a machine learning automatikus?

Ebben a cikkben megismerheti az automatikus gépi tanulás. Az Azure Machine Learning szolgáltatás automatikusan algoritmus kiválasztása az Ön számára, és hozzon létre egy modellt. A célok és a kísérlethez, például az idő a kísérlet futtatásához definiált megkötések lehetőségeire modellek, vagy amely tiltólistára kell helyezni a modellek létrehozásával időt automatizált machine learning-menti.

## <a name="how-it-works"></a>Működés

1. Konfigurálja a machine learning problémát megoldani kívánt típusát. Felügyelt tanítás kategóriáit támogatottak:
   + Besorolás
   + Regresszió
   + Előrejelzések 

   Automatizált a machine learning szolgáltatás általánosan elérhető, amíg **az előrejelzési funkció nyilvános előzetes verzióként.**

   Tekintse meg a [modellek listájának](how-to-configure-auto-train.md#select-your-experiment-type) Azure Machine Learning betanításakor próbálja meg.

1. Megadhatja, hogy a forrás- és a betanítási adatok formátumát. Az adatok címkével kell lennie, és a fejlesztői környezetben vagy Azure Blob Storage-ban tárolhatók. Ha az adatok tárolása a fejlesztési környezetet, a betanítási szkriptekhez ugyanabban a címtárban kell lennie. Ez a könyvtár a számítási célnak választja, a betanításhoz másolódik.

    A tanítási szkriptet az adatok Numpy tömbök vagy Pandas dataframe is olvasható.

    Képzés és az érvényesítési adatok kiválasztásának split beállítások konfigurálhatók, vagy külön képzés és érvényesítési adatkészleteket is megadhat.

1. Konfigurálja a [számítási célt](how-to-set-up-training-targets.md) használt betanítja a modellt.

1. Konfigurálja az automatikus machine learning configuration. Ez vezérli a paramétereket, az Azure Machine Learning ismétel különböző modell, hiperparaméter beállításait, és hogy milyen metrikákat, és mikor tekintse meg, amely meghatározza a legjobb modellt. 

1. Küldje el a betanítási Futtatás.

Betanítás, során az Azure Machine Learning szolgáltatás számos különböző algoritmusok és paraméterek próbálja folyamatok hoz létre. Miután megadta az iteráció korlátot elér, vagy ha eléri a metrika azt adja meg a célérték megszűnik.

[ ![Automatizált Machine learning](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

A naplózott futtatási információk, amely tartalmazza a futtatás során gyűjtött metrikák vizsgálhatja meg. A betanítási Futtatás is hoz létre egy Python-szerializált objektumot (.pkl fájlt), amely tartalmazza a modell és az adatok előfeldolgozása.

## <a name="next-steps"></a>További lépések

Példák, és ismerje meg, hogyan hozhat létre automatizált Machine Learning használatával:

+ [Oktatóanyag: Automatikusan az Azure automatikus Machine Learning egy osztályozási modell betanítása](tutorial-auto-train-models.md)

+ [Automatikus képzéshez beállításainak konfigurálása](how-to-configure-auto-train.md)

+ [A távoli erőforrás automatikus képzési használata](how-to-auto-train-remote.md) 
