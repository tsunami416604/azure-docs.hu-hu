---
title: Offline Widevine streaming az Androidhoz Azure Media Services v3
description: Ez a témakör bemutatja, hogyan konfigurálhatja a Azure Media Services-fiókját a Widevine által védett tartalmak offline folyamatos átviteléhez.
services: media-services
keywords: DASH, DRM, Widevine offline üzemmód, ExoPlayer, Android
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
ms.openlocfilehash: 17416fce887f5f5b952232e284873e539fd68337
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779909"
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine streaming Android rendszerhez

Az online streaming-tartalmak védelme mellett a Media Content előfizetés és a Rental Services olyan letölthető tartalmakat is kínál, amelyek akkor működnek, ha nem csatlakozik az internethez. Előfordulhat, hogy a hálózati kapcsolat megszakadása esetén a tartalmat le kell töltenie a telefonra vagy a Tablet-ra. További forgatókönyvek, amelyekben előfordulhat, hogy le szeretné tölteni a tartalmat:

- Egyes tartalomszolgáltatók nem engedélyezhetik az ország/régió szegélyén túli DRM-licencek kézbesítését. Ha a felhasználó a külföldön való utazás közben szeretné megtekinteni a tartalmat, offline letöltésre van szükség.
- Egyes országokban/régiókban az Internet rendelkezésre állása és/vagy sávszélessége korlátozott. A felhasználók úgy dönthetnek, hogy letöltik a tartalmat, hogy elég nagy felbontásban tudják megtekinteni a megfelelő megjelenítési élményt.

Ez a cikk azt ismerteti, hogyan lehet az Android-eszközökön a Widevine által védett DASH-tartalmak offline módú lejátszását megvalósítani. Az offline DRM lehetővé teszi, hogy előfizetést, bérletet és vásárlási modelleket biztosítson a tartalomhoz, és lehetővé teszi a szolgáltatások ügyfelei számára, hogy az internetről való leválasztáskor könnyedén fogadják a tartalmakat.

Az androidos lejátszó alkalmazások létrehozásához három lehetőség áll rendelkezésre:

> [!div class="checklist"]
> * A ExoPlayer SDK Java API-ját használó játékosok létrehozása
> * A ExoPlayer SDK Xamarin-kötését használó lejátszó létrehozása
> * Lejátszó létrehozása titkosított Media Extension (EME) és Media Source Extension (MSE) használatával a Chrome Mobile Browser v62 vagy újabb verziójában

A cikk a Widevine által védett tartalom offline adatfolyamával kapcsolatos gyakori kérdésekre is választ ad.

> [!NOTE]
> Az offline DRM-t csak akkor számítjuk fel, ha egy licencre vonatkozó kérést készítenek a tartalom letöltésekor. A hibák számlázása nem történik meg.

## <a name="prerequisites"></a>Előfeltételek 

Mielőtt offline DRM-t implementál a Widevine Android-eszközökön, először a következőket kell tennie:

- Ismerkedjen meg az online tartalomvédelem Widevine DRM használatával bevezetett fogalmakkal. Ezt részletesen a következő dokumentumok/minták tartalmazzák:
    - [Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md)
    - [A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata](protect-with-drm.md)
- https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git klónozása.

    A Widevine-konfigurációk hozzáadásához módosítania kell a kódot a [DRM-titkosítással a .NET használatával](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) .  
- Ismerkedjen meg az Androidhoz készült Google ExoPlayer SDK-val, amely az offline Widevine DRM-lejátszást támogató nyílt forráskódú videolejátszó SDK-t támogatja. 
    - [ExoPlayer SDK](https://github.com/google/ExoPlayer)
    - [ExoPlayer fejlesztői útmutató](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer fejlesztői blog](https://medium.com/google-exoplayer)

## <a name="configure-content-protection-in-azure-media-services"></a>Tartalomvédelem konfigurálása a Azure Media Servicesban

A [GetOrCreateContentKeyPolicyAsync](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L189) metódusban a következő szükséges lépések jelennek meg:

1. Annak meghatározása, hogyan legyen engedélyezve a tartalmi kulcs kézbesítése a licenc kézbesítési szolgáltatásában: 

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
2. Widevine-licenc sablonjának konfigurálása:  

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

## <a name="enable-offline-mode"></a>Offline mód engedélyezése

A Widevine-licencek **Offline** módjának engedélyezéséhez konfigurálnia kell a [Widevine-licenc sablonját](widevine-license-template-overview.md). A **policy_overrides** objektumban állítsa **igaz** értékre a **can_persist** tulajdonságot (az alapértelmezett érték a False), ahogyan az a [ConfigureWidevineLicenseTempateben](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L563)látható. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#ConfigureWidevineLicenseTempate)]

## <a name="configuring-the-android-player-for-offline-playback"></a>Az Android-lejátszó konfigurálása offline lejátszáshoz

Az Android-eszközökhöz készült natív alkalmazások fejlesztésének legegyszerűbb módja, ha a [Google EXOPLAYER SDK](https://github.com/google/ExoPlayer)-t, egy nyílt forráskódú videolejátszó SDK-t használja. A ExoPlayer az Android natív Media Player API-ját jelenleg nem támogató funkciókat támogatja, beleértve az MPEG-DASH és a Microsoft Smooth Streaming kézbesítési protokollokat.

A ExoPlayer 2,6-es és újabb verziója számos olyan osztályt tartalmaz, amelyek támogatják az offline Widevine DRM lejátszását. A OfflineLicenseHelper osztály olyan segédprogram-funkciókat biztosít, amelyek megkönnyítik a DefaultDrmSessionManager használatát az offline licencek letöltéséhez, megújításához és felszabadításához. A "Library/Core/src/Main/Java/com/Google/Android/exoplayer2/offline/" SDK mappában megadott osztályok támogatják az offline videotartalom letöltését.

Az alábbi osztályok az Androidhoz készült ExoPlayer SDK-ban az offline üzemmódot teszik lehetővé:

- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/OfflineLicenseHelper. Java  
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSessionManager. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/DrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/ErrorStateDrmSession. Java
- Library/Core/src/Main/Java/com/Google/Android/exoplayer2/DRM/ExoMediaDrm. Java
- könyvtár/mag/src/Main/Java/com/Google/Android/exoplayer2/offline/SegmentDownloader. Java
- könyvtár/mag/src/Main/Java/com/Google/Android/exoplayer2/offline/DownloaderConstructorHelper. Java 
- könyvtár/mag/src/Main/Java/com/Google/Android/exoplayer2/offline/Downloader. Java
- könyvtár/kötőjel/src/Main/Java/com/Google/Android/exoplayer2/Source/Dash/offline/DashDownloader. Java 

Az alkalmazások fejlesztése során a fejlesztőknek hivatkoznia kell a [ExoPlayer fejlesztői útmutatóra](https://google.github.io/ExoPlayer/guide.html) és a megfelelő [fejlesztői blogra](https://medium.com/google-exoplayer) . A Google nem bocsátott ki teljes körűen dokumentált referenciát a ExoPlayer alkalmazáshoz, amely jelenleg a Widevine-et támogatja, így az információk a fejlesztői útmutatóra és blogra korlátozódnak. 

### <a name="working-with-older-android-devices"></a>A régebbi androidos eszközök használata

Egyes régebbi Android-eszközök esetén a következő **policy_overrides** tulajdonságok értékeit kell megadnia ( [Widevine-licenc sablonban](widevine-license-template-overview.md)definiálva: **rental_duration_seconds**, **playback_duration_seconds**és **license_duration_seconds**. Azt is megteheti, hogy nullára állítja őket, ami végtelen/korlátlan időtartamot jelent.  

Az értékeket úgy kell beállítani, hogy elkerülje az egész túlcsordulási hibát. A probléma részletes ismertetését lásd: https://github.com/google/ExoPlayer/issues/3150 és https://github.com/google/ExoPlayer/issues/3112. <br/>Ha nem adja meg explicit módon az értékeket, a **PlaybackDurationRemaining** és a **LicenseDurationRemaining** nagyon nagy érték lesz hozzárendelve (például 9223372036854775807, amely a 64 bites egész szám maximális pozitív értéke). Ennek eredményeképpen a Widevine-licenc lejárt, ezért a visszafejtés nem fog történni. 

Ez a probléma az Android 5,0-es vagy újabb verziójában nem fordul elő, mivel az Android 5,0 az első Android-verzió, amelyet a ARMv8 ([Advanced RISC Machine](https://en.wikipedia.org/wiki/ARM_architecture)) és a 64 bites platform teljes körű támogatására terveztek, míg az Android 4,4 KitKat eredetileg a ARMv7 és a 32-bites platformok támogatásához lett tervezve, más régebbi Android-verziókhoz hasonlóan.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Androidos lejátszási alkalmazás létrehozása a Xamarin használatával

A ExoPlayer Xamarin-kötéseit az alábbi hivatkozásokkal érheti el:

- [Xamarin-kötések könyvtára a Google ExoPlayer könyvtárához](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin-kötések a ExoPlayer-NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Továbbá tekintse meg a következő szálat: [Xamarin-kötés](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome Player-alkalmazások Android rendszerhez

A [Chrome for Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)verziójának megjelenése után a rendszer támogatja az állandó licencet az eme-ben. Az [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) mostantól az Androidhoz készült Chrome-ban is támogatott. Ez lehetővé teszi offline lejátszási alkalmazások létrehozását a Chrome-ban, ha a végfelhasználók a Chrome ezen (vagy újabb) verzióját használja. 

Emellett a Google létrehozta a progresszív Web App-(PWA-) mintát és a nyílt forráskódú IT-t: 

- [Forráskód](https://github.com/GoogleChromeLabs/sample-media-pwa)
- A [Google által üzemeltetett verzió](https://biograf-155113.appspot.com/ttt/episode-2/) (csak a Chrome v 62-es és újabb verzióiban működik Android-eszközökön)

Ha Android-telefonon frissíti a Mobile Chrome böngészőt a v62 (vagy újabb verzióra), és teszteli a fent üzemeltetett minta alkalmazást, látni fogja, hogy az online streaming és az offline lejátszás is működik-e.

A fenti nyílt forráskódú PWA alkalmazás a Node. js-ben lett létrehozva. Ha Ubuntu-kiszolgálón szeretné üzemeltetni a saját verzióját, vegye figyelembe a következő gyakori problémákat, amelyek megakadályozhatják a lejátszást:

1. CORS probléma: a minta alkalmazásban található minta videó https://storage.googleapis.com/biograf-video-files/videos/ található. A Google CORS állított be a Google Cloud Storage-gyűjtőben üzemeltetett összes tesztelési mintához. Ezek a CORS-fejlécekkel rendelkeznek, explicit módon megadják a CORS bejegyzést: https://biograf-155113.appspot.com (az a tartomány, amelyben a Google a mintát tárolja) megakadályozza a hozzáférést bármely más hely számára. Ha próbálkozik, a következő HTTP-hiba jelenik meg: nem sikerült https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: betölteni a (z) "hozzáférés-vezérlés – engedélyezés – forrás" fejlécet a kért erőforráson. A (z) "https:\//13.85.80.81:8080" forrás ezért nem engedélyezett a hozzáféréshez. Ha egy átlátszatlan válasz az igényét szolgálja, állítsa a kérést "No-CORS" értékre, hogy letiltsa az erőforrást a CORS letiltásával.
2. Tanúsítványra vonatkozó probléma: a Chrome v 58-től kezdődően a Widevine-hez szükséges, HTTPS-t igényel. Ezért a minta alkalmazást HTTPS-en keresztül kell üzemeltetni egy X509-tanúsítvánnyal. A szokásos tesztelési tanúsítvány a következő követelmények miatt nem működik: be kell szereznie egy tanúsítványt, amely megfelel a következő minimális követelményeknek:
    - A Chrome és a Firefox megköveteli, hogy a tanúsítványban létezik a SAN-tulajdonos alternatív neve beállítás
    - A tanúsítványnak megbízható HITELESÍTÉSSZOLGÁLTATÓval kell rendelkeznie, és az önaláírt fejlesztési tanúsítvány nem működik.
    - A tanúsítványnak rendelkeznie kell egy, a webkiszolgáló vagy az átjáró DNS-nevével egyező KN-névvel.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="question"></a>Kérdés

Hogyan biztosíthatok állandó licenceket (kapcsolat nélküli üzemmódban) egyes ügyfelekhez/felhasználókhoz és nem állandó licencekhez (offline – letiltva) mások számára? Duplikálni kell a tartalmat, és külön tartalmi kulcsot kell használnia?

### <a name="answer"></a>Válasz
Mivel a Media Services v3 lehetővé teszi, hogy az eszközök több StreamingLocators is rendelkezzenek. Lehetősége van

1.  Egy ContentKeyPolicy, amely license_type = "állandó", az "állandó" ContentKeyPolicyRestriction és a StreamingLocator;
2.  Egy másik ContentKeyPolicy, amely license_type = "nem állandó", a "nem állandó" értékű ContentKeyPolicyRestriction és a StreamingLocator.
3.  A két StreamingLocators eltérő ContentKey rendelkezik.

Az egyéni STS üzleti logikától függően a rendszer különböző jogcímeket ad ki a JWT-jogkivonatban. A jogkivonattal csak a megfelelő licenc szerezhető be, és csak a megfelelő URL-cím játszható le.

### <a name="question"></a>Kérdés

A Widevine biztonsági szintjeinek a Google [WIDEVINE DRM-architektúra áttekintés](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) dokumentációjában három különböző biztonsági szintet határoz meg. A Widevine- [licenc sablonjának Azure Media Services dokumentációjában](widevine-license-template-overview.md)azonban öt különböző biztonsági szint látható. Mi a kapcsolat vagy a leképezés két különböző biztonsági szint között?

### <a name="answer"></a>Válasz

A Google [WIDEVINE DRM-architektúrájának áttekintése](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)a következő három biztonsági szintet határozza meg:

1.  1\. biztonsági szint: a rendszer az összes tartalom feldolgozását, titkosítását és ellenőrzését a megbízható végrehajtási környezetben (TEE) hajtja végre. Egyes implementációs modellekben a biztonsági feldolgozás különböző chipeken végezhető el.
2.  2\. biztonsági szint: titkosítás (de nem a videó feldolgozása) végrehajtása a PÓLÓn belül: a visszafejtett pufferek visszakerülnek az alkalmazás-tartományba, és külön videó hardveren vagy szoftveren keresztül dolgozzák fel azokat. A 2. szinten azonban a titkosítási adatok feldolgozása még csak a PÓLÓn belül történik.
3.  A 3. biztonsági szint nem rendelkezik PÓLÓval az eszközön. A titkosítási információk és a visszafejtett tartalmak a gazdagép operációs rendszerén való védetté tételéhez megfelelő intézkedéseket lehet tenni. A 3. szintű implementációk hardveres titkosítási motort is tartalmazhatnak, de ez csak a teljesítményt javítja, nem pedig a biztonságot.

Ugyanakkor a [Widevine-licenc sablonjának Azure Media Services dokumentációjában](widevine-license-template-overview.md)a content_key_specs security_level tulajdonsága a következő öt különböző értékkel rendelkezhet (a lejátszáshoz szükséges ügyfél-megbízhatósági követelmények):

1.  A szoftveres alapú Whitebox-titkosítás szükséges.
2.  A szoftveres kriptográfia és a megzavarodott dekóder szükséges.
3.  A kulcsfontosságú anyagokat és titkosítási műveleteket egy hardveres biztonsági másolaton belül kell végrehajtani.
4.  A tartalom titkosítását és dekódolását egy hardveres PÓLÓn belül kell végrehajtani.
5.  A titkosítást, a dekódolást és az adathordozó összes kezelését (tömörítve és tömörítve) egy hardveres biztonsági másolaton belül kell kezelni.

A Google Widevine mindkét biztonsági szintet meghatározza. A különbség a használati szintjén van: architektúra vagy API-szint. Az öt biztonsági szint a Widevine API-ban használatos. A security_levelt tartalmazó content_key_specs objektum deszerializált, és a Widevine globális kézbesítési szolgáltatásnak továbbítja a Azure Media Services Widevine License Service. Az alábbi táblázat a két biztonsági szint közötti leképezést mutatja be.

| **A Widevine architektúrában definiált biztonsági szintek** |**A Widevine API-ban használt biztonsági szintek**|
|---|---| 
| **1. biztonsági szint**: a rendszer az összes tartalom feldolgozását, titkosítását és ellenőrzését a megbízható végrehajtási környezetben (Tee) hajtja végre. Egyes implementációs modellekben a biztonsági feldolgozás különböző chipeken végezhető el.|**security_level = 5**: a titkosítást, a dekódolást és az adathordozó összes kezelését (tömörítve és tömörítve) egy HARDVERes pólón belül kell kezelni.<br/><br/>**security_level = 4**: a tartalom titkosítását és dekódolását egy HARDVERes pólón belül kell végrehajtani.|
**2. biztonsági szint**: titkosítás (de nem a videó feldolgozása) végrehajtása a pólón belül: a visszafejtett pufferek visszakerülnek az alkalmazás-tartományba, és külön videó hardveren vagy szoftveren keresztül dolgozzák fel azokat. A 2. szinten azonban a titkosítási adatok feldolgozása még csak a PÓLÓn belül történik.| **security_level = 3**: a kulcsfontosságú anyagokat és titkosítási műveleteket egy hardveres biztonsági másolaton belül kell végrehajtani. |
| **3. biztonsági szint**: nem tartozik póló az eszközön. A titkosítási információk és a visszafejtett tartalmak a gazdagép operációs rendszerén való védetté tételéhez megfelelő intézkedéseket lehet tenni. A 3. szintű implementációk hardveres titkosítási motort is tartalmazhatnak, de ez csak a teljesítményt javítja, nem pedig a biztonságot. | **security_level = 2**: a szoftveres titkosítás és a megzavarodott dekóder szükséges.<br/><br/>**security_level = 1**: a szoftveres Whitebox-alapú titkosítás szükséges.|

### <a name="question"></a>Kérdés

Miért tart ilyen sokáig a tartalom letöltése?

### <a name="answer"></a>Válasz

A letöltési sebesség javítása kétféleképpen lehetséges:

1.  Engedélyezze a CDN-t, hogy a végfelhasználók a tartalom letöltéséhez a forrás/streaming végpont helyett nagyobb eséllyel megkeressék a CDN-t. Ha a felhasználó eléri a folyamatos átviteli végpontot, a rendszer minden egyes HLS-szegmenst vagy DASH-töredéket dinamikusan csomagol és titkosít. Annak ellenére, hogy ez a késés az egyes szegmensek/töredékek esetében ezredmásodperces skálán van, ha egy órás videóval rendelkezik, a felhalmozott késés nagy valószínűséggel hosszabb időt is igénybe vehet.
2.  Adja meg a végfelhasználók számára a minőségi rétegek és hangsávok szelektív letöltését az összes tartalom helyett. Offline módban nem lehet letölteni az összes minőségi réteget. A következő két módon valósítható meg:
    1.  Ügyfél által vezérelt: vagy a Player alkalmazás automatikus kiválasztása vagy a felhasználó kiválasztja a videó minőségi réteget és a hangsávokat a letöltéshez.
    2.  Szolgáltatás által vezérelt: a (z) Azure Media Services dinamikus jegyzékfájl funkciójának használatával létrehozhat egy (globális) szűrőt, amely korlátozza a HLS lejátszási listákat vagy a DASH MPD-t egyetlen videó minőségi rétegre és kiválasztott hangsávokra. Ezután a végfelhasználók számára megjelenített letöltési URL-cím tartalmazza ezt a szűrőt.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="summary"></a>Összefoglalás

Ez a cikk azt ismerteti, hogyan lehet az Android-eszközökön a Widevine által védett DASH-tartalmak offline módú lejátszását megvalósítani.  Emellett a Widevine által védett tartalom offline adatfolyamával kapcsolatos gyakori kérdésekre is válaszolt.
