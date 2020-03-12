---
title: Azure Media Services v3 – gyakori kérdések | Microsoft Docs
description: Ez a cikk a Azure Media Services v3 gyakori kérdéseire ad választ.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: a2619293bf3641cdca370ff528a87ae879460a3b
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086782"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 – gyakori kérdések

Ez a cikk választ ad a Azure Media Services (AMS) v3 gyakori kérdésekre.

## <a name="general"></a>Általános kérdések

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Milyen Azure-szerepkörök végezhetnek műveleteket Azure Media Services erőforrásokon? 

Lásd: [szerepköralapú hozzáférés-vezérlés (RBAC) Media Services fiókokhoz](rbac-overview.md).

### <a name="how-do-you-stream-to-apple-ios-devices"></a>Hogyan történik az Apple iOS-eszközök továbbítása?

Győződjön meg arról, hogy az elérési út végén található "(Format = m3u8-AAPL)" (az URL-cím "/manifest" szakasza után), hogy az adatfolyam-küldő kiszolgáló visszaadja-e a HLS-tartalmat az Apple iOS Native-eszközökön (részletekért lásd: [tartalom kézbesítése](dynamic-packaging-overview.md)).

### <a name="how-do-i-configure-media-reserved-units"></a>Hogyan a Media szolgáltatás számára fenntartott egységek konfigurálása?

A Media Services v3 vagy a Video Indexer által aktivált hangelemzési és videoelemzési feladatok esetében javasolt 10 S3 MRU-val ellátni a fiókot. Ha több mint 10 S3 MRUs van szüksége, nyisson meg egy támogatási jegyet a [Azure Portal](https://portal.azure.com/)használatával.

Részletekért lásd: [a médiafájlok feldolgozásának skálázása a CLI-vel](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Mi az ajánlott módszer a videók feldolgozására?

Az [átalakítások](https://docs.microsoft.com/rest/api/media/transforms) használatával konfigurálhatja a videók kódolására vagy elemzésére szolgáló gyakori feladatokat. Mindegyik **transzformáció** egy receptet vagy egy munkafolyamatot ír le, amely a videó-vagy hangfájlok feldolgozására használható. A [feladatnak](https://docs.microsoft.com/rest/api/media/jobs) az a tényleges kérése, hogy Media Services az **átalakítást** egy adott bemeneti videóra vagy hangtartalomra alkalmazza. Az átalakítás létrehozása után Media Services API-kkal vagy a közzétett SDK-kkal is elküldheti a feladatokat. További információt az [átalakításokkal és feladatokkal](transforms-jobs-concept.md) kapcsolatos cikkben olvashat.

### <a name="i-uploaded-encoded-and-published-a-video-what-would-be-the-reason-the-video-does-not-play-when-i-try-to-stream-it"></a>Feltöltöttem, kódoltam és közzétettem egy videót. Mi lenne az oka, hogy a videó nem játszható le, amikor megpróbálok adatfolyamot továbbítani?

Az egyik leggyakoribb ok az, ha nem rendelkezik a folyamatos átviteli végponttal, amelyről a futó állapotot próbálja lejátszani.

### <a name="how-does-pagination-work"></a>Hogyan működik a tördelés?

A tördelés használatakor mindig a következő hivatkozásra kell használnia a gyűjtemény számbavételéhez, és nem függ egy adott oldalméret méretétől. A részleteket és példákat lásd: [szűrés, rendezés, lapozás](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Milyen funkciók még nem érhetők el a Azure Media Services v3 verzióban?

Részletekért lásd: a [szolgáltatás hiányosságainak a v2 API-k tekintetében](media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Milyen folyamattal kell áthelyezni Media Services fiókot az előfizetések között?  

Részletekért lásd: [Media Services fiók áthelyezése az előfizetések között](media-services-account-concept.md).

## <a name="live-streaming"></a>Live streaming (Élő adatfolyam) 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Szünetek/videók és képbeszúrások beillesztése az élő streambe

Media Services v3 élő kódolás még nem támogatja a videó vagy a képek beszúrását az élő adatfolyamban. 

A forrás videó átváltásához használhat egy [élő helyszíni kódolót](recommended-on-premises-live-encoders.md) . Számos alkalmazás lehetővé teszi a források váltását, beleértve a Wirecast, a Switching Studio (iOS), a OBS Studio (ingyenes alkalmazás) és sok más szolgáltatást.

## <a name="content-protection"></a>Tartalomvédelem

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Használjon AES-128 titkosítatlan titkosítási vagy DRM-rendszer használatát?

Ügyfeleink gyakran wonder, hogy használják inkább a AES-titkosítás vagy a DRM-rendszer. A két rendszer közötti elsődleges különbség az, hogy az AES-titkosítás a tartalmi kulcsot TLS-kapcsolaton keresztül továbbítja az ügyfélnek, így a kulcs továbbítása átvitel alatt áll, de további titkosítás nélkül ("Clear"). Ennek eredményeképpen a tartalom visszafejtéséhez használt kulcs elérhető az ügyfél számára, és az ügyfélen lévő hálózati nyomkövetésben is megtekinthető egyszerű szövegként. Az AES-128 Clear Key encryption olyan használati esetekben alkalmas, ahol a megjelenítő megbízható fél (például a vállalaton belül elosztott vállalati videók titkosítása az alkalmazottak számára).

A DRM-rendszerek (például a PlayReady, a Widevine és a FairPlay) minden további titkosítási szintet biztosítanak a tartalom visszafejtéséhez használt kulcshoz képest egy AES-128 tiszta kulccsal szemben. A tartalmi kulcs titkosítása a DRM-futtatókörnyezet által védett kulcshoz történik, a TLS által biztosított átviteli szintű titkosítás további részében. Ezenkívül a visszafejtési az operációs rendszer szintjén, ahol nehezebb a támadásokkal szemben egy rosszindulatú felhasználó biztonságos környezetben történik. DRM ajánlott használati esetek, ahol a megjelenítő lehetséges, hogy nem egy megbízható entitás, és Ön a legmagasabb szintű biztonságot követelhetnek meg.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hogyan jeleníthető meg a videó csak azokra a felhasználókra, akik rendelkeznek az Azure AD-vel való használattal az adott engedéllyel?

Nem kell semmilyen speciális jogkivonat-szolgáltatót (például az Azure AD-t) használni. A saját [JWT](https://jwt.io/) -szolgáltató (ÚN. STS, biztonságos jogkivonat-szolgáltatás) használatával aszimmetrikus kulcsú titkosítást használhat. Az egyéni STS-ben az üzleti logikája alapján adhat hozzá jogcímeket.

Győződjön meg arról, hogy a kibocsátó, a célközönség és a jogcímek pontosan egyeznek a JWT és a ContentKeyPolicy használt ContentKeyPolicyRestriction között.

További információ: [a tartalom Media Services dinamikus titkosítással való védelemmel](content-protection-overview.md)való ellátása.

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Hogyan és hol érdemes a JWT jogkivonat beszerzése és a kérés licenc- vagy kulcs?

1. Éles környezetben olyan biztonságos jogkivonat-szolgáltatásokkal (STS) kell rendelkeznie, amely JWT-tokent ad a HTTPS-kérések esetén. A teszthez használhatja a [program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)-ben definiált **GetTokenAsync** metódusban megjelenő kódot.
2. Indítson egy, az STS-re, például egy jogkivonatot a felhasználó hitelesítése után, és rendelje hozzá a token értékeként Player kell. Használhatja a [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)-t.

* Ha például az STS-t szimmetrikus és aszimmetrikus kulccsal futtatja, tekintse meg a [https://aka.ms/jwt](https://aka.ms/jwt). 
* Ha az ilyen JWT-tokent használó Azure Media Playerra szeretne példát használni, tekintse meg a [https://aka.ms/amtest](https://aka.ms/amtest) ("player_settings" hivatkozás kibontásával a jogkivonat bemenetének megjelenítéséhez).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hogyan engedélyezi, hogy kérelmeket a Videóknak az AES-titkosítás?

A megfelelő módszer, hogy kihasználja az STS (Secure Token Service):

Az STS-ben a felhasználói profiltól függően adjon hozzá különböző jogcímeket (például a "prémium szintű felhasználó", az "alapszintű felhasználó", az "ingyenes próbaverzió felhasználója"). A különböző jogcímek a jwt-t a felhasználó megtekintheti a különböző tartalmát. Természetesen különböző tartalom eszköz, a ContentKeyPolicyRestriction rendelkezik a megfelelő RequiredClaims.

Azure Media Services API-k használatával konfigurálhatja a licencek/kulcsok küldését és titkosíthatja az adategységeket (ahogy az [ebben a mintában](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)is látható).

További információkért lásd:

- [A tartalomvédelem áttekintése](content-protection-overview.md)
- [Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>A HTTP vagy HTTPS?
Az ASP.NET MVC-lejátszóalkalmazás támogatnia kell a következőket:

* Felhasználó hitelesítése az Azure AD, amely HTTPS alatt található.
* JWT exchange, az ügyfél és a HTTPS alatt álló Azure AD között.
* DRM-licenc beszerzése az ügyfél, amely a HTTPS alá kell tartoznia, ha a Media Services által biztosított licencekkel történő kézbesítés. A PlayReady termékcsomag nem határozza meg, hogy HTTPS licencekkel történő kézbesítés számára. Ha a PlayReady-licenc kiszolgálón kívül a Media Services, HTTP vagy HTTPS is használhatja.

Az ASP.NET-lejátszóalkalmazás a HTTPS, ajánlott eljárásként használja, így a Media Player alatt HTTPS oldalon szerepel. Azonban HTTP, figyelembe kell vennie a probléma a vegyes tartalom nem előnyben részesített streameléshez.

* A böngésző nem engedélyezi a vegyes tartalom. De modulok, a Silverlight beépülő modul OSMF smooth és kötőjel engedélyezhető. A vegyes tartalom egyik biztonsági szempont, a rosszindulatú JavaScript, okozhat a vásárlói adatokat veszélyben új funkció lehetővé teszi a fenyegetés miatt. Böngészők alapértelmezés szerint ez a funkció letiltása. Az egyetlen megoldás módja a kiszolgáló (forrás) oldalán azáltal, hogy minden tartomány (függetlenül a HTTPS vagy HTTP). Ez a valószínűleg nem célszerű vagy.
* A vegyes tartalom elkerülése érdekében. A lejátszóalkalmazás és a Media Player HTTP vagy HTTPS PROTOKOLLT kell használnia. A vegyes tartalom lejátszása, a silverlightSS tech kell rendelkeznie a vegyes tartalom figyelmeztetés törlése. A flashSS tech kezeli a vegyes tartalom vegyes tartalmú figyelmeztetés nélkül.
* Ha a tartalomstreameléshez használt streamvégpont 2014. augusztus előtt jött létre, azt nem támogatja a HTTPS. Ebben az esetben létrehozhat, és egy új streamvégpont használhat HTTPS-hez.

### <a name="what-about-live-streaming"></a>Mi a helyzet élő adások online közvetítése?

Pontosan azonos megtervezését és megvalósítását segítségével megvédheti az élő streamelés a Media Services szolgáltatásban való kezelésével VOD eszközként program társított adategységet. Az élő tartalom több DRM elleni védelmének biztosításához alkalmazza ugyanazt a telepítést/feldolgozást az eszközre, mintha egy VOD-eszköz lenne, mielőtt az eszközt az élő kimenethez társítja.

### <a name="what-about-license-servers-outside-media-services"></a>Mi a helyzet licenckiszolgálókat kívül a Media Services?

Gyakran ügyfelek befektetni licenc kiszolgálófarm vagy a saját adatközpontban vagy egy DRM szolgáltató felhője. A Media Services content protection esetében működhet a hibrid üzemmódot. Tartalom is üzemeltetett és dinamikusan a Media Services szolgáltatásban védett, míg DRM-licencek kívül a Media Services-kiszolgálók által lépnek. Ebben az esetben fontolja meg a következő módosításokat:

* STS kell elfogadható és a licenc kiszolgálófarm által ellenőrzött jogkivonatokat kibocsátani. A Widevine-licenc Axinom által biztosított kiszolgálók például egy adott JWT-jogosultság üzenetet tartalmazó igényelnek. Ezért szüksége lesz az STS szolgáltatással kiadására ilyen jwt-t. 
* Már nincs szüksége a Media Services licenctovábbítási szolgáltatása konfigurálása. Meg kell adnia a licenc licenckérési URL-címek (a PlayReady, Widevine és FairPlay) ContentKeyPolicies konfigurálása során.

> [!NOTE]
> A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Használhatom a Azure Portal a v3-erőforrások kezeléséhez?

Jelenleg a [Azure Portal](https://portal.azure.com/) a következőket használhatja:

* Media Services v3 [élő események](live-events-outputs-concept.md)kezelése 
* v3- [eszközök](assets-concept.md)megtekintése (nem felügyelt) 
* [az API-k elérésére vonatkozó információk beolvasása](access-api-portal.md). 

Az összes többi felügyeleti feladathoz (például [átalakításokhoz, feladatokhoz](transforms-jobs-concept.md) és [tartalmak védelméhez](content-protection-overview.md)) használja a [REST API](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK](media-services-apis-overview.md#sdks)-k egyikét.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Létezik AssetFile-koncepció a v3-as verzióban?

A AssetFiles el lettek távolítva az AMS API-ból, hogy el lehessen különíteni a Media Services a Storage SDK-függőségtől. Most a Storage, nem Media Services, megtartja a tárolóban található adatokat. 

További információ: [migrálás Media Services v3](media-services-v2-vs-v3.md)-ra.

### <a name="where-did-client-side-storage-encryption-go"></a>Hol található az ügyféloldali tároló-titkosítás?

Javasoljuk, hogy használja a kiszolgálóoldali tároló-titkosítást (amely alapértelmezés szerint be van kapcsolva). További információ: [Azure Storage Service encryption for](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)inaktív adatok.

## <a name="next-steps"></a>Következő lépések

[Media Services v3 – áttekintés](media-services-overview.md)
