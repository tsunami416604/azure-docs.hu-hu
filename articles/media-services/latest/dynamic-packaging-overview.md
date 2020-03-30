---
title: Dinamikus csomagolás az Azure Media Services v3-as részén
titleSuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt az Azure Media Services dinamikus csomagolásáról.
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
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: ae049d7486007696d8038eb4e6593cf996df659e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372604"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Dinamikus csomagolás a Media Services v3-ban

A Microsoft Azure Media Services számos médiaforrás-fájlformátum kódolására használható. Különböző streamelési protokollokon keresztül biztosítja őket, tartalomvédelemmel vagy anélkül, hogy elérjék az összes főbb eszközt (például az iOS és az Android eszközöket). Ezek az ügyfelek különböző protokollokat értenek. Az iOS például megköveteli, hogy az adatfolyamokat HTTP Live Streaming (HLS) formátumban kell kézbesíteni, és az Android-eszközök támogatják a HLS-t és az MPEG DASH-t.

A Media Services szolgáltatásban a [streamelési végpont](streaming-endpoint-concept.md) egy dinamikus (just-in-time) csomagolási és származási szolgáltatást jelent, amely az élő és az igény szerinti tartalmat közvetlenül egy ügyféllejátszó alkalmazásnak juttatja el. A következő szakaszban említett gyakori adatfolyam-protokollok egyikét használja. A dinamikus csomagolási funkciót minden Streamvégpont tartalmazza (a standard és a prémium szintűek egyaránt).

> [!NOTE]
> Az Azure [Portal](https://portal.azure.com/) segítségével kezelheti a v3-as [élő eseményeket,](live-events-outputs-concept.md)tekintse meg a v3 [Assets nézetet,](assets-concept.md)és információkat kaphat az API-k eléréséről. Az összes többi felügyeleti feladathoz (például átalakítások és feladatok) használja a [REST API-t,](https://docs.microsoft.com/rest/api/media/)a [CLI-t](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK-k egyikét.](media-services-apis-overview.md#sdks)

## <a name="to-prepare-your-source-files-for-delivery"></a><a id="delivery-protocols"/>A forrásfájlok előkészítése a kézbesítésre

A dinamikus csomagolás előnyeinek kihasználásához a mezzanine (forrás) fájlt több bitráta MP4 (ISO Base Media 14496-12) fájlokba kell [kódolnia.](encoding-concept.md) A Media Services dinamikus csomagolásához szükséges tartalommal rendelkeznie kell egy [eszközre.](assets-concept.md) Ebből az MP4-fájlkészletből a Dynamic Packaging segítségével az alábbiakban ismertetett adatfolyam-protokollokon keresztül juttathatja el a videókat.

> [!TIP]
> Az EGYIK módja annak, hogy az MP4 és streaming konfigurációs fájlokat, hogy [kódolja a félemeleten fájlt Media Services](#encode-to-adaptive-bitrate-mp4s). 

Ahhoz, hogy a kódolt eszközben lévő videókat elérhetővé tegye az ügyfelek számára a lejátszáshoz, létre kell hoznia egy [streamelési lokátort,](streaming-locators-concept.md) és streamelési URL-eket kell létrehoznia. Ezután a streamelési ügyféljegyzékben megadott formátum (HLS, MPEG DASH vagy Smooth Streaming) alapján a kiválasztott protokollban kapja meg az adatfolyamot.

Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Ha a tartalom védelmét a Media Services dinamikus titkosításával kívánja védeni, olvassa el a [Streamelési protokollok és titkosítási típusok (Streamelési protokollok és titkosítási típusok) témakört.](content-protection-overview.md#streaming-protocols-and-encryption-types)

### <a name="hls-protocol"></a>HLS protokoll

A streamelt ügyfél a következő HLS-formátumokat adhatja meg:

|Protocol (Protokoll)|Példa|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

### <a name="mpeg-dash-protocol"></a>MPEG-DASH protokoll

A streamelési ügyfél a következő MPEG-DASH formátumokat adhatja meg:

|Protocol (Protokoll)|Példa|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>Sima streamelési protokoll

A streamelt ügyfél a következő Smooth Streaming formátumokat adhatja meg:

|Protocol (Protokoll)|Megjegyzések/példák| 
|---|---|
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|Sima streamelés 2.0 (örökölt jegyzékfájl)|Alapértelmezés szerint a Smooth Streaming jegyzékfájl formátuma tartalmazza az ismétlődő címkét (r-tag). Egyes játékosok azonban nem `r-tag`támogatják a . Az ilyen lejátszóval rendelkező ügyfelek olyan formátumot használhatnak, amely letiltja az r-címkét:<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> A Smooth Streaming használatához mind a hang, mind a videó jelen kell lennie az adatfolyamban.

## <a name="on-demand-streaming-workflow"></a>Igény szerinti streamelési munkafolyamat

A következő lépések egy közös Media Services streamelési munkafolyamatot mutatnak be, ahol a dinamikus csomagolást az Azure Media Services standard kódolójával együtt használják.

1. Töltsön fel egy bemeneti fájlt, például egy QuickTime/MOV vagy MXF fájlt. Ez a fájl is nevezik a mezzanine vagy forrás fájlt. A támogatott formátumok listáját a Szabványos kódoló által támogatott formátumok című témakörben [tetszésszerint.](media-encoder-standard-formats.md)
1. [Kódolja](#encode-to-adaptive-bitrate-mp4s) a félemeleti fájlt egy H.264/AAC MP4 adaptív bitráta készletbe.
1. Tegye közzé az adaptív sávszélességű MP4-készletet tartalmazó kimeneti eszközt. A streamelési lokátor létrehozásával teheti közzé.
1. Különböző formátumokat (HLS, MPEG-DASH és Smooth Streaming) célzó URL-eket hozhat létre. A **streamelési végpont** gondoskodna a megfelelő jegyzékfájl és a különböző formátumokra vonatkozó kérések kiszolgálásáról.

Az alábbi ábrán az igény szerinti streamelés dinamikus csomagolási munkafolyamattal látható.

![Munkafolyamat diagramja az igény szerinti streameléshez dinamikus csomagolással](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

### <a name="encode-to-adaptive-bitrate-mp4s"></a>Kódolás adaptív sávszélességű MP4-ekhez

Az alábbi cikkek példákat mutatnak be [arra, hogyan kódoljon egy videót a Media Services szolgáltatással:](encoding-concept.md)

* [Https-URL-címről kódolva beépített készletek használatával.](job-input-from-http-how-to.md)
* [Helyi fájl kódolása beépített készletek kel.](job-input-from-local-file-how-to.md)
* [Hozzon létre egy egyéni készletet az adott forgatókönyv vagy eszközkövetelmények célzásához.](customize-encoder-presets-how-to.md)

Tekintse meg a szabványos [kódolóformátumok és kodekek](media-encoder-standard-formats.md)listáját.

## <a name="live-streaming-workflow"></a>Élő közvetítési munkafolyamat

Az élő esemény beállítható *áthaladásra* (egy helyszíni élő kódoló több bitráta-adatfolyamot küld) vagy *élő kódolásra* (a helyszíni élő kódoló egyetlen átviteli adatfolyamot küld). 

Íme egy gyakori munkafolyamat az élő streameléshez a dinamikus csomagolással:

1. [Hozzon](live-events-outputs-concept.md)létre egy élő eseményt .
1. A betöltési URL-cím betöltése és a helyszíni kódoló konfigurálása az URL-cím használatával küldje el a hozzájárulási hírcsatornát.
1. Az előnézeti URL-cím leérkezésével ellenőrizheti, hogy a kódoló bemenete érkezik-e.
1. Hozzon létre egy új eszközt.
1. Hozzon létre egy élő kimenetet, és használja a létrehozott eszköznevet.<br />Az élő kimenet archiválja a streamet az eszközbe.
1. Hozzon létre egy streamelési lokátort a beépített streamelési házirend-típusokkal.<br />Ha titkosítani kívánja a tartalmat, tekintse át a [Tartalomvédelem áttekintését.](content-protection-overview.md)
1. Sorolja fel a streamelési lokátor elérési útjait az URL-címek használatának lenyomásához.
1. A streamelési végpont állomásnevének beszereznie, amelyből streamelni szeretne.
1. Különböző formátumokat (HLS, MPEG-DASH és Smooth Streaming) célzó URL-eket hozhat létre. A streamelési végpont gondoskodik a megfelelő jegyzékfájl és a különböző formátumok kérései kiszolgálásáról.

Ez az ábra a dinamikus csomagolással rendelkező élő streamelés munkafolyamatát mutatja:

![Munkafolyamat diagramja átmenő kódoláshoz dinamikus csomagolással](./media/live-streaming/pass-through.svg)

A Media Services 3-as v3-as szolgáltatásában az élő közvetítésről az [Élő közvetítés áttekintése című témakörben](live-streaming-overview.md)olvashat.

## <a name="video-codecs-supported-by-dynamic-packaging"></a>A dinamikus csomagolás által támogatott videokodekek

A Dynamic Packaging támogatja a [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC vagy AVC1) vagy [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 vagy hvc1) kódolású videót tartalmazó MP4-fájlokat.

> [!NOTE]
> Dinamikus csomagolással akár 4K felbontást és akár 60 képkocka/másodperc képkocka sebességet is teszteltünk. A [prémium kódoló](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) támogatja a H.265-re történő kódolást az örökölt v2 API-kon keresztül.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a><a id="audio-codecs"/>A dinamikus csomagolás által támogatott hangkodekek

A Dynamic Packaging támogatja a következő protokollokkal kódolt hangot:

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1 vagy HE-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Továbbfejlesztett AC-3 vagy E-AC3)
* Dolby Atmos<br />
   A Dolby Atmos-tartalom streamelése olyan szabványok esetében támogatott, mint az MPEG-DASH protokoll, amely vagy a Common Streaming Format (CSF) vagy a Common Media Application Format (CMAF) töredezett MP4, valamint a HTTP Live Streaming (HLS) protokollon keresztül a CMAF-fel.

* [Dts](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   A DASH-CSF, DASH-CMAF, HLS-M2TS és HLS-CMAF csomagolási formátumok által támogatott DTS kodekek a következők:  

    * DTS digitális térre (dtsc)
    * DTS-HD nagy felbontású és DTS-HD master audio (dtsh)
    * DTS Express (dtse)
    * DTS-HD Veszteségmentes (nincs mag) (dtsl)

A Dynamic Packaging több hangsávot támogat DASH vagy HLS (4-es vagy újabb verzió) segítségével olyan streamelési eszközök esetén, amelyek több kodeket és nyelvet tartalmazó hangsávot is tartalmazóak.

### <a name="additional-notes"></a>További megjegyzések

A Dinamikus csomagolás nem támogatja a [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) hangot tartalmazó fájlokat (ez egy örökölt kodek).

> [!NOTE]
> A [prémium kódoló](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset#media-encoder-premium-workflow) támogatja a Dolby Digital Plus kódolását az örökölt v2 API-kon keresztül.

## <a name="manifests"></a>Rakományjegyzék

A Media Services dinamikus csomagolásában a HLS, az MPEG-DASH és a Smooth Streaming streamelési adatfolyamok dinamikusan jönnek létre az URL-cím formátumválasztója alapján.  

A jegyzékfájl olyan adatfolyam-metaadatokat tartalmaz, mint a műsorszám típusa (hang, videó vagy szöveg), a műsorszám neve, a kezdési és befejezési idő, a bitráta (tulajdonságok), a műsoridő, a bemutatóablak (rögzített időtartamú csúszóablak) és a videokodek (FourCC). Arra is utasítja a játékost, hogy a következő töredéket szerezze be a következő rendelkezésre álló lejátszható videotöredékekről és azok helyéről. A töredékek (vagy szegmensek) a videotartalom tényleges "részei".

### <a name="examples"></a>Példák

#### <a name="hls"></a>HLS

Íme egy példa egy HLS jegyzékfájlra, más néven EGY HLS mesterlejátszási listára: 

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

#### <a name="mpeg-dash"></a>MPEG-DASH

Íme egy példa egy MPEG-DASH jegyzékfájlra, más néven MPEG-DASH médiabemutató-leírásra (MPD):

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
#### <a name="smooth-streaming"></a>Smooth Streaming

Íme egy példa a Smooth Streaming jegyzékfájlra:

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

### <a name="naming-of-tracks-in-the-manifest"></a>A jegyzékben lévő sávok elnevezése

Ha az .ism fájlban meg van adva egy `Label` hangsáv `AdaptationSet` neve, a Media Services hozzáad egy elemet az adott hangsáv szöveges adatainak megadásához. Példa a kimeneti DASH-jegyzékfájlra:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

A lejátszó az `Label` elem segítségével megjelenítheti a felhasználói felületét.

### <a name="signaling-audio-description-tracks"></a>A hangleírásának jelzése

Kísérőszöveg-sávot adhat a videóhoz, hogy a látássérült ügyfelek a kísérőszöveg meghallgatásával kövessék a videofelvételt. Egy hangsávot kell jegyzeteket jegyzetelnie a jegyzékben. Ehhez adja hozzá a "kisegítő lehetőségek" és a "szerep" paramétereket az .ism fájlhoz. Az Ön felelőssége, hogy ezeket a paramétereket helyesen állítsa be, hogy a hangsávot hangleírásként jelezze. Például adja `<param name="accessibility" value="description" />` `<param name="role" value="alternate"` hozzá és adja hozzá az .ism fájlt egy adott hangsávhoz. 

További információt a [Példaa Leíró hangsáv jelzése](signal-descriptive-audio-howto.md) című témakörben talál.

#### <a name="smooth-streaming-manifest"></a>Sima streamelési jegyzék

Ha sima streamelési adatfolyamot játszik le, a jegyzékfájl értékeket `Accessibility` és `Role` attribútumokat hordoz az adott hangsávhoz. Például `Role="alternate" Accessibility="description"` az elem bekerül az `StreamIndex` elembe, jelezve, hogy ez egy hangleírás.

#### <a name="dash-manifest"></a>DASH-jegyzékfájl

A DASH jegyzékfájl esetében a következő két elem jelenik meg a hangleírás jelzéséhez:

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS lejátszási lista

A HLS v7 `(format=m3u8-cmaf)`és újabb `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"` , a lejátszási lista járna, ha a hang leírás sáv jelzésre.

#### <a name="example"></a>Példa

További információ: [A hangleírás-sávok jelzése.](signal-descriptive-audio-howto.md)

## <a name="dynamic-manifest"></a>Dinamikus jegyzékfájl

A lejátszóknak küldött zeneszámok, formátumok, bitráták és bemutató-időablakok számának szabályozásához dinamikus szűrést használhat a Media Services dinamikus csomagolójával. További információ: [A jegyzékek előszűrése a dinamikus csomagolóval](filters-dynamic-manifest-overview.md)című témakörben található.

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

A *dinamikus titkosítással* dinamikusan titkosíthatja élő vagy igény szerinti tartalmait az AES-128 vagy a három fő digitális jogkezelési (DRM) rendszer bármelyikével: a Microsoft PlayReady, a Google Widevine és az Apple FairPlay rendszerekkel. A Media Services szolgáltatást is biztosít az AES-kulcsok és DRM-licencek engedélyezett ügyfelek nek történő kézbesítéséhez. További információt a [dinamikus titkosítás](content-protection-overview.md)című témakörben talál.

> [!NOTE]
> A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="more-information"></a>További információ

Tekintse meg [az Azure Media Services-közösségből,](media-services-community.md) hogy megtekintse a kérdések et, visszajelzéseket és frissítéseket kaphat a Media Services szolgáltatásról.

## <a name="need-help"></a>Segítségre van szüksége?

A támogatási jegyet az [Új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)való navigálással nyithatja meg.

## <a name="next-steps"></a>További lépések

[Videók feltöltése, kódolása és streamelése](stream-files-tutorial-with-api.md)
