---
title: Descripters – LUIS
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) használatával olyan alkalmazás-funkciókat adhat hozzá, amelyek javítják a kategóriákat és a mintákat tartalmazó szándékok és entitások észlelését vagy előrejelzését
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80631445"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>A leírók használatával növelheti a szólisták jelét

A LUIS-alkalmazáshoz további funkciókat adhat hozzá, hogy javítsa a pontosságot. A-funkciók segítséget nyújtanak a LUIS számára olyan javaslatok biztosításához, amelyekkel bizonyos szavak és kifejezések egy alkalmazás-tartománybeli szókincs részét képezik.

A [leíró](luis-concept-feature.md) (kifejezések listája) olyan értékek (szavak vagy kifejezések) egy csoportját tartalmazza, amelyek ugyanahhoz az osztályhoz tartoznak, és hasonlóan kell kezelni őket (például városok vagy termékek neve). Arról, hogy a LUIS hogyan tanulja meg az egyiket, automatikusan alkalmazza a többire is. Ez a lista nem ugyanaz, mint a [listában szereplő entitások](reference-entity-list.md) (pontos szöveges egyezések) a megfeleltetett szavak esetében.

Egy leíró az alkalmazás tartományának szókincsét második jelként adja meg a LUIS-hez a szóban forgó szavakkal.

A [szolgáltatással kapcsolatos fogalmak](luis-concept-feature.md) áttekintésével megismerheti, hogy mikor és miért érdemes leírót használni.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Leíró hozzáadása

1. Nyissa meg az alkalmazást a saját **alkalmazások** lapján a nevére kattintva, majd kattintson a **Build**, majd a **descripters** elemre az alkalmazás bal oldali paneljén.

1. A **descripters** lapon kattintson a **+ leíró hozzáadása**elemre.

1. Az **új kifejezés lista leírójának létrehozása** párbeszédpanelen adjon meg egy nevet, például `Cities` a leíróhoz. Az **érték** mezőbe írja be a descripters értékeit, például: `Seattle`. Egyszerre csak egy értéket lehet beírni, vagy vesszővel elválasztva az értékek halmazát, majd nyomja le az **ENTER**billentyűt.

    > [!div class="mx-imgBorder"]
    > ![Leíró városok hozzáadása](./media/luis-add-features/add-phrase-list-cities.png)

    Ha a LUIS számára elég értéket adott meg, a javaslatok megjelennek. Az összes javasolt értéket **+ felveheti** , vagy kijelölhet egyéni kifejezéseket is.

1. Ha a hozzáadott leíró értékek olyan alternatívák, amelyek szinonimaként használhatók, tartsa meg **ezeket az értékeket** .

1. A kifejezések listája a teljes alkalmazásra vonatkozik a **globális** beállítással, vagy egy adott modellre (cél vagy entitás). Ha a kifejezés listát egy szándék vagy entitás _leírójának_ létrehozásakor hozza létre, a váltógomb nem globális értékre van állítva. Ebben az esetben a váltás jelentése az, hogy a leíró egy olyan szolgáltatás, amely _nem globális_ az alkalmazás számára.

1. Válassza a **Done** (Kész) lehetőséget. Az új leíró bekerül a **descripters** lapra.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Egy leírót törölheti vagy inaktiválhat a **descripters** lapon található környezetfüggő eszköztárból.

## <a name="next-steps"></a>További lépések

Egy leíró hozzáadását, szerkesztését, törlését vagy inaktiválását követően ismét [betaníthatja és tesztelheti az alkalmazást, és](luis-interactive-test.md) ellenőrizheti, hogy javul-e a teljesítmény.
