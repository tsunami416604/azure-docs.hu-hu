---
title: Tudásbázis – QnA Maker élettartama
titleSuffix: Azure Cognitive Services
description: A QnA Maker legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 6ffc8931f23835f096c99480b286422fc6e20119
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447620"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>A QnA Maker Tudásbázis életciklusa
A QnA Maker legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések. 

![Tartalomkészítési ciklus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>A QnA Maker Tudásbázis létrehozása
A QnA Maker Tudásbázis (KB) végpontja biztosítja a legjobb-match válasz tartalma a KB-os felhasználói lekérdezés. Tudásbázis létrehozása egy egyszeri művelet egy content tárház kérdések, válaszok és kapcsolódó metaadatok beállítására. Tudásbázis például gyakori kérdéseket tartalmazó oldalak, kézikönyvek vagy strukturált Q-A-párokat már meglévő tartalmak bejárásához hozható létre. Ismerje meg, hogyan [Tudásbázis létrehozása](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Tesztelése és frissítése a Tudásbázis

A Tudásbázis készen áll a tesztelésre, a rendszer kitölti tartalmat, besorolást vagy automatikus kivonása után. A QnA Maker portálon keresztül végezhető interaktív tesztelése a **teszt** panel gyakori felhasználói lekérdezések beírásával, és annak ellenőrzésére, hogy a válaszokat a helyes választ, és elegendő magabiztossági pontszámot ad vissza. 

* **Alacsony megbízhatósági pontszámok megoldásához**: alternatív kérdések hozzáadása. 
* **Ha a lekérdezés nem megfelelően adja vissza a [alapértelmezett válasz](confidence-score.md#change-default-answer)** : Adja hozzá a megfelelő kérdésekre adott válaszok új. 

Ez a teszt-frissítés szoros ciklus továbbra is fennáll, addig, amíg az eredmények elégedett. Ismerje meg, hogyan [a Tudásbázis tesztelése](../How-To/test-knowledge-base.md).

A nagy Tudásbázis használata automatizált teszteléshez a a [generateAnswer API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) és a `isTest` törzs tulajdonság azokat a lekérdezéseket a `test` Tudásbázis helyett a közzétett Tudásbázis. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>A tudásbázis közzététele
Ha elkészült a Tudásbázis tesztelése, közzéteheti azt. A legújabb verzióra a tesztelt Tudásbázis egy dedikált Azure Search-index jelölő leküldések közzététele a **közzétett** Tudásbázis. Egy végpontot is létre fog hozni, amelyet az alkalmazásban vagy a csevegőrobot meg tud hívni.

Ezzel a módszerrel végzett módosítások folyamatban van a Tudásbázis tesztelése verziója nincsenek hatással a közzétett verzió, amely egy éles alkalmazásban élő lehet.

Ezek tudásbázisok mindegyike külön tesztelési megcélozhatóvá válnak. A teszt verzióját a Tudásbázis-célként az API-k használatával, `isTest` törzs generateAnswer hívásában tulajdonság.

Ismerje meg, hogyan [közzéteheti a tudásbázist](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Használat monitorozása
Kell tudniuk jelentkezni a csevegési naplók, a szolgáltatás, akkor engedélyeznie kell Application Insights amikor Ön [a QnA Maker szolgáltatás létrehozása](../How-To/set-up-qnamaker-service-azure.md).

A szolgáltatás használatának különböző analytics kérheti le. További információ az application insights használatával első [a QnA Maker szolgáltatás analytics](../How-To/get-analytics-knowledge-base.md).

Analytics-témák alapján, győződjön meg arról, megfelelő [frissítéseit a Tudásbázis](../How-To/edit-knowledge-base.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megbízhatósági pontszám](./confidence-score.md)

## <a name="see-also"></a>Lásd még 

[Tudásbázis](./knowledge-base.md)
[QnA Maker – áttekintés](../Overview/overview.md)
