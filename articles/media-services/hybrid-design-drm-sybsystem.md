---
title: "Az Azure Media Services DRM subsystem(s) hibrid kialakítása |} Microsoft Docs"
description: "Ez a témakör ismerteti az Azure Media Services használatával DRM subsystem(s) hibrid kialakítással."
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: willzhan;juliako
ms.openlocfilehash: 841b1164db6fd1a2c029b98392509c15f23158e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="hybrid-design-of-drm-subsystems"></a>DRM subsystem(s) hibrid kialakítása

Ez a témakör ismerteti az Azure Media Services használatával DRM subsystem(s) hibrid kialakítással.

## <a name="overview"></a>Áttekintés

Az Azure Media Services támogatást nyújt a következő három DRM rendszer:

* PlayReady
* Widevine (moduláris)
* FairPlay

A DRM-támogatást tartalmaz, amelynek Azure Media Player támogató összes 3 DRMs adott böngésző SDK DRM titkosítás (a dinamikus titkosítás) és a licenc kézbesítési.

Részletes kezelésére DRM/CENC alrendszer megtervezését és megvalósítását, lásd: a című dokumentum [Multi-DRM-és hozzáférés-vezérlés CENC](media-services-cenc-with-multidrm-access-control.md).

Bár a teljes mértékben támogatják a három DRM rendszerek kínálunk, néha kell használni a saját Azure Media Services mellett infrastruktúra/alrendszereket különböző részeivel DRM alrendszer hibrid létrehozásához.

Alább néhány általános kérdés felkérést ügyfelek:

* "Használható saját DRM licenckiszolgálókat?" (Ebben az esetben az ügyfelek befektettek DRM licenc kiszolgálófarm beágyazott üzleti logika).
* "Használható csak a DRM licenc kézbesítési Azure Media Services nélkül AMS tartalma üzemeltető?"

## <a name="modularity-of-the-ams-drm-platform"></a>Az AMS DRM platform modularitás

Egy kiterjedt funkciókészlettel ellátott felhőbeli videó platform részeként Azure Media Services DRM rugalmasságot és modularitást biztosítson a kialakítás rendelkezik szem előtt. Azure Media Services használatával az alábbi különböző kombinációk (a tábla használt jelölés magyarázata a következő) az alábbi táblázat ismerteti. 

|**Tartalom üzemeltető & forrása**|**Tartalom titkosítása**|**DRM-licenckézbesítés**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Külső|
|AMS|Külső|AMS|
|AMS|Külső|Külső|
|Külső|Külső|AMS|

### <a name="content-hosting--origin"></a>Tartalom üzemeltető & forrása

* AMS: video asset tárolódik AMS és adatfolyam-AMS streamvégpontok (de nem feltétlenül dinamikus becsomagolás) keresztül.
* Külső: videó üzemeltetett és egy külső adatfolyam platform kívül AMS-i.

### <a name="content-encryption"></a>Tartalom titkosítása

* AMS: tartalomtitkosító elvégezni dinamikusan/igény szerint AMS a dinamikus titkosítás.
* Külső: tartalomtitkosító előre feldolgozásra munkafolyamattal AMS kívül történik.

### <a name="drm-license-delivery"></a>DRM-licenckézbesítés

* AMS: DRM licenc hozta AMS licenctovábbítási szolgáltatásra.
* Külső: DRM-licenc egy külső DRM licenckiszolgáló AMS kívül érkeznek.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurálja a hibrid forgatókönyvek alapján

### <a name="content-key"></a>Tartalomkulcs

Tartalomkulcs beállításon szabályozhatja a AMS a dinamikus titkosítás és a szállítási AMS-licencelési szolgáltatástól a következő attribútumokat:

* A dinamikus DRM-titkosításhoz használt tartalomkulcsot.
* DRM licenc tartalom licenctovábbítási szolgáltatások által: jogosultságokat, tartalomkulcsot és korlátozásokat.
* Írja be a **hitelesítési házirend-korlátozás tartalom**: IP- vagy a token korlátozás megnyitásához.
* Ha **token** típusú **szolgál a tartalomkulcs-hitelesítési házirend-korlátozás**, a **hitelesítési házirend-korlátozás tartalom** teljesülniük kell ahhoz licencet ad ki.

### <a name="asset-delivery-policy"></a>Objektumtovábbítási szabályzat

Objektumtovábbítási szabályzat beállításon szabályozhatja a AMS dinamikus csomagoló és a dinamikus titkosítás AMS adatfolyam-továbbítási végpontok használja a következő attribútumokat:

* Folyamatos átviteli protokoll és DRM titkosítási kombináció, például a CENC (PlayReady és Widevine), KÖTŐJELET sima a PlayReady, alatt áll, a PlayReady vagy Widevine HLS streamelési.
* Az alapértelmezett/beágyazott licenc kézbesítési URL-címeket az egyes az érintett DRMs.
* E licenc licenckérési URL-címeit (LA_URLs) DASH MPD vagy HLS lista tartalmaz kulcs azonosítója (KID) lekérdezési karakterláncot a Widevine és FairPlay, illetve.

## <a name="scenarios-and-samples"></a>Forgatókönyvek és minták

Az előző szakaszban magyarázatokat alapján, az alábbi öt hibrid forgatókönyvek használata megfelelő **tartalomkulcs**-**objektumtovábbítási szabályzat** konfigurációkat támogat (utolsó oszlop szerepel a minták hajtsa végre a táblázat):

|**Tartalom üzemeltető & forrása**|**DRM-titkosítás**|**DRM-licenckézbesítés**|**Konfigurálja a tartalomkulcs**|**Objektumtovábbítási szabályzat konfigurálása**|**Minta**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Igen|Igen|1. példa|
|AMS|AMS|Külső|Igen|Igen|2. példa|
|AMS|Külső|AMS|Igen|Nem|3. példa|
|AMS|Külső|Külső|Nem|Nem|4. példa|
|Külső|Külső|AMS|Igen|Nem|    

A mintákat a PlayReady-védelem működik DASH vagy smooth streaming. Az alábbi videó URL-címei smooth streaming URL-címeket. Ahhoz, hogy a megfelelő vonal URL-címeket, csak hozzáfűzése "(formátum = mpd-idő-csf)". Használhatja a [azure media player tesztelése](http://aka.ms/amtest) tesztelése a böngészőben. Ez lehetővé teszi, hogy szeretné használni, mely technológia a folyamatos átviteli protokoll konfigurálása. IE11 és a Windows 10 MS Edge támogatja a PlayReady EME keresztül. További információkért lásd: [a vizsgálati eszköz részleteit](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>1. példa

* Forrás (alap) URL-címe: https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady-LA_URL (DASH & smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (DASH): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>2. példa

* Forrás (alap) URL-címe: http://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady-LA_URL (DASH & smooth): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>3. példa

* Forrás URL-címe: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady-LA_URL (DASH & smooth): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>4. példa

* Forrás URL-címe: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady-LA_URL (DASH & smooth): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Összefoglalás

Az összegzés Azure Media Services DRM-összetevők rugalmas, használhatja őket egy hibrid forgatókönyvben megfelelően konfigurálja a tartalomkulcs és az adategység továbbítási házirendjét, ebben a témakörben leírtak szerint.

## <a name="next-steps"></a>Következő lépések
Nézet Media Services tanulási útvonalai.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

