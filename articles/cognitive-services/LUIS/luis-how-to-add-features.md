---
title: Leírók - LUIS
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) segítségével olyan alkalmazásfunkciókat adhat hozzá, amelyek javíthatják a kategóriákat és mintákat tartalmazó szándékok és entitások észlelését vagy előrejelzését
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631445"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Leírók használata a szólista jelének kiemelésére

A LUIS-alkalmazás hoz a pontosság javítása érdekében funkciókat adhat hozzá. A szolgáltatások segítenek a LUIS-nak azáltal, hogy tippeket adnak arra vonatkozóan, hogy bizonyos szavak és kifejezések egy alkalmazástartomány-szókincs részét képezik.

A [leíró](luis-concept-feature.md) (kifejezéslista) olyan értékcsoportot (szavakat vagy kifejezéseket) tartalmaz, amelyek ugyanabba az osztályba tartoznak, és amelyeket hasonlóan kell kezelni (például városok vagy termékek nevei). A LUIS-t az egyikről a luis automatikusan alkalmazza a többire is. Ez a lista nem ugyanaz, mint egy [listaentitás](reference-entity-list.md) (pontos szöveges egyezések) az egyező szavakkal.

A leíró hozzáteszi, hogy az alkalmazás tartomány szókincsét, mint egy második jel a LUIS ezekkel a szavakkal kapcsolatban.

Tekintse át [a szolgáltatásfogalmakat,](luis-concept-feature.md) hogy megértse, mikor és miért kell használni a leírót.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Leíró hozzáadása

1. Nyissa meg az alkalmazást úgy, hogy a **Saját alkalmazások** lapon a nevére kattint, majd a **Build**gombra, majd az alkalmazás bal oldali paneljén kattintson a **Leírók** elemre.

1. A **Leírók** lapon kattintson a **+ Leíró hozzáadása gombra.**

1. Az **Új kifejezéslista leírójának létrehozása** párbeszédpanelen `Cities` adja meg a leíró nevét. Az **Érték** mezőbe írja be a leírók értékeit, például `Seattle`a. Egyszerre csak egy értéket vagy vesszővel elválasztott értékhalmazt írhat be, majd nyomja le az **Enter billentyűt.**

    > [!div class="mx-imgBorder"]
    > ![Leíró városok hozzáadása](./media/luis-add-features/add-phrase-list-cities.png)

    Miután elegendő értéket adott meg a LUIS számára, javaslatok jelennek meg. **Hozzáadhatja az összes** javasolt értéket, vagy kiválaszthatja az egyes kifejezéseket.

1. Tartsa: **Ezek az értékek felcserélhetők,** ha a hozzáadott leíró értékek olyan alternatívák, amelyek felcserélhetők.

1. A kifejezéslista a **globális** beállítással rendelkező teljes alkalmazásra, illetve egy adott modellre (szándék vagy entitás) is alkalmazható. Ha a kifejezéslistát egy szándék vagy entitás _leírójaként_ hozza létre, a váltó nem globális. Ebben az esetben a kapcsoló jelentése az, hogy a leíró csak az adott modell, ezért _nem globális_ az alkalmazás számára.

1. Válassza a **Done** (Kész) lehetőséget. Az új leíró hozzáadódik a **leírók** laphoz.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> A leírót törölheti vagy inaktiválhatja a **Leírók** lap környezetfüggő eszköztárából.

## <a name="next-steps"></a>További lépések

A leíró hozzáadása, szerkesztése, törlése vagy inaktiválása után [újra betanítsa és tesztelje az alkalmazást,](luis-interactive-test.md) hogy lássa, javul-e a teljesítmény.
