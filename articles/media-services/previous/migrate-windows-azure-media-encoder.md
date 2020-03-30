---
title: Áttelepítés a Windows Azure Media Kódolóról a Media Encoder Standard szolgáltatásra | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan lehet áttelepíteni az Azure Media Kódolóa a Media Encoder Standard médiaprocesszor.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: e75e3f3eecf6c34050aeaa7fe387fffb0de58a74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513201"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Áttelepítés a Windows Azure Media Kódolóról a Media Encoder Standard szolgáltatásra

Ez a cikk az örökölt (kivonandó) (WAME) médiaprocesszorról a Media Encoder Standard médiaprocesszorra való áttelepítés lépéseit ismerteti. A nyugdíjazási dátumokat lásd az [örökölt összetevők](legacy-components.md) témakörben.

A FÁJLOK WAME-mel történő kódolásakor az ügyfelek `H264 Adaptive Bitrate MP4 Set 1080p`általában egy elnevezett előre beállított karakterláncot használtak, például . Az áttelepítéshez a kódot frissíteni kell, hogy a **MEDIA Encoder Standard** médiaprocesszort használhassa a `H264 Multiple Bitrate 1080p`WAME helyett, és az egyik egyenértékű [rendszerkészletet,](media-services-mes-presets-overview.md) például a . 

## <a name="migrating-to-media-encoder-standard"></a>Áttelepítés a Media Encoder Standard alkalmazásba

Itt van egy tipikus C# kód minta, amely az örökölt összetevőt használja. 

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("WAME Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Adaptive Bitrate MP4 Set 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Adaptive Bitrate MP4 Set 1080p", 
    TaskOptions.None); 
```

Itt van a frissített verzió, amely a Media Encoder Standard.Here is the updated version that uses Media Encoder Standard.

```csharp
// Declare a new job. 
IJob job = _context.Jobs.Create("Media Encoder Standard Job"); 
// Get a media processor reference, and pass to it the name of the  
// processor to use for the specific task. 
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard"); 

// Create a task with the encoding details, using a string preset. 
// In this case " H264 Multiple Bitrate 1080p" preset is used. 
ITask task = job.Tasks.AddNew("My encoding task", 
    processor, 
    "H264 Multiple Bitrate 1080p", 
    TaskOptions.None); 
```

### <a name="advanced-scenarios"></a>Speciális forgatókönyvek 

Ha saját kódolási készletet hozott létre a WAME számára a sémával, akkor a [Media Encoder Standard megfelelő séma](media-services-mes-schema.md)jelenik meg.

## <a name="known-differences"></a>Ismert különbségek 

A Media Encoder Standard robusztusabb, megbízhatóbb, jobb teljesítményt nyújt, és jobb minőségű kimenetet eredményez, mint a régebbi WAME kódoló. ráadásul: 

* A Media Encoder Standard a WAME-tól eltérő elnevezési konvencióval rendelkező kimeneti fájlokat hoz létre.
* A Media Encoder Standard olyan összetevőket hoz létre, mint például a [bemeneti fájl metaadatait](media-services-input-metadata-schema.md) és a [kimeneti fájl metaadatait](media-services-output-metadata-schema.md)tartalmazó fájlok.
* Ahogy az a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/media-services/#encoding) is szerepel (különösen a GYIK részben), amikor a Media Encoder Standard használatával kódolja a videókat, a számlázás a kimenetként előállított fájlok időtartama alapján kerül számlázásra. A WAME-nál a bemeneti videofájl(ok) és a kimeneti videofájl(ok) mérete alapján kell fizetnie.

## <a name="need-help"></a>Segítségre van szüksége?

Támogatási jegyet úgy nyithat meg, hogy az [Új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) navigál.

## <a name="next-steps"></a>További lépések

* [Örökölt összetevők](legacy-components.md)
* [Díjszabás lap](https://azure.microsoft.com/pricing/details/media-services/#encoding)
