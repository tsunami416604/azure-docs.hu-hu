---
title: Az Azure Media Services v3-as rendszergyakran feltett kérdései| Microsoft dokumentumok
description: Ez a cikk választ ad az Azure Media Services v3-as ával kapcsolatos gyakori kérdésekre.
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
ms.openlocfilehash: 713acbd098255af2869d7a462c9990f3d7e10bf1
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309191"
---
# <a name="media-services-v3-frequently-asked-questions"></a>A Media Services v3-as kérdése

Ez a cikk választ ad az Azure Media Services v3-as ával kapcsolatos gyakori kérdésekre.

## <a name="general"></a>Általános kérdések

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Milyen Azure-szerepkörök hajthatnak végre műveleteket az Azure Media Services-erőforrásokon? 

Lásd: [Szerepköralapú hozzáférés-vezérlés (RBAC) a Media Services-fiókokhoz.](rbac-overview.md)

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Hogyan streamelhetek Apple iOS-eszközökre?

Győződjön meg arról, hogy **van (format=m3u8-aapl)** az elérési út végén (az URL **/manifest** része után), hogy a streamelési forráskiszolgáló http-élő streamelési (HLS) tartalmat adjon vissza az Apple iOS natív eszközökön való felhasználásra. További információt a [Tartalom kézbesítése (Kézbesítés) (Tartalom kézbesítése) (Tartalom kézbesítése) (Tartalom kézbesít](dynamic-packaging-overview.md)

### <a name="how-do-i-configure-media-reserved-units"></a>Hogyan konfigurálhatom a Media által lefoglalt egységeket?

A Media Services v3 vagy Video Indexer által indított hangelemzési és videoelemzési feladatok esetében azt javasoljuk, hogy 10 S3 media szolgáltatás számára fenntartott egységgel (MRUs) építse ki fiókját. Ha 10-nél több S3 MRUs-ra van szüksége, nyisson meg egy támogatási jegyet az [Azure Portalon](https://portal.azure.com/)keresztül.

További információt az [Adathordozó-feldolgozás méretezése](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Mi az ajánlott módszer a videók feldolgozására?

[Az Átalakítások](https://docs.microsoft.com/rest/api/media/transforms) segítségével konfigurálhatja a videók kódolására vagy elemzésére vonatkozó gyakori feladatokat. Minden átalakítás egy receptet vagy a video- vagy hangfájlok feldolgozására szolgáló feladatok munkafolyamatát írja le. A [feladat](https://docs.microsoft.com/rest/api/media/jobs) az a tényleges kérés a Media Services-hez, hogy az Átalakítás tanusító videóra vagy hangtartalomra alkalmazza az átalakítást. Az átalakítás létrehozása után a feladatokat a Media Services API-k vagy a közzétett SDK-k bármelyikének használatával küldheti el. További információt az [átalakításokkal és feladatokkal](transforms-jobs-concept.md) kapcsolatos cikkben olvashat.

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Feltöltöttem, kódoltam és közzétettem egy videót. Miért nem játszható le a videó, amikor megpróbálom streamelni?

Az egyik leggyakoribb oka az, hogy nem rendelkezik a streamelési végpont, ahonnan próbál lejátszani a futó állapotban.

### <a name="how-does-pagination-work"></a>Hogyan működik a tördelés?

A tördelés használatakor mindig a következő hivatkozást használja a gyűjtemény számbavételéhez, és ne függjön egy adott oldalmérettől. További részletek és példák: [Szűrés, rendezés, lapozás.](entities-overview.md)

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Milyen funkciók még nem érhetők el az Azure Media Services v3-as verzióban?

További információt a [Funkcióhiányosságok a v2 API-kkal kapcsolatban című témakörben talál.](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis)

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Mi a Media Services-fiók előfizetések közötti áthelyezésének folyamata?  

További információt a [Media Services-fiók áthelyezése előfizetések között](media-services-account-concept.md).

## <a name="live-streaming"></a>Live streaming (Élő adatfolyam) 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>Hogyan állíthatom le az élő közvetítést az adás befejezése után?

Az ügyfél- vagy a kiszolgálóoldalról is megközelítheti.

#### <a name="client-side"></a>Ügyféloldal

A webalkalmazásnak meg kell kérnie a felhasználót, ha be szeretné zárni az adást, miközben bezárja a böngészőt. Ez egy olyan böngészőesemény, amelyet a webalkalmazás kezelni tud.

#### <a name="server-side"></a>Szerver oldal

Az Azure Event Grid-eseményekre való feliratkozással figyelheti az élő eseményeket. További információt az [EventGrid eseménysémájában](media-services-event-schemas.md#live-event-types)talál.

A következő lehetőségek közül választhat:

* [Iratkozzon fel](reacting-to-media-services-events.md) a stream-szintű [Microsoft.Media.LiveEventEncoderLeed eseményekre,](media-services-event-schemas.md#liveeventencoderdisconnected) és figyelje, hogy egy ideig nem jönnek létre újracsatlakozások az élő esemény leállításához és törléséhez.
* [Iratkozzon fel](reacting-to-media-services-events.md) a pályaszintű [szívveréses](media-services-event-schemas.md#liveeventingestheartbeat) eseményekre. Ha az összes szám bejövő bitráta 0-ra csökken, vagy az utolsó időbélyegző már nem növekszik, biztonságosan leállíthatja az élő eseményt. A szívveréses események minden 20 másodpercben érkeznek minden számhoz, így lehet, hogy egy kicsit részletes.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Hogyan szúrhatok be szüneteket/videókat és képeket élő közvetítés közben?

A Media Services v3 élő kódolása még nem támogatja a video- vagy képlapképek beillesztését az élő közvetítés során. 

A forrásvideó közötti váltáshoz használhatja a [helyszíni kódolót.](recommended-on-premises-live-encoders.md) Számos alkalmazás biztosítja a források közötti váltást, beleértve a Telestream Wirecast, a Switcher Studio (iOS rendszeren) és az OBS Studio (ingyenes alkalmazás).

## <a name="content-protection"></a>Tartalomvédelem

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Használjam az AES-128 titkosítása nélkül történik, vagy drm-rendszert használjak?

Az ügyfelek gyakran csodálkoznak, hogy AES titkosítást vagy DRM-rendszert kell-e használniuk. A fő különbség a két rendszer között az, hogy az AES titkosítással a tartalomkulcs továbbításra kerül az ügyfélnek TLS-en keresztül, így a kulcs átvitel közben titkosítva van, de további titkosítás nélkül ("a tiszta"). Ennek eredményeképpen a tartalom visszafejtéséhez használt kulcs elérhető az ügyféllejátszó számára, és az ügyfél hálózati nyomkövetésében egyszerű szövegként tekinthető meg. Az AES-128 egyértelmű kulcstitkosítás olyan esetekben használható, amikor a megtekintő megbízható fél (például a vállalaton belül terjesztett vállalati videók titkosítása, amelyeket az alkalmazottak megtekintenek).

A DRM-rendszerek, például a PlayReady, a Widevine és a FairPlay egyaránt további titkosítási szintet biztosítanak a tartalom visszafejtéséhez használt kulcson, szemben az AES-128 egyértelmű kulcsokkal. A tartalomkulcs a DrM-futásidejű által védett kulcsra van titkosítva a TLS által biztosított átviteli szintű titkosítás mellett. Emellett a visszafejtést biztonságos környezetben kezelik az operációs rendszer szintjén, ahol a rosszindulatú felhasználók nehezebben támadnak. A DRM-et olyan használati esetekhez javasoljuk, ahol a megjelenítő esetleg nem megbízható fél, és a legmagasabb szintű biztonságra van szüksége.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hogyan jeleníthetek meg egy videót csak azoknak a felhasználóknak, akik rendelkeznek egy adott engedéllyel, az Azure AD használata nélkül?

Nem kell használni a konkrét jogkivonat-szolgáltató, például az Azure Active Directory (Azure AD). Létrehozhat saját [JWT-szolgáltatót](https://jwt.io/) (úgynevezett Secure Token Service-t vagy STS-t) aszimmetrikus kulcstitkosítás használatával. Az egyéni STS-ben az üzleti logika alapján adhat hozzá jogcímeket.

Győződjön meg arról, hogy a kibocsátó, a közönség és a követelések `ContentKeyPolicyRestriction` pontosan `ContentKeyPolicy`megegyeznek a JWT-ben lévő és a alkalmazásban használt érték között.

További információt [a Tartalom védelme a Media Services dinamikus titkosításával című témakörben](content-protection-overview.md)talál.

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Hogyan és hol kaptam egy JWT token használata előtt, hogy kérjen engedélyt vagy kulcsot?

Éles környezetben, rendelkeznie kell secure token service (azaz egy webszolgáltatás), amely egy JWT-jogkivonatot ad ki egy HTTPS-kérelem esetén. A teszteléshez használhatja a `GetTokenAsync` [Program.cs.](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)

A játékos a felhasználó hitelesítése után kéri az STS-t egy ilyen tokenért, és hozzárendeli a token értékeként. Használhatja az [Azure Media Player API-t.](https://amp.azure.net/libs/amp/latest/docs/)

Az STS szimmetrikus kulccsal vagy aszimmetrikus kulccsal való futtatására vonatkozó példát a [JWT eszköz](https://aka.ms/jwt)ben láthatja. Egy példa egy ilyen JWT-jogkivonatot használó Azure Media Player-lejátszóra, tekintse meg az [Azure media test tool](https://aka.ms/amtest). (Bontsa ki a **player_settings** hivatkozást a tokenbemenet megtekintéséhez.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hogyan engedélyezhetem az AES titkosítással rendelkező videók streamelésére vonatkozó kérelmeket?

A helyes megközelítés a Secure Token Service használata. Az STS-ben a felhasználói profiltól függően különböző jogcímeket adhat hozzá (például "Prémium felhasználó", "Alapfelhasználó", "Ingyenes próbafelhasználó"). A JWT különböző jogcímekkel a felhasználó különböző tartalmakat láthat. A különböző tartalmak `ContentKeyPolicyRestriction` vagy eszközök `RequiredClaims` esetében a megfelelő értékkel rendelkezik.

Az Azure Media Services API-k használatával konfigurálhatja a licenc-/kulcskézbesítést és titkosíthatja az eszközöket (amint az ebben a [példában](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)látható).

További információkért lásd:

- [Tartalomvédelem – áttekintés](content-protection-overview.md)
- [Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Http-t vagy HTTPS-t használjak?
A ASP.NET MVC lejátszó alkalmazásnak a következőket kell támogatnia:

* Felhasználói hitelesítés az Azure AD-n keresztül, amely HTTPS alatt van.
* JWT csere az ügyfél és az Azure AD, amely HTTPS alatt.
* DRM-licencbeszerzés az ügyfél által, amelynek HTTPS alatt kell lennie, ha a Media Services biztosítja a licenckézbesítést. A PlayReady termékcsomag nem írja elő a HTTPS-t a licenc kézbesítéséhez. Ha a PlayReady licenckiszolgálója a Media Services szolgáltatáson kívül található, http vagy HTTPS protokollt is használhat.

A ASP.NET lejátszó alkalmazás https protokollt használ a legjobb gyakorlatként, így a Media Player https alatt található. A http-t azonban előnyben részesítik a streameléshez, ezért figyelembe kell vennie ezeket a problémákat a vegyes tartalommal:

* A böngésző nem engedélyezi a vegyes tartalmat. De plug-inek, mint a Silverlight és az OSMF plug-in a Smooth és a DASH nem teszik lehetővé. A vegyes tartalom biztonsági szempont, mivel fennáll a veszélye a rosszindulatú JavaScript-írás befecskendezésének, ami veszélybe sodorhatja az ügyféladatokat. A böngészők alapértelmezés szerint blokkolják ezt a funkciót. Az egyetlen módja annak, hogy a munka körül van a szerver (forrás) oldalon, amely lehetővé teszi az összes domain (függetlenül a TTÓL, HOGY HTTPS vagy HTTP). Ez valószínűleg nem is jó ötlet.
* Kerülje a vegyes tartalmat. Mind a lejátszóalkalmazásnak, mind a Media Playernek HTTP-t vagy HTTPS-t kell használnia. Vegyes tartalom lejátszásakor a SilverlightSS technológiának törölnie kell a vegyes tartalomra vonatkozó figyelmeztetést. A FlashSS tech kezeli a vegyes tartalom nélkül vegyes tartalom figyelmeztetés.
* Ha a streamelési végpont 2014 augusztusa előtt jött létre, nem támogatja a HTTPS-t. Ebben az esetben hozzon létre és használjon egy új streamelési végpontot https.In this case, create and use a new stream endpoint for HTTPS.

### <a name="what-about-live-streaming"></a>Mi a helyzet az élő közvetítés?

Pontosan ugyanazt a kialakítást és megvalósítást használhatja az élő streamelés védelmére a Media Services szolgáltatásban, ha a programhoz társított eszközt VOD-eszközként kezeli. Az élő tartalom több DRM-védelemének biztosításához alkalmazza ugyanazt a beállítást/feldolgozást az eszközre, mintha VOD-eszköz lenne, mielőtt az eszközt az élő kimenethez társítana.

### <a name="what-about-license-servers-outside-media-services"></a>Mi a helyzet a Media Servicesen kívüli licenckiszolgálókkal?

Az ügyfelek gyakran fektettek be egy licenckiszolgáló-farmba a saját adatközpontjukban vagy a DRM-szolgáltatók által üzemeltetett szolgáltatóban. A Media Services tartalomvédelemmel hibrid módban is működhet. A media services szolgáltatásban a tartalom üzemeltethető és dinamikusan védhető, míg a DRM-licenceket a Media Services szolgáltatáson kívüli kiszolgálók szállítják. Ebben az esetben vegye figyelembe a következő módosításokat:

* Az STS-nek elfogadható és a licenckiszolgáló-farm által ellenőrizhető jogkivonatokat kell kiadnia. Például az Axinom által biztosított Widevine licenckiszolgálókhoz egy jogosultsági üzenetet tartalmazó adott JWT szükséges. Be kell, hogy egy STS kiadni egy ilyen JWT. 
* A továbbiakban nem kell konfigurálnia a licenckézbesítési szolgáltatást a Media Services szolgáltatásban. A licencbeszerzési URL-címeket (PlayReady, Widevine és FairPlay `ContentKeyPolicy`esetén) kell megadnia a konfiguráláskor.

> [!NOTE]
> A Widevine a Google által nyújtott szolgáltatás, amelyre a Google szolgáltatási feltételei és adatvédelmi irányelvei vonatkoznak.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs. v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Használhatom az Azure Portalon a v3-as erőforrások kezeléséhez?

Jelenleg használhatja az [Azure Portalon:](https://portal.azure.com/)

* [Élő események](live-events-outputs-concept.md) kezelése a Media Services 3-as verzióját. 
* Tekintse meg (nem kezeli) a [v3-as eszközöket.](assets-concept.md) 
* [Az API-k elérésével kapcsolatos információk.](access-api-portal.md) 

Az összes többi felügyeleti feladathoz (például [átalakítások és feladatok](transforms-jobs-concept.md) és [tartalomvédelem)](content-protection-overview.md)használja a [REST API-t,](https://docs.microsoft.com/rest/api/media/)az [Azure CLI-t](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK-k egyikét.](media-services-apis-overview.md#sdks)

### <a name="is-there-an-assetfile-concept-in-v3"></a>Van AssetFile koncepció a v3-ban?

A `AssetFile` koncepció t lett távolítva a Media Services API-ból a Media Services és a Storage SDK-függőség elkülönítéséhez. Most az Azure Storage, nem a Media Services, a storage SDK-hoz tartozó információkat tartja. 

További információt az [Áttelepítés a Media Services 3-as v3-as című témakörben](media-services-v2-vs-v3.md)talál.

### <a name="where-did-client-side-storage-encryption-go"></a>Hová került az ügyféloldali tárolótitkosítás?

Most azt javasoljuk, hogy kiszolgálóoldali tárolótitkosítást használjon (amely alapértelmezés szerint be van kapcsolva). További információ: [Azure Storage Service Encryption for data on in.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

## <a name="offline-streaming"></a>Offline adatfolyam

### <a name="fairplay-streaming-for-ios"></a>FairPlay streaming iOS-hez

Az alábbi gyakori kérdések segítséget nyújtanak az offline FairPlay streamelési hibaelhárításhoz iOS rendszeren.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Miért csak a hang játszható le, de a videó offline módban nem?

Ez a viselkedés úgy tűnik, hogy a mintaalkalmazás kialakítása. Ha offline módban egy másik hangsáv is jelen van (ez a HLS esetében is előfordul), akkor az iOS 10 és az iOS 11 alapértelmezés szerint az alternatív hangsávra van. Az FPS offline mód dalának kompenzálása érdekében távolítsa el az alternatív hangsávot az adatfolyamból. Ehhez a Media Services, adja hozzá a dinamikus jegyzékfájl **szűrő csak audio=false**. Más szóval a HLS URL-címe **.ism/manifest(format=m3u8-aapl,audio-only=false)** végződik. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Miért csak videó nélkül játssza le a hangot offline módban, miután hozzáadtam a csak hang=hamis hangot?

A tartalomkézbesítési hálózat gyorsítótárkulcs-kialakításától függően előfordulhat, hogy a tartalom gyorsítótárba kerül. A gyorsítótár kiürítése.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Az IOS 10 mellett az iOS 11-en is támogatott az FPS offline mód?

Igen. Az FPS offline mód támogatott az iOS 10 és az iOS 11 rendszerben.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Miért nem találom az "Offline lejátszás FairPlay streameléssel és HTTP Live Streaming tel" dokumentumot az FPS Server SDK-ban?

Az FPS Server SDK 4-es verziója óta ez a dokumentum beolvadt a "FairPlay Streaming Programming Guide"-ba.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Mi a letöltött/offline fájlstruktúra az iOS-eszközökön?

Az iOS-eszközön letöltött fájlstruktúra a következő képernyőképhez hasonlóan néz ki. A `_keys` mappa tárolja a letöltött FPS-licenceket, minden licencszolgáltatás-állomáshoz egy tárolófájltartozik. A `.movpkg` mappa hang- és videotartalmat tárol. 

Az első olyan mappa, amelynek neve kötőjellel végződik, majd egy szám, videotartalmat tartalmaz. A numerikus érték a videointerpretációk maximális sávszélessége. A második mappa, amelynek neve kötőjellel végződik, majd 0-val, hangtartalmat tartalmaz. A harmadik `Data` nevű mappa tartalmazza az FPS-tartalom fő lejátszási listáját. Végül a boot.xml fájl teljes `.movpkg` körű leírást ad a mappa tartalmáról. 

![Kapcsolat nélküli fájlstruktúra a FairPlay iOS mintaalkalmazáshoz](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Íme egy boot.xml mintafájl:

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

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Hogyan szállíthatok állandó licenceket (offline engedélyezve) egyes ügyfelek/felhasználók és nem állandó licencek (offline letiltva) mások számára? Meg kell kettőznem a tartalmat, és külön tartalomkulcsokat kell használnom?

Mivel a Media Services v3 `StreamingLocator` lehetővé teszi, hogy egy eszköz több példányt, akkor a következők:

* Egy `ContentKeyPolicy` `license_type = "persistent"`például `ContentKeyPolicyRestriction` a `"persistent"`, a `StreamingLocator`követeléssel és a.
* Egy `ContentKeyPolicy` másik `license_type="nonpersistent"` `ContentKeyPolicyRestriction` esetben a `"nonpersistent`, a `StreamingLocator`követelés ", és annak .
* Két `StreamingLocator` példány, amelyek `ContentKey` különböző értékeket tartalmaznak.

Az egyéni STS üzleti logikájától függően a JWT-jogkivonat ban különböző jogcímek kerülnek kiadásra. A jogkivonattal csak a megfelelő licenc szerezhető be, és csak a megfelelő URL-cím játszható le.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Mi a Widevine és a Media Services DRM biztonsági szintjei közötti leképezés?

A Google "Widevine DRM Architecture Overview" című szolgáltatása három biztonsági szintet határoz meg. A [Widevine licencsablonazure Media Services-dokumentációja](widevine-license-template-overview.md) azonban öt biztonsági szintet vázol fel (a lejátszáshoz szükséges ügyfélrobusztussági követelmények). Ez a szakasz bemutatja, hogyan térképezik le a biztonsági szinteket.

A biztonsági szintek mindkét csoportját a Google Widevine határozza meg. A különbség a használati szint: architektúra vagy API. Az öt biztonsági szint a Widevine API-ban használatos. Az `content_key_specs` objektum, `security_level`amely tartalmazza , deszerializált, és átadta a Widevine globális kézbesítési szolgáltatás az Azure Media Services Widevine licencszolgáltatás. Az alábbi táblázat a két biztonsági szint csoport közötti leképezést mutatja be.

| **A Widevine architektúrában meghatározott biztonsági szintek** |**A Widevine API-ban használt biztonsági szintek**|
|---|---| 
| **1. biztonsági szint:** Minden tartalomfeldolgozás, kriptográfia és vezérlés a megbízható végrehajtási környezetben (TEE) történik. Egyes implementációs modellekben a biztonsági feldolgozás különböző chipekben hajtható végre.|**security_level=5**: Az adathordozók titkosítását, dekódolását és minden kezelését (tömörített és tömörítetlen) hardveres pólón belül kell kezelni.<br/><br/>**security_level=4**: A tartalom titkosítását és dekódolását hardveres en-alapú TEE-n belül kell elvégezni.|
**2. biztonsági szint:** A titkosítás (de nem videófeldolgozás) a TEE-n belül történik. A visszafejtett pufferek visszakerülnek az alkalmazástartományba, és külön videohardveren vagy szoftveren keresztül kerülnek feldolgozásra. A 2.| **security_level=3**: A kulcsanyag- és titkosítási műveleteket hardveres pólón belül kell végrehajtani. |
| **3-as biztonsági szint:** Nincs TEE a készüléken. Megfelelő intézkedéseket lehet tenni a gazdaoperációs rendszeren található kriptográfiai információk és visszafejtett tartalom védelmére. A 3- as szintű implementáció tartalmazhat egy hardveres kriptográfiai motort is, de ez csak a teljesítményt növeli, a biztonságot nem. | **security_level=2**: Szoftveres kriptográfia és egy elbúzított dekóder szükséges.<br/><br/>**security_level=1**: Szoftveralapú fehérdobozos titkosítás szükséges.|

#### <a name="why-does-content-download-take-so-long"></a>Miért tart ilyen sokáig a tartalom letöltése?

A letöltési sebesség kétféleképpen javítható:

* Engedélyezze a tartalomkézbesítési hálózatot, hogy a felhasználók nagyobb valószínűséggel érjék el azt a tartalomletöltési végpont helyett az eredeti/streamelési végpontot. Ha egy felhasználó egy streamelési végpontot ér el, minden HLS-szegmens vagy DASH-töredék dinamikusan van csomagolva és titkosítva. Annak ellenére, hogy ez a késés ezredmásodpercben van minden szegmensben vagy töredékben, ha egy órás videóval rendelkezik, a felhalmozott késés nagy lehet, és hosszabb letöltést okozhat.
* Adja meg a felhasználóknak a lehetőséget, hogy szelektíven töltse le a videó minőségű rétegek és hangsávok helyett minden tartalmát. Offline módban nincs értelme letölteni az összes minőségi réteget. Ezt kétféleképpen lehet elérni:

  * Ügyfél által vezérelt: A lejátszó alkalmazás automatikusan kiválasztja, vagy a felhasználó kiválasztja a videó minőségi réteget és a letöltandó hangsávokat.
  * Szolgáltatás vezérelte: Az Azure Media Services dinamikus jegyzékfájl szolgáltatásával létrehozhat egy (globális) szűrőt, amely a HLS-lejátszási listát vagy a DASH MPD-t egyetlen videominőségi rétegre és a kiválasztott hangsávokra korlátozza. Ezután a felhasználók számára bemutatott letöltési URL-cím tartalmazza ezt a szűrőt.

## <a name="next-steps"></a>További lépések

[A Media Services 3-as v3-as ügyének áttekintése](media-services-overview.md)
