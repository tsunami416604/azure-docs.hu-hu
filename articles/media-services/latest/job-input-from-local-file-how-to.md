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
ms.openlocfilehash: 3eb16034cc6507944ca7bebb59893e0d72a6f4c9
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415459"
---
# <a name="create-a-job-input-from-a-local-file"></a>Hozzon létre egy feladat bemenete egy helyi fájlból

A Media Services v3-as a videók feldolgozásához feladatok elküldésekor meg kell mondanunk a Media Services, hogy hol található a bemeneti videó. A bemeneti videó tárolható Media Service eszközként, ebben az esetben létrehozhat egy bemeneti objektuma a (helyi vagy az Azure Blob storage-ban tárolt) fájl alapján. Ez a témakör bemutatja, hogyan hozhat létre egy feladat bemenete egy helyi fájlból. Egy teljes példa: Ez [GitHub-minta](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET sample

A következő kód bemutatja, hogyan hozzon létre egy bemeneti objektumot, és a feladat bemeneteként használja. A CreateInputAsset függvény a következő műveleteket hajtja végre:

* Az objektum létrehozása
* Lekér egy írható [SAS URL-címet](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) az objektum [tárolójába](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container).
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Feladat-hibakódok

Lásd: [hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>További lépések

[Hozzon létre egy feladat bemenete HTTPS URL-címet](job-input-from-http-how-to.md).
