---
title: Minták Súgó előrejelzése – LUIS
titleSuffix: Azure Cognitive Services
description: A mintákkal magasabb szintű pontosság érhető el egy beszédelemnél anélkül, hogy sok további beszédelemre lenne szükség.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 4ca3a27a63f84eccb66b24d5046e2ae7d751387d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487547"
---
# <a name="patterns-improve-prediction-accuracy"></a>A minták az előrejelzési pontosságot javítják
A minták úgy vannak kialakítva, hogy javítsák a pontosságot, ha több hosszúságú kimondott szöveg nagyon hasonló.  A mintákkal magasabb szintű pontosság érhető el egy beszédelemnél anélkül, hogy sok további beszédelemre lenne szükség. 

## <a name="patterns-solve-low-intent-confidence"></a>Az alacsony szándékú megbízhatósági viszonyok megoldása
Vegyünk egy olyan emberi erőforrás-alkalmazást, amely jelentést készít a szervezeti diagramon egy alkalmazotthoz képest. Az alkalmazott neve és kapcsolata miatt LUIS a résztvevő alkalmazottakat adja vissza. Vegye fontolóra egy alkalmazott, Tom, az Alice nevű felettes nevét, és a neve: Michael, Rebecca és Carl.

![Szervezeti diagram képe](./media/luis-concept-patterns/org-chart.png)

|Beszédmódok|Előre jelzett szándék|Szándék pontszáma|
|--|--|--|
|Ki Tom alárendelt?|GetOrgChart|.30|
|Ki a Tom alárendeltje?|GetOrgChart|.30|

Ha egy alkalmazás 10 – 20 hosszúságú kimondott szöveg tartalmaz, különböző hosszúságú mondattal, különböző Word-sorrendtel és akár más szóval ("alárendelt", "Manage", "jelentés"), akkor a LUIS alacsony megbízhatósági pontszámot adhat vissza. Hozzon létre egy mintát, amely segít megismerni a Word-sorrend fontosságát. 

A minták a következő helyzeteket oldják meg: 

* A cél pontszáma alacsony
* A megfelelő szándék nem a legfelső pontszám, hanem a legfelső pontszámhoz közeledik. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>A minták nem garantálják a szándékot
A minták előrejelzési technológiák kombinációját használják. A sablonhoz való Kimondás szándékának beállítása a mintázatban nem garantálja a szándék-előrejelzést, de ez egy erős jel. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>A minták nem javítják a géppel megtanult entitások észlelését

A mintázat elsődleges célja, hogy segítse a szándékok és a szerepkörök előrejelzését. A minta. minden entitás a szabad formátumú entitások kinyerésére szolgál. Míg a minták entitásokat használnak, a minta nem segít felderíteni a géppel megtanult entitásokat.  

Ne várja meg a továbbfejlesztett entitások előrejelzését, ha több hosszúságú kimondott szöveg összecsuk egyetlen mintára. Az egyszerű entitások a hosszúságú kimondott szöveg hozzáadásához vagy a listához tartozó entitások használata más, mint a minta nem fog tüzet használni.

## <a name="patterns-use-entity-roles"></a>A minták entitás-szerepköröket használnak
Ha a mintában két vagy több entitás kontextussal kapcsolatos kapcsolatban áll, a minták az entitások környezetfüggő információinak kinyeréséhez használják az entitás- [szerepköröket](luis-concept-roles.md) .  

## <a name="prediction-scores-with-and-without-patterns"></a>Előrejelzési pontszámok és mintázatok nélkül
Mivel a hosszúságú kimondott szöveg-hez elég példa, a LUIS a minták nélkül is növelheti az előrejelzési megbízhatóságot. A minták megnövelik a megbízhatósági pontszámot anélkül, hogy annyi hosszúságú kimondott szöveg kellene biztosítania.  

## <a name="pattern-matching"></a>Minta egyeztetése
A rendszer a mintázatot először a mintában lévő entitások észlelése alapján rendezi, majd ellenőrzi a minta többi szövegét és sorrendjét. Az entitásokat a minta alapján kell megfeleltetni. A minta a token szintjén, nem pedig a karakter szintjén lesz alkalmazva. 

## <a name="pattern-syntax"></a>Minta szintaxisa
A minta szintaxisa egy kiírás sablonja. A sablonnak tartalmaznia kell azokat a szavakat és entitásokat, amelyeket szeretne egyeztetni, valamint a figyelmen kívül hagyni kívánt szavakat és írásjeleket. **Nem** reguláris kifejezés. 

A mintákban lévő entitások kapcsos zárójelek között találhatók, `{}`. A minták tartalmazhatnak entitásokat és szerepkörökkel rendelkező entitásokat. [Minta. a bármely](luis-concept-entity-types.md#patternany-entity) entitás csak mintázatokban használható. 

A minta szintaxisa a következő szintaxist támogatja:

|Függvény|Szintaxis|Beágyazási szint|Példa|
|--|--|--|--|
|Entitás| {} kapcsos zárójelek|2|Hol van a (z) {Entity-Name} űrlap?|
|Választható|[] – szögletes zárójelek<BR><BR>A választható és a csoportosítás bármely kombinációja esetében 3 korlát van |2|A kérdőjel nem kötelező [?]|
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
|RedWest – C|megfelel a külső csoportosítási entitásnak|
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

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Entitás hozzáadása egy minta sablonhoz
Ha entitást szeretne hozzáadni a mintázat sablonhoz, az entitás nevét kapcsos zárójelekkel kell megadnia, például `Who does {Employee} manage?`. 

|Minta entitással|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Az entitások és a szerepkörök sablonba való felvételének szintaxisa
Az entitások szerepköre `{entity:role}`ként van megjelölve az entitás nevével, majd egy kettősponttal, majd a szerepkör nevével. Ha szerepkörrel rendelkező entitást szeretne felvenni a minta sablonba, az entitás nevét és a szerepkör nevét kapcsos zárójelekkel kell megadnia, például `Book a ticket from {Location:Origin} to {Location:Destination}`. 

|Minta entitási szerepkörökkel|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>A minta hozzáadására szolgáló szintaxis. bármely minta sablon
A minta. bármely entitás lehetővé teszi, hogy egy változó hosszúságú entitást adjon hozzá a mintához. Ha a minta sablont követi, a mintázatot. bármilyen hosszúságú lehet. 

Egy **minta** hozzáadásához vegye fel a mintát a mintázat sablonba. bármely olyan entitást, amely kapcsos zárójelekkel rendelkezik, például `How much does {Booktitle} cost and what format is it available in?`.  

|Minta. bármely entitás|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Könyv címei a mintában|
|--|
|Mennyibe kerül a **könyv ellopása** , és milyen formátumban érhető el?|
|Mennyibe kerül a **Rákérdezés** és milyen formátumban érhető el?|
|Mennyibe kerül a **kutya furcsa incidense az éjszakai időszakban** , és milyen formátumban érhető el?| 

A könyv címének szavai nem zavaróak a LUIS számára, mert LUIS tudja, hol fejeződik be a könyv címe, a minta. bármely entitás alapján.

## <a name="explicit-lists"></a>Explicit felsorolások

hozzon létre egy [explicit listát](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) a szerzői API-n keresztül, hogy a kivételt a következő esetekben engedélyezze:

* A minta egy [mintát tartalmaz. bármilyen](luis-concept-entity-types.md#patternany-entity)
* A minta szintaxisa pedig lehetővé teszi a helytelen entitások kinyerésének lehetőségét a Kimondás alapján. 

Tegyük fel például, hogy rendelkezik egy olyan mintázattal, amely tartalmazza a választható szintaxist, a `[]`és az entitás szintaxisát, `{}`, kombinálva az adatok helytelen kinyerésének módját.

Vegye figyelembe a (z) [Find] e-mailt a (z) {subject} [{person}] címről.

A következő hosszúságú kimondott szöveg a **tulajdonos** és a **személy** entitás helyesen és helytelenül van kibontva:

|Kimondott szöveg|Entitás|Helyes extrakció|
|--|--|:--:|
|e-mailek a Christől érkező kutyákról|tárgy = kutyák<br>személy = Chris|✔|
|e-mailek az embertől a La Manchatól|tárgy = a Man<br>személy = La Mancha|X|

Az előző táblázatban a tulajdonosnak `the man from La Mancha` kell lennie (könyv címe), de mivel a tárgy tartalmazza a választható szót `from`, a cím helytelenül van jelezve. 

Ha ki szeretné javítani ezt a kivételt a mintához, adja hozzá a (`the man from la mancha` explicit listaként a {subject} entitáshoz a [explicit listához tartozó authoring API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)használatával.

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Nem kötelező szöveg megjelölésének szintaxisa a sablonban
Megadhatja a szöveg jelölését a kifejezésben a reguláris kifejezés szögletes zárójelének szintaxisa `[]`használatával. A választható szöveg legfeljebb két szögletes zárójelet tud beágyazni.

|Minta opcionális szöveggel|Jelentés|
|--|--|
|`[find] email about {subject} [from {person}]`|a `find` és a `from {person}` nem kötelező|
|"Tud segíteni [?]|Az írásjel nem kötelező.|

A központozás (`?`, `!`, `.`) figyelmen kívül hagyható, és a mintázatok szögletes zárójelének szintaxisa alapján figyelmen kívül kell hagyni őket. 

## <a name="pattern-only-apps"></a>Csak minta alkalmazások
Olyan alkalmazásokat hozhat létre, amelyek nem rendelkeznek példa hosszúságú kimondott szöveg, feltéve, hogy az egyes szándékok esetében minta van. Csak minta alkalmazás esetében a minta nem tartalmazhat géppel megtanult entitásokat, mert ezek a hosszúságú kimondott szöveg példaként szükségesek. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg az [ajánlott eljárásokat](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan implementálhat mintákat ebben az oktatóanyagban](luis-tutorial-pattern.md)
