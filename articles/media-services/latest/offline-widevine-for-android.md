---
title: Az offline streamelési Widevine védett tartalom – Azure-fiók konfigurálása
description: Ez a témakör bemutatja, hogyan konfigurálhatja az Azure Media Services-fiók védett Widevine streameléshez offline tartalom.
services: media-services
keywords: DASH, DRM, Widevine ExoPlayer, a kapcsolat nélküli módban Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: willzhan
ms.openlocfilehash: 5d7dccfecc47b14be62a78600561a8ff0f7ca501
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312257"
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine androidhoz streaming

Online streamelés tartalmának védelme, mellett a multimédiás tartalom előfizetés és a bérleti szolgáltatások ajánlat letölthető tartalom, amely akkor működik, ha nem csatlakoznak az internethez. Szüksége lehet letölteni a tartalmat a telefon az alakzatot vagy lejátszható repülőgépes módra, amikor hajózó a tábla nem kapcsolódik a hálózathoz. További forgatókönyvek, ahol érdemes letölteni a tartalmat:

- Néhány tartalomszolgáltatók előfordulhat, hogy ne engedélyezze a DRM-licenckézbesítés túl egy ország szegély. Ha a felhasználó szeretné, tekintse meg a tartalom külföldön utazás közben, offline letöltési van szükség.
- Egyes országokban Internet rendelkezésre állási és/vagy a sávszélesség korlátozva. Előfordulhat, hogy a felhasználók megadhatják tudja, hogy megtekintse a megfelelő engedélymegtekintési elég nagy felbontású tartalmat töltenek le.

Ez a cikk ismerteti, hogyan valósíthat meg a kapcsolat nélküli módban lejátszás DASH tartalom Widevine védi az Android-eszközökön. A kapcsolat nélküli DRM lehetővé teszi, hogy adja meg az előfizetés, bérbeadás és beszerzési modelleket a tartalom, így az ügyfelek a szolgáltatások egyszerűen igénybe a tartalmat a őket, ha az internethez csatlakozik.

Az Android-lejátszó alkalmazások készítéséhez, hogy szerkezeti három beállítások:

> [!div class="checklist"]
> * A lejátszó SDK-val Java API-ExoPlayer összeállítása
> * A player ExoPlayer SDK Xamarin-kötés használatával hozhat létre
> * Egy titkosított adathordozó-bővítmény (EME) és a Media forrás bővítmény (MSE) használatával, a Chrome mobilböngészőben v62 vagy újabb verziójú player létrehozása

A cikket is offline streamelési Widevine védett tartalom kapcsolatos gyakori kérdésekre ad választ.

## <a name="prerequisites"></a>Előfeltételek 

Mielőtt Widevine offline DRM végrehajtása Android-eszközökön, akkor először:

- Alaposabban megismerheti a használata a Widevine DRM-mel online content protection bemutatott fogalmakkal. Ez az a következő dokumentumok minták részletesen ismertetett:
    - [Egy DRM-mel a content protection rendszert a hozzáférés-vezérléssel](design-multi-drm-system-with-access-control.md)
    - [DRM a dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata](protect-with-drm.md)
- Klónozás https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    Módosítsa a kódot kell [titkosítása a .NET használatával DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) Widevine konfigurációk hozzáadása.  
- Megismerkedhet a Google ExoPlayer SDK-val Android-egy nyílt forráskódú videólejátszó SDK offline Widevine DRM-mel lejátszási támogathatja. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer fejlesztői útmutató](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer fejlesztői Blog](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>A tartalomvédelem konfigurálása az Azure Media Servicesben

Az a [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metódus, a következő szükséges lépéseket találhatók:

1. Adja meg, hogyan tartalom kulcskézbesítési engedélyezve van a a licenctovábbítási szolgáltatása: 

    ```csharp
    ContentKeyPolicySymmetricTokenKey primaryKey = new ContentKeyPolicySymmetricTokenKey(tokenSigningKey);
    List<ContentKeyPolicyTokenClaim> requiredClaims = new List<ContentKeyPolicyTokenClaim>()
    {
        ContentKeyPolicyTokenClaim.ContentKeyIdentifierClaim
    };
    List<ContentKeyPolicyRestrictionTokenKey> alternateKeys = null;
    ContentKeyPolicyTokenRestriction restriction 
        = new ContentKeyPolicyTokenRestriction(Issuer, Audience, primaryKey, ContentKeyPolicyRestrictionTokenType.Jwt, alternateKeys, requiredClaims);
    ```
2. Widevine-licencsablon konfigurálása:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. Create ContentKeyPolicyOptions:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Offline üzemmód engedélyezése

Ahhoz, hogy **offline** mód a Widevine-licencek, konfigurálnia kell [Widevine-licencsablon](widevine-license-template-overview.md). Az a **policy_overrides** objektumazonosító, állítsa be a **can_persist** tulajdonságot **igaz** (az alapértelmezett érték FALSE (hamis)), ahogyan [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>A kapcsolat nélküli lejátszás céljából Android player konfigurálása

Android-eszközök esetén a natív lejátszó alkalmazások fejlesztése a legegyszerűbb módja az, hogy használja a [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), egy videó lejátszási nyílt forráskódú SDK-t. ExoPlayer támogatja az Android a natív Media Player API-t, beleértve a Microsoft Smooth Streaming és MPEG-dash PROTOKOLLHOZ a kézbesítési protokollra által jelenleg nem támogatott szolgáltatásokat.

ExoPlayer 2.6-os vagy újabb verzió, amelyek támogatják az offline Widevine DRM-mel lejátszási sok osztály tartalmazza. Ilyen például a OfflineLicenseHelper osztály megkönnyítése érdekében a DefaultDrmSessionManager letöltésével, megújítása és felszabadítása offline licencek használatát segédprogram funkciókat biztosít a. Az SDK mappából a megadott osztályokat "könyvtár/core/src/main/java/com/google/android/exoplayer2/offline /" támogatja az offline videó tartalom letöltése.

Az alábbi listában szereplő osztályok elősegíti a kapcsolat nélküli módban ExoPlayer Androidhoz készült SDK-ban:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- library/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- library/dash/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

A fejlesztők kell hivatkoznia a [ExoPlayer fejlesztői útmutató](https://google.github.io/ExoPlayer/guide.html) és a megfelelő [fejlesztői Blog](https://medium.com/google-exoplayer) -alkalmazások fejlesztése során. Google nem jelent meg egy teljes körű dokumentációval rendelkező referencia megvalósítása vagy minta kódot támogató jelenleg Widevine offline, így az adatokat a fejlesztők számára az útmutató és blog ExoPlayer alkalmazás. 

### <a name="working-with-older-android-devices"></a>Régebbi Android-eszközök használata

Egyes régebbi Android-eszközön újra be kell állítani a következő értékek **policy_overrides** tulajdonságok (meghatározott [Widevine-licencsablon](widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, és **license_duration_seconds**. Másik lehetőségként beállíthatja őket nulla, ami azt jelenti, hogy a végtelen vagy határozatlan időre szól.  

Egy egész túlcsordulási hiba elkerülése érdekében állítson be az értékeket. A problémáról további ismertetése: https://github.com/google/ExoPlayer/issues/3150 és https://github.com/google/ExoPlayer/issues/3112. <br/>Ha az értékek nem meg explicit módon, a nagyon nagy értékeket **PlaybackDurationRemaining** és **LicenseDurationRemaining** hozzá lesz rendelve, (például 9223372036854775807, amely a legnagyobb egy 64 bites egész szám pozitív érték). Ennek eredményeképpen a Widevine-licenc lejárt jelenik meg, és ezért nem történik a titkosítást. 

Ez a probléma jelentkezik az Android 5.0 Lollipop és újabb, mivel az Android 5.0-s az első Androidos verziója, amely teljes körű támogatásához ARMv8 úgy lett kialakítva ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) és 64 bites rendszerek, az Android 4.4-es KitKat közben eredetileg készült ARMv7 és 32 bites platformon az egyéb régebbi verziók támogatása.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>A Xamarin segítségével Android lejátszási alkalmazás készítése

Xamarin-kötések a ExoPlayer találja meg a következő hivatkozásokkal:

- [A Google ExoPlayer szalagtár Xamarin-kötések klienskódtár](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin-kötések a ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Lásd még a következő szálat: [Xamarin-kötés](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Androidhoz készült Chrome-lejátszó alkalmazások

A kiadásával kezdődően [Chrome Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), állandó EME-licencek használata támogatott. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) most is támogatott a Chrome-ban Android. Ez lehetővé teszi, hogy offline lejátszási alkalmazások létrehozása a Chrome-ban, ha a végfelhasználók számára ez (vagy újabb) verziója a Chrome-ban. 

Emellett a Google rendelkezik fokozatos Web App (PWA) minta előállított és nyílt forráskódú: 

- [Forráskód](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google üzemeltetett verzió](https://biograf-155113.appspot.com/ttt/episode-2/) (csak a Chrome v 62 vagy újabb Android-eszközökön működik)

Ha frissít a mobil Chrome böngészőt v62 (vagy magasabb) egy Android telefont és tesztelés az a fenti üzemeltetett mintaalkalmazást, látni fogja, hogy működik is online streamelés és offline lejátszási.

A fenti nyílt forráskódú PWA alkalmazás hozta létre a node.js-ben. Ha szeretné a saját verziója egy Ubuntu Servert futtatni, vegye figyelembe a következő gyakori észlelt problémák, amelyek megakadályozhatják a lejátszás:

1. CORS-hiba: A videó a mintaalkalmazás minta üzemeltetett https://storage.googleapis.com/biograf-video-files/videos/. Google állított be a CORS minden, a Google Cloud Storage gyűjtőben lévő üzemeltetett teszt minták. Ezek a szolgálja ki a CORS fejlécek, explicit módon a CORS-bejegyzés megadása: https://biograf-155113.appspot.com (a tartomány melyik google-beli üzemelteti a minta) megakadályozza a hozzáférést a többi webhelyhez sem. Ha megpróbál, látni fogja a következő HTTP-hiba: Nem sikerült betölteni a https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: nincs "Access-Control-Allow-Origin" fejléc nem található a kért erőforrás. Forrás "https:\//13.85.80.81:8080" hozzáférés ezért nem engedélyezett. Ha egy nem átlátszó választ szolgálja ki az igényeinek, "no-cors" beolvasni az erőforrás le van tiltva cors-támogatással rendelkező a kérelem módjának beállítása.
2. A tanúsítvány kiadása: Kezdet: Chrome v 58, a Widevine az EME HTTPS van szüksége. Ezért a mintaalkalmazás futtatásához egy X509 a HTTPS-kapcsolaton keresztül kell tanúsítványt. Az alábbi követelmények miatt nem működik a szokásos tesztelési tanúsítvány: Be kell szereznie egy tanúsítványt a következő minimális rendszerkövetelményeknek megfelelő:
    - A Chrome és a Firefox SAN-tulajdonos alternatív neve beállítást a tanúsítvány léteznie kell
    - Kell a tanúsítvány megbízható hitelesítésszolgáltató és a egy önaláírt fejlesztési tanúsítvány nem működik.
    - A tanúsítvány CN-nevének a DNS-név a webalkalmazás-kiszolgáló vagy átjáró kell rendelkeznie.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="question"></a>Kérdés

Hogyan juttathat az állandó licencek (offline-kompatibilis) az egyes ügyfelek és felhasználói számára, és mások számára nem állandó licencek (offline letiltva)? Kell külön tartalomkulcsot, és a tartalom ismétlődő?

### <a name="answer"></a>Válasz
Mivel a Media Services v3 lehetővé teszi, hogy egy eszköz több StreamingLocators rendelkeznie. Rendelkezhet

1.  A license_type egy ContentKeyPolicy = "állandó", "állandó" a jogcím-ContentKeyPolicyRestriction, és annak StreamingLocator;
2.  Egy másik ContentKeyPolicy a license_type = "nonpersistent", "nonpersistent" a jogcím-ContentKeyPolicyRestriction és annak StreamingLocator.
3.  A két StreamingLocators különböző ContentKey rendelkezik.

Üzleti logikát az egyéni STS, attól függően a különböző jogcímek a JWT jogkivonat kiadása. A jogkivonattal a csak a megfelelő licenc szerezhető be, és csak a megfelelő URL-cím lejátszhatók.

### <a name="question"></a>Kérdés

A Widevine biztonsági szintek, a Google [Widevine DRM-architektúra áttekintése doc](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentáció, három különböző biztonsági szintet határoz meg. Azonban a [Azure Media Services dokumentációja a Widevine-licencsablon](widevine-license-template-overview.md), vázolt öt különböző biztonsági szintet. Mi a kapcsolat vagy a két különböző eljáráscsoport biztonsági szintek közötti leképezést?

### <a name="answer"></a>Válasz

A Google [Widevine DRM-architektúra áttekintése](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), azt határozza meg a következő három biztonsági szintek:

1.  1. biztonsági szint: Az összes tartalom feldolgozása, titkosítás és vezérlés belül a megbízható végrehajtási környezetben (TEE) történik. Néhány megvalósítás modellekben biztonsági feldolgozási hajtható végre a különböző lapkák.
2.  2. biztonsági szint: Titkosítás (de nem Videofeldolgozás) végez a TEE belül: visszafejtett pufferek alkalmazás tartományhoz adja vissza, és külön videó hardver vagy szoftver keresztül. 2. szintű azonban titkosítási információk feldolgozása van csak a TEE belül.
3.  3. biztonsági szint nem rendelkezik egy TEE az eszközön. Megfelelő intézkedéseket előfordulhat, hogy a titkosítási adatokat és a gazdagép operációs rendszere a visszafejtett tartalom védelme érdekében. A 3. szint megvalósítás is tartalmazhat egy hardveres titkosítási motor, de, amely csak növeli a teljesítményt, nem biztonsági.

Ugyanakkor, a [Azure Media Services dokumentációja a Widevine-licencsablon](widevine-license-template-overview.md), content_key_specs security_level tulajdonságát az alábbi öt különböző érték (megbízhatóságára vonatkozó ügyfélkövetelmények lejátszás) is rendelkezik:

1.  A szoftveres whitebox kriptográfiai megadása kötelező.
2.  Szoftvert a titkosításnak és a egy rejtjelezett dekóder megadása kötelező.
3.  A megosztottkulcs-anyag és a titkosítási műveletek belül kell végrehajtani egy hardveres biztonsági TEE.
4.  A titkosítási és a tartalom dekódolási belül kell végrehajtani egy hardveres biztonsági TEE.
5.  A titkosítási alakíthatják és az összes kezelési az adathordozó (tömörített és tömörítetlen) belül kell kezelni egy hardveres biztonsági TEE.

A Google Widevine mindkét biztonsági szintek határozzák meg. A különbség a használati kellően: architektúra vagy API szintjén. Az öt biztonsági szintek a Widevine API-t kell használni. A content_key_specs objektum, amely tartalmazza a security_level deszerializálását és a Widevine-globális tartalomkézbesítési szolgáltatás az Azure Media Services Widevine-licencszolgáltatást átadott. Az alábbi táblázat a két fajta biztonsági szintek közötti leképezést.

| **Biztonsági szintjének Widevine-architektúra** |**A Widevine API használt biztonsági szintek**|
|---|---| 
| **1. biztonsági szint**: Az összes tartalom feldolgozása, titkosítás és vezérlés belül a megbízható végrehajtási környezetben (TEE) történik. Néhány megvalósítás modellekben biztonsági feldolgozási hajtható végre a különböző lapkák.|**security_level=5**: A titkosítási alakíthatják és az összes kezelési az adathordozó (tömörített és tömörítetlen) belül kell kezelni egy hardveres biztonsági TEE.<br/><br/>**security_level=4**: A titkosítási és a tartalom dekódolási belül kell végrehajtani egy hardveres biztonsági TEE.|
**Biztonsági szint 2**: Titkosítás (de nem Videofeldolgozás) végez a TEE belül: visszafejtett pufferek alkalmazás tartományhoz adja vissza, és külön videó hardver vagy szoftver keresztül. 2. szintű azonban titkosítási információk feldolgozása van csak a TEE belül.| **security_level=3**: A megosztottkulcs-anyag és a titkosítási műveletek belül kell végrehajtani egy hardveres biztonsági TEE. |
| **3. biztonsági szint**: Nem rendelkezik egy TEE az eszközön. Megfelelő intézkedéseket előfordulhat, hogy a titkosítási adatokat és a gazdagép operációs rendszere a visszafejtett tartalom védelme érdekében. A 3. szint megvalósítás is tartalmazhat egy hardveres titkosítási motor, de, amely csak növeli a teljesítményt, nem biztonsági. | **security_level=2**: Szoftvert a titkosításnak és a egy rejtjelezett dekóder szükség.<br/><br/>**security_level=1**: A szoftveres whitebox kriptográfiai megadása kötelező.|

### <a name="question"></a>Kérdés

Miért nem tartalomletöltés tart sokáig?

### <a name="answer"></a>Válasz

Letöltési sebesség javítása érdekében két módja van:

1.  Engedélyezze a CDN-t, hogy a végfelhasználók nagyobb valószínűséggel nyomja le az CDN origin/folyamatos átviteli végponton tartalomletöltés helyett. Ha a felhasználó eléri a tartalomstreameléshez használt streamvégpont, minden egyes szegmens HLS vagy szaggatott fragment dinamikus csomagolása és titkosított. Annak ellenére, hogy a késés ezredmásodperces méretezési csoport esetében minden egyes szegmens/töredék, ha egy óránál hosszú videó, az összesített késés nagy okoz a letöltés hosszabb lehet.
2.  Egyenkénti letöltéséhez a videó minősége rétegek és helyett minden tartalmat hangsáv lehetőséget nyújt a végfelhasználók számára. A kapcsolat nélküli üzemmódban nem nincs pont letölti a minőségi rétegeit. Ennek érdekében két módja van:
    1.  Szabályozott ügyfél: player alkalmazás automatikusan kiválasztja, vagy a felhasználó kijelöli a videó minősége réteg és zeneszámok letöltésére;
    2.  Szolgáltatás szabályozza: az egyik dinamikus Manifest szolgáltatás segítségével az Azure Media Services (globális) szűrő, amely HLS-lista vagy kötőjel MPD korlátozza az egyetlen jó minőségű réteghez, és a kiválasztott hangsáv létrehozása. Ezután a letöltési URL-CÍMÉT, a végfelhasználók számára megjelenik ez a szűrő tartalmazza.

## <a name="summary"></a>Összegzés

Ebben a cikkben tárgyalt megvalósítása a kapcsolat nélküli módban lejátszás DASH tartalom Widevine védi az Android-eszközökön.  Azt a Widevine-által védett tartalom offline streamelési kapcsolatos gyakori kérdésekre is választ adni.
