---
title: 'Döntési erdő regressziója: modul leírása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható a kétosztályos átlagú Perceptron modul a Azure Machine Learningban egy gépi tanulási modell létrehozásához az átlagos Perceptron algoritmus alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920773"
---
# <a name="two-class-averaged-perceptron-module"></a>Kétosztályos átlagú Perceptron modul

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal gépi tanulási modellt hozhat létre az átlagos perceptron algoritmus alapján.  
  
Ez a besorolási algoritmus felügyelt tanulási módszer, és olyan *címkézett adatkészletet*igényel, amely tartalmaz egy címke oszlopot. A modellt úgy is betaníthatja, hogy a modell és a címkézett adatkészlet bemenetként adja meg a [betanítási modellt](./train-model.md). A betanított modell ezután felhasználható az új bemeneti példák értékeinek előrejelzésére.  

### <a name="about-averaged-perceptron-models"></a>Az átlagos perceptron modellek

Az *átlagos perceptron módszer* egy neurális hálózat korai és egyszerű verziója. Ebben a megközelítésben a bemenetek egy lineáris függvény alapján több lehetséges kimenetbe vannak besorolva, és a szolgáltatás vektora által származtatott súlyok készletével kombinálva, így a "perceptron" névvel.

Az egyszerűbb perceptron modellek a lineárisan elválasztható minták tanulására alkalmasak, míg a neurális hálózatok (különösen a mély neurális hálózatok) összetettebb osztálybeli határokat tudnak modellezni. Azonban a perceptrons gyorsabbak, és mivel az eseteket a sorosan dolgozzák fel, a perceptrons folyamatos képzéssel használható.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Kétosztályos átlagú Perceptron konfigurálása

1.  Adja hozzá a **kétosztályos átlagú Perceptron-** modult a folyamathoz.  

2.  Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    -   **Egyetlen paraméter**: ha tudja, hogyan szeretné konfigurálni a modellt, adjon meg egy adott értékeket argumentumként.

    -   **Paraméter tartománya**: akkor válassza ezt a lehetőséget, ha nem biztos benne, hogy a legjobb paramétereket szeretné használni, és szeretne futtatni egy paramétert. Válassza ki a megismételni kívánt értékek tartományát, és a [finomhangolási modell hiperparaméterek beállítása](tune-model-hyperparameters.md) az optimális eredményeket eredményező hiperparaméterek beállítása meghatározásához megadott beállítások összes lehetséges kombinációján.  
  
3.  A **tanulási arány**mezőben határozza meg a *képzési arány*értékét. A tanulási arány értéke a modell minden egyes tesztelésekor és javításakor a sztochasztikus gradiensben használt lépés méretét szabályozza.
  
     A sebesség csökkentésével gyakrabban teszteli a modellt, és azzal a kockázattal jár, hogy egy helyi fennsíkon fog elakadni. A lépés elvégzésével megközelítheti a valódi minimumokat, és így gyorsabban is megoszthatja őket.
  
4.  Az **Ismétlések maximális száma**beállításnál adja meg, hogy az algoritmus hány alkalommal vizsgálja meg a betanítási adatmennyiséget.  
  
     A korai leállítás gyakran jobb általánosítást biztosít. Az ismétlések számának növelése növeli a beilleszkedés kockázatát.
  
5.  A **véletlenszerű számú magok**esetében szükség esetén adjon meg egy egész számot, amelyet magként kíván használni. A magok használata ajánlott, ha biztosítani szeretné, hogy a folyamat megismételhetősége fusson.  
  
1.  Csatlakoztasson egy betanítási adatkészletet és egy betanítási modult:
  
    -   Ha az **oktatói módot** **egyetlen paraméterként**állítja be, használja a [Train Model](train-model.md) modult.




## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 