---
title: Gyakori kérdések – egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Az egyéni a fordítót kapcsolatos gyakori kérdésekre adott válaszokat biztosít.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: reference
ms.openlocfilehash: b09b2cd3a7a642debb28de7ce71a2b3af4f558a8
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730257"
---
# <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a cikk gyakori kérdésekre adott válaszokat tartalmazza kapcsolatos [egyéni a fordítót](https://portal.customtranslator.azure.ai).

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Mik azok az egyéni a fordítót a jelenlegi korlátozások?

Nincsenek korlátozások és a fájlméret, a modell betanítása és a modell-üzembehelyezés korlátok. Ne feledje ezeket a korlátozásokat állítson be a tanítási modell létrehozása az egyéni a fordítót a.

- Elküldött fájlok mérete 100 MB-nál kisebbnek kell lennie.
- Monolingual adatok nem támogatott.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>Amikor igényeljen egy fordítási rendszer, amely rendelkezik betanítva üzembe?

Az optimális fordítási rendszerét a projekt létrehozásához több betanítások eltarthat. Előfordulhat, hogy szeretné próbálni a további betanítási adatok vagy több gondosan a szűrt adatokat, ha a BLEU pontszám és / vagy a vizsgálati eredmények nem kielégítő. Szigorú kell és gondos kialakítani a beállítási csoportot és a teszt állítsa be, teljes körűen reprezentatívnak a terminológia és fordítandó anyag stílusát. Kell több szabad a összeállítása a betanítási adatok, és kísérletezzen a különböző lehetőségeket. Központi telepítés rendszer kérése, ha elégedett a fordítások a rendszer vizsgálati eredmények között, a képzés a betanított rendszer javítása érdekében hozzá további adatokat nem, és szeretné elérni a betanított modell API-kon keresztül.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>Hány betanított rendszereket is telepíthető egy projektet?

Csak egy betanított rendszer projektenként is telepíthető. A projektnek megfelelő fordítási rendszer létrehozása több betanítások eltarthat, és azt javasoljuk, hogy a központi telepítését, amely biztosítja a legjobb eredmény képzési kérése. A betanítási minőségét segítségével meghatározhatja a BLEU pontszám által (nagyobb érték jobb), és annak eldöntésében, hogy a fordítások minőségét ideális választás a központi telepítés előtt a elbírálóival consulting által.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>Mikor várható saját betanítások üzembe helyezni?

Az üzembe helyezés általában egy óránál rövidebb ideig tart.

## <a name="how-do-you-access-a-deployed-system"></a>Hogyan el a telepített rendszer?

Telepített rendszerek a CategoryID megadásával elérhetők a Microsoft Translator Text API V3-n keresztül. A Translator Text API további információ található a [API-referencia](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) weblapon.

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>Hogyan kihagyása igazítás és használhatatlanná tévő, ha az adatok már igazítva mondat mondat?

Az egyéni a fordítót kihagyja a mondat igazítás és mondat kompatibilitástörő TMX fájlokat és a szöveges fájlokat a `.align` bővítmény. `.align` fájlok felhasználóknak engedélyezi a használhatatlanná tévő egyéni a fordítót mondat és igazítási folyamat, amely tökéletesen igazított, és nincs további feldolgozás szükséges fájlok kihagyása lehetőség. Azt javasoljuk, `.align` csak olyan fájlok, tökéletesen igazított kiterjesztése.

Kinyert mondatokat száma nem egyezik a két fájlt alap ugyanazzal a névvel, ha egyéni a fordítót továbbra is futtatni fogják a mondat aligner `.align` fájlokat.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>Megpróbáltam saját TMX feltöltése, de ugyanakkor "dokumentumfeldolgozást sikertelen".

Győződjön meg arról, hogy megfelel-e a TMX a TMX 1.4b specifikációt az <https://www.gala-global.org/tmx-14b>.
