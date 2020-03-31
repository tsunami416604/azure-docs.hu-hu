---
title: 'Többosztályos súlyozott döntési fa: modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a többosztályos súlyozott döntési fa modult az Azure Machine Learningben egy osztályozó létrehozásához címkézett adatok használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920059"
---
# <a name="multiclass-boosted-decision-tree"></a>Többosztályos súlyozott döntési fa

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal hozzon létre egy gépi tanulási modell, amely a kiemelt döntési fák algoritmus on alapul.

A kiemelt döntési fa egy együttes tanulási módszer, amelyben a második fa kijavítja az első fa hibáit, a harmadik fa kijavítja az első és a második fa hibáit, és így tovább. Az előrejelzések a fák együttesén alapulnak.

## <a name="how-to-configure"></a>Hogyan kell beállítani 

Ez a modul egy képzetlen besorolási modellt hoz létre. Mivel a besorolás felügyelt tanulási módszer, egy *címkézett adatkészletre* van szükség, amely tartalmazza az összes sor értékével rendelkező címkeoszlopot.

Az ilyen típusú modellt a [Vonatmodell](././train-model.md)segítségével taníthatja be. 

1.  Adja hozzá a **többosztályos súlyozott döntési fa** modult a folyamathoz.

1.  Adja meg, hogyan szeretné betanítani a modellt az **Oktató létrehozása mód beállítás** beállításával.

    + **Egyetlen paraméter:** Ha tudja, hogyan szeretné konfigurálni a modellt, argumentumként megadhat egy adott értékkészletet.
    
    + **Paramétertartomány**: Akkor válassza ezt a lehetőséget, ha nem biztos a legjobb paraméterekben, és paraméterkeresést szeretne futtatni. Válasszon ki egy értéktartományt, amely felett iterálni, és a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterálja az összes lehetséges kombinációa a megadott beállítások meghatározásához a hiperparaméterek, amelyek az optimális eredményt.  

1. **A levelek maximális száma egy fán korlátozza** a bármely fában létrehozható terminálcsomópontok (levelek) maximális számát.
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **A minták minimális száma levélcsomópontonként** azt jelzi, hogy hány eset szükséges egy terminálcsomópont (levél) létrehozásához egy fán.  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **A tanulási sebesség** határozza meg a lépésméretet a tanulás során. Írjon be egy 0 és 1 közötti számot.

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **A felépített fák száma** az együttesben létrehozandó döntési fák teljes számát jelzi. Azáltal, hogy több döntés fák, akkor potenciálisan jobb lefedettséget, de a képzési idő növekedni fog.

1. **A véletlenszám-mag** opcionálisan beállít egy nem negatív egész számot, amelyet véletlenszerű magértékként használ. A vetőmag megadása biztosítja a reprodukálhatóságot az azonos adatokkal és paraméterekkal rendelkező futtatások között.  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> Ha az **Oktató létrehozása módot** egy **paraméterre állítja**be, csatlakoztasson egy címkézett adatkészletet és a [Modell betanítása](./train-model.md) modult.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
