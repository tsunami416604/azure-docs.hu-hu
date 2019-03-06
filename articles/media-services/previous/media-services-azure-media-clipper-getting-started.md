---
title: Ismerkedés az Azure Media Clipperrel |} A Microsoft Docs
description: Ismerkedés az Azure Media Clipperrel, egy eszköz, amellyel videoklipeket az AMS-eszközök
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 02/10/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: a67315287f90143701ca38b9f42e243b2d4913a3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437700"
---
# <a name="create-clips-with-azure-media-clipper"></a>Készíthet az Azure Media Clipperrel
Ez a szakasz bemutatja az alapvető lépéseken: első lépések az Azure Media Clipperrel. Az alábbi szakaszok nyújtanak konfigurálása az Azure Media Clipperrel adatait.

- Először adja hozzá az alábbi hivatkozásokat az Azure Media Player és az Azure Media Clipperrel, a dokumentum fő. Azt javasoljuk, hogy a Clipperrel és az Azure Media Player verzió explicit módon megadása az URL-címeket. Ne használja ezeket az erőforrásokat a legújabb éles környezetben azok igény szerint változhatnak.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Ezután adja hozzá a következő osztályok a div elembe a Clipperrel vezérlőként hova.

```javascript
<div id="root" class="azure-subclipper" />
```

Opcionálisan ahhoz, hogy a sötét téma, adja hozzá a sötét-felszín osztály:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- Következő lépésként hozza létre a Clipperrel a következő API-hívással:

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

Az inicializálás metódus hívásához paraméterei a következők:
- `selector` {Az szükséges, karakterlánc}: CSS-választójára az egyező HTML elem, ahol a widget legyen megjelenítve.
- `restVersion` {Az szükséges, karakterlánc}: A cél az Azure Media Services REST API verziója. A REST-verzió határozza meg a widget kimenetet formátumát. Jelenleg csak 2.0 támogatott.
- `submitSubclipCallback` {SZÜKSÉGES, megtartva} A visszahívási függvény hívása a widget a "submit" gombra való kattintáskor. A visszahívási függvény a widget (egy renderelési feladat konfigurálása vagy egy szűrő definícióját) kimenetet kell látnia. További információkért tekintse meg a Küldés részklip visszahívás.
- `logLevel` {OPTIONAL, {'info', 'warn', 'error'}}: A naplózási szint a böngészőbeli konzolon megjeleníteni. Alapértelmezett érték: hiba
- `minimumMarkerGap` {Nem kötelező, csak int}: A minimális mérete egy részklip (másodpercben). Megjegyzés: az érték lehet kisebb, mint 6, ami egyben az alapértelmezett.
- `singleBitrateMp4Profile` {A JSON-objektum OPCIONÁLIS} A renderelési feladat konfigurálása a vezérlő által generált használandó egyféle sávszélességű mp4 profil. Ha nincs megadva, akkor használja a [egyszeres átviteli sebességű MP4 profil alapértelmezett](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile` {A JSON-objektum OPCIONÁLIS} A többszörös sávszélességű mp4-profil használandó feladat konfigurálása a vezérlő által generált jelennek meg. Ha nincs megadva, akkor használja a [többszörös sávszélességű MP4-profil alapértelmezett](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap` {A json-objektum OPCIONÁLIS} Lehetővé teszi, hogy a billentyűparancsokat a widget az testreszabása. További információkért lásd: [testre szabható billentyűparancsok](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback` {Nem kötelező, megtartva} A visszahívási függvény hívása (aszinkron módon) egy új lap betöltéséhez az eszközök be az adategységek panelen minden alkalommal, amikor a felhasználó jobbra görget le a panel alján. További információkért tekintse meg az eszköz panel betöltő visszahívási.
- `height` {Nem kötelező, number} A widget teljes magassága (minimális magasság legyen 600 képpont eszközök ablaktábla és a 850 nélkül az eszközök ablaktábla képpont).
- `subclippingMode` (OPTIONAL, {'all', 'render', 'filter'}): Az engedélyezett részklipkészítés módokat. Az alapértelmezett érték az összeset használja.
- `filterAssetsTypes` (Nem kötelező, logikai): filterAssetsTypes lehetővé teszik az Eszközök panelen szűrők legördülő megjelenítése/elrejtése. Az alapértelmezett érték: igaz.
- `speedLevels` (Nem kötelező, tömb): speedLevels lehetővé teszi különböző sebességű szint beállítása a videolejátszó, lásd: [dokumentáció az Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) további információ.
- `resetOnJobDone` (Nem kötelező, logikai): resetOnJobDone lehetővé teszi, hogy a Clipperrel alaphelyzetbe állítása a részklipkészítési egy kezdeti állapota, amikor egy feladat elküldése sikerült.
- `autoplayVideo` (Nem kötelező, logikai): autoplayVideo lehetővé teszi, hogy Clipperrel automatikus lejátszás a betöltéskor a videót. Az alapértelmezett érték: igaz.
- `language` {Nem kötelező, csak karakterlánc}: nyelv, a widget nyelvet határozza meg. Ha nincs megadva, a widget próbálja meg megkeresni az üzeneteket, a böngésző nyelve alapján. A böngésző nincs nyelv észlelése esetén a widget alapértelmezés szerint angol. További információkért lásd: a [honosítás konfigurálása](media-services-azure-media-clipper-localization.md) szakaszban.
- `languages` {Nem kötelező, JSON}: a nyelv paraméter az alapértelmezett szótár nyelveken cseréli a felhasználó által definiált egyéni szótár. További információkért lásd: a [honosítás konfigurálása](media-services-azure-media-clipper-localization.md) szakaszban.
- `extraLanguages` (Nem kötelező, JSON): a extraLanguages paramétert az alapértelmezett szótár ad hozzá új nyelvek. További információkért lásd: a [honosítás konfigurálása](media-services-azure-media-clipper-localization.md) szakaszban.

## <a name="typescript-definition"></a>TypeScript-definíció
A [TypeScript](https://www.typescriptlang.org/) a Clipperrel-definíciós fájljának található [Itt](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Az Azure Media Clipperrel API
Ez a szakasz a dokumentumok a Clipperrel által biztosított API-felületet.

- `ready(handler)`: egy JavaScript futtatása, amint a Clipperrel, teljesen betöltött és használatra kész megoldást kínál.
- `load(assets)`: az eszközök listáját betölti a widget ütemterv (assetsPanelLoaderCallback együtt nem használható). Ez [cikk](media-services-azure-media-clipper-load-assets.md) eszközöket tölthet be a Clipperrel részleteiért.
- `setLogLevel(level)`: a böngészőbeli konzolon megjelenő naplózási szintjének beállítása. Lehetséges értékek a következők: `info`, `warn`, `error`.
- `setHeight(height)`: Beállítja a widget teljes magassága képpontban (minimális magasság legyen 600 képpont Eszközök panel nélkül, és 850 az eszközök ablaktábla képpont).
- `version`: a widget-verzió beolvasása.

## <a name="next-steps"></a>További lépések
Tekintse meg a következő lépések az Azure Media Clipperrel konfigurálása:
- [Eszközök betöltése az Azure Media clipperbe](media-services-azure-media-clipper-load-assets.md)
- [Egyéni billentyűparancsok konfigurálása](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Az a Clipperrel klipekkel kapcsolatos feladatok elküldése](media-services-azure-media-clipper-submit-job.md)
- [Honosítás konfigurálása](media-services-azure-media-clipper-localization.md)