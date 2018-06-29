---
title: Oktatóanyag – Kulcskifejezéseket visszaadó LUIS-alkalmazás létrehozása – Azure | Microsoft Docs
description: Az oktatóanyagból megtudhatja, hogyan adhat hozzá, illetve kaphat vissza keyPhrase entitást a LUIS-alkalmazából a kulcstémák kimondott szövegének elemzéséhez.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264615"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>Oktatóanyag: Kimondott szövegekben található keyPhrases entitás adatait visszaadó alkalmazás létrehozása
Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan nyerhetők ki kulcstémák a kimondott szövegekből.

<!-- green checkmark -->
> [!div class="checklist"]
> * Ismerkedés a keyPhrase entitásokkal 
> * Új LUIS-alkalmazás létrehozása az emberi erőforrások tartományához
> * _None_ szándék és például szolgáló kimondott szövegek hozzáadása
> * Egy keyPhrase entitás hozzáadása a kimondott szöveg tartalmának kinyeréséhez
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez

Ebben a cikkben használhatja az ingyenes [LUIS][LUIS]-fiókot a LUIS-alkalmazás létrehozásához.

## <a name="keyphrase-entity-extraction"></a>A keyPhrase entitás kinyerése
A kulcstémát az előre összeállított entitás, a **keyPhrase** biztosítja. Ez az entitás a kimondott szöveg kulcstémáját adja vissza.

Az alábbi kimondott szövegek például szolgáló kulcskifejezései:

|Kimondott szöveg|keyPhrase entitásértékek|
|--|--|
|Lesz jövőre olyan egészségbiztosítási csomag, amelynek alacsonyabb lesz az önrésze?|„alacsonyabb önrész”<br>„új egészségbiztosítási csomag”<br>„jövőre”|
|A nagyobb önrészű egészségbiztosítási csomag kiterjed a látáskorrekciós vizsgálatokra?|„nagyobb önrészű egészségbiztosítási csomag”<br>„látáskorrekciós vizsgálat”|

A csevegőrobot figyelembe veszi ezeket az értékeket, illetve minden további kinyerhető entitást, amikor eldönti, hogy mi legyen a beszélgetés következő lépése.

## <a name="download-sample-app"></a>A mintaalkalmazás letöltése
Töltse le a [Human Resources](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) alkalmazást, és mentse egy *.json kiterjesztésű fájlba. Ez a mintaalkalmazás felismeri az alkalmazotti juttatások, a szervezeti diagramok és a fizikai eszközök szempontjából releváns kimondott szövegeket.

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
1. Jelentkezzen be a [LUIS][LUIS] webhelyére. Győződjön meg arról, hogy abban a [régióban][LUIS-regions] jelentkezik be, ahol közzé szeretné tenni a LUIS-végpontokat.

2. A [LUIS][LUIS] webhelyén válassza ki az **Import new app** (Új alkalmazás importálása) lehetőséget az előző szakaszban letöltött Human Resources alkalmazás importálásához. 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "Alkalmazáslistát tartalmazó oldal képernyőképe")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. Az **Import new app** (Új alkalmazás importálása) párbeszédablakban nevezze el az alkalmazást: `Human Resources with Key Phrase entity`. 

    ![A Create new app (Új alkalmazás létrehozása) párbeszédpanel képe](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    Az alkalmazás létrehozását követően a LUIS megjeleníti a szándékok listáját.

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "Szándéklistát tartalmazó oldal képernyőképe")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>keyPhrase entitás hozzáadása 
A kimondott szöveg témájának kinyeréséhez adja hozzá az előre összeállított keyPhrase entitást.

1. Válassza az **Entities** (Entitások) elemet a bal oldali menüben.

    [ ![Az összeállítási szakasz bal oldali navigációs panelének képernyőképe kiemelt entitásokkal](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. Válassza a **Manage prebuilt entities** (Előre összeállított entitások kezelése) lehetőséget.

    [ ![Az entitáslistát tartalmazó előugró párbeszédpanel képernyőképe](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. Az előugró párbeszédpanelen válassza ki a **keyPhrase** elemet, majd a **Done** (Kész) lehetőséget. 

    [ ![Az entitáslistát tartalmazó előugró párbeszédpanel képernyőképe](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Amíg nincs betanítva, a LUIS nem ismeri fel a modell módosításait. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

    ![A kiemelt Train (Betanítás) gomb képernyőképe](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    ![Sikeres betanítást jelző értesítési sáv képernyőképe ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>Alkalmazás közzététele a végponton

1. A jobb felső navigációs menüben válassza a **Publish** (Közzététel) lehetőséget.

    ![Az entitások lapjának képernyőképe a megnyitott közzétételi gombbal ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "A Publish (Közzététel) lap képernyőképe a kiemelt Publish to production slot (Közzététel éles termelési helyre) elemmel")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="query-the-endpoint-with-an-utterance"></a>A kimondott szöveget tartalmazó végpont lekérdezése

1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

    ![A Publish (Közzététel) lap képernyőképe a kiemelt végponti URL-címmel](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Is there a new medical plan with a lower deductible offered next year?`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Milyen műveleteket végzett el a LUIS-alkalmazás?
A keyPhrase entitás észlelésére képes alkalmazás azonosított egy természetes nyelvi lekérdezési szándékot, és visszaadta a kinyert adatokat, közöttük a fő témát is. 

A csevegőrobot már elegendő információval rendelkezik a beszélgetés következő lépésének megállapításához. 

## <a name="where-is-this-luis-data-used"></a>Hol vannak használatban ezek a LUIS-adatok? 
A LUIS végzett ezzel a kéréssel. A hívó alkalmazás, például egy csevegőrobot, felhasználhatja a topScoringIntent eredményt és a kimondott szöveg keyPhrase adatait a következő lépés végrehajtásához. A LUIS nem végzi el ezt a programozható munkát a csevegőrobotnak vagy a hívó alkalmazásnak. A LUIS csak azt határozza meg, hogy mi a felhasználó szándéka. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza az alkalmazáslistában az alkalmazás neve mellett jobbra található három pontot (...), majd a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hangulatot és szándék-előrejelzést visszaadó alkalmazás létrehozása](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
