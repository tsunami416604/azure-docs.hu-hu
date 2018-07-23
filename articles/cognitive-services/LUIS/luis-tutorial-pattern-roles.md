---
title: Az oktatóanyag minta szerepkörök segítségével javíthatja a LUIS-előrejelzés – Azure |} A Microsoft Docs
titleSuffix: Cognitive Services
description: Ebben az oktatóanyagban a minta szerepkörök kontextusban kapcsolódó entitások használatával javíthatja a LUIS előrejelzéseket.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: v-geberr;
ms.openlocfilehash: 938b868fadfa102174b270a222494710fc156442
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39174564"
---
# <a name="tutorial-improve-app-with-pattern-roles"></a>Oktatóanyag: A minta szerepkörökkel alkalmazás fejlesztéséhez

Ebben az oktatóanyagban használja egy egyszerű entitás minták kombinálva szerepkörökkel szándékot és egyéb entitások előrejelzési növelése érdekében.  Minták használata esetén kevesebb példa utterances célja van szükség.

> [!div class="checklist"]
* A minta szerepkörök ismertetése
* Egyszerű entitás használata szerepkörök 
* Az egyszerű entitás használata szerepkörök utterances minta létrehozása
* Azt, hogyan ellenőrizheti, mintát előrejelzési fejlesztései

Ehhez a cikkhez egy ingyenes [LUIS](luis-reference-regions.md)-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="before-you-begin"></a>Előkészületek
Ha nem rendelkezik az emberi erőforrások alkalmazásból a [minta](luis-tutorial-pattern.md) oktatóanyagban [importálása](luis-how-to-start-new-app.md#import-new-app) a JSON-kódot egy új alkalmazást a [LUIS](luis-reference-regions.md#luis-website) webhely. Az alkalmazás importálása megtalálható a [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-patterns-HumanResources-v2.json) GitHub-adattárban.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `roles`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. 

## <a name="the-purpose-of-roles"></a>A szerepkörök célja
A szerepkörök célja, hogy az utterance (kifejezés) kontextusban kapcsolódó entitások kinyeréséhez. Az utterance (kifejezés) a `Move new employee Robert Williams from Sacramento and San Francisco`, a város forrás és cél város értékek egymáshoz kapcsolódó és a közös nyelvi használatával minden egyes helyet jelöl. 

Minták használata esetén a mintában a entitások észlelendő _előtt_ a minta megfelel az utterance (kifejezés). 

Amikor létrehoz egy mintát, az első lépéseként válassza ki a leképezést a minta. A cél kiválasztásával, ha a minta egyezik, a megfelelő cél mindig ad vissza a magas pontszám (általában a 99-100 %). 

### <a name="compare-hierarchical-entity-to-simple-entity-with-roles"></a>Hasonlítsa össze a szerepkörök egyszerű entitás hierarchikus entitás

Az a [hierarchikus oktatóanyag](luis-quickstart-intent-and-hier-entity.md), a **MoveEmployee** leképezés észlelhető mikor áthelyezése egy meglévő alkalmazott épület és az office egy másik. Példa megcímkézzen forrás-és célhelynek volt, de nem használta a szerepköröket. Ehelyett a forrás és cél volt a hierarchikus entitás gyermekei. 

Ebben az oktatóanyagban az emberi erőforrások alkalmazáson észleli az új alkalmazottak áthelyezését egy város egy másik kapcsolatos kimondott szöveg. A két típusú utterances olyan hasonló, de különböző LUIS képességekhez megoldani.

|Oktatóanyag|Példa utterance (kifejezés)|Forrás-és célhelynek|
|--|--|--|
|[Hierarchikus (nincsenek szerepkörök)](luis-quickstart-intent-and-hier-entity.md)|mV Jill Jones a **a-2349** való **b-1298**|a-2349 b-1298|
|Ez az oktatóanyag (a szerepkörök)|Helyezze át a Billy Patterson **Yuma** való **Denver**.|Yuma, Denver|

A mintában a hierarchikus entitás nem használható, mert csak a hierarchikus szülők szülők használatban vannak. Annak érdekében, hogy a forrás és cél nevesített helyek visszatér, muse használatát egy minta.

### <a name="simple-entity-for-new-employee-name"></a>Új alkalmazottak neveként az egyszerű entitás
Az új alkalmazott Billy Patterson, neve nem szerepel a lista entitás **alkalmazott** még. Az új alkalmazottak neve ki kell olvasni, annak érdekében, hogy a név küldeni egy külső rendszer a vállalati hitelesítő adatok létrehozásához. A vállalati hitelesítő adatok létrehozása után az alkalmazott hitelesítő adatok kerülnek a lista entitás **alkalmazott**.

A **alkalmazott** lista sikeresen létrehozva a [lista az oktatóanyagban](luis-quickstart-intent-and-list-entity.md).

A **NewEmployee** entitás, egy egyszerű entitásnak nincsenek szerepkörök. 

### <a name="simple-entity-with-roles-for-relocation-cities"></a>Az adatáthelyezési városok szerepkörök egyszerű entitás
Az újonnan felvettek betanítása és a termékcsalád kell egy város, ahol a kitalált vállalat megtalálható a jelenlegi városához a áthelyezni. Az új alkalmazott bármely az városa származhatnak, mert a helyek kell deríteni. Például egy lista entitás set listáját nem működne, mivel csak a listában a városok szeretne kinyerni.

A forrás és cél városok társított szerepkörneveket kell egyedinek lennie az összes entitást. Ellenőrizze, hogy a szerepkör a egyedi egyszerűen az elősegítsék őket a befoglaló entitás egy elnevezési gyakorlat sok keresztül való. A **NewEmployeeRelocation** entitás, egy egyszerű entitás kétféle szerepkörben működnek: **NewEmployeeReloOrigin** és **NewEmployeeReloDestination**.

### <a name="simple-entities-need-enough-examples-to-be-detected"></a>Egyszerű entitások kell észleltnek elegendő példák
Mivel a példa utterance (kifejezés) `Move new employee Robert Williams from Sacramento and San Francisco` csak gép megtanult entitásának, fontos, hogy elegendő a példában szándéka kimondott szöveg biztosítanak, így az entitásokat a rendszer észleli.  

**Minták lehetővé teszik, hogy kevesebb példa kimondott szöveg, ha az entitások nem észlelt, miközben a mintázat nem felel meg.**

Ha problémákba ütközik az egyszerű entitás észlelési egy nevet, például a város, mert, érdemes hasonló értékek listáját kifejezés helyett szerepel. A LUIS egy adott szó vagy kifejezés típusa kapcsolatos további jel, így ez segít a észlelését, a város nevét. Kifejezés listák csak segítik a minta segít az entitás szolgáltatását, amely egyeztetni kívánt minta szükséges. 

## <a name="create-new-entities"></a>Új entitások létrehozása
1. Válassza ki **összeállítása** a felső menüben.

2. Válassza ki **entitások** a bal oldali navigációs sávon. 

3. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget.

4. Az előugró ablakban adja meg a `NewEmployee` , egy **egyszerű** entitás.

5. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget.

6. Az előugró ablakban adja meg a `NewEmployeeRelocation` , egy **egyszerű** entitás.

7. Válassza ki **NewEmployeeRelocation** az entitások listájában. 

8. Adja meg az első szerepét, `NewEmployeeReloOrigin` , és válassza ki, adja meg.

9. Adja meg, a második szerepkör `NewEmployeeReloDestination` , és válassza ki, adja meg.

## <a name="create-new-intent"></a>Új leképezésének létrehozása
Az entitások ezeket a lépéseket a címkézés egyszerűbb, ha az előre összeállított keyPhrase entitás eltávolít, majd hozzá a majd, miután végzett a jelen szakaszban ismertetett lépések megkezdése előtt lehet. 

1. Válassza ki **leképezések** a bal oldali navigációs sávon.

2. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

3. Adja meg `NewEmployeeRelocationProcess` a felugró párbeszédpanel a szándék neveként.

4. Adja meg a következő példa utterances, a címkézés új entitásokat. Az entitás- és szerepkör értékei a félkövérrel szedett. Váltson a vágólapra a **jogkivonatok nézet** Ha ez egyszerűbbé teszi a szöveg megjelölésére. 

    Az entitás az szerepkör nem ad meg, ha egységes szóhasználat érdekében az a célja. Megteheti, hogy később a minta létrehozásakor. 

    |Kimondott szöveg|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Helyezze át **Bob Jones** a **Seattle** való **Los Colinas**|Bob Jones|Budapest, Los Colinas|
    |Helyezze át **Dave c Cooper** a **Redmond** való **New York City**|Dave c Cooper|Redmond, New York City|
    |Helyezze át **Jim Paul Smith** a **Toronto** való **Vancouver Nyugat-India**|Jim Paul Smith|Toronto, Nyugat-India Vancouver|
    |Helyezze át **J. Benson** a **Boston** való **Staines a Thames esetén**|J. Benson|Boston, Staines a Thames esetén|
    |Helyezze át **Travis "Trav" Hinton** a **Castelo Branco** való **Orlandóban**|Travis "Trav" Hinton|Castelo Branco Orlandóban|
    |Helyezze át **Trevor Nottington III** a **Aranda Németország Duerotól** való **Boise**|Trevor Nottington III.|De Aranda, Duerotól Boise|
    |Helyezze át **Dr. Greg Williams** a **Orlandóban** való **Ellicott város**|Dr. Greg Williams|Orlandóban, Ellicott város|
    |Helyezze át **Robert "Bobby" Gregson** a **céges Város** való **San Juan Capistrano**|Robert "Bobby" Gregson|Céges város, San Juan Capistrano|
    |Helyezze át **Patti Owens** a **Bellevue** való **Rockford**|Patti Owens|Bellevue, Rockford|
    |Helyezze át **Erzsébet Bartlet** a **Tuscan** való **Santa Fe**|Erzsébet Bartlet|Tuscan, Santa Fe|

    Az alkalmazott neve előtag, a word-count, a szintaxist és a utótag különféle rendelkezik. Ez fontos a LUIS tudni, hogy egy új alkalmazott neve változata. A város nevét is rendelkezik különböző word-count és szintaxist. Ez számos fontos tanít LUIS hogyan jelenhetnek meg ezeket az entitásokat a felhasználó utterance (kifejezés). 
    
    Ha bármelyik entitásának volt-e az azonos word száma és semmilyen egyéb változatok, akkor tanít LUIS, hogy az entitás csak rendelkezik a word-count, és nincs más változata létezik. A LUIS nem tudná megfelelően előre változatok szélesebb készletét használja, mert ez nem látható volt bármilyen. 

    Ha eltávolította a keyPhrase entitás, csatolja vissza az alkalmazás most már.

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Az új szándékot és a kimondott szöveg szükséges képzés. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez
Ahhoz, hogy LUIS-előrejelzéseket kaphasson egy csevegőrobotban vagy más alkalmazásban, közzé kell tennie az alkalmazást. 

1. A LUIS-webhely jobb felső részén válassza a **Publish** (Közzététel) lehetőséget. 

2. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

3. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="query-the-endpoint-without-pattern"></a>A végpont nélkül minta lekérdezése
1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Move Wayne Berry from Miami to Mount Vernon`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. 

    ```JSON
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

A szándék előrejelzési pontszám, csak 50 %-át. Ha az ügyfélalkalmazást igényel nagyobb, ki kell javítani kell. Az entitások nem voltak előre jelzett vagy.

Minták segítséget nyújt az előrejelzési pontszám, azonban az entitásokat kell lennie megfelelően előre jelzett előtt a minta megfelel az utterance (kifejezés). 

## <a name="add-a-pattern-that-uses-roles"></a>Egy mintát, használja a szerepkörök hozzáadása
1. Válassza ki **összeállítása** a felső navigációs.

2. Válassza ki **minták** a bal oldali navigációs menüben.

3. Válassza ki **NewEmployeeRelocationProcess** származó a **megjelölésű válassza** legördülő listából. 

4. Adja meg a következő mintának: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Ha betanításához, közzétételét és lekérdezni a végpont, előfordulhat, hogy disappointed megtekintéséhez, hogy az entitás nem található, így a minta nem egyeztek, ezáltal az előrejelzés nem javítható. Ez a nincs elég példa utterances címkézett entitásokkal következménye. További példák, hanem a probléma megoldásához kifejezés lista hozzáadása.

## <a name="create-a-phrase-list-for-cities"></a>Egy kifejezés lista városok létrehozása
Város, például people's nevek a következők megjelenítéséhez abban, hogy a szavakat és írásjelek bármilyen kombinációját is lehetnek. De a várost, a régiót és a világ ismertek, így a LUIS kell város, amellyel megkezdheti a tanulási kifejezés listáját. 

1. Válassza ki **kifejezéslista** származó a **megnövelheti az alkalmazások teljesítményét** szakaszában a bal oldali menüben. 

2. Nevezze el a lista `Cities` , és adja hozzá a következő `values` listája:

    |Kifejezéslista értékét|
    |--|
    |Seattle|
    |A San Diego|
    |New York City|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Ne vegye fel a világ vagy akár minden város régióban található minden város. A LUIS képesnek kell lennie általánosításához milyen város van a listából. 

    Győződjön meg arról, hogy **ezeket az értékeket felcserélhetők** kiválasztott. Ez a beállítás azt jelenti, hogy a szavakat a listán a szinonimák számít. Ez a pontosan, hogyan kezelni a mintában.

    Ne feledje [utoljára](luis-quickstart-primary-and-secondary-data.md) az oktatóanyag-sorozatban létrehozott kifejezés lista egyben egy egyszerű entitás entitás észlelési növelése érdekében.  

3. Betanítása, és tegye közzé az alkalmazást.

## <a name="query-endpoint-for-pattern"></a>Lekérdezés végpont minta
1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Move wayne berry from miami to mount vernon`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. 

    ```JSON
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
}
    ```

A leképezési pontszám most már sokkal nagyobb és a szerepkör nevét az entitás válasz részét képezik.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza a három pontot (***...*** ) az alkalmazások listájában, az alkalmazás nevétől jobbra, válassza ki a **törlése**. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerje meg, a LUIS-alkalmazások ajánlott eljárásai](luis-concept-best-practices.md)