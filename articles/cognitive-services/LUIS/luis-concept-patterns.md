---
title: Ismerje meg, hogyan minták növelni előrejelzés pontosságát |} Microsoft Docs
titleSuffix: Azure
description: Útmutató a kialakítási minták leképezési előrejelzés pontszámok és entitások található.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr
ms.openlocfilehash: f45a9dfaf2863b81083fbfb647f420d9a4baed5a
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264663"
---
# <a name="patterns-improve-prediction-accuracy"></a>Minták előrejelzési pontosság növeléséhez.
Minták pontosság növeléséhez, ha több utterances nagyon hasonló készültek. A minta biztosítva a utterance, LUIS lehet a magas megbízhatósági előrejelzését. 

## <a name="patterns-solve-low-intent-confidence"></a>Minták alacsony leképezési abban, hogy megoldása
Fontolja meg egy az emberi erőforrások alkalmazást, amely a szervezeti diagram viszonyítva egy alkalmazott kapcsolatos jelentések. Az alkalmazottak és a kapcsolat megadott, LUIS adja vissza az alkalmazottak jár. Fontolja meg az alkalmazottak, Tom, egy Manager Alice nevét, és a beosztottak nevű csoport: Michael Rebecca és Carl.

![Szervezeti diagram képe](./media/luis-concept-patterns/org-chart.png)

|Beszédmódok|Előre meghatározott leképezés|Leképezési pontszám|
|--|--|--|
|Ki az alárendelt Tom meg?|GetOrgChart|.30|
|A beosztottja az Tom ki?|GetOrgChart|.30|

Ha egy alkalmazás közötti 10 és 20 utterances különböző hosszúságú mondat, más word sorrendben és még más szavakat (szinonimák "alárendelt", "kezelése", "jelentés"), a LUIS térhetnek vissza egy kis abban, hogy pontszám. Ahhoz, hogy a word sorrendje fontos megérteni LUIS érdekében hozzon létre egy mintát. 

Minták oldja meg a következő esetekben: 

* Ha a szándék pontszám alacsony
* Ha a megfelelő célja nem a legfelső szintű pontszám, de a felső pontszám túl közel. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>Minták nincsenek-e a céljának garancia
Minták előrejelzés technológiák vegyesen használni. Egy olyan sablon utterance leképezésének beállítása egy minta nem garantált leképezési előrejelzését, de egy erős jel. 

## <a name="patterns-do-not-improve-entity-detection"></a>Minták növelhető az entitás észlelése
Pedig minták entitások igényelnek, akkor a minta nem működik a észlelésére az entitás. A minta csak arra szolgál, hogy súgó leképezések és szerepköröket előrejelzését.  

## <a name="prediction-scores-with-and-without-patterns"></a>Előrejelzés pontszámok rendelkező és anélküli minták
Elegendő példa utterances tekintve LUIS lenne növelheti a előrejelzés abban, hogy mintákat nélkül. Minták növelje az vetett bizalmat pontszám annyi utterances megadása nélkül.  

## <a name="pattern-matching"></a>Mintaegyezéshez
A minta egyezik belül a minta entitások először észlelésére, majd a többi szavak és a word sorrendjét a minta ellenőrzése alapján. A minta egyező mintát entitások kell megadni. 

## <a name="pattern-syntax"></a>Minta szintaxis
A minták entitások tette kerek zárójeleket. Minták entitásokat és szerepkörök rendelkező entitások lehetnek. Pattern.any egy entitás csak akkor minták használják. Minden egyes szintaxisa a következő szakaszok az ismertetése.

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Egy entitás hozzáadása egy minta sablon szintaxis
A minta sablonba vehető fel olyan entitás, helyezze az entitás nevével és a kapcsos zárójelek, például a `Who does {Employee} manage?`. 

```
Who does {Employee} manage?
```

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Egy entitás és a szerepkör hozzáadása egy minta sablon szintaxis
Egy entitás szerepkör jelölése `{entity:role}` az az entitás nevét kettősponttal, majd a szerepkör neve követ. A minta sablonba szerepkörrel rendelkező entitás hozzáadásához helyezze az entitás és a kapcsos zárójelek szerepkör nevét, mint `Book a ticket from {Location:Origin} to {Location:Destination}`. 

```
Book a ticket from {Location:Origin} to {Location:Destination}
```

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Szintaxis egy pattern.any mintát sablon hozzáadása
A Pattern.any entitás változó hosszúságú entitás hozzáadása a minta teszi lehetővé. Mindaddig, amíg a minta sablont követi, akkor a pattern.any tetszőleges hosszúságúak lehetnek. 

Hozzáadása egy **Pattern.any** entitás a minta sablonba, helyezze a kapcsos zárójelek Pattern.any egyed például `How much does {Booktitle} cost and what format is it available in?`.  

```
How much does {Booktitle} cost and what format is it available in?
```

|A mintában könyv címek|
|--|
|Mennyibe **ellopni a könyv** költségeket, és milyen formátumban is elérhető?|
|Mennyibe **kérje meg** költségeket, és milyen formátumban is elérhető?|
|Mennyibe **az éjszakai a kutya a fejezetét incidens** költségeket, és milyen formátumban is elérhető?| 

Könyv cím példákban a könyv cím környezetfüggő szavak nincsenek zavaró LUIS való. LUIS tudja, ahol a könyv címe véget ér, mert egy mintát és egy Pattern.any entitás jelölésű.

### <a name="explicit-lists"></a>Explicit listája
Ha a minta egy Pattern.any tartalmazza, és a minta-szintaxis lehetővé teszi, hogy a lehetőségét a utterance alapján egy helytelen entitás kinyerési, hozzon létre egy [Explicit lista](https://aka.ms/ExplicitList) a beállítását a szerzői műveletekhez API-n keresztül. 

Tegyük fel például, van egy mintát tartalmazó mindkét választható szintaxis `[]`, és az entitás `{}`, az adatok kinyerése érdekében helytelen módon kombinált.

Fontolja meg a minta "[keresése] e-mail vonatkozó {} [személytől {}]". A következő utterances a **tulajdonos** és **személy** entitást ki kell olvasni a helyes és helytelen:

|Utterance|Entitás|Megfelelő kivonása|
|--|--|:--:|
|a e-mailekhez kapcsolatos kutya Chris|tulajdonos kutya =<br>személy = Chris|✔|
|e-mailben a La Mancha man kapcsolatos|tulajdonos a man =<br>személy La Mancha =|X|

Az előző táblázatban, a utterance `email about the man from La Mancha`, a tulajdonosának kell lennie `the man from La Mancha` (a könyv címét), de, mert a tulajdonos tartalmazza a választható word `from`, a cím helytelenül várhatóan. 

Ez a kivétel a típus kijavításához vegye fel `the man from la mancha` használó {tulajdonos} entitás egy explicit lista megfelelést a [API szerzői explicit listáját](https://aka.ms/ExplicitList).

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>A sablon utterance a választható szöveg szintaxis
Jelölje meg a reguláris kifejezés szögletes zárójelet szintaxis használatával utterance választható szöveg `[]`. A választható szöveg ágyazhatja legfeljebb két zárójeleket csak a kapcsos zárójeleket.

```
[find] email about {subject} [from {person}]
```

Például írásjelek `.`, `!`, és `?` figyelmen kívül hagyhatja a kapcsos zárójeleket használatával. Ahhoz, hogy figyelmen kívül hagyja ezeket a jeleket, minden be van jelölve egy külön mintát kell lennie. A választható szintaxis jelenleg nem támogatja a rendszer figyelmen kívül hagyja a több elem a lista egy elemére.

## <a name="patterns-only"></a>Csak minták
LUIS leképezés számára lehetővé teszi bármely példa utterances nem rendelkező alkalmazások. Ez csak akkor, ha használatban minták engedélyezett. Minták megkövetelése minden mintában legalább egy entitást. Csak minta alkalmazások esetén a minta nem tartalmazhatja a entitások gép-megtanulta, mert például utterances szükséges. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg, [ajánlott eljárások](luis-concept-best-practices.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Megtudhatja, hogyan minták megvalósítását az oktatóanyag a](luis-tutorial-pattern.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
