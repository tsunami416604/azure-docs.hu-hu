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
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: 11123ee04dd02a60dff0b88e2e6e85fcd613a7d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068009"
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

## <a name="next-steps"></a>További lépések

[A Media Services 3-as v3-as ügyének áttekintése](media-services-overview.md)
