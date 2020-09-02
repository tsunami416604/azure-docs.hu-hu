---
title: Adathordozók feltöltése
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan tölthet fel médiatartalmakat adatfolyamként vagy kódolásra.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0bdb2c36bc895c9229e4c04e9e0d76aa852bd139
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297306"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Média feltöltése adatfolyamként vagy kódolásra

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

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

## <a name="rest"></a>[REST](#tab/rest/)

Miután [létrehozott egy eszközt a Poster vagy más Rest metódus használatával, és felhagyta az eszköz sas URL-címét](how-to-create-asset.md?tabs=rest), használja az Azure Storage API-kat vagy SDK-kat (például a [Storage REST API](../../storage/common/storage-rest-api-auth.md) vagy a [.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)-t.

---
<!-- add these to the tabs when available -->
Más módszerekkel kapcsolatban lásd: az [Azure Storage dokumentációja](https://docs.microsoft.com/azure/storage/blobs/) a Blobok használatához a [.net](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet), a [Java](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-java), a [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)és a [JavaScript (Node.js)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-nodejs)használatával.

## <a name="next-steps"></a>További lépések

> [Media Services áttekintése](media-services-overview.md)
