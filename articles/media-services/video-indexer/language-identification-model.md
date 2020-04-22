---
title: A Video Indexelő használata a beszélt nyelvek automatikus azonosításához - Azure
titleSuffix: Azure Media Services
description: Ez a cikk azt ismerteti, hogy a videóindexelő nyelvazonosító modellje hogyan használható a videó ban a beszélt nyelv automatikus azonosítására.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687124"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>A beszélt nyelv automatikus azonosítása a nyelvazonosító modellel

A Video Indexer támogatja az automatikus nyelvazonosítást (LID), amely a beszélt nyelvű tartalom automatikus azonosításának és a médiafájl domináns azonosított nyelven történő átírásának folyamata. 

Jelenleg a LID támogatja: angol, spanyol, francia, német, olasz, mandarin kínai, japán, orosz és portugál (brazil). 

Mindenképpen tekintse át az alábbi [Irányelvek és korlátozások](#guidelines-and-limitations) című szakaszt.

## <a name="choosing-auto-language-identification-on-indexing"></a>Automatikus nyelvazonosító kiválasztása indexeléskor

Amikor indexel vagy újra indexel egy videót az [API-val,](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) válassza ki a `auto detect` lehetőséget a `sourceLanguage` paraméterben.

A portál használatakor nyissa meg **a Fiókvideóit** a [Video Indexer](https://www.videoindexer.ai/) kezdőlapján, és mutasson az újraindexelni kívánt videó nevére. A jobb alsó sarokban kattintson az újraindexgombra. A **Videó újraindexelése** párbeszédpanelen válassza az *Automatikus észlelés* lehetőséget a **Videó forrásnyelve** legördülő lista ból.

![automatikus észlelés](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>A modell kimenete

Video Indexer átírja a videót szerint a legvalószínűbb nyelvet, `> 0.6`ha a bizalom az adott nyelv . Ha a nyelv nem azonosítható bizalommal, akkor azt feltételezi, hogy a beszélt nyelv az angol. 

Modell domináns nyelv érhető el az insights `sourceLanguage` JSON attribútumként (a root/videos/insights). A megfelelő megbízhatósági pontszám `sourceLanguageConfidence` is elérhető az attribútum alatt.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Irányelvek és korlátozások

* Az automatikus nyelvazonosító (LID) a következő nyelveket támogatja: 

    Angol, spanyol, francia, német, olasz, mandarin kínai, japán, orosz és portugál (brazil).
* Annak ellenére, hogy a Video Indexer támogatja az arab (modern szabvány és levantein), hindi és koreai nyelvet, ezeket a nyelveket a LID nem támogatja.
* Ha a hang a fenti támogatott listátaktól eltérő nyelveket tartalmaz, az eredmény nem várt.
* Ha a Video Indexer nem tudja azonosítani`>0.6`a nyelvet elég nagy megbízhatósággal ( ), a tartalék nyelv az angol.
* A vegyes nyelvű hanggal rendelkező fájlok jelenleg nem támogatottak. Ha a hang vegyes nyelveket tartalmaz, az eredmény váratlan. 
* Az alacsony minőségű hang hatással lehet a modell eredményére.
* A modell legalább egy perces beszédfelismerést igényel a hangban.
* A modell célja, hogy ismerje el a spontán társalgási beszéd (nem hangutasítások, éneklés, stb.)

## <a name="next-steps"></a>További lépések

* [Áttekintés](video-indexer-overview.md)
* [Többnyelvű tartalom automatikus azonosítása és átírása](multi-language-identification-transcription.md)
