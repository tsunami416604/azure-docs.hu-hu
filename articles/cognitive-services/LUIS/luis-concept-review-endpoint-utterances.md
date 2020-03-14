---
title: Felhasználói kifejezés áttekintése – LUIS
titleSuffix: Azure Cognitive Services
description: Aktív tanulás, a felülvizsgálat végpont utterances megfelelő leképezés és entitás. A LUIS úgy dönt, hogy pontosan végpontja kimondott szöveg.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 375d4b4e7c3fcafbdfde1ff447bedc3e16aff2f2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219939"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Aktív tanulás engedélyezésének végpont utterances áttekintésével kapcsolatos fogalmak
Aktív tanulás az előrejelzési pontosság és a legegyszerűbb megvalósításához három stratégia egyik. Aktív tanulás, a felülvizsgálat végpont utterances megfelelő leképezés és entitás. A LUIS úgy dönt, hogy pontosan végpontja kimondott szöveg.

## <a name="what-is-active-learning"></a>Mi az aktív tanulás
Aktív tanulás két lépésből áll. Először LUIS választ kap az alkalmazás végponti érvényesítési igénylő kimondott szöveg. A második lépést az alkalmazás tulajdonosa vagy a közreműködő ellenőrzi a kiválasztott hosszúságú kimondott szöveg [ellenőrzéséhez](luis-how-to-review-endpoint-utterances.md), beleértve a megfelelő szándékot és a szándékon belüli entitásokat is. A kimondott szöveg áttekintése, után betanítása, és tegye közzé újra az alkalmazást.

## <a name="which-utterances-are-on-the-review-list"></a>A felülvizsgálati listán vannak melyik kimondott szöveg
A LUIS ad hozzá a kimondott szöveg felülvizsgálati szándékot kiváltó felső alacsony pontszámmal rendelkezik, vagy a felső két szándék pontszámok túl kicsi.

## <a name="single-pool-for-utterances-per-app"></a>Egyetlen készletet az egyes alkalmazások kimondott szöveg
A **felülvizsgálati végpont hosszúságú kimondott szöveg** listája nem változik a verzió alapján. A kimondott szövegek egyetlen készletét kell áttekintenie, függetlenül attól, hogy a kimondott szöveg melyik verzióját szerkeszti vagy az alkalmazás melyik verziója lett közzétéve a végponton.

## <a name="where-are-the-utterances-from"></a>Hol találhatók a megcímkézzen
Végpont utterances végfelhasználói lekérdezések, az alkalmazás HTTP-végponton kell venni. Ha az alkalmazás nincs közzétéve, vagy nem kapott találatok még, nem rendelkezik megszólalásokat áttekintéséhez. Nincs végpont találatok érkezik egy adott szándékot vagy entitáshoz, ha nem rendelkezik a kimondott szöveg tekintse át azokat.

## <a name="schedule-review-periodically"></a>Rendszeres időközönként ütemezés áttekintése
Tekintse át a javasolt utterances nem kell végrehajtani minden nap, de a LUIS rendszeres karbantartása részének kell lennie.

## <a name="delete-review-items-programmatically"></a>Programozott módon felülvizsgálati elemek törlése
Használja a nem **[címkézett hosszúságú kimondott szöveg API törlését](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . **[A naplófájlok exportálásával a](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** törlés előtt biztonsági mentést készíthet a hosszúságú kimondott szöveg.

## <a name="enable-active-learning"></a>Aktív tanulás engedélyezése

Az aktív tanulás engedélyezéséhez naplóznia kell a felhasználói lekérdezéseket. Ezt úgy hajthatja végre, hogy meghívja a [végponti lekérdezést](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) a `log=true` querystring paraméterrel és értékkel.

## <a name="next-steps"></a>Következő lépések

* Útmutató az Endpoint hosszúságú kimondott szöveg [áttekintéséhez](luis-how-to-review-endpoint-utterances.md)
