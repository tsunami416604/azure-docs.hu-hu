---
title: "Ismerkedés az Azure Media Webmontázskészítő |} Microsoft Docs"
description: "Ismerkedés az Azure Media Webmontázskészítő, egy eszköz-adathordozót a levágja a eszközök"
services: media-services
keywords: "Klip; subclip; kódolás; adathordozó"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8a4f2c79131664ca0d078fa58c6a75b54243e705
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
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
- `language`{Nem kötelező, karakterlánc}: nyelv, a widget nyelvének beállítása. Ha nincs megadva, a widget megpróbálja localize az üzenetek a böngésző nyelvének alapján. Ha nincs nyelv észlel a böngészőben, a widget alapértelmezés szerint az angol. További információ a támogatott nyelvek című rész.
- `languages`{Nem kötelező, JSON}: a nyelv paraméter az alapértelmezett szótár nyelv cseréli a felhasználó által definiált egyéni szótár. További információ a támogatott nyelvek című rész.
- `extraLanguages`(Nem kötelező, JSON): a extraLanaguages a paraméter hozzáadja az új nyelvek az alapértelmezett szótár. További információ a támogatott nyelvek című rész.

## <a name="typescript-definition"></a>Géppel meghatározása
A [géppel](https://www.typescriptlang.org/) a Webmontázskészítő definíciós fájljának található [Itt](http://amp.azure.net/libs/amc/latest/azuremediaclipper.d.ts).

## <a name="azure-media-clipper-api"></a>Az Azure Media Webmontázskészítő API
Ez a szakasz az API felületén a Webmontázskészítő által biztosított dokumentumokat.

- `load(assets)`: az eszközök listáját betöltődnek az Eszközök panelen (együtt nem használhatók `assetsPanelLoaderCallback`). Ez [cikk](media-services-azure-media-clipper-load-assets.md) kapcsolatos részletes tudnivalókért eszközök betölthető a Webmontázskészítő.
- `setLogLevel(level)`: a böngészőbeli konzolon megjelenítendő naplózási szintjének beállítása. Lehetséges értékek a következők: `info`, `warn`, `error`.
- `setHeight(height)`: Beállítja a widget teljes magassága képpontban (minimális magasság értéke 600 képpont eszközök ablaktábla nélkül, és 850 az eszközök ablaktábla képpont).
- `version`: a widget verzió lekérdezi.

## <a name="configuring-azure-media-clipper"></a>Az Azure Media Webmontázskészítő konfigurálása
A következő lépések Azure Media Webmontázskészítő konfigurálásához:
- [Az Azure Media Webmontázskészítő eszközök betöltése](media-services-azure-media-clipper-load-assets.md)
- [Egyéni billentyűparancsok konfigurálása](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [A Webmontázskészítő Kivágás-feladatok elküldése](media-services-azure-media-clipper-submit-job.md)

## <a name="supported-languages"></a>Támogatott nyelvek
A Webmontázskészítő widget 18 nyelveken érhető el. A widget nyelvi beállításához meg kell adnia a `language` paraméter inicializálása során. A kívánt nyelvet kód karakterláncban át az alábbi listából:
- Kínai (egyszerűsített): zh-hans
- Kínai (hagyományos): zh-hant
- Cseh: cs
- Holland, Flemish: nl
- Angol: en
- Francia: fr
- Német: Németország
- Magyar: hu
- Olasz:,
- Japán: japán
- Koreai: ko
- Lengyel: pl
- Portugál (brazíliai): pt-br
- Portugál (Portugália): pt-pt
- Orosz: ru
- Spanyol: es
- Svéd: sv
- Török: tr

Állítsa be az egyéni szótár, vagy az alapértelmezett nyelv szótár kiterjesztése, meg kell határoznia a `languages` vagy `extraLanguages` paraméter, illetve. Adjon át a következő JSON formátummal egyéni szótár:

```javascript
{
      "{language-code}":
          "{message-id}": "{message}"
          ...
      }
      ...
}
```

Például az alábbi példa meghatározza, hogy az angol nyelvű honosított karakterláncok:

```javascript
export default {
  'VideoPlayer.noPreview': 'No video preview',
  'VideoPlayer.loadAsset': 'You must provide a valid asset',
  'AssetsPanel.name': 'Name',
  'AssetsPanel.type': 'Asset type',
  'AssetsPanel.actions': 'Actions',
  'AssetsPanel.loading': 'Loading...',
  'AssetsPanel.duration': 'Duration',
  'AssetsPanel.resolution': 'Resolution',
  'AssetsPanel.pluralFiles': '{0} assets',
  'AssetsPanel.searchFiles': 'Search assets',
  'AssetsPanel.showTypes': 'Show:',
  'AssetsPanel.typesInfo': 'Rendered assets are actual MP4 files. Dynamic manifest filters are filters applied to a parent asset\'s video segment playlist.',
  'AssetsPanel.filterTypes': 'Filters',
  'AssetsPanel.assetTypes': 'Assets',
  'AssetsPanel.assetsAll': 'All',
  'AssetsPanel.addAsset': 'Add asset to the end',
  'AssetsPanel.addFilter': 'Add filter to the timeline',
  'AssetsPanel.invalidAsset': 'The metadata of this asset is not compatible with the other assets in the timeline',
  'AssetsPanel.addAssetWarning': 'Subclipping on assets with different resolutions may cause resolution autoscaling.',
  'AssetsPanel.live': 'LIVE',
  'AssetsPanel.unknown': 'UNKNOWN',
  'AssetsPanel.minimGapNotMet': 'The asset duration must be greater than the minimum clip duration ({0} seconds)',
  'VideoPlayer.openAdvancedSettings': 'Advanced settings',
  'VideoPlayer.singleBitrate': 'Single-bitrate MP4 (rendered)',
  'VideoPlayer.multiBitrate': 'Multi-bitrate MP4 (rendered)',
  'VideoPlayer.dynamicManifest': 'Dynamic manifest filter',
  'VideoPlayer.ErrorWithMessage': 'There was an error in the video player, code {0}, message: {1}',
  'Common.cancel': 'Cancel',
  'Common.OK': 'OK',
  'AdvancedSettings.framerate': 'Frame rate',
  'Dropdown.select': 'Select an option...',
  'InputAsset.RemoveInput': 'Remove source',
  'Zoom.startTime': 'Start time',
  'Zoom.endTime': 'End time',
  'VideoPlayer.subclips': 'Subclips:',
  'VideoPlayer.length': 'Clip length:',
  'Accordion.scrollLeft': 'Scroll to the left',
  'Accordion.scrollRight': 'Scroll to the right',
  'AdvancedSettings.title': 'Advanced settings',
  'AdvancedSettings.subclipName': 'Subclip name',
  'AdvancedSettings.subclipType': 'Subclipping mode',
  'AdvancedSettings.includeAudioTracks': 'Include audio tracks',
  'AdvancedSettings.subclipTypeInfo': 'Single-bitrate and multi-bitrate MP4s are frame accurate rendered assets. Dynamic manifest filters are group-of-pictures (GOP) accurate filters applied to a parent asset. Creating filters does not create a new asset and does not require encoding. Subclipping jobs on live assets are valid as long as their mark times are within the archive window of the parent asset. Filters are valid as long as the parent asset exists and mark times are within its archive window.',
  'AdvancedSettings.frameRateInfo': 'We autodetect frame rate under most scenarios. however, If we cannot autodetect, choose a frame rate from the dropdown for the selected asset(s).',
  'AdvancedSettings.frameRateError': 'Unable to determine frame rate',
  'AdvancedSettings.subclipNameInfo': 'Choose a name for your subclip.',
  'AdvancedSettings.singleAudioTrack': '1 audio track selected',
  'AdvancedSettings.allAudioTracks': 'All audio tracks selected',
  'AdvancedSettings.someAudioTracks': '{0} audio tracks selected',
  'AdvancedSettings.includeAllAudioTracks': 'Include all audio tracks',
  'AssetsPanel.loadingError': 'Failed to retreive assets from server.',
  'AssetsPanel.retry': 'Retry?',
  'CommandBar.prevFrameTitle': 'Back up one frame',
  'CommandBar.prevKeyFrameTitle': 'Back up one GOP',
  'CommandBar.cleanJob': 'Remove all assets',
  'CommandBar.cleanJobTitle': 'Remove all assets from timeline',
  'CommandBar.cleanJobMessage': 'This will empty all video clips from your timeline.',
  'CommandBar.update': 'Update filter',
  'CommandBar.createFilter': 'Create filter',
  'CommandBar.submit': 'Submit subclipper job',
  'CommandBar.jobErrorTitle': 'Operation failed',
  'CommandBar.jobErrorMessage': 'Your subclip failed to submit. Please try again.',
  'CommandBar.markInTitle': 'Set in at playhead',
  'CommandBar.markInPosition': 'Mark in timecode',
  'CommandBar.markOutTitle': 'Set out at playhead',
  'CommandBar.markOutPosition': 'Mark out timecode',
  'CommandBar.nextFrameTitle': 'Advance one frame',
  'CommandBar.nextKeyFrameTitle': 'Advance one GOP',
  'CommandBar.play': 'Play video',
  'CommandBar.pause': 'Pause video',
  'CommandBar.playPreviewTitle': 'Play subclip preview',
  'CommandBar.pausePreviewTitle': 'Pause subclip preview',
  'CommandBar.redoTitle': 'Redo last action',
  'CommandBar.removeAsset': 'Remove current asset',
  'CommandBar.undoTitle': 'Undo last action',
  'VideoPlayer.errorTitle': 'Error',
  'VideoPlayer.errorMessage': 'There was an error loading the selected asset.',
  'Timeline.markIn': 'Mark in bracket',
  'Timeline.markOut': 'Mark out bracket',
  'Timeline.playHead': 'Play head',
};
```