---
title: Védve a tartalmat az Azure Media Services |} Microsoft Docs
description: Ez a cikk áttekintést a Media Services tartalomvédelem.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 13447fd9193374d80ed5c2e6af8543f11b95e709
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="content-protection-overview"></a>A védett tartalom áttekintése
 Azure Media Services segítségével biztonságos az adathordozó a tárhely, feldolgozás és kézbesítési keresztül elhagyja óta. A Media Services, az élő és igény tartalom Advanced Encryption Standard (AES-128) vagy a három fő digitális tartalomvédelmi (DRM) felügyeleti rendszerek dinamikusan titkosított biztosíthat: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services is biztosít egy szolgáltatás, amelynek segítségével a AES-kulcsok és DRM hitelesített ügyfelek (PlayReady, Widevine és FairPlay) licenceket. 

Az alábbi ábrán a Media Services tartalomvédelem-munkafolyamat: 

![Védelem biztosítása a PlayReadyvel](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Ez a cikk azt ismerteti, fogalmak és terminológiát ismertetése a védett tartalom Media Services kapcsolódik. A cikk ismertetik a tartalmak védelméhez hivatkozásokat is tartalmaz. 

## <a name="dynamic-encryption"></a>Dinamikus titkosítás
 A Media Services segítségével a tartalom titkosított dinamikusan AES-kulcs törlése vagy DRM-titkosítást a PlayReady, Widevine vagy FairPlay. Jelenleg a HTTP-Live Streaming (HLS), MPEG DASH vagy Smooth Streaming formátumban titkosíthatók. Progresszív letöltés titkosítás nem támogatott. Minden titkosítási módszer a következő adatfolyam-továbbítási protokollok támogatja:

- AES: MPEG-DASH, Smooth Streaming vagy HLS
- PlayReady: MPEG-DASH, Smooth Streaming vagy HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Az objektum titkosítására, meg kell rendelje hozzá egy titkosítási tartalomkulcsot az eszköz és a kulcs az engedélyezési házirend is konfigurálhatja. Tartalomkulcs megadva, vagy a Media Services által automatikusan generált.

Azt is konfigurálnia kell az adategység továbbítási házirendjét. Ha azt szeretné, hogy adatfolyamként küldje el a tárolási titkosított eszköz, ellenőrizze, hogy hogyan kívánja kézbesíti úgy konfigurálja az adategység továbbítási házirendjét.

Adatfolyam egy player kér, amikor Media Services a megadott kulcs segítségével dinamikusan titkosítani az AES tiszta kulcsot vagy DRM-titkosítás segítségével. Az adatfolyam visszafejtés, a Windows Media player kér a kulcs Media Services kulcs kézbesítési szolgáltatás. Döntse el, hogy a felhasználó jogosult-e a kulcs eléréséhez, hogy a szolgáltatás értékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128 egyértelműen kulcs vs. DRM
Az ügyfelek gyakran wonder, hogy azok használja-e az AES titkosítási vagy DRM rendszert. Az elsődleges különbség a két rendszer között, hogy az AES titkosítási a tartalomkulcs átkerülnek az ügyfél egy nem titkosított formátumban ("a clear"). Ennek eredményeképpen a tartalom titkosításához használt kulcs tekintheti meg a hálózati nyomkövetés egyszerű szöveges az ügyfélen. AES-128 egyértelműen kulcstitkosítási alkalmas használata esetekben, amikor a jelentésmegjelenítő egy megbízható entitás (például titkosított vállalati videók legyen az alkalmazottak által megtekinthető a vállalaton belül terjesztett).

PlayReady Widevine és FairPlay AES-128 titkosítást képest magasabb szintű összes biztosítása törölje a titkosítás. A tartalomkulcs átkerülnek a titkosított formátumban. Emellett visszafejtési az operációs rendszer szintjén, ott, ahol egy rosszindulatú felhasználó támadható meg a biztonságos környezetben kell kezelni. DRM használati esetek, ahol az használatával nem lehet egy megbízható entitás, és szüksége van a legmagasabb szintű biztonság ajánlott.

## <a name="storage-encryption"></a>Storage-titkosítás
Tárolás titkosítása segítségével titkosíthatja a tiszta tartalom helyileg AES 256 bites titkosítás használata. Majd feltöltheti az Azure Storage, hol tárolják titkosítása. Storage-titkosítással védett adategységek automatikusan titkosítás és egy titkosított fájlrendszerbe kódolás előtt helyezve. Az eszközök olyan opcionális újra titkosítani, egy új kimeneti eszközként feltöltés előtt. A tárolás titkosítása elsődleges használati eset az, amikor biztonságossá tételéhez a kiváló minőségű bemeneti médiafájljait erős titkosítással aktívan a lemezen.

Egy tároló által titkosított eszköz képes biztosítani, konfigurálnia kell az adategység továbbítási házirendjét, hogy a Media Services tudja, hogyan kívánja a tartalom. Mielőtt az eszköz továbbítható, a streamelési kiszolgáló visszafejti, és a tartalom az adatfolyamokat, a megadott továbbítási házirendjét (például AES, általános titkosítás vagy titkosítás nélkül) használatával.

## <a name="types-of-encryption"></a>Titkosítási típusok
PlayReady és Widevine használatára közös titkosítás (AES Parancsra mód). FairPlay CBC módban AES titkosítást használja. AES-128 egyértelműen kulcstitkosítási boríték titkosítást használja.

## <a name="licenses-and-keys-delivery-service"></a>Licencek és a kulcsok kézbesítési szolgáltatás
A Media Services (PlayReady, Widevine, FairPlay) DRM-licencek és AES-kulcsok kézbesítéséhez hitelesített ügyfelek kulcs kézbesítési szolgáltatást nyújt. Használhat [az Azure-portálon](media-services-portal-protect-content.md), a REST API-t, vagy a Media Services SDK for .NET a licencekkel és a kulcsok hitelesítési és engedélyezési házirend-beállításokkal.

## <a name="control-content-access"></a>Tartalom elérés
Azt is szabályozhatja, hogy ki férhet hozzá a tartalmat úgy konfigurálja a tartalomkulcs-hitelesítési szabályzatot. A tartalomkulcs-hitelesítési házirend nyitott vagy token korlátozás támogatja.

### <a name="open-authorization"></a>Megnyitás engedélyezése
Egy nyitott engedélyezési házirend a tartalomkulcs esetén a rendszer küld minden olyan ügyfél (korlátozás).

### <a name="token-authorization"></a>Engedélyezési jogkivonat
Jogkivonat-korlátozott engedélyezési házirendnek a tartalomkulcs érkezik csak egy ügyfél, amely a kulcs/licenc kérelem jeleníti meg egy érvényes JSON webes jogkivonat (JWT) vagy egyszerű webes jogkivonat (SWT). Ez a token biztonságijogkivonat-szolgáltatás (STS) kell kiállítania. Az Azure Active Directoryt az STS-ként, vagy egy egyéni STS telepítése. Az STS be kell állítani a megadott kulcs és a probléma JOGCÍMEKKEL, amely a token korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcs kézbesítési szolgáltatás a kért kulcs/licencfeltételeket az ügyfélnek ad vissza, ha a jogkivonat érvényes, és a jogkivonatában lévő jogcímeket pontosan megegyeznek a kulcs/licencfeltételeket konfigurálva.

A token korlátozott házirendjének konfigurálásakor adjon meg, hogy az elsődleges hitelesítési kulcs, a kibocsátó és a célközönség paramétereket. Az elsődleges hitelesítési kulcs, amely a token aláírt kulcsot tartalmazza. A nem a biztonságos biztonságijogkivonat-szolgáltatás, amely kibocsátja a jogkivonatot. A célközönséget, más néven a hatókör, ismerteti a jogkivonat a leképezést, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcs kézbesítési szolgáltatás ellenőrzi, hogy ezek az értékek a token egyeznek-e a sablonban szereplő értékeket.

## <a name="streaming-urls"></a>Streamelési URL-ek
Az objektum egynél több DRM lett titkosítva, használjon egy titkosítási címke a streamelési URL-cím: (formátum = "m3u8-aapl" titkosítási = "xxx").

A következők érvényesek:

* Legfeljebb egy titkosítási típus adható meg.
* Titkosítási típus nem kell az URL-cím adható meg, ha csak egy titkosítási alkalmazta az eszközhöz.
* Titkosítási típus-és nagybetűket.
* A következő titkosítási típusok adhatók meg:
  * **cenc**: A PlayReady vagy Widevine (common encryption)
  * **cbcs-aapl**: A FairPlay (AES CBC titkosítást)
  * **CBC**: az AES envelope titkosítás

## <a name="next-steps"></a>További lépések
Az alábbi cikkek segítséget nyújtanak a védett tartalom az első lépései a következő lépések ismertetik:

* [A tárolás titkosítása védelme](media-services-rest-storage-encryption.md)
* [Az AES titkosítási védelme](media-services-protect-with-aes128.md)
* [PlayReady és/vagy Widevine védelme](media-services-protect-with-playready-widevine.md)
* [FairPlay védelme](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Kapcsolódó hivatkozások

* [Az Azure Media Services PlayReady licenc kézbesítési árképzési ismertetése](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)
* [Az Azure Media Services-AES titkosított adatfolyam hibakeresése](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)
* [JWT jogkivonat hitelesítési](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Azure Media Services OWIN MVC-alapú alkalmazások integrálása az Azure Active Directoryban, és korlátozhatja a kulcs továbbítási JWT jogcímei alapján](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
