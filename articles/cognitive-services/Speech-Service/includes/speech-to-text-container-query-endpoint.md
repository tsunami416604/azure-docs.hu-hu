---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: cba789b6a40dd23309bb94289b187209893908e3
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92116789"
---
A tároló olyan WebSocket-alapú lekérdezési végpont API-kat biztosít, amelyek a [SPEECH SDK](../index.yml)-n keresztül érhetők el. Alapértelmezés szerint a Speech SDK az online Speech Services szolgáltatást használja. A tároló használatához módosítania kell az inicializálási módszert.

> [!TIP]
> Ha a Speech SDK-t tárolókkal használja, nem kell megadnia az Azure Speech Resource [-előfizetés kulcsát vagy egy hitelesítési tulajdonosi jogkivonatot](../rest-speech-to-text.md#authentication).

Ez az alábbi példákon látható.

# <a name="c"></a>[C#](#tab/csharp)

Váltás az Azure-felhő inicializálási hívásának használatával:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

A hívás használata a tároló [gazdagépével](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet):

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

Váltás az Azure-felhő inicializálási hívásának használatával:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

A hívás használata a tároló [végponttal](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python):

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
