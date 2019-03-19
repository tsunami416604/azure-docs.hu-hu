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
ms.date: 02/08/2019
ms.author: willzhan, dwgeo
ms.openlocfilehash: bf2f2db57f33645389fd751c8c00f9f135416c50
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57864133"
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine androidhoz streaming  

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [3-as verzió](../latest/offline-widevine-for-android.md)
> * [2-es verzió](offline-widevine-for-android.md)

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

## <a name="requirements"></a>Követelmények 

Mielőtt Widevine offline DRM végrehajtása Android-eszközökön, akkor először:

- Alaposabban megismerheti a használata a Widevine DRM-mel online content protection bemutatott fogalmakkal. Ez az a következő dokumentumok minták részletesen ismertetett:
    - [DRM-licencek és az AES-kulcsok az Azure Media Services használatával](media-services-deliver-keys-and-licenses.md)
    - [CENC többplatformos DRM és hozzáférés-vezérlés: Egy Referenciaterv és megvalósítás az Azure és az Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [PlayReady és/vagy Widevine Dynamic Common Encryption titkosítás használata a .NET használatával](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [A PlayReady és/vagy Widevine-licencek .NET-tel az Azure Media Services használatával](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Megismerkedhet a Google ExoPlayer SDK-val Android-egy nyílt forráskódú videólejátszó SDK offline Widevine DRM-mel lejátszási támogathatja. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer fejlesztői útmutató](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer fejlesztői Blog](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Az Azure Media Services Content protection konfigurálása

A Media Services Widevine védelmi eszköz konfigurálásakor ContentKeyAuthorizationPolicyOption, amely a következő három dolgot megadott létrehozásához szükséges:

1. DRM-rendszer (Widevine)
2. Adja meg, hogyan kulcs tartalomkézbesítési ContentKeyAuthorizationPolicyRestriction engedélyezve van a szolgáltatásra vonatkozó (nyitott vagy jogkivonat-engedélyezési)
3. DRM (Widevine) licencsablon

Ahhoz, hogy **offline** mód a Widevine-licencek, konfigurálnia kell [Widevine-licencsablon](media-services-widevine-license-template-overview.md). Az a **policy_overrides** objektumazonosító, állítsa be a **can_persist** tulajdonságot **igaz** (az alapértelmezett érték FALSE (hamis)). 

Az alábbi kódmintában engedélyezéséhez használja a .NET **offline** Widevine-licencek módot. A kód alapján a [ segítségével PlayReady és/vagy Widevine Dynamic Common Encryption .NET-tel](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) minta. 

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

## <a name="configuring-the-android-player-for-offline-playback"></a>A kapcsolat nélküli lejátszás céljából Android player konfigurálása

Android-eszközök esetén a natív lejátszó alkalmazások fejlesztése a legegyszerűbb módja az, hogy használja a [Google ExoPlayer SDK](https://github.com/google/ExoPlayer), egy videó lejátszási nyílt forráskódú SDK-t. ExoPlayer támogatja az Android a natív Media Player API-t, beleértve a Microsoft Smooth Streaming és MPEG-dash PROTOKOLLHOZ a kézbesítési protokollra által jelenleg nem támogatott szolgáltatásokat.

ExoPlayer 2.6-os vagy újabb verzió, amelyek támogatják az offline Widevine DRM-mel lejátszási sok osztály tartalmazza. Ilyen például a OfflineLicenseHelper osztály megkönnyítése érdekében a DefaultDrmSessionManager letöltésével, megújítása és felszabadítása offline licencek használatát segédprogram funkciókat biztosít a. Az SDK mappából a megadott osztályokat "könyvtár/core/src/main/java/com/google/android/exoplayer2/offline /" támogatja az offline videó tartalom letöltése.

A következő osztályok listáját, megkönnyítik a kapcsolat nélküli módban ExoPlayer Androidhoz készült SDK-ban:

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

A fejlesztők kell hivatkoznia a [ExoPlayer fejlesztői útmutató](https://google.github.io/ExoPlayer/guide.html) és a megfelelő [fejlesztői Blog](https://medium.com/google-exoplayer) -alkalmazások fejlesztése során. Google nem jelent meg egy teljes körű dokumentációval rendelkező referencia megvalósítása vagy minta kódot támogató jelenleg Widevine offline, így az információk a fejlesztői útmutató és blog ExoPlayer alkalmazás. 

### <a name="working-with-older-android-devices"></a>Régebbi Android-eszközök használata

Egyes régebbi Android-eszközön újra be kell állítani a következő értékek **policy_overrides** tulajdonságok (meghatározott [Widevine-licencsablon](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds**, és **license_duration_seconds**. Másik lehetőségként beállíthatja őket nulla, ami azt jelenti, hogy a végtelen vagy határozatlan időre szól.  

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

1. CORS-hiba: A videó a mintaalkalmazás minta üzemeltetett https://storage.googleapis.com/biograf-video-files/videos/. Google állított be a CORS minden, a Google Cloud Storage gyűjtőben lévő üzemeltetett teszt minták. Ezek a szolgálja ki a CORS fejlécek, explicit módon a CORS-bejegyzés megadása: https://biograf-155113.appspot.com (a tartomány melyik google-beli üzemelteti a minta) megakadályozza a hozzáférést a többi webhelyhez sem. Ha megpróbál, látni fogja a következő HTTP-hiba: Nem sikerült betölteni a https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: nincs "Access-Control-Allow-Origin" fejléc nem található a kért erőforrás. Forrás:https://13.85.80.81:8080"hozzáférés ezért nem engedélyezett. Ha egy nem átlátszó választ szolgálja ki az igényeinek, "no-cors" beolvasni az erőforrás le van tiltva cors-támogatással rendelkező a kérelem módjának beállítása.
2. A tanúsítvány kiadása: Kezdet: Chrome v 58, a Widevine az EME HTTPS van szüksége. Ezért a mintaalkalmazás futtatásához egy X509 a HTTPS-kapcsolaton keresztül kell tanúsítványt. Az alábbi követelmények miatt nem működik a szokásos tesztelési tanúsítvány: Be kell szereznie egy tanúsítványt a következő minimális rendszerkövetelményeknek megfelelő:
    - A Chrome és a Firefox SAN-tulajdonos alternatív neve beállítást a tanúsítvány léteznie kell
    - Kell a tanúsítvány megbízható hitelesítésszolgáltató és a egy önaláírt fejlesztési tanúsítvány nem működik.
    - A tanúsítvány CN-nevének a DNS-név a webalkalmazás-kiszolgáló vagy átjáró kell rendelkeznie.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="question"></a>Kérdés

Hogyan juttathat az állandó licencek (offline-kompatibilis) az egyes ügyfelek és felhasználói számára, és mások számára nem állandó licencek (offline letiltva)? Kell külön tartalomkulcsot, és a tartalom ismétlődő?

### <a name="answer"></a>Válasz
Nem kell a tartalom másolása. Egyszerűen használhatja a tartalom és a egy egyetlen ContentKeyAuthorizationPolicy, de a két külön ContentKeyAuthorizationPolicyOption egy példányát:

1. IContentKeyAuthorizationPolicyOption 1: használja a állandó licenc és a egy jogcímet, például a license_type tartalmazó ContentKeyAuthorizationPolicyRestriction 1 = "Állandó"
2. 2. IContentKeyAuthorizationPolicyOption: használja a nem állandó licenc és a egy jogcímet, például a license_type tartalmazó ContentKeyAuthorizationPolicyRestriction 2 = "Nonpersistent"

Ez esetben, amikor az ügyfél alkalmazásból licenc kérelem érkezik, licenc kérelem nincs különbség. A különböző felhasználó/eszköz, azonban az STS-re kell az üzleti logika különböző jogcímeket (a fenti két license_type egy) tartalmazó különböző JWT-jogkivonatok kiállításához. A JWT jogkivonat jogcím értéke használandó licenc szolgáltatás úgy dönt, hogy ki milyen típusú licenc: állandó és nem állandó.

Ez azt jelenti, hogy a Secure Token Service (STS) kell rendelkeznie a üzleti logikát és az ügyfél és eszköz adatokat adhat hozzá megfelelő jogcím értéke egy token.

### <a name="question"></a>Kérdés

A Widevine biztonsági szintek, a Google [Widevine DRM-architektúra áttekintése doc](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentáció, három különböző biztonsági szintet határoz meg. Azonban a [Azure Media Services dokumentációja a Widevine-licencsablon](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), vázolt öt különböző biztonsági szintet. Mi a kapcsolat vagy a két különböző eljáráscsoport biztonsági szintek közötti leképezést?

### <a name="answer"></a>Válasz

A Google [Widevine DRM-architektúra áttekintése](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf), azt határozza meg a következő három biztonsági szintek:

1.  1. biztonsági szint: Az összes tartalom feldolgozása, titkosítás és vezérlés belül a megbízható végrehajtási környezetben (TEE) történik. Néhány megvalósítás modellekben biztonsági feldolgozási hajtható végre a különböző lapkák.
2.  2. biztonsági szint: Titkosítás (de nem Videofeldolgozás) végez a TEE belül: visszafejtett pufferek alkalmazás tartományhoz adja vissza, és külön videó hardver vagy szoftver keresztül. 2. szintű azonban titkosítási információk feldolgozása van csak a TEE belül.
3.  3. biztonsági szint nem rendelkezik egy TEE az eszközön. Megfelelő intézkedéseket előfordulhat, hogy a titkosítási adatokat és a gazdagép operációs rendszere a visszafejtett tartalom védelme érdekében. A 3. szint megvalósítás is tartalmazhat egy hardveres titkosítási motor, de, amely csak növeli a teljesítményt, nem biztonsági.

Ugyanakkor, a [Azure Media Services dokumentációja a Widevine-licencsablon](https://docs.microsoft.com/azure/media-services/media-services-widevine-license-template-overview), content_key_specs security_level tulajdonságát az alábbi öt különböző érték (megbízhatóságára vonatkozó ügyfélkövetelmények lejátszás) is rendelkezik:

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
| **3. biztonsági szint**: Nem rendelkezik egy TEE az eszközön. Megfelelő intézkedéseket előfordulhat, hogy a titkosítási adatokat és a gazdagép operációs rendszere a visszafejtett tartalom védelme érdekében. A 3. szint megvalósítás is tartalmazhat egy hardveres titkosítási motor, de, amely csak növeli a teljesítményt, nem biztonsági. | **security_level=2**: Szoftvert a titkosításnak és a egy rejtjelezett dekóder megadása kötelező.<br/><br/>**security_level=1**: A szoftveres whitebox kriptográfiai megadása kötelező.|

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
