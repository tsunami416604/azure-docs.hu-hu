---
title: Fájlok feltöltése Azure Media Services-fiók, REST használatával |} A Microsoft Docs
description: Megtudhatja, hogyan médiatartalmak kerülnek a Media Services létrehozása és feltöltése az eszközök által.
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
ms.openlocfilehash: a6f872880b61a5bd9510abda2f15e2edea16e940
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703874"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Fájlok feltöltése Media Services-fiók REST használatával

A Media Services szolgáltatásban, a digitális fájlok feltöltése az eszközhöz társított blob-tárolóba. A [eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) entitás tartalmazhat videókhoz, audiofájlokhoz, képeket, miniatűröket, szöveges nyomon követi és akadálymentes felirat fájlokat (és mindezen fájlok metaadatait). Ha a fájlok feltöltése az eszközintelligencia-tárolóba, a lesz biztonságosan tárolva a tartalmat a felhő további feldolgozás és streamelés céljából.

Ez a cikk bemutatja, hogyan REST használatával helyi fájl feltöltése.

## <a name="prerequisites"></a>Előfeltételek

Az ebben a témakörben leírt lépések elvégzéséhez kell:

- Felülvizsgálat [eszköz fogalom](assets-concept.md).
- [Postman konfigurálása az Azure Media Services REST API-hívások](media-rest-apis-with-postman.md).
    
    Kövesse a témakör az utolsó lépés [lekérése az Azure AD-Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Hozzon létre egy objektumot

Ez a szakasz bemutatja, hogyan hozzon létre egy új eszközt.

1. Válassza ki **eszközök** -> **létrehozás vagy frissítés egy eszköz**.
2. Kattintson a **Küldés** gombra.

    ![Hozzon létre egy objektumot](./media/upload-files/postman-create-asset.png)

Megjelenik a **válasz** újonnan létrehozott eszköz kapcsolatos információval.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Olvasási és írási engedélyekkel egy SAS URL-Címének lekéréséhez 

Ez a szakasz bemutatja, hogyan jött létre SAS URL-cím beszerzése a létrehozott eszköz. Az SAS URL-cím olvasási és írási engedélyekkel készült, és a digitális fájlok feltöltése az Eszközintelligencia-tároló használható.

1. Válassza ki **eszközök** -> **eszköz URL-címek listája**.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/upload-files/postman-create-sas-locator.png)

Megjelenik a **válasz** objektum URL-címek kapcsolatos információval. Másolja az első URL-címet, és ezzel a fájl feltöltéséhez.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Fájl feltöltése a blob storage a feltöltése URL-cím használatával

Az Azure Storage API-k és SDK-kkal (például a [Storage REST API-val](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), vagy [.NET SDK-val](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Távoli fájl kódolása URL-cím alapján és videó streamelése – REST](stream-files-tutorial-with-rest.md)
