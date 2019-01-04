---
title: Automatizált gépi Tanulási algoritmus kiválasztása és hangolás
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan Azure Machine Learning szolgáltatás automatikusan algoritmus kiválasztása az Ön számára, és hozzon létre egy modellt, hogy időt takaríthat meg a paramétereket, és az Ön által megadott feltételeknek, a legjobb algoritmust határozza meg a modellhez.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 9e3297b2493ea12f9da50556e4fc9e72d625fd25
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583458"
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

Az automatikus machine learning egy közös adatelemekkel tekintse meg a teljes körű folyamatot, hogy nem tud. Az Azure Machine Learning lehetővé teszi a modellek átlátható képet adnak a háttér-futó növelése kapcsolatos részletes információk megtekintése. Egyes modellek, például lineáris regresszió, viszonylag egyértelmű, ezért könnyen érthető lehet minősülnek. Azonban, hogy a további funkciók hozzáadása, és használja a bonyolultabb gépi tanulási modelleket, nehezebb azok ismertetése beolvasása. Két fontos szempontot jegyében a machine Learning szolgáltatásban:

1. Ismertségét, a gépi tanulási folyamat és az összes lépést vesz részt, beleértve az adatok előfeldolgozása/featurization, és a hiperparaméter értékeket.
1. Tudnivalók a bemeneti változók (más néven "szolgáltatások") és a modell kimeneti közötti kapcsolat.  A magnitude és az ütközésnek az előre jelzett érték az egyes szolgáltatások ismerete segít jobb megismerésében és a modell ismertetik. Ez az úgynevezett funkció fontosságát.

Engedélyezheti a globális szolgáltatás fontosság post igény szerinti képzés a folyamat a kiválasztott, vagy automatikus ML képzési részét képező összes folyamatok esetében engedélyezi azt.  Ez az előzetes verziójú funkció, és továbbra is be jobb megértése a Machine Learning-modellek részletesebb információkat biztosít.  

Kövesse ezt [minta notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/model-explanation/auto-ml-model-explanation.ipynb) modell találja az Azure Machine Learning kísérletezhet.

## <a name="next-steps"></a>További lépések

Példák, és ismerje meg, hogyan hozhat létre automatizált Machine Learning használatával:
+ [Példák: A Jupyter notebookok használata az Azure Machine Learning szolgáltatás megismeréséhez](samples-notebooks.md#automated-ml-setup)

+ [Oktatóanyag: Az Azure automatikus Machine Learning egy osztályozási modell automatikusan betanítása](tutorial-auto-train-models.md)

+ [A távoli erőforrás automatikus képzési használata](how-to-auto-train-remote.md)

+ [Automatikus képzési beállításainak konfigurálása](how-to-configure-auto-train.md)
