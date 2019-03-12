---
title: Minták segítségével előrejelzési
titleSuffix: Language Understanding - Azure Cognitive Services
description: Egy minta segítségével megjelölésű a nagyobb pontosság számos további kimondott szöveg megadása nélkül.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: diberry
ms.openlocfilehash: 42ac75b6ed0d4489ccae014b9cfe3b08269c1218
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547418"
---
# <a name="patterns-improve-prediction-accuracy"></a>Minták előrejelzési pontosság növeléséhez.
Pontosság javításához, ha több utterances nagyon hasonló minták lettek kialakítva.  Egy minta segítségével megjelölésű a nagyobb pontosság számos további kimondott szöveg megadása nélkül. 

## <a name="patterns-solve-low-intent-confidence"></a>Minták szándék alacsony megbízhatósági megoldása
Fontolja meg egy emberi erőforrások alkalmazáson, amely a szervezeti diagram viszonyítva egy alkalmazott a jelentést. Az alkalmazottak és a kapcsolat, LUIS ad vissza, az alkalmazottak érint. Egy alkalmazott felettesétől az alkalmazott, a Tom, vegye figyelembe, Alice nevét, és a beosztottak nevű csapat: Michael Rebecca és Carl.

![Szervezeti diagram képe](./media/luis-concept-patterns/org-chart.png)

|Beszédmódok|Előre jelzett leképezés|Leképezési pontszám|
|--|--|--|
|A Tom alárendelt ki?|GetOrgChart|.30|
|Tom beosztottja ki?|GetOrgChart|.30|

Ha egy alkalmazás közötti 10 és 20 mondat, különböző szórendjét és még más szavakat (szinonimák "alárendelt", "felügyelet", "jelentés") különböző hosszúságú kimondott szöveg, LUIS egy alacsony megbízhatósági pontszám adhatnak vissza. Hozzon létre egy minta segítségével megismerheti a szórendjét fontossága LUIS. 

Minták oldja meg a következő helyzetekben: 

* A leképezési pontszám, alacsony
* A megfelelő leképezés nem a felső pontszám, de túl zárja be a felső pontszámot. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Minták amelyek nem garantálja az leképezés
Minták előrejelzési technológiák kombinációját használja. Egy leképezésének egy sablon utterance (kifejezés) a mintát, nem garantálja a szándék előrejelzési de erős védelem. 

<a name="patterns-do-not-improve-entity-detection"/>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>Minták növelhető az entitás gép megtanult észlelése

A minta elsősorban az adott leképezések és szerepkörök előrejelzését segítségével. Szabad formátumú entitások kinyeréséhez a pattern.any entitás szolgál. Minták entitásokat használ, miközben a minta nem észlelhetők a gép megtanult entitás.  

Továbbfejlesztett entitás előrejelzési megtekintéséhez be egyetlen mintát több utterances összecsukása után nem számít. Egyszerű entitások üzenetszám meg kell beszédmódok hozzáadása, vagy használjon lista entitásokat más a minta nem indulnak el.

## <a name="patterns-use-entity-roles"></a>Minták entitás szerepkörök használata
Ha két vagy több entitások egy minta kontextusban kapcsolódó, mintákat használják az entitást [szerepkörök](luis-concept-roles.md) entitásokról környezeti információk kinyeréséhez. Ez a hierarchikus gyermekek egyenértékűek, de **csak** minták érhető el. 

## <a name="prediction-scores-with-and-without-patterns"></a>Előrejelzési pontszámok rendelkező és anélküli minták
Adott elegendő példa utterances, LUIS elvileg látnia kell növelni az előrejelzés megbízhatósági minták nélkül. Minták növelheti a megbízhatósági pontszám annyi kimondott szöveg megadása nélkül.  

## <a name="pattern-matching"></a>Minták egyeztetése
A minta egyezik az entitások belül a mintát először észlelése, majd ellenőrzése a többi szavak és szórendjét, a minta alapján. Egyeztetendő mappaminta minta entitások szükséges. A minta a token szintjén, nem a karakter szint alkalmazzák. 

## <a name="pattern-syntax"></a>A minta-szintaxis
A minta szintaxisa az utterance (kifejezés) sablont. A sablon tartalmaznia kell a szavak és szavak és egyeztetni kívánt entitásokat és írásjelek figyelmen kívül kívánja. Ez **nem** reguláris kifejezést. 

Entitások minták a kapcsos zárójeleket veszi körül `{}`. Minták lehetnek, alá tartozó alanyokra és szerepkörökkel. [Pattern.any](luis-concept-entity-types.md#patternany-entity) csak használatban minták entitás. 

A minta szintaxis támogatja a következő szintaxist:

|Függvény|Szintaxis|A beágyazási szint|Példa|
|--|--|--|--|
|entitás| {} -kapcsos zárójelek|2|Hol található az űrlap {entitás neve üzemeltetett szolgáltatás}?|
|választható|[] - szögletes zárójelek<BR><BR>A 3-ból a beágyazási szinttel, tetszőleges választható és csoportosítás kombinációját korlátja |2|A kérdőjel [?]|
|A csoportosítás|() - zárójelek|2|rendszer (egy \| b).|
|vagy| \| – függőleges vonal (függőleges)<br><br>2. a függőleges vonal (vagy) több csoport korlátja |-|Hol található az űrlap ({űrlap neve, short} &#x7c; {űrlap-name-hosszú} &#x7c; {űrlap, number})| 
|kezdő és/vagy az utterance (kifejezés) vége|^-jel|-|^ megkezdése az utterance (kifejezés)<br>az utterance (kifejezés) történik ^<br>^ {number} entitással teljes utterance (kifejezés) szigorú szövegkonstans egyeznie ^|

## <a name="nesting-syntax-in-patterns"></a>A beágyazási szintaxis a minták

A **választható** szintaxist, a szögletes zárójelek között, beágyazott két szinten is lehet. Például: `[[this]is] a new form`. Ebben a példában a következő megcímkézzen számára lehetővé teszi: 

|Beágyazott választható utterance (kifejezés) Példa|Magyarázat|
|--|--|
|Ez az új űrlap|megfelel a mintában minden szót|
|Új űrlap|külső nem kötelező a word és minta – nem kötelező szavak megfelelő|
|Új űrlap|csak a szükséges egyezések szavakat|

A **csoportosítási** szintaxis, és zárójelek között, beágyazott két szinten is lehet. Például: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Ez lehetővé teszi bármely illeszkednie kell a három entitásokat. 

Ha Entity1 szerepköröket, például a (Budapesten) forrás és cél (Cairo) egy olyan helyre, és entitás 2 lista entitásból (RedWest-C) building ismert nevét, a következő kimondott szöveg ezt a mintát lenne leképezése:

|Beágyazott csoportosítási utterance (kifejezés) Példa|Magyarázat|
|--|--|
|RedWest-C|megegyezik a külső csoportosítási entitás|
|Seattle|a belső csoportosítási entitások szerepel|
|Kairó|a belső csoportosítási entitások szerepel|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>A választható szintaxissal csoportok beágyazási korlátai

Kombinációját **csoportosítási** a **választható** szintaxis legfeljebb 3 beágyazási szinttel rendelkezik.

|Engedélyezve|Példa|
|--|--|
|Igen|([(Teszt1 &#x7c; Teszt2)] &#x7c; Teszt3)|
|Nem|([([Teszt1] &#x7c; Teszt2)] &#x7c; Teszt3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Csoportok vagy-ing szintaxissal beágyazási korlátai

Kombinációját **csoportosítási** a **vagy-ing** szintaxis 2 függőleges sávok határral rendelkezik.

|Engedélyezve|Példa|
|--|--|
|Igen|(Teszt1 &#x7c; Teszt2 &#x7c; (Teszt3 &#x7c; test4))|
|Nem|(Teszt1 &#x7c; Teszt2 &#x7c; Teszt3 &#x7c; (test4 &#x7c; test5)) |

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

A könyv cím szavak nem állnak zavaró, LUIS, LUIS tudni fogja, ahol a könyv cím ér véget, mert a Pattern.any entitás alapján.

## <a name="explicit-lists"></a>Explicit listák

Hozzon létre egy [Explicit lista](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) a szerzői műveletekhez részben API-val, hogy a kivétel során:

* A minta tartalmaz egy [Pattern.any](luis-concept-entity-types.md#patternany-entity)
* És a minta-szintaxis lehetővé teszi egy helytelen entitások kinyeréséhez alapján az utterance (kifejezés) lehetőségét. 

Tegyük fel például, mind a nem kötelező szintaxist tartalmazó mintát rendelkezik `[]`, és az entitás `{}`, kombinált úgy, hogy helytelen adatokat nyerhet ki.

Fontolja meg a minta "[keresése] e-mail kapcsolatos {subject} [{személy}]".

A következő megcímkézzen a **tulajdonos** és **személy** entitás ki kell olvasni, megfelelő és nem megfelelően:

|Kimondott szöveg|Entitás|Megfelelő kivonása|
|--|--|:--:|
|a Chris kutyák kapcsolatos e-mail|tulajdonos kutyák =<br>személy = Chris|✔|
|a férfi a La Mancha kapcsolatos e-mailben|tulajdonos férfi =<br>személy La Mancha =|X|

Az előző táblázatban a tulajdonosának kell lennie `the man from La Mancha` (a könyv címét), de a tulajdonos tartalmazza a nem kötelező szó `from`, a cím helytelenül összegyűjtése várható. 

Javítsa ki a kivételt a minta, adjon hozzá `the man from la mancha` egy explicit lista egyezik meg a {subject} entitás használatával, a [API szerzői explicit listát](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Egy sablon utterance (kifejezés) a nem kötelező szöveg megjelölésére szintaxis
A reguláris kifejezés szögletes zárójel szintaxis használatával az utterance (kifejezés) a nem kötelező szöveg `[]`. A nem kötelező szöveg ágyazhatja szögletes zárójelek között legfeljebb csak két zárójelek közé.

|Nem kötelező szöveg minta|Jelentés|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` és `from {person}` megadása nem kötelező|
|"Lehet, segítséget kérek a [?]|Nem kötelező, az írásjelek|

Írásjelek (`?`, `!`, `.`) figyelmen kívül lesz hagyva, és ezek a minták a szögletes zárójelet szintaxis használatával figyelmen kívül kell. 

## <a name="pattern-only-apps"></a>Csak a minta alkalmazások
Mindaddig, amíg az egyes szándékot utaló mintázat hozhat létre egy alkalmazást, amelyek nincsenek példa utterances szándék fog vonatkozni. Csak a minta alkalmazások esetén a minta nem tartalmazhat gép megismert entitások, mert az ilyen esetekben példa kimondott szöveg. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ebben az oktatóanyagban minták megvalósítása](luis-tutorial-pattern.md)
