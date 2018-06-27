---
title: Érzelemfelismerési API cURL – első lépések |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan Ismerkedés a Érzelemfelismerési API használata cURL kognitív szolgáltatásban.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: a7ca2cac718797462bb4dc889b3f1361b252435e
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021098"
---
# <a name="emotion-api-curl-quick-start"></a>Gyors üzembe helyezés érzelemfelismerési API-cURL

> [!IMPORTANT]
> Villámnézet API a 2017. október 30 véget ér. Kipróbálhatja az új [videó indexelő API előnézete](https://azure.microsoft.com/services/cognitive-services/video-indexer/) insights könnyen kibontani videók, és tartalom felderítési lép, például a keresési eredmények, növelje a szóbeli szavakat, a lapok, a karakterek és a érzelmek észlelésével. [További információk](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Ez a cikk és a segítségével gyorsan kódmintákat az első lépéseiben a [Érzelemfelismerési API-t ismeri fel a metódust](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) a cURL ismeri fel a kép egy vagy több személy által kifejezett érzelmek. 

## <a name="prerequisite"></a>Előfeltétel
* Az ingyenes előfizetés kulcs lekérése [Itt](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-curl-example-request"></a>Felismeri a érzelmek cURL példa kérése

> [!NOTE]
> Ugyanazon a helyen, ha Ön az előfizetés kulcsok beszerzéséhez használt a REST-hívást kell használnia. Például ha az előfizetés kulcsok nyert westcentralus, cserélje le "westus" az alábbi URL-cím "westcentralus".

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}" 
```

## <a name="recognize-emotions-sample-response"></a>Ismeri fel a érzelmek Mintaválasz
Sikeres meghívását arcfelismerési bejegyzések tömbje és a kapcsolódó érzelemfelismerési eredményeiket, arc téglalap mérete csökkenő sorrendben szerinti sorrendben adja vissza. Üres választ, hogy nincs lapok észlelt. Érzelemfelismerési bejegyzés a következő mezőket tartalmazza:
* faceRectangle - felületen a kép helyének.
* pontszámok - Érzelemfelismerési pontszámok minden lap a lemezképben. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]

