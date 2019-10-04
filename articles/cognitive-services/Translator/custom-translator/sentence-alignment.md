---
title: Mondat párosítása és igazítása – egyéni fordító
titleSuffix: Azure Cognitive Services
description: A betanítás végrehajtása során a párhuzamos dokumentumokban lévő mondatok párosítva vannak vagy igazíthatók. Az egyéni fordító egyszerre tanul egy mondatot a mondatok beolvasásával, a mondat fordításával. Ezután a két mondatban található szavakat és kifejezéseket egymáshoz igazítja.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: e9bc5c876da6bd2be1b22b389b819e51330b2e50
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595461"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Mondat párosítása és igazítása párhuzamos dokumentumokban

A képzés során a párhuzamos dokumentumokban lévő mondatok párosítva vannak vagy illeszkednek egymáshoz. Az egyéni fordító jelentést készít az egyes adatkészletek igazított mondatait tartalmazó mondatok számáról.

## <a name="pairing-and-alignment-process"></a>Párosítási és igazítási folyamat

Az egyéni fordító egyszerre tanulja meg a mondatok egy mondatának fordítását. Beolvasott egy mondatot a forrástól, majd a mondat fordítását a célhelyről. Ezután a két mondatban található szavakat és kifejezéseket egymáshoz igazítja. Ez a folyamat lehetővé teszi, hogy egy mondatban hozza létre a szavakat és kifejezéseket tartalmazó térképet az ennek a mondatnak a fordításában szereplő egyenértékű szavakra és kifejezésekre. Az igazítás megkísérli biztosítani, hogy a rendszer olyan mondatokat használjon, amelyek egymást fordítanak.

## <a name="pre-aligned-documents"></a>Előre igazított dokumentumok

Ha tudja, hogy rendelkezik párhuzamos dokumentumokkal, felülbírálhatja a mondatok igazítását az előre igazított szövegfájlok beszerzésével. Mindkét mondatot kinyerheti szövegfájlba, soronként egy mondatot, és egy `.align` kiterjesztéssel feltöltheti a fájlt. A `.align` bővítmény egyéni fordítóként jelzi, hogy ki kell hagyni a mondatok igazítását.

A legjobb eredmények érdekében érdemes meggyőződni arról, hogy soronként egy mondat van a fájlokban. Egy mondaton belül ne legyen sortörés karakter, mert ez gyenge igazítást okoz.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>A kinyert és igazított mondatok minimális száma javasolt

Ahhoz, hogy egy képzés sikeres legyen, az alábbi táblázat a kinyert mondatok és az egyes adatkészletekben szükséges igazított mondatok minimális számát mutatja. A kinyert mondatok javasolt minimális száma sokkal nagyobb, mint a javasolt minimális számú, igazított mondatok, amelyek figyelembe veszik, hogy a mondat igazítása nem képes az összes kinyert mondatot sikeresen összehangolni.

| Adathalmaz   | Javasolt minimális kinyert mondatok száma | Javasolt minimálisan igazított mondatok száma | Igazított mondatok maximális száma |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| Képzés   | 10,000                                     | 2,000                                    | Nincs felső korlát                 |
| Tuning     | 2,000                                      | 500                                      | 2,500                          |
| Tesztelés    | 2,000                                      | 500                                      | 2,500                          |
| Szótár | 0                                          | 0                                        | Nincs felső korlát                 |

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [](what-is-dictionary.md) használhat szótárt az egyéni fordítóban.
