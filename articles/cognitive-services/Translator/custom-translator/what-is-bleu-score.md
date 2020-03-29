---
title: Mi az a BLEU pontszám? - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A BLEU az ugyanazon forrásmondat gépi fordítása és az ember által létrehozott referenciafordításai közötti különbségek mérése.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 85c4ee27a828a05c64ca6cbf84bff438535328be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647359"
---
# <a name="what-is-a-bleu-score"></a>Mi az a BLEU pontszám?

[A BLEU (Kétnyelvű értékelés Beugró)](https://en.wikipedia.org/wiki/BLEU) az automatikus fordítás és ugyanazon forrásmondat egy vagy több, ember által létrehozott referenciafordítása közötti különbségek mérése.

## <a name="scoring-process"></a>Pontozási folyamat

A BLEU algoritmus összehasonlítja az automatikus fordítás egymást követő kifejezéseit a referenciafordításban talált egymást követő kifejezésekkel, és súlyozott módon számolja a találatok számát. Ezek a mérkőzések pozíciófüggetlenek. A magasabb találati fokozat nagyobb fokú hasonlóságot jelez a referenciafordítással és magasabb pontszámot. Az érthetőséget és a nyelvtani helyességet nem veszik figyelembe.

## <a name="how-bleu-works"></a>Hogyan működik a BLEU?

BLEU erőssége az, hogy jól korrelál az emberi ítélet átlagolásával ki az egyes mondat ítélet hibák at a vizsgálati corpus, ahelyett, hogy megpróbálja kidolgozni a pontos emberi ítélet minden mondatot.

Egy szélesebb körű vitát BLEU pontszámok [itt](https://youtu.be/-UqDljMymMg).

A BLEU-eredmények nagymértékben függnek a tartomány szélességétől, a tesztadatok konzisztenciájától a betanítási és hangolási adatokkal, valamint attól, hogy mennyi adat áll rendelkezésre a betanításhoz. Ha a modellek egy szűk tartományban vannak betanítva, és a betanítási adatok konzisztensek a tesztadatokkal, magas BLEU-pontszámra számíthat.

>[!NOTE]
>A BLEU pontszámok összehasonlítása csak akkor igazolható, ha a BLEU eredményeket ugyanazzal a tesztkészlettel, azonos nyelvi párral és ugyanazzal az MT motorral hasonlítják össze. Egy másik tesztkészlet bleu pontszáma eltérő nek kell lennie.
