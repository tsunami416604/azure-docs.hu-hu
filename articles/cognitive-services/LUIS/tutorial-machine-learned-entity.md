---
title: 'Oktatóanyag: strukturált adatok kinyerése gépi tanulási entitással – LUIS'
description: A gépi tanulási entitás használatával kinyerheti a strukturált adatok teljes körű kinyerését. A kinyerési pontosság növeléséhez adja hozzá az alentitásokat a szolgáltatásokhoz.
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: eb9761a3d3a98a3318fe0adc6fa170652639a9a1
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "86045603"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>Oktatóanyag: strukturált adatok kinyerése a felhasználóktól a gépi tanulási entitásokkal Language Understanding (LUIS)

Ebben az oktatóanyagban a gépi tanulási entitás használatával kinyerheti a strukturált adatokból való kinyerést.

A gépi tanulási entitás támogatja a [modell dekompozíciós fogalmát](luis-concept-model.md#v3-authoring-model-decomposition) azáltal, hogy alentitási entitásokat biztosít a [funkciókhoz](luis-concept-feature.md).

**Eben az oktatóanyagban az alábbiakkal fog megismerkedni:**

> [!div class="checklist"]
> * Alkalmazás importálása – példa
> * Gépi tanulási entitás hozzáadása
> * Alentitás és szolgáltatás hozzáadása
> * Betanítás, tesztelés, alkalmazás közzététele
> * Entitások előrejelzésének beolvasása a végpontról

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>Miért érdemes gépi tanulási entitást használni?

Ez az oktatóanyag egy gépi tanulási entitást hoz létre, amellyel kinyerheti az adatok felhasználó általi kinyerését.

Az entitás a kinyerni kívánt adatok meghatározását határozza meg. Ebbe beletartozik az adatok nevének, típusának (ha lehetséges) megadása, az adatok bármilyen felbontása, ha kétértelmű, és az adatok pontos szövege.

Az adattípusok meghatározásához a következőket kell tennie:
* Az entitás létrehozása
* Az entitást jelölő, például hosszúságú kimondott szöveg lévő szöveg címkéje. Ezek a címkével ellátott példák megtanítják, hogy LUIS mit tartalmaz az entitás, és hol található a teljes leírás.

## <a name="entity-decomposability-is-important"></a>Az entitások lebontása fontos

Az entitások lebontása mind a szándék-előrejelzés, mind az entitással végzett kinyerés szempontjából fontos.

Kezdje egy gépi tanulási entitással, amely az első és legfelső szintű entitás az kinyeréshez. Ezután bontsa ki az entitást alentitásokra.

Habár előfordulhat, hogy nem tudja, milyen részletesen szeretné kipróbálni az alkalmazást az alkalmazás indításakor, az ajánlott eljárás a gépi tanulási entitások használata, majd az alentitások használata, amikor az alkalmazás kiforr.

Ebben az oktatóanyagban egy gépi tanulási entitást hoz létre, amely egy pizza-alkalmazás rendelését jelöli. Az entitás Kinyeri a sorrendtel kapcsolatos szöveget, kihúzza a méretet és a mennyiséget.

A `Please deliver one large cheese pizza to me` kinyerés a `one large cheese pizza` sorrend szerint történik, majd a `1` mennyiség és a méret kibontása is `large` .

## <a name="download-json-file-for-app"></a>Alkalmazáshoz tartozó JSON-fájl letöltése

Töltse le és mentse az [alkalmazás JSON-fájlját](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

## <a name="import-json-file-for-app"></a>JSON-fájl importálása az alkalmazáshoz

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>Gépi megtanult entitás létrehozása

A pizza-sorrend részleteinek kinyeréséhez hozzon létre egy legfelső szintű, gépi tanulási `Order` entitást.

1. A **leképezések** lapon válassza ki a **OrderPizza** szándékot.

1. A példa hosszúságú kimondott szöveg listán válassza ki a következő megjelölést.

    |Megrendelés – példa Kimondás|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Először válassza a (#1) bal szélső szövegét `pickup` , majd a jobb szélső szövegnél ugorjon `anchovies` (#2 – ezzel véget ér a címkézési folyamat). Megjelenik egy előugró menü. Az előugró ablakban adja meg az entitás nevét `Order` (#3). Ezután válassza ki `Order Create new entity` a listából (#4).

    ![A szöveg elején és végén lévő felirat a teljes sorrendhez](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Az entitások nem mindig a teljes Kimondás. Ebben a konkrét esetben `pickup` a rendelés fogadásának módját jelzi. Fogalmi szempontból a `pickup` megrendeléshez a címkével ellátott entitásnak kell tartoznia.

1. Az **entitás típusának kiválasztása** mezőben válassza a **struktúra hozzáadása** lehetőséget, majd válassza a **tovább**lehetőséget. Az alentitások, például a méret és a mennyiség hozzáadásához struktúra szükséges.

    ![Struktúra hozzáadása az entitáshoz](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Az **alentitások hozzáadása (nem kötelező)** mezőben válassza ki **+** a `Order` sort, majd adja hozzá a `Size` és `Quantity` az alentitások elemet, majd válassza a **Létrehozás**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Struktúra hozzáadása az entitáshoz](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>Alentitások szerkesztése a kinyerés javítása érdekében

Az előző lépések az entitást és alentitást hozza létre. A kivonás javításához vegyen fel szolgáltatásokat az alentitásokhoz.

### <a name="improve-size-extraction-with-phrase-list"></a>A méret kinyerésének javítása a kifejezések listájával

1. Válassza az **entitások** lehetőséget a bal oldali menüben, majd válassza a **megrendelés** entitás elemet.

1. A **séma és szolgáltatások** lapon válassza ki a **méret** alentitást, majd válassza a **+ szolgáltatás hozzáadása**elemet.

1. Válassza az **új kifejezés létrehozása** lehetőséget a legördülő menüből.

1. Az **új kifejezés létrehozása listában** adja meg a nevet, `SizePhraselist` majd adja meg a: `small` , és a értéket `medium` `large` . A **javaslatok** mező kitöltése után válassza a és a elemet `extra large` `xl` . Válassza a **Létrehozás** lehetőséget az új kifejezések listájának létrehozásához.

    Ez a kifejezés-lista funkció segít az `Size` alentitásnak a mérettel kapcsolatos szavak megkeresésében, ha például szavakat biztosít. A kifejezések listájának nem kell tartalmaznia minden méret szót, de tartalmaznia kell azokat a szavakat, amelyek várhatóan a méretet jelzik.

### <a name="add-sizelist-entity"></a>SizeList entitás hozzáadása

Ismert méretek listájának hozzáadása esetén az ügyfélalkalmazás felismeri a kinyerést is.

1. Válassza az **entitások** lehetőséget a bal oldali menüben, majd válassza a **+ Létrehozás**elemet.

1. Állítsa be az entitás nevét a (z) értékre, `SizeListentity` és állítsa be a típust a **listára** , hogy könnyen azonosítható legyen az `SizePhraselist` előző szakaszban létrehozotthoz képest.

1. Adja hozzá az ügyfélalkalmazás által várt méreteket: `Small` , `Medium` , `Large` , `XLarge` majd adja hozzá a szinonimákat. A szinonimáknak azokat a kifejezéseket kell megadniuk, amelyeknek a felhasználó belép a csevegési robotba. A rendszer kinyeri az entitást egy lista entitással, ha pontosan egyezik a normalizált értékkel vagy szinonimákkal.

    |Normalizált érték|Szinonimák|
    |--|--|
    |Kicsi|SM, SML, Tiny, legkisebb|
    |Közepes|MD, Mdm, normál, átlag, középső|
    |Nagy|LG, LRG, Big|
    |XLarge|XL, legnagyobb, óriás|


    > [!div class="mx-imgBorder"]
    > ![Struktúra hozzáadása az entitáshoz](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>SizeList-entitás funkciójának hozzáadása

1. Az entitások listájához való visszatéréshez válassza a bal oldali menü **entitások** elemét.

1. Válassza ki a **sorrendet** az entitások listájáról.

1. A **séma és szolgáltatások** lapon válassza ki a **méret** entitást, majd válassza a **+ szolgáltatás hozzáadása**elemet.

1. Válassza a **@ SizeListentity** elemet a legördülő listából.

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>Előre összeépített számú entitás hozzáadása

Egy előre összeépített számú entitás hozzáadása a kinyeréshez is segítséget nyújt.

1. Válassza az **entitások** lehetőséget a bal oldali menüben, majd válassza az **előre felépített entitás hozzáadása**elemet.

1. Válassza a **szám** lehetőséget a listából, majd válassza a **kész**lehetőséget.

1. Az entitások listájához való visszatéréshez válassza a bal oldali menü **entitások** elemét.

### <a name="add-feature-of-prebuilt-number-entity"></a>Előre összeépített számú entitás funkciójának hozzáadása

1. Válassza ki a **sorrendet** az entitások listájáról.

1. A **séma és szolgáltatások** lapon válassza ki a **mennyiség** entitást, majd válassza a **+ szolgáltatás hozzáadása**elemet.

1. Válassza ki a **@ Number** elemet a legördülő listából.

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>A szükséges szolgáltatások konfigurálása

Az entitás részletei lapon a **rendelés** entitásnál válassza a csillag, a `*` **@ SizeList** és a **@ Number** funkciót. A csillag a szolgáltatás nevével megegyező címkében jelenik meg.

> [!div class="mx-imgBorder"]
> ![Struktúra hozzáadása az entitáshoz](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>Címke – példa hosszúságú kimondott szöveg

A rendszer létrehozta a Machine megtanult entitást, és az alentitások rendelkeznek a szolgáltatásokkal. A kinyerési fejlesztés befejezéséhez a példát hosszúságú kimondott szöveg kell címkézni az alentitásokkal.

1. A bal oldali navigációs sávon válassza a **leképezések** lehetőséget, majd válassza ki a **OrderPizza** szándékot.

1. Az **entitások palettájának**megnyitásához válassza ki a **@** szimbólumot a környezetfüggő eszköztáron.

1. Válassza ki az egyes entitások sorát a palettán, majd a paletta kurzor használatával válassza ki az entitást az egyes példák kiválasztásában. Ha elkészült, az entitások listájának a következő képhez hasonlóan kell kinéznie.

    > [!div class="mx-imgBorder"]
    > ![A kötelező funkció konfigurálásának részleges képernyőképe](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>Az alkalmazás betanítása

Az alkalmazás betanításához válassza a **betanítás**lehetőséget. A képzés alkalmazza a módosításokat, például az új entitásokat és a címkével ellátott hosszúságú kimondott szöveg az aktív modellre.

## <a name="add-a-new-example-utterance"></a>Új példás Kimondás hozzáadása

1. A képzés után adjon hozzá egy új példát a szándékhoz, hogy megismerje, `OrderPizza` milyen jól ismeri el az Luis a gépi tanulási entitást.

    |Megrendelés – példa Kimondás|
    |--|
    |`I need a large pepperoni pizza`|

    Az általános felső entitás `Order` címkével van ellátva, és az `Size` alentitás is pontozott vonallal van megjelölve.

    > [!div class="mx-imgBorder"]
    > ![Részleges képernyőkép az új példáról az entitással előre jelezve](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    A pontozott vonal jelzi az előrejelzést az aktuálisan betanított alkalmazás alapján.

1. Ha módosítani szeretné az előrejelzést egy címkével ellátott entitásra, jelölje be a jelölőnégyzetet ugyanazon a sorban.

    > [!div class="mx-imgBorder"]
    > ![Részleges képernyőkép az új példáról az entitással előre jelezve](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    Ezen a ponton a gépi tanulási entitás működik, mert az új példán belül megtalálja az entitást. Ha például hosszúságú kimondott szöveg ad hozzá, ha az entitás nem megfelelően van előre jelezve, címkézze fel az entitást és az alentitásokat. Ha az entitás előre jelezve van, ügyeljen arra, hogy erősítse meg az előrejelzéseket.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Az alkalmazás betanítása az entitás módosításának alkalmazására

Válassza a **betanítás** lehetőséget az alkalmazás az új kifejezéssel való betanításához.

Ezen a ponton a sorrendnek van néhány részlete, amely kinyerhető (méret, mennyiség és teljes sorrendű szöveg). Az entitás további finomítása `Order` , például a pizza-fehéráru, a kéreg típusa és a Side Orders. Mindegyiket az entitás alentitásának kell létrehoznia `Order` .

## <a name="test-the-app-to-validate-the-changes"></a>Az alkalmazás tesztelése a módosítások ellenőrzéséhez

Tesztelje az alkalmazást az interaktív **teszt** panel használatával. Ezzel a folyamattal megadhat egy új kiírást, majd megtekintheti az előrejelzés eredményeit, és megtekintheti, hogy mennyire jól működik az aktív, betanított alkalmazás. A szándék előrejelzésének meglehetősen magabiztosnak kell lennie (60% fölött) a kinyerési entitásnak legalább az entitást kell felvennie `Order` . Előfordulhat, hogy a megrendelési entitás adatai hiányoznak, mert ezek a kevés hosszúságú kimondott szöveg nem elegendőek minden eset kezelésére.

1. A felső navigációs ablakban válassza a **Test** (Tesztelés) elemet.
1. Adja meg a teljes értéket `2 small cheese pizzas for pickup` , és válassza az ENTER billentyűt. Az aktív modell a megfelelő szándékot jelezte, több mint 60%-os megbízhatósággal.


1. Az entitások előrejelzésének megtekintéséhez kattintson a **vizsgálat** elemre.

    > [!div class="mx-imgBorder"]
    > ![Részleges képernyőkép az entitások előrejelzéséről az interaktív teszt panelen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Az alkalmazás közzététele a HTTP-végpontról való hozzáféréshez

Ahhoz, hogy LUIS-előrejelzéseket kaphasson egy csevegőrobotban vagy más ügyfélalkalmazásban, közzé kell tennie az alkalmazást a végponton.

1. A jobb felső navigációs sávon válassza a **Közzététel** lehetőséget.

    ![Képernyőkép a LUIS-közzététel a végpontra gomb a jobb felső menüben](./media/howto-publish/publish-button.png)

1. Válassza ki az **üzemi** tárolóhelyet, majd válassza a **beállítások módosítása**lehetőséget, válassza a **Hangulatelemzés**lehetőséget, majd kattintson a **kész**gombra.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a LUIS-közzétételi végpontról](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. Válassza ki az értesítésben a **végpont URL-címeire** mutató hivatkozást az **Azure-erőforrások** lapra való ugráshoz. A végpont URL-címei a **példa lekérdezésként**jelennek meg.

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Cél és entitások előrejelzése HTTP-végpontból

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Lépjen az URL-cím végére a címsorban, és cserélje le a _YOUR_QUERY_HEREt_ ugyanazzal a lekérdezéssel, mint amit az interaktív teszt panelen megadott.

    `2 small cheese pizzas for pickup`

    Az utolsó querystring paraméter a `query` teljes **lekérdezés**.

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
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
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
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

Ebben az oktatóanyagban az alkalmazás egy gépi tanulási entitás használatával keresi meg a felhasználó teljes részletességét, és Kinyeri a részleteket ebből a részletekből. A gépi tanulási entitás segítségével elvégezheti az entitás részleteit.

> [!div class="nextstepaction"]
> [Előre összeállított kulcskifejezés-entitás hozzáadása](luis-quickstart-intent-and-key-phrase.md)
