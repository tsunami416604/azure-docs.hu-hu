---
title: Oktatóanyag – Hangulatelemzést visszaadó LUIS-alkalmazás létrehozása – Azure | Microsoft Docs
description: Az oktatóanyagból megtudhatja, hogyan adhat hozzá hangulatelemzést LUIS-alkalmazásához a kimondott szövegek pozitív, negatív vagy semleges érzelmeinek elemzésére.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265334"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>Oktatóanyag: Hangulatot és szándék-előrejelzést visszaadó alkalmazás létrehozása
Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan nyerhető ki pozitív, negatív vagy semleges érzelem a kimondott szövegekből.

<!-- green checkmark -->
> [!div class="checklist"]
> * Hierarchikus entitások és környezetfüggő tanult gyermekek megismerése 
> * Új LUIS-alkalmazás létrehozása egy utazási tartományhoz Bookflight szándékkal
> * _None_ szándék és például szolgáló kimondott szövegek hozzáadása
> * Forrással és célgyermekkel rendelkező helyhierarchikus entitás hozzáadása
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz, többek között a hierarchikus gyermekek megtekintéséhez 

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="sentiment-analysis"></a>Hangulatelemzés
A hangulatelemzés az a képesség, amellyel megállapítható, hogy egy felhasználó által kimondott szöveg pozitív, negatív, vagy semleges. 

Az alábbi kimondott szövegek a különböző hangulatokat példázzák:

|Hangulat és pontszám|Kimondott szöveg|
|:--|--|
|pozitív – 0,89 |A leves és saláta nagyszerű volt.|
|negatív – 0,07 |Nem ízlett az előétel a vacsorán.|

A hangulatelemzés egy olyan alkalmazásbeállítás, amely minden kimondott szövegre vonatkozik. Önnek nem kell megkeresnie és felcímkéznie a hangulatot kifejező szavakat a kimondott szövegekben. Ezt a LUIS megteszi Ön helyett.

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
1. Jelentkezzen be a [LUIS][LUIS] webhelyére. Győződjön meg arról, hogy abban a [régióban][LUIS-regions] jelentkezik be, ahol közzé szeretné tenni a LUIS-végpontokat.

2. A [LUIS][LUIS] webhelyén válassza a **Create new app** (Új alkalmazás létrehozása) lehetőséget. 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "Alkalmazáslistát tartalmazó oldal képernyőképe")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. A **Create new app** (Új alkalmazás létrehozása) párbeszédpanelen adja a következő nevet az alkalmazásnak: `Restaurant Reservations With Sentiment`, majd válassza a **Done** (Kész) lehetőséget. 

    ![A Create new app (Új alkalmazás létrehozása) párbeszédpanel képe](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    Az alkalmazáslétrehozási folyamat befejezése után a LUIS megjeleníti a None szándékot tartalmazó szándéklistát.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "Szándéklistát tartalmazó oldal képernyőképe")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása
Egy előre összeállított tartomány hozzáadásával gyorsan adhat hozzá a szándékokat, entitásokat és címkézett kimondott szövegeket.

1. Válassza a **Prebuilt Domains** (Előre összeállított tartományok) elemet a bal oldali menüben.

    [ ![A Prebuilt Domain (Előre összeállított tartományok) gomb képernyőképe](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. Válassza az **Add domain** (Tartomány hozzáadása) lehetőséget a **RestaurantReservation** előre összeállított tartományhoz. Várja meg, amíg befejeződik a tartomány hozzáadása.

    [ ![Az előre összeállított tartományok listájának képernyőképe](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. A bal oldali navigációs menüben válassza az **Intents** (Szándékok) lehetőséget. Ez az előre összeállított tartomány egy szándékkal rendelkezik.

    [ ![Az előre összeállított tartományok listájának képernyőképe, a szándékok kiemelve a bal oldali navigációs menüben](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  Válassza ki a **RestaurantReservation.Reserve** szándékot. 

    [ ![A szándékok listájának képernyőképe, a RestaurantReservation.Reserve kiemelve](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. Váltson az **Entities** (Entitások) nézetre a megadott, tartományspecifikus entitásokkal címkézett kimondott szövegek megtekintéséhez.

    [ ![A RestaurantReservation.Reserve szándék képernyőképe, a Token (Jogkivonat) nézetre váltott Entities (Entitások) nézet kiemelve](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Amíg nincs betanítva, a LUIS nem ismeri fel a szándékok és entitások (a modell) módosításait. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

    ![A kiemelt Train (Betanítás) gomb képernyőképe](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    ![Sikeres betanítást jelző értesítési sáv képernyőképe ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Alkalmazás konfigurálása hangulatelemzés belefoglalásához
A hangulatelemzés a **Publish** (Közzététel) lapon engedélyezhető. 

1. A jobb felső navigációs menüben válassza a **Publish** (Közzététel) lehetőséget.

    ![Az Intent (Szándék) lap képernyőképe a kibontott Publish (Közzététel) gombbal ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. Válassza az **Enable Sentiment Analysis** (Hangulatelemzés engedélyezése) lehetőséget.

    ![A Publish (Közzététel) lap képernyőképe a kiemelt Enable Sentiment Analysis (Hangulatelemzés engedélyezése) elemmel ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "A Publish (Közzététel) lap képernyőképe a kiemelt Publish to production slot (Közzététel éles termelési helyre) elemmel")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="query-the-endpoint-with-an-utterance"></a>A kimondott szöveget tartalmazó végpont lekérdezése

1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

    ![„Publish (Közzététel) lap képernyőképe a kiemelt végponti URL-címmel](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Reserve table for  10 on upper level away from kitchen`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő szándékot kell visszaadnia kinyert hangulatelemzéssel: `RestaurantReservation.Reserve`.

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Milyen műveleteket végzett el a LUIS-alkalmazás?
Az engedélyezett hangulatelemzéssel beállított alkalmazás azonosított egy természetes nyelvezetű lekérdezési szándékot, és visszaadta a kinyert adatokat, például az általános hangulatot pontszám formájában. 

A csevegőrobot már elegendő információval rendelkezik a beszélgetés következő lépésének megállapításához. 

## <a name="where-is-this-luis-data-used"></a>Hol vannak használatban ezek a LUIS-adatok? 
A LUIS végzett ezzel a kéréssel. A hívó alkalmazás, például egy csevegőrobot, felhasználhatja a topScoringIntent eredményt és a kimondott szövegből származó hangulati adatokat a következő lépés végrehajtásához. A LUIS nem végzi el ezt a programozható munkát a csevegőrobotnak vagy a hívó alkalmazásnak. A LUIS csak azt határozza meg, hogy mi a felhasználó szándéka. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza az alkalmazáslistában az alkalmazás neve mellett jobbra található három pontot (...), majd a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [LUIS végponti API hívása a C# használatával](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
