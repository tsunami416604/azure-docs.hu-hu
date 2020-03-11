---
title: Példa tervezői folyamatokra
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning Designerben mintákat használhat a gépi tanulási folyamatok elindításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/10/2020
ms.openlocfilehash: 82476b9cc8d92c815df602496ed3dcb33014a4fd
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037401"
---
# <a name="designer-sample-pipelines"></a>Tervezői mintavételi folyamatok

A Azure Machine Learning Designer beépített példái segítségével gyorsan megkezdheti saját gépi tanulási folyamatainak kialakítását. A Azure Machine Learning Designer [GitHub-tárház](https://github.com/Azure/MachineLearningDesigner) részletes dokumentációt tartalmaz, amely megkönnyíti a gyakori gépi tanulási forgatókönyvek megismerését.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).
* Azure Machine Learning munkaterület a vállalati SKU-val.


## <a name="how-to-use-sample-pipelines"></a>A mintavételi folyamatok használata

A tervező elmenti a mintavételi folyamatok egy példányát a Studio-munkaterületre. A folyamat szerkesztésével módosíthatja az igényeinek megfelelően, és mentheti a folyamatot. A projektek kiindulási pontként használhatók.

1. Jelentkezzen be a <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>-be, és válassza ki a munkaterületet, amellyel dolgozni szeretne.

1. Válassza a **tervező**lehetőséget.

1. Válasszon egy minta folyamatot az **új folyamat** szakaszban.

    Válassza a **további minták megjelenítése** lehetőséget a minták teljes listájához.

## <a name="regression-samples"></a>Regressziós minták

További információ a beépített regressziós mintákról.

| Mintacím | Leírás | 
| --- | --- |
| [1. példa: regresszió – autó árának előrejelzése (alapszintű)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Az autó árának előrejelzése lineáris regresszióval. |
| [2. minta: regresszió – autó árának előrejelzése (speciális)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Előre jelezheti az autók árát a döntési erdőben és a döntési fa regresszort. Hasonlítsa össze a modelleket a legjobb algoritmus megkereséséhez.

## <a name="classification-samples"></a>Besorolási minták

További információ a beépített besorolási mintákról. A mintákról a minták megnyitásával és a modulok megjegyzésének megtekintésével többet is megtudhat.

| Mintacím | Leírás | 
| --- | --- |
| [3. minta: bináris besorolás a funkciók kiválasztásával – bevétel előrejelzése](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | A bevételt magas vagy alacsony értékűre becsülheti, kétosztályos kibővített döntési fában. A Pearson korreláció használatával válassza ki a funkciókat.
| [4. minta: bináris besorolás egyéni Python-parancsfájllal – hitelkockázat-előrejelzés](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Magas vagy alacsony kockázatú kredit alkalmazások besorolása. Az adatai súlyozásához használja a Python-szkript végrehajtása modult.
| [5. példa: bináris besorolás – ügyfélkapcsolatok előrejelzése](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | A kétosztályos, megnövelt döntési fák használatával megjósolhatja az ügyfelek adatforgalmát. A ARCUL ütötte használata az elfogult adatértékek megkóstolására.
| [7. minta: szöveges besorolás – wikipedia SP 500 adatkészlet](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Vállalati típusok besorolása a wikipedia-cikkekből többosztályos logisztikai regresszióval. |
| 12. példa: többosztályos besorolás – levél-felismerés | Hozzon létre a bináris osztályozók együttesét írásos betűk besorolásához. |

## <a name="recommender-samples"></a>Ajánlott minták

További információ a beépített Ajánlói mintákról. A mintákról a minták megnyitásával és a modulok megjegyzésének megtekintésével többet is megtudhat.

| Mintacím | Leírás | 
| --- | --- |
| 10. minta: javaslat – film minősítési tweetek | Hozzon létre egy Movie ajánló motort a film címei és minősítése alapján. |

## <a name="utility-samples"></a>Segédprogram-minták

További információ a Machine learning segédprogramokat és szolgáltatásokat bemutató mintákról. A mintákról a minták megnyitásával és a modulok megjegyzésének megtekintésével többet is megtudhat.

| Mintacím | Leírás | 
| --- | --- |
| [6. példa: egyéni R-szkript használata – repülési késleltetés előrejelzése](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| 8\. példa: a bináris besorolások átellenőrzése – felnőtt bevétel előrejelzése | Használjon kereszt-érvényesítést a felnőtt jövedelmek bináris besorolásának kiépítéséhez.
| 9\. példa: a permutáció funkció fontossága | Az adatkészlethez tartozó számítási fontossági pontszámok kiszámításához használja a permutáció funkció fontosságát. 
| 11. minta: a bináris besorolás paramétereinek hangolása – felnőtt jövedelem előrejelzése | A Hiperparaméterek beállítása hangolása segítségével optimális hiperparaméterek beállítása találhat a bináris osztályozók létrehozásához. |

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan hozhat létre és helyezhet üzembe gépi tanulási modelleket [oktatóanyaggal: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)
