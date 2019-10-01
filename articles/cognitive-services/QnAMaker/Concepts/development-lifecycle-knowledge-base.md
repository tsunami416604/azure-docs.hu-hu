---
title: Tudásbázis – QnA Maker élettartama
titleSuffix: Azure Cognitive Services
description: A QnA Maker legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 530869928f7a25e779cb01f0fe392efdbb54c5ba
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695119"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>A QnA Maker Tudásbázis életciklusa
A QnA Maker legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések. 

![Tartalomkészítési ciklus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>A QnA Maker Tudásbázis létrehozása
A QnA Maker Tudásbázis (KB) végpontja biztosítja a legjobb-match válasz tartalma a KB-os felhasználói lekérdezés. Tudásbázis létrehozása egy egyszeri művelet egy content tárház kérdések, válaszok és kapcsolódó metaadatok beállítására. Tudásbázis például gyakori kérdéseket tartalmazó oldalak, kézikönyvek vagy strukturált Q-A-párokat már meglévő tartalmak bejárásához hozható létre. Ismerje meg, hogyan [Tudásbázis létrehozása](../How-To/create-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Tesztelése és frissítése a Tudásbázis

A Tudásbázis készen áll a tesztelésre, a rendszer kitölti tartalmat, besorolást vagy automatikus kivonása után. Az interaktív tesztelés a QnA Maker portálon keresztül végezhető el a **tesztelési** panelen a gyakori felhasználói lekérdezések beírásával és annak ellenőrzésével, hogy a válaszok a megfelelő válasz és a megfelelő megbízhatósági pontszám alapján lettek-e visszaküldve. 

* **Az alacsony megbízhatósági pontszámok kijavítása**: alternatív kérdések hozzáadása. 
* **Ha egy lekérdezés helytelenül adja vissza az [alapértelmezett választ](confidence-score.md#change-default-answer)** : új válaszok hozzáadása a megfelelő kérdéshez. 

Ez a teszt-frissítés szoros ciklus továbbra is fennáll, addig, amíg az eredmények elégedett. Ismerje meg, hogyan [a Tudásbázis tesztelése](../How-To/test-knowledge-base.md).

Nagyméretű Tudásbázis esetén a [GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) -val és a `isTest` Body tulajdonsággal automatizált tesztelést használhat, amely a közzétett Tudásbázis helyett a `test` tudásbázist kérdezi le. 

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

Ezek tudásbázisok mindegyike külön tesztelési megcélozhatóvá válnak. Az API-k használatával megcélozhatja a Tudásbázis tesztelési verzióját `isTest` Body tulajdonsággal a generateAnswer hívásban.

Ismerje meg, hogyan [közzéteheti a tudásbázist](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Használat monitorozása
Kell tudniuk jelentkezni a csevegési naplók, a szolgáltatás, akkor engedélyeznie kell Application Insights amikor Ön [a QnA Maker szolgáltatás létrehozása](../How-To/set-up-qnamaker-service-azure.md).

A szolgáltatás használatának különböző analytics kérheti le. További információ az application insights használatával első [a QnA Maker szolgáltatás analytics](../How-To/get-analytics-knowledge-base.md).

Analytics-témák alapján, győződjön meg arról, megfelelő [frissítéseit a Tudásbázis](../How-To/edit-knowledge-base.md).

## <a name="version-control-of-a-knowledge-base"></a>Tudásbázis verziószám-vezérlése

A QnA Maker nem biztosít verziószám-vezérlést. A tudásbázist a **Beállítások** lapról kell exportálnia, és saját módszert és eszközöket kell használnia.

A Tudásbázis a TSV vagy az XLS formátumra való exportálása a **Beállítások** lapról végezhető el. 

Ha egy adott verzióra vissza kell térnie, importálnia kell a fájlt a helyi rendszerből. A **Beállítások** lapon importálja a TSV vagy XLS fájlt. Ezzel felülírja a Tudásbázisban az importált fájl tartalmával kapcsolatos kérdéseket és válaszokat.   

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megbízhatósági pontszám](./confidence-score.md)

## <a name="see-also"></a>Lásd még 

[Tudásbázis](./knowledge-base.md)
[QnA Maker – áttekintés](../Overview/overview.md)
