---
title: Címke-entitás – példa Kimondás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan címkézheti egy géppel megtanult entitást alösszetevőkkel egy példa Kimondás formájában a LUIS-portálon található szándék részletek oldalán.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: 5499e8362e1107a18b997a6a485e11343c328d0d
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898371"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Címkézett gép – megtanult entitás egy példa kimondottan

Ha egy entitást címkéz egy példa kiírásakor, az a LUIS példa arra, hogy mi is az entitás, és hogy az entitás hol jelenhet meg a teljes tartalomban.

## <a name="labeling-machine-learned-entity"></a>Gépi megtanult entitás címkézése

Vegye figyelembe a kifejezést, `hi, please I want a cheese pizza in 20 minutes`.

1. Válassza ki a bal szélső szöveget, majd válassza ki az entitás jobb szélső szövegét, majd válassza ki a címkével ellátni kívánt entitást, ebben az esetben a teljes sorrendet. A _teljes sorrend_ a következő képen látható.

    > [!div class="mx-imgBorder"]
    > ![címke teljes gépi megtanult entitás](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Válassza ki az entitást az előugró ablakban. A címkével ellátott teljes pizza Order entitás tartalmazza a címkével ellátott összes szót (balról jobbra, angol nyelven).

## <a name="review-labeled-text"></a>Címkézett szöveg áttekintése

A címkézést követően tekintse át a példa teljes számát, és gondoskodjon arról, hogy a kijelölt szöveg a kiválasztott entitással legyen aláhúzva. A folytonos vonal azt jelzi, hogy a szöveg címkével van ellátva.

> [!div class="mx-imgBorder"]
> ![címkézett gépi megtanult entitás](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Előre jelzett entitás megerősítése

Ha a szöveg keretén belüli pontozott téglalap található, és az entitás neve meghaladja a rövidítést, akkor a szöveg előre látható, de _még nincs megjelölve_. Az előrejelzés címkévé való bekapcsolásához jelölje ki a teljes sort, majd válassza az **entitások előrejelzésének megerősítése**lehetőséget.

> [!div class="mx-imgBorder"]
> ![megjósolni a géppel megtanult entitások teljes](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

Azt is megteheti, hogy a szöveg fölött kijelöli az entitás nevét, majd a megjelenő menüben kiválasztja a **jóslat megerősítése** lehetőséget.

> [!div class="mx-imgBorder"]
> ![megjósolni, hogy a gép megtanulta a menüt](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Címke entitás festéssel az Entity paletta kurzorral

Az entitás-paletta a korábbi címkézési felület alternatívájaként nyújt alternatívát. Lehetővé teszi a szöveg átméretezését, ha azonnal címkével látja el azt egy entitással.

1. Nyissa meg az entitás-palettát a kiemelési tábla jobb felső sarkában található kiemelő ikonra kattintva.

    > [!div class="mx-imgBorder"]
    > ![a gépi megtanult entitáshoz tartozó Entity paletta](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Válassza ki az entitás összetevőt. Ez a művelet vizuálisan egy új kurzorral van megjelölve. A kurzor az egérmutatót követi a portálon való áthelyezés során.

    > [!div class="mx-imgBorder"]
    > ![a gépi megtanult entitáshoz tartozó Entity paletta](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. A példában a Kimondás elemre _festi_ az entitást a kurzorral.

    > [!div class="mx-imgBorder"]
    > ![a gépi megtanult entitáshoz tartozó Entity paletta](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Egy gép megtanult entitás alösszetevőinek címkézése

Az entitásokban lévő alösszetevők pontosan ugyanúgy vannak megjelölve, mint a legfelső szintű entitások. A szöveg kiválasztásakor az előugró ablakban elérhető entitások a szövegnek megfelelő kontextushoz viszonyítva jelennek meg. Ha például egy 5 szintű, géppel megtanult entitást használ, és olyan szöveget választ ki, amely az 1. és a 2. szinttel van megjelölve (a példában szereplő címkével ellátott entitás neve jelzi), az előugró ablakban elérhető entitások köre a a harmadik szint összetevőinek kontextusa. Ha más entitásokkal szeretné felcímkézni a szöveget, válassza a **címke másik entitásként** lehetőséget.

> [!div class="mx-imgBorder"]
> ![a gépi megtanult entitáshoz tartozó Entity paletta](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Az alösszetevők csak akkor címkével rendelkezhetnek, ha a szülő is címkével van ellátva.

## <a name="labeling-entity-roles"></a>Entitás szerepköreinek címkézése

Az entitások szerepkörei az Entity paletta használatával vannak megjelölve.

1. A leképezés részletei lapon válassza ki az Entity ( **entitás) palettát** a helyi eszköztárból.
1. Az entitás-paletta megnyitása után válassza ki az entitást az entitás listából.
1. Lépjen az **entitás-felügyelőre**, és válasszon ki egy meglévő szerepkört, vagy hozzon létre egy új szerepkört.
1. A példában a Kimondás szövegében címkézze fel a szöveget az entitás szerepkörrel.

## <a name="unlabel-entities"></a>Entitások címkézésének kicsomagolása

Entitás kicímkézéséhez válassza ki az entitás nevét a szöveg alatt, és válassza a **címke**kijelölése elemet. Ha a felcímkézni próbált entitás címkével ellátott alösszetevőket tartalmaz, akkor először el kell távolítani az alösszetevőket.

## <a name="editing-labels-using-the-entity-palette"></a>Címkék szerkesztése az Entity paletta használatával

Ha a címkézés során hiba történik, az Entity paletta egy egyszerű eszköz, amely lehetővé teszi a gyors szerkesztést. Ha például egy entitás címkéje egy további szót mutat be a hiba alapján, és már rendelkezik címkézett alösszetevőkkel, akkor az Entity paletta használatával ecsetet adhat a szavak rövidebb időtartamára.

Például:

1. A pizza Type alösszetevő a "Cheese pizza with" kifejezéssel is rendelkezik, amely tartalmaz egy extra helytelen szót – "with"

    > [!div class="mx-imgBorder"]
    > ![a gépi megtanult entitáshoz tartozó Entity paletta](media/label-utterances/edit-label-with-palette-1.png)

2. Az Entity paletta használatával válassza ki a pizza típust, és ecsetet a "Cheese pizza" kifejezésre. Ennek eredménye, hogy csak a Cheese pizza van megjelölve pizza típusúként.

    > [!div class="mx-imgBorder"]
    > ![a gépi megtanult entitáshoz tartozó Entity paletta](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Egyező szövegű entitások címkéi

Az egyező szöveges entitások közé tartoznak az előre elkészített entitások, a reguláris kifejezések entitásai, az entitások listázása és a minta. bármely entitás. Ezeket a rendszer automatikusan a LUIS címkével jelölte meg, így a felhasználóknak nem kell manuálisan megadniuk a címkét.

## <a name="entity-prediction-errors"></a>Entitás-előrejelzési hibák

Az entitás-előrejelzési hibák azt jelzik, hogy az előre jelzett entitás nem egyezik a címkével ellátott entitással. Ezt a rendszer a kiírás melletti óvatossági mutatóval ábrázolja.

> [!div class="mx-imgBorder"]
> ![a gépi megtanult entitáshoz tartozó Entity paletta](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>További lépések

Használja az [irányítópultot](luis-how-to-use-dashboard.md) , és [tekintse át a végpont hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md) az alkalmazás előrejelzési minőségének javítása érdekében.