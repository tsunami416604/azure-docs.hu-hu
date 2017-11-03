---
title: "Hozzon létre speciális kódolási munkafolyamatok a munkafolyamat-tervezővel |} Microsoft Docs"
description: "További tudnivalók a munkafolyamat-tervezővel speciális kódolási munkafolyamatok létrehozásához."
services: media-services
documentationcenter: 
author: anilmur
manager: cfowler
editor: 
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako;johndeu;anilmur
ms.openlocfilehash: ef277710713d342eb9aaaf119e2985e8c1d82d0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Speciális kódolási munkafolyamatok létrehozása a munkafolyamat-tervezővel
## <a name="overview"></a>Áttekintés
A **munkafolyamat-Tervező** Windows asztali eszköz, amellyel tervezése és készítése egyéni munkafolyamatokat kódolás **Media Encoder prémium munkafolyamat**.
A teljesítmény, a munkafolyamat-Tervező eszköz használatával megtervezésének és létrehozásának fog futni a bonyolult munkafolyamatok **Media Encoder prémium**.  

Munkafolyamatok lehetnek ügyfél döntési programot, és a bemeneti forrás fájltulajdonságok ugorhat alapján. Felülbírálható tulajdonságok és dinamikus értékekkel még a legösszetettebb kódolási feladatok megkönnyítése ismételje meg a és a felhőben testre munkafolyamatokat hozhat létre.

Példa munkafolyamatokat hozhat létre a következők:

* Döntési alapú munkafolyamatok, amelyek vizsgálhatja meg a tartalmat a feloldásához, és csak a kívánt kimeneti számok kódolása.  Ez az helfpul a feleslegesen nyomon követi, amelyek előállítják a forrás tartalom elírás upscaling által kiküszöbölése révén.
* Több bemeneti fájl segítségével támogatja a feliratok, átfedések és való együttes tartalmak. 

Ez az eszköz is segítségével módosítsa a [munkafolyamatok közzétett](media-services-workflow-designer.md#existing_workflows). 

> [!NOTE]
> Ahhoz, hogy a munkafolyamat-Tervező eszköz példányának, lépjen kapcsolatba mepd@microsoft.com.
> 
> 

Egy munkafolyamat-fájl jön létre, ha eszközként fel kell tölteni, és ezután használható a médiafájlokat kódolási. A kódolással kapcsolatos **Media Encoder prémium munkafolyamat** használatával **.NET**, lásd: [Media Encoder prémium munkafolyamat kódolás speciális](media-services-encode-with-premium-workflow.md).

## <a id="existing_workflows"></a>Módosítsa a meglévő munkafolyamatokat
Az alapértelmezett [munkafolyamatok közzétett](media-services-workflow-designer.md#existing_workflows) a Tervező eszköz használatával módosítható. Az alapértelmezett munkafolyamat-fájlok is ki [Itt](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A mappa is leírja, hogy ezeket a fájlokat tartalmazza.

A következő videók használatát mutatják be a tervezőben.

### <a name="day-1--getting-started"></a>Naponta 1 – első lépések
Naponta 1 videó ismerteti:

* Tervező áttekintése
* Alapvető munkafolyamatok – a "Hello World"
* Több létrehozása kimeneti való használathoz az Azure Media Services streaming MP4-fájlok

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>2 nap
Naponta 2 videó ismerteti:

* Forrás fájlelérési helyzetben változó – hang kezelése
* Speciális logikával munkafolyamatok
* Graph szakaszból

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Nap 3
Nap 3 videó ismerteti:

* Parancsfájl-kezelési munkafolyamatok/tervrajzokat belül
* Az aktuális Encoder korlátozások
* A KÉRDÉSEK ÉS VÁLASZOK

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

Ha támogatja az kell, vagy a munkafolyamat-Tervező eszköz egyéni munkafolyamatokat létrehozásával kapcsolatos kérdése van, kérjük, küldjön e-mailek mepd@microsoft.com.

## <a name="see-also"></a>Lásd még:
[Prémium szintű Azure kódoló munkafolyamat Tervező képzési videók](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

