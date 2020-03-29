---
title: Jó példa kimondott szöveg - LUIS
titleSuffix: Azure Cognitive Services
description: A beszédelem az a felhasználói bemenet, amelyet az alkalmazásnak értelmeznie kell. Gyűjtse össze azokat a kifejezéseket, amelyeket a felhasználók beírnak. Tartalmazza kimondott szöveg, amely ugyanazt jelenti, de a szavak hossza és a szavak elhelyezése eltérően épülnek fel.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: diberry
ms.openlocfilehash: 7412677773b60a1894a6ece7251e797bfddee091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219917"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Ismerje meg, milyen jó kimondott szöveg a LUIS-alkalmazáshoz

**Utterances bemeneta** a felhasználótól, hogy az alkalmazás kell értelmezni. Luis betanításához leképezések és entitások kinyerése tőlük, fontos, hogy rögzítse a különböző példa utterances az egyes szándékok. Aktív tanulás, vagy a folyamat továbbra is a betanításúj utterances, elengedhetetlen a gépi tanult intelligencia, amely a LUIS nyújt.

Gyűjtse össze a kimondott szövegeket, amelyekről úgy gondolja, hogy a felhasználók bekerülnek. Utterances, ami ugyanazt jelenti, de épített különböző különböző módokon:

* Kimondott szöveg hossza - rövid, közepes és hosszú az ügyfél-alkalmazás
* Szó és kifejezés hossza 
* Szóelhelyezés – entitás az utterance (kifejezés) elején, közepén és végén
* Nyelvtan 
* Pluralizáció
* Eredet
* Főnév és igeválasztás
* Írásjelek - egy jó fajta segítségével helyes, helytelen, és nem nyelvtan

## <a name="how-to-choose-varied-utterances"></a>Hogyan válasszuk ki a különböző kimondott szövegeket?

Amikor először kezdi el [a luis-modellhez adott példakimondott szövegeket,](luis-how-to-add-example-utterances.md) az alábbiakban néhány olyan alapelvet kell szem előtt tartania, amelyeket szem előtt kell tartania.

### <a name="utterances-arent-always-well-formed"></a>A kimondott szöveg nem mindig jól alakul ki

Lehet egy mondat, mint a "Foglaljon jegyet Párizsba nekem", vagy egy mondat töredéke, mint a "Foglalás" vagy a "Párizsi járat".  A felhasználók gyakran helyesírási hibákat követnek el. Az alkalmazás tervezésekor fontolja meg, hogy a [Bing helyesírás-ellenőrzés](luis-tutorial-bing-spellcheck.md) segítségével javítsa ki a felhasználói bevitelt, mielőtt átadná azt a LUIS-nak. 

Ha nem helyesírás-ellenőrzés felhasználói utterances, be kell képeznie a LUIS kimondott szöveg, amely elírásokat és elírásokat tartalmaz.

### <a name="use-the-representative-language-of-the-user"></a>A felhasználó reprezentatív nyelvének használata

Utterances kiválasztásakor vegye figyelembe, hogy mit gondol egy közös kifejezés vagy kifejezés nem lehet helyes az ügyfélalkalmazás tipikus felhasználója számára. Lehet, hogy nincs tartományi tapasztalatuk. Legyen óvatos, amikor olyan kifejezéseket vagy kifejezéseket használ, amelyeket a felhasználó csak akkor mondana, ha szakértő lenne.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Válasszon változatos terminológiát, valamint kifejezéseket

Meg fogja találni, hogy még akkor is, ha erőfeszítéseket tesz, hogy hozzon létre változatos mondat minták, akkor is ismételje meg néhány szókincs.

Vegyük ezt a példát utterances:

|Példák kimondott szövegekre|
|--|
|hogyan juthatok be a számítógép?|
|Honnan szerezhetek számítógépet?|
|Azt akarom, hogy a számítógép, hogyan megy ez?|
|Mikor lehet számítógépem?| 

A fő kifejezés itt, "számítógép", nem változatos. Használjon olyan alternatívákat, mint az asztali számítógép, laptop, munkaállomás, vagy akár csak gép. A LUIS intelligensen következtetheti a szinonimákat a környezetből, de amikor kimondott szövegeket hoz létre a betanításhoz, mindig jobb, ha változik.

## <a name="example-utterances-in-each-intent"></a>Példa kimondott szövegre az egyes szándékokban

Minden szándéknak legalább 15 példa utterances kell rendelkeznie. Ha van egy szándék, amely nem rendelkezik példa utterances, nem lesz képes betanítása LUIS. Ha egy vagy nagyon kevés példa utterances szándékkal rendelkezik, a LUIS nem pontosan előre a szándékot. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>15 utterances-ből álló kis csoportok hozzáadása minden egyes szerzői ismétléshez

A modell minden egyes iterációjában ne adjon hozzá nagy mennyiségű utterances. 15-ös mennyiségben adja hozzá a kimondott szöveget. [Betanítás,](luis-how-to-train.md) [közzététel](luis-how-to-publish-app.md)és [tesztelés](luis-interactive-test.md) újra.  

A LUIS hatékony modelleket hoz létre a LUIS-modell szerzője által gondosan kiválasztott kimondott szövegekkel. Túl sok kimondott szöveg hozzáadása nem értékes, mert zavart okoz.

Jobb, ha néhány utterances, majd tekintse át a [végpont kimondott szöveghelyes](luis-how-to-review-endpoint-utterances.md) szándék előrejelzési és entitás kinyerése.

## <a name="utterance-normalization"></a>Kimondott szöveg normalizálása

Utterance normalizálása az a folyamat, figyelmen kívül hagyja a hatását írásjelek és mellékjelek a betanítás és előrejelzés során.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Az írásjelek és az írásjelek kimondott szövegnormalizálása

Az utterance normalizálás a rendszer az alkalmazás létrehozásakor vagy importálásakor van definiálva, mert az az alkalmazás JSON-fájljában található. Az utterance (kifejezés normalizálási) beállításai alapértelmezés szerint ki vannak kapcsolva. 

A mellékjelek a szövegben lévő jelek vagy jelek, például: 

```
İ ı Ş Ğ ş ğ ö ü
```

Ha az alkalmazás bekapcsolja a normalizálást, a **Teszt** ablaktáblában lévő pontszámok, a kötegtesztek és a végpontlekérdezések minden mellékjelet vagy írásjelet használó kimondott szövegre változnak.

Kapcsolja be az utterance (kifejezés normalizálása mellékjelek vagy írásjelek) a LUIS JSON alkalmazásfájl a `settings` paraméterben.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Az **írásjelek normalizálása** azt jelenti, hogy mielőtt a modellek betanítása és a végpontlekérdezések előre jelzett, írásjelek törlődnek a kimondott szövegeket. 

A **mellékjelek normalizálása** a karaktereket a kimondott szövegekben a mellékjelekre helyettesíti a normál karakterekre. Például: `Je parle français` `Je parle francais`lesz . 

Normalizálás nem jelenti azt, hogy nem fogja látni írásjelek és mellékjelek a példa utterances vagy előrejelzési válaszok, csak, hogy figyelmen kívül hagyja a képzés és az előrejelzés során.


### <a name="punctuation-marks"></a>Írásjelek

Az írásjelek egy külön token a LUIS-ban. Egy utterance (kifejezés) amely egy időszak végén, szemben egy utterance (kifejezés, amely nem tartalmaz egy időszak végén a végén két külön utterances, és két különböző előrejelzéseket kap. 

Ha az írásjelek nem normalizálódnak, a LUIS alapértelmezés szerint nem hagyja figyelmen kívül az írásjeleket, mert egyes ügyfélalkalmazások jelentőséget helyezhetnek ezeken a jeleken. Győződjön meg arról, hogy a példa utterances használata írásjelek és nem írásjelek annak érdekében, hogy mindkét stílus vissza ugyanazt a relatív pontszámokat. 

Győződjön meg arról, hogy a modell kezeli az írásjeleket a példa kimondott szövegekben (írásjellel és írásjellel nem), vagy azokban a [mintákban,](luis-concept-patterns.md) ahol könnyebb figyelmen kívül hagyni az írásjeleket a speciális szintaxissal:`I am applying for the {Job} position[.]`

Ha az írásjeleknek nincs konkrét jelentése az ügyfélalkalmazásban, fontolja meg az [írásjelek figyelmen kívül hagyását](#utterance-normalization) az írásjelek normalizálásával. 

### <a name="ignoring-words-and-punctuation"></a>Szavak és írásjelek figyelmen kívül hagyása

Ha figyelmen kívül szeretne hagyni bizonyos szavakat vagy írásjeleket a mintákban, `[]`használjon [szögletes](luis-concept-patterns.md#pattern-syntax) zárójelek _szintaxisát._ 

## <a name="training-utterances"></a>Betanítási kimondott szöveg

A betanítás általában nem determinisztikus: az utterance (kifejezéselőrejelzés előrejelzés) verziónként vagy alkalmazásonként kissé eltérhet. Eltávolíthatja a nem determinisztikus betanításfrissítésével `UseAllTrainingData` a [verzióbeállítások](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API a név/érték pár az összes betanítási adatok használata.

## <a name="testing-utterances"></a>Kimondott szöveg tesztelése 

A fejlesztőknek meg kell kezdeniük a LUIS-alkalmazás valós forgalommal való tesztelését az [előrejelzési végpont](luis-how-to-azure-subscription.md) URL-címére való kimondott szöveg elküldésével. Ezeket az utterances használják a szándékok és entitások teljesítményének javítására [a felülvizsgálati utterances.](luis-how-to-review-endpoint-utterances.md) A LUIS webhelytesztelési ablaktáblán keresztül elküldött teszteket a rendszer nem küldi el a végponton keresztül, és így nem járulnak hozzá az aktív tanuláshoz. 

## <a name="review-utterances"></a>Kimondott szöveg áttekintése

Miután a modell bevan tanítva, közzétett és [végponti](luis-glossary.md#endpoint) lekérdezéseket kap, tekintse át a LUIS által javasolt [kimondott szövegeket.](luis-how-to-review-endpoint-utterances.md) A LUIS olyan végpontkimondott szövegeket választ ki, amelyek a szándékhoz vagy az entitáshoz alacsony pontszámokat tartalmazó akta. 

## <a name="best-practices"></a>Ajánlott eljárások

Tekintse át [az ajánlott eljárásokat,](luis-concept-best-practices.md) és alkalmazza azokat a szokásos szerzői ciklus részeként.

## <a name="label-for-word-meaning"></a>Szójelentés-címke

Ha a szóválasztás vagy a szóelrendezés megegyezik, de nem ugyanazt jelenti, ne címkézze fel az entitással. 

A következő kimondott szöveg, a szó `fair` egy homográfia. Ugyanazt írják, de más jelentése van:

|Kimondott szöveg|
|--|
|Milyen megyei vásárok történnek a Seattle területén ezen a nyáron?|
|A jelenlegi értékelés a Seattle felülvizsgálat tisztességes?|

Ha azt szeretné, hogy egy eseményentitás megtalálja `fair` az összes eseményadatot, címkézze fel a szót az első utterance (kifejezés), de nem a második.


## <a name="next-steps"></a>További lépések
Lásd: [Példa utterances](luis-how-to-add-example-utterances.md) a LUIS-alkalmazás betanításával kapcsolatos információkért a felhasználói utterances.

