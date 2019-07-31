---
title: Azure Media Services dinamikus csomagolás áttekintése | Microsoft Docs
description: A cikk áttekintést nyújt a Azure Media Services dinamikus csomagolásáról.
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
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 5979e34e7c186a0484c8db2d432a3c57a5ed1d15
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679156"
---
# <a name="dynamic-packaging"></a>Dinamikus csomagolás

A Microsoft Azure Media Services számos különböző ügyfél-technológiához (például iOS és XBOX) biztosíthatja a médiafájl-formátumok, a média-adatfolyam-formátumok és a tartalomkezelési formátumok széles körének továbbítását. Ezek az ügyfelek különböző protokollokat ismernek, például az iOS megköveteli a HTTP Live Streaming (HLS) formátumot, és az Xbox megköveteli a Smooth Streaming. Ha az adaptív sávszélességű (többszörös sávszélességű) MP4 (ISO Base Media 14496-12) fájlokkal vagy az adaptív sávszélességű Smooth Streaming fájlokkal rendelkezik, amelyeket a HLS, az MPEG DASH vagy a Smooth Streaming megértéséhez szeretne használni, akkor kihasználhatja a *dinamikus Csomagolás*. A csomagolás az SD/HD/UHD-4K által támogatott videó-felbontáshoz is használható.

Media Services a [folyamatos átviteli végpontok](streaming-endpoint-concept.md) egy dinamikus (igény szerinti) csomagolási és forrás-szolgáltatást jelentenek, amely az élő és az igény szerinti tartalmat közvetlenül egy ügyfél-lejátszó alkalmazás számára teszi elérhetővé az egyik közös Streaming Media Protocol (HLS vagy KÖTŐJEL). A dinamikus csomagolás egy olyan szolgáltatás, amely minden **streaming végponton** (standard vagy prémium) szabványos. 

A dinamikus csomagolás kihasználása érdekében az adaptív sávszélességű MP4-fájlokkal és a streaming konfigurációs fájlokkal (. ISM,. ISMC,. MPI stb.) rendelkező [eszközzel](assets-concept.md) kell rendelkeznie. A fájlok létrehozásának egyik módja a mezzanine (forrás) fájlok kódolása a Media Services használatával. Ahhoz, hogy a kódolt eszközön a videók elérhetők legyenek az ügyfelek számára a lejátszáshoz, létre kell hoznia egy [folyamatos átviteli lokátort](streaming-locators-concept.md) és a streaming URL-címeket. Ezután a streaming Client manifest (HLS, DASH vagy Smooth) megadott formátuma alapján a kiválasztott protokollban megkapja az adatfolyamot.

Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ. 

A Media Services dinamikus csomagolást használ, függetlenül attól, hogy élő vagy igény szerinti folyamatos átvitelre van-e szükség. 

> [!NOTE]
> Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](media-services-apis-overview.md#sdks).

## <a name="on-demand-streaming-workflow"></a>Igény szerinti folyamatos átviteli munkafolyamat

Az alábbi gyakori munkafolyamat a dinamikus csomagolással rendelkező Media Services igény szerinti folyamatos átvitelhez:

1. Töltsön fel egy bemeneti vagy forrásfájl (úgynevezett *köztes* fájl). Ilyenek például az MP4, a MOV vagy a MXF fájl. 
1. Kódolja a köztes fájlt H. 264 MP4 adaptív sávszélességű készletbe. 
1. Tegye közzé az adaptív sávszélességű MP4-készletet tartalmazó kimeneti eszközt. Egy adatfolyam-kereső létrehozásával teheti közzé.
1. Különböző formátumokat (HLS, MPEG-DASH és Smooth Streaming) célzó URL-címeket hozhat létre. A folyamatos átviteli végpont gondoskodik a megfelelő jegyzékfájl és kérések kiszolgálásáról a különböző formátumokban.

Ez az ábra az igény szerinti folyamatos átvitel munkafolyamatát mutatja dinamikus csomagolással:

![Az igény szerinti folyamatos átvitelre szolgáló munkafolyamat ábrája dinamikus csomagolással](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

### <a name="encoding-to-adaptive-bitrate-mp4s"></a>Kódolás az adaptív sávszélességű MP4

A következő cikkek példákat mutatnak [be a videók Media Services használatával történő kódolására](encoding-concept.md):

* [Kódolás HTTPS-URL-címről beépített beállításkészletek használatával](job-input-from-http-how-to.md)
* [Helyi fájl kódolása beépített beállításkészletek használatával](job-input-from-local-file-how-to.md)
* [Egyéni beállításkészlet kiépítése az adott forgatókönyv vagy eszköz követelményeinek megcélzásához](customize-encoder-presets-how-to.md)

Tekintse meg Media Encoder Standard [formátumok és kodekek](media-encoder-standard-formats.md)listáját.

## <a name="live-streaming-workflow"></a>Élő adatfolyam-továbbítási munkafolyamat

Az élő esemény a következő két típus egyike lehet: átmenő vagy élő kódolás. 

Az alábbi gyakori munkafolyamat a dinamikus csomagolással folytatott élő közvetítéshez használható:

1. Hozzon létre egy [élő eseményt](live-events-outputs-concept.md).
1. Szerezze be a betöltési URL-címet, és konfigurálja a helyszíni kódolót úgy, hogy az URL-cím használatával küldje el a hozzájárulási csatornát.
1. Szerezze be az előnézeti URL-címet, és annak ellenőrzéséhez, hogy a rendszer beolvassa-e a kódolótól érkező adatokat.
1. Hozzon létre egy új eszközt.
1. Hozzon létre egy élő kimenetet, és használja a létrehozott eszköz nevét.<br />Az élő kimenet archiválja a streamet az eszközre.
1. Hozzon létre egy streaming-keresőt a beépített folyamatos átviteli házirend-típusokkal.<br />Ha titkosítani szeretné a tartalmakat, tekintse át a [tartalomvédelem áttekintését](content-protection-overview.md).
1. Listázza a streaming-lokátor elérési útját a használandó URL-címek lekéréséhez.
1. Szerezze be annak a streaming-végpontnak az állomásnevét, amelyről streamet szeretne továbbítani.
1. Különböző formátumokat (HLS, MPEG-DASH és Smooth Streaming) célzó URL-címeket hozhat létre. A folyamatos átviteli végpont gondoskodik a megfelelő jegyzékfájl és kérések kiszolgálásáról a különböző formátumokban.

Ez az ábra az élő adatfolyamok dinamikus csomagolással való működésének munkafolyamatát mutatja be:

![A dinamikus csomagolással rendelkező, átmenő kódolásra szolgáló munkafolyamat ábrája](./media/live-streaming/pass-through.svg)

További információ a Media Services v3 élő közvetítéséről: [élő közvetítés – áttekintés](live-streaming-overview.md).

## <a name="delivery-protocols"></a>Kézbesítési protokollok

Ezeket a kézbesítési protokollokat Media Services dinamikus csomagolásban használhatja a tartalomhoz:

|Protocol|Példa|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## <a name="delivery-codecs-support"></a>Kézbesítési kodekek támogatása 

### <a name="video-codecs"></a>Videós kodekek

A dinamikus csomagolás a következő video-kodekeket támogatja:
* MP4-fájlok, amelyek [h. 264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC vagy AVC1) vagy [h. 265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 vagy hvc1) kódolású videókat tartalmaznak.

### <a name="audio-codecs"></a>Hangkodekek

A dinamikus csomagolás a következőkben ismertetett hangprotokollokat támogatja:

* MP4-fájlok
* Több zeneszám

A dinamikus csomagolás nem támogatja a [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) hanganyagot tartalmazó fájlokat (ez egy örökölt kodek).

#### <a name="mp4-files"></a>MP4-fájlok

A dinamikus csomagolás támogatja a következő protokollokkal kódolt hanganyagot tartalmazó MP4-fájlokat: 

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, it-AAC v1 vagy it-AAC v2)
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Bővített AC-3 vagy E-AC3)
* Dolby Atmos<br />
   A folyamatos átviteli Dolby Atmos-tartalom olyan szabványok esetében támogatott, mint például az MPEG-DASH protokoll a Common Streaming Format (CSF) vagy a Common Media Application Format (CMAF) darabolt MP4-vel, valamint a (z) HTTP Live Streaming (HLS) és a CMAF használatával.

* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   A DASH-CSF, DASH-CMAF, HLS-M2TS és HLS-CMAF csomagolási formátumok által támogatott DTS-kodekek a következők:  

    * DTS digitális surround (dtsc)
    * DTS-HD nagy felbontású és DTS-HD Master hang (dtsh)
    * DTS Express (dtse)
    * DTS-HD veszteségmentes (nincs mag) (DTSL)

#### <a name="multiple-audio-tracks"></a>Több zeneszám

A dinamikus csomagolás támogatja a több hangsávot a HLS-kimenet (4-es vagy újabb verzió) számára több, több kodekkel és nyelvtel rendelkező hangsávokkal rendelkező adatfolyam-továbbítási eszköz számára.

## <a name="manifests"></a>Jegyzékek 
 
Media Services dinamikus csomagolásban a HLS, MPEG-DASH és Smooth Streaming adatfolyam-ügyfél-jegyzékfájlok dinamikusan jönnek létre az URL-cím kiválasztó alapján. További információ: kézbesítési [protokollok](#delivery-protocols). 

A jegyzékfájlok olyan adatfolyam-metaadatokat tartalmaznak, mint például a követés típusa (hang, videó vagy szöveg), a nyomon követési idő, a kezdő és a befejező időpont, a bitráta (Tulajdonságok), a nyelvek nyomon követése, a megjelenítési időszak (rögzített időtartamú ablak) és a video codec (FourCC). Arra is utasítja a lejátszót, hogy a következő töredéket olvassa be a rendelkezésre álló, valamint a helyükre tartozó következő lejátszható videó-töredékek adatainak megadásával. A töredékek (vagy szegmensek) a videotartalom tényleges "darabjai".

### <a name="examples"></a>Példák

#### <a name="hls"></a>HLS

Íme egy példa egy HLS manifest-fájlra, más néven HLS fő lejátszási listára: 

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

Íme egy példa egy MPEG-DASH manifest-fájlra, más néven MPEG-DASH Media Presentation Description (MPD):

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

Példa Smooth Streaming manifest-fájlra:

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

### <a name="naming-of-tracks-in-the-manifest"></a>A jegyzékben szereplő számok elnevezése

Ha meg van adva egy hangsáv neve az. ISM fájlban, Media Services egy `Label` elemet ad hozzá az a `AdaptationSet` -n belül, hogy megadja az adott hangsávokhoz tartozó anyagminta-információkat. Példa a kimeneti kötőjel jegyzékfájlra:

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

A lejátszó használhatja az `Label` elemet a felhasználói felületen való megjelenítéshez.

### <a name="signaling-audio-description-tracks"></a>A hangjelzések leírásának nyomon követése

Az ügyfél hang-és hangfelvételi leírást adhat a jegyzékfájlban. Ehhez hozzá kell adnia a "kisegítő" és "szerepkör" paramétereket az. ISM-fájlhoz. A Media Services felismeri a hangleírást, ha egy hangsáv "Description" értékkel rendelkezik, és a "role" kifejezés "alternate" értékkel rendelkezik. Ha Media Services észleli a hang leírását az. ISM fájlban, a rendszer a hangleírási adatokat átadja az ügyfél `Accessibility="description"` jegyzékfájljának, és `StreamIndex` `Role="alternate"` attribútumokat ad a elemnek.

Ha az "accessibility" = "Description" és a "role" = "póttag" kombinációja be van állítva. ISM fájlban van beállítva, a kötőjel és a simított jegyzékfájl a "hozzáférhetőség" és "szerepkör" paraméterekben beállított értékeket adja meg. Az ügyfél felelőssége, hogy ezt a két értéket állítsa be jobbra, és a hangfelvételt hangleírásként jelöli meg. /KÖTŐJEL spec, "accessibility" = "Description" és "role" = "póttag" együttesen azt jelenti, hogy a hangsáv hangleírása.

A HLS v7 és újabb`format=m3u8-cmaf`verziók esetében a `CHARACTERISTICS="public.accessibility.describes-video"` lista csak akkor használható, ha az "accessibility" = "Description" és a "role" = "póttag" kombinációja. ISM fájlban van beállítva. 

## <a name="dynamic-manifest"></a>Dinamikus jegyzékfájl

A játékosok számára eljuttatott zeneszámok, formátumok, bitráták és a bemutató időpontok számának szabályozásához dinamikus szűrést használhat a Media Services dinamikus csomagoló használatával. További információkért lásd: [a dinamikus csomagolóval kapcsolatos előzetes szűrési jegyzékek](filters-dynamic-manifest-overview.md).

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

A *dinamikus titkosítás* használatával az élő vagy igény szerinti tartalmakat az AES-128 vagy a három nagy digitális jogkezelési (DRM) rendszerű rendszeren keresztül dinamikusan titkosíthatja: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services egy szolgáltatást is biztosít az AES-kulcsok és a DRM-licencek engedélyezésére a hitelesítő ügyfelek számára. További információ: [dinamikus titkosítás](content-protection-overview.md).

## <a name="more-information"></a>További információ

Tekintse meg [Azure Media Services közösségét](media-services-community.md) , hogy különböző módokon lásson kérdéseket, visszajelzést küldjön, és frissítéseket kérjen a Media Servicesról.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [tölthet fel, kódolhat és továbbíthat videókat](stream-files-tutorial-with-api.md).

