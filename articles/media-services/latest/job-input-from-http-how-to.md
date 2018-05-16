---
title: Azure Media Services feladat bemeneti készíteni egy HTTP (s) URL-címe |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozható létre egy feladat bemeneti a URL-címének http (s).
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
ms.openlocfilehash: d429665de64dacc5818d1d26c2a9029531cd39b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="create-a-job-input-from-an-https-url"></a>Hozzon létre egy feladat bemeneti az URL-címének http (s)

A Media Services v3 amikor a videók feldolgozásához feladatok rendelkezik állapítható meg, hogy a Media Services hol található a bemeneti videó. A beállítások egyik adja meg az URL-címének http (s) (a példa szerint) bemeneti feladatként. Tekintse meg a teljes például [github minta](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-minta

A következő kód bemutatja, hogyan hozzon létre egy HTTPS URL-címet adjon meg egy feladatot.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>További lépések

[Hozzon létre egy feladat bemeneti helyi fájlból](job-input-from-local-file-how-to.md).
