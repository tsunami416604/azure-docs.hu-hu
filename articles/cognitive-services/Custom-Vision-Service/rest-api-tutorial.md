---
title: 'Oktatóanyag: Használja a Custom Vision Service REST API-val'
titlesuffix: Azure Cognitive Services
description: A REST API használatával custom vision modellt hozhat létre, taníthat be, tesztelhet és exportálhat.
services: cognitive-services
author: blackmist
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 08/07/2018
ms.author: larryfr
ms.openlocfilehash: e33eb58dd4228bb1093c239bae960f71c0f3788c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884981"
---
# <a name="tutorial-use-the-custom-vision-rest-api"></a>Oktatóanyag: A Custom Vision REST API használata

Ismerje meg, hogyan történhet a Custom Vision REST API segítségével egy modell létrehozása, betanítása, tesztelése és exportálása.

A jelen dokumentum bemutatja, hogyan lehet REST-ügyféllel a REST API-t felhasználni a Custom Vision szolgáltatás betanítására. A példák bemutatják, hogyan használható az API a bash környezetből a `curl` segédprogrammal és Windows PowerShellből a `Invoke-WebRequest` segítségével.

> [!div class="checklist"]
> * Kulcsok megszerzése
> * Projekt létrehozása
> * Címkék létrehozása
> * Képek hozzáadása
> * A modell betanítása és tesztelése
> * A modell exportálása

## <a name="prerequisites"></a>Előfeltételek

* A Representational State Transfer (REST) alapszintű ismerete. Ez a dokumentum nem foglalkozik részletesen olyan dolgokkal, mint a HTTP-parancsok, a JSON és más REST esetén gyakran használt dolgok.

* Vagy bash (Bourne újra Shell) és [curl](https://curl.haxx.se) segédprogram vagy a Windows PowerShell 3.0-s (vagy újabb).

* Custom Vision fiók. További információkért lásd az [Osztályozó létrehozása](getting-started-build-a-classifier.md) dokumentumot.

## <a name="get-keys"></a>Kulcsok megszerzése

A REST API használata esetén hitelesítenie kell magát egy kulccsal. Felügyeleti vagy a betanítási műveletek végrehajtásakor a __betanítási kulcs__ használatos. Ha a modell segítségével előrejelzéseket végez, az __előrejelzési kulcsot__ használhatja.

Kérés küldésekor a kulcsot a rendszer a kérés fejlécben küldi.

A fiókjához használt kulcsok megszerzéséhez látogasson el a [Custom Vision weboldalra](https://customvision.ai), válassza ki a __fogaskerék ikont__ a jobb felső sarokban. A __Fiókok__ területen másolja ki a __Betanítási kulcs__ és __Előrejelzési kulcs__ mezők értékeit.

![A kulcsok felhasználói felület képe](./media/rest-api-tutorial/training-prediction-keys.png)

> [!IMPORTANT]
> Mivel a kulcsok minden egyes kérés hitelesítéséhez szükségesek, a dokumentumban szereplő példáknál azt feltételezzük, hogy a kulcs értékeket környezeti változók tárolják. Mielőtt használná a dokumentumból valamely kódrészletet, mentse el a kulcsokat a környezeti változókba a következő parancsokkal:
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

Az alábbi példák egy új, `myproject` nevű projektet hoznak létre a saját Custom Vision Service-példányában. A szolgáltatás alapértelmezés szerinti `General` tartománya:

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

A kérésre adott válasz a következő JSON dokumentumhoz hasonló:

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
> A válaszban a `id` bejegyzés az új projekt azonosítója. Ezt használni fogjuk a dokumentum más példáiban a későbbiekben.

A kéréssel kapcsolatos további információk: [CreateProject](https://go.microsoft.com/fwlink/?linkid=865446).

### <a name="specific-domains"></a>Adott tartományok

Ha a projektet egy adott tartományban szeretné létrehozni, akkor nem kötelező paraméterként megadhatja a __tartományazonosítót__. Az alábbi példák bemutatják a rendelkezésre álló tartományok listájának lekérdezését:

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

A kérésre adott válasz a következő JSON dokumentumhoz hasonló:

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

A kéréssel kapcsolatos további információk: [GetDomains](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a827d).

A következő példa a __Landmarks__ tartományt használó projekt létrehozását mutatja be:

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

Képek címkézéséhez címkeazonosítót kell használni. A következő példa bemutatja, hogyan hozhat létre egy új, `cat` nevű címkét és címkeazonosítót. A `{projectId}` helyett használja a saját projekt azonosítóját. A `name=` paraméter a címke nevének megadására használható:

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

A kérésre adott válasz a JSON dokumentumhoz hasonló: 

```json
{"id":"ed6f7ab6-5132-47ad-8649-3ec42ee62d43","name":"cat","description":null,"imageCount":0}
```

Mentse a `id` értékét, mivel a képek címkézésénél szükség van rá.

A kéréssel kapcsolatos további információk: [CreateTag](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829d).

## <a name="add-images"></a>Képek hozzáadása

Az alábbi példák fájl URL-címről történő hozzáadását mutatják be. A `{projectId}` helyett használja a saját projekt azonosítóját. A `{tagId}` helyett használja a kép címkeazonosítóját:

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

A kérésre adott válasz a következő JSON dokumentumhoz hasonló:

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

A kéréssel kapcsolatos további információk: [CreateImagesFromUrls](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8287).

## <a name="train-the-model"></a>A modell betanítása

Az alábbi példák bemutatják, hogyan lehet a modellt betanítani. A `{projectId}` helyett használja a saját projekt azonosítóját:

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

A kérésre adott válasz a következő JSON dokumentumhoz hasonló:

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

Mentse a `id` értékét, mivel ennek segítségével tesztelheti, és exportálhatja a modellt.

További információk: [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a8294).

## <a name="test-the-model"></a>A modell tesztelése

Az alábbi példák bemutatják, hogyan lehet a modell tesztelését elvégezni. A `{projectId}` helyett használja a saját projekt azonosítóját. A `{iterationId}` helyett használja a modell betanításakor visszaadott azonosítót. A `https://linktotestimage` helyett a tesztkép elérési útvonalát használja.

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

A kérésre adott válasz a következő JSON dokumentumhoz hasonló:

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

További információk: [QuickTestImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a828d).

## <a name="export-the-model"></a>A modell exportálása

A modell exportálása két lépésből áll. Először meg kell adni a modell formátumot, majd kérni kell az exportált modellhez az URL-t.

### <a name="request-a-model-export"></a>A modell exportálás kérése

Az alábbi példák bemutatják, hogyan lehet a `coreml` modellt exportálni. A `{projectId}` helyett használja a saját projekt azonosítóját. A `{iterationId}` helyett használja a modell betanításakor visszaadott azonosítót.

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

A kérésre adott válasz a következő JSON dokumentumhoz hasonló:

```json
{
    "platform": "CoreML",
    "status": "Exporting",
    "downloadUri": null,
    "flavor": null
}
```

További információk: [ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829b).

### <a name="download-the-exported-model"></a>Az exportált modell letöltése

Az alábbi példák bemutatják, hogyan kérheti le az exportált modell URL-címét. A `{projectId}` helyett használja a saját projekt azonosítóját. A `{iterationId}` helyett használja a modell betanításakor visszaadott azonosítót.

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

A kérésre adott válasz a következő JSON dokumentumhoz hasonló:

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

További információk: [GetExports](https://southcentralus.dev.cognitive.microsoft.com/docs/services/d0e77c63c39c4259a298830c15188310/operations/5a59953940d86a0f3c7a829a).
