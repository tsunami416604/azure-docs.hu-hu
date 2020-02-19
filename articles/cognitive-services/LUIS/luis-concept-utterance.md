---
title: Jó példa hosszúságú kimondott szöveg – LUIS
titleSuffix: Azure Cognitive Services
description: A hosszúságú kimondott szöveg olyan adatokat kapnak a felhasználótól, akiket az alkalmazásnak kell értelmezni. Gyűjtse be azokat a kifejezéseket, amelyeket a felhasználók meg fognak adni. Olyan hosszúságú kimondott szöveg adjon meg, amelyek ugyanazt a dolgot jelentik, de a szó hosszában és a Word elhelyezésekor máshogy vannak kiépítve.
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280797"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Ismerje meg, hogy a LUIS-alkalmazás milyen jó hosszúságú kimondott szöveg

A **hosszúságú kimondott szöveg** olyan adatokat kapnak a felhasználótól, akiket az alkalmazásnak kell értelmezni. A LUIS betanításához, hogy kinyerje a szándékait és az entitásokat, fontos, hogy az egyes szándékok különböző példáit hosszúságú kimondott szöveg. Az aktív tanulás, illetve az új hosszúságú kimondott szöveg való folyamatos betanítás folyamata elengedhetetlen a géppel megszerzett intelligenciához, amelyet a LUIS biztosít.

Gyűjtse össze azokat a hosszúságú kimondott szöveg, amelyeket a felhasználók meg fognak adni. Vegyen fel hosszúságú kimondott szöveg, ami ugyanazt jelenti, de különböző módokon vannak kiépítve:

* Teljes hossz – rövid, közepes és hosszú az ügyfél-alkalmazáshoz
* Szó és kifejezés hossza 
* Szó elhelyezése – entitás elején, közepén és végén
* Nyelvtan 
* Pluralization
* Eredő
* Főnév és ige választása
* Írásjelek – a helyes, helytelen és nem nyelvtant használó számos fajta

## <a name="how-to-choose-varied-utterances"></a>Változatos hosszúságú kimondott szöveg kiválasztása

Első lépésként vegyen fel [például hosszúságú kimondott szöveg](luis-how-to-add-example-utterances.md) a Luis-modellbe, és tekintse meg az alábbi alapelveket.

### <a name="utterances-arent-always-well-formed"></a>A hosszúságú kimondott szöveg nem mindig megfelelően formázott

Ez lehet egy mondat, például: "jegy befoglalása Párizsba", vagy egy mondat töredéke, például "foglalás" vagy "Párizs Flight".  A felhasználók gyakran végeznek helyesírási hibákat. Az alkalmazás megtervezése során érdemes megfontolni, hogy az [Bing Spell Check](luis-tutorial-bing-spellcheck.md) használatával javítsa ki a felhasználói adatokat, mielőtt elkezdené a Luis-nek. 

Ha nem jelöli be a felhasználó hosszúságú kimondott szöveg, akkor a LUIS-t a hosszúságú kimondott szöveg kell betanítania, amely tartalmazza az elírásokat és a hibákat.

### <a name="use-the-representative-language-of-the-user"></a>A felhasználó reprezentatív nyelvének használata

A hosszúságú kimondott szöveg kiválasztásakor vegye figyelembe, hogy a leggyakoribb kifejezés vagy kifejezés nem megfelelő az ügyfélalkalmazás jellemző felhasználója számára. Előfordulhat, hogy nem rendelkeznek tartományi tapasztalattal. Legyen körültekintő, ha olyan kifejezéseket vagy kifejezéseket használ, amelyeket a felhasználó csak szakértőnek mond.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Válassza a különböző terminológiát és a beszédmód

Azt tapasztalhatja, hogy még akkor is, ha a különböző mondatok létrehozásához tett erőfeszítéseket, továbbra is meg kell ismételnie néhány szókincset.

Végezze el az alábbi példát a hosszúságú kimondott szöveg:

|Példák kimondott szövegekre|
|--|
|how do I get a computer?|
|Where do I get a computer?|
|I want to get a computer, how do I go about it?|
|When can I have a computer?| 

A "számítógép" alapvető kifejezés nem változtatható meg. Használjon olyan alternatívákat, mint az asztali számítógép, a laptop, a munkaállomás vagy akár a gép. A LUIS intelligens módon kikövetkeztetheti a szinonimákat a kontextusból, de ha hosszúságú kimondott szöveg hoz létre a betanításhoz, mindig érdemes megváltoznia.

## <a name="example-utterances-in-each-intent"></a>Példa hosszúságú kimondott szöveg az egyes leképezésekben

Minden leképezésnek legalább 15 hosszúságú kimondott szöveg kell lennie. Ha olyan szándékkal rendelkezik, amely nem rendelkezik például hosszúságú kimondott szöveg, nem fogja tudni betanítani a LUIS-t. Ha a hosszúságú kimondott szöveg egy vagy nagyon kevés példával rendelkezik, akkor a LUIS nem tudja pontosan megjósolni a szándékot. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>15 hosszúságú kimondott szöveg kisebb csoportjainak hozzáadása minden szerzői iterációhoz

A modell minden egyes iterációjában ne adjon hozzá nagy mennyiségű hosszúságú kimondott szöveg. Adja hozzá a hosszúságú kimondott szöveg a 15. számú mennyiséghez. A [betanítás](luis-how-to-train.md), [Közzététel](luis-how-to-publish-app.md)és [tesztelés](luis-interactive-test.md) .  

LUIS olyan hatékony modelleket épít be a hosszúságú kimondott szöveg, amelyeket a LUIS Model szerzője körültekintően választ ki. Túl sok hosszúságú kimondott szöveg hozzáadása nem értékes, mert zavart okoz.

Érdemes kezdeni néhány hosszúságú kimondott szöveg, majd [tekintse át a végpontok hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md) a megfelelő szándék-előrejelzés és az entitások kinyerése érdekében.

## <a name="utterance-normalization"></a>Kimondás normalizálása

A kizáró normalizálás az a folyamat, amelynek során figyelmen kívül hagyja a központozás és a Mellékjelek hatásait a képzés és az előrejelzés során.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Mellékjelek és írásjelek teljes normalizálása

A teljes normalizálás az alkalmazás létrehozásakor vagy importálásakor van meghatározva, mert az alkalmazás JSON-fájljának beállítása. Alapértelmezés szerint a teljes normalizálás beállításai ki vannak kapcsolva. 

A Mellékjelek a szövegben lévő jelek vagy jelek, például: 

```
İ ı Ş Ğ ş ğ ö ü
```

Ha az alkalmazás bekapcsolja a normalizálás bekapcsolását, a pontszámok a **teszt** ablaktáblában, a Batch-tesztek és a végpont-lekérdezések minden hosszúságú kimondott szöveg a mellékjeleket vagy írásjeleket használva változnak.

A `settings` paraméterben bekapcsolhatja a mellékjeleket vagy a központozást a LUIS JSON-alkalmazás fájljába.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

A **központozás** normalizálása azt jelenti, hogy a modellek betanítása előtt és a végponti lekérdezések megkezdése előtt az írásjelek el lesznek távolítva a hosszúságú kimondott szöveg. 

A **mellékjelek** normalizálása a hosszúságú kimondott szöveg lévő mellékjeleket normál karakterekkel helyettesíti. Például: a `Je parle français` `Je parle francais`válik. 

A normalizálás nem azt jelenti, hogy a hosszúságú kimondott szöveg vagy az előrejelzési válaszokat nem fogja látni a központozás és a Mellékjelek, csupán azt, hogy a képzés és az előrejelzés során figyelmen kívül lesz hagyva.


### <a name="punctuation-marks"></a>Központozás jelei

Absztrakt egy külön jogkivonatot a LUIS. Egy teljes időszakot, amely a végén található pontot és egy teljes időszakot tartalmaz, és nem tartalmaz két különálló hosszúságú kimondott szöveg, és két különböző előrejelzést kaphat. 

Ha a központozás nem normalizált, akkor a LUIS alapértelmezés szerint nem hagyja figyelmen kívül a központozás jelét, mert egyes ügyfélalkalmazások fontosnak helyezhetik el ezeket a jeleket. Győződjön meg arról, hogy a példa hosszúságú kimondott szöveg mindkét írásjelet használja, és nincs írásjel, hogy mindkét stílus ugyanazt a relatív pontszámot adja vissza. 

Győződjön meg arról, hogy a modell kezeli a központozást a példában szereplő hosszúságú kimondott szöveg (és nem rendelkezik írásjelekkel), vagy azokban a [mintázatokban](luis-concept-patterns.md) , amelyekben a speciális szintaxissal egyszerűbb a központozás figyelmen kívül hagyása: `I am applying for the {Job} position[.]`

Ha a központozás nem rendelkezik konkrét jelentéssel az ügyfélalkalmazás számára, akkor az írásjelek normalizálása révén érdemes [figyelmen kívül hagyni](#utterance-normalization) a központozást. 

### <a name="ignoring-words-and-punctuation"></a>Szavak és írásjelek figyelmen kívül hagyása

Ha nem szeretné figyelmen kívül hagyni a konkrét szavakat vagy írásjeleket a mintázatokban, használjon olyan [mintát](luis-concept-patterns.md#pattern-syntax) , amely a szögletes zárójelek _figyelmen kívül hagyása_ , `[]`. 

## <a name="training-utterances"></a>Hosszúságú kimondott szöveg betanítása

A képzés általában nem determinisztikus: a Kimondás előrejelzése némileg változhat a verziók és az alkalmazások között. A nem determinisztikus-képzések eltávolításához frissítse a [Version Settings](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API-t a `UseAllTrainingData` név/érték párral, hogy az összes betanítási adattal használhassa.

## <a name="testing-utterances"></a>Hosszúságú kimondott szöveg tesztelése 

A fejlesztőknek meg kell kezdeniük a LUIS-alkalmazás valós forgalmú tesztelését, ha hosszúságú kimondott szöveg küldenek az [előrejelzési végpont](luis-how-to-azure-subscription.md) URL-címére. Ezek a hosszúságú kimondott szöveg a leképezések és entitások teljesítményének növelésére szolgálnak a [felülvizsgálati hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md). A LUIS webhely tesztelési paneljén elküldött tesztek nem jutnak el a végponton keresztül, így nem járulnak hozzá az aktív tanuláshoz. 

## <a name="review-utterances"></a>Hosszúságú kimondott szöveg áttekintése

A modell kiképzése, közzététele és a [végponti](luis-glossary.md#endpoint) lekérdezések fogadása után [tekintse át a](luis-how-to-review-endpoint-utterances.md) Luis által javasolt hosszúságú kimondott szöveg. LUIS kijelöli a végpontok hosszúságú kimondott szöveg, amelyekben a szándék vagy az entitás számára alacsony pontszám van. 

## <a name="best-practices"></a>Ajánlott eljárások

Tekintse át az [ajánlott eljárásokat](luis-concept-best-practices.md) , és alkalmazza őket a szokásos szerzői ciklus részeként.

## <a name="label-for-word-meaning"></a>Azaz a word-címkét

Ha a választott word vagy a word megállapodás azonos, de nem ugyanazt jelenti, nem címkét, az entitáshoz. 

A következő hosszúságú kimondott szöveg a Word `fair` egy homográfia. Akkor helyesírása megegyezik, de eltérő jelentéssel rendelkezik:

|Kimondott szöveg|
|--|
|What kind of county fairs are happening in the Seattle area this summer?|
|Is the current rating for the Seattle review fair?|

Ha azt szeretné, hogy az esemény entitása az összes eseményt megkeresse, címkézse a szót `fair` az első kiíráskor, de a másodikban nem.


## <a name="next-steps"></a>További lépések
A felhasználói hosszúságú kimondott szöveg megismeréséhez lásd: [példa hosszúságú kimondott szöveg hozzáadása](luis-how-to-add-example-utterances.md) a Luis-alkalmazások betanításához.

