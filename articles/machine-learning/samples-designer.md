---
title: Példa tervezői folyamatokra
titleSuffix: Azure Machine Learning
description: Az Azure Machine Learning tervezőjének mintáival elindíthatja a gépi tanulási folyamatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389341"
---
# <a name="designer-sample-pipelines"></a>Tervezői mintafolyamatok

Az Azure Machine Learning-tervező beépített példáival gyorsan elkezdheti saját gépi tanulási folyamatai készítését. Az Azure Machine Learning [tervezője, a GitHub-tárház](https://github.com/Azure/MachineLearningDesigner) részletes dokumentációt tartalmaz, amely segít megérteni néhány gyakori gépi tanulási forgatókönyvet.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://aka.ms/AMLFree)
* Egy Azure Machine Learning-munkaterület a vállalati termékváltozattal.


## <a name="how-to-use-sample-pipelines"></a>Mintafolyamatok használata

A tervező a mintafolyamatok egy példányát menti a stúdió munkaterületére. A folyamat ot szerkesztheti, hogy az igényeihez igazítsa, és mentse a sajátjaként. Használja őket kiindulási pontként a projektek indításához.

### <a name="open-a-sample-pipeline"></a>Mintafolyamat megnyitása

1. Jelentkezzen be <a href="https://ml.azure.com?tabs=jre" target="_blank">a ml.azure.com,</a>és válassza ki azt a munkaterületet, amelyen dolgozni szeretne.

1. Válassza **a Tervező**lehetőséget.

1. Jelöljön ki egy mintafolyamatot az **Új csővezeték** szakasz alatt.

    Válassza a **További minták megjelenítése** lehetőséget a minták teljes listájához.

### <a name="submit-a-pipeline-run"></a>Folyamatfuttatás beküldése

Egy folyamat futtatásához először be kell állítania az alapértelmezett számítási célt a folyamat futtatásához.

1. A **Beállítások** ablaktáblán, a vászon tól jobbra válassza a **Számítási cél kiválasztása lehetőséget.**

1. A megjelenő párbeszédpanelen jelöljön ki egy meglévő számítási célt, vagy hozzon létre egy újat. Kattintson a **Mentés** gombra.

1. Válassza a **Küldés** lehetőséget a vászon tetején a folyamatfuttatás elküldéséhez.

A mintafolyamattól és a számítási beállításoktól függően a futtatások befejezéséhez eltarthat egy ideig. Az alapértelmezett számítási beállítások minimális csomópontmérete 0, ami azt jelenti, hogy a tervező nek erőforrásokat kell lefoglalnia, miután tétlen. Az ismétlődő folyamatfuttatások kevesebb időt vesznek igénybe, mivel a számítási erőforrások már le vannak foglalva. Emellett a tervező az egyes modulok gyorsítótárazott eredményeit használja a hatékonyság további növelése érdekében.


### <a name="review-the-results"></a>Az eredmények áttekintése

Miután a folyamat futása befejeződik, áttekintheti a folyamatot, és megtekintheti az egyes modulok kimenetét, hogy további információval rendelkezhet.

A modulkimenetek megtekintéséhez kövesse az alábbi lépéseket:

1. Jelöljön ki egy modult a vásznon.

1. A modul részletei ablaktáblában a vászon jobb oldalán válassza a **Kimenetek + naplók**lehetőséget. Az egyes ![modulok eredményének megjelenítéséhez válassza a diagram ikont.](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) 

Használja a mintákat kiindulási pontként néhány, a leggyakoribb gépi tanulási forgatókönyvek.

## <a name="regression-samples"></a>Regressziós minták

További információ a beépített regressziós mintákról.

| Mintacím | Leírás | 
| --- | --- |
| [1. minta: Regresszió - Autóár-előrejelzés (Alap)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Az autóárak előrejelzése lineáris regresszióval. |
| [2. minta: Regresszió - Autóár-előrejelzés (Haladó)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Tippelje meg az autóárakat a döntési erdő használatával, és növelte a döntési fa regresszióit. Hasonlítsa össze a modelleket, hogy megtalálja a legjobb algoritmust.

## <a name="classification-samples"></a>Osztályozási minták

További információ a beépített besorolási mintákról. A minták dokumentációs hivatkozások nélkül többet is megtudhat, ha megnyitja a mintákat, és helyette a modul megjegyzéseit tekinti meg.

| Mintacím | Leírás | 
| --- | --- |
| [3. minta: Bináris osztályozás a funkcióválasztással - Jövedelem-előrejelzés](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Tippelje meg a jövedelmet magasvagy alacsony, egy kétosztályos kiemelt döntési fa használatával. A Pearson korreláció segítségével válasszon ki funkciókat.
| [4. minta: Bináris besorolás egyéni Python-parancsfájllal – Hitelkockázat-előrejelzés](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | A hitelkérelmek besorolása magas vagy alacsony kockázatúként. A Python-parancsfájl végrehajtása modul segítségével súlyozzák az adatokat.
| [5. minta: Bináris besorolás - Ügyfélkapcsolat-előrejelzés](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Az ügyféllemorzsolódás előrejelzése kétosztályos kiemelt döntési fák használatával. Az SMOTE használatával minta elfogult adatok.
| [7. minta: Szövegosztályozás – Wikipédia SP 500 adatkészlet](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Osztályozza a vállalattípusokat a Wikipédia cikkeiből a többosztályos logisztikai regresszióval. |
| 12. minta: Többosztályos osztályozás – betűfelismerés | Hozzon létre egy együttest a bináris osztályozók osztályozni írott betűket. |

## <a name="recommender-samples"></a>Ajánlói minták

További információ a beépített ajánlómintákról. A minták dokumentációs hivatkozások nélkül többet is megtudhat, ha megnyitja a mintákat, és helyette a modul megjegyzéseit tekinti meg.

| Mintacím | Leírás | 
| --- | --- |
| 10. minta: Ajánlás - Movie Rating Tweets | Készíts egy filmajánló motort a filmcímekből és az értékelésből. |

## <a name="utility-samples"></a>Közüzemi minták

További információ a gépi tanulási segédprogramokat és -szolgáltatásokat szemléltető mintákról. A minták dokumentációs hivatkozások nélkül többet is megtudhat, ha megnyitja a mintákat, és helyette a modul megjegyzéseit tekinti meg.

| Mintacím | Leírás | 
| --- | --- |
| [6. minta: Egyéni R-parancsfájl használata – Járatkésleltetés-előrejelzés](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| 8. minta: Keresztérvényesítés bináris osztályozáshoz - Felnőtt jövedelem előrejelzése | Használja a keresztérvényesítést, hogy hozzon létre egy bináris osztályozó felnőtt jövedelem.
| 9. minta: Permutációs funkció fontossága | A permutációs funkció fontossága segítségével számítsa ki a tesztadatkészlet fontossági pontjait. 
| 11. minta: A bináris osztályozás paramétereinek finomhangolása - Felnőtt jövedelem-előrejelzés | A Tune Model Hyperparameters segítségével optimális hiperparamétereket találhat bináris osztályozó létrehozásához. |

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozhat létre és helyezhet üzembe gépi tanulási modelleket [az oktatóanyaggal: Az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)
