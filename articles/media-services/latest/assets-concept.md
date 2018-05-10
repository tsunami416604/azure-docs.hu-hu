---
title: Az Azure Media Services eszközök |} Microsoft Docs
description: Ez a cikk lehetőséget ad olyan eszközök vannak, és megismerkedhet használatukkal Azure Media Services által ismertetése.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>Objektumok

Egy **eszköz** digitális fájlok (például a videó, hang, képeket, miniatűröket, szöveges nyomon követi és feliratfájlokat fájlok) és a mindezen fájlok metaadatait tartalmazza. Miután a digitális fájlok feltöltése az objektumba, akkor a Media Services kódolási és adatfolyam-munkafolyamatok is használható.

Egy eszköz egy blob-tároló az van leképezve a [Azure Storage-fiók](storage-account-concept.md) és az adott tároló blokkblobként tárolja a fájlokat az eszköz. Az adategység-fájloknak a tárolókban, a Storage szolgáltatás SDK-ügyfél használatával kommunikálhat.

Az Azure Media Services támogatja a Blob rétegek, ha a fiók használ az általános célú v2 (GPv2) tároló. A GPv2 áthelyezheti hűtésével fájlok vagy a cold tároló. Cold tárolási alkalmas archiválás mezzanine lévő fájlok már nem szükséges (például után azok van kódolva.)

A Media Services v3 a feladat bemeneti is létrehozható eszközök vagy a http (s) URL-címeket. Egy eszköz, a feladat használható bemenetként létrehozásához lásd: [létrehozni a feladat bemeneti helyi fájlból](job-input-from-local-file-how-to.md).

Emellett olvassa [storage-fiókok a Media Services](storage-account-concept.md) és [átalakítások, a feladatok](transform-concept.md).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egy fájlt](stream-files-dotnet-quickstart.md)
