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
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 387668263a6bab6e12a21adf04aebfbbf108a006
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036490"
---
# <a name="add-example-utterances-and-label-with-entities"></a>Példa kimondott szöveg és az entitások címke hozzáadása

Példa utterances Példák szöveges felhasználói kérdések vagy parancsok. Language Understanding (LUIS) elemre, hozzá kell [példa utterances](luis-concept-utterance.md) , egy [szándékot](luis-concept-intent.md).

Általában ad hozzá egy példa utterance (kifejezés) megjelölésű először, és a entitásokat és a felirat utterances hozzon létre a szándék oldalon. Ha az entitások nem vznikla először, tekintse meg [entitások hozzáadása](luis-how-to-add-entities.md).

## <a name="add-an-utterance"></a>Adja hozzá az utterance (kifejezés)
Egy leképezési lapon adja meg egy megfelelő példa utterance (kifejezés) adatraktáraktól a felhasználók számára, mint például `book 2 adult business tickets to Paris tomorrow on Air France` alatti leképezési nevét, és nyomja le az ENTER billentyűt a szövegmezőben. 
 
>[!NOTE]
>A LUIS összes utterances kisbetűvé alakítja.

![Képernyőkép a leképezések Részletek lapján az utterance (kifejezés) kiemelésével](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

Beszédmódok kerülnek a kimondott szöveg listája a jelenlegi leképezés. 

## <a name="ignoring-words-and-punctuation"></a>Szavak és írásjelek figyelmen kívül hagyása
Ha azt szeretné, figyelmen kívül hagyja a szó vagy az a példában utterance (kifejezés) absztrakt, egy [minta](luis-concept-patterns.md#pattern-syntax) az a _figyelmen kívül hagyása_ szintaxist. 

## <a name="add-simple-entity-label"></a>Egyszerű entitás címke hozzáadása
A következő eljárással hozzon létre, és egyéni entitásokat a következő utterance (kifejezés) a szándék oldalon belüli címkét:

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. Válassza ki az utterance (kifejezés), a felirat szövegét egy egyszerű entitásként "Vezeték nélkül Franciaország".

    > [!NOTE]
    > Címkézze entitásokként szavak kiválasztásakor:
    > * Egyetlen szó csak adja meg azt. 
    > * Két vagy több szóból készletének válassza ki a elején és végén található a készletet.

2. Az entitás legördülő mezőben, amely akkor jelenik meg válasszon egy meglévő entitásra, vagy adjon hozzá egy új entitást. Új entitások hozzáadásához írja be annak nevét a szövegmezőbe, és válassza **új entitás létrehozása**. 
 
    ![Képernyőkép a leképezések Részletek lap, az egyszerű entitás címkézés opció kiemelésével](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. Az a **milyen típusú entitást szeretne létrehozni?** előugró párbeszédpanelen ellenőrizze az entitás nevét, és válassza ki az egyszerű entitás típusát, és válassza **kész**.

    ![Megerősítési párbeszédpanel képe](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md#simple-entity-data) tudhat meg többet az egyszerű entitás kinyerését a végpont JSON lekérdezési válasz. Próbálja ki az egyszerű entitás [rövid](luis-quickstart-primary-and-secondary-data.md) tudhat meg többet az egyszerű entitás használatával.


## <a name="add-list-entity-and-label"></a>Lista entitás- és címke hozzáadása
Lista entitások rögzített, lezárt csoportját képviselik (pontos egyezés megegyezik) kapcsolódó szavakat a rendszerben. 

Egy ital lista entitás két normalizált érték szerepelhet: víz és soda a jelenléti pontra irányíthatja. Minden egyes normalizált név szinonimák rendelkezik. A vízzel, a szinonimák használata H20, gáz, az egyszerű. Soda csatlakozási pontot a szinonimák használata a gyümölcs, cola, gyömbér. Nem kell tudnia értékek az entitás létrehozásakor. Hozzáadhat további valós felhasználói utterances a szinonimák áttekintése után.

|Normalizált neve|Szinonimák|
|--|--|
|Víz|H20, gáz, átalánydíjjal|
|Soda pop|Gyümölcs, cola, gyömbér|

A leképezési oldaláról lista új entitások létrehozásakor végez két dolgokat, amelyek nem maguktól értetődőek. Először létrehoz egy új lista az első listaelemhez hozzáadásával. Másodszor az első listaelemhez nevű szó vagy kifejezés, szerezte be az utterance (kifejezés). Ezeket később az entitás lapon módosíthatja, bár lehet gyorsabb jelölje be az utterance (kifejezés) feliratú, amelyeket szeretne a listaelem nevét.

Például akkor, ha szeretne, hozzon létre egy típusú, és ital választott szó `h2o` az utterance (kifejezés) létrehozni az entitást, az a lista egy elem, amelynek a neve lett h20 lenne. Ha egy több általános nevet, válassza az utterance (kifejezés), amelyek általánosabb érvényűvé nevet használja. 

1. A word, az első elem a listában válassza az utterance (kifejezés), majd be a szövegmezőbe írja be a lista neve, majd válassza ki **új entitás létrehozása**.   

    ![Képernyőkép a leképezések részletek oldala, amelyen kiemelve új entitás létrehozása](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. Az a **milyen típusú entitást szeretne létrehozni?** párbeszédpanelen adjon hozzá a listaelem szinonimák. A vízjelek cikkhez ital listában, adja hozzá a `h20`, `perrier`, és `waters`, és válassza ki **kész**. Figyelje meg, hogy a "víz" kerül-e, mivel a lista szinonimák megfeleltetődnek a token szintjén. Az angol kulturális környezetben, hogy a szint a word szintjén van így "vizeken" lenne sem feleltethető "water", ha a listában volt. 

    ![Képernyőkép a milyen típusú entitás tegye meg szeretné létrehozása párbeszédpanel](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    Ezen a listán szereplő ital csak egy ital típusa, víz rendelkezik. Más utterances címkézéssel, vagy az a entitás szerkesztésével hozzáadhat több ital típust a **entitások** a bal oldali navigációs menüben. [Szerkesztési](luis-how-to-add-entities.md#add-list-entities) az entitásokat a további elemek a megfelelő szinonimák beírását lehetőséget kínál fel vagy [importálása](luis-how-to-add-entities.md#import-list-entity-values) listáját. 

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md#list-entity-data) tudhat meg többet a végpontról JSON lekérdezési válasz lista entitások kinyeréséhez. Próbálja ki a [rövid](luis-quickstart-intent-and-list-entity.md) egy lista entitás használatával kapcsolatos további.

## <a name="add-synonyms-to-the-list-entity"></a>Szinonimák felvétele a lista entitáshoz 
A lista entitás kiválasztásával a szót vagy kifejezést az utterance (kifejezés) szinonima hozzá. Ha olyan entitás listában, és hozzá szeretné adni, amelyet `agua` víz szinonimájaként, kövesse a lépéseket:

Az utterance (kifejezés), válassza a azonos szót, például `aqua` vízzel, majd válassza ki a lista entitás nevét a legördülő listából válassza ki, például **ital**, majd **szinonimát állítja**, majd válassza ki a listát elem azonos, a, mint például **víz**.

![Képernyőkép a leképezések részletek oldala, amelyen hozzon létre egy új szinonimát kiemelésével](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>Új elem a lista entitás létrehozása
Hozzon létre egy új elem egy meglévő lista entitás kiválasztásával a szót vagy kifejezést az utterance (kifejezés). Ha a listában, és hozzá szeretné adni ital `tea` új elemként, kövesse a lépéseket:

Az utterance (kifejezés), válassza az új listaelem a word például `tea`, majd válassza ki a lista entitás nevét a legördülő listában, például **ital**, majd **hozzon létre egy új szinonimát**. 

![Képernyőkép az új listaelem hozzáadása](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

A szó kék most kiemelve. Ha a kurzort a word, a címke a lista elem nevét, például tea tartalmazó jeleníti meg.

![Új lista-elem címke képernyőképe](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>Zabalit do entitások összetett felirat
Entitások összetett lekérdezés alapján hoztak létre **entitások**. Nem hozható létre egy összetett entitást a szándék oldaláról. Az összetett entitás létrehozása után, wrap funkciót az entitásokat az utterance (kifejezés), a szándék oldalon. 

Az utterance (kifejezés), feltéve, hogy `book 2 tickets from Seattle to Cairo`, egy összetett utterance (kifejezés) adhat vissza az Entitásadatok jegyek (2), a (Budapesten) forrás és cél száma (Cairo) helyek egyetlen szülőhöz entitásokban. 

Kövesse az alábbi [lépéseket](luis-how-to-add-entities.md#add-prebuilt-entity) hozzáadása a **szám** előre összeállított entitások. Az entitás létrehozását követően a `2` az utterance (kifejezés) a rendszer kék, jelezve, hogy ez egy címkével rendelkező entitást. Előre összeállított entitások LUIS vannak ellátva. Nem adhat hozzá, és távolítsa el az előre összeállított entitások címkét egy egyetlen utterance (kifejezés). Csak adja hozzá, vagy távolítsa el az előre összeállított címkék hozzáadásával vagy eltávolításával az előre összeállított entitások az alkalmazásból.

Kövesse az alábbi [lépéseket](#add-hierarchical-entity-and-label) hozhat létre egy **hely** hierarchikus entitás. A forrás és cél helyeken, és a példa utterance (kifejezés) címkét. 

Az entitások összetett entitásokban, burkolása, előtt győződjön meg arról, a gyermekentitások ki vannak emelve az összes kék színnel, ami azt jelenti, az az utterance (kifejezés) címkével.

1. Burkolása az egyes entitások összetett be, válassza ki az első címkézett entitás az utterance (kifejezés), az összetett entitás. A példa utterance (kifejezés), a `book 2 tickets from Seattle to Cairo`, az első entitás az a szám 2. Egy legördülő lista megjelenik ez a beállítás az egyik lehetőséget.

    ![Képernyőfelvétel a kiválasztott száma és a legördülő lehetőségeket vannak kiemelve](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. Válassza ki **összetett entitást burkolása** a legördülő listából. 

    ![Képernyőkép a alkalmazásburkoló összetett entitást az újraindulás összetett entitásban kiemelt legördülő lehetőségei](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. Válassza ki az összetett entitás az utolsó szót. Az ebben a példában az utterance válassza a "Location::Destination" (Cairo képviselő). A zöld sor van most alatt a szavakat, beleértve nem entitás szavak, az utterance (kifejezés), amelyek az összetett.

    ![BookFlight szándékot képernyőkép oldala, amelyen kiemelve száma](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. A legördülő listából válassza ki az összetett entitás nevét. Ebben a példában ez **TicketOrder**.

    ![Képernyőkép a alkalmazásburkoló szavak összetett entitással, a legördülő listából válassza ki a kiemelt összetett entitás neve](./media/luis-how-to-add-example-utterances/wrap-4.png)

    Az entitások megfelelően be zöld vonal esetén a teljes kifejezés alapján.

    ![Képernyőkép az utterance (kifejezés), az összetett entitás kiemelésével](./media/luis-how-to-add-example-utterances/wrap-5.png)

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md#composite-entity-data) tudhat meg többet az összetett entitás kinyerését a végpont JSON lekérdezési válasz. Próbálja ki az összetett entitás [oktatóanyag](luis-tutorial-composite-entity.md) egy összetett entitást használatával kapcsolatos további.

## <a name="add-hierarchical-entity-and-label"></a>A hierarchikus és címke hozzáadása
Egy hierarchikus entitás, de megismert és elméletben kapcsolódó entitások egy kategóriát. A következő példában az entitás tartalmazza a forrás-és célhelynek. 

Az utterance (kifejezés) a `Book 2 tickets from Seattle to Cairo`, Seattle a feladás helyét pedig Cairo a célhelyen. Minden egyes helye kontextusban különböző és megismert szórendjét és a word választott az utterance (kifejezés).

1. Az utterance (kifejezés), a szándék oldalon válassza ki a "Seattle", majd adja meg az entitás neve "helyre, és válassza ki **új entitás létrehozása**.

    ![Képernyőkép az létrehozása hierarchikus entitás címkézés párbeszédpanel](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. A megjelenő párbeszédpanelen, válassza a hierarchikus **entitástípus**, majd adja hozzá `Origin` és `Destination` gyermekei, és válassza ki, **kész**.

    ![Képernyőkép a leképezések részletei oldala, amelyen kiemelve ToLocation entitás](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. A word, az utterance (kifejezés) a következő címkét kapta a szülő hierarchikus entitással. A word hozzárendelése egy gyermek entitásnak kell. Térjen vissza az utterance (kifejezés) a szándék oldalon. Válassza ki a word-, majd a legördülő listából válassza ki a létrehozott entitás nevét, és hajtsa végre a menüben válassza ki a megfelelő gyermek entitásnak jobb.

    ![Képernyőkép a leképezések részletei oldala, amelyen kiemelve ToLocation entitás](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >Gyermek entitásnévnek egyedinek kell lennie minden entitás egyetlen alkalmazásban. Két különböző hierarchikus entitások nem tartalmazhat gyermekentitások ugyanazzal a névvel. 

    Lásd: [Adatkinyerés](luis-concept-data-extraction.md#hierarchical-entity-data) tudhat meg többet a végpont JSON lekérdezési válasz a hierarchikus entitás kinyerését. Próbálja ki a hierarchikus entitás [rövid](luis-quickstart-intent-and-hier-entity.md) hierarchikus entitás használatával kapcsolatos további.


## <a name="remove-entity-labels-from-utterances"></a>Beszédmódok entitás címkék eltávolítása
Gép megismert entitások címkék eltávolíthatók az utterance (kifejezés), a szándék oldalon. Ha a gép megismerte az entitás nem áll, akkor nem lehet eltávolítani az utterance (kifejezés). Ha egy nem gép megismert entitások eltávolítása az utterance (kifejezés) van szüksége, kell törölni az entitást a teljes alkalmazásban. 

Egy entitás gép megtanult címke eltávolítása az utterance (kifejezés), válassza ki az entitást az utterance (kifejezés). Válassza ki **címke eltávolítása** az entitás legördülő mezőben, amely akkor jelenik meg.

![Képernyőkép a leképezések Részletek lap, távolítsa el a kiemelt címkével ellátott](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>Előre összeállított entitások címke hozzáadása
Ha az előre összeállított entitások ad hozzá a LUIS-alkalmazás, nem kell címke utterances az entitásokhoz. Adja hozzá őket, illetve előre összeállított entitások kapcsolatos további tudnivalókért lásd: [entitások hozzáadása](luis-how-to-add-entities.md#add-prebuilt-entity).

## <a name="add-regular-expression-entity-label"></a>Reguláris kifejezés entitás címke hozzáadása
Ha a reguláris kifejezés entitásokat ad hozzá a LUIS-alkalmazás, nem kell címke utterances az entitásokhoz. Reguláris kifejezés entitásokat, és hogyan adja hozzá őket kapcsolatos további információkért lásd: [entitások hozzáadása](luis-how-to-add-entities.md#add-regular-expression-entities).

## <a name="create-a-pattern-from-an-utterance"></a>Hozzon létre egy minta az utterance (kifejezés)
Lásd: [Hozzáadás minta a leképezés vagy entitás oldalon meglévő utterance (kifejezés)](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="add-patternany-entity-label"></a>Pattern.any entitás címke hozzáadása
Pattern.any entitásokat ad hozzá a LUIS-alkalmazás, ha meg nem címkét az entitásokhoz kimondott szöveg. Csak azok a minták érvényes. Adja hozzá őket, illetve pattern.any entitások kapcsolatos további tudnivalókért lásd: [entitások hozzáadása](luis-how-to-add-entities.md#add-patternany-entities).

<!--
Fix this - moved to luis-how-to-add-intents.md - how ?

## Search in utterances
## Prediction discrepancy errors
## Filter by intent prediction discrepancy errors
## Filter by entity type
## Switch to token view
## Delete utterances
## Edit an utterance
## Reassign utterances

-->
## <a name="train-your-app-after-changing-model-with-utterances"></a>Beszédmódok modell módosítása után az alkalmazás betanítása
Miután hozzáadása, szerkesztése vagy eltávolítása a kimondott szöveg, [betanításához](luis-how-to-train.md) és [közzététele](luis-how-to-publish-app.md) a végpont lekérdezéseket érintő módosítások az alkalmazás. 

## <a name="next-steps"></a>További lépések

Beszédmódok a szándék fog vonatkozni a címkézés, után most már létrehozhat egy [összetett entitást](luis-how-to-add-entities.md).
