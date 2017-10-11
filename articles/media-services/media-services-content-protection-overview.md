---
title: "Védve a tartalmat az Azure Media Services |} Microsoft Docs"
description: "Ez a cikk áttekintést a Media Services tartalomvédelem."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 64be4ea104bd11b8e191e2c8d4170a2de88acb47
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="protecting-content-overview"></a>Védett tartalom áttekintése
A Microsoft Azure Media Services lehetővé teszi a médiatartalmak védelmét attól a ponttól kezdve, ahogy az elhagyja a számítógépét, egészen a tároláson, a feldolgozáson és a továbbításon át. A Media Services lehetővé teszi az élő és igény szerinti tartalom Advanced Encryption Standard (AES) (a 128 bites titkosítási kulcsok használatával) vagy a fő DRMs bármelyikét dinamikusan titkosított: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services is biztosít egy szolgáltatás, amelynek segítségével a AES-kulcsok és DRM hitelesített ügyfelek (PlayReady, Widevine és FairPlay) licenceket. 

Az alábbi képen a tartalomvédelem-munkafolyamatok, amelyek AMS támogatja. 

![Védelem biztosítása a PlayReadyvel](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

Ez a témakör azt ismerteti, [fogalmakat és terminológiát](media-services-content-protection-overview.md) ismertetése a védett tartalom az AMS kapcsolódik. A témakör is tartalmaz [hivatkozások](media-services-content-protection-overview.md#common-scenarios) témakörökre mutatnak, amelyek bemutatják, hogyan tartalomvédelmi feladatok eléréséhez. 

## <a name="dynamic-encryption"></a>A dinamikus titkosítás
A Microsoft Azure Media Services lehetővé teszi, hogy az AES-kulcs törlése vagy DRM titkosítási dinamikusan titkosított tartalom: Microsoft PlayReady, Google Widevine és Apple FairPlay.

Jelenleg titkosíthatja a következő formátumban: HLS, MPEG DASH vagy Smooth Streaming. Progresszív letöltés nem titkosítható.

Ha azt szeretné, a Media Services az objektum titkosítására, meg kell rendelje hozzá egy titkosítási kulcsot (CommonEncryption vagy EnvelopeEncryption) az eszköz és engedélyezési házirendeket, a kulcs is konfigurálhatja.

Azt is konfigurálnia kell az adategység továbbítási házirendjét. Ha azt szeretné, hogy adatfolyamként küldje el a tárolási titkosított eszköz, ellenőrizze, hogy hogyan kívánja kézbesíti úgy konfigurálja az adategység továbbítási házirendjét.

Adatfolyam egy player kér, amikor a Media Services dinamikus titkosítást a tartalom törlése kulcs AES titkosítással vagy DRM titkosítási használja a megadott kulcs. Az adatfolyam visszafejtése, a Windows Media player a kulcs kézbesítési szolgáltatás fog igényelni a kulcsot. Döntse el, hogy a felhasználó jogosult-e a kulcs eléréséhez, hogy a szolgáltatás értékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.


## <a name="storage-encryption"></a>Tárolás titkosítása
Tárolás titkosítása segítségével a tiszta tartalom helyileg az AES 256 bites titkosítás használata, majd töltse fel az Azure Storage helyén titkosítása. Storage-titkosítással védett adategységek automatikusan a titkosítás és helyezni egy titkosított fájlrendszerbe kódolás előtt, és egy új kimeneti eszközként feltöltés előtt esetleg újra titkosítja. A tárolás titkosítása elsődleges használati eset az, amikor biztonságossá tételéhez a kiváló minőségű bemeneti médiafájljait erős titkosítással aktívan a lemezen.

A tárolási titkosított eszköz kezelni, konfigurálnia kell az adategység továbbítási házirendjét, a Media Services tudja, hogyan kívánja a tartalom. Mielőtt az eszköz továbbítható, a streamelési kiszolgáló eltávolítja a tárolás titkosítása, és az adatfolyamokat a tartalmat a megadott továbbítási házirendjét (például AES, általános titkosítás vagy titkosítás nélkül) használatával.

## <a name="common-encryption-cenc"></a>Common encryption (CENC)
Közös titkosítás titkosításához a tartalmaknak a PlayReady vagy / és Widewine.

## <a name="using-cbcs-aapl-encryption"></a>Cbcs-aapl titkosítással
Cbcs-aapl FairPlay a tartalom titkosításához használatos.

## <a name="envelope-encryption"></a>Boríték titkosítás
Használja ezt a beállítást, ha azt szeretné, hogy a tartalmak védelméhez AES-128 tiszta kulcs használatával. Ha azt szeretné, hogy egy biztonságosabb beállítás, válassza ki a DRMs egyik szerepel ebben a témakörben. 

## <a name="licenses-and-keys-delivery-service"></a>Licencek és a kulcsok kézbesítési szolgáltatás
Media Services (PlayReady, Widevine, FairPlay) DRM-licencek kézbesítéséhez szolgáltatást nyújt, és AES törölje az arra jogosult ügyfelek kulccsal. Használhat [az Azure-portálon](media-services-portal-protect-content.md), REST API vagy Media Services SDK for .NET a licencekkel és a kulcsok hitelesítési és engedélyezési házirend-beállításokkal.

## <a name="token-restriction"></a>Token korlátozása
A tartalomkulcs-hitelesítési szabályzat egy vagy több hitelesítési korlátozást tartalmazhat: ezek lehetnek nyitott vagy jogkivonat-korlátozások. A tokennel korlátozott szabályzatokhoz a Secure Token Service (Biztonsági jegykiadó szolgáltatás, STS) által kiadott tokennek kell tartoznia. A Media Services Simple Web Tokens (SWT) és a JSON webes jogkivonat (JWT) formátumú tokeneket támogatja. A Media Services nem nyújt Secure Token szolgáltatásokat. Hozzon létre egy egyéni STS, vagy probléma jogkivonatokat a Microsoft Azure ACS kihasználja. Az STS be kell állítani a megadott kulcs és a probléma JOGCÍMEKKEL, amely a token korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcs kézbesítési szolgáltatás visszaáll a kért (vagy licencelési) az ügyfél, ha a jogkivonat érvényes, és a jogcímek, az token találat azokat konfigurált kulcsot (vagy licenc).

Amikor a jogkivonat konfigurálása korlátozzák a házirend, az elsődleges hitelesítési kulcs, a kibocsátó és a célközönség paramétereket kell megadnia. Az elsődleges hitelesítési kulcs, amely a token aláírt kulcsot tartalmazza, a kibocsátó a biztonságos biztonságijogkivonat-szolgáltatás, amely kibocsátja a jogkivonatot. A célközönség (más néven hatókör) ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a token egyeznek-e a sablonban szereplő értékeket.

## <a name="streaming-urls"></a>Adatfolyam-továbbítási URL-címek
Ha az objektum egynél több DRM lett titkosítva, egy titkosítási címke használjon az adatfolyam-továbbítási URL-cím: (formátum = "m3u8-aapl" titkosítási = "xxx").

A következők érvényesek:

* Csak nulla vagy egy titkosítási típus adható meg.
* Titkosítási típus nem kell az URL-cím adható meg, ha csak egy titkosítási alkalmazta az eszközhöz.
* Titkosítási típus-és nagybetűket.
* A következő titkosítási típusok adhatók meg:  
  * **cenc**: Common encryption (Playready vagy Widevine)
  * **cbcs-aapl**: Fairplay
  * **CBC**: AES envelope titkosítást.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek
A következő témakörök bemutatják, hogyan lehet a tartalom védelme a tárolón, dinamikusan titkosított folyamatos médiatovábbítás használja AMS kulcs/licenctovábbítási szolgáltatásra

* [AES védelme](media-services-protect-with-aes128.md) 
* [PlayReady és/vagy Widevine védelme](media-services-protect-with-drm.md)
* [A HLS tartalom védett Apple FairPlay és/vagy a PlayReady-adatfolyam](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>További helyzetek
* [Azure PlayReady licencelési szolgáltatás integrálása a saját titkosító/streamelési kiszolgáló](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
* [CastLabs DRM-licencek kézbesíthet Azure Media Services használatával](media-services-castlabs-integration.md)

>[!NOTE]
>A forgatókönyv külső DRM server(technology) és AMS kapott használatához jelenleg nem támogatott.


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Szolgáltatás-és AES az Azure Media Services dinamikus titkosítást a PlayReady bejelentése](http://mingfeiy.com/playready)

[Az Azure Media Services PlayReady licenc kézbesítési árképzési ismertetése](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Az Azure Media Services-AES titkosított adatfolyam hibakeresése](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT jogkivonat authenitcation](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Azure Media Services OWIN MVC-alapú alkalmazások integrálása az Azure Active Directoryban, és korlátozhatja a JWT jogcímei alapján kulcs tartalomkézbesítési](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Azure ACS használatával probléma jogkivonatok](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
