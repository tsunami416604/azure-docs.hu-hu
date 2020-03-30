---
title: Munkaterületi adatok exportálása vagy törlése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan exportálhatja vagy törölheti a munkaterületet az Azure Machine Learning stúdióval, a CLI-vel, az SDK-val és a hitelesített REST API-kkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 4abef0146b4bf0cfaa254d196b0ca68f0d8ac883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218291"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>A Machine Learning szolgáltatás munkaterületi adatainak exportálása vagy törlése

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learningben exportálhatja vagy törölheti a munkaterületi adatokat a hitelesített REST API-val. Ez a cikk bemutatja, hogyan.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>A munkaterületi adatok szabályozása

Az Azure Machine Learning által tárolt terméken belüli adatok az Azure Machine Learning stúdión, a CLI-n, az SDK-n és a hitelesített REST API-kon keresztül exportálhatók és tárolhatók. A telemetriai adatok az Azure Privacy portalon keresztül érhetők el. 

Az Azure Machine Learningben a személyes adatok a szolgáltatással való egyes felhasználói interakciók futtatási előzménydokumentumaiban és telemetriai rekordjaiban lévő felhasználói adatokból állnak.

## <a name="delete-workspace-data-with-the-rest-api"></a>Munkaterületi adatok törlése a REST API-val

Az adatok törléséhez a http DELETE művelettel a következő API-hívások kezdeményezhetők. Ezek a kérelemben `Authorization: Bearer <arm-token>` egy fejléc, ahol `<arm-token>` a `https://management.core.windows.net/` végpont AAD-hozzáférési jogkivonata rendelkezik.  

Ha meg szeretné tudni, hogyan szerezheti be ezt a jogkivonatot, és hogyan hívhatja meg az Azure-végpontokat, olvassa el a REST használata a ml-erőforrások és [az Azure REST API dokumentációjának](https://docs.microsoft.com/rest/api/azure/) [kezelése.](how-to-manage-rest.md)  

A következő példákban {} cserélje le a szöveget a társított erőforrást meghatározó példánynevekre.

### <a name="delete-an-entire-workspace"></a>Teljes munkaterület törlése

Ezzel a hívással egy teljes munkaterületet törölhet.  
> [!WARNING]
> Minden információ törlődik, és a munkaterület a továbbiakban nem lesz használható.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="delete-models"></a>Modellek törlése

Ezzel a hívással lehívhatja a modellek listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Az egyes modellek a következőkkel törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="delete-assets"></a>Eszközök törlése

Ezzel a hívással lehívhatja az eszközök listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Az egyes eszközök a következőkkel törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="delete-images"></a>Rendszerképek törlése

Ezzel a hívással listát kaphat a képekről és az azonosítóikról:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Az egyes képek a következő kkel törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="delete-services"></a>Szolgáltatások törlése

Ezzel a hívással lehívhatja a szolgáltatások listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Az egyes szolgáltatások a következőkkel törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

## <a name="export-service-data-with-the-rest-api"></a>Szolgáltatásadatok exportálása a REST API-val

Az adatok exportálásához a következő API-hívások kezdeményezhetők a HTTP GET-verbtel. Ezek a kérelemben `Authorization: Bearer <arm-token>` egy fejléccel vannak `<arm-token>` engedélyezve, ahol a végpont AAD-hozzáférési jogkivonata`https://management.core.windows.net/`  

Ha meg szeretné tudni, hogyan szerezheti be ezt a jogkivonatot, és hogyan hívhatja meg az Azure-végpontokat, olvassa el a REST használata a ml-erőforrások és az Azure REST API [dokumentációjának kezeléséhez.To](how-to-manage-rest.md) learn how to get this token and call Azure endpoints, see Use REST to manage ML resources and [Azure REST API documentation](https://docs.microsoft.com/rest/api/azure/)..   

A következő példákban {} cserélje le a szöveget a társított erőforrást meghatározó példánynevekre.

### <a name="export-workspace-information"></a>Munkaterületadatainak exportálása

Ezzel a hívással listát kaphat az összes munkaterületről:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2019-11-01

Az egyes munkaterületekkel kapcsolatos információk a következőkből szerezhetők be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2019-11-01

### <a name="export-compute-information"></a>Számítási adatok exportálása

A munkaterülethez csatolt összes számítási cél a következőképpen szerezhető be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2019-11-01

Egyetlen számítási célra vonatkozó információk a következőképpen szerezhetők be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2019-11-01

### <a name="export-run-history-data"></a>Futtatási előzmények adatainak exportálása

Ezzel a hívással listát kaphat az összes kísérletről és azok adatairól:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Egy adott kísérlet összes futása a következőképpen szerezhető be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

A futtatási előzményelemek a következő kérhető:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

A kísérlet összes futtatási mérőszáma a következő módon szerezhető be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Egyetlen futtatási metrika a következők ből szerezhető be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}

### <a name="export-artifacts"></a>Összetevők exportálása

Ezzel a hívással listát kaphat az összetevőkről és azok útvonalairól:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}

### <a name="export-notifications"></a>Kiviteli értesítések

Ezzel a hívással lehívhatja a tárolt feladatok listáját:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Egyetlen feladatra vonatkozó értesítések a következő címen szerezhetők be:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Adattárak exportálása

Ezzel a hívással lehívhatja az adattárak listáját:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Az egyes adattárak a következők segítségével szerezhetők be:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Modellek exportálása

Ezzel a hívással lehívhatja a modellek listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2019-11-01

Az egyes modellek a következők segítségével szerezhetők be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2019-11-01

### <a name="export-assets"></a>Exporteszközök

Ezzel a hívással lehívhatja az eszközök listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2019-11-01

Az egyes eszközök a következőkből szerezhetők be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2019-11-01

### <a name="export-images"></a>Képek exportálása

Ezzel a hívással listát kaphat a képekről és az azonosítóikról:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2019-11-01

Az egyes képek a következőkből szerezhetők be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2019-11-01

### <a name="export-services"></a>Exportszolgáltatások

Ezzel a hívással lehívhatja a szolgáltatások listáját és azonosítóit:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2019-11-01

Az egyes szolgáltatások a következők segítségével szerezhetők be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2019-11-01

### <a name="export-pipeline-experiments"></a>Csővezeték-kísérletek exportálása

Az egyes kísérletek a következők segítségével szerezhetők be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Csővezeték-diagramok exportálása

Az egyes grafikonok a következők segítségével szerezhetők be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Folyamatmodulok exportálása

A modulok a következők segítségével szerezhetők be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Folyamatsablonok exportálása

A sablonok a következő kérhetőek:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Folyamatadatforrások exportálása

Az adatforrások a következők segítségével szerezhetők be:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}

## <a name="delete-assets-in-the-designer"></a>Eszközök törlése a tervezőben

Abban a tervezőben, ahol a kísérletet létrehozta, törölje az egyes kellékeket:

1. Ugrás a tervezőre

    ![Eszközök törlése](./media/how-to-export-delete-data/delete-experiment.png)

1. A listában válassza ki a törölni kívánt egyes folyamatpiszkozatot.

1. Válassza a **Törlés** elemet.

### <a name="delete-datasets-in-the-designer"></a>Adatkészletek törlése a tervezőben

Adatkészletek törléséhez a tervező, használja az Azure Portalon vagy a Storage Explorer a csatlakoztatott tárfiókok és az adatkészletek törlése ott. Az adatkészletek név nélküli bejegyzésének megszüntetése a tervezőben csak a tárolóban lévő referenciapontot távolítja el.

## <a name="export-data-in-the-designer"></a>Adatok exportálása a tervezőben

Abban a tervezőben, ahol a kísérletet létrehozta, exportálja a hozzáadott adatokat:

1. A bal oldalon válassza az **Adatkészletek**lehetőséget.

1. A listában jelölje ki az exportálni kívánt adatkészletet.

    ![Adatok letöltése](./media/how-to-export-delete-data/unregister-dataset.png)
