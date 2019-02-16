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
ms.openlocfilehash: 1760c63bee6bc1b0a631499a8875bedb34e71afd
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308842"
---
# <a name="create-a-job-input-from-an-https-url"></a>Hozzon létre egy feladat bemenete HTTPS URL-címet

A Media Services v3-as a videók feldolgozásához feladatok elküldésekor meg kell mondanunk a Media Services, hogy hol található a bemeneti videó. A beállításokat egyik HTTPS URL-címet adja meg a feladat bemeneti (ahogyan az ebben a példában látható). Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást. Egy teljes példa: Ez [GitHub-minta](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET sample

A következő kód bemutatja, hogyan hozhat létre egy feladatot egy HTTPS URL-címet adjon meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>További lépések

[Hozzon létre egy feladat bemenete egy helyi fájlból](job-input-from-local-file-how-to.md).
