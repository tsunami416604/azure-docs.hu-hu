---
title: Oktatóanyag – LUIS-alkalmazás létrehozása pontos szövegegyezést mutató listázott adatok lekéréséhez – Azure | Microsoft Docs
description: Az oktatóanyag azt ismerteti, hogyan hozhat létre szándékokkal és listaentitásokkal egy egyszerű LUIS-alkalmazást az adatok kinyeréséhez.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 04411f415b7cfe07d893c43e758bd2a4a226472a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162198"
---
# <a name="tutorial-4-add-list-entity"></a>Oktatóanyag: 4. Listaentitás hozzáadása
Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan kérhetők le egy előre meghatározott listával egyező adatok. 

<!-- green checkmark -->
> [!div class="checklist"]
> * A listaentitások ismertetése 
> * Új LUIS-alkalmazás létrehozása az emberi erőforrások (HR) tartományához a MoveEmployee szándékkal
> * Egy listaentitás hozzáadása a kimondott szöveg alkalmazottakra vonatkozó elemeinek kinyeréséhez
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Előkészületek
Ha még nincs meg az Emberi erőforrások alkalmazása a [regex entitás](luis-quickstart-intents-regex-entity.md) oktatóanyagából, [importálja](luis-how-to-start-new-app.md#import-new-app) a JSON-t egy új alkalmazásba a [LUIS](luis-reference-regions.md#luis-website) webhelyén. Az importálandó alkalmazás a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json) GitHub-adattárban található.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `list`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. 

## <a name="purpose-of-the-list-entity"></a>A listaentitás feladata
Ez az alkalmazás előre jelzi egy alkalmazott egyik épületből másik épületbe történő áthelyezéséről szóló kimondott szöveget. Ez az alkalmazás egy listaentitást használ az alkalmazott kinyeréséhez. Az alkalmazottra név, telefonszám, e-mail-cím vagy USA-beli szövetségi társadalombiztosítási szám alapján lehet hivatkozni. 

A listaentitás számos elemet tartalmazhat, mindegyik elem szinonimájával együtt. Egy kis és közepes méretű vállalat esetében a listaentitás az alkalmazotti információk kinyerésére használható. 

Az egyes elemek kanonikus neve az alkalmazotti szám. Ebben a tartományban a szinonimák példái: 

|Szinonima célja|Szinonima értéke|
|--|--|
|Name (Név)|John W. Smith|
|E-mail-cím|john.w.smith@mycompany.com|
|Telefonmellék|x12345|
|Saját mobiltelefonszám|425-555-1212|
|USA-beli szövetségi társadalombiztosítási szám (SSN)|123-45-6789|

A listaentitás megfelelő választás az ilyen típusú adatok esetén, amikor:

* Az adatok értékei egy ismert készletbe tartoznak.
* A készlet nem haladja meg a LUIS maximális [határait](luis-boundaries.md) ezen entitástípus esetében.
* A kimondott szöveg egy része pontosan megegyezik egy szinonimával. 

A LUIS úgy nyeri ki az alkalmazottakat, hogy az ügyfélalkalmazás létrehozhasson egy szokványos sorrendet az alkalmazottak áthelyezéséhez.
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>MoveEmployee szándék hozzáadása

1. Győződjön meg arról, hogy az Emberi erőforrások alkalmazás a LUIS **Build** (Létrehozás) szakaszában van. Ha erre a szakaszra szeretne lépni, válassza a jobb felső menüsávon a **Build** (Létrehozás) elemet. 

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

## <a name="create-an-employee-list-entity"></a>Alkalmazottlista-entitás létrehozása
Most, hogy a **MoveEmployee** szándéknak vannak kimondott szövegei, a LUIS-nak meg kell értenie, hogy az alkalmazott pontosan micsoda. 

1. Válassza az **Entities** (Entitások) elemet a bal oldali ablaktáblán.

2. Válassza a **Create new entity** (Új entitás létrehozása) lehetőséget.

3. A felugró párbeszédpanelen adja meg az `Employee` értéket az entitás neveként, és a **List** (Lista) értéket az entitás típusaként. Válassza a **Done** (Kész) lehetőséget.  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "Új entitás létrehozása előugró párbeszédpanel képernyőképe")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. Az Employee entity (Alkalmazottentitás) lapon az új értéknek adja meg a következőt: `Employee-24612`.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Érték megadásának képernyőképe")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. Szinonimák esetében vegye fel az alábbi értékeket:

    |Szinonima célja|Szinonima értéke|
    |--|--|
    |Name (Név)|John W. Smith|
    |E-mail-cím|john.w.smith@mycompany.com|
    |Telefonmellék|x12345|
    |Saját mobiltelefonszám|425-555-1212|
    |USA-beli szövetségi társadalombiztosítási szám (SSN)|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Szinonimák megadásának képernyőképe")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. Új értékként írja be a következőt: `Employee-45612`.

7. Szinonimák esetében vegye fel az alábbi értékeket:

    |Szinonima célja|Szinonima értéke|
    |--|--|
    |Name (Név)|Jill Jones|
    |E-mail-cím|jill-jones@mycompany.com|
    |Telefonmellék|x23456|
    |Saját mobiltelefonszám|425-555-0000|
    |USA-beli szövetségi társadalombiztosítási szám (SSN)|234-56-7891|

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Lépjen az URL-cím végéhez, és írja be a következőt: `shift 123-45-6789 from Z-1242 to T-54672`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **q**uery. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a `Employee` szándékot kell visszaadnia, kinyerve a következőt: `MoveEmployee`.

  ```JSON
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

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Hogyan viszonyul a természetes nyelvi feldolgozás a listaentitásokhoz? 
Mivel a listaentitás pontos szövegegyezés, ezért nem támaszkodik a természetes nyelvi feldolgozásra (vagy gépi tanulásra). A LUIS a természetes nyelvi feldolgozás (vagy gépi tanulás) segítségével választja ki a legvalószínűbb szándékot. Egy kimondott szöveg állhat több entitás keverékéből, vagy akár többféle entitástípusból is. Az alkalmazás minden kimondott szöveget minden entitáshoz feldolgoz, beleértve a természetes nyelvi feldolgozási (vagy gépi tanulási) entitásokat is.

## <a name="what-has-this-luis-app-accomplished"></a>Milyen műveleteket végzett el a LUIS-alkalmazás?
Ez az alkalmazás a listaentitással a megfelelő alkalmazottat nyerte ki. 

A csevegőrobot már elég információval rendelkezik az elsődleges művelet (`MoveEmployee`) és az áthelyezendő alkalmazott meghatározásához. 

## <a name="where-is-this-luis-data-used"></a>Hol vannak használatban ezek a LUIS-adatok? 
A LUIS végzett ezzel a kéréssel. A hívó alkalmazás, például egy csevegőrobot, felhasználhatja a topScoringIntent eredményt és az entitás adatait a következő lépés végrehajtásához. A LUIS nem végzi el ezt a programozható munkát a csevegőrobotnak vagy a hívó alkalmazásnak. A LUIS csak azt határozza meg, hogy mi a felhasználó szándéka. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hierarchikus entitás hozzáadása az alkalmazáshoz](luis-quickstart-intent-and-hier-entity.md)

