---
title: Az Application Insights az Azure Machine Learning szolgáltatás engedélyezése
description: Ismerje meg, hogyan állíthatja be az Application Insights az Azure Machine Learning szolgáltatás használatával telepített szolgáltatások
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 9e0f07e744aaf5f1c35666b40285937dce6dd4de
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275054"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>Az Application insights szolgáltatással az Azure Machine Learning-modellek figyelése

Ebből a cikkből elsajátíthatja az Azure Application Insights beállítása az Azure Machine Learning szolgáltatás. Az Application Insights lehetővé teszi, hogy figyelése:
* Kérelem értékek, válaszidők és hibaarányok.
* Függőségi értékek, válaszidők és hibaarányok.
* Kivételek.

[További információ az Application Insights](../../application-insights/app-insights-overview.md). 

>[!NOTE]
> Ebben a cikkben kód teszteltük az Azure Machine Learning SDK verziója 0.1.74


## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLfree), mielőtt hozzákezd.
* Egy helyi könyvtárba, amely tartalmazza a parancsfájlokat, és az Azure Machine Learning SDK telepítve van a Pythonhoz készült Azure Machine Learning munkaterület. Ismerje meg, hogyan tehet szert az Előfeltételek, lásd: [a fejlesztési környezet konfigurálása](how-to-configure-environment.md).
* Egy betanított gépi tanulási modellt az Azure Kubernetes Service (AKS) vagy az Azure Container Instance (aci) Szolgáltatásban üzembe helyezhető. Ha még nincs fiókja, tekintse meg a [Train kép osztályozási modell](tutorial-train-models-with-aml.md) oktatóanyag.


## <a name="enable-and-disable-from-the-sdk"></a>Engedélyezheti vagy letilthatja az SDK-ból

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
Ha azt szeretné, hogy egyéni nyomkövetési naplók, kövesse a normál telepítés folyamata [AKS](how-to-deploy-to-aks.md) vagy [ACI](how-to-deploy-to-aci.md) . Ezután:

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
    
## <a name="enable-and-disable-in-the-portal"></a>Engedélyezheti vagy letilthatja a portálon

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
A szolgáltatás adatainak az Application Insights-fiók, az Azure Machine Learning szolgáltatás ugyanabban az erőforráscsoportban találhatók.
A megtekintéséhez:
1. Nyissa meg a Machine Learning szolgáltatás munkaterületén a [az Azure portal](https://portal.azure.com) és Application Insights hivatkozásra kattintva.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Válassza ki a **áttekintése** lapján megtekintheti a metrikákat a szolgáltatás egy alapvető házirendcsoport.

   [![– Áttekintés](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Az egyéni nyomkövetési megvizsgáljuk, jelölje be **Analytics**.
4. A séma területen válassza ki a **nyomkövetések**. Válassza ki **futtatása** futtatni a lekérdezést. Adatok táblázatos formában kell megjelennie, és hozzá kell rendelnie a egyéni hívás a pontozás fájlban. 

   [![Egyéni nyomkövetési](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Az Application Insights használatával kapcsolatos további tudnivalókért lásd: [Mi az Application Insights?](../../application-insights/app-insights-overview.md).
    

## <a name="example-notebook"></a>Példa notebook

A [00. első Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) notebook mutatja be a jelen cikk fogalmait.  Ez a jegyzetfüzet lekérése:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések
A modellek éles a is gyűjthet adatokat. A cikk a [modellek éles adatokat gyűjthessen](how-to-enable-data-collection.md). 


## <a name="other-references"></a>Egyéb segédanyagok
* [Az Azure Monitor for containers szolgáltatásban](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json)
