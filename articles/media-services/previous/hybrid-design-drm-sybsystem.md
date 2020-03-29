---
title: Drm-alrendszer(ek) hibrid kialakítása az Azure Media Services használatával | Microsoft dokumentumok
description: Ez a témakör a DRM-alrendszer(ek) azure Media Services használatával történő hibrid tervezését ismerteti.
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
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: d2f4ddfbff791fbfeb2eb006a628c0fdeb4fdce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975193"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Drm-alrendszerek hibrid kialakítása 

Ez a témakör a DRM-alrendszer(ek) azure Media Services használatával történő hibrid tervezését ismerteti.

## <a name="overview"></a>Áttekintés

Az Azure Media Services a következő három DRM-rendszerhez nyújt támogatást:

* PlayReady
* Widevine (moduláris)
* FairPlay

A DRM-támogatás drm-titkosítást (dinamikus titkosítást) és licenckézbesítést tartalmaz, az Azure Media Player pedig mind a 3 DRM-et támogatja böngészőlejátszóként, sdk-ként.

A DRM/CENC alrendszer tervezésének és megvalósításának részletes kezelését lásd a CENC with [Multi-DRM és a Access Control című dokumentumban.](media-services-cenc-with-multidrm-access-control.md)

Bár három DRM-rendszer teljes körű támogatást nyújtunk, néha az ügyfeleknek az Azure Media Services mellett saját infrastruktúrájuk/alrendszereik különböző részeit kell használniuk a hibrid DRM-alrendszer létrehozásához.

Az alábbiakban felsorolunk néhány gyakori kérdést, amelyeket az ügyfelek feltettek:

* "Használhatom a saját DRM licenckiszolgálóimat?" (Ebben az esetben az ügyfelek beágyazott üzleti logikával rendelkező DRM licenckiszolgáló-farmba fektettek be).
* "Használhatom csak a DRM-licenckézbesítést az Azure Media Servicesben anélkül, hogy az AMS-ben tartalmat üzemeltetnék?"

## <a name="modularity-of-the-ams-drm-platform"></a>Az AMS DRM platform modularitása

Egy átfogó felhőalapú videoplatform részeként az Azure Media Services DRM a rugalmasságot és a modularitást szem előtt tartva rendelkezik. Az Azure Media Services az alábbi táblázatban ismertetett alábbi különböző kombinációk bármelyikével használható (a táblázatban használt jelölés magyarázata). 

|**Tartalomtárhely & eredete**|**Tartalom titkosítása**|**DRM-licenckézbesítés**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Harmadik fél|
|AMS|Harmadik fél|AMS|
|AMS|Harmadik fél|Harmadik fél|
|Harmadik fél|Harmadik fél|AMS|

### <a name="content-hosting--origin"></a>Tartalomtárhely & eredete

* AMS: a videoeszköz az AMS-ben található, a streamelés pedig az AMS-streamelési végpontokon keresztül történik (de nem feltétlenül dinamikus csomagoláson keresztül).
* Harmadik fél: a videót az AMS-en kívüli, harmadik féltől származó streaming platformon tároljuk és szállítjuk.

### <a name="content-encryption"></a>Tartalom titkosítása

* AMS: a tartalomtitkosításdinamikus ams dinamikus titkosítással dinamikusan/igény szerint történik.
* Harmadik féltől származó: a tartalomtitkosítás az AMS-en kívül történik egy előfeldolgozási munkafolyamat használatával.

### <a name="drm-license-delivery"></a>DRM-licenckézbesítés

* AMS: A DRM-licencet az AMS licenckézbesítési szolgáltatás ad ki.
* Harmadik féltől származó: A DRM-licencet egy külső GYÁRTÓ TÓL származó DRM-licenckiszolgáló szállítja az AMS-en kívül.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurálás a hibrid forgatókönyv alapján

### <a name="content-key"></a>Tartalomkulcs

A tartalomkulcs konfigurálása révén az AMS dinamikus titkosítás és az AMS-licenckézbesítési szolgáltatás következő attribútumait szabályozhatja:

* A dinamikus DRM-titkosításhoz használt tartalomkulcs.
* Drm licenc tartalom által szállított licenc kézbesítési szolgáltatások: jogok, tartalomkulcs és korlátozások.
* A **tartalomkulcs-engedélyezési házirend korlátozásának**típusa: nyitott, IP vagy jogkivonat-korlátozás.
* Ha **jogkivonattípusú** **tartalomkulcs-engedélyezési házirend-korlátozást használ,** a licenc kiadása előtt teljesíteni kell a **tartalomkulcs-engedélyezési házirend korlátozását.**

### <a name="asset-delivery-policy"></a>Eszközkézbesítési irányelv

Az eszközkézbesítési szabályzat konfigurálásával az AMS dinamikus csomagolója által használt alábbi attribútumokat és az AMS-streamelési végpontok dinamikus titkosítását szabályozhatja:

* Streamelési protokoll és DRM titkosítási kombináció, például a DASH CENC (PlayReady és Widevine) alatt, zökkenőmentes streamelés a PlayReady, hls widevine alatt vagy PlayReady alatt.
* Az alapértelmezett/beágyazott licenckézbesítési URL-címek az egyes érintett DRM-ekhez.
* Azt jelzi, hogy a DASH MPD vagy A HLS lejátszási listában szereplő licencbeszerzési URL-ek (LA_URLs) tartalmazzák-e a Widevine és a FairPlay kulcsazonosítójának (KID) lekérdezési karakterláncát.

## <a name="scenarios-and-samples"></a>Forgatókönyvek és minták

Az előző szakasz magyarázatai alapján a következő öt hibrid forgatókönyv a megfelelő-**tartalomkulcs-eszközkézbesítési házirend** konfigurációs kombinációit használja (az utolsó oszlopban említett minták a táblázatot követik): **Content key**

|**Tartalomtárhely & eredete**|**DRM-titkosítás**|**DRM-licenckézbesítés**|**Tartalomkulcs konfigurálása**|**Objektumtovábbítási szabályzat konfigurálása**|**Minta**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Igen|Igen|1. példa|
|AMS|AMS|Harmadik fél|Igen|Igen|2. példa|
|AMS|Harmadik fél|AMS|Igen|Nem|3. példa|
|AMS|Harmadik fél|Kívül|Nem|Nem|4. példa|
|Harmadik fél|Harmadik fél|AMS|Igen|Nem|    

A mintákban a PlayReady védelem a DASH és a smooth streaming esetén is működik. Az alábbi videó URL-ek sima streamelési URL-ek. A megfelelő DASH URL-ek betöltéséhez csak fűzz hozzá "(format=mpd-time-csf)".To get the corresponding DASH URLs, just append "(format=mpd-time-csf)". Az azure [media test player](https://aka.ms/amtest) segítségével tesztelheti a böngészőben. Ez lehetővé teszi, hogy konfigurálja, amely streamelési protokollhasználata, amely tech. A Windows 10-en futó IE11 és Microsoft Edge támogatja a PlayReady-t az EME-n keresztül. További információt [a teszteszköz részleteiben talál.](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/)

### <a name="sample-1"></a>1. példa

* Forrás (alap) URL-címe:https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady LA_URL (DASH & sima):https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine LA_URL (DASH):https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay LA_URL (HLS):https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>2. példa

* Forrás (alap) URL-címe:https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH & sima):http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>3. példa

* Forrás URL-címe:https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & sima):https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>4. példa

* Forrás URL-címe:https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & sima):https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="summary"></a>Összefoglalás

Összefoglalva, az Azure Media Services DRM-összetevői rugalmasak, használhatja őket egy hibrid forgatókönyvben a tartalomkulcs és az eszközkézbesítési szabályzat megfelelő konfigurálásával, ebben a témakörben leírtak szerint.

## <a name="next-steps"></a>További lépések
A Media Services tanulási útvonalainak megtekintése.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

