---
title: ML-folyamatok közzététele
titleSuffix: Azure Machine Learning
description: Gépi tanulási munkafolyamatok futtatása gépi tanulási folyamatokkal és a Pythonhoz készült Azure Machine Learning SDK-val.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.openlocfilehash: ddc8186e85001a2a3ed2ed9f57b8f025133ef16a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897772"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Gépi tanulási folyamatok közzététele és nyomon követése



Ez a cikk bemutatja, hogyan oszthat meg egy gépi tanulási folyamatot kollégáival vagy ügyfeleivel.

A gépi tanulási folyamatok a gépi tanulási feladatok újrafelhasználható munkafolyamatai. A folyamatok egyik előnye a nagyobb együttműködés. Emellett olyan folyamatokat is használhat, amelyek lehetővé teszik az ügyfelek számára az aktuális modell használatát, amikor új verzióval dolgozik. 

## <a name="prerequisites"></a>Előfeltételek

* [Azure Machine learning munkaterület](how-to-manage-workspace.md) létrehozása az összes folyamat erőforrásának tárolásához

* [Állítsa be a fejlesztési környezetet](how-to-configure-environment.md) az Azure Machine learning SDK telepítéséhez, vagy használjon olyan [Azure Machine learning számítási példányt](concept-compute-instance.md) , amelyen már telepítve van az SDK

* Hozzon létre és futtasson egy gépi tanulási folyamatot, például a következő [oktatóanyaggal: Azure Machine learning folyamat létrehozása a Batch pontozáshoz](tutorial-pipeline-batch-scoring-classification.md). További lehetőségek: [Machine learning-folyamatok létrehozása és futtatása Azure Machine learning SDK-val](how-to-create-your-first-pipeline.md)

## <a name="publish-a-pipeline"></a>Folyamat közzététele

Ha már rendelkezik egy folyamattal, akkor közzétehet egy folyamatot, hogy az a különböző bemenetekkel fusson. Ahhoz, hogy egy már közzétett folyamat REST-végpontja fogadja a paramétereket, konfigurálnia kell a folyamatot úgy, hogy `PipelineParameter` objektumokat használjon a változó argumentumokhoz.

1. A folyamat paramétereinek létrehozásához használjon egy alapértelmezett értéket használó [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) objektumot.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Adja hozzá ezt az `PipelineParameter` objektumot paraméterként a folyamat lépéseihez a következőképpen:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Tegye közzé ezt a folyamatot, amely fogadja a paramétert a meghívásakor.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Közzétett folyamat futtatása

Minden közzétett folyamat REST-végponttal rendelkezik. A folyamat végpontja segítségével bármely külső rendszerből elindíthatja a folyamat futtatását, beleértve a nem Python-ügyfeleket is. Ez a végpont lehetővé teszi a "felügyelt ismételhetőség" használatát a Batch-pontozási és-átképzési forgatókönyvekben.

Az előző folyamat futtatásának meghívásához egy Azure Active Directory hitelesítési fejléc tokenre van szükség. Az ilyen jogkivonatok lekérése a [AzureCliAuthentication osztály](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py&preserve-view=true) referenciájában és a Azure Machine learning jegyzetfüzetben történő [hitelesítésben](https://aka.ms/pl-restep-auth) olvasható.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Verzióval ellátott folyamat végpontjának létrehozása

A folyamat végpontja több közzétett folyamattal is létrehozható. Ez egy rögzített REST-végpontot biztosít, amellyel megismételheti és frissítheti a ML-folyamatokat.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>Feladatok elküldése egy folyamat végpontjának

Elküldheti a feladatokat egy folyamat-végpont alapértelmezett verziójára:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

A feladatokat egy adott verzióra is elküldheti:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Ugyanezt a REST API használatával is elvégezheti:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Közzétett folyamatok használata a Studióban

A közzétett folyamatokat a studióból is futtathatja:

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. [Megtekintheti a munkaterületet](how-to-manage-workspace.md#view).

1. A bal oldalon válassza a **végpontok**lehetőséget.

1. A felső részen válassza a **folyamat-végpontok**lehetőséget.
 ![a Machine learning által közzétett folyamatok listája](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Válasszon ki egy adott folyamatot a folyamat végpontjának korábbi futtatásainak futtatásához, felhasználásához vagy áttekintéséhez.

## <a name="disable-a-published-pipeline"></a>Közzétett folyamat letiltása

Ha el szeretné rejteni egy folyamatot a közzétett folyamatok listájáról, tiltsa le azt a Studióban vagy az SDK-ban:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

A használatával újra engedélyezheti `p.enable()` . További információ: PublishedPipeline- [osztály](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py&preserve-view=true) referenciája.

## <a name="next-steps"></a>Következő lépések

- [Ezeket a Jupyter-jegyzetfüzeteket a githubon](https://aka.ms/aml-pipeline-readme) a gépi tanulási folyamatok további megismeréséhez használhatja.
- Tekintse meg a [azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py&preserve-view=true) csomag és a azureml-folyamatok – [STEPs](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py&preserve-view=true) csomag SDK-referenciáját.
- A folyamatokkal kapcsolatos hibakeresési és hibaelhárítási tippekért lásd: [útmutató](how-to-debug-pipelines.md) .
