---
title: A REST használata az ML-erőforrások kezeléséhez
titleSuffix: Azure Machine Learning
description: A REST API-k használata Azure ML-erőforrások létrehozásához, futtatásához és törléséhez
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: e1e19f985c9aa02759c6fff3c634c216c7ef42ef
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525549"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Azure ML-erőforrások létrehozása, futtatása és törlése a REST használatával

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure ML-erőforrások több módon is kezelhetők. Használhatja a [portált](https://portal.azure.com/), a [parancssori felületet](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)vagy a [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-t. Vagy a REST API is kiválaszthatja. A REST API a HTTP-műveleteket szabványos módon használja az erőforrások létrehozásához, lekéréséhez, frissítéséhez és törléséhez. A REST API bármely olyan nyelven vagy eszközzel működik, amely HTTP-kéréseket tesz elérhetővé. A REST egyszerű szerkezete sokszor jó választás a parancsfájlkezelési környezetekben és a MLOps-automatizálásban. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Engedélyezési jogkivonat beolvasása
> * Megfelelően formázott REST-kérelem létrehozása az egyszerű szolgáltatás hitelesítésének használatával
> * Az Azure ML hierarchikus erőforrásaival kapcsolatos információk lekérése a GET kérelmek használatával
> * Erőforrások létrehozására és módosítására szolgáló PUT és POST kérelmek használata
> * TÖRLÉSi kérések használata az erőforrások tisztításához 
> * Kulcs alapú hitelesítés használata az üzembe helyezett modellek pontozásához

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** , amelyhez rendszergazdai jogosultságokkal rendelkezik. Ha nem rendelkezik ilyen előfizetéssel, próbálja ki az [ingyenes vagy fizetős személyes előfizetést](https://aka.ms/AMLFree)
- Egy [Azure Machine learning-munkaterület](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- A rendszergazdai REST-kérelmek az egyszerű szolgáltatásnév hitelesítését használják. Az egyszerű szolgáltatás munkaterületen való létrehozásához kövesse a [hitelesítés beállítása Azure Machine learning erőforrásokhoz és munkafolyamatokhoz](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) című témakör lépéseit.
- A **curl** segédprogram. A **curl** program a [Windows alrendszerben Linux](https://aka.ms/wslinstall/) vagy bármely Unix-disztribúció számára elérhető. A PowerShellben a **curl** a **meghívó-webkérések** aliasa, és a `curl -d "key=val" -X POST uri` `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`válik. 

## <a name="retrieve-a-service-principal-authentication-token"></a>Egyszerű szolgáltatásnév hitelesítési jogkivonatának beolvasása

A rendszergazdai REST-kérelmek hitelesítése OAuth2 implicit folyamattal történik. Ez a hitelesítési folyamat az előfizetés egyszerű szolgáltatásnév által biztosított jogkivonatot használja. A jogkivonat lekéréséhez a következőkre lesz szüksége:

- A bérlő azonosítója (annak a szervezetnek az azonosítása, amelyhez az előfizetés tartozik)
- Az ügyfél-azonosító (amely a létrehozott jogkivonathoz lesz társítva)
- Az ügyfél titkos kulcsa (amelyet védenie kell)

Ezeket az értékeket a szolgáltatásnak a [Azure Machine learning erőforrások és munkafolyamatok hitelesítésének beállítása](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication)című szakaszban ismertetett válasz alapján kell megállapítania. Ha céges előfizetését használja, lehet, hogy nincs engedélye egy egyszerű szolgáltatásnév létrehozásához. Ebben az esetben [ingyenes vagy fizetős személyes előfizetést](https://aka.ms/AMLFree)kell használnia.

Token lekérése:

1. Nyisson meg egy terminálablakot.
1. Adja meg a következő kódot a parancssorban
1. Helyettesítse be a saját értékeit `{your-tenant-id}`, `{your-client-id}`és `{your-client-secret}`. Ebben a cikkben a kapcsos zárójelek által körülvett sztringek a saját megfelelő értékeire cserélt változók.
1. Futtassa a

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

A válasznak egy órára jó hozzáférési jogkivonatot kell biztosítania:

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

Jegyezze fel a jogkivonatot, mivel az összes további rendszergazdai kérelem hitelesítéséhez használni fogja. Ezt úgy teheti meg, hogy az összes kérelemben beállítja az engedélyezési fejlécet:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Vegye figyelembe, hogy az érték a "tulajdonos" karakterlánccal kezdődik, beleértve a token hozzáadása előtt egy szóközzel.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Az előfizetéshez társított erőforráscsoportok listájának beolvasása

Az előfizetéshez társított erőforráscsoportok listájának lekéréséhez futtassa a következőt:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Az Azure-ban számos REST API van közzétéve. Minden szolgáltató frissíti az API-t a saját ritmusán, de a meglévő programok megszakítása nélkül. A szolgáltató a `api-version` argumentumot használja a kompatibilitás biztosításához. A `api-version` argumentum a szolgáltatásról a szolgáltatásra változik. A Machine Learning szolgáltatás esetében például a jelenlegi API-verzió `2019-11-01`. A Storage-fiókok esetében `2019-06-01`. A Key vaultok esetében `2019-09-01`. Az összes REST-hívásnak a várt értékre kell állítania a `api-version` argumentumot. A megadott verzió szintaxisát és szemantikai lehetőségeit is használhatja, még akkor is, ha az API továbbra is fejlődik. Ha a `api-version` argumentum nélkül küld egy szolgáltatónak kérelmet, a válasz a támogatott értékek egy ember által olvasható listáját fogja tartalmazni. 

A fenti hívás az űrlap tömörített JSON-válaszát eredményezi: 

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


## <a name="drill-down-into-workspaces-and-their-resources"></a>Munkaterületek és erőforrásaik részletezése

Egy erőforráscsoport munkaterületének lekéréséhez futtassa a következő, behelyettesített `{your-subscription-id}`, `{your-resource-group}`és `{your-access-token}`: 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Ekkor megjelenik egy JSON-lista, amely egy listát tartalmaz, amely egy adott munkaterületet részletez:

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

A munkaterületen belüli erőforrásokkal való munkavégzéshez az általános **Management.Azure.com** -kiszolgálóról a munkaterület helyére jellemző REST API kiszolgálóra kell váltania. Jegyezze fel a fenti JSON-válasz `discoveryUrl` kulcsának értékét. Ha ezt az URL-címet kapja, a következőhöz hasonló választ kap:

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

A `api` válasz értéke annak a kiszolgálónak az URL-címe, amelyet a további kérésekhez fog használni. A kísérletek listázásához például küldje el a következő parancsot. Cserélje le a `regional-api-server` értéket a `api` válasz értékére (például `centralus.api.azureml.ms`). A szokásos módon cserélje le `your-subscription-id`, `your-resource-group`, `your-workspace-name`és `your-access-token` is:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Hasonlóképpen, ha a munkaterületen regisztrált modelleket szeretne beolvasni, küldje el a következőt:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Figyelje meg, hogy az elérési út `history/v1.0` a modellek listázása közben megjelenő kísérletekkel kezdődik, és az elérési út `modelmanagement/v1.0`. A REST API több operatív csoportba vannak osztva, amelyek mindegyike külön elérési úttal rendelkezik. Az alábbi hivatkozásokon található API-referenciák a különböző műveletekhez tartozó műveletekkel, paraméterekkel és válaszokkal kapcsolatos kódokat felsorolják.

|Terület|Útvonal|Referencia|
|-|-|-|
|Összetevők|összetevő/v 2.0/|[REST API-referencia](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|Adattárak|adattár/v 1.0/|[REST API-referencia](https://docs.microsoft.com/rest/api/azureml/datastores)|
|hiperparaméter finomhangolása|HyperDrive/v 1.0/|[REST API-referencia](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Modellek|modelmanagement/v 1.0/|[REST API-referencia](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|Előzmények|végrehajtás/v 1.0/és előzmények/v 1.0/|[REST API-referencia](https://docs.microsoft.com/rest/api/azureml/runs)|

A REST API a következő általános mintázattal derítheti fel:

|URL-összetevő|Példa|
|-|-|
| https://| |
| regionális-API-kiszolgáló/ | centralus.api.azureml.ms/ |
| műveletek – elérési út/ | előzmények/v 1.0/ |
| előfizetések/{saját előfizetés-azonosító}/ | előfizetések/abcde123-ABAB-ABAB-1234-0123456789abc/ |
| resourceGroups/{erőforrás-csoport}/ | resourceGroups/MyResourceGroup/ |
| szolgáltatók/művelet – szolgáltató/ | szolgáltatók/Microsoft. MachineLearningServices/ |
| Szolgáltató – erőforrás-útvonal/ | munkaterület/MLWorkspace/Sajátmunkaterület/FirstExperiment/Run/1/ |
| műveletek – végpont/ | összetevők/metaadatok/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Erőforrások létrehozása és módosítása PUT és POST kérelmek használatával

A GET művelet erőforrás-lekérése mellett a REST API támogatja az összes olyan erőforrás létrehozását, amely a ML-megoldások betanításához, üzembe helyezéséhez és figyeléséhez szükséges. 

Az ML-modellek betanítása és futtatása számítási erőforrásokat igényel. A munkaterületek számítási erőforrásait az alábbiak szerint listázhatja: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Elnevezett számítási erőforrás létrehozásához vagy felülírásához PUT-kérést kell használni. A következőkben a `your-subscription-id`, a `your-resource-group`, a `your-workspace-name`és a `your-access-token`, a `your-compute-name`és a `location`, `vmSize`, `vmPriority`, `scaleSettings`, `adminUserName`és `adminUserPassword`értékeinek megismerése mellett. A (z) [Machine learning Compute – SDK létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)című hivatkozásban megadott módon a következő parancs létrehoz egy dedikált, egycsomópontos Standard_D1 (egy alapszintű CPU-számítási erőforrást), amely 30 perc után le lesz méretezve:

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
> A Windows-terminálokon a JSON-adatok küldésekor előfordulhat, hogy el kell menekülnie a dupla idézőjeles szimbólumokkal. Ez a szöveg, például a `"location"` `\"location\"`válik. 

A sikeres kérések `201 Created` választ kapnak, de a válasz egyszerűen azt jelenti, hogy a kiépítési folyamat megkezdődött. A sikeres Befejezés megerősítéséhez le kell kérdezni (vagy a portált kell használnia).

### <a name="create-an-experimental-run"></a>Kísérleti Futtatás létrehozása

Egy kísérleten belüli Futtatás indításához szüksége van egy zip-mappára, amely tartalmazza a betanítási parancsfájlt és a kapcsolódó fájlokat, valamint egy Run definition JSON-fájlt. A zip-mappának tartalmaznia kell a Python-bejegyzés fájlját a gyökérkönyvtárában. Tegyük fel például, hogy egy triviális Python-programot, például a következőt egy **Train. zip**nevű mappába helyezi.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Mentse ezt a következő kódrészletet **definition. JSON**néven. Ellenőrizze, hogy a "script" érték megegyezik-e a csak a tömörített Python-fájl nevével. Ellenőrizze, hogy a "Target" érték megegyezik-e egy elérhető számítási erőforrás nevével. 

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

Tegye közzé ezeket a fájlokat a kiszolgálóra `multipart/form-data` tartalom használatával:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

A sikeres POST kérelem `200 OK` állapotot hoz létre, amelynek a válasz törzse tartalmazza a létrehozott Futtatás azonosítóját:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

A futtatást a REST-ful mintázattal figyelheti, amely most már ismerős lehet:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>A már nem szükséges erőforrások törlése

Néhány, de nem minden erőforrás támogatja a TÖRLÉSi műveletet. Győződjön meg arról, hogy az [API-hivatkozás](https://docs.microsoft.com/rest/api/azureml/) a törléshez szükséges REST API véglegesítése előtt. Egy modell törléséhez például a következőt használhatja:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>A REST használata egy üzembe helyezett modell kiértékeléséhez

Habár lehetséges a modell üzembe helyezése úgy, hogy az egy egyszerű szolgáltatással legyen hitelesítve, a legtöbb ügyfélre irányuló központi telepítés a kulcs alapú hitelesítést használja. A megfelelő kulcsot az üzembe helyezés lapján találja a Studio **endpoints (végpontok** ) lapján. Ugyanazon a helyen jelenik meg a végpont pontozási URI-ja. A modell bemeneteit `data`nevű JSON-tömbként kell modellezni:

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Munkaterület létrehozása REST használatával 

Minden Azure ML-munkaterület négy további Azure-erőforrástól függ: egy, a felügyeletet engedélyező tároló-beállításjegyzékkel, egy kulcstartóval, egy Application Insights erőforrással és egy Storage-fiókkal. Nem hozhat létre munkaterületet, amíg ezek az erőforrások nem léteznek. Az egyes erőforrások létrehozásának részleteiért lásd a REST API referenciát.

Munkaterület létrehozásához a következőhöz hasonló hívást HELYEZZEN el `management.azure.com`. Habár a híváshoz nagy számú változót kell beállítania, szerkezetileg megegyeznek a cikk által tárgyalt más hívásokkal. 

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

`202 Accepted` választ kell kapnia, és a visszaadott fejlécekben egy `Location` URI-t kell megadnia. Ezt az URI-t beolvashatja a központi telepítéssel kapcsolatos információkért, beleértve a hasznos hibakeresési információkat is, ha probléma merül fel az egyik függő erőforrással kapcsolatban (például ha elfelejtette a rendszergazdai hozzáférés engedélyezését a tároló beállításjegyzékében). 

## <a name="next-steps"></a>Következő lépések

- Ismerkedjen meg a teljes [AzureML REST API-referenciával](https://docs.microsoft.com/rest/api/azureml/).
- Megtudhatja, hogyan használhatja a Studio & designert [az autó árának előrejelzésére a Designerben (előzetes verzió)](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score).
- Ismerkedjen meg [Azure Machine learning Jupyter notebookokkal](https://docs.microsoft.com/azure//machine-learning/samples-notebooks).
