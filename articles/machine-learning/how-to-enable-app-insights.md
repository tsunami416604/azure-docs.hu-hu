---
title: Machine Learning webszolgáltatás-végpontokról származó adatok figyelése és gyűjtése
titleSuffix: Azure Machine Learning
description: Azure Machine Learning üzembe helyezett webszolgáltatások figyelése az Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: how-to, tracking-python
ms.openlocfilehash: 88a122a9af4a5edac45a3189df5ffb78fb2ce271
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423813"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>A ML webszolgáltatás-végpontokról származó adatok figyelése és gyűjtése
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan gyűjthet adatokat a webszolgáltatási végpontokon üzembe helyezett modellekről az Azure Kubernetes szolgáltatásban (ak) vagy Azure Container Instances (ACI), ha engedélyezi az Azure Application Insightst 
* [Python SDK Azure Machine Learning](#python)
* [Azure Machine learning Studio](#studio) itt:https://ml.azure.com

A végpont kimeneti adatok és válaszok összegyűjtése mellett a következőket is megfigyelheti:

* Kérelmek díjai, válaszideje és meghibásodási aránya
* Függőségi arányok, válaszidő és sikertelenség aránya
* Kivételek

[További információ az Azure Application Insightsról](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy ingyenes fiókot a virtuális gép létrehozásának megkezdése előtt. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma

* Egy Azure Machine Learning munkaterület, egy helyi könyvtár, amely tartalmazza a parancsfájlokat, és a Azure Machine Learning SDK for Python telepítve van. Az előfeltételek beszerzéséről a [fejlesztési környezet konfigurálása](how-to-configure-environment.md) című cikkben olvashat bővebben.

* Az Azure Kubernetes Service (ak) vagy az Azure Container instance (ACI) számára üzembe helyezett, betanított gépi tanulási modell. Ha még nem rendelkezik ilyennel, tekintse meg a következő témakört: a [rendszerképek besorolási modellje](tutorial-train-models-with-aml.md) oktatóanyaga

## <a name="web-service-metadata-and-response-data"></a>Webszolgáltatás metaadatainak és válaszideje

> [!IMPORTANT]
> Az Azure Application Insights csak a legfeljebb 64 kb adattartalmakat naplózza. Ha eléri ezt a korlátot, akkor előfordulhat, hogy olyan hibákat lát, mint a memória, vagy nem lehet információt naplózni.

A webszolgáltatásra irányuló kérések adatainak naplózásához adjon hozzá `print` utasításokat a score.py-fájlhoz. Minden `print` utasítás egy bejegyzést eredményez az Application Insights nyomkövetési táblájában, az üzenet alatt `STDOUT` . Az utasítás tartalma a `print` `customDimensions` és `Contents` a nyomkövetési táblában is szerepelni fog. Ha JSON-karakterláncot nyomtat ki, akkor az a nyomkövetési kimenetben hierarchikus adatstruktúrát hoz létre `Contents` .

Az Azure Application Insights közvetlenül is lekérdezheti az adatok eléréséhez, vagy beállíthat [folyamatos exportálást](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) egy Storage-fiókba a hosszú megőrzés vagy a további feldolgozás érdekében. A modell adatai ezután felhasználhatók a Azure Machine Learning a címkézés, az átképzés, a magyarázat, az adatelemzés vagy más felhasználás beállításához. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>A Python SDK használata a konfiguráláshoz 

### <a name="update-a-deployed-service"></a>Központilag telepített szolgáltatás frissítése

1. Azonosítsa a szolgáltatást a munkaterületen. A értéke a `ws` munkaterület neve.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. A szolgáltatás frissítése és az Azure Application Insights engedélyezése

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Egyéni Nyomkövetések naplózása a szolgáltatásban

Ha egyéni nyomkövetést szeretne naplózni, kövesse a normál üzembe helyezési folyamatot az AK vagy ACI esetében a dokumentum [üzembe helyezése és helye témakörben](how-to-deploy-and-where.md) . Ezután kövesse az alábbi lépéseket:

1. Ha a következtetés során Application Insights adatokat szeretne küldeni, frissítse a pontozási fájlt a Print utasítások hozzáadásával. Összetettebb információk naplózása, például a kérelem adatai és a válasz, US a JSON-struktúra. A következő példa score.py a modell inicializálásának időpontját, a bemenetet és a kimenetet a következtetés során, valamint az esetleges hibák időpontját:

    > [!IMPORTANT]
    > Az Azure Application Insights csak a legfeljebb 64 kb adattartalmakat naplózza. Ha eléri ezt a korlátot, megjelenhetnek a hibák, például a memóriából, vagy nem lehet információt naplózni. Ha a naplózni kívánt adatok nagyobb 64 kb, akkor ehelyett a blob Storage-tárolóban kell tárolnia az [adatok gyűjtése az éles modellekben](how-to-enable-data-collection.md)című témakörben leírtak alapján.
    
    ```python
    import pickle
    import json
    import numpy 
    from sklearn.externals import joblib
    from sklearn.linear_model import Ridge
    from azureml.core.model import Model
    import time

    def init():
        global model
        #Print statement for appinsights custom traces:
        print ("model initialized" + time.strftime("%H:%M:%S"))
        
        # note here "sklearn_regression_model.pkl" is the name of the model registered under the workspace
        # this call should return the path to the model.pkl file on the local disk.
        model_path = Model.get_model_path(model_name = 'sklearn_regression_model.pkl')
        
        # deserialize the model file back into a sklearn model
        model = joblib.load(model_path)
    

    # note you can pass in multiple rows for scoring
    def run(raw_data):
        try:
            data = json.loads(raw_data)['data']
            data = numpy.array(data)
            result = model.predict(data)
            # Log the input and output data to appinsights:
            info = {
                "input": raw_data,
                "output": result.tolist()
                }
            print(json.dumps(info))
            # you can return any datatype as long as it is JSON-serializable
            return result.tolist()
        except Exception as e:
            error = str(e)
            print (error + time.strftime("%H:%M:%S"))
            return error
    ```

2. A szolgáltatás konfigurációjának frissítése
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Hozzon létre egy rendszerképet, és telepítse azt [AK-ra vagy ACI-](how-to-deploy-and-where.md)ra.

A naplózással és az adatok gyűjtésével kapcsolatos további információkért tekintse meg a [naplózás engedélyezése Azure Machine learning](how-to-enable-logging.md) és az [adatok gyűjtése az éles modellből](how-to-enable-data-collection.md)című témakört.

### <a name="disable-tracking-in-python"></a>A nyomkövetés letiltása a Pythonban

Az Azure Application Insights letiltásához használja a következő kódot:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>A Azure Machine Learning Studio használata a konfiguráláshoz

Az Azure Application Insights a Azure Machine Learning studióból is engedélyezhető, ha készen áll a modell üzembe helyezésére ezekkel a lépésekkel.

1. Jelentkezzen be a munkaterületre a következő címen:https://ml.azure.com/
1. Nyissa meg a **modelleket** , és válassza ki, hogy melyik modellt szeretné telepíteni
1. Válassza a **+ üzembe helyezés** lehetőséget
1. A **modell üzembe helyezése** űrlap feltöltése
1. A **speciális** menü kibontása

    ![Űrlap üzembe helyezése](./media/how-to-enable-app-insights/deploy-form.png)
1. Válassza ki **a diagnosztika és az adatgyűjtés engedélyezése Application Insights**

    ![Az alkalmazás-felismerés engedélyezése](./media/how-to-enable-app-insights/enable-app-insights.png)

## <a name="view-metrics-and-logs"></a>Metrikák és naplók megtekintése

A szolgáltatás adatait a rendszer az Azure Application Insights-fiókjában tárolja, amely ugyanabban az erőforráscsoporthoz van, mint Azure Machine Learning.
A megtekintéshez:

1. Lépjen a Azure Machine Learning munkaterületére a [Studióban](https://ml.azure.com/).
1. Válassza a **végpontok**lehetőséget.
1. Válassza ki a telepített szolgáltatást.
1. Görgessen le a **Application Insights URL-cím** megkereséséhez, és kattintson a hivatkozásra.

    [![Application Insights URL-cím keresése](./media/how-to-enable-app-insights/appinsightsloc.png)](././media/how-to-enable-app-insights/appinsightsloc.png#lightbox)

1. Application Insights az **Áttekintés** lapon vagy a bal oldali listában a __figyelés__ szakaszban válassza a __naplók__lehetőséget.

    [![A figyelés áttekintése lap](./media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. A score.py-fájlból naplózott információk megtekintéséhez tekintse meg a __nyomkövetési__ táblázatot. A következő lekérdezés olyan naplókat keres, amelyekben a __bemeneti__ érték naplózva lett:

    ```kusto
    traces
    | where customDimensions contains "input"
    | limit 10
    ```

   [![nyomkövetési adat](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)

Ha többet szeretne megtudni az Azure Application Insights használatáról, tekintse meg a [Mi az a Application Insights?](../azure-monitor/app/app-insights-overview.md)című témakört.

## <a name="export-data-for-further-processing-and-longer-retention"></a>Adatok exportálása a további feldolgozáshoz és a hosszú megőrzéshez

>[!Important]
> Az Azure Application Insights csak a blob Storage-ba való exportálást támogatja. Az exportálási lehetőség további korlátai az alkalmazás-elemzések [Exportálás telemetria](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration)találhatók.

Az Azure Application Insights [folyamatos exportálásával](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) üzeneteket küldhet egy támogatott Storage-fiókba, ahol megtarthat egy nagyobb adatmegőrzést. Az adatok JSON formátumban tárolódnak, és könnyen elemezhetők a modell-adatok kinyeréséhez. 

A Azure Data Factory, az Azure ML-feldolgozási folyamatai vagy más adatfeldolgozási eszközök is használhatók az adatátalakításhoz igény szerint. Ha átalakította az adatokat, akkor a Azure Machine Learning munkaterületre is regisztrálhat adatkészletként. Ehhez lásd: [adatkészletek létrehozása és regisztrálása](how-to-create-register-datasets.md).

:::image type="content" source="media/how-to-enable-app-insights/continuous-export-setup.png" alt-text="Folyamatos exportálás":::


## <a name="example-notebook"></a>Példajegyzetfüzet

Az [enable-app-Insight-in-producting-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) notebook bemutatja a cikkben szereplő fogalmakat. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések

* Lásd: [modell üzembe helyezése Azure Kubernetes Service-fürtön](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) vagy [modell üzembe](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) helyezése Azure Container instances a modellek webszolgáltatás-végpontokra való üzembe helyezéséhez, valamint az Azure-Application Insights engedélyezése az adatgyűjtés és a végpontok figyeléséhez
* Tekintse meg a [MLOps: modellek kezelése, üzembe helyezése és monitorozása a Azure Machine learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) segítségével további információ az éles modellekben gyűjtött adatok kihasználásáról. Ezek az adatkezelési lehetőségek segíthetnek a gépi tanulási folyamat folyamatos fejlesztésében
