---
title: 'Oktatóanyag: strukturált adatok kinyerése géppel megtanult entitással – LUIS'
description: Strukturált adatok kinyerése egy teljes körű használatból a géppel megtanult entitás használatával. A kinyerési pontosság növeléséhez adjon hozzá alösszetevőket és megkötéseket.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 52bf2fb0b9f37e0c731a46c0aaf8b6c5e7f0e911
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80545856"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Oktatóanyag: strukturált adatok kinyerése a felhasználóktól a számítógép által megtanult entitásokkal Language Understanding (LUIS)

Ebben az oktatóanyagban kinyerheti a strukturált adatokból való kinyerését a géppel megtanult entitás használatával.

A géppel megtanult entitás támogatja a [modell dekompozíciós fogalmát](luis-concept-model.md#v3-authoring-model-decomposition) azáltal, hogy alösszetevő entitásokat biztosít a leírókkal és korlátozásokkal.

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * Gépi megtanult entitás hozzáadása
> * Alösszetevő hozzáadása
> * Alösszetevő leírójának hozzáadása
> * Alösszetevő korlátozásának hozzáadása
> * Alkalmazás betanítása
> * Alkalmazás tesztelése
> * Alkalmazás közzététele
> * Entitások előrejelzésének beolvasása a végpontról

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Miért érdemes gépi megtanult entitást használni?

Ez az oktatóanyag egy géppel megtanult entitást vesz fel az adatok kinyeréséhez.

Az entitás a kinyerni kívánt adatok meghatározását határozza meg. Ebbe beletartozik az adatok nevének, típusának (ha lehetséges) megadása, az adatok bármilyen felbontása, ha kétértelmű, és az adatok pontos szövege.

Az entitás definiálásához létre kell hoznia az entitást, majd fel kell címkéznie az entitást jelképező szöveget az összes szándékon belüli hosszúságú kimondott szöveg. Ezek a címkével ellátott példák megtanítják, hogy LUIS mit tartalmaz az entitás, és hol található a teljes leírás.

## <a name="entity-decomposability-is-important"></a>Az entitások lebontása fontos

Az entitások lebontása mind a szándék-előrejelzés, mind az entitással végzett kinyerés szempontjából fontos.

Kezdje egy géppel megtanult entitással, amely az kinyerés kezdetét és legfelső szintű entitása. Ezután az entitást az ügyfélalkalmazás által igényelt részekre kell bontani.

Habár előfordulhat, hogy nem tudja, milyen részletesen szeretné kipróbálni az alkalmazást az alkalmazás megkezdése után, az ajánlott eljárás az, hogy egy géppel megtanult entitással induljon el, majd az alkalmazás által lebontott alösszetevőkkel együtt felbomlik.

Ebben az esetben létrehoz egy géppel megtanult entitást, amely egy pizza-alkalmazás rendelését jelöli. A sorrendnek tartalmaznia kell a rendelés fullfil szükséges összes részt. Az entitás elindításához a rendszer kinyeri a sorrendtel kapcsolatos szöveget, kihúzza a méretet és a mennyiséget.

`Please deliver one large cheese pizza to me` A `one large cheese pizza` kinyerés a sorrend szerint, majd a és `1` `large`a kibontása.

További bontást is hozzáadhat, például alösszetevőket hozhat létre a feltöltésekhez vagy a kéreghez. Az oktatóanyag után érdemes felvennie ezeket az alösszetevőket a meglévő `Order` entitásba.

## <a name="import-example-json-to-begin-app"></a>Importálja például a. JSON fájlt az alkalmazás megkezdéséhez

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Szöveg címkéje entitásként például hosszúságú kimondott szöveg

A pizza-sorrend részleteinek kinyeréséhez hozzon létre egy legfelső szintű `Order` , géppel megtanult entitást.

1. A **leképezések** lapon válassza ki a **OrderPizza** szándékot.

1. A példa hosszúságú kimondott szöveg listán válassza ki a következő megjelölést.

    |Megrendelés – példa Kimondás|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Először válassza a `pickup` (#1) bal szélső szövegét, majd a jobb szélső szövegnél ugorjon `anchovies` (#2 – ezzel véget ér a címkézési folyamat). Megjelenik egy előugró menü. Az előugró ablakban adja meg az entitás nevét `Order` (#3). Ezután válassza `Order - Create new entity` ki a listából (#4).

    ![A szöveg elején és végén lévő felirat a teljes sorrendhez](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Az entitások nem mindig a teljes Kimondás. Ebben a konkrét esetben a `pickup` rendelés fogadásának módját jelzi. Fogalmi szempontból `pickup` a megrendeléshez a címkével ellátott entitásnak kell tartoznia.

1. Az **entitás típusának kiválasztása** mezőben válassza a **struktúra hozzáadása** lehetőséget, majd válassza a **tovább**lehetőséget. Az alösszetevők, például a méret és a mennyiség hozzáadásához struktúra szükséges.

    ![Struktúra hozzáadása az entitáshoz](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. A **megtanult számítógép létrehozása** mezőben a **struktúra** mezőben adja meg a Hozzáadás `Size` elemet, majd válassza az ENTER billentyűt.
1. **Leíró**hozzáadásához jelölje ki a elemet `+` **a leíró területen,** majd válassza az **új kifejezések listájának létrehozása**lehetőséget.

1. Az **új kifejezés létrehozása lista leíró** mezőjébe írja be a nevet `SizeDescriptor` , majd adja meg a `small`következőket `medium`:, `large`és. A **javaslatok** mező kitöltése után válassza `extra large`a és `xl`a elemet. Az új kifejezések listájának létrehozásához kattintson a **kész** gombra.

    Ez a kifejezés-lista leírója segít az alösszetevőnek a `Size` mérettel kapcsolatos szavak megkeresésében, például szavakkal megadva. A listának nem kell tartalmaznia minden méret szót, de tartalmaznia kell azokat a szavakat, amelyek várhatóan a méretet jelzik.

    ![Leíró létrehozása a méret alösszetevőhöz](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Az alösszetevő létrehozásának `Size` befejezéséhez a **Machine relearned Entity (gép létrehozása** ) ablakban válassza a **Létrehozás** lehetőséget.

    A `Order` rendszer létrehoz egy `Size` összetevővel rendelkező entitást, `Order` de csak az entitást alkalmazta a teljes értékre. Az `Size` entitás szövegét fel kell címkéznie a példában szereplő kifejezésre.

1. Ugyanebben a példában a legördülő listából válassza ki a **kívánt méretet** , és `large` válassza ki a **méret** entitást.

    ![A méret entitás szövegként való címkézése.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    A sor a szöveg alatt szilárd, mert a címkézési és az előrejelzési egyezés is, mivel _explicit módon_ címkézte a szöveget.

1. Címkézze fel `Order` az entitást a fennmaradó hosszúságú kimondott szöveg és a méret entitással együtt. A szöveg szögletes zárójelei a címkével ellátott `Order` entitást és a `Size` benne lévő entitást jelölik.

    |Rendelési példa hosszúságú kimondott szöveg|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Az entitások és alösszetevők az összes többi példa hosszúságú kimondott szöveg legyenek.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Hogyan kezelheti az olyan burkolt adategységeket, `a` mint például a levél egyetlen pizzát? Vagy a hiánya `pickup` , `delivery` illetve annak jelzése, hogy hol várható a pizza? Vagy egy méret hiánya, amely az alapértelmezett kis-vagy nagyméretet jelzi? A LUIS használata helyett vegye fontolóra a hallgatólagos adatkezelés kezelését az ügyfélalkalmazás üzleti szabályainak részeként.

1. Az alkalmazás betanításához válassza a **betanítás**lehetőséget. A képzés alkalmazza a módosításokat, például az új entitásokat és a címkével ellátott hosszúságú kimondott szöveg az aktív modellre.

1. A képzés után adjon hozzá egy új példát a szándékhoz, hogy megismerje, milyen jól ismeri el az LUIS a géppel megtanult entitást.

    |Megrendelés – példa Kimondás|
    |--|
    |`pickup XL meat lovers pizza`|

    Az általános felső entitás a `Order` címkével van ellátva `Size` , és az alösszetevő pontozott vonallal is címkézve van.

    ![Az entitással megjósolt új példa](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    A pontozott vonal jelzi az előrejelzést.

1. Ha módosítani szeretné az előrejelzést egy címkézett entitásra, válassza ki a sort, majd válassza az **entitások előrejelzésének megerősítése**lehetőséget.

    ![Az entitások előrejelzésének megerősítése lehetőség választásával fogadja el az előrejelzést.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Ezen a ponton a gép által megtanult entitás működik, mert az új példán belül megtalálja az entitást. Ha például hosszúságú kimondott szöveg ad hozzá, ha az entitás nem megfelelően van előre jelezve, címkézze fel az entitást és az alösszetevőket. Ha az entitás előre jelezve van, ügyeljen arra, hogy erősítse meg az előrejelzéseket.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Előre elkészített szám hozzáadása az adatok kinyeréséhez

A rendelési információnak tartalmaznia kell azt is, hogy hány elem van a sorrendben, például hogy hány pizzát tartalmaz. Az adatok kinyeréséhez új, géppel megismert alösszetevőt kell hozzáadni, `Order` és az összetevőnek előre elkészített számra vonatkozó korlátozást kell tartalmaznia. Az entitás egy előre elkészített számra való korlátozásával az entitás megkeresi és Kinyeri a számokat, hogy a szöveg `2`számjegyből, vagy `two`szövegből áll-e.

Először adja hozzá az előre elkészített szám entitást az alkalmazáshoz.

1. Válassza az **entitások** lehetőséget a bal oldali menüben, majd válassza az **+ előre összeépített entitás hozzáadása**elemet.

1. Az **előre létrehozott entitások hozzáadása** mezőben keresse meg és válassza a **szám** lehetőséget, majd válassza a **kész**lehetőséget.

    ![Előre összeépített entitás hozzáadása](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Az előre összeépített entitás hozzá van adva az alkalmazáshoz, de még nem megkötés.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Alösszetevő entitás létrehozása korlátozással az adatok kinyerése érdekében

Az `Order` entitásnak rendelkeznie kell `Quantity` egy alösszetevővel annak meghatározásához, hogy hány elem van a sorrendben. A mennyiséget korlátozni kell egy számra, hogy a kinyert mennyiség azonnal elérhető legyen az ügyfélalkalmazás számára.

A rendszer a megkötést szöveges egyeztetésként alkalmazza, vagy pontos egyezéssel (például lista entitással) vagy reguláris kifejezésekkel (például egy reguláris kifejezéssel vagy egy előre elkészített entitással).

A megkötés használatával csak a megkötésnek megfelelő szöveg lesz kibontva.

1. Válassza az **entitások** lehetőséget `Order` , majd válassza ki az entitást.
1. Válassza az **+ összetevő hozzáadása** lehetőséget, majd `Quantity` adja meg a nevet, majd válassza az ENTER billentyűt `Order` az új alösszetevő az entitáshoz való hozzáadásához.
1. A sikeres értesítés után a **Speciális beállítások**között válassza ki a megkötési ceruza elemet.
1. A legördülő listában válassza ki az előre elkészített számot.

    ![Előre elkészített számmal rendelkező mennyiségi entitás létrehozása korlátozásként.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    Az `Quantity` entitás akkor lesz alkalmazva, ha a szöveg megegyezik az előre elkészített szám entitással.

    A korlátozással rendelkező entitás létrejött, de még nincs alkalmazva a példában szereplő hosszúságú kimondott szöveg.

    > [!NOTE]
    > Egy alösszetevő legfeljebb 5 szintre ágyazható be egy alösszetevőn belül. Habár ez nem jelenik meg ebben a cikkben, a Portálon és az API-ban is elérhető.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Az entitással kapcsolatos LUIS tanítása – példa a kifejezésre

1. Válassza ki a bal oldali navigációs listából a **szándékot** , majd válassza ki a **OrderPizza** szándékát. A következő hosszúságú kimondott szöveg lévő három szám a címkével van ellátva, de vizuálisan az `Order` Entity (entitás) sorban látható. Ez az alacsonyabb szint azt jelenti, hogy az entitások találhatók, de nem minősülnek az `Order` entitás részének.

    ![A rendszer előre elkészített számot talált, de még nem veszi figyelembe a megrendelési entitást.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Címkézze fel a számokat az `Quantity` entitással úgy, `2` hogy kijelöli a példában a `Quantity` kiválasztás a listából lehetőséget. A `6` és a `1` címkét ugyanazzal a példával kell megjelölni.

    ![Szöveg címkéje mennyiségi entitással.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Az alkalmazás betanítása az entitás módosításának alkalmazására

Válassza a **betanítás** lehetőséget az alkalmazás az új hosszúságú kimondott szöveg való betanításához. A betanítást `Quantity` követően az alösszetevő megfelelően meg van jósolva az `Order` összetevőben. Ez a helyes előrejelzés folytonos vonallal van megjelölve.

![Végezze el az alkalmazás betanítását, majd tekintse át a példa hosszúságú kimondott szöveg.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Ezen a ponton a sorrendnek van néhány részlete, amely kinyerhető (méret, mennyiség és teljes sorrendű szöveg). Az entitás további finomítása, például `Order` a pizza-fehéráru, a kéreg típusa és a Side Orders. Mindegyiket az `Order` entitás alösszetevőiként kell létrehozni.

## <a name="test-the-app-to-validate-the-changes"></a>Az alkalmazás tesztelése a módosítások ellenőrzéséhez

Tesztelje az alkalmazást az interaktív **teszt** panel használatával. Ezzel a folyamattal megadhat egy új kiírást, majd megtekintheti az előrejelzés eredményeit, és megtekintheti, hogy milyen jól működik az aktív és a betanított alkalmazás. A szándék előrejelzésének meglehetősen magabiztosnak kell lennie (70% fölött) a kinyerési entitásnak legalább az `Order` entitást kell felvennie. Lehetséges, hogy a megrendelési entitás adatai hiányoznak, mert az 5 hosszúságú kimondott szöveg nem elegendő az összes eset kezelésére.

1. A felső navigációs ablakban válassza a **Test** (Tesztelés) elemet.
1. Adja meg a teljes `deliver a medium veggie pizza` értéket, és válassza az ENTER billentyűt. Az aktív modell a megfelelő szándékot jelezte, több mint 70%-os megbízhatósággal.

    ![Adjon meg egy új kiírást a szándék teszteléséhez.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Az entitások előrejelzésének megtekintéséhez kattintson a **vizsgálat** elemre.

    ![Tekintse meg az entitások előrejelzéseit az interaktív teszt panelen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    A méret helyesen lett azonosítva. Ne feledje, hogy a példában szereplő `OrderPizza` hosszúságú kimondott szöveg nem rendelkezik például `medium` mérettel, de egy közepes méretű `SizeDescriptor` kifejezési lista leíróját használja.

    A mennyiség nem megfelelően van előre jelezve. Ezt megteheti az ügyfélalkalmazás alapértelmezett méretének (1) értékének megadásával, ha a LUIS előrejelzés nem ad vissza értéket.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Az alkalmazás közzététele a HTTP-végpontról való hozzáféréshez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Cél és entitások előrejelzése HTTP-végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címsorban, és cserélje le a _YOUR_QUERY_HEREt_ ugyanazzal a lekérdezéssel, mint amit az interaktív teszt panelen megadott.

    `deliver a medium veggie pizza`

    Az utolsó querystring paraméter `query`a teljes **lekérdezés**.

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

* [Oktatóanyag – szándékok](luis-quickstart-intents-only.md)
* [Concept-entitások](luis-concept-entity-types.md) fogalmi információi
* [Koncepció – a funkciók](luis-concept-feature.md) fogalmi adatai
* [Betanítás](luis-how-to-train.md)
* [Közzétételi útmutató](luis-how-to-publish-app.md)
* [Tesztelés a LUIS portálon](luis-interactive-test.md)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az alkalmazás egy géppel megtanult entitás használatával keresi meg a felhasználó teljes részletességét, és Kinyeri a részleteket ebből a részletekből. A géppel megtanult entitás segítségével elvégezheti az entitás részleteit.

> [!div class="nextstepaction"]
> [Előre összeállított kulcskifejezés-entitás hozzáadása](luis-quickstart-intent-and-key-phrase.md)
