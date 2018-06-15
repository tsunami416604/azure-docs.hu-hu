---
title: Az ügyfél számára a tartalom továbbítása |} Microsoft Docs
description: Ez a témakör áttekintést mi részt vesz a szerinti tartalomtovábbítás az Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2017
ms.author: juliako
ms.openlocfilehash: 815aae57af93b0e4870bd9f61da248e4be328db4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23860807"
---
# <a name="deliver-content-to-customers"></a>Továbbítanak tartalmat az ügyfél számára
Ha az ügyfél számára rendelkezik a streaming vagy videotartalom tartalmat, célja, hogy a jó minőségű videó kézbesíthet különböző hálózati körülmények között különféle eszközök.

E cél eléréséhez a következőket teheti:

* Az adatfolyamot többféle bitrátájúvá (adaptív sávszélességűvé) video-adatfolyamot kódolása. Ez a minőségi és hálózati körülményekhez kezeli.
* Használja a Microsoft Azure Media Services [dinamikus becsomagolás](media-services-dynamic-packaging-overview.md) dinamikusan őket csomagolni az adatfolyam különböző protokollokat. Ez az adatfolyam a különböző eszközökön kezeli. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: <br/>
    * **HTTP Live Streaming** (HLS) - hozzáadása "(formátum = m3u8-aapl)" elérési út az URL-cím állapítható meg, hogy az adatfolyam-továbbítási eredeti kiszolgálóra visszaadandó hátsó HLS tartalom felhasználása "/ Manifest" részéhez **Apple iOS** (a részleteket, natív eszköz Lásd: [keresők](#locators) és [URL-címek](#URLs)),
    * **MPEG-DASH** -hozzáadása "(formátum = mpd-idő-csf)" elérési út az URL-CÍMÉT, hogy az adatfolyam-továbbítási eredeti kiszolgálóra való visszatéréshez "/ Manifest" részéhez biztonsági MPEG-DASH (további információkért lásd: [keresők](#locators) és [URL-címek](#URLs) ),
    * **Smooth Streaming**.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

Ez a cikk áttekintést fontos tartalomkézbesítési fogalmakat.

Ismert problémák ellenőrzéséhez tekintse meg a [ismert problémák](media-services-deliver-content-overview.md#known-issues).

## <a name="dynamic-packaging"></a>Dinamikus csomagolás
A dinamikus csomagolás, hogy a Media Services nyújt, a Media Services (MPEG-DASH, HLS, Smooth Streaming) által támogatott streamformátumok adaptív sávszélességű MP4 vagy Smooth Streaming-kódolású tartalmak biztosíthat anélkül, hogy kellene őket csomagolni ezekbe a streamformátumokba. Azt javasoljuk, hogy a dinamikus becsomagolás révén a tartalmak továbbításával.

A dinamikus csomagolás előnyeinek kódolja a mezzanine (forrás) fájlt az adaptív sávszélességű MP4-fájlokká vagy Smooth Streaming-fájlsorozattá kell.

A dinamikus csomagolás tárolja, és a fájlok egyetlen tárolási formátumban kell fizetnie. A Media Services elkészíti és kiszolgálja az a kérésnek megfelelő választ.

A dinamikus csomagolás standard és premium adatfolyam-végpontok érhető el. 

További információkért lásd: [dinamikus becsomagolás](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Szűrők és dinamikus jegyzékfájlokban
A Media Services eszközök szűrőket adhat meg. Ezek a szűrők és kiszolgálóoldali szabályok, amelyeket az ügyfelek számára, például egy adott részének videó lejátszása vagy a hang- és interpretációk, amelyet a felhasználói eszköz kezelni tud (az összes a interpretációk társított adategységet) helyett egy részét. A szűrés sorrendekben *dinamikus jegyzékfájlokban* , amely jönnek létre, ha az ügyfél alapján videó adatfolyam kér, vagy több megadott szűrőket.

További információkért lásd: [szűrőket és dinamikus jegyzékfájlokban](media-services-dynamic-manifest-overview.md).

## <a name="a-idlocatorslocators"></a><a id="locators"/>Keresők
Ahhoz, hogy a felhasználó továbbításához vagy a tartalom letöltésére használható URL-címet, először tegye közzé az adategységet egy kereső létrehozásával. Egy kereső biztosít egy belépési pont egy eszköz tárolt fájlok eléréséhez. A Media Services két lokátortípust támogat:

* OnDemandOrigin keresők. Ezek a médiaadatfolyam (például MPEG-DASH, HLS vagy Smooth Streaming) segítségével, vagy fokozatosan letölteni a fájlokat.
* Közös hozzáférésű jogosultságkód (SAS) URL-cím lokátorokat. Ezek használhatók a helyi számítógépen médiafájlok letöltéséhez.

Egy *házirendhez* definiálja az engedélyek (például az olvasási, írási és lista) és az időtartamot, amelynek az ügyfél rendelkezik hozzáféréssel egy adott eszközre. Vegye figyelembe, hogy az a lista engedélyt (AccessPermissions.List) nem használható egy OrDemandOrigin kereső létrehozása.

Keresők lejárati dátummal rendelkezik. Az Azure-portálon a jövőben 100 éves lejárati dátumot beállítja a lokátorokat.

> [!NOTE]
> Ha az Azure-portál használatával hoz létre keresőket 2015. márciusi előtt, ezeket a lokátorokat beállított két év után lejár.
> 
> 

A lokátor lejárati idejének módosításához használjon [REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) vagy [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API-t. Ne feledje, hogy a SAS-lokátor lejárati idejének módosításával az URL-cím is megváltozik.

Keresők nem tervezték, hogy a felhasználói hozzáférés-vezérlés kezelése. Különböző hozzáférési jogosultsága ahhoz, hogy egyes felhasználók biztosíthat a digitális tartalomvédelmi (DRM) megoldásokat. További információkért lásd: [biztonságossá tétele Media](http://msdn.microsoft.com/library/azure/dn282272.aspx).

Ha egy kereső létrehozása miatt szükséges tárolási és az Azure Storage propagálás folyamatok 30 másodperces késés előfordulhat.

## <a name="adaptive-streaming"></a>Adaptív adatfolyam
Adaptív sávszélességű technológiák lehetővé teszik a hálózati feltételek meghatározására, és válassza ki a több bitrates videólejátszó alkalmazások. Csökkenti a hálózati kommunikáció, amikor az ügyfél kiválaszthatja egy alacsonyabb sávszélességű így lejátszási alacsonyabb videominőséget folytatása. Javíthatja a hálózati feltételek mellett, mivel az ügyfél egy nagyobb átviteli sebesség a továbbfejlesztett videominőséget válthat. Az Azure Media Services a következő adaptív sávszélességű technológiákat támogatja: HTTP Live Streaming (HLS), Smooth Streaming vagy MPEG-DASH.

Streamelési URL-címek biztosít a felhasználók, akkor először hozzon létre egy OnDemandOrigin lokátort. A lokátor létrehozása lehetővé teszi az alap elérési útja az eszközhöz, amely tartalmazza a tartalmat továbbítani kívánja. Azonban ahhoz, hogy adatfolyamként küldje el ezt a tartalmat, módosítania további az elérési út. A teljes URL-címet a folyamatos átviteli jegyzékfájl létrehozásához a lokátor elérési út érték és a jegyzékfájl (filename.ism) kell összefűzésére fájl nevét. Majd fűzze **/Manifest** és a lokátor elérési útja megfelelő formátumot (ha szükséges).

> [!NOTE]
> Is SSL-kapcsolaton keresztül adatfolyam formájában a tartalmat. Ehhez ellenőrizze, hogy a streamelési URL-címének HTTPS kezdődhet. Vegye figyelembe, hogy jelenleg AMS nem támogatja az SSL az egyéni tartomány.  
> 

Akkor is csak adatfolyam SSL-en keresztül Ha a streamvégpontján, amelyről a tartalmat továbbít a 2014. szeptember 10 után készült. Ha 2014. szeptember 10. után létrehozott streamvégpontok alapul a streamelési URL-címek URL-CÍMÉT tartalmazza-e a "streaming.mediaservices.windows.net." Adatfolyam-továbbítási URL-címek, amelyek tartalmazzák a "origin.mediaservices.windows.net" (a régi formátumot) nem támogatja az SSL. Ha a régi formátumban kell megadni az URL-címet, és képesek lesznek streamelni az SSL-en keresztül szeretné, hozzon létre egy új streamvégpontra. Az új streamvégpont alapján URL-címek használatával a adatfolyamként SSL-en keresztül.

## <a name="a-idurlsstreaming-url-formats"></a><a id="URLs"/>Adatfolyam-továbbítási URL-formátumokra

### <a name="mpeg-dash-format"></a>MPEG-DASH-formátum
{streaming endpoint név-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=mpd-Time-CSF)

### <a name="apple-http-live-streaming-hls-v4-format"></a>Apple HTTP Live Streaming (HLS) V4 formátumban
{streaming endpoint név-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) V3 formátumban
{streaming endpoint név-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl-v3)

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming (HLS) formátum csak szűrővel
Alapértelmezés szerint csak számok szerepelnek a HLS jegyzékben. Ez azért szükséges, az Apple Store tanúsításhoz cellás hálózatokhoz. Ebben az esetben ha egy ügyfél nem rendelkezik elegendő sávszélesség, vagy 2/g. kapcsolaton keresztül csatlakozik, a lejátszás vált csak. Ez elősegíti a pufferelés nélkül adatfolyamként, de nincs videó van. Bizonyos esetekben pufferelés player lehet előnyben részesített csak keresztül. Ha el szeretné távolítani a csak nyomon követése, vegye fel **csak = false** URL-címét.

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest(Format=m3u8-aapl-v3,audio-only=FALSE)

További információkért lásd: [dinamikus jegyzékfájl létrehozása támogatási és HLS kimeneti további funkciók](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Smooth Streaming formátumban
{stream végpontjának neve-Media Services fiók neve}.streaming.mediaservices.windows.net/{kereső azonosítója}/{fájlnév}.ism/Manifest

Példa:

http://testendpoint-testaccount.Streaming.mediaservices.Windows.NET/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a id="fmp4_v20"></a>Smooth Streaming 2.0 jegyzékfájl (örökölt jegyzékfájl)
Alapértelmezés szerint a jegyzékfájl formátuma Smooth Streaming tartalmaz ismétlődő címke (r-kód). Néhány lejátszó azonban nem támogatja az r-kód. Ezeket az ügyfelek használhatják az r-tag letiltása formátuma:

{streaming endpoint név-media services fiók name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=fmp4-v20)

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)

## <a name="progressive-download"></a>Progresszív letöltés
Progresszív letöltés el lehet indítani játszott media, mielőtt a teljes fájl be van töltve. Nem tudja fokozatosan .ism * (ismv, isma, ismt vagy ismc) fájlok letöltése.

Töltse le fokozatosan a tartalmat, használja a lokátor OnDemandOrigin típusú. A következő példa bemutatja a lokátor OnDemandOrigin típusú alapuló URL-címe:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

Tárolási titkosított eszközök, az eredeti szolgáltatásból a progresszív letöltés adatfolyamként történő küldéséhez használni kívánt vissza kell fejtenie.

## <a name="download"></a>Letöltés
Töltse le a tartalmat egy ügyféleszközön, létre kell hoznia egy SAS-kereső. A SAS-kereső hozzáférést biztosít az Azure Storage-tároló amennyiben a fájl nem található. Hozhat létre a letöltési URL-címet, akkor a fájl nevét, a gazdagép és a SAS-aláírás közötti beágyazása.

A következő példa bemutatja az URL-címet, amely a SAS-kereső alapul:

    https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D

A következők érvényesek:

* Tárolási titkosított eszközök, az eredeti szolgáltatásból a progresszív letöltés adatfolyamként történő küldéséhez használni kívánt vissza kell fejtenie.
* Egy letöltést, amely még nem fejeződött be 12 órában sikertelen lesz.

## <a name="streaming-endpoints"></a>Streamvégpontok

A streamvégpont egy adatfolyam-szolgáltatás által biztosított tartalom közvetlenül egy ügyfélalkalmazás player vagy későbbi terjesztés tartalomkézbesítési hálózat (CDN) jelöli. A kimenő adatfolyam adatfolyam-továbbítási végpont szolgáltatásból egy élő adatfolyam vagy a Media Services-fiók egy video-on-demand eszköz lehet. Adatfolyam-végpontok, két típusa van **szabványos** és **prémium**. További információkért lásd: [Streaming végpontok áttekintése](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

## <a name="known-issues"></a>Ismert problémák
### <a name="changes-to-smooth-streaming-manifest-version"></a>Smooth Streaming módosításai manifest verziója
– 2016. július szolgáltatás kiadása előtt, amikor a Media Encoder Standard által előállított eszközök Media Encoder prémium munkafolyamat, vagy a korábbi Azure Media Encoder továbbítva lettek a dinamikus becsomagolás--a Smooth Streaming visszaadott jegyzékfájl 2.0-s verziójának volna felelnek meg. A 2.0-s verzióját töredék időtartamok ne használja az úgynevezett Ismétlés (r) címkék. Példa:

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

2016. július szolgáltatás kiadása a Smooth Streaming létrehozott jegyzékre megfelel verzióra 2.2, töredék időtartamok ismétlődő címkék használatával. Példa:

    <?xml version="1.0" encoding="UTF-8"?>
    <SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
        <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
            <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
            <c t="0" d="2000" r="4" />
        </StreamIndex>
    </SmoothStreamingMedia>

A Smooth Streaming tanúsítványbeléptetési némelyike esetleg nem támogatja az ismétlődő címkék, és nem tölthető be a jegyzékfájl. A probléma orvoslása érdekében használhatja a hagyományos jegyzékfájl formátuma paraméter **(formátum = fmp4-v20)** vagy az ügyfél frissítése a legújabb verzióra, amely támogatja az ismétlődő címkék. További információkért lásd: [Smooth Streaming 2.0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Kapcsolódó témakörök
[A Media Services keresők frissítése után működés közbeni tárolási kulcsok](media-services-roll-storage-access-keys.md)

