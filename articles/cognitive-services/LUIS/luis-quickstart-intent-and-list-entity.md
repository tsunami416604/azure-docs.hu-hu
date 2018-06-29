---
title: Oktatóanyag – LUIS-alkalmazás létrehozása pontos szövegegyezést mutató listázott adatok lekéréséhez – Azure | Microsoft Docs
description: Az oktatóanyag azt ismerteti, hogyan hozhat létre szándékokkal és listaentitásokkal egy egyszerű LUIS-alkalmazást az adatok kinyeréséhez.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264827"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>Oktatóanyag: Alkalmazás létrehozása listaentitás használatával
Ebben az oktatóanyagban létrehozunk egy alkalmazást, amely bemutatja, hogyan kérhetők le egy előre meghatározott listával egyező adatok. 

<!-- green checkmark -->
> [!div class="checklist"]
> * A listaentitások ismertetése 
> * Új LUIS-alkalmazás létrehozása OrderDrinks szándékkal rendelkező italtartományhoz
> * _None_ szándék és például szolgáló kimondott szövegek hozzáadása
> * Egy listaentitás hozzáadása a kimondott szöveg italokra vonatkozó elemeinek kinyeréséhez
> * Alkalmazás betanítása és közzététele
> * Alkalmazás végpontjának lekérdezése a LUIS által visszaadott JSON-válasz megtekintéséhez

Ehhez a cikkhez egy ingyenes [LUIS][LUIS]-fiókra van szüksége a LUIS-alkalmazás létrehozásához.

## <a name="purpose-of-the-list-entity"></a>A listaentitás feladata
Ez az alkalmazás italrendeléséket fogad, például `1 coke and 1 milk please`, és különböző adatokat ad vissza, például a rendelt italok típusát. Az italok **listaentitása** pontos szövegegyezéseket keres, és visszaadja a talált egyezéseket. 

A listaentitás megfelelő választás az ilyen típusú adatok esetén, amikor az adatok értékei egy ismert készletbe tartoznak. Az italok nevei némileg eltérhetnek a szlengnek és rövidítéseknek köszönhetően, de ritkán változnak. 

## <a name="app-intents"></a>Az alkalmazás szándékai
A szándékok a felhasználói kívánságok kategóriái. Ez az alkalmazás két szándékkal rendelkezik: OrderDrink és None. A [None](luis-concept-intent.md#none-intent-is-fallback-for-app) szándékkal jelezhető minden olyan kívánság, amely az alkalmazás körén kívül esik.  

## <a name="list-entity-is-an-exact-text-match"></a>A listaentitások pontos szövegegyezések
Az entitás célja, hogy megtalálja és kategorizálja a kimondott szöveg egyes részeit. A [listaentitás](luis-concept-entity-types.md) a szavak vagy kifejezések pontos egyeztetését teszi lehetővé.  

Egy italrendelő alkalmazásnál a LUIS úgy nyeri ki az italrendelést, hogy létre lehessen hozni és teljesíteni lehessen egy szokványos rendelést. A LUIS lehetővé teszi, hogy a kimondott szöveg változatokat, rövidítéseket és szlenget tartalmazzon. 

Egyszerű példák felhasználók által kimondott szövegekre:

```
2 glasses of milk
3 bottles of water
2 cokes
```

A kimondott szövegek rövidített vagy szlenges verziói:

```
5 milk
3 h2o
1 pop
```
 
A listaentitás megfeleltetései: `h2o` = víz, `pop` = üdítőital.  

## <a name="what-luis-does"></a>A LUIS által elvégzett feladat
A LUIS akkor van készen, ha azonosította, [kinyerte](luis-concept-data-extraction.md#list-entity-data), és a [végpontról](https://aka.ms/luis-endpoint-apis) JSON-formátumban visszaadta a kimondott szöveg szándékát és entitásait. A hívó alkalmazás vagy a csevegőrobot fogadja a JSON-választ és teljesíti a kérést a kialakításának megfelelő módon. 

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
1. Jelentkezzen be a [LUIS][LUIS] webhelyére. Győződjön meg arról, hogy abban a [régióban][LUIS-regions] jelentkezik be, ahol közzé szeretné tenni a LUIS-végpontokat.

2. A [LUIS][LUIS] webhelyén válassza a **Create new app** (Új alkalmazás létrehozása) lehetőséget.  

    ![Új alkalmazás létrehozása](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. Az előugró párbeszédpanelen írja be a következő nevet: `MyDrinklist`. 

    ![Az alkalmazásnak adja a MyDrinkList nevet](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. Amikor a folyamat befejeződött, az alkalmazás megjeleníti az **Intents** (Szándékok) lapot és rajta a **None** szándékot. 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "A szándékok oldalának képernyőképe")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Új szándék létrehozása

1. Az **Intents** (Szándékok) lapon válassza a **Create new intent** (Új szándék létrehozása) lehetőséget. 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "A szándékok lapjának képernyőképe az új szándék létrehozására szolgáló gomb kiemelésével")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. Adja meg az új szándék nevét: `OrderDrinks`. Ezt a szándékot kell kiválasztani, ha egy felhasználó italt szeretne rendelni.

    A szándék létrehozásával azt az elsődleges információkategóriát is létrehozza, amelyet azonosítani szeretne. A kategória elnevezése lehetővé teszi, hogy a LUIS lekérdezési eredményeit használó egyéb alkalmazások is használják ezt a kategórianevet a megfelelő válasz megkereséséhez vagy a megfelelő művelet végrehajtásához. A LUIS nem válaszol ezekre a kérdésekre, csak azonosítja a természetes nyelvezeten kért információ típusát. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "Az új OrderDrinks szándék létrehozásának képernyőképe")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. Az `OrderDrinks` szándékhoz számos olyan kimondott szöveget is hozzáadhat, amelyet a felhasználók várhatóan használni fognak, például:

    | Példák kimondott szövegekre|
    |--|
    |Kérem, küldjön a szobámba 2 kólát és egy üveg vizet|
    |2 Perrier egy kis lime-mal|
    |h20|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "A kimondott szöveg megadásának képernyőképe az OrderDrinks szándék oldalán")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Kimondott szövegek hozzáadása a None szándékhoz

A LUIS-alkalmazásnak jelenleg nincsenek kimondott szövegei a **None** szándékhoz. Meg kell adni olyan kimondott szövegeket, amelyekre nem szeretné, hogy az alkalmazás válaszoljon, így a **None** szándékban is lenniük kell kimondott szövegeknek. Ne hagyja üresen a szándékot. 

1. A bal oldali panelen válassza az **Intents** (Szándékok) lehetőséget. 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "Képernyőkép a bal oldali panel Intents (Szándékok) hivatkozásának kiválasztásáról")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. Válassza ki a **None** szándékot. Adjon meg három olyan kimondott szöveget, amelyet a felhasználó megadhat, de az alkalmazás nem fog foglalkozni velük:

    | Példák kimondott szövegekre|
    |--|
    |Mégse|
    |Viszlát|
    |Mi történik?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Amikor a kimondott szövegből a None szándékra lehet következtetni
Ha a LUIS-t hívó alkalmazásban, például egy csevegőrobotban, a LUIS a **None** szándékot adja vissza egy kimondott szöveghez, a robot meg tudja kérdezni, hogy a felhasználó be szeretné-e fejezni a beszélgetést. A robot további iránymutatásokat is adhat a beszélgetés folytatásához, ha a felhasználó azt szeretné. 

Az entitások a **None** szándék esetében is működnek. Ha a **None** a legmagasabb pontszámú szándék, de egy kinyert entitás a csevegőrobot számára értelmezhető, a csevegőrobot folytathatja a kommunikációt egy, az ügyfél szándékát feltáró kérdéssel. 

## <a name="create-a-menu-entity-from-the-intent-page"></a>Menüentitás létrehozása a szándék oldalán
Most, hogy a két szándéknak vannak kimondott szövegei, a LUIS-nak meg kell értenie, hogy az ital pontosan mit takar. Lépjen vissza az `OrderDrinks` szándékra, és címkézze (jelölje) meg az italokat egy kimondott szövegben a következő lépések alapján:

1. Térjen vissza az `OrderDrinks` szándékhoz a bal oldali navigációs menü **Intents** (Szándékok) menüpontjának kiválasztásával.

2. A szándékok listájáról válassza ki a következőt: `OrderDrinks`.

3. A(z) `Please send 2 cokes and a bottle of water to my room` kimondott szövegben válassza ki a következő szót: `water`. Az új entitás létrehozásához megjelenik egy legördülő menü, szövegmezővel a tetején. Adja meg az entitás nevét (`Drink`) a szövegmezőben, majd a legördülő menüben válassza a **Create new entity** (Új entitás létrehozása) lehetőséget. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "Új entitás létrehozásának képernyőképe a kimondott szöveg egy szavának kiválasztásával")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. Az előugró ablakban válassza a **List** (Lista) entitástípust. Adja hozzá a `h20` szinonimát. Nyomja le az Enter billentyűt minden szinonima után. A szinonimalistához ne adja hozzá a `perrier` kifejezést. Azt a következő lépés során fogjuk hozzáadni. Válassza a **Done** (Kész) lehetőséget.

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "Az új entitás konfigurálásának képernyőképe")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. Most, hogy az entitás elkészült, a szinonimák kiválasztásával címkézze meg a víz további szinonimáit, majd a legördülő listában válassza ki a `Drink` elemet. Kövesse a jobb oldali menüt, majd válassza a `Set as synonym`, végül pedig a `water` lehetőséget.

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "A kimondott szöveg felcímkézésének képernyőképe meglévő entitással")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>Entitáslista módosítása az Entity (Entitás) oldalon
Az ital entitáslistája elkészült, de még nem rendelkezik túl sok elemmel és szinonimával. Ha ismeri a vonatkozó kifejezések, rövidítések és szlengek egy részét, a listát az **Entity** (Entitás) oldalon gyorsabban kitöltheti. 

1. Válassza az **Entities** (Entitások) elemet a bal oldali ablaktáblán.

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "Képernyőkép az Entities (Entitások) kiválasztásáról a bal oldali panelen")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. Az entitáslistán válassza ki a következőt: `Drink`.

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "Képernyőkép a Drink (Ital) entitás entitáslistából történő kiválasztásáról")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. A szövegmezőbe írja be a következőt: `Soda pop`, majd nyomja le az Enter billentyűt. Ez egy olyan kifejezés, amelyet általában a szénsavas italokra használnak. Minden kultúrának van valamilyen helyi neve vagy szlengje az ilyen típusú italokra.

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "Kanonikus név bevitelének képernyőképe")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. Ugyanabban a sorban, ahol a `Soda pop` van, adjon meg szinonimákat, például: 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    A szinonimák tartalmazhatnak kifejezéseket, írásjeleket, birtokos eseteket és többes számú alakokat. Mivel a listaentitás pontos szövegegyezés (a nyelvtani esetet kivéve), a szinonimák összes változatát meg kell adni. A listát később kibővítheti a lekérdezési naplók vagy a végponti találatok áttekintése során feltárt változatokkal. 

    Ebben az oktatóanyagban csak néhány szinonimát használunk, hogy a példa ne legyen túl hosszú. Az éles LUIS-alkalmazásnak azonban számos szinonimával kell rendelkeznie, és a listát rendszeresen ellenőrizni és bővíteni kell. 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "Szinonimák hozzáadásának képernyőképe")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása
Amíg nincs betanítva, a LUIS nem ismeri fel a szándékok és entitások (a modell) módosításait. 

1. A LUIS-webhely jobb felső részén kattintson a **Train** (Betanítás) gombra.

    ![Az alkalmazás betanítása](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. A betanítás akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

    ![A betanítás sikeres volt](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez
Ahhoz, hogy LUIS-előrejelzéseket kaphasson egy csevegőrobotban vagy más alkalmazásban, közzé kell tennie az alkalmazást. 

1. A LUIS-webhely jobb felső részén válassza a **Publish** (Közzététel) lehetőséget. 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "A közzétételi gomb kiválasztásának képernyőképe")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "Képernyőkép a kiválasztott Publish to production slot (Közzététel éles termelési helyre) elemmel")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. A közzététel akkor van kész, ha a webhely tetején megjelenik a sikerességet jelző zöld állapotsáv.

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel
1. A **Publish** (Közzététel) lapon kattintson a lap alján található **Endpoint** (Végpont) hivatkozásra. Ez a művelet megnyit egy másik böngészőablakot, amelynek címsorában a végpont URL-címe látható. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "A végponti URL-cím képernyőképe a közzétételi lapon")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. Lépjen az URL-cím végéhez, és írja be a következőt: `2 cokes and 3 waters`. Az utolsó lekérdezésisztring-paraméter `q`, a kimondott szöveg pedig a **q**uery. A kimondott szöveg nem egyezik meg egyik címkézett kimondott szöveggel sem, ezért tesztnek pont megfelelő, és `OrderDrinks` szándékot kell visszaadnia `cokes` és `waters` italtípussal.

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Hogyan viszonyul a természetes nyelvi feldolgozás a listaentitásokhoz? 
Mivel a listaentitás pontos szövegegyezés, ezért nem támaszkodik a természetes nyelvi feldolgozásra (vagy gépi tanulásra). A LUIS a természetes nyelvi feldolgozás (vagy gépi tanulás) segítségével választja ki a legvalószínűbb szándékot. Egy kimondott szöveg állhat több entitás keverékéből, vagy akár többféle entitástípusból is. Az alkalmazás minden kimondott szöveget minden entitáshoz feldolgoz, beleértve az olyan természetes nyelvi feldolgozási (vagy gépi tanulási) entitásokat is, amilyen például a **Simple**.

## <a name="what-has-this-luis-app-accomplished"></a>Milyen műveleteket végzett el a LUIS-alkalmazás?
Az alkalmazás mindössze két szándékkal és egy listaentitással azonosított egy természetes nyelvi lekérdezési szándékot, és visszaadta a kinyert adatokat. 

A csevegőrobot már elég információval rendelkezik az elsődleges művelet, az `OrderDrinks` meghatározásához, valamint a rendelt italok típusának azonosításához a Drink list (Itallista) entitás alapján. 

## <a name="where-is-this-luis-data-used"></a>Hol vannak használatban ezek a LUIS-adatok? 
A LUIS végzett ezzel a kéréssel. A hívó alkalmazás, például egy csevegőrobot, felhasználhatja a topScoringIntent eredményt és az entitás adatait a következő lépés végrehajtásához. A LUIS nem végzi el ezt a programozható munkát a csevegőrobotnak vagy a hívó alkalmazásnak. A LUIS csak azt határozza meg, hogy mi a felhasználó szándéka. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza az alkalmazáslistában az alkalmazás neve mellett jobbra található három pontot (...), majd a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a reguláriskifejezés-entitás hozzáadásának módjával](luis-quickstart-intents-regex-entity.md)

**Number** (szám) [előre összeállított entitás](luis-how-to-add-entities.md#add-prebuilt-entity) megadása a szám kinyeréséhez. 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
