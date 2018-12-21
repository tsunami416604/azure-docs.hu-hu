---
title: A minta-szerepkörök
titleSuffix: Azure Cognitive Services
description: Egy minta segítségével adatokat nyerhet ki a helyesen formázott kimondottszöveg-sablonokból. A kimondottszöveg-sablonok egy egyszerű entitás és szerepkörök segítségével nyernek ki olyan kapcsolódó adatokat, mint például a kiindulási hely és a célhely.
ms.custom: seodec18
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d789954c0ebc71f88fb434430de5b5076ca6c246
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53713719"
---
# <a name="tutorial-4-extract-contextually-related-patterns"></a>4. oktatóanyag: Szövegkörnyezet szerint kapcsolódó minták kinyerése

Ebben az oktatóanyagban egy minta segítségével adatokat nyerhet ki a helyesen formázott kimondottszöveg-sablonokból. A kimondottszöveg-sablonok egy egyszerű entitás és szerepkörök segítségével nyernek ki olyan kapcsolódó adatokat, mint például a kiindulási hely és a célhely.  A minták használatakor kevesebb kimondottszöveg-példára van szükség egy adott szándékhoz.

A szerepkörök célja az, hogy szövegkörnyezethez kapcsolódó entitásokat nyerjenek ki a kimondott szövegből. A `Move new employee Robert Williams from Sacramento and San Francisco` kimondott szövegben a kiindulási város és a célváros értékek kapcsolódnak egymáshoz, és köznyelven jelölik az egyes helyeket. 


Az új alkalmazott, Billy Patterson neve egyelőre nem része az **Alkalmazott** listaentitásnak. Először az új alkalmazott nevét kell kinyerni, hogy aztán elküldjük egy külső rendszerre a vállalati hitelesítő adatok létrehozásához. A vállalati hitelesítő adatok létrehozása után az alkalmazott hitelesítő adatai bekerülnek az **Alkalmazott** listaentitásba.

Az új alkalmazottat át kell költöztetni a jelenlegi városból abba a városba, ahol a kitalált vállalat található. Mivel az új alkalmazottak bármely városból származhatnak, a helyeket fel kell deríteni. Egy készletlista, például egy listaentitás azért nem működne, mert ekkor csak a listában szereplő városokat lehetne kinyerni.

A kiindulási és célvárosokhoz tartozó szerepkörneveknek egyedinek kell lenniük az összes entitás körében. A szerepkörök egyedisége könnyedén biztosítható, ha a tartalmazó entitáshoz kötjük őket egy elnevezési stratégia alkalmazásával. A **NewEmployeeRelocation** entitás, egy egyszerű entitás kétféle szerepkörben működnek: **NewEmployeeReloOrigin** és **NewEmployeeReloDestination**. A „Relo” a relocation (áthelyezés) szó rövidítése.

Mivel a `Move new employee Robert Williams from Sacramento and San Francisco` példa kimondott szöveg csak gép által tanult entitásokkal rendelkezik, fontos, hogy elegendő példaszöveget adjunk meg az adott szándékhoz, hogy a rendszer észlelje az entitásokat.  

**Habár a minták lehetővé teszik, hogy kevesebb kimondottszöveg-példát adjunk meg, ha a rendszer nem észleli az entitásokat, akkor a mintát nem lehet megfeleltetni.**

Ha problémája van egy egyszerű entitás észlelésével, mert az egy név (például egy városé), érdemes megadni egy hasonló értékeket tartalmazó kifejezéslistát. Ez segíti a város nevének észlelését, mivel egy újabb jelet ad a LUIS számára az adott típusú szóra vagy kifejezésre vonatkozóan. A kifejezéslisták a mintának csak az entitások észlelésében segítenek, ami azonban szükséges ahhoz, hogy a minta megfeleltethető legyen. 

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Meglévő oktatóalkalmazás használata
> * Új entitások létrehozása
> * Új szándék létrehozása
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról
> * Szerepköröket tartalmazó minták létrehozása
> * Városneveket tartalmazó kifejezéslista létrehozása
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata
Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Amennyiben nem rendelkezik az előző oktatóanyagból származó EmberiErőforrások alkalmazással, kövesse a következő lépéseket:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-patterns-HumanResources-v2.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `roles` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.

## <a name="create-new-entities"></a>Új entitások létrehozása

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Válassza az **Entities** (Entitások) elemet a bal oldali navigációs menüben. 

3. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget.

4. Az előugró ablakban adja meg a `NewEmployee` értéket egy **Egyszerű** entitásként.

5. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget.

6. Az előugró ablakban adja meg a `NewEmployeeRelocation` értéket egy **Egyszerű** entitásként.

7. Az entitások listájából válassza ki a **NewEmployeeRelocation** entitást. 

8. Adja meg az első szerepkört (`NewEmployeeReloOrigin`), és válassza a bevitel lehetőséget.

9. Adja meg a második szerepkört (`NewEmployeeReloDestination`), és válassza a bevitel lehetőséget.

## <a name="create-new-intent"></a>Új szándék létrehozása
Az ezekben a lépésekben szereplő entitások címkézése egyszerűbb lehet, ha a címkézés előtt eltávolítjuk az előre összeállított keyPhrase entitást, majd újra hozzáadjuk a szakasz lépéseinek elvégzését követően. 

1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

2. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

3. Az előugró párbeszédpanelen adja a szándéknak a következő nevet: `NewEmployeeRelocationProcess`.

4. Adja meg a következő kimondottszöveg-példákat, és címkézze az új entitásokat. Az entitás és szerepkör értékek félkövérrel vannak szedve. Ne felejtsen átváltani a **Tokens View** (Jogkivonatok) nézetre, ha egyszerűbbnek találja a szöveg címkézését. 

    A szándék címkézésekor nem kell megadni az entitás szerepkörét. Ezt később, a minta létrehozásakor teheti meg. 

    |Kimondott szöveg|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |**Bob Jones** áthelyezése **Seattle**-ből **Los Colinasba**|Bob Jones|Seattle, Los Colinas|
    |**Dave C. Cooper** áthelyezése **Redmondból** **New York City**-be|Dave C. Cooper|Redmond, New York City|
    |**Jim Paul Smith** áthelyezése **Torontóból** **Nyugat-Vancouverbe**|Jim Paul Smith|Toronto, Nyugat-Vancouver|
    |**J. Benson** áthelyezése **Bostonból** **Staines-upon-Thamesbe**|J. Benson|Boston, Staines-upon-Thames|
    |**Travis „Trav” Hinton** áthelyezése **Castelo Brancóból** **Orlandóba**|Travis „Trav” Hinton|Castelo Branco, Orlando|
    |**Trevor Nottington III** áthelyezése **Aranda de Dueróból** **Boise**-ba|Trevor Nottington III|Aranda de Duero, Boise|
    |**Dr. Greg Williams** áthelyezése **Orlandóból** **Ellicott Citybe**|Dr. Greg Williams|Orlando, Ellicott City|
    |**Robert „Bobby” Gregson** áthelyezése **Kansas Cityből** **San Juan Capistranóba**|Robert „Bobby” Gregson|Kansas City, San Juan Capistrano|
    |**Patti Owens** áthelyezése **Bellevue**-ből **Rockfordba**|Patti Owens|Bellevue, Rockford|
    |**Janet Bartlet** áthelyezése **Tuscanből** **Santa Fébe**|Janet Bartlet|Tuscan, Santa Fe|

    Az alkalmazottak neveinek számos különféle előtagja, szószáma, szintaxisa és utótagja lehet. Ezt fontos megállapítani ahhoz, hogy a LUIS képes legyen felismerni az új alkalmazottak neveire jellemző változatokat. A városok neveinek szintén változó a szószáma és a szintaxisa. Ez a változatosság azért fontos, hogy a LUIS ez alapján megtanulja, hogyan jelenhetnek meg ezek az entitások a felhasználók kimondott szövegeiben. 
    
    Ha bármelyik entitásának állandó és változatlan lenne a szószáma, akkor azt tanítanánk meg a LUIS-nak, hogy ennek az entitásnak a szószáma változatlanul mindig ennyi. A LUIS nem tudná megfelelően előrejelezni a változatok szélesebb körét, mivel nem találkozott ilyen változatokkal. 

    Ha eltávolította a keyPhrase entitást, most adja hozzá újra az alkalmazáshoz.

## <a name="train"></a>Betanítás

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Közzététel

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Szándék és entitások lekérése a végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Move Wayne Berry from Miami to Mount Vernon`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. 

    ```json
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

A szándék-előrejelzési pontszám mindössze kb. 50%. Ha az ügyfél alkalmazása magasabb pontszámot igényel, akkor javítani kell rajta. Az entitások sem voltak előrejelezve.

Az egyik hely ki lett nyerve, de a másik nem. 

A minták segítenek abban, hogy az előrejelzési pontszám minél magasabb legyen, azonban a rendszernek megfelelően előre kell jeleznie az entitásokat ahhoz, hogy a minta és a kimondott szöveg között egyezést találjon. 

## <a name="pattern-with-roles"></a>Szerepköröket tartalmazó minta

1. A felső navigációs ablakban válassza a **Build** (Létrehozás) elemet.

2. A bal oldali navigációs menüben válassza a **Patterns** (Minták) lehetőséget.

3. A **Select an intent** (Szándék kiválasztása) legördülő listából válassza ki a **NewEmployeeRelocationProcess** lehetőséget. 

4. Írja be a következő mintát: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Ha betanítja, közzéteszi és lekérdezi a végpontot, lehet, hogy az entitások nem találhatók, így a minta nem felel meg, ezáltal az előrejelzés nem javult. Ennek az az oka, hogy nincs elég címkézett entitásokkal rendelkező kimondottszöveg-példa. A probléma megoldásához még több példa hozzáadása helyett adjon hozzá egy kifejezéslistát.

## <a name="cities-phrase-list"></a>Városneveket tartalmazó kifejezéslista
A városok nevei a személyek neveihez hasonlóan összetettek lehetnek abból a szempontból, hogy szavakat és írásjelek bármilyen kombinációját tartalmazhatják. A régió és a világ városai ismertek, így a LUIS-nak szüksége van egy városneveket tartalmazó kifejezéslistára, amely alapján elkezdhet tanulni. 

1. Válassza a **Phrase lists** (Kifejezéslisták) lehetőséget az **Improve app performance** (Az alkalmazás teljesítményének növelése) menüben. 

2. Adja a listának a `Cities` nevet, majd adja hozzá a következőket: `values`

    |Kifejezéslista értékei|
    |--|
    |Seattle|
    |San Diego|
    |New York City|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Ne adja hozzá a világ összes városát, de még csak a régió összes városát sem. A LUIS-nak meg kell tanulnia a lista alapján általánosítani, hogy milyen egy városnév. Ügyeljen arra, hogy a **These values are interchangeable** (Az értékek felcserélhetők) beállítás ki legyen választva. Ez a beállítás azt jelenti, hogy a listában szereplő szavakat a rendszer szinonimaként kezeli. 

3. Tanítsa be és tegye közzé az alkalmazást.

## <a name="get-intent-and-entities-from-endpoint"></a>Szándék és entitások lekérése a végpontból

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Move wayne berry from miami to mount vernon`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. 

    ```json
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

A szándék pontszáma most már sokkal magasabb, a szerepkörök nevei pedig az entitás válaszának részét képezik.

## <a name="hierarchical-entities-versus-roles"></a>A hierarchikus entitások és a szerepkörök

A [hierarchiai oktatóanyagban](luis-quickstart-intent-and-hier-entity.md) a **MoveEmployee** szándék észlelte, mikor kell áthelyezni egy meglévő alkalmazottat egy épületből és irodából egy másikba. A kimondottszöveg-példák tartalmazták a kiindulási és célhelyeket, de nem használtak szerepköröket. Ehelyett a kiindulási és a célhelyek alá voltak rendelve a hierarchikus entitásnak. 

Ebben az oktatóanyagban az Emberi erőforrások alkalmazás észleli azokat a kimondott szövegeket, amelyek egy új alkalmazott egyik városból a másikba való áthelyezéséről szólnak. Ez a kétféle kimondott szöveg azonos, de a LUIS különböző módokon ismeri fel őket.

|Oktatóanyag|Példa kimondott szöveg|Kiindulási és célhelyek|
|--|--|--|
|[Hierarchikus (nincsenek szerepkörök)](luis-quickstart-intent-and-hier-entity.md)|Jill Jones áthelyezése innen: **a-2349**, ide: **b-1298**|a-2349, b-1298|
|Ez az oktatóanyag (szerepkörökkel)|Billy Patterson áthelyezése **Yumából** **Denverbe**.|Yuma, Denver|

További információt a [szerepköröket és a hierarchikus entitásokat](luis-concept-roles.md#roles-versus-hierarchical-entities) ismertető cikkben talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban hozzáadott egy entitást szerepkörökkel és egy szándékot kimondottszöveg-példákkal. Az entitást használó első végpont-előrejelzés helyesen előrejelezte a szándékot, de alacsony megbízhatósági pontszámmal. Csak a két entitás egyikét észlelte. Ezután az oktatóanyag hozzáadott egy, az entitásszerepköröket használó mintát, és egy kifejezéslista hozzáadásával növelte a városnevek értékét a kimondott szövegekben. A második végpont-előrejelzés magasabb megbízhatósági pontszámot adott vissza, és mindkét entitásszerepkört megtalálta. 

> [!div class="nextstepaction"]
> [Ajánlott eljárások a LUIS-alkalmazásokhoz](luis-concept-best-practices.md)
