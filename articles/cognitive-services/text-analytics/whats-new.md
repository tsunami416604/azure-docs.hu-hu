---
title: A Text Analytics API újdonságai
titlesuffix: Text Analytics - Azure Cognitive Services
description: További információk a Text Analytics szolgáltatással kapcsolatos új fejleményekről
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 44ef6fb118be4d1110a693faded6c57bc8b4e2fd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499957"
---
# <a name="whats-new-in-the-text-analytics-api"></a>A Text Analytics API újdonságai.

A Text Analytics API frissítése folyamatosan történik. Ha naprakészen szeretne maradni a legutóbbi fejleményekkel, ez a cikk az új kiadásokkal és szolgáltatásokkal kapcsolatos információkat tartalmaz.

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>Elnevezett Entity Recognition v3 Public Preview – október 2019

Az elnevezett entitások felismerésének következő verziója már elérhető a nyilvános előzetes verzióban, és a szövegben talált entitások kibővített észlelését és kategorizálását biztosítja. Az alábbiakat biztosítja:

* A következő új entitások típusának felismerése:
    * Telefonszám
    * IP-cím

* Egy új végpont a személyes információk entitás-típusai felismeréséhez (csak angol nyelven)
* Különálló végpontok az entitások felismeréséhez és az entitások összekapcsolásához.

Az entitások csatolása az angol és a spanyol nyelvet támogatja. A nyelv támogatása az entitás típusától függően változik. További információ az alábbi hivatkozáson található. 

> [!div class="nextstepaction"]
> [További információ az elnevezett Entity Recognition v3-ról](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>Hangulatelemzés v3 nyilvános előzetes verzió – október 2019

A Hangulatelemzés következő verziója már elérhető a nyilvános előzetes verzióban, és jelentős fejlesztéseket biztosít az API Text kategorizálásának és pontozásának pontosságával és részleteivel. Emellett a következőket biztosítja:

* Automatikus címkézés a különböző érzelemekhez a szövegben.
* A dokumentumok és a mondatok szintjének elemzése és kimenete. 

Támogatja az angol (`en`), a japán (`ja`), a kínai egyszerűsített (`zh-Hans`), a kínai hagyományos (`zh-Hant`), a francia (`fr`), az olasz (`it`), a spanyol (`es`), a holland (`nl`), a portugál (`pt`) és a német (`de`), és a következő régiókban érhető el: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`és `West US 2`.

> [!div class="nextstepaction"]
> [További információ a Hangulatelemzés v3-ról](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>További lépések

* [Mi a Text Analytics API?](overview.md)  
* [Példa felhasználói forgatókönyvekre](text-analytics-user-scenarios.md)
* [Hangulat elemzése](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Nyelvfelismerés](how-tos/text-analytics-how-to-language-detection.md)
* [Entitások felismerése](how-tos/text-analytics-how-to-entity-linking.md)
* [Fő kifejezés kibontása](how-tos/text-analytics-how-to-keyword-extraction.md)
