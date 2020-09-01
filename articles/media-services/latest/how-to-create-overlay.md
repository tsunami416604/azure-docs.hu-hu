---
title: Átfedés létrehozása a Media Encoder Standard használatával
description: Megtudhatja, hogyan hozhat létre átfedést Media Encoder Standard használatával.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6c93408bce8da9f8cd0e4a0d0bab615e2bd362dc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267326"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Átfedés létrehozása a Media Encoder Standard használatával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Media Encoder Standard lehetővé teszi egy rendszerkép átfedését egy meglévő videóra. Jelenleg a következő formátumok támogatottak: PNG, jpg, GIF és BMP.

## <a name="prerequisites"></a>Előfeltételek

* Gyűjtsön olyan fiókadatokat, amelyekre szükség van a *appsettings.js* konfigurálásához a minta fájljában. Ha nem tudja, hogyan teheti meg, tekintse meg a rövid útmutató [: alkalmazás regisztrálása a Microsoft Identity platformon](../../active-directory/develop/quickstart-register-app.md)című témakört. A következő értékeket kell megvárni a fájl *appsettings.js* .

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Ha még nem ismeri az átalakításokat, javasoljuk, hogy hajtsa végre a következő tevékenységeket:

* [Videó és hang kódolásának](encoding-concept.md) olvasása Media Services
* Olvassa el, [Hogyan kódolhat egyéni átalakító-.net-](customize-encoder-presets-how-to.md)tel. A cikk lépéseit követve állítsa be, hogy az átalakításokkal való együttműködéshez szükség van-e a .NET-re, majd térjen vissza ide az átfedések előre beállított mintájának kipróbálásához.
* Lásd az [átalakítások hivatkozási dokumentumát](/rest/api/media/transforms).

Ha már ismeri az átalakításokat, töltse le az átfedések mintáját.

## <a name="overlays-preset-sample"></a>Átfedések előre definiált mintája

Töltse le a [Media-Services-overlay mintát](https://github.com/Azure-Samples/media-services-overlays) az átfedések megkezdéséhez.

## <a name="next-steps"></a>További lépések

* [Videó kivágása Media Services-.NET-kódolással](subclip-video-dotnet-howto.md)