---
title: Oktatóanyag – Helyadatokat visszaadó LUIS-alkalmazás létrehozása – Azure | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egyszerű LUIS-alkalmazást szándékok és egy hierarchikus entitás használatával az adatok kinyerésére.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/04/2018
ms.author: v-geberr
ms.openlocfilehash: babfc2f82e17f3745af1d940df89763170a002bd
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929586"
---
# <a name="tutorial-5-add-hierarchical-entity"></a>Oktatóanyag: 5. Hierarchikus entitás hozzáadása
Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan lehet kapcsolódó adatrészleteket keresni kontextus alapján. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Hierarchikus entitások és környezetfüggő tanult gyermekek megismerése 
> * A LUIS-alkalmazás használata az emberi erőforrások (HR) tartományban 
> * Forrással és célgyermekkel rendelkező helyhierarchikus entitás hozzáadása
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz, többek között a hierarchikus gyermekek megtekintéséhez 

Ehhez a cikkhez egy ingyenes [LUIS](luis-reference-regions.md#luis-website)-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="before-you-begin"></a>Előkészületek
Ha még nincs meg az Emberi erőforrások alkalmazása a [lista entitások](luis-quickstart-intent-and-list-entity.md) oktatóanyagából, [importálja](luis-how-to-start-new-app.md#import-new-app) a JSON-t egy új alkalmazásba a [LUIS](luis-reference-regions.md#luis-website) webhelyén. Az importálandó alkalmazás a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-list-HumanResources.json) GitHub-adattárban található.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `hier`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. 

## <a name="purpose-of-the-app-with-this-entity"></a>Az ezzel az entitással rendelkező alkalmazás célja
Ez az alkalmazás meghatározza, hova lesz áthelyezve egy alkalmazott az indulási helyről (épület és iroda) az érkezési helyre (épület és iroda). A hierarchikus entitás segítségével határozza meg a kimondott szövegben található helyeket. 

Ez a hierarchikus entitás jó választás az ilyen típusú adatok esetén, mivel a két adatrészlet:

* Kapcsolódik egymáshoz a kimondott szövegkörnyezetben.
* Adott szavakat használ az egyes helyek jelzéséhez. Ilyen szavak például: innen/oda, elhagyja/felé tart, távolodik/közeledik.
* Mindkét hely általában egyazon kimondott szövegrészletben található. 

A **hierarchikus** entitás célja kapcsolódó adatok keresése kontextus alapján a kimondott szövegben. Vegyük például a következő kimondott szöveget:

```JSON
mv Jill Jones from a-2349 to b-1298
```
A kimondott szövegben két hely van megadva: `a-2349` és `b-1298`. Tegyük fel, hogy a betű megfelel egy épület nevének, a szám pedig az irodát jelzi az épületen belül. Ezért érdemes a kettőt egy hierarchikus entitás, a `Locations` entitás gyermekeiként összerendelni, mert mindkét információt a kimondott szövegből kell kinyerni, és a két információ kapcsolódik egymáshoz. 
 
Ha egy hierarchikus entitásnak csak az egyik gyermeke (az indulás vagy az érkezés) van jelen, az alkalmazás akkor is kinyeri. Egy vagy néhány gyermek kinyeréséhez nem szükséges az összes gyermeket megtalálni. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Előre összeállított szám entitás eltávolítása az alkalmazásból
A teljes kimondott szöveg megjelenítéséhez és a hierarchikus gyermek megjelöléséhez ideiglenesen távolítsa el az előre összeállított szám entitást.

1. Győződjön meg arról, hogy az Emberi erőforrások alkalmazás a LUIS **Build** (Létrehozás) szakaszában van. Ha erre a szakaszra szeretne lépni, válassza a jobb felső menüsávon a **Build** (Létrehozás) elemet. 

    [ ![Képernyőfelvétel a LUIS-alkalmazásról a kiemelt Létrehozás elemmel a jobb felső navigációs sávon](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png)](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png#lightbox)

2. Válassza az **Entities** (Entitások) elemet a bal oldali menüben.

    [ ![Képernyőkép a LUIS-alkalmazásról a kiemelt Szándékok gombbal a bal menüben](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png#lightbox)


3. Kattintson a listában lévő szám entitás jobb oldalán található három pontra (***...***). Válassza a **Törlés** elemet. 

    [ ![A LUIS-alkalmazás entitások listaoldalának képernyőképe, a törlés gomb kiemelve az előre összeállított szám entitásnál](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png)](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png#lightbox)


## <a name="add-utterances-to-moveemployee-intent"></a>Kimondott szövegek hozzáadása a MoveEmployee szándékhoz

1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

    [ ![Képernyőfelvétel a LUIS-alkalmazásról a kiemelt Szándékok elemmel a bal menüben](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png#lightbox)

2. A szándékok listájából válassza ki a **MoveEmployee** elemet.

    [ ![Képernyőfelvétel a LUIS-alkalmazásról a kiemelt MoveEmployee szándékkal a bal menüben](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png)](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png#lightbox)

3. Vegye fel az alábbi kimondott szövegpéldákat:

    |Példák kimondott szövegekre|
    |--|
    |John W. Smith áthelyezése **ide**: a-2345|
    |Jill Jones átirányítása **ide**: b-3499|
    |x23456 áthelyezésének megszervezése **innen**: hh-2345, **ide**: e-0234|
    |Papírmunka megkezdése a következő beállításához: x12345 **elhagyja** a következőt: a-3459, és az f-34567 **felé tart**|
    |425-555-0000 áthelyezése: **távolodik** a következőtől: g-2323, és **közeledik** hh-2345 felé|

    A [lista entitás](luis-quickstart-intent-and-list-entity.md) oktatóanyagban egy alkalmazott kijelölhető név, e-mail-cím, telefonmellék, mobiltelefonszám, vagy USA-beli szövetségi társadalombiztosítási szám alapján. Ezeket az alkalmazotti számokat a kimondott szövegekben használja a rendszer. Az előző kimondott szövegpélda különböző módokat tartalmaz az indulási és érkezési hely jelzésére, amelyek félkövér betűvel vannak kiemelve. Néhány kimondott szöveg szándékosan csak érkezési hellyel rendelkezik. Ez segít megérteni a LUIS-nak, hogyan helyezkednek el ezek a helyek a kimondott szövegben, amikor az indulási hely nincs megadva.

    [![Képernyőkép a LUIS-ról, új kimondott szövegek a MoveEmployee szándékban](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)
     

## <a name="create-a-location-entity"></a>Hely entitás létrehozása
A LUIS-nak meg kell értenie, hogy a hely pontosan mit jelent. Ez a kimondott szövegben található indulási és érkezési hely címkézésével lehetséges. Ha a kimondott szöveget jogkivonat (nyers) nézetben szeretné megtekinteni, válassza az **Entities View** (Entitások nézet) címkével ellátott, a kimondott szövegek fölötti sávban található kapcsolót. Miután átkapcsolta a kapcsolót, a vezérlő **Tokens View** (Jogkivonatok nézet) címkével lesz ellátva.

1. A(z) `Displace 425-555-0000 away from g-2323 toward hh-2345` kimondott szövegben válassza ki a következő szót: `g-2323`. Megjelenik egy legördülő menü egy szövegmezővel a tetején. Adja meg az entitás nevét (`Locations`) a szövegmezőben, majd a legördülő menüben válassza a **Create new entity** (Új entitás létrehozása) lehetőséget. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Képernyőkép: Új entitás létrehozása a szándék lapon")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. Az előugró ablakban válassza a **Hierarchikus** entitástípust, gyermekentitásokként a következőkkel: `Origin` és `Destination`. Válassza a **Done** (Kész) lehetőséget.

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Képernyőkép: Entitás létrehozása felugró párbeszédpanel új Hely entitáshoz")

3. A `g-2323` címkéje `Locations`, mivel a LUIS nem tudja, hogy a kifejezés a kiindulási vagy az érkezési hely-e, vagy egyik sem. Válassza ki a `g-2323`, majd a **Locations** (Helyek) elemet, azután pedig a jobb oldali menüben a `Origin` lehetőséget.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Az entitáscímkézés előugró párbeszédpanel képernyőképe, a hely entitásgyermek módosításához")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Címkézze meg az egyéb helyeket a többi kimondott szövegben: a kimondott szövegben válassza ki az épületet és az irodát, majd válassza ki a Locations (Helyek) elemet, majd a jobb oldali menüben válassza az `Origin` vagy `Destination` lehetőséget. Ha minden hely fel lett címkézve, a **Tokens View** (Jogkivonatok nézet) kimondott szövegei egy mintára kezdenek hasonlítani. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Képernyőkép: Helyek entitás felcímkézve a kimondott szövegekben")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Előre összeállított szám entitás hozzáadása az alkalmazáshoz
Adja hozzá ismét az előre összeállított szám entitást az alkalmazáshoz.

1. Válassza az **Entities** (Entitások) elemet a bal oldali navigációs menüben.

    [ ![Képernyőkép: az Entitások gomb kiemelve a bal oldali navigációs menüben](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png#lightbox)

2. Válassza a **Manage prebuilt entities** (Előre összeállított entitások kezelése) gombot.

    [ ![Entitások lista képernyőképe, Előre összeállított entitások gomb kiemelve](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png#lightbox)

3. Az előre összeállított entitások listájából válassza a **number** (szám) entitást, majd kattintson a **Done** (Kész) gombra.

    ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Amíg nincs betanítva, a LUIS nem ismeri fel a szándékok és entitások (a modell) módosításait. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

    ![Az alkalmazás betanítása](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    ![A betanítás sikeres volt](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez
Ahhoz, hogy LUIS-előrejelzéseket kaphasson egy csevegőrobotban vagy más alkalmazásban, közzé kell tennie az alkalmazást. 

1. A LUIS-webhely jobb felső részén válassza a **Publish** (Közzététel) lehetőséget. 

2. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "A Publish (Közzététel) lap képernyőképe a kiemelt Publish to production slot (Közzététel éles termelési helyre) elemmel")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel
1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "A Publish (Közzététel) lap képernyőképe a kiemelt végponti URL-címmel")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Lépjen az URL-cím végéhez a címsorban, és írja be a következőt: `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő szándékot kell visszaadnia a kinyert hierarchikus entitással: `MoveEmployee`.

```JSON
{
  "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
  "topScoringIntent": {
    "intent": "MoveEmployee",
    "score": 0.9966052
  },
  "intents": [
    {
      "intent": "MoveEmployee",
      "score": 0.9966052
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.0325253047
    },
    {
      "intent": "FindForm",
      "score": 0.006137873
    },
    {
      "intent": "GetJobInformation",
      "score": 0.00462633232
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00415637763
    },
    {
      "intent": "ApplyForJob",
      "score": 0.00382325822
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00249120337
    },
    {
      "intent": "None",
      "score": 0.00130756292
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00119622645
    },
    {
      "intent": "Utilities.Confirm",
      "score": 1.26910036E-05
    }
  ],
  "entities": [
    {
      "entity": "jill - jones @ mycompany . com",
      "type": "Employee",
      "startIndex": 18,
      "endIndex": 41,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "x - 2345",
      "type": "Locations::Origin",
      "startIndex": 48,
      "endIndex": 53,
      "score": 0.8520272
    },
    {
      "entity": "g - 23456",
      "type": "Locations::Destination",
      "startIndex": 58,
      "endIndex": 64,
      "score": 0.974032
    },
    {
      "entity": "-2345",
      "type": "builtin.number",
      "startIndex": 49,
      "endIndex": 53,
      "resolution": {
        "value": "-2345"
      }
    },
    {
      "entity": "-23456",
      "type": "builtin.number",
      "startIndex": 59,
      "endIndex": 64,
      "resolution": {
        "value": "-23456"
      }
    }
  ]
}
```

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Használhat reguláris kifejezést minden helyhez?
Igen, hozza létre a reguláris kifejezést indulási és érkezési szerepkörökkel, és használja egy mintában.

Az ebben a példában lévő helyek, például az `a-1234`, egy adott formátumot követnek: egy vagy két betű, kötőjellel elválasztva, ezután egy 4 vagy 5 számból álló sorozat. Ez az adat leírható reguláris kifejezés entitásként az egyes helyekhez kapcsolódó szerepkörrel. A szerepkörök elérhetők a mintákhoz. Ezen kimondott szövegek alapján létrehozhat mintákat, majd létrehozhat egy reguláris kifejezést a helyformátumhoz, és hozzáadhatja a mintákhoz. <!-- Go to this tutorial to see how that is done -->

## <a name="what-has-this-luis-app-accomplished"></a>Milyen műveleteket végzett el a LUIS-alkalmazás?
Az alkalmazás mindössze néhány szándékkal és egy hierarchikus entitással azonosított egy természetes nyelvi lekérdezési szándékot, és visszaadta a kinyert adatokat. 

A csevegőrobot már elég információval rendelkezik az elsődleges művelet (`MoveEmployee`) és a kimondott szövegben szereplő helyadatok meghatározásához. 

## <a name="where-is-this-luis-data-used"></a>Hol vannak használatban ezek a LUIS-adatok? 
A LUIS végzett ezzel a kéréssel. A hívó alkalmazás, például egy csevegőrobot, felhasználhatja a topScoringIntent eredményt és az entitás adatait a következő lépés végrehajtásához. A LUIS nem végzi el ezt a programozható munkát a csevegőrobotnak vagy a hívó alkalmazásnak. A LUIS csak azt határozza meg, hogy mi a felhasználó szándéka. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza az alkalmazáslistában az alkalmazás neve mellett jobbra található három pontot (***...***), majd a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Ismerkedés az összetett entitások hozzáadásának módjával](luis-tutorial-composite-entity.md) 