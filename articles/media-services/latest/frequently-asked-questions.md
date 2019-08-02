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
ms.date: 06/21/2019
ms.author: juliako
ms.openlocfilehash: 28b9c8f343437c20e277d2f3ba53767afa45a5c2
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501260"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 – gyakori kérdések

Ez a cikk választ ad a Azure Media Services (AMS) v3 gyakori kérdésekre.

## <a name="general"></a>Általános

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Milyen Azure-szerepkörök végezhetnek műveleteket Azure Media Services erőforrásokon? 

Lásd: [szerepköralapú hozzáférés-vezérlés (RBAC) Media Services fiókokhoz](rbac-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Hogyan a Media szolgáltatás számára fenntartott egységek konfigurálása?

A hangelemzés és videó elemzési feladatokat a Media Services v3 által aktivált vagy a Video Indexer azt javasoljuk 10 S3 szintű helyet a fiók kiépítése. Ha több mint 10 S3-helyet, nyisson meg egy támogatási jegyet a a [az Azure portal](https://portal.azure.com/).

Részletekért lásd: [a médiafájlok feldolgozásának skálázása a CLI-vel](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Mi az ajánlott módszer a videók feldolgozására?

Az [átalakítások](https://docs.microsoft.com/rest/api/media/transforms) használatával konfigurálhatja a videók kódolására vagy elemzésére szolgáló gyakori feladatokat. Mindegyik **transzformáció** egy receptet vagy egy munkafolyamatot ír le, amely a videó-vagy hangfájlok feldolgozására használható. A [](https://docs.microsoft.com/rest/api/media/jobs) feladatnak az a tényleges kérése, hogy Media Services az **átalakítást** egy adott bemeneti videóra vagy hangtartalomra alkalmazza. Az átalakítás létrehozása után Media Services API-kkal vagy a közzétett SDK-kkal is elküldheti a feladatokat. További információt az [átalakításokkal és feladatokkal](transforms-jobs-concept.md) kapcsolatos cikkben olvashat.

### <a name="how-does-pagination-work"></a>Hogyan működik a tördelés?

A tördelés használatakor mindig a következő hivatkozásra kell használnia a gyűjtemény számbavételéhez, és nem függ egy adott oldalméret méretétől. A részleteket és példákat lásd: [szűrés, rendezés, lapozás](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Milyen funkciók még nem érhetők el a Azure Media Services v3 verzióban?

Részletekért lásd: a [szolgáltatás hiányosságainak a v2 API-k tekintetében](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Milyen folyamattal kell áthelyezni Media Services fiókot az előfizetések között?  

Részletekért lásd: [Media Services fiók áthelyezése](media-services-account-concept.md)az előfizetések között.

## <a name="live-streaming"></a>Élő közvetítés 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Szünetek/videók és képbeszúrások beillesztése az élő streambe

Media Services v3 élő kódolás még nem támogatja a videó vagy a képek beszúrását az élő adatfolyamban. 

A forrás videó átváltásához használhat egy [élő helyszíni kódolót](recommended-on-premises-live-encoders.md) . Számos alkalmazás lehetővé teszi a források váltását, beleértve a Wirecast, a Switching Studio (iOS), a OBS Studio (ingyenes alkalmazás) és sok más szolgáltatást.

## <a name="content-protection"></a>Tartalomvédelem

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Használjon AES-128 titkosítatlan titkosítási vagy DRM-rendszer használatát?

Ügyfeleink gyakran wonder, hogy használják inkább a AES-titkosítás vagy a DRM-rendszer. A két rendszer közötti elsődleges különbség az, hogy az AES-titkosítás a tartalmi kulcsot TLS-kapcsolaton keresztül továbbítja az ügyfélnek, így a kulcs továbbítása átvitel alatt áll, de további titkosítás nélkül ("Clear"). Ennek eredményeképpen a tartalom visszafejtéséhez használt kulcs elérhető az ügyfél számára, és az ügyfélen lévő hálózati nyomkövetésben is megtekinthető egyszerű szövegként. Az AES-128 Clear Key encryption olyan használati esetekben alkalmas, ahol a megjelenítő megbízható fél (például a vállalaton belül elosztott vállalati videók titkosítása az alkalmazottak számára).

A DRM-rendszerek (például a PlayReady, a Widevine és a FairPlay) minden további titkosítási szintet biztosítanak a tartalom visszafejtéséhez használt kulcshoz képest egy AES-128 tiszta kulccsal szemben. A tartalmi kulcs titkosítása a DRM-futtatókörnyezet által védett kulcshoz történik, a TLS által biztosított átviteli szintű titkosítás további részében. Ezenkívül a visszafejtési az operációs rendszer szintjén, ahol nehezebb a támadásokkal szemben egy rosszindulatú felhasználó biztonságos környezetben történik. DRM ajánlott használati esetek, ahol a megjelenítő lehetséges, hogy nem egy megbízható entitás, és Ön a legmagasabb szintű biztonságot követelhetnek meg.

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Hogyan és hol érdemes a JWT jogkivonat beszerzése és a kérés licenc- vagy kulcs?

1. Éles környezetben olyan biztonságos jogkivonat-szolgáltatásokkal (STS) kell rendelkeznie, amely JWT-tokent ad a HTTPS-kérések esetén. Tesztelési, használhatja a kód látható **GetTokenAsync** meghatározott metódus [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Indítson egy, az STS-re, például egy jogkivonatot a felhasználó hitelesítése után, és rendelje hozzá a token értékeként Player kell. Használhatja a [az Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/).

* Példa STS, futtatása vagy szimmetrikus vagy aszimmetrikus kulccsal, tekintse meg [ https://aka.ms/jwt ](https://aka.ms/jwt). 
* Egy ilyen JWT-jogkivonat használatával az Azure Media Player-alapú Media Player egy példa: [ https://aka.ms/amtest ](https://aka.ms/amtest) (bontsa ki a "player_settings" hivatkozásra kattintva megtekintheti a token bemeneti).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hogyan engedélyezi, hogy kérelmeket a Videóknak az AES-titkosítás?

A megfelelő módszer, hogy kihasználja az STS (Secure Token Service):

Az STS-ben a felhasználói profiltól függően adjon hozzá különböző jogcímeket (például a "prémium szintű felhasználó", az "alapszintű felhasználó", az "ingyenes próbaverzió felhasználója"). A különböző jogcímek a jwt-t a felhasználó megtekintheti a különböző tartalmát. Természetesen különböző tartalom eszköz, a ContentKeyPolicyRestriction rendelkezik a megfelelő RequiredClaims.

Azure Media Services API-k használatával konfigurálhatja a licencek/kulcsok küldését és titkosíthatja az adategységeket (ahogy az [ebben](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)a mintában is látható).

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

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Használhatom a Azure Portal a v3-erőforrások kezeléséhez?

Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Létezik AssetFile-koncepció a v3-as verzióban?

A AssetFiles el lettek távolítva az AMS API-ból, hogy el lehessen különíteni a Media Services a Storage SDK-függőségtől. Most a Storage, nem Media Services, megtartja a tárolóban található adatokat. 

További információ: Migrálás [Media Services v3](migrate-from-v2-to-v3.md)-ra.

### <a name="where-did-client-side-storage-encryption-go"></a>Hol található az ügyféloldali tároló-titkosítás?

Javasoljuk, hogy használja a kiszolgálóoldali tároló-titkosítást (amely alapértelmezés szerint be van kapcsolva). További információ: [Azure Storage Service encryption for](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)inaktív adatok.

## <a name="next-steps"></a>További lépések

[Media Services v3 – áttekintés](media-services-overview.md)
