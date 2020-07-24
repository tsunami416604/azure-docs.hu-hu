---
title: Azure Media Services-feladathoz tartozó bemenet létrehozása HTTPS URL-címről | Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozhat létre egy Azure Media Services-feladatot egy HTTPS URL-címről.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: 1ff2bc6d4bb4f7b983787b287fbb27e1c33cc179
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022990"
---
# <a name="create-a-job-input-from-an-https-url"></a>Feladathoz tartozó bemenet létrehozása HTTPS URL-címről

A Media Services v3-ban a videófeldolgozási feladatok indításakor meg kell adnia a Media Services számára a bemeneti videó helyét. Az egyik lehetőség, ha egy HTTPS URL-címet ad meg a feladathoz tartozó bemenetként (a példában látható módon). Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást. Teljes példaként tekintse meg ezt a [GitHub-mintát](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> A fejlesztés megkezdése előtt tekintse át [az Media Services V3 API](media-services-apis-overview.md) -kkal való fejlesztést (az API-k elérésére vonatkozó információkat, elnevezési konvenciókat stb.).

## <a name="net-sample"></a>.NET-minta

A következő kód bemutatja, hogyan hozhat létre egy HTTPS URL-címmel rendelkező feladatot.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Feladathibakódok

Lásd: [hibakódok](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>További lépések

[Hozzon létre egy feladatot egy helyi fájlból](job-input-from-local-file-how-to.md).
