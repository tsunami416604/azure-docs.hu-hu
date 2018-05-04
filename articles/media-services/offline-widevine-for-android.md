---
title: A kapcsolat nélküli adatfolyamként való küldése a Widevine védett tartalom - Azure fiókjának konfigurálása
description: Ez a témakör bemutatja a konfigurálása az Azure Media Services-fiókjához offline adatfolyamként való küldése a Widevine védett tartalom.
services: media-services
keywords: Widevine-kapcsolat nélküli módban, ExoPlayer, Android DASH, DRM,
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: willzhan, dwgeo
ms.openlocfilehash: 158b58c13aee4d6241900db4a5e2b3fe8a45cc3c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="offline-widevine-streaming-for-android"></a>Kapcsolat nélküli Widevine streaming androidhoz

Mellett az online adatfolyamként történő tartalomvédelemről, a media tartalom előfizetés és bérleti szolgáltatások ajánlat letölthető tartalom működik, amikor nem csatlakoznak az internethez. Töltse le a tartalom betöltését a telefonja szükség lehet, vagy lejátszható repülési üzemmód, amikor hajózó a tábla nem kapcsolódik a hálózathoz. További helyzeteket, amelyekben érdemes a tartalom letöltésére:

- Néhány tartalomszolgáltatók DRM licenc kézbesítési ország szegély túl lehet letiltása. Ha egy felhasználó kíván tartalom megtekintése a külföldön utazás közben, kapcsolat nélküli letöltési van szükség.
- Egyes országokban Internet rendelkezésre állási és/vagy a sávszélesség korlátozva. Előfordulhat, hogy a felhasználók eldönthetik tudjanak megnézheti felbontása kielégítő megtekintését a tartalom letöltéséhez.

Ez a cikk ismerteti a valósítja meg a kapcsolat nélküli módban lejátszás DASH tartalom Widevine védi az Android-eszközökön. Kapcsolat nélküli DRM lehetővé teszi, hogy adja meg az előfizetést, bérbeadás és beszerzési modellek könnyen érvénybe, ha kapcsolódik az internethez a tartalom az ügyfelek a szolgáltatások engedélyezése a tartalomhoz.

Az Android player alkalmazások készítéséhez, azt szerkezeti három beállítások:

> [!div class="checklist"]
> * Egy Java API a ExoPlayer SDK használatával player összeállítása
> * Egy ExoPlayer SDK Xamarin-kötést használja player összeállítása
> * Egy titkosított adathordozó-bővítmény (EME) és Media forrás bővítmény (MSE) használatával, vagy újabb rendszerben Chrome böngésző mobil v62 player összeállítása

A cikk is a kapcsolat nélküli adatfolyamként való küldése a Widevine védett tartalom kapcsolatos gyakori kérdésekre ad választ.

## <a name="requirements"></a>Követelmények 

Mielőtt Widevine offline DRM megvalósításához Android-eszközön, akkor először:

- Ismerkedjen meg a Widevine DRM segítségével online tartalomvédelmi bemutatott fogalmakkal. Ez részletesen is ismertetjük a következő dokumentumok minták:
    - [Az Azure Media Services DRM-licencek vagy AES-kulcsok](media-services-deliver-keys-and-licenses.md)
    - [CENC többplatformos DRM és hozzáférés-vezérlés használatával: Egy referenciaterv és megvalósítás az Azure-on és az Azure Media Services szolgáltatásban](media-services-cenc-with-multidrm-access-control.md)
    - [PlayReady és/vagy Widevine Dynamic Common Encryption a .NET használatával](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [A .NET PlayReady és/vagy Widevine licencek Azure Media Services segítségével](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Ismerkedjen meg a Google ExoPlayer SDK az Android, az offline Widevine DRM-lejátszás támogathatja SDK nyílt forráskódú videólejátszó. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer fejlesztői útmutató](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer fejlesztői Blog](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>A tartalom védelmi konfigurációja az Azure Media Services

Egy eszköz Widevine-védelem konfigurálásakor a Media Services ContentKeyAuthorizationPolicyOption, amely a következő három műveletet a megadott létrehozásához szükséges:

1. DRM rendszer (Widevine)
2. Hogyan kulcs tartalomkézbesítési megadó ContentKeyAuthorizationPolicyRestriction engedélyezett licenctovábbítási szolgáltatása (nyitott vagy engedélyezési jogkivonatot)
3. DRM (Widevine) licenc sablonja

Ahhoz, hogy **offline** Widevine-licencek módját, konfigurálnia kell [Widevine-licencsablon](media-services-widevine-license-template-overview.md). Az a **policy_overrides** objektumazonosító, állítsa be a **can_persist** tulajdonságot **igaz** (alapértelmezett értéke false). 

A következő példakód engedélyezéséhez használja a .NET **offline** Widevine-licencek módját. A kód alapján a [ segítségével PlayReady és/vagy Widevine Dynamic Common Encryption a .NET](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) minta. 

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
            //can_renew = true,                             //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),   //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
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

## <a name="configuring-the-android-player-for-offline-playback"></a>A kapcsolat nélküli hanglejátszáshoz Android player konfigurálása

Android-eszközökhöz a natív lejátszó alkalmazások fejlesztéséhez legegyszerűbb módja a használja a [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), egy nyílt forráskódú videólejátszó SDK. ExoPlayer támogatja az Android tartozó natív MediaPlayer API, például MPEG-DASH vagy Microsoft Smooth Streaming kézbesítési protokollok által jelenleg nem támogatott funkciókat.

2.6-os vagy újabb verzió ExoPlayer sok osztály, amely támogatja a kapcsolat nélküli Widevine DRM-lejátszási tartalmaz. Különösen a OfflineLicenseHelper osztály biztosít a segédprogram-függvények letöltése, megújításával és offline licencet felengedése a DefaultDrmSessionManager alkalmazásának elősegítése érdekében. Az SDK mappából megadott osztályok "könyvtár/core/src/main/java/com/google/android/exoplayer2/offline /" támogatja az offline videó tartalom letöltése.

Az alábbi listán szereplő osztályok elősegítheti a offline mód az ExoPlayer SDK for Android:

- library/core/src/main/java/com/google/android/exoplayer2/drm/OfflineLicenseHelper.java  
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DefaultDrmSessionManager.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/DrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ErrorStateDrmSession.java
- library/core/src/main/java/com/google/android/exoplayer2/drm/ExoMediaDrm.java
- library/core/src/main/java/com/google/android/exoplayer2/offline/SegmentDownloader.java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/DownloaderConstructorHelper.Java 
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/Downloader.Java
- Library/dash/src/Main/Java/com/Google/Android/exoplayer2/Source/dash/offline/DashDownloader.Java 

A fejlesztők hivatkoznia kell a [ExoPlayer – útmutató fejlesztőknek](https://google.github.io/ExoPlayer/guide.html) és a megfelelő [fejlesztői blogja](https://medium.com/google-exoplayer) az alkalmazások fejlesztése során. Google nem adott ki a kapcsolat nélküli Widevine támogató jelenleg, úgy az adatokat pedig csak a fejlesztői útmutató és blog ExoPlayer alkalmazás teljesen dokumentált hivatkozás végrehajtása vagy a minta kódját. 

### <a name="working-with-older-android-devices"></a>Régebbi Android-eszközök használata

Néhány régebbi Android-eszközökhöz, meg kell adni a következő értékek **policy_overrides** tulajdonságok (definiált [Widevine-licencsablon](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, és **license_duration_seconds**. Másik lehetőségként állíthatja őket nulla, ami azt jelenti, hogy a végtelen korlátlan időtartama.  

Az értékeket kell állítani egy egész túlcsordulási hiba elkerülése érdekében. A problémával kapcsolatos további ismertetése https://github.com/google/ExoPlayer/issues/3150 és https://github.com/google/ExoPlayer/issues/3112. <br/>Ha az értékek nem meg explicit módon, a nagyon nagy értékeket **PlaybackDurationRemaining** és **LicenseDurationRemaining** lesz hozzárendelve, (például 9223372036854775807, azaz a maximálisan pozitív értéket egy 64 bites egész szám). Ennek eredményeképpen a Widevine-licenc lejárt jelenik meg, és ezért a visszafejtés nem fog megtörténni. 

Ez a probléma jelentkezik Android 5.0 Egyszerű fejrészalakzat vagy újabb Android 5.0 az első Android verziója, amely úgy tervezték, hogy teljes mértékben támogassa az ARMv8 óta ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) és 64 bites platformon, Android 4.4 KitKat közben eredetileg arra tervezték, hogy ARMv7 és más régebbi Android verzióival 32 bites platformon.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Használja a Xamarin Android lejátszás alkalmazás létrehozásához

Xamarin kötései ExoPlayer találja meg a következő hivatkozásokkal:

- [Xamarin kötések könyvtár a Google ExoPlayer könyvtára](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin ExoPlayer NuGet kötései](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Azt is ellenőrizze, a következő szálat: [Xamarin kötés](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Androidhoz készült Chrome-player alkalmazások

A kiadástól kezdve [Chrome Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates), EME állandó licenc esetén támogatott. [Widevine-L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) most is támogatott Chrome Android rendszerhez. Ez lehetővé teszi, hogy a Chrome-ban offline lejátszás alkalmazások létrehozása, ha a végfelhasználók számára a (vagy magasabb) Chrome verzióját. 

Ezenkívül Google fokozatos Web App (a Project Web Access) minta előállított és nyílt-forrása azt: 

- [Forráskód](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google üzemeltetett verzió](https://biograf-155113.appspot.com/ttt/episode-2/) (csak akkor működik, a Chrome-v 62 és újabb, Android-eszközökön)

Ha a mobil Chrome böngésző v62 (vagy újabb rendszerre) frissítése során az Android rendszerű telefonok és a teszt a fenti üzemeltetett mintaalkalmazás, látni fogja, hogy működik is online adatfolyam-továbbításhoz és offline lejátszás.

A fenti nyílt forráskódú Project Web Access alkalmazásnak a Node.js-ben hozta létre. Ha szeretné futtatni a saját egy Ubuntu server verziója, vegye figyelembe a következő általános talált hibák, amelyek megakadályozhatják a lejátszás:

1. A CORS probléma: A minta a mintaalkalmazás videó üzemeltetett https://storage.googleapis.com/biograf-video-files/videos/. Google van állítva a CORS minden, a Google Cloud Storage gyűjtő tárolt vizsgálati minták. Azok a CORS fejlécek, a CORS-bejegyzés explicit megadásával szolgáltatott: https://biograf-155113.appspot.com (a tartomány mely google üzemelteti a minta) által a webhelyek elérésének megakadályozásához. Ha megpróbál, látni fogja a következő HTTP-hiba: nem sikerült betölteni a https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: nincs "Hozzáférés-vezérlési-engedélyezése-forrás" fejléc-e a kért erőforrás. Forrás "https://13.85.80.81:8080" ezért nem megengedett hozzáférést. Ha egy nem átlátszó választ szolgál az igényeinek, "no-cors" az erőforrást beolvasni a CORS le van tiltva a kérelem mód beállítása.
2. A probléma tanúsítvány: Chrome v 58 kezdve a Widevine EME HTTPS PROTOKOLLT igényel. Ezért egy X509 HTTPS-KAPCSOLATON keresztüli mintaalkalmazás tárolásához szükséges tanúsítvány. A szokásos teszttanúsítványt a következő követelmények miatt nem működik: be kell szereznie egy tanúsítványt a következő minimális követelményeknek:
    - Chrome és Firefox szükséges SAN-tulajdonos alternatív neve a beállítást, ha a tanúsítvány szerepel
    - Kell a tanúsítvány megbízható Hitelesítésszolgáltatói és önaláírt fejlesztési tanúsítványt nem működik.
    - A tanúsítvány CN-a DNS-nevét a webkiszolgáló vagy az átjáró nevének kell lennie.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="question"></a>Kérdés

Hogyan által biztosított a (offline-kompatibilis) az egyes ügyfelek és felhasználók állandó licencek és mások számára nem állandó licencek (offline kikapcsolva)? Van a tartalom másolása és külön tartalomkulcsot használni?

### <a name="answer"></a>Válasz
Nem kell a tartalom másolása. A tartalmat, és egyetlen ContentKeyAuthorizationPolicy, de két külön ContentKeyAuthorizationPolicyOption egyetlen példányát segítségével egyszerűen:

1. IContentKeyAuthorizationPolicyOption 1: használ állandó licenccel, valamint egy jogcímet, például a license_type tartalmazó ContentKeyAuthorizationPolicyRestriction 1 = "Állandó"
2. IContentKeyAuthorizationPolicyOption 2: használja a nem állandó licenc, és egy jogcímet, például a license_type tartalmazó ContentKeyAuthorizationPolicyRestriction 2 = "Nonpersistent"

Így, ha az ügyfél alkalmazásból licenc kérelem érkezik, licenc kérelemből nincs különbség. Másik felhasználó vagy eszköz, azonban az STS kell a különböző jogcímeket (a fenti két license_type egyik) tartalmazó különböző JWT-jogkivonatokat kibocsátani, üzleti logikát. A JWT jogkivonat jogcím értéke használandó licenc szolgáltatás úgy dönt, hogy milyen típusú licenc: állandó vagy nem állandó.

Ez azt jelenti, hogy az üzleti logika és ügyféleszköz/adatokat adja hozzá a megfelelő jogcím értékét egy jogkivonatba rendelkeznie kell a Secure Token Service (STS).

### <a name="question"></a>Kérdés

Widevine biztonsági szintek, a Google [Widevine DRM architektúra – áttekintés doc](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentáció, akkor három különböző biztonsági szintet határozza meg. Azonban a [Widevine-licencsablon Azure Media Services-dokumentációja](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), vázolt öt különböző biztonsági szint. Mi az a kapcsolatot, vagy a két különböző biztonsági szintek közötti leképezést?

### <a name="answer"></a>Válasz

A Google [Widevine DRM-architektúra áttekintése](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), azt határozza meg a következő három biztonsági szintek:

1.  1. biztonsági szint: Az összes tartalom feldolgozása, a titkosítás és a vezérlés készül belül a megbízható végrehajtási környezetet (TEE). Az egyes megvalósítási modellek biztonsági feldolgozása a különböző processzorok hajthatja végre.
2.  Biztonsági szint 2: Végez cryptography (de nem videó feldolgozási) belül a TEE: visszafejtett pufferek alkalmazástartományt vissza, és külön videó hardveres vagy szoftveres keresztül. 2. szint azonban kriptográfiai információk feldolgozása van csak a TEE belül.
3.  3. biztonsági szint nem rendelkezik egy TEE az eszközön. Előfordulhat, hogy a titkosított adatok és a visszafejtett tartalmat a gazda operációs rendszer védelme hajtja végre a megfelelő intézkedéseket. A 3. szint megvalósításának tartalmazhat egy hardver kriptográfiai motor azonban, hogy csak javítja a teljesítményt, nem biztonsági.

Ugyanakkor, a [Widevine-licencsablon Azure Media Services-dokumentációja](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), content_key_specs security_level tulajdonságának rendelkezhet a következő öt különböző érték (szolgáltatás megbízhatóságára vonatkozó ügyfélkövetelmények lejátszás):

1.  A szoftveres whitebox titkosítási szükség.
2.  Szoftver titkosítási és egy rejtjelezett dekóder szükség.
3.  A kulcsokat tárol, és a titkosítási műveletek belül kell végrehajtani egy hardveres biztonsági TEE.
4.  A titkosítási és a tartalom dekódolási belül kell végrehajtani egy hardveres biztonsági TEE.
5.  A titkosítási dekódolási és az összes kezelési az adathordozó (tömörített és tömörítetlen) belül kell kezelni egy hardveres biztonsági TEE.

Google Widevine mindkét biztonsági szintek határozzák meg. A különbség van a használati szintje: architektúra vagy API szintjén. Az öt biztonsági szintek a Widevine API-t használnak. Security_level tartalmazó content_key_specs objektum deszerializálása és a Widevine globális kézbesítési szolgáltatás Azure Media Services Widevine-licenc szolgáltatás által átadott. Az alábbi táblázat a két különböző biztonsági szintek közötti leképezést.

| **Widevine-architektúra meghatározott biztonsági szintek** |**A Widevine API használt biztonsági szintek**|
|---|---| 
| **Biztonsági szint 1**: az összes tartalom feldolgozása, a titkosítás és a vezérlés belül a megbízható végrehajtási környezetet (TEE) hajtja végre. Az egyes megvalósítási modellek biztonsági feldolgozása a különböző processzorok hajthatja végre.|**security_level = 5**: A titkosítási dekódolási és az összes kezelési az adathordozó (tömörített és tömörítetlen) belül kell kezelni egy hardveres biztonsági TEE.<br/><br/>**security_level = 4**: A titkosítási és a tartalom dekódolási belül kell végrehajtani egy hardveres biztonsági TEE.|
**Biztonsági szint 2**: hajtja végre a titkosítás (de nem videó feldolgozási) belül a TEE: visszafejtett pufferek alkalmazástartományt vissza, és külön videó hardveres vagy szoftveres keresztül. 2. szint azonban kriptográfiai információk feldolgozása van csak a TEE belül.| **security_level = 3**: A kulcsokat tárol, és a titkosítási műveletek belül kell végrehajtani egy hardveres biztonsági TEE. |
| **3. biztonsági szint**: nem rendelkezik egy TEE az eszközön. Előfordulhat, hogy a titkosított adatok és a visszafejtett tartalmat a gazda operációs rendszer védelme hajtja végre a megfelelő intézkedéseket. A 3. szint megvalósításának tartalmazhat egy hardver kriptográfiai motor azonban, hogy csak javítja a teljesítményt, nem biztonsági. | **security_level = 2**: szoftver titkosítási és egy rejtjelezett dekóder szükség.<br/><br/>**security_level = 1**: whitebox szoftveres titkosítási szükség.|

### <a name="question"></a>Kérdés

Miért nem tartalom letöltése igénybe túl sokáig?

### <a name="answer"></a>Válasz

Letöltési sebesség növelése érdekében két módja van:

1.  Engedélyezze a CDN, hogy a végfelhasználók várhatóan több CDN találati származási/streamvégponton való tartalomletöltés helyett. Ha a felhasználó találatok száma a streamvégpontján, minden egyes HLS szegmens vagy kötőjel töredék dinamikusan csomagolt és titkosított. Annak ellenére, hogy a késés az ezredmásodperces skálájának minden szegmens vagy töredék, ha egy hosszú videó óra, a halmozott késés, amely hosszabb letöltési tekintélyes lehet.
2.  Adja meg a végfelhasználók szelektív a videominőséget rétegek és zeneszámok helyett minden tartalom letöltése beállítás. A kapcsolat nélküli módban nincs letöltése minőségi rétegeit pont. Ennek eléréséhez két módja van:
    1.  Ügyfél szabályozott: player alkalmazás automatikus kiválasztása vagy a felhasználó kijelöli a jó minőségű réteg és zeneszámok letöltésére;
    2.  Szolgáltatás szabályozott: egy dinamikus Manifest szolgáltatás segítségével az Azure Media Services (globális) szűrő, amely HLS lista vagy kötőjel MPD korlátozza az egyetlen jó minőségű réteghez és zeneszámok kijelölt létrehozása. A letöltési URL-címet, a végfelhasználók számára megjelenített tartalmazza majd a szűrőt.

## <a name="summary"></a>Összegzés

Ez a cikk tárgyalja megvalósításához a kapcsolat nélküli módban lejátszás DASH tartalom Widevine védi az Android-eszközökön.  Azt is választ offline adatfolyamként való küldése a Widevine védett tartalom kapcsolatos gyakori kérdésekre.
