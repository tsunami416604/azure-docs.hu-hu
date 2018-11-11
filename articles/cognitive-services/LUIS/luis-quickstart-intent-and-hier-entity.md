---
title: '5. oktatóanyag: Szülő/gyermek kapcsolatok – LUIS hierarchikus entitás környezetfüggő tanult adatokhoz'
titleSuffix: Azure Cognitive Services
description: Kapcsolódó adatrészletek keresése kontextus alapján. Kapcsolódó lehet például az indulási hely és a cél egy épületből és irodából egy másik épületbe és irodába történő fizikai mozgás során.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d3adb6c27d3550777f0a5b34b3e7e329f42432ab
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138463"
---
# <a name="tutorial-5-extract-contextually-related-data"></a>5. oktatóanyag: Szövegkörnyezet szerint kapcsolódó adatok kinyerése
Ebben az oktatóanyagban kapcsolódó adatrészleteket keresünk szövegkörnyezet alapján. Kapcsolódó lehet például az indulási hely és a cél egy épületből és irodából egy másik épületbe és irodába történő fizikai mozgás során. Munkarendelés létrehozásához mindkét adatrészletre szükség lehet, és az adatrészletek kapcsolódnak egymáshoz.  

Ez az alkalmazás meghatározza, hova lesz áthelyezve egy alkalmazott az indulási helyről (épület és iroda) az érkezési helyre (épület és iroda). A hierarchikus entitás segítségével határozza meg a kimondott szövegben található helyeket. A **hierarchikus** entitás célja kapcsolódó adatok keresése kontextus alapján a kimondott szövegben. 

Ez a hierarchikus entitás jó választás az ilyen típusú adatok esetén, mivel a két adatrészlet:

* Egyszerű entitás.
* Kapcsolódik egymáshoz a kimondott szövegkörnyezetben.
* Adott szavakat használ az egyes helyek jelzéséhez. Ilyen szavak például: innen/oda, elhagyja/felé tart, távolodik/közeledik.
* Mindkét hely általában egyazon kimondott szövegrészletben található. 
* Csoportosítását és feldolgozását az ügyfélalkalmazásoknak egy információegységként kell végezniük.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Meglévő oktatóalkalmazás használata
> * Szándék hozzáadása 
> * Forrással és célgyermekkel rendelkező helyhierarchikus entitás hozzáadása
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata
Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Amennyiben nem rendelkezik az előző oktatóanyagból származó EmberiErőforrások alkalmazással, kövesse a következő lépéseket:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-list-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `hier` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Előre összeállított szám entitás eltávolítása az alkalmazásból
A teljes kimondott szöveg megjelenítéséhez és a hierarchikus gyermek megjelöléséhez ideiglenesen távolítsa el az előre összeállított szám entitást.

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Válassza az **Entities** (Entitások) elemet a bal oldali menüben.

3. Jelölje be a listában lévő számentitás bal oldalán található jelölőnégyzetet. Válassza a **Törlés** elemet. 

## <a name="add-utterances-to-moveemployee-intent"></a>Kimondott szövegek hozzáadása a MoveEmployee szándékhoz

1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

2. A szándékok listájából válassza ki a **MoveEmployee** elemet.

3. Vegye fel az alábbi kimondott szövegpéldákat:

    |Példák kimondott szövegekre|
    |--|
    |John W. Smith áthelyezése **ide**: a-2345|
    |Jill Jones átirányítása **ide**: b-3499|
    |x23456 áthelyezésének megszervezése **innen**: hh-2345, **ide**: e-0234|
    |Papírmunka megkezdése a következő beállításához: x12345 **elhagyja** a következőt: a-3459, és az f-34567 **felé tart**|
    |425-555-0000 áthelyezése: **távolodik** a következőtől: g-2323, és **közeledik** hh-2345 felé|

    [![Képernyőkép a LUIS-ról, új kimondott szövegek a MoveEmployee szándékban](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    A [listaentitással](luis-quickstart-intent-and-list-entity.md) kapcsolatos oktatóanyagban az alkalmazottak név, e-mail-cím, telefonmellék, mobiltelefonszám, vagy USA-beli szövetségi társadalombiztosítási szám alapján vannak kijelölve. Ezeket az alkalmazotti számokat a kimondott szövegekben használja a rendszer. Az előző kimondott szövegpélda különböző módokat tartalmaz az indulási és érkezési hely jelzésére, amelyek félkövér betűvel vannak kiemelve. Néhány kimondott szöveg szándékosan csak érkezési hellyel rendelkezik. Ez segít megérteni a LUIS-nak, hogyan helyezkednek el ezek a helyek a kimondott szövegben, amikor az indulási hely nincs megadva.     

    [!include[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="create-a-location-entity"></a>Hely entitás létrehozása
A LUIS-nak meg kell értenie, hogy a hely pontosan mit jelent. Ez a kimondott szövegben található indulási és érkezési hely címkézésével lehetséges. Ha a kimondott szöveget jogkivonat (nyers) nézetben szeretné megtekinteni, válassza az **Entities View** (Entitások nézet) címkével ellátott, a kimondott szövegek fölötti sávban található kapcsolót. Miután átkapcsolta a kapcsolót, a vezérlő **Tokens View** (Jogkivonatok nézet) címkével lesz ellátva.

Vegyük például a következő kimondott szöveget:

```JSON
mv Jill Jones from a-2349 to b-1298
```

A kimondott szövegben két hely van megadva: `a-2349` és `b-1298`. Tegyük fel, hogy a betű megfelel egy épület nevének, a szám pedig az irodát jelzi az épületen belül. Ezért érdemes a kettőt egy hierarchikus entitás, a `Locations` entitás gyermekeiként csoportosítani, mert mindkét információt ki kell nyerni a kimondott szövegből a kérelem teljesítéséhez az ügyfélalkalmazásban, és a két információ kapcsolódik egymáshoz. 
 
Ha egy hierarchikus entitásnak csak az egyik gyermeke (az indulás vagy az érkezés) van jelen, az alkalmazás akkor is kinyeri. Egy vagy néhány gyermek kinyeréséhez nem szükséges az összes gyermeket megtalálni. 

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

2. Válassza az **Add prebuilt entity** (Előre összeállított entitás hozzáadása) gombot.

3. Az előre összeállított entitások listájából válassza a **number** (szám) entitást, majd kattintson a **Done** (Kész) gombra.

    ![Képernyőkép: számválasztó az előre összeállított entitások párbeszédpanelen](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


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
    
    A rendszer előrejelzi a helyes szándékot, és az entitások tömbje az indulás és a cél értékét is tartalmazza a megfelelő **entitás** tulajdonságban.
    

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Használhat reguláris kifejezést minden helyhez?
Igen, létrehozhatja a reguláriskifejezés-entitást indulási és érkezési szerepkörökkel, és használhatja egy mintában.

Az ebben a példában lévő helyek, például az `a-1234`, egy adott formátumot követnek: egy vagy két betű, kötőjellel elválasztva, ezután egy 4 vagy 5 számból álló sorozat. Ez az adat leírható reguláris kifejezés entitásként az egyes helyekhez kapcsolódó szerepkörrel. A szerepkörök csak a mintákhoz érhetők el. Ezen kimondott szövegek alapján létrehozhat mintákat, majd létrehozhat egy reguláris kifejezést a helyformátumhoz, és hozzáadhatja a mintákhoz. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="hierarchical-entities-versus-roles"></a>A hierarchikus entitások és a szerepkörök

További információt a [szerepköröket és a hierarchikus entitásokat](luis-concept-roles.md#roles-versus-hierarchical-entities) ismertető cikkben talál.

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag létrehozott egy új szándékot, és kimondott példaszövegeket adott hozzá az indulási és célhelyek környezetfüggő tanult adataihoz. Amint megtörtént az alkalmazás betanítása és közzététele, az ügyfélalkalmazások felhasználhatják az adott információt a megfelelő információt tartalmazó mozgatási jegy létrehozásához.

> [!div class="nextstepaction"] 
> [Ismerkedés az összetett entitások hozzáadásának módjával](luis-tutorial-composite-entity.md) 