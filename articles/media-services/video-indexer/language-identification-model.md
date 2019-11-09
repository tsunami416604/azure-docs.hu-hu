---
title: A beszélt nyelv automatikus azonosítása a Video Indexer-Azure-ban
titleSuffix: Azure Media Services
description: Ez a cikk azt ismerteti, hogyan használható a Video Indexer nyelvi azonosító modell a beszélt nyelvnek a videóban való automatikus azonosítására.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: ce3e488a6387f9a823d7c1b514b52af24944776b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838996"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>A beszélt nyelv automatikus azonosítása nyelvi azonosító modellel

A Video Indexer támogatja az automatikus nyelvi azonosítást (FEDŐt), amely automatikusan azonosítja a beszélt nyelvi tartalmakat a hangból, és elküldi a médiafájlt a dominánsan azonosított nyelven. A fedél jelenleg angol, spanyol, francia, német, olasz, Kínai (egyszerűsített), Japán, orosz és portugál (brazíliai) változatot támogat. 

## <a name="choosing-auto-language-identification-on-indexing"></a>Automatikus nyelvi azonosítás kiválasztása az indexeléshez

Ha az API használatával indexel vagy [újraindexel](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) egy videót, válassza a `auto detect` lehetőséget a `sourceLanguage` paraméterben.

A portál használatakor a [video Indexer](https://www.videoindexer.ai/) kezdőlapján keresse meg a **fiókja videóit** , és vigye az egérmutatót az újra indexelni kívánt videó nevére. A jobb alsó sarokban kattintson az újra index gombra. A **videó újraindexelése** párbeszédpanelen válassza az *automatikus észlelés* lehetőséget a **videó forrása nyelv** legördülő listából.

![automatikus észlelés](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Modell kimenete

Video Indexer a videót a legvalószínűbb nyelvnek megfelelően, ha az adott nyelv megbízhatósága `> 0.6`. Ha a nyelv nem azonosítható magabiztosan, feltételezi, hogy a beszélt nyelv angol. 

A modell domináns nyelve a `sourceLanguage` attribútumként érhető el a betekintő JSON-ben (a root/videos/bepillantás alatt). A megfelelő megbízhatósági pontszám a `sourceLanguageConfidence` attribútum alatt is elérhető.

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

* A támogatott nyelvek közé tartoznak a következők: angol, spanyol, francia, német, olasz, Kínai (egyszerűsített), Japán, orosz és brazíliai portugál.
* Ha a hang a fenti támogatott listán kívül más nyelveket is tartalmaz, az eredmény nem várt.
* Ha Video Indexer nem tudja azonosítani a nyelvet elég magas megbízhatósággal (`>0.6`), a tartalék nyelv angol.
* A vegyes nyelvi hanggal rendelkező fájlok jelenleg nem támogatottak. Ha a hang vegyes nyelveket tartalmaz, az eredmény nem várt. 
* Az alacsony minőségű hang befolyásolhatja a modell eredményeit.
* A modellhez legalább egy perces beszéd szükséges a hanganyagban.
* A modell úgy lett kialakítva, hogy felismerje a spontán társalgási beszédeket (nem hangparancsokat, éneklést stb.).

## <a name="next-steps"></a>További lépések

* [Áttekintés](video-indexer-overview.md)
* [Többnyelvű tartalom automatikus azonosítása és átírása](multi-language-identification-transcription.md)
