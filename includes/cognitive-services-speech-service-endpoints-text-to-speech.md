---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: a1de313245e826511a7e9c864165ac44bcb48530
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80234159"
---
### <a name="standard-and-neural-voices"></a>Standard és neurális hangok

A táblázat segítségével meghatározhatja a standard és a neurális hangok rendelkezésre állását régiónként/végpont szerint:

| Régió | Végpont | Standard hangok | Neurális hangok |
|--------|----------|-----------------|---------------|
| Kelet-Ausztrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| Dél-Brazília | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| Közép-Kanada | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| USA középső régiója | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| Kelet-Ázsia | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| USA keleti régiója | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| USA 2. keleti régiója | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| Közép-Franciaország | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| Közép-India | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| Kelet-Japán | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| Nyugat-Japán | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| Dél-Korea középső régiója | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| USA északi középső régiója | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| Észak-Európa | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| USA déli középső régiója | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| Délkelet-Ázsia | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| Az Egyesült Királyság déli régiója | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| Nyugat-Európa | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |
| USA nyugati régiója | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Nem |
| USA 2. nyugati régiója | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Igen | Igen |

### <a name="custom-voices"></a>Egyéni hangok

Ha egyéni hangbetűkészletet hozott létre, használja a létrehozott végpontot. Az alább felsorolt végpontokat is használhatja, és lecserélheti a `{deploymentId}` hangmodell üzembe helyezési azonosítóját.

| Régió | Végpont |
|--------|----------|
| Kelet-Ausztrália | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Dél-Brazília | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Közép-Kanada | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA középső régiója | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kelet-Ázsia | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA keleti régiója | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA 2. keleti régiója | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Közép-Franciaország | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Közép-India | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Kelet-Japán | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Nyugat-Japán | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Dél-Korea középső régiója | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA északi középső régiója | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Észak-Európa | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA déli középső régiója | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Délkelet-Ázsia | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Az Egyesült Királyság déli régiója | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Nyugat-Európa | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA nyugati régiója | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| USA 2. nyugati régiója | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
