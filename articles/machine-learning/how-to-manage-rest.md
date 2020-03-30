---
title: A REST segítségével kezelheti a pénzmosáselleni erőforrásokat
titleSuffix: Azure Machine Learning
description: REST API-k használata Azure ML-erőforrások létrehozásához, futtatásához és törléséhez
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77580628"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Azure ML-erőforrások létrehozása, futtatása és törlése a REST használatával

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure ML-erőforrások kezelésére számos módon kezelhetők. Használhatja a [portált](https://portal.azure.com/), [a parancssori felületet](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)vagy a [Python SDK-t.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Vagy választhatja a REST API-t. A REST API http-műveleteket használ szabványos módon erőforrások létrehozására, beolvasására, frissítésére és törlésére. A REST API minden olyan nyelvvel vagy eszközzel működik, amely HTTP-kérelmeket tud letenni. A REST egyszerű struktúrája gyakran jó választássá teszi a parancsfájlok futtatásához és az MLOps-automatizáláshoz. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Engedélyezési jogkivonat beolvasása
> * Megfelelően formázott REST-kérelem létrehozása egyszerű szolgáltatáshitelesítéssel
> * Get-kérelmek használatával az Azure ML hierarchikus erőforrásaival kapcsolatos információk lekérése
> * Erőforrások létrehozása és módosítása PUT és POST kérelmek használatával
> * Erőforrások törlése DELETE kérelmek használatával 
> * Kulcsalapú engedélyezés használata az üzembe helyezett modellek pontozásához

## <a name="prerequisites"></a>Előfeltételek

- Olyan **Azure-előfizetés,** amelyhez rendszergazdai jogokkal rendelkezik. Ha nincs ilyen előfizetése, próbálja ki az [ingyenes vagy fizetős személyes előfizetést](https://aka.ms/AMLFree)
- [Egy Azure Machine Learning-munkaterület](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- A felügyeleti REST-kérelmek egyszerű szolgáltatáshitelesítést használnak. Kövesse az [Azure Machine Learning-erőforrások és -munkafolyamatok hitelesítésének beállítása című lépéseit,](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) és hozzon létre egy egyszerű szolgáltatásszolgáltatást a munkaterületen
- A **göndör** segédprogram. A **curl** program elérhető a [Windows Alrendszer Linux](https://aka.ms/wslinstall/) vagy bármely UNIX disztribúció. A PowerShellben a **curl** az **Invoke-WebRequest aliasa,** és `curl -d "key=val" -X POST uri` a . `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` 

## <a name="retrieve-a-service-principal-authentication-token"></a>Egyszerű szolgáltatáshitelesítési jogkivonat beolvasása

A felügyeleti REST-kérelmek hitelesítése OAuth2 implicit folyamattal van hitelesítve. Ez a hitelesítési folyamat az előfizetés egyszerű szolgáltatása által biztosított jogkivonatot használ. A token beolvasásához a következőkre van szükség:

- A bérlőazonosító (annak a szervezetnek az azonosítása, amelyhez az előfizetés tartozik)
- Az ügyfélazonosító (amely a létrehozott jogkivonathoz lesz társítva)
- Az ügyfél titkos (amit meg kell védeni)

Ezeket az értékeket az egyszerű szolgáltatás létrehozására adott válaszból kell rendelkeznie az [Azure Machine Learning-erőforrások és -munkafolyamatok hitelesítésének beállítása](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication)című részében tárgyaltak szerint. Ha a vállalati előfizetés, lehet, hogy nincs engedélye, hogy hozzon létre egy egyszerű szolgáltatás. Ebben az esetben [ingyenes vagy fizetős személyes előfizetést](https://aka.ms/AMLFree)kell használnia.

Token beolvasása:

1. Nyisson meg egy terminálablakot.
1. Írja be a következő kódot a parancssorba
1. Helyettesítse saját `{your-tenant-id}` `{your-client-id}`értékeit `{your-client-secret}`a és a lehetőségre. Ebben a cikkben a göndör zárójelekkel körülvett karakterláncok olyan változók, amelyeket a saját megfelelő értékekkel kell helyettesítenie.
1. Futtassa a

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

A válasznak egy órán keresztül egy hozzáférési jogkivonatot kell biztosítania:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Jegyezze fel a jogkivonatot, mert az összes további felügyeleti kérelem hitelesítésére fogja használni. Ezt úgy teheti meg, hogy engedélyezési fejlécet állít be az összes kérelemben:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Vegye figyelembe, hogy az érték a "Bemutatóra" karakterlánccal kezdődik, amely egyetlen helyet foglal magában a jogkivonat hozzáadása előtt.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Az előfizetéshez társított erőforráscsoportok listájának beszereznie

Az előfizetéshez társított erőforráscsoportok listájának lekéréséhez futtassa a következőt:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Az Azure-ban számos REST API-k vannak közzétéve. Minden szolgáltató saját ütemben frissíti az API-t, de ezt a meglévő programok feltörése nélkül teszi. A szolgáltató az `api-version` argumentumot használja a kompatibilitás biztosítása érdekében. Az `api-version` argumentum szolgáltatásonként változik. A Machine Learning szolgáltatás esetében például a `2019-11-01`jelenlegi API-verzió a . A tárfiókok esetében `2019-06-01`ez a . A kulcstartók, ez `2019-09-01`. Minden REST-hívásnak `api-version` a várt értékre kell állítania az argumentumot. Támaszkodhat a szintaxis és a szemantika a megadott verzió tisa folyamatosan fejlődik. Ha `api-version` egy kérelmet az argumentum nélkül küld egy szolgáltatónak, a válasz a támogatott értékek ember által olvasható listáját fogja tartalmazni. 

A fenti hívás eredményeként az űrlap tömörített JSON válasza: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Részletezés a munkaterületekre és erőforrásaikra

Az erőforráscsoport munkaterületeinek beolvasásához futtassa a következőt, helyettesítve `{your-subscription-id}`a `{your-resource-group}`és `{your-access-token}`a : 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Ismét kap egy JSON listát, ezúttal tartalmaz egy listát, amelynek minden eleme részletezi a munkaterületet:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

A munkaterületen belüli erőforrásokkal való munkához az általános **management.azure.com** kiszolgálóról a munkaterület helyére vonatkozó REST API-kiszolgálóra vált. Figyelje meg `discoveryUrl` a kulcs értékét a fenti JSON-válaszban. Ha megkapja ezt az URL-t, a következőválaszt kapja:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

A válasz `api` értéke annak a kiszolgálónak az URL-címe, amelyet további kérésekhez fog használni. A kísérletek listázásához például küldje el a következő parancsot. Cserélje `regional-api-server` le a `api` válasz értékére `centralus.api.azureml.ms`(például ). A `your-subscription-id`, `your-resource-group` `your-workspace-name`, `your-access-token` és a szokásos módon:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Hasonlóképpen, a regisztrált modellek beolvasásához a munkaterületen, küldje el:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Figyelje meg, hogy a `history/v1.0` modellek listázása korával `modelmanagement/v1.0`kezdődő kísérletek listázásához az elérési út a . A REST API több operatív csoportra van osztva, mindegyik külön elérési úttal. Az API-referencia-dokumentumok az alábbi linkeken a műveletek, paraméterek és válaszkódok a különböző műveletek.

|Terület|Útvonal|Referencia|
|-|-|-|
|Összetevők|műtárgy/v2.0/|[REST API-referencia](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|Adattárolók|adattár/v1.0/|[REST API-referencia](https://docs.microsoft.com/rest/api/azureml/datastores)|
|Hiperparaméterek finomhangolása|hiperhajtómű/v1.0/|[REST API-referencia](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Modellek|modellmenedzsment/v1.0/|[REST API-referencia](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|Előzmények|végrehajtás/v1.0/ és előzmények/1.0/|[REST API-referencia](https://docs.microsoft.com/rest/api/azureml/runs)|

A REST API-t a következők általános mintájával fedezheti fel:

|URL-összetevő|Példa|
|-|-|
| https://| |
| regionális-api-szerver/ | centralus.api.azureml.ms/ |
| műveleti-útvonal/ | előzmények/1.0/ |
| előfizetések/{előfizetés-id}/ | előfizetések/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{your-resource-group}/ | resourceGroups/SajatResourceGroup/ |
| szolgáltatók/műveletszolgáltató/ | szolgáltatók/Microsoft.MachineLearningServices/ |
| szolgáltató-erőforrás-elérési út/ | munkaterületek/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| műveleti végpont/ | összetevők/metaadatok/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Erőforrások létrehozása és módosítása PUT és POST kérésekkel

A GET-műveleterőforrás-lekérés mellett a REST API támogatja az ml-megoldások betanításához, üzembe helyezéséhez és figyeléséhez szükséges összes erőforrás létrehozását. 

A betanításés és az ML-modellek futtatása számítási erőforrásokat igényel. A munkaterület számítási erőforrásait a következő kkel sorathatja fel: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Egy elnevezett számítási erőforrás létrehozásához vagy felülírásához put-kérelmet kell használnia. A következőkben a `your-subscription-id`, `your-resource-group`, , `your-workspace-name`, és `your-access-token`a helyettesítő `your-compute-name`, , `location` `vmSize`és `vmPriority` `scaleSettings`az `adminUserName`értékek `adminUserPassword`, , , , és . A [Machine Learning Compute – Create or Update SDK Reference](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)hivatkozásában megadottak szerint a következő parancs létrehoz egy dedikált, egycsomópontos Standard_D1 (egy alapvető CPU számítási erőforrás), amely 30 perc elteltével lecsökken:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> A Windows-terminálokon előfordulhat, hogy a JSON-adatok küldésekor el kell menekülnie a duplaidéző szimbólumok elől. Ez azt, hogy `"location"` `\"location\"`a szöveg, mint lesz . 

A sikeres kérelem `201 Created` választ kap, de vegye figyelembe, hogy ez a válasz egyszerűen azt jelenti, hogy a kiépítési folyamat megkezdődött. A sikeres befejezés megerősítéséhez le kell hallgatnia (vagy használnia kell a portált).

### <a name="create-an-experimental-run"></a>Kísérleti futtatás létrehozása

A kísérleten belüli futtatás elindításához egy zip mappára van szükség, amely tartalmazza a betanítási parancsfájlt és a kapcsolódó fájlokat, valamint egy futtatási definíciós JSON-fájlra. A zip mappának rendelkeznie kell a Python bejegyzésfájllal a gyökérkönyvtárában. Például zip egy triviális Python program, mint például a következő egy mappába nevű **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

A következő kódrészlet mentése **definition.json**néven. Ellenőrizze, hogy a "Script" érték megegyezik-e az imént becipzározott Python-fájl nevével. Ellenőrizze, hogy a "Cél" érték megegyezik-e egy elérhető számítási erőforrás nevével. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Tegye közzé ezeket `multipart/form-data` a fájlokat a kiszolgálón a tartalom használatával:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

A sikeres POST-kérelem `200 OK` állapotot hoz létre, amelynek a létrehozott futtatás azonosítóját tartalmazó választörzse:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

A futtatást a REST-ful minta segítségével figyelheti, amelynek most már ismerősnek kell lennie:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>A már nem szükséges erőforrások törlése

Néhány, de nem az összes erőforrás támogatja a DELETE műveletet. Ellenőrizze az [API-referencia](https://docs.microsoft.com/rest/api/azureml/) véglegesítése előtt a REST API-t a törlési használati esetek. Modell törléséhez például a következőket használhatja:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Üzembe helyezett modell pontozása rest használatával

Bár lehetőség van egy modell központi telepítésére, hogy az egyszerű szolgáltatással hitelesítse magát, a legtöbb ügyfélfelé irányuló központi telepítés kulcsalapú hitelesítést használ. A megfelelő kulcsot a központi telepítés lapján, a Studio **Végpontok** lapján találja. Ugyanez en a hely jelenik meg a végpont pontozási URI.The same location will show your endpoint's scoring URI. A modell bemeneteit JSON-tömbként kell `data`modellezni:

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Munkaterület létrehozása a REST használatával 

Minden Azure ML-munkaterület négy másik Azure-erőforrástól függ: egy tárolóbeállítás-beállításjegyzéktől, amelyen engedélyezve van a felügyelet, egy key vault, egy Application Insights-erőforrás és egy tárfiók. Nem hozhat létre munkaterületet, amíg ezek az erőforrások nem léteznek. Az egyes erőforrások létrehozásának részleteiért tekintse meg a REST API-referencia.

Munkaterület létrehozásához a következőhöz hasonló hívást `management.azure.com`helyezzen el. Bár ez a hívás megköveteli, hogy nagy számú változót állítson be, szerkezetileg megegyezik a jelen cikkáltal tárgyalt többi hívással. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Meg kell `202 Accepted` kapnia a választ, és `Location` a visszaadott fejlécek, uri. Ez az URI a központi telepítéssel kapcsolatos információkért kaphat információt, beleértve a hasznos hibakeresési információkat, ha probléma van az egyik függő erőforrással (például ha elfelejtette engedélyezni a rendszergazdai hozzáférést a tároló beállításjegyzékében). 

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>A munkaterület áthelyezése

> [!WARNING]
> Az Azure Machine Learning-munkaterület áthelyezése egy másik előfizetésre, vagy a tulajdonában lévő előfizetés áthelyezése egy új bérlőre, nem támogatott. Ez hibákat okozhat.

### <a name="deleting-the-azure-container-registry"></a>Az Azure Container-beállításjegyzék törlése

Az Azure Machine Learning-munkaterület bizonyos műveletekhez az Azure Container Registry (ACR) szolgáltatást használja. Automatikusan létrehoz egy ACR-példányt, amikor először szüksége van rá.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>További lépések

- Fedezze fel a teljes [AzureML REST API-hivatkozást.](https://docs.microsoft.com/rest/api/azureml/)
- Ismerje meg, hogyan használhatja a Studio & Designer [segítségével az autó árának előrejelzését a tervezővel (előzetes verzió)](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score).
- Fedezze fel [az Azure Machine Learninget jupyter-jegyzetfüzetekkel.](https://docs.microsoft.com/azure//machine-learning/samples-notebooks)
