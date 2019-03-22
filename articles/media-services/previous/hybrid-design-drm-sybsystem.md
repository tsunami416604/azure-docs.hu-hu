---
title: Az Azure Media Services használatával DRM subsystem(s) kialakítása |} A Microsoft Docs
description: Ez a témakör ismerteti az Azure Media Services használatával DRM subsystem(s) kialakítása.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan;juliako
ms.openlocfilehash: 5c86a49cd9dc26f724de12ed2e5e77e645e4ab53
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57886711"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hibrid DRM-alrendszer kialakítása 

Ez a témakör ismerteti az Azure Media Services használatával DRM subsystem(s) kialakítása.

## <a name="overview"></a>Áttekintés

Az Azure Media Services a következő három DRM-rendszer támogatja:

* PlayReady
* Widevine (moduláris)
* FairPlay

A DRM-támogatás magában foglalja a DRM titkosítást (a dinamikus titkosítás) és -licenckézbesítés, az Azure Media Player támogató összes 3 DRMs egy böngésző lejátszó SDK-t.

DRM/CENC alrendszer tervezési és megvalósítási részletes kezelése, tekintse meg a című dokumentum [CENC többplatformos DRM és hozzáférés-vezérlés](media-services-cenc-with-multidrm-access-control.md).

Bár három DRM rendszerek teljes körű támogatása kínálunk, néha ügyfelek kell használniuk a saját infrastruktúra/alrendszerek mellett az Azure Media Services különböző részeivel hozhat létre olyan hibrid DRM-alrendszer.

Alább néhány gyakran felmerülő kérdésre által feltett ügyfelek:

* "Használható a saját DRM-licenckiszolgáló?" (Ebben az esetben ügyfelek befektettek DRM licenc beágyazott üzleti logikával rendelkező kiszolgálófarm).
* "Használható csak a DRM-licenckézbesítés az Azure Media Services az AMS tartalmat tároló nélkül?"

## <a name="modularity-of-the-ams-drm-platform"></a>Az AMS DRM platform modularitás

Egy átfogó felhőalapú videoplatformok részeként az Azure Media Services DRM figyelembe a tervezés, rugalmasságot és modularitást biztosítson az rendelkezik. Használhatja az Azure Media Services (a tábla használt jelölés részéből követi) az alábbi táblázat ismerteti a következő különböző kombinációit állítsák bármelyikével. 

|**Tartalom üzemeltetési & forrás**|**Tartalom titkosítása**|**DRM-licenckézbesítés**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Külső|
|AMS|Külső|AMS|
|AMS|Külső|Külső|
|Külső|Külső|AMS|

### <a name="content-hosting--origin"></a>Tartalom üzemeltetési & forrás

* Az AMS: videó az eszközintelligencia szolgáltatásban üzemeltetett AMS és streamelési AMS streamvégpontok (de nem feltétlenül a dinamikus csomagolás) keresztül.
* Külső: a videó üzemeltetett és egy külső adatstreamelési platform kívül az AMS-i.

### <a name="content-encryption"></a>Tartalom titkosítása

* Az AMS: tartalom titkosítás a elvégezni dinamikusan/igény szerinti dinamikus titkosítás AMS által.
* Külső: tartalomtitkosító kívül AMS előfeldolgozási munkafolyamat használatával történik.

### <a name="drm-license-delivery"></a>DRM-licenckézbesítés

* AMS: DRM-licenckiszolgáló AMS licenctovábbítási szolgáltatása hozta.
* Külső: DRM-licenckiszolgáló egy külső DRM-licenckiszolgáló AMS-en kívül hozta.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurálja a hibrid forgatókönyvek alapján

### <a name="content-key"></a>Tartalomkulcs

Tartalomkulcs konfigurálással szabályozhatja az AMS a dinamikus titkosítás és az AMS licenctovábbítási szolgáltatása, a következő attribútumokat:

* A tartalomkulcs DRM dinamikus titkosítást használja.
* DRM-licenc tartalom licenctovábbítási szolgáltatások által: jogosultságokat, tartalomkulcs és korlátozásokat.
* Írja be a **tartalom-hitelesítési szabályzat korlátozási is megszűnnek**: IP- vagy a token korlátozás megnyitásához.
* Ha **token** típusú **szolgál a tartalomkulcs-hitelesítési szabályzat korlátozási is megszűnnek**, a **tartalom-hitelesítési szabályzat korlátozási is megszűnnek** teljesülniük kell ahhoz egy licencet ad ki.

### <a name="asset-delivery-policy"></a>Objektumtovábbítási szabályzat

Objektumtovábbítási szabályzat konfigurálással szabályozhatja az AMS dinamikus packager és a egy AMS streamvégpont a dinamikus titkosítás által használt a következő attribútumokat:

* Streamelési protokoll és a DRM-titkosítás kombináció, például a DASH alatt CENC (PlayReady és Widevine), smooth a PlayReady, Widevine vagy PlayReady HLS streamelési.
* Az alapértelmezett/beágyazott licenc kézbesítési URL-címek az érintett DRMs mindegyikéhez.
* E licenc licenckérési URL-címek (LA_URLs) DASH MPD vagy HLS lista tartalmazza a lekérdezési kulcs azonosítója (GYEREK) Widevine és FairPlay, jelölik.

## <a name="scenarios-and-samples"></a>Forgatókönyvek és minták

Az előző szakaszban a magyarázatokat alapján, az alábbi öt hibrid forgatókönyvek használata megfelelő **tartalomkulcs**-**állít be objektumtovábbítási szabályzatot** konfigurációs kombinációk (a minták az említett az utolsó oszlopban kövesse a táblázatban):

|**Tartalom üzemeltetési & forrás**|**DRM-titkosítás**|**DRM-licenckézbesítés**|**Konfigurálja a tartalomkulcs**|**Objektumtovábbítási szabályzat konfigurálása**|**Minta**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Igen|Igen|1. példa|
|AMS|AMS|Külső|Igen|Igen|2. példa|
|AMS|Külső|AMS|Igen|Nem|3. példa|
|AMS|Külső|Külső|Nem|Nem|4. példa|
|Külső|Külső|AMS|Igen|Nem|    

A minták a PlayReady-védelmet a DASH és smooth Streaming formátumba esetében működik. A Videó URL-címek az alábbi olyan smooth streaming URL-címeket. A megfelelő DASH URL-címek lekérése, csak hozzáfűzése "(formátum = mpd-time-csf)". Használhatja a [az azure media player tesztelése](https://aka.ms/amtest) ellenőrzéséhez egy böngészőben. Lehetővé teszi a streamelési protokoll szeretne használni, mely technológiai területen konfigurálja. IE11 és a Microsoft Edge a Windows 10-es támogatja a PlayReady eme. További információkért lásd: [a vizsgálati eszköz részleteit](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>1. példa

* Forrás (alap) URL-címe: https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH és smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (DASH): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>2. példa

* Forrás (alap) URL-címe: https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH és smooth): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>3. példa

* Forrás URL-címe: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH és smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>4. példa

* Forrás URL-címe: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH és smooth): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Összegzés

Összefoglalva az Azure Media Services DRM összetevők rugalmasak, használhatja őket egy hibrid forgatókönyvben megfelelően konfigurálja a tartalomkulcs és állít be objektumtovábbítási szabályzatot, ebben a témakörben leírtak szerint.

## <a name="next-steps"></a>További lépések
Nézet Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

