---
title: Machine Learning folyamat YAML
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan határozhat meg gépi tanulási folyamatot egy YAML-fájl használatával. A YAML-folyamat definíciói az Azure CLI Machine learning bővítménnyel együtt használhatók.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: a677aaa891e21f4c9eeda02eebcb94e9d79a55ad
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368825"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Gépi tanulási folyamatok definiálása a YAML-ben

Ismerje meg, hogyan határozhatja meg a gépi tanulási folyamatokat a [YAML](https://yaml.org/)-ben. Ha az Azure CLI Machine learning-bővítményét használja, a folyamattal kapcsolatos számos parancs egy YAML-fájlt vár, amely meghatározza a folyamatot.

A következő táblázat felsorolja, hogy mi az, és jelenleg nem támogatott a folyamat definiálásakor a YAML-ben:

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
| EstimatorStep | Nem |

## <a name="pipeline-definition"></a>Folyamat definíciója

A folyamat definíciója a következő kulcsokat használja, amelyek megfelelnek a [folyamatok](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) osztálynak:

| YAML kulcs | Leírás |
| ----- | ----- |
| `name` | A folyamat leírása. |
| `parameters` | Paraméter (ek) a folyamathoz. |
| `data_reference` | Azt határozza meg, hogyan és hol kell elérhetővé tenni az adathalmazt a futtatásban. |
| `default_compute` | Alapértelmezett számítási cél, amely a folyamat összes lépését futtatja. |
| `steps` | A folyamatban használt lépések. |

## <a name="parameters"></a>Paraméterek

A `parameters` szakasz a következő kulcsokat használja, amelyek megfelelnek a [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) osztálynak:

| YAML kulcs | Leírás |
| ---- | ---- |
| `type` | A paraméter értéktípus. Az érvényes típusok: `string`, `int`, `float`, `bool`vagy `datapath`. |
| `default` | Az alapértelmezett érték. |

Minden paraméter neve. Például a következő YAML-kódrészlet három, `NumIterationsParameter`, `DataPathParameter`és `NodeCountParameter`nevű paramétert határoz meg:

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

A `data_references` szakasz a következő kulcsokat használja, amelyek megfelelnek a [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py):

| YAML kulcs | Leírás |
| ----- | ----- |
| `datastore` | Az adattár, amelyre hivatkozni kell. |
| `path_on_datastore` | Az adathivatkozáshoz tartozó tárolt tároló relatív elérési útja. |

Az egyes adathivatkozások egy kulcsban találhatók. Például a következő YAML-kódrészlet egy `employee_data`nevű kulcsban tárolt adathivatkozást definiál:

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

A lépések meghatározzák a számítási környezetet, valamint a környezetben futtatandó fájlokat. A lépés típusának meghatározásához használja a `type` kulcsot:

| Lépés típusa | Leírás |
| ----- | ----- |
| `AdlaStep` | Egy U-SQL-szkriptet futtat Azure Data Lake Analytics. Megfelel a [AdlaStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) osztálynak. |
| `AzureBatchStep` | A feladatokat Azure Batch használatával futtatja. Megfelel a [AzureBatchStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) osztálynak. |
| `DatabricsStep` | Databricks-jegyzetfüzet, Python-parancsfájl vagy JAR hozzáadására szolgál. Megfelel a [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) osztálynak. |
| `DataTransferStep` | Adatátvitel a tárolási lehetőségek között. Megfelel a [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) osztálynak. |
| `PythonScriptStep` | Egy Python-szkriptet futtat. Megfelel a [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) osztálynak. |

### <a name="adla-step"></a>ADLA lépés

| YAML kulcs | Leírás |
| ----- | ----- |
| `script_name` | Az U-SQL-parancsfájl neve (a `source_directory`hoz képest). |
| `compute_target` | Az ehhez a lépéshez használt Azure Data Lake számítási cél. |
| `parameters` | A folyamathoz tartozó [Paraméterek](#parameters) . |
| `inputs` | A bemenetek lehetnek [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)vagy [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | A kimenetek lehetnek [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) vagy [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | A parancsfájlt, szerelvényeket stb. tartalmazó könyvtár |
| `priority` | Az aktuális feladathoz használandó prioritási érték. |
| `params` | Név-érték párok szótára. |
| `degree_of_parallelism` | A feladatokhoz használandó párhuzamosság foka. |
| `runtime_version` | A Data Lake Analytics motor futtatókörnyezet-verziója. |
| `allow_reuse` | Meghatározza, hogy a lépés újra fel kell-e használni az előző eredményeket, ha ugyanazokkal a beállításokkal futnak újra. |

A következő példa egy ADLA lépés definícióját tartalmazza:

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

### <a name="azure-batch-step"></a>Azure Batch lépés

| YAML kulcs | Leírás |
| ----- | ----- |
| `compute_target` | Az ehhez a lépéshez használt Azure Batch számítási cél. |
| `inputs` | A bemenetek lehetnek [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)vagy [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | A kimenetek lehetnek [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) vagy [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | A modul bináris fájljait, végrehajtható fájlját és szerelvényeit tartalmazó könyvtár. |
| `executable` | Annak a parancsnak/végrehajtható fájlnak a neve, amelyet a rendszer a feladatok részeként futtat. |
| `create_pool` | Logikai jelző, amely azt jelzi, hogy a rendszer létrehozza-e a készletet a feladatok futtatása előtt. |
| `delete_batch_job_after_finish` | Logikai jelző, amely azt jelzi, hogy a rendszer törli-e a feladatot a Batch-fiókból a befejezés után. |
| `delete_batch_pool_after_finish` | Logikai jelző, amely jelzi, hogy a művelet befejezése után törölni kell-e a készletet. |
| `is_positive_exit_code_failure` | Logikai jelző, amely azt jelzi, hogy a feladat meghiúsul-e, ha a feladat pozitív kóddal kilép. |
| `vm_image_urn` | Ha `create_pool` `True`, és a virtuális gép `VirtualMachineConfiguration`használ. |
| `pool_id` | Annak a készletnek az azonosítója, amelyben a feladatot futtatni fogja. |
| `allow_reuse` | Meghatározza, hogy a lépés újra fel kell-e használni az előző eredményeket, ha ugyanazokkal a beállításokkal futnak újra. |

Az alábbi példa egy Azure Batch lépés definícióját tartalmazza:

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

| YAML kulcs | Leírás |
| ----- | ----- |
| `compute_target` | Az ehhez a lépéshez használt Azure Databricks számítási cél. |
| `inputs` | A bemenetek lehetnek [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)vagy [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | A kimenetek lehetnek [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) vagy [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | A Databricks tartozó név ehhez a futtatáshoz. |
| `source_directory` | A parancsfájlt és más fájlokat tartalmazó könyvtár. |
| `num_workers` | A Databricks-fürtön futó feldolgozók statikus száma. |
| `runconfig` | Egy `.runconfig` fájl elérési útja. Ez a fájl a [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) osztály YAML ábrázolása. A fájl struktúrájával kapcsolatos további információkért lásd: [runconfigschema. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Meghatározza, hogy a lépés újra fel kell-e használni az előző eredményeket, ha ugyanazokkal a beállításokkal futnak újra. |

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

| YAML kulcs | Leírás |
| ----- | ----- |
| `compute_target` | Az ehhez a lépéshez használt Azure Data Factory számítási cél. |
| `source_data_reference` | Bemeneti kapcsolatok, amely adatátviteli műveletek forrásaként szolgál. A támogatott értékek a következők: [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)vagy [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Az adatátviteli műveletek céljaként szolgáló bemeneti kapcsolatok. A támogatott értékek a következők: [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) és [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Meghatározza, hogy a lépés újra fel kell-e használni az előző eredményeket, ha ugyanazokkal a beállításokkal futnak újra. |

Az alábbi példa egy adatátviteli lépést tartalmaz:

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

### <a name="python-script-step"></a>Python-parancsfájl lépései

| YAML kulcs | Leírás |
| ----- | ----- |
| `compute_target` | A lépéshez használandó számítási cél. A számítási cél lehet Azure Machine Learning számítási, virtuális gép (például a Data Science VM) vagy a HDInsight. |
| `inputs` | A bemenetek lehetnek [InputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [adatkészlet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)vagy [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | A kimenetek lehetnek [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) vagy [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | A Python-szkript neve (`source_directory`hoz viszonyítva). |
| `source_directory` | A parancsfájlt, a Conda-környezetet stb. tartalmazó könyvtár. |
| `runconfig` | Egy `.runconfig` fájl elérési útja. Ez a fájl a [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) osztály YAML ábrázolása. A fájl struktúrájával kapcsolatos további információkért lásd: [runconfig. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Meghatározza, hogy a lépés újra fel kell-e használni az előző eredményeket, ha ugyanazokkal a beállításokkal futnak újra. |

A következő példa egy Python-parancsfájl lépését tartalmazza:

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

## <a name="schedules"></a>Ütemezések

Egy folyamat ütemtervének meghatározásakor az adattárolók által aktivált vagy ismétlődő érték lehet egy adott időintervallum alapján. Az alábbi kulcsok az ütemterv definiálásához használhatók:

| YAML kulcs | Leírás |
| ----- | ----- |
| `description` | Az ütemterv leírása. |
| `recurrence` | Ismétlődési beállításokat tartalmaz, ha az ütemezése ismétlődő. |
| `pipeline_parameters` | A folyamat által igényelt paraméterek. |
| `wait_for_provisioning` | Meg kell-e várni az ütemezett kiépítés befejezését. |
| `wait_timeout` | Azon másodpercek száma, ameddig az időtúllépés előtt várni kell. |
| `datastore_name` | A módosított/hozzáadott Blobok figyelésére szolgáló adattár. |
| `polling_interval` | A módosított/hozzáadott Blobok lekérdezési ideje (percben). Alapértelmezett érték: 5 perc. Csak az adattár-ütemtervek esetében támogatott. |
| `data_path_parameter_name` | Annak az adatelérési útvonal-feldolgozási paraméternek a neve, amely a módosított blob elérési úttal van megadva. Csak az adattár-ütemtervek esetében támogatott. |
| `continue_on_step_failure` | Azt határozza meg, hogy folytatja-e a beküldött PipelineRun más lépéseinek végrehajtását, ha egy lépés meghiúsul. Ha meg van adni, felülbírálja a folyamat `continue_on_step_failure` beállításait.
| `path_on_datastore` | Választható. Az adattár elérési útja a módosított/hozzáadott Blobok figyeléséhez. Az elérési út az adattár tárolójában van, így az ütemezett figyelők tényleges elérési útja tároló/`path_on_datastore`. Ha nincs, a rendszer figyeli az adattár tárolóját. A `path_on_datastore` egy almappájában végrehajtott hozzáadások/módosítások nem lesznek figyelve. Csak az adattár-ütemtervek esetében támogatott. |

Az alábbi példa egy adattár által aktivált ütemezés definícióját tartalmazza:

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

**Ismétlődő ütemterv**definiálásakor használja a következő kulcsokat a `recurrence`alatt:

| YAML kulcs | Leírás |
| ----- | ----- |
| `frequency` | Az ütemezett ismétlődések gyakorisága. Az érvényes értékek: `"Minute"`, `"Hour"`, `"Day"`, `"Week"`vagy `"Month"`. |
| `interval` | Milyen gyakran következik be az ütemterv. Az egész érték azon időegységek száma, ameddig az ütemezés újra be nem következik. |
| `start_time` | Az ütemterv kezdési időpontja. Az érték karakterlánc-formátuma `YYYY-MM-DDThh:mm:ss`. Ha nincs megadva kezdési idő, az első számítási feladat azonnal fut, és a jövőbeli munkaterhelések az ütemterv alapján futnak. Ha a kezdési időpont a múltban van, akkor az első munkaterhelés a következő számított futási időpontban fut. |
| `time_zone` | A kezdési idő időzónája. Ha nincs megadva időzóna, a rendszer UTC-t használ. |
| `hours` | Ha `frequency` `"Day"` vagy `"Week"`, egy vagy több, 0 és 23 közötti egész számot is megadhat, vesszővel elválasztva, a folyamat futási idejét. Csak `time_of_day` vagy `hours` és `minutes` használható. |
| `minutes` | Ha `frequency` `"Day"` vagy `"Week"`, megadhat egy vagy több egész számot 0 és 59 között, vesszővel elválasztva, az óra percében, amikor a folyamat futni fog. Csak `time_of_day` vagy `hours` és `minutes` használható. |
| `time_of_day` | Ha `frequency` `"Day"` vagy `"Week"`, megadhatja az ütemterv futtatásának időpontját. Az érték karakterlánc-formátuma `hh:mm`. Csak `time_of_day` vagy `hours` és `minutes` használható. |
| `week_days` | Ha `frequency` `"Week"`, egy vagy több napot is megadhat, vesszővel elválasztva, az ütemterv futtatásakor. Az érvényes értékek: `"Monday"`, `"Tuesday"`, `"Wednesday"`, `"Thursday"`, `"Friday"`, `"Saturday"`és `"Sunday"`. |

Az alábbi példa egy ismétlődő ütemezés definícióját tartalmazza:

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

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [használható a CLI bővítmény a Azure Machine Learninghoz](reference-azure-machine-learning-cli.md).
