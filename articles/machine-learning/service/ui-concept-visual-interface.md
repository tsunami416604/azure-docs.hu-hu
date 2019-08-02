---
title: Vizuális felület
titleSuffix: Azure Machine Learning service
description: Ismerje meg a Azure Machine Learning szolgáltatás vizualizációs felületét (előnézet) alkotó feltételeket, fogalmakat és munkafolyamatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 1cb56386f52d1c7b1ec357b912c648c1961b4c1d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68677992"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>Mi a Azure Machine Learning szolgáltatás vizuális felülete? 

A Azure Machine Learning szolgáltatás vizuális felülete (előzetes verzió) lehetővé teszi a gépi tanulási modellek előkészítését, használatát, tesztelését, üzembe helyezését, kezelését és nyomon követését programkód írása nélkül.

Nincs szükség programozásra, vizuálisan összekapcsolhatók az adatkészletek és [modulok](#module) a modell létrehozásához. [](#dataset)

A vizuális felület a Azure Machine Learning szolgáltatás munkaterületét használja a következőhöz: [](concept-workspace.md)

+ Az írási összetevők a munkaterületen [futnak.](#experiment)
+ Hozzáférési [](#dataset)adatkészletek.
+ A kísérlet futtatásához használja a munkaterület [számítási erőforrásait](#compute) . 
+ [Modellek](concept-azure-machine-learning-architecture.md#models)regisztrálása.
+ A modelleket webszolgáltatásként [helyezheti üzembe](#deployment) a munkaterületen lévő számítási erőforrásokon.

![A vizuális felület áttekintése](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Munkafolyamat

A vizuális felület interaktív, vizualizációs vászon használatával gyorsan kiépítheti, tesztelheti és megismételheti a modelleket. 

+ A vászonra húz és húzhatja [](#module) a modulokat.
+ A modulok összekapcsolása [kísérlet](#experiment)létrehozásához.
+ Futtassa a kísérletet a Machine Learning szolgáltatás munkaterület számítási erőforrásának használatával.
+ A kísérlet szerkesztésével és újbóli futtatásával ismételje meg a modell kialakítását.
+ Ha elkészült, alakítsa át a betanítási **kísérletet** egy **prediktív kísérletbe**.
+ A prediktív kísérletet webszolgáltatásként [helyezheti üzembe](#deployment) , hogy a modell mások számára is elérhető legyen.

## <a name="experiment"></a>Kísérlet

Hozzon létre egy kísérletet a semmiből, vagy használjon egy meglévő minta kísérletet sablonként.  Minden alkalommal, amikor kísérletet futtat, az összetevők a munkaterületen tárolódnak.

A kísérlet olyan adatkészleteket és analitikai modulokat tartalmaz, amelyek egy modell összeállításához kapcsolódnak. Az érvényes kísérletek a következő jellemzőkkel rendelkeznek:

* Az adatkészletek csak modulokhoz csatlakoztathatók.
* A modulok lehetnek adatkészletekhez vagy más modulokhoz csatlakoztathatók.
* A modulok összes bemeneti portjának csatlakoznia kell az adatfolyamhoz.
* Az egyes modulokhoz szükséges összes paramétert be kell állítani.


A vizuális felület megismeréséhez tekintse [meg az oktatóanyag: Az autó árának előrejelzése a vizualizációs](ui-tutorial-automobile-price-train-score.md)felületen.

## <a name="dataset"></a>Adathalmaz

Az adatkészlet olyan adatokat tartalmaz, amelyeket a rendszer feltöltött a vizualizációs felületen a modellezési folyamatban való használatra. Több minta adatkészletet is tartalmaz a kipróbáláshoz, és több adatkészletet is feltölthet, ahogy szüksége van rájuk.

## <a name="module"></a>Modul

A modulok az adatokon végezhető algoritmusok. A vizualizációs felület számos modult tartalmaz, amelyek az adatok beáramlása függvénytől kezdve a képzésre, a pontozásra és az érvényesítési folyamatokra terjednek ki.

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paramétereinek a vászontól jobbra látható Tulajdonságok ablaktáblán jelennek meg. A modell hangolásához módosíthatja a paramétereket a panelen.

![Modul tulajdonságai](media/ui-concept-visual-interface/properties.png)

Ha segítségre van az elérhető gépi tanulási algoritmusok könyvtára között, tekintse meg a következő témakört: [algoritmus & modul ismertetése – áttekintés](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Számítási erőforrások

A munkaterületen lévő számítási erőforrások használatával futtathatja a kísérletet, vagy üzemeltetheti a telepített modelleket webszolgáltatásként. A támogatott számítási célnak a következők:


| Számítási célt | Képzés | Környezet |
| ---- |:----:|:----:|
| Az Azure Machine Learning compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

A számítási célok a Machine Learning munkaterülethez [](concept-workspace.md)vannak csatolva. A számítási célokat a [Azure Portal](https://portal.azure.com)munkaterületén kezelheti.

## <a name="deployment"></a>Környezet

Miután a prediktív elemzési modell elkészült, üzembe helyezheti azt webszolgáltatásként közvetlenül a vizualizáció felületéről.

A webszolgáltatások felületet biztosítanak egy alkalmazás és a pontozási modell között. Egy külső alkalmazás valós időben tud kommunikálni a pontozási modellel. A webszolgáltatás felé irányuló hívások visszaküldik az előrejelzési eredményeket egy külső alkalmazásnak. A webszolgáltatások felé irányuló hívások indításához át kell adnia azt az API-kulcsot, amelyet a webszolgáltatás üzembe helyezésekor hozott létre. A webszolgáltatások a webes programozási projektek számára népszerű architektúrán alapulnak.

A modell üzembe helyezésének megismeréséhez tekintse [meg az oktatóanyag: Helyezzen üzembe egy gépi tanulási modellt a vizuális felületen](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a prediktív elemzés és a gépi tanulás [alapjaival az Oktatóanyaggal: Az autó árának előrejelzése a vizuális felületen](ui-tutorial-automobile-price-train-score.md)
* Használja az egyik mintát, és módosítsa az igényeinek megfelelően:
    * [1. példa – regresszió: Előre jelzett ár](ui-sample-regression-predict-automobile-price-basic.md)
    * [2. minta – regresszió: Előrejelzési ár és összehasonlítási algoritmusok](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [3. példa – besorolás: Hitelkockázat előrejelzése](ui-sample-classification-predict-credit-risk-basic.md)
    * [4. minta – besorolás: Hitelkockázat (Cost szenzitív)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [5. példa – besorolás: A forgalom előrejelzése, a szolgáltatóváltást és az értékesítés](ui-sample-classification-predict-churn.md)
