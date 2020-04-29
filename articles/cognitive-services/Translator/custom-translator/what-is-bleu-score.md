---
title: Mi az a BLEU pontszám? -Egyéni fordító
titleSuffix: Azure Cognitive Services
description: BLEU a gépi fordítás és az emberi által létrehozott, ugyanazon forrásra vonatkozó mondatok közötti különbségek mérése.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: swmachan
ms.openlocfilehash: 85c4ee27a828a05c64ca6cbf84bff438535328be
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73647359"
---
# <a name="what-is-a-bleu-score"></a>Mi az a BLEU pontszám?

[Bleu (kétnyelvű kiértékelési tanulmány)](https://en.wikipedia.org/wiki/BLEU) az automatikus fordítás és egy vagy több emberi által létrehozott, azonos forrásoldali mondatban található fordítás közötti különbségek mérése.

## <a name="scoring-process"></a>Pontozási folyamat

A BLEU algoritmus összehasonlítja az automatikus fordítás egymást követő kifejezéseit a hivatkozási fordításban megtalált egymást követő kifejezésekkel, és a találatok számát súlyozott módon számítja ki. Ezek a egyezések egymástól függetlenek. A magasabb szintű egyeztetési fok magasabb fokú hasonlóságot jelez a hivatkozás fordításával és a magasabb pontszámot. Az érthetőség és a nyelvtan helyességét nem veszi figyelembe a rendszer.

## <a name="how-bleu-works"></a>Hogyan működik a BLEU?

BLEU erőssége az, hogy jól összefügg az emberi ítélettel azáltal, hogy az egyes mondatok esetében az egyes mondatok esetében az egyes mondatokra vonatkozó mondatokat az egyes mondatok pontos emberi ítéletének kijavítása helyett.

A BLEU-pontszámok széles körű megbeszélése [itt](https://youtu.be/-UqDljMymMg)található.

A BLEU eredményei erősen függenek a tartomány szélességével, a tesztelési adatok konzisztenciájával az adatok betanításával és hangolásával, valamint a betanításhoz rendelkezésre álló adatok mennyiségével. Ha a modelljeit egy keskeny tartományon szerezték be, és a betanítási adatok konzisztensek a tesztelési adatokkal, akkor magas BLEU-pontszámot várhat.

>[!NOTE]
>A BLEU-pontszámok összehasonlítása csak akkor igazolható, ha a BLEU eredményeit ugyanazzal a tesztelési készlettel, azonos nyelvi párral és ugyanazzal az MT-motorral hasonlítják össze. Egy másik tesztelési készletből származó BLEU-pontszám nem lehet más.
