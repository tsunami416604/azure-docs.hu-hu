---
title: Exportálja, kísérletezhet törölni vagy felügyeleti adatok - Azure Machine Learning |} Microsoft Docs
description: Az Azure Machine Learning exportál, vagy törölje a kapcsolódik kísérletezhet vagy a modell-kezelés az Azure-portálon, a CLI, az SDK és a hitelesített REST API-k az fiók adatait. Ez a cikk bemutatja, hogyan.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 7db37865c99908e0fd44be3ec04a8493d190e941
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833512"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Exportálja, vagy a kísérleti vagy gépi tanulási modell management adatok törlése

Az Azure Machine Learning exportálása, vagy a fiók kísérletezés vagy a modell felügyeleti hitelesített REST API-val kapcsolatos adatok törlése. Ez a cikk azt ismerteti, hogyan.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>A fiókadatokhoz szabályozása
Azure Machine Learning kísérletezhet és modell felügyeleti tárolt a terméken belüli adatokat az exportálás és törlése az Azure-portálon, a CLI, az SDK és a hitelesített REST API-k keresztül érhető el. Telemetriai adatok elérhetők az adatvédelmi Azure portálon keresztül. 

Az Azure Machine Learning a személyes adatok áll futtatási előzményei dokumentumok és telemetriai rögzíti a szolgáltatás egyes felhasználói interakció a felhasználói adatokat.

## <a name="delete-account-data-with-the-rest-api"></a>A REST API-val fiók adatok törlése 

Ahhoz, hogy az adatok törléséhez a következő API-hívások a HTTP DELETE művelet. Ezek azzal, hogy jogosult egy `Authorization: Bearer <arm-token>` fejléc a következő a kérelem, ahol `<arm-token>` van az AAD-jogkivonat a végpont `https://management.core.windows.net/` végpont.  

Szerezze be a tokent, és hívja az Azure-végpontok, lásd: [Azure REST API-dokumentáció](https://docs.microsoft.com/rest/api/azure/).  

A következő példákban cserélje le a szöveget {} a társított erőforrást meghatározó példánynevekkel.

## <a name="delete-from-a-hosting-account"></a>A birtokosi fiók törlése

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>A modell felügyeleti szolgáltatás törlése

### <a name="model-document"></a>Modell dokumentum
Ez a hívás segítségével modellek és azok azonosítók listájának beolvasása:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Egyes modellek törölhető:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>Jegyzékdokumentum
Ez a hívás segítségével minden jegyzékfájlban és azok azonosítók listájának beolvasása:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Egyes jegyzékfájlokat törölhető:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>Dokumentumok
Ez a hívás segítségével az összes szolgáltatás és az azonosítók listájának beolvasása:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Egyes szolgáltatások törölhetők:    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>Kép dokumentum
Ez a hívás segítségével összes lemezkép és az azonosítók listájának beolvasása:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Egyéni képek törölhető:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>Futtatási előzmények, az összetevő és az értesítési adatok törlése
A projekt futtatási előzmények, az összetevő és az értesítési tárolóinak a megfelelő projektet dokumentum törlése után törlődnek:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>Kísérletezhet fiók erőforrás-szolgáltató törlése
Projekt dokumentumok rendelkező törlődnek:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

Munkaterület dokumentumok rendelkező törlődnek:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

A teljes kísérletezhet a fiókot törölték a:
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>A REST API-t szolgáltatás adatok exportálása
Ahhoz, hogy az adatok exportálása, a következő API-hívások a HTTP GET művelet. Ezek jogosult azzal, hogy egy `Authorization: Bearer <arm-token>` fejléc a következő a kérelem, ahol `<arm-token>` van az AAD-jogkivonat a végpont `https://management.core.windows.net/`  

Szerezze be a tokent, és hívja az Azure-végpontok, lásd: [Azure REST API-dokumentáció](https://docs.microsoft.com/rest/api/azure/).   

A következő példákban cserélje le a szöveget {} a társított erőforrást meghatározó példánynevekkel.

## <a name="export-hosting-account-data"></a>Futtató fiók adatainak exportálása

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>Modell felügyeleti szolgáltatás adatok exportálása
### <a name="model-document"></a>Modell dokumentum

Ez a hívás segítségével modellek és azok azonosítók listájának beolvasása:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

Egyes modellek kérhetők le:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>Jegyzékek
Ez a hívás segítségével minden jegyzékfájlban és azok azonosítók listájának beolvasása:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

Egyes jegyzékfájlokat kérhetők le:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>Szolgáltatások
Ez a hívás segítségével az összes szolgáltatás és az azonosítók listájának beolvasása:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

Egyes szolgáltatások kérhetők le: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>Képek
Ez a hívás segítségével összes lemezkép és az azonosítók listájának beolvasása:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Egyes szolgáltatások kérhetők le: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>Számítási adatok exportálása
### <a name="compute-clusters"></a>Számítási fürtök
Ez a hívás segítségével minden számítási fürt és a nevek listája:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Az egyes fürtök kérhetők le:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>Operationalization fürtök
Ez a hívás segítségével az összes fürt és a nevek listáját:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

Az egyes fürtök kérhetők le:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>Exportálás futtatási előzményeit
Ez a hívás segítségével az összes futtatása és a azonosítók listáját:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

Ez a hívás segítségével minden kísérletet, és azok azonosítók listájának beolvasása:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

Futtatási előzményei, elemek kérhetők le:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

Futtassa a metrikák elemek kérhetők le:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

Futtatási kísérletek kérhetők le:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

Futtassa az előzmények összetevők:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

Futtassa az előzmények összetevők URI-azonosítók:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>Az összetevők exportálása
Ez a hívás használja az eszközök és a nevek listáját:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

Ez a hívás segítségével összetevők és az elérési utak listájának beolvasása:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>Összetevő tartalma

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>Összetevő dokumentumok

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>Objektumok

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>Értesítések exportálása

1. Lépjen a [az Azure-portálon a felhasználók szakaszban](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/), majd válassza ki a felhasználó a **neve** oszlop. 
2. Megjegyzés: a **Objektumazonosító**, és a következő hívást használhatja:     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>Kísérletezhet fiókadatok
### <a name="experimentation-account-information"></a>Kísérletezhet fiókadatok

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>Munkaterület-információk

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>Projekt adatai

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
