---
title: Az Azure Media Services v3-as rendszergyakran feltett kérdései| Microsoft dokumentumok
description: Ez a cikk választ ad az Azure Media Services v3-as, gyakran ismételt kérdésekre.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/07/2020
ms.author: juliako
ms.openlocfilehash: a4f4bd6eaa07907dd672abe068b515b5127adac9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886823"
---
# <a name="media-services-v3-frequently-asked-questions"></a>A Media Services v3-as kérdése

Ez a cikk választ ad az Azure Media Services (AMS) v3-as, gyakran ismételt kérdésekre.

## <a name="general"></a>Általános kérdések

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Milyen Azure-szerepkörök hajthatnak végre műveleteket az Azure Media Services-erőforrásokon? 

Lásd: [Szerepköralapú hozzáférés-vezérlés (RBAC) a Media Services-fiókokhoz.](rbac-overview.md)

### <a name="how-do-you-stream-to-apple-ios-devices"></a>Hogyan streamelhető Apple iOS-eszközökre?

Győződjön meg arról, hogy az elérési út végén (az URL "/manifest" része után) a "(format=m3u8-aapl)" van az elérési út végén, hogy a streamelési forráskiszolgáló visszaadja a HLS-tartalmat az Apple iOS natív eszközein történő felhasználáshoz (a részleteket lásd: [tartalomkézbesítés).](dynamic-packaging-overview.md)

### <a name="how-do-i-configure-media-reserved-units"></a>Hogyan konfigurálhatom a Media által lefoglalt egységeket?

A Media Services v3 vagy a Video Indexer által aktivált hangelemzési és videoelemzési feladatok esetében javasolt 10 S3 MRU-val ellátni a fiókot. Ha 10-nél több S3 MRUs-ra van szüksége, nyisson meg egy támogatási jegyet az [Azure Portalon.](https://portal.azure.com/)

További információt az [Adathordozó-feldolgozás méretezése CLI-vel](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Mi az ajánlott módszer a videók feldolgozására?

[Az Átalakítások](https://docs.microsoft.com/rest/api/media/transforms) segítségével konfigurálhatja a videók kódolására vagy elemzésére vonatkozó gyakori feladatokat. Minden **átalakítás** egy receptet vagy a video- vagy hangfájlok feldolgozására szolgáló feladatok munkafolyamatát írja le. A [feladat](https://docs.microsoft.com/rest/api/media/jobs) az a tényleges kérés a Media Services számára, hogy az **Átalakítás** t egy adott bemeneti videóra vagy hangtartalomra alkalmazza. Az átalakítás létrehozása után a feladatokat a Media Services API-k vagy a közzétett SDK-k használatával küldheti el. További információt az [átalakításokkal és feladatokkal](transforms-jobs-concept.md) kapcsolatos cikkben olvashat.

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>Feltöltöttem, kódoltam és közzétettem egy videót. Mi lenne az oka a videó nem játszik le, amikor megpróbálom stream el?

Az egyik leggyakoribb oka az, hogy nem rendelkezik a streamelési végpont, amelyből próbál lejátszani a futó állapotban.

### <a name="how-does-pagination-work"></a>Hogyan működik a tördelés?

A tördelés használatakor mindig a következő hivatkozást használja a gyűjtemény számbavételéhez, és ne függjön egy adott oldalmérettől. További részletek és példák: [Szűrés, rendezés, lapozás.](entities-overview.md)

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Milyen funkciók még nem érhetők el az Azure Media Services v3-as verzióban?

A részleteket lásd a [v2 API-kkal kapcsolatos funkcióhiányosságokban.](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Mi a Media Services-fiók előfizetések közötti áthelyezésének folyamata?  

További információt a [Media Services-fiók áthelyezése előfizetések között](media-services-account-concept.md).

## <a name="live-streaming"></a>Live streaming (Élő adatfolyam) 

### <a name="how-to-stop-the-live-stream-after-the-broadcast-is-done"></a>Hogyan lehet leállítani az élő közvetítést az adás befejezése után?

Az ügyfél- vagy kiszolgálóoldalról is megközelítheti.

#### <a name="client-side"></a>Ügyféloldal

A webalkalmazásnak meg kell kérnie a felhasználót, ha be szeretné zárni az adást, ha bezárja a böngészőt. Ez egy olyan böngészőesemény, amelyet a webalkalmazás kezelni tud.

#### <a name="server-side"></a>Szerver oldal

Az eseményrács eseményeire való feliratkozással figyelheti az élő eseményeket. További információt az [eventgrid eseményséma című témakörben talál.](media-services-event-schemas.md#live-event-types)

* Feliratkozhat [subscribe](reacting-to-media-services-events.md) a [Microsoft.Media.LiveEventEncoderKapcsolat](media-services-event-schemas.md#liveeventencoderdisconnected) streamszintjére, és figyelheti, hogy egy ideig nem jönnek létre újracsatlakozások az élő esemény leállításához és törléséhez.
* Vagy [feliratkozhat](reacting-to-media-services-events.md) a pályaszintű [szívverési](media-services-event-schemas.md#liveeventingestheartbeat) eseményekre. Ha az összes szám bejövő bitrátája 0-ra csökken; vagy az utolsó időbélyeg már nem növekszik, akkor is biztonságosan állítsa le az élő eseményt. A szívveréses események minden 20 másodpercben érkeznek minden számra, így egy kicsit bőbeszédűek lehetnek.

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Hogyan lehet beszúrni szünetek / videók és kép pala során élő közvetítés?

A Media Services v3 élő kódolása még nem támogatja a video- vagy képlapképek beillesztését az élő közvetítés során. 

A forrásvideó közötti váltáshoz használhatja a [helyszíni kódolót.](recommended-on-premises-live-encoders.md) Számos alkalmazás lehetővé teszi a források közötti váltást, beleértve a Telestream Wirecast, switcher studio (iOS), OBS Studio (ingyenes alkalmazás) és még sok más.

## <a name="content-protection"></a>Tartalomvédelem

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>AES-128-as titkosítás vagy DRM-rendszer használatát kell használnom?

Az ügyfelek gyakran csodálkoznak, hogy AES titkosítást vagy DRM-rendszert kell-e használniuk. Az elsődleges különbség a két rendszer között az, hogy az AES titkosítással a tartalomkulcs továbbításra kerül az ügyfélnek TLS-en keresztül, így a kulcs átvitel közben titkosítva van, de további titkosítás nélkül ("a tiszta"). Ennek eredményeképpen a tartalom visszafejtéséhez használt kulcs elérhető az ügyféllejátszó számára, és az ügyfél hálózati nyomkövetésében egyszerű szövegként tekinthető meg. Az AES-128 egyértelmű kulcstitkosítás olyan esetekben használható, amikor a megtekintő megbízható fél (például a vállalaton belül terjesztett vállalati videók titkosítása, amelyeket az alkalmazottak megtekintenek).

A DRM-rendszerek, például a PlayReady, a Widevine és a FairPlay egyaránt további titkosítási szintet biztosítanak a tartalom visszafejtéséhez használt kulcson, mint az AES-128 egyértelmű kulcs. A tartalomkulcs a DRM-futásidejű által védett kulcsra van titkosítva a TLS által biztosított átviteli szintű titkosításmellett. Emellett a visszafejtést biztonságos környezetben kezelik az operációs rendszer szintjén, ahol a rosszindulatú felhasználók nehezebben támadnak. A DRM olyan esetekhez ajánlott, ahol a megjelenítő esetleg nem megbízható fél, és a legmagasabb szintű biztonságra van szükség.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hogyan jeleníthet meg egy videót csak azoknak a felhasználóknak, akik rendelkeznek egy adott engedéllyel, az Azure AD használata nélkül?

Nem kell használni a konkrét jogkivonat-szolgáltató (például az Azure AD). Létrehozhat jaaszimmetrikus kulcstitkosítást használó saját [JWT-szolgáltatót](https://jwt.io/) (úgynevezett STS, Secure Token Service). Az egyéni STS-ben az üzleti logika alapján adhat hozzá jogcímeket.

Győződjön meg róla, hogy a kibocsátó, a közönség és a követelések pontosan egyeznek meg a JWT-ben és a ContentKeyPolicyPolicy-ben használt ContentKeyPolicy.Make sure the issuer, audience and claims all match up exactly between what is in JWT and the ContentKeyPolicyRestriction used in ContentKeyPolicy.

További információt [a Tartalom védelme a Media Services dinamikus titkosításával című témakörben](content-protection-overview.md)talál.

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Hogyan és hol kap JWT token használata előtt engedély vagy kulcs?

1. Éles környezetben rendelkeznie kell egy Biztonságos jogkivonat-szolgáltatások (STS) (webszolgáltatás), amely kiadja a JWT-jogkivonatot https-kérelem esetén. A teszteléshez használhatja a **GetTokenAsync** metódusban megadott kódot, amelyet [a Program.cs.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)
2. A játékosnak a felhasználó hitelesítése után kérelmet kell benyújtania az STS-hez egy ilyen tokenért, és hozzá kell rendelnie azt a token értékeként. Használhatja az [Azure Media Player API-t.](https://amp.azure.net/libs/amp/latest/docs/)

* Az STS futtatásának például szimmetrikus és aszimmetrikus kulccsal, lásd a . [https://aka.ms/jwt](https://aka.ms/jwt) 
* Egy példa egy játékos alapján az Azure Media Player ilyen [https://aka.ms/amtest](https://aka.ms/amtest) JWT-jogkivonatot, lásd: (bontsa ki a "player_settings" hivatkozást a token bemeneti megtekintéséhez).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hogyan engedélyezi az AES titkosítással rendelkező videók streamelésére vonatkozó kérelmeket?

A helyes megközelítés az STS (Secure Token Service) kihasználása:

Az STS-ben a felhasználói profiltól függően különböző jogcímeket adhat hozzá (például "Prémium felhasználó", "Alapfelhasználó", "Ingyenes próbafelhasználó"). A JWT különböző jogcímekkel a felhasználó különböző tartalmakat láthat. Természetesen a különböző tartalom/eszköz esetében a ContentKeyPolicyRestriction a megfelelő RequiredClaims-címeket fogja megadni.

Az Azure Media Services API-k használatával konfigurálhatja a licenc-/kulcskézbesítést és titkosíthatja az eszközöket (amint az ebben a [példában](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)látható).

További információkért lásd:

- [Tartalomvédelem – áttekintés](content-protection-overview.md)
- [Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP vagy HTTPS?
A ASP.NET MVC lejátszó alkalmazásnak a következőket kell támogatnia:

* Felhasználói hitelesítés az Azure AD-n keresztül, amely HTTPS alatt van.
* JWT csere az ügyfél és az Azure AD, amely HTTPS alatt.
* DRM-licencbeszerzés az ügyfél által, amelynek HTTPS alatt kell lennie, ha a Media Services biztosítja a licenckézbesítést. A PlayReady termékcsomag nem írja elő a HTTPS-t a licenc kézbesítéséhez. Ha a PlayReady licenckiszolgálója a Media Services szolgáltatáson kívül található, http vagy HTTPS protokollt is használhat.

A ASP.NET lejátszó alkalmazás https protokollt használ a legjobb gyakorlatként, így a Media Player https alatt található. A HTTP azonban előnyben részesítésre szolgál a streameléshez, ezért figyelembe kell vennie a vegyes tartalom kérdését.

* A böngésző nem engedélyezi a vegyes tartalmat. De plug-inek, mint a Silverlight és az OSMF plug-in a sima és DASH nem teszik lehetővé. A vegyes tartalom biztonsági szempont, mivel fennáll a veszélye a rosszindulatú JavaScript-írás befecskendezésének, ami az ügyféladatok veszélyének teheti ki. A böngészők alapértelmezés szerint blokkolják ezt a funkciót. Az egyetlen módja annak, hogy a munka körül van a szerver (forrás) oldalon, amely lehetővé teszi az összes domain (függetlenül a TTÓL, HOGY HTTPS vagy HTTP). Ez valószínűleg nem is jó ötlet.
* Kerülje a vegyes tartalmat. Mind a lejátszóalkalmazásnak, mind a Media Playernek HTTP-t vagy HTTPS-t kell használnia. Vegyes tartalom lejátszásakor a silverlightSS tech-nek törölnie kell a vegyes tartalomra vonatkozó figyelmeztetést. A flashSS tech kezeli a vegyes tartalom nélkül vegyes tartalom figyelmeztetés.
* Ha a streamelési végpont 2014 augusztusa előtt jött létre, nem támogatja a HTTPS-t. Ebben az esetben hozzon létre és használjon egy új streamelési végpontot https.In this case, create and use a new stream endpoint for HTTPS.

### <a name="what-about-live-streaming"></a>Mi a helyzet az élő közvetítés?

Pontosan ugyanazt a kialakítást és megvalósítást használhatja az élő közvetítés védelmére a Media Services szolgáltatásban, ha a programhoz társított eszközt VOD-eszközként kezeli. Az élő tartalom több DRM-védelemének biztosításához alkalmazza ugyanazt a beállítást/feldolgozást az eszközre, mintha VOD-eszköz lenne, mielőtt az eszközt az élő kimenethez társítana.

### <a name="what-about-license-servers-outside-media-services"></a>Mi a helyzet a Media Servicesen kívüli licenckiszolgálókkal?

Az ügyfelek gyakran saját adatközpontjukban vagy drm-szolgáltatók által üzemeltetett licenckiszolgáló-farmba fektettek be. A Media Services tartalomvédelemmel hibrid módban is működhet. A tartalom üzemeltethető és dinamikusan védhető a Media Services szolgáltatásban, míg a DRM-licenceket a Media Services szolgáltatáson kívüli kiszolgálók szállítják. Ebben az esetben vegye figyelembe a következő módosításokat:

* Az STS-nek elfogadható és a licenckiszolgáló-farm által ellenőrizhető jogkivonatokat kell kiadnia. Például az Axinom által biztosított Widevine licenckiszolgálókhoz egy jogosultsági üzenetet tartalmazó adott JWT szükséges. Ezért egy Ilyen JWT kibocsátásához sts-re van szükség. 
* A továbbiakban nem kell konfigurálnia a licenckézbesítési szolgáltatást a Media Services szolgáltatásban. A ContentKeyPolicies konfigurálásakor meg kell adnia a licencbeszerzési URL-címeket (PlayReady, Widevine és FairPlay esetén).

> [!NOTE]
> A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Használhatom az Azure Portalon a v3-as erőforrások kezeléséhez?

Jelenleg használhatja az [Azure Portalon:](https://portal.azure.com/)

* Media Services v3 [élő események](live-events-outputs-concept.md)kezelése, 
* nézet (nem kezeli) v3 [Eszközök,](assets-concept.md) 
* [az API-k elérésével kapcsolatos információk.](access-api-portal.md) 

Minden más felügyeleti feladathoz (például [átalakítások és feladatok](transforms-jobs-concept.md) és [tartalomvédelem)](content-protection-overview.md)használja a [REST API-t,](https://docs.microsoft.com/rest/api/media/)a [CLI-t](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK-k egyikét.](media-services-apis-overview.md#sdks)

### <a name="is-there-an-assetfile-concept-in-v3"></a>Van AssetFile koncepció a v3-ban?

Az AssetFiles eltávolításra került az AMS API-ból, hogy elválassza a Media Services-t a Storage SDK-függőségtől. Most a Storage, nem pedig a Media Services tartja a Storage-ban lévő adatokat. 

További információt az [Áttelepítés a Media Services 3-as v3-as című témakörben](media-services-v2-vs-v3.md)talál.

### <a name="where-did-client-side-storage-encryption-go"></a>Hová került az ügyféloldali tárolótitkosítás?

Most már ajánlott a kiszolgálóoldali tárolótitkosítás használata (amely alapértelmezés szerint be van kapcsolva). További információ: [Azure Storage Service Encryption for Data at In.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

## <a name="offline-streaming"></a>Offline adatfolyam

### <a name="fairplay-streaming-for-ios"></a>FairPlay streaming iOS-hez

A következő gyakori kérdések segítséget nyújtanak az offline FairPlay streamelési hibaelhárításhoz iOS rendszeren:

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Miért csak a hang játszható le, de a videó offline módban nem?

Ez a viselkedés úgy tűnik, hogy a mintaalkalmazás kialakítása. Ha offline módban egy másik hangsáv is jelen van (ez a HLS esetében is előfordul), akkor az iOS 10 és az iOS 11 alapértelmezés szerint az alternatív hangsávra van. Az FPS offline mód dalának kompenzálása érdekében távolítsa el az alternatív hangsávot az adatfolyamból. Ehhez a Media Services, adja hozzá a dinamikus jegyzékfájl szűrő "audio-only=false." Más szóval a HLS URL-címe .ism/manifest(format=m3u8-aapl,audio-only=false) végződik. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Miért csak videó nélkül játssza le a hangot offline módban, miután hozzáadtam a csak hang=hamis hangot?

A tartalomkézbesítési hálózat (CDN) gyorsítótárkulcs-kialakításától függően előfordulhat, hogy a tartalom gyorsítótárba kerül. A gyorsítótár kiürítése.

#### <a name="is-fps-offline-mode-also-supported-on-ios-11-in-addition-to-ios-10"></a>Az FPS offline mód az iOS 11-en is támogatott az iOS 10 mellett?

Igen. Az FPS offline mód támogatott az iOS 10 és az iOS 11 rendszerben.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Miért nem találom az "Offline lejátszás FairPlay streameléssel és HTTP Live Streaming tel" dokumentumot az FPS Server SDK-ban?

Az FPS Server SDK 4-es verziója óta ez a dokumentum beolvadt a "FairPlay Streaming Programming Guide"-ba.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Mi a letöltött/offline fájlstruktúra az iOS-eszközökön?

Az iOS-eszközön letöltött fájlstruktúra a következő képernyőképhez hasonlóan néz ki. A `_keys` mappa tárolja a letöltött FPS-licenceket, minden licencszolgáltatás-állomáshoz egy tárolófájltartozik. A `.movpkg` mappa hang- és videotartalmat tárol. Az első olyan mappa, amelynek neve kötőjellel végződik, majd numerikus, videotartalmat tartalmaz. A numerikus érték a videointerpretációk PeakBandwidth értéke. A második mappa, amelynek neve kötőjellel végződik, majd 0-val, hangtartalmat tartalmaz. A harmadik mappa neve "Adatok" tartalmazza a fő lejátszási listát az FPS tartalmat. Végül a boot.xml fájl teljes `.movpkg` körű leírást ad a mappa tartalmáról. 

![Offline FairPlay iOS mintaalkalmazásfájl-struktúra](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Mintaboot.xml fájl:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Widevine streaming Androidra

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-key"></a>Hogyan szállíthatok állandó licenceket (offline módban) egyes ügyfelek/felhasználók és nem állandó licencek (offline-letiltva) mások számára? Meg kell kettőznem a tartalmat, és külön tartalomkulcsot kell használnom?

Mivel a Media Services v3 lehetővé teszi, hogy egy eszköz több StreamingLocators. Lehet, hogy

* One ContentKeyPolicy with license_type = "persistent", ContentKeyPolicyRestriction az "állandó" jogcímekkel és a StreamingLocator;
* Egy másik ContentKeyPolicy license_type="nonpersistent", ContentKeyPolicyRestriction a "nonpersistent" jogcímekkel, és a StreamingLocator.
* A két StreamingLocators különböző ContentKey rendelkezik.

Az egyéni STS üzleti logikájától függően a JWT-jogkivonat ban különböző jogcímek kerülnek kiadásra. A jogkivonattal csak a megfelelő licenc szerezhető be, és csak a megfelelő URL-cím játszható le.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Mi a Widevine és a Media Services DRM biztonsági szintjei közötti leképezés?

A Google "Widevine DRM Architecture Overview" című szolgáltatása három különböző biztonsági szintet határoz meg. Azonban az [Azure Media Services dokumentációjában a Widevine licencsablonöt](widevine-license-template-overview.md)ismertetett öt különböző biztonsági szint van körvonalazva. Ez a szakasz bemutatja, hogyan térképezik le a biztonsági szinteket.

A Google "Widevine DRM Architecture Review" dokumentuma a következő három biztonsági szintet határozza meg:

* 1. biztonsági szint: Minden tartalomfeldolgozás, kriptográfia és vezérlő a megbízható végrehajtási környezetben (TEE) történik. Egyes implementációs modellekben a biztonsági feldolgozás különböző chipekben végezhető.
* 2. biztonsági szint: Titkosítást hajt végre (de nem videofeldolgozást) a TEE-n belül: a visszafejtett pufferek visszakerülnek az alkalmazástartományba, és külön videohardveren vagy -szoftveren keresztül kerülnek feldolgozásra. szinten azonban a kriptográfiai információk feldolgozása még mindig csak a TEE-n belül történik.
* A 3-as biztonsági szint nem rendelkezik TEE-vel az eszközön. Megfelelő intézkedéseket lehet tenni a gazdaoperációs rendszeren található kriptográfiai információk és visszafejtett tartalom védelmére. A Level 3 implementáció tartalmazhat hardveres kriptográfiai motort is, de ez csak növeli a teljesítményt, a biztonságot nem.

Ugyanakkor a [Widevine licencsablon Azure Media Services dokumentációjában](widevine-license-template-overview.md)a content_key_specs security_level tulajdonsága a következő öt különböző értékkel rendelkezhet (a lejátszáshoz szükséges ügyfélrobusztussági követelmények):

* Szoftveralapú fehér doboz osszes titkosítás szükséges.
* Szoftver es és egy homályos dekóder szükséges.
* A kulcsanyag- és titkosítási műveleteket egy hardveres pólón belül kell végrehajtani.
* A tartalom titkosítását és dekódolását egy hardveres pólón belül kell elvégezni.
* A titkosítást, a dekódolást és az adathordozók (tömörített és tömörítetlen) minden kezelését egy hardveres TEE-n belül kell kezelni.

Mindkét biztonsági szintet a Google Widevine határozza meg. A különbség a használati szint: architektúra szint vagy API-szint. Az öt biztonsági szint a Widevine API-ban használatos. A content_key_specs objektum, amely security_level tartalmazza, deszerializált, és átadta a Widevine globális kézbesítési szolgáltatás az Azure Media Services Widevine licencszolgáltatás. Az alábbi táblázat a két biztonsági szint csoport közötti leképezést mutatja.

| **A Widevine architektúrában meghatározott biztonsági szintek** |**A Widevine API-ban használt biztonsági szintek**|
|---|---| 
| **1. biztonsági szint:** Minden tartalomfeldolgozás, kriptográfia és vezérlés a megbízható végrehajtási környezetben (TEE) történik. Egyes implementációs modellekben a biztonsági feldolgozás különböző chipekben végezhető.|**security_level=5**: Az adathordozók titkosítását, dekódolását és minden kezelését (tömörített és tömörítetlen) egy hardverrel támogatott TEE-n belül kell kezelni.<br/><br/>**security_level=4**: A tartalom titkosítását és dekódolását egy hardveres PÓLÓ-alapú területen kell elvégezni.|
**2. biztonsági szint:** Titkosítást végez (de nem videofeldolgozást) a TEE-n belül: a visszafejtett pufferek visszakerülnek az alkalmazástartományba, és külön videohardveren vagy -szoftveren keresztül kerülnek feldolgozásra. szinten azonban a kriptográfiai információk feldolgozása még mindig csak a TEE-n belül történik.| **security_level=3**: A kulcsanyag- és titkosítási műveleteket egy hardveres PÓLÓ-n belül kell végrehajtani. |
| **3. biztonsági szint:** Nincs TEE a készüléken. Megfelelő intézkedéseket lehet tenni a gazdaoperációs rendszeren található kriptográfiai információk és visszafejtett tartalom védelmére. A Level 3 implementáció tartalmazhat hardveres kriptográfiai motort is, de ez csak növeli a teljesítményt, a biztonságot nem. | **security_level=2**: Szoftveres kriptográfia és egy elbúzított dekóder szükséges.<br/><br/>**security_level=1**: Szoftveralapú fehérdobozos titkosítás szükséges.|

#### <a name="why-does-content-download-take-so-long"></a>Miért tart ilyen sokáig a tartalom letöltése?

A letöltési sebesség kétféleképpen javítható:

* Engedélyezze a CDN-t, hogy a végfelhasználók nagyobb valószínűséggel nyomják meg a CDN-t az origin/streaming végpont helyett a tartalom letöltéséhez. Ha a felhasználó eléri a streamelési végpontot, minden HLS-szegmens vagy DASH-töredék dinamikusan van csomagolva és titkosítva. Annak ellenére, hogy ez a késés ezredmásodpercben van minden szegmens/töredék esetében, ha egy órás videóval rendelkezik, a felhalmozott késés nagy lehet, ami hosszabb letöltést okozhat.
* Adja meg a végfelhasználóknak azt a lehetőséget, hogy az összes tartalom helyett szelektíven töltsék le a videominőségű rétegeket és hangsávokat. Offline módban nincs értelme letölteni az összes minőségi réteget. Ezt kétféleképpen lehet elérni:

   * Ügyfél által vezérelt: vagy a lejátszó alkalmazás automatikusan kiválasztja, vagy a felhasználó kiválasztja a videó minőségű réteget és a letöltandó hangsávokat;
   * Szolgáltatás vezérelt: az Azure Media Services dinamikus jegyzékfájl-szolgáltatásával létrehozhat egy (globális) szűrőt, amely a HLS-lejátszási listát vagy a DASH MPD-t egyetlen videominőségi rétegre és a kiválasztott hangsávokra korlátozza. Ezután a végfelhasználóknak bemutatott letöltési URL-cím tartalmazza ezt a szűrőt.

## <a name="next-steps"></a>További lépések

[A Media Services 3-as v3-as ügyének áttekintése](media-services-overview.md)
