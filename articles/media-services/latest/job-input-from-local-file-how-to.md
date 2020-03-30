---
title: Azure Media Services-feladatbevitel létrehozása helyi fájlból | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan hozhat létre egy Azure Media Services-feladat bemenet egy helyi fájlból.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: aba987ba232a29ffc240f72039b1e24bb87a2ed4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345908"
---
# <a name="create-a-job-input-from-a-local-file"></a>Feladatbevitel létrehozása helyi fájlból

A Media Services v3-ban a videófeldolgozási feladatok indításakor meg kell adnia a Media Services számára a bemeneti videó helyét. A bemeneti videó tárolható Media Service Asset, ebben az esetben hozzon létre egy bemeneti eszköz alapján egy fájlt (helyileg tárolt vagy az Azure Blob storage). Ez a témakör bemutatja, hogyan hozhat létre feladatbemenetet egy helyi fájlból. Egy teljes példa, lásd ezt a [GitHub-minta.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs)

## <a name="prerequisites"></a>Előfeltételek 

* [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md).
* Tekintse át az eszközök kezelése című [véleményt.](manage-asset-concept.md)

## <a name="net-sample"></a>.NET minta

A következő kód bemutatja, hogyan hozhat létre egy bemeneti eszközt, és használja azt a feladat bemeneti. A CreateInputAsset függvény a következő műveleteket hajtja végre:

* Létrehozza az eszközt
* Lekér egy írható [SAS URL-címet](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) az objektum [tárolójába](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container).
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

A következő kódrészlet létrehoz egy kimeneti eszközt, ha még nem létezik:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

A következő kódrészlet kódolási feladatot küld:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Feladathibakódok

Lásd: [Hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>További lépések

[Hozzon létre egy feladatbemenetet HTTPS-URL-címről.](job-input-from-http-how-to.md)
