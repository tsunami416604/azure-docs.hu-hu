---
title: Az offline streamelési PlayReady által védett tartalom – Azure-fiók konfigurálása
description: Ez a cikk bemutatja, hogyan konfigurálhatja a folyamatos átviteli offline PlayReady Windows 10-es Azure Media Services-fiók.
services: media-services
keywords: DASH, DRM, Widevine ExoPlayer, a kapcsolat nélküli módban Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/30/2018
ms.author: willzhan
ms.openlocfilehash: 8a5aefe1bade27c2f71914b61b332d5583026081
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53980740"
---
# <a name="offline-playready-streaming-for-windows-10"></a>Offline PlayReady Windows 10-es Streaming

Az Azure Media Services támogatja a DRM-védelemmel rendelkező offline letöltési/lejátszását. Ez a cikk ismerteti az Azure Media Services for Windows 10-es vagy PlayReady ügyfelek offline támogatást. IOS/FairPlay és Android/Widevine eszközökhöz az alábbi cikkekben olvashat bővebben az offline mód támogatása:

- [Offline FairPlay Streaming iOS-hez](media-services-protect-hls-with-offline-fairplay.md)
- [Offline Widevine androidhoz Streaming](offline-widevine-for-android.md)

## <a name="overview"></a>Áttekintés

Ez a szakasz áttekintést nyújt a bizonyos tapasztalattal a lejátszás kapcsolat nélküli üzemmódban különösen miért:

* Egyes országokban Internet rendelkezésre állási és/vagy a sávszélesség korlátozva. Előfordulhat, hogy a felhasználók megadhatják nézheti elég nagy felbontású kielégítő élményt a tartalom letöltéséhez először. Ebben az esetben gyakrabban, a probléma nem hálózat rendelkezésre állásának, hanem azt korlátozott a sávszélesség. OTT/OVP szolgáltatók arra utasítja a kapcsolat nélküli módban támogatás.
* Közzétett Netflix 2016 3. negyedévi részvényes konferencián, letölti a tartalmat egy olyan "szélén a kért funkció" és "vagyunk rá nyissa meg" kifejezés utal Reed Hastings, a Netflix Vezérigazgató által.
* Néhány tartalomszolgáltatók előfordulhat, hogy ne engedélyezze a DRM-licenckézbesítés túl egy ország szegély. Ha a felhasználó továbbítani a külföldön van szüksége, és tekintse meg a tartalom továbbra is szeretné, offline letöltési van szükség.
 
A kihívás, mi a face megvalósításában kapcsolat nélküli módban a következő:

* MP4 számos lejátszó, kódoló eszközök által támogatott, de nem létezik a kötés nélküli MP4-tároló és a DRM; között
* A hosszú távú CFF CENC az módja a go. Azonban még ma, az eszközök/player támogatási ökoszisztéma még nem létezik. A Microsoft olyan megoldásra van szüksége, még ma.
 
Az elképzelés: smooth streaming ([PIFF](http://go.microsoft.com/?linkid=9682897)) H264/AAC-formátum egy kötés PlayReady (AES-128 Parancsra) rendelkezik. Egyedi smooth streaming .ismv fájl (feltételezve hang egyesített a videóban) önmagában egy fMP4 és lejátszás is használható. Egy smooth streaming tartalom végighalad a PlayReady-titkosítás, ha egyes .ismv fájl lesz-e a PlayReady-védelemmel töredékes MP4. Válasszon egy .ismv fájlt az előnyben részesített sávszélességű lehet és letölthető .mp4, nevezze át.

Két lehetőség van a PlayReady üzemeltető védett MP4 progresszív letöltés:

* Egy adott tároló/media service objektum a MP4 helyezni, és kihasználhatja az Azure Media Services a progresszív letöltés; streamvégpont
* Az SAS-kereső progresszív letöltés közvetlenül az Azure Storage, a rendszer megkerüli az Azure Media Services egyik is használhatja.
 
A PlayReady-licencekkel történő kézbesítés két típusú használhatja:

* PlayReady szolgáltatásra vonatkozó az Azure Media Servicesben;
* A PlayReady-licenc kiszolgálók üzemeltetett.

Az alábbiakban két készlet tesztelési eszközök, az elsőt a PlayReady-licencekkel történő kézbesítés a másodiknál, használatával egy Azure virtuális Gépen lévő üzemeltetett saját PlayReady-licenckiszolgáló az AMS használatával:

#1. eszköz:

* A progresszív letöltési URL-címe: [http://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](http://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4")
* PlayReady LA_URL (AMS): [https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

#2. eszköz:

* A progresszív letöltési URL-címe: [http://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](http://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (helyszíni): [https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

A lejátszási vizsgálatára, egy univerzális Windows-alkalmazás a Windows 10-es használt. A [univerzális Windows 10-minták](https://github.com/Microsoft/Windows-universal-samples), egy alapszintű player mintáját nevű [adaptív Streamelés minta](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Mindössze azt kell az adja hozzá a kódot ahhoz, hogy válassza ki a letöltött videót, és szeretné használni a forrás, az adaptív adatfolyam-továbbítási forrás helyett. A változások a következők gomb eseménykezelő gombra:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

 ![Kapcsolat nélküli módban lejátszási PlayReady-védelemmel fMP4](./media/offline-playready/offline-playready1.jpg)

Mivel a videó alatt PlayReady-védelmet, a képernyőképen nem lesz képes a videókat is tartalmazhatnak.

Összefoglalva az Azure Media Services kapcsolat nélküli módban el:

* Tartalom átkódolása és a PlayReady-titkosítás teheti meg az Azure Media Services vagy más eszközökkel;
* Tartalom lehet üzemeltetni az Azure Media Services vagy az Azure Storage a progresszív letöltés;
* A PlayReady-licencekkel történő kézbesítés lehet az Azure Media Services vagy máshol;
* A előkészített smooth streaming tartalom továbbra is használható online streamelés DASH-n keresztül, vagy a PlayReady, a DRM smooth.

## <a name="next-steps"></a>További lépések

[Hibrid DRM-rendszer terve](hybrid-design-drm-sybsystem.md)
