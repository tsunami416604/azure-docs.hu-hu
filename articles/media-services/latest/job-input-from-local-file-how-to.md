---
title: Azure Media Services-feladathoz tartozó bemenet létrehozása helyi fájlból | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre egy Azure Media Services-feladathoz tartozó bemenetet egy helyi fájlból.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/18/2020
ms.author: inhenkel
ms.openlocfilehash: 4d7e6d9daa5d30c5cbf5b198a147564d0c05c074
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607228"
---
# <a name="create-a-job-input-from-a-local-file"></a>Feladathoz tartozó bevitel létrehozása helyi fájlból

A Media Services v3-ban a videófeldolgozási feladatok indításakor meg kell adnia a Media Services számára a bemeneti videó helyét. A bemeneti videó adathordozó-szolgáltatási eszközként tárolható, ebben az esetben egy bemeneti eszközt hoz létre egy fájl alapján (helyileg vagy az Azure Blob Storage-ban tárolva). Ez a témakör azt mutatja be, hogyan lehet feladatot létrehozni egy helyi fájlból. Teljes példaként tekintse meg ezt a [GitHub-mintát](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Előfeltételek 

* [Hozzon létre egy Media Services fiókot](./create-account-howto.md).

## <a name="net-sample"></a>.NET-minta

A következő kód bemutatja, hogyan hozhat létre bemeneti adategységet, és hogyan használhatja azt bemenetként a feladathoz. A CreateInputAsset függvény a következő műveleteket hajtja végre:

* Az eszköz létrehozása
* Lekér egy írható [SAS URL-címet](../../storage/common/storage-sas-overview.md) az objektum [tárolójába](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container).
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

A következő kódrészlet egy kimeneti eszközt hoz létre, ha még nem létezik:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

A következő kódrészlet elküld egy kódolási feladatot:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Feladathibakódok

Lásd: [hibakódok](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Következő lépések

[Hozzon létre egy feladatot a HTTPS URL-címről](job-input-from-http-how-to.md).
