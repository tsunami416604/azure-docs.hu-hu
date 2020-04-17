---
title: Gépi tanulási folyamat YAML
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan definiálhat gépi tanulási folyamatot YAML-fájl használatával. A YAML-folyamatdefiníciók az Azure CLI gépi tanulási bővítményével használatosak.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 40e6d7f3d9c28708c5adec26ddc3c0463e75adc0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529705"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Gépi tanulási folyamatok definiálása a YAML-ben

Ismerje meg, hogyan definiálhatja a gépi tanulási folyamatokat a [YAML-ben.](https://yaml.org/) Az Azure CLI gépi tanulási bővítményének használatakor a folyamattal kapcsolatos parancsok közül sok egy YAML-fájlt vár, amely meghatározza a folyamatot.

Az alábbi táblázat felsorolja, hogy mi támogatott és jelenleg nem támogatott a YAML-ben lévő folyamatok definiálásakor:

| Lépés típusa | Támogatott? |
| ----- | :-----: |
| PythonScriptStep | Igen |
| AdlaStep | Igen |
| AzureBatchStep | Igen |
| DatabricksStep | Igen |
| DataTransferStep | Igen |
| AutoMLStep | Nem |
| HyperDriveStep | Nem |
| ModuleStep | Igen |
| MPIStep | Nem |
| EstimatorStep (EstimatorStep) | Nem |

## <a name="pipeline-definition"></a>Csővezeték definíciója

A folyamatdefiníció a következő kulcsokat használja, amelyek megfelelnek a [Pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) osztálynak:

| YAML-kulcs | Leírás |
| ----- | ----- |
| `name` | A csővezeték leírása. |
| `parameters` | A folyamat paramétere(i). |
| `data_reference` | Azt határozza meg, hogy az adatokat hogyan és hol kell elérhetővé tenni egy futtatáskor. |
| `default_compute` | Alapértelmezett számítási cél, ahol a folyamat összes lépése fut. |
| `steps` | A folyamatban használt lépések. |

## <a name="parameters"></a>Paraméterek

A `parameters` szakasz a következő kulcsokat használja, amelyek megfelelnek a [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) osztálynak:

| YAML-kulcs | Leírás |
| ---- | ---- |
| `type` | A paraméter értéktípusa. Az érvényes `string` `int`típusok `float` `bool`a `datapath`, , , , vagy . |
| `default` | Az alapértelmezett érték. |

Minden paraméter neve. A következő YAML-kódrészlet például három paramétert `DataPathParameter`határoz `NodeCountParameter`meg, a ( y) és `NumIterationsParameter`a :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Adatreferencia

A `data_references` szakasz a következő kulcsokat használja, amelyek megfelelnek a [DataReference:](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)

| YAML-kulcs | Leírás |
| ----- | ----- |
| `datastore` | A hivatkozott adattár. |
| `path_on_datastore` | Az adathivatkozás háttértárolójának relatív elérési útja. |

Minden adathivatkozás egy kulcsban található. A következő YAML-kódrészlet például a következő, a kulcsban tárolt adathivatkozást határoz `employee_data`meg:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Lépések

A lépések számítási környezetet határoznak meg a környezetben futtatandó fájlokkal együtt. A lépés típusának meghatározásához `type` használja a kulcsot:

| Lépés típusa | Leírás |
| ----- | ----- |
| `AdlaStep` | U-SQL-parancsfájlt futtat az Azure Data Lake Analytics szolgáltatással. Megfelel az [AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) osztálynak. |
| `AzureBatchStep` | Feladatok at futtatja az Azure Batch használatával. Megfelel az [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) osztály. |
| `DatabricsStep` | Databricks-jegyzetfüzet, Python-parancsfájl vagy JAR hozzáadása. Megfelel a [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) osztálynak. |
| `DataTransferStep` | Adatok átvitele a tárolási lehetőségek között. Megfelel a [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) osztálynak. |
| `PythonScriptStep` | Python-parancsfájlt futtat. A [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) osztálynak felel meg. |

### <a name="adla-step"></a>ADLA lépés

| YAML-kulcs | Leírás |
| ----- | ----- |
| `script_name` | Az U-SQL parancsfájl neve (a `source_directory`hoz viszonyítva). |
| `compute_target` | Az Azure Data Lake számítási cél ehhez a lépéshez használható. |
| `parameters` | A folyamat [paraméterei.](#parameters) |
| `inputs` | A bemenetek lehetnek [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)vagy [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | A kimenetek lehetnek [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) vagy [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Könyvtár, amely tartalmazza a forgatókönyvet, szerelvények, stb |
| `priority` | Az aktuális feladathoz használandó prioritási érték. |
| `params` | Név-érték párok szótára. |
| `degree_of_parallelism` | A feladathoz használt párhuzamosság mértéke. |
| `runtime_version` | A Data Lake Analytics-motor futásidejű verziója. |
| `allow_reuse` | Azt határozza meg, hogy a lépés újra felhasználja-e a korábbi eredményeket, amikor újra futtatja ugyanazokat a beállításokat. |

A következő példa egy ADLA lépésdefiníciót tartalmaz:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Az Azure Batch-lépés

| YAML-kulcs | Leírás |
| ----- | ----- |
| `compute_target` | Az Azure Batch számítási cél ehhez a lépéshez használható. |
| `inputs` | A bemenetek lehetnek [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)vagy [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | A kimenetek lehetnek [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) vagy [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | A modulbinárisfájlokat, végrehajtható fájlokat, szerelvényeket stb. |
| `executable` | A feladat részeként futtatandó parancs/végrehajtható fájl neve. |
| `create_pool` | Logikai jelző annak jelzésére, hogy létre kell-e hozni a készletet a feladat futtatása előtt. |
| `delete_batch_job_after_finish` | Logikai jelző annak jelzésére, hogy törli-e a feladatot a Batch-fiókból a befejezés után. |
| `delete_batch_pool_after_finish` | Logikai jelző annak jelzésére, hogy a feladat befejezése után törölje-e a készletet. |
| `is_positive_exit_code_failure` | Logikai jelző, amely jelzi, hogy a feladat sikertelen-e, ha a feladat pozitív kóddal lép ki. |
| `vm_image_urn` | Ha `create_pool` `True`van, és `VirtualMachineConfiguration`a virtuális gép a . |
| `pool_id` | Annak a készletnek az azonosítója, amelyen a feladat futni fog. |
| `allow_reuse` | Azt határozza meg, hogy a lépés újra felhasználja-e a korábbi eredményeket, amikor újra futtatja ugyanazokat a beállításokat. |

A következő példa egy Azure Batch-lépésdefiníciót tartalmaz:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Databricks lépés

| YAML-kulcs | Leírás |
| ----- | ----- |
| `compute_target` | Az Azure Databricks számítási cél ehhez a lépéshez használható. |
| `inputs` | A bemenetek lehetnek [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)vagy [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | A kimenetek lehetnek [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) vagy [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | A név databricks ehhez a futtatáshoz. |
| `source_directory` | A parancsfájlt és más fájlokat tartalmazó könyvtár. |
| `num_workers` | A Databricks dolgozók statikus száma fürtöt futtat. |
| `runconfig` | A `.runconfig` fájl elérési útja. Ez a fájl a [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) osztály YAML-ábrázolása. A fájl szerkezetéről további információt a [runconfigschema.json fájlban talál.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) |
| `allow_reuse` | Azt határozza meg, hogy a lépés újra felhasználja-e a korábbi eredményeket, amikor újra futtatja ugyanazokat a beállításokat. |

A következő példa egy Databricks lépést tartalmaz:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Adatátviteli lépés

| YAML-kulcs | Leírás |
| ----- | ----- |
| `compute_target` | Az Azure Data Factory számítási cél, amelyet ehhez a lépéshez kell használni. |
| `source_data_reference` | Bemeneti kapcsolat, amely az adatátviteli műveletek forrásaként szolgál. A támogatott értékek: [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)vagy [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Bemeneti kapcsolat, amely az adatátviteli műveletek célhelyeként szolgál. A támogatott értékek a [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) és [az OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Azt határozza meg, hogy a lépés újra felhasználja-e a korábbi eredményeket, amikor újra futtatja ugyanazokat a beállításokat. |

A következő példa egy adatátviteli lépést tartalmaz:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Python-parancsfájl lépése

| YAML-kulcs | Leírás |
| ----- | ----- |
| `inputs` | A bemenetek lehetnek [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)vagy [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | A kimenetek lehetnek [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) vagy [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | A Python-parancsfájl neve (a következőhöz `source_directory`viszonyítva). |
| `source_directory` | Könyvtár, amely tartalmazza a parancsfájlt, Conda környezet, stb. |
| `runconfig` | A `.runconfig` fájl elérési útja. Ez a fájl a [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) osztály YAML-ábrázolása. A fájl szerkezetéről további információt a [runconfig.json fájlban talál.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) |
| `allow_reuse` | Azt határozza meg, hogy a lépés újra felhasználja-e a korábbi eredményeket, amikor újra futtatja ugyanazokat a beállításokat. |

A következő példa egy Python-parancsfájl-lépést tartalmaz:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Több lépésből áll a folyamat 

| YAML-kulcs | Leírás |
| ----- | ----- |
| `steps` | Egy vagy több PipelineStep definíció sorrendje. Ne feledje, hogy `destination` `outputs` az egyik lépés `inputs` lett a kulcsa a .| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Ütemezések

A folyamat ütemezésének meghatározásakor lehet adattár által aktivált vagy ismétlődő időintervallum alapján. Az ütemezés definiálására a következő kulcsok szolgálnak:

| YAML-kulcs | Leírás |
| ----- | ----- |
| `description` | Az ütemterv leírása. |
| `recurrence` | Ismétlődési beállításokat tartalmaz, ha az ütemezés ismétlődő. |
| `pipeline_parameters` | A folyamat által megkövetelt paraméterek. |
| `wait_for_provisioning` | Megkell-e várni az ütemezés kiépítésével. |
| `wait_timeout` | Az időtúllépés előtti várakozási idő másodperceinek száma. |
| `datastore_name` | A módosított/hozzáadott blobok figyelésére szolgáló adattár. |
| `polling_interval` | Mennyi ideig, percek ben a módosított/hozzáadott blobok lekérdezése között. Alapértelmezett érték: 5 perc. Csak az adattár ütemezése támogatott. |
| `data_path_parameter_name` | A módosított blobelérési úttal beállított adatelérési út paraméterének neve. Csak az adattár ütemezése támogatott. |
| `continue_on_step_failure` | Ha egy lépés sikertelen, folytassa-e a többi lépés végrehajtását a benyújtott PipelineRun-ban. Ha rendelkezésre áll, `continue_on_step_failure` felülírja a csővezeték beállítását.
| `path_on_datastore` | Választható. Az elérési út az adattárban a módosított/hozzáadott blobok figyelésére. Az elérési út az adattár tárolója alatt van, így az`path_on_datastore`ütemezési figyelők által figyelt tényleges elérési út a container/ . Ha nincs, az adattártároló figyelése. A részmappában végzett kiegészítéseket/módosításokat a `path_on_datastore` rendszer nem figyeli. Csak az adattár ütemezése támogatott. |

A következő példa egy adattár által aktivált ütemezés definícióját tartalmazza:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

**Ismétlődő ütemezés**definiálásakor a következő `recurrence`billentyűket használja a következő gombok között:

| YAML-kulcs | Leírás |
| ----- | ----- |
| `frequency` | Az ütemezés ismétlődésének gyakran. Az érvényes `"Minute"` `"Hour"`értékek `"Day"` `"Week"`a `"Month"`, , , , vagy . |
| `interval` | Milyen gyakran tüzek a menetrend. Az egész érték azon időegységek száma, amelyekkel meg kell várni, amíg az ütemezés újra aktiválódik. |
| `start_time` | Az ütemezés kezdési időpontja. Az `YYYY-MM-DDThh:mm:ss`érték karakterláncformátuma . Ha nincs megadva kezdési időpont, az első számítási feladatok azonnal futnak, és a jövőbeli számítási feladatok az ütemezés alapján futnak. Ha a kezdési időpont a múltban van, az első számítási feladat a következő számított futási időpontban fut. |
| `time_zone` | A kezdési időpont időzónája. Ha nincs megadva időzóna, a használat utc lesz használva. |
| `hours` | Ha `frequency` `"Day"` van `"Week"`vagy, akkor a folyamat futtatásának napjaként megadhat egy vagy több, vesszővel elválasztott 0 és 23 között lévő egész kétes számokat. Csak `time_of_day` `hours` vagy `minutes` használható. |
| `minutes` | Ha `frequency` `"Day"` van `"Week"`vagy, akkor a folyamat futtatásának órájában egy vagy több, vesszővel elválasztott 0 és 59 között több egész szám adható meg. Csak `time_of_day` `hours` vagy `minutes` használható. |
| `time_of_day` | Ha `frequency` `"Day"` van `"Week"`vagy, megadhatja az ütemezés futtatásának napszakát. Az `hh:mm`érték karakterláncformátuma . Csak `time_of_day` `hours` vagy `minutes` használható. |
| `week_days` | Ha `frequency` `"Week"`van, akkor megadhat egy vagy több napot vesszővel elválasztva, amikor az ütemezésnek futnia kell. Az érvényes `"Monday"` `"Tuesday"`értékek `"Wednesday"` `"Thursday"`a `"Friday"` `"Saturday"`következők: `"Sunday"`, , , , , és . |

A következő példa egy ismétlődő ütemezés definícióját tartalmazza:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [használhatja a CLI-bővítményt az Azure Machine Learninghez.](reference-azure-machine-learning-cli.md)
