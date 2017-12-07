---
title: "Azure Media Services hitelesítési tokent átadni |} Microsoft Docs"
description: "Ismerje meg, hogyan küldhet a hitelesítési tokenek az ügyfél az Azure Media Services kulcs kézbesítési szolgáltatás"
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
ms.openlocfilehash: 0e56726266898e5738dd797a8a019987d457170e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="how-clients-pass-tokens-to-azure-media-services-key-delivery-service"></a>Az ügyfelek hogyan jogkivonatok átadni Azure Media Services kulcs kézbesítési szolgáltatás
Folyamatosan kapunk körül hogyan egy player sikerült átadni token kulcs kézbesítési szolgáltatásokba, amely fogja lekérni ellenőrzése kérdések, és a Windows Media player beolvassa a kulcs. Támogatott egyszerű webes jogkivonat (SWT) és a JSON webes jogkivonat (JWT) ezek két token formátum. Jogkivonat hitelesítési kulcs bármilyen alkalmazható – függetlenül végez Common Encryption és az AES envelope titkosítási a rendszerben.

Ezek a következő módon sikerült adja át a jogkivonatot a lejátszóval, player és célplatformokhoz függ:
- A HTTP-hitelesítési fejlécéhez.
> [!NOTE]
> Vegye figyelembe, hogy a "tulajdonos" előtag az OAuth 2.0 specifikációk / várt. Van egy Azure Media Player a(z) Token konfigurációs minta játékosok [bemutató oldal](http://ampdemo.azureedge.net/). Válasszon, AES (JWT jogkivonat), vagy a AES (SWT Token) videoforrást állíthat be. Token Authorization fejlécet keresztül lett átadva.

- Az URL-cím lekérdezési paraméter hozzáadásával keresztül "token = tokenvalue".  
> [!NOTE]
> Vegye figyelembe, hogy nincs "Tulajdonos" előtag várt. Token URL-címet keresztül küld el, mivel szüksége lesz a lexikális elem karakterlánca amelyeket védelmére. Íme egy C# mintakód a menete:

```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
```

- A CustomData mező.
PlayReady licenc beszerzési csak, a PlayReady licenc beszerzési kihívás az CustomData mezője révén. Ebben az esetben a jogkivonat az XML-dokumentumot, az alábbiakban belül kell lennie.

```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
```
A hitelesítési jogkivonat be a <Token> elemet.

- Keresztül egy másik HLS-listát. Ha szeretné-e az AES + HLS tokent használó hitelesítés konfigurálása a lejátszás iOS/Safari böngészőben oly módon, közvetlenül küldhet a jogkivonat nem létezik. Lásd: a [blogbejegyzés](http://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) számára, hogy a lista ezt a forgatókönyvet ismerteti.

## <a name="next-steps"></a>Következő lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]