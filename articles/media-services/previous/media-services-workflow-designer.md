---
title: Speciális kódolási munkafolyamatok létrehozása a Munkafolyamat-tervezővel | Microsoft dokumentumok
description: További információ arról, hogyan hozhat létre speciális kódolási munkafolyamatokat a Munkafolyamat-tervezővel.
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
ms.date: 03/20/2019
ms.author: anilmur
ms.reviewer: juliako;johndeu
ms.openlocfilehash: 4dceb558532305c6d2e84563e25ab05508423090
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72801955"
---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>Speciális kódolási munkafolyamatok létrehozása a munkafolyamat-tervezővel  
## <a name="overview"></a>Áttekintés
A **Workflow Designer** egy asztali Windows-eszköz, amely a Media **Encoder Premium Workflow**segítségével történő kódoláshoz egyéni munkafolyamatok tervezésére és létrehozására szolgál.
A munkafolyamat-tervező eszköz segítségével összetett munkafolyamatokat tervezhet és hozhat létre, amelyek a **Media Encoder Premium**programban futnak.  

A munkafolyamatok magukban foglalhatják az ügyféldöntési logikát és a bemeneti forrásfájl tulajdonságai alapján történő elágazást. A túlságosan megalható tulajdonságokkal és dinamikus értékekkel rendelkező munkafolyamatokat hozhat létre, hogy még a legösszetettebb kódolási feladatok is könnyen megismételhetők és testreszabhatók legyenek a felhőben.

Példaként létrehozható munkafolyamatokra:

* Döntésalapú munkafolyamatok, amelyek a forrástartalmat ellenőrzik megoldásért, és csak a kívánt kimeneti sávokat kódolják.  Ez akkor hasznos, ha kiküszöböli azokat az elpazarolt számokat, amelyeket a forrástartalom véletlen felskálázása generálna.
* Több bemeneti fájl használható a feliratok, átfedések és a tartalom összefűzésének támogatására. 

Ez az eszköz bármely [közzétett munkafolyamatunk módosítására](media-services-workflow-designer.md#existing_workflows)is használható. 

> [!NOTE]
> A Munkafolyamat-tervező eszköz másolatának bemásolásához lépjen kapcsolatba a programmal. mepd@microsoft.com

A munkafolyamat-fájl létrehozása után eszközként tölthető fel, majd médiafájlok kódolására használható. A **Media Encoder Premium Workflow** használatával a **.NET**használatával történő kódolásról a [Speciális kódolás a Media Encoder Premium Workflow használatával című](media-services-encode-with-premium-workflow.md)témakörben talál.

## <a name="modify-existing-workflows"></a><a id="existing_workflows"></a>Meglévő munkafolyamatok módosítása
Az alapértelmezett közzétett munkafolyamatok a tervezőeszközzel [módosíthatók.](media-services-workflow-designer.md#existing_workflows) Az alapértelmezett munkafolyamat-fájlokat [itt](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)kaphatja meg. A mappa tartalmazza a fájlok leírását is.

Az alábbi videók bemutatják, hogyan kell használni a tervezőt.

### <a name="day-1--getting-started"></a>1. nap – Első lépések
1. nap videó borítók:

* Tervező – áttekintés
* Alapvető munkafolyamatok – "Hello World"
* Több kimeneti MP4-fájl létrehozása az Azure Media Services adatfolyam-továbbításához

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>2. nap
2. nap videó borítók:

* Különböző forrásfájl-forgatókönyvek – hangkezelés
* Speciális logikával rendelkező munkafolyamatok
* Diagram szakaszai

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>3. nap
3. nap videó borítók:

* Parancsfájlok írása a munkafolyamatokban/tervrajzokon belül
* Korlátozások az aktuális kódolóval
* Kérdések és válaszok

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="need-help"></a>Segítségre van szüksége?

Támogatási jegyet úgy nyithat meg, hogy az [Új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) navigál.

## <a name="next-step"></a>Következő lépés
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Azure Premium Kódoló munkafolyamat-tervezői oktatóvideói](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)

