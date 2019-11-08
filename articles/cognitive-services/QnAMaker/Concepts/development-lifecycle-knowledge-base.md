---
title: Tudásbázis – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker a legjobbat tanulja meg a modell változásainak, a kitalált példáknak, a közzétételnek és az adatoknak a végponti lekérdezésekből való összegyűjtése során.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1cb5af13bdd309c762337e64ecde8538afc756b0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794838"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Tudásbázis életciklusa QnA Maker
QnA Maker a legjobbat tanulja meg a modell változásainak, a kitalált példáknak, a közzétételnek és az adatoknak a végponti lekérdezésekből való összegyűjtése során. 

![Tartalomkészítési ciklus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker Tudásbázis létrehozása
A (z) QnA Maker Tudásbázis (KB) végpontja a TUDÁSBÁZISCIKK tartalmának megfelelő választ ad a felhasználók lekérdezéséhez. A Tudásbázis létrehozása egy egyszeri művelet, amely a kérdések, válaszok és a hozzájuk kapcsolódó metaadatok tartalmi tárházának beállítására szolgál. A Tudásbázis olyan meglévő tartalmak bejárásával hozható létre, mint a GYIK-lapok, a termék-kézikönyvek vagy A strukturált Q-A pár. Útmutató [Tudásbázis létrehozásához](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>A Tudásbázis tesztelése és frissítése

A Tudásbázis készen áll a tesztelésre, ha a tartalom fel van töltve, vagy akár szerkesztőségi módon, akár automatikus kinyeréssel. Az interaktív tesztelés a QnA Maker portálon keresztül végezhető el a **tesztelési** panelen a gyakori felhasználói lekérdezések beírásával és annak ellenőrzésével, hogy a válaszok a megfelelő válasz és a megfelelő megbízhatósági pontszám alapján lettek-e visszaküldve. 

* **Az alacsony megbízhatósági pontszámok kijavítása**: alternatív kérdések hozzáadása. 
* **Ha egy lekérdezés helytelenül adja vissza az [alapértelmezett választ](confidence-score.md#change-default-answer)** : új válaszok hozzáadása a megfelelő kérdéshez. 

A test-Update ezen szoros ciklusa addig folytatódik, amíg meg nem felel az eredményekkel. Ismerje meg, hogyan [tesztelheti tudásbázisát](../How-To/test-knowledge-base.md).

Nagyméretű Tudásbázis esetén a [GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) és a `isTest` Body tulajdonság használatával automatizált tesztelést használhat, amely a közzétett Tudásbázis helyett a `test` tudásbázist kérdezi le. 

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>A tudásbázis közzététele
Ha elkészült a Tudásbázis tesztelésével, közzéteheti. A közzététel leküldi a tesztelt Tudásbázis legújabb verzióját egy dedikált Azure Cognitive Search indexre, amely a **közzétett** tudásbázist jelképezi. Egy végpontot is létre fog hozni, amelyet az alkalmazásban vagy a csevegőrobot meg tud hívni.

Így a Tudásbázis tesztelési verziójában végrehajtott módosítások nem érintik az éles alkalmazásokban élő közzétett verziót.

Ezen tudásbázisok mindegyike külön tesztelésre is megcélozható. Az API-k használatával megcélozhatja a Tudásbázis tesztelési verzióját `isTest` Body tulajdonsággal a generateAnswer hívásban.

Ismerje meg, hogyan [teheti közzé a tudásbázist](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Használat monitorozása
Ahhoz, hogy naplózni tudja a szolgáltatás csevegési naplóit, engedélyeznie kell Application Insights a [QnA Maker szolgáltatás létrehozásakor](../How-To/set-up-qnamaker-service-azure.md).

A szolgáltatás használatának különböző elemzéseit is lekérheti. További információ az Application-elemzések használatáról az [QnA Maker szolgáltatás elemzési](../How-To/get-analytics-knowledge-base.md)adatainak beszerzéséhez.

Az elemzések alapján elsajátított megfelelő [frissítéseket a Tudásbázisban](../How-To/edit-knowledge-base.md)teheti meg.

## <a name="version-control-of-a-knowledge-base"></a>Tudásbázis verziószám-vezérlése

A QnA Maker nem biztosít verziószám-vezérlést. A tudásbázist a **Beállítások** lapról kell exportálnia, és saját módszert és eszközöket kell használnia.

A Tudásbázis a TSV vagy az XLS formátumra való exportálása a **Beállítások** lapról végezhető el. 

Ha egy adott verzióra vissza kell térnie, importálnia kell a fájlt a helyi rendszerből. A **Beállítások** lapon importálja a TSV vagy XLS fájlt. Ezzel felülírja a Tudásbázisban az importált fájl tartalmával kapcsolatos kérdéseket és válaszokat.   

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megbízhatósági pontszám](./confidence-score.md)