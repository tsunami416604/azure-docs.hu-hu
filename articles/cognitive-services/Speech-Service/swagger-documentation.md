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
ms.openlocfilehash: 30e8224b6cb757f044a5eac598d834cee838391e
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629981"
---
# <a name="swagger-documentation"></a>A Swagger dokumentációja

A beszédfelismerési szolgáltatás olyan hencegő specifikációt kínál, amely az adatok importálására, a modellek létrehozására, a modell pontosságára, az egyéni végpontok létrehozására, a Batch-átírások várólistára helyezésére és az előfizetések kezelésére szolgál. A Custom Speech-portálon keresztül elérhető legtöbb művelet programozott módon is elvégezhető az API-k használatával.

> [!NOTE]
> A beszéd-szöveg és a szövegről beszédre irányuló műveletek egyaránt elérhetők REST API-kként, amelyek viszont a hencegés specifikációjában vannak dokumentálva.

## <a name="generating-code-from-the-swagger-specification"></a>Kód generálása a hencegés specifikációból

A [hencegés specifikációja](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) olyan lehetőségeket kínál, amelyekkel gyorsan tesztelheti a különböző útvonalakat. Előfordulhat azonban, hogy minden elérési útra vonatkozóan létre kell hoznia egy kódot, így egyetlen, a jövőbeli megoldások alapjául szolgáló hívásokat hozhat létre. Vessünk egy pillantást a Python-könyvtár létrehozási folyamatára.

Be kell állítania a hencegő szolgáltatást ugyanahhoz a régióhoz, mint a beszédfelismerési szolgáltatás előfizetése. A régiót a Speech Service-erőforrás alatt lévő Azure Portal ellenőrizheti. A támogatott régiók teljes listájáért lásd: [régiók](regions.md).

1. A böngészőben nyissa meg a terület hencegő specifikációját `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0` . Az oldalon kattintson az **API-definíció**elemre, majd kattintson a **hencegés**elemre. Másolja a megjelenő lap URL-címét.
1. Új böngészőben nyissa meg a következőt: https://editor.swagger.io
1. Kattintson a **fájl**menü **URL importálása**parancsára, illessze be az URL-címet, majd kattintson **az OK**gombra.
1. Kattintson az **ügyfél előállítása** és a **Python**elemre. Az ügyféloldali kódtár letölti a számítógépet.

Használhatja a [githubon a Speech Service-mintákkal](https://aka.ms/csspeech/samples)generált Python-könyvtárat.

## <a name="reference-docs"></a>Dokumentációs dokumentumok

* [REST (hencegés): kötegelt átírás és testreszabás](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [REST API: beszéd – szöveg](rest-speech-to-text.md)
* [REST API: szövegről beszédre](rest-text-to-speech.md)

## <a name="next-steps"></a>További lépések

* [Speech Service-minták a githubon](https://aka.ms/csspeech/samples).
* [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](overview.md#try-the-speech-service-for-free)
