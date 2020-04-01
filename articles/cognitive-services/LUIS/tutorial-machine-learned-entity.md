---
title: 'Oktatóanyag: strukturált adatok kinyerése gépmegtanult entitással – LUIS'
description: Strukturált adatok kinyerése egy utterance (kifejezés) a gép által megtanult entitás használatával. A kivonáspontosság növelése érdekében adjon hozzá alösszetevőket leírókkal és megkötésekkel.
ms.topic: tutorial
ms.date: 03/31/2020
ms.openlocfilehash: f521e634c129aaf03ee341328721bd89723e8264
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478338"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Oktatóanyag: Strukturált adatok kinyerése a felhasználói utterance (kifejezés) a gép által megtanult entitások nyelvi megértés (LUIS)

Ebben az oktatóanyagban a strukturált adatok kinyerése egy utterance (kifejezés) a gép által megtanult entitás használatával.

A gép megtanult entitás támogatja a [modell bomlási koncepció](luis-concept-model.md#v3-authoring-model-decomposition) azáltal, hogy alkomponens entitások azok leírók és korlátozások.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Példaalkalmazás importálása
> * Gépben tanult entitás hozzáadása
> * Alösszetevő hozzáadása
> * Alösszetevő leírójának hozzáadása
> * Alkomponens megkötésének hozzáadása
> * Alkalmazás betanítása
> * Teszt alkalmazás
> * Alkalmazás közzététele
> * Entitás-előrejelzés beszereznie a végpontból

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Miért érdemes gépileg megtanult entitást használni?

Ez az oktatóanyag egy gép által megtanult entitást ad hozzá egy utterance (kifejezés) adatok kinyerése.

Az entitás határozza meg az adatokat kinyerni az utterance (kifejezés) belül. Ez magában foglalja, hogy az adatok nevét, típusát (ha lehetséges), az adatok bármilyen felbontását, ha van kétértelműség, és a pontos szöveget, amely az adatokat alkotja.

Az entitás meghatározásához létre kell hoznia az entitást, majd meg kell címkéznie az entitást képviselő szöveget a példa kimondott szövegben az összes leképezésen belül. Ezek a címkézett példák tanítják meg a LUIS-t, hogy mi az entitás, és hol található egy utterance (kifejezés).

## <a name="entity-decomposability-is-important"></a>A szervezet bomlása fontos

Entitás bomlási fontos mind a szándék előrejelzése és az entitással történő adatkinyerés fontos.

Kezdje egy gép által megtanult entitás, amely a kezdő és legfelső szintű entitás adatkinyerés. Ezután az entitást az ügyfélalkalmazás által szükséges részekre bomlik le.

Bár előfordulhat, hogy nem tudja, hogy milyen részletesen szeretné az entitást az alkalmazás indításakor, a legjobb gyakorlat az, hogy egy gép által megtanult entitással kezdi, majd az alkalmazás érlelődése során alösszetevőkkel bomlik le.

Ebben hozzon létre egy gép megtanult entitást egy pizzaalkalmazás rendelésének ábrázolásához. A parancsnak rendelkeznie kell minden olyan alkatrészt, ami a rendelés teljes beszivárgásához szükséges. Először is, az entitás kinyeri a rendeléssel kapcsolatos szöveget, kihúzva a méretet és a mennyiséget.

Egy utterance `Please deliver one large cheese pizza to me` (kifejezés) kell kivonat `one large cheese pizza` `1` a `large`sorrendben, majd kivonat és.

Van további bomlás felveheti, mint például a létrehozása alkomponensek öntetek vagy kéreg. Az oktatóanyag után biztosnak kell lennie abban, `Order` hogy ezeket az alösszetevőket hozzáadja a meglévő entitáshoz.

## <a name="import-example-json-to-begin-app"></a>Példát importálhat.json alkalmazás kezdéséhez

1.  Töltse le és mentse az [alkalmazás JSON fájlt](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Szöveg feliratozása entitásként a példa kimondott szövegekben

A pizzarendelés részleteinek kinyeréséhez hozzon `Order` létre egy legfelső szintű, gépáltal megtanult entitást.

1. A **Szándékok** lapon válassza ki az OrderPizza-leképezést. **OrderPizza**

1. A példa utterances listában válassza ki a következő utterance (kifejezés).

    |Rendelési példa utterance|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Kezdje el a kijelölést közvetlenül a (#1) bal szélső szövege `pickup` előtt, majd lépjen túl a jobb szélső szövegen (#2 `anchovies` - ez befejezi a címkézési folyamatot). Megjelenik egy előugró menü. Az előugró mezőbe írja be az entitás `Order` nevét (#3). Ezután `Order - Create new entity` válasszon a listából (#4).

    ![A teljes rendeléshez szükséges szöveg kezdő és záró felirata](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Egy entitás nem mindig lesz a teljes utterance (kifejezés). Ebben a konkrét `pickup` esetben azt jelzi, hogy a rendelés hogyan érkezik. Fogalmi szempontból `pickup` a rendelés címkézett entitásának részét kell, hogy vegye.

1. Az **Entitástípus kiválasztása** mezőben válassza a **Struktúra hozzáadása** lehetőséget, majd a **Tovább**lehetőséget. A szerkezet szükséges az olyan alösszetevők hozzáadásához, mint a méret és a mennyiség.

    ![Struktúra hozzáadása az entitáshoz](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. A **Gép tanult entitás létrehozása** párbeszédpanel **Struktúra mezőben** válassza a Hozzáadás `Size` lehetőséget, majd válassza az Enter lehetőséget.
1. Leíró **descriptor**hozzáadásához jelölje ki `+` a **leírók** területen, majd kattintson **az Új kifejezéslista létrehozása**gombra.

1. Az **Új kifejezéslista létrehozása** mezőben adja `SizeDescriptor` meg a nevét, `medium`majd `large`írja be a következő értékeket: `small`, és . Amikor a **Javaslatok** mező kitöltődik, válassza a `extra large`és `xl`a lehetőséget. Az új kifejezéslista létrehozásához válassza a **Kész** lehetőséget.

    Ez a kifejezéslista-leíró segít az alösszetevőnek a `Size` mérethez kapcsolódó szavak keresésében azáltal, hogy példaszavakat ad. Ennek a listának nem kell minden méretű szót tartalmaznia, de tartalmaznia kell azokat a szavakat, amelyek várhatóan jelzik a méretet.

    ![A méret-alösszetevő leírójának létrehozása](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. A **Gépmegtanult entitás létrehozása** ablakban válassza `Size` a **Létrehozás** lehetőséget az alösszetevő létrehozásának befejezéséhez.

    Az `Order` `Size` összetevővel rendelkező entitás jön `Order` létre, de csak az entitás lett alkalmazva az utterance (kifejezés). Meg kell címkézni az `Size` entitás szövegét a példa utterance (kifejezés) felirat.

1. Ugyanebben a példában az utterance `large` (kifejezés) felirata a **méret** alösszetevő kiválasztásával a szót, majd kiválasztja a **Méret** entitást a legördülő listából.

    ![Címkézze fel a szöveg et tartalmazó szöveg méretentitását.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    A sor tömör a szöveg alatt, mert mind a feliratozás, mind az előrejelzés egyezik, mert _a szöveget kifejezetten_ címkézte.

1. Címkézze `Order` fel az entitást a fennmaradó kimondott szövegben a méret entitással együtt. A szövegben lévő szögletes `Order` zárójelek `Size` a címkézett entitást és a benne lévő entitást jelzik.

    |Rendelési példa kimondott szöveg|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Entitás és alösszetevők az összes fennmaradó példa utterances.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Hogyan kezeli implikált adatok, mint például a levél `a` arra utal, hogy egy pizza? Vagy a `pickup` hiánya, és `delivery` jelzi, ahol a pizza várható? Vagy a méret hiánya jelzi az alapértelmezett mérete kicsi vagy nagy? Fontolja meg a hallgatólagos adatkezelés kezelése az üzleti szabályok részeként az ügyfélalkalmazásban a LUIS helyett vagy mellett.

1. Az alkalmazás betanításához válassza a **Vonat**lehetőséget. A betanítás a módosításokat, például az új entitásokat és a címkézett utterances az aktív modell.

1. A betanítás után adjon hozzá egy új példa utterance (kifejezés) a szándékot, hogy milyen jól LUIS megérti a gép által megtanult entitás.

    |Rendelési példa utterance|
    |--|
    |`pickup XL meat lovers pizza`|

    A teljes felső `Order` entitás van `Size` címkézve, és az alösszetevő is feliratozott pontozott vonalak.

    ![Új példa az entitással előrejelzett kimondott szöveg](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    A pontozott vonal jelzi az előrejelzést.

1. Ha az előrejelzést címkézett entitássá szeretné módosítani, jelölje ki a sort, majd válassza **az Entitás-előrejelzések megerősítése**lehetőséget.

    ![Az előrejelzés elfogadása az entitás-előrejelzés megerősítése lehetőség kiválasztásával.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Ezen a ponton a gép megtanult entitás működik, mert egy új példa utterance (kifejezés) az entitás t. Példautterances hozzáadásakor, ha az entitás nem előre jelzett megfelelően, címkézze az entitás és az alösszetevők. Ha az entitás előre jelzett helyesen, győződjön meg arról, hogy erősítse meg az előrejelzéseket.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Előre összeállított szám hozzáadása az adatok kinyeréséhez

A rendelési adatoknak tartalmazniuk kell azt is, hogy egy cikk hány van a sorrendben, például hány pizza. Ezek az adatok kinyeréséhez egy új, gép `Order` megtanult alösszetevőt kell hozzáadni, és az összetevőnek egy előre összeállított szám megkötésére van szüksége. Ha az entitást egy előre összeállított számra korlátozza, az entitás megfogja `2`találni és `two`kinyeri a számokat, függetlenül attól, hogy a szöveg számjegy, vagy szöveg.

Kezdje azzal, hogy hozzáadja az előre összeállított számentitást az alkalmazáshoz.

1. Válassza az **Entitások lehetőséget** a bal oldali menüből, majd válassza **az + Előre összeállított entitás hozzáadása**lehetőséget .

1. Az **Előre összeállított entitások hozzáadása** mezőben keresse meg és válassza ki a **számot,** majd válassza a **Kész**lehetőséget.

    ![Előre összeállított entitás hozzáadása](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Az előre összeállított entitás hozzáadódik az alkalmazáshoz, de még nem megkötés.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Kényszerrel rendelkező alösszetevő létrehozása az adatok kinyeréséhez

Az `Order` entitásnak `Quantity` rendelkeznie kell egy alösszetevővel annak meghatározásához, hogy egy cikk hány cikk van a sorrendben. A mennyiséget egy számra kell korlátozni, hogy a kinyert adatok azonnal elérhetők legyenek az ügyfélalkalmazás számára név szerint.

A megkötés szövegegyezésként kerül alkalmazásra, vagy pontos egyezéssel (például listaentitással), vagy reguláris kifejezéseken (például reguláris kifejezésentitáson vagy előre összeállított entitáson) keresztül.

Megkötés használatával csak a megkötésnek megfelelő szöveg lesz kibontva.

1. Válassza **az Entitások lehetőséget,** majd válassza ki az `Order` entitást.
1. Válassza a **+ Összetevő hozzáadása lehetőséget,** majd írja be a nevet, `Quantity` majd válassza az Enter lehetőséget az új alösszetevő `Order` entitáshoz való hozzáadásához.
1. A sikerességi értesítés után a **Speciális beállítások**párbeszédpanelen válassza a Kényszer ceruzát.
1. A legördülő listában válassza ki az előre összeállított számot.

    ![Előre összeállított számmal rendelkező mennyiségi entitás létrehozása megszorításként.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    Az `Quantity` entitás akkor kerül alkalmazásra, ha a szöveg megegyezik az előre összeállított számentitással.

    A megkötéssel rendelkező entitás létrejön, de még nem alkalmazva a példa kimondott szövegre.

    > [!NOTE]
    > Egy alösszetevő legfeljebb 5 szintbe ágyazható be egy alösszetevőbe. Bár ez nem jelenik meg ebben a cikkben, a portálról és az API-ból érhető el.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Címke példa utterance s teach LUIS az entitásról

1. Válassza **a szándékok** a bal oldali navigációs, majd válassza ki a **OrderPizza** szándékot. A következő kimondott szövegben a három szám van `Order` címkézve, de vizuálisan az entitássor alatt vannak. Ez az alacsonyabb szint azt jelenti, hogy az `Order` entitások megtalálhatók, de nem tekinthetők a gazdálkodó egység részének.

    ![Az előre összeállított szám megtalálható, de még nem része a Rendelés entitásnak.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Címkézze fel `Quantity` a számokat `2` az entitással a `Quantity` példa utterance (kifejezés) kiválasztásával, majd a listából való kiválasztással. Címkézze `6` fel `1` a és az ugyanabban a példában utterance (kifejezés).

    ![Címkézze fel a szöveget mennyiségi entitással.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Az alkalmazás betanítása az entitásmódosítások alkalmazásra való alkalmazásához

Válassza a **Vonat** az alkalmazás betanításához ezekkel az új kimondott szövegekkel. Edzés után `Quantity` az alkomponens megfelelően előre `Order` jelzett az összetevőben. Ezt a helyes előrejelzést egy folytonos vonal jelzi.

![Az alkalmazás betanítása, majd tekintse át a példa utterances.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Ezen a ponton a rendelés tartalmaz néhány részletet, amelyek kinyerhetők (méret, mennyiség és teljes rendelésszöveg). Van további finomítása a `Order` szervezet, mint a pizza öntetek, típusú kéreg, és az oldalsó megrendelések. Ezeket az `Order` entitás alösszetevőiként kell létrehozni.

## <a name="test-the-app-to-validate-the-changes"></a>Az alkalmazás tesztelése a módosítások ellenőrzéséhez

Tesztelje az alkalmazást **Test** az interaktív tesztpanelen. Ez a folyamat lehetővé teszi, hogy adjon meg egy új utterance (kifejezés) majd tekintse meg az előrejelzési eredményeket, hogy milyen jól működik az aktív és a betanított alkalmazás. A szándék előrejelzése kell meglehetősen magabiztos (70% felett és az entitás kivonásának `Order` legalább a gazdálkodó egységet fel kell vennie. A rendelési entitás részletei hiányozhatnak, mert 5 kimondott szöveg nem elegendő minden eset kezeléséhez.

1. A felső navigációs ablakban válassza a **Test** (Tesztelés) elemet.
1. Adja meg `deliver a medium veggie pizza` az utterance (kifejezés) szöveget, és válassza az Enter lehetőséget. Az aktív modell több mint 70%-os megbízhatósággal jósolta meg a helyes szándékot.

    ![Adjon meg egy új utterance (kifejezés) a szándék teszteléséhez.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Válassza **a Vizsgálat** lehetőséget az entitás-előrejelzések megtekintéséhez.

    ![Tekintse meg az entitás előrejelzések az interaktív teszt panelen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    A méretet helyesen azonosították. Ne feledje, hogy a `OrderPizza` szándékban szereplő példautterances nem rendelkezik példát `medium` a `SizeDescriptor` méret, de használja a kifejezéslista, amely közepes.

    A mennyiség nem megfelelően előre jelzett. Ezt az ügyfélalkalmazásban úgy javíthatja ki, hogy a mérete tegy (1) lesz, ha a LUIS-előrejelzésnem ad vissza méretet.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Az alkalmazás közzététele a HTTP-végpontról való eléréséhez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Leképezés és entitás-előrejelzés beszerezni e-http-végpontról

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címben, és cserélje le _YOUR_QUERY_HERE_ ugyanazzal a lekérdezéssel, mint amelyet az interaktív tesztpanelen írt be.

    `deliver a medium veggie pizza`

    Az utolsó querystring `query`paraméter a ( utterance ( kifejezés ) **lekérdezés.**

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Kapcsolódó információk

* [Oktatóanyag - szándékok](luis-quickstart-intents-only.md)
* [Koncepció - entitások](luis-concept-entity-types.md) koncepcionális tájékoztatása
* [Koncepció - funkciók](luis-concept-feature.md) fogalmi információk
* [Hogyan kell a vonat](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az alkalmazás egy gép által megtanult entitást használ a felhasználó utterance (kifejezés) szándékának megkereséséhez, és az utterance (kifejezés) részleteinek kinyeréséhez. A gép megtanult entitás használatával lehetővé teszi az entitás részleteinek lefektése.

> [!div class="nextstepaction"]
> [Előre összeállított kulcskifejezés-entitás hozzáadása](luis-quickstart-intent-and-key-phrase.md)
