---
title: Tudásbázis – QnA Maker élettartama
description: A QnA Maker legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914952"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>A QnA Maker Tudásbázis életciklusa
A QnA Maker legjobb megtanulja az iteratív ciklusának adatmodell változásainak, utterance (kifejezés) példákat, közzététel és adatok összegyűjtése a végpont lekérdezések.

![Tartalomkészítési ciklus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>A QnA Maker Tudásbázis létrehozása
A QnA Maker Tudásbázis (KB) végpontja biztosítja a legjobb-match válasz tartalma a KB-os felhasználói lekérdezés. A Tudásbázis létrehozása egy egyszeri művelet, amely a kérdések, válaszok és a kapcsolódó metaadatok tartalmi tárházának beállítására szolgál. Tudásbázis például gyakori kérdéseket tartalmazó oldalak, kézikönyvek vagy strukturált Q-A-párokat már meglévő tartalmak bejárásához hozható létre. Útmutató [Tudásbázis létrehozásához](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Tesztelése és frissítése a Tudásbázis

A Tudásbázis készen áll a tesztelésre, a rendszer kitölti tartalmat, besorolást vagy automatikus kivonása után. Az interaktív tesztelés a QnA Maker portálon keresztül végezhető el a **tesztelési** panelen a gyakori felhasználói lekérdezések beírásával és annak ellenőrzésével, hogy a válaszok a megfelelő válasz és a megfelelő megbízhatósági pontszám alapján lettek-e visszaküldve.

* **Az alacsony megbízhatósági pontszámok kijavítása**: alternatív kérdések hozzáadása.
* **Ha egy lekérdezés helytelenül adja vissza az [alapértelmezett választ](../How-to/change-default-answer.md)** : új válaszok hozzáadása a megfelelő kérdéshez.

Ez a teszt-frissítés szoros ciklus továbbra is fennáll, addig, amíg az eredmények elégedett. Ismerje meg, hogyan [tesztelheti tudásbázisát](../How-To/test-knowledge-base.md).

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
Ha elkészült a Tudásbázis tesztelése, közzéteheti azt. A közzététel leküldi a tesztelt Tudásbázis legújabb verzióját egy dedikált Azure Cognitive Search indexre, amely a **közzétett** tudásbázist jelképezi. Egy végpontot is létre fog hozni, amelyet az alkalmazásban vagy a csevegőrobot meg tud hívni.

Ezzel a módszerrel végzett módosítások folyamatban van a Tudásbázis tesztelése verziója nincsenek hatással a közzétett verzió, amely egy éles alkalmazásban élő lehet.

Ezek tudásbázisok mindegyike külön tesztelési megcélozhatóvá válnak. Az API-k használatával megcélozhatja a Tudásbázis tesztelési verzióját `isTest` Body tulajdonsággal a generateAnswer hívásban.

Ismerje meg, hogyan [teheti közzé a tudásbázist](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Használat monitorozása
Ahhoz, hogy naplózni tudja a szolgáltatás csevegési naplóit, engedélyeznie kell Application Insights a [QnA Maker szolgáltatás létrehozásakor](../How-To/set-up-qnamaker-service-azure.md).

A szolgáltatás használatának különböző analytics kérheti le. További információ az Application-elemzések használatáról az [QnA Maker szolgáltatás elemzési](../How-To/get-analytics-knowledge-base.md)adatainak beszerzéséhez.

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
> [Aktív tanulási javaslatok](./active-learning-suggestions.md)