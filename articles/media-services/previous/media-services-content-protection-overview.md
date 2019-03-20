---
title: A tartalmat az Azure Media Services |} A Microsoft Docs
description: Ez a cikk a Media Services content protection áttekintést.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 0d37f0596069899bbeb9dfeafa5d487aee910cd8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183662"
---
# <a name="content-protection-overview"></a>A Content protection áttekintése 

 Az Azure Media Services segítségével az az idő, akkor hagyja, hogy a számítógép tárolási, feldolgozási és kézbesítési a médiatartalmak védelmét. A Media Services élő és igény szerinti tartalmait az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek bármelyikét dinamikusan titkosított juttathat el: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek. 

Az alábbi ábrán a Media Services content protection munkafolyamat: 

![Védelem biztosítása a PlayReadyvel](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

Ez a cikk ismerteti az alapelvek és fogalmak ismertetése a Media Services content protection kapcsolódik. A cikk a cikkek, amelyek ismertetik a tartalmak védelméhez mutató hivatkozásokat is tartalmaz. 

## <a name="dynamic-encryption"></a>Dinamikus titkosítás
 A Media Services segítségével dinamikusan az AES-kulcsok vagy titkosított DRM-titkosítást a PlayReady, Widevine vagy FairPlay használatával a tartalmat. Jelenleg hogy titkosítsa a HTTP Live Streaming (HLS), MPEG DASH és Smooth Streaming formátumban. A progresszív letöltés titkosítás nem támogatott. Minden egyes titkosítási módszer a következő streamelési protokollokat támogatja:

- AES: MPEG-DASH, Smooth Streaming és HLS
- PlayReady: MPEG-DASH, Smooth Streaming és HLS
- Widevine: MPEG-DASH
- FairPlay: HLS

Az objektum titkosítására, meg kell rendel hozzá egy titkosítási tartalomkulcsot az objektumot, és is konfigurálhatja a kulcs az engedélyezési házirend. Tartalomkulcs megadott vagy a Media Services által automatikusan generált is.

Azt is konfigurálnia kell az adategység továbbítási házirendjét. Ha azt szeretné, a storage-titkosítású eszköz streamelésére, ügyeljen arra, hogy adja meg, hogyan konfigurálja az adategység továbbítási házirendjét küldéséhez.

Adatfolyam-lejátszó kér, amikor a Media Services a megadott kulcs használatával dinamikusan titkosítani a titkosítatlan AES vagy a DRM-titkosítás segítségével. A stream visszafejteni, az a Windows Media player a kulcs kér a Media Services kulcstovábbítást. Annak eldöntéséhez, hogy-e a felhasználó jogosult-e a kulcs lekérése, a szolgáltatás kiértékeli az engedélyezési házirendeket, amelyek a kulcshoz megadott.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128-kulcsok vs. DRM
Ügyfeleink gyakran wonder, hogy használják inkább a AES-titkosítás vagy a DRM-rendszer. Az a két rendszer közötti elsődleges különbség az, hogy az AES-titkosítás a tartalomkulcs küldött a rendszer az ügyfél ("az a titkosítatlan") nem titkosított formátumban. Ennek eredményeképpen a tartalom titkosításához használt kulcs lehet megtekinteni a hálózati nyomkövetés egyszerű szöveges az ügyfélen. AES-128-kulcsok titkosítási ideális használati esetekben, amikor a jelentésmegjelenítő egy megbízható entitás (például titkosított vállalati videók megtekintését a az alkalmazottak által a vállalaton belül terjesztett).

PlayReady, Widevine és FairPlay összes titkosítási képest magasabb szintű adja meg az AES-128 titkosítatlan kulcsokat titkosítás. A tartalomkulcs továbbított titkosított formában. Ezenkívül a visszafejtési az operációs rendszer szintjén, ahol nehezebb a támadásokkal szemben egy rosszindulatú felhasználó biztonságos környezetben történik. DRM ajánlott használati esetek, ahol a megjelenítő lehetséges, hogy nem egy megbízható entitás, és Ön a legmagasabb szintű biztonságot követelhetnek meg.

## <a name="storage-encryption"></a>Storage-titkosítás
Storage-titkosítás segítségével titkosítja a tiszta tartalom helyileg 256 bites AES-titkosítás segítségével. Ezután feltöltheti azt az Azure Storage, hol tárolják titkosítása. Storage-titkosítással védett adategységek automatikus titkosítás és kódolás előtt egy titkosított fájlrendszerbe kerülnek. Az eszközök olyan igény szerint újra titkosítani; az új kimeneti adategységként való feltöltés előtt. Az elsődleges használati eset, a tárolás titkosítása akkor, ha biztonságos, kiváló minőségű bemeneti médiafájlok az erős titkosítás inaktív állapotban a lemezen.

A storage-titkosítású eszköz biztosításához, konfigurálnia kell az adategység továbbítási házirendjét, hogy a Media Services tudja, hogyan szeretné a tartalmat. Az eszközintelligencia továbbítható, mielőtt a streamelési kiszolgáló visszafejti, és streameli a tartalmat a megadott objektumtovábbítási szabályzat (például AES, common Encryption titkosítás vagy titkosítás nélkül) használatával.

## <a name="types-of-encryption"></a>Titkosítási típusok
PlayReady és Widevine használatára az általános titkosítás (AES Parancsra mód). FairPlay AES-CBC-mód titkosítást használja. AES-128-kulcsok titkosítási boríték-titkosítást használja.

## <a name="licenses-and-keys-delivery-service"></a>Licencek és a kulcsok kézbesítési szolgáltatás
A Media Services DRM (PlayReady, Widevine, FairPlay) licencek és az AES-kulcsok kézbesítéséhez a hitelesített ügyfelek kulcskézbesítési szolgáltatást biztosít. Használhat [az Azure Portalon](media-services-portal-protect-content.md), a REST API vagy .NET-keretrendszerhez készült Media Services SDK konfigurálhat a licencekkel és a kulcsok a hitelesítési és engedélyezési házirendeket.

## <a name="control-content-access"></a>Tartalom-hozzáférés vezérlése
Szabályozhatja, hogy ki férhet hozzá a tartalmat a tartalomkulcs-hitelesítési szabályzat konfigurálásával. A tartalomkulcs-hitelesítési szabályzat ezek lehetnek nyitott vagy jogkivonat korlátozás támogatja.

### <a name="open-authorization"></a>Megnyitás engedélyezése
Egy megnyitott engedélyezési házirend a tartalomkulcs küldött bármely ügyfél (nincs korlátozás).

### <a name="token-authorization"></a>Engedélyezési jogkivonat
A jogkivonattal korlátozott engedélyezési házirend a tartalomkulcs küldendő csak egy ügyfél, amely egy érvényes JSON webes jogkivonat (JWT) vagy simple web Tokens (SWT) jeleníti meg a kulcs kérésben. Ez a token biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) kell kiállítaniuk. Használja az Azure Active Directory, az STS szolgáltatással, vagy egy egyéni STS üzembe helyezése. Az STS-re kell állítani a megadott kulcs és a probléma jogcímek jogkivonat korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcstovábbítást adja vissza a kért kulcs licenc az ügyfélnek, ha a jogkivonat érvényes, és a jogkivonat jogcímeiben megfelelnek a kulcs/licenc.

A tokennel korlátozott szabályzatokhoz konfigurálásakor adjon meg, hogy az elsődleges ellenőrzőkulcs, a kibocsátó és a célközönség paramétereket. Az elsődleges ellenőrzőkulcs tartalmazza a kulcsot, a jogkivonat írták-e. A kibocsátó a biztonságos jogkivonat-szolgáltatás, amely a jogkivonatot. A célközönség, más néven a hatókör, ismerteti a token szándéka, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcstovábbítást ellenőrzi, hogy ezeket az értékeket a jogkivonat egyezik a sablonban szereplő értékeket.

## <a name="streaming-urls"></a>Streamelési URL-ek
Az eszköz titkosítva van az egynél több DRM, használjon egy titkosítási címke a streamelési URL-cím: (formátum = "m3u8-aapl" titkosítási = "xxx").

A következő szempontokat kell figyelembe venni:

* Legfeljebb egy titkosítási típus adható meg.
* Titkosítási típus nem kell az URL-cím adható meg, ha csak az eszköz egy titkosítási lett alkalmazva.
* Titkosítási típus megkülönbözteti a kis-és nagybetű nincs megkülönböztetve.
* A következő titkosítási típusok adható meg:
  * **cenc**: A PlayReady vagy Widevine (common encryption)
  * **cbcs-aapl**: A fairplay rendszerhez (AES-CBC titkosítás)
  * **cbc**: A boríték AES-titkosítás

## <a name="next-steps"></a>További lépések
Az alábbi cikkek segítséget nyújtanak a content protection használatának első lépései a következő lépések ismertetik:

* [A storage encryption védelme](media-services-rest-storage-encryption.md)
* [Az AES-titkosítással védeni](media-services-protect-with-aes128.md)
* [Védelem PlayReady és/vagy Widevine](media-services-protect-with-playready-widevine.md)
* [FairPlay védelme](media-services-protect-hls-with-FairPlay.md)

## <a name="related-links"></a>Kapcsolódó hivatkozások

* [JWT jogkivonat-hitelesítés](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
* [Az Azure Media Services OWIN MVC-alapú alkalmazások integrálása az Azure Active Directory és a JWT jogcímszolgáltatói alapján kulcs tartalomkézbesítési korlátozása](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
