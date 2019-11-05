---
title: Kifejezések listája – LUIS
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) használatával olyan alkalmazás-funkciókat adhat hozzá, amelyek javítják a kategóriákat és a mintákat tartalmazó szándékok és entitások észlelését vagy előrejelzését
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 0e3e4226eaaa0505eea96d8b3aca820f2327349e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467619"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>A szólisták használata a szavak listájának növelésére

A LUIS-alkalmazáshoz további funkciókat adhat hozzá, hogy javítsa a pontosságot. A-funkciók segítséget nyújtanak a LUIS számára olyan javaslatok biztosításához, amelyekkel bizonyos szavak és kifejezések egy alkalmazás-tartománybeli szókincs részét képezik. 

A [kifejezések listája](luis-concept-feature.md) olyan értékek (szavak vagy kifejezések) egy csoportját tartalmazza, amelyek ugyanahhoz az osztályhoz tartoznak, és hasonló módon kell kezelni őket (például városok vagy termékek neve). Arról, hogy a LUIS hogyan tanulja meg az egyiket, automatikusan alkalmazza a többire is. Ez a lista nem ugyanaz, mint a [listában szereplő entitások](reference-entity-list.md) (pontos szöveges egyezések) a megfeleltetett szavak esetében.

A kifejezések listája az alkalmazás tartományának szókincsét adja hozzá második jelként, amely az említett szavakat mutatja.

A [szolgáltatással kapcsolatos fogalmak](luis-concept-feature.md) áttekintésével megtudhatja, hogy mikor és miért érdemes használni a kifejezések listáját. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-phrase-list"></a>Kifejezések listájának hozzáadása

A LUIS legfeljebb 10 kifejezési listát tesz lehetővé az alkalmazásokban. 

1. Nyissa meg az alkalmazást a saját **alkalmazások** lapján a nevére kattintva, majd kattintson a **Létrehozás**, majd a **kifejezések listája** elemre az alkalmazás bal oldali paneljén. 

1. A **listák felsorolása** lapon kattintson az **új kifejezések listájának létrehozása**elemre. 
 
1. A **kifejezések listájának hozzáadása** párbeszédpanelen írja be a `Cities` nevet a kifejezések listájának neveként. Az **érték** mezőbe írja be a kifejezési lista értékeit. Egyszerre csak egy értéket lehet beírni, vagy vesszővel elválasztva az értékek halmazát, majd nyomja le az **ENTER**billentyűt.

    ![Kifejezések listázása városok hozzáadása](./media/luis-add-features/add-phrase-list-cities.png)

1. A LUIS javasolhat kapcsolódó értékeket a kifejezések listájához való hozzáadáshoz. Kattintson az **ajánlott** elemre, ha a javasolt értékek egy csoportját szeretné beolvasni, amely szemantikailag kapcsolódik a hozzáadott érték (ek) hez. Bármelyik javasolt értékre kattinthat, vagy az **összes hozzáadása** lehetőségre kattintva hozzáadhatja őket.

    ![Kifejezések listájának javasolt értékei – az összes hozzáadása](./media/luis-add-features/related-values.png)

1. Kattintson **ezekre az értékekre,** ha a hozzáadott kifejezési lista értékei olyan alternatívák, amelyek szinonimaként használhatók.

    ![Kifejezések listájának javasolt értékei – jelölje ki a felcserélhetők jelölőnégyzetet](./media/luis-add-features/interchangeable.png)

1. Kattintson a **Done** (Kész) gombra. A "városok" kifejezést a rendszer hozzáadja a **kifejezések** listája oldalhoz.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> A kifejezések listáját törölheti vagy inaktiválhatja a **kifejezési listák** oldalon található környezetfüggő eszköztárból.

## <a name="next-steps"></a>További lépések

A kifejezések listájának hozzáadását, szerkesztését, törlését vagy inaktiválását követően ismét [betaníthatja és tesztelheti az alkalmazást, és](luis-interactive-test.md) ellenőrizheti, hogy javul-e a teljesítmény.
