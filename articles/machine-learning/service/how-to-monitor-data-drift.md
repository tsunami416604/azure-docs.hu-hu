---
title: Adatok eltolódása (előzetes verzió) az AKS üzemelő példányok esetében észlelése
titleSuffix: Azure Machine Learning service
description: Annak észlelése, adatok eltéréseket az Azure Kubernetes Service-ben üzembe helyezett modellek Azure Machine Learning szolgáltatásban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 3b8152bde8b7e44dde1b0b9c82216333778f83da
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806018"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Adatok eltolódása (előzetes verzió) a modellek üzembe helyezett Azure Kubernetes Service (AKS) észlelése

Ebből a cikkből megismerheti, hogyan adatokat egy üzembe helyezett modell a betanítási adatkészletet tanuláshoz és következtetésekhez közötti adatok eltéréseket figyelni. A machine Learning a környezetben betanított gépi tanulási modelleket csökkentett teljesítményű előrejelzési teljesítményt tapasztalhat eltéréseket miatt. Az Azure Machine Learning szolgáltatással megfigyelheti az adatok eltéréseket, és a szolgáltatás is e-mail értesítés küldése, eltéréseket észlelése esetén.

## <a name="what-is-data-drift"></a>Mi az adatok eltéréseket?

Adatok eltéréseket történjen az adatok rendereléséhez szükséges az üzemi modell eltér az adatokat a modell betanításához használja. A leggyakoribb okai az ahol modellpontosságból csökkenti az idő múlásával legyen így monitorozási adatok eltéréseket észlelni tudja a modell teljesítménybeli problémák. 

## <a name="what-can-i-monitor"></a>Mit képes figyelni?

Az Azure Machine Learning szolgáltatás a bemeneteket az AKS üzembe helyezett modell figyelheti, és hasonlítsa össze ezeket az adatokat a modell a betanítási adatkészletet. Rendszeres időközönként következtetésekhez adata [pillanatkép és a profilkészítés során létrehozott](how-to-explore-prepare-data.md), majd kiszámított az alapkonfiguráció adatkészlet létrehozásához egy adatelemzés eltéréseket, amelyek: 

+ Adatok eltéréseket, az eltéréseket együttható nevű mértékétől méri.
+ Mértékek a data közreműködői konfigurációsodródás a funkció, mely funkciókat okozott adatok eltéréseket tájékoztatja.
+ Mértékek távolságskála metrikákat. Jelenleg Wasserstein és energia távolság számítja ki.
+ Mértékek disztribúciók funkcióját. Jelenleg a kernel sűrűségű Költségbecslési és hisztogramok.
+ Küldjön e-mailben konfigurációsodródás riasztásokat az adatok.

> [!Note]
> Ez a szolgáltatás (előzetes verzió) van, és csak a konfigurációs beállítások. Tekintse át a [API-dokumentáció](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) és [kibocsátási megjegyzések](azure-machine-learning-release-notes.md) a részletek és a frissítések. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Hogyan felügyelik adatok eltéréseket az Azure Machine Learning szolgáltatás

Az Azure Machine Learning szolgáltatást használja, adatok eltéréseket adatkészletek és a központi telepítések keresztül figyel. Adatok eltéréseket monitorozásához – általában a betanítási adatkészletet egy modell - alapkonfiguráció adatkészlet van megadva. Egy második adatkészlet - az alapkonfiguráció élelmiszer-általában a bemeneti adatok modellezése a központi telepítés – összeállításunkat lett tesztelve. Mindkét adatkészletek [profiled](how-to-explore-prepare-data.md#explore-with-summary-statistics) és az adatok bemeneti konfigurációsodródás figyelési szolgáltatás. Egy gépi tanulási modell tanítása észleli a különbség a két adatkészlet között. A modell teljesítményét az eltéréseket együttható, amely méri a két adatkészlet között eltéréseket mértékétől alakítja át. Használatával [model e](machine-learning-interpretability-explainability.md), a Funkciók, amelyek hozzájárulnak a eltéréseket együttható számítja ki. Az adatkészlet profilban, a nyomon követett statisztikai információkat talál az egyes szolgáltatásokról. 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs fiókja, létrehozhat egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

- Az Azure Machine Learning szolgáltatás munkaterület és az Azure Machine Learning SDK telepítve van a Pythonhoz készült. Kövesse az utasításokat, [hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](setup-create-workspace.md#sdk) , tegye a következőket:

    - A Miniconda-környezet létrehozása
    - Telepítse az Azure Machine Learning SDK a Pythonhoz
    - Munkaterület létrehozása
    - Munkaterület konfigurációs fájl (aml_config/config.json) írása.

- Az adatok eltéréseket SDK a következő paranccsal telepítse:

    ```shell
    pip install azureml-contrib-datadrift
    ```

- Hozzon létre egy [adatkészlet](how-to-create-register-datasets.md) a modell betanítási adatokból.

- Adja meg a betanítási adatkészletet amikor [regisztrálása](concept-model-management-and-deployment.md) a modellt. A következő példa bemutatja, hogy használatával a `datasets` paraméterrel adja meg a betanítási adatkészletet:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [A modelladatok gyűjtésének engedélyezése](how-to-enable-data-collection.md) adatokat gyűjteni az AKS üzembe helyezési modell, és erősítse meg az adatok gyűjtése a a `modeldata` blob-tárolóba.

## <a name="configure-data-drift"></a>Adatok eltéréseket konfigurálása
A kísérlethez adatok eltéréseket konfigurálásához importálja függőségek a következő Python-példában látható módon. 

Ez a példa bemutatja a konfigurálása a [ `DataDriftDetector` ](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) objektum:

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

## <a name="submit-a-datadriftdetector-run"></a>Küldje el a DataDriftDetector Futtatás

Az a `DataDriftDetector` küldhet konfigurált objektumot, egy [futtatása adatok eltéréseket](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) modell egy adott időpontban. A Futtatás részeként engedélyezése DataDriftDetector riasztások beállításával a `drift_threshold` paraméter. Ha a [datadrift_coefficient](#metrics) meghaladja a megadott `drift_threshold`, egy e-mailt küld.

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

## <a name="visualize-drift-metrics"></a>Eltolódás metrikák megjelenítése

<a name="metrics"></a>

Miután a Futtatás DataDriftDetector elküldött, is tudja a mentett adatok eltéréseket feladat futtatási törzsének eltéréseket mérőszámokért:


|Metrika|Leírás|
--|--|
wasserstein_distance|Statisztikai távolság egydimenziós numerikus terjesztési definiálva.|
energy_distance|Statisztikai távolság egydimenziós numerikus terjesztési definiálva.|
datadrift_coefficient|Hasonlóképpen Matthew a korrelációs együtthatóját számítja ki, de ez a kimenet egy 0 és 1 közötti valós szám. A eltéréseket kontextusában a 0 azt jelzi, hogy nincs eltéréseket, és 1 azt jelzi, hogy maximális eltéréseket.|
datadrift_contribution|Ez a funkció konfigurációsodródás hozzájáruló funkciók fontosságát.|

Több módon eltéréseket metrikákat tekinthet meg:

* A Jupyter widget használja.
* Használja a [ `get_metrics()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) bármely függvény `datadrift` objektumot futtatni.
* A metrikák megtekintése az adatmodell az Azure Portalon

A következő Python-példa bemutatja, hogyan vonatkozó adatokat eltéréseket metrikák ábrázolásához. A visszaadott mérőszámok segítségével egyéni vizualizációkat hozhat létre:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Tekintse meg az Azure Machine Learning által észlelt adatok eltolódása](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Ütemezési adatok eltéréseket vizsgálatok 

Ha engedélyezi az adatok eltéréseket észlelési, egy DataDriftDetector futtatja a megadott, ütemezett gyakorisággal. Ha a datadrift_coefficient eléri a megadott `drift_threshold`, egy e-mailt küld az egyes ütemezett futtatását. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Az adatok eltéréseket érzékelő konfigurálása az Azure Portalon a modell részleteit megjelenítő oldalra láthatók.

![Az Azure portal adatok eltéréseket Config](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Eredmények megtekintése az Azure Machine Learning munkaterület felhasználói felületen

Eredmények megtekintése az Azure Machine Learning munkaterület felhasználói felületén, keresse meg a Rétegmodellek oldalán. A modell részletei lapon jelenik meg az eltéréseket konfigurációt. Egy "Data eltolódása (előzetes verzió)" lapon már elérhető az adatok eltéréseket metrikákat jelenítenek meg. 

![Az Azure portal Data eltéréseket](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>Fogadó eltéréseket riasztások

A beállítás az eltéréseket együttható riasztási küszöbérték és a egy e-mail-címét, egy [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) értesítő e-mail küldése automatikusan, amikor a eltéréseket értéke a küszöbérték felett. 

Ahhoz, hogy egyéni riasztások és műveletek beállítását, az összes adat eltéréseket metrikák vannak tárolva a [Application Insights](how-to-enable-app-insights.md) erőforrás, amely az Azure Machine Learning szolgáltatás munkaterület együtt lett létrehozva. Az Application Insights-lekérdezéshez, a hivatkozásra az e-mail-értesítés is követheti.

![Adatriasztás eltéréseket e-mailben](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>A modell újratanítása eltéréseket után

Adatok eltéréseket negatív hatással van az üzembe helyezett modell teljesítményét, esetén a modell újratanítása időt. A következő [ `diff()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#diff-rhs-dataset--compute-target-none--columns-none-
) módszert biztosít egy kezdeti érteni, mi változott a régi és új képzési adatok között. 

```python
from azureml.core import Dataset

old_training_dataset.diff(new_training_dataset)
```

A kimenetét az előző kód alapján, érdemes a modell újratanítása. Ehhez, folytassa a következő lépéseket.

* Vizsgálja meg az összegyűjtött adatokat, és előkészíti az adatokat az új modell betanításához.
* Ossza a tanítási és tesztelési adatokká.
* Betanítja a modellt, újra az új adatokkal.
* Értékelje ki az újonnan létrehozott modell teljesítményét.
* Új modell üzembe helyezése, ha a teljesítmény jobb, mint az üzemi modell.

## <a name="next-steps"></a>További lépések

* Az adatok eltéréseket használatával egy teljes példa: a [Azure ML adatok konfigurációsodródás notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). A Jupyter Notebook használatát mutatja be egy [nyissa meg az Azure-adatkészlet](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) az időjárás-előrejelzési modell betanításához az aks üzembe helyezni, és figyelje a adatok eltéréseket. 

* Nagy mértékben örömmel a kérdéseit, megjegyzéseit és javaslatait, adatok eltéréseket felé általános mozog, rendelkezésre állást. Az alábbi termék visszajelzésküldő gombot használva! 
