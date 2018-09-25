---
title: Mi az automatikus Machine Learning – az Azure Machine Learning
description: Ebben a cikkben megismerheti az automatikus gépi tanulás. Az Azure Machine Learning szolgáltatás automatikusan algoritmus kiválasztása az Ön számára, és hozzon létre egy modellt. Automatikus machine learning segít időt takarít meg a paramétereket, és az Ön által megadott feltételeknek, válassza ki a legjobb algoritmus a modell használatával.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: krishnan
author: krishnaanumalasetty
ms.date: 9/24/2018
ms.openlocfilehash: 2a9c05b68d05102fab80b2aa8fb1c1dad8a367ea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960039"
---
# <a name="what-is-automated-machine-learning"></a>Mi a machine learning automatikus?

Ebben a cikkben megismerheti az automatikus gépi tanulás. Az Azure Machine Learning szolgáltatás automatikusan algoritmus kiválasztása az Ön számára, és hozzon létre egy modellt. Automatikus machine learning segít időt takarít meg a paramétereket, és az Ön által megadott feltételeknek, válassza ki a legjobb algoritmus a modell használatával.

## <a name="how-it-works"></a>Működés

1. Konfigurálja a machine learning problémát megoldani kívánt típusát. Felügyelt tanítás két kategóriába támogatottak:
   + Besorolás
   + Regresszió

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
