---
title: "A hitelesítési tokenek át Azure Media Services |} Microsoft Docs"
description: "Útmutató: az ügyfél hitelesítési jogkivonatokat elküldje az Azure Media Services kulcs kézbesítési szolgáltatás"
services: media-services
keywords: "a védett tartalom, DRM, tokent használó hitelesítés"
documentationcenter: 
author: dbgeorge
manager: jasonsue
editor: 
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: dwgeo
ms.openlocfilehash: 7d143242231444b8557a303d1b504d5311693f1a
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Ismerje meg, hogyan ügyfelek átadása jogkivonatok az Azure Media Services kulcs kézbesítési szolgáltatás
Az ügyfelek gyakran kérje meg, hogyan egy player képes továbbadni jogkivonatok az Azure Media Services kulcs kézbesítési szolgáltatás ellenőrzésre, a Windows Media player szerezheti be a kulcsot. Media Services támogatja a egyszerű webes jogkivonat (SWT), és JSON webes jogkivonat (JWT) formátumú. Jogkivonat hitelesítési kulcs, függetlenül attól, hogy használ-e általános titkosítás vagy a boríték titkosítás Advanced Encryption Standard (AES) a rendszer bármilyen típusú vonatkozik.

 Attól függően, hogy a player és célozhat meg platform a a következőképpen továbbíthatja a jogkivonat a lejátszóval:

- A HTTP-hitelesítési fejlécéhez.
    > [!NOTE]
    > A "Tulajdonos" előtag az OAuth 2.0 specifikációk / várt. A token konfiguráció egy minta player üzemelteti-e az Azure Media Player [bemutató oldal](http://ampdemo.azureedge.net/). A videoforrást beállításához válassza **(JWT jogkivonat) AES** vagy **(SWT Token) AES**. A jogkivonat hitelesítési fejlécéhez keresztül lett átadva.

- Egy URL-cím hozzáadása keresztül lekérdezési paraméter "lexikális elem = tokenvalue."  
    > [!NOTE]
    > A "Tulajdonos" előtag nem várt. A token egy URL-cím keresztül küld el, mert szükség amelyeket védelmére a lexikális elem karakterlánca. Íme egy C# mintakód bemutatja, hogyan kell tenni:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- A CustomData mező.
Ez a beállítás használható a PlayReady licenc beszerzési csak, a PlayReady licenc beszerzési kihívás az CustomData mezője révén. Ebben az esetben a token belül kell lennie az XML-dokumentum itt leírtak szerint:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Helyezze el a hitelesítési jogkivonat a Token elemet.

- Keresztül egy másik HTTP Live Streaming (HLS) listát. Ha szeretné-e az AES + HLS tokent használó hitelesítés konfigurálása a lejátszás iOS/Safari böngészőben oly módon, közvetlenül elküldheti a jogkivonat nem létezik. Alternatív a lista a forgatókönyv engedélyezésének módjáról további információkért tekintse meg a [blogbejegyzés](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

## <a name="next-steps"></a>További lépések

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]