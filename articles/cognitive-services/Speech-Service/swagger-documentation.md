---
title: Hencegő dokumentáció – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A kivágási dokumentáció használatával számos programozási nyelvhez automatikusan létrehozhat SDK-kat. A szolgáltatás minden műveletét a hencegés támogatja
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: fcc43caf895dadfaf832a47c3254f9b828bcb71a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "77430805"
---
# <a name="swagger-documentation"></a>A Swagger dokumentációja

A beszédfelismerési szolgáltatás olyan hencegő specifikációt kínál, amely az adatok importálására, a modellek létrehozására, a modell pontosságára, az egyéni végpontok létrehozására, a Batch-átírások várólistára helyezésére és az előfizetések kezelésére szolgál. A Custom Speech-portálon keresztül elérhető legtöbb művelet programozott módon is elvégezhető az API-k használatával.

> [!NOTE]
> A beszéd-szöveg és a szövegről beszédre irányuló műveletek egyaránt elérhetők REST API-kként, amelyek viszont a hencegés specifikációjában vannak dokumentálva.

## <a name="generating-code-from-the-swagger-specification"></a>Kód generálása a hencegés specifikációból

A [hencegés specifikációja](https://cris.ai/swagger/ui/index) olyan lehetőségeket kínál, amelyekkel gyorsan tesztelheti a különböző útvonalakat. Előfordulhat azonban, hogy minden elérési útra vonatkozóan létre kell hoznia egy kódot, így egyetlen, a jövőbeli megoldások alapjául szolgáló hívásokat hozhat létre. Vessünk egy pillantást a Python-könyvtár létrehozási folyamatára.

Be kell állítania a hencegő szolgáltatást ugyanahhoz a régióhoz, mint a beszédfelismerési szolgáltatás előfizetése. A régiót a Speech Service-erőforrás alatt lévő Azure Portal ellenőrizheti. A támogatott régiók teljes listájáért lásd: [régiók](regions.md).

1. Nyissa meg a következőt: https://editor.swagger.io
2. Kattintson a **fájl**, majd az **Importálás** elemre.
3. Adja meg a felvágási URL-címet, beleértve a Speech Service-előfizetés régióját`https://<your-region>.cris.ai/docs/v2.0/swagger`
4. Kattintson az **ügyfél előállítása** és a Python elemre.
5. Az ügyféloldali kódtár mentése

Használhatja a [githubon a Speech Service-mintákkal](https://aka.ms/csspeech/samples)generált Python-könyvtárat.

## <a name="reference-docs"></a>Dokumentációs dokumentumok

* [REST (hencegés): kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)
* [REST API: beszéd – szöveg](rest-speech-to-text.md)
* [REST API: szövegről beszédre](rest-text-to-speech.md)

## <a name="next-steps"></a>További lépések

* [Speech Service-minták a githubon](https://aka.ms/csspeech/samples).
* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](get-started.md)
