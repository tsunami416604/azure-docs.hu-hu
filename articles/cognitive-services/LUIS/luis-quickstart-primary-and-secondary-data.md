---
title: '7. oktatóanyag: Egyszerű entitás kifejezéslistával a LUIS-ban'
titleSuffix: Azure Cognitive Services
description: Gépi tanulással létrejött adatok kinyerése kimondott szövegből
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 4647e663f4a2734451835f90d2687bb6aaf91712
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277800"
---
# <a name="tutorial-7-extract-names-with-simple-entity-and-phrase-list"></a>7. oktatóanyag: Nevek kinyerése egyszerű entitással és kifejezéslistával

Ebben az oktatóanyagban egy állás nevével kapcsolatos gépi tanulással létrejött adatokat nyer ki egy kimondott szövegből az **Egyszerű** entitás használatával. A kinyerés pontosságának növeléséhez adjon hozzá egy kifejezéslistát az egyszerű entitásra jellemző kifejezésekről.

Ez az oktatóanyag egy új egyszerű entitást vesz fel az állás nevének kinyeréséhez. Az ebben a LUIS-alkalmazásban található egyszerű entitás célja megtanítani a LUIS-nak, hogy mi az állás neve, és hol található meg a kimondott szövegben. A kimondott szöveg állásnevet jelentő része szövegenként változhat a szóhasználat és a kimondott szöveg hossza alapján. A LUIS-nak példákra van szüksége az állások neveire minden állásnevet használó szándékhoz.  

Az egyszerű entitás megfelelő választás az ilyen típusú adatok esetén, amikor:

* Az adatok egyetlen fogalmat alkotnak.
* Az adatok nincsenek helyesen formázva, mint a reguláris kifejezések.
* Az adatok nem általánosak, mint a telefonszámok vagy adatok előre összeállított entitásai.
* Az adatok nem kapcsolódnak ismert szavak listájához, mint a listaentitások.
* Az adatok nem tartalmaznak más adatelemeket, mint az összetett vagy a hierarchikus entitások.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Meglévő oktatóalkalmazás használata
> * Egyszerű entitás felvétele egy alkalmazásban található állások kinyeréséhez
> * Kifejezéslista hozzáadása az állással kapcsolatos szavak jelének erősítéséhez
> * Betanítás 
> * Közzététel 
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata

Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Amennyiben nem rendelkezik az előző oktatóanyagból származó EmberiErőforrások alkalmazással, kövesse a következő lépéseket:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-composite-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `simple` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="simple-entity"></a>Egyszerű entitás
Az egyszerű entitás egyetlen, szavakban vagy kifejezésekben szereplő adatfogalmat észlelésére szolgál.

Vegyük például egy csevegőrobot által kimondott következő szövegeket:

|Kimondott szöveg|Kinyerhető állásnév|
|:--|:--|
|Szeretnék jelentkezni az új könyvelői állásra.|könyvelés|
|Adja be az önéletrajzomat a mérnöki pozícióra.|mérnöki terület|
|Jelentkezés kitöltése az 123456 számú állásra|123456|

Az állás nevét nehéz megállapítani, mert a név lehet főnév, ige, vagy több szóból álló kifejezés. Például:

|Feladatok|
|--|
|mérnök|
|szoftvermérnök|
|vezető szoftvermérnök|
|mérnöki csoportvezető |
|légiforgalmi irányító|
|gépjárműkezelő|
|mentőautó-sofőr|
|gondozó|
|présgépkezelő|
|gépszerelő|

Ez a LUIS-alkalmazás számos szándékban rendelkezik állásnevekkel. Ezen szavak a szándékok minden kimondott szövegében való felcímkézésével a LUIS többet tud meg arról, mi az állásnév, és hol található a kimondott szövegekben.

Miután megjelöli az entitásokat a példaszövegekben, fontos lépés a kifejezéslista hozzáadása. Ez a lista felerősíti az egyszerű entitás jelét. A kifejezéslistát a rendszer **nem** pontos egyezésként használja, így nem szükséges benne megadni az összes várható értéket. 

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Az **Intents** (Szándékok) lapon válassza az **ApplyForJob** szándékot. 

3. Az `I want to apply for the new accounting job` kimondott szövegben válassza az `accounting` lehetőséget, az előugró menü felső mezőjébe írja be a `Job` kifejezést, majd válassza a **Create new entity** (Új entitás létrehozása) elemet az előugró menüben. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "A LUIS képernyőképe a kiemelt ApplyForJob szándékkal és az entitás létrehozásának lépéseivel")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. Az előugró ablakban ellenőrizze az entitás nevét és típusát, majd válassza a **Done** (Kész) lehetőséget.

    ![Job (állás) nevű és simple (egyszerű) típusú egyszerű entitás modális előugró párbeszédpaneljének létrehozása](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. A `Submit resume for engineering position` kimondott szövegben címkézze meg a `engineering` kifejezést állásentitásként. Válassza ki a `engineering` kifejezést, majd válassza a **Job** (Állás) lehetőséget az előugró menüben. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Képernyőkép a LUIS-ról, az állásentitás címkézése kiemelve")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    Minden kimondott szöveg meg van címkézve, de öt kimondott szöveg nem elég arra, hogy megtanítsa a LUIS-t az állással kapcsolatos szavakra és kifejezésekre. Azokhoz az állásokhoz, amelyek számértéket használnak, nem kell több példa, mert reguláris kifejezés entitást használnak. Azokhoz az állásokhoz, amelyek szavakból vagy kifejezésekből állnak, legalább 15 további példa szükséges. 

6. Adjon hozzá további kimondott szövegeket, és jelölje meg az állással kapcsolatos szavakat vagy kifejezéseket **Állás** entitásként. Az állástípusok általánosak minden foglalkoztatás esetében egy foglalkoztatási szolgáltatásban. Ha egy adott iparághoz kapcsolódó állásokat szeretne, az állással kapcsolatos szavaknak ezt kell tükröznie. 

    |Kimondott szöveg|Állásentitás|
    |:--|:--|
    |Jelentkezem a programmenedzseri állásra az R&D-nél|Programmenedzser|
    |Itt található a jelentkezésem az előkészítő szakács állásra.|előkészítő szakács|
    |Csatoltam az önéletrajzom a tábori tanácsadói álláshoz.|tábori tanácsadó|
    |Ez az önéletrajzom az ügyintézői álláshoz.|ügyintéző|
    |Szeretnék jelentkezni az értékesítési területen meghirdetett vezetői állásra.|vezető, értékesítés|
    |Ez az új könyvelői pozícióra benyújtott önéletrajzom.|könyvelés|
    |Csatoltam a jelentkezésem a pultos állásra.|pultos|
    |Beadom a jelentkezésem a tetőfedő és gazdálkodó állásra.|tetőfedő, gazdálkodó|
    |Ez az önéletrajzom a buszvezetői álláshoz.|buszvezető|
    |Szakképzett nővér vagyok. Itt az önéletrajzom.|szakképzett nővér|
    |Szeretném beadni a dokumentumokat az újságban látott tanári pozícióra.|tanári|
    |Ez az önéletrajzom a zöldség- és gyümölcsraktárosi pozícióra.|raktáros|
    |Jelentkezés a csempéző állásra.|csempéző|
    |Csatoltam az önéletrajzom a tájépítészi álláshoz.|tájépítész|
    |Csatoltam az önéletrajzom a biológiaprofesszori álláshoz.|biológiaprofesszor|
    |Szeretnék jelentkezni a fényképészi állásra.|fényképész|git 

## <a name="label-entity-in-example-utterances"></a>Entitás megcímkézése kimondott példaszövegekben

Az entitás megcímkézése vagy _megjelölése_ megmutatja a LUIS-nak, hol található az entitás a kimondott példaszövegekben.

1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

2. A szándékok listájából válassza ki a **GetJobInformation** elemet. 

3. Címkézze meg a kimondott példaszövegben az állásokat:

    |Kimondott szöveg|Állásentitás|
    |:--|:--|
    |Található munka az adatbázisokban?|adatbázisok|
    |Új lehetőségek keresése a könyvelés területén|könyvelés|
    |Milyen pozíciók érhetők el a vezető mérnökök számára?|vezető mérnökök|

    Vannak más kimondott példaszövegek, de nem tartalmaznak állással kapcsolatos szavakat.

## <a name="train"></a>Betanítás

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Közzététel

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Szándék és entitások lekérése a végpontból 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Here is my c.v. for the programmer job`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő kimondott szövegeket kell visszaadnia: `ApplyForJob`.

    ```JSON
    {
      "query": "Here is my c.v. for the programmer job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9826467
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9826467
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0218927357
        },
        {
          "intent": "MoveEmployee",
          "score": 0.007849265
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00349470088
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00348804821
        },
        {
          "intent": "None",
          "score": 0.00319909188
        },
        {
          "intent": "FindForm",
          "score": 0.00222647213
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00211193133
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00172086991
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00138010911
        }
      ],
      "entities": [
        {
          "entity": "programmer",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 33,
          "score": 0.5230502
        }
      ]
    }
    ```
    
    A LUIS megtalálta a helyes szándékot (**ApplyForJob**), és kinyerte a helyes entitást (**Job**) a következő értékkel: `programmer`.


## <a name="names-are-tricky"></a>A nevek bonyolultak
A LUIS-alkalmazás nagy magabiztossággal megtalálta a megfelelő szándékot és kinyerte az állás nevét, a nevek azonban bonyolultak. Próbálja meg a `This is the lead welder paperwork` kimondott szöveget.  

A következő JSON-ben, a LUIS a megfelelő szándékkal (`lead welder`) válaszol, de nem nyerte ki a `ApplyForJob` állásnevet. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

Mivel a név bármi lehet, a LUIS pontosabban jósolja meg az entitásokat, ha rendelkezik egy szavakból álló kifejezéslistával a jel erősítéséhez.

## <a name="to-boost-signal-add-phrase-list"></a>A jel erősítéséhez vegyen fel egy kifejezéslistát.

Nyissa meg a [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) fájlt a LUIS-minták GitHub-adattárából. A lista több mint ezer, állásokkal kapcsolatos szót és kifejezést tartalmaz. Keressen a listában olyan állással kapcsolatos szavakat, amelyek hasznosak Önnek. Ha a keresett szavak vagy kifejezések nincsenek a listán, adja hozzá őket.

1. A LUIS-alkalmazás **Build** (Létrehozás) szakaszában válassza a **Phrase lists** (Kifejezéslisták) lehetőséget az **Improve app performance** (Az alkalmazás teljesítményének növelése) menüben.

2. Válassza a **Create new phrase list** (Új kifejezéslista létrehozása) lehetőséget. 

3. Az új kifejezéslistának adja a `Job` nevet és a jobs-phrase-list.csv fájlban található listát másolja a **Values** (Értékek) szövegmezőbe. Nyomja le az Enter billentyűt. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Képernyőkép: az új kifejezéslista létrehozása előugró párbeszédpanel")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Ha több szót szeretne hozzáadni a kifejezéslistához, tekintse át **Related Values** (Kapcsolódó értékek) listáját, és adja hozzá a relevánsakat. 

4. A kifejezéslista aktiválásához kattintson a **Save** (Mentés) gombra.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Képernyőkép: az új kifejezéslista létrehozása előugró párbeszédpanel, a kifejezéslista értékmezőjében található szavakkal")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Tanítsa be](#train) és [tegye közzé](#publish) ismét az alkalmazást a kifejezéslista használatához.

6. Végezzen újra lekérdezést ugyanazzal a kimondott szöveggel: `This is the lead welder paperwork.`

    A JSON-válasz tartalmazza a kinyert entitást:

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Emberi erőforrások alkalmazás egy gépi tanulással létrejött egyszerű entitást használ az állások neveinek megkeresésére a kimondott szövegekben. Mivel számos különféle szó és kifejezés jelölhet állásnevet, az alkalmazásnak szüksége van egy kifejezéslistára az állásneveket jelentő szavak jelének felerősítéséhez. 

> [!div class="nextstepaction"]
> [Előre összeállított kulcskifejezés-entitás hozzáadása](luis-quickstart-intent-and-key-phrase.md)