---
title: A REST API-k használata CI/CD-t az Azure Stream Analytics ioT Edge-en
description: Ismerje meg, hogyan valósíthat meg folyamatos integrációs és üzembe helyezési folyamatot az Azure Stream Analytics számára REST API-k használatával.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 328ca7cd2c6f76095c8334ae6fdb4aa75fbb867d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292007"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>CI/CD megvalósítása a Stream Analytics számára az IoT Edge-en API-k használatával

Az Azure Stream Analytics-feladatok folyamatos integrációját és üzembe helyezését rest API-k használatával engedélyezheti. Ez a cikk példákat, amelyek API-kat használni, és hogyan kell használni. REST API-k nem támogatottak az Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Api-k hívása különböző környezetekből

REST API-k lehet hívni a Linux és a Windows. Az alábbi parancsok az API-hívás megfelelő szintaxisát mutatják be. Adott API-használat a cikk későbbi szakaszaiban lesz ismertetve.

### <a name="linux"></a>Linux

Linux esetén használhatja `Curl` `Wget` vagy parancsokat használhat:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Windows esetén használja a Powershellt: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>ASA-feladat létrehozása az Edge-en 
 
Stream Analytics-feladat létrehozásához hívja meg a PUT metódust a Stream Analytics API használatával.

|Módszer|Kérés URL-címe|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
Példa a **curl parancsora:**

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Példa a kérelem törzsére a JSON-ban:

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
 
További információt az [API dokumentációjában](/rest/api/streamanalytics/stream-analytics-job)talál.  
 
## <a name="publish-edge-package"></a>Edge-csomag közzététele 
 
Stream Analytics-feladat ioT Edge-en való közzétételéhez hívja meg a POST metódust az Edge Package Publish API használatával.

|Módszer|Kérés URL-címe|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

Ez az aszinkron művelet 202-es állapotot ad vissza a feladat sikeres közzétételéig. A helyválasz fejléce a folyamat állapotának leolvasásához használt URI-t tartalmazza. A folyamat futása közben a helyfejlécben lévő URI-hívás 202-es állapotot ad vissza. Amikor a folyamat befejeződik, a helyfejlécben lévő URI 200-as állapotot ad vissza. 

Példa egy Edge csomag közzétételi hívás **curl**használatával: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
A POST hívás után üres testtel kell várnia a választ. Keresse meg a válasz fejében található URL-t, és rögzítse további használatra.
 
Példa a válaszfej URL-címére:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
A Várjon egy vagy két percet, mielőtt futtatja a következő parancsot, hogy egy API-hívást az URL-t talált a fejét a válasz. Próbálkozzon újra a paranccsal, ha nem kap 200-as választ.
 
Példa az API-hívás rakoncátoros visszaadott URL-lel: **curl**

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

A válasz tartalmazza az Edge központi telepítési parancsfájlhoz hozzáadni kívánt információkat. Az alábbi példák bemutatják, hogy milyen információkat kell gyűjteni, és hol kell hozzáadni a központi telepítési jegyzékben.
 
A sikeres közzététel után a választörzs:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

A telepítési jegyzékből vett példa: 

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

A központi telepítési jegyzék konfigurálása után tekintse [meg az Azure IoT Edge-modulok üzembe helyezését az Azure CLI-vel](../iot-edge/how-to-deploy-modules-cli.md) üzembe helyezéshez.


## <a name="next-steps"></a>További lépések 
 
* [IoT Edge-eszközökön futó Azure Stream Analytics](stream-analytics-edge.md)
* [ASA az IoT Edge oktatóanyagán](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Stream Analytics Edge-feladatok fejlesztése a Visual Studio eszközeivel](stream-analytics-tools-for-visual-studio-edge-jobs.md)
