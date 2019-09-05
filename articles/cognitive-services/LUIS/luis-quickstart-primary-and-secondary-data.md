---
title: 'Oktatóanyag: Egyszerű entitás, kifejezések listája – LUIS'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban az egyszerű entitás használatával kinyerheti a feldolgozói feladatokból származó, a vállalat által megtanult adatok nevét. A kinyerés pontosságának növeléséhez adjon hozzá egy kifejezéslistát az egyszerű entitásra jellemző kifejezésekről.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 87e4fe3671f419383cb342fdb7dca55a8d2eb45d
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376262"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Oktatóanyag: Nevek kinyerése egyszerű entitással és egy kifejezés listával

Ebben az oktatóanyagban egy állás nevével kapcsolatos gépi tanulással létrejött adatokat nyer ki egy kimondott szövegből az **Egyszerű** entitás használatával. A kinyerés pontosságának növeléséhez adjon hozzá egy kifejezéslistát az egyszerű entitásra jellemző kifejezésekről.

Az egyszerű entitás egyetlen, szavakban vagy kifejezésekben szereplő adatfogalmat észlelésére szolgál.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * Egyszerű entitás hozzáadása 
> * Kifejezések listájának hozzáadása a jel szavainak növeléséhez
> * Betanítás 
> * Közzététel 
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>Egyszerű entitás

Ez az oktatóanyag egy új egyszerű entitást vesz fel az állás nevének kinyeréséhez. Az ebben a LUIS-alkalmazásban található egyszerű entitás célja megtanítani a LUIS-nak, hogy mi az állás neve, és hol található meg a kimondott szövegben. A kimondott szöveg állásnevet jelentő része szövegenként változhat a szóhasználat és a kimondott szöveg hossza alapján. A LUIS-nak példákra van szüksége az állások neveire minden állásnevet használó szándékhoz.  

Az egyszerű entitás megfelelő választás az ilyen típusú adatok esetén, amikor:

* Az adatok egyetlen fogalmat alkotnak.
* Az adatok nincsenek helyesen formázva, mint a reguláris kifejezések.
* Az adatok nem általánosak, mint a telefonszámok vagy adatok előre összeállított entitásai.
* Az adatok nem kapcsolódnak ismert szavak listájához, mint a listaentitások.
* Az adatok nem tartalmaznak más adatelemeket, például összetett entitásokat vagy környezetfüggő szerepköröket.

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

## <a name="import-example-app"></a>Alkalmazás importálása – példa

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) a szándékok oktatóanyagból.

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `simple` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Entitások megjelölése például egy szándék hosszúságú kimondott szöveg

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Az **Intents** (Szándékok) lapon válassza az **ApplyForJob** szándékot. 

1. Az `I want to apply for the new accounting job` kimondott szövegben válassza az `accounting` lehetőséget, az előugró menü felső mezőjébe írja be a `Job` kifejezést, majd válassza a **Create new entity** (Új entitás létrehozása) elemet az előugró menüben. 

    [![Képernyőkép a LUIS "ApplyForJob" leképezést és a kiemelt entitás lépéseket létrehozása](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "képernyőképe a LUIS \"ApplyForJob\" leképezést és az entitás lépéseket kiemelt létrehozása")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. Az előugró ablakban ellenőrizze az entitás nevét és típusát, majd válassza a **Done** (Kész) lehetőséget.

    ![Job (állás) nevű és simple (egyszerű) típusú egyszerű entitás modális előugró párbeszédpaneljének létrehozása](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. A fennmaradó hosszúságú kimondott szöveg jelölje meg a feladattal kapcsolatos szavakat a **feladattal** együtt a szó vagy kifejezés kiválasztásával, majd a felugró menüből válassza a **feladat** lehetőséget. 

    [![Képernyőkép a LUIS kiemelt feladat entitás címkézés](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "képernyőképe, a LUIS címkézés feladat entitás kiemelésével")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>További példa a hosszúságú kimondott szöveg és az entitás megjelölésére

Az egyszerű entitásoknak számos példát kell megadniuk ahhoz, hogy magas szintű előrejelzéssel rendelkezzenek. 
 
1. Adjon hozzá további kimondott szövegeket, és jelölje meg az állással kapcsolatos szavakat vagy kifejezéseket **Állás** entitásként. 

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
    |Szeretnék jelentkezni a fényképészi állásra.|fényképész|

## <a name="mark-job-entity-in-other-intents"></a>Feladatok entitásának megjelölése más szándékokban

1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

1. A szándékok listájából válassza ki a **GetJobInformation** elemet. 

1. A példában szereplő hosszúságú kimondott szöveg feladatok címkézése

    Ha a hosszúságú kimondott szöveg több példája van, mint egy másik szándék, ez a szándék nagyobb valószínűséggel éri el a legmagasabb várható szándékot. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanítása, hogy tesztelni lehessen a szándék változásait 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, hogy a betanított modell lekérdezhető legyen a végpontról.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Cél-és entitás-előrejelzés beolvasása a végpontról 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Here is my c.v. for the engineering job`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő kimondott szövegeket kell visszaadnia: `ApplyForJob`.

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    A LUIS megtalálta a helyes szándékot (**ApplyForJob**), és kinyerte a helyes entitást (**Job**) a következő értékkel: `engineering`.


## <a name="names-are-tricky"></a>A nevek bonyolultak
A LUIS-alkalmazás nagy magabiztossággal megtalálta a megfelelő szándékot és kinyerte az állás nevét, a nevek azonban bonyolultak. Próbálja meg a `This is the lead welder paperwork` kimondott szöveget.  

A következő JSON-ben, a LUIS a megfelelő szándékkal (`lead welder`) válaszol, de nem nyerte ki a `ApplyForJob` állásnevet. 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

Mivel a név bármi lehet, a LUIS pontosabban jósolja meg az entitásokat, ha rendelkezik egy szavakból álló kifejezéslistával a jel erősítéséhez.

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>A feladattal kapcsolatos szavak jelzésének növeléséhez vegyen fel egy, a feladattal kapcsolatos szavakat tartalmazó kifejezést.

Nyissa meg a [Jobs-phrase-list. csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) fájlt az Azure-Samples GitHub-adattárból. A lista a 1 000-as feladathoz tartozó szavakat és kifejezéseket tartalmazza. Keressen a listában olyan állással kapcsolatos szavakat, amelyek hasznosak Önnek. Ha a keresett szavak vagy kifejezések nincsenek a listán, adja hozzá őket.

1. A LUIS-alkalmazás **Build** (Létrehozás) szakaszában válassza a **Phrase lists** (Kifejezéslisták) lehetőséget az **Improve app performance** (Az alkalmazás teljesítményének növelése) menüben.

1. Válassza a **Create new phrase list** (Új kifejezéslista létrehozása) lehetőséget. 

1. Az új kifejezéslistának adja a `JobNames` nevet és a jobs-phrase-list.csv fájlban található listát másolja a **Values** (Értékek) szövegmezőbe.

    [![Képernyőkép a hozzon létre új kifejezést tartalmazó párbeszédpanelen előugró](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Képernyőkép az új kifejezést tartalmazó párbeszédpanelen előugró létrehozása")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Ha további szavakat szeretne felvenni a kifejezés listára, válassza az újraellenőrzés **lehetőséget,** majd tekintse át az új **kapcsolódó értékeket** , és adja hozzá a megfelelőt. 

    Ügyeljen arra, hogy az **alábbi értékek legyenek felcserélhetők** , mert ezeket az értékeket mind a feladatok szinonimájának kell tekinteni. További információ a megváltoztathatatlan és nem cserélhető [kifejezések listájának fogalmakról](luis-concept-feature.md#how-to-use-phrase-lists).

1. Kattintson a **kész** gombra a kifejezések listájának aktiválásához.

    [![Képernyőkép a hozzon létre új kifejezést tartalmazó párbeszédpanelen előugró szavakat tartalmaznak az értékek listában kifejezés](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "képernyőkép hozzon létre új kifejezést tartalmazó párbeszédpanelen előugró szavakat tartalmaznak az értékek listában kifejezés")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. A kifejezések listájának használatához betaníthatja és közzéteheti az alkalmazást.

1. Végezzen újra lekérdezést ugyanazzal a kimondott szöveggel: `This is the lead welder paperwork.`

    A JSON-válasz tartalmazza a kinyert entitást:

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Entitások nélküli leképezések – oktatóanyag](luis-quickstart-intents-only.md)
* [Egyszerű entitás](luis-concept-entity-types.md) fogalmi információi
* [Kifejezési lista](luis-concept-feature.md) fogalmi adatai
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Emberi erőforrások alkalmazás egy gépi tanulással létrejött egyszerű entitást használ az állások neveinek megkeresésére a kimondott szövegekben. Mivel számos különféle szó és kifejezés jelölhet állásnevet, az alkalmazásnak szüksége van egy kifejezéslistára az állásneveket jelentő szavak jelének felerősítéséhez. 

> [!div class="nextstepaction"]
> [Előre összeállított kulcskifejezés-entitás hozzáadása](luis-quickstart-intent-and-key-phrase.md)
