---
title: ML-kísérletek és -metrikák naplózása
titleSuffix: Azure Machine Learning
description: Monitorozza az Azure ML-kísérleteket és a futtatási metrikákat a modell-létrehozási folyamat továbbfejlesztése érdekében. Adjon naplózást a betanítási szkripthez a run.log, Run.start_logging vagy ScriptRunConfig paranccsal.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 44fe71f575a32ccc1a687bc87793cb6a8b6508a9
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650620"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Naplózás engedélyezése az Azure ML betanítási futtatásaiban
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learning Python SDK az alapértelmezett Python naplózási csomaggal és SDK-specifikus funkciókkal is lehetővé teszi valós idejű információk naplózását. Naplózhat helyben, és elküldheti a naplókat a portálon lévő munkaterületekre.

A naplókkal diagnosztizálhatja a hibákat és a figyelmeztetéseket, vagy nyomon követheti a teljesítménymetrikákat, például a paramétereket és a modell teljesítményét. Ebben a cikkben azzal fog megismerkedni, hogyan engedélyezheti a naplózást a következő forgatókönyvekben:

> [!div class="checklist"]
> * Interaktív betanítási munkamenetek
> * Betanítási feladatok elküldése a ScriptRunConfig használatával
> * Python natív `logging` beállításai
> * Naplózás további forrásokból


> [!TIP]
> Ez a cikk a modell betanítási folyamatának monitorozását mutatja be. Ha az erőforrás-használatot és az eseményeket szeretné monitorozni az Azure Machine Learningből, például a kvótákat, a befejezett betanítási futtatásokat vagy a modell kész üzemelő példányait, tekintse meg [az Azure Machine Learning monitorozásával](monitor-azure-machine-learning.md) kapcsolatos szakaszt.

## <a name="data-types"></a>Adattípusok

Többféle adattípust is naplózhat, például skaláris értékeket, listákat, táblákat, rendszerképeket, könyvtárakat és egyebeket. További információt és a különböző adattípusok Python-kódmintáit a [futtatási osztály referenciaoldalán](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py&preserve-view=true) találja.

## <a name="interactive-logging-session"></a>Interaktív naplózási munkamenet

Az interaktív naplózási munkameneteket általában jegyzetfüzet típusú környezetekben használják. Az [Experiment.start_logging()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) metódus elindít egy interaktív naplózási munkamenetet. A munkamenet során naplózott összes metrika a kísérlet futtatási rekordjába kerül. A [run.complete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) metódus befejezi a munkameneteket, és befejezettként jelöli meg a futtatást.

## <a name="scriptrunconfig-logs"></a>ScriptRunConfig naplók

Ebben a szakaszban azt ismertetjük, hogyan adhat meg naplózási kódot a ScriptConfig-futtatásokban. A [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true) osztállyal szkripteket és környezeteket ágyazhat be ismételhető futtatásokhoz. Ezzel a lehetőséggel azt is megteheti, hogy megjelenít egy vizuális Jupyter Notebooks-vezérlőt a monitorozáshoz.

Ez a példa paraméteres frissítést végez az alfa értékeken, és az eredményeket a [run.log()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#&preserve-view=truelog-name--value--description----) metódussal rögzíti.

1. Hozza létre a naplózási logikát tartalmazó `train.py` betanítási szkriptet.

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Küldje be a ```train.py``` szkriptet a felhasználó által kezelt környezetekben való futtatáshoz. A rendszer a teljes szkriptmappát elküldi a betanításhoz.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    A `show_output` paraméter bekapcsolja a részletes naplózást, amely lehetővé teszi a betanítási folyamat részleteinek, valamint a távoli erőforrásokkal vagy számítási célokkal kapcsolatos információknak a megtekintését. A következő kóddal kapcsolja be a részletes naplózást a kísérlet beküldésekor.

```python
run = exp.submit(src, show_output=True)
```

Ugyanezt a paramétert használhatja az eredményül kapott futtatás `wait_for_completion` függvényén.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Natív Python-naplózás

Az SDK egyes naplói olyan hibát tartalmazhatnak, amely arra utasítja, hogy a naplózást állítja DEBUG (hibakeresési) szintre. A naplózási szint beállításához adja a következő kódot a szkripthez.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>További naplózási források

Az Azure Machine Learning a betanítás során más forrásokból, például automatizált gépi tanulási futtatásokból vagy a feladatokat futtató Docker-tárolókból származó adatokat is tud naplózni. Ezek a naplók nincsenek dokumentálva, de ha problémákba ütközik, és kapcsolatba lép a Microsoft ügyfélszolgálatával, előfordulhat, hogy a hibaelhárítás során ők tudják használni ezeket a naplókat.

Az Azure Machine Learning-tervező (előzetes verzió) naplózási metrikáiról [az előzetes verziójú tervezőben végzett metrikanaplózással](how-to-track-designer-experiments.md) kapcsolatos témakörben talál információt.

## <a name="example-notebooks"></a>Példajegyzetfüzetek

A cikkben szereplő fogalmakat a következő jegyzetfüzetek mutatják be:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Ezekben a cikkekben többet tudhat meg az Azure Machine Learning használatáról:

* Ismerje meg, hogyan [naplózhat metrikákat az Azure Machine Learning-tervezőben (előzetes verzió)](how-to-track-designer-experiments.md).

* [A képosztályozási modellek Azure Machine Learninggel való betanításával](tutorial-train-models-with-aml.md) kapcsolatos témakörben talál példát arra, hogyan regisztrálhatja a legjobb modellt, és hogyan helyezheti üzembe.
