---
title: 'Rövid útmutató: Érzelemfelismerés képeken szereplő arcokon – Emotion API, cURL'
titlesuffix: Azure Cognitive Services
description: Információk és kódminták segítségével ismerkedhet meg az Emotion API cURL-lel való használatának első lépéseivel.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: dfdaa89c9d29e419539f385f601dc7f264bf838e
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237075"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Rövid útmutató: Alkalmazás létrehozása a képeken szereplő arcokon tükröződő érzelmek felismeréséhez.

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól általánosan elérhető a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként.

Ez a cikk információkkal és kódmintákkal szolgál, amelyeken keresztül gyorsan elsajátíthatja, hogyan ismerheti fel a képeken szereplő személy vagy személyek által kifejezett érzelmeket az [Emotion API Recognize metódusa](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) és a cURL használatával.

## <a name="prerequisite"></a>Előfeltétel
* Szerezzen be egy ingyenes előfizetői azonosítót [itt](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-curl-example-request"></a>Érzelemfelismerési cURL-kérésminta

> [!NOTE]
> A REST-hívásban ugyanazt a helyet kell használnia, ahonnan az előfizetési kulcsot beszerezte. Ha például a westcentralus régióból szerezte be az előfizetési kulcsot, cserélje le az alábbi URL-címben a „westus” elemet a „westcentralus” elemre.

```json
@ECHO OFF

curl -v -X POST "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"

--data-ascii "{body}"
```

## <a name="recognize-emotions-sample-response"></a>Érzelemfelismerési válaszminta
A sikeres hívás egy, az arcrekordokat és a hozzájuk tartozó érzelempontszámokat tartalmazó tömböt ad vissza, amely az adatokat az arcot jelölő téglalap mérete szerint csökkenő sorrendben listázza. Az üres válasz azt jelzi, hogy a rendszer nem észlelt arcot. Az érzelemrekord a következő mezőket foglalja magában:
* faceRectangle – Az arcot jelölő téglalap helye a képen.
* scores – A képen szereplő egyes arcokhoz tartozó érzelempontszámok.

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
