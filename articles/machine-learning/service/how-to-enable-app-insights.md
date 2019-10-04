---
title: Az Azure Application Insights beállítása a ML-modellek figyelésére
titleSuffix: Azure Machine Learning
description: Azure Machine Learning üzembe helyezett webszolgáltatások figyelése az Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 07/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 785507e9ae12d8da564a223c8cdf544a98b8de61
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002868"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>Az Application insights szolgáltatással az Azure Machine Learning-modellek figyelése

Ebből a cikkből megtudhatja, hogyan állíthatja be a Azure Machine Learning Azure-Application Insights. Az Application Insights lehetővé teszi, hogy figyelése:
* Kérelem értékek, válaszidők és hibaarányok.
* Függőségi értékek, válaszidők és hibaarányok.
* Kivételek.

[További információ az Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy helyi könyvtárba, amely tartalmazza a parancsfájlokat, és az Azure Machine Learning SDK telepítve van a Pythonhoz készült Azure Machine Learning munkaterület. Ismerje meg, hogyan tehet szert az Előfeltételek, lásd: [a fejlesztési környezet konfigurálása](how-to-configure-environment.md).
* Egy betanított gépi tanulási modellt az Azure Kubernetes Service (AKS) vagy az Azure Container Instance (aci) Szolgáltatásban üzembe helyezhető. Ha még nincs fiókja, tekintse meg a [Train kép osztályozási modell](tutorial-train-models-with-aml.md) oktatóanyag.


## <a name="use-sdk-to-configure"></a>Az SDK használata a konfiguráláshoz 

### <a name="update-a-deployed-service"></a>Telepített szolgáltatások frissítésével
1. A munkaterület a szolgáltatás azonosítására. Az érték `ws` a munkaterület neve.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Frissítse a szolgáltatást, és engedélyezze az Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>A szolgáltatási egyéni nyomkövetési naplók
Ha azt szeretné, hogy egyéni nyomkövetési naplók, hajtsa végre a szokásos központi telepítési folyamat az AKS vagy az aci Szolgáltatásban a [üzembe helyezése és hol](how-to-deploy-and-where.md) dokumentumot. Ezután kövesse az alábbi lépéseket:

1. Frissítse a pontozófájl nyomtatási utasítások hozzáadásával.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. A szolgáltatás konfigurációjának frissítése.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Állítson össze egy rendszerképet, és telepítenie a [AKS](how-to-deploy-to-aks.md) vagy [ACI](how-to-deploy-to-aci.md).  

### <a name="disable-tracking-in-python"></a>Tiltsa le a Pythonban nyomon követése

Az Application Insights letiltásához használja a következő kódot:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="use-portal-to-configure"></a>A portál használata a konfiguráláshoz

Engedélyezheti és letilthatja az Application Insights az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a munkaterületet.

1. Az a **központi telepítések** lapra, válassza ki a szolgáltatást, ha szeretné engedélyezni, az Application Insights.

   [![A központi telepítések lapon szolgáltatások listája](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Válassza a **Szerkesztés** elemet.

   [![Szerkesztés gomb](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. A **speciális beállítások**, jelölje be a **engedélyezni az AppInsights-diagnosztika** jelölőnégyzetet.

   [![Diagnosztika engedélyezése a bejelölt jelölőnégyzet](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Válassza ki **frissítés** alkalmazza a módosításokat a képernyő alján. 

### <a name="disable"></a>Letiltás
1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a munkaterületet.
1. Válassza ki **központi telepítések**, válassza ki a szolgáltatást, és válassza ki **szerkesztése**.

   [![Szerkesztés gomb](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. A **speciális beállítások**, törölje a **engedélyezni az AppInsights-diagnosztika** jelölőnégyzetet. 

   [![Diagnosztika engedélyezése az üres jelölőnégyzet](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Válassza ki **frissítés** alkalmazza a módosításokat a képernyő alján. 
 

## <a name="evaluate-data"></a>Adatok kiértékelése
A szolgáltatás adatait a rendszer a Application Insights fiókjában tárolja, amely ugyanabban az erőforráscsoporthoz van, mint Azure Machine Learning.
A megtekintéséhez:
1. Nyissa meg a Machine Learning szolgáltatás munkaterületén a [az Azure portal](https://portal.azure.com) és Application Insights hivatkozásra kattintva.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Válassza ki a **áttekintése** lapján megtekintheti a metrikákat a szolgáltatás egy alapvető házirendcsoport.

   [![– Áttekintés](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Az egyéni nyomkövetési megvizsgáljuk, jelölje be **Analytics**.
4. A séma területen válassza ki a **nyomkövetések**. Válassza ki **futtatása** futtatni a lekérdezést. Adatok táblázatos formában kell megjelennie, és hozzá kell rendelnie a egyéni hívás a pontozás fájlban. 

   [![Egyéni nyomkövetési](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Az Application Insights használatával kapcsolatos további tudnivalókért lásd: [Mi az Application Insights?](../../azure-monitor/app/app-insights-overview.md).


## <a name="example-notebook"></a>Példa notebook

Az [enable-app-Insight-in-producting-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) notebook bemutatja a cikkben szereplő fogalmakat. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések
A modellek éles a is gyűjthet adatokat. A cikk a [modellek éles adatokat gyűjthessen](how-to-enable-data-collection.md). 

[A tárolók Azure monitor](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json)is olvashatja.
