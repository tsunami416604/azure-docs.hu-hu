---
title: Azure Media Services-feladatbemenet létrehozása HTTPS-URL-címről | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan hozhat létre egy Azure Media Services-feladat bemenetegy HTTPS URL-címről.
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
ms.openlocfilehash: a3d4568dd237491f28ae2880bdd78dd236870c3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899830"
---
# <a name="create-a-job-input-from-an-https-url"></a>Feladatbevitel létrehozása HTTPS-URL-címről

A Media Services v3-ban a videófeldolgozási feladatok indításakor meg kell adnia a Media Services számára a bemeneti videó helyét. Az egyik lehetőség az, hogy adja meg a HTTPS URL-címet a feladat bemenete (ahogy ebben a példában). Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást. Egy teljes példa, lásd ezt a [GitHub-minta.](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)

> [!TIP]
> A fejlesztés megkezdése előtt tekintse át [a Fejlesztés a Media Services v3 API-jával](media-services-apis-overview.md) című irányelveket (beleértve az API-k elérésére, elnevezési konvenciókra stb.)

## <a name="net-sample"></a>.NET minta

A következő kód bemutatja, hogyan hozhat létre egy feladatot HTTPS URL-címbevitellel.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Feladathibakódok

Lásd: [Hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>További lépések

[Hozzon létre egy feladatbemenetet helyi fájlból.](job-input-from-local-file-how-to.md)
