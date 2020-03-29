---
title: A tudásbázis életciklusa - QnA Maker
description: A QnA Maker a modellmódosítások iteratív ciklusában, az utterance (kifejezés) példák, a közzététel és a végpontlekérdezések adatok gyűjtésével tanul a legjobban.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914952"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Tudásbázis életciklusa a QnA Maker programban
A QnA Maker a modellmódosítások iteratív ciklusában, az utterance (kifejezés) példák, a közzététel és a végpontlekérdezések adatok gyűjtésével tanul a legjobban.

![Tartalomkészítési ciklus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker tudásbázis létrehozása
A QnA Maker tudásbázis (KB) végpontja a tudásbázis tartalma alapján a legjobb választ adja a felhasználói lekérdezésre. A tudásbázis létrehozása egyszeri művelet a kérdések, válaszok és a kapcsolódó metaadatok tartalomtárának beállításához. A tudásbázis meglévő tartalmak, például GYIK-oldalak, termékkézikönyvek vagy strukturált Kérdések-A párok feltérképezésével hozható létre. További információ a [tudásbázis létrehozásáról.](../quickstarts/create-publish-knowledge-base.md)

## <a name="testing-and-updating-the-knowledge-base"></a>A tudásbázis tesztelése és frissítése

A tudásbázis készen áll a tesztelésre, ha a tartalom feltöltése, akár szerkesztőileg, akár automatikus kinyerés útján. Az interaktív tesztelés a Teszt **panelen** keresztül végezhető el a QnA Maker portálon, ha közös felhasználói lekérdezéseket ad meg, és ellenőrzi, hogy a válaszok a helyes válaszés a megfelelő megbízhatósági pontszám alapján adták-e vissza.

* **Az alacsony megbízhatósági pontszámok javítása**: alternatív kérdések hozzáadása.
* **Ha egy lekérdezés helytelenül adja vissza az [alapértelmezett választ:](../How-to/change-default-answer.md)** adjon új válaszokat a megfelelő kérdésre.

Ez a szoros hurok a teszt-frissítés addig folytatódik, amíg meg nem elégedett az eredménnyel. További információ [a tudásbázis teszteléséről.](../How-To/test-knowledge-base.md)

Nagy kb-ek esetén automatikus tesztelést használjon `isTest` a [generateAnswer API-val](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) és a törzs tulajdonsággal, amely a közzétett tudásbázis helyett a `test` tudásbázist kérdezi le.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>A tudásbázis közzététele
Miután végzett a tudásbázis tesztelése, közzéteheti azt. A közzététel leküldésea a tesztelt tudásbázis legújabb verziójára egy dedikált Azure Cognitive Search indexre, amely a **közzétett** tudásbázist képviseli. Egy végpontot is létre fog hozni, amelyet az alkalmazásban vagy a csevegőrobot meg tud hívni.

Ily módon a tudásbázis tesztverziójában végrehajtott módosítások nincsenek hatással az éles alkalmazásokban élő közzétett verzióra.

Ezen tudásbázisok mindegyike külön-külön tesztelhető. Az API-k használatával a tudásbázis tesztverzióját `isTest` testtulajdonsággal célozhatja meg a generateAnswer-hívásban.

További információ [a tudásbázis közzétételéről.](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)

## <a name="monitor-usage"></a>Használat monitorozása
Ahhoz, hogy naplózhassa a szolgáltatás csevegőnaplóit, engedélyeznie kell az Application Insights szolgáltatást [a QnA Maker szolgáltatás létrehozásakor.](../How-To/set-up-qnamaker-service-azure.md)

A szolgáltatás használatának különböző elemzéseit kaphatja meg. További információ arról, hogyan használhatja az alkalmazáselemzéseket [a QnA Maker szolgáltatás elemzéséhez.](../How-To/get-analytics-knowledge-base.md)

Az elemzések alapján tegye meg a megfelelő [frissítéseket a tudásbázisán.](../How-To/edit-knowledge-base.md)

## <a name="version-control-for-data-in-your-knowledge-base"></a>Verziókövetés a tudásbázisban lévő adatokhoz

Az adatok verziószabályozása a QnA Maker portál **Beállítások** lapján található importálási/exportálási funkciókon keresztül érhető el.

A tudásbázis biztonsági másolatot kaphat a tudásbázis `.tsv` `.xls` exportálásával, akár formátumban, akár formátumban. Exportálás után ezt a fájlt a rendszeres forrásellenőrzési ellenőrzés részeként adja meg.

Ha vissza kell lépnie egy adott verzióra, importálnia kell a fájlt a helyi rendszerből. Az exportált tudásbázist csak a **Beállítások** lapon történő importálással **szabad** használni. Nem használható fájl- vagy URL-dokumentum-adatforrásként. Ez felváltja a tudásbázisban jelenleg szereplő kérdéseket és válaszokat az importált fájl tartalmával.

## <a name="test-and-production-knowledge-base"></a>Vizsgálati és termelési tudásbázis
A tudásbázis a QnA Maker által létrehozott, karbantartott és használt kérdések és válaszkészletek tárháza. Minden QnA Maker erőforrás több tudásbázist is képes tárolni.

A tudásbázis nak két állapota van: *teszt* és *közzététel.*

### <a name="test-knowledge-base"></a>Vizsgálati tudásbázis

A *teszttudás-bázis* az a verzió, amelyet jelenleg szerkesztettek, mentettek és teszteltek a válaszok pontossága és teljessége szempontjából. A teszttudás-bázison végrehajtott módosítások nem érintik az alkalmazás vagy a csevegőrobot végfelhasználóját. A teszttudás-bázis `test` a HTTP-kérelemben ismert. A `test` tudás elérhető a QnA Maker portál interaktív **teszt** ablaktábláján.

### <a name="production-knowledge-base"></a>Termelési tudásbázis

A *közzétett tudásbázis* a csevegőrobotban vagy az alkalmazásban használt verzió. A tudásbázis közzétételének művelete a tudásbázis közzétett verziójába helyezi a teszttudásbázis tartalmát. Mivel a közzétett tudásbázis az a verzió, amelyet az alkalmazás a végponton keresztül használ, győződjön meg arról, hogy a tartalom helyes és jól tesztelt. A közzétett tudásbázis `prod` a HTTP-kérelemben ismert.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Aktív tanulási javaslatok](./active-learning-suggestions.md)