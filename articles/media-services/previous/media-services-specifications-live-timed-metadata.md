---
title: Az Azure Media Services - jelzés időzített metaadatok jelzése az élő Streamelés |} A Microsoft Docs
description: Ez az meghatározás Media Services élő streamelési belül jelképző időzített metaadatok által támogatott kétféle vázolja fel. Ez az általános időzített metaadatok jelek, valamint SCTE – 35 splice beillesztését számára jelzés támogatását tartalmazza.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: johndeu;
ms.openlocfilehash: 89a19d53046afd8d2b16b23508e952989091c8d2
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005267"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Jelzés időzített metaadatok jelzése az élő Streamelés 


## <a name="1-introduction"></a>1. bevezetés 
A hirdetményeket vagy egyéni események ügyfél lejátszón, gyakran műsorszolgáltatók beszúrási márka elősegítése érdekében időzített metaadatok felhasználása a videó beágyazva. Ahhoz, hogy ezeket a forgatókönyveket, a Media Services támogatást nyújt az adathordozót, a betöltés pontról az ügyfélalkalmazásnak az élő adatfolyam-továbbítási csatorna együtt időzített metaadatok átvitelét.
Ez specifikáció körvonalak kétféle időzített metaadatok belül a Media Services által támogatott élő streamelési jelek:

1. [SCTE – 35], [SCTE-67-es] által leírt ajánlott eljárásokat heeds jelzés

2. Egy általános időzített metaadatok mód, které nejsou jelzés [SCTE – 35]

### <a name="12-conformance-notation"></a>1.2 megfelelési jelöléssel
A kulcs szavak "kell", "Nem", "Kötelező", "SHALL", "NOT kell", "SHOULD", "NOT kell", "Ajánlott", "MAY" és "nem" kötelező ebben a dokumentumban vannak RFC 2119 leírtak szerint kell értelmezni

### <a name="13-terms-used"></a>1.3 használt kifejezések

| Időtartam              | Meghatározás                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bemutató idő | Az esemény megjelenik egy megjelenítőt időpontja. Az idő a pillanatban, hogy egy megjelenítőt jelennének meg az esemény az adathordozó idősoron jelöli. Ha például a bemutató SCTE – 35 splice_info() parancs üzenet ideje a splice_time(). |
| Érkezés időpontja      | Az az idő, egy üzenet érkezik. Az idő az általában azonos a bemutató az esemény időpontjában, mivel esemény üzeneteket küld az előre a bemutatót az esemény.                                     |
| Ritka nyomon követése      | adathordozó, amely nem folyamatos nyomon követheti, és időt szinkronizálva van egy szülő- vagy nyomon követése.                                                                                                                                    |
| Forrás            | Az Azure Media adatfolyam-szolgáltatás                                                                                                                                                                                                |
| Csatorna fogadó      | Az Azure Media élő adatfolyam-szolgáltatást                                                                                                                                                                                           |
| HLS               | Apple HTTP Live Streaming protokoll                                                                                                                                                                                               |
| DASH              | Dinamikus adaptív Streamelés HTTP-n keresztül                                                                                                                                                                                             |
| Sima            | Zökkenőmentes Streamelési protokoll                                                                                                                                                                                                        |
| MPEG2-TS          | MPEG-2 Transport Streams                                                                                                                                                                                                         |
| RTMP              | Valós idejű multimédiás protokoll                                                                                                                                                                                                    |
| uimsbf            | Előjel nélküli egész számokat legjelentősebb először bit.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>2 időzített metaadatok betöltése
## <a name="21-rtmp-ingest"></a>2.1 RTMP betöltési
RTMP időzített metaadatok jelek AMF köteg üzenet beágyazva az RTMP-stream támogatja. Előfordulhat, hogy a köteg üzeneteket küldött egy kicsit, mielőtt a tényleges esemény, vagy splice beillesztését kell megtörténnie. Ez a forgatókönyv támogatása érdekében a a tényleges idő a splice vagy szegmens található találjuk zajlik. További információkért lásd: [AMF0].

A következő táblázat ismerteti a Media Services rendszer betöltési AMF üzenet hasznos adattartalmából formátumát.

A AMF üzenet neve azonos típusú több esemény-adatfolyam megkülönböztetéséhez használható.  [SCTE – 35]-üzenetek a AMF üzenet kell megadni "onAdCue" [SCTE-67-es] javasolt módon.  A listában nem szereplő mezőket figyelmen kívül kell hagyni, úgy, hogy ez a kialakítás innováció nem gátolt a jövőben.

### <a name="signal-syntax"></a>Jel szintaxis
Media Services RTMP egyszerű módban, egy AMF köteg üzenet a következő formátumú "onAdCue" nevű támogatja:

### <a name="simple-mode"></a>Egyszerű módban

| Mezőnév | Mező típusa | Kötelező? | Leírások                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| Köteg        | String     | Szükséges | Az eseményüzenet.  Kell lennie egy egyszerű módot kijelölése "SpliceOut" splice.                                              |
| id         | String     | Szükséges | A splice vagy szegmens leíró egyedi azonosítója. Ez az üzenet példányát azonosítja.                            |
| időtartam   | Szám     | Szükséges | A splice időtartama. Egységek törtmásodpercek.                                                                |
| elapsed    | Szám     | Optional | A jel támogatása érdekében folyamatban ismételt hallgassa meg, ezt a mezőt a bemutatót, hogy mennyi ideig a splice kezdete óta eltelt kell lennie. Egységek törtmásodpercek. Egyszerű mód használatakor ez az érték nem haladhatja meg a splice eredeti időtartamára.                                                  |
| time       | Szám     | Szükséges | A splice idején bemutató időben kell lennie. Egységek törtmásodpercek.                                     |

---------------------------

### <a name="scte-35-mode"></a>SCTE-35 Mode

| Mezőnév | Mező típusa | Kötelező? | Leírások                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| Köteg        | String     | Szükséges | Az eseményüzenet.  [SCTE – 35]-üzenetek a Base64-kódolású kell lennie (IETF RFC 4648) bináris kódolású splice_info_section() ahhoz, hogy megfelelnek-e [SCTE-67-es] Dash, HLS és Smooth ügyfeleknek küldött üzeneteket.                                              |
| type       | String     | Szükséges | URN vagy URL-cím az üzenet rendszer azonosítása. [SCTE – 35]-üzenetek "urn: scte:scte35:2013a:bin" ahhoz, hogy megfelelnek-e [SCTE-67-es] Dash, HLS és Smooth ügyfeleknek küldött üzeneteket kell lennie.  |
| id         | String     | Szükséges | A splice vagy szegmens leíró egyedi azonosítója. Ez az üzenet példányát azonosítja.  Egyenértékű sémantikou üzenetek ugyanazzal az értékkel rendelkezik.|
| időtartam   | Szám     | Szükséges | Az esemény vagy ad splice-szegmens, ha ismert időtartama. Ismeretlen, ha az érték lehet 0.                                                                 |
| elapsed    | Szám     | Optional | A [SCTE – 35] hirdetési jel esetén ismételt alatt annak érdekében, hogy hallgassa meg, ezt a mezőt kell bemutató a splice kezdete óta eltelt idő mennyisége. Egységek törtmásodpercek. [SCTE – 35] módban Ez az érték haladhatja meg az eredeti megadott időtartam a splice vagy szegmens.                                                  |
| time       | Szám     | Szükséges | Az esemény vagy ad splice bemutató idején.  A bemutató időpontja és időtartama kell igazítani a Stream hozzáférési pontok (SAP) 1 vagy 2, típus meghatározott i. [ISO-14496 – 12]. Kimenő HLS időpontja és időtartama szegmens határokkal kell igazítani. A bemutató idő és az előfizetésen belül különböző eseményüzenetek időtartamát eseménystream nem LEHETNEK egymással átfedésben. Egységek törtmásodpercek.

---------------------------

#### <a name="211-cancellation-and-updates"></a>2.1.1 megszakítás és frissítések

Üzenetek megszakadt, vagy frissítette a több üzenetküldésre ilyen bemutató idő és azonosítóját. A bemutató idő és az azonosító egyedi azonosításához az eseményt, és az utolsó üzenet érkezett, amely megfelel az üzem előtti összegző megkötések meghatározott megjelenítési ideje, hogy van-e bírálni az üzenetet. A frissített esemény váltja fel a korábban fogadott üzeneteket. Az üzem előtti összegző pedig az négy másodperc. Legalább négy másodpercig bemutató időpontja előtt Beérkezett üzenetek intézkedni lesz.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2-es darabolt MP4 feldolgozása (Smooth Streaming)
A követelmények az élő stream betöltési tekintse meg [LIVE-FMP4]. A következő szakaszok a bemutató időzített metaadatok betöltés részletei.  Időzített bemutató metaadatok eseménystreamként ritka nyomon követése, amelyet a mind az élő kiszolgáló Manifest mezőbe (lásd az MS-SSTR) és a film be (moov).  Minden egyes ritka töredék áll egy filmet töredék mezőbe (moof) és a Media Data Box ("mdat"), ahol a "mdat" be-e az üzenet bináris.

#### <a name="221-live-server-manifest-box"></a>2.2.1-es élő kiszolgáló jegyzékfájl mező
A ritka fájlok nyomon követése deklarálni kell a kiszolgáló Manifest élő mezőt egy \<textstream\> bejegyzést, és állítsa be a következő attribútumokkal kell rendelkeznie:

| **Attribútum neve** | **Mező típusa** | **Kötelező?** | **Leírás**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Szám         | Szükséges      | "0", jelezve az ismeretlen, változó bitsebességű nyomon kell lennie.                                                                                                                                                                                                 |
| parentTrackName    | String         | Szükséges      | A szülő pálya, amelyhez a ritka fájlok nyomon követése időkódját igazítva időskálára nevének kell lennie. A szülő nyomon követése nem lehet egy ritka nyomon követése.                                                                                                                    |
| manifestOutput     | Logikai        | Szükséges      | "True", annak jelzésére, hogy a ritka fájlok nyomon követése ágyazva a zökkenőmentes ügyfél jegyzékfájl kell lennie.                                                                                                                                                               |
| Altípus            | String         | Szükséges      | KELL lennie a négy karaktert code "Adatok".                                                                                                                                                                                                                         |
| Séma             | String         | Szükséges      | Kell egy URN vagy URL-cím azonosítására az üzenet sémát. [SCTE – 35]-üzenetek "urn: scte:scte35:2013a:bin" ahhoz, hogy megfelelnek-e [SCTE-67-es] Dash, HLS és Smooth ügyfeleknek küldött üzeneteket kell lennie. |
| TrackName          | String         | Szükséges      | A ritka fájlok nyomon követése a nevének kell lennie. A trackName ugyanazt a sémát használja több esemény-adatfolyamok megkülönböztetéséhez használható. Minden egyedi esemény-adatfolyam követése egyedi névvel kell rendelkeznie.                                                                           |
| időskálára          | Szám         | Optional      | Csökkentse a szülő követése kell lennie.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 ennél rosszabb filmet mezőbe

A film mezőbe (moov) a következő élő kiszolgáló Manifest mezőbe egy ritka nyomon követése a stream fejlécének részeként.

A "moov" mezőben tartalmaznia kell egy **TrackHeaderBox (tkhd)** mezőben definiált [ISO-14496 – 12] a következő korlátozásokkal:

| **Mező neve** | **Mező típusa**          | **Kötelező?** | **Leírás**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| időtartam       | 64 bites, előjel nélküli egész | Szükséges      | 0, kell lennie, mivel a nyomon követése be nulla mintákat, és a minták a nyomon követése a boxban teljes időtartamát 0. |
-------------------------------------

A "moov" mezőben tartalmaznia kell egy **HandlerBox (hdlr)** [ISO-14496 – 12] a következő korlátozások meghatározottak szerint:

| **Mező neve** | **Mező típusa**          | **Kötelező?** | **Leírás**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32 bites, előjel nélküli egész | Szükséges      | 'Meta' kell lennie. |
-------------------------------------

A "stsd" mezőben tartalmaznia kell egy MetaDataSampleEntry kódolási név mezőt, a [ISO-14496 – 12].  SCTE – 35 üzenetek például a kódolási név legyen "scte".

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 ennél rosszabb filmet töredék és Media Data be

Ritka követése töredék egy filmet töredék mező (moof) és a egy Media Data Box (mdat) állnak.

A MovieFragmentBox (moof) tartalmaznia kell egy **TrackFragmentExtendedHeaderBox (uuid)** mezőbe, ahogyan az az [MS-SSTR] a következő mezőket:

| **Mező neve**         | **Mező típusa**          | **Kötelező?** | **Leírás**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64 bites, előjel nélküli egész | Szükséges      | Az esemény érkezési ideje kell lennie. Ezt az értéket a szülő nyomon követése az üzenet igazítja.   |
| fragment_duration      | 64 bites, előjel nélküli egész | Szükséges      | Az esemény időtartama kell lennie. Az időtartam azt jelzi, hogy az időtartam ismeretlen nulla lehet. |

------------------------------------


A MediaDataBox (mdat) mezőben a következő formátumban kell rendelkeznie:

| **Mező neve**          | **Mező típusa**                   | **Kötelező?** | **Leírás**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32 bites, előjel nélküli egész (uimsbf) | Szükséges      | Meghatározza, hogy a "mdat" mező tartalmát formátumát. Ismeretlen verziók figyelmen kívül lesz hagyva. Jelenleg az egyetlen támogatott verzió: 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 bites, előjel nélküli egész (uimsbf) | Szükséges      | Ez az üzenet példányát azonosítja. Üzenetek egyenértékű sémantikou ugyanazt az értéket; kell rendelkeznie. hogy a feldolgozási bármilyen egy esemény üzenetablak ugyanazzal az azonosítóval elegendő.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 bites, előjel nélküli egész (uimsbf) | Szükséges      | A fragment_absolute_time a TrackFragmentExtendedHeaderBox és a presentation_time_delta megadott összege kell lennie a bemutatót az esemény időpontja. A bemutató időpontja és időtartama kell igazítani a Stream hozzáférési pontok (SAP) 1 vagy 2, típus meghatározott i. [ISO-14496 – 12]. Kimenő HLS időpontja és időtartama szegmens határokkal kell igazítani. A bemutató idő és az előfizetésen belül különböző eseményüzenetek időtartamát eseménystream nem LEHETNEK egymással átfedésben. |
| message                 | bájttömb                       | Szükséges      | Az eseményüzenet. [SCTE – 35] üzeneteket az üzenet a bináris splice_info_section() nincs, bár [SCTE-67-es] más javasolja. [SCTE – 35]-üzenetek a splice_info_section() ahhoz, hogy megfelelnek-e [SCTE-67-es] Dash, HLS és Smooth ügyfeleknek küldött üzeneteket kell lennie. [SCTE – 35] üzeneteket a bináris splice_info_section() hasznos a "mdat" mezőbe, és már nem base64-kódolású.                                                            |

------------------------------


### <a name="224-cancellation-and-updates"></a>2.2.4 megszakítás és frissítések
Üzenetek megszakadt, vagy frissítette a több üzenetküldésre ilyen bemutató idő és azonosítóját.  A bemutató idő és az azonosító az esemény egyedi azonosítására. Az utolsó üzenet érkezett, amely megfelel az üzem előtti összegző megkötések, adott bemutató ideig, hogy van-e bírálni az üzenetet. A frissített üzenetet minden korábban fogadott üzenetek váltja fel.  Az üzem előtti összegző pedig az négy másodperc. Legalább négy másodpercig bemutató időpontja előtt Beérkezett üzenetek intézkedni lesz. 


## <a name="3-timed-metadata-delivery"></a>3 időzített metaadatok kézbesítés

Eseményadatok stream nem átlátszó, a Media Services. A Media Services három információt csupán továbbítja a betöltés végpont és az ügyfél végpontja között. A következő tulajdonságokat is továbbítja az ügyfélnek, [SCTE-67-es] megfelelnek-e, és/vagy az ügyfél a streamelési protokoll:

1.  Séma – a URN vagy URL-cím azonosítása a rendszer az üzenet.

2.  Bemutató idő – az esemény az adathordozó idővonalon a bemutató időpontja.

3.  Időtartama – az esemény időtartamát.

4.  Azonosító – egy nem kötelező az esemény egyedi azonosítója.

5.  Message – az eseményadatokat.


## <a name="31-smooth-streaming-delivery"></a>3.1 zökkenőmentes adatfolyamként történő továbbítását

Tekintse meg az [MS-SSTR] alfolyamatot ritka nyomon követése.

#### <a name="smooth-client-manifest-example"></a>Zökkenőmentes ügyfél jegyzékfájl példa
~~~ xml
<?xml version=”1.0” encoding=”utf-8”?>
<SmoothStreamingMedia MajorVersion=”2” MinorVersion=”0” TimeScale=”10000000” IsLive=”true” Duration=”0”
  LookAheadFragmentCount=”2” DVRWindowLength=”6000000000”>

  <StreamIndex Type=”video” Name=”video” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(video={start time})”>
    <QualityLevel Index=”0” Bitrate=”230000”
      CodecPrivateData=”250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <QualityLevel Index=”1” Bitrate=”305000”
      CodecPrivateData=”250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840” FourCC=”WVC1”
      MaxWidth=”364” MaxHeight=”272”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”audio” Name=”audio” Subtype=”” Chunks=”0” TimeScale=”10000000”
    Url=”QualityLevels({bitrate})/Fragments(audio={start time})”>
    <QualityLevel Index=”0” Bitrate=”96000” CodecPrivateData=”1000030000000000000000000000E00042C0”
      FourCC=”WMAP” AudioTag=”354” Channels=”2” SamplingRate=”44100” BitsPerSample=”16” PacketSize=”4459”/>
    <c t=”0” d=”20000000” r=”300” />
  </StreamIndex>
  <StreamIndex Type=”text” Name=”scte35-event-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013a:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- <f> contains base-64 encoded splice_info_section() message 
    <c t=”600000000” d=”300000000”>
<f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia> 
~~~ 

## <a name="32--apple-hls-delivery"></a>3.2-es Apple HLS-kézbesítés
A Szegmens-lista egy egyéni M3U címkében időzített metaadatok az Apple HTTP Live Streaming (HLS) ágyazhatók be.  Az alkalmazási rétegre elemezni a M3U lista és M3U címkék feldolgozására alkalmas. Az Azure Media Services [HLS] meghatározott EXT-X-KÖTEG címkében időzített metaadatok ágyazhat be.  EXT-X-KÖTEG címke jelenleg ADI3 típusú üzenetek DynaMux használják.  SCTE – 35 üzenetek SCTE – 35 és a nem támogatja, állítsa be a EXT-X-KÖTEG címke attribútumait alábbiakban meghatározottak szerint:

| **Attribútum neve** | **Típus**                      | **Kötelező?**                             | **Leírás**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| KÖTEG                | Idézett karakterlánc                 | Szükséges                                  | Az ismertetett módon Base64 kódolású karakterláncként kódolt üzenet [IETF RFC 4648](http://tools.ietf.org/html/rfc4648). [SCTE – 35] üzeneteket ez nem a base64-kódolású splice_info_section().                                                                                                |
| TÍPUS               | Idézett karakterlánc                 | Szükséges                                  | URN vagy URL-cím az üzenet rendszer azonosítása. [SCTE – 35] üzenetek esetében a típus a speciális értéket "scte35" vesz fel.                                                                                                                                |
| ID (Azonosító)                 | Idézett karakterlánc                 | Szükséges                                  | Az esemény egyedi azonosítója. Ha azonosítója nincs megadva, ha az üzenet betöltött, az Azure Media Services egyedi azonosítót hoz létre.                                                                                                                                          |
| IDŐTARTAM           | decimális lebegőpontos szám | Szükséges                                  | Az esemény időtartamát. Ismeretlen, ha az érték lehet 0. Egységek factional másodpercek alatt.                                                                                                                                                                                           |
| ELAPSED            | decimális lebegőpontos szám | Nem kötelező, de szükség szerint eltoltan | A jel ismételt folyamatban van egy bemutató csúszóablakban támogatásához, ezt a mezőt a bemutatót, hogy mennyi ideig az esemény kezdete óta eltelt kell lennie. Egységek törtmásodpercek. Ez az érték haladhatja meg az eredeti megadott időtartam a splice vagy szegmens. |
| TIME               | decimális lebegőpontos szám | Szükséges                                  | A bemutató az esemény időpontja. Egységek törtmásodpercek.                                                                                                                                                                                                                    |


A HLS-lejátszó alkalmazásréteg a típus használatával azonosítására az üzenet formátumát, a-üzenet dekódolása, a alkalmazni a szükséges idő átalakítások és feldolgozni az eseményt.  Az események időt szinkronizálva a szegmens listát, amely a szülő nyomon követése az esemény időbélyegzője megfelelően.  Beillesztés előtt a legközelebbi szegmens (#EXTINF címke).

#### <a name="hls-segment-playlist-example"></a>HLS-szegmens lista példa
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:346
#EXT-X-TARGETDURATION:6
#EXT-X-I-FRAMES-ONLY
#EXT-X-PROGRAM-DATE-TIME:2018-12-13T15:54:19.462Z
#EXTINF:4.000000,no-desc
KeyFrames(video_track=15447164594627600,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447164634627600,format=m3u8-aapl)
#EXT-X-CUE:ID="1026",TYPE="scte35",DURATION=30.000000,TIME=1544716520.022760,CUE="/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w=="
#EXTINF:6.000000,no-desc
KeyFrames(video_track=15447165474627600,format=m3u8-aapl)
~~~

#### <a name="hls-message-handling"></a>HLS-üzenet kezelése

Események jelzésküldés szegmens a listán. az egyes video- és nyomon követése. A pozíció a EXT-X-KÖTEG címke mindig kell lehet azonnal, mielőtt az első HLS szegmens (az splice ki vagy szegmens start), vagy az utolsó HLS szegmens (az a splice vagy a szegmens záró) időpontja és időtartama attribútumainak hivatkozni után azonnal, ahogyan azt az [ HLS].

Ha egy bemutató csúszóablakban engedélyezve van, a EXT-X-KÖTEG címke meg kell ismételni, elég gyakran, hogy a splice vagy szegmens nem mindig részletesen a szegmens listát, és mennyi ideig a splice jelzi a ELTELT attribútumot kell használni, vagy szegmens már aktív, [HLS] igényeinek megfelelően.

Ha engedélyezve van egy bemutató csúszóablakban, EXT-X-KÖTEG címkék a szegmens listából törlődnek, amikor, tekintse meg az adathordozó idő bemutató csúszóablakban ki lett állítva.

## <a name="33--dash-delivery"></a>3.3-as DASH-kézbesítés
[DASH] jel események három módot biztosít:

1.  Az a MPD jelzést események
2.  Események jelzést sávon (esemény üzenet mezőjében (emsg) használatával a Reprezentációban szereplő
3.  1. és 2 egyaránt kombinációja

VOD streaming, mert az ügyfelek hozzáférjenek összes eseményt, azonnal, amikor a MPD jelzést a MPD az események hasznosak. A sávon kívüli a megoldás akkor hasznos, az élő streameléshez, mert az ügyfelek nem kell újra letölteni a MPD. Időalapú szegmentálását az ügyfél határozza meg a következő szegmens az URL-cím az időtartam és a Küldés időbélyegzője legyen az aktuális szegmens hozzáadásával. Amely a kérelem meghiúsul, ha az ügyfél kihagyást feltételezi és letölti a MPD, de egyébként továbbra is fennáll, hogy nem tölti le a MPD streamelési.

Az Azure Media Services fog ne mindkét jelzés a a MPD és sávon jelzés a használatával az esemény üzenet mezőbe.

#### <a name="mpd-signaling"></a>MPD jelzés

Események az EventStream-elemmel, amely akkor jelenik meg, az időszak elemben található MPD a fogja jelezni.

Az EventStream elem a következő tulajdonságokkal rendelkezzen:

| **Attribútum neve** | **Típus**                | **Kötelező?** | **Leírás**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | sztring                  | Szükséges      | A rendszer az üzenet azonosítja. A rendszer a kiszolgáló Manifest élő írja a rendszer attribútum értéke. Az érték lehet egy URN vagy URL-cím az üzenet séma; azonosítása Ha például "urn: scte:scte35:2013a:bin".                                                                |
| érték              | sztring                  | Optional      | Az üzenet szemantikáját testreszabása a tulajdonosok között, a rendszer által használt egy további karakterláncértéket. Annak érdekében, hogy megkülönböztethető több esemény-adatfolyamok ugyanazt a sémát használja, az értéket kell beállítani az eseménystream (betöltési Smooth trackName vagy AMF üzenet neve az RTMP betöltési) nevére. |
| Időskála          | 32 bites, előjel nélküli egész | Szükséges      | A időskálára órajel során végbemenő másodpercenként hányszor és időtartam mezők a "emsg" mezőben található.                                                                                                                                                                                                       |


Nulla vagy több esemény elemek találhatók meg az EventStream elemben található, és a következő attribútumokkal rendelkeznek:

| **Attribútum neve**  | **Típus**                | **Kötelező?** | **Leírás**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | 64 bites, előjel nélküli egész | Optional      | Időnek kell lennie az adathordozó bemutató képest az időszak kezdete esemény. A bemutató időpontja és időtartama kell igazítani a Stream hozzáférési pontok (SAP) 1 vagy 2, típus meghatározott i. [ISO-14496 – 12]. |
| időtartam            | 32 bites, előjel nélküli egész | Optional      | Az esemény időtartamát. Ez az időtartam ismeretlen esetén kell hagyni.                                                                                                                                                 |
| id                  | 32 bites, előjel nélküli egész | Optional      | Ez az üzenet példányát azonosítja. Egyenértékű sémantikou üzenetek ugyanazzal az értékkel rendelkezik. Ha azonosítója nincs megadva, ha az üzenet betöltött, az Azure Media Services egyedi azonosítót hoz létre.             |
| Esemény elemének értéke | sztring                  | Szükséges      | Az eseményüzenet Base64 kódolású karakterláncként leírtak szerint [IETF RFC 4648](http://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>XML-szintaxist és példát a DASH (MPD) Signaling manifest

~~~ xml
<!-- XML Syntax -->
<xs:complexType name=”EventStreamType”>
  <xs:sequence>
    <xs:element name=”Event” type=”EventType” minOccurs=”0” maxOccurs=”unbounded”/>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute ref=”xlink:href”/>
  <xs:attribute ref=”xlink:actuate” default=”onRequest”/>
  <xs:attribute name=”schemeIdUri” type=”xs:anyURI” use=”required”/>
  <xs:attribute name=”value” type=”xs:string”/>
  <xs:attribute name=”timescale” type=”xs:unsignedInt”/>
</xs:complexType>
<!-- Event -->
<xs:complexType name=”EventType”>
  <xs:sequence>
    <xs:any namespace=”##other” processContents=”lax” minOccurs=”0” maxOccurs=”unbounded”/>
  </xs:sequence>
  <xs:attribute name=”presentationTime” type=”xs:unsignedLong” default=”0”/>
  <xs:attribute name=”duration” type=”xs:unsignedLong”/>
  <xs:attribute name=”id” type=”xs:unsignedInt”/>
  <xs:anyAttribute namespace=”##other” processContents=”lax”/>
</xs:complexType><?xml version=”1.0” encoding=”utf-8”?>


<!-- Example Section in MPD -->
  <EventStream schemeIdUri="urn:scte:scte35:2013a:bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">/DAlAAAAAAAAAP/wFAUAAAQCf+//KRjAfP4AKTLgAAAAAAAAVYsh2w==</Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">/DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==</Event>
        <Event presentationTime="15447167300227600" duration="600000000" id="1028">/DAlAAAAAAAAAP/wFAUAAAQEf+//KjkknP4AUmXAAAAAAAAAWcEldA==</Event>
        <Event presentationTime="15447168350227600" duration="600000000" id="1029">/DAlAAAAAAAAAP/wFAUAAAQFf+//KslyqP4AUmXAAAAAAAAAvKNt0w==</Event>
        <Event presentationTime="15447169400227600" duration="300000000" id="1030">/DAlAAAAAAAAAP/wFAUAAAQGf+//K1mIvP4AKTLgAAAAAAAAt2zEbw==</Event>
        <Event presentationTime="15447170450227600" duration="600000000" id="1031">/DAlAAAAAAAAAP/wFAUAAAQHf+//K+hc/v4AUmXAAAAAAAAANNRzVw==</Event>
    </EventStream>
~~~

>[!NOTE]
>Vegye figyelembe, hogy presentationTime a bemutatót az esemény időpontja, nem az üzenet érkezésének ideje.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 sávon esemény üzenet mezőbe jelzés
Egy sávon eseménystream a MPD InbandEventStream elem hogy a betanítás beállítása szintjén van szükség.  Ez az elem egy kötelező schemeIdUri attribútum és választható időskálára attribútum, amely is szerepel az esemény üzenet mezőjében (emsg) rendelkezik.  Esemény üzenet mezők, amelyek nincsenek meghatározva a MPD a séma-azonosítók nem szabad megadni. DASH ügyfél, amely nincs definiálva a MPD a rendszert egy esemény üzenetablak észleli, ha az ügyfél várhatóan figyelmen kívül hagyhatja azt.
Az esemény üzenet mezőbe (emsg) biztosít az adathordozó bemutató idő kapcsolatos általános események jelzés. Ha van ilyen, bármely "emsg" mezőben előtt bármely "moof" mezőben kell elhelyezni.

### <a name="dash-event-message-box-emsg"></a>VONAL-esemény üzenetablak "emsg"
~~~
Box Type: `emsg’
Container: Segment
Mandatory: No
Quantity: Zero or more
~~~

~~~ c
aligned(8) class DASHEventMessageBox extends FullBox(‘emsg’, version = 0, flags =0) 
{
    string scheme_id_uri;
    string value;
    unsigned int(32) timescale;
    unsigned int(32) presentation_time_delta;
    unsigned int(32) event_duration;
    unsigned int(32) id;
    unsigned int(8) message_data[];
}
~~~

A mezők a DASHEventMessageBox az alábbi vannak meghatározva:

| **Mező neve**          | **Mező típusa**          | **Kötelező?** | **Leírás**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | sztring                  | Szükséges      | A rendszer az üzenet azonosítja. A rendszer a kiszolgáló Manifest élő írja a rendszer attribútum értéke. Az érték lehet egy URN vagy URL-cím az üzenet rendszer azonosítása. [SCTE – 35] üzenetek esetében ez értéket vesz fel a speciális "urn: scte:scte35:2013a:bin", bár [SCTE-67-es] más javasolja. |
| Érték                   | sztring                  | Szükséges      | Az üzenet szemantikáját testreszabása a tulajdonosok között, a rendszer által használt egy további karakterláncértéket. Annak érdekében, hogy megkülönböztethető több esemény-adatfolyamok ugyanazt a sémát használja, az értéke lesz az eseménystream (betöltési Smooth trackName vagy AMF üzenet neve az RTMP betöltési) nevére.                                                                  |
| Időskála               | 32 bites, előjel nélküli egész | Szükséges      | A időskálára órajel során végbemenő másodpercenként hányszor és időtartam mezők a "emsg" mezőben található.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32 bites, előjel nélküli egész | Szükséges      | A media bemutató eltelt időtartam a bemutatót az esemény időpontja és a legkorábbi bemutató idő a szegmensben. A bemutató időpontja és időtartama kell igazítani a Stream hozzáférési pontok (SAP) 1 vagy 2, típus meghatározott i. [ISO-14496 – 12].                                                                                            |
| event_duration          | 32 bites, előjel nélküli egész | Szükséges      | Az esemény vagy egy ismeretlen időtartamot jelzi 0xFFFFFFFF időtartama.                                                                                                                                                                                                                                                                                          |
| Azonosító                      | 32 bites, előjel nélküli egész | Szükséges      | Ez az üzenet példányát azonosítja. Egyenértékű sémantikou üzenetek ugyanazzal az értékkel rendelkezik. Ha azonosítója nincs megadva, ha az üzenet betöltött, az Azure Media Services egyedi azonosítót hoz létre.                                                                                                                                                    |
| Message_data            | bájttömb              | Szükséges      | Az eseményüzenet. [SCTE – 35]-üzenetek az állapotüzenet-adatokat a bináris splice_info_section() nincs, bár [SCTE-67-es] más javasolja.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 vonal-üzenetkezelés

Események jelzésküldés sávon, a "emsg" mezőben, a videó- és audio nyomon követi.  A jelzés akkor fordul elő, az összes szegmentálhatja a kérelmek, amelyek a presentation_time_delta esetében 15 másodperc vagy annál kisebb. Ha engedélyezve van egy bemutató csúszóablakban, eseményüzenetek törlődnek a MPD amikor idő összege és időtartama az eseményüzenet kisebb, mint a ideje az adathordozó-adatok a jegyzékfájlban.  Más szóval az eseményt üzenetek törlődnek a jegyzékfájl amikor bemutató csúszóablakban ki lett állítva, az adathordozó idő, amelyre vonatkoznak.

## <a name="4-scte-35-ingest"></a>4 SCTE – 35 betöltési

[SCTE – 35] üzenetek a séma bináris formátumban vannak betöltött **"urn: scte:scte35:2013a:bin"** Smooth fogadása és a típus **"scte35"** RTMP betöltési. [SCTE – 35] időzítési, MPEG-2 transport stream bemutató időbélyegeket (pont), PT közötti leképezést alapuló átalakítása megkönnyítése érdekében (pts_time + a splice_time()) és az adathordozó ütemterv pts_adjustment által biztosított az esemény bemutató idő () Zökkenőmentes fragment_absolute_time mezőjére fogadni, és az idő mezője RTMP betöltési). A leképezés szükség, mert a 33 bites PT értéket mutatnak körülbelül 26.5 óránként.

Smooth Streaming betöltési megköveteli, hogy a Media Data Box (mdat) kell tartalmaznia a **splice_info_section()** definiálva a 8 – 1. táblázat [SCTE – 35]. Az RTMP betöltési, AMF üzenet a köteg attribútum értéke a base64encoded **splice_info_section()**. Az üzenetek a fent ismertetett formátumban van, amikor azok érkeznek Dash, HLS és Smooth ügyfelek [SCTE-67-es] megfelelnek-e.


## <a name="5-references"></a>5 referenciák

**[SCTE – 35]**  ANSI/SCTE 35 2013a – csatlakoztassa, 2013a digitális Program beszúrási Cueing üzenet

**[SCTE-67-ES]**  ANSI/SCTE 67-es 2014 – ajánlott eljárás, SCTE 35: Digitális Program beszúrási Cueing üzenet kábel

**[DASH]**  ISO/IEC 23009-1 2014 – információtechnológiai – dinamikus adaptív streamelés keresztül HTTP (DASH) – 1. rész: Bemutató média leírása és a szegmens formátum, 2. kiadás

**[HLS]** [“HTTP Live Streaming”, draft-pantos-http-live-streaming-14, October 14, 2014,](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]**  ["A Microsoft Smooth Streaming protokoll", 2014. május 15.](https://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]**  ["Művelet üzenet formátuma AMF0"](http://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE-FMP4]**  [Specifikáció: darabolt MP4 élő azure Media Services feldolgozása](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]** ISO/IEC 14496-12: Rész 12 ISO alap médiafájl-formátumban, a negyedik kiadása 2012-07-15.

**[RTMP]**  ["Adobe valós idejű üzenetkezelő protokoll", 2012. December 21.](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>További lépések
Nézet Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
