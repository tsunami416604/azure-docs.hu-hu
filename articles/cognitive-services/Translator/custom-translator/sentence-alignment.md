---
title: Párosítás és -igazításának – egyéni a fordítót Mondatkezdő
titleSuffix: Azure Cognitive Services
description: A betanítási futtatás során mondatokat párhuzamos dokumentumokban található a párosított vagy igazítva. Egyéni a fordítót fordítások egy mondatban tanul, egyszerre egy mondatát, a fordítás ezt a mondatot, olvassa el. Ezután azt igazítja szavak és kifejezések ezen két mondatokban egymáshoz.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 557cd8d3af0c774d4dd0558d5d25dba8eec07268
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627426"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Mondat párosítási és párhuzamos dokumentumokban igazítása

A képzés során mondatokat párhuzamos dokumentumokban található a párosított vagy igazítva. Egyéni a fordítót a mondatok tudta párosítsa az egyes adatkészletek a igazítva mondatokat, számát jelenti.

## <a name="pairing-and-alignment-process"></a>Párosítás és -igazításának folyamat

Egyéni a fordítót megtanulja a mondatok egy mondatot fordításának egyszerre. Ez a forrás, és a fordítás célról a mondat mondat olvasmányok. Ezután azt igazítja szavak és kifejezések ezen két mondatokban egymáshoz. Ez a folyamat lehetővé teszi, hogy egy mondatban az egyenértékű szavak és kifejezések a fordítás a mondat a szavak és kifejezések térkép létrehozásához. Győződjön meg arról, hogy a rendszer a mondatokat, amelyek minden más fordítások betanítja megpróbálja igazítását.

## <a name="pre-aligned-documents"></a>Előre igazított dokumentumok

Ha ismeri a párhuzamos dokumentumok rendelkezik, felülbírálhatja a mondat zarovnání előre igazított szöveges fájlok listájának megadásával. Kibonthatja összes mondatokat mindkét dokumentum a szövegfájlba, egy vonal- és feltöltés a szervezett egy mondatban egy `.align` bővítmény. A `.align` bővítmény jelzi a egyéni a fordítót, hogy azt ki kell hagyni az mondat igazítását.

A legjobb eredmények érdekében próbálja meg ahhoz, hogy, hogy a fájl minden sorában egy mondatot. Nem rendelkezik a sortörés karakterek mondaton belül, ennek hatására a gyenge illesztését.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>Kivont és igazított mondatokat javasolt minimális száma

Sikeres képzéshez az alábbi táblázat a kinyert mondatokat, majd minden egyes adatkészlethez szükséges igazított mondatokat minimális számát. Kivont mondatokat javasolt minimális számát, sokkal nagyobb figyelembe kell venni a tény, hogy mondat zarovnání nem feltétlenül tudja sikeresen igazítása minden kinyert mondatokat igazított mondatokat javasolt minimális számánál.

| Adatkészlet   | Ajánlott minimális kinyert mondat száma | Ajánlott minimális igazított mondat száma | Igazított mondat maximális száma |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Képzés   | 10,000                                     | 2,000                                    | Nincs felső korlátja                 |
| Hangolás     | 2,000                                      | 500                                      | 2,500                          |
| Tesztelés    | 2,000                                      | 500                                      | 2,500                          |
| Szótár | 0                                          | 0                                        | Nincs felső korlátja                 |

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használható egy [szótár](what-is-dictionary.md) az egyéni a fordítót.
