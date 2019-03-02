---
title: Az Azure Media Services dinamikus becsomagolást áttekintő |} A Microsoft Docs
description: A témakör áttekintést nyújt a dinamikus csomagolás, a Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: juliako
ms.openlocfilehash: c16a3ff28eff18b1638fbdbac2282b690093783e
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244927"
---
# <a name="dynamic-packaging"></a>Dinamikus csomagolás

A Microsoft Azure Media Services is használható, hogy sok adatforrás fájl médiaformátumok, adatfolyam-továbbítási formátumokba, media, és a content protection formátumok számos különböző ügyfél-technológiák (például iOS- és XBOX). Ezek az ügyfelek különböző protokollok ismertetése, például iOS-HTTP Live Streaming (HLS) formátumban és Xbox igényelnek, Smooth Streaming igényel-e. Ha rendelkezik egy adaptív sávszélességű (többszörös sávszélességű MP4) készletét MP4 (ISO alap 14496-12) médiafájlok vagy egy adaptív sávszélességű Smooth Streaming-fájlsorozattá szolgálja ki, hogy ismerje a HLS, MPEG DASH és Smooth Streaming-ügyfelek kívánt, igénybe veheti a dinamikus Csomag. A csomagolási képernyőfelbontást független, SD/HD/UHD - 4K támogatottak.

[Streamvégpontok](streaming-endpoint-concept.md) a médiatartalmak eredményjelző ügyfél lejátszók Media Services dinamikus csomagolási szolgáltatás. A dinamikus csomagolás funkciója, az összes standard előre **adatfolyam-továbbítási végpontok** (Standard vagy prémium). Részeként van ennek a funkciónak a Media Services v3 kapcsolódó költségek. 

Kihasználásához **dinamikus csomagolási**, szüksége lesz egy **eszköz** az adaptív sávszélességű MP4-fájlokat és a Media Services dinamikus becsomagolást szükséges folyamatos átviteli konfigurációs fájlokat. Kódolja a mezzanine (forrás) fájlt, a Media Services egyik módja a fájlok lekérése az. A videók a kódolt objektumhoz az elérhetővé tenni az ügyfelek számára a lejátszás, létre kell hoznia egy **Streamelési lokátor** és létrehozása a streamelési URL-címek. Ezt követően a streaming (HLS, DASH vagy Smooth) ügyfél jegyzékfájlban megadott formátumnak megfelelően, Önnek a streamet a kiválasztott protokollal.

Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ. 

A Media Services szolgáltatásban a dinamikus csomagolás e élő vagy igény szerinti folyamatos átvitel szolgál. Az alábbi ábrán látható, a dinamikus csomagolás munkafolyamat igényalapú streameléshez.

![A dinamikus csomagolás](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>Közös videó igény szerinti munkafolyamat

Az alábbiakban látható egy közös Media Services adatfolyam-továbbítási munkafolyamat, a dinamikus csomagolás szolgál.

1. Töltse fel egy bemeneti fájlt (úgynevezett mezzanine-fájlt). Ha például H.264, MP4 vagy WMV (a támogatott formátumok listáját lásd: [a Media Encoder Standard által támogatott formátumok](media-encoder-standard-formats.md).
2. Kódolja a mezzanine-fájlt a H.264 MP4 adaptív sávszélességű csoportok.
3. Tegye közzé az adategységet, amely tartalmazza az adaptív sávszélességű MP4 típusú beállításkészlettel. Tegye közzé létrehozásával egy **Streamelési lokátor**.
4. Hozhat létre, amelyek különböző formátumokban (HLS, Dash és Smooth Streaming) cél URL-címeket. A **folyamatos átviteli végponton** lenne gondoskodik a megfelelő jegyzékfájlban és a kérelmek ezeket különböző formátumokban kiszolgáló.

## <a name="encode-to-adaptive-bitrate-mp4s"></a>Kódolása az adaptív sávszélességű MP4

További információ [kódolása a Media Services-videó](encoding-concept.md), lásd az alábbi példákat:

* [Kódolás a beépített készlet használatával HTTPS URL-címet](job-input-from-http-how-to.md)
* [Beépített készlet használatával egy helyi fájl kódolása](job-input-from-local-file-how-to.md)
* [Az egyedi, amelyekre az adott forgatókönyv vagy eszközkövetelmények készletek létrehozása](customize-encoder-presets-how-to.md)

Media Encoder Standard-formátumok és -kodekek listáját lásd: [formátumai és kodekei](media-encoder-standard-formats.md)

## <a name="delivery-protocols"></a>Kézbesítési protokollra

|Protokoll|Példa|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="video-codecs-supported-by-dynamic-packaging"></a>A dinamikus becsomagolás által támogatott videókodekek

Támogatja a dinamikus csomagolás MP4-fájlokat, amelyek tartalmazzák a videó kódolású [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC vagy AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 vagy hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>A dinamikus becsomagolás által támogatott hangkodekek

A dinamikus csomagolás támogatja az MP4-fájlokat, amelyek tartalmazzák a hang a kódolt [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 az AAC-HE), [Dolby digitális Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 vagy E-AC3), vagy [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, veszteségmentes DTS HD).

> [!NOTE]
> A dinamikus csomagolás nepodporuje tartalmazó fájlokat [Dolby digitális](https://en.wikipedia.org/wiki/Dolby_Digital) (nem örökölt kodekkel) (AC3) hang.

## <a name="manifests"></a>Jegyzékek 
 
A Media Services támogatja a HLS, MPEG DASH, Smooth Streaming protokollokat. Részeként **dinamikus csomagolási**, a streaming (HLS fő lista, DASH Media bemutató leírása (MPD) és Smooth Streaming) ügyfél jegyzékek dinamikus jönnek létre a formátum választó az URL-cím alapján. Tekintse meg a kézbesítési protokollok [ebben a szakaszban](#delivery-protocols). 

A jegyzékfájlt tartalmaz, például a folyamatos átviteli metaadatok: nyomon követheti a típusa (hang, videó vagy szöveg), nyomon követheti a nevét, a kezdési és befejezési idő, a sávszélességű (Tulajdonságok), a nyomon követése nyelven, bemutató ablakban (csúszóablakban rögzített időtartama), a videó kodek (FourCC). A arra utasítja a Windows Media player beolvasni a következő részlet azáltal, hogy a következő lejátszható videó szilánkok érhető el, és azok helyétől kapcsolatos információkat is. Szilánk (vagy szegmensek) olyan a tényleges "" a videó tartalmát.

### <a name="hls-master-playlist"></a>HLS Master Playlist

Íme egy példa egy HLS-jegyzékfájl: 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="dash-media-presentation-description-mpd"></a>DASH Media bemutató leírása (MPD)

Íme egy példa a DASH-jegyzékfájl:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
### <a name="smooth-streaming"></a>Smooth Streaming

Íme egy példa a Smooth Streaming jegyzékfájl:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```
## <a name="next-steps"></a>További lépések

[Feltöltés, kódolás, stream-videók](stream-files-tutorial-with-api.md)

