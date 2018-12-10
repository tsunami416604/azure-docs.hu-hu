---
title: Mi a Machine Learning automatikus
titleSuffix: Azure Machine Learning service
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
ms.openlocfilehash: 8c1641c975ff235b581f784f0965caf203a9562f
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53142533"
---
# <a name="what-is-automated-machine-learning"></a>Mi a machine learning automatikus?

Automatizált machine learning szolgáltatás véve a betanítási adatok egy meghatározott célhoz szolgáltatással, és iterálás algoritmusok és a szolgáltatások kiválasztása ki automatikusan a legoptimálisabb modellt, a képzési pontszámok alapján az adatok kombinációját. A hagyományos gépi tanulási modell fejlesztési folyamatok magas erőforrás-igényes, és jelentős tartomány ismeretek és idő befektetési futtatásához, és több tucatnyi modellek eredményeinek összehasonlítására, igényel. Automatizált machine learning leegyszerűsíti ezt a folyamatot a célokat és a kísérlethez, például az idő a kísérlet futtatásához definiált megkötések lehetőségeire modellek, vagy amely tiltólistára kell helyezni a modellek létrehozásával.

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

1. Konfigurálja az automatikus machine learning configuration. Ez vezérli a paramétereket, az Azure Machine Learning ismétel különböző modell, hiperparaméter beállításait, és hogy milyen metrikákat, és mikor tekintse meg, amely meghatározza a legjobb modellt 

1. Küldje el a betanítási Futtatás.

Betanítás, során az Azure Machine Learning szolgáltatás számos különböző algoritmusok és paraméterek próbálja folyamatok hoz létre. Miután megadta az iteráció korlátot elér, vagy ha eléri a metrika azt adja meg a célérték megszűnik.

[ ![Automatizált Machine learning](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

A naplózott futtatási információk, amely tartalmazza a futtatás során gyűjtött metrikák vizsgálhatja meg. A betanítási Futtatás is hoz létre egy Python-szerializált objektumot (`.pkl` fájl), amely tartalmazza a modell és az adatok előfeldolgozása.

## <a name="model-explainability"></a>Modell explainability

Az automatikus machine learning egy közös adatelemekkel tekintse meg a teljes körű folyamatot, hogy nem tud. Az Azure Machine Learning lehetővé teszi a modellek átlátható képet adnak a háttér-futó növelése kapcsolatos részletes információk megtekintése. Megjelenítheti fontosság. a szolgáltatás általános. a modell finomhangolása, rangsorolás az eredményeket a szolgáltatások által befolyásolt a modell a legtöbb. Ezenkívül osztályozási problémák láthatja az osztály funkció fontosság és ennek a területnek.

## <a name="next-steps"></a>További lépések

Példák, és ismerje meg, hogyan hozhat létre automatizált Machine Learning használatával:

+ [Oktatóanyag: Automatikusan az Azure automatikus Machine Learning egy osztályozási modell betanítása](tutorial-auto-train-models.md)

+ [Automatikus képzési beállításainak konfigurálása](how-to-configure-auto-train.md)

+ [A távoli erőforrás automatikus képzési használata](how-to-auto-train-remote.md) 
