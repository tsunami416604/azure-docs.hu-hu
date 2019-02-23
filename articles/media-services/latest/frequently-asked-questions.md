---
title: Az Azure Media Services v3 – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk – gyakori kérdések az Azure Media Services v3 választ ad.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: c0dc67ddf0f1de9ca72fd14a9113219209a6bda0
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56674210"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Az Azure Media Services v3 – gyakori kérdések

Ez a cikk – gyakori kérdések az Azure Media Services (AMS) v3-as választ ad.

## <a name="v3-apis"></a>V3 API-k

### <a name="how-do-i-configure-media-reserved-units"></a>Hogyan konfigurálhatom a Media szolgáltatás számára fenntartott egységeket?

A hangelemzés és videó elemzési feladatokat a Media Services v3 által aktivált vagy a Video Indexer azt javasoljuk 10 S3 szintű helyet a fiók kiépítése. Ha több mint 10 S3-helyet, nyisson meg egy támogatási jegyet a a [az Azure portal](https://portal.azure.com/).

További információkért lásd: [méretezhető médiafeldolgozás a parancssori felület segítségével](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Mi az az ajánlott módszer a folyamat videókhoz?

Használat [alakítja át](https://docs.microsoft.com/rest/api/media/transforms) és videók elemzése gyakori feladatok konfigurálása. Minden egyes **átalakítása** egy módszereivel, vagy egy munkafolyamatot a video- és audiotartalmak fájlok feldolgozása kapcsolatos feladatokat ismerteti. A [feladat](https://docs.microsoft.com/rest/api/media/jobs) Media Services-tényleges kérés a alkalmazni a **átalakítása** egy adott a bemeneti videó vagy hang tartalomhoz. Az átalakítás létrehozása után küldhet feladatokat a Media Services API-k és az egyéb közzétett SDK-k használatával. További információkért lásd: [átalakítások és feladatok](transforms-jobs-concept.md).

### <a name="how-does-pagination-work"></a>Hogyan működik a tördelés?

Tördelés használatakor mindig használjon a következő hivatkozás enumerálása a gyűjteményt, és nem függ egy adott oldal méretét. Részletek és példák: [szűrése, rendezése, lapozás](entities-overview.md).

## <a name="live-streaming"></a>Live streaming (Élő adatfolyam) 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Hogyan szúrhat be a sortöréseket és videók és lemezkép befutók során élő streamet?

A Media Services v3 valós idejű kódolás még nem támogatja beszúrása video- vagy képfájl befutók során élő streamet. 

Használhat egy [élő helyszíni kódolót](recommended-on-premises-live-encoders.md) váltani a forrásvideókat. Számos alkalmazás lehetővé teszi, hogy váltson a forrásból, például Telestream Wirecast, váltó Studio (az iOS esetében), OBS Studio (ingyenes app) és sok más.

## <a name="content-protection"></a>Tartalomvédelem

### <a name="how-and-where-to-get-jwt-token-before-using-it-to-request-license-or-key"></a>Hogyan és hol érdemes a JWT jogkivonat beszerzése és a kérés licenc- vagy kulcs?

1. Éles környezetben szüksége lesz egy Secure Token szolgáltatások (STS) (webszolgáltatás) amely JWT jogkivonatot egy HTTPS-kérés esetén. Tesztelési, használhatja a kód látható **GetTokenAsync** meghatározott metódus [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).
2. Indítson egy, az STS-re, például egy jogkivonatot a felhasználó hitelesítése után, és rendelje hozzá a token értékeként Player kell. Használhatja a [az Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/).

* Példa STS, futtatása vagy szimmetrikus vagy aszimmetrikus kulccsal, tekintse meg [ http://aka.ms/jwt ](https://aka.ms/jwt). 
* Egy ilyen JWT-jogkivonat használatával az Azure Media Player-alapú Media Player egy példa: [ http://aka.ms/amtest ](https://aka.ms/amtest) (bontsa ki a "player_settings" hivatkozásra kattintva megtekintheti a token bemeneti).

### <a name="how-do-you-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hogyan engedélyezi, hogy kérelmeket a Videóknak az AES-titkosítás?

A megfelelő módszer, hogy kihasználja az STS (Secure Token Service):

STS attól függően, felhasználói profil hozzá más jogcímeket (például a "Prémium szintű felhasználó", "Alapszintű felhasználó", "Ingyenes próbaverzió felhasználó"). A különböző jogcímek a jwt-t a felhasználó megtekintheti a különböző tartalmát. Természetesen különböző tartalom eszköz, a ContentKeyPolicyRestriction rendelkezik a megfelelő RequiredClaims.

Használja az Azure Media Services API-k konfigurálása/licenckulcs kézbesítési és az eszközök titkosítása (ahogyan az [ezt a mintát](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

További információkért lásd:

- [A Content protection áttekintése](content-protection-overview.md)
- [Egy DRM-mel a content protection rendszert a hozzáférés-vezérléssel](design-multi-drm-system-with-access-control.md)

## <a name="media-services-v2-vs-v3"></a>A Media Services v2 és v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Az Azure portal használatával v3-erőforrások kezelése?

még nem. A támogatott SDK-k egyikét használhatja. Oktatóanyagaink és példáink ezekben az doc talál.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Van egy AssetFile fogalma a v3-as?

A AssetFiles annak érdekében, hogy a Media Services elkülönítése Storage SDK-függőség eltávolítása az AMS API-ból. Most már tárolási, nem a Media Services, Storage-ban tartja az információ, amelyhez tartozik. 

További információkért lásd: [áttelepítése a Media Services v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Hová tűntek a storage ügyféloldali titkosítását?

Most már a kiszolgálóoldali tártitkosítás (amely alapértelmezés szerint be van kapcsolva) használata ajánlott. További információkért lásd: [Azure Storage Service Encryption az inaktív adatok](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>További lépések

[Media Services v3 áttekintése](media-services-overview.md)
