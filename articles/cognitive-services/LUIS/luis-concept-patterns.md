---
title: Ismerje meg, hogyan a minták előrejelzési pontosság növeléséhez
titleSuffix: Azure Cognitive Services
description: Pontosság javításához, ha több utterances nagyon hasonló minták lettek kialakítva. Egy minta segítségével megjelölésű a nagyobb pontosság számos további kimondott szöveg megadása nélkül.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 5ade15b3f80d725af4ece31a36ea0b670f5f5147
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031543"
---
# <a name="patterns-improve-prediction-accuracy"></a>Minták előrejelzési pontosság növeléséhez.
Pontosság javításához, ha több utterances nagyon hasonló minták lettek kialakítva.  Egy minta segítségével megjelölésű a nagyobb pontosság számos további kimondott szöveg megadása nélkül. 

## <a name="patterns-solve-low-intent-confidence"></a>Minták szándék alacsony megbízhatósági megoldása
Fontolja meg egy emberi erőforrások alkalmazáson, amely a szervezeti diagram viszonyítva egy alkalmazott a jelentést. Az alkalmazottak és a kapcsolat, LUIS ad vissza, az alkalmazottak érint. Fontolja meg egy alkalmazott Tom, egy alkalmazott felettesétől az Alice nevét, és a beosztottak nevű csapat: Michael Rebecca és Carl.

![Szervezeti diagram képe](./media/luis-concept-patterns/org-chart.png)

|Beszédmódok|Előre jelzett leképezés|Leképezési pontszám|
|--|--|--|
|A Tom alárendelt ki?|GetOrgChart|.30|
|Tom beosztottja ki?|GetOrgChart|.30|

Ha egy alkalmazás közötti 10 és 20 mondat, különböző szórendjét és még más szavakat (szinonimák "alárendelt", "felügyelet", "jelentés") különböző hosszúságú kimondott szöveg, LUIS egy alacsony megbízhatósági pontszám adhatnak vissza. Annak érdekében, hogy tisztában a szórendjét fontosságával LUIS érdekében hozzon létre egy minta. 

Minták oldja meg a következő helyzetekben: 

* Ha a szándék pontszám, alacsony
* Ha a megfelelő leképezés nem a felső pontszám, de túl zárja be a felső pontszámot. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Minták amelyek nem garantálja az leképezés
Minták előrejelzési technológiák kombinációját használja. Egy leképezésének egy sablon utterance (kifejezés) a mintát, nem garantálja a szándék előrejelzési de erős védelem. 

## <a name="patterns-do-not-improve-entity-detection"></a>Minták növelhető az entitás észlelése
Minták entitások használatához, a minta nem észlelhetők az entitás. Egy minta csak célja, hogy az előrejelzés leképezések és szerepkörök segítségével.  

## <a name="patterns-use-entity-roles"></a>Minták entitás szerepkörök használata
Ha két vagy több entitások egy minta kontextusban kapcsolódó, mintákat használják az entitást [szerepkörök](luis-concept-roles.md) entitásokról környezeti információk kinyeréséhez. Ez a hierarchikus gyermekek egyenértékűek, de **csak** minták érhető el. 

## <a name="prediction-scores-with-and-without-patterns"></a>Előrejelzési pontszámok rendelkező és anélküli minták
Adott elegendő példa utterances, LUIS elvileg látnia kell növelni az előrejelzés megbízhatósági minták nélkül. Minták növelheti a megbízhatósági pontszám annyi kimondott szöveg megadása nélkül.  

## <a name="pattern-matching"></a>Minták egyeztetése
A minta egyezik az entitások belül a mintát először észlelése, majd ellenőrzése a többi szavak és szórendjét, a minta alapján. Egyeztetendő mappaminta minta entitások szükséges. 

## <a name="pattern-syntax"></a>A minta-szintaxis
A minta szintaxisa az utterance (kifejezés) sablont. A sablon tartalmaznia kell a szavak és szavak és egyeztetni kívánt entitásokat és írásjelek figyelmen kívül kívánja. Ez **nem** reguláris kifejezést. 

Entitások minták a kapcsos zárójeleket veszi körül `{}`. Minták lehetnek, alá tartozó alanyokra és szerepkörökkel. Pattern.any csak használatban minták entitás. A szintaxis a következő szakaszok ismertetése.

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Entitás hozzáadása egy minta sablon szintaxisát
Entitás hozzáadása a minta-sablonba, körülvevő az entitás neve a kapcsos zárójelek között, mint például `Who does {Employee} manage?`. 

|Az entitás minta|
|--|
|`Who does {Employee} manage?`|

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Az entitásokhoz és a szerepkör hozzáadása egy minta sablon szintaxisát
Egy entitás szerepkör jelölése `{entity:role}` kettősponttal, akkor a szerepkör neve követ entitás nevét. A szerepkörrel rendelkező entitás hozzáadása a minta-sablonba, körülvevő az entitás neve és a szerepkör nevét a kapcsos zárójelek között, mint például `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Az entitás szerepkörök minta|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Egy pattern.any hozzáadása minta sablon szintaxisát
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

Könyv cím példákban szereplő a környezetfüggő szavakat a könyv cím nem, a LUIS kikapcsolnia. A LUIS tudja, ahol a könyv cím ér véget, mert azt a mintát, és Pattern.any entitáshoz.

### <a name="explicit-lists"></a>Explicit listák
Ha a minta egy Pattern.any tartalmaz, és a minta-szintaxis lehetővé teszi a lehetőségét az utterance (kifejezés) alapján egy helytelen entitások kinyeréséhez, hozzon létre egy [Explicit lista](https://aka.ms/ExplicitList) , hogy a kivételt a szerzői műveletekhez részben API-n keresztül. 

Tegyük fel például, mind a nem kötelező szintaxist tartalmazó mintát rendelkezik `[]`, és az entitás `{}`, kombinált úgy, hogy helytelen adatokat nyerhet ki.

Fontolja meg a minta "[keresése] e-mail kapcsolatos {subject} [{személy}]". A következő megcímkézzen a **tulajdonos** és **személy** entitás ki kell olvasni, megfelelő és nem megfelelően:

|Kimondott szöveg|Entitás|Megfelelő kivonása|
|--|--|:--:|
|a Chris kutyák kapcsolatos e-mail|tulajdonos kutyák =<br>személy = Chris|✔|
|a férfi a La Mancha kapcsolatos e-mailben|tulajdonos férfi =<br>személy La Mancha =|X|

Az előző táblázatban, az utterance (kifejezés) `email about the man from La Mancha`, a tulajdonosának kell lennie `the man from La Mancha` (a könyv címét), de a tulajdonos tartalmazza a nem kötelező szó `from`, a cím helytelenül összegyűjtése várható. 

Javítsa ki a kivételt a minta, adjon hozzá `the man from la mancha` egy explicit lista egyezik meg a {subject} entitás használatával, a [API szerzői explicit listát](https://aka.ms/ExplicitList).

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Egy sablon utterance (kifejezés) a nem kötelező szöveg megjelölésére szintaxis
A reguláris kifejezés szögletes zárójel szintaxis használatával az utterance (kifejezés) a nem kötelező szöveg `[]`. A nem kötelező szöveg ágyazhatja szögletes zárójelek között legfeljebb csak két zárójelek közé.

|Nem kötelező szöveg minta|
|--|
|`[find] email about {subject} [from {person}]`|

Például írásjelek `.`, `!`, és `?` figyelmen kívül hagyhatja a szögletes zárójelek segítségével. Annak érdekében, hogy figyelmen kívül hagyja ezeket a jeleket, minden be van jelölve egy külön mintának kell lennie. A választható szintaxist jelenleg nem támogatja a rendszer figyelmen kívül hagyja a több elem a lista egy elemének.

## <a name="patterns-only"></a>Csak a minták
LUIS lehetővé teszi, hogy egy alkalmazás például megszólalásokat nélkül szándékot. A használati engedélyezett csak akkor, ha a mintákat használják. Minták legalább egy entitás minden egyes mintában van szükség. Csak a minta alkalmazások esetén a mintázat nem tartalmazhat gép megismert entitások, mert az ilyen esetekben példa kimondott szöveg. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ebben az oktatóanyagban minták megvalósítása](luis-tutorial-pattern.md)