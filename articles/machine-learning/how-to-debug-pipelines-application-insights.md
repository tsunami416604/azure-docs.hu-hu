---
title: Gépi tanulási folyamatok hibakeresése és hibaelhárítása az Application Insightsban
titleSuffix: Azure Machine Learning
description: Adja hozzá a naplózást a betanítási és kötegelt pontozási folyamatokhoz, és tekintse meg a naplózott eredményeket az Application Insightsban.
services: machine-learning
author: sanpil
ms.author: sanpil
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: b3e4bf19a7ec153f85483f3c5028e468e06ed7f0
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982361"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines-in-application-insights"></a>Gépi tanulási folyamatok hibakeresése és hibaelhárítása az Application Insightsban
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az [OpenCensus](https://opencensus.io/quickstart/python/) python-kódtár segítségével a parancsfájlok ból az Application Insights naplók útvonala. A naplók egy helyen történő összesítése lehetővé teszi a lekérdezések létrehozását és a problémák diagnosztizálását. Az Application Insights használatával nyomon követheti a naplókat az idő múlásával, és összehasonlíthatja a folyamatnaplókat a futtatások között.

Miután a naplók egyszer helyen biztosítja a hibák és hibaüzenetek előzményeit. Mivel az Application Insights integrálható az Azure Alerts, az Application Insights-lekérdezések alapján riasztásokat is létrehozhat.

## <a name="prerequisites"></a>Előfeltételek

* Kövesse az Azure [Machine Learning-munkaterület](./how-to-manage-workspace.md) létrehozásának lépéseit, és [hozza létre az első folyamatot](./how-to-create-your-first-pipeline.md)
* [Konfigurálja a fejlesztői környezetet](./how-to-configure-environment.md) az Azure Machine Learning SDK telepítéséhez.
* Telepítse az [OpenCensus Azure Monitor Exporter](https://pypi.org/project/opencensus-ext-azure/) csomagot helyileg:
  ```python
  pip install opencensus-ext-azure
  ```
* [Alkalmazásinsights-példány](../azure-monitor/app/opencensus-python.md) létrehozása (ez a dokumentum az erőforrás kapcsolati karakterláncának beszerzésével kapcsolatos információkat is tartalmaz)

## <a name="getting-started"></a>Első lépések

Ez a szakasz egy bevezetés az OpenCensus egy Azure Machine Learning-folyamatból való használatával kapcsolatos. Részletes oktatóanyag: [OpenCensus Azure Monitor exporters](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Adjon hozzá egy PythonScriptStep az Azure ML-folyamathoz. Konfigurálja a [RunConfiguration-t](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) az opencensus-ext-azure függéssel. Konfigurálja `APPLICATIONINSIGHTS_CONNECTION_STRING` a környezeti változót.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Hozzon létre egy `sample_step.py` nevű fájlt. Importálja az AzureLogHandler osztályt a naplók alkalmazáselemzési útvonalához. A Python naplózási könyvtárat is importálnia kell.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Ezután adja hozzá az AzureLogHandlert a python-naplózóhoz.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Naplózás egyéni dimenziókkal
 
Alapértelmezés szerint az Application Insights-ba továbbított naplók nem rendelkeznek elegendő környezettel a futtatáshoz vagy a kísérlethez való visszakövetéshez. Ahhoz, hogy a naplók perelhető a problémák diagnosztizálására, további mezőkre van szükség. 

Ezeknek a mezőknek a hozzáadásához egyéni dimenziók adhatók hozzá, hogy kontextust biztosítsanak a naplóüzenethez. Egy példa, ha valaki meg akarja tekinteni a naplókat több lépésben ugyanabban a folyamatfuttatásban.

Az Egyéni dimenziók a kulcs-érték (karakterlánc, karakterlánc) párok szótárát alkotják. A szótár ezután elküldi az Application Insights és oszlopként jelenik meg a lekérdezés eredménye. Az egyes dimenziók [lekérdezési paraméterekként](#additional-helpful-queries)használhatók.

### <a name="helpful-context-to-include"></a>Hasznos kontextus, amely tartalmazza

| Mező                          | Érvelés/Példa                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Lekérdezheti az azonos parent_run_id rendelkezők naplóit, hogy az összes lépéshez idővel láthassa a naplókat, ahelyett, hogy minden egyes lépésbe belekellene merülnie                                        |
| step_id                        | Lekérdezheti az azonos step_id rendelkezők naplóit, hogy lássa, hol történt a probléma szűk hatókörrel, és csak az egyes lépéseket                                                        |
| step_name                      | Lekérdezési naplók, hogy a lépés teljesítményét az idő múlásával. Is segít megtalálni a step_id a legutóbbi fut anélkül, hogy belevetik a portál Felhasználói felület                                          |
| experiment_name                | Lekérdezheti a naplók között, hogy a kísérlet teljesítményét az idő múlásával. Is segít megtalálni a parent_run_id vagy step_id a legutóbbi fut anélkül, hogy beleveti magát a portál felhasználói felület                   |
| run_url                 | Tud nyújtani egy linket közvetlenül vissza a távon a vizsgálat. |

**Egyéb hasznos mezők**

Ezek a mezők további kódinstrumentációt igényelhetnek, és a futtatási környezet nem biztosítja őket.

| Mező                   | Érvelés/Példa                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Ha CI/CD-t használ a telepítéshez, ez a mező korrelálhatja a naplókat a lépés- és folyamatlogikát tartalmazó kódverzióval. Ez a hivatkozás további segítséget nyújt a problémák diagnosztizálásában, vagy a konkrét tulajdonságokkal rendelkező modellek azonosításában (napló/metrikus értékek) |
| run_type                       | Különbséget tehet a különböző modelltípusok, illetve a betanítás és a pontozási futtatások között |

### <a name="creating-a-custom-dimensions-dictionary"></a>Egyéni dimenziószótár létrehozása

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", custom_dimensions)
```

## <a name="opencensus-python-logging-considerations"></a>OpenCensus Python naplózási szempontok

Az OpenCensus AzureLogHandler segítségével a Python-naplók az Application Insights. Ennek eredményeképpen a Python naplózási árnyalatokat figyelembe kell venni. Amikor egy naplózó jön létre, akkor egy alapértelmezett naplószintet, és megmutatja naplók nagyobb vagy egyenlő, hogy a szintet. Egy jó referencia a Python naplózási funkciók a [Naplózás Cookbook](https://docs.python.org/3/howto/logging-cookbook.html).

A `APPLICATIONINSIGHTS_CONNECTION_STRING` környezeti változó szükséges az OpenCensus könyvtárhoz. Azt javasoljuk, hogy ezt a környezeti változót adja meg ahelyett, hogy átadná azt csővezeték-paraméterként, hogy elkerülje az egyszerű szöveges kapcsolati karakterláncok átadását.

## <a name="querying-logs-in-application-insights"></a>Naplók lekérdezése az Application Insightsban

Az Application Insights-hoz irányított naplók "nyomkövetések" vagy "kivételek" alatt jelennek meg. Ügyeljen arra, hogy állítsa be az időablakot, hogy tartalmazza a folyamat fut.

![Az Application Insights lekérdezésének eredménye](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Az Application Insights eredménya a naplóüzenet és a szint, a fájl elérési útja és a kódsor száma jelenik meg. Azt is megmutatja az egyéni dimenziók is. Ezen a képen a customDimensions szótár az előző kódminta kulcs-érték [párjait](#creating-a-custom-dimensions-dictionary)jeleníti meg.

### <a name="additional-helpful-queries"></a>További hasznos lekérdezések

Az alábbi lekérdezések némelyike az "customDimensions.Level" (az "customDimensions.Level" (az) Ezek a súlyossági szintek megfelelnek annak a szintnek, amelyet a Python-napló eredetileg küldött. További lekérdezési információkat az [Azure Figyelő naplólekérdezései című témakörben talál.](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)

| Használati eset                                                               | Lekérdezés                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Adott egyéni dimenzióra vonatkozó eredmények naplózása, például "parent_run_id" | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Az elmúlt 7 nap összes edzésének eredményeinek naplózása                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Eredmények naplózása az elmúlt 7 nap súlyossági szinthibával              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| A naplóeredmények száma súlyosságiszintű hibával az elmúlt 7 napban     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Következő lépések

Miután az Application Insights-példányban naplókat, azok segítségével [azure Monitor riasztások lekérdezési](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) eredmények alapján állíthatók be.

A lekérdezések eredményeit is hozzáadhatja egy [Azure-irányítópulthoz](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards#add-logs-analytics-query) további elemzési adatokért.
