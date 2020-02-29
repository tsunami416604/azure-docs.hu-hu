---
title: 'Többosztályos növelt döntési fa: modulok leírása'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a többosztályos növelt döntési famodult a Azure Machine Learning egy osztályozó használatával a címkézett adategységek létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920059"
---
# <a name="multiclass-boosted-decision-tree"></a>Többosztályos súlyozott döntési fa

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal létrehozhat egy gépi tanulási modellt, amely a megnövelt döntési fák algoritmusán alapul.

A megnövelt döntési fa egy olyan együttes tanulási módszer, amelyben a második fa kijavítja az első fa hibáit, a harmadik fa kijavítja az első és a második fa hibáit, és így tovább. Az előrejelzések együttesen a fák együttesén alapulnak.

## <a name="how-to-configure"></a>Konfigurálás 

Ez a modul egy képzetlen besorolási modellt hoz létre. Mivel a besorolás egy felügyelt tanulási módszer, szüksége van egy *címkével ellátott adatkészletre* , amely tartalmazza az összes sor értékét tartalmazó címke oszlopot.

Az ilyen típusú modellt a [Train modellel](././train-model.md)is betaníthatja. 

1.  Adja hozzá a **többosztályos növelt döntési fa** modult a folyamathoz.

1.  Válassza ki, hogyan szeretné képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.

    + **Egyetlen paraméter**: ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek adott készletét argumentumként.
    
    + **Paraméter tartománya**: akkor válassza ezt a lehetőséget, ha nem biztos benne, hogy a legjobb paramétereket szeretné használni, és szeretne futtatni egy paramétert. Válassza ki a megismételni kívánt értékek tartományát, és a [finomhangolási modell hiperparaméterek beállítása](tune-model-hyperparameters.md) az optimális eredményeket eredményező hiperparaméterek beállítása meghatározásához megadott beállítások összes lehetséges kombinációján.  

1. A **kihagyott levelek maximális száma a fa** korlátaiban a bármely fában létrehozható terminál-csomópontok (levelek) maximális száma.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. A **mintavételek minimális száma a levél csomópontjaiban** azt jelzi, hogy hány esetben szükséges a terminál csomópont (levél) létrehozása a fában.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. A **tanulási ráta** meghatározza a lépés méretét a tanulás során. Adjon meg egy 0 és 1 közötti számot.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. A **felépített fák száma** azt jelzi, hogy hány döntési fát kell létrehozni az Ensemble-ban. További döntési fák létrehozásával lehetőség van jobb lefedettségre, de a képzési idő növekedni fog.

1. A **véletlenszerű számú magok** opcionálisan nem negatív egész számot állítanak be a véletlenszerű mag értékeként való használatra. A vetőmagok megadásával biztosítható az azonos adatokkal és paraméterekkel rendelkező futtatások közötti reprodukálhatóság.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Ha az **oktatói módot** **egyetlen paraméterként**állítja be, csatlakoztasson egy címkézett adatkészletet és a [betanítási modell](./train-model.md) modult.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
