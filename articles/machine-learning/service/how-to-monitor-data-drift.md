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
ms.openlocfilehash: e4deeab28fb643ff32624ba9dd16574e621f508c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333034"
---
# <a name="how-to-detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service"></a>Adatok eltolódása (előzetes verzió) az Azure Kubernetes Service-ben üzembe helyezett modell észlelése
Ebből a cikkből elsajátíthatja, hogyan figyelheti [adatok eltéréseket](concept-data-drift.md) egy üzembe helyezett modell betanítási adatkészletet tanuláshoz és következtetésekhez adatok között. 

Adatok eltéréseket az egyik a leggyakoribb okok ahol rontja a pontosság idővel. Ez történik, ha az adatok rendereléséhez szükséges az üzemi modell eltér az adatokat a modell betanításához használja. Az Azure Machine Learning szolgáltatás használatával az adatok eltéréseket detector használatával adatok eltéréseket követheti nyomon. Eltolódás észlelése esetén a szolgáltatás riasztást küldhet Önnek.  

> [!Note]
> Ez a szolgáltatás (előzetes verzió) van, és csak a konfigurációs beállítások. Tekintse át a [API-dokumentáció](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) és [kibocsátási megjegyzések](azure-machine-learning-release-notes.md) a részletek és a frissítések. 

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

- Állítsa be a [modell adatgyűjtő](how-to-enable-data-collection.md) adatokat gyűjteni az AKS üzembe helyezési modell, és erősítse meg az adatok gyűjtése a a `modeldata` blob-tárolóba.

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

További információkért lásd: a [DataDrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) hivatkozást.

## <a name="submit-a-datadriftdetector-run"></a>Küldje el a DataDriftDetector Futtatás

A DataDriftDetector konfigurált, és küldhet egy [adatok eltéréseket futtassa](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) modell egy adott dátumon. 

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

## <a name="get-data-drift-analysis-results"></a>Adatok eltéréseket elemzés eredményeinek beolvasása

A következő Python-példa bemutatja, hogyan vonatkozó adatokat eltéréseket metrikák ábrázolásához. A visszaadott mérőszámok segítségével egyéni vizualizációkat hozhat létre:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Adatok eltéréseket Show](media/how-to-monitor-data-drift/drift_show.png)

A mérőszámok, amelyek számítja ki a részletekért lásd: a [adatok eltéréseket fogalom](concept-data-drift.md) cikk.

## <a name="schedule-data-drift-detection"></a>Ütemezési adatok eltéréseket észlelése 

Adatok eltéréseket ütemezés engedélyezése hajt végre egy DataDriftDetector, futtassa a megadott gyakorisággal. Ha az eltéréseket együttható a megadott küszöbérték felett van, egy e-mailt küld. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

Az adatok eltéréseket érzékelő konfigurálása az Azure Portalon a modell részleteit megjelenítő oldalra láthatók.

![Az Azure portal adatok eltéréseket Config](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Eredmények megtekintése az Azure Machine Learning munkaterület felhasználói felületen

Eredmények megtekintése az Azure Machine Learning munkaterület felhasználói felületén, keresse meg a Rétegmodellek oldalán. A modell részletei lapon jelenik meg az eltéréseket konfigurációt. Egy "Data eltolódása (előzetes verzió)" lapon már elérhető az adatok eltéréseket metrikákat jelenítenek meg. 

![Az Azure portal Data eltéréseket](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="setting-up-alerts"></a>Riasztások beállítása 

A beállítás az eltéréseket együttható riasztási küszöbérték és a egy e-mail-címet, amely egy [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) e-mail riasztást küldi el, ha a eltéréseket értéke a küszöbérték felett. Minden adat eltéréseket metrika az app insights-erőforrást, hogy állítsa be az egyéni riasztási vagy műveleteket az Azure Machine Learning szolgáltatás munkaterület társítva vannak tárolva. Az app insights-lekérdezés a hivatkozásra az e-mail-értesítés követésével.

![Adatriasztás eltéréseket e-mailben](media/how-to-monitor-data-drift/drift_email.png)

## <a name="next-steps"></a>További lépések

* Az adatok eltéréseket használatával egy teljes példa: a [Azure ML adatok konfigurációsodródás notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). A Jupyter Notebook használatát mutatja be egy [nyissa meg az Azure-adatkészlet](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) az időjárás-előrejelzési modell betanításához az aks üzembe helyezni, és figyelje a adatok eltéréseket. 

* Nagy mértékben örömmel a kérdéseit, megjegyzéseit és javaslatait, adatok eltéréseket felé általános mozog, rendelkezésre állást. Az alábbi termék visszajelzésküldő gombot használva! 
