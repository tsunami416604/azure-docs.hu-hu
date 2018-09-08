---
title: Az oktatóanyag pedig az entitások összetett bontsa ki az összetett adatokat – Azure |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre összetett entitásokat a különböző típusú entitások adatainak kinyeréséhez a LUIS-alkalmazás.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 17a8110624975d8053ad69c5bf30477e6d715ee8
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159826"
---
# <a name="tutorial-6-add-composite-entity"></a>Oktatóanyag: 6. Összetett entitás hozzáadása 
Ebben az oktatóanyagban egy összetett entitás szeretné a befoglaló entitás be kinyert adatok hozzáadása.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

<!-- green checkmark -->
> [!div class="checklist"]
> * Összetett entitásainak ismertetése 
> * Adatokat nyerhet ki összetett entitás hozzáadása
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Előkészületek
Ha még nincs meg az Emberi erőforrások alkalmazása a [hierarchikus entitás](luis-quickstart-intent-and-hier-entity.md) oktatóanyagából, [importálja](luis-how-to-start-new-app.md#import-new-app) a JSON-t egy új alkalmazásba a [LUIS](luis-reference-regions.md#luis-website) webhelyén. Az importálandó alkalmazás a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json) GitHub-adattárban található.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `composite`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne.  

## <a name="composite-entity-is-a-logical-grouping"></a>Összetett entitás, logikai csoportosítása 
Az összetett entitás az a célja, hogy egy szülőentitás kategória kapcsolódó entitások csoportosíthatja. Az információk létezik külön entitásokként összetett létrehozása előtt. Azt a hierarchikus hasonló, de több entitástípus is tartalmazhat. 

 Hozzon létre egy összetett entitást, ha a különálló entitások logikailag csoportosíthatók, és ez a logikai csoportosítás akkor hasznos, az ügyfélalkalmazásnak. 

Ebben az alkalmazásban, az alkalmazott neve van megadva a **alkalmazott** entitás listában, és tartalmazza a neve, e-mail címét, vállalati a mellék, mobiltelefonszám és USA szinonimák Szövetségi adózási azonosítója. 

A **MoveEmployee** szándékot rendelkezik példa utterances kérése egy alkalmazott egy épület és az office áthelyezni egy másikba. Épület nevében betűk, a rendszer: "A", "B", stb. amíg irodák numerikus: "1234", "13245". 

A példa utterances a **MoveEmployee** szándékot tartalmazza:

|Példák kimondott szövegekre|
|--|
|John W át. A-2345 Smith|
|x12345 váltása holnap: h-1234|
 
Az áthelyezési kérelemnek legalább tartalmaznia kell az alkalmazott (bármely szinonimát használatával), illetve a végső épület és az office helyét. A kérelem is tartalmazhat az eredeti office, valamint egy dátumot, az áthelyezés történjen. 

A kibontott adatok végpontról tartalmazzák ezt az információt és küldje vissza, az egy `RequestEmployeeMove` összetett entitást. 

## <a name="create-composite-entity"></a>Összetett entitás létrehozása
1. Győződjön meg arról, hogy az Emberi erőforrások alkalmazás a LUIS **Build** (Létrehozás) szakaszában van. Ha erre a szakaszra szeretne lépni, válassza a jobb felső menüsávon a **Build** (Létrehozás) elemet. 

2. Az a **leképezések** lapon jelölje be **MoveEmployee** szándékot. 

3. Válassza a Nagyító ikont, az eszköztáron a kimondott szöveg szűréséhez. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Képernyőkép a LUIS \"MoveEmployee\" szándékot Nagyító gomb kiemelésével")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Adja meg `tomorrow` be a szűrő szövegmezőbe az utterance (kifejezés) található `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Képernyőkép a LUIS \"MoveEmployee\" leképezés \"holnap innovációit\" szűrővel kiemelésével")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    Hogy az entitás szűrés datetimeV2, kiválasztásával **entitás szűrők** majd **datetimeV2** a listából. 

5. Válassza ki az első entitás `Employee`, majd **Zabalit do összetett entitást** előugró menü listájában. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Képernyőkép a LUIS \"MoveEmployee\" szándékot összetett, kiemelve az első entitás kiválasztása")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Azonnal válassza ki az utolsó entitás `datetimeV2` az utterance (kifejezés) található. Zöld sáv megjelenítése a kiválasztott szavakat jelző egy összetett entitás alapján. Az előugró menüben írja be a összetett nevét `RequestEmployeeMove` majd **hozzon létre új összetett** a a helyi menüből. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Képernyőkép a LUIS összetett és létrehozni egy entitásban utolsó entitás kiválasztása \"MoveEmployee\" szándékot kiemelésével")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. A **milyen típusú entitást szeretne létrehozni?**, szinte az összes, a szükséges mezők szerepelnek a listában. Csak a származási helye nincs megadva. Válassza ki **adjon hozzá egy gyermek entitásnak**, jelölje be **Locations::Origin** meglévő entitások, majd válassza ki a listáról **kész**. 

    ![Képernyőkép a LUIS "MoveEmployee" szándékot egy másik entitás hozzáadása az előugró ablakban](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Válassza a Nagyító ikonra az eszköztáron a szűrő eltávolításához. 

## <a name="label-example-utterances-with-composite-entity"></a>Az összetett entitás címke példa kimondott szöveg
1. Valamennyi példa utterance (kifejezés) válassza ki a bal szélső entitást, amely az összetett kell lennie. Válassza ki **Zabalit do összetett entitást**.

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Képernyőkép a LUIS \"MoveEmployee\" szándékot összetett, kiemelve az első entitás kiválasztása")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Válassza ki az utolsó szót az összetett entitásban, majd válassza ki **RequestEmployeeMove** az előugró menüben. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Képernyőkép a LUIS \"MoveEmployee\" szándékot összetett, kiemelve az utolsó entitás kiválasztása")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Ellenőrizze, hogy a célt az összes utterances vannak ellátva, az összetett entitáshoz. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Képernyőkép a LUIS \"MoveEmployee\" címkével ellátott összes utterances az")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint"></a>A végpont lekérdezése 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`. Az utolsó lekérdezési karakterlánc paraméter `q`, az utterance (kifejezés) lekérdezést. 

    Mivel ez a teszt ellenőrzése az összetett ki kell olvasni megfelelően, egy tesztet vagy tartalmazhat egy meglévő minta utterance (kifejezés) vagy egy új utterance (kifejezés). Ellenőrzi, hogy a gyermekentitások tartalmazza az összetett entitásban.

    ```JSON
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
          "type": "requestemployeemove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "requestemployeemove",
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
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
    }
    ```

  Az utterance (kifejezés) egy összetett entitások tömböt ad vissza. Minden entitás egy típusa és értéke van megadva. Minden gyermek entitásnak pontossággal megkereséséhez használja a megfelelő elem található entitások tömbben típusa és értéke összetett tömb elemének kombinációja.  

## <a name="what-has-this-luis-app-accomplished"></a>Milyen műveleteket végzett el a LUIS-alkalmazás?
Az alkalmazás azonosítani a természetes nyelvű lekérdezés szándékát, és a nevesített csoportként kinyert adatokat adott vissza. 

A csevegőrobot most már elég információt meghatározhatja az elsődleges művelet és a kapcsolódó részletes adatait a az utterance (kifejezés). 

## <a name="where-is-this-luis-data-used"></a>Hol vannak használatban ezek a LUIS-adatok? 
A LUIS végzett ezzel a kéréssel. A hívó alkalmazás, például egy csevegőrobot, felhasználhatja a topScoringIntent eredményt és az entitás adatait a következő lépés végrehajtásához. A LUIS nem végzi el ezt a programozható munkát a csevegőrobotnak vagy a hívó alkalmazásnak. A LUIS csak azt határozza meg, hogy mi a felhasználó szándéka. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Egy kifejezés listát egy egyszerű entitás hozzáadása](luis-quickstart-primary-and-secondary-data.md)  