---
title: Adateltolódás észlelése AKS-telepítéseken
titleSuffix: Azure Machine Learning
description: Észlelheti az adatok eltolódását (előzetes verzió) az Azure Kubernetes Service által telepített modelleken az Azure Machine Learningben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75537005"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Adateltolódás (előzetes verzió) észlelése az Azure Kubernetes-szolgáltatásba (AKS) telepített modelleken
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan figyelheti az adatok közötti eltolódás a betanítási adatkészlet és az üzembe helyezett modell adatok következtetése között. A gépi tanulás környezetében a betanított gépi tanulási modellek a drift miatt leromlott előrejelzési teljesítményt tapasztalhatnak. Az Azure Machine Learning segítségével figyelheti az adatok eltolódását, és a szolgáltatás e-mailben riasztást küldhet Önnek, ha a rendszer driftet észlel.

## <a name="what-is-data-drift"></a>Mi az adateltolódás?

A gépi tanulás környezetében az adateltolódás a modell bemeneti adatainak változása, amely a modell teljesítményének csökkenéséhez vezet. Ez az egyik legfontosabb oka annak, hogy a modell pontossága idővel romlik, így az adatok eltolódásának figyelése segít a modell teljesítményproblémáinak észlelésében. 

## <a name="what-can-i-monitor"></a>Mit figyelhetek?

Az Azure Machine Learning segítségével figyelheti az AKS-en üzembe helyezett modell bemeneteit, és összehasonlíthatja ezeket az adatokat a modell betanítási adatkészletével. Rendszeres időközönként a következtetési adatok [pillanatkép és profilozott,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)majd kiszámított a kiindulási adatkészlet, hogy egy adateltolódás elemzés, amely: 

+ Méri az adateltolódás nagyságát, az úgynevezett eltolódási együtthatót.
+ Az adatok eltolódásának funkciószerint történő használatát méri, jelezve, hogy mely funkciók okozták az adatok eltolódását.
+ A távolságmérőszámokat méri. Jelenleg Wasserstein és Energy Distance számítják ki.
+ Méri a funkciók eloszlását. Jelenleg kernel sűrűség becslés és histogramok.
+ Értesítések küldése az adateltolódásról e-mailben.

> [!Note]
> Ez a szolgáltatás (előzetes verzió) és a konfigurációs beállítások korlátozott. Kérjük, olvassa el [az API dokumentációját](https://docs.microsoft.com/python/api/azureml-datadrift/) és [kiadási megjegyzéseit](azure-machine-learning-release-notes.md) a részletekért és a frissítésekért. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Az adatok eltolódásának figyelése az Azure Machine Learningben

Az Azure Machine Learning használatával az adateltolódást adatkészletek vagy központi telepítések figyelik. Az adatok eltolódásának figyeléséhez egy alapszintű adatkészlet – általában egy modell betanítási adatkészlete – van megadva. Egy második adatkészlet – általában a modell bemeneti adatok at egy központi telepítésből gyűjtött – az alapkonfigurációs adatkészlettel szemben tesztelt. Mindkét adatkészlet profilozott, és az adateltetésfigyelési szolgáltatásba kerülnek. A gépi tanulási modell be tanítása a két adatkészlet közötti különbségek észlelésére. A modell teljesítményét az eltolódási együtthatóra konvertálja, amely a két adatkészlet közötti eltolódás nagyságát méri. A [modell értelmezhetősége](how-to-machine-learning-interpretability.md)alapján kiszámítják azokat a jellemzőket, amelyek hozzájárulnak az eltolódási együtthatóhoz. Az adatkészlet-profilból az egyes funkciók statisztikai adatait követi nyomon a rendszer. 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nem rendelkezik ilyen, hozzon létre egy ingyenes fiókot, mielőtt elkezdené. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

- Az Azure Machine Learning SDK python telepítve. Az Azure [Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) utasításait az alábbi műveletekhez használja:

    - Miniconda környezet létrehozása
    - Az Azure Machine Learning SDK python-hoz telepítése

- Egy [Azure Machine Learning-munkaterület.](how-to-manage-workspace.md)

- Munkaterület [konfigurációs fájlja](how-to-configure-environment.md#workspace).

- Telepítse az sdk adateltolódást a következő paranccsal:

    ```shell
    pip install azureml-datadrift
    ```

- Hozzon létre egy [adatkészletet](how-to-create-register-datasets.md) a modell betanítási adataiból.

- Adja meg a betanítási adatkészletet a modell [regisztrálásakor.](concept-model-management-and-deployment.md) A következő példa bemutatja a `datasets` paraméter használatával a betanítási adatkészlet megadásához:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Engedélyezze a modelladatgyűjtést](how-to-enable-data-collection.md) a modell AKS-üzembe helyezéséből származó `modeldata` adatok gyűjtéséhez, és ellenőrizze, hogy a blobtárolóban adatok at gyűjtenek-e.

## <a name="configure-data-drift"></a>Adateltolódás konfigurálása
Az adateltolódás konfigurálásához importálja a függőségeket a következő Python-példában látható módon. 

Ez a példa bemutatja az [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) objektum konfigurálását:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>DataDriftDetector-futtatás beküldése

Az `DataDriftDetector` objektum konfigurálásával [adateltolódást](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) küldhet a modell adott dátumán. A futtatás részeként engedélyezze a DataDriftDetector `drift_threshold` riasztásokat a paraméter beállításával. Ha a [datadrift_coefficient](#visualize-drift-metrics) meghaladja a megadottat, `drift_threshold`a rendszer e-mailt küld.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Drift-mérőszámok megjelenítése

<a name="metrics"></a>

A DataDriftDetector futtatásának elküldése után láthatja az adatátolási feladat minden egyes futtatási iterációjában mentett drift-mérőszámokat:


|Metrika|Leírás|
--|--|
wasserstein_distance|Az egydimenziós numerikus eloszláshoz meghatározott statisztikai távolság.|
energy_distance|Az egydimenziós numerikus eloszláshoz meghatározott statisztikai távolság.|
datadrift_coefficient|Máté korrelációs együtthatójával hasonlóan számítva, de ez a kimenet egy 0 és 1 közötti valós szám. Az eltolódás kontextusában a 0 nem esést, az 1 pedig a maximális eltolódást jelzi.|
datadrift_contribution|A sodródáshoz hozzájáruló funkciók jellemzője.|

Az eltolódási mutatók többféleképpen is megtekinthetők:

* Használja `RunDetails`a [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Használja [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) a függvényt bármely `datadrift` futtatási objektumon.
* Tekintse meg a metrikákat a modellek a munkaterület **modellek** szakaszában az [Azure Machine Learning stúdióban.](https://ml.azure.com)

A következő Python-példa bemutatja, hogyan ábrázolható a releváns adateltolódási metrikák. A visszaadott metrikák segítségével egyéni vizualizációkat hozhat létre:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Az Azure Machine Learning által észlelt adateltolódás megtekintése](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Adateltolódás-ellenőrzések ütemezése 

Ha engedélyezi az adateltolódás észlelését, a DataDriftDetector a megadott, ütemezett gyakorisággal fut. Ha a datadrift_coefficient eléri `drift_threshold`a megadott, egy e-mailt küld minden ütemezett futtatás. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Az adateltolódás-érzékelő konfigurációja az [Azure Machine Learning stúdió](https://ml.azure.com)munkaterületén a Részletek lap **Modellek** csoportjában látható. **Details**

[![Azure Machine Learning stúdió adatsodródása](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Eredmények megtekintése az Azure Machine Learning stúdiójában

Az eredmények megtekintéséhez a munkaterületen az [Azure Machine Learning stúdióban](https://ml.azure.com)keresse meg a modell lapot. A modell részletek lapján megjelenik az adateltolódás konfigurációja. Az **adateltolódás** lap most már elérhető az adateltolódási mérőszámok megjelenítéséhez. 

[![Azure Machine Learning stúdió adatsodródása](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Drift riasztások fogadása

Az átlós együttható riasztási küszöbértékének beállításával és egy e-mail-cím megadásával az [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e-mail riasztás automatikusan elküldésre kerül, ha az átlóási együttható a küszöbérték felett van. 

Annak érdekében, hogy egyéni riasztásokat és műveleteket állítson be, az összes adateltolódási metrika az Azure Machine Learning-munkaterülettel együtt létrehozott [Application Insights-erőforrásban](how-to-enable-app-insights.md) tárolódik. Kövesse az e-mailben küldött riasztásban lévő hivatkozást az Application Insights-lekérdezésre.

![Adatdrift e-mail riasztás](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>A modell újratanítása a sodródás után

Ha az adatok eltolódása negatívan befolyásolja az üzembe helyezett modell teljesítményét, itt az ideje a modell újratanítása. Ehhez folytassa a következő lépésekkel.

* Vizsgálja meg az összegyűjtött adatokat, és készítsen adatokat az új modell betanításához.
* Osszuk fel vonat/teszt adatokra.
* A modell betanítása újra az új adatok használatával.
* Az újonnan létrehozott modell teljesítményének kiértékelése.
* Telepítsen új modellt, ha a teljesítmény jobb, mint az éles modell.

## <a name="next-steps"></a>További lépések

* Az adatok eltolódásának teljes példáját az [Azure ML-es adateltolódási jegyzetfüzet ben láthatja.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb) Ez a Jupyter-jegyzetfüzet egy [Azure Open Dataset](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) használatával mutatja be a modell betanítását az időjárás előrejelzéséhez, az AKS-hez való üzembe helyezéséhez és az adatok eltolódásának figyeléséhez. 

* Az adateltolódás észlelése [adatkészlet-figyelőkkel.](how-to-monitor-datasets.md)

* Nagyra értékelnénk a kérdéseit, megjegyzéseit vagy javaslatait, ahogy az adatok eltolódása az általános elérhetőség felé halad. Használja az alábbi termékvisszajelzés gombot! 
