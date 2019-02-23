---
title: Mi az a BLEU pontszám? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: BLEU egy mérték automatikus fordítását és a egy vagy több ember által létrehozott referencia fordítását egy forrás mondatban közötti különbségeket. A BLEU algoritmus az egymást követő kifejezések, a hivatkozás fordítás talál, és egyezést, súlyozott módon megszámolja a automatikus fordítás egymást követő kifejezések hasonlítja össze.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-rada
ms.openlocfilehash: 354c26e1d7290a6296b80eabcd8e63ff304e180f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730171"
---
# <a name="what-is-a-bleu-score"></a>Mi az a BLEU pontszám?

[(Kétnyelvű értékelési Understudy) BLEU](https://en.wikipedia.org/wiki/BLEU) mérésére szolgál egy automatikus fordítás a és a egy vagy több ember által létrehozott referencia fordításának forrás egy mondatban közötti különbségeket.

## <a name="scoring-process"></a>Kiértékelés folyamatban

A BLEU algoritmus az egymást követő kifejezések, a hivatkozás fordítás talál, és egyezést, súlyozott módon megszámolja a automatikus fordítás egymást követő kifejezések hasonlítja össze. Az egyezések elhelyezése független. Egyezés nagyobb mértékben azt jelzi, hogy a referencia-fordítási és a magasabb pontszámot hasonlóság nagyobb fokú. Képminőséget és nyelvtani helyességét nem veszik figyelembe.

## <a name="how-bleu-works"></a>BLEU működését?

A BLEU erőssége, hogy azt utal. jól emberi ítélet keresztül egy teszt forrásgyűjteményébe ki egyes mondathoz ítélet hibák átlagolás, helyett próbál megtervezi a pontos emberi határozat minden mondat helyett szerepel.

A pontszámok BLEU szélesebb körű vita van [Itt](https://youtu.be/-UqDljMymMg).

BLEU eredmények erősen függ, a tartomány, a képzés és finomhangolása az adatokat, a Tesztadatok konzisztenciájának technológiai spektrumunk kihasználtságának növelését, és hogy mennyi adatot elérhető betanításához. A modellek rendelkezik betanítva keskeny tartományban, és a betanítási adatok konzisztensek legyenek a tesztadatok, ha várhatóan magas BLEU pontszámot.

>[!NOTE]
>BLEU pontszámok összehasonlítását csak akkor indokolható BLEU eredmények teszt ugyanezek, ugyanarra a nyelv két és ugyanazt a fő Célkiszolgáló motort képest. Egy másik teszt készletből BLEU pontszámot kötött kell lennie.
