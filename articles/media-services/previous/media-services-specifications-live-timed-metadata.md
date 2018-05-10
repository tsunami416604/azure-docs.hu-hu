---
title: Az Azure Media Services - jelzés metaadat az élő adatfolyam-továbbítás időtúllépés miatt megszakadt. |} Microsoft Docs
description: Ez az előírás jelzésátviteli rendszeres metaadat belül élő adatfolyam-Media Services által támogatott két módot ismerteti. Ez magában foglalja az általános rendszeres metaadat jelek, valamint SCTE-35 jelzés ad splice beszúrásához támogatása.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2018
ms.author: johndeu;
ms.openlocfilehash: 2e736872dc3e471af7c5b3f758516910a02067fe
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Jelzés túllépte az időkorlátot metaadat az élő adatfolyam-továbbítás


## <a name="1-introduction"></a>1. bevezetés 
A beszúrási hirdetéseket vagy egyéni események ügyféllejátszó, gyakran műsorszolgáltatók ellenőrizze elősegítése érdekében időzített metaadatok felhasználása a videó ágyazott. Ahhoz, hogy ezek a forgatókönyvek, a Media Services támogatást nyújt az átvitel mellett az adathordozót, a feldolgozást, a ponttól, az ügyfélalkalmazás az élő adatfolyam-továbbítási csatorna időzített metaadatok.
A meghatározás vázol fel két mód belül rendszeres metaadat Media Services által támogatott élő adatfolyam-továbbítási jelek:

1. [SCTE-35], amely az ajánlott eljárásokat, amelyet [SCTE-67] heeds jelzés

2. Egy általános metaadatok módot, az üzenetek nem jelzés [SCTE-35] túllépte az időkorlátot

### <a name="12-conformance-notation"></a>1.2 megfelelési jelöléssel
A kulcs szavak "kell", "Nem", "REQUIRED", "SHALL", "Nem kell", "SHOULD", "Nem kell", "Ajánlott", "Is" és "OPTIONAL" Ebben a dokumentumban vannak kell értelmezni az RFC 2119 leírtak szerint

### <a name="13-terms-used"></a>1.3 használt kifejezések

| Időtartam              | Meghatározás                                                                                                                                                                                                                       |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bemutató idő | Az esemény nem jelenik meg a megjelenítő időpontja. Az idő az adathordozó idősoron, hogy a megjelenítő jelenik meg az esemény jelenleg jelöli. Például a bemutató SCTE-35 splice_info() parancs üzenet ideje a splice_time(). |
| Érkezésének ideje      | Egy üzenet érkezik időpontja. Az idő az általában különálló, a bemutató óta az esemény, mivel esemény üzenetek küldése történik az esemény bemutató időben.                                     |
| Ritka nyomon követése      | Media nyomon, hogy nincs folyamatos, és az idő szinkronizálva van egy szülő- vagy nyomon követése.                                                                                                                                    |
| Forrás            | Az Azure Media adatfolyam-szolgáltatás                                                                                                                                                                                                |
| Csatornagyűjtő      | Az Azure Media élő adatfolyam-szolgáltatás                                                                                                                                                                                           |
| HLS               | Apple HTTP Live Streaming protokoll                                                                                                                                                                                               |
| KÖTŐJEL              | Dinamikus adaptív adatfolyam-HTTP-kapcsolaton keresztül                                                                                                                                                                                             |
| Zökkenőmentes            | Zökkenőmentes Streamelési protokoll                                                                                                                                                                                                        |
| MPEG2 – TS          | MPEG-2 Transport adatfolyamok                                                                                                                                                                                                         |
| RTMP              | Valós idejű multimédiás protokoll                                                                                                                                                                                                    |
| uimsbf            | Előjel nélküli egész számokat, legjelentősebb először bit.                                                                                                                                                                                    |

-----------------------

## <a name="2-timed-metadata-ingest"></a>2 rendszeres metaadat-feldolgozó
## <a name="21-rtmp-ingest"></a>2.1 RTMP betöltési
RTMP rendszeres metaadat jelek AMF köteg üzenet ágyazva a RTMP adatfolyam támogatja. A köteg üzeneteket küldhet a tényleges esemény előtt egy ideig, vagy ad splice beszúrási kell megtörténnie. Ez a forgatókönyv támogatása érdekében a tényleges idő splice vagy szegmens belül a kezelési üzenetet küldött. További információ: [AMF0].

A következő táblázat ismerteti, amely a Media Services rendszer betöltési AMF üzenetadatokat formátuma.

A név az AMF üzenet különbséget tenni több, azonos típusú eseményfolyamokat használható.  [SCTE-35] üzenetek a AMF üzenet nevét kell "onAdCue" [SCTE-67] szereplő ajánlás szerint.  A listában nem szereplő mezőket kell figyelmen kívül lesz hagyva, így ez a kialakítás innováció nem gátolt a jövőben.

### <a name="signal-syntax"></a>Jel szintaxis
Media Services RTMP egyszerű mód, támogatja a "onAdCue" a következő formátumú nevű egyetlen AMF köteg üzenet:

### <a name="simple-mode"></a>Egyszerű mód

| Mező neve | Mező típusa | Kötelező? | Leírások                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| Köteg        | Karakterlánc     | Szükséges | Az esemény üzenet.  Kell kell jelölnie egy egyszerű módot "SpliceOut" splice.                                              |
| id         | Karakterlánc     | Szükséges | A splice vagy szegmens leíró egyedi azonosítója. Ez az üzenet példányát azonosítja.                            |
| Időtartam   | Szám     | Szükséges | A splice időtartama. Egységek törtrész másodperc alatt.                                                                |
| elapsed    | Szám     | Optional | A jel alatt ismételt támogatása érdekében hangolására, ebben a mezőben kell lennie a bemutató, hogy mennyi ideig a splice kezdete óta eltelt. Egységek törtrész másodperc alatt. Ha egyszerű módot, ez az érték nem haladhatja meg a splice eredeti időtartama.                                                  |
| time       | Szám     | Szükséges | A splice bemutató időben idején kell lennie. Egységek törtrész másodperc alatt.                                     |

---------------------------

### <a name="scte-35-mode"></a>SCTE-35 mód

| Mező neve | Mező típusa | Kötelező? | Leírások                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| Köteg        | Karakterlánc     | Szükséges | Az esemény üzenet.  [SCTE-35] üzenetek a base64 kell lennie (IETF RFC 4648) bináris kódolású splice_info_section() ahhoz, hogy az [SCTE-67] HLS, Smooth és kötőjel ügyfelek küldendő üzenetek.                                              |
| type       | Karakterlánc     | Szükséges | Egy URN vagy URL-cím azonosítására az üzenet séma; például "urn: Példa: jelzés: 1.0".  [SCTE-35] üzenetek esetén "urn: scte:scte35:2013a:bin" ahhoz, hogy az [SCTE-67] HLS, Smooth és kötőjel ügyfelek küldendő üzenetek kell.  |
| id         | Karakterlánc     | Szükséges | A splice vagy szegmens leíró egyedi azonosítója. Ez az üzenet példányát azonosítja.  Egyenértékű szemantika-üzenetek rendelkezik ugyanazt az értéket.|
| Időtartam   | Szám     | Szükséges | Az esemény vagy ad splice-szegmens, ha ismert időtartama. Ismeretlen, ha az érték 0 kell lennie.                                                                 |
| elapsed    | Szám     | Optional | A [SCTE-35] ad jel ahhoz, hogy hangolására alatt ismételt, ez a mező a bemutató, hogy mennyi ideig a splice kezdete óta eltelt kell lennie. Egységek törtrész másodperc alatt. [SCTE-35] módban Ez az érték haladhatja meg a splice vagy szegmens eredeti megadott időtartama.                                                  |
| time       | Szám     | Szükséges | Az esemény vagy ad splice bemutató idején.  A bemutató időpontja és időtartama kell igazítania az adatfolyam hozzáférési pontok (SAP) 1 vagy 2, típus i. [ISO-14496-12] melléklet. A HLS kilépő időpontja és időtartama kell igazodni szegmens határokat. A bemutató idejét és a különböző eseményüzeneteket belül azonos időtartamát eseményfelhasználó nem lehet átfedésben. Egységek törtrész másodperc alatt.

---------------------------

#### <a name="211-cancelation-and-updates"></a>2.1.1 gyermektevékenysége és frissítések

Üzenetek megszakítva, vagy frissítette több üzenetküldésre azonos bemutató dátumával és azonosítóját. A bemutató idő és azonosító egyedi módon azonosítja az eseményt, és az utolsó üzenet érkezett, amely megfelel a előtti összegző megkötések meghatározott megjelenítési ideje, hogy van-e bírálni. A frissített esemény felülírja a korábban fogadott üzenetek. A előtti összegző pedig az négy másodperc. A bemutató időpont előtti legalább négy másodpercenként fogadott üzenetek reakciót lesz.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 töredezett MP4 betöltési) (Smooth Streaming)
További részletek [LIVE-FMP4] tartozó betöltési élő adatfolyam vonatkozó követelmények. A következő szakaszokban időzített bemutató metaadatok bemeneti kapcsolatos részleteket.  Időzített bemutató metaadatok keresztül van a szervezetbe, a ritka nyomon követése, amelyet mindkét élő kiszolgáló Manifest mezőbe (lásd az MS-SSTR) és a Movie be ("moov").  Minden egyes ritka töredék áll egy Movie töredék (moof) és Media adatok be ("mdat"), ahol a "mdat" be-e a bináris üzenet.

#### <a name="221-live-server-manifest-box"></a>2.2.1-en élő kiszolgáló jegyzék mezőbe
A ritka követése deklarálni kell a kiszolgáló Manifest élő mezőt a \<textstream\> bejegyzést, és állítsa be a következő attribútumokkal kell rendelkeznie:

| **Attribútum neve** | **Mező típusa** | **Kötelező megadni?** | **Leírás**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Szám         | Szükséges      | "0", amely jelzi, az ismeretlen, változó sávszélességű nyomon kell lennie.                                                                                                                                                                                                 |
| parentTrackName    | Karakterlánc         | Szükséges      | A szülő track, amelyhez a ritka követése idő kódok igazítva időskálára nevének kell lennie. A szülő nyomon követése nem lehet ritka nyomon.                                                                                                                    |
| manifestOutput     | Logikai        | Szükséges      | "True", annak jelzésére, hogy a ritka követése beágyazza a zökkenőmentes ügyfél jegyzékfájl kell lennie.                                                                                                                                                               |
| Altípus            | Karakterlánc         | Szükséges      | KELL lennie a következő négy karaktert code "DATA".                                                                                                                                                                                                                         |
| séma             | Karakterlánc         | Szükséges      | Csak URN vagy URL-cím azonosítására az üzenet séma; például "urn: Példa: jelzés: 1.0". [SCTE-35] üzenetek esetén "urn: scte:scte35:2013a:bin" ahhoz, hogy az [SCTE-67] HLS, Smooth és kötőjel ügyfelek küldendő üzenetek kell. |
| TrackName          | Karakterlánc         | Szükséges      | A ritka track nevének kell lennie. A trackName segítségével az ugyanazon séma több eseményfolyamokat megkülönböztetéséhez. Minden egyedi eseményfelhasználó követése egyedi névvel kell rendelkeznie.                                                                           |
| időskálára          | Szám         | Optional      | A szülő track időskálára kell lennie.                                                                                                                                                                                                                      |

-------------------------------------

### <a name="222-movie-box"></a>2.2.2 movie mezőbe

A Movie jelölőnégyzetet (moov) a következő élő kiszolgáló Manifest mezőbe a ritka nyomon az adatfolyam fejlécének részeként.

A "moov" mezőben tartalmaznia kell egy **TrackHeaderBox (tkhd)** mezőben meghatározott [ISO-14496-12] ezekkel a korlátozásokkal:

| **Mező neve** | **Mező típusa**          | **Kötelező megadni?** | **Leírás**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| Időtartam       | 64 bites előjel nélküli egészként. | Szükséges      | 0, kell lennie, mert a statisztikák nyomon követésének nulla minták van, és a minták a szám mezőbe a teljes időtartam 0. |
-------------------------------------

A "moov" mezőben tartalmaznia kell egy **HandlerBox (hdlr)** ezekkel a korlátozásokkal [ISO-14496-12] meghatározottak szerint:

| **Mező neve** | **Mező típusa**          | **Kötelező megadni?** | **Leírás**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32 bites előjel nélküli egészként. | Szükséges      | "Meta" kell lennie. |
-------------------------------------

A "stsd" mezőben a [ISO-14496-12] tartalmaznia kell egy kódolási név MetaDataSampleEntry található.  Például SCTE-35 üzenetek a kódolási név legyen "scte".

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 movie töredéket és Media adatok be

Ritka követése töredék egy Movie töredék (moof) és egy adathordozó adatok be (mdat) áll.

A MovieFragmentBox (moof) tartalmaznia kell egy **TrackFragmentExtendedHeaderBox (uuid)** mezőben a következő mezőket az [MS-SSTR] meghatározottak szerint:

| **Mező neve**         | **Mező típusa**          | **Kötelező megadni?** | **Leírás**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64 bites előjel nélküli egészként. | Szükséges      | Az esemény a érkezésének ideje kell lennie. Ez az érték igazítja a szülő nyomon követése az üzenetet.   |
| fragment_duration      | 64 bites előjel nélküli egészként. | Szükséges      | Az esemény időtartama kell lennie. Az időtartam azt jelzi, hogy a duration ismeretlen nulla is lehet. |

------------------------------------


A MediaDataBox (mdat) mezőben a következő formátumban kell rendelkeznie:

| **Mező neve**          | **Mező típusa**                   | **Kötelező megadni?** | **Leírás**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| verzió:                 | 32 bites, előjel nélküli egész (uimsbf) | Szükséges      | Meghatározza, hogy a "mdat" mező tartalmát formátuma. Ismeretlen verziójú a rendszer figyelmen kívül hagyja. Jelenleg az egyetlen támogatott verzió: 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 bites, előjel nélküli egész (uimsbf) | Szükséges      | Ez az üzenet példányát azonosítja. Egyenértékű szemantika-üzenetek van ugyanarra az értékre; Ez azt jelenti, hogy bármely események üzenetablak ugyanezzel az azonosítóval rendelkező feldolgozása is használhatók.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 bites, előjel nélküli egész (uimsbf) | Szükséges      | A fragment_absolute_time a TrackFragmentExtendedHeaderBox és a presentation_time_delta megadott összege kell lennie a bemutató esemény időpontja. A bemutató időpontja és időtartama kell igazítania az adatfolyam hozzáférési pontok (SAP) 1 vagy 2, típus i. [ISO-14496-12] melléklet. A HLS kilépő időpontja és időtartama kell igazodni szegmens határokat. A bemutató idejét és a különböző eseményüzeneteket belül azonos időtartamát eseményfelhasználó nem lehet átfedésben. |
| message                 | bájttömbben.                       | Szükséges      | Az esemény üzenet. [SCTE-35] üzenetek az üzenet a bináris splice_info_section(), bár [SCTE-67] azt javasolja, hogy valami mással. [SCTE-35] üzenetek kell ahhoz, hogy az [SCTE-67] HLS, Smooth és kötőjel ügyfelek küldendő üzenetek splice_info_section(). [SCTE-35] üzenetek a bináris splice_info_section() "mdat" mezőben a tartalom, pedig nem base64-kódolású.                                                            |

------------------------------


### <a name="224-cancelation-and-updates"></a>2.2.4 gyermektevékenysége és frissítések
Üzenetek megszakítva, vagy frissítette több üzenetküldésre azonos bemutató dátumával és azonosítóját.  A bemutató ideje és az azonosító az esemény egyedi azonosítására. Az utolsó üzenet érkezett egy meghatározott megjelenítési elindításakor, amely megfelel a előtti összegző megkötések, az, hogy van-e bírálni. A frissített üzenet a felváltja a korábban fogadott üzenetek.  A előtti összegző pedig az négy másodperc. A bemutató időpont előtti legalább négy másodpercenként fogadott üzenetek reakciót lesz. 


## <a name="3-timed-metadata-delivery"></a>3 metaadatok kézbesítési túllépte az időkorlátot

Az adatfolyam eseményadat fedett, a Media Services. A Media Services három adatra csupán továbbítja a bemeneti végpont és az ügyfél végpont között. A következő tulajdonságok az ügyfél megfelelősége [SCTE-67] érkeznek, és/vagy az ügyfél a folyamatos átviteli protokoll:

1.  Séma – a URN vagy URL-cím az üzenet a rendszer azonosítása.

2.  Bemutató idő – a bemutató az adathordozó idősoron esemény időpontja.

3.  Időtartam – az esemény időtartamát.

4.  Azonosító – a esemény választható egyedi azonosítója.

5.  Üzenet – az eseményadatok.


## <a name="31-smooth-streaming-delivery"></a>3.1 zökkenőmentes adatfolyamként történő továbbítását

Tekintse meg a ritka követése kezelése az [MS-SSTR] részletei.

#### <a name="smooth-client-manifest-example"></a>Zökkenőmentes ügyfél jegyzék – példa
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

## <a name="32--apple-hls-delivery"></a>3.2 Apple HLS Delivery
A Szegmens lista egy egyéni M3U címkében rendszeres metaadat az Apple HTTP Live Streaming (HLS) ágyazhatók be.  Az alkalmazási rétegre a M3U lista elemzése és M3U címkék feldolgozására alkalmas. Az Azure Media Services időzített metaadatok beágyazása a [HLS] definiált EXT-X-KÖTEG címke.  A EXT-X-KÖTEG címke jelenleg ADI3 típusú üzenetek DynaMux használják.  SCTE-35 és nem SCTE-35 üzenetek támogatásához a fájlattribútumok EXT-X-KÖTEG címke alatt meghatározott:

| **Attribútum neve** | **Típus**                      | **Kötelező megadni?**                             | **Leírás**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| KÖTEG                | idézőjelek közé zárt karakterlánc                 | Szükséges                                  | Az üzenet kódolt Base64 kódolású karakterlánc leírtak [IETF RFC 4648](http://tools.ietf.org/html/rfc4648). [SCTE-35] üzenetek ez pedig a base64-kódolású splice_info_section().                                                                                                |
| TÍPUS               | idézőjelek közé zárt karakterlánc                 | Szükséges                                  | Egy URN vagy URL-cím azonosítására az üzenet séma; például "urn: Példa: jelzés: 1.0". [SCTE-35] üzenetek típusa "scte35" különleges értéket veszi fel.                                                                                                                                |
| ID (Azonosító)                 | idézőjelek közé zárt karakterlánc                 | Szükséges                                  | Az esemény egyedi azonosítója. Ha nincs megadva a azonosító, ha az üzenet van okozhatnak, Azure Media Services egyedi azonosítót hoz létre.                                                                                                                                          |
| IDŐTARTAM           | decimális lebegőpontos szám | Szükséges                                  | Az esemény időtartamát. Ismeretlen, ha az érték 0 kell lennie. Egységek factional másodperc alatt.                                                                                                                                                                                           |
| ELTELT IDŐ            | decimális lebegőpontos szám | Nem kötelező, de szükséges késleltetett ablak | A jel bemutató csúszóablak támogatásához alatt ismételt, írjon be az esemény kezdete óta eltelt idő bemutató mennyisége. Egységek törtrész másodperc alatt. Ez az érték haladhatja meg a splice vagy szegmens eredeti megadott időtartama. |
| IDŐ               | decimális lebegőpontos szám | Szükséges                                  | A bemutató esemény időpontja. Egységek törtrész másodperc alatt.                                                                                                                                                                                                                    |


A HLS player alkalmazásréteg típus használatával azonosítására az üzenet formátuma, az üzenet dekódolása, alkalmazza a szükséges idő átalakítások és feldolgozni az eseményt.  Az események az esemény időbélyegzője megfelelően szinkronizálásakor a Szegmens lista szülő track.  Beillesztés előtt a legközelebbi szegmens (#EXTINF címke).

#### <a name="hls-segment-playlist-example"></a>HLS szegmens lista példa
~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:6
#EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
#EXTINF:6.000000,no-desc
Fragments(video=0,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=60000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
#EXT-X-CUE: ID=”metadata-12.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”12.000000”, DURATION=”18.000000”,CUE=”HrwOi8vYmWVkaWEvhhaWFRlRDa=”
Fragments(video=120000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=180000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=240000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=300000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=360000000,format=m3u8-aapl)
#EXT-X-CUE: ID=”metadata-42.000000”,TYPE=”urn:example:signaling:1.0”,TIME=”42.000000”, DURATION=”60.000000”,CUE=”PD94bWwgdm0iMS4wIiBlbmNvpD4=”
#EXTINF:6.000000,no-desc
Fragments(video=420000000,format=m3u8-aapl)
#EXTINF:6.000000,no-desc
Fragments(video=480000000,format=m3u8-aapl)
…
~~~

#### <a name="hls-message-handling"></a>HLS üzenet kezelése

Események a Szegmens lista minden video- és track felé a jelzésküldés. A pozíció EXT-X-KÖTEG címke mindig kell vagy azonnal, mielőtt az első HLS szegmenseket (kimenő splice vagy szegmens kezdési) vagy közvetlenül az után utolsó HLS szegmenseket (a splice vagy szegmens célból) időpontja és időtartama attribútumainak utal, kérelmének [ HLS].

Ha egy bemutató csúszóablak engedélyezve van, a EXT-X-KÖTEG címke meg kell ismételni elég gyakran, hogy a splice vagy szegmens mindig részletesen a szegmens listát, és az ELTELT attribútumot kell használni, időt jelzi a splice vagy szegmens Nincs aktív [HLS] követelményei szerint.

Bemutató csúszóablak engedélyezésekor a rendszer a EXT-X-KÖTEG címkék a szegmens listából törlődnek, amikor a media idő, amelyre vonatkoznak átfordították bemutató csúszóablak kívül.

## <a name="33--dash-delivery"></a>3.3 DASH kézbesítés
[DASH] jel események három módot biztosít:

1.  Az a MPD jelzést események
2.  Események jelzést sávon a ábrázolás (esemény üzenetablak (emsg) használatával
3.  1 és a 2. régiója

VOD streaming, mert az összes esemény, azonnal, amikor a MPD ügyfelek hozzáférjenek a MPD a jelzést események hasznosak. A sávon megoldás élő adatfolyam, mert az ügyfelek nem kell újra letölteni a MPD. Időalapú Szegmentálás esetén az ügyfél a következő szegmens az URL-cím a telepítések időtartamát és a jelenlegi szegmens időbélyegzője hozzáadásával határozza meg. Amely igénylése sikertelen, ha az ügyfél azt feltételezi, hogy a kihagyást és letölti a MPD, de egyébként továbbra is fennáll, a MPD letöltése nélkül streaming.

Azure Media Services mindkét jelzés a MPD a tegye és sávon jelzés a esemény üzenetablak használatával.

#### <a name="mpd-signaling"></a>MPD jelzés

Események fogja jelezni a használatával a EventStream elemet, amely akkor jelenik meg, a Period elemen belül MPD.

A EventStream elem a következő attribútumokkal rendelkezik:

| **Attribútum neve** | **Típus**                | **Kötelező megadni?** | **Leírás**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | karakterlánc                  | Szükséges      | Az üzenet a rendszer azonosítja. A rendszer a kiszolgáló Manifest élő mezőbe értéke a rendszer attribútum értéke. Az értéknek egy URN vagy URL-cím az üzenet séma; azonosító kell esnie. például "urn: Példa: jelzés: 1.0".                                                                |
| érték              | karakterlánc                  | Optional      | Egy további karakterlánc-érték a szemantikáját, az üzenet testreszabásához tulajdonosai a rendszer által használt. Ugyanazon séma több eseményfolyamokat megkülönböztetéséhez, az értéket meg kell az eseménystream (betöltési Smooth trackName vagy AMF üzenet nevét RTMP betöltési) nevét. |
| időskálára          | 32 bites előjel nélküli egészként. | Szükséges      | A időskálára a ticks / másodperc, a "emsg" mezőben időtartam mezők és a alkalommal.                                                                                                                                                                                                       |


Nulla vagy több esemény elemek EventStream elemen belül találhatók, és a következő attribútumokkal rendelkeznek:

| **Attribútum neve**  | **Típus**                | **Kötelező megadni?** | **Leírás**                                                                                                                                                                                                             |
|---------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| presentation_time   | 64 bites előjel nélküli egészként. | Optional      | A media kell lennie az időszak kezdete viszonyítva esemény bemutató időpontja. A bemutató időpontja és időtartama kell igazítania az adatfolyam hozzáférési pontok (SAP) 1 vagy 2, típus i. [ISO-14496-12] melléklet. |
| Időtartam            | 32 bites előjel nélküli egészként. | Optional      | Az esemény időtartamát. Ez az időtartam ismeretlen esetén kell hagyni.                                                                                                                                                 |
| id                  | 32 bites előjel nélküli egészként. | Optional      | Ez az üzenet példányát azonosítja. Egyenértékű szemantika-üzenetek rendelkezik ugyanazt az értéket. Ha nincs megadva a azonosító, ha az üzenet van okozhatnak, Azure Media Services egyedi azonosítót hoz létre.             |
| Esemény elem értéke | karakterlánc                  | Szükséges      | A Base64 kódolású karakterlánc az eseményüzenet [IETF RFC 4648](http://tools.ietf.org/html/rfc4648).                                                                                                                   |

#### <a name="xml-syntax-and-example-for-dash-manifest-mpd-signaling"></a>XML-szintaxis és a kötőjel példa manifest (MPD) Signaling

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

<EventStream schemeIdUri=”urn:example:signaling:1.0” timescale=”1000” value=”player-statistics”>
  <Event presentationTime=”0” duration=”10000” id=”0”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
  <Event presentationTime=”20000” duration=”10000” id=”1”> PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</Event>
</EventStream>
~~~

>[!NOTE]
>Vegye figyelembe, hogy presentationTime a bemutató esemény időpontja, nem az üzenet érkezésének ideje.
>

### <a name="431-in-band-event-message-box-signaling"></a>4.3.1 sávon esemény üzenet mezőbe jelzés
Egy sávon eseményfelhasználó InbandEventStream elemet a szint kiigazítása beállítása, hogy MPD igényel.  Ez az elem egy kötelező schemeIdUri attribútummal és választható időskálára attribútum is szerepel az esemény üzenetablak (emsg) rendelkezik.  Esemény üzenet mezők nem definiált a MPD séma-azonosítók nem lehetnek jelen. DASH-ügyfél, amely nincs definiálva a MPD a rendszert egy esemény üzenetablak észleli, ha az ügyfél várhatóan figyelmen kívül hagyja.
Az esemény üzenetablak (emsg) biztosít a bemutató amikor kapcsolatos általános események jelzés. Ha van ilyen, bármely "emsg" mezőben a "moof" mező előtt kell elhelyezni.

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

A DASHEventMessageBox mezőket az alábbiakban:

| **Mező neve**          | **Mező típusa**          | **Kötelező megadni?** | **Leírás**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | karakterlánc                  | Szükséges      | Az üzenet a rendszer azonosítja. A rendszer a kiszolgáló Manifest élő mezőbe értéke a rendszer attribútum értéke. Az értéknek egy URN vagy URL-cím az üzenet séma; azonosító kell esnie. például "urn: Példa: jelzés: 1.0". [SCTE-35] üzenetek, ez a különleges értéket vesz "urn: scte:scte35:2013a:bin", bár [SCTE-67] azt javasolja, hogy valami mással. |
| Érték                   | karakterlánc                  | Szükséges      | Egy további karakterlánc-érték a szemantikáját, az üzenet testreszabásához tulajdonosai a rendszer által használt. Ugyanazon séma több eseményfolyamokat megkülönböztetéséhez, az értéket állítja be az eseménystream (betöltési Smooth trackName vagy AMF üzenet nevét RTMP betöltési) nevét.                                                                  |
| időskálára               | 32 bites előjel nélküli egészként. | Szükséges      | A időskálára a ticks / másodperc, a "emsg" mezőben időtartam mezők és a alkalommal.                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32 bites előjel nélküli egészként. | Szükséges      | A media bemutató eltelt idő a bemutató az esemény vagy a legkorábbi bemutató idő ebbe a szegmensbe. A bemutató időpontja és időtartama kell igazítania az adatfolyam hozzáférési pontok (SAP) 1 vagy 2, típus i. [ISO-14496-12] melléklet.                                                                                            |
| event_duration          | 32 bites előjel nélküli egészként. | Szükséges      | Az esemény vagy egy ismeretlen időtartamot jelzi 0xFFFFFFFF időtartama.                                                                                                                                                                                                                                                                                          |
| Azonosító                      | 32 bites előjel nélküli egészként. | Szükséges      | Ez az üzenet példányát azonosítja. Egyenértékű szemantika-üzenetek rendelkezik ugyanazt az értéket. Ha nincs megadva a azonosító, ha az üzenet van okozhatnak, Azure Media Services egyedi azonosítót hoz létre.                                                                                                                                                    |
| Message_data            | bájttömbben.              | Szükséges      | Az esemény üzenet. [SCTE-35] üzenetek az üzenet adataihoz a bináris splice_info_section(), bár [SCTE-67] azt javasolja, hogy valami mással.                                                                                                                                                                                                                                 |

### <a name="332-dash-message-handling"></a>3.3.2 vonal-üzenetek kezelésének

Események felé a jelzésküldés sávon, belül, mind video- és nyomon követi a "emsg" jelölőnégyzet.  A jelzés akkor fordul elő, az összes szegmentálni kérelmeket, amelynek a presentation_time_delta 15 másodperc vagy annál kisebb. Ha egy bemutató csúszóablak engedélyezve van, eseményüzenetei törlődnek a MPD esetén az összegük az idő és az esemény üzenetének időtartama időnél az adathordozó-adatok a jegyzékfájlban.  Ez azt jelenti az eseményüzeneteket törlődnek a jegyzékfájlból, amelyre vonatkoznak media idő átfordították bemutató csúszóablak kívül.

## <a name="4-scte-35-ingest"></a>4 SCTE-35 betöltési

A séma bináris formátumú üzenetek [SCTE-35] keresztül a szervezetbe **"urn: scte:scte35:2013a:bin"** Smooth betöltési és a típus **"scte35"** RTMP betöltési. [SCTE-35] időzítési, MPEG-2 transport stream bemutató időbélyegeket (pont), PT közötti leképezést alapuló átalakítása megkönnyítésére (pts_time + a splice_time()) és az adathordozó ütemterv pts_adjustment biztosítja az esemény bemutató idő ( a Smooth fragment_absolute_time mezője betöltési, és az mező az RTMP betöltési). A leképezés szükség, mert a 33 bites PT érték körülbelül 26.5 óránként mutatnak.

Smooth Streaming-betöltési megköveteli, hogy az adathordozó adatok (mdat) tartalmaznia kell a **splice_info_section()** a 8 – 1. táblázat [SCTE-35] definiálva. RTMP betöltési, a AMF üzenet köteg attribútumának értéke a base64encoded **splice_info_section()**. Az üzenetek formátuma a fent leírt, akkor küld, HLS, Smooth és kötőjel [SCTE-67] az ügyfeleknek.


## <a name="5-references"></a>5 hivatkozások

**[SCTE-35]**  ANSI/SCTE 35 2013a – digitális Program beszúrási Cueing üzenet kábel, 2013a

**[SCTE-67]**  ANSI/SCTE 67 2014 – ajánlott eljárás a SCTE 35: digitális Program beszúrási Cueing üzenet kábel

**[DASH]**  ISO/IEC 23009-1 – informatika – 2014 dinamikus adaptív adatfolyam-keresztül HTTP (DASH) – 1. rész: Media bemutató leírását és a szegmens formátumok, 2. kiadás

**[HLS]**  ["HTTP Live Streaming", draft-pantos-http-live-streaming-14, 2014. október 14.,](http://tools.ietf.org/html/draft-pantos-http-live-streaming-14)

**[MS-SSTR]**  ["Microsoft Smooth Streaming protokoll", 2014. május 15.](http://download.microsoft.com/download/9/5/E/95EF66AF-9026-4BB0-A41D-A4F81802D92C/%5bMS-SSTR%5d.pdf)

**[AMF0]**  ["Művelet üzenet formátuma AMF0"](http://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)

**[LIVE-FMP4]**  [Töredezett MP4 élő azure Media Services betöltési meghatározása](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)

**[ISO-14496-12]**  ISO/IEC 14496-12: rész 12 ISO alap media fájlformátum – Edition 2012-07-15 negyedik.

**[RTMP]**  ["Adobe valós idejű üzenetküldési protokoll" December 21, 2012](https://www.adobe.com/devnet/rtmp.html) 

------------------------------------------

## <a name="next-steps"></a>További lépések
Nézet Media Services tanulási útvonalai.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
