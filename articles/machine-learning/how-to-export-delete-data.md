---
title: Munkaterület-adatexportálás vagy-törlés
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan exportálhatja vagy törölheti a munkaterületet a Azure Machine Learning Studio, CLI, SDK és hitelesített REST API-k használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.custom: seodec18
ms.openlocfilehash: 5d10228eb76f95a7d5225b6b414ca622c92bf1c5
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434276"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Machine Learning szolgáltatás-munkaterület adatainak exportálása vagy törlése

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning a munkaterület adatait exportálhatja vagy törölheti a hitelesített REST API használatával. Ebből a cikkből megtudhatja, hogyan.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>A munkaterület adatainak szabályozása

A Azure Machine Learning által tárolt termékbeli adatok a Azure Machine Learning Studio, a CLI, az SDK és a hitelesített REST API-k használatával exportálhatók és törlésre használhatók. A telemetria-adatok az Azure adatvédelmi portálján érhetők el. 

Azure Machine Learning a személyes adatok a futtatási előzmények dokumentumaiban lévő felhasználói adatokból és a szolgáltatással kapcsolatos egyes felhasználói interakciók telemetria állnak.

## <a name="delete-workspace-data-with-the-rest-api"></a>Munkaterület-adatbázis törlése a REST API

Az adattörléshez a következő API-hívások hajthatók végre a HTTP-TÖRLÉSi művelettel. Ezeket a rendszer a `Authorization: Bearer <arm-token>` kérelem fejlécének engedélyezésével engedélyezi, ahol a a `<arm-token>` végpont HRE hozzáférési jogkivonata `https://management.core.windows.net/` .  

Ha meg szeretné tudni, hogyan kérheti le ezt a tokent, és hogyan hívhat meg Azure-végpontokat, tekintse meg a további tudnivalók az [ml-erőforrások és az](how-to-manage-rest.md) [Azure REST API](https://docs.microsoft.com/rest/api/azure/)  

Az alábbi példákban cserélje le a szöveget a {} társított erőforrást meghatározó példányok neveire.

### <a name="delete-an-entire-workspace"></a>Teljes munkaterület törlése

Ez a hívás egy teljes munkaterület törlésére használható.  
> [!WARNING]
> Minden információ törölve lesz, és a munkaterület többé nem lesz használható.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Modellek törlése

Ezzel a hívással lekérheti a modellek listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Az egyes modellek a használatával törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Eszközök törlése

Ezzel a hívással lekérheti az eszközök és azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Az egyes eszközök a használatával törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Rendszerképek törlése

Használja ezt a hívást a rendszerképek és azonosítók listájának lekéréséhez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Az egyes lemezképek a használatával törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Szolgáltatások törlése

Ezzel a hívással lekérheti a szolgáltatások listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Az egyes szolgáltatások a használatával törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>A szolgáltatásban tárolt adatértékek exportálása a REST API

Az adatexportáláshoz a következő API-hívások hajthatók végre a HTTP GET művelettel. Ezeket a rendszer a `Authorization: Bearer <arm-token>` kérelem fejlécének engedélyezésével engedélyezi, ahol a a `<arm-token>` végpont HRE hozzáférési jogkivonata`https://management.core.windows.net/`  

Ha meg szeretné tudni, hogyan kérheti le ezt a tokent, és hogyan hívhat meg Azure-végpontokat, tekintse meg a további tudnivalók az [ml-erőforrások és az](how-to-manage-rest.md) [Azure REST API dokumentáció](https://docs.microsoft.com/rest/api/azure/)   

Az alábbi példákban cserélje le a szöveget a {} társított erőforrást meghatározó példányok neveire.

### <a name="export-workspace-information"></a>Munkaterület adatainak exportálása

Ezzel a hívással lekérheti az összes munkaterület listáját:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Az egyes munkaterületekkel kapcsolatos információk az alábbiakkal szerezhetők be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Számítási adatok exportálása

A munkaterülethez csatolt számítási célok a következővel szerezhetők be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

Az egyetlen számítási célhoz tartozó információk az alábbiak szerint szerezhetők be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

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

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Az egyes modelleket az alábbiak alapján szerezheti be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Eszközök exportálása

Ezzel a hívással lekérheti az eszközök és azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Az egyes eszközök az alábbiak szerint szerezhetők be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Lemezképek exportálása

Használja ezt a hívást a rendszerképek és azonosítók listájának lekéréséhez:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Az egyes rendszerképeket az alábbiak szerint lehet beszerezni:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Szolgáltatások exportálása

Ezzel a hívással lekérheti a szolgáltatások listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Az egyes szolgáltatásokhoz a következőket lehet beszerezni:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

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

    ![Eszközök törlése](./media/how-to-export-delete-data/delete-experiment.png)

1. A listából válassza ki a törölni kívánt folyamathoz tartozó egyéni piszkozatot.

1. Válassza a **Törlés** elemet.

### <a name="delete-datasets-in-the-designer"></a>Adatkészletek törlése a tervezőben

A tervezőben lévő adatkészletek törléséhez használja a Azure Portal vagy Storage Explorer a csatlakoztatott Storage-fiókok eléréséhez, és ott törölje az adatkészleteket. Az adatkészletek tervezőben való regisztrációjának törlése csak a tárolóban lévő hivatkozási pontot távolítja el.

