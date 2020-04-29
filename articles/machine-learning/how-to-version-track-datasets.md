---
title: Adatkészlet verziószámozása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható az adatkészletek legjobb verziója, és hogyan működik a verziószámozás a Machine learning-folyamatokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: 5bd4436fc63fb570f052606ab557dbcf243cf5e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80476859"
---
# <a name="version-and-track-datasets-in-experiments"></a>Adatkészletek verziója és nyomon követése kísérletekben
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan lehet a reprodukálni Azure Machine Learning adatkészletek verzióját és nyomon követését. Az adatkészlet verziószámozása lehetővé teszi az adathalmazok könyvjelzővel való megjelölését, így az adatkészlet egy adott verzióját később is alkalmazhatja a jövőbeli kísérletekhez.

Jellemző verziószámozási forgatókönyvek:

* Ha új adatgyűjtési lehetőség áll rendelkezésre
* Különböző adatelőkészítési vagy szolgáltatás-mérnöki módszerek alkalmazása esetén

## <a name="prerequisites"></a>Előfeltételek

Ebben az oktatóanyagban a következőkre lesz szüksége:

- [A Azure Machine learning SDK for Python telepítve van](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). Ez az SDK tartalmazza a [azureml-adatkészletek](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) csomagot.
    
- Egy [Azure Machine learning munkaterület](concept-workspace.md). A következő kód futtatásával vagy [egy új munkaterület létrehozásával](how-to-manage-workspace.md)kérjen le egy meglévőt.

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Egy [Azure Machine learning adatkészlet](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Adatkészlet-verziók regisztrálása és lekérése

Az adatkészletek regisztrálásával a kísérleteken és a munkatársakon is megtekintheti, újból felhasználhatja és megoszthatja azokat. Több adatkészletet is regisztrálhat ugyanazon a néven, és egy adott verziót is beolvashat név és verziószám alapján.

### <a name="register-a-dataset-version"></a>Adatkészlet verziójának regisztrálása

A következő kód regisztrálja az `titanic_ds` adatkészlet új verzióját úgy, hogy a `create_new_version` paramétert a `True`értékre állítja be. Ha nincs regisztrálva létező `titanic_ds` adatkészlet a munkaterületen, a kód létrehoz egy új adatkészletet a `titanic_ds` névvel, és beállítja annak verzióját 1-re.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Adatkészlet lekérése név szerint

Alapértelmezés szerint a (z `Dataset` [) osztály get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) metódusa a munkaterületen regisztrált adatkészlet legújabb verzióját adja vissza. 

A következő kód az `titanic_ds` adatkészlet 1. verzióját kapja meg.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>A verziószámozás ajánlott gyakorlata

Amikor létrehoz egy adatkészlet-verziót, az adatokat *nem* kell külön másolatot készítenie a munkaterületről. Mivel az adatkészletek a Storage szolgáltatásban lévő adatokra hivatkoznak, egyetlen, a tárolási szolgáltatás által felügyelt igazság-forrással rendelkezik.

>[!IMPORTANT]
> Ha az adatkészlet által hivatkozott adatokat felülírják vagy törlik, az adatkészlet egy adott verziójának meghívása *nem* tér át a módosításra.

Amikor adatokat tölt be egy adatkészletből, az adatkészlet által hivatkozott aktuális adattartalom mindig betöltődik. Ha meg szeretné győződni arról, hogy az egyes adatkészlet-verziók reprodukálva vannak, javasoljuk, hogy ne módosítsa az adatkészlet verziója által hivatkozott adattartalmat. Ha az új adatok bekerülnek, mentse az új adatfájlokat egy külön adatmappába, majd hozzon létre egy új adatkészlet-verziót, amely az adott új mappából származó adatokat tartalmazza.

A következő rendszerkép és mintakód mutatja az adatmappák strukturálása és az ezen mappákra hivatkozó adatkészlet-verziók létrehozásának ajánlott módját:

![Mappa szerkezete](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-a-pipeline-output-dataset"></a>A folyamat kimeneti adatkészletének verziója

Az adatkészleteket minden Machine Learning folyamat lépéseinek bemenete és kimenete használatával is használhatja. Ha Újrafuttatja a folyamatokat, az egyes folyamatokhoz tartozó lépések kimenete új adatkészlet-verzióként lesz regisztrálva.

Mivel Machine Learning folyamatok minden egyes lépés kimenetét egy új mappába töltik fel, valahányszor a folyamat újratöltődik, a verziószámmal ellátott kimeneti adatkészletek reprodukálva lesznek. További információ a [folyamatok adatkészletekről](how-to-create-your-first-pipeline.md#steps).

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Adathalmazok nyomon követése kísérletekben

Az egyes Machine Learning kísérleteknél könnyedén nyomon követheti a bemenetként használt adatkészleteket a kísérlet `Run` objektumon keresztül.

A következő kód a [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) metódus használatával követi nyomon, hogy mely bemeneti adatkészletek lettek használva a kísérlet futtatásával:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

A `input_datasets` from kísérletek a használatával https://ml.azure.com/is megtalálhatók. 

Az alábbi képen látható, hol található egy kísérlet bemeneti adatkészlete Azure Machine Learning Studióban. Ebben a példában lépjen a **kísérletek** ablaktáblára, és nyissa meg a kísérlet adott futtatásának **Tulajdonságok** lapját `keras-mnist`.

![Bemeneti adatkészletek](./media/how-to-version-track-datasets/input-datasets.png)

A következő kód használatával regisztrálhat modelleket adatkészletekkel:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

A regisztráció után megtekintheti az adatkészlethez regisztrált modellek listáját a Python használatával vagy a következővel https://ml.azure.com/:.

A következő nézet az **adatkészletek** ablaktábla **eszközök**területén található. Válassza ki az adatkészletet, majd válassza a **modellek** fület az adatkészletben regisztrált modellek listájához. 

![Bemeneti adatkészletek modelljei](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>További lépések

* [Betanítás adathalmazok használatával](how-to-train-with-datasets.md)
* [További minta-adathalmazi jegyzetfüzetek](https://aka.ms/dataset-tutorial)
