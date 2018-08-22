---
title: Használja a Custom Vision Service REST API-t – az Azure Cognitive Services |} A Microsoft Docs
description: A REST API használatával hozzon létre, betanítását, tesztelése és exportálni egy egyéni látástechnológiai modellje.
services: cognitive-services
author: blackmist
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: 44fa4d45c33f3064c089724ee761a70d0a8421ab
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "40250269"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>Oktatóanyag: A Custom Vision API – REST

Ismerje meg, hogyan használhatja a Custom Vision REST API létrehozása, betanítását, tesztelése és exportálni egy modellt.

A jelen dokumentumban lévő információk bemutatja, hogyan lehet REST-ügyféllel használható oktatási a Custom Vision service REST API-val. A példák bemutatják, hogyan használja az API használatával a `curl` segédprogram a bash-környezetet és `Invoke-WebRequest` Windows powershellből.

> [!div class="checklist"]
> * Kulcsok beszerzése
> * Projekt létrehozása
> * Címkék létrehozása
> * Képek hozzáadása
> * Betanítása és teszteli a modellt
> * A modell exportálása

## <a name="prerequisites"></a>Előfeltételek

* Egy alapszintű ismerete a Representational State Transfer (REST). Ez a dokumentum nem lépnek részletei a például a HTTP-műveletek, JSON, vagy további tevékenység következik, általánosan használt REST-tel.

* Vagy a bash (Bourne újra Shell) együtt a [curl](https://curl.haxx.se) segédprogramot vagy a Windows PowerShell 3.0-s (vagy újabb).

* A Custom Vision fiók. További információkért lásd: a [Tartalombesoroló létrehozása](getting-started-build-a-classifier.md) dokumentumot.

## <a name="get-keys"></a>Kulcsok beszerzése

A REST API használata esetén, hitelesítenie kell magát egy kulcs használatával. Felügyeleti vagy a betanítási műveletek végrehajtásakor használni a __képzési kulcs__. Ha a modell segítségével előrejelzéseket, használja a __előrejelzési kulcs__.

A kérés küldésekor a kulcsot a rendszer elküldte, fejléc.

Kérnie a kulcsokat a fiókját, látogasson el a [Custom Vision weblap](https://customvision.ai) , és válassza ki a __fogaskerék ikont__ kattintson a jobb felső sarokban. Az a __fiókok__ területén másolja a __képzési kulcs__ és __előrejelzési kulcs__ mezőket.

![A felhasználói felület kulcsok képe](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> A kulcsok minden egyes kérés hitelesítésére szolgálnak, mivel ebben a dokumentumban szereplő példák azt feltételezik, hogy a kulcs értékeit tárolják a környezeti változók. A következő parancsokat használja a dokumentum bármely más kódtöredékek használata előtt a környezeti változókat a kulcsok tárolásához:
>
> ```bash
> read -p "Enter the training key: " TRAININGKEY
> read -p "Enter the prediction key: " PREDICTIONKEY
> ```
>
> ```powershell
> $trainingKey = Read-Host 'Enter the training key'
> $predictionKey = Read-Host 'Enter the prediction key'
> ```

## <a name="create-a-new-project"></a>Új projekt létrehozása

Az alábbi példák, hozzon létre egy új projektet nevű `myproject` Custom Vision service-példányában. Ez a szolgáltatás alapértelmezés szerint a `General` tartomány:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

A válasz a kérésre a következő JSON-dokumentum hasonlít:

```json
{
  "id":"45d1b19b-69b6-4a22-8e7e-d1ca37504686",
  "name":"myproject",
  "description":"",
  "settings":{
    "domainId":"ee85a72c-405e-4adc-bb47-ffa8ca0c9f31",
    "useNegativeSet":true,
    "classificationType":"Multilabel",
    "detectionParameters":null
  },
  "created":"2018-08-10T17:39:02.5633333",
  "lastModified":"2018-08-10T17:39:02.5633333",
  "thumbnailUri":null
}
```

> [!TIP]
> A `id` a válaszban bejegyzés az új projekt azonosítója. További példák a jelen dokumentum a használható.

A kérelem további információkért lásd: [CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8290).

### <a name="specific-domains"></a>Bizonyos tartományok

Hozzon létre egy projektet egy adott tartományban, megadhatja a __ID domény__ , mint egy nem kötelező paraméter. Az alábbi példák bemutatják, hogyan kérdezheti le a rendelkezésre álló tartományok:

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/domains" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

A válasz a kérésre a következő JSON-dokumentum hasonlít:

```json
[
    {
        "id": "ee85a74c-405e-4adc-bb47-ffa8ca0c9f31",
        "name": "General",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "c151d5b5-dd07-472a-acc8-15d29dea8518",
        "name": "Food",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    {
        "id": "ca455789-012d-4b50-9fec-5bb63841c793",
        "name": "Landmarks",
        "type": "Classification",
        "exportable": false,
        "enabled": true
    },
    ...
]
```

A kérelem további információkért lásd: [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

A következő példa bemutatja, hogy használó új projekt létrehozása a __arcrész__ tartomány:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects?name=myproject&domainId=ca455789-012d-4b50-9fec-5bb63841c793" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

## <a name="create-tags"></a>Címkék létrehozása

Címke lemezképek kell használnia egy címkét. A következő példa bemutatja, hogyan hozhat létre egy új címke nevű `cat` , és egy címkét. Cserélje le `{projectId}` azonosítójú, a projekthez. Használja a `name=` paraméterrel adja meg a címke nevét:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" -H "Training-Key: $TRAININGKEY" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/tags?name=cat" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey" }
$resp.Content
```

A válasz a kérésre hasonlít a JSON-dokumentum: 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Mentse a `id` értékét, mivel a rendszerképek címkézése szolgál.

A kérelem további információkért lásd: [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Képek hozzáadása

Az alábbi példák bemutatják, hogy egy fájl hozzáadásával URL-címről. Cserélje le `{projectId}` azonosítójú, a projekthez. Cserélje le `{tagId}` azonosítójú, a címke a rendszerkép:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/images/urls" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"images": [{"url": "http://myimages/cat.jpg","tagIds": ["{tagId}"],"regions": [{"tagId": "{tagId}","left": 119.0,"top": 94.0,"width": 240.0,"height": 140.0}]}], "tagIds": ["{tagId}"]}'
$resp.Content
```

A válasz a kérésre a következő JSON-dokumentum hasonlít:

```json
{
    "isBatchSuccessful": true,
    "images": [
        {
            "sourceUrl": "http://myimages/cat.jpg",
            "status": "OK",
            "image": {
                "id": "081adaee-a76b-4d94-a70e-e4fd0935a28f",
                "created": "2018-08-13T13:24:22.0815638",
                "width": 640,
                "height": 480,
                "imageUri": "https://linktoimage",
                "thumbnailUri": "https://linktothumbnail",
                "tags": [
                    {
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936"
                    }
                ],
                "regions": [
                    {
                        "regionId": "40f206a1-3f8a-4de7-a6c3-c7b4643117df",
                        "tagName": null,
                        "created": "2018-08-13T13:24:22.104936",
                        "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
                        "left": 119,
                        "top": 94,
                        "width": 240,
                        "height": 140
                    }
                ]
            }
        }
    ]
}
```

A kérelem további információkért lásd: [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287).

## <a name="train-the-model"></a>A modell betanítása

Az alábbi példák bemutatják, hogyan lehet a modell betanításához. Cserélje le `{projectId}` azonosítójú, a projekthez:

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ""
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/train" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } 
$resp.Content
```

A válasz a kérésre a következő JSON-dokumentum hasonlít:

```json
{
    "id": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "name": "Iteration 1",
    "isDefault": false,
    "status": "Training",
    "created": "2018-08-10T17:39:02.5766667",
    "lastModified": "2018-08-16T17:15:07.5250661",
    "projectId": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "exportable": false,
    "domainId": null
}
```

Mentse a `id` értékét, mivel segítségével tesztelheti, és exportálja a modellt.

További információkért lásd: [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>A modell tesztelése

Az alábbi példák bemutatják, hogyan hajthat végre egy tesztet a modell. Cserélje le `{projectId}` azonosítójú, a projekthez. Cserélje le `{iterationId}` a modell által visszaadott azonosítóval. Cserélje le `https://linktotestimage` a tesztképre az elérési útját.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii '{"url":"https://linktotestimage"}'
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/quicktest/url?iterationId={iterationId}" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" } `
    -Body '{"url":"https://linktotestimage"}'
$resp.Content
```

A válasz a kérésre a következő JSON-dokumentum hasonlít:

```json
{
    "id": "369b010b-2a92-4f48-a918-4c1a0af91888",
    "project": "45d1b19b-69b8-4b22-8e7e-d1ca37504686",
    "iteration": "23de09d6-42a1-413e-839e-8db6ee6d3496",
    "created": "2018-08-16T17:39:20.7944508Z",
    "predictions": [
        {
            "probability": 0.8390652,
            "tagId": "ed6f7ab6-5132-47ad-8649-3ec42ee62d43",
            "tagName": "cat"
        }
    ]
}
```

További információkért lásd: [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>A modell exportálása

Modell exportálása két lépésből áll. Először meg kell az adatmodell-formátumban adja meg, és lekérhetik az exportált modell URL-CÍMÉT.

### <a name="request-a-model-export"></a>A modell exportálási kérelem

Az alábbi példák bemutatják, hogyan lehet exportálni egy `coreml` modell. Cserélje le `{projectId}` azonosítójú, a projekthez. Cserélje le `{iterationId}` a modell által visszaadott azonosítóval.

```bash
curl -X POST "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'POST' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export?platform=coreml" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

A válasz a kérésre a következő JSON-dokumentum hasonlít:

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

További információkért lásd: [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Az exportált modell letöltése

Az alábbi példák bemutatják, hogyan kérheti le az URL-címét az exportált modell. Cserélje le `{projectId}` azonosítójú, a projekthez. Cserélje le `{iterationId}` a modell által visszaadott azonosítóval.

```bash
curl -X GET "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" -H "Training-Key: $TRAININGKEY" -H "Content-Type: application/json" --data-ascii ''
```

```powershell
$resp = Invoke-WebRequest -Method 'GET' `
    -Uri "https://southcentralus.api.cognitive.microsoft.com/customvision/v2.0/Training/projects/{projectId}/iterations/{iterationId}/export" `
    -UseBasicParsing `
    -Headers @{ "Training-Key"="$trainingKey"; "Content-Type"="application/json" }
$resp.Content
```

A válasz a kérésre a következő JSON-dokumentum hasonlít:

```json
[
    {
        "platform": "CoreML",
        "status": "Done",
        "downloadUri": "https://linktoexportedmodel",
        "flavor": null
    }
]
```

További információkért lásd: [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a).