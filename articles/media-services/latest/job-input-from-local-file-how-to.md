---
title: Hozzon létre egy Azure Media Services feladat bemeneti helyi fájlból |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozzon létre egy feladat bemeneti helyi fájlból.
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
ms.openlocfilehash: 94e7192e13397ad8ec973d92f4c538f430c9cd60
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="create-a-job-input-from-a-local-file"></a>Hozzon létre egy feladat bemeneti helyi fájlból

A Media Services v3 amikor a videók feldolgozásához feladatok rendelkezik állapítható meg, hogy a Media Services hol található a bemeneti videó. A bemeneti videó tárolható Media Service eszközként, ebben az esetben hozzon létre egy bemeneti eszköz, a (helyi vagy az Azure Blob Storage tárolóban tárolt) fájl alapján. Ez a témakör bemutatja, hogyan hozzon létre egy feladat bemeneti helyi fájlból. Tekintse meg a teljes például [github minta](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-minta

A következő kód bemutatja, hogyan hozzon létre egy bemeneti eszközt, és a feladat használható a bemeneti adatok. A CreateInputAsset függvény a következő műveleteket hajtja végre:

* Az eszköz létrehozása 
* Lekér egy írható [SAS URL-címet](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) az objektum [tárolójába](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container).
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>További lépések

[Hozzon létre egy feladat bemeneti egy HTTP (s) URL-címről](job-input-from-http-how-to.md).
