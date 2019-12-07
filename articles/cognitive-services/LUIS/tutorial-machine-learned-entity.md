---
title: 'Oktatóanyag: strukturált adatok kinyerése géppel megtanult entitással – LUIS'
titleSuffix: Azure Cognitive Services
description: Strukturált adatok kinyerése egy teljes körű használatból a géppel megtanult entitás használatával. A kinyerési pontosság növeléséhez adjon hozzá alösszetevőket és megkötéseket.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: diberry
ms.openlocfilehash: 2d3bffd025d484ac928e21003b7cba9c63d2c514
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74885846"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Oktatóanyag: strukturált adatok kinyerése a felhasználóktól a számítógép által megtanult entitásokkal Language Understanding (LUIS)

Ebben az oktatóanyagban kinyerheti a strukturált adatokból való kinyerését a géppel megtanult entitás használatával.

A géppel megtanult entitás támogatja a [modell dekompozíciós fogalmát](luis-concept-model.md#v3-authoring-model-decomposition) azáltal, hogy alösszetevő entitásokat biztosít a leírókkal és korlátozásokkal.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:**

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

Az entitás célja a kinyerni kívánt adatok meghatározása. Ebbe beletartozik az adatok nevének, típusának (ha lehetséges) megadása, az adatok bármilyen felbontása, ha kétértelmű, és az adatok pontos szövege.

Az entitás definiálásához létre kell hoznia az entitást, majd fel kell címkéznie az entitást jelképező szöveget a példában szereplő szövegben. Ezek a címkével ellátott példák megtanítják, hogy LUIS mit tartalmaz az entitás, és hol található a teljes leírás.

## <a name="entity-decomposability-is-important"></a>Az entitások lebontása fontos

Az entitások lebontása mind a szándék-előrejelzés, mind az adatkiemelés szempontjából fontos.

Kezdje egy géppel megtanult entitással, amely az kinyerés kezdetét és legfelső szintű entitása. Ezután az entitást az ügyfélalkalmazás által igényelt részekre kell bontani.

Habár előfordulhat, hogy nem tudja, milyen részletesen szeretné kipróbálni az alkalmazást az alkalmazás megkezdése után, az ajánlott eljárás az, hogy egy géppel megtanult entitással induljon el, majd az alkalmazás által lebontott alösszetevőkkel együtt felbomlik.

A gyakorlatban egy géppel megtanult entitást hoz létre, amely egy pizza-alkalmazás rendelését jelöli. A sorrendnek tartalmaznia kell a rendelés fullfil szükséges összes részt. Az entitás elindításához a rendszer kinyeri a sorrendtel kapcsolatos szöveget, kihúzza a méretet és a mennyiséget.

A `Please deliver one large cheese pizza to me` kibontása `one large cheese pizza` rendelésként való kinyerése, majd `1` és `large`kinyerése.

További bontást is hozzáadhat, például alösszetevőket hozhat létre a feltöltésekhez vagy a kéreghez. Az oktatóanyag után érdemes felvennie ezeket az alösszetevőket a meglévő `Order` entitásba.

## <a name="import-example-json-to-begin-app"></a>Importálja például a. JSON fájlt az alkalmazás megkezdéséhez

1.  Töltse le és mentse az [alkalmazás JSON-fájlját](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Szöveg címkéje entitásként például hosszúságú kimondott szöveg

A pizza-sorrend részleteinek kinyeréséhez hozzon létre egy legfelső szintű, gépi megtanult `Order` entitást.

1. A **leképezések** lapon válassza ki a **OrderPizza** szándékot.

1. A példa hosszúságú kimondott szöveg listán válassza ki a következő megjelölést.

    |Megrendelés – példa Kimondás|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Először válassza ki a `pickup` (#1) bal szélső szövegét, majd a jobb szélső szövegnél, `anchovies` (#2 – Ez véget ér a címkézési folyamatnak). Megjelenik egy előugró menü. Az előugró ablakban adja meg az entitás nevét `Order` (#3). Ezután válassza ki a `Order - Create new entity` elemet a listából (#4).

    ![A szöveg elején és végén lévő felirat a teljes sorrendhez](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Az entitások nem mindig a teljes Kimondás. Ebben az esetben a `pickup` azt jelzi, hogyan kell fogadni a rendelést. Fogalmi szempontból `pickup` a megrendeléshez a címkével ellátott entitás részét kell képeznie.

1. Az **entitás típusának kiválasztása** mezőben válassza a **struktúra hozzáadása** lehetőséget, majd válassza a **tovább**lehetőséget. Az alösszetevők, például a méret és a mennyiség hozzáadásához struktúra szükséges.

    ![Struktúra hozzáadása az entitáshoz](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. A **megtanult számítógép létrehozása** mezőben, a **struktúra** mezőben adja hozzá `Size` majd kattintson az ENTER gombra.
1. **Leíró**hozzáadásához jelölje ki a `+` a méret terület **leírókban** , majd válassza az **új kifejezések listájának létrehozása**lehetőséget.

1. Az **új kifejezés létrehozása lista leíró** mezőjébe írja be a nevet `SizeDescriptor` majd adja meg a (z) `small`, `medium`és `large`értékeit. A **javaslatok** mező kitöltése után válassza a `extra large`lehetőséget, és `xl`. Az új kifejezések listájának létrehozásához kattintson a **kész** gombra.

    Ez a kifejezési lista leírója segít a `Size` alösszetevőnek a mérettel kapcsolatos szavakat megtalálnia, ha például szavakat biztosít. A listának nem kell tartalmaznia minden méret szót, de tartalmaznia kell azokat a szavakat, amelyek várhatóan a méretet jelzik.

    ![Leíró létrehozása a méret alösszetevőhöz](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Az `Size` alösszetevő létrehozásához a **Machine retanult entitás létrehozása** ablakban kattintson a **Létrehozás** gombra.

    A rendszer létrehoz egy `Size` összetevővel rendelkező `Order` entitást, de csak a `Order` entitás lett alkalmazva a teljes értékre. Az `Size` entitás szövegét kell megadnia a példa kimondása során.

1. Ugyanebben a példában a legördülő listából válassza ki a `large` **méretét** . Ehhez jelölje ki a szót, majd válassza ki a **méret** entitást.

    ![A méret entitás szövegként való címkézése.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    A sor a szöveg alatt szilárd, mert a címkézési és az előrejelzési egyezés is, mivel explicit módon címkézte a szöveget.

1. Címkézze fel a `Order` entitást a fennmaradó hosszúságú kimondott szöveg és a méret entitással együtt. A szöveg szögletes zárójelei azt jelzik, hogy a címkével ellátott `Order` entitás és a `Size` entitás található.

    |Rendelési példa hosszúságú kimondott szöveg|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Az entitások és alösszetevők az összes többi példa hosszúságú kimondott szöveg legyenek.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Hogyan kezelheti az olyan vélelmezett adatszolgáltatásokat, mint például a levél `a` egyetlen pizzát? Vagy `pickup` hiánya és `delivery`, hogy jelezze, hol várható a pizza? Vagy egy méret hiánya, amely az alapértelmezett kis-vagy nagyméretet jelzi? A LUIS használata helyett vegye fontolóra a hallgatólagos adatkezelés kezelését az ügyfélalkalmazás üzleti szabályainak részeként.

1. Az alkalmazás betanításához válassza a **betanítás**lehetőséget. A képzés alkalmazza a módosításokat, például az új entitásokat és a címkével ellátott hosszúságú kimondott szöveg az aktív modellre.

1. A képzés után adjon hozzá egy új példát a szándékhoz, hogy megismerje, milyen jól ismeri el az LUIS a géppel megtanult entitást.

    |Megrendelés – példa Kimondás|
    |--|
    |`pickup XL meat lovers pizza`|

    A legfelső szintű entitás, `Order` címkével van ellátva, és a `Size` alösszetevő is pontozott vonallal van megjelölve. Ez egy sikeres előrejelzés.

    ![Az entitással megjósolt új példa](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    A pontozott vonal jelzi az előrejelzést.

1. Ha módosítani szeretné az előrejelzést egy címkézett entitásra, válassza ki a sort, majd válassza az **entitások előrejelzésének megerősítése**lehetőséget.

    ![Az entitások előrejelzésének megerősítése lehetőség választásával fogadja el az előrejelzést.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Ezen a ponton a gép által megtanult entitás működik, mert az új példán belül megtalálja az entitást. Ha például hosszúságú kimondott szöveg ad hozzá, ha az entitás nem megfelelően van előre jelezve, címkézze fel az entitást és az alösszetevőket. Ha az entitás előre jelezve van, ügyeljen arra, hogy erősítse meg az előrejelzéseket.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Előre elkészített szám hozzáadása az adatok kinyeréséhez

A rendelési információnak tartalmaznia kell azt is, hogy hány elem van a sorrendben, például hogy hány pizzát tartalmaz. Az adatok kinyeréséhez új, géppel megismert alösszetevőt kell hozzáadni a `Order`hoz, és az összetevőnek előre elkészített számra vonatkozó korláttal kell rendelkeznie. Az entitás előre elkészített számra való korlátozásával az entitás megkeresi és Kinyeri a számokat, hogy a szöveg számjegy, `2`vagy szöveg, `two`.

Először adja hozzá az előre elkészített szám entitást az alkalmazáshoz.

1. Válassza az **entitások** lehetőséget a bal oldali menüben, majd válassza az **+ előre összeépített entitás hozzáadása**elemet.

1. Az **előre létrehozott entitások hozzáadása** mezőben keresse meg és válassza a **szám** lehetőséget, majd válassza a **kész**lehetőséget.

    ![Előre összeépített entitás hozzáadása](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Az előre összeépített entitás hozzá van adva az alkalmazáshoz, de még nem megkötés.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Alösszetevő entitás létrehozása korlátozással az adatok kinyerése érdekében

A `Order` entitásnak `Quantity` alösszetevővel kell rendelkeznie annak meghatározásához, hogy hány elem van a sorrendben. A mennyiséget korlátozni kell egy számra, hogy a kinyert adatmennyiség azonnal használható legyen az ügyfélalkalmazás számára.

A rendszer a megkötést szöveges egyeztetésként alkalmazza, vagy pontos egyezéssel (például lista entitással) vagy reguláris kifejezésekkel (például egy reguláris kifejezéssel vagy egy előre elkészített entitással).

A megkötés használatával csak a megkötésnek megfelelő szöveg lesz kibontva.

1. Válassza az **entitások** lehetőséget, majd válassza ki a `Order` entitást.
1. Válassza az **+ összetevő hozzáadása** lehetőséget, majd adja meg `Quantity` nevét, majd az ENTER billentyűt választva adja hozzá az új entitást az alkalmazáshoz.
1. A sikeres értesítés után válassza ki a `Quantity` alösszetevőt, majd válassza ki a megkötési ceruza elemet.
1. A legördülő listában válassza ki az előre elkészített számot.

    ![Előre elkészített számmal rendelkező mennyiségi entitás létrehozása korlátozásként.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    A `Quantity` entitás akkor van alkalmazva, ha és csak akkor, ha az előre elkészített szám entitásnak megfelelő szöveg található.

    A korlátozással rendelkező entitás létrejött, de még nincs alkalmazva a példában szereplő hosszúságú kimondott szöveg.

    > [!NOTE]
    > Egy alösszetevő legfeljebb 5 szintre ágyazható be egy alösszetevőn belül. Habár ez nem jelenik meg ebben a cikkben, a Portálon és az API-ban is elérhető.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Az entitással kapcsolatos LUIS tanítása – példa a kifejezésre

1. Válassza ki a bal oldali navigációs listából a **szándékot** , majd válassza ki a **OrderPizza** szándékát. A következő hosszúságú kimondott szöveg szereplő három szám a címkével van ellátva, de a `Order` az entitás sorban látható. Ez az alacsonyabb szint azt jelenti, hogy az entitások találhatók, de nem a `Order` entitástól függetlenül.

    ![A rendszer előre elkészített számot talált, de még nem veszi figyelembe a megrendelési entitást.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Címkézze fel a számokat a `Quantity` entitással úgy, hogy kijelöli a `2` a példában a kiválasztó elemben, majd kiválasztja a `Quantity` lehetőséget a listából. Címkézze fel a `6` és a `1` ugyanabban a példában.

    ![Szöveg címkéje mennyiségi entitással.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Az alkalmazás betanítása az entitás módosításának alkalmazására

Válassza a **betanítás** lehetőséget az alkalmazás az új hosszúságú kimondott szöveg való betanításához.

![Végezze el az alkalmazás betanítását, majd tekintse át a példa hosszúságú kimondott szöveg.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Ezen a ponton a sorrendnek van néhány részlete, amely kinyerhető (méret, mennyiség és teljes sorrendű szöveg). További finomításra kerül a `Order` entitás, például a pizza-fehéráru, a kéreg típusa és a Side Orders. Mindegyiket a `Order` entitás alösszetevőiként kell létrehozni.

## <a name="test-the-app-to-validate-the-changes"></a>Az alkalmazás tesztelése a módosítások ellenőrzéséhez

Tesztelje az alkalmazást az interaktív **teszt** panel használatával. Ezzel a folyamattal megadhat egy új kiírást, majd megtekintheti az előrejelzés eredményeit, és megtekintheti, hogy milyen jól működik az aktív és a betanított alkalmazás. A szándék előrejelzésének meglehetősen magabiztosnak kell lennie (70% fölött) az entitások kinyerésének legalább a `Order` entitást kell felvennie. Lehetséges, hogy a megrendelési entitás adatai hiányoznak, mert az 5 hosszúságú kimondott szöveg nem elegendő az összes eset kezelésére.

1. A felső navigációs ablakban válassza a **Test** (Tesztelés) elemet.
1. Adja meg a teljes `deliver a medium veggie pizza`, majd válassza az ENTER billentyűt. Az aktív modell a megfelelő szándékot jelezte, több mint 70%-os megbízhatósággal.

    ![Adjon meg egy új kiírást a szándék teszteléséhez.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Az entitások előrejelzésének megtekintéséhez kattintson a **vizsgálat** elemre.

    ![Tekintse meg az entitások előrejelzéseit az interaktív teszt panelen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    A méret helyesen lett azonosítva. Ne feledje, hogy a `OrderPizza` szándékban lévő példa hosszúságú kimondott szöveg nem rendelkezik például mérettel `medium`, de egy közepes méretű `SizeDescriptor`-kifejezés leíróját használja.

    A mennyiség nem megfelelően van előre jelezve. Ennek a megoldásnak a kijavításához több példát is hozzáadhat a hosszúságú kimondott szöveg, hogy jelezze a Word `Quantity` entitásának mennyiségét és címkéjét.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Az alkalmazás közzététele a HTTP-végpontról való hozzáféréshez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Cél és entitások előrejelzése HTTP-végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Nyissa meg a címet az URL-cím végére, és adja meg ugyanazt a lekérdezést, amelyet az interaktív tesztelési panelen megadott.

    `deliver a medium veggie pizza`

    Az utolsó lekérdezésisztring-paraméter `query`, a kimondott szöveg pedig a **query**.

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

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az alkalmazás egy géppel megtanult entitás használatával keresi meg a felhasználó teljes részletességét, és Kinyeri a részleteket ebből a részletekből. A géppel megtanult entitás segítségével elvégezheti az entitás részleteit.

> [!div class="nextstepaction"]
> [Előre összeállított kulcskifejezés-entitás hozzáadása](luis-quickstart-intent-and-key-phrase.md)
