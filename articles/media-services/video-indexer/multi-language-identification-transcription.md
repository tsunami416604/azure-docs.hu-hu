---
title: Többnyelvű tartalom automatikus azonosítása és átírása a Video Indexelő vel
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan lehet automatikusan azonosítani és átírni a többnyelvű tartalmat a Video Indexer segítségével.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72968743"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>Többnyelvű tartalom automatikus azonosítása és átírása (előzetes verzió)

A Video Indexer támogatja az automatikus nyelvi azonosítást és átírást a többnyelvű tartalmakban. Ez a folyamat magában foglalja a beszélt nyelv automatikus azonosítását a különböző szegmensekben a hangból, elküldve a médiafájl minden egyes szegmensét átírni, és egyesíteni az átírást egy egységes átiratra. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Többnyelvű azonosítás kiválasztása a portállal történő indexeléskor

A videó feltöltésekénél és indexelésekéneknél **többnyelvű észlelést** is választhatsz. Másik lehetőségként választhatja a **többnyelvű észlelést** a videó újraindexelésekénél. Az alábbi lépések az újraindexelést ismertetik:

1. Nyissa meg a [Video Indexer](https://vi.microsoft.com/) webhelyét, és jelentkezzen be.
1. Nyissa meg a **Könyvtár** lapot, és mutasson az újraindexelni kívánt videó nevére. 
1. A jobb alsó sarokban kattintson a **Videó újraindexelése** gombra. 
1. A **Videó újraindexelése** párbeszédpanelen válassza a **többnyelvű észlelést** a **Videó forrásnyelv** legördülő listaából.

    * Ha egy videót többnyelvűként indexel, az insight oldal tartalmazza ezt a lehetőséget, és egy további betekintési típus jelenik meg, amely lehetővé teszi a felhasználó számára, hogy megtekintse, melyik szegmens tanusított a "Beszélt nyelv" nyelven.
    * Az összes nyelvre történő fordítás teljes mértékben elérhető a többnyelvű átiratból.
    * Minden más betekintést fog megjelenni a mester nyelv észlelt - ez az a nyelv, amely megjelent leginkább a hang.
    * A játékoson a feliratozás többnyelven is elérhető.

![Portal-felület](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Többnyelvű azonosítás kiválasztása az API-val történő indexeléskor

Amikor indexel vagy [újraindexel](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) egy videót `multi-language detection` az API-val, válassza ki a lehetőséget a `sourceLanguage` paraméterben.

### <a name="model-output"></a>A modell kimenete

A modell egy listában letölti a videóban észlelt összes nyelvet

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Ezenkívül az átírási szakasz minden egyes példánya tartalmazni fogja azt a nyelvet, amelyen átírták

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Irányelvek és korlátozások

* Támogatott nyelvek készlete: angol, francia, német, spanyol.
* Többnyelvű tartalom támogatása legfeljebb három támogatott nyelven.
* Ha a hang a fenti támogatott listátaktól eltérő nyelveket tartalmaz, az eredmény nem várt.
* Minimális szegmenshossz az egyes nyelvek észleléséhez – 15 másodperc.
* A nyelvfelismerés eltolása átlagosan 3 másodperc.
* A beszéd várhatóan folyamatos lesz. A nyelvek közötti gyakori váltások befolyásolhatják a modellek teljesítményét.
* A nem anyanyelvi beszélők beszéde befolyásolhatja a modell teljesítményét (például amikor a hangszórók az anyanyelvüket használják, és más nyelvre váltanak).
* A modell célja, hogy ismerje el a spontán társalgási beszéd ésszerű audio akusztika (nem hangutasítások, éneklés, stb.)
* A többnyelvű videók hozása és szerkesztése jelenleg nem érhető el.
* Többnyelvű észlelés használata esetén az egyéni nyelvi modellek nem érhetők el.
* A kulcsszavak hozzáadása nem támogatott.
* A feliratfájlok exportálásakor a nyelvi jelzés nem jelenik meg.
* A frissítési átirat API nem támogat több nyelvű fájlt.

## <a name="next-steps"></a>További lépések

[A Video Indexer áttekintése](video-indexer-overview.md)
