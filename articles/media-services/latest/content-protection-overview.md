---
title: A tartalmat a Media Services – Azure |} A Microsoft Docs
description: Ez a cikk a Media Services content protection áttekintést.
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
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 09c8b63cddde547b0dc42cf1f15a8ac5002d0a3e
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725138"
---
# <a name="content-protection-overview"></a>A Content protection áttekintése

Az Azure Media Services segítségével az az idő, akkor hagyja, hogy a számítógép tárolási, feldolgozási és kézbesítési a médiatartalmak védelmét. A Media Services élő és igény szerinti tartalmait az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek bármelyikét dinamikusan titkosított juttathat el: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek. 

Az alábbi ábrán a Media Services content protection munkafolyamat: 

![Tartalom védelme](./media/content-protection/content-protection.png)

&#42;*a dinamikus titkosítás AES-128 "tiszta kulcsot" CBCS és CENC támogatja. További részletekért lásd: a támogatási mátrix [Itt](#streaming-protocols-and-encryption-types).*

Ez a cikk ismerteti az alapelvek és fogalmak ismertetése a Media Services content protection kapcsolódik. A cikk is rendelkezik a [– gyakori kérdések](#faq) szakaszt, és cikkeket, amelyek bemutatják a tartalmak védelméhez mutató hivatkozásokat tartalmaz. 

## <a name="main-components-of-the-content-protection-system"></a>A content protection rendszer fő összetevői

Fejezze be a "content protection" rendszer vagy alkalmazás-tervezés, teljes ismertetése az részéről az erőfeszítés hatókörének kell. Az alábbi lista áttekintést ad végrehajtására kellene három részből áll. 

1. Az Azure Media Services-kódot
  
  * A PlayReady, Widevine és/vagy FairPlay licenc sablonok. A sablonok segítségével jogosultságai és engedélyei a használt DRMs mindegyike konfigurálása
  * Licenc kézbesítési engedélyezési, adja meg a JWT jogcímszolgáltatói alapján jogosultsági ellenőrzés logikája
  * Tartalomkulcs, adatfolyam-továbbítási protokollok és a megfelelő DRMs a alkalmazni, meghatározó DRM-titkosítás

  > [!NOTE]
  > Minden objektumot több titkosítási típussal titkosíthat (AES-128, PlayReady, Widevine, FairPlay). A [streamelési protokollokkal és a titkosítási típusokkal](#streaming-protocols-and-encryption-types) kapcsolatos szakaszban megtekintheti, hogy mit mivel érdemes kombinálni.
  
  A következő cikkek az AES és/vagy DRM tartalmak megjelenítése az titkosításához lépéseket: 
  
  * [Az AES-titkosítással védeni](protect-with-aes128.md)
  * [DRM védelme](protect-with-drm.md)

2. A Player AES vagy DRM-ügyféllel. Egy videolejátszó alkalmazást, a lejátszó SDK-t (natív vagy böngészőalapú) alapján kell az alábbi követelményeknek:
  * A lejátszó SDK támogatja a szükséges DRM-ügyfelek
  * A lejátszó SDK-t a szükséges streamelési protokollokat támogatja: A Smooth, DASH és/vagy a HLS
  * A lejátszó SDK képesnek kell lennie a JWT jogkivonat passing licenc beszerzési kérés kezelésére
  
    A player használatával hozhat létre a [az Azure Media Player API](http://amp.azure.net/libs/amp/latest/docs/). Használja a [az Azure Media Player ProtectionInfo API](http://amp.azure.net/libs/amp/latest/docs/) , adja meg, melyik DRM-technológiával használandó DRM különböző platformokon.

    A tesztelési AES vagy CENC (Widevine és/vagy a PlayReady) titkosított tartalmat használhatja [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Győződjön meg arról, kattintson a "Speciális beállítások", és ellenőrizze a titkosítási beállításokat.

    Ha titkosított FairPlay teszttartalmat szeretne, használja a [ezen teszt player](https://aka.ms/amtest). A Windows Media player támogatja, a PlayReady, Widevine és FairPlay DRMs, valamint az AES-128 titkosítatlan kulcs titkosítás. Válassza ki a megfelelő böngészőt másik DRMs teszteléséhez szüksége: Chrome/Opera/Firefox Widevine, MS Edge/IE11 a PlayReady, a Safari a fairplay rendszerhez macOS rendszeren.

3. A biztonságos jogkivonat-szolgáltatás (STS), amely JSON webes jogkivonat (JWT), a hozzáférési jogkivonatot az háttérbeli erőforrások eléréséhez. Az AMS licenctovábbítási szolgáltatások a háttérerőforrásra is használhatja. Az STS szolgáltatással rendelkezik, az alábbiak megadásához:

  * Kibocsátó és a célközönség (vagy hatókör)
  * A jogcímek, ami függ a content protection az üzleti követelmények
  * A szimmetrikus vagy aszimmetrikus ellenőrzési aláírás-ellenőrzés
  * Kulcsváltás támogatás (ha szükséges)

    Használható [az STS eszköz](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) tesztelési STS, amely támogatja az ellenőrzőkulcs 3 típusú: szimmetrikus, aszimmetrikus, vagy az AAD-kulcshoz kapcsolódó kulcsváltás. 

> [!NOTE]
> Azt javasoljuk hogy teljes körű tesztelését az egyes részek (lásd fent), és mielőtt a következő részére. A "content protection" rendszer teszteléséhez használja a fenti listában megadott eszközök.  

## <a name="streaming-protocols-and-encryption-types"></a>Adatfolyam-továbbítási protokollok és a titkosítási típusok

A Media Services segítségével dinamikusan az AES-kulcsok vagy titkosított DRM-titkosítást a PlayReady, Widevine vagy FairPlay használatával a tartalmat. Jelenleg hogy titkosítsa a HTTP Live Streaming (HLS), MPEG DASH és Smooth Streaming formátumban. Minden egyes protokollhoz a következő titkosítási módszereket támogatja:

|Protokoll|Tároló-formátum|Titkosítási sémával|
|---|---|---|---|
|MPEG-DASH|Összes|AES|
||CSF(fmp4) |CENC (Widevine + PlayReady) |
||CMAF(fmp4)|CENC (Widevine + PlayReady)|
|HLS|Összes|AES|
||MPG2 – TS |CBCS (Fairplay) |
||MPG2 – TS |CENC (PlayReady) |
||CMAF(fmp4) |CENC (PlayReady) |
|Smooth Streaming|fMP4|AES|
||fMP4 | CENC (PlayReady) |

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

A Media Services v3, tartalomkulcs StreamingLocator társítva (lásd: [ebben a példában](protect-with-aes128.md)). Ha a Media Services kulcstovábbítást használ, meg kell automatikus létrehozása a tartalomkulcsot. Kell létrehoznia a tartalomkulcs saját kezűleg használatakor, saját kulcstovábbítást, vagy ha egy magas rendelkezésre állású forgatókönyv kezel, amelyekben két adatközpontban tartalom ugyanazzal a kulccsal van szükségük.

Adatfolyam-lejátszó kér, amikor a Media Services a megadott kulcs használatával dinamikusan titkosítani a titkosítatlan AES vagy a DRM-titkosítás segítségével. Visszafejteni a streamet, a Windows Media player kér a kulcsot a Media Services kulcstovábbítást vagy a kulcstovábbítást megadott. Annak eldöntéséhez, hogy-e a felhasználó jogosult-e a kulcs lekérése, a szolgáltatás kiértékeli a tartalom a kulcshoz megadott kulcs házirend.

## <a name="aes-128-clear-key-vs-drm"></a>AES-128-kulcsok vs. DRM

Ügyfeleink gyakran wonder, hogy használják inkább a AES-titkosítás vagy a DRM-rendszer. Az a két rendszer közötti elsődleges különbség az, hogy az AES-titkosítás a tartalomkulcs küldött a rendszer az ügyfél ("az a titkosítatlan") nem titkosított formátumban. Ennek eredményeképpen a tartalom titkosításához használt kulcs lehet megtekinteni a hálózati nyomkövetés egyszerű szöveges az ügyfélen. AES-128-kulcsok titkosítási ideális használati esetekben, amikor a jelentésmegjelenítő egy megbízható entitás (például titkosított vállalati videók megtekintését a az alkalmazottak által a vállalaton belül terjesztett).

PlayReady, Widevine és FairPlay összes titkosítási képest magasabb szintű adja meg az AES-128 titkosítatlan kulcsokat titkosítás. A tartalomkulcs továbbított titkosított formában. Ezenkívül a visszafejtési az operációs rendszer szintjén, ahol nehezebb a támadásokkal szemben egy rosszindulatú felhasználó biztonságos környezetben történik. DRM ajánlott használati esetek, ahol a megjelenítő lehetséges, hogy nem egy megbízható entitás, és Ön a legmagasabb szintű biztonságot követelhetnek meg.

## <a name="storage-side-encryption"></a>Storage ügyféloldali titkosítása

Az inaktív eszközök védelmére, titkosítani kell az eszközök által a storage ügyféloldali titkosítása. Az alábbi táblázat a storage ügyféloldali titkosítása működését mutatja a Media Services v3:

|Titkosítási beállítás|Leírás|Media Services v3|
|---|---|---|---|
|Media Services, tárolás titkosítása| AES-256 titkosítással, kulcsfontosságú a Media Services által felügyelt|Nem támogatott<sup>(1)</sup>|
|[A Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|A kiszolgálóoldali titkosítást az Azure Storage által kínált kulcs felügyelt ügyfél vagy Azure által|Támogatott|
|[Storage ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure storage, a Key Vault az ügyfél által felügyelt kulcs által kínált ügyféloldali titkosítás|Nem támogatott|

<sup>1</sup> a Media Services v3, tárolás titkosítása (AES-256 titkosítás) van csak támogatott a visszamenőleges kompatibilitás a Media Services v2 létrehozásakor a rendszer az eszközöket. Tehát v3 együttműködik a meglévő tárhely eszközök titkosított, de nem teszi meg újakat.

## <a name="licenses-and-keys-delivery-service"></a>Licencek és a kulcsok kézbesítési szolgáltatás

A Media Services DRM (PlayReady, Widevine, FairPlay) licencek és az AES-kulcsok kézbesítéséhez a hitelesített ügyfelek kulcskézbesítési szolgáltatást biztosít. A REST API vagy egy Media Services ügyféloldali kódtárának használatával konfigurálhat a licencekkel és a kulcsok a hitelesítési és engedélyezési házirendeket.

## <a name="control-content-access"></a>Tartalom-hozzáférés vezérlése

Szabályozhatja, hogy ki férhet hozzá a tartalom a content key házirend konfigurálásával. A Media Services szolgáltatásban több különböző módot is beállíthat, amelynek segítségével a rendszer hitelesítheti a kulcskérelmet küldő felhasználókat. A tartalom fő házirendet kell konfigurálnia. Az ügyfélnek (lejátszó) meg kell felelnie a szabályzat, a kulcs letöltéséhez az ügyfél előtt. A tartalom kulcs házirendhez **nyissa meg a** vagy **token** korlátozás. 

A jogkivonattal korlátozott tartalom kulcs szabályzattal a tartalomkulcs van csak ügyfélnek küldött, amely egy érvényes JSON webes jogkivonat (JWT) vagy simple web Tokens (SWT) jeleníti meg a kulcs kérésben. Ez a token biztonsági jogkivonatokkal kapcsolatos szolgáltatás (STS) kell kiállítaniuk. Használja az Azure Active Directory, az STS szolgáltatással, vagy egy egyéni STS üzembe helyezése. Az STS-re kell állítani a megadott kulcs és a probléma jogcímek jogkivonat korlátozás konfigurációjában megadott aláírt jogkivonat létrehozásához. A Media Services kulcstovábbítást adja vissza a kért kulcs licenc az ügyfélnek, ha a jogkivonat érvényes, és a jogkivonat jogcímeiben megfelelnek a kulcs/licenc.

A tokennel korlátozott szabályzatokhoz konfigurálásakor adjon meg, hogy az elsődleges ellenőrzőkulcs, a kibocsátó és a célközönség paramétereket. Az elsődleges ellenőrzőkulcs tartalmazza a kulcsot, a jogkivonat írták-e. A kibocsátó a biztonságos jogkivonat-szolgáltatás, amely a jogkivonatot. A célközönség, más néven a hatókör, ismerteti a token szándéka, vagy az erőforrás a token engedélyezi a hozzáférést. A Media Services kulcstovábbítást ellenőrzi, hogy ezeket az értékeket a jogkivonat egyezik a sablonban szereplő értékeket.

## <a name="a-idfaqfrequently-asked-questions"></a><a id="faq"/>Gyakori kérdések

### <a name="question"></a>Kérdés

Hogyan valósíthat meg az Azure Media Services (AMS) v3-as, és használja az AMS licenckulcs/kézbesítési szolgáltatás használatával többplatformos DRM (PlayReady, Widevine és FairPlay) rendszer?

### <a name="answer"></a>Válasz

Teljes körű, olvassa az [alábbi kódpéldát](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs). 

A példa bemutatja, hogyan:

1. Létrehozhat és konfigurálhat ContentKeyPolicies.

  A mintául szolgáló függvényeket tartalmaz, amelyek konfigurálása [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), és [FairPlay](fairplay-license-overview.md) licenceket.

    ```
    ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
    ```

2. Hozzon létre egy StreamingLocator adatfolyam egy titkosított eszköz van konfigurálva. 

  Beállíthatja például, a StreamingLocator.StreamingPolicyName "Predefined_MultiDrmCencStreaming" szabályzat. Ez a szabályzat jelzi, hogy két tartalomkulcsot (envelope és CENC) szeretne létrehozni és beállítani a lokátoron. Így az envelope, a PlayReady és a Widevine titkosítások lesznek alkalmazva (a kulcsot a konfigurált DRM-licencek alapján továbbítja a rendszer a lejátszást végző ügyfelének). Ha CBCS (FairPlay) licenccel is titkosítani szeretné a streamet, használja a következőt: „Predefined_MultiDrmStreaming”.

3. A test-token létrehozásához.

  A **GetTokenAsync** módszer bemutatja, hogyan hozzon létre egy tesztet token.
  
4. A streamelési URL-cím összeállítását.

  A **GetDASHStreamingUrlAsync** metódus azt mutatja be, hogyan hozhat létre a streamelési URL-CÍMÉT. Ebben az esetben az URL-cím Streamek a **DASH** tartalmat.

### <a name="question"></a>Kérdés

Hogyan és hol érdemes a JWT jogkivonat beszerzése és a kérés licenc- vagy kulcs?

### <a name="answer"></a>Válasz

1. Éles környezetben szüksége lesz egy Secure Token szolgáltatások (STS) (webszolgáltatás) amely JWT jogkivonatot egy HTTPS-kérés esetén. Tesztelési, használhatja a kód látható **GetTokenAsync** meghatározott metódus [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Indítson egy, az STS-re, például egy jogkivonatot a felhasználó hitelesítése után, és rendelje hozzá a token értékeként Player kell. Használhatja a [az Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/).

* Példa STS, futtatása vagy szimmetrikus vagy aszimmetrikus kulccsal, tekintse meg [ http://aka.ms/jwt ](https://aka.ms/jwt). 
* Egy ilyen JWT-jogkivonat használatával az Azure Media Player-alapú Media Player egy példa: [ http://aka.ms/amtest ](https://aka.ms/amtest) (bontsa ki a "player_settings" hivatkozásra kattintva megtekintheti a token bemeneti).

### <a name="question"></a>Kérdés

Hogyan engedélyezi, hogy kérelmeket a Videóknak az AES-titkosítás?

### <a name="answer"></a>Válasz

A megfelelő módszer, hogy kihasználja az STS (Secure Token Service):

STS attól függően, felhasználói profil hozzá más jogcímeket (például a "Prémium szintű felhasználó", "Alapszintű felhasználó", "Ingyenes próbaverzió felhasználó"). A különböző jogcímek a jwt-t a felhasználó megtekintheti a különböző tartalmát. Természetesen különböző tartalom eszköz, a ContentKeyPolicyRestriction rendelkezik a megfelelő RequiredClaims.

Használja az Azure Media Services API-k konfigurálása/licenckulcs kézbesítési és az eszközök titkosítása (ahogyan az [ezt a mintát](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs).

## <a name="next-steps"></a>További lépések

Tekintse meg a következő cikkeket:

  * [Az AES-titkosítással védeni](protect-with-aes128.md)
  * [DRM védelme](protect-with-drm.md)

További információ található a [a hozzáférés-vezérléssel drm-mel a content protection rendszer tervezése](design-multi-drm-system-with-access-control.md)


