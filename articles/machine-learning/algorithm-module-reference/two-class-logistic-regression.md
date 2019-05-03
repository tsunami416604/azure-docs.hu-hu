---
title: 'Kétosztályos logisztikai regresszió: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogy a két osztályú logisztikai regressziós modul használata az Azure Machine Learning szolgáltatás egy két (és csak két) következmények előrejelzésére szolgáló logisztikai regressziós modell létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aacaf6c64ef77d0e694f97e3675060eca33794ed
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029250"
---
# <a name="two-class-logistic-regression-module"></a>Kétosztályos logisztikai regressziós modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával két (és csak két) következmények előrejelzésére szolgáló egy logisztikai regressziós modell létrehozásához. 

Logisztikai regressziós a jól ismert statisztikai technika, amely számos különböző típusú problémákat modellezési szolgál. Ez az algoritmus a *felügyelt tanítással* metódus;  ezért meg kell adnia egy adatkészletet, amely már tartalmazza a modell betanításához az eredményekkel.  

### <a name="about-logistic-regression"></a>Tudnivalók a logisztikai regressziós  

Logisztikai regressziós a statisztika, amely előre jelezni a valószínűsége annak az eredménye, szolgál, és különösen népszerű fájlosztályozási feladatokhoz a jól ismert módszer. Az algoritmus igyekeznek az adatok egy logisztikai függvény által előre megbecsüli egy esemény előfordulásának valószínűségét.
  
Ez a modul az osztályozó algoritmus dichotomous vagy bináris változók van optimalizálva. Ha több eredményekkel besorolása van szüksége, használja a [osztályú logisztikai regressziós](./multiclass-logistic-regression.md) modul.

##  <a name="how-to-configure"></a>Konfigurálása  

Ez a modell betanítására, meg kell adnia egy adatkészletet, amely egy címkét vagy osztály oszlopot tartalmaz. Ez a modul két osztályú problémák szól, mert a címkét vagy osztály oszlop pontosan két értéket kell tartalmaznia. 

Például a címke oszlop előfordulhat, hogy lehet [szavazott] "Yes" vagy "No" lehetséges értékeket. Vagy [hitelkockázat], a lehetséges értékek a "Nagy" vagy "Alacsony" lehet. 
  
1.  Adja hozzá a **Two-Class logisztikai regressziós** modult a kísérletvászonra.  
  
2.  Adja meg, hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.  
  
    -   **Egyetlen paraméter**: Ha tudja, hogyan szeretné konfigurálni a modellt, megadhat egy adott értékhalmazt argumentumként.  
  
3.  A **optimalizálási tolerancia**, adjon meg egy küszöbértéket. a modell optimalizálása használja. Az ismétlések között továbbfejlesztése a megadott küszöbérték alá csökken, ha az algoritmus tekinthető rendelkezik hiperkonvergens megoldás a, és képzési leállítja.  
  
4.  A **L1 regularizációs súly** és **L2 regularizációs súly**, adjon meg egy értéket a regularizációs paraméterek L1 és L2 használatára. Egy nem nulla értéket is ajánlott.  
  
     *Regularizációs* módszer megakadályozza, hogy a overfitting által büntetése modellek rendkívül együttható értékekkel. Regularizációs működik, a társított együttható értékeket a feltételezést hiba büntetés hozzáadásával. Így egy rendkívüli együttható értékek pontos modellhez lenne több büntetik, de kevésbé pontos modell korlátozóbb értékekkel kevesebb lenne büntetik.  
  
     L1 és L2 regularizációs különböző következményekkel járhat, és használja.  
  
    -   L1 alkalmazhatók a ritka modellek, amelyek akkor hasznos, ha nagy dimenziós adatok használata.  
  
    -   Ezzel szemben a L2 regularizációs célszerű a nem ritka adatok számára.  
  
     Ez az algoritmus támogat L1 és L2 regularizációs értékek lineáris kombinációját: azt jelenti, ha <code>x = L1</code> és <code>y = L2</code>, majd <code>ax + by = c</code> lineáris leforgása alatt a regularizációs feltételeket határozza meg.  
  
    > [!NOTE]
    >  További tudnivalók a L1 és L2 regularizációs szeretne? A következő cikk ismerteti azokat a hogyan L1 és L2 regularizációs különböző, és hogy ez hogyan befolyásolja a modell méretezés, a logisztikai regressziós és-modellek Neurális hálózat Kódminták biztosítja:  [L1 és gépi tanulási L2 Regularizációs](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Logisztikai regressziós modell lineáris különböző kombinációit L1 és L2 feltételek lett kidolgozták: például [rugalmas nettó regularizációs](https://wikipedia.org/wiki/Elastic_net_regularization). Javasoljuk, hogy a következő billentyűkombinációk meghatározásához, hogy hatékonyan a modell lineáris együttes hivatkoznak.
      
5.  A **L-BFGS memóriaméret**, adja meg a használt memória mennyiségét *L-BFGS* optimalizálás.  
  
     Az L-BFGS "korlátozott memória Broyden-Fletcher-Goldfarb-Shanno" jelöli. Egy algoritmus, amely általában a paraméter-becslésére esetén fontos. Ez a paraméter elmúlt pozíciók és tárolására, a következő lépés a törölje a számításhoz átmenetekhez számát jelzi.  
  
     Az optimalizálás paraméter számítási a következő lépés, valamint iránya használt memória mennyiségét korlátozza. Adja meg kevesebb memóriát, képzési esetén gyorsabb, de kevésbé pontos.  
  
6.  A **véletlenszerű szám kezdőérték**, írja be az egész szám. Egy kezdeti érték meghatározása fontos, ha azt szeretné, hogy az ugyanazon kísérletben a több Futtatás reprodukálható kell az eredményeket.  
  
  
8. Címkézett adatkészlet hozzáadása a kísérletet, és csatlakozzon az egyik a [képzési modulok](module-reference.md).  
  
    -   Ha **létrehozási trainer módban** való **egyetlen paramétert**, használja a [tanítási modell](./train-model.md) modul.  
  
9. Futtassa a kísérletet.  
  
## <a name="results"></a>Results (Eredmények)

Képzési befejezése után:

+ A modell paraméterek együtt a szolgáltatás súlyok származó képzési, összefoglaló kattintson a jobb gombbal a kimenetét [tanítási modell](./train-model.md) válassza **Visualize**.   
  
+ Az új adatok előrejelzéseket, használja a betanított modell és az új adatok bemenetként a [Score Model](./score-model.md) modul. 


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 