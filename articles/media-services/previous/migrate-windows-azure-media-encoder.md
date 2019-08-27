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
ms.date: 08/21/2019
ms.author: juliako
ms.openlocfilehash: 2e9008d039f014c95d473f3197b48651bdaa5a45
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019367"
---
# <a name="migrate-from-windows-azure-media-encoder-to-media-encoder-standard"></a>Áttelepítés Windows Azure Media Encoderról Media Encoder Standardre

Ez a cikk a régi Windows Azure Media Encoder (Tamás) adathordozó-processzorról való Migrálás lépéseit ismerteti, amely 2019 november 30-án megszűnik a Media Encoder Standard Media Processor számára.

A Tamás-mel rendelkező fájlok kódolásakor az ügyfelek általában egy megnevezett előre `H264 Adaptive Bitrate MP4 Set 1080p`definiált karakterláncot használnak, például:. A Migrálás érdekében a kódot frissíteni kell, hogy a Tamás helyett a **Media Encoder standard** Media processzort használja, és az egyik egyenértékű [rendszer](media-services-mes-presets-overview.md) -előállítson, `H264 Multiple Bitrate 1080p`például:. 

## <a name="migrating-to-media-encoder-standard"></a>Áttelepítés Media Encoder Standardre

Az alábbi példa egy C# tipikus mintakód, amely az örökölt összetevőt használja. 

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

Ha a Tamás a saját sémája alapján hozta létre a saját kódolási beállításkészletét, akkor a [Media Encoder standard egyenértékű sémával](media-services-mes-schema.md)rendelkezik. Ha kérdése van, hogy miként képezhető le a régebbi beállítások az új kódolóhoz, forduljon hozzánk a következőn keresztülmailto:amshelp@microsoft.com  

## <a name="known-differences"></a>Ismert különbségek 

Media Encoder Standard robusztusabb, megbízhatóbb, jobb teljesítményű, és jobb minőségű kimenetet hoz létre, mint az örökölt Tamás kódoló. ráadásul: 

* Media Encoder Standard a Tamás eltérő elnevezési konvencióval rendelkező kimeneti fájlokat hoz létre.
* A Media Encoder Standard összetevőket, például a [bemeneti fájl metaadatait](media-services-input-metadata-schema.md) és a [kimeneti fájl (oka) metaadatokat](media-services-output-metadata-schema.md)tartalmazó fájlokat hoz létre.
* A [díjszabási oldalon](https://azure.microsoft.com/pricing/details/media-services/#encoding) (különösen a gyakori kérdések szakaszban) a videók Media Encoder standard használatával történő kódolásakor a rendszer a kimenetként létrehozott fájlok időtartamán alapul. A Tamás a bemeneti videó fájl (ok) és a kimeneti videofájl mérete alapján számítjuk fel a díjat.

## <a name="next-steps"></a>További lépések

* [Örökölt összetevők](legacy-components.md)
* [Díjszabási oldal](https://azure.microsoft.com/pricing/details/media-services/#encoding)
