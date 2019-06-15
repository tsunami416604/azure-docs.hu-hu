---
title: Hozzon létre egy Azure Media Services-feladat bemeneti HTTPS URL-címet a |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozhat létre egy feladat bemenete egy HTTP (s) URL-CÍMÉT.
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
ms.openlocfilehash: f6eee912bb3bba112bd13969f1a8d9cb5748e387
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413814"
---
# <a name="create-a-job-input-from-an-https-url"></a>Hozzon létre egy feladat bemenete HTTPS URL-címet

A Media Services v3-ban a videófeldolgozási feladatok indításakor meg kell adnia a Media Services számára a bemeneti videó helyét. A beállításokat egyik HTTPS URL-címet adja meg a feladat bemeneti (ahogyan az ebben a példában látható). Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást. Egy teljes példa: Ez [GitHub-minta](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Fejlesztés megkezdése előtt tekintse át a [fejlesztés a Media Services v3 API-k](media-services-apis-overview.md) (tartalmazza a eléréséről az API-k elnevezési konvenciók stb.)

## <a name="net-sample"></a>.NET sample

A következő kód bemutatja, hogyan hozhat létre egy feladatot egy HTTPS URL-címet adjon meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Feladathibakódok

Lásd: [hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>További lépések

[Hozzon létre egy feladat bemenete egy helyi fájlból](job-input-from-local-file-how-to.md).
