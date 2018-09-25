---
title: Exportálás, Kísérletezési törölni vagy felügyeleti adatok – az Azure Machine Learning modellkezelési |} A Microsoft Docs
description: Az Azure Machine Learning exportál, vagy törölni az Azure portal, a CLI, az SDK-t és a hitelesített REST API-k a Kísérletezési vagy a modell felügyeleti kapcsolatos fiók adatait. Ez a cikk bemutatja, hogyan.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 05/22/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 7daa3bdf9fb51fee6b0e190625f07e0d14c3d1f4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995142"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Exportálhatók és nem a Kísérletezési és a gépi tanulási modell felügyeleti adatok törlése

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

Az Azure Machine Learning exportál, vagy törölje a Kísérletezési vagy a modell felügyeleti hitelesített REST API-val kapcsolatos fiókja adatait. Ez a cikk bemutatja, hogyan.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>Szabályozhatja a fiókja adatait
Terméken belüli, az Azure Machine Learning-kísérletezés és a modell felügyeleti által tárolt adatok exportálása és törlése az Azure portal, a CLI, az SDK-t és a hitelesített REST API-k keresztül érhető el. Az adatvédelem az Azure Portalon keresztül elérhető telemetriai adatokat. 

Az Azure Machine Learning a futtatási előzmények dokumentumok és a szolgáltatás egyes felhasználói interakció telemetriai rekordjait felhasználói adatokat a személyes adatok áll.

## <a name="delete-account-data-with-the-rest-api"></a>A REST API-fiók adatainak törlése 

Annak érdekében, hogy az adatok törléséhez a következő API-hívások a HTTP DELETE művelet is végezhető. Ezek jogosult létesíteni egy `Authorization: Bearer <arm-token>` a kérelemben szereplő tartományfejléc ahol `<arm-token>` van az AAD-jogkivonat a végpont `https://management.core.windows.net/` végpont.  

Ismerje meg, hogyan ez token beszerzése és az Azure-végpontok hívása, lásd: [Azure REST API-dokumentáció](https://docs.microsoft.com/rest/api/azure/).  

A következő példákban cserélje le a szöveget {} a példány nevekkel, amelyek meghatározzák a társított erőforrás.

## <a name="delete-from-a-hosting-account"></a>A szolgáltatási fiók törlése

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>A modellkezelési szolgáltatás törlése

### <a name="model-document"></a>Modell dokumentum
Ez a hívás segítségével modelleket és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Egyéni modellek törölhetők:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>A dokumentum manifest
Ez a hívás használatával minden jegyzékek és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Az egyes jegyzékek törölhetők:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>Adatszolgáltatási dokumentumok
Ez a hívás használatával az összes szolgáltatás és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Az egyes szolgáltatások is törölve:    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>Képdokumentum
Ez a hívás használatával az összes rendszerkép és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Az egyéni lemezképek törlése:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>Futtatási előzmények összetevő és értesítési adatok törlése
Projekt futtatási előzményei, az összetevő és a notification tárolók a megfelelő projektet a dokumentum törlése után törlődnek:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>Kísérletezési fiók erőforrás-szolgáltató törlése
A projekt dokumentumok törlése:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

A munkaterület dokumentumok törlése:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

A teljes Kísérletezési fiókot törölték a:
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>A REST API-val szolgáltatás az adatok exportálása
Annak érdekében, hogy az adatok exportálása a következő API-hívások a HTTP GET művelet végrehajtásával lehessen elvégezni. Ezek jogosult létesíteni egy `Authorization: Bearer <arm-token>` a kérelemben szereplő tartományfejléc ahol `<arm-token>` van az AAD-jogkivonat a végpont `https://management.core.windows.net/`  

Ismerje meg, hogyan ez token beszerzése és az Azure-végpontok hívása, lásd: [Azure REST API-dokumentáció](https://docs.microsoft.com/rest/api/azure/).   

A következő példákban cserélje le a szöveget {} a példány nevekkel, amelyek meghatározzák a társított erőforrás.

## <a name="export-hosting-account-data"></a>Szolgáltatási fiók adatainak exportálása

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>Modell felügyeleti szolgáltatás adatainak exportálása
### <a name="model-document"></a>Modell dokumentum

Ez a hívás segítségével modelleket és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Egyéni modellek szerezhető be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>Jegyzékek
Ez a hívás használatával minden jegyzékek és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Az egyes jegyzékek szerezhető be:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>Szolgáltatások
Ez a hívás használatával az összes szolgáltatás és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Egyes szolgáltatások szerezhető be: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>Képek
Ez a hívás használatával az összes rendszerkép és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Egyes szolgáltatások szerezhető be: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>Compute-adatok exportálása
### <a name="compute-clusters"></a>Számítási fürtök
Ez a hívás használatával az összes számítási fürtök és a nevük listájának lekérése:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Az egyes fürtök szerezhető be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>Fürtök operacionalizálás
Ez a hívás használatával minden fürt és a nevük listájának lekérése:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

Az egyes fürtök szerezhető be:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>Exportálás futtatási előzményadatokat
Ez a hívás használatával minden futtatás és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

Ez a hívás használatával minden kísérletet, és a hozzájuk tartozó azonosítóik listáját:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

Futtatási előzmények, elemek lekérheti:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

Futtassa a metrikák elemek lekérheti:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

Futtatási kísérleteket szerezhető be:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

Futtassa az előzmények összetevők:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

Futtassa az előzmények összetevők URI:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>Összetevők exportálása
Ez a hívás használatával eszközök és a nevük listájának lekérése:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

Ez a hívás használatával összetevők és az elérési utak listájának lekérése:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>Összetevő-tartalom

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>Összetevő-dokumentumok

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>Objektumok

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>Értesítések exportálása

1. Nyissa meg a [felhasználók szakaszban az Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/), majd válassza ki a felhasználó a **neve** oszlop. 
2. Megjegyzés: a **Objektumazonosító**, és a következő hívást használhatja:     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>Kísérletezési fiók adatainak exportálása
### <a name="experimentation-account-information"></a>Kísérletezési fiók adatait

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>Munkaterület-információk

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>Projekt adatai

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
