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
ms.openlocfilehash: a9fd38b857f513ff67ae2fbe4ccd0c6c9540967e
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779161"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Fájlok feltöltése egy Media Services-fiókba REST használatával

Media Services a digitális fájlokat feltölti egy eszközhöz társított blob-tárolóba. Az [eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) entitás tartalmazhat videó-, hang-, kép-, miniatűr-gyűjtemények, szöveg-és zárt feliratú fájlokat (valamint a fájlokra vonatkozó metaadatokat is). A fájlok az objektum tárolóba való feltöltése után a tartalom biztonságosan tárolódik a felhőben a további feldolgozáshoz és folyamatos átvitelhez.

Ez a cikk bemutatja, hogyan tölthet fel egy helyi fájlt a REST használatával.

## <a name="prerequisites"></a>Előfeltételek

A jelen témakörben ismertetett lépések végrehajtásához a következőket kell tennie:

- Az [eszköz koncepciójának](assets-concept.md)áttekintése.
- [A Poster beállítása Azure Media Services REST API-hívásokhoz](media-rest-apis-with-postman.md).
    
    Ügyeljen arra, hogy kövesse az [Azure ad-token beszerzése](media-rest-apis-with-postman.md#get-azure-ad-token)című témakör utolsó lépését. 

## <a name="create-an-asset"></a>Eszköz létrehozása

Ebből a szakaszból megtudhatja, hogyan hozhat létre új eszközt.

1. Válassza **ki** az adategységeket -> **hozzon létre vagy frissítsen egy eszközt**.
2. Kattintson a **Küldés** gombra.

    ![Eszköz létrehozása](./media/upload-files/postman-create-asset.png)

Ekkor megjelenik a **Válasz** az újonnan létrehozott objektumra vonatkozó információkkal.

## <a name="get-a-sas-url-with-read-write-permissions"></a>SAS URL-cím beszerzése írási és olvasási engedélyekkel 

Ez a szakasz bemutatja, hogyan szerezhet be egy SAS URL-címet, amelyet a létrehozott eszközhöz generáltak. A SAS URL-cím írási és olvasási engedéllyel lett létrehozva, és a digitális fájlok az objektum tárolóba való feltöltésére használható.

1. Válassza az **eszközök** lehetőséget -> **az eszköz URL-címeinek listázásához**.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/upload-files/postman-create-sas-locator.png)

A **Válasz** az eszköz URL-címeivel kapcsolatos információkkal jelenik meg. Másolja ki az első URL-címet, és használja fel a fájl feltöltéséhez.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Fájl feltöltése a blob Storage-ba a feltöltési URL-cím használatával

Használja az Azure Storage API-jait vagy SDK-kat (például a [Storage REST API](../../storage/common/storage-rest-api-auth.md) vagy a [.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)-t.

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: távoli fájl kódolása URL-cím alapján és stream a videó – REST](stream-files-tutorial-with-rest.md)
