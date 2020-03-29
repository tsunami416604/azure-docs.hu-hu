---
title: Címkeentitás példa utterance
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan címkézhet egy gép megtanult entitás alösszetevők egy példa utterance (kifejezés) a LUIS-portál leképezési részletes lapján.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: 5499e8362e1107a18b997a6a485e11343c328d0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898371"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>A machine-learned entitás címkéje egy példa utterance

Egy entitás címkézése egy példa utterance (kifejezés) egy példa luis egy példát, hogy mi az entitás, és ahol az entitás megjelenhet az utterance (kifejezés).

## <a name="labeling-machine-learned-entity"></a>A gép által megtanult entitás címkézése

Tekintsük `hi, please I want a cheese pizza in 20 minutes`a kifejezést, .

1. Jelölje ki a bal szélső szöveget, majd jelölje ki az entitás jobb szélső szövegét, majd válassza ki azt az entitást, amelyhez címkézni szeretne, ebben az esetben a Teljes sorrend. A _teljes rendelés_ az alábbi képen látható.

    > [!div class="mx-imgBorder"]
    > ![Címke teljes gépmegtanult entitás](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Válassza ki az entitást az előugró ablakból. A címkézett teljes pizzarendelési entitás tartalmazza az összes olyan szót (balról jobbra angolnyelven), amely címkével van ellátva.

## <a name="review-labeled-text"></a>Címkézett szöveg áttekintése

A címkézés után tekintse át a példa utterance (kifejezés) és győződjön meg arról, hogy a kiválasztott szövegtartomány alá van húzva a kiválasztott entitással. A folytonos vonal azt jelzi, hogy a szöveg meg van címkézve.

> [!div class="mx-imgBorder"]
> ![Címkézett teljes gépmegtanult entitás](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Előre jelzett entitás megerősítése

Ha a szöveg tartománya körül pontozott vonalú mező található, és az entitás neve az utterance (kifejezés) felett van, akkor azt jelzi, hogy a szöveg előre jelzett, de _még nincs megcímkézve._ Az előrejelzés címkénnyé alakításához jelölje ki az utterance (kifejezés) sort, majd válassza **az Entitás-előrejelzések megerősítése**lehetőséget.

> [!div class="mx-imgBorder"]
> ![Teljes gépmegtanult entitás előrejelzése](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

Másik lehetőségként kiválaszthatja az entitás nevét a szöveg felett, majd a megjelenő menüből válassza az **Előrejelzés megerősítése** parancsot.

> [!div class="mx-imgBorder"]
> ![A teljes gépi legeltetésű entitás előrejelzése menüvel](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Entitás felirata entitáspaletta kurzorral való festéssel

Az entitáspaletta alternatívát kínál az előző címkézési élményhez képest. Lehetővé teszi a szöveg ecsettel történő átmosását, hogy azonnal címkézze egy entitással.

1. Nyissa meg az entitáspalettát az utterance (kifejezés) táblázat jobb felső részén található Kiemelő ikon kiválasztásával.

    > [!div class="mx-imgBorder"]
    > ![Entitáspaletta gépmegtanult entitáshoz](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Válassza ki az entitásösszetevőt. Ezt a műveletet vizuálisan egy új kurzorral jelzi. A kurzor követi az egeret, ahogy mozog a portálon.

    > [!div class="mx-imgBorder"]
    > ![Entitáspaletta gépmegtanult entitáshoz](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. A példa utterance (kifejezés) az entitás _a_ kurzorral.

    > [!div class="mx-imgBorder"]
    > ![Entitáspaletta gépmegtanult entitáshoz](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Egy gép által megtanult entitás alösszetevőinek címkézése

Az entitások alösszetevői pontosan ugyanúgy vannak címkézve, mint a legfelső szintű entitások. Szöveg kijelölésétorként az előugró ablakban elérhető entitások a szöveg szövegének környezetéhez viszonyítva jelennek meg. Ha például egy 5 szintű, gép megtanult entitással rendelkezik, és olyan szöveget választ, amely az első és a 2. a harmadik szint összetevőinek összefüggésében. Ha a szöveget más entitásokkal szeretné címkézni, válassza a **Címke másik entitásként** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Entitáspaletta gépmegtanult entitáshoz](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Az alösszetevők csak akkor címkézhetők, ha a szülő is meg van címkézve.

## <a name="labeling-entity-roles"></a>Entitásszerepkörök címkézése

Az entitásszerepkörök az entitáspaletta használatával vannak címkézve.

1. A Szándék részletei lapon válassza ki az **Entitás palettát** a környezet eszköztáráról.
1. Az Entitás paletta megnyitása után válassza ki az entitást az entitás listájából.
1. Lépjen az **Entitásfelügyelőre**, és válasszon ki egy meglévő szerepkört, vagy hozzon létre egy új szerepkört.
1. A példa utterance (kifejezés) szöveget, felirata a szöveget az entitás szerepkör.

## <a name="unlabel-entities"></a>Címke nélküli entitások

Entitás címzésének megszüntetéséhez jelölje ki az entitás nevét a szöveg alatt, és válassza a **Címke megszüntetése**lehetőséget. Ha az entitás, amelyet le szeretne vonni a felirattal, feliratozott alösszetevőkkel rendelkezik, akkor az alösszetevőket először nem kell címkézni.

## <a name="editing-labels-using-the-entity-palette"></a>Címkék szerkesztése az entitáspalettával

Ha hibát követ el a címkézés során, az entitáspaletta egy egyszerű eszköz, amely lehetővé teszi a gyors szerkesztést. Ha például egy entitáscímke véletlenül egy további szót ölel fel, és már rendelkezik címkézett alösszetevőkkel, akkor az entitáspalettával ecsettel átsöpörhet a kívánt rövidebb szavakra.

Példa:

1. Pizza Típus alkomponens ível "sajt pizza", amely magában foglalja egy extra helytelen szó - "a"

    > [!div class="mx-imgBorder"]
    > ![Entitáspaletta gépmegtanult entitáshoz](media/label-utterances/edit-label-with-palette-1.png)

2. Használja a entitás paletta felvenni Pizza type és ecsettel át "sajt pizza". Az eredmény az, hogy csak a sajt pizza van címkézve Pizza Type most.

    > [!div class="mx-imgBorder"]
    > ![Entitáspaletta gépmegtanult entitáshoz](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Címkék a megfelelő szöveges entitásokhoz

A megfelelő szöveges entitások közé tartoznak az előre összeállított entitások, reguláris kifejezés entitások, listaentitások és pattern.any entitások. Ezeket a LUIS automatikusan címkézi, így nem kell manuálisan címkézni a felhasználók.

## <a name="entity-prediction-errors"></a>Entitás-előrejelzési hibák

Az entitás előrejelzési hibái azt jelzik, hogy az előre jelzett entitás nem egyezik meg a címkézett entitással. Ez láthatóvá egy figyelmeztető jelzés mellett az utterance (kifejezés) jelző.

> [!div class="mx-imgBorder"]
> ![Entitáspaletta gépmegtanult entitáshoz](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>További lépések

Használja az [irányítópultot,](luis-how-to-use-dashboard.md) és tekintse át a [végpont kimondott szövegét](luis-how-to-review-endpoint-utterances.md) az alkalmazás előrejelzési minőségének javításához.