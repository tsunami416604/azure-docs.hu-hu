---
title: Fájlok feltöltése egy Azure Media Services v3-fiókba a REST használatával | Microsoft Docs
description: Megtudhatja, hogyan szerezhet be médiatartalmakat Media Servicesba eszközök létrehozásával és feltöltésével.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 57839c82af71f7cedfe1b3c77095210d273be4a1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091844"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Fájlok feltöltése egy Media Services v3-fiókba REST használatával

Media Services a digitális fájlokat feltölti egy eszközhöz társított blob-tárolóba. Az [eszköz](/rest/api/media/operations/asset) entitás tartalmazhat videó-, hang-, kép-, miniatűr-gyűjtemények, szöveg-és zárt feliratú fájlokat (valamint a fájlokra vonatkozó metaadatokat is). A fájlok az objektum tárolóba való feltöltése után a tartalom biztonságosan tárolódik a felhőben a további feldolgozáshoz és folyamatos átvitelhez.

Ez a cikk bemutatja, hogyan tölthet fel egy helyi fájlt a REST használatával.

## <a name="prerequisites"></a>Előfeltételek

A jelen témakörben ismertetett lépések végrehajtásához a következőket kell tennie:

- Az [eszköz koncepciójának](assets-concept.md)áttekintése.
- [A Poster beállítása Azure Media Services REST API-hívásokhoz](media-rest-apis-with-postman.md).
    
    Ügyeljen arra, hogy kövesse az [Azure ad-token beszerzése](media-rest-apis-with-postman.md#get-azure-ad-token)című témakör utolsó lépését. 

## <a name="create-an-asset"></a>Adategység létrehozása

Ebből a szakaszból megtudhatja, hogyan hozhat létre új eszközt.

1. Válassza **Assets**  ->  **az eszközök létrehozás vagy az eszköz frissítése**lehetőséget.
2. Kattintson a **Küldés** gombra.

    ![Adategység létrehozása](./media/upload-files/postman-create-asset.png)

Ekkor megjelenik a **Válasz** az újonnan létrehozott objektumra vonatkozó információkkal.

## <a name="get-a-sas-url-with-read-write-permissions"></a>SAS URL-cím beszerzése írási és olvasási engedélyekkel 

Ez a szakasz bemutatja, hogyan szerezhet be egy SAS URL-címet, amelyet a létrehozott eszközhöz generáltak. A SAS URL-cím írási és olvasási engedéllyel lett létrehozva, és a digitális fájlok az objektum tárolóba való feltöltésére használható.

1. Válassza az **eszközök**lehetőséget  ->  **az eszköz URL-címeinek listázásához**.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/upload-files/postman-create-sas-locator.png)

A **Válasz** az eszköz URL-címeivel kapcsolatos információkkal jelenik meg. Másolja ki az első URL-címet, és használja fel a fájl feltöltéséhez.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Fájl feltöltése a blob Storage-ba a feltöltési URL-cím használatával

Használja az Azure Storage API-jait vagy SDK-kat (például a [Storage REST API](../../storage/common/storage-rest-api-auth.md) vagy a [.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)-t.

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Távoli fájl kódolása URL-cím alapján és videó streamelése – REST](stream-files-tutorial-with-rest.md)
