---
title: Az entitástípusok LUIS alkalmazásokban az Azure-ban ismertetése |} Microsoft Docs
description: Adja hozzá a entitások (fontos adatot az alkalmazás tartomány) nyelvi ismertetése intelligens szolgáltatás (LUIS) alkalmazásokban.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/22/2018
ms.author: v-geberr
ms.openlocfilehash: 918f5d9efa1163558e44c2c67028dbf802f479a5
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266787"
---
# <a name="entities-in-luis"></a>Bejegyzései szerepelnek LUIS

Entitások szavakat vagy kifejezéseket a utterances, amelyek fontos adatot az alkalmazás a tartományban.

## <a name="entity-compared-to-intent"></a>Entitás leképezés képest
Az entitás egy szót vagy kifejezést a kibontott kívánt utterance belül jelöli. Egy utterance számos entitás vagy tartalmazhatnak nincs minden. Egy entitás egy osztály, beleértve a hasonló objektumok (helyek, dolog, személyek, események vagy fogalmak) gyűjteménye jelöli. Entitások leíró szándékával vonatkozó információt, és néha nélkülözhetetlenek az alkalmazás a feladat végrehajtásához. Például egy hírek fájlkeresés alkalmazás tartalmazhat entitások, például a "témakör", "forrás", "kulcsszó" és "közzététel dátuma", amelyek fontos adatot hírek kereséséhez. Egy utazás foglalási alkalmazást, a "hely", "date", "légitársaság" "utazás class" és "jegyek" repülési foglalási (a "Bookflight" célt vonatkozó) tartozó nyilvánoskulcs-adatokat.

A cél képest, a teljes utterance előrejelzését jelöli. 

## <a name="entities-represent-data"></a>Entitások-adatait tartalmazzák.
Entitások legyenek lekérés a utterance a kívánt adatokat. Ez lehet a nevét, a dátum, a termék neve vagy a bármely szavak csoportja. 

|Utterance|Entitás|Adatok|
|--|--|--|
|A New York 3 jegyek megvásárlása|Előre elkészített száma<br>Location.Destination|3<br>New York|
|A jegy Győr londonba megvásárlása március 5|Location.Origin<br>Location.Destination<br>Előre elkészített datetimeV2|New York<br>London<br>2018. március 5.|

## <a name="entities-are-optional-but-highly-recommended"></a>Entitások legyenek kötelező, de erősen ajánlott
Leképezések szükség, entitásokat is választható. Nem kell minden koncepció az alkalmazásban, de csak az alkalmazás beavatkozásra szükséges entitásokat hozhatnak létre. 

Ha a utterances nem rendelkezik a botot folytatásához szükséges részleteket, nem kell adja hozzá. Az alkalmazás Miután kiforrottá válik, hogy később is hozzáadhatja őket. 

Ha nem biztos abban, hogyan használhatja az adatokat, adjon hozzá néhány gyakori előre elkészített entitások, például datetimeV2, sorszám, e-mailek és telefonszámát.

## <a name="label-for-word-meaning"></a>Word jelentése címkéje
Ha a választott word vagy a word megállapodás azonos, de nem ugyanazt jelenti, nem címkével az entitás. 

A következő utterances, a word `fair` egy homográfokat van. Azonos írta be, de eltérő jelentéssel rendelkezhetnek rendelkezik:

```
What kind of county fairs are happening in the Seattle area this summer?
Is the current rating for the Seattle review fair?
```

Ha egy esemény entitás található összes esemény adatai, a word címke `fair` az első utterance, de nem a második.

## <a name="entities-are-shared-across-intents"></a>Entitások közösen használhatóak leképezések
Entitások leképezések között vannak megosztva. Bármilyen egyetlen szándék nem tartoznak. Leképezések és entitások társíthatók szemantikailag, de nem kizárólagos kapcsolatot jelent.

A utterance a "könyv me Párizsi jegy", "Párizsi" a hely típusú entitás. Által ismer fel a felhasználói bevitel ismertetett entitások, LUIS segít kiválasztani a konkrét műveleteket megjelölésű teljesítésére.

## <a name="assign-entities-in-none-intent"></a>Bejegyzései szerepelnek nincs leképezési hozzárendelése
Minden leképezések, beleértve a **nincs** leképezés, rendelkeznie kell címkével entitásokat. Ezzel a megoldással LUIS további információk a utterances belül hol áll az entitásokat és szavak Mik az entitások körül. 

## <a name="types-of-entities"></a>Entitások típusai
LUIS kínál számos különböző típusú entitásokat; előre elkészített entitások, egyéni gép megtanulta, entitásokat és a lista entitásokat.

| Name (Név) | A címkézés | Leírás |
| -- |--|--|
| **Előre elkészített** <br/>[Egyéni](#prebuilt)| |  **Definíció**<br>Beépített általánosan használt fogalmakat ismertető képviselő típusok. <br><br>**List**<br/>kulcs kifejezés száma, sorszámát, hőmérséklet, dimenzió, pénzt, kor, százalékos, e-mail, URL-cím, telefonszám és kulcs kifejezést. <br><br>Előre elkészített entitásnévnek vannak fenntartva. <br><br>Az alkalmazás által hozzáadott összes előre elkészített entitások vissza a [végpont](luis-glossary.md#endpoint) lekérdezés. További információkért lásd: [előre elkészített entitások](./Pre-builtEntities.md). <br/><br/>[Példa egy válasz entitás](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **Reguláris kifejezés**<br/>[RegEx](#regex)||**Definíció**<br>Formázott szöveget egyéni reguláris kifejezést. Figyelmen kívül hagyja az eset, és figyelmen kívül hagyja a kulturális változat.  <br><br>Ehhez az entitáshoz jó szót vagy kifejezést semmilyen változást, amely egyúttal összhangban következetesen formázott.<br><br>Reguláris kifejezések egyeztetésének helyesírását változásokból eredő után alkalmazza. <br><br>Ha a reguláris kifejezés túl összetett, például számos zárójelek használatával, még nem adhat hozzá a kifejezés a modellben. <br><br>**Példa**<br>`kb[0-9]{6,}` kb123456 megegyezik.<br/><br/>[Gyors útmutató](luis-quickstart-intents-regex-entity.md)<br>[Példa egy válasz entitás](luis-concept-data-extraction.md)|
| **Egyszerű** <br/>[Számítógép-nal](#machine-learned) | ✔ | **Definíció**<br>Egy egyszerű entitás egy általános entitás, amely leírja egy egyetlen fogalom, és van megtanulta, a gép megtanulta környezetből. A környezetben például a word választás, word elhelyezési és utterance hossza.<br/><br/>Ez az a jó entitás szót vagy kifejezést, amely nem egységesen formátumú, de ugyanazt jelenti. <br/><br/>[Gyors útmutató](luis-quickstart-primary-and-secondary-data.md)<br/>[Példa egy válasz entitás](luis-concept-data-extraction.md#simple-entity-data)|  
| **List** <br/>[Pontos egyezés](#exact-match)|| **Definíció**<br>Lista entitások együtt a synoymns kapcsolódó szavak rögzített, lezárt csoportját képviselik, a rendszer. <br><br>Előfordulhat, hogy minden lista entitás egy vagy több. Legjobb használja ugyanazt a fogalmat képviselik módjai változata egy ismert gyűjteményével.<br/><br/>LUIS további értékek lista entitások nem deríti fel. Használja a megjelenítéséhez [szemantikai szótár](luis-glossary.md#semantic-dictionary) ahol tanácsokat új szavak jelenlegi listája alapján.<br/><br>Ha egynél több lista entitás ugyanarra az értékre, akkor a végpont lekérdezés által visszaadott minden entitáshoz. <br/><br/>[Gyors útmutató](luis-quickstart-intent-and-list-entity.md)<br>[Példa egy válasz entitás](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[Vegyes](#mixed) | ✔|**Definíció**<br>Patterns.any pedig változó hosszúságú használt csak egy minta sablon utterance jelöli, ahol az entitás kezdődik, és ezzel véget ér.  <br><br>**Példa**<br>Egy utterance keressen rá a címe alapján könyvek megadott, a pattern.any kibontja a teljes nevét. A sablon utterance pattern.any használatával van `Who wrote {BookTitle}[?]`.<br/><br/>[Oktatóanyag](luis-tutorial-pattern.md)<br>[Példa egy válasz entitás](luis-concept-data-extraction.md#composite-entity-data)|  
| **Összetett** <br/>[Számítógép-nal](#machine-learned) | ✔|**Definíció**<br>Egy összetett entitást egyéb entitások, például az előre elkészített entitásokat, és egyszerű tevődik össze. A különálló entitások alkotnak teljes entitás. Lista entitások összetett entitások nem engedélyezettek. <br><br>**Példa**<br>Egy összetett entitást PlaneTicketOrder nevű rendelkezhetnek gyermek entitások előre elkészített `number` és `ToLocation`. <br/><br/>[Oktatóanyag](luis-tutorial-composite-entity.md)<br>[Példa egy válasz entitás](luis-concept-data-extraction.md#composite-entity-data)|  
| **Hierarchikus** <br/>[Számítógép-nal](#machine-learned) |✔ | **Definíció**<br>A hierarchikus egy kategória összefüggéseikben való megismert entitások.<br><br>**Példa**<br>A hierarchikus entitására megadott `Location` gyermekkel rendelkező `ToLocation` és `FromLocation`, minden gyermek alapján meghatározható a **környezetben** a utterance belül. A utterance a `Book 2 tickets from Seattle to New York`, a `ToLocation` és `FromLocation` összefüggéseikben való különböző alapú felhasználókat ezekbe a csoportokba a szavakat. <br/><br/>**Ne használjon, ha**<br>Ha a keresett olyan entitás, amely rendelkezik a pontos szöveg egyezik a gyermekobjektumok függetlenül a környezetben, egy lista entitás kell használnia. Ha a szülő-gyermek kapcsolat más entitás típusokat, az összetett entitást kell használnia.<br/><br/>[Gyors útmutató](luis-quickstart-intent-and-hier-entity.md)<br>[Példa egy válasz entitás](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
**Előre elkészített** entitások LUIS által biztosított egyéni entitások legyenek. A nyílt forráskódú definiált néhány ezeket az entitásokat [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text) projekt. Nincsenek a sok [példák](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs) a támogatott kulturális környezetek /Specs könyvtárában. A megadott kulturális környezet vagy a szervezet jelenleg nem támogatott, ha hozzájárulnak a projektet. 

<a name="machine-learned"></a>
**Számítógép-megtanulta,** entitások legmegfelelőbb keresztül tesztelésekor [végpont lekérdezések](luis-concept-test.md#endpoint-testing) és [végpont utterances megtekintésével](label-suggested-utterances.md). 

<a name="regex"></a>
**Reguláris kifejezés entitások** határozzák meg a reguláris kifejezés a felhasználó megadja az entitás definíciójának részeként. 

<a name="exact-match"></a>
**Pontos egyezést** entitások az entitást a meglévőt használja egy pontos szöveg felel meg.

<a name="mixed"></a>
**Vegyes** entitások entitás észlelési módszerekkel együtt használja.

## <a name="entity-limits"></a>Entitás korlátok
Felülvizsgálati [korlátok](luis-boundaries.md#model-boundaries) megérteni az egyes entitás hány is hozzáadhat egy olyan modell felé.

## <a name="composite-vs-hierarchical-entities"></a>Összetett vs hierarchikus entitások
Összetett entitások hierarchikus entitások szülő-gyermek kapcsolatban és számítógép-nal. A gépi tanulás lehetővé teszi, hogy a LUIS tudni, hogy az entitások különböző környezetekben (szavak elrendezésének) alapján. Összetett entitások jai rugalmasabbak, mivel lehetővé teszik a különböző entitástípusok gyermekobjektumokkal. A hierarchikus entitás gyermekek csak egyszerű entitások. 

|Típus|Cél|Példa|
|--|--|--|
|Hierarchikus|Szülő-gyermek egyszerű entitások|Location.Origin=New York<br>Location.Destination=London|
|Összetett|Szülő-gyermek entitások: előre elkészített, lista egyszerű, hierarchikus| szám = 3<br>lista első osztály =<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>Több entitás megfelelő adatok
Ha egy szót vagy kifejezést megegyezik egy entitást, a végpont lekérdezés minden entitás adja vissza. Ha mind az előre elkészített számú entitáshoz, és a prebuild datetimeV2 hozzáadása, és egy utterance `create meeting on 2018/03/12 for lunch with wayne`, LUIS képes felismerni az entitások és entitások tömbjét adja vissza a JSON-végpont válasz részeként: 

```JSON
{
  "query": "create meeting on 2018/03/12 for lunch with wayne",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.9333419
  },
  "entities": [
    {
      "entity": "2018/03/12",
      "type": "builtin.datetimeV2.date",
      "startIndex": 18,
      "endIndex": 27,
      "resolution": {
        "values": [
          {
            "timex": "2018-03-12",
            "type": "date",
            "value": "2018-03-12"
          }
        ]
      }
    },
    {
      "entity": "2018",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 21,
      "resolution": {
        "value": "2018"
      }
    },
    {
      "entity": "03/12",
      "type": "builtin.number",
      "startIndex": 23,
      "endIndex": 27,
      "resolution": {
        "value": "0.25"
      }
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Több lista entitás megfelelő adatok
Ha egy szót vagy kifejezést megegyezik egy lista entitást, az a végpont lekérdezés minden lista entitást adja vissza.

A lekérdezés `when is the best time to go to red rock?`, és az alkalmazás még a word `red` egynél több listában LUIS képes felismerni az entitások és entitások tömbjét adja vissza a JSON-végpont válasz részeként: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Ha több mint entitások maximális száma 

Előfordulhat, hogy szeretné használni, hierarchikus és összetett entitások. Hierarchikus entitások tükrözze jellemzőkkel rendelkezik, vagy egy kategóriát tagjai entitások közötti kapcsolat. A gyermek entitások a szülő kategória összes tagja. A hierarchikus entitás PlaneTicketClass nevű Előfordulhat például, a gyermek entitások EconomyClass és FirstClass. A hierarchia csak egy egymásba ágyazási szintjét is.  

Összetett entitások egész részeit jelölik. Egy összetett entitást PlaneTicketOrder nevű Előfordulhat például, alárendelt entitások légitársaság, cél, DepartureCity, DepartureDate és PlaneTicketClass. Készít egy összetett entitást a már meglévő egyszerű entitásokból, hierarchikus, vagy előre elkészített entitásokat gyermekei.  

LUIS is biztosít a lista entity Type típusként van nem gép-megtanulta, de lehetővé teszi az LUIS alkalmazás rögzített értékek listáját adja meg. Lásd: [LUIS határok](luis-boundaries.md) tekintse át a lista entitástípusú korlátok mutató hivatkozás. 

Ha már minősül, hierarchikus, összetett, és entitások listában, és továbbra is szükséges több, mint a korlátot, forduljon a támogatási szolgálathoz. Ehhez az szükséges, a rendszer részletes információt gyűjteni, írja be a [LUIS] [ LUIS] webhelyet, és válassza ki azt **támogatási**. Ha az Azure-előfizetés magában foglalja a támogatási szolgálathoz, forduljon a [Azure technikai támogatás](https://azure.microsoft.com/support/options/). 

## <a name="best-practices"></a>Ajánlott eljárások

Hozzon létre egy [entitás](luis-concept-entity-types.md) Ha a hívó alkalmazás vagy a kell bizonyos paramétereit vagy a művelet végrehajtásához szükséges utterance adatait. Egy entitás szót vagy kifejezést a utterance, amelyekre szüksége van a kibontott--lehet, hogy egy függvény paramétereként. 

Válassza ki a megfelelő típusú entitás hozzáadása az alkalmazáshoz, akkor tudnia kell, hogy adatbevitel felhasználók. Minden entitás típusa megtalálható egy másik mechanizmussal, például a gépi tanulásra, lezárt lista vagy reguláris kifejezést. Ha biztos abban, egy egyszerű entitás kezdődnie, és a szó vagy kifejezés, többek között a nincs minden leképezések között tárolt adatokat az összes utterances jelölő leképezési címkézését.  

Tekintse át a végpont utterances rendszeresen gyakori használati ahol reguláris kifejezésként azonosított vagy egy szöveges pontos egyezés található a entitás található.  

A felülvizsgálati részeként fontolja meg egy kifejezéslista LUIS szót vagy kifejezést, hogy szükség a tartományhoz, de nincsenek pontos egyezés, és amelynek LUIS nem rendelkezik a magas megbízhatósági jel hozzáadása.  

Lásd: [ajánlott eljárások](luis-concept-best-practices.md) további információt.

## <a name="next-steps"></a>További lépések

Ismerje meg a helyes fogalmait [utterances](luis-concept-utterance.md). 

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) entitások felvétele a LUIS app tájékozódhat.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website