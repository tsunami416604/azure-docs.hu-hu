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
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: a6b89b315c4cdb1438fc8256cfc01793b3c0f920
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619744"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Aktív tanulás engedélyezésének végpont utterances áttekintésével kapcsolatos fogalmak
Aktív tanulás az előrejelzési pontosság és a legegyszerűbb megvalósításához három stratégia egyik. Aktív tanulás, a felülvizsgálat végpont utterances megfelelő leképezés és entitás. A LUIS úgy dönt, hogy pontosan végpontja kimondott szöveg.

## <a name="what-is-active-learning"></a>Mi az aktív tanulás
Aktív tanulás két lépésből áll. Először LUIS választ kap az alkalmazás végponti érvényesítési igénylő kimondott szöveg. A második lépést az alkalmazás tulajdonos vagy közreműködő, érvényesíteni a kiválasztott megcímkézzen [tekintse át](luis-how-to-review-endpoint-utterances.md), beleértve a megfelelő cél és minden olyan entitások a célt. A kimondott szöveg áttekintése, után betanítása, és tegye közzé újra az alkalmazást. 

## <a name="which-utterances-are-on-the-review-list"></a>A felülvizsgálati listán vannak melyik kimondott szöveg
A LUIS ad hozzá a kimondott szöveg felülvizsgálati szándékot kiváltó felső alacsony pontszámmal rendelkezik, vagy a felső két szándék pontszámok túl kicsi. 

## <a name="single-pool-for-utterances-per-app"></a>Egyetlen készletet az egyes alkalmazások kimondott szöveg
A **tekintse át a végpont utterances** lista alapján a verzió nem változik. A kimondott szövegek egyetlen készletét kell áttekintenie, függetlenül attól, hogy a kimondott szöveg melyik verzióját szerkeszti vagy az alkalmazás melyik verziója lett közzétéve a végponton. 

## <a name="where-are-the-utterances-from"></a>Hol találhatók a megcímkézzen
Végpont utterances végfelhasználói lekérdezések, az alkalmazás HTTP-végponton kell venni. Ha az alkalmazás nincs közzétéve, vagy nem kapott találatok még, nem rendelkezik megszólalásokat áttekintéséhez. Nincs végpont találatok érkezik egy adott szándékot vagy entitáshoz, ha nem rendelkezik a kimondott szöveg tekintse át azokat. 

## <a name="schedule-review-periodically"></a>Rendszeres időközönként ütemezés áttekintése
Tekintse át a javasolt utterances nem kell végrehajtani minden nap, de a LUIS rendszeres karbantartása részének kell lennie. 

## <a name="delete-review-items-programmatically"></a>Programozott módon felülvizsgálati elemek törlése
Használja a nem **[címkézett hosszúságú kimondott szöveg](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API törlését. Készítsen biztonsági másolatot a törlés előtt ezeket utterances  **[naplófájlok exportálása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** .

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [tekintse át](luis-how-to-review-endpoint-utterances.md) végpont kimondott szöveg
