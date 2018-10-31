---
title: Speciális kódolási munkafolyamatok létrehozása a munkafolyamat-tervezővel |} A Microsoft Docs
description: Ismerje meg a munkafolyamat-tervezővel speciális kódolási munkafolyamatok létrehozása.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako;johndeu;anilmur
ms.openlocfilehash: 1521d18ab94c647aebf303b660574afe6612474d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250512"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Speciális kódolási munkafolyamatok létrehozása a munkafolyamat-tervezővel
## <a name="overview"></a>Áttekintés
A **munkafolyamat-tervezővel** egy Windows asztali eszköz, amellyel tervezése és létrehozása egyéni munkafolyamatokat az Encoding **Media Encoder Premium munkafolyamat**.
A teljesítmény, a munkafolyamat-Tervező eszköz segítségével tervezheti meg és komplex, amely munkafolyamatokat hozhat létre **prémium szintű Media Encoder**.  

Munkafolyamatok ügyfél döntési logikát tartalmazhatnak, és a bemeneti forrásfájl Tulajdonságok elágaztatás alapján. Felülbírálható tulajdonságok és a dinamikus értékeket, hogy még a legösszetettebb kódolási feladatok egyszerűen ismételje meg a, és testre szabhatja a felhőben munkafolyamatokat hozhat létre.

A példában a munkafolyamatok hozhatja létre a következők:

* Döntési-alapú munkafolyamatok, amelyek névfeloldási forrása tartalmának vizsgálata, és csak a kívánt kimeneti számok kódolása.  Ez a helfpul az elpazarolt nyomon követi a forrás-tartalom elírás upscaling szerint fognak létrejönni kiküszöbölése révén.
* Több bemeneti fájllal a feliratok, átfedések és való együttes tartalom használható. 

Ezzel az eszközzel is használható, módosíthatja a [munkafolyamatok közzétett](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> A munkafolyamat-Tervező eszköz példányának lekéréséhez lépjen kapcsolatba mepd@microsoft.com.
> 
> 

Egy munkafolyamat-fájl létrehozása után eszközként tölthet fel, és ezután médiafájlok a kódoláshoz használható. Információk a kódolással **Media Encoder Premium munkafolyamat** használatával **.NET**, lásd: [speciális kódolás a Media Encoder Premium munkafolyamat](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Módosítsa a meglévő munkafolyamatokat
Az alapértelmezett [munkafolyamatok közzétett](media-services-workflow-designer.md#existing_workflows) a Tervező eszköz használatával módosítható. Megjelenik az alapértelmezett munkafolyamat-fájlok [Itt](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A mappa leírását, ezeket a fájlokat is tartalmaz.

A következő videók bemutatják, hogyan lehet a tervező.

### <a name="day-1--getting-started"></a>Naponta 1 – első lépések
1. napi videó mutatja be:

* Modelltervező – áttekintés
* Alapszintű munkafolyamatok – a "Hello World"
* Több létrehozása kimeneti MP4-fájlokat az Azure Media Services streaming való használatra

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>2. napon
2. napon a videó ismerteti:

* Különböző forrás fájlelérési helyzetben – hang kezelése
* Fejlett logika munkafolyamatok
* Graph szakaszai

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>3 nap
Naponta 3 videó mutatja be:

* Parancsfájl-kezelési munkafolyamatok/tervezetek belül
* A jelenlegi kódoló vonatkozó korlátozások
* A Q &AMP; A

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

Ha a támogatási van szüksége, vagy a munkafolyamat-Tervező eszköz egyéni munkafolyamatok létrehozásával kapcsolatos kérdése van, küldje el e-mailek mepd@microsoft.com.

## <a name="see-also"></a>Lásd még:
[Az Azure prémium szintű kódoló munkafolyamat Tervező képzési videók](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

