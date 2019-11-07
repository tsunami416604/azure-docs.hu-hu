---
title: Munkaterület-adatexportálás vagy-törlés
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan exportálhatja vagy törölheti a munkaterületet a Azure Machine Learning Studio, CLI, SDK és hitelesített REST API-k használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 34ff3f9704b9c84a7daddcfd14fb9cd3e990f794
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716513"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Machine Learning szolgáltatás-munkaterület adatainak exportálása vagy törlése 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning a munkaterület adatait exportálhatja vagy törölheti a hitelesített REST API használatával. Ebből a cikkből megtudhatja, hogyan.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>A munkaterület adatainak szabályozása
A Azure Machine Learning által tárolt termékbeli adatok a Azure Machine Learning Studio, a CLI, az SDK és a hitelesített REST API-k használatával exportálhatók és törlésre használhatók. A telemetria-adatok az Azure adatvédelmi portálján érhetők el. 

Azure Machine Learning a személyes adatok a futtatási előzmények dokumentumaiban lévő felhasználói adatokból és a szolgáltatással kapcsolatos egyes felhasználói interakciók telemetria állnak.

## <a name="delete-workspace-data-with-the-rest-api"></a>Munkaterület-adatbázis törlése a REST API 

Az adattörléshez a következő API-hívások hajthatók végre a HTTP-TÖRLÉSi művelettel. Ezek a kérelemben `Authorization: Bearer <arm-token>` fejléctel rendelkeznek, ahol a `<arm-token>` az `https://management.core.windows.net/` végpont HRE hozzáférési jogkivonata.  

A jogkivonat beszerzéséről és az Azure-végpontok meghívásáról az [azure REST API dokumentációjában](https://docs.microsoft.com/rest/api/azure/)talál további információt.  

Az alábbi példákban cserélje le a {} szövegét a társított erőforrást meghatározó példányok neveire.

### <a name="delete-an-entire-workspace"></a>Teljes munkaterület törlése

Ez a hívás egy teljes munkaterület törlésére használható.  
> [!WARNING]
> Minden információ törölve lesz, és a munkaterület többé nem lesz használható.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Modellek törlése

Ezzel a hívással lekérheti a modellek listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Az egyes modellek a használatával törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Eszközök törlése

Ezzel a hívással lekérheti az eszközök és azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Az egyes eszközök a használatával törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Lemezképek törlése

Használja ezt a hívást a rendszerképek és azonosítók listájának lekéréséhez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Az egyes lemezképek a használatával törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Szolgáltatások törlése

Ezzel a hívással lekérheti a szolgáltatások listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Az egyes szolgáltatások a használatával törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>A szolgáltatásban tárolt adatértékek exportálása a REST API

Az adatexportáláshoz a következő API-hívások hajthatók végre a HTTP GET művelettel. Ezek a kérelemben `Authorization: Bearer <arm-token>` fejléctel rendelkeznek, ahol `<arm-token>` a végpont HRE hozzáférési jogkivonata `https://management.core.windows.net/`  

A jogkivonat beszerzéséről és az Azure-végpontok meghívásáról az [azure REST API dokumentációjában](https://docs.microsoft.com/rest/api/azure/)talál további információt.   

Az alábbi példákban cserélje le a {} szövegét a társított erőforrást meghatározó példányok neveire.

### <a name="export-workspace-information"></a>Munkaterület adatainak exportálása

Ezzel a hívással lekérheti az összes munkaterület listáját:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Az egyes munkaterületekkel kapcsolatos információk az alábbiakkal szerezhetők be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Számítási adatok exportálása

A munkaterülethez csatolt számítási célok a következővel szerezhetők be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Az egyetlen számítási célhoz tartozó információk az alábbiak szerint szerezhetők be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Futtatási előzményekre vonatkozó adatexportálás
Használja ezt a hívást az összes kísérlet és a hozzájuk tartozó információk listájának lekéréséhez:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Egy adott kísérlet összes futtatása a következővel szerezhető be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

A futtatási előzmények elemek a következőkkel szerezhetők be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

A kísérletekhez tartozó összes futtatási metrikát a következővel lehet beszerezni:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Egyetlen futtatási metrikát a:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Összetevők exportálása

Használja ezt a hívást az összetevők és az elérési utak listájának lekéréséhez:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Értesítések exportálása

Használja ezt a hívást a tárolt feladatok listájának lekéréséhez:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Egyetlen feladatra vonatkozó értesítések a következővel szerezhetők be:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Adattárak exportálása

Használja ezt a hívást az adattárak listájának lekéréséhez:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Az egyes adattárakat a alábbiak érhetik el:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Modellek exportálása

Ezzel a hívással lekérheti a modellek listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Az egyes modelleket az alábbiak alapján szerezheti be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Eszközök exportálása

Ezzel a hívással lekérheti az eszközök és azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Az egyes eszközök az alábbiak szerint szerezhetők be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Lemezképek exportálása

Használja ezt a hívást a rendszerképek és azonosítók listájának lekéréséhez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Az egyes rendszerképeket az alábbiak szerint lehet beszerezni:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Szolgáltatások exportálása

Ezzel a hívással lekérheti a szolgáltatások listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Az egyes szolgáltatásokhoz a következőket lehet beszerezni:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Folyamat-kísérletek exportálása

Az egyes kísérletek a alábbiak szerint szerezhetők be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Folyamat-diagramok exportálása

Az egyes diagramok a segítségével szerezhetők be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Folyamat moduljainak exportálása

A modulok a használatával szerezhetők be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Folyamat-sablonok exportálása

A sablonok a következőket tudják beszerezni:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Folyamat-adatforrások exportálása

Az adatforrások az alábbiak szerint szerezhetők be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Eszközök törlése a tervezőben

A tervezőben, ahol létrehozta a kísérletet, töröljön egyedi eszközöket:

1. Ugrás a tervezőbe

    ![Eszközök törlése](media/how-to-export-delete-data.md/delete-experiment.png)

1. A listából válassza ki a törölni kívánt folyamathoz tartozó egyéni piszkozatot.

1. Válassza a **Törlés** elemet.

### <a name="delete-datasets-in-the-designer"></a>Adatkészletek törlése a tervezőben

A tervezőben lévő adatkészletek törléséhez használja a Azure Portal vagy Storage Explorer a csatlakoztatott Storage-fiókok eléréséhez, és ott törölje az adatkészleteket. Az adatkészletek tervezőben való regisztrációjának törlése csak a tárolóban lévő hivatkozási pontot távolítja el. 

## <a name="export-data-in-the-designer"></a>Adatexportálás a tervezőben

A tervezőben, amelyben létrehozta a kísérletet, exportálja a hozzáadott adatait:

1. A bal oldalon válassza az **adatkészletek**lehetőséget.

    ![Az adatletöltés](media/how-to-export-delete-data.md/unregister-dataset.png)
