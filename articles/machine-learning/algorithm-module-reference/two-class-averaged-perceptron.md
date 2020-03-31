---
title: 'Döntési erdő regressziója: Modul hivatkozása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a kétosztályos átlagérték-perceptron modult az Azure Machine Learningben egy gépi tanulási modell létrehozásához az átlagos perceptron algoritmus alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920773"
---
# <a name="two-class-averaged-perceptron-module"></a>Kétosztályos átlagos perceptron modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal hozzon létre egy gépi tanulási modell alapján az átlagos perceptron algoritmus.  
  
Ez a besorolási algoritmus egy felügyelt tanulási módszer, és egy *címkézett adatkészletet*igényel, amely tartalmaz egy címkeoszlopot. A modell betanításához adja meg a modellt és a címkézett adatkészletet a [betanítási modell bemeneteként.](./train-model.md) A betanított modell ezután az új bemeneti példák értékeinek előrejelzésére használható.  

### <a name="about-averaged-perceptron-models"></a>Körülbelül átlagos perceptron modellek

Az *átlagos perceptron módszer* egy neurális hálózat korai és egyszerű változata. Ebben a megközelítésben a bemenetek lineáris függvényen alapuló több lehetséges kimenetbe vannak besorolva, majd a jellemzővektorból származó súlyokkal kombinálva – innen a "perceptron" név.

Az egyszerűbb észlelési modellek alkalmasak a lineárisan elkülöníthető minták tanulására, míg a neurális hálózatok (különösen a mély neurális hálózatok) összetettebb osztályhatárokat modellezhetnek. Az észlelések azonban gyorsabbak, és mivel az eseteket sorozatosan dolgozzák fel, az észlelések folyamatos képzéssel használhatók.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>A kétosztályos átlagos perceptron beállítása

1.  Adja hozzá a **kétosztályos átlagú perceptron** modult a folyamathoz.  

2.  Adja meg, hogyan szeretné betanítani a modellt az **Oktatói mód létrehozása** beállítás beállításával.  
  
    -   **Egyetlen paraméter:** Ha tudja, hogyan szeretné konfigurálni a modellt, adjon meg egy adott értékkészletet argumentumként.

    -   **Paramétertartomány**: Akkor válassza ezt a lehetőséget, ha nem biztos a legjobb paraméterekben, és paraméterkeresést szeretne futtatni. Válasszon ki egy értéktartományt, amely felett iterálni, és a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterálja az összes lehetséges kombinációa a megadott beállítások meghatározásához a hiperparaméterek, amelyek az optimális eredményt.  
  
3.  A **tanulási arány**hoz adja meg a tanulási arány *értékét*. A tanulási sebesség értékek szabályozzák a sztocastikus gradiens esésben használt lépés méretét a modell minden egyes tesztelésekor és javításakor.
  
     Azáltal, hogy az arány kisebb, akkor tesztelje a modell gyakrabban, azzal a kockázattal, hogy esetleg elakad egy helyi fennsíkon. Azáltal, hogy a lépés nagyobb, akkor konvergál gyorsabban, a kockázat overshooting az igazi minimumok.
  
4.  Az **ismétlések maximális száma**mezőbe írja be, hogy az algoritmus hányszor vizsgálja meg a betanítási adatokat.  
  
     A korai megállás gyakran jobb általánosítást biztosít. Számának növelése iterációk javítja illeszkedés, a kockázata a túlszerelés.
  
5.  A **véletlenszám-vetőmagesetében**tetszés szerint írjon be egy egész értéket, amelyet vetőmagként szeretne használni. A mag használata ajánlott, ha biztosítani szeretné a folyamat reprodukálhatóságát a futtatások között.  
  
1.  Hozzon össze egy betanítási adatkészletet és az egyik betanítási modult:
  
    -   Ha az **Oktató létrehozása módot** egy **paraméterre állítja**be, használja a [Betanítási modell](train-model.md) modult.




## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 