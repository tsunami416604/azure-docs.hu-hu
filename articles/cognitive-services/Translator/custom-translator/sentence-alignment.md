---
title: Mondatpárosítás és -igazítás - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A betanítás végrehajtása során a párhuzamos dokumentumokban lévő mondatok párosítva vannak vagy igazítva. Egyéni fordító megtanulja fordítások egy mondatot egy időben, elolvasva egy mondatot, a fordítás ezt a mondatot. Ezután a szavakat és kifejezéseket e két mondatban egymáshoz igazítja.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf5b2b84142c9104ea5b3afa3ad179fd0ec07449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80370142"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Mondatpárosítás és -igazítás párhuzamos dokumentumokban

A képzés során a párhuzamos dokumentumokban lévő mondatokat párosítja vagy igazítja. Egyéni fordító jelenti a mondatok számát, hogy képes volt párosítani, mint az igazított mondatok az egyes adatkészletek.

## <a name="pairing-and-alignment-process"></a>Párosítási és igazítási folyamat

Egyéni fordító megtanulja a mondatok fordítását egy mondattal egy időben. Egy mondatot olvas fel a forrásból, majd ennek a mondatnak a lefordítását a célpontból. Ezután a szavakat és kifejezéseket e két mondatban egymáshoz igazítja. Ez a folyamat lehetővé teszi, hogy egy mondatban készítsen térképet a szavakról és kifejezésekről az egyenértékű szavakhoz és kifejezésekhez e mondat fordításában. Az igazítás megpróbálja biztosítani, hogy a rendszer olyan mondatokra igazodjon, amelyek egymás fordításai.

## <a name="pre-aligned-documents"></a>Előre igazított dokumentumok

Ha tudja, hogy vannak párhuzamos dokumentumai, felülbírálhatja a mondatigazítást előre igazított szövegfájlok biztosításával. Mindkét dokumentum összes mondatát kibonthatja szöveges fájlba, soronként egy `.align` mondatot rendszerezhet, és kiterjesztéssel töltheti fel. A `.align` bővítmény jelzi az egyéni fordítónak, hogy ki kell hagynia a mondatigazítást.

A legjobb eredmény érdekében győződjön meg arról, hogy soronként egy mondat van a fájlokban.Ne legyenek új sorkarakterek egy mondatban, mert ez rossz igazításokat okoz.

## <a name="suggested-minimum-number-of-sentences"></a>Javasolt minimális számú mondat

Ahhoz, hogy a képzés sikeres legyen, az alábbi táblázat az egyes dokumentumtípusokhoz szükséges mondatok minimális számát mutatja.Ez a korlátozás egy biztonsági háló, amely biztosítja, hogy a párhuzamos mondatok elegendő egyedi szókincset tartalmaznak a fordítási modell sikeres betanításához. Az általános iránymutatás szerint az emberi fordítás minőségének több, a domainen belüli párhuzamos mondatnak jobb minőségű modelleket kell eredményeznie.

| Dokumentum típusa   | Javasolt minimális mondatszám | Maximális büntetésszám |
|------------|--------------------------------------------|--------------------------------|
| Képzés   | 10,000                                     | Nincs felső határ                 |
| Tuning     | 500                                      | 2500       |
| Tesztelés    | 500                                      | 2500  |
| Szótár | 0                                          | Nincs felső határ                 |

> [!NOTE]
> - Képzés nem indul el, és nem fog sikerülni, ha a 10.000 minimális büntetés számít képzés nem teljesül. 
> - A hangolás és tesztelés nem kötelező. Ha nem adja meg őket, a rendszer eltávolítja a megfelelő százalékot a képzésből az érvényesítéshez és teszteléshez. 
> - A modell csak szótáradatok használatával tanítható be. Kérjük, olvassa el [a Mi a szótár](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary).

## <a name="next-steps"></a>További lépések

- További információ [aszótár](what-is-dictionary.md) használatáról az Egyéni fordítóban.
