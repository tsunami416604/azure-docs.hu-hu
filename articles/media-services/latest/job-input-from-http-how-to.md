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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 2295df97dfe6792979738debcc56e3b18b271e69
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53412939"
---
# <a name="create-a-job-input-from-an-https-url"></a>Hozzon létre egy feladat bemenete HTTPS URL-címet

A Media Services v3-as a videók feldolgozásához feladatok elküldésekor meg kell mondanunk a Media Services, hogy hol található a bemeneti videó. A beállítások egyike egy HTTP (s) URL-cím megadásához adjon meg (ahogyan az ebben a példában látható) feladatként. Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást. Egy teljes példa: Ez [GitHub-minta](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-minta

A következő kód bemutatja, hogyan hozhat létre egy feladatot egy HTTPS URL-címet adjon meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>További lépések

[Hozzon létre egy feladat bemenete egy helyi fájlból](job-input-from-local-file-how-to.md).
