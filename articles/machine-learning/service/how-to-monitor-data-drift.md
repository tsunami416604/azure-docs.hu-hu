---
title: Adateltolódás (előzetes verzió) észlelése az AK-beli üzemelő példányokon
titleSuffix: Azure Machine Learning service
description: Az Azure Kubernetes Service-ben üzembe helyezett modellek adateltolódásának észlelése Azure Machine Learning szolgáltatásban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 7d47b74d4fef3676101f3f624dcacb832dcedc3a
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858708"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Az Azure Kubernetes Service (ak) szolgáltatásban üzembe helyezett modellek adateltolódásának (előzetes verzió) észlelése

Ebből a cikkből megtudhatja, hogyan figyelheti az adateltolódást a betanítási adatkészlet és a központilag telepített modell információi között. A gépi tanulás kontextusában a betanított gépi tanulási modellek a drift miatt csökkenhetnek az előrejelzési teljesítménytől. A Azure Machine Learning szolgáltatással figyelheti az adateltolódást, és a szolgáltatás elküldheti e-mailben a riasztást, ha a rendszer a drift észlelését észleli.

## <a name="what-is-data-drift"></a>Mi az az adateltolódás?

Az adateltolódás akkor történik meg, ha az éles modellbe való kiszolgált adatok eltérnek a modell betanításához használt adatoktól. Ez az egyik legfőbb oka annak, hogy a modell pontossága az idő múlásával romlik, így az adateltolódás monitorozása segít a modell teljesítményével kapcsolatos problémák észlelésében. 

## <a name="what-can-i-monitor"></a>Mit tudok figyelni?

A Azure Machine Learning szolgáltatással figyelheti a bemeneteket egy AK-ra telepített modellbe, és összehasonlíthatja ezeket az adatokat a modell betanítási adatkészletével. A következtetések rendszeres időközönként [pillanatfelvételt](how-to-explore-prepare-data.md)készítenek, és a rendszer az alapadatkészlet alapján számítja ki az adateltolódás elemzését, amely a következő adatokat eredményezi: 

+ A drift együtthatónak nevezett adateltolódás mértékét méri.
+ A szolgáltatás által okozott adateltolódási hozzájárulás mértéke, amely tájékoztatja, hogy mely funkciók okozták az adateltolódást.
+ Méri a távolsági metrikákat. Jelenleg a Wasserstein és az energia távolságát számítjuk ki.
+ A funkciók eloszlását méri. Jelenleg a kernel sűrűségének becslése és hisztogramja.
+ Riasztásokat küldhet az adateltolódásnak e-mailben.

> [!Note]
> Ez a szolgáltatás jelenleg előzetes verzióban érhető el, és korlátozott a konfigurációs beállításokban. A részletekért és a frissítésért tekintse meg az [API-dokumentációt](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) és a [kibocsátási megjegyzéseket](azure-machine-learning-release-notes.md) . 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Az adateltolódások figyelése Azure Machine Learning szolgáltatásban

Azure Machine Learning szolgáltatás használatakor az adateltolódást adathalmazok vagy központi telepítések figyelik. Az adateltolódás figyeléséhez egy alapkonfigurációt – általában a modell betanítási adatkészletét – kell megadni. Egy második adatkészlet – általában az üzembe helyezésből összegyűjtött bemeneti adatok modellezése az alapadatkészlet alapján történik. Mind az adathalmazok, mind az adateltolódás-figyelési szolgáltatás bemenete. A Machine learning-modellek a két adathalmaz közötti különbségek észlelésére vannak kiképezve. A modell teljesítményét a rendszer a drift együtthatóra konvertálja, amely a két adathalmaz közötti eltolódás mértékét méri. A [modell értelmezése](machine-learning-interpretability-explainability.md)alapján a rendszer kiszámítja a drift együtthatóhoz hozzájáruló funkciókat. Az adatkészlet-profilban az egyes szolgáltatásokra vonatkozó statisztikai adatokat követjük nyomon. 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning Service ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

- A Azure Machine Learning SDK for Python telepítve van. A [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) utasításait követve tegye a következőket:

    - Miniconda-környezet létrehozása
    - A Pythonhoz készült Azure Machine Learning SDK telepítése

- Egy [Azure Machine learning szolgáltatás](how-to-manage-workspace.md)munkaterülete.

- Munkaterület- [konfigurációs fájl](how-to-configure-environment.md#workspace).

- Telepítse az adatdrift SDK-t a következő parancs használatával:

    ```shell
    pip install azureml-contrib-datadrift
    ```

- Hozzon létre egy [adatkészletet](how-to-create-register-datasets.md) a modell betanítási adataiból.

- Válassza ki a betanítási adatkészletet a modell [regisztrálása](concept-model-management-and-deployment.md) során. Az alábbi példa azt mutatja be, `datasets` hogyan használhatja a paramétert a betanítási adatkészlet megadásához:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Engedélyezheti a modell adatgyűjtését](how-to-enable-data-collection.md) , hogy adatokat gyűjtsön a modell AK-ból történő telepítéséről, és `modeldata` erősítse meg az adatok gyűjtését a blob-tárolóban.

## <a name="configure-data-drift"></a>Az adateltolódás konfigurálása
Ha a kísérlethez adateltolódást szeretne beállítani, importálja a függőségeket a következő Python-példában látható módon. 

Ez a példa az [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) objektum konfigurálását mutatja be:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>DataDriftDetector futtatásának elküldése

Ha az `DataDriftDetector` objektum konfigurálva van, elküldheti a modell egy adott dátumára vonatkozó [adateltolódást](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) . A Futtatás részeként engedélyezze a DataDriftDetector riasztásokat a `drift_threshold` paraméter beállításával. Ha a [datadrift_coefficient](#metrics) a megadott `drift_threshold`érték felett van, a rendszer elküld egy e-mailt.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Drift mérőszámok megjelenítése

<a name="metrics"></a>

Miután elküldte a DataDriftDetector-futtatást, megtekintheti az egyes futtatási ismétlésekben mentett drift mérőszámokat az adateltolódási feladatokhoz:


|Metrika|Leírás|
--|--|
wasserstein_distance|Egy dimenziós numerikus eloszláshoz megadott statisztikai távolság.|
energy_distance|Egy dimenziós numerikus eloszláshoz megadott statisztikai távolság.|
datadrift_coefficient|Hasonlóan, mint Máté korrelációs együtthatója, de ez a kimenet egy 0 és 1 közötti valós szám. A drift kontextusban 0 azt jelzi, hogy nincs eltolódás, és az 1 a maximális eltolódást jelzi.|
datadrift_contribution|A funkciók fontossága a drifthez hozzájáruló szolgáltatások szempontjából.|

A drift mérőszámok több módon is megtekinthetők:

* Használja a `RunDetails` [Jupyter widgetet](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Használja a [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) függvényt bármely `datadrift` futtatási objektumon.
* Megtekintheti a modell Azure Portal mérőszámait.
* A mérőszámokat a munkaterület kezdőlapjának **modellek** szakaszában tekintheti meg [(előzetes verzió)](https://ml.azure.com).

A következő Python-példa bemutatja, hogyan ábrázolhatja a kapcsolódó adateltolódási metrikákat. A visszaadott metrikák használatával egyéni vizualizációkat hozhat létre:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Lásd: Azure Machine Learning által észlelt adateltolódás](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Adateltolódások ellenőrzésének ütemezett időpontja 

Ha engedélyezi az adateltolódás észlelését, a DataDriftDetector a megadott ütemezett gyakorisággal fut le. Ha a datadrift_coefficient eléri a megadott `drift_threshold`értéket, a rendszer minden ütemezett futtatással elküld egy e-mailt. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Az adatdrift-detektor konfigurációját a Azure Portal vagy a munkaterület kezdőlapja (előzetes verzió) modell részletei oldalán tekintheti meg.

![Azure Portal adateltolódás-konfiguráció](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-portal"></a>Eredmények megtekintése Azure Portal

Ha [Azure Portalban](https://portal.azure.com)szeretné megtekinteni az eredményeket a munkaterületen, navigáljon a modell lapra. A modell részletek lapján megjelenik az adatdrift-konfiguráció. Mostantól elérhető az adateltolódási mérőszámok megjelenítésére szolgáló "adateltolódás (előzetes verzió)" lap. 

![Adateltolódás Azure Portal](media/how-to-monitor-data-drift/drift_ui.png)

Az eredmények a munkaterület kezdőlapján [(előzetes verzió)](https://ml.azure.com)a modell részletei között is elérhetők.

## <a name="receiving-drift-alerts"></a>Drift-riasztások fogadása

A drift együttható riasztási küszöbértékének beállításával és e-mail-cím megadásával a rendszer automatikusan elküld egy [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e-mail riasztást, ha a drift együttható meghaladja a küszöbértéket. 

Ahhoz, hogy egyéni riasztásokat és műveleteket állítson be, az összes adateltolódási metrika a Azure Machine Learning szolgáltatás munkaterülettel együtt létrehozott [Application Insights](how-to-enable-app-insights.md) erőforrásban tárolódik. Követheti az e-mail-riasztásban szereplő hivatkozást a Application Insights lekérdezésre.

![Adatdrift E-mail riasztás](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Modell újratanítása a drift után

Ha az adateltolódás negatív hatással van a telepített modell teljesítményére, ideje a modell újratanítására. Ehhez folytassa a következő lépésekkel.

* Vizsgálja meg az összegyűjtött adatokat, és készítse elő az adatokat az új modell betanításához.
* Felosztás a betanítási/tesztelési adatként.
* A modell tanítása az új adataival.
* Az újonnan generált modell teljesítményének kiértékelése.
* Új modell üzembe helyezése, ha a teljesítmény jobb, mint az éles modell.

## <a name="next-steps"></a>További lépések

* Az adateltolódás használatának teljes példáját az [Azure ml adatdrift notebookja](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/azure-ml-datadrift.ipynb)tartalmazza. Ez a Jupyter Notebook egy [Azure Open-adatkészletet](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) mutat be a modell betanításához, az időjárás előrejelzéséhez, az AK-ra való üzembe helyezéséhez és az adateltolódás figyeléséhez. 

* Nagyra értékeljük a kérdéseit, megjegyzéseit vagy javaslatait, mivel az adateltolódás az általános elérhetőség irányába mozdul el. Használja az alábbi termék-visszajelzés gombot! 
