---
title: A Machine Learning webszolgáltatás végpontjaiból származó adatok figyelése és gyűjtése
titleSuffix: Azure Machine Learning
description: Az Azure Machine Learning használatával üzembe helyezett webszolgáltatások figyelése az Azure Application Insights használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 03/12/2020
ms.openlocfilehash: 464ec1fcf0986dc04bd92bbe9e31b5675e5822d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136193"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Az ml-es webszolgáltatás végpontjaiból származó adatok figyelése és gyűjtése
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan gyűjthet adatokat az Azure Kubernetes-szolgáltatás (AKS) vagy az Azure Container Instances (ACI) webszolgáltatás-végpontjaira telepített modellekből, és figyelheti azokat, amelyek az Azure Application Insights segítségével 
* [Azure Machine Learning Python SDK](#python)
* [Azure Machine Learning stúdió,](#studio) itt:https://ml.azure.com

A végpont kimeneti adatainak és válaszának gyűjtése mellett a következőket is figyelheti:

* Kérési arányok, válaszidők és hibaarányok
* Függőségi arányok, válaszidők és hibaarányok
* Kivételek

[További információ az Azure Application Insights ról.](../azure-monitor/app/app-insights-overview.md) 


## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree)

* Egy Azure Machine Learning-munkaterület, egy helyi könyvtár, amely tartalmazza a parancsfájlok, és az Azure Machine Learning SDK python telepítve. Az előfeltételek beeléréséről a [Fejlesztői környezet konfigurálása](how-to-configure-environment.md) című témakörben olvashat.

* Az Azure Kubernetes-szolgáltatás (AKS) vagy az Azure Container Instance (ACI) üzembe helyezendő betanított gépi tanulási modell. Ha még nem rendelkezik ilyen, tekintse meg a [Vonat képbesorolási modell](tutorial-train-models-with-aml.md) oktatóanyag

## <a name="web-service-metadata-and-response-data"></a>Webszolgáltatás metaadatai és válaszadatai

>[!Important]
> Az Azure Application Insights csak legfeljebb 64 kb-os hasznos adatokat naplóz. Ha ezt a korlátot eléri, akkor csak a modell legutóbbi kimenetei kerülnek naplózásra. 

A metaadatok és a szolgáltatásra adott válasz – amely megfelel a webszolgáltatás metaadatainak és a modell `"model_data_collection"`előrejelzéseinek – az üzenet alatt az Azure Application Insights-nyomkövetések bekerülnek. Az Azure Application Insights lekérdezheti közvetlenül az adatok eléréséhez, vagy hozzon létre egy [folyamatos exportálás](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) egy tárfiókba hosszabb megőrzése vagy további feldolgozás. A modelladatok ezután felhasználhatók az Azure Machine Learningben címkézés, átképzés, magyarázhatóság, adatelemzés vagy egyéb felhasználás beállításához. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Konfigurálás a Python SDK használatával 

### <a name="update-a-deployed-service"></a>Telepített szolgáltatás frissítése

1. Azonosítsa a szolgáltatást a munkaterületen. A munkaterület `ws` értéke a munkaterület neve.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. A szolgáltatás frissítése és az Azure Application Insights engedélyezése

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Egyéni nyomkövetések naplózása a szolgáltatásban

Ha egyéni nyomkövetéseket szeretne naplózni, kövesse az AKS vagy az ACI szokásos telepítési folyamatát a [Hogyan és hol](how-to-deploy-and-where.md) telepítse a dokumentumot. Ezután kövesse a következő lépéseket:

1. A pontozási fájl frissítése nyomtatási utasítások hozzáadásával
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. A szolgáltatás konfigurációjának frissítése
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Hozzon létre egy lemezképet, és telepítse azt az [AKS vagy ACI.](how-to-deploy-and-where.md)

### <a name="disable-tracking-in-python"></a>Követés letiltása pythonban

Az Azure Application Insights letiltásához használja a következő kódot:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Konfigurálás az Azure Machine Learning stúdióval

Az Azure Application Insights ot is engedélyezheti az Azure Machine Learning studióból, ha készen áll a modell üzembe helyezésére ezekkel a lépésekkel.

1. Jelentkezzen be a munkaterületrehttps://ml.azure.com/
1. Lépjen a **Modellek** elemre, és válassza ki, hogy melyik modellt szeretné telepíteni
1. **+Telepítés** kiválasztása
1. A Modell telepítése űrlap **feltöltése**
1. A **Speciális** menü kibontása

    ![Űrlap telepítése](./media/how-to-enable-app-insights/deploy-form.png)
1. Válassza **az Application Insights-diagnosztika és adatgyűjtés engedélyezése lehetőséget.**

    ![Az App Insights engedélyezése](./media/how-to-enable-app-insights/enable-app-insights.png)
## <a name="evaluate-data"></a>Adatok kiértékelése
A szolgáltatás adatait az Azure Application Insights-fiók tárolja, ugyanabban az erőforráscsoportban, mint az Azure Machine Learning.
A megtekintéshez:

1. Nyissa meg az Azure Machine Learning-munkaterületét az [Azure Portalon,](https://ms.portal.azure.com/) és kattintson az Application Insights hivatkozásra

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Az **Áttekintés** lapon megtekintheti a szolgáltatás hozadékok alapkészletét.

   [![Áttekintés](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. A webszolgáltatás-kérelem metaadatainak és válaszának megtekintéséhez jelölje ki a **kérelmek** táblázatát a **Naplók (Analytics)** szakaszban, és válassza a **Futtatás** lehetőséget a kérések megtekintéséhez.

   [![Adatmodellezés](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Az egyéni nyomkövetések vizsgálatához válassza az **Analytics** lehetőséget.
4. A séma szakaszban válassza a **Nyomkövetések**lehetőséget. Ezután a Lekérdezés futtatásához válassza a **Futtatás** lehetőséget. Az adatoknak táblázatformátumban kell megjelenniük, és a pontozási fájlban lévő egyéni hívásokhoz kell leképezniük őket.

   [![Egyéni nyomkövetések](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Ha többet szeretne megtudni az Azure Application Insights használatáról, olvassa el [a Mi az Application Insights?](../azure-monitor/app/app-insights-overview.md)

## <a name="export-data-for-further-processing-and-longer-retention"></a>Adatok exportálása további feldolgozásra és hosszabb megőrzésre

>[!Important]
> Az Azure Application Insights csak a blob storage-ba való exportálást támogatja. Az exportálási funkció további korlátai az [App Insights exportálása telemetriai adatok](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration)között találhatók.

Az Azure Application Insights [folyamatos exportálásával](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) üzeneteket küldhet egy támogatott tárfiókba, ahol hosszabb adatmegőrzésállítható be. Az `"model_data_collection"` üzenetek JSON formátumban tárolódnak, és könnyen elemezhetők a modelladatok kinyeréséhez. 

Az Azure Data Factory, az Azure ML-folyamatok vagy más adatfeldolgozási eszközök szükség szerint átalakíthatók az adatok. Miután átalakította az adatokat, majd regisztrálhatja őket az Azure Machine Learning-munkaterület adatkészletként. Ehhez olvassa el [Az adatkészletek létrehozása és regisztrálása című témakört.](how-to-create-register-datasets.md)

   [![Folyamatos exportálás](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Példa jegyzetfüzetre

Az [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) notebook ismerteti a jelen cikkben szereplő fogalmakat. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan helyezhet üzembe egy modellt egy Azure Kubernetes-szolgáltatásfürtre,](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) vagy hogyan helyezhet üzembe egy [modellt az Azure Container Instances szolgáltatásba](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) a modellek webszolgáltatás-végpontokra való üzembe helyezéséhez, és az Azure Application Insights engedélyezése az adatgyűjtés és a végpontfigyelés kihasználásához
* Tekintse meg [az MLOps: Modellek kezelése, üzembe helyezése és figyelése az Azure Machine Learning segítségével,](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) hogy többet tudjon meg az éles környezetben lévő modellekből gyűjtött adatok kihasználásáról. Ezek az adatok segíthetnek a gépi tanulási folyamat folyamatos javításában
