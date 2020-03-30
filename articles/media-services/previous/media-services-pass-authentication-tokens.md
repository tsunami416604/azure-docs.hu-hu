---
title: Hitelesítési jogkivonatok áthárítása az Azure Media Services szolgáltatásnak | Microsoft dokumentumok
description: Ismerje meg, hogyan küldhet hitelesítési jogkivonatokat az ügyféltől az Azure Media Services kulcskézbesítési szolgáltatásának
services: media-services
keywords: tartalomvédelem, DRM, token hitelesítés
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 15d4cbc372f5d5ec0d323170189329152ed436e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73684940"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Ismerje meg, hogyan továbbítják az ügyfelek a jogkivonatokat az Azure Media Services kulcskézbesítési szolgáltatásához
Az ügyfelek gyakran kérdezik, hogy egy játékos hogyan adhat át jogkivonatokat az Azure Media Services kulcskézbesítési szolgáltatásának ellenőrzéscéljából, hogy a játékos beszerezhesse a kulcsot. A Media Services támogatja az egyszerű webes jogkivonat (SWT) és json webtoken (JWT) formátumokat. A tokenhitelesítés bármilyen típusú kulcsra vonatkozik, függetlenül attól, hogy közös titkosítást vagy speciális titkosítási szabvány (AES) borítéktitkosítást használ a rendszerben.

 A megcélzott játékostól és platformtól függően a következő módokon adhatod át a zsetont a játékosodnak:

- A HTTP-engedélyezési fejlécen keresztül.
    > [!NOTE]
    > A "bemutatóra" előtag az OAuth 2.0 specifikációi szerint várható. A tokenkonfigurációval rendelkező mintalejátszó az Azure Media Player [bemutatóoldalán](https://ampdemo.azureedge.net/)található. A videoforrás beállításához válassza az **AES (JWT Token)** vagy az **AES (SWT Token) lehetőséget.** A jogkivonat az engedélyezési fejlécen keresztül kerül átadásra.

- A "token=tokenvalue" URL-lekérdezési paraméter hozzáadásával.  
    > [!NOTE]
    > A "bemutatóra" előtag nem várható. Mivel a jogkivonat egy URL-címen keresztül kerül elküldésre, a tokenkarakterláncot kell páncélolnia. Itt van egy C# mintakód, amely bemutatja, hogyan kell csinálni:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- A CustomData mezőn keresztül.
Ez a beállítás csak a PlayReady licencbeszerzéshez használható, a PlayReady licencbeszerzési kihívás CustomData mezőjén keresztül. Ebben az esetben a jogkivonatnak az XML-dokumentumban kell lennie az itt leírtak szerint:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Helyezze a hitelesítési jogkivonatot a Token elembe.

- Egy másik HTTP Live Streaming (HLS) lejátszási listán keresztül. Ha konfigurálnia kell a token hitelesítést az AES + HLS lejátszáshoz az iOS/Safari-n, nincs mód közvetlenül a jogkivonat elküldésére. Ha többet szeretne tudni arról, hogy miként válthatja fel a lejátszási listát a forgatókönyv engedélyezéséhez, olvassa el ezt a [blogbejegyzést.](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)

## <a name="next-steps"></a>További lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
