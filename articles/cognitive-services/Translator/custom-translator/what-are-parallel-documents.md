---
title: Mik azok a párhuzamos dokumentumok? - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A párhuzamos dokumentumok olyan dokumentumok párjai, ahol az egyik a másik fordítása. A pár egyik dokumentuma a forrásnyelven lévő mondatokat, a másik pedig ezeket a mondatokat a célnyelvre fordította.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d7c38a44e3111a319e4146b3c9b71a22b0d31bfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72675468"
---
# <a name="what-are-parallel-documents"></a>Mik azok a párhuzamos dokumentumok?

A párhuzamos dokumentumok olyan dokumentumok párjai, ahol az egyik a másik fordítása. A pár egyik dokumentuma a forrásnyelven lévő mondatokat, a másik pedig ezeket a mondatokat a célnyelvre fordította.
Nem számít, hogy melyik nyelv van megjelölve "forrás", és melyik nyelv van megjelölve "cél" - egy párhuzamos dokumentum használható a fordítási rendszer betanításához mindkét irányban.

## <a name="requirements"></a>Követelmények

A rendszer betanításához legalább 10 000 egyedi, egymáshoz igazított párhuzamos mondatra lesz szükség. Ez a korlátozás egy biztonsági háló, amely biztosítja, hogy a párhuzamos mondatok elegendő egyedi szókincset tartalmaznak a fordítási modell sikeres betanításához. Ajánlott eljárásként folyamatosan adjon hozzá párhuzamos tartalmat, és képezze át a fordítási rendszer minőségének javítása érdekében. Olvassa el a [Mondatigazítás](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment)t .

A Microsoft megköveteli, hogy az Egyéni Fordítóba feltöltött dokumentumok ne sértsék meg harmadik fél szerzői vagy szellemi tulajdonát. További információt a [Használati feltételekben](https://azure.microsoft.com/support/legal/cognitive-services-terms/)talál.
A dokumentum portálon történő feltöltése nem változtatja meg magának a dokumentumnak a tulajdonjogát.

## <a name="use-of-parallel-documents"></a>Párhuzamos dokumentumok használata

A párhuzamos dokumentumokat a rendszer használja:

1.  Ha meg szeretné tudni, hogy a szavak, kifejezések és mondatok általában hogyan vannak leképezve a két nyelv között.

2.  A környező kifejezésektől függően a megfelelő környezet feldolgozásának megismerése. Előfordulhat, hogy egy szó nem mindig ugyanarra a szóra fordítható a másik nyelven.

Ajánlott eljárásként győződjön meg arról, hogy a dokumentumok forrás- és célnyelvi verziói között 1:1 mondatos levelezés van.

Ha a projekt tartományspecifikus (kategória)specifikus, a dokumentumoknak konzisztensnek kell lenniük az adott kategórián belüli terminológiában. Az eredményül kapott fordítási rendszer minősége a dokumentumkészletben lévő mondatok számától és a mondatok minőségétől függ. Minél több példát tartalmaznak a dokumentumok a kategóriára jellemző szó különböző használatával, annál jobb munkát tud végezni a rendszer a fordítás során.

A feltöltött dokumentumok minden munkaterületen privátak, és annyi projektben vagy tréningben használhatók, amennyit csak szeretne. A dokumentumokból kinyert mondatokat a rendszer külön tárolja a tárházban egyszerű Unicode szövegfájlként, és törölheti őket. Ne használja az egyéni fordítót dokumentumtárként, nem tudja letölteni a feltöltött dokumentumokat a feltöltött formátumban.



## <a name="next-steps"></a>További lépések

- További információ [aszótár](what-is-dictionary.md) használatáról az Egyéni fordítóban.
