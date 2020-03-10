---
title: Minta szintaxisának hivatkozása – LUIS
titleSuffix: Azure Cognitive Services
description: Entitásokat hozhat létre a Language Understanding (LUIS) alkalmazások felhasználói hosszúságú kimondott szöveg származó kulcsfontosságú adatok kinyeréséhez. Az ügyfélalkalmazás a kinyert adatszolgáltatásokat használja.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 696f4bdc22bed01a4b5be8bff63ade482a8dbe0a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393812"
---
# <a name="pattern-syntax"></a>Minta szintaxisa

A minta szintaxisa az utterance (kifejezés) sablont. A sablon tartalmaznia kell a szavak és szavak és egyeztetni kívánt entitásokat és írásjelek figyelmen kívül kívánja. **Nem** reguláris kifejezés.

> [!CAUTION]
> A minták csak a gépi megtanult entitások szüleit tartalmazzák, nem alösszetevőkből.

A mintákban lévő entitások kapcsos zárójelek között találhatók, `{}`. Minták lehetnek, alá tartozó alanyokra és szerepkörökkel. [Minta. a bármely](luis-concept-entity-types.md#patternany-entity) entitás csak mintázatokban használható.

A minta szintaxisa a következő szintaxist támogatja:

|Függvény|Szintaxis|Beágyazási szint|Példa|
|--|--|--|--|
|entitás| {} kapcsos zárójelek|2|Hol van a (z) {Entity-Name} űrlap?|
|választható|[] – szögletes zárójelek<BR><BR>A választható és a csoportosítás bármely kombinációja esetében 3 korlát van |2|A kérdőjel nem kötelező [?]|
|Csoportosítás|() – zárójelek|2|(a \| b)|
|vagy| \| – függőleges sáv (cső)<br><br>A függőleges sávokon (vagy) legfeljebb 2 van egy csoportban |-|Hol van az űrlap ({Form-Name-Short &#x7c; } {Form-Name-Long &#x7c; } {Form-Number})|
|a Kimondás kezdete és/vagy vége|^-kalap|-|^ a Kimondás kezdete<br>a Kimondás befejeződött ^<br>^ Strict teljes körű megfeleltetés a ({Number}} entitással) ^|

## <a name="nesting-syntax-in-patterns"></a>Szintaxisok beágyazásának mintázata

A szögletes zárójelek **választható** szintaxisa két szint beágyazására használható. Például: `[[this]is] a new form`. Ez a példa a következő hosszúságú kimondott szöveg teszi lehetővé:

|Beágyazott opcionális Kimondás – példa|Magyarázat|
|--|--|
|Ez egy új űrlap|a minta összes szavának felel meg|
|új űrlap|megfelel a külső opcionális és nem választható szavaknak a mintában|
|új űrlap|csak a szükséges szavakra illeszkedik|

A zárójelekkel rendelkező **csoportosítási** szintaxis két szinten ágyazható be. Például: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Ez a funkció lehetővé teszi a három entitás összeegyeztetését.

Ha a Entity1 olyan hely, ahol a szerepkörök, például a Origin (Seattle) és a Destination (Kairó) és a 2. entitás egy ismert épület neve a List entitásból (RedWest-C), a következő hosszúságú kimondott szöveg az alábbi mintát képezi le:

|Beágyazott csoportosítás – példa|Magyarázat|
|--|--|
|RedWest-C|megfelel a külső csoportosítási entitásnak|
|Seattle|megfelel a belső csoportosítási entitások egyikének.|
|Cairo|megfelel a belső csoportosítási entitások egyikének.|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Csoportok korlátainak beágyazása opcionális szintaxissal

A **választható** szintaxisú **Csoportosítás** kombinációja legfeljebb 3 beágyazási szinttel rendelkezik.

|Engedélyezett|Példa|
|--|--|
|Igen|([(test1 &#x7c; teszt2)] &#x7c; test3)|
|Nem|([([test1] &#x7c; teszt2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>A vagy-ing szintaxissal rendelkező csoportok korlátainak beágyazásával

A **Group** with vagy a **-ing** szintaxisának kombinációja legfeljebb 2 függőleges sávot tartalmaz.

|Engedélyezett|Példa|
|--|--|
|Igen|(test1 &#x7c; teszt2 &#x7c; (test3 &#x7c; test4))|
|Nem|(test1 &#x7c; teszt2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Entitás hozzáadása egy minta sablon szintaxisát
Ha entitást szeretne hozzáadni a mintázat sablonhoz, az entitás nevét kapcsos zárójelekkel kell megadnia, például `Who does {Employee} manage?`.

|Az entitás minta|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Az entitásokhoz és a szerepkör hozzáadása egy minta sablon szintaxisát
Az entitások szerepköre `{entity:role}`ként van megjelölve az entitás nevével, majd egy kettősponttal, majd a szerepkör nevével. Ha szerepkörrel rendelkező entitást szeretne felvenni a minta sablonba, az entitás nevét és a szerepkör nevét kapcsos zárójelekkel kell megadnia, például `Book a ticket from {Location:Origin} to {Location:Destination}`.

|Az entitás szerepkörök minta|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Egy pattern.any hozzáadása minta sablon szintaxisát
A Pattern.any entitás lehetővé teszi, hogy a minta egy entitás különböző hosszúságú. Mindaddig, amíg a minta sablon követik, a pattern.any lehet bármely olyan.

Egy **minta** hozzáadásához vegye fel a mintát a mintázat sablonba. bármely olyan entitást, amely kapcsos zárójelekkel rendelkezik, például `How much does {Booktitle} cost and what format is it available in?`.

|Az entitás Pattern.any minta|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|A mintában a könyv címek|
|--|
|Mennyibe kerül a **könyv ellopása** , és milyen formátumban érhető el?|
|Mennyibe kerül a **Rákérdezés** és milyen formátumban érhető el?|
|Mennyibe kerül a **kutya furcsa incidense az éjszakai időszakban** , és milyen formátumban érhető el?|

A könyv címének szavai nem zavaróak a LUIS számára, mert LUIS tudja, hol fejeződik be a könyv címe, a minta. bármely entitás alapján.

## <a name="explicit-lists"></a>Explicit listák

hozzon létre egy [explicit listát](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) a szerzői API-n keresztül, hogy a kivételt a következő esetekben engedélyezze:

* A minta egy [mintát tartalmaz. bármilyen](luis-concept-entity-types.md#patternany-entity)
* A minta szintaxisa pedig lehetővé teszi a helytelen entitások kinyerésének lehetőségét a Kimondás alapján.

Tegyük fel például, hogy rendelkezik egy olyan mintázattal, amely tartalmazza a választható szintaxist, a `[]`és az entitás szintaxisát, `{}`, kombinálva az adatok helytelen kinyerésének módját.

Fontolja meg a minta "[keresése] e-mail kapcsolatos {subject} [{személy}]".

A következő hosszúságú kimondott szöveg a **tulajdonos** és a **személy** entitás helyesen és helytelenül van kibontva:

|Kimondott szöveg|Entitás|Megfelelő kivonása|
|--|--|:--:|
|a Chris kutyák kapcsolatos e-mail|tulajdonos kutyák =<br>személy = Chris|✔|
|a férfi a La Mancha kapcsolatos e-mailben|tulajdonos férfi =<br>személy La Mancha =|X|

Az előző táblázatban a tulajdonosnak `the man from La Mancha` kell lennie (könyv címe), de mivel a tárgy tartalmazza a választható szót `from`, a cím helytelenül van jelezve.

Ha ki szeretné javítani ezt a kivételt a mintához, adja hozzá a (`the man from la mancha` explicit listaként a {subject} entitáshoz a [explicit listához tartozó authoring API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)használatával.

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Egy sablon utterance (kifejezés) a nem kötelező szöveg megjelölésére szintaxis
Megadhatja a szöveg jelölését a kifejezésben a reguláris kifejezés szögletes zárójelének szintaxisa `[]`használatával. A nem kötelező szöveg ágyazhatja szögletes zárójelek között legfeljebb csak két zárójelek közé.

|Nem kötelező szöveg minta|Jelentés|
|--|--|
|`[find] email about {subject} [from {person}]`|a `find` és a `from {person}` nem kötelező|
|"Tud segíteni [?]|Az írásjel nem kötelező.|

A központozás (`?`, `!`, `.`) figyelmen kívül hagyható, és a mintázatok szögletes zárójelének szintaxisa alapján figyelmen kívül kell hagyni őket.

## <a name="next-steps"></a>Következő lépések

További információ a mintákról:

* [Mintázatok hozzáadása](luis-how-to-model-intent-pattern.md)
* [Minta hozzáadása bármely entitáshoz](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Minták – fogalmak](luis-concept-patterns.md)

Ismerje meg, hogy a. JSON válasz hogyan adja vissza a [véleményét](luis-reference-prebuilt-sentiment.md) .