---
title: 'Oktatóanyag: strukturált adatok kinyerése géppel megtanult entitással – LUIS'
description: Strukturált adatok kinyerése egy teljes körű használatból a géppel megtanult entitás használatával. A kinyerési pontosság növeléséhez adja hozzá az alentitásokat a szolgáltatásokhoz.
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: d1bc8fc6aac52e264cb4352ca05f9df45ccfc50e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588870"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Oktatóanyag: strukturált adatok kinyerése a felhasználóktól a számítógép által megtanult entitásokkal Language Understanding (LUIS)

Ebben az oktatóanyagban kinyerheti a strukturált adatokból való kinyerését a géppel megtanult entitás használatával.

A géppel megtanult entitás támogatja a [modell dekompozíciós fogalmát](luis-concept-model.md#v3-authoring-model-decomposition) azáltal, hogy alentitási entitásokat biztosít a [funkciókhoz](luis-concept-feature.md).

**Az oktatóanyag a következőket ismerteti:**

> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * Gépi megtanult entitás hozzáadása
> * Alentitás és szolgáltatás hozzáadása
> * Betanítás, tesztelés, alkalmazás közzététele
> * Entitások előrejelzésének beolvasása a végpontról

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Miért érdemes gépi megtanult entitást használni?

Ez az oktatóanyag egy géppel megtanult entitást hoz létre, amellyel kinyerheti az adatok felhasználó általi kinyerését.

Az entitás a kinyerni kívánt adatok meghatározását határozza meg. Ebbe beletartozik az adatok nevének, típusának (ha lehetséges) megadása, az adatok bármilyen felbontása, ha kétértelmű, és az adatok pontos szövege.

Az adattípusok meghatározásához a következőket kell tennie:
* Az entitás létrehozása
* Az entitást jelölő, például hosszúságú kimondott szöveg lévő szöveg címkéje. Ezek a címkével ellátott példák megtanítják, hogy LUIS mit tartalmaz az entitás, és hol található a teljes leírás.

## <a name="entity-decomposability-is-important"></a>Az entitások lebontása fontos

Az entitások lebontása mind a szándék-előrejelzés, mind az entitással végzett kinyerés szempontjából fontos.

Kezdje egy géppel megtanult entitással, amely az kinyerés kezdetét és legfelső szintű entitása. Ezután bontsa ki az entitást alentitásokra.

Habár előfordulhat, hogy nem tudja, milyen részletesen szeretné kipróbálni az alkalmazást az alkalmazás megkezdése után, az ajánlott eljárás az, hogy egy géppel megtanult entitással induljon el, majd az alentitások alapján felbomlik az alkalmazás.

Ebben az oktatóanyagban egy géppel megtanult entitást hoz létre, amely egy pizza-alkalmazás rendelését jelöli. Az entitás Kinyeri a sorrendtel kapcsolatos szöveget, kihúzza a méretet és a mennyiséget.

A `Please deliver one large cheese pizza to me` kinyerés a `one large cheese pizza` sorrend szerint történik, majd a `1` mennyiség és a méret kibontása is `large` .

## <a name="download-json-file-for-app"></a>Alkalmazáshoz tartozó JSON-fájl letöltése

Töltse le és mentse az [alkalmazás JSON-fájlját](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

## <a name="import-json-file-for-app"></a>JSON-fájl importálása az alkalmazáshoz

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Szöveg címkéje entitásként például hosszúságú kimondott szöveg

A pizza-sorrend részleteinek kinyeréséhez hozzon létre egy legfelső szintű, géppel megtanult `Order` entitást.

1. A **leképezések** lapon válassza ki a **OrderPizza** szándékot.

1. A példa hosszúságú kimondott szöveg listán válassza ki a következő megjelölést.

    |Megrendelés – példa Kimondás|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Először válassza a (#1) bal szélső szövegét `pickup` , majd a jobb szélső szövegnél ugorjon `anchovies` (#2 – ezzel véget ér a címkézési folyamat). Megjelenik egy előugró menü. Az előugró ablakban adja meg az entitás nevét `Order` (#3). Ezután válassza ki `Order - Create new entity` a listából (#4).

    ![A szöveg elején és végén lévő felirat a teljes sorrendhez](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Az entitások nem mindig a teljes Kimondás. Ebben a konkrét esetben `pickup` a rendelés fogadásának módját jelzi. Fogalmi szempontból a `pickup` megrendeléshez a címkével ellátott entitásnak kell tartoznia.

1. Az **entitás típusának kiválasztása** mezőben válassza a **struktúra hozzáadása** lehetőséget, majd válassza a **tovább**lehetőséget. Az alentitások, például a méret és a mennyiség hozzáadásához struktúra szükséges.

    ![Struktúra hozzáadása az entitáshoz](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. A **megtanult számítógép létrehozása** mezőben a **struktúra** mezőben adja meg a Hozzáadás elemet, `Size` majd válassza az ENTER billentyűt.
1. **Szolgáltatás**hozzáadásához jelölje ki a elemet a `+` **szolgáltatások** területen, majd válassza az **új kifejezések listájának létrehozása**lehetőséget.

1. Az **új kifejezés létrehozása listában** adja meg a nevet, `SizeFeature` majd adja meg a: `small` , és a értéket `medium` `large` . A **javaslatok** mező kitöltése után válassza a és a elemet `extra large` `xl` . Az új kifejezések listájának létrehozásához kattintson a **kész** gombra.

    Ez a kifejezés-lista funkció segít az `Size` alentitásnak a mérettel kapcsolatos szavak megkeresésében, ha például szavakat biztosít. A listának nem kell tartalmaznia minden méret szót, de tartalmaznia kell azokat a szavakat, amelyek várhatóan a méretet jelzik.

    ![Szolgáltatás létrehozása a méret alentitáshoz](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Az alentitás létrehozásának befejezéséhez a **Machine retanult entitás létrehozása** ablakban válassza a **Létrehozás** lehetőséget `Size` .

    A rendszer `Order` létrehoz egy entitást, `Size` de csak az `Order` entitást alkalmazza a teljes értékre. Az entitás szövegét fel kell címkéznie a példában szereplő kifejezésre `Size` .

1. Ugyanebben a példában a kilépéshez a **méret** alentitást kell kijelölni a `large` szó kiválasztásával, majd a **méret** entitás kiválasztásával a legördülő listából.

    ![A méret entitás szövegként való címkézése.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    A sor a szöveg alatt szilárd, mert a címkézési és az előrejelzési egyezés is, mivel _explicit módon_ címkézte a szöveget.

1. Címkézze `Order` fel az entitást a fennmaradó hosszúságú kimondott szöveg és a méret entitással együtt. A szöveg szögletes zárójelei a címkével ellátott `Order` entitást és a `Size` benne lévő entitást jelölik.

    |Rendelési példa hosszúságú kimondott szöveg|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Az entitások és alentitások legyenek az összes többi példa hosszúságú kimondott szöveg.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Hogyan kezelheti az olyan burkolt adategységeket, mint például a levél `a` egyetlen pizzát? Vagy a hiánya, `pickup` illetve `delivery` annak jelzése, hogy hol várható a pizza? Vagy egy méret hiánya, amely az alapértelmezett kis-vagy nagyméretet jelzi? A LUIS használata helyett vegye fontolóra a hallgatólagos adatkezelés kezelését az ügyfélalkalmazás üzleti szabályainak részeként.

1. Az alkalmazás betanításához válassza a **betanítás**lehetőséget. A képzés alkalmazza a módosításokat, például az új entitásokat és a címkével ellátott hosszúságú kimondott szöveg az aktív modellre.

1. A képzés után adjon hozzá egy új példát a szándékhoz, hogy megismerje, milyen jól ismeri el az LUIS a géppel megtanult entitást.

    |Megrendelés – példa Kimondás|
    |--|
    |`pickup XL meat lovers pizza`|

    Az általános felső entitás `Order` címkével van ellátva, és az `Size` alentitás is pontozott vonallal van megjelölve.

    ![Az entitással megjósolt új példa](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    A pontozott vonal jelzi az előrejelzést.

1. Ha módosítani szeretné az előrejelzést egy címkézett entitásra, válassza ki a sort, majd válassza az **entitások előrejelzésének megerősítése**lehetőséget.

    ![Az entitások előrejelzésének megerősítése lehetőség választásával fogadja el az előrejelzést.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Ezen a ponton a gép által megtanult entitás működik, mert az új példán belül megtalálja az entitást. Ha például hosszúságú kimondott szöveg ad hozzá, ha az entitás nem megfelelően van előre jelezve, címkézze fel az entitást és az alentitásokat. Ha az entitás előre jelezve van, ügyeljen arra, hogy erősítse meg az előrejelzéseket.


<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="add-subentity-with-feature-of-prebuilt-entity"></a>Alentitás hozzáadása előre összeépített entitás funkcióval

A rendelési információnak tartalmaznia kell azt is, hogy hány elem van a sorrendben, például hogy hány pizzát tartalmaz. Ezeknek az adatoknak a kinyeréséhez hozzá kell adnia egy új, géppel megtanult alentitást, `Order` és az alentitásnak egy előre elkészített szám szükséges funkcióra van szüksége. Ha egy előre elkészített entitás funkcióját használja az entitás egy előre elkészített számra, akkor az entitás megkeresi és Kinyeri a számokat, hogy a szöveg számjegy, `2` vagy szöveg `two` .

## <a name="add-prebuilt-number-entity-to-app"></a>Előre összeállított szám entitás hozzáadása az alkalmazáshoz
A rendelési információnak tartalmaznia kell azt is, hogy hány elem van a sorrendben, például hogy hány pizzát tartalmaz. Az adatok kinyeréséhez új, géppel megismert alösszetevőt kell hozzáadni a `Order` szolgáltatáshoz, és az összetevőnek egy előre elkészített szám kötelező funkciójának kell lennie. Az entitás egy előre elkészített számra való korlátozásával az entitás megkeresi és Kinyeri a számokat, hogy a szöveg számjegyből, `2` vagy szövegből áll-e `two` .

Először adja hozzá az előre elkészített szám entitást az alkalmazáshoz.

1. Válassza az **entitások** lehetőséget a bal oldali menüben, majd válassza az **+ előre összeépített entitás hozzáadása**elemet.

1. Az **előre létrehozott entitások hozzáadása** mezőben keresse meg és válassza a **szám** lehetőséget, majd válassza a **kész**lehetőséget.

    ![Előre összeépített entitás hozzáadása](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Az előre összeépített entitás hozzá van adva az alkalmazáshoz, de még nem.

## <a name="create-subentity-entity-with-required-feature-to-help-extract-data"></a>Alentitás entitás létrehozása a szükséges funkcióval az adatok kinyerése érdekében

Az `Order` entitásnak rendelkeznie kell egy `Quantity` alentitással annak meghatározásához, hogy hány elem van a sorrendben. A mennyiségnek egy előre elkészített szám kötelező funkciójának kell lennie, hogy a kinyert érték azonnal elérhető legyen az ügyfélalkalmazás számára a név alapján.

Egy kötelező funkció szöveges egyeztetésként van alkalmazva, vagy pontos egyezéssel (például lista entitással) vagy reguláris kifejezésekkel (például egy reguláris kifejezéssel vagy egy előre összeépített entitással).

A nem géppel megismert entitások szolgáltatásként való használatával csak a megfelelő szöveg lesz kibontva.

1. Válassza az **entitások** lehetőséget, majd válassza ki az `Order` entitást.
1. Válassza az **+ entitás hozzáadása** lehetőséget, majd adja meg a nevet, majd válassza az ENTER billentyűt az `Quantity` új alentitás az entitáshoz való hozzáadásához `Order` .
1. A sikeres értesítés után a **Speciális beállítások**között válassza ki a megkötési ceruza elemet.
1. A legördülő listában válassza ki az előre elkészített számot.

    ![Előre elkészített számmal rendelkező mennyiségi entitás létrehozása korlátozásként.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    Az `Quantity` entitás akkor lesz alkalmazva, ha a szöveg megegyezik az előre elkészített szám entitással.

    A rendszer létrehozza a szükséges szolgáltatást tartalmazó entitást, de még nincs alkalmazva a példában szereplő hosszúságú kimondott szöveg.

    > [!NOTE]
    > Egy alentitás legfeljebb 5 szinten ágyazható be egy alentitásba. Habár ez nem jelenik meg ebben a cikkben, a Portálon és az API-ban is elérhető.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Az entitással kapcsolatos LUIS tanítása – példa a kifejezésre

1. Válassza ki a bal oldali navigációs listából a **szándékot** , majd válassza ki a **OrderPizza** szándékát. A következő hosszúságú kimondott szöveg lévő három szám a címkével van ellátva, de vizuálisan az `Order` Entity (entitás) sorban látható. Ez az alacsonyabb szint azt jelenti, hogy az entitások találhatók, de nem minősülnek az entitás részének `Order` .

    ![A rendszer előre elkészített számot talált, de még nem veszi figyelembe a megrendelési entitást.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Címkézze fel a számokat az `Quantity` entitással úgy, hogy kijelöli a `2` példában a kiválasztás a `Quantity` listából lehetőséget. A `6` és a címkét `1` ugyanazzal a példával kell megjelölni.

    ![Szöveg címkéje mennyiségi entitással.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Az alkalmazás betanítása az entitás módosításának alkalmazására

Válassza a **betanítás** lehetőséget az alkalmazás az új hosszúságú kimondott szöveg való betanításához. A betanítást követően az `Quantity` alentitás megfelelően meg van jósolva az `Order` entitásban. Ez a helyes előrejelzés folytonos vonallal van megjelölve.

![Végezze el az alkalmazás betanítását, majd tekintse át a példa hosszúságú kimondott szöveg.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Ezen a ponton a sorrendnek van néhány részlete, amely kinyerhető (méret, mennyiség és teljes sorrendű szöveg). Az entitás további finomítása `Order` , például a pizza-fehéráru, a kéreg típusa és a Side Orders. Mindegyiket az entitás alentitásának kell létrehoznia `Order` .

## <a name="test-the-app-to-validate-the-changes"></a>Az alkalmazás tesztelése a módosítások ellenőrzéséhez

Tesztelje az alkalmazást az interaktív **teszt** panel használatával. Ezzel a folyamattal megadhat egy új kiírást, majd megtekintheti az előrejelzés eredményeit, és megtekintheti, hogy milyen jól működik az aktív és a betanított alkalmazás. A szándék előrejelzésének meglehetősen magabiztosnak kell lennie (70% fölött) a kinyerési entitásnak legalább az entitást kell felvennie `Order` . Lehetséges, hogy a megrendelési entitás adatai hiányoznak, mert az 5 hosszúságú kimondott szöveg nem elegendő az összes eset kezelésére.

1. A felső navigációs ablakban válassza a **Test** (Tesztelés) elemet.
1. Adja meg a teljes értéket `deliver a medium veggie pizza` , és válassza az ENTER billentyűt. Az aktív modell a megfelelő szándékot jelezte, több mint 70%-os megbízhatósággal.

    ![Adjon meg egy új kiírást a szándék teszteléséhez.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Az entitások előrejelzésének megtekintéséhez kattintson a **vizsgálat** elemre.

    ![Tekintse meg az entitások előrejelzéseit az interaktív teszt panelen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    A méret helyesen lett azonosítva. Ne feledje, hogy a példában szereplő hosszúságú kimondott szöveg `OrderPizza` nem rendelkezik például `medium` mérettel, de használjon egy `SizeFeature` közepes méretű kifejezési lista funkcióját.

    A mennyiség nem megfelelően van előre jelezve. Ezt megteheti az ügyfélalkalmazás alapértelmezett méretének (1) értékének megadásával, ha a LUIS előrejelzés nem ad vissza értéket.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Az alkalmazás közzététele a HTTP-végpontról való hozzáféréshez

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Cél és entitások előrejelzése HTTP-végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címsorban, és cserélje le a _YOUR_QUERY_HEREt_ ugyanazzal a lekérdezéssel, mint amit az interaktív teszt panelen megadott.

    `deliver a medium veggie pizza`

    Az utolsó querystring paraméter a `query` teljes **lekérdezés**.

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
