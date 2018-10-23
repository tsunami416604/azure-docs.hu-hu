---
title: A LUIS-alkalmazások – beszédfelismerés entitástípusok
titleSuffix: Azure Cognitive Services
description: A Language Understanding Intelligent Service (LUIS) alkalmazások hozzáadása a entitások (a tartomány az alkalmazás legfontosabb adatok).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: 92b4864f8991380740e6edb498328ce2eea98250
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650111"
---
# <a name="entities-in-luis"></a>A LUIS entitások

Entitások olyan szavak vagy kifejezések, amelyek az alkalmazás tartományban lévő fontos adatokat kimondott szöveg.

## <a name="entity-compared-to-intent"></a>Beszédszándék képest entitás
Az entitás egy szót vagy kifejezést az utterance (kifejezés), amelyeket szeretne kinyert belül jelöli. Az utterance (kifejezés) is tartalmazhat számos entitás vagy nincs minden. Egy entitás egy osztályt, beleértve a hasonló objektumok (helyek, dolog, személyek, események vagy fogalmak) gyűjteményét jelképezi. Entitások ismertetik a leképezés kapcsolódó információk, és néha nélkülözhetetlenek az alkalmazás a feladat végrehajtásához. Például egy News Search alkalmazás tartalmazhat például a "témakör", "forrás", "kulcsszó" és "közzétételi dátuma", amelyek hírkeresés a fontos adatokat. A foglalási utazási alkalmazás, a "hely", "dátum", "légitársaság" "utazási class" és "jegyek" repülési foglalási (a "Bookflight" beszédszándék releváns) adatainak.

Ezzel a célja a teljes utterance (kifejezés) előrejelzését jelöli. 

## <a name="entities-represent-data"></a>Entitások adatokat képviselik.
Entitások az utterance (kifejezés) lekérni kívánt adatok. Ez lehet a nevét, a dátum, a termék neve vagy a szó bármilyen csoport. 

|Kimondott szöveg|Entitás|Adatok|
|--|--|--|
|A New York-i 3 jegyek megvásárlása|Előre összeállított száma<br>Location.Destination|3<br>New York|
|London, New York-i jegyet vásárolni március 5|Location.Origin<br>Location.Destination<br>Előre összeállított datetimeV2|New York<br>London<br>2018. március 5.|

## <a name="entities-are-optional-but-highly-recommended"></a>Entitások nem kötelező, de a erősen ajánlott
Leképezések szükség, míg az entitások nem kötelező. Nem kell minden fogalom, az alkalmazásban, de csak az alkalmazás műveletet szükséges entitások létrehozása. 

Ha a kimondott szöveg nem rendelkezik a robot folytatásához szükséges részleteket, nem kell adja hozzá őket. Az alkalmazás kiforrottá, később is hozzáadhatja. 

Ha nem biztos abban, hogy hogyan használja a információkat, adjon hozzá néhány gyakori előre összeállított entitások, például a datetimeV2 sorszámnál, e-mailek és telefonszámát.

## <a name="label-for-word-meaning"></a>Azaz a word-címkét
Ha a választott word vagy a word megállapodás azonos, de nem ugyanazt jelenti, nem címkét, az entitáshoz. 

A következő utterances, a word `fair` egy homográfokat van. Akkor helyesírása megegyezik, de eltérő jelentéssel rendelkezik:

|Kimondott szöveg|
|--|
|Milyen típusú megye vásárokon Seattle környékén lévő történik az Ez évi riói nyári?|
|Az a jelenlegi besorolása a Seattle felülvizsgálatra valós?|

Ha egy esemény entitás összes esemény az adatok keresésére, a word címke `fair` az első utterance (kifejezés), de nem a második.

## <a name="entities-are-shared-across-intents"></a>Entitások leképezések vannak osztva.
Entitások közötti leképezések vannak megosztva. Minden olyan egyetlen célja nem tartoznak. Szándékok és entitások társíthatók szemantikailag, de nem a kizárólagos kapcsolat.

Az utterance (kifejezés) a "Book me egy jegyet Párizs", "Párizs" a hely típusú entitás. Által az entitások, amelyek nem szerepelnek a felhasználói bevitel FELISMERVE, LUIS segítségével válassza ki a meghatározott műveleteket megjelölésű teljesítéséhez.

## <a name="assign-entities-in-none-intent"></a>Nincs leképezés entitások hozzárendelése
Minden szándék fog vonatkozni, beleértve a **nincs** szándékkal, rendelkeznie kell címkével entitásokat. Ez segít a LUIS-további információ az entitások amelyeknél megcímkézzen és szavakat Mik az entitások körül. 

## <a name="entity-status-for-predictions"></a>Entitás állapota ismeretekkel

Lásd: [entitás állapota előrejelzéseket](luis-how-to-add-example-utterances.md#entity-status-predictions) további információt. 

## <a name="types-of-entities"></a>Entitástípus
A LUIS kínál számos különböző típusú entitások; előre összeállított entitások lista alanyokra és egyedi gépi megtanult.

| Name (Név) | A címkézés | Leírás |
| -- |--|--|
| **Előre összeállított** <br/>[Egyéni](#prebuilt)| |  **Definíció**<br>Beépített típusok, amelyek közös fogalmait. <br><br>**List**<br/>kulcskifejezések száma, sorszámát, hőmérséklet, dimenzió, pénzt, kor, százalékos, e-mailt, URL-cím, telefonszám és a kulcsfontosságú kifejezések. <br><br>Előre összeállított entitások neve vannak fenntartva. <br><br>Az összes olyan előre összeállított entitások, az alkalmazáshoz hozzáadott rendszer adja vissza a [végpont](luis-glossary.md#endpoint) lekérdezés. További információkért lásd: [előre összeállított entitások](./luis-prebuilt-entities.md). <br/><br/>[Entitás példaválasz](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **Reguláris kifejezés**<br/>[Reguláris kifejezés](#regex)||**Definíció**<br>Egyéni reguláris kifejezés formázott nyers utterance (kifejezés) szöveget. Figyelmen kívül hagyja az esetet, és figyelmen kívül hagyja a kulturális változat.  <br><br>Ehhez az entitáshoz jó szót vagy kifejezést semmilyen változást, amely egyben egységes konzisztens módon formázott.<br><br>Reguláris kifejezések egyeztetésének helyesírás-ellenőrzésének átalakítás után alkalmazza. <br><br>Ha a reguláris kifejezés túl összetett, például sok zárójelben, Ön nem adhat hozzá a kifejezés a modellbe. <br><br>**Példa**<br>`kb[0-9]{6,}` kb123456 illeszkedik.<br/><br/>[Gyors útmutató](luis-quickstart-intents-regex-entity.md)<br>[Entitás példaválasz](luis-concept-data-extraction.md)|
| **Egyszerű** <br/>[Gép megtanult](#machine-learned) | ✔ | **Definíció**<br>Egy egyszerű entitás egy általános entitás, amely egy egyetlen fogalom ismerteti, és van megtanult a gép megtanult környezetből. Környezet például a word választott, a word elhelyezési és az utterance (kifejezés) hossza.<br/><br/>Ez az a jó entitás szavakat vagy kifejezéseket, következetesen formátuma nem, de ugyanazt jelenti. <br/><br/>[Gyors útmutató](luis-quickstart-primary-and-secondary-data.md)<br/>[Entitás példaválasz](luis-concept-data-extraction.md#simple-entity-data)|  
| **List** <br/>[Pontos egyezés](#exact-match)|| **Definíció**<br>Entitások listája és azok synoymns kapcsolódó szavakat rögzített, lezárt csoportját képviselik, a rendszer. <br><br>Előfordulhat, hogy minden lista entitás egy vagy több űrlap. Legjobban használható módszereket, amelyek ugyanazt a fogalmat változata egy ismert gyűjteményével.<br/><br/>A LUIS nem deríti fel a további értékek a lista entitásokat. Használja a **javasoljuk** funkció, amellyel új szavak javaslatokat tekintse meg az aktuális lista alapján.<br/><br>Ha egynél több lista entitás ugyanazzal az értékkel, a végpont lekérdezés minden entitás adja vissza. <br/><br/>[Gyors útmutató](luis-quickstart-intent-and-list-entity.md)<br>[Entitás példaválasz](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[Vegyes](#mixed) | ✔|**Definíció**<br>Patterns.any pedig változó hosszúságú való megjelöléséhez, ahol az entitás kezdődik és végződik csak egy minta sablon utterance (kifejezés) használja.  <br><br>**Példa**<br>Adja meg az utterance (kifejezés) keresése könyvek cím alapján, a pattern.any kibontja a teljes cím. Egy sablon utterance (kifejezés) pattern.any használatával van `Who wrote {BookTitle}[?]`.<br/><br/>[Oktatóanyag](luis-tutorial-pattern.md)<br>[Entitás példaválasz](luis-concept-data-extraction.md#composite-entity-data)|  
| **Összetett** <br/>[Gép megtanult](#machine-learned) | ✔|**Definíció**<br>Egy összetett entitást más entitásokkal, például az előre összeállított entitások, egyszerű, a regex, a hierarchikus listájában épül fel. A különálló entitások teljes entitás űrlap. <br><br>**Példa**<br>Egy összetett entitást PlaneTicketOrder nevű lehet előre összeállított gyermekentitások `number` és `ToLocation`. <br/><br/>[Oktatóanyag](luis-tutorial-composite-entity.md)<br>[Entitás példaválasz](luis-concept-data-extraction.md#composite-entity-data)|  
| **Hierarchikus** <br/>[Gép megtanult](#machine-learned) |✔ | **Definíció**<br>Egy hierarchikus entitás egy egyszerű de megismert entitások kategóriáját.<br><br>**Példa**<br>Egy hierarchikus entitása megadott `Location` gyermekkel rendelkező kifejezések `ToLocation` és `FromLocation`, minden gyermek alapján lehet meghatározni a **környezet** az utterance (kifejezés) belül. Az utterance (kifejezés) a `Book 2 tickets from Seattle to New York`, a `ToLocation` és `FromLocation` kontextusban különböző alapú őket a szavakat. <br/><br/>**Ne használjon, ha**<br>Ha egy entitás, amely rendelkezik a pontos szöveg egyezések függetlenül környezet gyermekek számára, egy lista entitást kell használnia. Ha a szülő-gyermek kapcsolatot keres a többi entitás esetében, az összetett entitást kell használnia.<br/><br/>[Gyors útmutató](luis-quickstart-intent-and-hier-entity.md)<br>[Entitás példaválasz](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
**Előre összeállított** entitások LUIS által biztosított, egyéni entitásokat is. Néhányat ezek az entitások határozzák meg a nyílt forráskódú [felismerő szöveges](https://github.com/Microsoft/Recognizers-Text) projekt. Számos [példák](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs) a támogatott kulturális környezetek /Specs könyvtárában. A megadott kulturális környezet vagy a szervezet jelenleg nem támogatott, ha járulnak hozzá a projekthez. 

<a name="machine-learned"></a>
**Gép megtanult** entitások működnek a legjobban keresztül tesztelésekor [végpont lekérdezések](luis-concept-test.md#endpoint-testing) és [végpont kimondott szöveg felülvizsgálata](luis-how-to-review-endoint-utt.md). 

<a name="regex"></a>
**Reguláris kifejezés entitások** egy reguláris kifejezés definícióját részeként biztosít a felhasználó által definiált. 

<a name="exact-match"></a>
**Pontos egyezést** entitások használata a szöveg található az entitás ahhoz, hogy egy pontosan szöveg felel meg.

<a name="mixed"></a>
**Vegyes** entitások entitás észlelési módszerek kombinációját használják.

## <a name="entity-limits"></a>Entitás korlátok
Felülvizsgálat [korlátok](luis-boundaries.md#model-boundaries) megértéséhez, hogy hány különböző típusú entitás is hozzáadhat egy modellt.

## <a name="entity-roles"></a>Entitás szerepkörök
Entitás [szerepkörök](luis-concept-roles.md) egyéni és előre összeállított entitások a alkalmazni, és csak a mintákat használják. 

## <a name="composite-vs-hierarchical-entities"></a>Hierarchikus entitások összetett vs
Összetett hierarchikus alá tartozó alanyokra és egyaránt rendelkezik szülő-gyermek típusú kapcsolatokat és megtanult gép. A machine learning lehetővé teszi, hogy a LUIS tudni, hogy az entitások különböző környezetekben (szavak elrendezésének) alapján. Összetett entitások olyan rugalmasabb, mivel lehetővé teszik különböző entitástípusok gyermekeként. Egy hierarchikus entitás gyermekek csak egyszerű entitásokat is. 

|Típus|Cél|Példa|
|--|--|--|
|Hierarchikus|Szülő-gyermek típusú, egyszerű entitások|Location.Origin=New York<br>Location.Destination=London|
|Összetett|Szülő-gyermek típusú entitások: előre elkészített, list, egyszerű, hierarchikus| szám = 3<br>lista első osztályú =<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>Az adatokat több entitás megfelelő
Ha egy szót vagy kifejezést megegyezik a több entitást, a végpont lekérdezés minden entitás adja vissza. Ha előre összeállított számú entitást és prebuild datetimeV2 is hozzáadja, és rendelkezik az utterance (kifejezés) `create meeting on 2018/03/12 for lunch with wayne`, LUIS felismeri az összes entitást, és a JSON-végpont válasz részeként entitásokat tömbjét adja vissza: 

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
Ha egy szót vagy kifejezést megegyezik a listában több entitást, a végpont lekérdezés minden egyes lista entitás adja vissza.

A lekérdezés `when is the best time to go to red rock?`, és az alkalmazás még szó `red` egynél több listában LUIS felismeri az összes entitást és a JSON-végpont válasz részeként entitásokat tömbjét adja vissza: 

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

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Ha több, mint az entitások maximális száma 

Előfordulhat, hogy szeretné használni a hierarchikus és összetett entitásokat. Hierarchikus entitások tükrözik, amely jellemzőkkel rendelkeznek, vagy egy kategória tagja entitások közötti kapcsolat. A gyermekentitások tagjai az összes, a szülő kategória. Ha például egy hierarchikus PlaneTicketClass nevű előfordulhat, hogy entitásnak EconomyClass és FirstClass gyermekentitások. A hierarchia mélységét csak egy szintjének átnyúlik.  

Összetett entitások egész részét jelölik. Ha például egy összetett PlaneTicketOrder nevű előfordulhat, hogy entitásnak gyermekentitások légitársaság, cél, DepartureCity, DepartureDate és PlaneTicketClass. Létrehozhat egy összetett entitást a már meglévő egyszerű entitások, hierarchikus entitásokat, vagy előre összeállított entitások gyermekei.  

A LUIS is biztosít a lista entitástípus van nem machine-megtudhatta, de lehetővé teszi, hogy a rögzített értékek listáját adja meg a LUIS-alkalmazás. Lásd: [LUIS határok](luis-boundaries.md) tekintse át a listában entitástípus korlátait mutató hivatkozás. 

Ha már tekinthető hierarchikus, összetett, és az entitások listája, és továbbra is több, mint a korlát, forduljon az ügyfélszolgálathoz. Ehhez gyűjtsön a rendszer részletes adatait, lépjen a [LUIS](luis-reference-regions.md#luis-website) webhelyet, és válassza ki **támogatási**. Ha az Azure-előfizetés tartalmazza a támogatási szolgálathoz, lépjen kapcsolatba [technikai Azure-támogatás](https://azure.microsoft.com/support/options/). 

## <a name="best-practices"></a>Ajánlott eljárások

Hozzon létre egy [entitás](luis-concept-entity-types.md) . Ha a hívó alkalmazás vagy robot igények egyes paraméterek vagy a művelet végrehajtásához szükséges az utterance (kifejezés) származó adatokat. Egy entitás szó vagy kifejezés az utterance (kifejezés) szükséges a kibontott--talán függvény paramétereként. 

Válassza ki a megfelelő típusú entitás hozzáadása az alkalmazásához, kell tudni, hogy hogyan felhasználók adatok vannak megadva. Minden entitás típusa megtalálható egy másik mechanizmust, például a gépi tanulási, lezárt listát vagy reguláris kifejezést használ. Ha bizonytalan, egy egyszerű entitás előtaggal kell kezdődnie, és szó vagy kifejezés összes szándék fog vonatkozni, akár a egy sem között az adatokat az összes utterances képviselő szándék címke.  

Tekintse át a végpont kimondott szöveg keresése a gyakori használati, ahol egy entitás reguláris kifejezésként azonosíthatók vagy egy szöveges pontos egyezés található rendszeres időközönként.  

A felülvizsgálat részeként fontolja jel hozzáadandó LUIS szót vagy kifejezést, amely jelentős tartományához, de nem pontos egyezések, és amelyhez LUIS nem rendelkezik egy megbízható kifejezés listáját.  

Lásd: [ajánlott eljárások](luis-concept-best-practices.md) további információt.

## <a name="next-steps"></a>További lépések

Ismerje meg a helyes kapcsolatos alapvető fogalmakat [beszédmódok](luis-concept-utterance.md). 

Lásd: [entitások hozzáadása](luis-how-to-add-entities.md) további információ entitások hozzáadása a LUIS-alkalmazás.