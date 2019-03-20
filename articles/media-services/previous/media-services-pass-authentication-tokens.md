---
title: Hitelesítési jogkivonatok átadása az Azure Media Services |} A Microsoft Docs
description: Ismerje meg, hogyan lehet elküldeni a hitelesítési tokenek az ügyfél az Azure Media Services kulcstovábbítást
services: media-services
keywords: a Content protection, DRM, jogkivonat-hitelesítés
documentationcenter: ''
author: dbgeorge
manager: jasonsue
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: dwgeo
ms.openlocfilehash: 6fc13010d5dd888f28b5634b1f70aaa3326e54cf
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837507"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Ismerje meg, hogyan ügyfelek jogkivonatok átadása az Azure Media Services kulcskézbesítési szolgáltatásba
Ügyfeleink gyakran kérdezik, hogyan játékos is jogkivonatok átadása ellenőrzése az Azure Media Services kulcskézbesítési szolgáltatáshoz, a Windows Media player szerezheti be a kulcsot. A Media Services a simple web Tokens (SWT) és a JSON webes jogkivonat (JWT) formátumokat támogatja. Jogkivonat-hitelesítés bármilyen típusú kulcsot, függetlenül attól, hogy használja vagy közös Advanced Encryption Standard (AES) boríték-titkosítást a rendszer alkalmazza a rendszer.

 Attól függően, a lejátszó és a megcélzott platform adhat át a jogkivonatot a lejátszóval a következő módon:

- – A HTTP-engedélyeztetési fejléc.
    > [!NOTE]
    > A "Tulajdonos" előtagot az OAuth 2.0-s specifikációt / várt. A token konfigurációval egy minta player üzemelteti az Azure Media Player [bemutató lapon](https://ampdemo.azureedge.net/). A videó forrása beállításához válassza **AES (JWT-jogkivonat)** vagy **AES (SWT-Token)**. A token via az engedélyezési fejléc lett átadva.

- Egy URL-cím hozzáadása keresztül lekérdezési paraméter "token = tokenvalue."  
    > [!NOTE]
    > A "Tulajdonos" előtag nem várt. A jogkivonat URL-cím keresztül zajlik, mert a jogkivonat-karakterláncot identitásinformációi kell. Íme egy C# mintakód bemutatja, hogyan teheti meg:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Keresztül a CustomData mező.
Ezt a beállítást csak, a PlayReady licenc beszerzése szolgál, a PlayReady-licenc beszerzési kihívás CustomData mezője révén. Ebben az esetben a token belül kell lennie az XML-dokumentum itt leírtak szerint:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    A hitelesítési jogkivonatot a jogkivonat elem helyezze el.

- Keresztül egy másik HTTP Live Streaming (HLS) listát. Ha a jogkivonat-hitelesítés az AES + HLS konfigurálnia kell a lejátszás iOS/Safari böngészőben oly módon, közvetlenül elküldheti a jogkivonat nem áll rendelkezésre. Alternatív a lista a forgatókönyv engedélyezésének módjáról további információkért lásd: Ez [blogbejegyzés](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>További lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]