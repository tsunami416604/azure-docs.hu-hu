---
title: Az application insights éles környezetben az Azure Machine Learning szolgáltatás engedélyezése
description: Ismerje meg, hogyan állíthatja be az Application Insights az Azure Machine Learning szolgáltatás egy Azure Kubernetes Service-ben üzembe helyezett
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 45871ab515c7ffd9520b1d77d3fd1e77abcc29ef
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114566"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Az Azure Machine Learning-modellek éles környezetben az Application Insights figyelése

Ebből a cikkből megtudhatja, hogyan állítható be **Application Insights** számára a **Azure Machine Learning** szolgáltatás. Ha engedélyezve van, az Application Insights lehetővé teszi, hogy figyelése:
* Értékek, válaszidők és hibaarányok kérése
* Függőségi értékek, válaszidők és hibaarányok
* Kivételek

További információ az Application Insights [Itt](../../application-insights/app-insights-overview.md). 

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.
* Az Azure Machine Learning-munkaterület, a parancsprogramok, és az Azure Machine Learning SDK-t tartalmazó Python telepítve van egy helyi könyvtárba. Ezekről az előfeltételekről használatával beszerzéséről a [a fejlesztési környezet konfigurálása](how-to-configure-environment.md) dokumentumot.
* Egy betanított gépi tanulási modellt az Azure Kubernetes Service (AKS) üzembe helyezni. Ha még nincs fiókja, tekintse meg a [kép osztályozási modell betanításához](tutorial-train-models-with-aml.md) oktatóanyag.
* Egy [AKS-fürt](how-to-deploy-to-aks.md).

## <a name="enable--disable-in-the-portal"></a>Tiltsa le a portálon & engedélyezése

Engedélyezheti és letilthatja az Application Insights az Azure Portalon.

### <a name="enable"></a>Bekapcsolás

1. A [az Azure portal](https://portal.azure.com), nyissa meg a munkaterületet.

1. Az üzemelő példányok lép, és válassza ki a szolgáltatást, ha szeretné engedélyezni, az Application Insights.

   [![Központi telepítések](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Kattintson a **szerkesztése** , majd **speciális beállítások**.

   [![Szerkesztése](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. A **speciális beállítások** kiválasztása **engedélyezze az Application Insights diagnosztikai**.

   [![Szerkesztése](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Válassza ki **frissítés** alkalmazza a módosításokat a képernyő alján. 

### <a name="disable"></a>Letiltás
Az Azure Portalon, tiltsa le az Application Insights, tegye a következőket:

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com.
1. Lépjen a munkaterülethez.
1. Válassza a **központi telepítések**, majd **szolgáltatás kiválasztása**, majd **szerkesztése**.

   [![Szerkesztése](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. A **speciális beállítások**, törölje a jelet a jelölőnégyzetből **engedélyezni az AppInsights-diagnosztika**. 

   [![Törölje a jelet](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Válassza ki **frissítés** alkalmazza a módosításokat a képernyő alján. 

## <a name="enable--disable-from-the-sdk"></a>Tiltsa le az SDK-ból & engedélyezése

### <a name="update-a-deployed-service"></a>Telepített szolgáltatások frissítésével
1. A munkaterület a szolgáltatás azonosítására (ws = a munkaterület neve)

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Frissítse a szolgáltatást, és engedélyezze az Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>A szolgáltatási egyéni nyomkövetési naplók
Ha azt szeretné, hogy egyéni nyomkövetési naplók, akkor kövesse a [szabványos telepítési folyamat az aks-ben](how-to-deploy-to-aks.md) , és a:

1. Frissítse a pontozófájl nyomtatási utasítások hozzáadásával.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aks-konfigurációjának frissítése.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Rendszerkép összeállítása és üzembe helyezni.](how-to-deploy-to-aks.md)  

### <a name="disable-tracking-in-python"></a>Tiltsa le a Pythonban nyomon követése

Az Application Insights letiltásához használja a következő kódot:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>Adatok kiértékelése
A szolgáltatás az adatok beolvasása az Application Insights-fiók van az Azure Machine Learning szolgáltatás munkaterület ugyanabban az erőforráscsoportban tárolja.
A megtekintéséhez:
1. Nyissa meg az erőforráscsoportot a [az Azure portal](https://portal.azure.com) az Application Insights-erőforrást, majd. 
2. A **áttekintése** a lapon megjelenik a szolgáltatás metrikák alapszintű készlete.

   [![– Áttekintés](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Az egyéni nyomkövetési kattintson megvizsgáljuk **Analytics**.
4. A séma területen kattintson a **nyomkövetések** , majd **futtatása** a lekérdezést. Adatok meg kell jelennie egy táblázatos formátumú le alatt, és leképezhető a egyéni hívásokat a pontozófájlt a. 

   [![Egyéni nyomkövetési](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Kattintson a [Itt](../../application-insights/app-insights-overview.md) további információ az Application Insights használatával.
    

## <a name="example-notebook"></a>Példa Notebook

A [00. első Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) notebook mutatja be a jelen cikk fogalmait.  Ez a jegyzetfüzet lekérése:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések
A modellek éles a is gyűjthet adatokat. A cikk a [a modellek éles adatok gyűjtése](how-to-enable-data-collection.md) 
