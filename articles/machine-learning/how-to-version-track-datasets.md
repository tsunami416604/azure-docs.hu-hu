---
title: Adatkészlet verziószámozása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan lehet a legjobb verzióaz adatkészletek és hogyan verziószámozás gépi tanulási folyamatok.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.custom: ''
ms.openlocfilehash: acbd2e3ba756255cbc69ae8a7b7ad62d7a1c1c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528472"
---
# <a name="version-and-track-datasets-in-experiments"></a>Verzió- és nyomon követhető adatkészletek a kísérletekben
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan verziózhatja és követheti nyomon az Azure Machine Learning-adatkészleteket a reprodukálhatóság érdekében. Az adatkészlet-verziószámozás sal az adatok állapotának könyvjelzővel történő alkalmazásával az adatkészlet egy adott verzióját alkalmazhatja a későbbi kísérletekhez.

Tipikus verziószámozási forgatókönyvek:

* Ha új adatok állnak rendelkezésre az átképzéshez
* Különböző adatelőkészítési vagy szolgáltatástervezési megközelítések alkalmazásakor

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következőkre van szükség:

- [Az Azure Machine Learning SDK python-hoz telepítve.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) Ez az SDK tartalmazza az [azureml-adatkészletek](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) csomag.
    
- Egy [Azure Machine Learning-munkaterület.](concept-workspace.md) A következő kód futtatásával egy meglévőt beolvasni, vagy [új munkaterületet létrehozni.](how-to-manage-workspace.md)

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Egy [Azure Machine Learning-adatkészlet.](how-to-create-register-datasets.md)

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Adatkészletverziók regisztrálása és beolvasása

Az adatkészlet regisztrálásával verziószámmal verziók, újrafelhasználhatja és megoszthatja azt a kísérletek ben és a munkatársakkal. Több adatkészletet is regisztrálhat ugyanazon a néven, és lekérhet egy adott verziót név és verziószám szerint.

### <a name="register-a-dataset-version"></a>Adatkészlet-verzió regisztrálása

A következő kód regisztrálja az `titanic_ds` adatkészlet új `create_new_version` verzióját `True`a paraméter beállításával. Ha nincs regisztrálva `titanic_ds` meglévő adatkészlet a munkaterületen, a kód létrehoz egy `titanic_ds` új adatkészletet a névvel, és 1-re állítja a verzióját.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```
Az adatkészlet új verzióját is regisztrálhatja a 

### <a name="retrieve-a-dataset-by-name"></a>Adatkészlet beolvasása név szerint

Alapértelmezés szerint az osztály [get_by_name()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) metódusa a `Dataset` munkaterülethez regisztrált adatkészlet legújabb verzióját adja vissza. 

A következő kód az `titanic_ds` adatkészlet 1.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Ajánlott verziószámozás

Adatkészlet-verzió létrehozásakor *nem* hoz létre további adatokat a munkaterülettel. Mivel az adatkészletek a tárolási szolgáltatásban lévő adatokra mutató hivatkozások, egyetlen igazságforrással rendelkezik, amelyet a tárolási szolgáltatás kezel.

>[!IMPORTANT]
> Ha az adatkészlet által hivatkozott adatok felülíródnak vagy törlődnek, az adatkészlet egy adott verziójának hívása *nem* állítja vissza a módosítást.

Amikor adatokat tölt be egy adatkészletből, az adatkészlet által hivatkozott aktuális adattartalom mindig betöltődik. Ha meg szeretne győződni arról, hogy minden adatkészlet-verzió reprodukálható, azt javasoljuk, hogy ne módosítsa az adatkészlet verziója által hivatkozott adattartalmat. Amikor új adatok érkeznek, mentse az új adatfájlokat egy külön adatmappába, majd hozzon létre egy új adatkészlet-verziót, amely az adott új mappából származó adatokat tartalmazza.

Az alábbi kép- és mintakód az adatmappák strukturálásának és a mappákra hivatkozó adatkészlet-verziók létrehozásának ajánlott módját mutatja be:

![Mappastruktúra](./media/how-to-version-track-datasets/folder-image.png)

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

## <a name="version-a-pipeline-output-dataset"></a>Folyamatkimeneti adatkészlet verziója

Az adatkészletet az egyes Machine Learning-folyamatlépésbemeneti és kimeneti bemeneteként és kimeneteként is használhatja. A folyamatok újrafuttatásakor az egyes folyamatlépések kimenete új adatkészlet-verzióként lesz regisztrálva.

Mivel a Machine Learning-folyamatok minden lépés kimenetét egy új mappába helyezik, minden alkalommal, amikor a folyamat újrafut, a verziózott kimeneti adatkészletek reprodukálhatók. További információ [a folyamatokadatkészleteiről.](how-to-create-your-first-pipeline.md#steps)

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

## <a name="track-datasets-in-experiments"></a>Adatkészletek nyomon követése kísérletekben

Minden egyes Machine Learning-kísérlet, könnyen nyomon követheti a használt `Run` adatkészletek a kísérlet objektumon keresztül a bemeneti.

A következő kód [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) a kísérletfuttatásához használt bemeneti adatkészletek nyomon követésére használja a módszert:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

A segítségével is `input_datasets` megtalálhatja a https://ml.azure.com/kísérleteket. 

Az alábbi képen látható, hogy hol található egy kísérlet bemeneti adatkészlete az Azure Machine Learning studióban. Ebben a példában nyissa meg a **Kísérletek** ablaktáblát, és nyissa `keras-mnist`meg a **Tulajdonságok** lapot a kísérlet egy adott futtatásához.

![Bemeneti adatkészletek](./media/how-to-version-track-datasets/input-datasets.png)

Az alábbi kóddal regisztrálhatja a modelleket adatkészletekkel:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

A regisztráció után megtekintheti az adatkészlettel regisztrált modellek listáját a https://ml.azure.com/Python használatával, vagy lépjen a rendszerbe.

A következő nézet az Eszközök ablaktábla **Adatkészletek** **ablaktáblájából származik.** Jelölje ki az adatkészletet, majd válassza a **Modellek** lapot az adatkészlethez regisztrált modellek listájához. 

![Bemeneti adatkészletek modelljei](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>További lépések

* [Betanítás adathalmazok használatával](how-to-train-with-datasets.md)
* [További mintaadatkészlet-jegyzetfüzetek](https://aka.ms/dataset-tutorial)
