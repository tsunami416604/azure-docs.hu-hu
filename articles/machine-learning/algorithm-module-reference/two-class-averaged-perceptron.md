---
title: 'Döntési erdő regresszió: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja a két osztályú átlagolt Perceptron modul az Azure Machine Learning szolgáltatásban hozzon létre egy gépi tanulási modellt az átlagolt perceptron algoritmus alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f0fec525ed87f91cf102053383b2934aac4b71c0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029235"
---
# <a name="two-class-averaged-perceptron-module"></a>Kétosztályos átlagolt Perceptron modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával hozzon létre egy gépi tanulási modellt az átlagolt perceptron algoritmus alapján.  
  
A besorolási algoritmus módszer a tanítás és szükséges egy *adatkészlet címkézett*, amely tartalmaz egy címkét. A modell betanításához biztosít a modell és a címkézett adatkészlet bemeneteként [Train Model](./train-model.md). A betanított modell majd az új bemeneti példák értékeinek használható.  

### <a name="about-averaged-perceptron-models"></a>Átlagolt perceptron modellek

A *perceptron metódus átlagolt* Neurális hálózat korai és egyszerű verziója. Ebben a megközelítésben bemenetek sorolhatók több lehetséges kimenetek lineáris függvény alapul, és majd a kombinálásukkal a szolgáltatás vektor származó súlyok vannak beállítva, ezért a neve "perceptron."

Az egyszerűbb perceptron modellek lineárisan elkülöníthető minták Learning vannak kialakítva, míg a Neurális hálózatok (különösen neurálishálózat) modellezhetik az összetettebb osztály határokat. Perceptrons azonban gyorsabb, és esetek tárolókonfigurációhoz feldolgozása, mert a folyamatos tanítási perceptrons használható.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Kétosztályos átlagolt Perceptron konfigurálása

1.  Adja hozzá a **Two-Class átlagolt Perceptron** modult a kísérletvászonra.  

2.  Adja meg, hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.  
  
    -   **Egyetlen paraméter**: Ha tudja, hogyan szeretné konfigurálni a modellt, argumentumaként adja meg az értékeket egy adott készletét.
  
3.  A **tanulási ráta**, adjon meg egy értéket a *tanulási ráta*. Megfelelő tanulási rátát a vezérlőelem mérete sztochasztikus gradiens módszeres használt minden alkalommal, amikor a modell tesztelése és javítani a lépés értékeket.
  
     Azáltal, hogy a sebessége kisebb, akkor teszteli a modellt gyakran, és annak a kockázata, hogy, előfordulhat, hogy elakadnak a helyi tetőzik. Azáltal, hogy a lépés nagyobb, akkor virtuálisgépbérlő gyorsabb és kockázatára túllépés igaz minimális követelménynek.
  
4.  A **az ismétlések maximális száma**, adja meg a számot, amikor azt szeretné, az algoritmus a betanítási adatok vizsgálata.  
  
     Jobb általánossá gyakran korai leállítása biztosít. Méretezés kockázatára overfitting ismétléseinek számának növelése növeli.
  
5.  A **véletlenszerű szám kezdőérték**, szükség esetén írja be a kezdőérték adatokként egész szám. Az egyik kezdőérték használata ajánlott, ha azt szeretné, a kísérlet között megismételhetőség futtatja.  
  
1.  Betanítási adatkészletet, és a egy képzési modul csatlakozzon:
  
    -   Ha **létrehozási trainer módban** való **egyetlen paramétert**, használja a [tanítási modell](train-model.md) modul.

## <a name="results"></a>Results (Eredmények)

Képzési befejezése után:

+ A modell paraméterek együtt a szolgáltatás súlyok származó képzési, összefoglaló kattintson a jobb gombbal a kimenetét [tanítási modell](./train-model.md).


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 