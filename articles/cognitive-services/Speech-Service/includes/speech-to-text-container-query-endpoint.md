---
title: Beszédfelismerés és a szöveges tároló végpontjának lekérdezése
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: a55cf5ea6aa696d0cf0cdd619dc22839d748d83c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491151"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Beszéd – szöveg vagy Custom Speech – szöveg

A tároló olyan WebSocket-alapú lekérdezési végpont API-kat biztosít, amelyek a [SPEECH SDK](../index.md)-n keresztül érhetők el. Alapértelmezés szerint a Speech SDK az online Speech Services szolgáltatást használja. A tároló használatához módosítania kell az inicializálási módszert.

> [!TIP]
> Ha a Speech SDK-t tárolókkal használja, nem kell megadnia az Azure Speech Resource [-előfizetés kulcsát vagy egy hitelesítési tulajdonosi jogkivonatot](../rest-speech-to-text.md#authentication).

Tekintse meg az alábbi példákat.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Váltás az Azure-felhő inicializálási hívásának használatával:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

Ehhez a híváshoz a tároló [végpontja](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet)használatával:

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"));
```
# <a name="pythontabpython"></a>[Python](#tab/python)

Váltás az Azure-felhő inicializálási hívásának használatával:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

Ehhez a híváshoz a tároló [végpontja](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)használatával:

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

***
