---
title: Összetett entitást"
titleSuffix: Azure Cognitive Services
description: Adjon hozzá egy összetett entitást szeretné a különböző típusú kinyert adatok egyetlen tartalmazó entitásba. A kötegelés az adatokat, az ügyfélalkalmazás is könnyen kinyerheti az különböző adattípusok kapcsolódó adatokat.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b5923d5cd4a704dda76e33ee6a2b76cfd903219d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079211"
---
# <a name="tutorial-6-group-and-extract-related-data"></a>Oktatóanyag 6: Csoport és a kapcsolódó adatokat nyerhet ki
Ebben az oktatóanyagban egy összetett entitás szeretné a kinyert adatokkal történő tartalmazó egyetlen entitás hozzáadása. A kötegelés az adatokat, az ügyfélalkalmazás is könnyen kinyerheti az különböző adattípusok kapcsolódó adatokat.

Az összetett entitás az a célja, hogy egy szülőentitás kategória kapcsolódó entitások csoportosíthatja. Az információk létezik külön entitásokként összetett létrehozása előtt. Ez hasonlít a hierarchikus, de eltérő típusú entitásokat is tartalmazhat. 

Az összetett entitás ideális ehhez az adattípushoz, mert az adatok:

* Kapcsolódnak egymáshoz. 
* Többféle típusú entitás használja.
* Csoportosítását és feldolgozását az ügyfélalkalmazásoknak egy információegységként kell végezniük.

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Meglévő oktatóalkalmazás használata
> * Összetett entitás hozzáadása 
> * Betanítás
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata
Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Amennyiben nem rendelkezik az előző oktatóanyagból származó EmberiErőforrások alkalmazással, kövesse a következő lépéseket:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-hier-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `composite` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes.


## <a name="composite-entity"></a>Összetett entitást
Hozzon létre egy összetett entitást, ha a különálló entitások logikailag csoportosíthatók, és ez a logikai csoportosítás akkor hasznos, az ügyfélalkalmazásnak. 

Ebben az alkalmazásban, az alkalmazott neve van megadva a **alkalmazott** entitás listában, és tartalmazza a neve, e-mail címét, vállalati a mellék, mobiltelefonszám és USA szinonimák Szövetségi adózási azonosítója. 

A **MoveEmployee** szándékot rendelkezik példa utterances kérése egy alkalmazott egy épület és az office áthelyezni egy másikba. Épület nevében betűk, a rendszer: "A", "B", stb. amíg irodák numerikus: "1234", "13245". 

A példa utterances a **MoveEmployee** szándékot tartalmazza:

|Példák kimondott szövegekre|
|--|
|John W át. A-2345 Smith|
|x12345 váltása holnap: h-1234|
 
Az áthelyezési kérelemnek tartalmaznia kell az alkalmazott (bármely szinonimát használatával), illetve a végső épület és az office helyét. A kérelem is tartalmazhat az eredeti office, valamint egy dátumot, az áthelyezés történjen. 

A kibontott adatok végpontról tartalmazzák ezt az információt és küldje vissza a a `RequestEmployeeMove` összetett entitást:

```json
"compositeEntities": [
  {
    "parentType": "RequestEmployeeMove",
    "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
    "children": [
      {
        "type": "builtin.datetimeV2.datetime",
        "value": "march 3 2 p.m"
      },
      {
        "type": "Locations::Destination",
        "value": "z - 2345"
      },
      {
        "type": "Employee",
        "value": "jill jones"
      },
      {
        "type": "Locations::Origin",
        "value": "a - 1234"
      }
    ]
  }
]
```

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Az a **leképezések** lapon jelölje be **MoveEmployee** szándékot. 

3. Válassza a Nagyító ikont, az eszköztáron a kimondott szöveg szűréséhez. 

    [![Képernyőkép a LUIS "MoveEmployee" szándékot Nagyító gomb kiemelésével](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "képernyőképe a LUIS \"MoveEmployee\" szándékot Nagyító gomb kiemelésével")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Adja meg `tomorrow` be a szűrő szövegmezőbe az utterance (kifejezés) található `shift x12345 to h-1234 tomorrow`.

    [![Képernyőkép a LUIS "MoveEmployee" leképezés "holnap innovációit" szűrővel kiemelt](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "képernyőképe a LUIS \"MoveEmployee\" leképezés \"holnap innovációit\" szűrővel kiemelésével")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    Hogy az entitás szűrés datetimeV2, kiválasztásával **entitás szűrők** majd **datetimeV2** a listából. 

5. Válassza ki az első entitás `Employee`, majd **Zabalit do összetett entitást** előugró menü listájában. 

    [![Képernyőkép a LUIS első entitás kiválasztásával a kiemelt összetett "MoveEmployee" szándékot](media/luis-tutorial-composite-entity/hr-create-entity-1.png "képernyőképe a LUIS \"MoveEmployee\" szándékot összetett, kiemelve az első entitás kiválasztása")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Azonnal válassza ki az utolsó entitás `datetimeV2` az utterance (kifejezés) található. Zöld sáv megjelenítése a kiválasztott szavakat jelző egy összetett entitás alapján. Az előugró menüben adja meg az összetett név `RequestEmployeeMove` majd válassza ki az Entert. 

    [![Képernyőkép a LUIS utolsó entitás kiválasztása az összetett és entitások kiemelt "MoveEmployee" szándékot](media/luis-tutorial-composite-entity/hr-create-entity-2.png "képernyőképe a LUIS \"MoveEmployee\" szándékot utolsó entitás kiválasztása az összetett és kiemelt entitás létrehozása")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. A **milyen típusú entitást szeretne létrehozni?**, szinte az összes, a szükséges mezők szerepelnek a listában. Csak a származási helye nincs megadva. Válassza ki **adjon hozzá egy gyermek entitásnak**, jelölje be **Locations::Origin** meglévő entitások, majd válassza ki a listáról **kész**. 

    Figyelje meg, hogy az előre összeállított entitások, szám, az összetett entitás hozzá lett adva. Ha egy előre összeállított entitások jelennek meg a kezdő és záró jogkivonatok összetett entitás között lehetnek, az összetett entitás ezeket előre összeállított entitások kell tartalmaznia. Ha az előre összeállított entitások nem szerepelnek, az összetett entitás nem jelzett megfelelően, de az egyes elemeket.

    ![Képernyőkép a LUIS "MoveEmployee" szándékot egy másik entitás hozzáadása az előugró ablakban](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Válassza a Nagyító ikonra az eszköztáron a szűrő eltávolításához. 

9. Távolítsa el a word `tomorrow` a szűrés, így újra valamennyi példa megcímkézzen láthatja. 

## <a name="label-example-utterances-with-composite-entity"></a>Az összetett entitás címke példa kimondott szöveg


1. Valamennyi példa utterance (kifejezés) válassza ki a bal szélső entitást, amely az összetett kell lennie. Válassza ki **Zabalit do összetett entitást**.

    [![Képernyőkép a LUIS első entitás kiválasztásával a kiemelt összetett "MoveEmployee" szándékot](media/luis-tutorial-composite-entity/hr-label-entity-1.png "képernyőképe a LUIS \"MoveEmployee\" szándékot összetett, kiemelve az első entitás kiválasztása")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Válassza ki az utolsó szót az összetett entitásban, majd válassza ki **RequestEmployeeMove** az előugró menüben. 

    [![Képernyőkép a LUIS utolsó entitás kiválasztásával a kiemelt összetett "MoveEmployee" szándékot](media/luis-tutorial-composite-entity/hr-label-entity-2.png "képernyőképe a LUIS \"MoveEmployee\" szándékot összetett, kiemelve az utolsó entitás kiválasztása")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Ellenőrizze, hogy a célt az összes utterances vannak ellátva, az összetett entitáshoz. 

    [![Képernyőkép a LUIS "MoveEmployee" címkével ellátott összes utterances a](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "\"MoveEmployee\" címkével ellátott összes utterances a képernyőkép, LUIS")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train"></a>Betanítás

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Közzététel

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Szándék és entitások lekérése a végpontból 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`. Az utolsó lekérdezési karakterlánc paraméter `q`, az utterance (kifejezés) lekérdezést. 

    Mivel ez a teszt ellenőrzése az összetett ki kell olvasni megfelelően, egy tesztet vagy tartalmazhat egy meglévő minta utterance (kifejezés) vagy egy új utterance (kifejezés). Ellenőrzi, hogy a gyermekentitások tartalmazza az összetett entitásban.

    ```json
    {
      "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9959525
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9959525
        },
        {
          "intent": "GetJobInformation",
          "score": 0.009858314
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00728598563
        },
        {
          "intent": "FindForm",
          "score": 0.0058053555
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.005371796
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00266987388
        },
        {
          "intent": "None",
          "score": 0.00123299169
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00116407464
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00102653319
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0006628214
        }
      ],
      "entities": [
        {
          "entity": "march 3 2 p.m",
          "type": "builtin.datetimeV2.datetime",
          "startIndex": 41,
          "endIndex": 54,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2018-03-03 14:00:00"
              },
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2019-03-03 14:00:00"
              }
            ]
          }
        },
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 5,
          "endIndex": 14,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "z - 2345",
          "type": "Locations::Destination",
          "startIndex": 31,
          "endIndex": 36,
          "score": 0.9690751
        },
        {
          "entity": "a - 1234",
          "type": "Locations::Origin",
          "startIndex": 21,
          "endIndex": 26,
          "score": 0.9713137
        },
        {
          "entity": "-1234",
          "type": "builtin.number",
          "startIndex": 22,
          "endIndex": 26,
          "resolution": {
            "value": "-1234"
          }
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 32,
          "endIndex": 36,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 47,
          "endIndex": 47,
          "resolution": {
            "value": "3"
          }
        },
        {
          "entity": "2",
          "type": "builtin.number",
          "startIndex": 50,
          "endIndex": 50,
          "resolution": {
            "value": "2"
          }
        },
        {
          "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "type": "RequestEmployeeMove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "RequestEmployeeMove",
          "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "children": [
            {
              "type": "builtin.datetimeV2.datetime",
              "value": "march 3 2 p.m"
            },
            {
              "type": "Locations::Destination",
              "value": "z - 2345"
            },
            {
              "type": "Employee",
              "value": "jill jones"
            },
            {
              "type": "Locations::Origin",
              "value": "a - 1234"
            }
          ]
        }
      ]
    }
    ```

  Az utterance (kifejezés) egy összetett entitások tömböt ad vissza. Minden entitás egy típusa és értéke van megadva. Minden gyermek entitásnak pontossággal megkereséséhez használja a megfelelő elem található entitások tömbben típusa és értéke összetett tömb elemének kombinációja.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy összetett entitást, hogy magába foglalja a meglévő entitásokat. Ez lehetővé teszi, hogy az ügyfélalkalmazás található kapcsolódó adatok csoportja folytatja a beszélgetés különböző adattípusokat. Egy ügyfélalkalmazás az emberi erőforrások alkalmazás megkérheti, mely napokon és időpontban az áthelyezés kell kezdődik és ér véget. Azt is kérheti kapcsolatos egyéb logisztikai a movesuch, mint egy fizikai phone. 

> [!div class="nextstepaction"] 
> [Egy kifejezés listát egy egyszerű entitás hozzáadása](luis-quickstart-primary-and-secondary-data.md)  
