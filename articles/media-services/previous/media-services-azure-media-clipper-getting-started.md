---
title: "Ismerkedés az Azure Media Webmontázskészítő |} Microsoft Docs"
description: "Ismerkedés az Azure Media Webmontázskészítő, egy eszköz a videó videóklipeket felépítése az AMS-eszközök"
services: media-services
keywords: "Klip; subclip; kódolás; adathordozó"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ac64d97aeeef6147aa62658c9ee440bf058f4db1
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Az Azure Media Webmontázskészítő videóklipeket létrehozása
Ez a szakasz bemutatja az alapvető lépéseken, Ismerkedés az Azure Media Webmontázskészítő. Hajtsa végre a következő szakaszok konkrétumok az Azure Media Webmontázskészítő konfigurálásához.

- Első lépésként adja a következő hivatkozásokat az Azure Media Player és az Azure Media Webmontázskészítő a dokumentum head. Azt javasoljuk, hogy a Webmontázskészítő vagy az Azure Media Player explicit megadása az URL-címéből. Ne használja ezeket az erőforrásokat a legújabb éles környezetben szerint az igény szerinti változhat.

```javascript
<!--Azure Media Player 2.1.4 or later is a prerequisite-->
<link href="//amp.azure.net/libs/amp/2.1.4/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amp/2.1.4/azuremediaplayer.min.js"></script>
<!--Azure Media Clipper script and stylesheet-->
<link href="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.css" rel="stylesheet">
<script src="//amp.azure.net/libs/amc/0.1.0/azuremediaclipper.min.js"></script>
```

- Ezután adja hozzá a következő osztályok a hol szeretné példányosítani a Webmontázskészítő div elembe.

```javascript
<div id="root" class="azure-subclipper" />
```

Opcionálisan ahhoz, hogy a sötét téma, adja hozzá a sötét-felszín osztály:

```javascript
<div id="root" class="azure-subclipper dark-skin" />
```

- A következő példányt létrehozni a következő API-hívással Webmontázskészítő:

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

Az inicializálás metódus hívásához a paraméterek a következők:
- `selector`{A szükséges, karakterlánc}: CSS választó, ahol kell megjeleníteni a widgetet hozzá tartozó HTML-elem.
- `restVersion`{A szükséges, karakterlánc}: cél számára, az Azure Media Services REST API-verzió. A REST-verzió határozza meg a widget által létrehozott kimeneti formátuma. Jelenleg csak 2.0 használata támogatott.
- `submitSubclipCallback`{SZÜKSÉGES, ígéret} A visszahívási függvény meghívni, amikor a widget a "submit" gombra kattint. A visszahívási függvény a widgetet (a leképezési feladat konfigurációját vagy a szűrő definícióját) által létrehozott kimeneti kell látnia. További információkért lásd: küldés subclip visszahívás.
- `logLevel`{Nem kötelező, {"információ", "figyelmeztetés", "error"}}: A naplózási szint a böngészőbeli konzolon megjelenik. Alapértelmezett érték: hiba
- `minimumMarkerGap`{Nem kötelező, int}: egy subclip (másodpercben) legkisebb méretét. Megjegyzés: az érték nem lehet kisebb 6, mint az alapértelmezett.
- `singleBitrateMp4Profile`{A JSON-objektum OPCIONÁLIS} A leképezési feladat konfigurációját a widget által generált használandó egyféle sávszélességű mp4 profil. Ha nincs megadva, akkor az a [egyféle sávszélességű MP4-profil alapértelmezett](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-single-bitrate-1080p).
- `multiBitrateMp4Profile`{A JSON-objektum OPCIONÁLIS} A többszörös sávszélességű mp4 profil használandó képezhető le a widget által generált feladat konfigurációs. Ha nincs megadva, akkor az a [többszörös sávszélességű MP4-profil alapértelmezett](https://docs.microsoft.com/azure/media-services/media-services-mes-preset-h264-multiple-bitrate-1080p).
- `keymap`{A json-objektum OPCIONÁLIS} Lehetővé teszi, hogy a widget billentyűparancsok testreszabása. További információkért lásd: [testre szabható billentyűparancsok](media-services-azure-media-clipper-keyboard-shortcuts.md).
- `assetsPanelLoaderCallback`{Nem kötelező, ígéret} A visszahívási függvény meghívása betöltés (aszinkron) új eszközök a eszközök ablaktáblára minden alkalommal, amikor a felhasználó görget le a képernyő aljára, ha a panel. További információkért lásd: eszköz panelen betöltő visszahívás.
- `height`{Nem kötelező, number} A widget teljes magasságát (minimális magasság értéke 600 képpont eszközök ablaktábla és 850 nélkül az eszközök ablaktábla képpont).
- `subclippingMode`(Nem kötelező, {"all", "leképezési", "szűrése"}): A subclipping módokat engedélyezett. Az alapértelmezett érték: az összes.
- `filterAssetsTypes`(Nem kötelező, logikai): filterAssetsTypes lehetővé teszi az Eszközök panelről a szűrők legördülő lista megjelenítése. Az alapértelmezett érték: igaz.
- `speedLevels`(Nem kötelező, tömb): speedLevels lehetővé teszi különböző sebességű szint beállítása a videólejátszó című [Azure Media Player dokumentáció](http://amp.azure.net/libs/amp/latest/docs/#amp.player.playbackspeedoptions) vonatkozó információ.
- `resetOnJobDone`(Nem kötelező, logikai): resetOnJobDone lehetővé teszi, hogy a Webmontázskészítő alaphelyzetbe állítja a subclipper Alapállapot, ha a feladat elküldése sikerült.
- `autoplayVideo`(Nem kötelező, logikai): autoplayVideo lehetővé teszi, hogy Webmontázskészítő automatikus lejátszásának betöltése a videót. Az alapértelmezett érték: igaz.
- `language`{Nem kötelező, karakterlánc}: nyelv, a widget nyelvének beállítása. Ha nincs megadva, a widget megpróbálja localize az üzenetek a böngésző nyelvének alapján. Ha nincs nyelv észlel a böngészőben, a widget alapértelmezés szerint az angol. További információkért lásd: a [honosítási konfigurálása](media-services-azure-media-clipper-localization.md) szakasz.
- `languages`{Nem kötelező, JSON}: a nyelv paraméter az alapértelmezett szótár nyelv cseréli a felhasználó által definiált egyéni szótár. További információkért lásd: a [honosítási konfigurálása](media-services-azure-media-clipper-localization.md) szakasz.
- `extraLanguages`(Nem kötelező, JSON): a extraLanaguages a paraméter hozzáadja az új nyelvek az alapértelmezett szótár. További információkért lásd: a [honosítási konfigurálása](media-services-azure-media-clipper-localization.md) szakasz.

## <a name="typescript-definition"></a>Géppel meghatározása
A [géppel](https://www.typescriptlang.org/) a Webmontázskészítő definíciós fájljának található [Itt](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Az Azure Media Webmontázskészítő API
Ez a szakasz az API felületén a Webmontázskészítő által biztosított dokumentumokat.

- `ready(handler)`: a JavaScript futtatása, amint a Webmontázskészítő teljesen betöltve és használatra kész módját kínálja.
- `load(assets)`: az eszközök listáját betöltődnek az widget ütemterv (assetsPanelLoaderCallback együtt nem használható). Ez [cikk](media-services-azure-media-clipper-load-assets.md) kapcsolatos részletes tudnivalókért eszközök betölthető a Webmontázskészítő.
- `setLogLevel(level)`: a böngészőbeli konzolon megjelenítendő naplózási szintjének beállítása. Lehetséges értékek a következők: `info`, `warn`, `error`.
- `setHeight(height)`: Beállítja a widget teljes magassága képpontban (minimális magasság értéke 600 képpont eszközök ablaktábla nélkül, és 850 az eszközök ablaktábla képpont).
- `version`: a widget verzió lekérdezi.

## <a name="next-steps"></a>Következő lépések
A következő lépések Azure Media Webmontázskészítő konfigurálásához:
- [Az Azure Media Webmontázskészítő eszközök betöltése](media-services-azure-media-clipper-load-assets.md)
- [Egyéni billentyűparancsok konfigurálása](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [A Webmontázskészítő Kivágás-feladatok elküldése](media-services-azure-media-clipper-submit-job.md)
- [Honosítási konfigurálása](media-services-azure-media-clipper-localization.md)