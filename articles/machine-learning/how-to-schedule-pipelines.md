---
title: Azure Machine Learning folyamatok ütemezhetnek
titleSuffix: Azure Machine Learning
description: Azure Machine Learning folyamatok ütemezhetnek a Pythonhoz készült Azure Machine Learning SDK-val. Az ütemezett folyamatok lehetővé teszik a rutin, az időigényes feladatok, például az adatfeldolgozás, a képzés és a figyelés automatizálását.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 351d52128660c25c557c536f2835e0e71c0115f7
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87842960"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Gépi tanulási folyamatok ütemezhetnek a Pythonhoz készült Azure Machine Learning SDK-val

Ebből a cikkből megtudhatja, hogyan ütemezhet programozott módon egy folyamat futtatását az Azure-ban. Dönthet úgy, hogy az eltelt idő alapján vagy a fájl-rendszer módosításain alapuló ütemtervet hoz létre. Az időalapú ütemtervek segítségével gondoskodhat a rutin feladatokról, például az adateltolódás figyeléséről. A változáson alapuló ütemtervek felhasználhatók a szabálytalan vagy kiszámíthatatlan változásokra való reagálásra, például a feltöltött új vagy régi adatfeldolgozásra. Az ütemtervek létrehozásának megismerése után megismerheti, hogyan kérheti le és inaktiválja azokat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).

* Python-környezet, amelyben a Pythonhoz készült Azure Machine Learning SDK telepítve van. További információkért lásd: [újrafelhasználható környezetek létrehozása és kezelése a Azure Machine learning-vel való betanításhoz és üzembe helyezéshez.](how-to-use-environments.md)

* Egy Machine Learning munkaterület közzétett folyamattal. A [Machine learning-folyamatokat a Azure Machine learning SDK-val létrehozhatja és futtathatja](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>A munkaterület inicializálása & az adatlekérdezés

Egy folyamat beütemezhet egy hivatkozást a munkaterületre, a közzétett folyamat azonosítóját, valamint annak a kísérletnek a nevére, amelyben létre kívánja hozni az ütemtervet. Ezeket az értékeket a következő kóddal érheti el:

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

## <a name="create-a-schedule"></a>Ütemterv létrehozása

Egy folyamat ismétlődő futtatásához létre kell hoznia egy ütemtervet. A egy folyamatot `Schedule` , egy kísérletet és egy triggert társít. Az trigger lehet egy `ScheduleRecurrence` , amely leírja a futtatások közötti várakozást, vagy egy adattár elérési útját, amely megadja a módosításokat bemutató könyvtárat. Mindkét esetben szüksége lesz a folyamat azonosítójára és annak a kísérletnek a nevére, amelyben létre kívánja hozni az ütemtervet.

Importálja a (z) `Schedule` és osztályokat a Python-fájl elejére `ScheduleRecurrence` :

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Időalapú ütemterv létrehozása

A `ScheduleRecurrence` konstruktor egy kötelező `frequency` argumentummal rendelkezik, amelynek a következő karakterláncok egyikének kell lennie: "minute", "Hour", "Day", "Week" vagy "Month". Egy egész szám argumentumot is igényel, amely `interval` meghatározza, hogy hány `frequency` egységnek kell eltelnie az ütemterv kezdete között. A választható argumentumok lehetővé teszik a [SCHEDULERECURRENCE SDK-dokumentációban](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py)részletezett időpontokra vonatkozó részletesebb tudnivalókat.

Hozzon létre egy-t `Schedule` , amely 15 percenként elindítja a futtatást:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Változáson alapuló ütemterv létrehozása

A fájlok változásai által aktivált folyamatok hatékonyabbak lehetnek, mint az időalapú ütemtervek. Előfordulhat például, hogy egy fájl módosításakor vagy egy új fájl egy adatkönyvtárhoz való hozzáadásakor szeretne elvégezni egy előfeldolgozási lépést. Az adattáron belüli adattárolók és az adattárban lévő módosítások változásai figyelhetők. Ha egy adott könyvtárat figyel, akkor a könyvtár alkönyvtárain belüli módosítások _nem_ indítják el a futtatást.

Fájl – reaktív létrehozásához a (z) `Schedule` paramétert be kell állítania `datastore` a [Schedule. Create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-)értékre. Egy mappa figyeléséhez állítsa be az `path_on_datastore` argumentumot.

Az `polling_interval` argumentummal percek alatt megadhatja, hogy az adattár milyen gyakorisággal legyen bejelölve a változásokhoz.

Ha a folyamat [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) - [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)lett létrehozva, a változót a módosított fájl nevére állíthatja be az argumentum beállításával `data_path_parameter_name` .

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Az ütemterv létrehozásakor nem kötelező argumentumok

A korábban tárgyalt argumentumokon kívül az argumentumot úgy is beállíthatja, hogy `status` `"Disabled"` inaktív ütemtervet hozzon létre. Végül a `continue_on_step_failure` lehetővé teszi, hogy egy olyan logikai értéket adjon át, amely felülbírálja a folyamat alapértelmezett meghibásodási viselkedését.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>A Azure Logic Apps használata összetettebb munkafolyamatokhoz

Azure Logic Apps támogatja az összetettebb munkafolyamatokat, és sokkal szélesebb körben integrálódik, mint Azure Machine Learning folyamatokat. További információért lásd: [Machine learning folyamat futtatásának elindítása logikai alkalmazásból](how-to-trigger-published-pipeline.md) .

## <a name="view-your-scheduled-pipelines"></a>Az ütemezett folyamatok megtekintése

A böngészőben nyissa meg a Azure Machine Learning. A navigációs panel **végpontok** szakaszában válassza a **folyamat végpontjai**lehetőséget. Ekkor megjelenik a munkaterületen közzétett folyamatok listája.

![PÉNZMOSÁS-folyamatok lapja](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

Ezen a lapon megtekintheti a munkaterület összes folyamatával kapcsolatos összegző információkat: nevek, leírások, állapot stb. Részletezés a folyamatra kattintva. Az eredményül kapott oldalon további részleteket talál a folyamatról, és az egyes futtatásokat is lerészletezheti.

## <a name="deactivate-the-pipeline"></a>A folyamat inaktiválása

Ha van `Pipeline` közzétett, de nincs ütemezve, akkor letilthatja a következőket:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Ha a folyamat ütemezve van, először le kell mondania az ütemezést. Kérje le az ütemterv azonosítóját a portálról, vagy futtassa a következőt:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Miután `schedule_id` letiltotta a letiltását, futtassa a következőt:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Ha ezt követően újra futtatja `Schedule.list(ws)` , üres listát kell kapnia.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a Pythonhoz készült Azure Machine Learning SDK-val két különböző módon ütemezhet egy folyamatot. Az egyik ütemezett ismétlődés az eltelt idő alapján. A másik ütemterv akkor fut, ha egy fájlt módosítanak egy adott `Datastore` vagy a tároló egyik könyvtárán belül. Látta, hogyan használhatja a portált a folyamat és az egyes futtatások vizsgálatára. Végezetül megtanulta, hogyan tilthatja le az ütemtervet, hogy a folyamat lefusson.

További információ:

> [!div class="nextstepaction"]
> [Azure Machine Learning folyamatok használata a Batch pontozáshoz](tutorial-pipeline-batch-scoring-classification.md)

* További információ a [folyamatokról](concept-ml-pipelines.md)
* További információ a [Azure Machine learning Jupyter való feltárásáról](samples-notebooks.md)

