---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 54ccaebd84c6af308ddcfa956add7f84b6e55832
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321034"
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

Ehhez a híváshoz a Container [Host](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)használatával:

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

---
