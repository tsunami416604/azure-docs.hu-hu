---
title: Machine Learning webszolgáltatás-végpontokról származó adatok figyelése és gyűjtése
titleSuffix: Azure Machine Learning
description: Azure Machine Learning üzembe helyezett webszolgáltatások figyelése az Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 03/12/2020
ms.custom: tracking-python
ms.openlocfilehash: 2473d864e0ad0fca4a886a6135a9caac0742e3d7
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84557074"
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

* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma

* Egy Azure Machine Learning munkaterület, egy helyi könyvtár, amely tartalmazza a parancsfájlokat, és a Azure Machine Learning SDK for Python telepítve van. Az előfeltételek beszerzéséről a [fejlesztési környezet konfigurálása](how-to-configure-environment.md) című cikkben olvashat bővebben.

* Az Azure Kubernetes Service (ak) vagy az Azure Container instance (ACI) számára üzembe helyezett, betanított gépi tanulási modell. Ha még nem rendelkezik ilyennel, tekintse meg a következő témakört: a [rendszerképek besorolási modellje](tutorial-train-models-with-aml.md) oktatóanyaga

## <a name="web-service-metadata-and-response-data"></a>Webszolgáltatás metaadatainak és válaszideje

>[!Important]
> Az Azure Application Insights csak a legfeljebb 64 kb adattartalmakat naplózza. Ha eléri ezt a korlátot, a rendszer csak a modell legújabb kimeneteit naplózza. 

A szolgáltatás metaadatainak és válaszai – a webszolgáltatás metaadatainak és a modell előrejelzésének megfelelően – a rendszer az üzenetben az Azure Application Insights-nyomkövetésre van naplózva `"model_data_collection"` . Az Azure Application Insights közvetlenül is lekérdezheti az adatok eléréséhez, vagy beállíthat [folyamatos exportálást](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) egy Storage-fiókba a hosszú megőrzés vagy a további feldolgozás érdekében. A modell adatai ezután felhasználhatók a Azure Machine Learning a címkézés, az átképzés, a magyarázat, az adatelemzés vagy más felhasználás beállításához. 

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

1. A pontozási fájl frissítése nyomtatási utasítások hozzáadásával
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. A szolgáltatás konfigurációjának frissítése
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Hozzon létre egy rendszerképet, és telepítse azt [AK-ra vagy ACI-](how-to-deploy-and-where.md)ra.

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
## <a name="evaluate-data"></a>Az adatértékelés
A szolgáltatás adatait a rendszer az Azure Application Insights-fiókjában tárolja, amely ugyanabban az erőforráscsoporthoz van, mint Azure Machine Learning.
A megtekintéshez:

1. Lépjen a [Azure Portal](https://ms.portal.azure.com/) Azure Machine learning munkaterületére, és kattintson a Application Insights hivatkozásra.

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. A szolgáltatáshoz tartozó mérőszámok alapszintű készletének megjelenítéséhez válassza az **Áttekintés** lapot

   [![Áttekintés](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. A webszolgáltatás-kérelem metaadatainak és válaszának megtekintéséhez jelölje ki a **kérelmek** táblát a **naplók (Analitika)** szakaszban, és válassza a **Futtatás** lehetőséget a kérelmek megtekintéséhez.

   [![Adatmodellezés](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Az egyéni Nyomkövetések kereséséhez válassza az **elemzés** lehetőséget
4. A séma szakaszban válassza a **Nyomkövetések**lehetőséget. Ezután válassza a **Futtatás** lehetőséget a lekérdezés futtatásához. Az adattartalomnak táblázatos formátumban kell szerepelnie, és a pontozási fájlban lévő egyéni hívásokra kell leképeznie

   [![Egyéni Nyomkövetések](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Ha többet szeretne megtudni az Azure Application Insights használatáról, tekintse meg a [Mi az a Application Insights?](../azure-monitor/app/app-insights-overview.md)című témakört.

## <a name="export-data-for-further-processing-and-longer-retention"></a>Adatok exportálása a további feldolgozáshoz és a hosszú megőrzéshez

>[!Important]
> Az Azure Application Insights csak a blob Storage-ba való exportálást támogatja. Az exportálási lehetőség további korlátai az alkalmazás-elemzések [Exportálás telemetria](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration)találhatók.

Az Azure Application Insights [folyamatos exportálásával](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) üzeneteket küldhet egy támogatott Storage-fiókba, ahol megtarthat egy nagyobb adatmegőrzést. A `"model_data_collection"` rendszer JSON formátumban tárolja az üzeneteket, és könnyen elemezheti a modell-adatok kinyerését. 

A Azure Data Factory, az Azure ML-feldolgozási folyamatai vagy más adatfeldolgozási eszközök is használhatók az adatátalakításhoz igény szerint. Ha átalakította az adatokat, akkor a Azure Machine Learning munkaterületre is regisztrálhat adatkészletként. Ehhez lásd: [adatkészletek létrehozása és regisztrálása](how-to-create-register-datasets.md).

   [![Folyamatos exportálás](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Példa jegyzetfüzetre

Az [enable-app-Insight-in-producting-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) notebook bemutatja a cikkben szereplő fogalmakat. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések

* Lásd: [modell üzembe helyezése Azure Kubernetes Service-fürtön](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) vagy [modell üzembe](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) helyezése Azure Container instances a modellek webszolgáltatás-végpontokra való üzembe helyezéséhez, valamint az Azure-Application Insights engedélyezése az adatgyűjtés és a végpontok figyeléséhez
* Tekintse meg a [MLOps: modellek kezelése, üzembe helyezése és monitorozása a Azure Machine learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) segítségével további információ az éles modellekben gyűjtött adatok kihasználásáról. Ezek az adatkezelési lehetőségek segíthetnek a gépi tanulási folyamat folyamatos fejlesztésében
