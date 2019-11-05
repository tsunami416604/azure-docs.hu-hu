---
title: 'Döntési erdő regressziója: modul leírása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható a kétosztályos átlagú Perceptron modul a Azure Machine Learningban egy gépi tanulási modell létrehozásához az átlagos Perceptron algoritmus alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 379cddd9654cc897b49fd085d8df55fcd77a7ce8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490364"
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
  
3.  A **tanulási arány**mezőben határozza meg a *képzési arány*értékét. A tanulási arány értéke a modell minden egyes tesztelésekor és javításakor a sztochasztikus gradiensben használt lépés méretét szabályozza.
  
     A sebesség csökkentésével gyakrabban teszteli a modellt, és azzal a kockázattal jár, hogy egy helyi fennsíkon fog elakadni. A lépés elvégzésével megközelítheti a valódi minimumokat, és így gyorsabban is megoszthatja őket.
  
4.  Az **Ismétlések maximális száma**beállításnál adja meg, hogy az algoritmus hány alkalommal vizsgálja meg a betanítási adatmennyiséget.  
  
     A korai leállítás gyakran jobb általánosítást biztosít. Az ismétlések számának növelése növeli a beilleszkedés kockázatát.
  
5.  A **véletlenszerű számú magok**esetében szükség esetén adjon meg egy egész számot, amelyet magként kíván használni. A magok használata ajánlott, ha biztosítani szeretné, hogy a folyamat megismételhetősége fusson.  
  
1.  Csatlakoztasson egy betanítási adatkészletet és egy betanítási modult:
  
    -   Ha az **oktatói módot** **egyetlen paraméterként**állítja be, használja a [Train Model](train-model.md) modult.

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Ha meg szeretné tekinteni a modell paramétereinek összegzését, valamint a képzésből beszerzett funkciók súlyozását, kattintson a jobb gombbal a [vonat modell](./train-model.md)kimenetére.


## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 