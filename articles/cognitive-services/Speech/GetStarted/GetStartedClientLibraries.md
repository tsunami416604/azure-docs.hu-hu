---
title: Ismerkedés a Bing Speech Recognition API-val klienskódtárak használatával |} A Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Microsoft Cognitive Services a Bing Speech klienskódtárak használatával hozhat létre alkalmazásokat, amelyek a beszélt hangot képes szöveggé alakítani.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 89eb18a2b4af76f6489442dc66ab12d0840e92c7
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669483"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Bing – beszédszolgáltatás klienskódtárak használatának első lépései

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

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

## <a name="additional-resources"></a>További források

- A [minták](../samples.md) lap Speech-klienskódtárakkal teljes mintáit tartalmazza.
- Ha egy ügyféloldali kódtár, amely még nem támogatott van szüksége, létrehozhat saját SDK-t. Alkalmazzon a [Speech WebSocket protokoll](../API-Reference-REST/websocketprotocol.md) a platform és a tetszőleges nyelv használata.

## <a name="license"></a>Licenc

Az összes Cognitive Services SDK-k és minták az MIT-licenccel rendelkező rendelkezik licenccel. További információkért lásd: [licenc](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
