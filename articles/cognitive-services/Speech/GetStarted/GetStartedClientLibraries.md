---
title: Első lépésként a Microsoft Speech Recognition API a Bing Speech klienskódtárak használatára |} A Microsoft Docs
description: A Microsoft Cognitive Services a Microsoft Speech Service klienskódtárak használatával hozhat létre alkalmazásokat, amelyek a beszélt hangot képes szöveggé alakítani.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: f4b6a97260c6dc176600af8844001e4de819ff7c
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42357510"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Bing – beszédszolgáltatás klienskódtárak használatának első lépései

Amellett, hogy a REST API-n keresztül közvetlen HTTP-kérelem indítására, a Bing Speech Service nyújt a fejlesztőknek Speech klienskódtárak különböző nyelveken. A beszédfelismerés klienskódtárai:

- Támogatja a speciális képességek beszédfelismerés, például a valós idejű, mennyi ideig hang stream (legfeljebb 10 perc) és folyamatos felismerés köztes eredményeket.
- Adjon meg egy egyszerű és bármilyen API-t a prioritási nyelven.
- Az alsó szintű kommunikációs Részletek elrejtése.

Jelenleg a következő Bing Speech-ügyfélkódtárak érhetők el:

- [C# asztali könyvtár](GetStartedCSharpDesktop.md)
- [C# szolgáltatási kódtár](GetStartedCSharpServiceLibrary.md)
- [JavaScript-kódtár](GetStartedJSWebsockets.md)
- [Androidhoz készült Java-kódtár](GetStartedJavaAndroid.md)
- [IOS-es Objective-C-függvénytár](Get-Started-ObjectiveC-iOS.md)

> [!NOTE] 
A 2018 május is megjelent az új [beszédszolgáltatás](/speech-service/overview.md) nyilvános előzetes verzióban érhető el. Javasoljuk, hogy [ingyenes kipróbálása](/speech-service/get-started.md). 

## <a name="additional-resources"></a>További források

- A [minták](../samples.md) lap Speech-klienskódtárakkal teljes mintáit tartalmazza.
- Ha egy ügyféloldali kódtár, amely még nem támogatott van szüksége, létrehozhat saját SDK-t. Alkalmazzon a [Speech WebSocket protokoll](../API-Reference-REST/websocketprotocol.md) a platform és a tetszőleges nyelv használata.

## <a name="license"></a>Licenc

Az összes Cognitive Services SDK-k és minták az MIT-licenccel rendelkező rendelkezik licenccel. További információkért lásd: [licenc](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
