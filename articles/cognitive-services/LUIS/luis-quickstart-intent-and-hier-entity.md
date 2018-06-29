---
title: Oktatóanyag – Helyadatokat visszaadó LUIS-alkalmazás létrehozása – Azure | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egyszerű LUIS-alkalmazást szándékok és egy hierarchikus entitás használatával az adatok kinyerésére.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: v-geberr
ms.openlocfilehash: 2547407126943161ba604fa2f5e80b9186cae57e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266498"
---
# <a name="tutorial-create-app-that-uses-hierarchical-entity"></a>Oktatóanyag: Hierarchikus entitást használó alkalmazás létrehozása
Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan lehet kapcsolódó adatrészleteket keresni kontextus alapján. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Hierarchikus entitások és környezetfüggő tanult gyermekek megismerése 
> * Új LUIS-alkalmazás létrehozása egy utazási tartományhoz Bookflight szándékkal
> * _None_ szándék és például szolgáló kimondott szövegek hozzáadása
> * Forrással és célgyermekkel rendelkező helyhierarchikus entitás hozzáadása
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz, többek között a hierarchikus gyermekek megtekintéséhez 

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="purpose-of-the-app-with-this-entity"></a>Az ezzel az entitással rendelkező alkalmazás célja
Ez az alkalmazás határozza meg, hogy a felhasználó szeretne-e repülőjegyet foglalni. A hierarchikus entitás segítségével határozza meg a helyeket, azaz az indulási és az érkezési várost a felhasználó által megadott szövegben. 

Ez a hierarchikus entitás jó választás az ilyen típusú adatok esetén, mivel a két adatrészlet:

* Hely, általában városnévvel vagy repülőtérkóddal kifejezve.
* Általában egyedi nyelvtani szerkezetek segítségével határozza meg, melyik az indulási és melyik az érkezési hely. Ezek a szerkezetek lehetnek: ~ba/be/ra/re, érkezés, ~ból/ből/ról/ről, indulás.
* Mindkét hely általában egyazon kimondott szövegrészletben található. 

A **hierarchikus** entitás célja kapcsolódó adatok keresése kontextus alapján a kimondott szövegben. Vegyük például a következő kimondott szöveget:

```JSON
1 ticket from Seattle to Cairo`
```

A kimondott szövegben két hely van megadva. Az egyik az indulási (Budapest), a másik az érkezési (Kairó) város. Mindkét város szükséges a repülőjegy-foglaláshoz. Bár ezek egyszerű entitások használatával is megtalálhatók, kapcsolódnak egymáshoz és gyakran szerepelnek egyazon kimondott szövegben. Ezért érdemes a kettőt egy hierarchikus entitás, a **„Hely”** entitás gyermekeiként összerendelni. 

Mivel ezek gép által tanult entitások, az alkalmazáshoz meg kell adni példaszövegeket, amelyekben az indulási és az érkezési városok fel vannak címkézve. Ez megtanítja a LUIS-nak, hogy az entitások hol találhatók a kimondott szövegben, milyen hosszúak, és milyen szavak találhatók körülöttük. 

## <a name="app-intents"></a>Az alkalmazás szándékai
A szándékok a felhasználói kívánságok kategóriái. Ez az alkalmazás két szándékkal rendelkezik: BookFlight és None. A [None](luis-concept-intent.md#none-intent-is-fallback-for-app) szándékkal jelezhető minden olyan kívánság, amely az alkalmazás körén kívül esik.  

## <a name="hierarchical-entity-is-contextually-learned"></a>A hierarchikus entitás tanulása környezetfüggő módon történik 
Az entitás célja, hogy megtalálja és kategorizálja a kimondott szöveg egyes részeit. A [hierarchikus](luis-concept-entity-types.md) entitások az alkalmazási környezeten alapuló szülő–gyermek entitások. A valós személyek a következők használata alapján képesek azonosítani az indulási és érkezési városokat a kimondott szövegekben: `to` és `from`. Ez egy példa a környezetfüggő használatra.  

Az utazási alkalmazásunk esetében a LUIS kinyeri az indulási és érkezési városokat, hogy azok alapján létre lehessen hozni és kitölteni egy szabványos foglalást. A LUIS lehetővé teszi, hogy a kimondott szöveg változatokat, rövidítéseket és szlenget tartalmazzon. 

Egyszerű példák felhasználók által kimondott szövegekre:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Researve a seat from New York to Paris on the first of April
```

A kimondott szövegek rövidített vagy szlenges verziói:

```
LHR tomorrow
SEA to NYC next Monday
LA to MCO spring break
```
 
A hierarchikus entitás összerendeli az indulási és érkezési helyeket. Ha egy hierarchikus entitásnak csak az egyik gyermeke (az indulás vagy az érkezés) van jelen, az alkalmazás akkor is kinyeri. Egy vagy néhány gyermek kinyeréséhez nem szükséges az összes gyermeket megtalálni. 

## <a name="what-luis-does"></a>A LUIS által elvégzett feladat
A LUIS akkor van készen, ha azonosította, [kinyerte](luis-concept-data-extraction.md#list-entity-data), és a [végpontról](https://aka.ms/luis-endpoint-apis) JSON-formátumban visszaadta a kimondott szöveg szándékát és entitásait. A hívó alkalmazás vagy a csevegőrobot fogadja a JSON-választ és teljesíti a kérést a kialakításának megfelelő módon. 

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
1. Jelentkezzen be a [LUIS][LUIS] webhelyére. Győződjön meg arról, hogy abban a [régióban][LUIS-regions] jelentkezik be, ahol közzé szeretné tenni a LUIS-végpontokat.

2. A [LUIS][LUIS] webhelyén válassza a **Create new app** (Új alkalmazás létrehozása) lehetőséget.  

    [![](media/luis-quickstart-intent-and-hier-entity/app-list.png "Alkalmazáslistát tartalmazó oldal képernyőképe")](media/luis-quickstart-intent-and-hier-entity/app-list.png#lightbox)

3. Az előugró párbeszédpanelen írja be a következő nevet: `MyTravelApp`. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-app.png "A Create new app (Új alkalmazás létrehozása) felugró párbeszédpanel képernyőképe")](media/luis-quickstart-intent-and-hier-entity/create-new-app.png#lightbox)

4. Amikor a folyamat befejeződött, az alkalmazás megjeleníti az **Intents** (Szándékok) lapot és rajta a **None** szándékot. 

    [![](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png "A szándékok listájának képernyőképe, csak a None szándékkal")](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Új szándék létrehozása

1. Az **Intents** (Szándékok) lapon válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png "A szándékok listájának képernyőképe az új szándék létrehozására szolgáló gomb kiemelésével")](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png#lightbox)

2. Adja meg az új szándék nevét: `BookFlight`. Ezt a szándékot kell kiválasztani, ha a felhasználó repülőjegyet szeretne foglalni.

    A szándék létrehozásával azt az elsődleges információkategóriát is létrehozza, amelyet azonosítani szeretne. A kategória elnevezése lehetővé teszi, hogy a LUIS lekérdezési eredményeit használó egyéb alkalmazások is használják ezt a kategórianevet a megfelelő válasz megkereséséhez vagy a megfelelő művelet végrehajtásához. A LUIS nem válaszol ezekre a kérdésekre, csak azonosítja a természetes nyelvezeten kért információ típusát. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png "A Create new intent (Új szándék létrehozása) felugró párbeszédpanel képernyőképe")](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png#lightbox)

3. Az `BookFlight` szándékhoz számos olyan kimondott szöveget is hozzáadhat, amelyet a felhasználók várhatóan használni fognak, például:

    | Példák kimondott szövegekre|
    |--|
    |Szeretnék 2 jegyet foglalni jövő hétfőre Budapestről Kairóba|
    |Szeretnék egy jegyet Londonba holnapra|
    |Lefoglalnék 4 jegyet Párizsból Londonba április 1-re|

    [![](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png "A kimondott szöveg megadásának képernyőképe a BookFlight Szándék oldalán")](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Kimondott szövegek hozzáadása a None szándékhoz

A LUIS-alkalmazásnak jelenleg nincsenek kimondott szövegei a **None** szándékhoz. Meg kell adni olyan kimondott szövegeket, amelyekre nem szeretné, hogy az alkalmazás válaszoljon, így a **None** szándékban is lenniük kell kimondott szövegeknek. Ne hagyja üresen a szándékot. 

1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget. 

    [![](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png "A BookFlight Szándék oldalának képernyőképe a kiemelt Intents (Szándékok) gombbal")](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png#lightbox)

2. Válassza ki a **None** szándékot. Adjon meg három olyan kimondott szöveget, amelyet a felhasználó megadhat, de az alkalmazás nem fog foglalkozni velük:

    | Példák kimondott szövegekre|
    |--|
    |Mégse|
    |Viszlát|
    |Mi történik?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Amikor a kimondott szövegből a None szándékra lehet következtetni
Ha a LUIS-t hívó alkalmazásban, például egy csevegőrobotban, a LUIS a **None** szándékot adja vissza egy kimondott szöveghez, a robot meg tudja kérdezni, hogy a felhasználó be szeretné-e fejezni a beszélgetést. A robot további iránymutatásokat is adhat a beszélgetés folytatásához, ha a felhasználó azt szeretné. 

Az entitások a **None** szándék esetében is működnek. Ha a **None** a legmagasabb pontszámú szándék, de egy kinyert entitás a csevegőrobot számára értelmezhető, a csevegőrobot folytathatja a kommunikációt egy, az ügyfél szándékát feltáró kérdéssel. 

## <a name="create-a-location-entity-from-the-intent-page"></a>Hely entitás létrehozása a Szándék oldalon
Most, hogy a két szándéknak vannak kimondott szövegei, a LUIS-nak meg kell értenie, hogy a hely pontosan mit takar. Lépjen vissza a `BookFlight` szándékra, és címkézze (jelölje) meg a városnevet egy kimondott szövegben a következő lépések alapján:

1. Térjen vissza az `BookFlight` szándékhoz a bal oldali navigációs menü **Intents** (Szándékok) menüpontjának kiválasztásával.

2. A szándékok listájáról válassza ki a következőt: `BookFlight`.

3. A(z) `Book 2 flights from Seattle to Cairo next Monday` kimondott szövegben válassza ki a következő szót: `Seattle`. Az új entitás létrehozásához megjelenik egy legördülő menü, szövegmezővel a tetején. Adja meg az entitás nevét (`Location`) a szövegmezőben, majd a legördülő menüben válassza a **Create new entity** (Új entitás létrehozása) lehetőséget. 

    [![](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png "A BookFlight Szándék oldalának képernyőképe: új entitás létrehozása a kijelölt szöveg alapján")](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png#lightbox)

4. Az előugró ablakban válassza a **Hierarchikus** entitástípust, gyermekentitásokként a következőkkel: `Origin` és `Destination`. Válassza a **Done** (Kész) lehetőséget.

    [![](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png "Képernyőkép: Entitás létrehozása felugró párbeszédpanel új Hely entitáshoz")](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png#lightbox)

    A `Seattle` címkéje `Location`, mivel a LUIS nem tudja, hogy a kifejezés a kiindulási vagy az érkezési hely-e, vagy egyik sem. Válassza ki a `Seattle`, majd a Hely elemet, azután pedig a jobb oldali menüben a `Origin` lehetőséget.

5. Most, hogy létrehozta az entitást és felcímkézett egy kimondott szöveget, címkézze fel a többi várost is – ehhez válassza ki a város nevét, majd a Hely elemet, azután pedig a jobb oldali menüben a `Origin` vagy `Destination` lehetőséget.

    [![](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png "A BookFlight entitás képernyőképe: a kimondott szöveg kijelölve az entitás kiválasztásához")](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png#lightbox)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Amíg nincs betanítva, a LUIS nem ismeri fel a szándékok és entitások (a modell) módosításait. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

    ![Az alkalmazás betanítása](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    ![A betanítás sikeres volt](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez
Ahhoz, hogy LUIS-előrejelzéseket kaphasson egy csevegőrobotban vagy más alkalmazásban, közzé kell tennie az alkalmazást. 

1. A LUIS-webhely jobb felső részén válassza a **Publish** (Közzététel) lehetőséget. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish.png "A BookFlight Szándék oldalának képernyőképe a kiemelt Publish (Közzététel) gombbal")](media/luis-quickstart-intent-and-hier-entity/publish.png#lightbox)

2. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "A Publish (Közzététel) lap képernyőképe a kiemelt Publish to production slot (Közzététel éles termelési helyre) elemmel")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel
1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "A Publish (Közzététel) lap képernyőképe a kiemelt végponti URL-címmel")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Lépjen az URL-cím végéhez, és írja be a következőt: `1 ticket to Portland on Friday`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **q**uery. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő szándékot kell visszaadnia a kinyert hierarchikus entitással: `BookFlight`.

```
{
  "query": "1 ticket to Portland on Friday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9998226
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.9998226
    },
    {
      "intent": "None",
      "score": 0.221926212
    }
  ],
  "entities": [
    {
      "entity": "portland",
      "type": "Location::Destination",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.564448953
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Milyen műveleteket végzett el a LUIS-alkalmazás?
Az alkalmazás mindössze két szándékkal és egy hierarchikus entitással azonosított egy természetes nyelvi lekérdezési szándékot, és visszaadta a kinyert adatokat. 

A csevegőrobot már elég információval rendelkezik az elsődleges művelet (`BookFlight`) és a kimondott szövegben szereplő helyadatok meghatározásához. 

## <a name="where-is-this-luis-data-used"></a>Hol vannak használatban ezek a LUIS-adatok? 
A LUIS végzett ezzel a kéréssel. A hívó alkalmazás, például egy csevegőrobot, felhasználhatja a topScoringIntent eredményt és az entitás adatait a következő lépés végrehajtásához. A LUIS nem végzi el ezt a programozható munkát a csevegőrobotnak vagy a hívó alkalmazásnak. A LUIS csak azt határozza meg, hogy mi a felhasználó szándéka. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza az alkalmazáslistában az alkalmazás neve mellett jobbra található három pontot (...), majd a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Ismerkedés a listaentitások hozzáadásának módjával](luis-quickstart-intent-and-list-entity.md) 

**Number** (szám) [előre összeállított entitás](luis-how-to-add-entities.md#add-prebuilt-entity) megadása a szám kinyeréséhez. 

A dátuminformációk kinyeréséhez adja hozzá a **datetimeV2** [előre összeállított entitást](luis-how-to-add-entities.md#add-prebuilt-entity).


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
