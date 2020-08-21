---
title: Log ML-kísérletek & metrikák
titleSuffix: Azure Machine Learning
description: Figyelje az Azure ML-kísérleteit, és figyelje a futtatási metrikákat a modell létrehozási folyamatának növelése érdekében. Adja hozzá a naplózást a betanítási parancsfájlhoz a Run. log, a Run. start_logging vagy a ScriptRunConfig használatával.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 18c7e5b3a1401540d7a94186fda647d413d562c0
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723843"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Naplózás engedélyezése az Azure ML betanítási futtatásában
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

A Azure Machine Learning Python SDK lehetővé teszi a valós idejű információk naplózását az alapértelmezett Python-naplózási csomag és az SDK-specifikus funkciók használatával. Helyileg is bejelentkezhet, és naplókat küldhet a munkaterületre a portálon.

A naplók segítségével diagnosztizálhatja a hibákat és a figyelmeztetéseket, vagy nyomon követheti a teljesítmény mérőszámait, például a paramétereket és a modell teljesítményét. Ebből a cikkből megtudhatja, hogyan engedélyezheti a naplózást a következő esetekben:

> [!div class="checklist"]
> * Interaktív betanítási tanfolyamok
> * Betanítási feladatok elküldése a ScriptRunConfig használatával
> * Python natív `logging` beállításai
> * Naplózás további forrásokból


> [!TIP]
> Ez a cikk bemutatja, hogyan figyelheti a modell betanítási folyamatát. Ha érdekli az Azure Machine learning erőforrás-felhasználásának és eseményeinek figyelése, például a kvóták, a befejezett betanítási futtatások vagy a modellen végzett központi telepítések, tekintse meg a [figyelés Azure Machine learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Adattípusok

Több adattípust is naplózhat, beleértve a skaláris értékeket, a listát, a táblákat, a képeket, a címtárakat és egyebeket. További információt és Python-programkódot a különböző adattípusokkal kapcsolatban a [futtatási osztály hivatkozása lapon](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)talál.

## <a name="interactive-logging-session"></a>Interaktív naplózási munkamenet

Az interaktív naplózási munkameneteket általában notebook-környezetekben használják. A [Experiment. start_logging ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) metódus egy interaktív naplózási munkamenetet indít el. A rendszer a munkamenet során naplózott összes mérőszámot hozzáadja a kísérlet futtatási rekordjához. A [Run. Complete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metódus befejezi a munkameneteket, és befejezettként jelöli meg a futtatást.

## <a name="scriptrunconfig-logs"></a>ScriptRunConfig-naplók

Ebből a szakaszból megtudhatja, hogyan adhat hozzá naplózási kódot a ScriptConfig-futtatásokban. A [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) osztály használatával a parancsfájlok és a környezetek is beágyazható az ismételhető futtatásokhoz. Ezt a lehetőséget is használhatja a Visual Jupyter notebookok figyelésére szolgáló widget megjelenítéséhez.

Ez a példa egy paramétert hajt végre az alfa-értékek fölé, és az eredményeket a [Run. log ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) metódus használatával rögzíti.

1. Hozzon létre egy képzési parancsfájlt, amely tartalmazza a naplózási logikát `train.py` .

   [! code-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Küldje ```train.py``` el a szkriptet, hogy az a felhasználó által felügyelt környezetben fusson. A teljes parancsfájl mappáját beküldi a rendszer a betanításhoz.

   [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? név = src)] [! notebook-Python [] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb? név = Futtatás)]

    A `show_output` paraméter bekapcsolja a részletes naplózást, amely lehetővé teszi a betanítási folyamat részleteit, valamint a távoli erőforrásokkal vagy számítási célokkal kapcsolatos információk megtekintését. A következő kód használatával bekapcsolhatja a részletes naplózást a kísérlet elküldésekor.

```python
run = exp.submit(src, show_output=True)
```

Ugyanezt a paramétert használhatja a `wait_for_completion` függvényben az eredményül kapott futtatásnál is.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Natív Python-naplózás

Az SDK egyes naplói tartalmazhatnak olyan hibát, amely arra utasítja a naplózási szint beállítását, hogy a HIBAKERESÉSt végezzen. A naplózási szint megadásához adja hozzá a következő kódot a parancsfájlhoz.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>További naplózási források

A Azure Machine Learning a betanítás során más forrásokból is naplózhat adatokat, például a gépi tanulási futtatásokat vagy a feladatokat futtató Docker-tárolókat. Ezek a naplók nincsenek dokumentálva, de ha problémákba ütközik, és a Microsoft támogatási szolgálatával fordulnak elő, előfordulhat, hogy a hibaelhárítás során ezeket a naplókat tudják használni.

A Azure Machine Learning Designer (előzetes verzió) metrikáinak naplózásával kapcsolatos információkért lásd: [mérőszámok naplózása a Designerben (előzetes verzió)](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Példajegyzetfüzetek

A következő jegyzetfüzetek a cikkben ismertetett fogalmakat mutatják be:
* [használati útmutató – azureml/képzés/helyi betanítás](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [használati útmutató – azureml/Track-and-monitor-kísérletek/naplózás – API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

A Azure Machine Learning használatáról a következő cikkekben talál további információt:

* Megtudhatja, hogyan [naplózhat metrikákat Azure Machine learning Designerben (előzetes verzió)](how-to-track-designer-experiments.md).

* Megtudhatja, hogyan regisztrálhat a legjobb modellt, és hogyan helyezheti üzembe az oktatóanyagban, hogyan [taníthatja be a rendszerképek besorolási modelljét Azure Machine learning](tutorial-train-models-with-aml.md).
