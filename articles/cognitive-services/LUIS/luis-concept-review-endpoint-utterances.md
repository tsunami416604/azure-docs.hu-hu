---
title: Felhasználói kimondott szöveg áttekintése – LUIS
titleSuffix: Azure Cognitive Services
description: Az aktív tanulás, a felülvizsgálat végpont kimondott szöveget a helyes szándék és entitás. A LUIS úgy dönt, végpont kimondott szöveg, amely nem biztos benne.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219939"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Az aktív tanulás engedélyezésének fogalmai a végpontkimondott szöveg áttekintésével
Az aktív tanulás az előrejelzéspontosságának és a legkönnyebben megvalósíthatónak a három stratégia egyike. Az aktív tanulás, a felülvizsgálat végpont kimondott szöveget a helyes szándék és entitás. A LUIS úgy dönt, végpont kimondott szöveg, amely nem biztos benne.

## <a name="what-is-active-learning"></a>Mi az aktív tanulás?
Az aktív tanulás két lépésből áll. Először a LUIS kiválasztja az alkalmazás végpontját kap, amely jóváhagyást igénylő kimondott szövegeket kap. A második lépés az alkalmazás tulajdonosa vagy munkatársa a kiválasztott kimondott szöveg [ellenőrzésre,](luis-how-to-review-endpoint-utterances.md)beleértve a megfelelő szándékot és a szándékon belüli entitásokat. Az utterances áttekintése után a betanítása és közzététele az alkalmazást újra.

## <a name="which-utterances-are-on-the-review-list"></a>Mely kimondott szöveg szerepel a véleményezési listán?
A LUIS hozzáadja a kimondott szöveget a felülvizsgálati listához, ha a legfelső tüzelési szándék alacsony pontszámmal rendelkezik, vagy a felső két leképezés idopontjai túl közel vannak.

## <a name="single-pool-for-utterances-per-app"></a>Egyetlen készlet alkalmazásonkénti kimondott szöveghez
A **felülvizsgálati végpont utterances** lista nem változik a verzió alapján. A kimondott szövegek egyetlen készletét kell áttekintenie, függetlenül attól, hogy a kimondott szöveg melyik verzióját szerkeszti vagy az alkalmazás melyik verziója lett közzétéve a végponton.

## <a name="where-are-the-utterances-from"></a>Hol találhatók a kimondott szöveg
A végpontkimondott szöveg ek az alkalmazás HTTP-végpontjának végfelhasználói lekérdezéseiből származnak. Ha az alkalmazás nem közzétett, vagy még nem kapott lekéréseket, nincs kimondott szöveg, amelyet át kell tekintenie. Ha egy adott szándékhoz vagy entitáshoz nem érkezik végpontlekérés, nem rendelkezik az azokat tartalmazó kimondott szövegekkel.

## <a name="schedule-review-periodically"></a>Rendszeres ellenőrzés ütemezése
A javasolt kimondott szöveg áttekintése nem kell minden nap elvégezni, de a LUIS rendszeres karbantartásának részét kell, hogy vegye.

## <a name="delete-review-items-programmatically"></a>Ellenőrző elemek törlése programozott módon
Használja a **[törlés címzetlen utterances API-t.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** A törlés előtt biztonsági másolatot kell fokozta **[a naplófájlok exportálásával.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**

## <a name="enable-active-learning"></a>Aktív tanulás engedélyezése

Az aktív tanulás engedélyezéséhez naplóznia kell a felhasználói lekérdezéseket. Ez úgy érhető el, hogy meghívja a [végpontlekérdezést](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) a `log=true` querystring paraméterrel és értékkel.

## <a name="next-steps"></a>További lépések

* További információ a végpontkimondott szöveg [áttekintése](luis-how-to-review-endpoint-utterances.md)
