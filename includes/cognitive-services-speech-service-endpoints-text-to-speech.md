---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145370"
---
### <a name="standard-and-neural-voices"></a>Standard és a Neurális beszédhangot

Ez a táblázat segítségével határozhatja meg a rendelkezésre állását a standard és a Neurális beszédhangot régió/végpont:

| Régió | Végpont | Standard beszédhangot | Neurális beszédhangot |
|--------|----------|-----------------|---------------|
| Kelet-Ausztrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| Közép-Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| USA középső régiója | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| Kelet-Ázsia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| USA keleti régiója | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| USA 2. keleti régiója | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| Közép-Franciaország | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| India középső régiója | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| Kelet-Japán | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| Korea középső régiója | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| USA északi középső régiója | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| Észak-Európa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| USA déli középső régiója | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| Délkelet-Ázsia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| Egyesült Királyság déli régiója | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| Nyugat-Európa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| USA nyugati régiója | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| USA 2. nyugati régiója | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |

### <a name="custom-voices"></a>Egyéni beszédhangot

Ha létrehozott egyéni hangtípust, használja a végpont, amelyet létrehozott. Is használhatja az alább felsorolt, végpontok és cserélje le a `{deploymentId}` a hangalapú modell üzembe helyezési azonosítóval.

| Régió | Végpont |
|--------|----------|
| Kelet-Ausztrália | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Közép-Kanada | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA középső régiója | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kelet-Ázsia | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA keleti régiója | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA 2. keleti régiója | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Közép-Franciaország | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| India középső régiója | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kelet-Japán | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Korea középső régiója | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA északi középső régiója | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Észak-Európa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA déli középső régiója | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Délkelet-Ázsia | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Egyesült Királyság déli régiója | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Nyugat-Európa | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA nyugati régiója | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA 2. nyugati régiója | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
