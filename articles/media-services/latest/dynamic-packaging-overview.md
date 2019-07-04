---
title: Az Azure Media Services dinamikus becsomagolást áttekintő |} A Microsoft Docs
description: A cikk áttekintést nyújt a dinamikus csomagolás az Azure Media Servicesben.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: 4836ec4bb66bbf8ced921dd1095665d004f8a28b
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542574"
---
# <a name="dynamic-packaging"></a>Dinamikus csomagolás

A Microsoft Azure Media Services is használható, hogy sok adatforrás fájl médiaformátumok, adatfolyam-továbbítási formátumokba, media, és a content protection formátumok számos különböző ügyfél-technológiák (például iOS- és XBOX). Ezek az ügyfelek különböző protokollok ismertetése, például iOS-HTTP Live Streaming (HLS) formátumban és Xbox igényelnek, Smooth Streaming igényel-e. Ha rendelkezik egy adaptív sávszélességű (többszörös sávszélességű MP4) készletét MP4 (ISO alap 14496-12) médiafájlok vagy egy adaptív sávszélességű Smooth Streaming-fájlsorozattá szolgálja ki, hogy ismerje a HLS, MPEG DASH és Smooth Streaming-ügyfelek kívánt, akkor kihasználhatja  *a dinamikus csomagolás*. A csomagolási képernyőfelbontást független, SD/HD/UHD - 4K támogatottak.

A Media Services szolgáltatásban egy [folyamatos átviteli végponton](streaming-endpoint-concept.md) egy dinamikus (just-in-time) csomagolás és a forrás szolgáltatás, amely az élő és igény szerinti tartalmat továbbít közvetlenül az ügyfél lejátszóalkalmazásába, az egyik gyakori streamelési jelöli Media protokollok (HLS vagy DASH). A dinamikus csomagolás funkciója, az összes standard előre **adatfolyam-továbbítási végpontok** (Standard vagy prémium). 

A dinamikus csomagolás kihasználásához, szüksége lesz egy **eszköz** az adaptív sávszélességű MP4-fájlokat és a Media Services dinamikus becsomagolást szükséges folyamatos átviteli konfigurációs fájlokat. A fájlok létrehozásának egyik módja a mezzanine (forrás) fájlok kódolása a Media Services használatával. A videók a kódolt objektumhoz az elérhetővé tenni az ügyfelek számára a lejátszás, létre kell hoznia egy **Streamelési lokátor** és létrehozása a streamelési URL-címek. Ezt követően a streaming (HLS, DASH vagy Smooth) ügyfél jegyzékfájlban megadott formátumnak megfelelően, Önnek a streamet a kiválasztott protokollal.

Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ. 

A Media Services szolgáltatásban a dinamikus csomagolás e élő vagy igény szerinti folyamatos átvitel szolgál. 

> [!NOTE]
> Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](media-services-apis-overview.md#sdks).

## <a name="on-demand-streaming-workflow"></a>Igény szerinti adatfolyam-továbbítási munkafolyamat

A következő általános munkafolyamatát, a Media Services igényalapú streaming a dinamikus csomagolás használatával:

1. Egy bemeneti vagy a forrás-fájl feltöltése (nevű egy *mezzanine* fájlt). Például egy MP4, MOV vagy MXF fájlt. 
1. Kódolja a mezzanine-fájlt a H.264 MP4 adaptív sávszélességű csoportok. 
1. Tegye közzé az adategységet, amely tartalmazza az adaptív sávszélességű MP4 típusú beállításkészlettel. A streamelési lokátorok létrehozásával tesz közzé.
1. Hozhat létre, amelyek különböző formátumokban (HLS, MPEG-DASH és Smooth Streaming) cél URL-címeket. A streamvégpont a megfelelő jegyzékfájl és a kéréseket a különböző formátumokban a kiszolgáló gondoskodik.

Ez az ábra bemutatja, a munkafolyamat az igény szerinti folyamatos átvitel, a dinamikus csomagolás használatával:

![Igény szerinti folyamatos átvitel, a dinamikus csomagolás használatával egy munkafolyamat ábrája](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="live-streaming-workflow"></a>Élő adatfolyam-továbbítási munkafolyamat

Élő esemény két típus egyike lehet: csatlakoztatott vagy élő kódolás. 

Itt látható az élő adások online közvetítése a dinamikus csomagolási közös munkafolyamat:

1. Hozzon létre egy [élő esemény](live-events-outputs-concept.md).
1. Betöltés URL-címére, és állítsa be az URL-címet használja a hírcsatorna-hozzájárulás küldése a helyszíni kódolót.
1. Előnézeti URL-címére, és a segítségével ellenőrizheti, hogy a kódoló a bemeneti fogadja.
1. Hozzon létre egy új eszközt.
1. Hozzon létre egy élő kimenet és az Ön által létrehozott objektum nevét.<br />Az élő kimeneti a stream az objektumba archiválja.
1. A beépített adatfolyam szabályzattípusok a streamelési lokátorok létrehozásához.<br />Ha azt tervezi, a tartalmak, tekintse át a [Content protection áttekintése](content-protection-overview.md).
1. Az elérési beolvasásához használandó URL-címeket a streamelési lokátorok listája.
1. Kérje le a gazdagép nevét, a stream a kívánt streamvégpontra.
1. Hozhat létre, amelyek különböző formátumokban (HLS, MPEG-DASH és Smooth Streaming) cél URL-címeket. A streamvégpont a megfelelő jegyzékfájl és a kéréseket a különböző formátumokban a kiszolgáló gondoskodik.

Ez az ábra bemutatja, a munkafolyamat az élő streameléshez a dinamikus csomagolás használatával:

![A dinamikus csomagolás használatával csatlakoztatott Encoding munkafolyamat diagramja](./media/live-streaming/pass-through.svg)

A Media Services v3 élő streameléssel kapcsolatos további információkért lásd: [élő streamelés – áttekintés](live-streaming-overview.md).

## <a name="delivery-protocols"></a>Kézbesítési protokollra

A következő kézbesítési protokollok tartalmait a Media Services dinamikus becsomagolást is használhat:

|Protocol|Példa|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="encode-to-adaptive-bitrate-mp4s"></a>Kódolása az adaptív sávszélességű MP4

A következő cikkekben talál példát [kódolása a Media Services-videó](encoding-concept.md):

* [Kódolás HTTPS URL-címet a beépített készlet használatával](job-input-from-http-how-to.md)
* [Helyi fájl kódolása beépített készlet használatával](job-input-from-local-file-how-to.md)
* [Az egyedi, amelyekre az adott forgatókönyv vagy eszközkövetelmények készletek létrehozása](customize-encoder-presets-how-to.md)

Media Encoder Standard listájának megtekintéséhez [formátumai és kodekei](media-encoder-standard-formats.md).

## <a name="video-codecs"></a>Videókodekek

A dinamikus csomagolás a következő videokodekek támogatja:
* MP4-fájlokat, amelyek tartalmazhat videót, a kódolt [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC vagy AVC1) vagy [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1, vagy hvc1).

## <a name="audio-codecs"></a>Hangkodekek

A dinamikus csomagolás a következő hang protokollokat támogatja:
* Mp4-fájlok
* Több hangsáv

Nem támogatja a dinamikus csomagolás tartalmazó fájlokat [Dolby digitális](https://en.wikipedia.org/wiki/Dolby_Digital) (nem örökölt kodekkel) (AC3) hang.

### <a name="mp4-files"></a>Mp4-fájlok

A dinamikus csomagolás MP4-fájlokat, amelyek tartalmazzák a következő protokollokkal kódolt hang támogatja: 

* [Az AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, az AAC-HE v1 vagy v2 az AAC-HE)
* [Dolby digitális plusz](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (továbbfejlesztett AC-3-as vagy E-AC3)
* Dolby Atmos<br />
   Dolby Atmos tartalmak online lejátszásához szabványok, például az MPEG-DASH-protokoll gyakori Streamelési formátum (CSF) vagy a közös Media alkalmazás formátum (CMAF) töredékes MP4, vagy via HTTP Live Streaming (HLS) rendelkező CMAF esetén támogatott.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DTS kodekek DASH-CSF, a DASH-CMAF, a HLS-M2TS és a HLS-CMAF csomagolási formátumot támogatja a következők:  

    * DTS digitális legyen (dtsc)
    * DTS-HD nagy felbontású és DTS-HD fő hang (dtsh)
    * DTS Express (dtse)
    * DTS-HD veszteségmentes (nincs mag) (dtsl)

### <a name="multiple-audio-tracks"></a>Több hangsáv

A dinamikus csomagolás HLS kimeneti (4 vagy újabb verzió) az eszközök, amelyek több hangsávval rendelkező több kodekeket és nyelvek streameléshez többszörös hangsáv támogatja.

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

Használhat *a dinamikus titkosítás* dinamikusan titkosítani az AES-128, vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek élő vagy igény szerinti tartalom: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. A Media Services az AES-kulcsok és a DRM-licencek kézbesítéséhez az arra jogosult ügyfelek szolgáltatást is nyújt. További információkért lásd: [a dinamikus titkosítás](content-protection-overview.md).

## <a name="manifest-examples"></a>Példák manifest 
 
A Media Services dinamikus csomagolást, HLS, MPEG-DASH és Smooth Streaming streamelési ügyfél jegyzékfájljainak dinamikusan jönnek létre a formátum választó az URL-cím alapján. További információkért lásd: [kézbesítési protokollra](#delivery-protocols). 

A jegyzékfájlt tartalmaz, például a szövegsáv típusát (hang, videó vagy szöveges) metaadatainak streaming, nyomon követheti a neve, kezdési és befejezési időpontja, sávszélességű (Tulajdonságok), nyomon követése nyelvek, bemutató ablak (csúszóablakon rögzített időtartamának) és videó kodek (FourCC). A arra utasítja a Windows Media player beolvasni a következő részlet azáltal, hogy a következő lejátszható videó töredék elérhető és azok helyétől kapcsolatos információkat is. Szilánk (vagy szegmensek) olyan a tényleges "" videóját.

### <a name="hls"></a>HLS

Íme egy példa egy HLS jegyzékfájl, más néven az HLS fő lista: 

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

### <a name="mpeg-dash"></a>MPEG-DASH

Íme egy példa egy MPEG-DASH jegyzékfájl, más néven az MPEG-DASH Media bemutató leírása (MPD):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
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

Íme egy példa, Smooth Streaming jegyzékfájl:

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

## <a name="dynamic-manifest"></a>A dinamikus manifest

Nyomon követi, a formátumok, a bitsebességre való átkódolása és a játékosok megismerése által küldött bemutató idő windows számának szabályozására, használhatja a Media Services dinamikus packager a dinamikus szűrés. További információkért lásd: [előre szűrés a dinamikus packager az alkalmazásjegyzékeket](filters-dynamic-manifest-overview.md).

## <a name="more-information"></a>További információ

Tekintse meg [Azure Media Services-Közösség](media-services-community.md) különböző módon tehet fel kérdéseket, küldje el visszajelzését, és tudnivalók a Media Services-frissítések megtekintéséhez.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [feltöltése, kódolása és streamelése videók](stream-files-tutorial-with-api.md).

