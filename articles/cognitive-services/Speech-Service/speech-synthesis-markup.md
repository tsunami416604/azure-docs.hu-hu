---
title: Beszéd összefoglaló Markup Language (SSML) – beszédszolgáltatások
titleSuffix: Azure Cognitive Services
description: Használja a Speech összefoglaló Markup Language írásmódja és a szöveg-hang transzformációs prosody szabályozásához.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 57fc7e699d88dbe777750e3acdb7f96794b66fc0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837155"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Beszédszintézis-jelölőnyelv (SSML)

A beszédfelismerés összefoglaló Markup Language (SSML) egy XML-alapú jelölőnyelv, amely lehetővé teszi a kiejtés szabályozhatja és *prosody* , szöveg-hang transzformációs. Prosody hivatkozik a munkatevékenységhez és a terv lényegét beszéd – a zene, ha a rendszer. Adja meg a szavak fonetikusan, tegyen számok értelmezéséhez, szünetel, vezérlőelem lényegét, kötet, és a sebesség és egyéb beszúrása. További információkért lásd: [Speech összefoglaló Markup Language (SSML) 1.0-s verzió](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/).

Beszédhangot (Neurális és standard), támogatott nyelvek és területi beállítások teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).

A következő szakaszok minták közös beszéd összefoglaló feladatok.

## <a name="add-a-break"></a>Szünet
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Nyelvű sebességének módosítása

A word vagy mondatszintű standard beszédhangot sebességét, és beszéljen is alkalmazható. Mivel a sebességét, és beszéljen csak alkalmazható a Neurális beszédhangot a mondat szintjén.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Kiejtése
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Kötet módosítása

A word vagy mondatszintű standard beszédhangot hangerőmódosítások is alkalmazható. Mivel a hangerőmódosítások csak a mondat szintjén Neurális beszédhangot is alkalmazható.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Térköz módosítása

A word vagy mondatszintű standard beszédhangot felébresztve módosítások is alkalmazható. Mivel a terv lényegét módosítások csak a mondat szintjén Neurális beszédhangot alkalmazható.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Lényegét kontúrt módosítása

> [!IMPORTANT]
> Lényegét Munkaeloszlás módosítások Neurális beszédhangot nem támogatott.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Több beszédhangot használata
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Good morning!
</voice>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>További lépések

* [Nyelvi támogatás: beszédhangot, a területi beállításokat, a nyelvek](language-support.md)
