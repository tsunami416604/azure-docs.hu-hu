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
ms.date: 01/27/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b1978e45a7554358ddd948879143411f89e4c1b2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843405"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Tudásbázis életciklusa QnA Maker
QnA Maker a legjobbat tanulja meg a modell változásainak, a kitalált példáknak, a közzétételnek és az adatoknak a végponti lekérdezésekből való összegyűjtése során.

![Tartalomkészítési ciklus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker Tudásbázis létrehozása
A (z) QnA Maker Tudásbázis (KB) végpontja a TUDÁSBÁZISCIKK tartalmának megfelelő választ ad a felhasználók lekérdezéséhez. A Tudásbázis létrehozása egy egyszeri művelet, amely a kérdések, válaszok és a hozzájuk kapcsolódó metaadatok tartalmi tárházának beállítására szolgál. A Tudásbázis olyan meglévő tartalmak bejárásával hozható létre, mint a GYIK-lapok, a termék-kézikönyvek vagy A strukturált Q-A pár. Útmutató [Tudásbázis létrehozásához](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>A Tudásbázis tesztelése és frissítése

A Tudásbázis készen áll a tesztelésre, ha a tartalom fel van töltve, vagy akár szerkesztőségi módon, akár automatikus kinyeréssel. Az interaktív tesztelés a QnA Maker portálon keresztül végezhető el a **tesztelési** panelen a gyakori felhasználói lekérdezések beírásával és annak ellenőrzésével, hogy a válaszok a megfelelő válasz és a megfelelő megbízhatósági pontszám alapján lettek-e visszaküldve.

* **Az alacsony megbízhatósági pontszámok kijavítása**: alternatív kérdések hozzáadása.
* **Ha egy lekérdezés helytelenül adja vissza az [alapértelmezett választ](../How-to/change-default-answer.md)** : új válaszok hozzáadása a megfelelő kérdéshez.

A test-Update ezen szoros ciklusa addig folytatódik, amíg meg nem felel az eredményekkel. Ismerje meg, hogyan [tesztelheti tudásbázisát](../How-To/test-knowledge-base.md).

Nagyméretű Tudásbázis esetében használja az automatikus tesztelést a [GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) -val és a `isTest` Body tulajdonsággal, amely a közzétett Tudásbázis helyett a `test` tudásbázist kérdezi le.

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

## <a name="version-control-for-data-in-your-knowledge-base"></a>A tudásbázisban található adatok verziószám-vezérlése

Az adatverzió-vezérlés a QnA Maker portál **Beállítások** lapjának importálási/exportálási szolgáltatásain keresztül érhető el.

A Tudásbázisról biztonsági mentést készíthet a Tudásbázis `.tsv` vagy `.xls` formátumban történő exportálásával. Az exportálást követően vegye fel ezt a fájlt a normál verziókövetés ellenőrzésének részeként.

Ha egy adott verzióra vissza kell térnie, importálnia kell a fájlt a helyi rendszerből. Az exportált tudásbázist **csak importálással kell használni** a **Beállítások** lapon. Nem használható fájl-vagy URL-dokumentum típusú adatforrásként. Ezzel felülírja a Tudásbázisban az importált fájl tartalmával kapcsolatos kérdéseket és válaszokat.

## <a name="test-and-production-knowledge-base"></a>Tesztelési és üzemi Tudásbázis
A Tudásbázis a QnA Maker által létrehozott, karbantartott és használt kérdések és válaszok tárháza. Minden QnA Maker erőforrás több Tudásbázisban is tarthat.

A Tudásbázis két állapottal rendelkezik: *tesztelés* és *Közzététel*.

### <a name="test-knowledge-base"></a>Tudásbázis tesztelése

A *teszt* Tudásbázis a válaszok pontosságának és teljességének aktuálisan szerkesztett, mentett és tesztelt verziója. A teszt Tudásbázisban végrehajtott módosítások nem érintik az alkalmazás vagy a csevegési robot végfelhasználóját. A teszt Tudásbázis a HTTP-kérelemben `test` néven ismert. A `test` ismerete a QnA Maker portál interaktív **teszt** paneljén érhető el.

### <a name="production-knowledge-base"></a>Üzemi Tudásbázis

A *közzétett Tudásbázis* a csevegési robotban vagy alkalmazásban használt verzió. A Tudásbázis közzétételének művelete a Tudásbázis közzétett verziójában a teszt Tudásbázis tartalmát helyezi el. Mivel a közzétett Tudásbázis az alkalmazás által a végponton keresztül használt verzió, győződjön meg arról, hogy a tartalom helyes és jól tesztelt. A közzétett Tudásbázis neve `prod` a HTTP-kérelemben.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Megbízhatósági pontszám](./confidence-score.md)