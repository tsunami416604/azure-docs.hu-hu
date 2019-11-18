---
title: Descripters – LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) használja, amely javítja az észlelésük vagy szándékok és entitások előrejelzését funkciók hozzáadása, a kategóriák és minták
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123148"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>A leírók használatával növelheti a szólisták jelét

A pontosság javítása érdekében a LUIS-alkalmazás funkciókat adhat hozzá. Szolgáltatások révén a LUIS mutatók azáltal, hogy egyes szavak és kifejezések egy alkalmazás tartomány szókincsből eredőket részét képezik. 

A [leíró](luis-concept-feature.md) (kifejezések listája) olyan értékek (szavak vagy kifejezések) egy csoportját tartalmazza, amelyek ugyanahhoz az osztályhoz tartoznak, és hasonlóan kell kezelni őket (például városok vagy termékek neve). Mi a LUIS megismerkedik az egyik automatikusan alkalmazza a mások számára is. Ez a lista nem ugyanaz, mint a [listában szereplő entitások](reference-entity-list.md) (pontos szöveges egyezések) a megfeleltetett szavak esetében.

Egy leíró az alkalmazás tartományának szókincsét második jelként adja meg a LUIS-hez a szóban forgó szavakkal.

A [szolgáltatással kapcsolatos fogalmak](luis-concept-feature.md) áttekintésével megismerheti, hogy mikor és miért érdemes leírót használni. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Leíró hozzáadása

1. Nyissa meg az alkalmazást a saját **alkalmazások** lapján a nevére kattintva, majd kattintson a **Build**, majd a **descripters** elemre az alkalmazás bal oldali paneljén. 

1. A **descripters** lapon kattintson a **+ leíró hozzáadása**elemre. 
 
1. Az **új kifejezési lista leírójának létrehozása** párbeszédpanelen adjon meg egy nevet, például `Cities` a leíróhoz. Az **érték** mezőbe írja be a descripters értékeit, például `Seattle`. Írjon be egy értéket egy időben, vagy értékek vesszővel elválasztva, és nyomja le az **Enter**.

    > [!div class="mx-imgBorder"]
    > ![leíró városok hozzáadása](./media/luis-add-features/add-phrase-list-cities.png)

    Ha a LUIS számára elég értéket adott meg, a javaslatok megjelennek. Az összes javasolt értéket **+ felveheti** , vagy kijelölhet egyéni kifejezéseket is.

1. Ha a hozzáadott leíró értékek olyan alternatívák, amelyek szinonimaként használhatók, tartsa meg **ezeket az értékeket** .

1. Válassza a **Done** (Kész) lehetőséget. Az új leíró bekerül a **descripters** lapra.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Egy leírót törölheti vagy inaktiválhat a **descripters** lapon található környezetfüggő eszköztárból.

## <a name="next-steps"></a>További lépések

Egy leíró hozzáadását, szerkesztését, törlését vagy inaktiválását követően ismét [betaníthatja és tesztelheti az alkalmazást, és](luis-interactive-test.md) ellenőrizheti, hogy javul-e a teljesítmény.
