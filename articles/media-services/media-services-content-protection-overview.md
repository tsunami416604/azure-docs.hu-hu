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
ms.openlocfilehash: 6475a865b9d1b263bd7cc68c99acdb5f6959531e
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="protecting-content-overview"></a>Védett tartalom áttekintése
A Microsoft Azure Media Services lehetővé teszi a médiatartalmak védelmét attól a ponttól kezdve, ahogy az elhagyja a számítógépét, egészen a tároláson, a feldolgozáson és a továbbításon át. A Media Services lehetővé teszi az élő és igény szerinti tartalom Advanced Encryption Standard (AES-128) vagy a három fő DRM rendszerek dinamikusan titkosított: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services is biztosít egy szolgáltatás, amelynek segítségével a AES-kulcsok és DRM hitelesített ügyfelek (PlayReady, Widevine és FairPlay) licenceket. 

Az alábbi ábrán az Azure Media Services tartalomvédelem munkafolyamat. 

![Védelem biztosítása a PlayReadyvel](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Ez a cikk azt ismerteti, fogalmak és terminológiát ismertetése a védett tartalom az AMS kapcsolódik. A cikk ismertetik a tartalmak védelméhez hivatkozásokat is tartalmaz. 

## <a name="dynamic-encryption"></a>A dinamikus titkosítás
Az Azure Media Services lehetővé teszi, hogy az AES-kulcs törlése vagy DRM titkosítási dinamikusan titkosított tartalom: Microsoft PlayReady, Google Widevine és Apple FairPlay. Jelenleg titkosíthatja a következő formátumban: HLS, MPEG DASH vagy Smooth Streaming. Progresszív letöltés titkosítás nem támogatott. Minden titkosítási módszer a következő adatfolyam-továbbítási protokollok támogatja:
- AES: MPEG-DASH, Smooth Streaming vagy HLS
- PlayReady: MPEG-DASH, Smooth Streaming vagy HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Az objektum titkosítására, meg kell rendelje hozzá egy titkosítási tartalomkulcsot az eszköz és a kulcs az engedélyezési házirend is konfigurálhatja. Tartalomkulcs megadva, vagy a Media Services által automatikusan generált.

Azt is konfigurálnia kell az adategység továbbítási házirendjét. Ha azt szeretné, hogy adatfolyamként küldje el a tárolási titkosított eszköz, ellenőrizze, hogy hogyan kívánja kézbesíti úgy konfigurálja az adategység továbbítási házirendjét.

Adatfolyam egy player kér, amikor a Media Services dinamikus titkosítást a tartalom törlése kulcs AES titkosítással vagy DRM titkosítási használja a megadott kulcs. Az adatfolyam visszafejtése, a Windows Media player kér a kulcs AMS kulcs továbbítási szolgáltatást hozhat létre. Döntse el, hogy a felhasználó jogosult-e a kulcs eléréséhez, hogy a szolgáltatás értékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 tiszta kulcsot vs DRM
Az ügyfelek gyakran wonder, hogy azok használja-e az AES titkosítási vagy DRM rendszert. Az AES titkosítási és a DRM-rendszerek között az elsődleges különbség az, hogy AES titkosítással a tartalomkulcs átkerülnek a titkosítatlan formában ("a szövegként") az ügyfél. Ennek eredményeképpen a tartalom titkosításához használt kulcs egy hálózati nyomkövetést, az ügyfél nem titkosított szövegként tekinthető. AES-128 tiszta kulcsot alkalmas használata esetekben, amikor a jelentésmegjelenítő egy megbízható entitás (pl.: legyen az alkalmazottak által megtekinthető a vállalaton belül terjesztett vállalati videók titkosítása).

PlayReady Widevine és FairPlay AES-128 titkosítást képest magasabb szintű összes biztosítása törölje a titkosítás. A tartalomkulcs átkerülnek a titkosított formátumban. Visszafejtési pedig az operációs rendszer szintjén biztonságos környezetben leíró ahol jelentős mértékben megnehezíti a egy rosszindulatú felhasználó támadásoknak. DRM használati esetek, ahol az használatával nem lehet egy megbízható entitás, és szüksége van a legmagasabb szintű biztonság ajánlott.

## <a name="storage-encryption"></a>Storage-titkosítás
A tiszta tartalom helyileg az AES-256 bites titkosítás használata a tárolás titkosítása segítségével, és ezután töltse fel az Azure Storage helyén titkosítása. Storage-titkosítással védett adategységek automatikusan a titkosítás és helyezni egy titkosított fájlrendszerbe kódolás előtt, és egy új kimeneti eszközként feltöltés előtt esetleg újra titkosítja. A tárolás titkosítása elsődleges használati eset az, amikor biztonságossá tételéhez a kiváló minőségű bemeneti médiafájljait erős titkosítással aktívan a lemezen.

A tárolási titkosított eszköz kezelni, konfigurálnia kell az adategység továbbítási házirendjét, a Media Services tudja, hogyan kívánja a tartalom. Mielőtt az eszköz továbbítható, a streamelési kiszolgáló visszafejti, és az adatfolyamokat a tartalmat a megadott továbbítási házirendjét (például AES, általános titkosítás vagy titkosítás nélkül) használatával.

## <a name="types-of-encryption"></a>Titkosítási típusok
PlayReady és Widevine használatára a Common Encryption (AES Parancsra mód). FairPlay AES CBC módban titkosítást használja. AES-128 egyértelműen kulcstitkosítási boríték titkosítást használja.

## <a name="licenses-and-keys-delivery-service"></a>Licencek és a kulcsok kézbesítési szolgáltatás
A Media Services (PlayReady, Widevine, FairPlay) DRM-licencek és AES-kulcsok kézbesítéséhez hitelesített ügyfelek kulcs kézbesítési szolgáltatást nyújt. Használhat [az Azure-portálon](media-services-portal-protect-content.md), REST API vagy Media Services SDK for .NET a licencekkel és a kulcsok hitelesítési és engedélyezési házirend-beállításokkal.

## <a name="control-content-access"></a>Tartalom elérés
Azt is szabályozhatja, hogy ki férhet hozzá a tartalmat úgy konfigurálja a tartalomkulcs-hitelesítési szabályzatot. A tartalomkulcs-hitelesítési házirend nyitott vagy token korlátozás támogatja.

### <a name="open-authorization"></a>Megnyitás engedélyezése
Egy nyitott engedélyezési házirend a tartalomkulcs esetén a rendszer küld minden olyan ügyfél (korlátozás).

### <a name="token-authorization"></a>Engedélyezési jogkivonat
Jogkivonat-korlátozott engedélyezési házirendnek a tartalomkulcs csak akkor egy ügyfél, amely egy érvényes JSON webes jogkivonat (JWT) vagy egy egyszerű webes jogkivonat (SWT) jeleníti meg a kulcs/licenc kérelem küldése. Ez a token által a Secure Token Service (STS) kell megadni. Microsoft Active Directory használata az STS szolgáltatással, vagy egy egyéni STS telepítése. Az STS be kell állítani a megadott kulcs és a probléma JOGCÍMEKKEL, amely a token korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcs kézbesítési szolgáltatás a kért kulcs/licencfeltételeket visszatér az ügyfélhez, ha a jogkivonat érvényes, és a jogkivonatában lévő jogcímeket pontosan megegyeznek a kulcs/licencfeltételeket konfigurálva.

Amikor a jogkivonat konfigurálása korlátozzák a házirend, az elsődleges hitelesítési kulcs, a kibocsátó és a célközönség paramétereket kell megadnia. Az elsődleges hitelesítési kulcs, amely a token aláírt kulcsot tartalmazza, a kibocsátó a biztonságos biztonságijogkivonat-szolgáltatás, amely kibocsátja a jogkivonatot. A célközönséget, más néven a hatókör, ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a token egyeznek-e a sablonban szereplő értékeket.

## <a name="streaming-urls"></a>Adatfolyam-továbbítási URL-címek
Ha az objektum egynél több DRM lett titkosítva, egy titkosítási címke használjon az adatfolyam-továbbítási URL-cím: (formátum = "m3u8-aapl" titkosítási = "xxx").

A következők érvényesek:
* Legfeljebb egy titkosítási típus adható meg.
* Titkosítási típus nem kell az URL-cím adható meg, ha csak egy titkosítási alkalmazta az eszközhöz.
* Titkosítási típus-és nagybetűket.
* A következő titkosítási típusok adhatók meg:  
  * **cenc**: a PlayReady vagy Widevine (Common Encryption)
  * **cbcs-aapl**: a FairPlay (AES CBC titkosítást)
  * **CBC**: AES envelope titkosítás (boríték titkosítás)

## <a name="next-steps"></a>Következő lépések
Az alábbi cikkek tartalomvédelem használatbavételének első lépések ismertetik:
* [A tárolás titkosítása védelme](media-services-rest-storage-encryption.md)
* [Az AES titkosítási védelme](media-services-protect-with-aes128.md)
* [PlayReady és/vagy Widevine védelme](media-services-protect-with-playready-widevine.md)
* [FairPlay védelme](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services PlayReady licenc kézbesítési árképzési ismertetése](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Az Azure Media Services-AES titkosított adatfolyam hibakeresése](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT jogkivonat hitelesítési](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Azure Media Services OWIN MVC-alapú alkalmazások integrálása az Azure Active Directoryban, és korlátozhatja a JWT jogcímei alapján kulcs tartalomkézbesítési](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
