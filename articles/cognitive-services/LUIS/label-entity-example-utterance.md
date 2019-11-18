---
title: Címke-entitás – példa Kimondás
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan címkézheti egy géppel megtanult entitást alösszetevőkkel egy példa Kimondás formájában a LUIS-portálon található szándék részletek oldalán.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135109"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Címkézett gép – megtanult entitás egy példa kimondottan

Az entitások egy példán belüli jelölése azt mutatja, hogy LUIS tartalmaz egy példát az entitásra, és ahol az entitás megjelenhet a teljes tartalomban. 

## <a name="labeling-machine-learned-entity"></a>Gépi megtanult entitás címkézése

Vegye figyelembe a kifejezést, `hi, please I want a cheese pizza in 20 minutes`. 

1. Válassza ki a bal szélső szöveget, majd válassza ki az entitás jobb szélső szövegét. A _teljes sorrend_ a következő képen látható.

    > [!div class="mx-imgBorder"]
    > ![címke teljes gépi megtanult entitás](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Válassza ki az entitást az előugró ablakban. A címkével ellátott teljes pizza Order entitás tartalmazza a címkével ellátott összes szót (balról jobbra, angol nyelven). 

> [!TIP]
> Az előugró ablakban elérhető entitások ahhoz a környezethez viszonyítva jelennek meg, amelyben a szöveg megjelenik. Ha például egy 5 szintű, géppel megtanult entitást használ, és a harmadik szinten (a példa megjelölése alatt a címkével ellátott entitás neve) választ, akkor az előugró ablakban elérhető entitások a harmadik alösszetevők környezetre korlátozódnak. szint (negyedik szintű alösszetevők). 

## <a name="review-labeled-text"></a>Címkézett szöveg áttekintése

A címkézést követően tekintse át a példa kiértékelését. LUIS az aktuális modellt a címkézést követően a példára alkalmazza. A folytonos vonal azt jelzi, hogy a szöveg címkével van ellátva. 

> [!div class="mx-imgBorder"]
> ![címkézett gépi megtanult entitás](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>Mikor kell betanítani

Ha az aktuális modellnek támogatnia kell a címkével ellátott entitást, de a Kimondás továbbra is a szöveget előre jelezve, de nem címkézett, betanítja az alkalmazást.  

## <a name="confirm-predicted-entity"></a>Előre jelzett entitás megerősítése

Ha a vizualizációs kijelző a kimaradás felett van, az azt jelzi, hogy a szöveg előre látható, de _még nincs megjelölve_. Az előrejelzés címkévé való bekapcsolásához válassza ki a megfelelő elemet, majd válassza az **entitások előrejelzésének megerősítése**lehetőséget.

> [!div class="mx-imgBorder"]
> ![megjósolni a géppel megtanult entitások teljes](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>Alösszetevő-entitás címkézése az entitás-paletta kurzorral való festéssel

1. Az előrejelzések kijavításához (amelyek a példa kimondása felett jelennek meg) nyissa meg az entitások palettáját. 

    > [!div class="mx-imgBorder"]
    > ![a gépi megtanult entitáshoz tartozó Entity paletta](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. Válassza ki az entitás alösszetevőt. Ez a művelet vizuálisan egy új kurzorral van megjelölve. A kurzor az egérmutatót követi a portálon való áthelyezés során. 

    > [!div class="mx-imgBorder"]
    > ![a gépi megtanult entitáshoz tartozó Entity paletta](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. A példában a Kimondás elemre _festi_ az entitást a kurzorral. 

    > [!div class="mx-imgBorder"]
    > ![a gépi megtanult entitáshoz tartozó Entity paletta](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>Egyező szöveges entitások címkézése egy géppel megtanult entitásban

Az egyező szövegű entitások közé tartoznak az előre összeépített entitások, a reguláris kifejezések entitásai és az entitások listázása. Ezeket felveheti egy géppel megtanult entitásba, mint egy alösszetevő korlátozásait, amikor létrehozza vagy szerkeszti a gép által megismert entitást. 

**Miután hozzáadta ezeket a korlátozásokat, nem kell megadnia a példában szereplő egyező szöveg címkéjét.**

## <a name="entity-prediction-errors"></a>Entitás-előrejelzési hibák

Az entitás-előrejelzési hibák figyelmeztető kijelzőt jelenítenek meg. Ez azt jelzi, hogy az előre jelzett entitás nem egyezik a címkével ellátott entitással. 

> [!div class="mx-imgBorder"]
> ![a gépi megtanult entitáshoz tartozó Entity paletta](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>További lépések

Használja az [irányítópultot](luis-how-to-use-dashboard.md) , és [tekintse át a végpont hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md) az alkalmazás előrejelzési minőségének javítása érdekében.
