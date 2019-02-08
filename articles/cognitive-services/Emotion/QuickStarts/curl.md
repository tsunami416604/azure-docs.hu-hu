---
title: 'Gyors útmutató: Felismerni az érzelmeket arcok a képen – Emotion API, a cURL'
titlesuffix: Azure Cognitive Services
description: Információk és kódminták segítségével ismerkedhet meg az Emotion API cURL-lel való használatának első lépéseivel.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: quickstart
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 8ecaf5e6de958a0f0af0ccccf9c712a227b504b9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857725"
---
# <a name="quickstart-build-an-app-to-recognize-emotions-on-faces-in-an-image"></a>Gyors útmutató: Érzelemfelismerés az arcok a képen az alkalmazás létrehozása.

> [!IMPORTANT]
> Az Emotion API 2019. február 15-ével elavulttá válik. Az érzelemfelismerési képesség mostantól általánosan elérhető a [Face API](https://docs.microsoft.com/azure/cognitive-services/face/) részeként.

Ez a cikk információkkal és kódmintákkal szolgál, amelyeken keresztül gyorsan elsajátíthatja, hogyan ismerheti fel a képeken szereplő személy vagy személyek által kifejezett érzelmeket az [Emotion API Recognize metódusa](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) és a cURL használatával.

## <a name="prerequisite"></a>Előfeltétel
* Ingyenes előfizetői azonosítóját [itt](https://azure.microsoft.com/try/cognitive-services/) szerezheti be

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
A sikeres hívás egy, az arcrekordokat és a hozzájuk tartozó érzelempontszámokat tartalmazó tömböt ad vissza, amely az adatokat az arcot jelölő téglalap mérete szerinti csökkenő sorrendben listázza. Az üres válasz azt jelzi, hogy a rendszer nem észlelt arcot. Az érzelemrekord a következő mezőket foglalja magában:
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
