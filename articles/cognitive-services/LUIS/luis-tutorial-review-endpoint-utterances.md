---
title: '1. oktatóanyag: A végponti kimondott szövegek áttekintése aktív tanulással'
titleSuffix: Azure Cognitive Services
description: Fejlesztheti az alkalmazás előrejelzéseit a LUIS által nem ismert LUIS HTTP-végponton keresztül kapott kimondott szövegek ellenőrzésével vagy javításával. Bizonyos kimondott szövegek esetében a szándékot, míg más kimondott szövegek esetében az entitást kell ellenőrizni. A végponti kimondott szövegek áttekintésének az ütemezett LUIS-karbantartás szerves részét kell képeznie.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 8fbe856826770eb38cd4a6d921c1b0f4eacf9c2a
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426961"
---
# <a name="tutorial-1-fix-unsure-predictions"></a>1. oktatóanyag: Bizonytalan előrejelzések javítása
Ebben az oktatóanyagban az alkalmazás előrejelzéseit fejlesztheti a LUIS által nem ismert LUIS HTTP-végponton keresztül kapott kimondott szövegek ellenőrzésével vagy javításával. Bizonyos kimondott szövegek esetében a szándékot, míg más kimondott szövegek esetében az entitást kell ellenőrizni. A végponti kimondott szövegek áttekintésének az ütemezett LUIS-karbantartás szerves részét kell képeznie. 

Az áttekintési folyamat egy másik módja annak, hogy a LUIS megismerje az alkalmazás tartományát. A LUIS kiválasztotta az áttekintési listában megjelenő kimondott szövegeket. Ez a lista a következő tulajdonságokkal rendelkezik:

* Az alkalmazásra jellemző.
* Az alkalmazás előrejelzési pontosságának fejlesztésére szolgál. 
* Rendszeresen át kell tekinteni. 

A végponti kimondott szövegek áttekintésével ellenőrizheti vagy kijavíthatja a kimondott szöveg előrejelzett szándékát. Emellett megcímkézheti azokat az egyéni entitásokat, amelyek nem lettek előrejelezve vagy helytelenül lettek előrejelezve. 

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Meglévő oktatóalkalmazás használata
> * A végpont beszédmódjainak áttekintése
> * Kifejezéslista frissítése
> * Alkalmazás betanítása
> * Alkalmazás közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata

Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Amennyiben nem rendelkezik az előző oktatóanyagból származó EmberiErőforrások alkalmazással, kövesse a következő lépéseket:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `review` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

    Ha ezt az oktatóanyagot új, importált alkalmazásként használja, be kell tanítania és közzé kell tennie az alkalmazást, majd kimondott szövegeket kell hozzáadnia a végponthoz egy [szkripttel](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js), vagy a végpontból egy böngészőben. A hozzáadandó kimondott szövegek a következők:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

    Ha az alkalmazás minden verziójával rendelkezik, mivel elvégezte az oktatóanyag-sorozatot, azt tapasztalhatja, hogy a **Végponti kimondott szövegek áttekintése** lista a verziótól függetlenül változatlan marad. A kimondott szövegek egyetlen készletét kell áttekintenie, függetlenül attól, hogy melyik verziót szerkeszti, vagy az alkalmazás melyik verziója lett közzétéve a végponton. 

## <a name="review-endpoint-utterances"></a>A végpont beszédmódjainak áttekintése

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Válassza a **Review endpoint utterances** (Végponti kimondott szövegek áttekintése) elemet a bal oldali navigációs menüben. A lista az **ApplyForJob** szándék szerint van szűrve. 

    [ ![A bal oldali navigációs menüben található Végponti kimondott szövegek áttekintése gomb képernyőképe](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

3. Váltson az **Entities view** (Entitások nézet) nézetre a címkézett entitások megtekintéséhez. 
    
    [ ![A Végponti kimondott szövegek áttekintése képernyőképe, az Entitások nézet kapcsolójának kiemelésével](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)

    |Kimondott szöveg|Megfelelő szándék|Hiányzó entitások|
    |:--|:--|:--|
    |Természetes nyelvi feldolgozással kapcsolatos állást keresek|GetJobInfo|Állás – „Természetes nyelvi feldolgozás”|

    Ez a kimondott szöveg nem a megfelelő szándékban van, és a pontszáma 50%-nál alacsonyabb. Az **ApplyForJob** szándék 21 kimondott szöveggel rendelkezik, míg a **GetJobInformation** csak héttel. A végponti kimondott szövegek helyes igazítása mellett további kimondott szövegeket kell hozzáadni a **GetJobInformation** szándékhoz. Ezt a feladatot önállóan kell végrehajtania. Minden szándéknak, a **None** szándék kivételével, megközelítőleg ugyanannyi példaként használt kimondott szöveggel kell rendelkeznie. A **None** szándéknak az összes kimondott szöveg 10%-ával kell rendelkeznie az alkalmazásban. 

4. Az `I'm looking for a job with Natual Language Processing` szándék esetében válassza ki a megfelelő szándékot (**GetJobInformation**) az **Aligned intent** (Igazított szándék) oszlopban. 

    [ ![A Végponti kimondott szövegek áttekintésének képernyőképe, a kimondott szöveg igazítása a szándékhoz](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

5. Ugyanabban a kimondott szövegben a `Natural Language Processing` entitása a keyPhrase. Ennek ehelyett **Job** (Állás) entitásnak kell lennie. Válassza a `Natural Language Processing` lehetőséget, majd a **Job** (Állás) entitást a listából.

    [ ![A Végponti kimondott szövegek áttekintésének képernyőképe, entitás címkézése kimondott szövegben](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

6. Ugyanabban a sorban válassza ki a bekarikázott pipát az **Add to aligned intent** (Hozzáadás igazított szándékhoz) oszlopban. 

    [ ![Képernyőkép: Kimondott szöveg igazításának véglegesítése a szándékban](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Ez a művelet áthelyezi a kimondott szöveget a **Review endpoint utterances** (Végponti kimondott szövegek áttekintése) helyről a **GetJobInformation** szándékba. A végponti kimondott szöveg mostantól ennek a szándéknak a példaként szolgáló kimondott szövege. 

7. Tekintse át a szándékban található további kimondott szövegeket, és lássa el őket címkével, illetve javítsa ki az **Igazított szándékot**, ha az nem megfelelő.

8. Ha minden kimondott szöveg megfelelő, jelölje be az egyes sorok elején található jelölőnégyzeteket, majd válassza az **Add selected** (Kiválasztott hozzáadása) lehetőséget a kimondott szövegek megfelelő igazításához. 

    [ ![Képernyőkép: Megmaradó kimondott szövegek véglegesítése az igazított szándékhoz](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

9. A listán már nem szabad ezeknek a kimondott szövegeknek megjelenniük. Ha további kimondott szövegek jelennek meg, haladjon végig a listán, javítsa ki a szándékokat, és címkézze meg a hiányzó entitásokat, amíg a listán már nem szerepel több elem. 

10. A Filter (Szűrő) listájában válassza ki a következő szándékot, majd folytassa a kimondott szövegek javítását és az entitások címkézését. Ne feledje, hogy minden szándék utolsó lépése az **Add to aligned intent** (Hozzáadás igazított szándékhoz) lehetőség kiválasztása a kimondott szöveg sorában, vagy a jelölőnégyzet bejelölése az egyes szándékok mellett és az **Add selected** (Kiválasztott hozzáadása) lehetőség kiválasztása a tábla felett.

    Addig folytassa, amíg a szűrési listában szereplő összes szándék és entitás listája üres nem lesz. Ez egy nagyon kicsi alkalmazás. Az áttekintési folyamat csak néhány percet vesz igénybe. 

## <a name="update-phrase-list"></a>Kifejezéslista frissítése
Tartsa naprakészen a kifejezéslistát az újonnan felfedezett állásnevek hozzáadásával. 

1. A bal oldali navigációs menüben válassza a **Phrase lists** (Kifejezéslisták) lehetőséget.

2. Válassza a **Jobs** (Állások) kifejezéslistát.

3. Adja hozzá a `Natural Language Processing` elemet értékként, majd válassza a **Save** (Mentés) lehetőséget. 

## <a name="train"></a>Betanítás

Amíg nincs betanítva, a LUIS nem ismeri a módosításokat. 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Közzététel

Ha importálta ezt az alkalmazást, válassza a **Sentiment analysis** (Hangulatelemzés) elemet.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Szándék és entitások lekérése a végpontból

Próbáljon ki egy, a javítotthoz hasonló kimondott szöveget. 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

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
Néhány végponti kimondott szöveg magas előrejelzési pontszámmal szerepel az áttekintési listán. Ezeknek a kimondott szövegeknek az áttekintésére és ellenőrzésére ugyanúgy szükség van. Azért szerepelnek a listán, mert a következő legnagyobb pontszámú szándék a legfelső szándék pontszámához túl közeli pontszámmal rendelkezik. Körülbelül 15%-os különbséget szeretne az első két szándék között.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések
Az oktatóanyag során áttekintette a LUIS számára ismeretlen végponton elküldött kimondott szöveget. Miután ezek a kimondott szövegek ellenőrizve lettek, és át lettek helyezve a megfelelő szándékhoz kimondott példaszövegként, a LUIS javítja az előrejelzés pontosságát.

> [!div class="nextstepaction"]
> [Útmutató a minták használatához](luis-tutorial-pattern.md)
