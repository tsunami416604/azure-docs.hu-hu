---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1773e22a54cc86e7736c91e4be757caa0250cbf7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422338"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>Beszéd-szöveg vagy egyéni beszéd-szöveg

A tároló websocket-alapú lekérdezési végponti API-kat biztosít, amelyek a [beszédskón](../index.yml)keresztül érhetők el. Alapértelmezés szerint a beszédfelismerési SDK online beszédszolgáltatásokat használ. A tároló használatához módosítania kell az inicializálási módszert.

> [!TIP]
> Ha a speech SDK tárolók, nem kell biztosítania az Azure Speech [erőforrás-előfizetési kulcs vagy egy hitelesítési tulajdonosi jogkivonatot.](../rest-speech-to-text.md#authentication)

Ez az alábbi példákon látható.

# <a name="c"></a>[C #](#tab/csharp)

Az Azure-felhőalapú inicializálási hívás használatának módosítása:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

a híváshoz a [tárolóállomás](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)használatával:

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```
# <a name="python"></a>[Python](#tab/python)

Az Azure-felhőalapú inicializálási hívás használatának módosítása:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

a híváshoz a [tárolóállomás](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)használatával:

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
