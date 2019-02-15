---
title: CI/CD megvalósításához az Azure Stream Analytics az IoT Edge-ben REST API-k használatával
description: Ismerje meg, hogy egy folyamatos integrációt és üzembe helyezési folyamat megvalósítása az Azure Stream Analytics – REST API-k használatával.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 40beb620e037061b189762a51e3c29d0fd251b27
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268499"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>CI/CD megvalósításához a Stream Analytics az IoT Edge-ben API-k használatával

Folyamatos integráció és üzembe helyezés az Azure Stream Analytics-feladatok REST API-k használatával engedélyezheti. Ez a cikk példákat a melyik API-k használatát, és azok használatát. REST API-k nem támogatottak az Azure Cloud Shellben.

## <a name="call-apis-from-different-environments"></a>API-k hívása a különböző környezetek

REST API-k a Linux és a Windows nem hívható meg. Az alábbi parancsok mutatják be a helyes szintaxist az API-hívás. Ez a cikk későbbi szakaszaiban meghatározott API-használat ismerteti.

### <a name="linux"></a>Linux

Linux rendszeren használható `Curl` vagy `Wget` parancsokat:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” { <url> }   
```

```bash
wget -q -O- --{ <method> }-data="<request body>”--header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Windows a Powershell használata: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url>-Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Az ASA-feladat létrehozása az Edge-ben 
 
Stream Analytics-feladat létrehozásához hívja a PUT metódust a Stream Analytics API-val.

|Módszer|Kérés URL-címe|
|------|-----------|
|PUT|https://management.azure.com/subscriptions/{**előfizetés-azonosító**} /resourcegroups/ {**erőforrás-csoportnevet**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**feladat neve**}? api-version = 2017-04-01-preview|
 
A parancs használatának példája **curl**:

```curl
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Példa a kéréstörzs JSON-fájlban:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
További információkért lásd: a [API-dokumentáció](/rest/api/streamanalytics/stream-analytics-job).  
 
## <a name="publish-edge-package"></a>Edge-csomag közzététele 
 
Az IoT Edge-ben a Stream Analytics-feladat közzéteszi, hívja meg a POST metódus az Edge csomag közzé API-val.

|Módszer|Kérés URL-címe|
|------|-----------|
|POST|https://management.azure.com/subscriptions/{**subscriptionid**} /resourceGroups/ {**resourcegroupname**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**jobname**} / publishedgepackage? api-version = 2017-04-01 - előzetes verzió|

A aszinkron művelet egy 202 állapotát adja vissza, mindaddig, amíg a feladat közzététele sikeresen befejeződött. A válasz location fejlécet tartalmaz a folyamat állapotának lekéréséhez használt URI-ja. A folyamat futása közben az URI-t a location fejlécet hívása egy 202 állapotát adja vissza. A folyamat befejeződése után, az URI-t a location fejlécet a 200-as állapotát adja vissza. 

Példa az Edge-csomag közzététele szkripthívásba az **curl**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Miután kiválasztotta a POST híváson, számíthat egy üres szövegtörzzsel választ. Keresse meg az URL-cím található a válasz vezetője, és jegyezze fel a további használatra.
 
Példa a válasz a SORVEZETŐ az URL-cím:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Egy API-hívás URL-címét a következő parancs futtatása előtt egy-két percig várjon, talált a válasz vezetője. Ha nem kap 200 választ, majd próbálja megismételni a parancsot.
 
Példa az, hogy az API-hívás URL-CÍMÉT adja vissza **curl**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

A válasz hozzáadása az Edge üzembe helyezési parancsfájlhoz szükséges információkat tartalmazza. Az alábbi példák bemutatják, milyen információkat kell gyűjtenie, és adja hozzá a központi telepítésben lévő hol jegyzékfájl.
 
Miután sikeresen közzétette példa a válasz törzse:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}",”PublishTimeStamp”:”{publishtimestamp}”}}}}" 
  status : "Succeeded" 
} 
```

Manifest Nasazení mintát: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Manifest nasazení konfigurációját követően tekintse meg [üzembe helyezése az Azure IoT Edge-modulok az Azure CLI-vel](../iot-edge/how-to-deploy-modules-cli.md) üzembe helyezéshez.


## <a name="next-steps"></a>További lépések 
 
* [Az Azure Stream Analytics az IoT Edge-ben](stream-analytics-edge.md)
* [Az IoT Edge-oktatóanyag ASA](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [A Visual Studio-eszközökkel Stream Analytics Edge-feladatok fejlesztése](stream-analytics-tools-for-visual-studio-edge-jobs.md)
