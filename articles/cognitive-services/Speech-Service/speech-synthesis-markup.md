---
title: A beszédfelismerés összefoglaló kódnyelv |} Microsoft Docs
description: A beszédfelismerés összefoglaló kódnyelv segítségével vezérelheti, kiejtés és a szöveg-beszéd átalakítás prosody.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 04/28/2018
ms.author: v-jerkin
ms.openlocfilehash: d955e7fd7805688ba103897c0d900c44f16514f8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348923"
---
# <a name="speech-synthesis-markup-language"></a>Beszéd összefoglaló kódnyelv

A beszédfelismerés összefoglaló Markup Language (SSML) egy XML-alapú kódnyelv, amelyek segítségével szabályozhatja a kiejtés és *prosody* a szöveg-beszéd átalakítás. (A ritmust és beszéd betűközű hivatkozik prosody – a zene, ha fogja). Adja meg a szavakat fonetikusan, javaslatokat tegyen számok értelmezése, szüneteltetése, vezérlő betűközű, kötet, és sebessége és több.

További információkért lásd: [beszéd összefoglaló Markup Language (SSML) 1.0-s verziója](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/) , a W3C.

A következő példák szemléltetik a SSML közös beszéd összefoglaló kell használni.

## <a name="add-a-break"></a>Szünet
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
</voice> </speak>
```

## <a name="change-speaking-rate"></a>Nyelvű sebességének módosítása
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
<prosody rate="+30.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="pronunciation"></a>Kiejtés
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
</voice> </speak>
```

## <a name="change-volume"></a>Kötet módosítása
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody volume="+20.00%">
    Welcome to Microsoft Cognitive Services Text-to-Speech API.
</prosody></voice> </speak>
```

## <a name="change-pitch"></a>Módosítsa a térköz
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
    <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)'>
    Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
</voice> </speak>
```

## <a name="change-pitch-contour"></a>Változás betűközű eloszlási
```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
<prosody contour="(80%,+20%) (90%,+30%)" >
    Good morning.
</prosody></voice> </speak>
```

## <a name="next-steps"></a>További lépések

* [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: how to a C# beszéd felismerésére](quickstart-csharp-windows.md)
