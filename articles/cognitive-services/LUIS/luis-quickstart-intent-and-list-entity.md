---
title: Pontos egyezés egyeztetése
titleSuffix: Azure Cognitive Services
description: Elemek előre meghatározott listájával egyező adatok lekérése. A lista minden elemének lehetnek pontosan megegyező szinonimái
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: cf2cd8a3dade408bf98a6fcc64af0d4ee4419fa0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718248"
---
# <a name="tutorial-4-extract-exact-text-matches"></a>4. oktatóanyag: Pontos szövegegyezések kinyerése
Ebben az oktatóanyagban megismerheti, hogyan kérheti le az elemek előre meghatározott listájával egyező adatokat. A lista minden eleme tartalmazhatja szinonimák egy listáját. Az Emberi erőforrások alkalmazásban egy alkalmazottat számos kulcsfontosságú információ azonosíthat, például a név, az e-mail-cím, a telefonszám és az USA-beli szövetségi adóazonosító szám. 

Az Emberi Erőforrások alkalmazásnak meg kell határoznia, melyik alkalmazottat helyezik át az egyik épületből egy másikba. Az alkalmazott költözéséről szóló kimondott szövegben a LUIS meghatározza a szándékot, és úgy nyeri ki az alkalmazottat, hogy az ügyfélalkalmazás létrehozhasson egy szokványos sorrendet az alkalmazott áthelyezéséhez.

Ez az alkalmazás egy listaentitást használ az alkalmazott kinyeréséhez. Az alkalmazottra név, vállalati telefonmellék, e-mail-cím, mobiltelefonszám vagy USA-beli szövetségi társadalombiztosítási szám alapján lehet hivatkozni. 

A listaentitás megfelelő választás az ilyen típusú adatok esetén, amikor:

* Az adatok értékei egy ismert készletbe tartoznak.
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* A kimondott szöveg egy része pontosan megegyezik egy szinonimával vagy a kanonikus névvel. 

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Meglévő oktatóalkalmazás használata
> * MoveEmployee szándék hozzáadása
> * Listaentitás hozzáadása 
> * Betanítás 
> * Közzététel
> * Szándék és entitások lekérése a végpontról

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Meglévő alkalmazás használata
Folytassa az előző oktatóanyagban létrehozott **EmberiErőforrások** nevű alkalmazással. 

Amennyiben nem rendelkezik az előző oktatóanyagból származó EmberiErőforrások alkalmazással, kövesse a következő lépéseket:

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-regex-HumanResources.json).

2. Importálja a JSON-t egy új alkalmazásba.

3. A **Manage** (Kezelés) szakasz **Versions** (Verziók) lapján klónozza a verziót, és adja neki a `list` nevet. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. Mivel a verzió neve az URL-útvonal részét képezi, a név nem tartalmazhat olyan karaktert, amely URL-címben nem érvényes. 


## <a name="moveemployee-intent"></a>MoveEmployee szándék

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

3. Az előugró párbeszédpanelen írja be a `MoveEmployee` karakterláncot, majd válassza a **Kész** elemet. 

    ![Új szándék létrehozása párbeszédpanel képernyőképe](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Adjon hozzá kimondott példaszövegeket a szándékhoz.

    |Példák kimondott szövegekre|
    |--|
    |John W. Smith áthelyezése innen: B-1234, ide: H-4452|
    |john.w.smith@mycompany.com áthelyezése a b-1234 irodából a h-4452 irodába|
    |x12345 váltása holnap: h-1234|
    |425-555-1212 elhelyezése a következőben: HH-2345|
    |123-45-6789 áthelyezése innen: A-4321, ide: J-23456|
    |Jill Jones áthelyezése innen: D-2345, ide: J-23456|
    |jill-jones@mycompany.com váltása: M-12345|
    |x23456 – M-12345|
    |425-555-0000 – h-4452|
    |234-56-7891 – hh-2345|

    [ ![Képernyőkép a Szándék lapról, az új kimondott szövegek kiemelésével](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

    Ne feledje, hogy a number és a datetimeV2 az előző oktatóanyagban hozzá lettek adva, így automatikusan meg lesznek címkézve, ha szerepelnek egy kimondottszöveg-mintában.

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="employee-list-entity"></a>Alkalmazottlista-entitás
Most, hogy a **MoveEmployee** szándéknak vannak kimondottszöveg-mintái, a LUIS-nak meg kell értenie, hogy az alkalmazott pontosan micsoda. 

Az egyes elemek elsődleges, _kanonikus_ neve az alkalmazotti szám. Ebben a tartományban az egyes kanonikus nevek szinonimáinak példái: 

|Szinonima célja|Szinonima értéke|
|--|--|
|Name (Név)|John W. Smith|
|E-mail-cím|john.w.smith@mycompany.com|
|Telefonmellék|x12345|
|Saját mobiltelefonszám|425-555-1212|
|USA-beli szövetségi társadalombiztosítási szám (SSN)|123-45-6789|


1. Válassza az **Entities** (Entitások) elemet a bal oldali ablaktáblán.

2. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget.

3. A felugró párbeszédpanelen adja meg az `Employee` értéket az entitás neveként, és a **List** (Lista) értéket az entitás típusaként. Válassza a **Done** (Kész) lehetőséget.  

    [![Új entitás felugró párbeszédpanel létrehozásának képernyőképe](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "létrehozása új entitás felugró párbeszédpanel képernyőképe")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. Az Employee entity (Alkalmazottentitás) lapon az új értéknek adja meg a következőt: `Employee-24612`.

    [![Képernyőkép a érték megadása](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "érték megadása képernyőképe")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. Szinonimák esetében vegye fel az alábbi értékeket:

    |Szinonima célja|Szinonima értéke|
    |--|--|
    |Name (Név)|John W. Smith|
    |E-mail-cím|john.w.smith@mycompany.com|
    |Telefonmellék|x12345|
    |Saját mobiltelefonszám|425-555-1212|
    |USA-beli szövetségi társadalombiztosítási szám (SSN)|123-45-6789|

    [![Képernyőkép a szinonimák megadásáról](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Képernyőkép a szinonimák megadása")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. Új értékként írja be a következőt: `Employee-45612`.

7. Szinonimák esetében vegye fel az alábbi értékeket:

    |Szinonima célja|Szinonima értéke|
    |--|--|
    |Name (Név)|Jill Jones|
    |E-mail-cím|jill-jones@mycompany.com|
    |Telefonmellék|x23456|
    |Saját mobiltelefonszám|425-555-0000|
    |USA-beli szövetségi társadalombiztosítási szám (SSN)|234-56-7891|

## <a name="train"></a>Betanítás

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Közzététel

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Szándék és entitások lekérése a végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Lépjen az URL-cím végéhez, és írja be a következőt: `shift 123-45-6789 from Z-1242 to T-54672`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **q**uery. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a `Employee` szándékot kell visszaadnia, kinyerve a következőt: `MoveEmployee`.

  ```json
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  A rendszer megtalálta az alkalmazottat, és visszaadta `Employee` típusúként, `Employee-24612` megoldásértékkel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag létrehozott egy új szándékot, kimondottszöveg-példákat adott hozzá, majd létrehozott egy listaentitást a pontos szövegegyezések kimondott szövegből történő kinyerése céljából. Az alkalmazás betanítása és közzététele után egy végpontlekérdezés azonosította a szándékot, és visszaadta a kinyert adatokat.

> [!div class="nextstepaction"]
> [Hierarchikus entitás hozzáadása az alkalmazáshoz](luis-quickstart-intent-and-hier-entity.md)

