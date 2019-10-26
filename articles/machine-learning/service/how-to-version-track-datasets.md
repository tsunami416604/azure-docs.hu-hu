---
title: Adatkészlet verziószámozása
titleSuffix: Azure Machine Learning service
description: Ismerje meg az adathalmazok verziójának és a verziószámozásnak a gépi tanulási folyamatokkal való használatának ajánlott eljárásait
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: sihhu
ms.reviewer: nibaccam
ms.date: 10/25/2019
ms.custom: ''
ms.openlocfilehash: a361800623796cb3bc26a47c4f8f532503836124
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902001"
---
# <a name="version-and-track-datasets-in-experiments"></a>Adatkészletek verziója és nyomon követése kísérletekben

Ebben a útmutatóban megismerheti, hogyan lehet a reprodukálhatóságot Azure Machine Learning adatkészletek verzióját és nyomon követni. Az adatkészlet verziószámozása lehetővé teszi az adathalmazok könyvjelzővel való megjelölését, így a későbbi kísérleteknél az adatkészlet adott verzióját is alkalmazhatja.

A verziószámozást általában a következő esetekben érdemes figyelembe venni:

* Ha új adatgyűjtési lehetőség áll rendelkezésre.
* Ha különböző adatelőkészítési vagy szolgáltatás-mérnöki módszereket alkalmaz.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a következő útmutatók szükségesek:

- A [Azure Machine learning SDK for Python telepítve](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), amely tartalmazza a [azureml-adatkészletek](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset?view=azure-ml-py) csomagot.
    
- Egy [Azure Machine learning munkaterület](concept-workspace.md). Kérje le a meglévőt a következő kóddal, vagy [hozzon létre egy új munkaterületet](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Egy [Azure Machine learning adatkészlet](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Adatkészlet-verziók regisztrálása és lekérése

Az adatkészlet regisztrálása lehetővé teszi, hogy a kísérleteken és a munkatársakon keresztül verziót, újrahasznosítsa és megossza azokat. Több adatkészletet is regisztrálhat ugyanazon a néven, és egy adott verziót is beolvashat név és verziószám alapján.

### <a name="register-a-dataset-version"></a>Adatkészlet verziójának regisztrálása

A következő kód regisztrálja az adatkészlet új verzióját, `titanic_ds`a `create_new_version` paraméter `True`értékre való beállításával. Ha nincs meglévő `titanic_ds` regisztrálva a munkaterületen, a létrehoz egy új adatkészletet, amelynek a neve `titanic_ds`, és beállítja annak verzióját 1-re.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Adatkészlet lekérése név szerint

Alapértelmezés szerint a `Dataset` osztály [get_by_name ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) metódusa a munkaterületen regisztrált adatkészlet legújabb verzióját adja vissza. 

A következő kód beolvassa a `titanic_ds` adatkészlet 1. verzióját.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>A verziószámozás ajánlott gyakorlata

Adatkészlet verziójának létrehozásakor **nem** kell külön másolatot készítenie az adatról a munkaterületről. Az adatkészletek a tárolási szolgáltatásban lévő adatokra hivatkoznak, így csak egyetlen, a tárolási szolgáltatás által felügyelt igazság van. 

>[!IMPORTANT]
> Ha az adatkészlet által hivatkozott adatokat felülírják vagy törlik, az adatkészlet egy adott verziójának meghívása nem tudja visszaállítani a változást. 

Amikor adatokat tölt be egy adatkészletből, az mindig az adatkészlet által hivatkozott aktuális adattartalmat fogja betölteni. Ha biztosítani szeretné az egyes adatkészlet-verziók reprodukálhatóságát, javasoljuk, hogy ne módosítsa az adatkészlet verziója által hivatkozott adattartalmat. Ha az új adatok bekerülnek, mentse az új adatfájlokat egy külön adatmappába, és hozzon létre egy új adatkészlet-verziót, amely az új adatmappa adatait tartalmazza.

Az alábbi ábra és mintakód mutatja az adatmappák strukturálása ajánlott módját, valamint az ezen mappákra hivatkozó adatkészlet-verziók létrehozását.

![Mappa szerkezete](media/how-to-version-datasets/folder-image.png)

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

Az adatkészletet az egyes Machine learning-(ML-) folyamatok bemenetének és kimenetének megfelelően használhatja. Amikor Újrafuttatja a folyamatokat, az egyes folyamatok lépéseinek kimenete új adatkészlet-verzióként lesz regisztrálva. 

Mivel a ML-folyamatok az egyes lépések kimenetét egy új mappába töltik fel a folyamat minden újrafuttatásakor, a verziószámmal ellátott kimeneti adatkészletek reprodukálva lesznek.

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-datasets-in-experiments"></a>Adathalmazok nyomon követése kísérletekben

Minden gépi tanulási kísérlet esetében könnyedén nyomon követheti a regisztrált modell `Run` objektumában bemenetként használt adatkészleteket.

A következő kód használatával regisztrálhat modelleket adatkészletekkel.

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

A regisztráció után megtekintheti az adatkészletben regisztrált modellek listáját a Python vagy a [munkaterület kezdőlapja](https://ml.azure.com/)segítségével.

A következő kód a [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-details--) metódus használatával követi nyomon, hogy mely bemeneti adatkészletek lettek használva a kísérlet futtatásához.

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
train_dataset = inputs[0]['dataset']

# list the files referenced by train_dataset
train_dataset.to_path()
```

A kísérletek `input_datasets` a munkaterület kezdőlapja [(előzetes verzió)](https://ml.azure.com/)használatával is megtalálhatja. 

Az alábbi képen látható, hol található a kísérlet bemeneti adatkészlete a munkaterület kezdőlapján. Ebben a példában navigáljon a **kísérletek** ablaktáblához, és nyissa meg a kísérlet adott futtatásához tartozó **tulajdonságok** lapot, `keras-mnist`. 

![Bemeneti adatkészletek](media/how-to-version-datasets/input-datasets.png)

Az adatkészletet használó modelleket a munkaterület kezdőlapján is megtalálhatja. A következő nézet az adatkészletek paneljén, az eszközök területen található. Válassza ki az adatkészletet, és navigáljon a Models (modellek) lapra az adott adatkészletet használó modellek listájához. 

![Bemeneti adatkészletek modelljei](media/how-to-version-datasets/dataset-models.png)

## <a name="next-steps"></a>Következő lépések

* [Adatkészletek betanítása](how-to-train-with-datasets.md).
* [Több minta jegyzetfüzet az adatkészletekhez](https://aka.ms/dataset-tutorial).
