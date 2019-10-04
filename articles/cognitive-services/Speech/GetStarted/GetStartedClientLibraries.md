---
title: Ismerkedés a Bing Speech-felismerési API-val az ügyféloldali kódtárak használatával | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Microsoft Cognitive Services Bing Speech ügyféloldali kódtárak használatával fejlesztheti a beszélt hangot szöveggé konvertáló alkalmazásokat.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8ea0c9e7104b29456749c7f1af2a671b32a8fde2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966847"
---
# <a name="get-started-with-bing-speech-service-client-libraries"></a>Ismerkedés a Bing Speech Service ügyféloldali kódtárakkal

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A közvetlen HTTP-kéréseket REST API segítségével Bing Speech szolgáltatás a különböző nyelveken beszélő ügyféloldali kódtárakat biztosít a fejlesztőknek. A beszédfelismerési ügyféloldali kódtárak:

- Támogatja a beszédfelismerés fejlettebb funkcióit, például a közbenső eredményeket valós időben, a hosszú hangadatfolyamot (akár 10 percet) és a folyamatos felismerést.
- Adjon meg egy egyszerű és köznyelvi API-t a preferencia nyelvén.
- Az alacsony szintű kommunikációs Részletek elrejtése.

Jelenleg a következő Bing Speech ügyféloldali kódtárak érhetők el:

- [C#asztali kódtár](GetStartedCSharpDesktop.md)
- [C#szolgáltatási könyvtár](GetStartedCSharpServiceLibrary.md)
- [JavaScript-kódtár](GetStartedJSWebsockets.md)
- [Java-kódtár Androidhoz](GetStartedJavaAndroid.md)
- [Objective-C függvénytár iOS-hez](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>További források

- A [minták](../samples.md) lap teljes mintákat biztosít a beszédfelismerési ügyféloldali kódtárak használatához.
- Ha olyan ügyféloldali függvénytárra van szüksége, amely még nem támogatott, létrehozhat saját SDK-t is. Implementálja a [Speech WebSocket protokollt](../API-Reference-REST/websocketprotocol.md) a platformon, és használja az Ön által választott nyelvet.

## <a name="license"></a>Licenc

Az SDK-k és a minták minden Cognitive Services licence az MIT licenccel rendelkezik. További információ: [License](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
