---
title: Adathordozó feltöltése
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan tölthet fel médiatartalmakat adatfolyamként vagy kódolásra.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: inhenkel
ms.openlocfilehash: 3da313803aa546c2399e3c8c18858bee5cfa2ea1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684371"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Média feltöltése adatfolyamként vagy kódolásra
Media Services a digitális fájlokat (adathordozókat) feltölti egy eszközhöz társított blob-tárolóba. Az [eszköz](/rest/api/media/operations/asset) entitás tartalmazhat videó-, hang-, kép-, miniatűr-gyűjtemények, szöveg-és zárt feliratú fájlokat (valamint a fájlokra vonatkozó metaadatokat is). A fájlok az objektum tárolóba való feltöltése után a tartalom biztonságosan tárolódik a felhőben a további feldolgozáshoz és folyamatos átvitelhez.

Mielőtt megkezdené a kezdést, össze kell gyűjtenie vagy gondolnia kell néhány értéket.

1. A feltölteni kívánt fájl helyi fájljának elérési útja
1. Az eszközhöz tartozó eszköz azonosítója (tároló)
1. A feltöltött fájlhoz használni kívánt név, beleértve a kiterjesztést is
1. A használt Storage-fiók neve
1. A Storage-fiók elérési kulcsa

## <a name="portal"></a>[Portál](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[Parancssori felület](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="cli-shell"></a>[CLI-rendszerhéj](#tab/clishell/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli-shell.md)]

## <a name="rest"></a>[REST](#tab/rest/)

Miután [létrehozott egy eszközt a Poster vagy más Rest metódus használatával, és felhagyta az eszköz sas URL-címét](how-to-create-asset.md?tabs=rest), használja az Azure Storage API-kat vagy SDK-kat (például a [Storage REST API](../../storage/common/storage-rest-api-auth.md) vagy a [.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)-t.

---
<!-- add these to the tabs when available -->
Más módszerekkel kapcsolatban lásd: az [Azure Storage dokumentációja](https://docs.microsoft.com/azure/storage/blobs/) a Blobok használatához a [.net](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet), a [Java](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-java), a [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)és a [JavaScript (Node.js)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-nodejs)használatával.

## <a name="next-steps"></a>További lépések

> [Media Services áttekintése](media-services-overview.md)
