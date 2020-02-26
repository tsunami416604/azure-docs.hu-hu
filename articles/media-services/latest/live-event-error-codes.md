---
title: Azure Media Services élő események hibakódja | Microsoft Docs
description: Ez a cikk az élő események hibakódait sorolja fel.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599469"
---
# <a name="media-services-live-event-error-codes"></a>Media Services élő események hibakódja

Az alábbi táblázat felsorolja az [élő események](live-events-outputs-concept.md) hibakódait:

|Hiba|Leírás|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|Ez a hiba akkor fordul elő, ha a bejövő kódoló a 30fps meghaladó streameket küld az élő események/csatornák kódolásához.|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|Ez a hiba akkor fordul elő, ha a bejövő kódoló a következő felbontást meghaladó streameket küldi el: 1920x1088 az élő események/csatornák kódolásához, valamint a 4096 x 2160 a továbbított élő eseményekhez/csatornákhoz.|

## <a name="see-also"></a>Lásd még

[Adatfolyam-végpont (forrás) hibakódok](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: élő közvetítés Media Services](stream-live-tutorial-with-api.md)
