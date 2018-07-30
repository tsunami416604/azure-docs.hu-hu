---
title: Oktatóanyag a végponti kimondott szövegek áttekintéséről a Language Understanding (LUIS) szolgáltatásban – Azure | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan tekintheti át a végponti kimondott szövegeket a LUIS Emberi erőforrások (HR) tartományában.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: v-geberr
ms.openlocfilehash: cd8374b3804594f96212dbe741f99ba22d33a4e8
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970506"
---
# <a name="tutorial-review-endpoint-utterances"></a>Oktatóanyag: Végponti kimondott szövegek áttekintése
Ebben az oktatóanyagban az alkalmazás előrejelzéseit fejlesztheti a LUIS HTTP-végponton keresztül kapott kimondott szöveg ellenőrzésével vagy javításával. 

<!-- green checkmark -->
> [!div class="checklist"]
> * A végponti kimondott szövegek áttekintésének ismertetése 
> * LUIS-alkalmazás használata az emberi erőforrások (HR) tartományához 
> * A végpont beszédmódjainak áttekintése
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez

Ehhez a cikkhez egy ingyenes [LUIS](luis-reference-regions.md#luis-website)-fiókra van szüksége a LUIS alkalmazás létrehozásához.

## <a name="before-you-begin"></a>Előkészületek
Ha még nincs meg az Emberi erőforrások alkalmazása a [hangulat](luis-quickstart-intent-and-sentiment-analysis.md) oktatóanyagából, importálja az alkalmazást a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-sentiment-HumanResources.json) GitHub-adattárából. Ha ezt az oktatóanyagot új, importált alkalmazásként használja, be kell tanítania és közzé kell tennie az alkalmazást, majd kimondott szövegeket kell hozzáadnia a végponthoz egy [szkripttel](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js), vagy a végpontból egy böngészőben. A hozzáadandó kimondott szövegek a következők:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `review`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. 

Ha az alkalmazás minden verziójával rendelkezik, mivel elvégezte az oktatóanyag-sorozatot, azt tapasztalhatja, hogy a **Végponti kimondott szövegek áttekintése** lista a verziótól függetlenül változatlan marad. A kimondott szövegek egyetlen készletét kell áttekintenie, függetlenül attól, hogy a kimondott szöveg melyik verzióját szerkeszti vagy az alkalmazás melyik verziója lett közzétéve a végponton. 

## <a name="purpose-of-reviewing-endpoint-utterances"></a>A végponti kimondott szövegek áttekintésének célja
Az áttekintési folyamat egy másik módja annak, hogy a LUIS megismerje az alkalmazás tartományát. A LUIS kiválasztotta a kimondott szövegeket az áttekintési listában. Ez a lista a következő tulajdonságokkal rendelkezik:

* Az alkalmazásra jellemző.
* Az alkalmazás előrejelzési pontosságának fejlesztésére szolgál. 
* Rendszeresen át kell tekinteni. 

A végponti kimondott szövegek áttekintésével ellenőrizheti vagy kijavíthatja a kimondott szöveg előrejelzett szándékát. Emellett megcímkézheti azokat az egyéni entitásokat, amelyek nem lettek előrejelezve. 

## <a name="review-endpoint-utterances"></a>A végpont beszédmódjainak áttekintése

1. Győződjön meg arról, hogy az Emberi erőforrások alkalmazás a LUIS **Build** (Létrehozás) szakaszában van. Ha erre a szakaszra szeretne lépni, válassza a jobb felső menüsávon a **Build** (Létrehozás) elemet. 

    [ ![Képernyőkép a LUIS-alkalmazásról a kiemelt Létrehozás elemmel a jobb felső navigációs sávon](./media/luis-tutorial-review-endpoint-utterances/first-image.png)](./media/luis-tutorial-review-endpoint-utterances/first-image.png#lightbox)

1. Válassza a **Review endpoint utterances** (Végponti kimondott szövegek áttekintése) elemet a bal oldali navigációs menüben. A lista az **ApplyForJob** szándék szerint van szűrve. 

    [ ![A bal oldali navigációs menüben található Végponti kimondott szövegek áttekintése gomb képernyőképe](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png)](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png#lightbox)

2. Váltson az **Entities view** (Entitások nézet) nézetre a címkézett entitások megtekintéséhez. 
    
    [ ![A Végponti kimondott szövegek áttekintése képernyőképe, az Entitások nézet kapcsolójának kiemelésével](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png)](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png#lightbox)

    |Kimondott szöveg|Megfelelő szándék|Hiányzó entitások|
    |:--|:--|:--|
    |Természetes nyelvi feldolgozással kapcsolatos állást keresek|GetJobInfo|Állás – „Természetes nyelvi feldolgozás”|

    Ez a kimondott szöveg nem a megfelelő szándékban van, és a pontszáma 50%-nál alacsonyabb. Az **ApplyForJob** szándék 21 kimondott szöveggel rendelkezik, míg a **GetJobInformation** csak héttel. A végponti kimondott szövegek helyes igazítása mellett további kimondott szövegeket kell hozzáadni a **GetJobInformation** szándékhoz. Ezt a feladatot önállóan kell végrehajtania. Minden szándéknak, a **None** szándék kivételével, megközelítőleg ugyanannyi példaként használt kimondott szöveggel kell rendelkeznie. A **None** szándéknak az összes kimondott szöveg 10%-ával kell rendelkeznie az alkalmazásban. 

    A **Jogkivonatok nézetben** a kimondott szövegben lévő kék szöveg fölé viheti a mutatót az entitás előrejelzett nevének megtekintéséhez. 

3. Az `I'm looking for a job with Natual Language Processing` szándék esetében válassza ki a megfelelő szándékot (**GetJobInformation**) az **Aligned intent** (Igazított szándék) oszlopban. 

    [ ![A Végponti kimondott szövegek áttekintésének képernyőképe, a kimondott szöveg igazítása a szándékhoz](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

4. Ugyanabban a kimondott szövegben a `Natural Language Processing` entitása a keyPhrase. Ennek ehelyett **Job** (Állás) entitásnak kell lennie. Válassza a `Natural Language Processing` lehetőséget, majd a **Job** (Állás) entitást a listából.

    [ ![A Végponti kimondott szövegek áttekintésének képernyőképe, entitás címkézése kimondott szövegben](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

5. Ugyanabban a sorban válassza ki a bekarikázott pipát az **Add to aligned intent** (Hozzáadás igazított szándékhoz) oszlopban. 

    [ ![Képernyőkép: Kimondott szöveg igazításának véglegesítése a szándékban](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Ez a művelet áthelyezi a kimondott szöveget a **Review endpoint utterances** (Végponti kimondott szövegek áttekintése) helyről a **GetJobInformation** szándékba. A végponti kimondott szöveg mostantól ennek a szándéknak a példaként szolgáló kimondott szövege. 

6. Tekintse át a szándékban található további kimondott szövegeket, és lássa el őket címkével, illetve javítsa ki az **Igazított szándékot**, ha az nem megfelelő.

7. Ha minden kimondott szöveg megfelelő, jelölje be az egyes sorok elején található jelölőnégyzeteket, majd válassza az **Add selected** (Kiválasztott hozzáadása) lehetőséget a kimondott szövegek megfelelő igazításához. 

    [ ![Képernyőkép: Megmaradó kimondott szövegek véglegesítése az igazított szándékhoz](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

8. A listán már nem szabad ezeknek a kimondott szövegeknek megjelenniük. Ha további kimondott szöveg jelenik meg, haladjon végig a listán, javítsa ki a szándékokat és címkézze fel a hiányzó entitásokat, amíg a listán már nem szerepel több elem. A Filter (Szűrő) listájában válassza ki a következő szándékot, majd folytassa a kimondott szövegek javítását és az entitások címkézését. Ne feledje, hogy minden szándék utolsó lépése az **Add to aligned intent** (Hozzáadás igazított szándékhoz) lehetőség kiválasztása a kimondott szöveg sorában, vagy a jelölőnégyzet bejelölése az egyes szándékok mellett és az **Add selected** (Kiválasztott hozzáadása) lehetőség kiválasztása a tábla felett. 

    Ez egy nagyon kicsi alkalmazás. Az áttekintési folyamat csak néhány percet vesz igénybe.

## <a name="add-new-job-name-to-phrase-list"></a>Új állás hozzáadása a kifejezéslistához
Tartsa naprakészen a kifejezéslistát az újonnan felfedezett állásnevek hozzáadásával. 

1. A bal oldali navigációs menüben válassza a **Phrase lists** (Kifejezéslisták) lehetőséget.

2. Válassza a **Jobs** (Állások) kifejezéslistát.

3. Adja hozzá a `Natural Language Processing` elemet értékként, majd válassza a **Save** (Mentés) lehetőséget. 

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Amíg nincs betanítva, a LUIS nem ismeri a módosításokat. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez
Ahhoz, hogy LUIS-alkalmazás frissített modelljét kapja meg egy csevegőrobotban vagy más alkalmazásban, közzé kell tennie az alkalmazást. 

1. A LUIS-webhely jobb felső részén válassza a **Publish** (Közzététel) lehetőséget. 

2. Ha importálta ezt az alkalmazást, válassza a **Sentiment analysis** (Hangulatelemzés) elemet. 

3. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

4. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="query-the-endpoint-with-an-utterance"></a>A kimondott szöveget tartalmazó végpont lekérdezése
Próbáljon ki egy, a javítotthoz hasonló kimondott szöveget. 

1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Are there any natural language processing jobs in my department right now?`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. 

```JSON
{
  "query": "are there any natural language processing jobs in my department right now?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.9247605
  },
  "intents": [
    {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    {
      "intent": "ApplyForJob",
      "score": 0.129989788
    },
    {
      "intent": "FindForm",
      "score": 0.006438211
    },
    {
      "intent": "EmployeeFeedback",
      "score": 0.00408575451
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00194211153
    },
    {
      "intent": "None",
      "score": 0.00166400627
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00118593348
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0007885918
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0006373631
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.0005980781
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.719905E-05
    }
  ],
  "entities": [
    {
      "entity": "right now",
      "type": "builtin.datetimeV2.datetime",
      "startIndex": 64,
      "endIndex": 72,
      "resolution": {
        "values": [
          {
            "timex": "PRESENT_REF",
            "type": "datetime",
            "value": "2018-07-05 15:23:18"
          }
        ]
      }
    },
    {
      "entity": "natural language processing",
      "type": "Job",
      "startIndex": 14,
      "endIndex": 40,
      "score": 0.9869922
    },
    {
      "entity": "natural language processing jobs",
      "type": "builtin.keyPhrase",
      "startIndex": 14,
      "endIndex": 45
    },
    {
      "entity": "department",
      "type": "builtin.keyPhrase",
      "startIndex": 53,
      "endIndex": 62
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8251864
  }
}
}
```

A megfelelő szándék magas pontszámmal lett előrejelezve, és a **Job** entitás a következőként lett észlelve: `natural language processing`. 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Az áttekintés helyettesíthető további kimondott szövegek hozzáadásával? 
Felmerülhet a kérdés, hogy miért ne adhatna hozzá további példaként szolgáló kimondott szövegeket. Mi a végponti kimondott szövegek áttekintésének célja? Egy valós LUIS-alkalmazásban a végponti kimondott szövegek a felhasználóktól származnak, Ön által még nem használt szóhasználattal és elrendezéssel. Ha ugyanazt a szóhasználatot és elrendezést alkalmazta volna, az eredeti előrejelzés nagyobb százalékos értékkel rendelkezne. 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Miért szerepel a felső szándék a kimondott szövegek listáján? 
Néhány végponti kimondott szöveg nagy százalékos értékkel szerepel az áttekintési listán. Ezeknek a kimondott szövegeknek az áttekintésére és ellenőrzésére ugyanúgy szükség van. Azért szerepelnek a listán, mert a következő legnagyobb pontszámú szándék a legfelső szándék pontszámához túl közeli pontszámmal rendelkezik. 

## <a name="what-has-this-tutorial-accomplished"></a>Mi valósult meg ebben az oktatóanyagban?
A végpontról származó kimondott szövegek áttekintésével javult az alkalmazás előrejelzési pontossága. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Válassza a **My apps** (Saját alkalmazások) elemet a bal oldali menüben. Válassza az alkalmazáslistában az alkalmazás neve mellett jobbra található három pontot (**...**), majd a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Útmutató a minták használatához](luis-tutorial-pattern.md)
