---
title: Áttelepítés Windows Azure Media Encoderról Media Encoder Standardra | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan lehet áttelepíteni a Azure Media Encoderról a Media Encoder Standard Media Processor szolgáltatásba.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: a4a5f2f448cc5e954cf21f9785f788925d5a461e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89263943"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Áttelepítés Windows Azure Media Encoderról Media Encoder Standardre

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Ez a cikk azt ismerteti, hogyan lehet áttelepíteni a régi Windows Azure Media Encoder (Tamás) adathordozó-processzorról (amely kivonásra kerül) a Media Encoder Standard Media Processor szolgáltatásba. A nyugdíjazási dátumokért tekintse meg ezt a [régi összetevőket](legacy-components.md) ismertető témakört.

A Tamás-mel rendelkező fájlok kódolásakor az ügyfelek általában egy megnevezett előre definiált karakterláncot használnak, például: `H264 Adaptive Bitrate MP4 Set 1080p` . A Migrálás érdekében a kódot frissíteni kell, hogy a Tamás helyett a **Media Encoder standard** Media processzort használja, és az egyik egyenértékű rendszer- [előállítson](media-services-mes-presets-overview.md) , például: `H264 Multiple Bitrate 1080p` . 

## <a name="migrating-to-media-encoder-standard"></a>Áttelepítés Media Encoder Standardre

Íme egy tipikus C#-mintakód, amely az örökölt összetevőt használja. 

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

A Media Encoder Standardt használó frissített verzió.

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

Ha a Tamás a saját sémája alapján hozta létre a saját kódolási beállításkészletét, akkor a [Media Encoder standard egyenértékű sémával](media-services-mes-schema.md)rendelkezik.

## <a name="known-differences"></a>Ismert különbségek 

Media Encoder Standard robusztusabb, megbízhatóbb, jobb teljesítményű, és jobb minőségű kimenetet hoz létre, mint az örökölt Tamás kódoló. ráadásul: 

* Media Encoder Standard a Tamás eltérő elnevezési konvencióval rendelkező kimeneti fájlokat hoz létre.
* A Media Encoder Standard összetevőket, például a [bemeneti fájl metaadatait](media-services-input-metadata-schema.md) és a [kimeneti fájl (oka) metaadatokat](media-services-output-metadata-schema.md)tartalmazó fájlokat hoz létre.
* A [díjszabási oldalon](https://azure.microsoft.com/pricing/details/media-services/#encoding) (különösen a gyakori kérdések szakaszban) a videók Media Encoder standard használatával történő kódolásakor a rendszer a kimenetként létrehozott fájlok időtartamán alapul. A Tamás a bemeneti videó fájl (ok) és a kimeneti videofájl mérete alapján számítjuk fel a díjat.

## <a name="need-help"></a>Segítségre van szüksége?

A támogatási jegy megnyitásához lépjen az [új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .

## <a name="next-steps"></a>További lépések

* [Örökölt összetevők](legacy-components.md)
* [Díjszabás lap](https://azure.microsoft.com/pricing/details/media-services/#encoding)
