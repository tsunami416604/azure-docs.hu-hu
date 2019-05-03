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
ms.openlocfilehash: 9871e0106ee6caf11c5a1e24459fbd2044f5f3d7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021441"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Beszédszintézis-jelölőnyelv (SSML)

A beszédfelismerés összefoglaló Markup Language (SSML) egy XML-alapú jelölőnyelv, amely lehetővé teszi a kiejtés szabályozhatja és *prosody* , szöveg-hang transzformációs. Prosody hivatkozik a munkatevékenységhez és a terv lényegét beszéd – a zene, ha a rendszer. Adja meg a szavak fonetikusan, tegyen számok értelmezéséhez, szünetel, vezérlőelem lényegét, kötet, és a sebesség és egyéb beszúrása. További információkért lásd: [Speech összefoglaló Markup Language (SSML) 1.0-s verzió](https://www.w3.org/TR/2009/REC-speech-synthesis-20090303/). 

Beszédhangot (Neurális és standard), támogatott nyelvek és területi beállítások teljes listáját lásd: [nyelvi támogatás](language-support.md#text-to-speech).

A következő szakaszok minták közös beszéd összefoglaló feladatok.

## <a name="adjust-speaking-style-for-neural-voices"></a>Módosítsa a Neurális beszédhangot beszédmódjához

SSML segítségével beszédmódjához módosíthatja, amikor a Neurális beszédhangot egyikével.

Alapértelmezés szerint a szöveg-hang transzformációs szolgáltatás szintetizál szöveg semleges stílusát. A Neurális beszédhangot kiterjesztése a SSML egy `<mstts:express-as>` elem, amely szöveges alakítja át a különböző nyelvű szintetizált stílusait. A stílus címkék jelenleg csak a ezek adott beszédhangot támogatják:

* `en-US-JessaNeural` 
* `zh-CN-XiaoxiaoNeural`.

Változtatásokat, és beszéljen a mondat szintjén is alkalmazható. A stílusok hangalapú változhat. Ha a stílus típusa nem támogatott, a szolgáltatás visszaadja a szintetizált alapértelmezett semleges stílust.

| Hang | Stílus | Leírás | 
|-----------|-----------------|----------|
| `en-US-JessaNeural` | type=`cheerful` | Egy pozitív és boldog érzelemfelismerési kifejezze |
| | type=`empathy` | Kifejezze megismerje a ápolásáért és ismertetése |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Fejezi ki a formális hangfrekvenciás, hasonló hírek szórás |
| | type=`sentiment ` | Ruház érintkező üzenet vagy egy történetet |

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
<voice name='en-US-JessaNeural'>
<mstts:express-as type="cheerful"> 
    That'd be just amazing! 
</mstts:express-as></voice></speak>
```

## <a name="add-a-break"></a>Szünet
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Nyelvű sebességének módosítása

A word vagy mondatszintű standard beszédhangot sebességét, és beszéljen is alkalmazható. Mivel a sebességét, és beszéljen csak alkalmazható a Neurális beszédhangot a mondat szintjén.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Guy24kRUS'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Kiejtése
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Kötet módosítása

A word vagy mondatszintű standard beszédhangot hangerőmódosítások is alkalmazható. Mivel a hangerőmódosítások csak a mondat szintjén Neurális beszédhangot is alkalmazható.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Térköz módosítása

A word vagy mondatszintű standard beszédhangot felébresztve módosítások is alkalmazható. Mivel a terv lényegét módosítások csak a mondat szintjén Neurális beszédhangot alkalmazható.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='en-US-Guy24kRUS'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Lényegét kontúrt módosítása

> [!IMPORTANT]
> Lényegét Munkaeloszlás módosítások Neurális beszédhangot nem támogatott.

```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="use-multiple-voices"></a>Több beszédhangot használata
```xml
<speak version='1.0' xmlns="https://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='en-US-Jessa24kRUS'>
    Good morning!
</voice>
<voice  name='en-US-Guy24kRUS'>
    Good morning to you too Jessa!
</voice> </speak>
```

## <a name="next-steps"></a>További lépések

* [Nyelvi támogatás: beszédhangot, a területi beállításokat, a nyelvek](language-support.md)
