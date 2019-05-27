---
title: Vizuális felület
titleSuffix: Azure Machine Learning service
description: Ismerje meg a feltételeket, fogalmak és a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás alkotó munkafolyamat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: 0b158a1d713e0de8f3d1b2672aed442fce66e724
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65917160"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>Mi az Azure Machine Learning szolgáltatás a vizuális felhasználói felületet? 

Az Azure Machine Learning szolgáltatás párbeszédhez (előzetes verzió) segítségével előkészítheti az adatokat, betanítását, teszteléséhez, üzembe helyezése, kezelése és nyomon követheti a machine learning-modellek kód írása nélkül is.

Nincs szükség programozásra van, vizuálisan csatlakozás [adatkészletek](#dataset) és [modulok](#module) a modell létrehozásához.

A vizuális felhasználói felületet használja az Azure Machine Learning szolgáltatás [munkaterület](concept-workspace.md) való:

+ Az összetevők írási [kísérletezhet](#experiment) futtatja a munkaterületre.
+ Hozzáférés [adatkészletek](#dataset).
+ Használja a [számítási erőforrások](#compute) futtathatja a kísérletet a munkaterületen található. 
+ Regisztráljon [modellek](concept-azure-machine-learning-architecture.md#model).
+ [Üzembe helyezése](#deployment) modellek, a webszolgáltatások számítási erőforrásokat a munkaterületen.

![A vizuális felhasználói felületet áttekintése](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Munkafolyamat

A vizuális felhasználói felületet biztosít egy interaktív, vizuális vászonalapú gyorsan hozhatók létre, tesztelheti és ismételt futtatásával egy modellt. 

+ Fogd és vidd [modulok](#module) a vászonra.
+ A modulok kapcsolhatja össze az űrlap egy [kísérletezhet](#experiment).
+ Futtassa a kísérletet a Machine Learning szolgáltatás munkaterület a számítási erőforrás használatával.
+ A modell felépítésének újrafuttathatja a kísérletet, és újra futtatni.
+ Amikor készen áll, konvertálja a **betanítási kísérlet** , egy **prediktív kísérletté**.
+ [Üzembe helyezése](#deployment) a prediktív kísérletté webes szolgáltatás, hogy a modell a mások által elérhető lesz.

## <a name="experiment"></a>Kísérlet

Egy kísérlet létrehozása az alapoktól, vagy egy meglévő mintakísérletet sablonként.  Kísérlet, minden egyes futtatásakor összetevők a munkaterületen tárolódnak.

Kísérlet az adatkészleteket és az elemzési modulok, a modell összeállításához kapcsolhatja össze áll. Az érvényes kísérletek a következő jellemzőkkel rendelkeznek:

* Az adatkészletek csak a modulokhoz csatlakoztathatók.
* Modulok csatlakoztathatók adathalmazokhoz és más modulok tesznek.
* Modulok összes bemeneti portjának rendelkeznie kell valamilyen kapcsolattal az adatfolyamhoz.
* Az összes szükséges paramétert, az egyes modulok kell beállítani.

Egyszerű kísérlet egy példa: [a rövid útmutató: Készítse elő és az adatok megjelenítése az Azure Machine Learning kódírás nélkül](ui-quickstart-run-experiment.md).

Prediktív elemzési megoldások részletesebb leírását lásd: [oktatóanyag: Vizuális felhasználói felülettel autó árát előrejelzése](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Adathalmaz

Az adathalmaz olyan adatok, amelyek a vizuális felhasználói felületet a modellezési folyamat során használandó lett feltöltve. Mintaadatkészletek számos megtalálhatók, kísérletezhet, és szükség esetén további adathalmazokat is feltölthet.

## <a name="module"></a>Modul

A modulok az adatokon végezhető algoritmusok. A vizuális felhasználói felületet és a bejövő forgalom adatfüggvény betanítási, pontozási és ellenőrzési folyamatokig modulok több rendelkezik.

A modul rendelkezhet a belső algoritmusok konfigurálásához használható paraméterek készletével. Amikor kiválaszt egy modult a vásznon, a modul paraméterei megjelennek a Tulajdonságok panelen a vászontól jobbra. A modell hangolásához módosíthatja a paramétereket a panelen.

![A modul tulajdonságai](media/ui-concept-visual-interface/properties.png)

Való eligazodást segíti a rendelkezésre álló gépi tanulási algoritmusok könyvtára, lásd: [Algoritmusokra és modulokra hivatkozhat áttekintése](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> A számítási erőforrásokat

A számítási erőforrásokat az munkaterületről, futtassa a kísérletet, vagy a gazdagép a telepített modellek webszolgáltatásként való használatát. A támogatott számítási célnak a következők:


| Számítási célt | Képzés | Környezet |
| ---- |:----:|:----:|
| Az Azure Machine Learning compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

Tárolók is csatlakozik a Machine Learning COMPUTE [munkaterület](concept-workspace.md). A számítási célokhoz kezelheti a munkaterület a [az Azure portal](https://portal.azure.com).

## <a name="deployment"></a>Környezet

Ha készen áll a prediktív elemzési modell, akkor helyezze üzembe, közvetlenül a vizuális felhasználói felületet a webszolgáltatás.

A web services-alkalmazások és a pontozási modelljével közötti illesztőfelületet szolgáltasson. Egy külső alkalmazás kommunikálhat a valós idejű pontozási modelljével. Webszolgáltatás hívása visszaadják az előrejelzés eredményét a külső alkalmazásnak. A webszolgáltatások felé irányuló hívások indításához át kell adnia azt az API-kulcsot, amelyet a webszolgáltatás üzembe helyezésekor hozott létre. A webszolgáltatás a webprogramozási projektekben népszerű architektúra választott REST alapul.

Ismerje meg, hogyan helyezi üzembe a modellt, lásd: [oktatóanyag: Vizuális felhasználói felülettel, egy gépi tanulási modell üzembe helyezése](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>További lépések

* Alapismeretek a Mobilfunkciók prediktív elemzési és gépi tanulás a [a rövid útmutató: Készítse elő és az adatok megjelenítése az Azure Machine Learning kódírás nélkül](ui-quickstart-run-experiment.md).
* Egy mintát használja, és a Suite módosítsa az igényeinek:
    * [1 – regressziós. példa: Ár előrejelzése](ui-sample-regression-predict-automobile-price-basic.md)
    * [2 – regressziós. példa: Ár előrejelzése és algoritmusok összehasonlítása](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Mintául szolgáló 3 - besorolás: Hitelkockázat előrejelzése](ui-sample-classification-predict-credit-risk-basic.md)
    * [4 – besorolási. példa: Hitelkockázatot (költség-és nagybetűket)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [5 – besorolási. példa: Előrejelezheti a forgalom szolgáltatóváltást és felfelé értékesítése](ui-sample-classification-predict-churn.md)
