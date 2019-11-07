---
title: Bevezetés az Azure Media Clipper használatába | Microsoft Docs
description: Ismerkedés az Azure Media Clipper szolgáltatással, amely az AMS-eszközökből származó videoklipek létrehozására szolgáló eszköz
services: media-services
keywords: klip; alklip; kódolás; média
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 45ecc81967d6a95f817b10bce7f8396d9379bc94
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685076"
---
# <a name="create-clips-with-azure-media-clipper"></a>Klipek létrehozása az Azure Media Clipper-vel
Ez a szakasz az Azure Media Clipper első lépéseinek alapvető lépéseit mutatja be. Az alábbi szakasz az Azure Media Clipper konfigurálásának sajátosságait adja meg.

- Először adja hozzá az alábbi hivatkozásokat az Azure Media Player és az Azure Media Clipper szolgáltatáshoz a dokumentum Head (bevezető) fiókjához. Javasoljuk, hogy explicit módon határozza meg a Clipper-verziót, és Azure Media Player az URL-címekben. Ne használja az erőforrások legújabb verzióját éles környezetben, mivel azok igény szerint változhatnak.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Ezután adja hozzá a következő osztályokat a div elemhez, ahová a Clipper-t szeretné létrehozni.

```javascript
<div id="root" class="azure-subclipper" />
```

Ha szeretné engedélyezni a sötét témát, adja hozzá a Dark-Skin osztályt:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Ezután hozza létre a Clipper-t a következő API-hívással:

```javascript
var subclipper = new subclipper({
    selector: '#root',
    logLevel: 'info',
    restVersion: '2.0',
    minimumMarkerGap: 6,
    submitSubclipCallback: onSubmitSubclip,
    singleBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with single H264Layers
                }, {
                    // Audio Codec
                }]
    },
    multiBitrateMp4Profile: {
            Codecs: [{
                    // Video Codec with multiple H264Layers
                }, {
                    // Audio Codec
                }]
    },
    keymap: {
      // See below for more info
    },
   // Enable the Video Assets panel in the widget to automatically load assets (input contract)
    assetsPanelLoaderCallback: onLoadAssets,
    height: 600,
    subclippingMode: 'all',
    filterAssetsTypes: true,
    speedLevels: [
        { name: '4x', value: 4.0 },
        { name: '3x', value: 3.0 },
        { name: '2x', value: 2.0 },
        { name: 'normal', value: 1.0 },
        { name: '0.75x', value: 0.75 },
        { name: '0.5x', value: 0.5 },
    ],
    resetOnJobDone: false,
    autoplayVideo: true,
    language: 'en'    
});
```

Az inicializálási metódus hívásának paraméterei a következők:
- `selector` {kötelező, karakterlánc}: a megfelelő HTML-elem CSS-választója, ahol a widgetet meg kell jeleníteni.
- `restVersion` {REQUIREd, string}: a Azure Media Services REST API a célként megadott verzió. A REST-verzió határozza meg a widget által generált kimenet formátumát. Jelenleg csak 2,0 támogatott.
- `submitSubclipCallback` {kötelező, ígéret} a visszahívási függvényt a rendszer a widget Küldés gombjára kattintva hívja meg. A visszahívási függvénynek meg kell várnia a widget által generált kimenetet (a renderelési feladat konfigurációját vagy egy szűrő definícióját). További információ: alklip visszahívásának elküldése.
- `logLevel` {opcionális, {"info", "warn", "Error"}}: a böngésző konzolján megjelenítendő naplózási szint. Alapértelmezett érték: hiba
- `minimumMarkerGap` {opcionális, int}: egy alklip minimális mérete (másodpercben). Megjegyzés: az érték nem lehet kisebb, mint 6, ami egyben az alapértelmezett is.
- `singleBitrateMp4Profile` {opcionális, JSON-objektum} a widget által generált renderelési feladatok konfigurációjának egyetlen bitráta MP4-profilját kell használnia. Ha nincs megadva, az [alapértelmezett egyetlen sávszélességű MP4-profilt](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p)használja.
- `multiBitrateMp4Profile` {opcionális, JSON-objektum} a többszörös sávszélességű MP4-profil, amelyet a widget által generált feladatok konfigurációjának megjelenítéséhez kíván használni. Ha nincs megadva, az [alapértelmezett többszörös sávszélességű MP4-profilt](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p)használja.
- `keymap` {opcionális, JSON-objektum} lehetővé teszi a widget billentyűparancsának testreszabását. További információ: [testreszabható billentyűparancsok](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {nem kötelező, ígéret} a visszahívási függvény meghívja az adategységek panel új lapjainak betöltését (aszinkron módon) az eszközök ablaktáblára, amikor a felhasználó a ablaktábla aljára görget. További információ: Asset panel betöltő visszahívás.
- `height` {opcionális, szám} a widget teljes magassága (a minimális magasság 600 px az eszközök ablaktábla nélkül és 850 px az eszközök ablaktáblán).
- `subclippingMode` (opcionális, {"all", "render", "filter"}): a kivágási mód (ok) engedélyezett. Az alapértelmezett érték az összes.
- `filterAssetsTypes` (nem kötelező, bool): a filterAssetsTypes lehetővé teszik a szűrők legördülő listájának megjelenítését vagy elrejtését az eszközök ablaktáblán. Az alapértelmezett érték TRUE (igaz).
- `speedLevels` (nem kötelező, tömb): a speedLevels lehetővé teszi a videolejátszó különböző sebességének beállítását, további információért lásd a [Azure Media Player dokumentációját](https://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) .
- `resetOnJobDone` (nem kötelező, bool): a resetOnJobDone lehetővé teszi a Clipper számára, hogy egy kezdeti állapotba állítsa alaphelyzetbe, ha a feladatot sikeresen elküldik.
- `autoplayVideo` (nem kötelező, bool): a autoplayVideo lehetővé teszi a betöltést a videó lejátszásához. Az alapértelmezett érték TRUE (igaz).
- `language` {OPTIONal, string}: Language beállítja a widget nyelvét. Ha nincs megadva, a widget megpróbálja honosítani az üzeneteket a böngésző nyelve alapján. Ha a böngészőben nem észlelhető nyelv, a widget alapértelmezett értéke az angol. További információ: [honosítás konfigurálása](media-services-azure-media-clipper-localization.md) szakasz.
- `languages` {opcionális, JSON}: a languages paraméter lecseréli a nyelvek alapértelmezett szótárát a felhasználó által definiált egyéni szótárra. További információ: [honosítás konfigurálása](media-services-azure-media-clipper-localization.md) szakasz.
- `extraLanguages` (nem kötelező, JSON): a extraLanguages paraméter új nyelveket helyez el az alapértelmezett szótárhoz. További információ: [honosítás konfigurálása](media-services-azure-media-clipper-localization.md) szakasz.

## <a name="typescript-definition"></a>Írógéppel-definíció
[Itt](https://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts)megtalálhatja a Clipper-hez készült, [géppel](https://www.typescriptlang.org/) ellátható definíciós fájlt.

## <a name="azure-media-clipper-api"></a>Azure Media Clipper API
Ez a szakasz a Clipper által biztosított API-felületet dokumentálja.

- `ready(handler)`: lehetővé teszik a JavaScript futtatását, amint a Clipper teljesen betöltődik, és készen áll a használatra.
- `load(assets)`: az eszközök listájának betöltése a widget-idővonalba (a assetsPanelLoaderCallback együtt nem használható). Tekintse meg ezt a [cikket](media-services-azure-media-clipper-load-assets.md) , amelyből megtudhatja, hogyan tölthetők be az eszközök a Clipper szolgáltatásba.
- `setLogLevel(level)`: a böngésző konzolján megjelenítendő naplózási szint beállítása. A lehetséges értékek a következők: `info`, `warn`, `error`.
- `setHeight(height)`: beállítja a widget teljes magasságát képpontban megadva (a minimális magasság 600 px az eszközök panel nélkül, és 850 px az eszközök ablaktáblával).
- `version`: lekéri a widget verzióját.

## <a name="next-steps"></a>További lépések
Tekintse meg az Azure Media Clipper konfigurálásának következő lépéseit:
- [Eszközök betöltése az Azure Media Clipperbe](media-services-azure-media-clipper-load-assets.md)
- [Egyéni billentyűparancsok konfigurálása](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Levágási feladatok elküldése a Clipperből](media-services-azure-media-clipper-submit-job.md)
- [Honosítás konfigurálása](media-services-azure-media-clipper-localization.md)
