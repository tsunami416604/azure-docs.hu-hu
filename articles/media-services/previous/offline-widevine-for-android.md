---
title: A Widevine által védett tartalmak offline streameléséhez konfigurálja a fiókját - Azure
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
ms.date: 04/16/2019
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: f3bd7bc78eeb62cc33a01ed31bb04d94078cae4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294337"
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine-streamelés Androidhoz  

> [!div class="op_single_selector" title1="Válassza ki a Media Services használt verzióját:"]
> * [3-as verzió](../latest/offline-widevine-for-android.md)
> * [2-es verzió](offline-widevine-for-android.md)

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

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

## <a name="requirements"></a>Követelmények 

Mielőtt offline DRM-et valósítana meg widevine-ra Android-eszközökön, először a következőket kell tennie:

- Ismerkedjen meg az online tartalomvédelem melómiával bevezetett fogalmakkal. Ezt részletesen a következő dokumentumok/minták ismertetik:
    - [Drm-licencek vagy AES-kulcsok kézbesítéséhez használja az Azure Media Services szolgáltatást](media-services-deliver-keys-and-licenses.md)
    - [CENC többplatformos DRM és hozzáférés-vezérlés használatával: Egy referenciaterv és megvalósítás az Azure-on és az Azure Media Services szolgáltatásban](media-services-cenc-with-multidrm-access-control.md)
    - [A PlayReady és/vagy a Widevine common encryption használata a .NET-tel](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Az Azure Media Services használatával PlayReady és/vagy Widevine-licenceket biztosíthat a .NET használatával](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Ismerkedjen meg a Google ExoPlayer SDK for Android, egy nyílt forráskódú videó lejátszó SDK támogató offline Widevine DRM lejátszás. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer fejlesztői útmutató](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Fejlesztői Blog](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Tartalomvédelmi konfiguráció az Azure Media Servicesben

Amikor egy eszköz Widevine-védelmét konfigurálja a Media Services szolgáltatásban, létre kell hoznia a ContentKeyAuthorizationPolicyOption paramétert, amely a következő három dolgot adta meg:

1. DRM rendszer (Widevine)
2. ContentKeyAuthorizationPolicyRestriction a tartalomkulcs kézbesítésének engedélyezését adja meg a licenckézbesítési szolgáltatásban (nyílt vagy tokenengedélyezés)
3. DRM (Widevine) licencsablon

A Widevine-licencek **offline** üzemmódjának engedélyezéséhez konfigurálnia kell a [Widevine licencsablont.](media-services-widevine-license-template-overview.md) A **policy_overrides** objektumban állítsa a **can_persist** tulajdonságot **true** értékre (az alapértelmezett érték hamis). 

A következő kódminta a .NET segítségével engedélyezi a Widevine-licencek **offline** üzemmódját. A kód a [PlayReady és/vagy a Widevine Common Common Encryption használatával alapul.](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                                //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),    //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

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

A fejlesztőknek hivatkozniuk kell az [ExoPlayer fejlesztői útmutatóra](https://google.github.io/ExoPlayer/guide.html) és a megfelelő [fejlesztői blogra](https://medium.com/google-exoplayer) az alkalmazás fejlesztése során. A Google nem adott ki teljes körűen dokumentált referencia-implementációt vagy mintakódot a Widevine offline támogató ExoPlayer alkalmazáshoz, így az információ a fejlesztők útmutatójára és blogjára korlátozódik. 

### <a name="working-with-older-android-devices"></a>Régebbi Android-eszközök kel való együttműködés

Egyes régebbi Android-eszközök esetén a következő **policy_overrides** tulajdonságok (a [Widevine licencsablonban](media-services-widevine-license-template-overview.md)definiálva: **rental_duration_seconds**, **playback_duration_seconds**és **license_duration_seconds**értékeket kell beállítania. Másik lehetőségként beállíthatja őket nullára, ami végtelen/korlátlan időtartamot jelent.  

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

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="question"></a>Kérdés

Hogyan szállíthatok állandó licenceket (offline módban) egyes ügyfelek/felhasználók és nem állandó licencek (offline-letiltva) mások számára? Meg kell kettőznem a tartalmat, és külön tartalomkulcsot kell használnom?

### <a name="answer"></a>Válasz
Nem kell duplikálnia a tartalmat. Egyszerűen használhatja a tartalom egyetlen példányát és egyetlen ContentKeyAuthorizationPolicy-et, de két különálló ContentKeyAuthorizationPolicyOption-t:

1. IContentKeyAuthorizationPolicyOption 1: állandó licencet használ, és ContentKeyAuthorizationPolicyRestriction 1-et, amely például egy jogcímet tartalmaz, például license_type = "Persistent"
2. IContentKeyAuthorizationPolicyOption 2: nem állandó licencet használ, és ContentKeyAuthorizationPolicyRestriction 2-t, amely olyan jogcímet tartalmaz, mint a license_type = "Nonpersistent"

Ily módon, ha egy licenckérelem érkezik az ügyfélalkalmazásból, a licenckérelemből nincs különbség. Azonban a különböző végfelhasználó/eszköz esetében az STS-nek rendelkeznie kell az üzleti logikával, hogy különböző jogcímeket tartalmazó különböző JWT-tokeneket bocsásson ki (a fenti két license_type egyikét). A JWT-jogkivonat jogcímértéke a licencszolgáltatás által a licenctípus kiadására szolgál: állandó vagy nem állandó.

Ez azt jelenti, hogy a biztonságos jogkivonat-szolgáltatás (STS) rendelkeznie kell az üzleti logika és az ügyfél/eszköz adatait a megfelelő jogcímérték hozzáadása a jogkivonathoz.

### <a name="question"></a>Kérdés

A Widevine biztonsági szintek esetében a Google [Widevine DRM Architektúra – Áttekintő dokumentum](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentációjában három különböző biztonsági szintet határoz meg. Azonban az [Azure Media Services dokumentációjában a Widevine licencsablonöt](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)ismertetett öt különböző biztonsági szint van körvonalazva. Mi a kapcsolat vagy leképezés a két különböző biztonsági szintcsoport között?

### <a name="answer"></a>Válasz

A Google [Widevine DRM architektúra áttekintése ,](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)ez határozza meg a következő három biztonsági szintet:

1.  1. biztonsági szint: Minden tartalomfeldolgozás, kriptográfia és vezérlő a megbízható végrehajtási környezetben (TEE) történik. Egyes implementációs modellekben a biztonsági feldolgozás különböző chipekben végezhető.
2.  2. biztonsági szint: Titkosítást hajt végre (de nem videofeldolgozást) a TEE-n belül: a visszafejtett pufferek visszakerülnek az alkalmazástartományba, és külön videohardveren vagy -szoftveren keresztül kerülnek feldolgozásra. szinten azonban a kriptográfiai információk feldolgozása még mindig csak a TEE-n belül történik.
3.  A 3-as biztonsági szint nem rendelkezik TEE-vel az eszközön. Megfelelő intézkedéseket lehet tenni a gazdaoperációs rendszeren található kriptográfiai információk és visszafejtett tartalom védelmére. A Level 3 implementáció tartalmazhat hardveres kriptográfiai motort is, de ez csak növeli a teljesítményt, a biztonságot nem.

Ugyanakkor a [Widevine licencsablon Azure Media Services dokumentációjában](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview)a content_key_specs security_level tulajdonsága a következő öt különböző értékkel rendelkezhet (a lejátszáshoz szükséges ügyfélrobusztussági követelmények):

1.  Szoftveralapú whitebox crypto szükséges.
2.  Szoftver es és egy homályos dekóder szükséges.
3.  A kulcsanyag- és titkosítási műveleteket egy hardveres pólón belül kell végrehajtani.
4.  A tartalom titkosítását és dekódolását egy hardveres pólón belül kell elvégezni.
5.  A titkosítást, a dekódolást és az adathordozók (tömörített és tömörítetlen) minden kezelését egy hardveres TEE-n belül kell kezelni.

Mindkét biztonsági szintet a Google Widevine határozza meg. A különbség a használati szint: architektúra szint vagy API-szint. Az öt biztonsági szint a Widevine API-ban használatos. A content_key_specs objektum, amely security_level tartalmazza, deszerializált, és átadta a Widevine globális kézbesítési szolgáltatás az Azure Media Services Widevine licencszolgáltatás. Az alábbi táblázat a két biztonsági szint csoport közötti leképezést mutatja.

| **A Widevine architektúrában meghatározott biztonsági szintek** |**A Widevine API-ban használt biztonsági szintek**|
|---|---| 
| **1. biztonsági szint:** Minden tartalomfeldolgozás, kriptográfia és vezérlés a megbízható végrehajtási környezetben (TEE) történik. Egyes implementációs modellekben a biztonsági feldolgozás különböző chipekben végezhető.|**security_level=5**: Az adathordozók titkosítását, dekódolását és minden kezelését (tömörített és tömörítetlen) egy hardverrel támogatott TEE-n belül kell kezelni.<br/><br/>**security_level=4**: A tartalom titkosítását és dekódolását egy hardveres PÓLÓ-alapú területen kell elvégezni.|
**2. biztonsági szint:** Titkosítást végez (de nem videofeldolgozást) a TEE-n belül: a visszafejtett pufferek visszakerülnek az alkalmazástartományba, és külön videohardveren vagy -szoftveren keresztül kerülnek feldolgozásra. szinten azonban a kriptográfiai információk feldolgozása még mindig csak a TEE-n belül történik.| **security_level=3**: A kulcsanyag- és titkosítási műveleteket egy hardveres PÓLÓ-n belül kell végrehajtani. |
| **3. biztonsági szint:** Nincs TEE a készüléken. Megfelelő intézkedéseket lehet tenni a gazdaoperációs rendszeren található kriptográfiai információk és visszafejtett tartalom védelmére. A Level 3 implementáció tartalmazhat hardveres kriptográfiai motort is, de ez csak növeli a teljesítményt, a biztonságot nem. | **security_level=2**: Szoftveres kriptográfia és egy elbúzított dekóder szükséges.<br/><br/>**security_level=1**: Szoftveralapú whitebox titkosítás szükséges.|

### <a name="question"></a>Kérdés

Miért tart ilyen sokáig a tartalom letöltése?

### <a name="answer"></a>Válasz

A letöltési sebesség kétféleképpen javítható:

1.  Engedélyezze a CDN-t, hogy a végfelhasználók nagyobb valószínűséggel nyomják meg a CDN-t az origin/streaming végpont helyett a tartalom letöltéséhez. Ha a felhasználó eléri a streamelési végpontot, minden HLS-szegmens vagy DASH-töredék dinamikusan van csomagolva és titkosítva. Annak ellenére, hogy ez a késés ezredmásodpercben van minden szegmens/töredék esetében, ha egy órás videóval rendelkezik, a felhalmozott késés nagy lehet, ami hosszabb letöltést okozhat.
2.  Adja meg a végfelhasználóknak azt a lehetőséget, hogy az összes tartalom helyett szelektíven töltsék le a videominőségű rétegeket és hangsávokat. Offline módban nincs értelme letölteni az összes minőségi réteget. Ezt kétféleképpen lehet elérni:
    1.  Ügyfél által vezérelt: vagy a lejátszó alkalmazás automatikusan kiválasztja, vagy a felhasználó kiválasztja a videó minőségű réteget és a letöltandó hangsávokat;
    2.  Szolgáltatás vezérelt: az Azure Media Services dinamikus jegyzékfájl-szolgáltatásával létrehozhat egy (globális) szűrőt, amely a HLS-lejátszási listát vagy a DASH MPD-t egyetlen videominőségi rétegre és a kiválasztott hangsávokra korlátozza. Ezután a végfelhasználóknak bemutatott letöltési URL-cím tartalmazza ezt a szűrőt.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="summary"></a>Összefoglalás

Ez a cikk a Widevine által védett DASH-tartalom offline módú lejátszásának androidos eszközökön történő megvalósítását ismerteti.  Megválaszolt néhány gyakori kérdést is a Widevine védett tartalmak offline streamelésével kapcsolatban.
