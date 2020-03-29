---
title: Gyakori kérdések - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services egyéni fordítóval kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: 97d399f4a8ec704fd90eb6c49f0835be7e9e4537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836599"
---
# <a name="custom-translator-frequently-asked-questions"></a>Egyéni fordító gyakori kérdések

Ez a cikk az egyéni [fordítóval](https://portal.customtranslator.azure.ai)kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Mik a jelenlegi korlátozások az egyéni fordítóban?

A fájlméretre, a modellbetanításra és a modellüzembe helyezésre vonatkozókorlátozások és korlátozások vannak érvényben. Tartsa szem előtt ezeket a korlátozásokat, amikor beállítja a betanítást, hogy hozzon létre egy modellt az egyéni fordítóban.

- A beküldött fájlok méretének 100 MB-nál kisebbnek kell lennie.
- Az egynyelvű adatok nem támogatottak.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Mikor kell kérni a telepítést egy betanított fordítási rendszerhez?

Több képzésre is eltarthat, hogy létrehozzák a projekt optimális fordítási rendszerét. Ha a BLEU pontszám és/ vagy a vizsgálati eredmények nem kielégítőek, érdemes több betanítási vagy gondosan szűrt adatot használni. Szigorúnak és óvatosnak kell lennie a hangolási készlet és a tesztkészlet megtervezésében, hogy teljes mértékben reprezentálja a lefordítani kívánt anyag terminológiáját és stílusát. Liberálisabb lehet a betanítási adatok összeállításában, és kísérletezhet a különböző lehetőségekkel. Szükség van egy rendszer központi telepítését, ha elégedett a fordítások a rendszer vizsgálati eredmények, nincs több adatot hozzáadni a betanítási a betanított rendszer javítása érdekében, és szeretné elérni a betanított modell API-kon keresztül.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Hány képzett rendszer telepíthető egy projektben?

Projektenként csak egy betanított rendszer telepíthető. Több képzésre is szüksége lehet a megfelelő fordítási rendszer létrehozásához a projekthez, és javasoljuk, hogy kérjen egy olyan képzést, amely a legjobb eredményt adja. Meghatározhatja a képzés minőségét a BLEU pontszám (magasabb jobb), és konzultálanak a véleményezők, mielőtt úgy dönt, hogy a fordítások minősége alkalmas a telepítésre.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Mikorra várható a kiképzésem bevetése?

A központi telepítés általában kevesebb, mint egy órát vesz igénybe.

## <a name="how-do-you-access-a-deployed-system"></a>Hogyan lehet hozzáférni egy telepített rendszerhez?

A telepített rendszerek a Microsoft Translator Text API V3-on keresztül érhetők el a CategoryID megadásával. A Translator Text API-ról további információ az [API referencia](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) weblapján található.

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Hogyan hagyhatom ki a igazítást és a mondattörést, ha az adataim már egy vonalban vannak?

Az egyéni fordító kihagyja a mondatigazítást és a mondattörést `.align` a TMX fájlokés a kiterjesztésű szövegfájlok esetében. `.align`fájlokat ad használók egy választás -hoz ugrál Szokás Fordító' mondat ablakbetörés és igazítás folyamat részére a fájlokat amit van teljesen igazodott, és szükség nem tovább feldolgozás. Javasoljuk, `.align` hogy csak a tökéletesen beállított fájlokhoz használjon kiterjesztést.

Ha a kibontott mondatok száma nem egyezik meg az azonos alapnevű két `.align` fájllal, az Egyéni fordító továbbra is futtatja a mondatigazítót a fájlokon.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Próbáltam feltölteni a TMX, de azt mondja: "dokumentum feldolgozás nem sikerült".

Győződjön meg arról, hogy a TMX 1.4b specifikációnak megfelel a . <https://www.gala-global.org/tmx-14b>
