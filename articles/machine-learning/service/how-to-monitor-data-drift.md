---
title: Adatok eltolódása (előzetes verzió) az AKS üzemelő példányok esetében észlelése
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan észleli az adatok eltéréseket az Azure Kubernetes Service-ben üzembe helyezett modellek Azure Machine Learning szolgáltatásban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 06/20/2019
ms.openlocfilehash: c446c8236ca64948f0bb6a8354a83579cc6ff24c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443943"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service"></a>Adatok eltolódása (előzetes verzió) az Azure Kubernetes Service-ben üzembe helyezett modell észlelése
Ebből a cikkből megismerheti, hogyan adatokat egy üzembe helyezett modell a betanítási adatkészletet tanuláshoz és következtetésekhez közötti adatok eltéréseket figyelni. 

## <a name="what-is-data-drift"></a>Mi az adatok eltéréseket?

Adatok eltéréseket, koncepció eltéréseket, más néven az egyik a leggyakoribb okok ahol rontja a pontosság idővel. Ez történik, ha az adatok rendereléséhez szükséges az üzemi modell eltér az adatokat a modell betanításához használja. Az Azure Machine Learning szolgáltatás képes figyelni az adatok eltéréseket, és eltéréseket észlel, amikor a szolgáltatás is értesítő e-mail küldése Önnek.  

> [!Note]
> Ez a szolgáltatás (előzetes verzió) van, és csak a konfigurációs beállítások. Tekintse át a [API-dokumentáció](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) és [kibocsátási megjegyzések](azure-machine-learning-release-notes.md) a részletek és a frissítések. 

## <a name="what-can-i-monitor"></a>Mit képes figyelni?
Az Azure Machine Learning szolgáltatás a bemeneteket az AKS üzembe helyezett modell figyelheti, és hasonlítsa össze ezeket az adatokat a modell a betanítási adatkészletet. Rendszeres időközönként következtetésekhez adata [pillanatkép és a profilkészítés során létrehozott](how-to-explore-prepare-data.md), majd kiszámított az alapkonfiguráció adatkészlet létrehozásához egy adatelemzés eltéréseket, amelyek: 

+ Adatok eltéréseket, az eltéréseket együttható nevű mértékétől méri.
+ Mértékek a data közreműködői konfigurációsodródás a funkció, mely funkciókat okozott adatok eltéréseket tájékoztatja.
+ Mértékek távolságskála metrikákat. Jelenleg Wasserstein és energia távolság számítja ki.
+ Mértékek disztribúciók funkcióját. Jelenleg a kernel sűrűségű Költségbecslési és hisztogramok.
+ Küldjön e-mailben konfigurációsodródás riasztásokat az adatok.

Hogyan számítja ki ezeket a metrikákat részletes ismertetéséért tekintse meg a [adatok eltéréseket fogalom](concept-data-drift.md) cikk.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

- Az Azure Machine Learning szolgáltatás munkaterület és az Azure Machine Learning SDK telepítve van a Pythonhoz készült. Ezekről az előfeltételekről használatával beszerzéséről a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.

- [Állítsa be a környezetet](how-to-configure-environment.md), majd telepítse az adatok eltéréseket SDK a következő paranccsal:

    ```
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

## <a name="import-dependencies"></a>Importálja a függőségeket 
Ebben az útmutatóban használt függőségek importálása:

```python
# Azure ML service packages 
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration
``` 

## <a name="configure-data-drift"></a>Adatok eltéréseket konfigurálása 

A következő Python-példa bemutatja a konfigurálása a `DataDriftDetector` objektum:

```python
# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

További információkért lásd: a `[DataDrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py)` osztály dokumentációja.

## <a name="submit-a-datadriftdetector-run"></a>Küldje el a DataDriftDetector Futtatás

Az a `DataDriftDetector` küldhet konfigurált objektumot, egy [futtatása adatok eltéréseket](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) modell egy adott időpontban. 

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

A következő Python-példa bemutatja, hogyan vonatkozó adatokat eltéréseket metrikák ábrázolásához. A visszaadott mérőszámok segítségével egyéni vizualizációkat hozhat létre:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Tekintse meg az Azure Machine Learning által észlelt adatok eltolódása](media/how-to-monitor-data-drift/drift_show.png)

A mérőszámok, amelyek számítja ki a részletekért lásd: a [adatok eltéréseket fogalom](concept-data-drift.md) cikk.

## <a name="schedule-data-drift-scans"></a>Ütemezési adatok eltéréseket vizsgálatok 

Ha engedélyezi az adatok eltéréseket észlelési, egy DataDriftDetector futtatja a megadott, ütemezett gyakorisággal. Ha az eltéréseket együttható a megadott küszöbérték felett van, egy e-mailt küld. 

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

A beállítás az eltéréseket együttható riasztási küszöbérték és a egy e-mail-címét, egy [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) értesítő e-mail küldése automatikusan, amikor a eltéréseket értéke a küszöbérték felett. Ahhoz, hogy egyéni riasztások és műveletek beállítását az összes adat eltéréseket metrikákat az Application Insights-erőforrást az Azure Machine Learning szolgáltatás munkaterület együtt létrehozott vannak tárolva. Az Application Insights-lekérdezéshez, a hivatkozásra az e-mail-értesítés is követheti.

![Adatriasztás eltéréseket e-mailben](media/how-to-monitor-data-drift/drift_email.png)

## <a name="next-steps"></a>További lépések

* Az adatok eltéréseket használatával egy teljes példa: a [Azure ML adatok konfigurációsodródás notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). A Jupyter Notebook használatát mutatja be egy [nyissa meg az Azure-adatkészlet](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) az időjárás-előrejelzési modell betanításához az aks üzembe helyezni, és figyelje a adatok eltéréseket. 

* Nagy mértékben örömmel a kérdéseit, megjegyzéseit és javaslatait, adatok eltéréseket felé általános mozog, rendelkezésre állást. Az alábbi termék visszajelzésküldő gombot használva! 
