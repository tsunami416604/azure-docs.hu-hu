---
title: Fájlok feltöltése Azure Media Services v3-as fiókba rest használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan tölthet be médiatartalmakat a Media Services szolgáltatásba eszközök létrehozásával és feltöltésével.
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
ms.openlocfilehash: 38d46978e37ead59deb17a86f643df041452e497
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705768"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Fájlok feltöltése Media Services v3-as fiókba REST használatával

A Media Services, töltse fel a digitális fájlokat egy egy eszközhöz társított blob tárolóba. Az [Eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) entitás tartalmazhat video-, hang-, kép-, miniatűr gyűjtemények, szöveges pályák és felirat fájlokat (és a metaadatokat ezekről a fájlokról). Miután feltöltötte a fájlokat az eszköz tárolójába, a tartalom biztonságosan tárolódik a felhőben további feldolgozás és streamelés céljából.

Ez a cikk bemutatja, hogyan tölthet fel helyi fájlt rest használatával.

## <a name="prerequisites"></a>Előfeltételek

A témakörben ismertetett lépések végrehajtásához a következőket kell elvégeznie:

- Az [Eszköz fogalmának áttekintése](assets-concept.md).
- [A Postman konfigurálása az Azure Media Services REST API-hívásaihoz.](media-rest-apis-with-postman.md)
    
    Győződjön meg arról, hogy kövesse az [Azure AD-token beszerezni című](media-rest-apis-with-postman.md#get-azure-ad-token)témakör utolsó lépését. 

## <a name="create-an-asset"></a>Eszköz létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre egy új eszközt.

1. **Válassza az Eszközök** -> **eszköz létrehozása vagy frissítése**lehetőséget .
2. Kattintson a **Küldés** gombra.

    ![Eszköz létrehozása](./media/upload-files/postman-create-asset.png)

Megjelenik a **Válasz** az újonnan létrehozott eszközadatait.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Olvasási és írási engedélyekkel rendelkező SAS-URL-cím beolvasása 

Ez a szakasz bemutatja, hogyan szerezhet be egy SAS-URL-címet, amely a létrehozott eszközhöz jött létre. A SAS URL-cím írási és írási engedélyekkel jött létre, és digitális fájlok feltöltésére használható az eszköztárolóba.

1. Válassza ki **az Eszközök** -> **listája az eszköz URL-eket**.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/upload-files/postman-create-sas-locator.png)

A **Válasz** az eszköz URL-címeivel kapcsolatos információkkal jelenik meg. Másolja az első URL-t, és használja a fájl feltöltéséhez.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Fájl feltöltése a blobstorage-ba a feltöltési URL-cím használatával

Használja az Azure Storage API-kat vagy SDK-kat (például a [Storage REST API-t](../../storage/common/storage-rest-api-auth.md) vagy a [.NET SDK-t.](../../storage/blobs/storage-quickstart-blobs-dotnet.md)

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Távoli fájl kódolása URL alapján és streamelés - REST](stream-files-tutorial-with-rest.md)
