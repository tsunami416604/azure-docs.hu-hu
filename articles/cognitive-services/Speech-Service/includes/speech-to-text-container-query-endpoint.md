---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77563309"
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
