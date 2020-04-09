---
title: Streamelés a Widevine Android offline szolgáltatásával az Azure Media Services v3-as verzióval
description: Ez a témakör bemutatja, hogyan konfigurálhatja az Azure Media Services-fiókot a Widevine által védett tartalmak offline streameléséhez.
services: media-services
keywords: DASH, DRM, Widevine Offline mód, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2020
ms.author: willzhan
ms.openlocfilehash: 94edec8261d9916b7575fb247e1698273f244130
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887197"
---
# <a name="offline-widevine-streaming-for-android-with-media-services-v3"></a>Offline Widevine streaming Androidra a Media Services v3-as verzióval

Az online adatfolyamok védelmén kívül a médiatartalom-előfizetési és -kölcsönzési szolgáltatások olyan letölthető tartalmakat is kínálnak, amelyek akkor is működnek, ha ön nem csatlakozik az internethez. Előfordulhat, hogy a hálózatról leválasztott repülés esetén a telefonra vagy táblagépre tartalmat kell letöltenie a repülőgép üzemmódban való lejátszáshoz. További forgatókönyvek, amelyekben érdemes lehet letölteni a tartalmat:

- Egyes tartalomszolgáltatók leengedélyezhetik a DRM-licenc kézbesítését az ország/régió határain túl. Ha a felhasználó külföldre utazva szeretne tartalmat nézni, offline letöltésre van szükség.
- Egyes országokban/régiókban az internet elérhetősége és/vagy sávszélessége korlátozott. A felhasználók dönthetnek úgy, hogy letöltik a tartalmat, hogy elég nagy felbontásban nézhessék a megfelelő vizuális élmény érdekében.

Ez a cikk bemutatja, hogyan valósítható meg offline módú lejátszás a Widevine által védett DASH-tartalmakhoz Android-eszközökön. Az offline DRM lehetővé teszi, hogy előfizetési, bérleti és vásárlási modelleket biztosítson a tartalomhoz, lehetővé téve a szolgáltatások ügyfelei számára, hogy könnyedén vigyenek velük tartalmakat, ha le vannak választva az internetről.

Az Android player alkalmazások létrehozásához három lehetőséget vázolunk fel:

> [!div class="checklist"]
> * Építs játékost az ExoPlayer SDK Java API-jával
> * Építs egy játékost az ExoPlayer SDK Xamarin-kötésével
> * Lejátszó létrehozása titkosított médiakiterjesztéssel (EME) és médiaforrás-kiterjesztéssel (MSE) a Chrome mobilböngészőv62-es vagy újabb böngészőjében

A cikk a Widevine által védett tartalmak offline streamelésével kapcsolatos néhány gyakori kérdésre is választ ad.

> [!NOTE]
> Az offline DRM-nek csak a tartalom letöltésekor kell fizetnie egyetlen licenckérelemért. A hibákat a rendszer nem számlázja.

## <a name="prerequisites"></a>Előfeltételek 

Mielőtt offline DRM-et valósítana meg widevine-ra Android-eszközökön, először a következőket kell tennie:

- Ismerkedjen meg az online tartalomvédelem melómiával bevezetett fogalmakkal. Ezt részletesen a következő dokumentumok/minták ismertetik:
    - [Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md)
    - [A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata](protect-with-drm.md)
- Klón https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git.

    A Widevine-konfigurációk hozzáadásához módosítania kell a kódot a [DRM titkosítása a DRM használatával.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM)  
- Ismerkedjen meg a Google ExoPlayer SDK for Android, egy nyílt forráskódú videó lejátszó SDK támogató offline Widevine DRM lejátszás. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer fejlesztői útmutató](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Fejlesztői Blog](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Tartalomvédelem konfigurálása az Azure Media Servicesszolgáltatásban

A [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metódusban a következő szükséges lépések állnak fenn:

1. Adja meg, hogyan engedélyezett a tartalomkulcs-kézbesítés a licenckézbesítési szolgáltatásban: 

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
2. Widevine licencsablon konfigurálása:  

    ```csharp
    ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
    ```

3. ContentKeyPolicyOptions létrehozása:

    ```csharp
    options.Add(
        new ContentKeyPolicyOption()
        {
            Configuration = widevineConfig,
            Restriction = restriction
        });
    ```

## <a name="enable-offline-mode"></a>Kapcsolat nélküli mód engedélyezése

A Widevine-licencek **offline** üzemmódjának engedélyezéséhez konfigurálnia kell a [Widevine licencsablont.](widevine-license-template-overview.md) A **policy_overrides** objektumban állítsa a **can_persist** tulajdonságot **igaz** értékre (az alapértelmezett értéke hamis), ahogy az a [ConfigureWidevineLicenseTempate](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563). 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Az Android-lejátszó konfigurálása offline lejátszáshoz

A legegyszerűbb módja annak, hogy dolgozzon ki egy natív játékos app Android készülékek, hogy a [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), egy nyílt forráskódú videó lejátszó SDK. Az ExoPlayer olyan funkciókat támogat, amelyeket jelenleg nem támogat az Android natív MediaPlayer API-ja, beleértve az MPEG-DASH és a Microsoft Smooth Streaming kézbesítési protokollokat.

ExoPlayer 2.6-os vagy újabb verzió sok olyan osztályt tartalmaz, amelyek támogatják az offline Widevine DRM lejátszást. Különösen az OfflineLicenseHelper osztály biztosít segédprogram-funkciókat, amelyek megkönnyítik a DefaultDrmSessionManager használatát az offline licencek letöltéséhez, megújításához és kiadásához. A "library/core/src/main/java/com/google/android/exoplayer2/offline/" mappában található osztályok támogatják az offline videotartalmak letöltését.

Az alábbi osztályok listája megkönnyíti az offline módot az Android ExoPlayer SDK-ban:

- könyvtár/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- könyvtár/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- könyvtár/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- könyvtár/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- könyvtár/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- könyvtár/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- könyvtár/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- könyvtár/core/src/main/java/com/google/android/exoplayer2/offline/DownloaderConstructorHelper.java 
- könyvtár/core/src/main/java/com/google/android/exoplayer2/offline/Downloader.java
- könyvtár/kötőjel/src/main/java/com/google/android/exoplayer2/source/dash/offline/DashDownloader.java 

A fejlesztőknek hivatkozniuk kell az [ExoPlayer fejlesztői útmutatóra](https://google.github.io/ExoPlayer/guide.html) és a megfelelő [fejlesztői blogra](https://medium.com/google-exoplayer) az alkalmazás fejlesztése során. A Google jelenleg nem adott ki teljes körűen dokumentált referenciaimplementációt vagy mintakódot a Widevine offline támogató ExoPlayer alkalmazáshoz, így az információ a fejlesztők útmutatójára és blogjára korlátozódik. 

### <a name="working-with-older-android-devices"></a>Régebbi Android-eszközök kel való együttműködés

Egyes régebbi Android-eszközök esetén a következő **policy_overrides** tulajdonságok (a [Widevine licencsablonban](widevine-license-template-overview.md)definiálva: **rental_duration_seconds**, **playback_duration_seconds**és **license_duration_seconds**értékeket kell beállítania. Másik lehetőségként beállíthatja őket nullára, ami végtelen/korlátlan időtartamot jelent.  

Az értékeket úgy kell beállítani, hogy elkerüljék az egész túlcsordulási hibákat. A problémával kapcsolatos további https://github.com/google/ExoPlayer/issues/3150 https://github.com/google/ExoPlayer/issues/3112magyarázatért lásd a és a témakört. <br/>Ha nem állítja be explicit módon az értékeket, akkor a **playbackDurationRemaining** és a **LicenseDurationRemaining** nagyon nagy értékei lesznek hozzárendelve (például 9223372036854775807, amely egy 64 bites egész szám maximális pozitív értéke). Ennek eredményeképpen a Widevine licenc lejártnak tűnik, és így a visszafejtés nem fog megtörténni. 

Ez a probléma nem fordul elő az Android 5.0 Lollipop vagy újabb, mivel az Android 5.0 az első Android verzió, amelyet úgy terveztek, hogy teljes mértékben támogassa az ARMv8[(Advanced RISC Machine)](https://en.wikipedia.org/wiki/ARM_architecture)és a 64 bites platformokat, míg az Android 4.4 KitKat eredetileg az ARMv7 és a 32 bites platformok támogatására készült, mint más régebbi Android verziók.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>A Xamarin használata Android-lejátszási alkalmazás létrehozásához

Xamarin kötések az ExoPlayer számára az alábbi linkek használatával találhatók:

- [Xamarin kötések könyvtár a Google ExoPlayer könyvtár](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin kötések az ExoPlayer NuGet számára](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Lásd még a következő szálat: [Xamarin kötés](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Androidra készült Chrome Player-alkalmazások

Kezdve a kiadás a [Chrome for Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), állandó licenc EME támogatott. [A Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) mostantól az Android Chrome-ban is támogatott. Ez lehetővé teszi, hogy offline lejátszóalkalmazásokat hozzon létre a Chrome-ban, ha a végfelhasználók a Chrome ezen (vagy újabb) verziójával rendelkeznek. 

Ezen túlmenően, a Google készített egy Progressive Web App (PWA) minta és nyílt forráskódú azt: 

- [Forráskód](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [A Google üzemeltetett verziója](https://biograf-155113.appspot.com/ttt/episode-2/) (csak Chrome v 62-es vagy újabb verziókban működik Android-eszközökön)

Ha androidos telefonon frissíti a mobil Chrome-böngészőt v62-re (vagy újabbra), és teszteli a fenti üzemeltetett mintaalkalmazást, látni fogja, hogy mind az online streaming, mind az offline lejátszás működik.

A fenti nyílt forráskódú PWA alkalmazás szerzője a Node.js. Ha saját verzióját szeretné üzemeltetni egy Ubuntu kiszolgálón, tartsa szem előtt a következő gyakori problémákat, amelyek megakadályozhatják a lejátszást:

1. CORS-probléma: A mintaalkalmazásban lévő https://storage.googleapis.com/biograf-video-files/videos/mintavideó a alkalmazásban található. A Google beállította a CORS-t a Google Cloud Storage gyűjtőben tárolt összes tesztmintához. Ezek kézbesítik a CORS fejlécek, megadva kifejezetten a CORS bejegyzés: `https://biograf-155113.appspot.com` (a domain, ahol a Google házigazdák a minta) megakadályozza a hozzáférést bármely más oldalakon. Ha megpróbálja, a következő HTTP-hiba jelenik meg:`Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Tanúsítvány kiállítása: A Chrome v 58-tól kezdve az EME for Widevine https-t igényel. Ezért a mintaalkalmazást HTTPS-kapcsolaton keresztül, X509-tanúsítvánnyal kell üzemeltetnie. A szokásos vizsgálati tanúsítvány a következő követelmények miatt nem működik: A következő minimumkövetelményeknek megfelelő tanúsítványt kell beszereznie:
    - A Chrome és a Firefox megköveteli, hogy a TANÚSÍTVÁNYban létezzen a SAN-Subject Alternatív név beállítás
    - A tanúsítványnak megbízható hitelesítésszolgáltatóval kell rendelkeznie, és az önaláírt fejlesztési tanúsítvány nem működik
    - A tanúsítványnak olyan CN-rel kell rendelkeznie, amely megfelel a webkiszolgáló vagy az átjáró DNS-nevének.

## <a name="faqs"></a>Gyakori kérdések

További információ: [Widevine GYIK](frequently-asked-questions.md#widevine-streaming-for-android).

## <a name="additional-notes"></a>További megjegyzések

A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="summary"></a>Összefoglalás

Ez a cikk a Widevine által védett DASH-tartalom offline módú lejátszásának androidos eszközökön történő megvalósítását ismerteti.  Megválaszolt néhány gyakori kérdést is a Widevine védett tartalmak offline streamelésével kapcsolatban.
