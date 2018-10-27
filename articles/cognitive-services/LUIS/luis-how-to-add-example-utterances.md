---
title: A LUIS-alkalmazások például beszédmódok hozzáadása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, a Language Understanding (LUIS) alkalmazások hozzáadása a kimondott szöveg.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/24/2018
ms.author: diberry
ms.openlocfilehash: caf8628569481149ee41e8253f4759e15e49150f
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50139123"
---
# <a name="add-an-entity-to-example-utterances"></a>Entitás hozzáadása a példa kimondott szöveg 

Példa utterances Példák szöveges felhasználói kérdések vagy parancsok. Language Understanding (LUIS) elemre, hozzá kell [példa utterances](luis-concept-utterance.md) , egy [szándékot](luis-concept-intent.md).

Általában először egy beszédszándék adjon hozzá egy példa utterance (kifejezés), és entitásokat és a felirat utterances hozzon létre a szándék oldalon. Ha az entitások nem vznikla először, tekintse meg [entitások hozzáadása](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Jelölés entitások példa kimondott szöveg

Amikor kiválaszt egy entitás megjelölheti például utterance (kifejezés) szöveg, egy helyszíni előugró menü jelenik meg. Ez a menü használatával hozzon létre vagy válasszon egy entitást. 

Bizonyos entitástípusok, előre összeállított entitások és a reguláris kifejezés entitások, például az a példában utterance (kifejezés) nem lehet címkézve, mivel a automatikusan címkézett. 

## <a name="add-a-simple-entity"></a>Egy egyszerű entitás hozzáadása

A következő eljárással hozzon létre, és egy egyéni entitást belül a következő utterance (kifejezés) címke a szándék oldalon:

```text
Does John Smith work in Seattle?
```

1. Válassza ki `Seattle` az utterance (kifejezés), a felirat szövegét egy egyszerű entitásként a.

    [![Képernyőkép az utterance (kifejezés) egyszerű entitás szöveg kijelölése](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)](./media/luis-how-to-add-example-utterances/hr-create-simple-1.png)

    > [!NOTE]
    > Amikor kiválasztja a címke a keresett szavakat entitásokként:
    > * Egyetlen szó csak adja meg azt. 
    > * Két vagy több szóból készletének válassza ki a elején és végén található a készletet.

1. Az entitás legördülő mezőben, amely akkor jelenik meg válasszon egy meglévő entitásra, vagy adjon hozzá egy új entitást. Új entitások hozzáadásához írja be annak nevét a szövegmezőbe, és válassza **új entitás létrehozása**. 

    ![Képernyőkép az entitás nevének megadása](./media/luis-how-to-add-example-utterances/hr-create-simple-2.png)

1. Az a **milyen típusú entitást szeretne létrehozni?** egy előugró, ellenőrizze az entitás nevét, és válassza ki a **egyszerű** entitástípus, és válassza ki **kész**.

    A [kifejezéslista](luis-concept-feature.md) gyakran használatos a jel, egy egyszerű entitás növelése érdekében.

## <a name="add-a-list-entity"></a>Egy lista entitás hozzáadása

Lista entitások rögzített, lezárt csoportját képviselik (pontos egyezés megegyezik) kapcsolódó szavakat a rendszerben. 

A vállalat részleg listát, akkor is rendelkezik normalized értékek: `Accounting` és `Human Resources`. Minden egyes normalizált név szinonimák rendelkezik. A részleg számára ezeket a szinonimákat bármelyik részleg betűszavakat, számok és szleng tartalmazhatnak. Nem kell tudnia értékek az entitás létrehozásakor. Hozzáadhat további valós felhasználói utterances a szinonimák áttekintése után.

1. A példa utterance (kifejezés) egy adott utterance (kifejezés), a listában válassza ki a szó vagy kifejezés, amelyet szeretne az új lista. A felső szövegmezőben adja meg a lista nevét, majd válassza ki **új entitás létrehozása**.   

    ![Képernyőkép az entitás listanév megadása](./media/luis-how-to-add-example-utterances/hr-create-list-1.png)


1. Az a **milyen típusú entitást szeretne létrehozni?** egy előugró, az entitás nevét, és válassza **lista** típusaként. Ez a listaelem szinonimák felvétele, majd válassza ki **kész**. 

    ![Képernyőkép a lista entitás szinonimák megadása](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Hozzáadhat további listaelemek vagy több elemet a szinonimák más utterances címkézéssel, vagy az a entitás szerkesztésével a **entitások** a bal oldali navigációs menüben. [Szerkesztési](luis-how-to-add-entities.md#add-list-entities) az entitásokat a további elemek a megfelelő szinonimák, vagy egy lista importálásával beírását lehetőségeket kínál. 

## <a name="add-composite-entity"></a>Összetett entitás hozzáadása

Összetett entitások jönnek létre a meglévő **entitások** egy fölérendelt entitásba. 

Az utterance (kifejezés), feltéve, hogy `Does John Smith work in Seattle?`, egy összetett utterance (kifejezés) adhat vissza az alkalmazott nevét és helyét az Entitásadatok egyetlen szülőhöz objektumban. 

Az alkalmazott nevét, a John Smith, egy előre elkészített [personName](luis-reference-prebuilt-person.md) entitás. A hely, Seattle, egy egyéni egyszerű entitás. Miután ezen a két entitás létrehozása és a egy példa utterance (kifejezés) címkével ellátott, ezeket az entitásokat is zárójelezhető összetett entitásokban. 

1. Wrap az egyes entitások összetett be, válassza a **első** entitás (bal szélső) az utterance (kifejezés), az összetett entitás a címkével. Egy legördülő lista megjelenik ez a beállítás az egyik lehetőséget.

1. Válassza ki **összetett entitást burkolása** a legördülő listából. 

    ![Képernyőkép az "Újraindulás összetett entitást a" kiválasztása](./media/luis-how-to-add-example-utterances/hr-create-composite-1.png)

1. Válassza ki az utolsó szót az összetett entitás (jobb szélső). Figyelje meg, egy zöld vonal követi az összetett entitást.

1. Adja meg az összetett entitás nevét a legördülő listában.

    ![Képernyőkép az entitás nevének megadása](./media/luis-how-to-add-example-utterances/hr-create-composite-2.png)

    Az entitások megfelelően be zöld vonal esetén a teljes kifejezés alapján.

1. Az összetett entitás adatai ellenőrzése a a **milyen típusú entitást szeretne létrehozni?** előugró üzenet mezőbe, majd válassza ki **kész**.

    ![Képernyőkép az entitásadatokat előugró](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. Az összetett entitás mindkét kék emeli ki, az egyéni entitások és a egy zöld aláhúzás a teljes összetett entitás jeleníti meg. 

    ![Képernyőkép a leképezések Részletek lap, az összetett entitás](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-hierarchical-entity"></a>Hierarchikus entitás hozzáadása

Egy hierarchikus entitás, de megismert és elméletben kapcsolódó entitások egy kategóriát. A következő példában az entitás tartalmazza a forrás-és célhelynek. 

Az utterance (kifejezés) a `Move John Smith from Seattle to Cairo`, Seattle a feladás helyét pedig Cairo a célhelyen. Minden egyes helye kontextusban különböző és megismert szórendjét és a word választott az utterance (kifejezés).

1. Az utterance (kifejezés), a szándék oldalán válassza `Seattle`, írja be az entitás nevét `Location`, majd válassza ki az Enter billentyűt.

    ![Képernyőkép az létrehozása hierarchikus entitás címkézés párbeszédpanel](./media/luis-how-to-add-example-utterances/hr-hier-1.png)

1. Az a **milyen típusú entitást szeretne létrehozni?** előugró jelölje ki _hierarchikus_ a **entitástípus**, majd adja hozzá `Origin` és `Destination` gyermekként, majd **kész**.

    ![Képernyőkép a leképezések részletei oldala, amelyen kiemelve ToLocation entitás](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

1. A word, az utterance (kifejezés) a következő címkét kapta a szülő hierarchikus entitással. A word hozzárendelése egy gyermek entitásnak kell. Térjen vissza az utterance (kifejezés) a szándék részletek lapon. Válassza ki a word-, majd a legördülő listából válassza ki a létrehozott entitás nevét, és hajtsa végre a menüben válassza ki a megfelelő gyermek entitásnak jobb.

    ![Képernyőkép a leképezések részletei oldala, amelyen kiemelve ToLocation entitás](./media/luis-how-to-add-example-utterances/hr-hier-3.png)

    >[!CAUTION]
    >Gyermek entitásnévnek egyedinek kell lennie minden entitás egyetlen alkalmazásban. Két különböző hierarchikus entitások nem tartalmazhat gyermekentitások ugyanazzal a névvel. 

## <a name="entity-status-predictions"></a>Entitás állapota előrejelzések

Amikor egy új utterance (kifejezés) a LUIS-portálon, az utterance (kifejezés) lehetséges, hogy entitás előrejelzési hibák léptek fel. Az előrejelzési hiba képest hogyan előre jelzett LUIS rendelkezik az entitás egy entitás feliratú hogyan eltérően működik. 

Ez a különbség egy piros vonallal az utterance (kifejezés) a vizuálisan jelölt a LUIS-portálon. A piros aláhúzás entitás zárójelek között, vagy kívül zárójelek jelenhet meg. 

![Képernyőkép az entitás állapota előrejelzés eltérés](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Válassza ki az aláhúzott szavakra vörös színnel az utterance (kifejezés). 

Az entitás mezőben jelenik meg a **entitás állapota** egy piros felkiáltójel, ha az előrejelzés eltérés van az. Az entitás állapot címkézett és előrejelzett entitások közötti különbségekkel kapcsolatos információk megtekintéséhez válasszon **entitás állapota** válassza ki az elemet a jobb oldalon.

![Képernyőkép az entitás állapota előrejelzés eltérés](./media/luis-how-to-add-example-utterances/entity-status.png)

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

Gép megismert entitások címkék eltávolíthatók az utterance (kifejezés), a szándék oldalon. Ha a gép megismerte az entitás nem áll, akkor nem lehet eltávolítani az utterance (kifejezés). Ha egy nem gép megismert entitások eltávolítása az utterance (kifejezés) van szüksége, kell törölni az entitást a teljes alkalmazásban. 

Egy entitás gép megtanult címke eltávolítása az utterance (kifejezés), válassza ki az entitást az utterance (kifejezés). Válassza ki **címke eltávolítása** az entitás legördülő mezőben, amely akkor jelenik meg.

![Képernyőkép a leképezések Részletek lap, távolítsa el a kiemelt címkével ellátott](./media/luis-how-to-add-example-utterances/remove-label.png) 

### <a name="add-prebuilt-entity-label"></a>Előre összeállított entitások címke hozzáadása

Ha az előre összeállított entitások ad hozzá a LUIS-alkalmazás, nem kell címke utterances az entitásokhoz. Adja hozzá őket, illetve előre összeállított entitások kapcsolatos további tudnivalókért lásd: [entitások hozzáadása](luis-how-to-add-entities.md#add-prebuilt-entity).

### <a name="add-regular-expression-entity-label"></a>Reguláris kifejezés entitás címke hozzáadása

Ha a reguláris kifejezés entitásokat ad hozzá a LUIS-alkalmazás, nem kell címke utterances az entitásokhoz. Reguláris kifejezés entitásokat, és hogyan adja hozzá őket kapcsolatos további információkért lásd: [entitások hozzáadása](luis-how-to-add-entities.md#add-regular-expression-entities).


### <a name="create-a-pattern-from-an-utterance"></a>Hozzon létre egy minta az utterance (kifejezés)

Lásd: [Hozzáadás minta a leképezés vagy entitás oldalon meglévő utterance (kifejezés)](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-patternany-entity"></a>Pattern.any entitás hozzáadása

Pattern.any entitásokat ad hozzá a LUIS-alkalmazás, ha meg nem címkét az entitásokhoz kimondott szöveg. Csak azok a minták érvényes. Adja hozzá őket, illetve pattern.any entitások kapcsolatos további tudnivalókért lásd: [entitások hozzáadása](luis-how-to-add-entities.md#add-patternany-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Beszédmódok modell módosítása után az alkalmazás betanítása

Miután hozzáadása, szerkesztése vagy eltávolítása a kimondott szöveg, [betanításához](luis-how-to-train.md) és [közzététele](luis-how-to-publish-app.md) a végpont lekérdezéseket érintő módosítások az alkalmazás. 

## <a name="next-steps"></a>További lépések

Beszédmódok a szándék fog vonatkozni a címkézés, után most már létrehozhat egy [összetett entitást](luis-how-to-add-entities.md).
