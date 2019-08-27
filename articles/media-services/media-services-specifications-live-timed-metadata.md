---
title: Azure Media Services – időzített metaadatok jelzése élő adatfolyamban | Microsoft Docs
description: Ez a specifikáció olyan metódusokat vázol fel, amelyek jelzik az időzített metaadatokat a Azure Media Servicesba való betöltéskor és továbbításkor. Ez magában foglalja az általános időzített metaadatok (ID3) használatát, valamint a SCTE-35 jelzést az ad-beszúráshoz és a összekötési feltétel jelzéséhez.
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
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: d2fec29c96639d21db362f6982b88a90bd6c319f
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019085"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Időzített metaadatok jelzése élő adatfolyamban 

Utolsó frissítés: 2019-08-22

### <a name="conformance-notation"></a>Megfelelőségi jelölés

A (z) "kötelező", "nem", "REQUIREd", ",", "NOT", "No", "not", "NOT", "ajánlott", "MAY" és "OPTIONal" kulcsszót a jelen dokumentumban leírt módon kell értelmezni, az RFC 2119

## <a name="1-introduction"></a>1. Bevezetés 

A hirdetmények és az egyéni metaadat-események az ügyfélszámítógépen való beszúrásának jelzése érdekében a műsorszolgáltatók gyakran használják a videón belül beágyazott időzített metaadatokat. Ezen forgatókönyvek engedélyezéséhez Media Services támogatja az időzített metaadatok átvitelét az élő adatfolyam-csatorna betöltési pontjáról az ügyfélalkalmazás számára.
Ez a specifikáció több, a Media Services által támogatott üzemmódot vázol fel az élő adatfolyam-jeleken belüli időzített metaadatokhoz.

1. [SCTE-35] olyan jelzés, amely megfelel a [SCTE-35], [SCTE-214-1], [SCTE-214-3] és [RFC8216] által ismertetett szabványoknak.

2. [SCTE-35] olyan jelzés, amely megfelel a régi [Adobe-főkiszolgálói] specifikációnak az RTMP ad-jelzéshez.
   
3. Általános időkorlátú metaadat-jelző mód, amely **nem** [SCTE-35], és a [ID3v2] vagy más, az alkalmazás fejlesztője által definiált egyéni sémákat is tartalmazhat.

## <a name="11-terms-used"></a>1,1 használt kifejezések

| Kifejezés              | Meghatározás |
|-------------------|------------|
| Ad-töréspont          | Az a hely vagy pont, ahol egy vagy több hirdetés ütemezhető kézbesítésre; ugyanaz, mint az igény szerinti és elhelyezési lehetőség. |
| Ad-döntési szolgáltatás| külső szolgáltatás, amely eldönti, hogy mely ad (k) és időtartamok jelenjenek meg a felhasználó számára. A szolgáltatásokat általában egy partner nyújtja, és a jelen dokumentum hatókörén kívül esik.|
| Cue               | A közelgő ad-töréspont időpontjának és paramétereinek a megjelölése. Vegye figyelembe, hogy a figyelmeztetések függőben lévő váltást jelezhetnek egy ad-töréspontra, függőben van egy ad-törésponton belüli következő hirdetésre, és függőben lévő váltás a fő tartalomra. |
| Packager          | A "folyamatos átviteli végpont" Azure Media Services dinamikus csomagolási képességeket biztosít a DASH és a HLS számára, és a Media Industry "csomagoló" néven ismert. 
| Megjelenítés időpontja | Az az idő, amikor egy esemény megjelenik egy megjelenítőben. Az idő az a pillanat, amely az adathordozó idővonalán látható, hogy a megjelenítő megtekinti az eseményt. Például egy SCTE-35 splice_info () parancssori üzenet megjelenítési időpontja a splice_time (). |
| Érkezés időpontja      | Az az idő, amikor egy esemény üzenet érkezik. Az idő általában eltér az esemény megjelenítési idejétől, mert az esemény üzeneteit az esemény megjelenítési időpontja előtt küldik el.                                     |
| Ritka nyomon követés      | a média nyomon követése nem folyamatos, és az idő a szülő-vagy a vezérlési követéssel szinkronizálva van.                                                                                                                                    |
| Forrás            | Az Azure Media Streaming Service                                                                                                                                                                                                |
| Csatorna fogadója      | Az Azure Media Live Streaming szolgáltatás                                                                                                                                                                                           |
| HLS               | Apple HTTP Live Streaming protokoll                                                                                                                                                                                               |
| DASH              | Dinamikus adaptív átvitel HTTP-n keresztül                                                                                                                                                                                             |
| Sima            | Smooth Streaming protokoll                                                                                                                                                                                                        |
| MPEG2-TS          | MPEG 2 átviteli streamek                                                                                                                                                                                                         |
| RTMP              | Valós idejű multimédia protokoll                                                                                                                                                                                                    |
| uimsbf            | Előjel nélküli egész, legjelentősebb bit.                                                                                                                                                                                    |

---

## <a name="12-normative-references"></a>1,2 normatív referenciák

A következő dokumentumok olyan rendelkezéseket tartalmaznak, amelyek az ebben a szövegben található hivatkozással alkotják a jelen dokumentum rendelkezéseit. Az összes dokumentumra a Standards szervek általi felülvizsgálat vonatkozik, és az olvasóknak javasoljuk, hogy vizsgálják meg az alább felsorolt dokumentumok legújabb kiadásainak alkalmazását. Az olvasókat arra is emlékezteti, hogy előfordulhat, hogy a hivatkozott dokumentumok újabb kiadásai nem kompatibilisek a Azure Media Serviceshoz tartozó, időzített metaadatok specifikációjának ezen verziójával.


|Standard  |Meghatározás  |
|---------|---------|
|[Adobe – főműsoridői] | [Főműsoridőben a digitális program beszúrási jelzője 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf) |
|[Adobe-Flash-AS] | [FLASH ActionScript nyelvi referenciája](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf) |
| [AMF0]            | ["A művelet üzenetének formátuma AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf) |
| [DASH-IF-IOP]     | A DASH Industry Forum együttműködési útmutatója v 4,2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html) |
| [HLS-TMD]         | HTTP Live Streaminghoz tartozó időzített metaadatok[https://developer.apple.com/streaming](https://developer.apple.com/streaming) |
| [CMAF-ID3]         | [Időzített metaadatok a Common Media Application Format (CMAF) szolgáltatásban](https://aomediacodec.github.io/av1-id3/)
| ID3v2           | ID3 tag verziója 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure) |
| [ISO-14496-12]    | ISO/IEC 14496-12: 12. rész ISO-alapú adathordozó-fájlformátum, FourthEdition 2012-07-15  |
| [MPEGDASH]        | Information Technology – dinamikus adaptív átvitel HTTP-n keresztül (DASH) – 1. rész: A Media Presentation leírása és a szegmens formátuma. Május 2014. Közzétett. URL: https://www.iso.org/standard/65274.html |
| [MPEGCMAF]        | Információtechnológia – multimédia-alkalmazás formátuma (MPEG-A) – 19. rész: A szegmentált adathordozók közös multimédia-alkalmazási formátuma (CMAF). Január 2018. Közzétett. URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Information Technology – MPEG Systems Technologies – 7. rész: Általános titkosítás az ISO-alapú médiafájl formátumú fájlokban. Február 2016. Közzétett. URL: https://www.iso.org/standard/68042.html |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", május 15., 2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) |
| [MS-SSTR-betöltés]  | [Azure Media Services darabolt MP4 élő betöltési specifikáció](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview) |
| [RFC8216]         | R. Pantos, Ed.; W. May. HTTP Live Streaming. Augusztus 2017. Tájékoztató. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216) |
| [RFC4648]         |A Base16, a Base32 és a Base64-alapú adatkódolások[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648) |
| RTMP            |["Az Adobe valós idejű üzenetkezelési protokollja", december 21., 2012](https://www.adobe.com/devnet/rtmp.html)  |
| [SCTE-35-2019]    | SCTE 35: 2019 – digitális program beszúrási cueing üzenet a kábelhez – https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf  |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH az IP-alapú kábeltelevíziós szolgáltatásokhoz 1. rész: MPD-korlátozások és-bővítmények |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH az IP-alapú kábeltelevíziós szolgáltatásokhoz, 3. rész: KÖTŐJEL/FF-profil |
| [SCTE-224]        | SCTE 224 2018r1 – esemény ütemezése és értesítési felület |
| [SCTE-250]        | Esemény-és jelzőlámpa-kezelési API (a () |

---


## <a name="2-timed-metadata-ingest"></a>2. Időzített metaadatok betöltése

Azure Media Services támogatja a valós idejű sávon belüli metaadatokat mind az [RTMP], mind a Smooth Streaming [MS-SSTR-betöltés] protokoll esetében. A valós idejű metaadatok egyéni események definiálására használhatók saját egyedi sémákkal (JSON, bináris, XML), valamint az iparág által definiált formátumokkal (például ID3 vagy SCTE-35) a szórásos streamekben. 

Ez a cikk részletesen ismerteti, hogyan küldhet egyéni időzített metaadatokat a Azure Media Services támogatott betöltési protokollok használatával. A cikk azt is ismerteti, hogyan történik a HLS, a kötőjel és a Smooth Streaming jegyzékfájljának az időzített metaadatokkal való megjelenítése, valamint a sávon belüli átvitel módja, ha a tartalmat a rendszer a CMAF (MP4-töredékek) vagy a HLS Transport stream (TS) szegmensének használatával továbbítja. 

Gyakori használati esetek az időzített metaadatok esetében:

 - SCTE-35 ad-jelek az ad-megszakítások aktiválására élő vagy lineáris szórás esetén
 - Egyéni ID3-metaadatok, amelyek aktiválják az eseményeket egy ügyfélalkalmazás (böngésző, iOS vagy Android)
 - Egyéni JSON-, bináris vagy XML-metaadatok egy ügyfélalkalmazás eseményeinek elindításához
 - Telemetria egy élő kódolóból, IP-kamerából vagy drone-ből
 - Olyan IP-kamera eseményei, mint például a mozgás, az Arcfelismerés stb.
 - A földrajzi helyzet információi egy művelet kamerából, a droneból vagy az eszköz áthelyezése
 - Song lyrics
 - A program határai a lineáris élő hírcsatornán
 - Az élő hírcsatornán megjelenítendő képek vagy kibővített metaadatok
 - Sportesemények vagy játék-óra információk
 - Interaktív hirdetési csomagok a böngészőben megjelenített videó mellett
 - Tesztek és lekérdezések
  
Azure Media Services az élő események és a csomagolók képesek fogadni ezeket az időzített metaadatokat, és olyan metaadatokba konvertálják őket, amelyek a szabványokon alapuló protokollok, például a HLS és a DASH használatával érik el az ügyfélalkalmazások elérését.


## <a name="21-rtmp-timed-metadata"></a>2,1 RTMP időzített metaadatok

Az [RTMP] protokoll lehetővé teszi, hogy a rendszer időzített metaadatokat küldjön a különböző forgatókönyvekhez, beleértve az egyéni metaadatokat és a SCTE-35 ad-jeleket. 

A hirdetési jelek (Cue-üzenetek) az [RTMP] streamben beágyazott [AMF0] Cue-üzenetekként lesznek elküldve. Előfordulhat, hogy a rendszer elküldte a Cue-üzeneteket a tényleges esemény vagy a [SCTE35] ad-összekötési jel előtt. Ennek a forgatókönyvnek a támogatásához a rendszer az esemény tényleges bemutató időbélyegét küldi el a végszó üzenetben. További információ: [AMF0].

A következő [AMF0] parancsokat az RTMP betöltéséhez Azure Media Services támogatja:

- **onUserDataEvent** – egyéni metaadatok vagy [ID3v2] időzített metaadatok használata
- **onAdCue** – elsősorban az élő streamben a hirdetmények elhelyezésére szolgáló lehetőség jelzésére használatos. A Cue két formája támogatott, egy egyszerű mód és egy "SCTE-35" üzemmód. 
- **onCuePoint** – bizonyos helyszíni hardveres kódolók, például az elemi élő kódoló által támogatott jelek [SCTE35] üzenetek. 
  

Az alábbi táblázatok ismertetik az AMF üzenet adattartalomának formátumát, amelyet a Media Services a "Simple" és a [SCTE35] üzenet módokon is betölt.

A [AMF0] üzenet neve több, egyazon típusú esemény-adatfolyam megkülönböztetésére használható.  A [SCTE-35] és az "Simple" mód esetében az AMF-üzenet nevének "onAdCue" értékűnek kell lennie az [Adobe-főkiszolgálói] specifikációban leírtak szerint.  Az alább nem felsorolt mezőket figyelmen kívül kell hagyni a betöltéskor Azure Media Services.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP egyéni metaadatokkal a "onUserDataEvent" használatával

Ha a felsőbb rétegbeli kódolótól, az IP-kamerától, a drone-től vagy az eszköztől az RTMP protokollt használó egyéni metaadatokat szeretne biztosítani, használja a "onUserDataEvent" [AMF0] adatüzenet parancs típusát.

A **"onUserDataEvent"** adatüzenet-parancsnak a következő definícióval ellátott üzenettel kell rendelkeznie a Media Services és a sávon belüli fájlformátumba való becsomagolással, valamint a HLS, DASH és Smooth streaming jegyzékfájlokkal együtt.
Azt javasoljuk, hogy az időzített metaadatokat legalább 0,5 másodpercenként (500ms), vagy az élő streamtel kapcsolatos stabilitási problémák esetén ne kelljen gyakrabban elküldeni. Minden üzenet több képkockából is összesítheti a metaadatokat, ha frame szintű metaadatokat kell megadnia. Ha a többszörös átviteli sebességű streameket küld, javasoljuk, hogy a metaadatokat csak egyetlen bitráta esetén adja meg, hogy csökkentse a sávszélességet, és ne zavarja a videó/hang feldolgozását. 

A **"onUserDataEvent"** értékének a következőnek kell lennie: [MPEGDASH] EventStream XML Format üzenet. Ez megkönnyíti az olyan egyéni definiált sémák átadását, amelyek a sávon belüli "emsg" adattartalomban hajthatók végre a CMAF [MPEGCMAF] tartalmak esetében, amelyeket HLS vagy DASH protokollon keresztül továbbítanak. Minden DASH Event stream-üzenet tartalmaz egy schemeIdUri, amely URN-üzenet séma-azonosítóként működik, és meghatározza az üzenet hasznos adatait. Egyes sémák (például https://aomedia.org/emsg/ID3"" for [ID3v2], vagy **urn: SCTE: scte35:2013: bin** for [SCTE-35]) szabványosítva vannak az iparági konzorciumok közötti együttműködésre. Bármely alkalmazás-szolgáltató definiálhatja saját egyéni sémáját egy olyan URL-cím használatával, amelyet a vezérlő (a tulajdonában lévő tartomány) határoz meg, és az adott URL-címen megadhat egy specifikációt. Ha egy lejátszó rendelkezik kezelővel a definiált sémához, akkor ez az egyetlen olyan összetevő, amelynek ismernie kell a hasznos adatokat és a protokollt.

Az [MPEG-DASH] EventStream XML-adattartalom sémája a következőképpen van definiálva: (kivonat a DASH ISO-IEC-23009-1-3rd Edition kiadásból). Vegye figyelembe, hogy jelenleg csak egy "EventType" adható meg "EventStream". A rendszer csak az első **esemény** elemét dolgozza fel, ha több esemény van megadva a **EventStream**.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Példa XML-eseményre az ID3 séma-AZONOSÍTÓval és a Base64 kódolású adattartalommal.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Példa az esemény-adatfolyamra egyéni séma-AZONOSÍTÓval és Base64 kódolású bináris adattal  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Példa az Event Streamre egyéni séma-AZONOSÍTÓval és egyéni JSON-vel  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>Beépített támogatott séma azonosító URI-k
| Séma AZONOSÍTÓjának URI-ja                 |  Leírás                                             |
|-------------------------------|----------------------------------------------------------|
| https://aomedia.org/emsg/ID3   | Leírja, hogy a [ID3v2] metaadatok hogyan hajthatók végre időzített metaadatokként egy CMAF-kompatibilis [MPEGCMAF] darabolt MP4-ben. További információért lásd az [időzített metaadatokat a Common Media Application Format (CMAF) alkalmazásban.](https://aomediacodec.github.io/av1-id3/) |

### <a name="event-processing-and-manifest-signaling"></a>Események feldolgozása és jegyzékfájlok jelzése

Egy érvényes **"onUserDataEvent"** esemény kézhezvétele után a Azure Media Services egy érvényes XML-adattartalmat keres, amely megfelel a EventStreamType ([MPEGDASH]), elemezve az XML-adattartalmat, és átalakítja a [MPEGCMAF] MP4-töredék "emsg" 1. verziójára tárolás az élő archívumban és továbbítás a Media Services csomagolóhoz.   A csomagoló a "emsg" mezőt fogja felderíteni az élő streamben, és:

- (a) "dinamikusan csomagolt" – a terminálszolgáltatási szegmensek számára történő kézbesítés a HLS-ügyfeleknek a HLS időzített metaadatok specifikációjának megfelelően [HLS-TMD], vagy
- (b) továbbítsa azt a CMAF-töredékek HLS vagy KÖTŐJELen keresztül történő kézbesítéséhez, vagy 
- (c) Smooth Streaming [MS-SSTR] használatával átalakíthatja a továbbítást egy ritka nyomon követési jelre.

Amellett, hogy a sávon belüli "emsg" formátum CMAF vagy TS PES-csomagok a HLS-hez, a kötőjel (MPD) jegyzékfájlja, és Smooth Streaming tartalmazni fog egy hivatkozást a sávon belüli esemény-adatfolyamokra (más néven a Smooth Streaming). 

Az egyes események vagy adattartalmaik nem közvetlenül a HLS, KÖTŐJELhez vagy simított jegyzékfájlokhoz tartoznak. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>További tájékoztató megkötések és alapértékek a onUserDataEvent eseményeihez

- Ha az időskála nincs beállítva a EventStream elemben, a rendszer alapértelmezés szerint az RTMP 1 kHz-es időkeretét használja.
- Egy onUserDataEvent-üzenet kézbesítése a 500ms-k maximális száma szerint van korlátozva. Ha gyakrabban küld eseményeket, az hatással lehet az élő csatorna sávszélességére és stabilitására.

## <a name="212-rtmp-ad-cue-signaling-with-oncuepoint"></a>2.1.2 RTMP ad Cue-jelzés a "onCuePoint" kifejezéssel

Azure Media Services több [AMF0] típusú üzenet figyelésére és reagálására is képes, amelyek különböző valós idejű szinkronizált metaadatokat jelezhetnek az élő adatfolyamban.  Az [Adobe-főkiszolgálói] specifikáció két, "Simple" és "SCTE-35" üzemmódú Cue-típust határoz meg. Az "egyszerű" mód esetében Media Services támogatja a "onAdCue" nevű egyetlen AMF Cue-üzenetet egy olyan adattartalom használatával, amely megfelel az "egyszerű mód" jel számára az alábbi táblázatnak.  

A következő szakasz az RTMP "Simple" (egyszerű) üzemmódját mutatja be, amely a HLS, DASH és Microsoft Smooth Streaming ügyfél-jegyzékfájlján keresztül átvitt alapvető "spliceOut" ad-jel jelzésére használható. Ez olyan esetekben hasznos, amikor az ügyfél nem rendelkezik összetett SCTE-35-alapú ad-jelző üzembe helyezési vagy beszúrási rendszerrel, és egy alapszintű helyszíni kódolót használ, amely egy API-n keresztül küldi el a végszó-üzenetet. A helyszíni kódoló általában egy REST-alapú API-t támogat a jel elindításához, ami "összefonási feltételt" is biztosít a videó streamnek, ha beszúr egy IDR-keretet a videóba, és új GOP-t indít el.

## <a name="213--rtmp-ad-cue-signaling-with-oncuepoint---simple-mode"></a>2.1.3 RTMP ad Cue-jelzés "onCuePoint" – egyszerű mód

| Mező neve | Mező típusa | Kötelező? | Leírások                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| Cue        | Sztring     | Kötelező | Az esemény üzenete.  A "SpliceOut" értékkel kell kijelölni egy egyszerű üzemmód összekötését.                                              |
| id         | Sztring     | Kötelező | A összefonást vagy szegmenst leíró egyedi azonosító. Az üzenet ezen példányát azonosítja.                            |
| duration   | Number     | Kötelező | A Összefonás időtartama. Az egységek töredékes másodpercek.                                                                |
| elapsed    | Number     | Választható | Ha a rendszer megismétli a jelet a hangolásának támogatásához, ez a mező a Kiindulás óta eltelt bemutatási idő mennyiségét jelöli. Az egységek töredékes másodpercek. Egyszerű mód használata esetén ez az érték nem lépheti túl a kötés eredeti időtartamát.                                                  |
| time       | Number     | Kötelező | A összekötés időpontjának kell lennie a bemutató időpontjában. Az egységek töredékes másodpercek.                                     |

---
 
## <a name="214-rtmp-ad-cue-signaling-with-oncuepoint---scte-35-mode"></a>2.1.4 RTMP ad Cue-jelzés "onCuePoint"-SCTE-35 móddal

Ha olyan speciális szórási üzemi munkafolyamattal dolgozik, amelyhez a teljes SCTE-35 adattartalom-üzenetet át kell vinni a HLS vagy a DASH jegyzékfájlba, érdemes az [Adobe-főkiszolgálói] specifikáció "SCTE-35" módszerét használni.  Ez a mód támogatja a sávon kívüli SCTE-35 jeleket, amelyek közvetlenül a helyszíni élő kódolóba kerülnek, amely ezután az [Adobe-főkiszolgálói] specifikációban megadott "SCTE-35 mód" használatával kódolja a jeleket az RTMP-adatfolyamba. 

Általában a SCTE-35 üzenetek csak az MPEG-2 Transport stream (TS) bemeneteken szerepelhetnek egy helyszíni kódolón. A kódoló gyártójával megtudhatja, hogyan konfigurálhat egy olyan átviteli adatfolyamot, amely SCTE-35-et tartalmaz, és engedélyezi azt a RTMP-re az Adobe SCTE-35 módban.

Ebben az esetben a következő adattartalomot kell elküldeni a helyszíni kódolóból az **"onAdCue"** [AMF0] típusú üzenet használatával.

| Mező neve | Mező típusa | Kötelező? | Leírások                                                                                                             |
|------------|------------|----------|--------------------------------------------------------------------------------------------------------------------------|
| Cue        | Sztring     | Kötelező | Az esemény üzenete.  A [SCTE-35] üzenetek esetében ennek a Base64-kódolású [RFC4648] bináris splice_info_section () kell lennie ahhoz, hogy üzeneteket lehessen elküldeni a HLS, a Smooth és a Dash ügyfeleknek.                                              |
| type       | Sztring     | Kötelező | Az üzenet sémáját azonosító URN vagy URL-cím. A (z) [SCTE-35] üzenetek esetében ennek a **"scte35"** értékűnek kell lennie ahhoz, hogy üzeneteket lehessen elküldeni a HLS, a Smooth és a Dash ügyfeleknek az [Adobe-főkiszolgálói] verziónak megfelelően. Szükség esetén az "urn: SCTE: scte35:2013: bin" nevű URN is használható a [SCTE-35] üzenet jelzésére. |
| id         | Sztring     | Kötelező | A összefonást vagy szegmenst leíró egyedi azonosító. Az üzenet ezen példányát azonosítja.  Az egyenértékű szemantikai értékkel rendelkező üzeneteknek azonos értékűeknek kell lenniük.|
| duration   | Number     | Kötelező | Az esemény vagy az ad összekötésének időtartama (ha ismert). Ha ismeretlen, az értéknek 0 értékűnek kell lennie.                                                                 |
| elapsed    | Number     | Választható | Amikor a [SCTE-35] ad-jelet megismétlik a behangolásához, ez a mező a Kiindulás óta eltelt megjelenítési idő mennyiségét mutatja. Az egységek töredékes másodpercek. A [SCTE-35] módban ez az érték túllépheti a Összefonás vagy a szegmens eredeti megadott időtartamát.                                                  |
| time       | Number     | Kötelező | Az esemény vagy az ad összekötésének megjelenítési ideje.  A megjelenítési időt és az időtartamot az 1. és 2. típusú stream hozzáférési pontokkal (SAP) **kell** összehangolni az [ISO-14496-12] i. mellékletben meghatározottak szerint. A kimenő HLS esetében az időt és az időtartamot a szegmens határaihoz **kell** igazítani. Az azonos esemény-adatfolyamon belüli különböző események megjelenítési ideje és időtartama nem lehet átfedésben. Az egységek töredékes másodpercek.

---
## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RTMP ad-jelzés a "onCuePoint" elemmel élő

A helyszíni élő helyszíni kódoló az RTMP-jel ad-jelölőit támogatja. A Azure Media Services jelenleg csak a "onCuePoint" ad-jelölő típusát támogatja az RTMP-hez.  Ez a "**ad_markers**" érték "onCuePoint" értékre állításával engedélyezhető az Adobe RTMP-csoport beállításaiban az elemi média élő kódoló beállításaiban vagy API-ban.  A részletekért tekintse meg az Element Live dokumentációját. Ha engedélyezi ezt a funkciót az RTMP-csoportban, a SCTE-35 jeleket továbbít az Adobe RTMP-kimenetekre, amelyeket Azure Media Services fog feldolgozni.

Az "onCuePoint" típusú üzenet típusa az [Adobe-Flash-AS] értékben van definiálva, és a következő adattartalom-szerkezettel rendelkezik, amikor az elemi élő RTMP kimenetből küldi el.


| Tulajdonság  |Leírás  |
|---------|---------|
|  name     | A névnek "**scte35**" értéknek kell lennie az elemi Live-ban. |
|time     |  Az az idő másodpercben, amikor a Cue-pont bekövetkezett a videóban az idősor során |
| type     | A Cue-pont típusát "**Event**" értékre kell beállítani. |
| parameters | A név/érték párok összevonási tömbje, amely a SCTE-35 üzenetből származó információkat tartalmazza, beleértve az azonosítót és az időtartamot is. Ezeket az értékeket Azure Media Services elemzi, és a jegyzékfájl díszítési címkéjén szerepel.  |


Ha ezt az ad-jelölőt használja, a HLS jegyzékfájl kimenete az Adobe "Simple" (egyszerű) üzemmódhoz hasonló. 

### <a name="216-cancellation-and-updates"></a>2.1.6 törlése és frissítése

Az üzenetek megvonhatók vagy frissíthetők úgy, hogy több üzenetet küldenek ugyanazzal a megjelenítési idővel és AZONOSÍTÓval. A megjelenítési idő és az azonosító egyedi módon azonosítja az eseményt, és egy adott bemutatási időre kapott utolsó üzenet, amely megfelel az előzetes korlátozásoknak, az az üzenet, amelyet a rendszer elvégez. A frissített esemény felülírja a korábban fogadott üzeneteket. A pre-roll korlátozás négy másodperc. A bemutató időpontja előtt legalább négy másodpercig fogadott üzenetek lesznek elvégezve.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2,2 töredezett MP4 betöltés (Smooth Streaming)

Az élő stream betöltésével kapcsolatos követelményekért tekintse meg az [MS-SSTR-betöltés] című témakört. A következő szakaszokban részletesen ismertetjük az időzített bemutató metaadatainak betöltésével kapcsolatos részleteket.  Az időzített megjelenítési metaadatokat a rendszer ritka számként tölti be, amely az élő kiszolgáló jegyzékfájljában (lásd: MS-SSTR) és a Movie Box ("Moov") van meghatározva.  

Minden ritka töredék egy film fragment Box ("moof") és a Media Data Box ("mdat")ből áll, ahol a "mdat" mező a bináris üzenet.

A keretek pontos beszúrásához a kódolónak fel kell osztania a töredéket a bemutató időpontjában, ahol a végszót be kell szúrni.  Létre kell hozni egy új töredéket, amely egy újonnan létrehozott IDR-kerettel kezdődik, vagy 1. vagy 2. típusú stream hozzáférési pontokkal (SAP), az [ISO-14496-12] i. mellékletben meghatározottak szerint. Ez lehetővé teszi az Azure adathordozó-csomagoló számára, hogy megfelelően létrehozzon egy HLS-jegyzékfájlt és egy KÖTŐJELes többpontos jegyzékfájlt, ahol az új időszak a kereten belüli, összefonásra vonatkozó, feltételes bemutató időpontjában kezdődik.

### <a name="221-live-server-manifest-box"></a>2.2.1 élő kiszolgáló jegyzékfájlja

A ritka számot be kell jelenteni az élő kiszolgáló jegyzékfájljában egy **\<textstream\>** -bejegyzéssel, és a következő tulajdonságokkal **kell** rendelkeznie:

| **Attribútum neve** | **Mező típusa** | **Szükséges?** | **Leírás**                                                                                                                                                                                                                                                 |
|--------------------|----------------|---------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| systemBitrate      | Number         | Kötelező      | "0" értéknek kell lennie, amely egy ismeretlen, változó bitsebességű számot jelez.                                                                                                                                                                                                 |
| parentTrackName    | Sztring         | Kötelező      | A fölérendelt nyomon követési időszaknak kell lennie, amelyhez a ritka nyomon követési időkódok időkerete igazított. A szülő nyomon követése nem lehet ritka.                                                                                                                    |
| manifestOutput     | Logikai        | Kötelező      | A "true" értéknek kell lennie, amely azt jelzi, hogy a ritka nyomon követés be lesz ágyazva a zökkenőmentes ügyfél-jegyzékfájlba.                                                                                                                                                               |
| Altípus            | Sztring         | Kötelező      | Az "adat" négy karakterből álló kódnak kell lennie.                                                                                                                                                                                                                        |
| Séma             | Sztring         | Kötelező      | Az üzenet-sémát azonosító urn vagy URL-címnek kell lennie. A (z) [SCTE-35] üzenetek esetében ennek az "urn: SCTE: scte35:2013: bin" értékűnek kell lennie ahhoz, hogy üzeneteket lehessen ELKÜLDENI a HLS, a Smooth és a Dash ügyfeleknek a [SCTE-35] megfelelőséggel. |
| trackName          | Sztring         | Kötelező      | A ritka sáv nevének kell lennie. A trackName használatával több esemény-adatfolyamot is megkülönböztetni ugyanazzal a sémával. Minden egyedi esemény-adatfolyamnak egyedi nyomkövetési névvel **kell** rendelkeznie.                                                                           |
| timescale          | Number         | Választható      | A szülő nyomon követési időkeretének kell lennie.                                                                                                                                                                                                                      |

---

### <a name="222-movie-box"></a>2.2.2 Movie Box

A Movie Box ("Moov") az élő kiszolgáló jegyzékfájljának részeként követi a ritka nyomon követéshez használt stream-fejlécet.

A "Moov" mezőnek tartalmaznia **kell** egy **TrackHeaderBox ("tkhd")** , az [ISO-14496-12] mezőben meghatározottak szerint, a következő korlátozásokkal:

| **Mező neve** | **Mező típusa**          | **Szükséges?** | **Leírás**                                                                                                |
|----------------|-------------------------|---------------|----------------------------------------------------------------------------------------------------------------|
| duration       | 64 bites előjel nélküli egész szám | Kötelező      | 0 értékűnek kell lennie, mivel a Track Box nulla mintákat tartalmaz, a számok teljes időtartama pedig 0. |

---

A "Moov" mezőnek tartalmaznia **kell** egy **HandlerBox ("hdlr")** az [ISO-14496-12] definiált módon az alábbi korlátozásokkal:

| **Mező neve** | **Mező típusa**          | **Szükséges?** | **Leírás**   |
|----------------|-------------------------|---------------|-------------------|
| handler_type   | 32 bites előjel nélküli egész szám | Kötelező      | A "meta" értéknek kell lennie. |

---

A "stsd" mezőnek tartalmaznia **kell** egy, az [ISO-14496-12] által definiált kódolási névvel rendelkező MetaDataSampleEntry-mezőt.  A SCTE-35 üzenetek esetében például a kódolás neve legyen "SCTE".

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Movie fragment Box és Media Data Box

A ritka követési töredékek egy film töredékét ("moof") és egy adathordozó-Data Box ("mdat") tartalmaznak.

> [!NOTE]
> A keretek pontos beszúrásához a kódolónak fel kell osztania a töredéket a bemutató időpontjában, ahol a végszót be kell szúrni.  Létre kell hozni egy új töredéket, amely egy újonnan létrehozott IDR-kerettel kezdődik, vagy 1. vagy 2. típusú stream hozzáférési pontok (SAP), az [ISO-14496-12] i. mellékletben meghatározottak szerint.
> 

A MovieFragmentBox ("moof") mezőben szerepelnie **kell** egy, az [MS-SSTR] mezőben definiált **TrackFragmentExtendedHeaderBox (UUID)** , amely a következő mezőket tartalmazza:

| **Mező neve**         | **Mező típusa**          | **Szükséges?** | **Leírás**                                                                               |
|------------------------|-------------------------|---------------|-----------------------------------------------------------------------------------------------|
| fragment_absolute_time | 64 bites előjel nélküli egész szám | Kötelező      | Az esemény érkezési időpontjának kell lennie. Ez az érték az üzenetet a fölérendelt nyomon követéssel igazítja.   |
| fragment_duration      | 64 bites előjel nélküli egész szám | Kötelező      | Az esemény időtartamának kell lennie. Az időtartam nulla lehet, jelezve, hogy az időtartam ismeretlen. |

---


A MediaDataBox ("mdat") mezőnek a következő formátumúnak kell **lennie** :

| **Mező neve**          | **Mező típusa**                   | **Szükséges?** | **Leírás**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|-------------------------|----------------------------------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| version                 | 32 bites előjel nélküli egész szám (uimsbf) | Kötelező      | Meghatározza a "mdat" mező tartalmának formátumát. Ismeretlen verziók figyelmen kívül lesznek hagyva. Jelenleg az egyetlen támogatott verzió az 1.                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 bites előjel nélküli egész szám (uimsbf) | Kötelező      | Az üzenet ezen példányát azonosítja. Az egyenértékű szemantikai értékkel rendelkező üzeneteknek azonos értékűeknek kell lenniük; Ez egy, az ugyanazzal az azonosítóval rendelkező esemény-üzenetpanel feldolgozása is elegendő.                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 bites előjel nélküli egész szám (uimsbf) | Kötelező      | A TrackFragmentExtendedHeaderBox megadott fragment_absolute_time összegének és a presentation_time_delta az esemény megjelenítési időpontjának kell lennie. A megjelenítési időt és az időtartamot az 1. és 2. típusú stream hozzáférési pontokkal (SAP) **kell** összehangolni az [ISO-14496-12] i. mellékletben meghatározottak szerint. A kimenő HLS esetében az időt és az időtartamot a szegmens határaihoz **kell** igazítani. Az azonos esemény-adatfolyamon belüli különböző események megjelenítési ideje és időtartama nem **lehet** átfedésben. |
| message                 | bájttömb                       | Kötelező      | Az esemény üzenete. A [SCTE-35] üzenetek esetében az üzenet a bináris splice_info_section (). A [SCTE-35] üzenetek esetében ennek a splice_info_section () értéknek kell lennie ahhoz, hogy üzeneteket lehessen ELKÜLDENI a HLS, a Smooth és a Dash ügyfeleknek a [SCTE-35] megfelelőséggel. A (z) [SCTE-35] üzenetek esetében a bináris splice_info_section () a "mdat" mező hasznos adatai, és **nem** Base64 kódolású.                                                            |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 törlés és frissítések

Az üzenetek megvonhatók vagy frissíthetők úgy, hogy több üzenetet küldenek ugyanazzal a megjelenítési idővel és AZONOSÍTÓval.  A megjelenítési idő és az azonosító egyedi módon azonosítja az eseményt. Az utolsó üzenet, amely egy adott bemutatási időre érkezett, amely megfelel az előre meghatározott megkötéseknek, az az üzenet, amelyet a rendszer elvégez. A frissített üzenet felülírja a korábban fogadott üzeneteket.  A pre-roll korlátozás négy másodperc. A bemutató időpontja előtt legalább négy másodpercig fogadott üzenetek lesznek elvégezve. 


## <a name="3-timed-metadata-delivery"></a>3 időzített metaadatok kézbesítése

Az esemény stream-adatterülete nem átlátszó Media Services. Media Services csupán három adatot továbbít a betöltési végpont és az ügyfél végpontja között. A rendszer a következő tulajdonságokat továbbítja az ügyfélnek a [SCTE-35] és/vagy az ügyfél folyamatos átviteli protokolljának megfelelően:

1.  Séma – az üzenet sémáját azonosító URN vagy URL-cím.
2.  Megjelenítés időpontja – az esemény megjelenítési időpontja az adathordozó idővonalán.
3.  Időtartam – az esemény időtartama.
4.  ID – az eseményhez nem kötelező egyedi azonosító.
5.  Üzenet – az eseményre vonatkozó adatértékek.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3,1 Microsoft Smooth Streaming jegyzékfájl  

A ritka üzenetek nyomon követésével kapcsolatos részletekért tekintse meg a ritka kezelés [MS-SSTR] című témakört. A [SCTE35] üzenetek esetében Smooth Streaming a Base64 kódolású splice_info_section () kimenetét egy ritka töredékbe helyezi.
A StreamIndex tartalmaznia **kell** az "adattípust", és a CustomAttributes tartalmaznia **kell** egy "Schema" és Value = "urn: SCTE: scte35:2013: bin" nevű attribútumot.

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Zökkenőmentes ügyféloldali jegyzékfájl, példa Base64 kódolású [SCTE35] splice_info_section ()
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
  <StreamIndex Type=”text” Name=”scte35-sparse-stream” Subtype=”DATA” Chunks=”0” TimeScale=”10000000”
    ParentStreamIndex=”video” ManifestOutput=”true” 
    Url=”QualityLevels({bitrate})/Fragments(captions={start time})”>
    <QualityLevel Index=”0” Bitrate=”0” CodecPrivateData=”” FourCC=””>
      <CustomAttributes>
        <Attribute Name=”Scheme” Value=”urn:scte:scte35:2013:bin”/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t=”600000000” d=”300000000”>    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t=”1200000000” d=”400000000”>      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3,2 Apple HLS-jegyzékfájlok díszítése

A Azure Media Services a következő HLS-jegyzékeket támogatja az ad-adatoknak az élő vagy igény szerinti eseményeknél való jelzéséhez. 

- EXT-X-DATERANGE az Apple HLS definiálva [RFC8216]
- EXT-X-CUE az [Adobe-főkiszolgálói] meghatározása szerint – ez a mód "örökölt". Ha lehetséges, az ügyfeleknek el kell fogadniuk az EXT-X-DATERANGE címkét.

Az egyes címkék adatkimenete a felhasznált betöltési mód alapján változhat. Például az Adobe Simple Mode módban való betöltés nem tartalmazza a teljes SCTE-35 Base64 kódolású adattartalmat.

## <a name="321-apple-hls-with-adobe-primetime-ext-x-daterange-recommended"></a>3.2.1 Apple HLS az Adobe főműsoridőhez EXT-X-DATERANGE (ajánlott)

Az Apple HTTP Live Streaming [RFC8216] specifikációja lehetővé teszi a [SCTE-35] üzenetek jelzését. Az üzenetek bekerülnek a szegmens lejátszási listára egy EXT-X-DATERANGE címke alapján [RFC8216] szakaszban, melynek címe: "Mapping SCTE-35 in EXT-X-DATERANGE".  Az ügyfélalkalmazás rétege elemezheti az M3U-listát, és feldolgozhatja az M3U-címkéket, vagy megtekintheti az eseményeket az Apple Player Framework használatával.  

A Azure Media Services (3. verzió API) **ajánlott** megközelítése a [RFC8216] követése, és a (SCTE35) ad X_DATERANGE címke használata a jegyzékfájlban.

## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS az Adobe főműsoridőben EXT-X-CUE (örökölt)

Létezik egy "örökölt" implementáció is a Azure Media Services (2. és 3. verziójú API), amely az EXT-X-CUE címkét használja az [Adobe-főkiszolgálói] "SCTE-35 Mode" kifejezésben meghatározottak szerint. Ebben a módban a Azure Media Services Base64 kódolású [SCTE-35] splice_info_section () beágyazását fogja beágyazni az EXT-X-CUE címkébe.  

Az "örökölt" EXT-X-CUE címke az alábbi módon van definiálva, és az [Adobe-főkiszolgálói] specifikációban hivatkozott normatíva is lehet. Ezt csak az örökölt SCTE35-jelzésekhez szabad használni, ha szükséges, ellenkező esetben az ajánlott címke a [RFC8216]-ben van definiálva EXT-X-DATERANGE. 

| **Attribútum neve** | **Típus**                      | **Szükséges?**                             | **Leírás**                                                                                                                                                                                                                                                                      |
|--------------------|-------------------------------|-------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CUE                | idézett karakterlánc                 | Kötelező                                  | Az üzenet Base64 kódolású karakterláncként van kódolva a következővel: [RFC4648]. A [SCTE-35] üzenetek esetében ez a Base64 kódolású splice_info_section ().                                                                                                |
| TÍPUS               | idézett karakterlánc                 | Kötelező                                  | Az üzenet sémáját azonosító URN vagy URL-cím. A (z) [SCTE-35] üzenetek esetében a típus a következő speciális értéket veszi fel: "scte35".                                                                                                                                |
| id                 | idézett karakterlánc                 | Kötelező                                  | Az esemény egyedi azonosítója. Ha az üzenet betöltése után nincs megadva az azonosító, Azure Media Services egyedi azonosítót fog előállítani.                                                                                                                                          |
| IDŐTARTAMA           | decimális lebegőpontos szám | Kötelező                                  | Az esemény időtartama. Ha ismeretlen, az értéknek 0 értékűnek kell lennie. Az egységek a frakciós másodpercek.                                                                                                                                                                                           |
| ELAPSED            | decimális lebegőpontos szám | Nem kötelező, de kötelező a csúszó ablakhoz | Ha a rendszer megismétli a jelet a csúszó bemutató ablakának támogatásához, akkor ebben a mezőben az esemény megkezdése óta eltelt bemutatási idő mennyiségének kell lennie. Az egységek töredékes másodpercek. Ez az érték túllépheti a Összefonás vagy a szegmens eredeti megadott időtartamát. |
| TIME               | decimális lebegőpontos szám | Kötelező                                  | Az esemény megjelenítési időpontja. Az egységek töredékes másodpercek.                                                                                                                                                                                                                    |


A HLS Player alkalmazás rétege a típus használatával azonosítja az üzenet formátumát, dekódolja az üzenetet, alkalmazza a szükséges időkonverziókat, és feldolgozza az eseményt.  Az események az esemény időbélyegének megfelelően szinkronizálva lesznek a fölérendelt sáv szegmensének lejátszási listájában.  Ezeket a rendszer a legközelebbi szegmens (#EXTINF címke) elé szúrja be.

### <a name="323-hls-segment-playlist-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS-szegmens lejátszási példája az "örökölt" Adobe főműsoridőnek EXT-X-CUE használatával

Az alábbi példa az Adobe főkiszolgálói EXT-X-CUE címke használatával mutatja be a HLS-jegyzékfájlok díszítését.  A "CUE" paraméter tartalmazza a teljes Base64 kódolású SCTE-35 splice_info adattartalomot, amely azt jelzi, hogy ez a jel a RTMP-t használja az Adobe SCTE-35 jel módban, vagy Smooth Streaming SCTE-35 jel módban. 

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

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 HLS az "örökölt" Adobe főműsoridőhez EXT-X-CUE

Az események az egyes videó-és hangsávok szegmensének lejátszási listáján vannak jelezve. Az EXT-X-CUE címke pozíciójának mindig közvetlenül az első HLS-szegmens (összekapcsolás vagy szegmens elején), vagy közvetlenül az utolsó HLS-szegmens (Összefonás vagy szegmens végén) után kell lennie, amely az idő-és időtartam-attribútumokra vonatkozik. kötelező: [Adobe-főműsoridőben].

Ha engedélyezve van egy csúszó bemutató ablak, a EXT-X-CUE címkét gyakran **meg** kell ismételni ahhoz, hogy a összekötés vagy a szegmens mindig teljes mértékben le legyen írva a szegmens lejátszási listájában, és az eltelt attribútumot fel kell használni, hogy jelezze, mennyi ideig kell a Összefonás vagy a szegmens aktív, az [Adobe-főkiszolgálói] által megkövetelt módon.

Ha engedélyezve van egy csúszó bemutató ablak, a rendszer eltávolítja az EXT-X-CUE címkét a szegmens lejátszási listáról, amikor az általa hivatkozott adathordozó-idő kiesik a csúszó bemutató ablakból.

## <a name="33-dash-manifest-decoration-mpd"></a>3,3 DASH-jegyzékfájl díszítése (MPD)

A [MPEGDASH] három módszert biztosít az események jelzésére:

1.  Az MPD EventStream jelzett események
2.  A sávon belül megjelenő események az Event Message Box ("emsg") használatával
3.  1 és 2 kombináció

Az MPD-EventStream jelzett események hasznosak a VOD-adatfolyamok számára, mivel az ügyfelek az összes eseményhez hozzáférnek, azonnal az MPD letöltésekor. A SSAI jelzése is hasznos, ahol az alsóbb rétegbeli SSAI gyártójának a jeleket egy többpontos MPD-jegyzékből kell elemezni, és az ad-tartalmat dinamikusan kell beszúrnia.  A sávon kívüli ("emsg") megoldás az élő adatfolyamok esetében hasznos, ahol az ügyfeleknek nem kell újra letölteniük az MPD-t, vagy az ügyfél és a forrás között nem történik SSAI-jegyzékfájl-manipuláció. 

Azure Media Services a kötőjel alapértelmezett viselkedése az MPD EventStream és sávon belüli, az Event Message Box ("emsg") használatával történő jel.

Az [RTMP] vagy [MS-SSTR-betöltés] során betöltött Cue-üzenetek KÖTŐJELes eseményekre vannak leképezve, sávon belüli "emsg" és/vagy in-MPD EventStreams használatával. 

Sávon belüli SCTE – 35 jelzés a KÖTŐJELhez a [SCTE-214-3] és a (z) [DASH-IF-IOP] szakasz 13.12.2 ("SCTE35 Events") meghatározott definícióját és követelményeit követi. 

A sávon belüli [SCTE-35] szállítás esetében az eseményazonosító ("emsg") a schemeId = "urn: SCTE: scte35:2013: bin" karaktert használja. Az MPD-jegyzékfájl díszítéséhez a EventStream schemeId az "urn: SCTE: scte35:2014: XML + bin" kifejezést használja.  Ez a formátum az esemény XML-ábrázolása, amely tartalmazza a betöltéskor megérkezett teljes SCTE-35 üzenet bináris Base64-kódolású kimenetét. 

A [SCTE-35] Cue-üzenetek DASH-ben történő szállításának normatív referenciái a [SCTE-214-1] SEC 6.7.4 (MPD) és [SCTE-214-3] SEC 7.3.2 (SCTE 35 Cue-üzenetek szállítása).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH (MPD) EventStream-jelzés

Az események manifest (MPD) díszítését az MPD jelzi az EventStream elem használatával, amely az időszak elemen belül jelenik meg. A használt schemeId az "urn: SCTE: scte35:2014: XML + bin".

> [!NOTE]
> A rövidség kedvéért a [SCTE-35] lehetővé teszi a Base64 kódolású szakasz használatát a Signal. Binary elemben (a Signal. SpliceInfoSection elem helyett), amely egy teljesen elemzett Cue-üzenet szállításának alternatívájaként.
> A Azure Media Services ezt az "XML + bin" módszert használja az MPD jegyzékfájlban való megjelenítéshez.
> Ez az ajánlott módszer az [DASH-IF-IOP] – Lásd a [kötőjel "ad inserting Event Streams" című szakaszát, ha IOP-útmutató](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

A EventStream elem a következő attribútumokkal rendelkezik:

| **Attribútum neve** | **Típus**                | **Szükséges?** | **Leírás**                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri      | Karakterlánc                  | Kötelező      | Az üzenet sémáját azonosítja. A séma a séma attribútum értékeként van beállítva az élő kiszolgáló jegyzékfájlja mezőben. Az értéknek az üzenet sémáját azonosító URN-nek vagy URL-címnek kell lennie; A támogatott kimeneti schemeId "urn: SCTE: scte35:2014: XML + bin"/[SCTE-214-1] SEC 6.7.4 (MPD) lehet, mivel a szolgáltatás jelenleg csak az "XML + bin"-t támogatja, ha rövid idő alatt az MPD szerepel.  |
| value              | Karakterlánc                  | Választható      | Egy további karakterlánc-érték, amelyet a séma tulajdonosai használ az üzenet szemantikai testreszabásához. Annak érdekében, hogy a több esemény streamjét ugyanazzal a sémával megkülönböztesse , az értéket az esemény-adatfolyam nevére kell beállítani (trackName az [MS-SSTR-betöltés] vagy az AMF-üzenet neveként az [RTMP] betöltéséhez). |
| Timescale          | 32 bites előjel nélküli egész szám | Kötelező      | Az időskála másodpercenkénti száma.                                                                                                                                                                                                       |


### <a name="332-example-mpeg-dash-manifest-mpd-signaling-of-scte-35-using-eventstream"></a>3.3.2 példa az SCTE-35 MPEG DASH manifest (MPD) jelzésére a EventStream használatával

~~~ xml
<!-- Example MPD using xml+bin style signaling per [SCTE-214-1] -->
  <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35_track_001_000" timescale="10000000">
        <Event presentationTime="15447165200227600" duration="300000000" id="1026">
            <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
        <Event presentationTime="15447166250227600" duration="300000000" id="1027">
           <scte35:Signal>
                <scte35:Binary>
                    /DAlAAAAAAAAAP/wFAUAAAQDf+//KaeGwP4AKTLgAAAAAAAAn75a3g==
                </scte35:Binary>
            </scte35:Signal>
        </Event>
    </EventStream>
~~~

> [!IMPORTANT]
> Vegye figyelembe, hogy a presentationTime a [SCTE-35] eseménynek a lefordított időponthoz viszonyított időpontja, amely nem az üzenet érkezési időpontja.
> A (z) [ Event@presentationTime MPEGDASH] a következőt határozza meg: "az esemény megjelenítésének időpontja az időszak elejéhez viszonyítva.
> A megjelenítési idő másodpercben megadott értéke az attribútum értékének és az EventStream@timescale attribútum értékének a felosztása.
> Ha nincs jelen, a megjelenítési idő értéke 0.


### <a name="333-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.3 MPEG-kötőjel sávon belüli Eseménynapló-jelzés

A sávon kívüli adatfolyamok esetén az MPD-nek egy InbandEventStream elemre van szüksége a beállított értéknél.  Ez az elem egy kötelező schemeIdUri attribútummal és választható időkeret-attribútummal rendelkezik, amely szintén megjelenik az esemény üzenet mezőjében ("emsg").  Az MPD-ben nem definiált séma-azonosítókkal rendelkező esemény- üzenetsor nem lehet jelen.

Sávon belüli [SCTE-35] szállítás esetén a jeleknek a schemeId = "urn: SCTE: scte35:2013: bin" kifejezést **kell** használniuk.
A [SCTE-35] sávon belüli üzenetek [SCTE-214-3] SEC 7.3.2 (SCTE 35 Cue-üzenetek szállítása) által definiált normatív meghatározásai.

A következő részletek felvázolják, hogy az ügyfélnek milyen értékeket kell várnia a "emsg" a [SCTE-214-3] megfelelőséggel:

| **Mező neve**          | **Mező típusa**          | **Szükséges?** | **Leírás**                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|-------------------------|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| scheme_id_uri           | Karakterlánc                  | Kötelező      | Az üzenet sémáját azonosítja. A séma a séma attribútum értékeként van beállítva az élő kiszolgáló jegyzékfájlja mezőben. Az értéknek az üzenet-sémát azonosító URN- **nek** kell lennie. A (z) [SCTE-35] üzenetek esetében ennek a következőnek kell lennie: "urn: SCTE: scte35:2013: bin" a [SCTE-214-3] megfelelőséggel |
| Value                   | Karakterlánc                  | Kötelező      | Egy további karakterlánc-érték, amelyet a séma tulajdonosai használ az üzenet szemantikai testreszabásához. Annak érdekében, hogy a több esemény streamjét ugyanazzal a sémával megkülönböztesse, az érték az esemény-adatfolyam neve lesz (trackName a Smooth betöltéshez vagy az AMF-üzenet neve az RTMP betöltéséhez).                                                                  |
| Timescale               | 32 bites előjel nélküli egész szám | Kötelező      | A "emsg" mezőben lévő időpontok és időtartam mezők időkerete (óra/s).                                                                                                                                                                                                                                                                        |
| Presentation_time_delta | 32 bites előjel nélküli egész szám | Kötelező      | A média bemutatójának időbeli változása az esemény megjelenítési idejének és a legkorábbi bemutatási idő különbözete ebben a szegmensben. A megjelenítési időt és az időtartamot az 1. és 2. típusú stream hozzáférési pontokkal (SAP) **kell** összehangolni az [ISO-14496-12] i. mellékletben meghatározottak szerint.                                                                                            |
| event_duration          | 32 bites előjel nélküli egész szám | Kötelező      | Az esemény vagy a 0xFFFFFFFF-es időtartam, amely ismeretlen időtartamot jelez.                                                                                                                                                                                                                                                                                          |
| Id                      | 32 bites előjel nélküli egész szám | Kötelező      | Az üzenet ezen példányát azonosítja. Az egyenértékű szemantikai értékkel rendelkező üzeneteknek azonos értékűeknek kell lenniük. Ha az üzenet betöltése után nincs megadva az azonosító, Azure Media Services egyedi azonosítót fog előállítani.                                                                                                                                                    |
| Message_data            | bájttömb              | Kötelező      | Az esemény üzenete. A [SCTE-35] üzenetek esetében az üzenet a bináris splice_info_section (), amely megfelel a következőnek: [SCTE-214-3]. |

### <a name="334-dash-message-handling"></a>3.3.4 DASH-üzenetek kezelésére

Az eseményeket a sávon belül, a "emsg" mezőben, a videó-és hangsávok esetében is jelezni kell.  Az összes olyan szegmensre vonatkozó kérelem, amelynél a presentation_time_delta 15 másodperc vagy kevesebb. 

Ha a csúszó bemutató ablak engedélyezve van, az esemény üzenetei el lesznek távolítva az MPD-ből, ha az üzenet időtartamának és időtartamának összege kevesebb, mint a jegyzékfájlban lévő adathordozó-adatok ideje.  Ez azt jelenti, hogy az esemény üzenetei el lesznek távolítva a jegyzékből, ha az arra a médiára vonatkozó idő, amelyre hivatkozik, kigördült a csúszó bemutató ablakból.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 bevezetési útmutató a kódoló-szállítókhoz

Az alábbi irányelvek olyan gyakori problémák, amelyek hatással lehetnek a kódoló gyártó általi megvalósítására a specifikációban.  Az alábbi irányelveket a valós partneri visszajelzések alapján gyűjtöttük össze, hogy könnyebben lehessen megvalósítani ezt a specifikációt mások számára. 

[SCTE-35] az üzeneteket bináris formátumban kell beírni az **"urn: SCTE: scte35:2013: bin"** sémával az [MS-SSTR-betöltés] és a (z) [RTMP] esetében a **"scte35"** típus használatával. A (z) [SCTE-35] időzítésének megkönnyítése érdekében, amely az MPEG-2 Transport stream bemutató időbélyegei (PTS) alapján történik, a PTS (pts_time + pts_adjustment, a splice_time ()) és az adathordozó idővonala közötti leképezést az esemény bemutatási ideje határozza meg ( a fragment_absolute_time mező a zökkenőmentes betöltéshez és az RTMP betöltésének időmezőjéhez. A leképezésre azért van szükség, mert a 33 bites PTS-érték körülbelül 26,5 óránként történik.

Smooth Streaming betöltés [MS-SSTR-betöltés] megköveteli, hogy a Media Data Box ("mdat") tartalmaznia **kell** a (z) [SCTE-35] által definiált **splice_info_section ()** . 

Az RTMP betöltéséhez az AMF-üzenet Cue attribútuma a [SCTE-35] által definiált Base64 kódolású **splice_info_section ()** értékre van állítva.  

Ha az üzenetek a fent ismertetett formátumban vannak megadva, a rendszer elküldi őket a HLS, a Smooth és a DASH ügyfeleknek a fentiekben leírtak szerint.  

Ha teszteli a megvalósítást a Azure Media Services platformmal, először a "pass-through" LiveEvent kell elkezdeni a tesztelést, mielőtt a kódolást LiveEvent.

---

## <a name="change-history"></a>Módosítási előzmények

| Date     | Módosítások                                                                            |
|----------|------------------------------------------------------------------------------------|
| 07/2/19  | Átdolgozott RTMP betöltés a SCTE35-támogatáshoz, új RTMP "onCuePoint" | 
| 08/22/19 | Frissítve a OnUserDataEvent és az RTMP egyéni metaadatokhoz való hozzáadásához                         |

## <a name="next-steps"></a>További lépések
Megtekintheti Media Services képzési útvonalakat.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
