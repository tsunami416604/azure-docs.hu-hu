---
title: Mintaszintaxis hivatkozása - LUIS
titleSuffix: Azure Cognitive Services
description: Entitások létrehozása a kulcsadatok kinyerése a felhasználói utterances a language understanding (LUIS) alkalmazásokban. A kinyert adatokat az ügyfélalkalmazás használja.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 696f4bdc22bed01a4b5be8bff63ade482a8dbe0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219730"
---
# <a name="pattern-syntax"></a>Minta szintaxisa

Minta szintaxis egy sablon egy utterance (kifejezés) . A sablonnak tartalmaznia kell az egyeztetni kívánt szavakat és entitásokat, valamint a figyelmen kívül hagyni kívánt szavakat és írásjeleket. Ez **nem** egy reguláris kifejezés.

> [!CAUTION]
> A minták csak a gép által megtanult entitás szülőket tartalmazzák, az alösszetevőket nem.

A mintákban lévő entitásokat göndör `{}`zárójelek veszik körül, . A minták tartalmazhatnak entitásokat és szerepkörökkel rendelkező entitásokat. [A pattern.any](luis-concept-entity-types.md#patternany-entity) olyan entitás, amelyet csak mintákban használnak.

A mintaszintaxis a következő szintaxist támogatja:

|Függvény|Szintaxis|Egymásba ágyazási szint|Példa|
|--|--|--|--|
|Entitás| {}- göndör zárójelek|2|Hol található a(z) {entity-name} űrlap?|
|választható|[] - szögletes zárójelek<BR><BR>Az opcionális és a csoportosítási szintek bármilyen kombinációjának legfeljebb 3 van korlátozva. |2|A kérdőjel nem kötelező [?]|
|Csoportosítás|() - zárójelek|2|(a \| b)|
|vagy| \|- függőleges rúd (cső)<br><br>Van egy 2-es korlát a függőleges sávokon (Vagy) egy csoportban |-|Hol van az űrlap ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number})|
|utterance (kifejezés) kezdete és/vagy vége|↑ - a kéthelyezett|-|^kezdődik az utterance (kifejezés)<br>az utterance (kifejezés) történik^<br>^A teljes utterance (z) {number} entitással való teljes szövegsor szerinti szigorú egyezése^|

## <a name="nesting-syntax-in-patterns"></a>Szintaxis beágyazása a mintákban

Az **opcionális** szintaxis szögletes zárójelekkel két szint beágyazható. Például: `[[this]is] a new form`. Ez a példa lehetővé teszi a következő utterances:

|Beágyazott, nem kötelező utterance (kifejezés) példa|Magyarázat|
|--|--|
|ez egy új űrlap|megegyezik a minta összes szójával|
|egy új űrlap|megegyezik a külső választható szavakkal és a nem választható szavakkal a mintában|
|új űrlap|csak a szükséges szavaknak felel meg|

A zárójeles **csoportosítási** szintaxis két szintbe ágyazható. Például: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Ez a funkció lehetővé teszi, hogy a három entitás bármelyike egyeztethető legyen.

Ha az Entity1 olyan hely, amelynek szerepkörei például az origó (Seattle) és a cél (Kairó) és az Entity 2 egy ismert épületnév egy listaentitásból (RedWest-C), a következő kimondott szövegek erre a mintára vannak leképezve:

|Példa beágyazott csoportosítási utterance|Magyarázat|
|--|--|
|RedWest-C között|megfelel a külső csoportosításentitásnak|
|Seattle|megfelel az egyik belső csoportosítási entitásnak|
|Cairo|megfelel az egyik belső csoportosítási entitásnak|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Beágyazási korlátok választható szintaxissal rendelkező csoportokhoz

A **csoportosítás** és a **választható** szintaxis kombinációja legfeljebb 3 beágyazási szint.

|Engedélyezve|Példa|
|--|--|
|Igen|( [ ( 1. &#x7c; &#x7c;-vizsgálat ) ] &#x7c; 3)|
|Nem|( [ [ [ test1 ] &#x7c; a &#x7c;) ] &#x7c; a 3.|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Beágyazási korlátok csoportokhoz vagy szintaxissal rendelkező csoportokhoz

A **vagy-ing** szintaxissal **történő csoportosítás** kombinációja legfeljebb 2 függőleges sáv.

|Engedélyezve|Példa|
|--|--|
|Igen|( 1. &#x7c; vizsgálat2 &#x7c; ( 3 &#x7c; 4. teszt )|
|Nem|( &#x7c; vizsgálat2 &#x7c; vizsgálat3 &#x7c; ( 4 &#x7c; 5. teszt ) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Entitás hozzáadása mintasablonhoz
Ha entitást szeretne hozzáadni a mintasablonhoz, vegye körül `Who does {Employee} manage?`az entitás nevét kapcsos zárójelekkel, például .

|Minta entitással|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Entitás és szerepkör hozzáadása mintasablonhoz
Az entitásszerepkört az `{entity:role}` entitás nevével, majd egy kettősponttal, majd a szerepkör nevével jelöli a jelölés. Ha szerepkörrel rendelkező entitást szeretne hozzáadni a mintasablonhoz, vegye körül az `Book a ticket from {Location:Origin} to {Location:Destination}`entitás nevét és szerepkörnevét kapcsos zárójelekkel, például .

|Minta entitásszerepkörökkel|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Szintaxis a minta.any hozzáadása mintasablonhoz
A Pattern.any entitás lehetővé teszi, hogy egy entitás változó hosszúságú a minta. Mindaddig, amíg a minta sablon követi, a pattern.any lehet bármilyen hosszúságú.

Ha **egy Pattern.any** entitást szeretne hozzáadni a mintasablonhoz, vegye körül `How much does {Booktitle} cost and what format is it available in?`a Pattern.any entitást a kapcsos zárójelekkel, például .

|Minta minta minta.any entitás|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Könyvcímek a mintában|
|--|
|Mennyibe kerül **ellopni ezt a könyvet,** és milyen formátumban áll rendelkezésre?|
|Mennyit **kér a** költség, és milyen formátumban érhető el?|
|Mennyibe kerül **a The Curious Incident of the Dog in the Night-Time** költség, és milyen formátumban érhető el?|

A könyv címének szavai nem zavaróak a LUIS számára, mert a LUIS tudja, hol végződik a könyv címe a Pattern.any entitás alapján.

## <a name="explicit-lists"></a>Explicit listák

explicit [listát](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) hozhat létre a szerzői API-n keresztül, hogy engedélyezze a kivételt, ha:

* A minta tartalmaz egy [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* És ez a minta szintaxis lehetővé teszi a helytelen entitás kinyerésének lehetőségét az utterance (kifejezés) alapján.

Tegyük fel például, hogy van egy `[]`minta, amely `{}`egyaránt tartalmaz választható szintaxist, a , és az entitás szintaxisát, kombinálva az adatok helytelen kinyerésének módját.

Tekintsük a "[find] e-mailt a következőről: {subject} [from {person}]".

A következő kimondott szövegben a **tárgy** és a **személy** entitás kibontása helyesen és helytelenül:

|Kimondott szöveg|Entitás|Helyes extrakció|
|--|--|:--:|
|e-mail a kutyák Chris|subject=kutyák<br>személy=Krisztián|✔|
|e-mail a férfi La Mancha|tárgy = az ember<br>személy=La Mancha|X|

Az előző táblázatban a `the man from La Mancha` tárgynak (könyvcím) kell lennie, `from`de mivel a téma tartalmazza a választható szót, a cím helytelenül van előre jelezve.

Ha ki szeretné javítani ezt `the man from la mancha` a kivételt a mintához, adja hozzá explicit listaegyezésként a(z) {subject} entitást a [szerzői API-val explicit listához.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Szintaxis a választható szöveg megjelöléséhez a sablon utterance
Jelöld meg a nem kötelező szöveget az `[]`utterance (kifejezés) kifejezés ben szögszög szintaxisával, . A választható szöveg legfeljebb két zárójelbe ágyazható.

|Minta választható szöveggel|Jelentés|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`és `from {person}` nem kötelező|
|"Tud nekem segíteni[?]|Az írásjel nem kötelező|

Az írásjeleket`?` `!`( `.`, , ) figyelmen kívül kell hagyni, és figyelmen kívül kell hagynia őket a négyzetes zárójel szintaxisával a mintákban.

## <a name="next-steps"></a>További lépések

További információ a mintákról:

* [Mintázatok hozzáadása](luis-how-to-model-intent-pattern.md)
* [A pattern.any entitás hozzáadása](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Minták fogalmak](luis-concept-patterns.md)

Ismerje meg, hogyan adja vissza a [hangulatot](luis-reference-prebuilt-sentiment.md) a .json válasz.