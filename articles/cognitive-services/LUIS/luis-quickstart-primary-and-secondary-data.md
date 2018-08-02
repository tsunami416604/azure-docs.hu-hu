---
title: Oktatóanyag – LUIS-alkalmazás létrehozása adatok kinyeréséhez – Azure | Microsoft Docs
description: Ebből az oktatóanyagban megismerheti, hogyan hozhat létre egy szándékokat és egyszerű entitást használó egyszerű LUIS-alkalmazást gépi tanulással létrejött adatok kinyeréséhez.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/26/2018
ms.author: diberry
ms.openlocfilehash: 2e100f2019c3bb99e3909e64305ebe641e2a637e
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308969"
---
# <a name="tutorial-7-add-simple-entity-and-phrase-list"></a>Oktatóanyag: 7. Egyszerű entitás és kifejezéslista hozzáadása
Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan nyerhetők ki gépi tanulással létrejött adatok egy kimondott szövegből az **Egyszerű** entitás használatával.

<!-- green checkmark -->
> [!div class="checklist"]
> * Az entitások értelmezése 
> * Új LUIS-alkalmazás létrehozása az emberi erőforrások (HR) tartományához 
> * Egyszerű entitás felvétele egy alkalmazásban található állások kinyeréséhez
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez
> * Kifejezéslista hozzáadása az állással kapcsolatos szavak jelének erősítéséhez
> * Betanítás, alkalmazás közzététele és végpont ismételt lekérdezése

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Előkészületek
Ha még nincs meg az Emberi erőforrások alkalmazása az [összetett entitás](luis-tutorial-composite-entity.md) oktatóanyagából, [importálja](luis-how-to-start-new-app.md#import-new-app) a JSON-t egy új alkalmazásba a [LUIS](luis-reference-regions.md#luis-website) webhelyén. Az importálandó alkalmazás a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-composite-HumanResources.json) GitHub-adattárban található.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `simple`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne.  

## <a name="purpose-of-the-app"></a>Az alkalmazás célja
Ez az alkalmazás bemutatja, hogy nyerhetők ki adatok egy kimondott szövegből. Vegyük például egy csevegőrobot által kimondott következő szövegeket:

|Kimondott szöveg|Kinyerhető állásnév|
|:--|:--|
|Szeretnék jelentkezni az új könyvelői állásra.|könyvelés|
|Kérem, adja be az önéletrajzomat a mérnöki pozícióra.|mérnöki terület|
|Jelentkezés kitöltése az 123456 számú állásra|123456|

Ez az oktatóanyag egy új entitást vesz fel az állás nevének kinyeréséhez. 

## <a name="purpose-of-the-simple-entity"></a>Az egyszerű entitás célja
Az ebben a LUIS-alkalmazásban található egyszerű entitás célja megtanítani a LUIS-nak, hogy mi az állás neve, és hol található meg a kimondott szövegben. A kimondott szöveg állás része szövegenként változhat a szóhasználat és a kimondott szöveg hossza alapján. A LUIS-nak minden kimondott szövegben lévő minden szándék álláspéldájára szüksége van.  

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

Ez a LUIS-alkalmazás számos szándékban rendelkezik állásnevekkel. Ezen szavak felcímkézésével a szándékok minden kimondott szövegében, a LUIS többet tud meg arról, hogy mi egy állás, és hol található a kimondott szövegben.

## <a name="create-job-simple-entity"></a>Egyszerű állásentitás létrehozása

1. Győződjön meg arról, hogy az Emberi erőforrások alkalmazás a LUIS **Build** (Létrehozás) szakaszában van. Ha erre a szakaszra szeretne lépni, válassza a jobb felső menüsávon a **Build** (Létrehozás) elemet. 

2. Az **Intents** (Szándékok) lapon válassza az **ApplyForJob** szándékot. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "A LUIS képernyőképe a kiemelt ApplyForJob szándékkal")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

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

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>Címkézze meg a kimondott példaszövegek entitásait GetJobInformation szándékkal
1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

2. A szándékok listájából válassza ki a **GetJobInformation** elemet. 

3. Címkézze meg a kimondott példaszövegben az állásokat:

    |Kimondott szöveg|Állásentitás|
    |:--|:--|
    |Található munka az adatbázisokban?|adatbázisok|
    |Új lehetőségek keresése a könyvelés területén|könyvelés|
    |Milyen pozíciók érhetők el a vezető mérnökök számára?|vezető mérnökök|

    Vannak más kimondott példaszövegek, de nem tartalmaznak állással kapcsolatos szavakat.

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Amíg nincs betanítva, a LUIS nem ismeri fel a szándékok és entitások (a modell) módosításait. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

    ![Train (Betanítás) gomb kiválasztása](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    ![Sikeres betanítást jelző értesítés](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

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

## <a name="to-boost-signal-add-jobs-phrase-list"></a>A jel erősítéséhez vegyen fel állással kapcsolatos kifejezéslistát
Nyissa meg a [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) fájlt a LUIS-minták GitHub-adattárából. A lista több mint ezer, állásokkal kapcsolatos szót és kifejezést tartalmaz. Keressen a listában olyan állással kapcsolatos szavakat, amelyek hasznosak Önnek. Ha a keresett szavak vagy kifejezések nincsenek a listán, adja hozzá őket.

1. A LUIS-alkalmazás **Build** (Létrehozás) szakaszában válassza a **Phrase lists** (Kifejezéslisták) lehetőséget az **Improve app performance** (Az alkalmazás teljesítményének növelése) menüben.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "Képernyőkép a kifejezéslistákról, a bal navigációs gomb kiemelve")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. Válassza a **Create new phrase list** (Új kifejezéslista létrehozása) lehetőséget. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "Képernyőkép: az új kifejezéslista létrehozására szolgáló gomb, kiemelve")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. Az új kifejezéslistának adja a `Jobs` nevet és a jobs-phrase-list.csv fájlban található listát másolja a **Values** (Értékek) szövegmezőbe. Nyomja le az Enter billentyűt. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Képernyőkép: az új kifejezéslista létrehozása előugró párbeszédpanel")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Ha több szót szeretne hozzáadni a kifejezéslistához, tekintse át **Related Values** (Kapcsolódó értékek) listáját, és adja hozzá a relevánsakat. 

4. A kifejezéslista aktiválásához kattintson a **Save** (Mentés) gombra.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Képernyőkép: az új kifejezéslista létrehozása előugró párbeszédpanel, a kifejezéslista értékmezőjében található szavakkal")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Tanítsa be](#train-the-luis-app) és [tegye közzé](#publish-the-app-to-get-the-endpoint-URL) ismét az alkalmazást a kifejezéslista használatához.

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

## <a name="phrase-lists"></a>Kifejezéslisták
A kifejezéslista hozzáadása felerősítette a listában szereplő szavak jelét, de a rendszer **nem** használja pontos egyezésként. A kifejezéslistán számos állás szerepel a `lead` első szóval, és a `welder` állást is tartalmazza, de nem szerepel benne a `lead welder` állás. Lehet, hogy az állások kifejezéslistája nem teljes. Ha rendszeresen [áttekinti a végponti kimondott szövegeket](luis-how-to-review-endoint-utt.md) és állásokkal kapcsolatos egyéb szavakat is keres, adja hozzá ezeket a kifejezéslistához. Ezután tanítsa be ismét és tegye újra közzé az alkalmazást.

## <a name="what-has-this-luis-app-accomplished"></a>Milyen műveleteket végzett el a LUIS-alkalmazás?
Az alkalmazás, egy egyszerű entitással és egy kifejezéslistával azonosított egy természetes nyelvi lekérdezési szándékot, és visszaadta az állásadatokat. 

A csevegőrobot már elég információval rendelkezik az állásra való jelentkezés elsődleges műveletének megállapításához, illetve a művelet paraméterének és a hivatkozott állás megállapításához. 

## <a name="where-is-this-luis-data-used"></a>Hol vannak használatban ezek a LUIS-adatok? 
A LUIS végzett ezzel a kéréssel. A hívó alkalmazás, például egy csevegőrobot, használhatja a topScoringIntent eredményt és az entitásból származó adatokat arra, hogy egy külső API használatával elküldje az állás információit egy emberierőforrás-képviselőhöz. Ha a csevegőrobot vagy a hívó alkalmazás egyéb programozható beállítással rendelkezik, a LUIS ezeket nem végzi el. A LUIS csak azt határozza meg, hogy mi a felhasználó szándéka. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Válassza a **My apps** (Saját alkalmazások) elemet a bal felső menüben. Válassza az alkalmazáslistában az alkalmazás neve mellett jobbra található három pontot (***...***), majd a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Előre összeállított kulcskifejezés-entitás hozzáadása](luis-quickstart-intent-and-key-phrase.md)