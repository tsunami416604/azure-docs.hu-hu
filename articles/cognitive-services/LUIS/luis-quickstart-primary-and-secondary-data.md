---
title: Egyszerű entitás, kifejezéslista
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban adatok kinyerése az gép megismerte az alkalmazási projekt neve az utterance (kifejezés) használatával az egyszerű entitás. A kinyerés pontosságának növeléséhez adjon hozzá egy kifejezéslistát az egyszerű entitásra jellemző kifejezésekről.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: d333eb6baa41321fdf7daca3c545d5d5e3ed5fe4
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732704"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Oktatóanyag: Az egyszerű entitás és egy kifejezés lista nevének kinyerése

Ebben az oktatóanyagban egy állás nevével kapcsolatos gépi tanulással létrejött adatokat nyer ki egy kimondott szövegből az **Egyszerű** entitás használatával. A kinyerés pontosságának növeléséhez adjon hozzá egy kifejezéslistát az egyszerű entitásra jellemző kifejezésekről.

Az egyszerű entitás egyetlen, szavakban vagy kifejezésekben szereplő adatfogalmat észlelésére szolgál.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Példa-alkalmazás importálása
> * Egyszerű entitás hozzáadása 
> * Adja hozzá a kifejezéslista jel szavak növelése érdekében
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
* Az adatok nem tartalmaznak más adatelemeket, mint az összetett vagy a hierarchikus entitások.

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

## <a name="import-example-app"></a>Példa-alkalmazás importálása

1.  Töltse le és mentse a [alkalmazás JSON-fájlt](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) az leképezések oktatóanyag.

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `simple` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Megjelölés entitások példa utterances-leképezés

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Az **Intents** (Szándékok) lapon válassza az **ApplyForJob** szándékot. 

1. Az `I want to apply for the new accounting job` kimondott szövegben válassza az `accounting` lehetőséget, az előugró menü felső mezőjébe írja be a `Job` kifejezést, majd válassza a **Create new entity** (Új entitás létrehozása) elemet az előugró menüben. 

    [![Képernyőkép a LUIS "ApplyForJob" leképezést és a kiemelt entitás lépéseket létrehozása](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "képernyőképe a LUIS \"ApplyForJob\" leképezést és az entitás lépéseket kiemelt létrehozása")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. Az előugró ablakban ellenőrizze az entitás nevét és típusát, majd válassza a **Done** (Kész) lehetőséget.

    ![Job (állás) nevű és simple (egyszerű) típusú egyszerű entitás modális előugró párbeszédpaneljének létrehozása](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. A fennmaradó megcímkézzen, jelölje meg a feladathoz kapcsolódó szavak **feladat** szó vagy kifejezés, majd kiválasztja az entitás **feladat** az előugró menüben. 

    [![Képernyőkép a LUIS kiemelt feladat entitás címkézés](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "képernyőképe, a LUIS címkézés feladat entitás kiemelésével")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>További példa beszédmódok hozzáadása, és jelölje meg az entitás

Egyszerű entitások példákat szükség van egy előrejelzési megbízhatónak fogja tekinteni. 
 
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
    |Szeretnék jelentkezni a fényképészi állásra.|fényképész|git 

## <a name="mark-job-entity-in-other-intents"></a>Megjelölés feladat entitás más leképezések

1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

1. A szándékok listájából válassza ki a **GetJobInformation** elemet. 

1. A feladatok a példa kimondott szöveg a címkét

    Vannak további példa utterances egy szándékot, mint egy másik szándékot, ha az adott szándékot, hogy a legmagasabb előre jelzett intext nagyobb valószínűséggel rendelkezik. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Az alkalmazás betanításához, így a módosítások a leképezés tesztelhető legyen 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Tegye közzé az alkalmazást, így a betanított modell lekérdezhető a végpontról

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Leképezés és egyéb entitások előrejelzés beolvasása végpont 

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

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>A feladathoz kapcsolódó szó boost jelet adjon hozzá szavak feladatokkal kapcsolatos kifejezések listája

Nyissa meg a [feladatok-kifejezés-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) az Azure-minták GitHub-adattárból. A lista több mint 1000 feladatot szavak és kifejezések. Keressen a listában olyan állással kapcsolatos szavakat, amelyek hasznosak Önnek. Ha a keresett szavak vagy kifejezések nincsenek a listán, adja hozzá őket.

1. A LUIS-alkalmazás **Build** (Létrehozás) szakaszában válassza a **Phrase lists** (Kifejezéslisták) lehetőséget az **Improve app performance** (Az alkalmazás teljesítményének növelése) menüben.

1. Válassza a **Create new phrase list** (Új kifejezéslista létrehozása) lehetőséget. 

1. Az új kifejezéslistának adja a `JobNames` nevet és a jobs-phrase-list.csv fájlban található listát másolja a **Values** (Értékek) szövegmezőbe. Nyomja le az Enter billentyűt. 

    [![Képernyőkép a hozzon létre új kifejezést tartalmazó párbeszédpanelen előugró](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Képernyőkép az új kifejezést tartalmazó párbeszédpanelen előugró létrehozása")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Ha több olyan szót, kifejezést listájához hozzáadni, jelölje be **Recommand** tekintse át az új **kapcsolódó értékek** , és adja hozzá bármelyik szempontjából releváns. 

    Győződjön meg arról, hogy a **ezeket az értékeket felcserélhetők** be van jelölve, mert ezeket az értékeket minden kell kezelni a szinonimák feladatok. További tudnivalók a cserélhető és noninterchangeable [kifejezést a lista fogalmak](luis-concept-feature.md#how-to-use-phrase-lists).

1. A kifejezéslista aktiválásához kattintson a **Save** (Mentés) gombra.

    [![Képernyőkép a hozzon létre új kifejezést tartalmazó párbeszédpanelen előugró szavakat tartalmaznak az értékek listában kifejezés](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "képernyőkép hozzon létre új kifejezést tartalmazó párbeszédpanelen előugró szavakat tartalmaznak az értékek listában kifejezés")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. Betanítása, és tegye közzé újra a kifejezés helyett szerepel a listában az alkalmazást.

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

* [Leképezések entitások oktatóanyag nélkül](luis-quickstart-intents-only.md)
* [Egyszerű entitás](luis-concept-entity-types.md) elméleti információk
* [Kifejezéslista](luis-concept-feature.md) elméleti információk
* [Hogyan betanítása](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [A LUIS-portál tesztelése](luis-interactive-test.md)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Emberi erőforrások alkalmazás egy gépi tanulással létrejött egyszerű entitást használ az állások neveinek megkeresésére a kimondott szövegekben. Mivel számos különféle szó és kifejezés jelölhet állásnevet, az alkalmazásnak szüksége van egy kifejezéslistára az állásneveket jelentő szavak jelének felerősítéséhez. 

> [!div class="nextstepaction"]
> [Előre összeállított kulcskifejezés-entitás hozzáadása](luis-quickstart-intent-and-key-phrase.md)
