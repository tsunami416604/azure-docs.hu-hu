---
title: A fiók konfigurálása a PlayReady által védett tartalmak offline streameléséhez - Azure
description: Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Media Services-fiókot a PlayReady for Windows 10 offline streameléséhez.
services: media-services
keywords: DASH, DRM, Widevine Offline mód, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: willzhan
ms.openlocfilehash: 350b8d111652511627ddf67236f63248a5489015
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74970448"
---
# <a name="offline-playready-streaming-for-windows-10"></a>Offline PlayReady-streamelés Windows 10-hez  

> [!div class="op_single_selector" title1="Válassza ki a Media Services használt verzióját:"]
> * [3-as verzió](../latest/offline-plaready-streaming-for-windows-10.md)
> * [2-es verzió](offline-playready-streaming-windows-10.md)

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Az Azure Media Services támogatja az offline letöltést/lejátszást a DRM-védelemmel. Ez a cikk az Azure Media Services for Windows 10/PlayReady-ügyfelek offline támogatását ismerteti. Az iOS/FairPlay és Android/Widevine eszközök offline módtámogatásáról az alábbi cikkekben olvashat:

- [Offline FairPlay-streamelés iOS-hez](media-services-protect-hls-with-offline-fairplay.md)
- [Offline Widevine Streaming Androidra](offline-widevine-for-android.md)

## <a name="overview"></a>Áttekintés

Ez a rész ad némi háttér offline módban lejátszás, különösen azért, mert:

* Egyes országokban/régiókban az internet elérhetősége és/vagy sávszélessége továbbra is korlátozott.Használók május választ -hoz letölt első -hoz képesnek lenni etyegés elégedett -ban magas elég határozat részére kielégítő megtekintés tapasztalat. Ebben az esetben gyakrabban a probléma nem a hálózat rendelkezésre állása, hanem a korlátozott hálózati sávszélesség. Az OTT/OVP szolgáltatók offline módtámogatást kérnek.
* Amint azt a Netflix 2016 Q3 részvényesi konferenciáján nyilvánosságra hozták, a tartalom letöltése "gyakran kért funkció", és "nyitottak vagyunk rá" - mondta Reed Hastings, a Netflix vezérigazgatója.
* Egyes tartalomszolgáltatók leengedélyezhetik a DRM-licenc kézbesítését az ország/régió határain túl. Ha a felhasználónak külföldre kell utaznia, és továbbra is meg szeretné nézni a tartalmat, offline letöltésre van szükség.
 
Az offline mód megvalósítása során a következő kihívással kell szembenéznünk:

* Az MP4-et számos lejátszó, kódoló eszköz támogatja, de nincs kötés az MP4 tároló és a DRM között;
* Hosszú távon a CENC-vel kötött CFF az út. Azonban ma, az eszközök / játékos támogató ökoszisztéma még nem létezik. Még ma megoldást kell adnunk.
 
Az ötlet a következő: sima streaming[(PIFF)](https://docs.microsoft.com/iis/media/smooth-streaming/protected-interoperable-file-format)fájlformátum H264/AAC van egy kötelező PlayReady (AES-128 CTR). Egyéni sima streaming .ismv fájl (feltételezve, hogy a hang muxed videó) maga is egy fMP4 és fel lehet használni a lejátszáshoz. Ha egy sima streamelési tartalom megy keresztül PlayReady titkosítás, minden .ismv fájl lesz PlayReady védett töredezett MP4. Választhatunk egy .ismv fájlt az előnyben részesített bitrátával, és átnevezhetjük .mp4-re letölthető.

Két lehetőség van a PlayReady védett MP4 üzemeltetésére a progresszív letöltéshez:

* Ezt az MP4-et ugyanabban a tároló-/médiaszolgáltatási eszközben helyezheti el, és az Azure Media Services streamelési végpontját használhatja a progresszív letöltéshez;
* A SAS-lokátor tveheti a fokozatos letöltésközvetlenül az Azure Storage-ból, megkerülve az Azure Media Services.
 
A PlayReady-licenckézbesítés nek két típusa használható:

* PlayReady licenckézbesítési szolgáltatás az Azure Media Servicesben;
* PlayReady licenc szerverek házigazdája bárhol.

Az alábbiakban két teszteszközkészlet található, amelyek közül az első a PlayReady-licenc kézbesítését használja az AMS-ben, míg a második az Azure virtuális gépen üzemeltetett PlayReady licenckiszolgálót:

Eszköz #1:

* Progresszív letöltési URL:[https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS):[https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

Eszköz #2:

* Progresszív letöltési URL:[https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (on-prem):[https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

A lejátszás teszteléséhez univerzális Windows-alkalmazást használtam a Windows 10 rendszeren. A [Windows 10 Universal mintákban](https://github.com/Microsoft/Windows-universal-samples)van egy adaptív streaming minta nevű alaplejátszóminta. [Adaptive Streaming Sample](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming) Mindössze annyit kell tennünk, hogy adjunk a kódot számunkra, hogy vegye letöltött videó és használja, mint a forrás, hanem az adaptív streaming forrás. A változások a gomb kattintási eseménykezelő:

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

 ![A PlayReady által védett fMP4 offline módú lejátszása](./media/offline-playready/offline-playready1.jpg)

Mivel a videó a PlayReady védelem alatt áll, a képernyőkép nem fogja tudni felvenni a videót.

Összefoglalva, offline módot értünk el az Azure Media Services szolgáltatásban:

* A tartalomátkódolás és a PlayReady titkosítás az Azure Media Services szolgáltatásban vagy más eszközökkel végezhető el;
* A tartalom az Azure Media Servicesben vagy az Azure Storage-ban is üzemeltethető fokozatos letöltésre;
* A PlayReady licenckézbesítése lehet az Azure Media Servicestől vagy máshonnan;
* Az előkészített sima streamelési tartalom továbbra is használható a DASH-en keresztüli online streameléshez, vagy a PlayReady-vel a DRM-mel.

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="next-steps"></a>További lépések

[Hibrid DRM-rendszer terve](hybrid-design-drm-sybsystem.md)
