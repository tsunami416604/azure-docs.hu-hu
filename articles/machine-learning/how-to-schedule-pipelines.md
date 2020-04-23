---
title: Az Azure Machine Learning-folyamatok ütemezése
titleSuffix: Azure Machine Learning
description: Ütemezze az Azure Machine Learning-folyamatokat az Azure Machine Learning SDK python-hoz használatával. Az ütemezett folyamatok lehetővé teszik a rutinszerű, időigényes feladatok, például az adatfeldolgozás, a betanítás és a figyelés automatizálását.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: 8e1e718fa4e6660d72203ac98bb6d427cdba2059
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024557"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Gépi tanulási folyamatok ütemezése az Azure Machine Learning Python-alapú SDK-val

Ebben a cikkben megtudhatja, hogyan ütemezheti a folyamatot az Azure-ban való futtatásra. Dönthet úgy, hogy az eltelt idő vagy a fájlrendszer módosításai alapján hoz létre ütemezést. Az időalapú ütemezések rutinfeladatok, például az adatok eltolódásának figyelése. A változásalapú ütemezések segítségével reagálhat a szabálytalan vagy előre nem látható változásokra, például a feltöltött új adatokra vagy a régi adatok szerkesztésre. Miután megtanulta, hogyan hozhat létre ütemezéseket, megtudhatja, hogyan kérheti le és inaktiválhatja őket.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://aka.ms/AMLFree)

* Egy Python-környezet, amelyben az Azure Machine Learning SDK python-hoz telepítve van. További információ: [Újrafelhasználható környezetek létrehozása és kezelése az Azure Machine Learning használatával történő betanításhoz és üzembe helyezéshez.](how-to-use-environments.md)

* Machine Learning-munkaterület közzétett folyamattal. Használhatja a beépített [létrehozása és futtatása gépi tanulási folyamatok az Azure Machine Learning SDK.](how-to-create-your-first-pipeline.md)

## <a name="initialize-the-workspace--get-data"></a>Adatok beakítása & munkaterület inicializálása

A folyamat ütemezéséhez szüksége lesz egy hivatkozása a munkaterületre, a közzétett folyamat azonosítójára és annak a kísérletnek a nevére, amelyben létre kívánja hozni az ütemezést. Ezeket az értékeket a következő kóddal szerezheti be:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Ütemezés létrehozása

A folyamat ismétlődő alapon történő futtatásához hozzon létre egy ütemezést. Egy `Schedule` folyamat, egy kísérlet és egy eseményindító társa. Az eseményindító lehet`ScheduleRecurrence` olyan eseményindító, amely leírja a futtatások közötti várakozást, vagy egy adattár elérési útját, amely meghatározza a módosításokat figyelő könyvtárat. Mindkét esetben szüksége lesz a folyamat azonosítójára és annak a kísérletnek a nevére, amelyben az ütemezés létrehozásához.

A python-fájl tetején importálja `Schedule` `ScheduleRecurrence` a és az osztályokat:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Időalapú ütemezés létrehozása

A `ScheduleRecurrence` konstruktornak van egy kötelező `frequency` argumentuma, amelynek a következő karakterláncok egyikének kell lennie: "Perc", "Óra", "Nap", "Hét" vagy "Hónap". Egy egész argumentumot `interval` is meg kell `frequency` adni, amely meghatározza, hogy hány egységnek kell eltelnie az ütemezés kezdete között. A választható argumentumok lehetővé teszik, hogy pontosabban megállapítsa a kezdési időpontokat, ahogy azt a [ScheduleRecurrence SDK-dokumentumok részletezik.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py)

Hozzon `Schedule` létre egy futtatást 15 percenként:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Változásalapú ütemezés létrehozása

A fájlmódosítások által kiváltott folyamatok hatékonyabbak lehetnek, mint az időalapú ütemezések. Előfordulhat például, hogy egy fájl módosításakor vagy új fájl adatkönyvtárba való hozzáadásakor előfeldolgozási lépést szeretne végrehajtani. Figyelheti az adattár változásait, vagy az adattár egy adott könyvtárán belüli módosításokat. Ha egy adott könyvtárat figyel, a könyvtár alkönyvtárain belüli módosítások _nem_ indítják el a futtatást.

Fájlreaktív `Schedule`létrehozásához a paramétert `datastore` a [Schedule.create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-)fájl hívásában kell beállítani. Mappa figyeléséhez állítsa `path_on_datastore` be az argumentumot.

Az `polling_interval` argumentum lehetővé teszi, hogy az adattár módosítási gyakoriságának percekben történő megadása.

Ha a folyamat egy [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)paraméterrel lett megalakítva, az `data_path_parameter_name` argumentum beállításával beállíthatja a változót a módosított fájl nevére.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Nem kötelező argumentumok ütemezés létrehozásakor

A korábban tárgyalt argumentumokon kívül `status` beállíthatja, hogy `"Disabled"` az argumentum inaktív ütemezést hozzon létre. Végül a `continue_on_step_failure` lehetővé teszi, hogy adja át a logikai, amely felülírja a folyamat alapértelmezett hiba viselkedését.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>Azure Logic Apps használata összetettebb munkafolyamatokhoz

Az Azure Logic Apps összetettebb munkafolyamatokat támogat, és sokkal szélesebb körben integrált, mint az Azure Machine Learning-folyamatok. További információ: [Egy machine learning-folyamat futtatása a logikai alkalmazásból.](how-to-trigger-published-pipeline.md)

## <a name="view-your-scheduled-pipelines"></a>Az ütemezett folyamatok megtekintése

A webböngészőben keresse meg az Azure Machine Learning. A navigációs panel **Végpontok** szakaszában válassza a **Folyamat végpontok lehetőséget.** Ezzel a munkaterületen közzétett folyamatok listájához vezet.

![Az AML folyamatai lapja](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Ezen a lapon a munkaterület összesítő információi láthatók: nevek, leírások, állapot stb. Részletezze a folyamatra kattintva. Az eredményül kapott oldalon további részletek találhatók a folyamatról, és előfordulhat, hogy az egyes futtatások ba ütköznek.

## <a name="deactivate-the-pipeline"></a>A csővezeték inaktiválása

Ha közzétett, `Pipeline` de nincs ütemezett, közzétett, de nincs beütemezve, a következő kkel tilthatja le:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Ha a folyamat ütemezve van, először meg kell szakítania az ütemezést. Az ütemezés azonosítójának lekérése a portálról vagy a következő futással:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Ha már `schedule_id` a kívánt letiltani kívánt, fuss:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Ha ezután `Schedule.list(ws)` újra fut, akkor kap egy üres listát.

## <a name="next-steps"></a>További lépések

Ebben a cikkben az Azure Machine Learning SDK python-hoz a folyamat ütemezése két különböző módon. Egy ütemezés ismétlődik az eltelt óraidő alapján. A másik ütemezés akkor fut, ha `Datastore` egy fájlt módosítanak az adott tároló egy megadott könyvtárában vagy egy könyvtárban. Látta, hogyan használhatja a portált a folyamat és az egyes futtatások vizsgálatához. Végül megtanulta, hogyan tilthatja le az ütemezést, hogy a folyamat ne fusson.

További információkért lásd:

> [!div class="nextstepaction"]
> [Az Azure Machine Learning-folyamatok használata kötegelt pontozáshoz](tutorial-pipeline-batch-scoring-classification.md)

* További információ [a folyamatokról](concept-ml-pipelines.md)
* További információ [az Azure Machine Learning jupyterrel történő felfedezéséről](samples-notebooks.md)

