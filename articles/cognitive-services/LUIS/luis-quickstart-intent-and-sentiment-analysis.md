---
title: Oktatóanyag – Hangulatelemzést visszaadó LUIS-alkalmazás létrehozása – Azure | Microsoft Docs
description: Az oktatóanyagból megtudhatja, hogyan adhat hozzá hangulatelemzést LUIS-alkalmazásához a kimondott szövegek pozitív, negatív vagy semleges érzelmeinek elemzésére.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: v-geberr
ms.openlocfilehash: ac959989dbe64460025bfba84df7b6f22c3c1c04
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958429"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>Oktatóanyag: Hangulatot és szándék-előrejelzést visszaadó alkalmazás létrehozása
Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan nyerhető ki pozitív, negatív vagy semleges érzelem a kimondott szövegekből.

<!-- green checkmark -->
> [!div class="checklist"]
> * A hangulatelemzés ismertetése
> * A LUIS-alkalmazás használata az emberi erőforrások (HR) tartományban 
> * Hangulatelemzés hozzáadása
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez 

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="before-you-begin"></a>Előkészületek
Ha még nincs meg az Emberi erőforrások alkalmazása a [keyPhrase entitások](luis-quickstart-intent-and-key-phrase.md) oktatóanyagából, [importálja](create-new-app.md#import-new-app) a JSON-t egy új alkalmazásba a [LUIS](luis-reference-regions.md#luis-website) webhelyén. Az importálandó alkalmazás a [LUIS-minták](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json) GitHub-adattárban található.

Ha meg szeretné tartani az eredeti Emberi erőforrások alkalmazást, klónozza a [Settings](luis-how-to-manage-versions.md#clone-a-version) (Beállítások) lapon a verziót, és adja neki a következő nevet: `sentiment`. A klónozás nagyszerű mód, hogy kísérletezhessen a különböző LUIS-funkciókkal anélkül, hogy az az eredeti verzióra hatással lenne. 

## <a name="sentiment-analysis"></a>Hangulatelemzés
A hangulatelemzés az a képesség, amellyel megállapítható, hogy egy felhasználó által kimondott szöveg pozitív, negatív, vagy semleges. 

Az alábbi kimondott szövegek a különböző hangulatokat példázzák:

|Hangulat|Pontszám|Kimondott szöveg|
|:--|:--|:--|
|pozitív|0,91 |John W. Smith remek prezentációt tartott Párizsban.|
|pozitív|0,84 |jill-jones@mycompany.com nagyszerű munkát végzett a Parker befektetői bemutatón.|

A hangulatelemzés egy olyan alkalmazásbeállítás, amely minden kimondott szövegre vonatkozik. Önnek nem kell megkeresnie és felcímkéznie a hangulatot kifejező szavakat a kimondott szövegekben, mert a hangulatelemzés a teljes kimondott szövegre vonatkozik. 

## <a name="add-employeefeedback-intent"></a>EmployeeFeedback szándék hozzáadása 
Adjon hozzá egy új szándékot a vállalat tagjaitól származó alkalmazotti visszajelzések rögzítéséhez. 

1. Győződjön meg arról, hogy az Emberi erőforrások alkalmazás a LUIS **Build** (Létrehozás) szakaszában van. Ha erre a szakaszra szeretne lépni, válassza a jobb felső menüsávon a **Build** (Létrehozás) elemet. 

    [ ![Képernyőfelvétel a LUIS-alkalmazásról a kiemelt Létrehozás elemmel a jobb felső navigációs sávon](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. Válassza a **Create new intent** (Új szándék létrehozása) lehetőséget.

    [ ![Képernyőfelvétel a LUIS-alkalmazásról a kiemelt Létrehozás elemmel a jobb felső navigációs sávon](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

3. Adja meg az új szándék nevét: `EmployeeFeedback`.

    ![Hozzon lére egy új, EmployeeFeedback nevű szándék párbeszédpanelt](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Adja hozzá több kimondott szöveget, amelyek azt jelzik, hogy az alkalmazott valamit jól végzett el, vagy amelyek olyan területet jelölnek, ahol fejlődni kell:

    Ne feledje, az Emberi erőforrások alkalmazásban az alkalmazottak az `Employee` listaentitásban vannak meghatározva, név, e-mail-cím, telefonmellék, mobiltelefonszám, és az USA-beli szövetségi társadalombiztosítási szám alapján. 

    |Beszédmódok|
    |--|
    |425-555-1212 szépen üdvözölte a szülési szabadságról visszatérő munkatársat|
    |234-56-7891 vigaszt nyújtott gyászoló munkatársa számára.|
    |jill-jones@mycompany.com nem rendelkezik a papírmunkához szükséges számlákkal.|
    |john.w.smith@mycompany.com egy hónap késéssel adta le a szükséges nyomtatványokat, aláírások nélkül|
    |x23456 nem ért oda a fontos, külső helyszínen tartott marketing értekezletre.|
    |x12345 nem jelent meg a júniusi felülvizsgálatokat ismertető értekezleten.|
    |Jill Jones nagyszerű befektetési bemutatót tartott a Harvardon|
    |John W. Smith remek prezentációt tartott a Stanfordon|

    [ ![A LUIS-alkalmazás képernyőképe: kimondott példaszövegek az EmployeeFeedback szándékban](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Amíg nincs betanítva, a LUIS nem ismeri az új szándékokat és a kimondott szövegpéldákat. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

    ![A kiemelt Train (Betanítás) gomb képernyőképe](./media/luis-quickstart-intent-and-sentiment-analysis/train-button.png)

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    ![Sikeres betanítást jelző értesítési sáv képernyőképe ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-trained-inline.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Alkalmazás konfigurálása hangulatelemzés belefoglalásához
Konfigurálja a hangulatelemzést a **Publish** (Közzététel) lapon. 

1. A jobb felső navigációs menüben válassza a **Publish** (Közzététel) lehetőséget.

    ![Az Intent (Szándék) lap képernyőképe a kibontott Publish (Közzététel) gombbal ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. Válassza az **Enable Sentiment Analysis** (Hangulatelemzés engedélyezése) lehetőséget. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png "A Publish (Közzététel) lap képernyőképe a kiemelt Publish to production slot (Közzététel éles termelési helyre) elemmel")](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png#lightbox)

4. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="query-the-endpoint-with-an-utterance"></a>A kimondott szöveget tartalmazó végpont lekérdezése

1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

    ![„Publish (Közzététel) lap képernyőképe a kiemelt végponti URL-címmel](media/luis-quickstart-intent-and-sentiment-analysis/hr-endpoint-url-inline.png)

2. Lépjen az URL-cím végéhez, és írja be a következőt: `Jill Jones work with the media team on the public portal was amazing`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő szándékot kell visszaadnia kinyert hangulatelemzéssel: `EmployeeFeedback`.

```
{
  "query": "Jill Jones work with the media team on the public portal was amazing",
  "topScoringIntent": {
    "intent": "EmployeeFeedback",
    "score": 0.4983256
  },
  "intents": [
    {
      "intent": "EmployeeFeedback",
      "score": 0.4983256
    },
    {
      "intent": "MoveEmployee",
      "score": 0.06617523
    },
    {
      "intent": "GetJobInformation",
      "score": 0.04631853
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0103248553
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.007531875
    },
    {
      "intent": "FindForm",
      "score": 0.00344597152
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00337914471
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0026357458
    },
    {
      "intent": "None",
      "score": 0.00214573368
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00157622492
    },
    {
      "intent": "Utilities.Confirm",
      "score": 7.379545E-05
    }
  ],
  "entities": [
    {
      "entity": "jill jones",
      "type": "Employee",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "media team",
      "type": "builtin.keyPhrase",
      "startIndex": 25,
      "endIndex": 34
    },
    {
      "entity": "public portal",
      "type": "builtin.keyPhrase",
      "startIndex": 43,
      "endIndex": 55
    },
    {
      "entity": "jill jones",
      "type": "builtin.keyPhrase",
      "startIndex": 0,
      "endIndex": 9
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8694164
  }
}
```

A SentimentAnalysis pozitív, 0,86 értékű pontszámmal. 

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
