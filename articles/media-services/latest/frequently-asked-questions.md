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
ms.openlocfilehash: c9da29ad288811bbed225fd906f2a7eb1fd9edf7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977726"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Media Services v3 – gyakori kérdések

Ez a cikk választ ad a Azure Media Services (AMS) v3 gyakori kérdésekre.

## <a name="general"></a>Általános

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Milyen Azure-szerepkörök végezhetnek műveleteket Azure Media Services erőforrásokon? 

Lásd: [szerepköralapú hozzáférés-vezérlés (RBAC) Media Services fiókokhoz](rbac-overview.md).

### <a name="how-do-i-configure-media-reserved-units"></a>Hogyan a Media szolgáltatás számára fenntartott egységek konfigurálása?

A Media Services v3 vagy a Video Indexer által aktivált hangelemzési és videoelemzési feladatok esetében javasolt 10 S3 MRU-val ellátni a fiókot. Ha több mint 10 S3 MRUs van szüksége, nyisson meg egy támogatási jegyet a [Azure Portal](https://portal.azure.com/)használatával.

Részletekért lásd: [a médiafájlok feldolgozásának skálázása a CLI-vel](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Mi az ajánlott módszer a videók feldolgozására?

Az [átalakítások](https://docs.microsoft.com/rest/api/media/transforms) használatával konfigurálhatja a videók kódolására vagy elemzésére szolgáló gyakori feladatokat. Mindegyik **transzformáció** egy receptet vagy egy munkafolyamatot ír le, amely a videó-vagy hangfájlok feldolgozására használható. A [feladatnak](https://docs.microsoft.com/rest/api/media/jobs) az a tényleges kérése, hogy Media Services az **átalakítást** egy adott bemeneti videóra vagy hangtartalomra alkalmazza. Az átalakítás létrehozása után Media Services API-kkal vagy a közzétett SDK-kkal is elküldheti a feladatokat. További információt az [átalakításokkal és feladatokkal](transforms-jobs-concept.md) kapcsolatos cikkben olvashat.

### <a name="how-does-pagination-work"></a>Hogyan működik a tördelés?

A tördelés használatakor mindig a következő hivatkozásra kell használnia a gyűjtemény számbavételéhez, és nem függ egy adott oldalméret méretétől. A részleteket és példákat lásd: [szűrés, rendezés, lapozás](entities-overview.md).

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Milyen funkciók még nem érhetők el a Azure Media Services v3 verzióban?

Részletekért lásd: a [szolgáltatás hiányosságainak a v2 API-k tekintetében](migrate-from-v2-to-v3.md#feature-gaps-with-respect-to-v2-apis).

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Milyen folyamattal kell áthelyezni Media Services fiókot az előfizetések között?  

Részletekért lásd: [Media Services fiók áthelyezése az előfizetések között](media-services-account-concept.md).

## <a name="live-streaming"></a>Live streaming (Élő adatfolyam) 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Szünetek/videók és képbeszúrások beillesztése az élő streambe

Media Services v3 élő kódolás még nem támogatja a videó vagy a képek beszúrását az élő adatfolyamban. 

A forrás videó átváltásához használhat egy [élő helyszíni kódolót](recommended-on-premises-live-encoders.md) . Számos alkalmazás lehetővé teszi a források váltását, beleértve a Wirecast, a Switching Studio (iOS), a OBS Studio (ingyenes alkalmazás) és sok más szolgáltatást.

## <a name="content-protection"></a>Tartalomvédelem

### <a name="should-i-use-an-aes-128-clear-key-encryption-or-a-drm-system"></a>Használjon AES-128 titkosítatlan titkosítási vagy DRM-rendszer használatát?

Az ügyfelek gyakran kíváncsiak arra, hogy az AES-titkosítást vagy a DRM-rendszereket használják-e. A két rendszer közötti elsődleges különbség az, hogy az AES-titkosítás a tartalmi kulcsot TLS-kapcsolaton keresztül továbbítja az ügyfélnek, így a kulcs továbbítása átvitel alatt áll, de további titkosítás nélkül ("Clear"). Ennek eredményeképpen a tartalom visszafejtéséhez használt kulcs elérhető az ügyfél számára, és az ügyfélen lévő hálózati nyomkövetésben is megtekinthető egyszerű szövegként. Az AES-128 Clear Key encryption olyan használati esetekben alkalmas, ahol a megjelenítő megbízható fél (például a vállalaton belül elosztott vállalati videók titkosítása az alkalmazottak számára).

A DRM-rendszerek (például a PlayReady, a Widevine és a FairPlay) minden további titkosítási szintet biztosítanak a tartalom visszafejtéséhez használt kulcshoz képest egy AES-128 tiszta kulccsal szemben. A tartalmi kulcs titkosítása a DRM-futtatókörnyezet által védett kulcshoz történik, a TLS által biztosított átviteli szintű titkosítás további részében. Emellett az operációs rendszer szintjén egy biztonságos környezetben kezeli a visszafejtést, ahol a rosszindulatú felhasználók támadása nehezebbé válik. A DRM használata olyan esetekben ajánlott, amikor a megjelenítő nem megbízható fél, és Önnek a legmagasabb szintű biztonságra van szüksége.

### <a name="how-to-show-a-video-only-to-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hogyan jeleníthető meg a videó csak azokra a felhasználókra, akik rendelkeznek az Azure AD-vel való használattal az adott engedéllyel?

Nem kell semmilyen speciális jogkivonat-szolgáltatót (például az Azure AD-t) használni. A saját [JWT](https://jwt.io/) -szolgáltató (ÚN. STS, biztonságos jogkivonat-szolgáltatás) használatával aszimmetrikus kulcsú titkosítást használhat. Az egyéni STS-ben az üzleti logikája alapján adhat hozzá jogcímeket.

Győződjön meg arról, hogy a kibocsátó, a célközönség és a jogcímek pontosan egyeznek a JWT és a ContentKeyPolicy használt ContentKeyPolicyRestriction között.

További információ: [a tartalom Media Services dinamikus titkosítással való védelemmel](content-protection-overview.md)való ellátása.

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Hogyan és honnan szerezhető be a JWT-token ahhoz, hogy licencet vagy kulcsot igényeljen?

1. Éles környezetben olyan biztonságos jogkivonat-szolgáltatásokkal (STS) kell rendelkeznie, amely JWT-tokent ad a HTTPS-kérések esetén. A teszthez használhatja a [program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs)-ben definiált **GetTokenAsync** metódusban megjelenő kódot.
2. A játékosnak kérést kell tennie a felhasználó hitelesítése után, az STS-hoz egy ilyen jogkivonat esetében, és azt a jogkivonat értékeként kell hozzárendelni. Használhatja a [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/)-t.

* Ha például az STS-t szimmetrikus és aszimmetrikus kulccsal futtatja, tekintse meg a [https://aka.ms/jwt](https://aka.ms/jwt). 
* Ha az ilyen JWT-tokent használó Azure Media Playerra szeretne példát használni, tekintse meg a [https://aka.ms/amtest](https://aka.ms/amtest) ("player_settings" hivatkozás kibontásával a jogkivonat bemenetének megjelenítéséhez).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hogyan engedélyezhető a videók AES-titkosítással való továbbítására irányuló kérések engedélyezése?

A helyes módszer az STS (Secure token Service) használata:

Az STS-ben a felhasználói profiltól függően adjon hozzá különböző jogcímeket (például a "prémium szintű felhasználó", az "alapszintű felhasználó", az "ingyenes próbaverzió felhasználója"). A JWT eltérő jogcímek esetén a felhasználó különböző tartalmakat láthat. Természetesen a különböző tartalmak/eszközök esetében a ContentKeyPolicyRestriction a megfelelő RequiredClaims fog rendelkezni.

Azure Media Services API-k használatával konfigurálhatja a licencek/kulcsok küldését és titkosíthatja az adategységeket (ahogy az [ebben a mintában](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)is látható).

További információ eléréséhez lásd:

- [A tartalomvédelem áttekintése](content-protection-overview.md)
- [Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md)

### <a name="http-or-https"></a>HTTP vagy HTTPS?
A ASP.NET MVC Player alkalmazásnak a következőket kell támogatnia:

* Felhasználói hitelesítés az Azure AD-n keresztül, amely a HTTPS alatt található.
* JWT az ügyfél és az Azure AD között, amely HTTPS-alapú.
* Az ügyfél által megvásárolt DRM-licenc, amelynek a HTTPS alatt kell lennie, ha a Media Services biztosít a licencek kézbesítését. A PlayReady termék csomagja nem kötelezi a HTTPS protokoll megadását a licencek kézbesítéséhez. Ha a PlayReady-licenckiszolgáló kívül esik Media Serviceson, HTTP vagy HTTPS protokollt is használhat.

A ASP.NET Player alkalmazás a HTTPS protokollt használja ajánlott eljárásként, így Media Player a HTTPS alatt található oldalon. A HTTP azonban a streaming számára ajánlott, ezért érdemes figyelembe vennie a vegyes tartalommal kapcsolatos problémákat.

* A böngésző nem engedélyezi a kevert tartalmat. A beépülő modulok (például a Silverlight és a OSMF beépülő modul) a zökkenőmentes és a kötőjel esetében is lehetővé teszik. A vegyes tartalom biztonsági problémát jelent a rosszindulatú JavaScript beadására irányuló fenyegetés miatt, ami kockázatot jelenthet az ügyfelek számára. A böngészők alapértelmezés szerint blokkolja ezt a funkciót. Az egyetlen módja annak, hogy megkerülje a kiszolgálót (forrás) az összes tartomány (a HTTPS vagy HTTP protokolltól függetlenül) engedélyezésével. Ez valószínűleg nem jó ötlet.
* Kerülje a kevert tartalmat. A Player alkalmazásnak és a Media Playernek egyaránt HTTP vagy HTTPS protokollt kell használnia. Ha vegyes tartalmat játszik, a Silverlight-technológiához vegyes tartalmú figyelmeztetést kell törölni. A flashs Tech vegyes tartalmú figyelmeztetés nélkül kezeli a kevert tartalmakat.
* Ha a folyamatos átviteli végpontot 2014 augusztusa előtt hozták létre, nem támogatja a HTTPS protokollt. Ebben az esetben hozzon létre és használjon egy új streaming-végpontot a HTTPS-hez.

### <a name="what-about-live-streaming"></a>Mi a helyzet az élő közvetítéssel?

Pontosan ugyanazt a kialakítást és megvalósítást használhatja a Media Services élő közvetítésének biztosításához, ha a programhoz társított eszközt a VOD-eszközként kezeli. Az élő tartalom több DRM elleni védelmének biztosításához alkalmazza ugyanazt a telepítést/feldolgozást az eszközre, mintha egy VOD-eszköz lenne, mielőtt az eszközt az élő kimenethez társítja.

### <a name="what-about-license-servers-outside-media-services"></a>Mi a teendő a Media Serviceson kívüli licenckiszolgálóra?

Az ügyfelek gyakran a saját adatközpontjában vagy a DRM-szolgáltatók által üzemeltetett licencelési kiszolgálófarm egyikében fektettek be. A Media Services Content Protection használatával hibrid módban is működhet. A tartalmak a Media Servicesban tárolhatók és dinamikusan védhetők, míg a DRM-licenceket a Media Serviceson kívüli kiszolgálók is továbbítják. Ebben az esetben vegye figyelembe a következő változásokat:

* Az STS-nek olyan jogkivonatokat kell kiállítania, amelyek elfogadhatók, és a licenckiszolgáló-Farm ellenőrizheti őket. A Axinom által biztosított Widevine-licenckiszolgálók például egy adott JWT igényelnek, amely jogosultsági üzenetet tartalmaz. Ezért rendelkeznie kell egy STS-vel, hogy ilyen JWT adjon ki. 
* Már nem kell konfigurálnia a Media Services licenc kézbesítési szolgáltatását. A ContentKeyPolicies konfigurálásakor meg kell adnia a licenc-beszerzési URL-címeket (a PlayReady, a Widevine és a FairPlay esetében).

> [!NOTE]
> A Widevine a Google Inc által biztosított szolgáltatás, és a Google, Inc. szolgáltatási és adatvédelmi szabályzatának feltételei vonatkoznak rá.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 vs v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Használhatom a Azure Portal a v3-erőforrások kezeléséhez?

Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Létezik AssetFile-koncepció a v3-as verzióban?

A AssetFiles el lettek távolítva az AMS API-ból, hogy el lehessen különíteni a Media Services a Storage SDK-függőségtől. Most a Storage, nem Media Services, megtartja a tárolóban található adatokat. 

További információ: [migrálás Media Services v3](migrate-from-v2-to-v3.md)-ra.

### <a name="where-did-client-side-storage-encryption-go"></a>Hol található az ügyféloldali tároló-titkosítás?

Javasoljuk, hogy használja a kiszolgálóoldali tároló-titkosítást (amely alapértelmezés szerint be van kapcsolva). További információ: [Azure Storage Service encryption for](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)inaktív adatok.

## <a name="next-steps"></a>Következő lépések

[Media Services v3 – áttekintés](media-services-overview.md)
