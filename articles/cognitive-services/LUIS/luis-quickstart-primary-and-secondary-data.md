---
title: Oktatóanyag – LUIS-alkalmazás létrehozása adatok kinyeréséhez – Azure | Microsoft Docs
description: Ebből az oktatóanyagban megismerheti, hogyan hozhat létre egy szándékokat és egyszerű entitást használó egyszerű LUIS-alkalmazást gépi tanulással létrejött adatok kinyeréséhez.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265360"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>Oktatóanyag: Egyszerű entitást használó alkalmazás létrehozása
Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan nyerhetők ki gépi tanulással létrejött adatok egy kimondott szövegből az **Egyszerű** entitás használatával.

<!-- green checkmark -->
> [!div class="checklist"]
> * Az entitások értelmezése 
> * Új LUIS-alkalmazás létrehozása SendMessage szándékkal rendelkező kommunikációs tartományhoz
> * _None_ szándék és például szolgáló kimondott szövegek hozzáadása
> * Egyszerű entitás felvétele egy kimondott szöveg üzenettartalmának kinyeréséhez
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="purpose-of-the-app"></a>Az alkalmazás célja
Ez az alkalmazás bemutatja, hogy nyerhetők ki adatok egy kimondott szövegből. Vegyük például egy csevegőrobot által kimondott következő szöveget:

```JSON
Send a message telling them to stop
```

A szándék egy üzenet a küldése. A kimondott szöveg fontos adata maga az üzenet: `telling them to stop`.  

## <a name="purpose-of-the-simple-entity"></a>Az egyszerű entitás célja
Az egyszerű entitás célja megtanítani a LUIS-nak, hogy mi az üzenet, és hol található meg a kimondott szövegben. A kimondott szöveg üzenet része szövegenként változhat a szóhasználat és a kimondott szöveg hossza alapján. A LUIS-nak minden kimondott szövegben lévő minden szándék üzenetpéldájára szüksége van.  

Ezen egyszerű alkalmazás esetében az üzenet a kimondott szöveg végén lesz. 

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
1. Jelentkezzen be a [LUIS][LUIS] webhelyére. Győződjön meg arról, hogy abban a régióban jelentkezik be, ahol közzé szeretné tenni a LUIS-végpontokat.

2. A [LUIS][LUIS] webhelyén válassza a **Create new app** (Új alkalmazás létrehozása) lehetőséget.  

    ![LUIS-alkalmazások listája](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. Az előugró párbeszédpanelen írja be a következő nevet: `MyCommunicator`. 

    ![LUIS-alkalmazások listája](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. Amikor a folyamat befejeződött, az alkalmazás megjeleníti az **Intents** (Szándékok) lapot és rajta a **None** szándékot. 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "A LUIS Intents (Szándékok) lapjának képernyőképe a None szándékkal")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>Új szándék létrehozása

1. Az **Intents** (Szándékok) lapon válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "A LUIS képernyőképe az új szándék létrehozására szolgáló gomb kiemelésével")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. Adja meg az új szándék nevét: `SendMessage`. Ezt a szándékot kell kiválasztani, ha egy felhasználó üzenetet szeretne küldeni.

    A szándék létrehozásával azt az elsődleges információkategóriát is létrehozza, amelyet azonosítani szeretne. A kategória elnevezése lehetővé teszi, hogy a LUIS lekérdezési eredményeit használó egyéb alkalmazások is használják ezt a kategórianevet a megfelelő válasz megkereséséhez vagy a megfelelő művelet végrehajtásához. A LUIS nem válaszol ezekre a kérdésekre, csak azonosítja a természetes nyelvezeten kért információ típusát. 

    ![Adja meg a SendMessage szándéknevet.](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. Adjon hozzá hét kimondott szöveget a `SendMessage` szándékhoz, amelyet a felhasználók várhatóan használni fognak, például:

    | Példák kimondott szövegekre|
    |--|
    |Válaszolj: Megkaptam az üzenetet, holnap válaszolok|
    |Küldj üzenetet: Mikor leszel otthon?|
    |Küldd el SMS-ben: Elfoglalt vagyok|
    |Mond meg nekik, hogy ma kell elvégezni|
    |Küldd el csevegőüzenetben, hogy vezetek, később válaszolok|
    |Írj üzenetet Davidnek, amelyben az áll: Ez mikor történt?|
    |Üdvözöld Greget|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "Képernyőkép a LUIS-ról a megadott kimondott szövegekkel")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Kimondott szövegek hozzáadása a None szándékhoz

A LUIS-alkalmazásnak jelenleg nincsenek kimondott szövegei a **None** szándékhoz. Meg kell adni olyan kimondott szövegeket, amelyekre nem szeretné, hogy az alkalmazás válaszoljon, így a **None** szándékban is lenniük kell kimondott szövegeknek. Ne hagyja üresen a szándékot. 
    
1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "A LUIS képernyőképe a kiemelt Intents (Szándékok) gombbal")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. Válassza ki a **None** szándékot. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "A None szándék kiválasztásának képernyőképe")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. Adjon hozzá három olyan kimondott szöveget, amelyet a felhasználó beírhat, de az alkalmazás számára nem releváns. Néhány jó **None** kimondott szöveg például a következő:

    | Példák kimondott szövegekre|
    |--|
    |Mégse|
    |Viszlát|
    |Mi történik?|
    
    Ha a LUIS-t hívó alkalmazásban, például egy csevegőrobotban, a LUIS a **None** szándékot adja vissza egy kimondott szöveghez, a robot meg tudja kérdezni, hogy a felhasználó be szeretné-e fejezni a beszélgetést. A robot további iránymutatásokat is adhat a beszélgetés folytatásához, ha a felhasználó azt szeretné. 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "Képernyőkép a LUIS-ról a None szándékhoz tartozó kimondott szövegekkel")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>Egyszerű entitás létrehozása az üzenet kinyeréséhez 
1. A bal oldali menüben válassza az **Intents** (Szándékok) lehetőséget.

    ![Az Intents (Szándékok) hivatkozás kiválasztása](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. A szándékok listájáról válassza ki a következőt: `SendMessage`.

    ![A SendMessage szándék kiválasztása](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. A(z) `Reply with I got your message, I will have the answer tomorrow` kimondott szövegben jelölje ki az üzenettörzs első (`I`) és utolsó (`tomorrow`) szavát. Ezek a szavak mind ki lesznek választva az üzenethez, és megjelenik egy legördülő menü, szövegmezővel a tetején.

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "Kimondott szöveg szavainak kiválasztása üzenethez – képernyőkép")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. A szövegmezőbe írja be a `Message` entitásnevet.

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "Entitásnév beírása a mezőbe – képernyőkép")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. A legördülő menüben válassza a **Create new entity** (Új entitás létrehozása) lehetőséget. Az entitás célja az üzenettörzs szövegének lehívása. Ebben a LUIS-alkalmazásban a szöveges üzenet a kimondott szöveg végén található, de a kimondott szöveg és az üzenet is bármilyen hosszú lehet. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "Új entitás kimondott szövegből történő létrehozásának képernyőképe")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. Az előugró ablakban az alapértelmezett entitástípus a **Simple** (Egyszerű), az entitás neve pedig `Message`. Tartsa meg ezeket a beállításokat, és válassza a **Done** (Kész) lehetőséget.

    ![Entitás típusának ellenőrzése](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. Most, hogy létrehozta az entitást és felcímkézett egy kimondott szöveget, a többi kimondott szöveget is címkézze fel azzal az entitással. Válasszon ki egy kimondott szöveget, majd jelölje ki az üzenet első és utolsó betűjét. A legördülő menüben válassza a `Message` entitást. Az üzenet most már fel van címkézve az entitásban. A többi kimondott szövegben is folytassa az üzenetkifejezések címkézését.

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "Képernyőkép az összes címkézett kimondott szövegbeli üzenetről")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    A kimondott szövegek alapértelmezett nézete az **Entities** (Entitások) nézet. Válassza ki az **Entities** (Entitások) nézet vezérlőjét a kimondott szövegek felett. A **Tokens** (Jogkivonatok) nézet megjeleníti a kimondott szöveget. 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "Képernyőkép: kimondott szövegek a Tokens (jogkivonatok) nézetben")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Amíg nincs betanítva, a LUIS nem ismeri fel a szándékok és entitások (a modell) módosításait. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

    ![Train (Betanítás) gomb kiválasztása](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    ![Sikeres betanítást jelző értesítés](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez
Ahhoz, hogy LUIS-előrejelzéseket kaphasson egy csevegőrobotban vagy más alkalmazásban, közzé kell tennie az alkalmazást. 

1. A LUIS-webhely jobb felső részén válassza a **Publish** (Közzététel) lehetőséget. 

2. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "A Publish (Közzététel) lap képernyőképe a kiemelt Publish to production slot (Közzététel éles termelési helyre) elemmel")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel
A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Publish (Közzététel) lap képernyőképe a kiemelt végponttal")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. Lépjen az URL-cím végéhez, és írja be a következőt: `text I'm driving and will be 30 minutes late to the meeting`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **query**. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek megfelelő, és a következő kimondott szövegeket kell visszaadnia: `SendMessage`.

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Milyen műveleteket végzett el a LUIS-alkalmazás?
Az alkalmazás mindössze két szándékkal és egy listaentitással azonosított egy természetes nyelvi lekérdezési szándékot, és visszaadta az üzenetadatokat. 

A JSON-eredmény megállapította, hogy a legmagasabb pontszámú szándék a `SendMessage`, 0,987501 értékű pontszámmal. Minden pontszám 1 és 0 közé esik, a jobb pontszám 1-hez közelebb található. A(z) `None` szándék pontszáma 0,111048922, amely a nullához sokkal közelebb van. 

Az üzenetadat típussal (`Message`) és értékkel (`i ' m driving and will be 30 minutes late to the meeting`) is rendelkezik. 

A csevegőrobot már elég információval rendelkezik a(z) `SendMessage` elsődleges művelet és a művelet paraméterének, az üzenet szövegének a megállapításához. 

## <a name="where-is-this-luis-data-used"></a>Hol vannak használatban ezek a LUIS-adatok? 
A LUIS végzett ezzel a kéréssel. A hívó alkalmazás, például egy csevegőrobot, használhatja a topScoringIntent eredményt és az entitásból származó adatokat az üzenet külső API-n keresztül történő küldéséhez. Ha a csevegőrobot vagy a hívó alkalmazás egyéb programozható beállítással rendelkezik, a LUIS ezeket nem végzi el. A LUIS csak azt határozza meg, hogy mi a felhasználó szándéka. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza az alkalmazáslistában az alkalmazás neve mellett jobbra található három pontot (...), majd a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a hierarchikus entitások hozzáadásának módjával](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
