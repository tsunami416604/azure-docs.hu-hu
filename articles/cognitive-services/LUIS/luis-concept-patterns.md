---
title: Minták Súgó előrejelzése – LUIS
titleSuffix: Azure Cognitive Services
description: Egy minta segítségével megjelölésű a nagyobb pontosság számos további kimondott szöveg megadása nélkül.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: acbcaa7e5588c0fecf2c20751e69442e1373cbb5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563982"
---
# <a name="patterns-improve-prediction-accuracy"></a>Minták előrejelzési pontosság növeléséhez.
Pontosság javításához, ha több utterances nagyon hasonló minták lettek kialakítva.  Egy minta segítségével megjelölésű a nagyobb pontosság számos további kimondott szöveg megadása nélkül. 

## <a name="patterns-solve-low-intent-confidence"></a>Minták szándék alacsony megbízhatósági megoldása
Fontolja meg egy emberi erőforrások alkalmazáson, amely a szervezeti diagram viszonyítva egy alkalmazott a jelentést. Az alkalmazottak és a kapcsolat, LUIS ad vissza, az alkalmazottak érint. Vegye fontolóra egy alkalmazott, Tom, az Alice nevű felettes nevét, és a megnevezett alárendeltek csoportjait: Michael, Rebecca és Carl.

![Szervezeti diagram képe](./media/luis-concept-patterns/org-chart.png)

|Beszédmódok|Előre jelzett leképezés|Leképezési pontszám|
|--|--|--|
|A Tom alárendelt ki?|GetOrgChart|.30|
|Tom beosztottja ki?|GetOrgChart|.30|

Ha egy alkalmazás közötti 10 és 20 mondat, különböző szórendjét és még más szavakat (szinonimák "alárendelt", "felügyelet", "jelentés") különböző hosszúságú kimondott szöveg, LUIS egy alacsony megbízhatósági pontszám adhatnak vissza. Hozzon létre egy mintát, amely segít megismerni a Word-sorrend fontosságát. 

Minták oldja meg a következő helyzetekben: 

* A cél pontszáma alacsony
* A megfelelő szándék nem a legfelső pontszám, hanem a legfelső pontszámhoz közeledik. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Minták amelyek nem garantálja az leképezés
Minták előrejelzési technológiák kombinációját használja. Egy leképezésének egy sablon utterance (kifejezés) a mintát, nem garantálja a szándék előrejelzési de erős védelem. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>A minták nem javítják a géppel megtanult entitások észlelését

A mintázat elsődleges célja, hogy segítse a szándékok és a szerepkörök előrejelzését. A minta. minden entitás a szabad formátumú entitások kinyerésére szolgál. Míg a minták entitásokat használnak, a minta nem segít felderíteni a géppel megtanult entitásokat.  

Ne várja meg a továbbfejlesztett entitások előrejelzését, ha több hosszúságú kimondott szöveg összecsuk egyetlen mintára. Az egyszerű entitások a hosszúságú kimondott szöveg hozzáadásához vagy a listához tartozó entitások használata más, mint a minta nem fog tüzet használni.

## <a name="patterns-use-entity-roles"></a>Minták entitás szerepkörök használata
Ha két vagy több entitások egy minta kontextusban kapcsolódó, mintákat használják az entitást [szerepkörök](luis-concept-roles.md) entitásokról környezeti információk kinyeréséhez.  

## <a name="prediction-scores-with-and-without-patterns"></a>Előrejelzési pontszámok rendelkező és anélküli minták
Adott elegendő példa utterances, LUIS elvileg látnia kell növelni az előrejelzés megbízhatósági minták nélkül. Minták növelheti a megbízhatósági pontszám annyi kimondott szöveg megadása nélkül.  

## <a name="pattern-matching"></a>Minták egyeztetése
A minta egyezik az entitások belül a mintát először észlelése, majd ellenőrzése a többi szavak és szórendjét, a minta alapján. Egyeztetendő mappaminta minta entitások szükséges. A minta a token szintjén, nem a karakter szint alkalmazzák. 

## <a name="pattern-syntax"></a>A minta-szintaxis
A minta szintaxisa az utterance (kifejezés) sablont. A sablon tartalmaznia kell a szavak és szavak és egyeztetni kívánt entitásokat és írásjelek figyelmen kívül kívánja. Ez **nem** reguláris kifejezést. 

Entitások minták a kapcsos zárójeleket veszi körül `{}`. Minták lehetnek, alá tartozó alanyokra és szerepkörökkel. [Minta. a bármely](luis-concept-entity-types.md#patternany-entity) entitás csak mintázatokban használható. 

A minta szintaxisa a következő szintaxist támogatja:

|Függvény|Szintaxis|Beágyazási szint|Példa|
|--|--|--|--|
|entitás| {}– kapcsos zárójelek|2|Hol van a (z) {Entity-Name} űrlap?|
|választható|[] – szögletes zárójelek<BR><BR>A választható és a csoportosítás bármely kombinációja esetében 3 korlát van |2|A kérdőjel nem kötelező [?]|
|Csoportosítás|() – zárójelek|2|(a \| b)|
|vagy| \|– függőleges sáv (cső)<br><br>A függőleges sávokon (vagy) legfeljebb 2 van egy csoportban |-|Hol van az űrlap ({Form-Name-Short &#x7c; } {Form-Name-Long &#x7c; } {Form-Number})| 
|a Kimondás kezdete és/vagy vége|^-kalap|-|^ a Kimondás kezdete<br>a Kimondás befejeződött ^<br>^ Strict teljes körű megfeleltetés a ({Number}} entitással) ^|

## <a name="nesting-syntax-in-patterns"></a>Szintaxisok beágyazásának mintázata

A szögletes zárójelek **választható** szintaxisa két szint beágyazására használható. Például: `[[this]is] a new form`. Ez a példa a következő hosszúságú kimondott szöveg teszi lehetővé: 

|Beágyazott opcionális Kimondás – példa|Magyarázat|
|--|--|
|Ez egy új űrlap|a minta összes szavának felel meg|
|új űrlap|megfelel a külső opcionális és nem választható szavaknak a mintában|
|új űrlap|csak a szükséges szavakra illeszkedik|

A  zárójelekkel rendelkező csoportosítási szintaxis két szinten ágyazható be. Például: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Ez a funkció lehetővé teszi a három entitás összeegyeztetését. 

Ha a Entity1 olyan hely, ahol a szerepkörök, például a Origin (Seattle) és a Destination (Kairó) és a 2. entitás egy ismert épület neve a List entitásból (RedWest-C), a következő hosszúságú kimondott szöveg az alábbi mintát képezi le:

|Beágyazott csoportosítás – példa|Magyarázat|
|--|--|
|RedWest-C|megfelel a külső csoportosítási entitásnak|
|Seattle|megfelel a belső csoportosítási entitások egyikének.|
|Kairó|megfelel a belső csoportosítási entitások egyikének.|

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
Entitás hozzáadása a minta-sablonba, körülvevő az entitás neve a kapcsos zárójelek között, mint például `Who does {Employee} manage?`. 

|Az entitás minta|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Az entitásokhoz és a szerepkör hozzáadása egy minta sablon szintaxisát
Egy entitás szerepkör jelölése `{entity:role}` kettősponttal, akkor a szerepkör neve követ entitás nevét. A szerepkörrel rendelkező entitás hozzáadása a minta-sablonba, körülvevő az entitás neve és a szerepkör nevét a kapcsos zárójelek között, mint például `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Az entitás szerepkörök minta|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Egy pattern.any hozzáadása minta sablon szintaxisát
A Pattern.any entitás lehetővé teszi, hogy a minta egy entitás különböző hosszúságú. Mindaddig, amíg a minta sablon követik, a pattern.any lehet bármely olyan. 

Hozzáadása egy **Pattern.any** a minta sablonba entitás körülvevő Pattern.any egyed kapcsos zárójeleket kell használni, mint például `How much does {Booktitle} cost and what format is it available in?`.  

|Az entitás Pattern.any minta|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|A mintában a könyv címek|
|--|
|Mennyibe kerül **ellopni a könyv** költség, és milyen formátumú érhető el?|
|Mennyibe kerül **kérje meg** költség, és milyen formátumú érhető el?|
|Mennyibe kerül **a inspirációkkal incidens a kutya az éjszakai a** költség, és milyen formátumú érhető el?| 

A könyv címének szavai nem zavaróak a LUIS számára, mert LUIS tudja, hol fejeződik be a könyv címe, a minta. bármely entitás alapján.

## <a name="explicit-lists"></a>Explicit listák

hozzon létre egy [explicit listát](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) a szerzői API-n keresztül, hogy a kivételt a következő esetekben engedélyezze:

* A minta egy [mintát tartalmaz. bármilyen](luis-concept-entity-types.md#patternany-entity)
* A minta szintaxisa pedig lehetővé teszi a helytelen entitások kinyerésének lehetőségét a Kimondás alapján. 

Tegyük fel például, mind a nem kötelező szintaxist tartalmazó mintát rendelkezik `[]`, és az entitás `{}`, kombinált úgy, hogy helytelen adatokat nyerhet ki.

Fontolja meg a minta "[keresése] e-mail kapcsolatos {subject} [{személy}]".

A következő megcímkézzen a **tulajdonos** és **személy** entitás ki kell olvasni, megfelelő és nem megfelelően:

|Kimondott szöveg|Entitás|Megfelelő kivonása|
|--|--|:--:|
|a Chris kutyák kapcsolatos e-mail|tulajdonos kutyák =<br>személy = Chris|✔|
|a férfi a La Mancha kapcsolatos e-mailben|tulajdonos férfi =<br>személy La Mancha =|X|

Az előző táblázatban a tulajdonosnak (könyv címe `the man from La Mancha` ) kell lennie, de mivel a tulajdonos a választható szót `from`is tartalmazza, a cím helytelenül van jelezve. 

Javítsa ki a kivételt a minta, adjon hozzá `the man from la mancha` egy explicit lista egyezik meg a {subject} entitás használatával, a [API szerzői explicit listát](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Egy sablon utterance (kifejezés) a nem kötelező szöveg megjelölésére szintaxis
A reguláris kifejezés szögletes zárójel szintaxis használatával az utterance (kifejezés) a nem kötelező szöveg `[]`. A nem kötelező szöveg ágyazhatja szögletes zárójelek között legfeljebb csak két zárójelek közé.

|Nem kötelező szöveg minta|Jelentés|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`és `from {person}` nem kötelező|
|"Tud segíteni [?]|Az írásjel nem kötelező.|

Az írásjeleket (`?`, `!`, `.`) figyelmen kívül kell hagyni, és a mintázat szögletes zárójelének szintaxisa alapján figyelmen kívül kell hagyni őket. 

## <a name="pattern-only-apps"></a>Csak minta alkalmazások
Olyan alkalmazásokat hozhat létre, amelyek nem rendelkeznek példa hosszúságú kimondott szöveg, feltéve, hogy az egyes szándékok esetében minta van. Csak minta alkalmazás esetében a minta nem tartalmazhat géppel megtanult entitásokat, mert ezek a hosszúságú kimondott szöveg példaként szükségesek. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ebben az oktatóanyagban minták megvalósítása](luis-tutorial-pattern.md)
