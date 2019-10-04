---
title: Példa hozzáadása hosszúságú kimondott szöveg-LUIS
titleSuffix: Azure Cognitive Services
description: Példa utterances Példák szöveges felhasználói kérdések vagy parancsok. Language Understanding (LUIS), akik az példa beszédmódok hozzáadása egy beszédszándék kell.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 1e170b86f573112cc5bc8dddd6f080921ef29d2d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932940"
---
# <a name="add-an-entity-to-example-utterances"></a>Entitás hozzáadása a példa kimondott szöveg 

Példa utterances Példák szöveges felhasználói kérdések vagy parancsok. Language Understanding (LUIS) elemre, hozzá kell [példa utterances](luis-concept-utterance.md) , egy [szándékot](luis-concept-intent.md).

Általában egy megjelölést kell hozzáadnia egy elsődleges szándékhoz, majd létre kell hoznia az entitásokat és címkéket hosszúságú kimondott szöveg a szándékok lapon. Ha az entitások nem vznikla először, tekintse meg [entitások hozzáadása](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Jelölés entitások példa kimondott szöveg

Amikor kiválaszt egy entitás megjelölheti például utterance (kifejezés) szöveg, egy helyszíni előugró menü jelenik meg. Ez a menü használatával hozzon létre vagy válasszon egy entitást. 

Bizonyos entitástípusok, előre összeállított entitások és a reguláris kifejezés entitások, például az a példában utterance (kifejezés) nem lehet címkézve, mivel a automatikusan címkézett. 

## <a name="add-a-simple-entity"></a>Egy egyszerű entitás hozzáadása

A következő eljárásban egy egyéni entitást hoz létre és címkéz a következő kifejezésen belül a **cél** lapon:

```text
Are there any SQL server jobs?
```

1. Válassza ki `SQL server` az utterance (kifejezés), a felirat szövegét egy egyszerű entitásként a. Az entitás legördülő mezőben, amely akkor jelenik meg válasszon egy meglévő entitásra, vagy adjon hozzá egy új entitást. Új entitás hozzáadásához írja be a nevét `Job` a szövegmezőbe, majd válassza az **új entitás létrehozása**lehetőséget.

    ![Képernyőkép az entitás nevének megadása](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Amikor kiválasztja a címke a keresett szavakat entitásokként:
    > * Egyetlen szó csak adja meg azt. 
    > * Két vagy több szó megadásához válassza ki az első szót, majd a végső szót.

1. Az a **milyen típusú entitást szeretne létrehozni?** egy előugró, ellenőrizze az entitás nevét, és válassza ki a **egyszerű** entitástípus, és válassza ki **kész**.

    A [kifejezéslista](luis-concept-feature.md) gyakran használatos a jel, egy egyszerű entitás növelése érdekében.

## <a name="add-a-list-entity"></a>Egy lista entitás hozzáadása

Az entitások felsorolása a rendszeren kapcsolódó szavak pontos szövegének megfelelő készletet jelöli. 

A vállalat részleg listát, akkor is rendelkezik normalized értékek: `Accounting` és `Human Resources`. Minden egyes normalizált név szinonimák rendelkezik. A részleg számára ezeket a szinonimákat bármelyik részleg betűszavakat, számok és szleng tartalmazhatnak. Nem kell tudnia értékek az entitás létrehozásakor. Hozzáadhat további valós felhasználói utterances a szinonimák áttekintése után.

1. A szándékok lapon válassza ki a kívánt szót vagy kifejezést az új listában. Amikor az entitás legördülő menü jelenik meg, adja meg az új lista entitás nevét a felső szövegmezőben, majd válassza az **új entitás létrehozása**lehetőséget.   

1. Az a **milyen típusú entitást szeretne létrehozni?** egy előugró, az entitás nevét, és válassza **lista** típusaként. Ez a listaelem szinonimák felvétele, majd válassza ki **kész**. 

    ![Képernyőkép a lista entitás szinonimák megadása](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Hozzáadhat további listaelemek vagy több elemet a szinonimák más utterances címkézéssel, vagy az a entitás szerkesztésével a **entitások** a bal oldali navigációs menüben. [Szerkesztési](luis-how-to-add-entities.md#add-list-entities) az entitásokat a további elemek a megfelelő szinonimák, vagy egy lista importálásával beírását lehetőségeket kínál. 

## <a name="add-a-composite-entity"></a>Összetett entitás hozzáadása

Az összetett entitásokat a rendszer meglévő entitásokból hozza létre egy szülő entitás létrehozásához. 

A Kimondás `Does John Smith work in Seattle?`feltételezi, hogy az összetett Kimondás visszaadja az alkalmazott nevét `John Smith`, valamint egy összetett `Seattle` entitás helyét. Az alárendelt entitásoknak már léteznie kell az alkalmazásban, és az összetett entitás létrehozása előtt meg kell adni a példa kiértékelését.

1. Az alárendelt entitások összetett entitásba való becsomagolásához válassza ki az **első** címkézett entitást (a bal szélsőt) az összetett entitás kiválasztásához. Megjelenik egy legördülő lista, amely megjeleníti a kijelölés lehetőségeit.

1. A legördülő listából válassza az **összetett entitás tördelése** elemet. 

1. Válassza ki az utolsó szót az összetett entitás (jobb szélső). Figyelje meg, egy zöld vonal követi az összetett entitást. Ez egy összetett entitás vizuális indikátora, és az összetett entitásban lévő, a bal szélső gyermek entitástól a jobb szélső entitáshoz tartozó összes szónak kell lennie.

1. Adja meg az összetett entitás nevét a legördülő listában.

    Az entitások megfelelően be zöld vonal esetén a teljes kifejezés alapján.

1. Az összetett entitás adatai ellenőrzése a a **milyen típusú entitást szeretne létrehozni?** előugró üzenet mezőbe, majd válassza ki **kész**.

    ![Képernyőkép az entitásadatokat előugró](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Az összetett entitás mindkét kék emeli ki, az egyéni entitások és a egy zöld aláhúzás a teljes összetett entitás jeleníti meg. 

    ![Képernyőkép a leképezések Részletek lap, az összetett entitás](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Entitás szerepkörének hozzáadása a Kimondás

A szerepkör egy entitás névvel ellátott altípusa, amelyet a teljes név kontextusa határoz meg. Entitásként megjelölheti az entitásokat, vagy kijelölhet egy szerepkört az entitáson belül. Minden entitás rendelkezhet olyan szerepkörökkel, mint például a gépi megismert egyéni entitások (egyszerű entitások és összetett entitások), nem gépi megtanultak (előre összeállított entitások, reguláris kifejezés entitások, listázási entitások). 

Megtudhatja [, hogyan jelölheti meg az entitás](tutorial-entity-roles.md) -szerepkörökkel való kiosztást egy gyakorlati oktatóanyagban. 

## <a name="entity-status-predictions"></a>Entitás állapota előrejelzések

Amikor egy új utterance (kifejezés) a LUIS-portálon, az utterance (kifejezés) lehetséges, hogy entitás előrejelzési hibák léptek fel. Az előrejelzési hiba képest hogyan előre jelzett LUIS rendelkezik az entitás egy entitás feliratú hogyan eltérően működik. 

Ez a különbség egy piros vonallal az utterance (kifejezés) a vizuálisan jelölt a LUIS-portálon. A piros aláhúzás entitás zárójelek között, vagy kívül zárójelek jelenhet meg. 

![Képernyőkép az entitás állapota előrejelzés eltérés](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Válassza ki az aláhúzott szavakra vörös színnel az utterance (kifejezés). 

Az entitás mezőben jelenik meg a **entitás állapota** egy piros felkiáltójel, ha az előrejelzés eltérés van az. Az entitás állapot címkézett és előrejelzett entitások közötti különbségekkel kapcsolatos információk megtekintéséhez válasszon **entitás állapota** válassza ki az elemet a jobb oldalon.

![Az entitás állapotának kiválasztására szolgáló képernyőkép](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

A piros vonal bármelyik a következő alkalommal jelenhetnek meg:

   * Ha az utterance (kifejezés) is meg kell adni, még mielőtt az entitás címkéje
   * Ha az entitás alkalmazott
   * Ha az entitás címke eltávolítása
   * Ha egynél több entitás címke a szöveg előre jelzett 

A következő megoldásokkal oldhatja meg az entitás előrejelzés eltérés:

|Entitás|Láthatja|Előrejelzés|Megoldás|
|--|--|--|--|
|A megadott utterance, entitás nem címkézett még.|piros aláhúzás|Előrejelzési helyességéről.|Az előre jelzett érték rendelkező entitás címkét.|
|Címke nélküli szöveg|piros aláhúzás|Helytelen előrejelzése|Az helytelen entitást használó aktuális megcímkézzen kell vizsgálni a összes leképezések között. Az aktuális utterances mistaught rendelkezik LUIS, hogy ez a szöveg-e az előre jelzett entitás.
|Megfelelően címkével ellátott szöveg|kék entitás kiemelése, piros aláhúzás|Helytelen előrejelzése|Adja meg a különböző helyeken a tanúsítványalgoritmusok és használatuk megfelelően címkézett entitással további kimondott szöveg. Az aktuális kimondott szöveg, hogy ez a LUIS, akik nem elegendő az entitás vagy hasonló entitások jelennek meg, ugyanabban a környezetben. Hasonló entitás kell kombinálni egyetlen entitást, LUIS nem összetéveszteni őket. Egy másik megoldás, hogy a szavakat vételének növelése érdekében kifejezés lista hozzáadása. |
|Helytelenül címkével ellátott szöveg|kék entitás kiemelése, piros aláhúzás|Megfelelő előrejelzése| Adja meg a különböző helyeken a tanúsítványalgoritmusok és használatuk megfelelően címkézett entitással további kimondott szöveg. 

## <a name="other-actions"></a>Egyéb műveletek

Elvégezhető műveletek a példa kimondott szöveg, a kiválasztott csoporthoz vagy egy egyéni elemet. Kiválasztott példa utterances csoportjait módosítsa a helyi menü az eszközlista feletti. Egyetlen elem mindkét környezetfüggő feletti menüben a listában, és egyes környezetfüggő három felhasználhatja az egyes utterance (kifejezés) sor végén található. 

### <a name="remove-entity-labels-from-utterances"></a>Beszédmódok entitás címkék eltávolítása

Eltávolíthatja a gépi megtanult entitások címkéit a szándékok oldaláról. Ha a gép megismerte az entitás nem áll, akkor nem lehet eltávolítani az utterance (kifejezés). Ha egy nem gép megismert entitások eltávolítása az utterance (kifejezés) van szüksége, kell törölni az entitást a teljes alkalmazásban. 

Egy entitás gép megtanult címke eltávolítása az utterance (kifejezés), válassza ki az entitást az utterance (kifejezés). Válassza ki **címke eltávolítása** az entitás legördülő mezőben, amely akkor jelenik meg.

### <a name="add-a-prebuilt-entity-label"></a>Előre összeépített entitás feliratának hozzáadása

Ha az előre összeállított entitások ad hozzá a LUIS-alkalmazás, nem kell címke utterances az entitásokhoz. Adja hozzá őket, illetve előre összeállított entitások kapcsolatos további tudnivalókért lásd: [entitások hozzáadása](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-a-regular-expression-entity-label"></a>Reguláris kifejezéssel rendelkező entitás feliratának hozzáadása

Ha a reguláris kifejezés entitásokat ad hozzá a LUIS-alkalmazás, nem kell címke utterances az entitásokhoz. Reguláris kifejezés entitásokat, és hogyan adja hozzá őket kapcsolatos további információkért lásd: [entitások hozzáadása](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Hozzon létre egy minta az utterance (kifejezés)

Lásd: [Hozzáadás minta a leképezés vagy entitás oldalon meglévő utterance (kifejezés)](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Minta hozzáadása bármely entitáshoz

Pattern.any entitásokat ad hozzá a LUIS-alkalmazás, ha meg nem címkét az entitásokhoz kimondott szöveg. Csak azok a minták érvényes. Adja hozzá őket, illetve pattern.any entitások kapcsolatos további tudnivalókért lásd: [entitások hozzáadása](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Beszédmódok modell módosítása után az alkalmazás betanítása

Miután hozzáadása, szerkesztése vagy eltávolítása a kimondott szöveg, [betanításához](luis-how-to-train.md) és [közzététele](luis-how-to-publish-app.md) a végpont lekérdezéseket érintő módosítások az alkalmazás. 

## <a name="next-steps"></a>További lépések

Miután megcímkézte a hosszúságú kimondott szövega szándékában, létrehozhat egy [összetett entitást](luis-how-to-add-entities.md).
