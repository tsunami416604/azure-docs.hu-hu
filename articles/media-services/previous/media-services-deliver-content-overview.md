---
title: Az ügyfelek számára történő tartalomtovábbításhoz |} A Microsoft Docs
description: Ez a témakör áttekintést a kézbesítse a tartalmakat az Azure Media Services vesz részt.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 3314ad4558fdd55429a5a68326dd46b5920d7daa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316235"
---
# <a name="deliver-content-to-customers"></a>Továbbítja a tartalmat az ügyfelek számára
Amikor, tartalom jusson el a streamelési vagy igény szerinti videó az ügyfelek számára, célja, hogy a magas színvonalú videó továbbítása különböző eszközökre, különböző hálózati körülmények között.

E cél elérése érdekében a következőket teheti:

* Kódolja többszörös sávszélességű (adaptív sávszélességűvé alakítják) video-adatfolyamot az adatfolyamot. Ez gondoskodik a minőségi és hálózati körülményekhez.
* A Microsoft Azure Media Services használatával [dinamikus csomagolási](media-services-dynamic-packaging-overview.md) dinamikusan csomagolni a stream eltérő protokollok használatára. Ez gondoskodik a streamelési különböző eszközökön. Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: <br/>
    * **HTTP Live Streaming** (HLS) – hozzáadása "(formátum = m3u8-aapl)" elérési útját az URL-címet adja meg a streamelési forráskiszolgáló, térjen vissza a felhasználásához HLS-tartalom a "/ jegyzékfájl" részének **Apple iOS** natív eszközök (a részleteket Lásd: [keresők](#locators) és [URL-címek](#URLs)),
    * **MPEG-DASH** -hozzáadása "(formátum = mpd-time-csf)" elérési út, ossza meg a visszaadandó streamelési forráskiszolgáló URL-címe "/ jegyzékfájl" részéhez biztonsági MPEG-DASH (további információkért lásd: [keresők](#locators) és [URL-címek](#URLs)),
    * **Smooth Streaming**.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

Ez a cikk áttekintést fontos tartalomkézbesítési fogalmakat.

Ismert problémák megtekintéséhez [ismert problémák](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dinamikus csomagolás
A dinamikus csomagolás, hogy a Media Services biztosít, adaptív sávszélességű MP4 vagy Smooth Streaming formátumban kódolt tartalmait streamformátumok valamelyikében (MPEG-DASH, HLS, Smooth Streaming) a Media Services által támogatott juttathat el anélkül, hogy kellene csomagolni ezekbe adatfolyam-továbbítási formátumokba. Azt javasoljuk, hogy kézbesítse a tartalmakat a dinamikus csomagolás használatával.

A dinamikus csomagolás kihasználásához kódolja a mezzanine (forrás) fájlt egy adaptív sávszélességű MP4-fájlokat vagy adaptív sávszélességű Smooth Streaming-fájlsorozattá kell.

A dinamikus csomagolás használatával tárolja, és a fájlokat egyetlen tárolási formátumban kell fizetnie. A Media Services elkészíti és kiszolgálja a kérelmeket a megfelelő választ.

A dinamikus csomagolás a standard és prémium szintű streamelési végpontok érhető el. 

További információkért lásd: [dinamikus csomagolási](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Szűrők és dinamikus jegyzékek
Az eszközök a Media Services szűrőket definiálhat. Ezeket a szűrőket, amelyek segítségével az ügyfelek van például egy adott szakasz videók lejátszásához, vagy adjon meg egy részét, amely a felhasználói eszköz (és nem az összes a beállításkészletben az eszközhöz társított) képes kezelni, hang- és beállításkészletben kiszolgálóoldali-szabályok vonatkoznak. Ez a szűrés a gazdafájlon keresztül *a dinamikus jegyzékek* , amikor az ügyfél kéri, hogy egy videó alapján egy vagy több megadott szűrők jönnek létre.

További információkért lásd: [szűrők és dinamikus jegyzékek](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Keresők
Ahhoz, hogy a felhasználó a tartalmak streamelésére vagy letöltésére használható URL-címet, akkor először tegye közzé az adategységet egy kereső létrehozásával. Egy kereső eléréséhez az eszközintelligencia mappában található fájlokat belépési pontként szolgál. A Media Services két lokátortípust támogat:

* OnDemandOrigin keresők. Ezek a médiatartalmak (például MPEG-DASH, HLS vagy Smooth Streaming) segítségével, vagy fokozatosan letölteni a fájlokat.
* Közös hozzáférésű jogosultságkód (SAS) URL-cím lokátorokat. Ezek használhatók a helyi számítógépen médiafájlok letöltéséhez.

Egy *hozzáférési szabályzat* az engedélyek (például olvasási, írási és listája) meghatározására szolgál, és időtartama, amelyhez az ügyfél rendelkezik hozzáféréssel egy adott eszközre. Vegye figyelembe, hogy a lista engedéllyel (AccessPermissions.List) nem lehet létrehozni egy OnDemandOrigin lokátort.

Lokátorok lejárati dátummal rendelkezik. Az Azure Portalon a jövőben 100 éves lejárati dátummal keresők állítja be.

> [!NOTE]
> Ha az Azure Portalon 2015 márciusa előtt hozzon létre a lokátorokat, ezeket a lokátorokat beállított két év után lejár.
> 
> 

A lokátor lejárati idejének módosításához használjon [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) vagy [.NET](https://go.microsoft.com/fwlink/?LinkID=533259) API-t. Ne feledje, hogy a SAS-lokátor lejárati idejének módosításával az URL-cím is megváltozik.

Lokátorok nem felhasználónkénti hozzáférés-vezérlés kezelésére tervezték. Az egyes felhasználóknak különböző hozzáférési jogosultságokat biztosíthat a digitális jogkezelési (DRM) megoldásokat. További információkért lásd: [média védelme](https://msdn.microsoft.com/library/azure/dn282272.aspx).

Amikor létrehoz egy keresőt, 30 másodperces késleltetést szükséges tárolási és az Azure Storage-propagálás folyamatok miatt lehet.

## <a name="adaptive-streaming"></a>Az adaptív streamelés
Az adaptív bitsebességű technológiák lehetővé teszik a hálózati körülmények meghatározásához, és válassza ki a több bitsebességre való átkódolása videólejátszó alkalmazások. Hálózati kommunikáció csökkenésekor az ügyfél választhat egy alacsonyabb sávszélességű, lejátszás továbbra is az alacsonyabb jó minőségű. Hálózati körülmények javításához, az ügyfél válthat egy magasabb átviteli sebesség, a továbbfejlesztett videó minősége. Az Azure Media Services a következő adaptív sávszélességű technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming és MPEG-DASH.

Streamelési URL-címek biztosíthatja a felhasználók számára, hogy először hozzon létre egy OnDemandOrigin lokátort. A lokátor létrehozása biztosít az alapútvonal adatfolyam kívánt tartalmat tartalmazó objektumot. Azonban, hogy ezt a tartalmat streamelni, szüksége további az elérési út módosítása. A teljes URL-címe a streamelési jegyzékfájlt létrehozására, a lokátor elérési útjának értéke és a jegyzékfájlt (filename.ism) kell összefűzni fájl nevét. Majd fűzze **/jegyzékfájl** és a egy megfelelő formátumú (ha szükséges) a lokátor elérési utat.

> [!NOTE]
> SSL-kapcsolaton keresztül is streamelheti a tartalmat. Ehhez ellenőrizze, hogy a streamelési URL-címek indítsa el a HTTPS. Vegye figyelembe, hogy jelenleg AMS nem támogatja az SSL egyéni tartománnyal rendelkező.  
> 

Csak akkor streamelheti SSL-en keresztül, ha a streamvégpontra, amelyről a tartalomkézbesítés 2014. szeptember 10-után jött létre. Az URL-cím tartalmazza-e a streamelési URL-címek alapján a 2014. szeptember 10-én után létrehozott streamelési végpontok a "streaming.mediaservices.windows.net." Streamelési URL-címek, amelyek tartalmazzák a "origin.mediaservices.windows.net" (a régi formátumot) nem támogatja az SSL. Ha a régi formátumot az URL-címe van, és képesek lesznek streamelni az SSL-en keresztül szeretne, hozzon létre egy új streamvégpont. Az új streamvégpont alapján URL-címek segítségével a tartalmak streamelésére SSL-en keresztül.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Streamelési URL-formátumok

### <a name="mpeg-dash-format"></a>MPEG-DASH formátumban
{Stream végpont neve-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Az Apple HTTP Live Streaming (HLS) V4 formátumban
{Stream végpont neve-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Az Apple HTTP Live Streaming (HLS) V3 formátum
{Stream végpont neve-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>A csak hangfájlt tartalmazó szűrővel Apple HTTP Live Streaming (HLS) formátumban
Alapértelmezés szerint csak számok szerepelnek a HLS jegyzékfájlt. Ez a kapcsolat mobilhálózati az Apple Store minősítésre szükséges. Ebben az esetben ha egy ügyfél nem rendelkezik elegendő sávszélesség, vagy 2G-kapcsolaton keresztül kapcsolódik, a lejátszás vált, amennyiben csak. Ez segít megőrizni a tartalomközvetítéshez pufferelés nélkül, de nincs videó van. Bizonyos esetekben pufferelés player lehet előnyben részesített csak keresztül. Ha el kívánja távolítani a csak hangfájlt tartalmazó nyomon követése, vegye fel **csak = false** az URL-címre.

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,audio-only=false)

További információkért lásd: [dinamikus Manifest összeállítás támogatási és HLS kimeneti további funkciók](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Smooth Streaming formátumban
{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

Példa:

http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest

### <a id="fmp4_v20"></a>Smooth Streaming 2.0 jegyzékfájlt (örökölt jegyzékfájl)
Alapértelmezés szerint a jegyzékfájl formátuma Smooth Streaming tartalmazza, ismételje meg a címke (az r-kód). Egyes lejátszók azonban nem támogatja az r-címkét. Ezek lejátszókkal ügyfelek használhatják a formátum, amely letiltja az r-kód:

{Stream végpont neve-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Progresszív letöltés
A progresszív letöltés megkezdése játszott adathordozó, mielőtt a teljes fájlt letöltötte. Ön nem tudja fokozatosan .ism * (ismv, isma, ismt vagy ismc) fájlok letöltése.

Fokozatosan letölteni a tartalmat, használja a kereső OnDemandOrigin típusát. Az alábbi példa bemutatja a lokátor OnDemandOrigin típusa alapján URL-címe:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Storage-titkosítású eszközök, adatfolyam-forrás a szolgáltatás progresszív letöltés kívánt vissza kell fejtenie.

## <a name="download"></a>Letöltés
Töltse le a tartalmat egy ügyféleszközön, létre kell hoznia egy SAS-kereső. Az SAS-kereső hozzáférést biztosít az Azure Storage-tárolóba, ahol a fájl megtalálható-e. A letöltési URL-cím összeállítását, akkor a fájl nevét, a gazdagép és a SAS-aláírás közötti beágyazási.

Az alábbi példa bemutatja az URL-cím, amely a SAS-kereső alapul:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

A következő szempontokat kell figyelembe venni:

* Storage-titkosítású eszközök, adatfolyam-forrás a szolgáltatás progresszív letöltés kívánt vissza kell fejtenie.
* Még nem fejeződött be 12 órán belül letöltések sikertelen lesz.

## <a name="streaming-endpoints"></a>Streamvégpontok

A streamvégpont jelöli a streamelési szolgáltatás, amely tartalmat továbbít közvetlenül az ügyfél lejátszóalkalmazásába, vagy a content delivery network (CDN) további terjesztés céljából. Az a végpont streamelési szolgáltatás kimenő adatfolyam élő stream és a egy video-on-demand eszközintelligencia, a Media Services-fiók is lehetnek. Streamvégpontok, két típusa van **standard** és **prémium**. További információk: [Streamvégpontok áttekintése](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

## <a name="known-issues"></a>Ismert problémák
### <a name="changes-to-smooth-streaming-manifest-version"></a>Smooth Streaming módosításai manifest verzió
Előtt a 2016. júliusi kiadás – Ha a Media Encoder Standard által előállított eszközök Media Encoder Premium munkafolyamat, vagy a korábbi Azure Media Encoder is streamelt dinamikus csomagolási--a Smooth Streaming használatával visszaadott jegyzékfájl verzió volna felel meg 2.0-s. A 2.0-töredék időtartamok ne használja az úgynevezett Ismétlés (r) címkéket. Példa:

<?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" n="0" />
            <c d="2000" />
            <c d="2000" />
            <c d="2000" />
        </StreamIndex>
    </SmoothStreamingMedia>

A 2016. július szolgáltatáskiadások a Smooth Streaming létrehozott jegyzékre megfelel, 2.2-es verzió töredék percentilisénél hosszabb időtartamú, ismételje meg a címkék használatával. Példa:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

Néhány örökölt Smooth Streaming klienssel ismételje meg a címkék nem támogatja, és nem tölthető be a jegyzékfájlban. A probléma megoldásához használhatja az örökölt a jegyzékfájl formátuma paraméter **(formátum fmp4-v20 =)** vagy az ügyfél frissítése a legújabb verzióra, amely támogatja a címkék ismételje meg a műveletet. További információkért lásd: [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[Lokátorok a Media Services frissítése tárelérési kulcsok váltása után](media-services-roll-storage-access-keys.md)

