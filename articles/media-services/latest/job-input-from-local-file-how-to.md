---
title: Hozzon létre egy Azure Media Services-feladat bemeneti egy helyi fájlból |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozhat létre egy feladat bemenete egy helyi fájlból.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: 399f6724b8948c8e507bc50622a4fb65b2262491
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653945"
---
# <a name="create-a-job-input-from-a-local-file"></a>Hozzon létre egy feladat bemenete egy helyi fájlból

A Media Services v3-ban a videófeldolgozási feladatok indításakor meg kell adnia a Media Services számára a bemeneti videó helyét. A bemeneti videó tárolható Media Service eszközként, ebben az esetben létrehozhat egy bemeneti objektuma a (helyi vagy az Azure Blob storage-ban tárolt) fájl alapján. Ez a témakör bemutatja, hogyan hozhat létre egy feladat bemenete egy helyi fájlból. Egy teljes példa: Ez [GitHub-minta](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET sample

A következő kód bemutatja, hogyan hozzon létre egy bemeneti objektumot, és a feladat bemeneteként használja. A CreateInputAsset függvény a következő műveleteket hajtja végre:

* Az objektum létrehozása
* Lekér egy írható [SAS URL-címet](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) az objektum [tárolójába](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container).
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Feladathibakódok

Lásd: [hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>További lépések

[Hozzon létre egy feladat bemenete HTTPS URL-címet](job-input-from-http-how-to.md).
