---
title: Adatkonverzió – LUIS
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan változtathatók meg a hosszúságú kimondott szöveg a Language Understanding (LUIS) előrejelzések előtt
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b2455df87c8eae1a48cb6c8b1381dad85d304bf4
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099240"
---
# <a name="convert-data-format-of-utterances"></a>Hosszúságú kimondott szöveg adatformátumának konvertálása
A LUIS a következő konverziókat biztosítja a felhasználók teljes leírásához az előrejelzés előtt: "

* Beszéd a szövegben [Cognitive Services Speech](../Speech-Service/overview.md) Service használatával.

## <a name="speech-to-text"></a>Diktálás

A beszéd szövege a LUIS-nal való integrációként van megadva.

### <a name="intent-conversion-concepts"></a>Szándék-átalakítási fogalmak
Ha a LUIS szövegre konvertálja a szöveget, lehetővé teszi a szóbeli hosszúságú kimondott szöveg küldését egy végpontra, és megkapta a LUIS-előrejelzési választ. A folyamat a [Speech](https://docs.microsoft.com/azure/cognitive-services/Speech) Service és a Luis közötti integráció. További információ a beszédfelismerési szándékról az [oktatóanyaghoz](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Fő követelmények
Ehhez az integrációhoz nem kell **Bing Speech API** kulcsot létrehoznia. Az integrációhoz a Azure Portalban létrehozott **Language Understanding** kulcs működik. Ne használja a LUIS Starter-kulcsot.

### <a name="pricing-tier"></a>Tarifacsomag
Ez az integráció eltérő [díjszabási](luis-limits.md#key-limits) modellt használ, mint a szokásos Language Understanding árképzési szintek.

### <a name="quota-usage"></a>Kvóta használata
További információ: [kulcs korlátai](luis-limits.md#key-limits) .

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatok kinyerése](luis-concept-data-extraction.md)

