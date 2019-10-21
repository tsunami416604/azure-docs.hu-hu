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
ms.openlocfilehash: adbc21c3e963a98a8482de0c26bf5e257f43013e
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675458"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Mondat párosítása és igazítása párhuzamos dokumentumokban

A képzés során a párhuzamos dokumentumokban lévő mondatok párosítva vannak vagy illeszkednek egymáshoz. Az egyéni fordító jelentést készít az egyes adatkészletek igazított mondatait tartalmazó mondatok számáról.

## <a name="pairing-and-alignment-process"></a>Párosítási és igazítási folyamat

Az egyéni fordító egyszerre tanulja meg a mondatok egy mondatának fordítását. Beolvasott egy mondatot a forrástól, majd a mondat fordítását a célhelyről. Ezután a két mondatban található szavakat és kifejezéseket egymáshoz igazítja. Ez a folyamat lehetővé teszi, hogy egy mondatban hozza létre a szavakat és kifejezéseket tartalmazó térképet az ennek a mondatnak a fordításában szereplő egyenértékű szavakra és kifejezésekre. Az igazítás megkísérli biztosítani, hogy a rendszer olyan mondatokat használjon, amelyek egymást fordítanak.

## <a name="pre-aligned-documents"></a>Előre igazított dokumentumok

Ha tudja, hogy rendelkezik párhuzamos dokumentumokkal, felülbírálhatja a mondatok igazítását az előre igazított szövegfájlok beszerzésével. Mindkét mondatot kinyerheti szövegfájlba, soronként egy mondatot, és feltöltheti `.align` bővítménnyel. A `.align` bővítmény egyéni fordítóként jelzi, hogy ki kell hagyni a mondatok igazítását.

A legjobb eredmények érdekében érdemes meggyőződni arról, hogy soronként egy mondat van a fájlokban. Egy mondaton belül ne legyen sortörés karakter, mert ez gyenge igazítást okoz.

## <a name="suggested-minimum-number-of-sentences"></a>Javasolt minimális számú mondat

Ahhoz, hogy egy képzés sikeres legyen, az alábbi táblázat az egyes dokumentum-típusokban szükséges mondatok minimális számát mutatja. Ez a korlátozás egy biztonsági háló, amely biztosítja, hogy a párhuzamos mondatok elég egyedi szókincset tartalmazzanak a fordítási modell sikeres betanításához. Az általános útmutatóban az emberi fordítási minőség több tartományra kiterjedő párhuzamos mondata is magasabb színvonalú modelleket eredményezhet.

| Dokumentumtípusok   | Javasolt minimális mondatok száma | Mondatok maximális száma |
|------------|--------------------------------------------|--------------------------------|
| Képzés   | 10,000                                     | Nincs felső korlát                 |
| Tuning     | 5000                                      | 2 500                          |
| Tesztelés    | 5000                                      | 2 500                          |
| Szótár | 0                                          | Nincs felső korlát                 |

> [!NOTE]
> - A képzés nem indul el, és sikertelen lesz, ha a betanításhoz a 10 000-es minimális számú mondat nem teljesül. 
> - A hangolás és a tesztelés nem kötelező. Ha nem adja meg őket, a rendszer eltávolítja a megfelelő százalékot az ellenőrzéshez és teszteléshez használt képzésből. 
> - A modelleket csak a szótárak adatai alapján lehet betanítani. Tekintse meg a [Mi az a szótár](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary)című témakört.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan használhat [szótárt](what-is-dictionary.md) az egyéni fordítóban.
