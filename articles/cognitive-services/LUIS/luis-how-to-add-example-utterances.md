---
title: Példa hozzáadása hosszúságú kimondott szöveg-LUIS
titleSuffix: Azure Cognitive Services
description: Például a hosszúságú kimondott szöveg a felhasználói kérdések és parancsok szöveges példái. Language Understanding (LUIS) tanításához hozzá kell adnia egy példa hosszúságú kimondott szöveg egy szándékhoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: ed6321963422cf17c858f43593092f8ffa4a1119
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467729"
---
# <a name="add-an-entity-to-example-utterances"></a>Entitás hozzáadása példa hosszúságú kimondott szöveg 

Például a hosszúságú kimondott szöveg a felhasználói kérdések és parancsok szöveges példái. Language Understanding (LUIS) tanításához hozzá kell adnia egy [példa hosszúságú kimondott szöveg](luis-concept-utterance.md) egy [szándékhoz](luis-concept-intent.md).


Általában egy megjelölést kell hozzáadnia egy elsődleges szándékhoz, majd létre kell hoznia az entitásokat és címkéket hosszúságú kimondott szöveg a **szándékok** lapon. Ha inkább először hozza létre az entitásokat, tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md)című témakört.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="marking-entities-in-example-utterances"></a>Entitások megjelölése például hosszúságú kimondott szöveg

Ha egy entitásnál a példában szereplő szöveg elemre kattint, megjelenik a helyi előugró menü. Ezzel a menüvel létrehozhat vagy kiválaszthat egy entitást. 

Bizonyos entitások típusai, például az előre összeépített entitások és a reguláris kifejezések entitásai nem címkézhető a példában szereplő kifejezéssel, mert azok automatikusan vannak címkézve. 

## <a name="add-a-simple-entity"></a>Egyszerű entitás hozzáadása

A következő eljárásban egy egyéni entitást hoz létre és címkéz a következő kifejezésen belül a **cél** lapon:

```text
Are there any SQL server jobs?
```

1. Az egyszerű entitásként való címkézéshez válassza a `SQL server` lehetőséget. A megjelenő entitás legördülő mezőben választhat egy meglévő entitást, vagy hozzáadhat egy új entitást. Új entitás hozzáadásához írja be a nevét `Job` a szövegmezőbe, majd válassza az **új entitás létrehozása**lehetőséget.

    ![Képernyőkép az entitás nevének beírásáról](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Ha entitásként címkével rendelkező szavakat választ:
    > * Egyetlen szó esetén egyszerűen jelölje ki. 
    > * Két vagy több szó megadásához válassza ki az első szót, majd a végső szót.

1. A **milyen típusú entitást kíván létrehozni?** előugró ablakban ellenőrizze az entitás nevét, és válassza ki az **egyszerű** entitás típusát, majd kattintson a **kész**gombra.

    A [kifejezések listáját](luis-concept-feature.md) általában egy egyszerű entitás jelzésének növelésére használják.

## <a name="add-a-list-entity"></a>Lista entitás hozzáadása

Az entitások felsorolása a rendszeren kapcsolódó szavak pontos szövegének megfelelő készletet jelöli. 

A vállalati részlegek listájához normalizált értékeket használhat: `Accounting` és `Human Resources`. Minden normalizált név szinonimákat tartalmaz. A részlegek esetében ezek a szinonimák bármilyen részlegbeli betűszót, számot vagy szlenget tartalmazhatnak. Az entitás létrehozásakor nem kell tudnia az összes értéket. A valós felhasználói hosszúságú kimondott szöveg szinonimákkal való áttekintése után további hozzáadást is hozzáadhat.

1. A **szándékok** lapon válassza ki a kívánt szót vagy kifejezést az új listában. Amikor az entitás legördülő menü jelenik meg, adja meg az új lista entitás nevét a felső szövegmezőben, majd válassza az **új entitás létrehozása**lehetőséget.   

1. A **milyen típusú entitást kíván létrehozni?** előugró ablakban nevezze el az entitást, és válassza a **lista** típusként lehetőséget. Adja hozzá a listaelem szinonimáit, majd válassza a **kész**elemet. 

    ![Képernyőfelvétel a lista entitás szinonimáinak beírásáról](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    További listaelemeket vagy több elem szinonimákat adhat hozzá más hosszúságú kimondott szöveg címkézésével, vagy az entitások a bal oldali navigációs **entitásokból** való szerkesztésével. Az entitások [szerkesztésével](luis-how-to-add-entities.md#add-list-entities) megadhatja a további elemek megfelelő szinonimákkal való megadását, vagy importálhat egy listát. 

## <a name="add-a-composite-entity"></a>Összetett entitás hozzáadása

Az összetett entitásokat a rendszer meglévő **entitásokból** hozza létre egy szülő entitás létrehozásához. 

Ha a kihagyás, `Does John Smith work in Seattle?`, egy összetett Kimondás visszatérhet az alkalmazott neve `John Smith`és a hely `Seattle` egy összetett entitásban. Az alárendelt entitásoknak már léteznie kell az alkalmazásban, és az összetett entitás létrehozása előtt meg kell adni a példa kiértékelését.

1. Az alárendelt entitások összetett entitásba való becsomagolásához válassza ki az **első** címkézett entitást (a bal szélsőt) az összetett entitás kiválasztásához. Megjelenik egy legördülő lista, amely megjeleníti a kijelölés lehetőségeit.

1. A legördülő listából válassza az **összetett entitás tördelése** elemet. 

1. Válassza ki az összetett entitás utolsó szavát (jobb szélső). Figyelje meg, hogy a zöld vonal az összetett entitást követi. Ez egy összetett entitás vizuális indikátora, és az összetett entitásban lévő, a bal szélső gyermek entitástól a jobb szélső entitáshoz tartozó összes szónak kell lennie.

1. Adja meg az összetett entitás nevét a legördülő listában.

    Az entitások megfelelő becsomagolásakor a teljes kifejezés alatt egy zöld vonal szerepel.

1. Ellenőrizze az összetett entitás részleteit, **hogy milyen típusú entitást szeretne létrehozni?** előugró ablakban válassza a **kész**lehetőséget.

    ![Az entitás részleteinek előugró ablak képernyőképe](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Az összetett entitás mind a kék, mind az egyes entitásokhoz, mind pedig a teljes összetett entitáshoz tartozó zöld aláhúzást jeleníti meg. 

    ![A szándékok részletei lap képernyőképe összetett entitással](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Entitás szerepkörének hozzáadása a Kimondás

A szerepkör egy entitás névvel ellátott altípusa, amelyet a teljes név kontextusa határoz meg. Entitásként megjelölheti az entitásokat, vagy kijelölhet egy szerepkört az entitáson belül. Minden entitás rendelkezhet olyan szerepkörökkel, mint például a gépi megismert egyéni entitások (egyszerű entitások és összetett entitások), nem gépi megtanultak (előre összeállított entitások, reguláris kifejezés entitások, listázási entitások). 

Megtudhatja [, hogyan jelölheti meg az entitás-szerepkörökkel](tutorial-entity-roles.md) való kiosztást egy gyakorlati oktatóanyagban. 

## <a name="entity-status-predictions"></a>Entitás állapotának előrejelzése

Ha új kiírást ad meg a LUIS-portálon, a Kimondás entitás-előrejelzési hibákkal rendelkezhet. Az előrejelzési hiba különbséget jelent az entitások címkézése és a LUIS által az entitás előrejelzése között. 

Ez a különbség vizuálisan ábrázolja a LUIS-portálon. A piros aláhúzás megjelenhet az entitások zárójelben vagy a zárójeleken kívül. 

![Képernyőkép az entitás állapotának előrejelzési eltéréséről](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Válassza ki a kiejtésben piros színnel aláhúzott szavakat. 

Az entitás mezőben piros felkiáltójel jelenik **meg, ha** a rendszer előrejelzési eltérést jelez. Ha meg szeretné tekinteni az entitás állapotát a címkézett és az előre jelzett entitások közötti különbséggel kapcsolatban, válassza az **entitás állapota** lehetőséget, majd válassza ki a jobb oldali elemet.

![Az entitás állapotának kiválasztására szolgáló képernyőkép](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

A piros vonal a következő időpontokban jelenhet meg:

   * Ha meg van adva egy kiírás, de az entitás címkéje előtt
   * Az entitás címkéjének alkalmazása
   * Az entitás feliratának eltávolításakor
   * Ha az adott szöveghez egynél több entitás felirata van előre jelezve 

A következő megoldások segítenek az entitás-előrejelzési eltérés feloldásában:

|Entitás|Vizuális kijelző|Jóslás|Megoldás|
|--|--|--|--|
|A Kimondás meg van adva, az entitás még nincs megjelölve.|piros aláhúzás|Az előrejelzés helyes.|Címkézze fel az entitást az előre jelzett értékkel.|
|Címke nélküli szöveg|piros aláhúzás|Helytelen előrejelzés|A helytelen entitást használó aktuális hosszúságú kimondott szöveg az összes leképezésen át kell tekinteni. A jelenlegi hosszúságú kimondott szöveg nem tanított LUIS, hogy ez a szöveg az előre jelzett entitás.
|Helyesen címkézett szöveg|kék entitás kiemelése, piros aláhúzás|Helytelen előrejelzés|A megfelelő címkével rendelkező entitások több különböző helyen és használatban hosszúságú kimondott szöveg. A jelenlegi hosszúságú kimondott szöveg nem elegendőek ahhoz, hogy megtanítsák LUIS számára, hogy ez az entitás, vagy hasonló entitások jelennek meg ugyanabban a kontextusban. A hasonló entitást egyetlen entitásba kell összevonni, így a LUIS nem tévesztendő össze. Egy másik megoldás egy kifejezési lista hozzáadása a szavak jelentőségének növeléséhez. |
|Helytelenül címkézett szöveg|kék entitás kiemelése, piros aláhúzás|Helyes előrejelzés| A megfelelő címkével rendelkező entitások több különböző helyen és használatban hosszúságú kimondott szöveg. 

## <a name="other-actions"></a>Egyéb műveletek

Műveleteket végezhet például hosszúságú kimondott szöveg kiválasztott csoportként vagy egyéni elemként. A kiválasztott példa hosszúságú kimondott szöveg módosíthatja a lista fölötti helyi menüt. Az egyes elemek a lista fölötti helyi menüt és az egyes kizáró sorok végén lévő egyéni környezetfüggő pontokat is használhatják. 

### <a name="remove-entity-labels-from-utterances"></a>Entitások címkéjének eltávolítása a hosszúságú kimondott szöveg

Eltávolíthatja a gépi megtanult entitások címkéit a szándékok oldaláról. Ha az entitás nem a gépi megtanulta, akkor nem távolítható el. Ha el kell távolítania egy nem géppel megismert entitást a kilépésből, törölnie kell az entitást a teljes alkalmazásból. 

Ha el szeretne távolítani egy géppel megtanult entitás címkéjét egy kifejezésből, válassza ki az entitást a teljes verzióban. Ezután válassza a **címke eltávolítása** elemet a megjelenő entitás legördülő listából.

### <a name="add-a-prebuilt-entity-label"></a>Előre összeépített entitás feliratának hozzáadása

Ha hozzáadja az előre elkészített entitásokat a LUIS-alkalmazáshoz, nem kell hosszúságú kimondott szöveg megcímkézni ezeket az entitásokat. Ha többet szeretne megtudni az előre elkészített entitásokról és azok hozzáadásáról, tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app)című témakört.

### <a name="add-a-regular-expression-entity-label"></a>Reguláris kifejezéssel rendelkező entitás feliratának hozzáadása

Ha a reguláris kifejezés entitásokat hozzáadja a LUIS-alkalmazáshoz, nem kell hosszúságú kimondott szöveg felcímkézni ezekkel az entitásokkal. Ha többet szeretne megtudni a reguláris kifejezések entitásokról és azok hozzáadásáról, tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts)című témakört.


### <a name="create-a-pattern-from-an-utterance"></a>Minta létrehozása egy teljes kifejezésből

Tekintse [meg a minta hozzáadása meglévő kifejezésből a szándék vagy az entitás oldalon](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Minta hozzáadása bármely entitáshoz

Ha hozzáadja a mintát. bármely entitást a LUIS-alkalmazáshoz, a hosszúságú kimondott szöveg nem címkézheti ezeket az entitásokat. Csak mintákban érvényesek. Ha többet szeretne megtudni a minta. bármely entitásról és azok hozzáadásáról, tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities)című témakört.

## <a name="train-your-app-after-changing-model-with-utterances"></a>Az alkalmazás betanítása a modell hosszúságú kimondott szöveg való módosítása után

A hosszúságú kimondott szöveg hozzáadása, szerkesztése vagy eltávolítása után [kitaníthatja](luis-how-to-train.md) és [közzéteheti](luis-how-to-publish-app.md) az alkalmazást, hogy a módosítások hatással legyenek a végponti lekérdezésekre. 

## <a name="next-steps"></a>További lépések

Miután megcímkézte a hosszúságú kimondott szöveg a **szándékában**, létrehozhat egy [összetett entitást](luis-how-to-add-entities.md).
