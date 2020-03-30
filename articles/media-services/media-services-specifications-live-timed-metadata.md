---
title: Azure Media Services – Időzített metaadatok jelzése élő közvetítésben
description: Ez a specifikáció ismerteti az idővel időzített metaadatok jelzésének módszereit az Azure Media Services betöltése és streamelése során. Ez magában foglalja az általános időzített metaadat-jelek (ID3) támogatását, valamint az SCTE-35 jelzést a hirdetés beillesztéséhez és az állapotjelzések összeillesztéséhez.
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
ms.openlocfilehash: 551fb0cb9f3745a62d5d84f2c4878bbbbe5ad9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137322"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Időzített metaadatok jelzése az élő közvetítésben 

Utoljára frissítve: 2019-08-22

### <a name="conformance-notation"></a>Szabálytalanság jelölése

A dokumentumban szereplő "MUST", "MUST", "REQUIRED", "SHALL", "NOT", "SHOULD", "NOT", "RECOMMENDED", "MAY" és "OPTIONAL" kulcsszavakat a 2119.

## <a name="1-introduction"></a>1. Bevezetés 

Annak jelzésére, hogy hirdetéseket vagy egyéni metaadat-eseményeket helyeznek el egy ügyféllejátszón, a műsorszolgáltatók gyakran használják a videóba ágyazott időzített metaadatokat. Ezeknek a forgatókönyveknek az engedélyezéséhez a Media Services támogatja az időzített metaadatok átvitelét az élő közvetítési csatorna betöltési pontjáról az ügyfélalkalmazásba.
Ez a specifikáció számos olyan módot vázol fel, amelyet a Media Services támogat az élő adatfolyam-jeleken belüli időzített metaadatokhoz.

1. [SCTE-35] jelzés, amely megfelel az [SCTE-35], [SCTE-214-1], [SCTE-214-3] és [RFC8216] által meghatározott szabványoknak

2. [SCTE-35] jelzés, amely megfelel az RTMP-hirdetésjelzés örökölt [Adobe-Primetime] specifikációjának.
   
3. Általános időzített metaadat-jelző mód, olyan üzenetek esetén, amelyek **NEM** [SCTE-35] üzenetek, és [ID3v2] vagy az alkalmazás fejlesztője által meghatározott más egyéni sémákat hordozhatnak.

## <a name="11-terms-used"></a>1.1 Használt kifejezések

| Időtartam                | Meghatározás                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hirdetésszünet            | Olyan hely vagy időpont, ahol egy vagy több hirdetés jeleníthető meg; megegyezik a haszonnal és az elhelyezési lehetőséggel.                                                                                                                     |
| Hirdetésdöntési szolgáltatás | külső szolgáltatás, amely eldönti, hogy mely hirdetés(ek) és időtartamok jelenjenek meg a felhasználó számára. A szolgáltatásokat általában egy partner biztosítja, és nem tartoznak a dokumentum hatálya alól.                                                                    |
| Végszó                 | A közelgő hirdetésszünet idő- és paramétereinek megjelölése. Ne feledje, hogy a jelzések jelezhetik a függőben lévő váltást egy hirdetésszünetre, függőben a következő hirdetésre való váltást egy hirdetésszüneten belül, és függőben lévő váltást a hirdetésszünetről a fő tartalomra.           |
| Packager            | Az Azure Media Services "Streaming Endpoint" dinamikus csomagolási lehetőségeket biztosít a DASH és a HLS számára, és a médiaiparban "Packager"-nek nevezik.                                                                              |
| Bemutató ideje   | Az az időpont, amikor egy eseményt a megjelenítő elé terjeszt. Az idő azt a pillanatot jelöli a média idővonalán, amikor a megtekintő látni fogja az eseményt. Egy SCTE-35 splice_info() parancsüzenet megjelenítési ideje például a splice_time(). |
| Érkezési idő        | Az eseményüzenet megérkezésének időpontja. Az idő általában különbözik az esemény megjelenítési idejétől, mivel az eseményüzenetek et az esemény megjelenítési ideje előtt küldi el a rendszer.                                                    |
| Ritka pálya        | nem folytonos, és egy szülő- vagy vezérlősávval szinkronizált idő.                                                                                                                                                  |
| Forrás              | Az Azure Media streaming szolgáltatás                                                                                                                                                                                                             |
| Csatorna mosogató        | Az Azure Media Élő streaming szolgáltatás                                                                                                                                                                                                        |
| HLS                 | Apple HTTP Live Streaming protokoll                                                                                                                                                                                                            |
| Kötőjel                | Dinamikus adaptív streamelés HTTP-n keresztül                                                                                                                                                                                                          |
| Sima              | Zökkenőmentes streamelési protokoll                                                                                                                                                                                                                     |
| MPEG2-TS            | MPEG 2 átviteli adatfolyamok                                                                                                                                                                                                                      |
| RTMP                | Valós idejű multimédiás protokoll                                                                                                                                                                                                                 |
| uimsbf között              | Aláíratlan egész szám, a legjelentősebb bit először.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 Normatív referenciák

A következő dokumentumok olyan rendelkezéseket tartalmaznak, amelyek e szövegben való hivatkozással e dokumentum rendelkezéseit képezik. A szabványügyi testületek minden dokumentumot felülvizsgálnak, és az olvasókat arra ösztönzik, hogy vizsgálják meg az alább felsorolt dokumentumok legfrissebb kiadásainak alkalmazásának lehetőségét. Az olvasók at is emlékezteti, hogy a hivatkozott dokumentumok újabb kiadásai nem feltétlenül kompatibilisek az Azure Media Services időzített metaadat-specifikációjának ezen verziójával.


| Standard          | Meghatározás                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Primetime digitális program beillesztése jelző specifikáció 1,2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [FLASH ActionScript nyelvi útmutató](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| - Nem, nem, nem, nem, nem, nem, nem, nem, nem, nem            | ["AmF0 műveletkérő üzenet formátuma"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | DASH Ipari Fórum Interop Útmutató v 4.2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Időzített metaadatok a HTTP Live Streaming szolgáltatáshoz -[https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Időzített metaadatok a közös médiaalkalmazás-formátumban (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 Címke 2.4.0-s verzió[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: 12. rész ISO alapmédia fájlformátum, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Informatika - Dinamikus adaptív streamelés HTTP-n (DASH) keresztül - 1. 2014. május. Közzétett. Url:https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Információtechnológia -- Multimédia alkalmazásformátum (MPEG-A) -- 19. 2018. január. Közzétett. Url:https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Informatika -- MPEG-rendszerek technológiái -- 7. 2016 februárjában. Közzétett. Url:https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 2014.](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-Betöltés]  | [Az Azure Media Services töredezett MP4 élő betöltési specifikációja](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | R. Pantos, Ed.; W. Május. HTTP élő közvetítés. 2017 augusztusában. Információs. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | A Base16, Base32 és Base64 adatkódolások -[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| - Nem, nem, nem, nem, nem, nem, nem            | ["Az Adobe valós idejű üzenetküldő protokollja", 2012.](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35: 2019 - Digitális program beillesztése üzenet kábelhez -https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH az IP-alapú kábelszolgáltatások 1.                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH az IP-alapú kábelszolgáltatások 3.                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – Eseményütemezési és értesítési felület                                                                                                                                                  |
| [SCTE-250]        | Esemény- és jelzőkezelési API (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. Időzített metaadat-betöltés

Az Azure Media Services támogatja a valós idejű sávon átnyúló metaadatokat mind az [RTMP] és a Smooth Streaming [MS-SSTR-Int) protokollokhoz. A valós idejű metaadatok egyéni események definiálására használhatók, saját egyedi egyéni sémákkal (JSON, Bináris, XML), valamint iparági definiált formátumokkal, például ID3-mal vagy SCTE-35-tel a szórásos adatfolyamokban a hirdetésjelzéshez. 

Ez a cikk az egyéni időzített metaadat-jelek küldésének részleteit ismerteti az Azure Media Services támogatott betöltési protokolljainak használatával. A cikk azt is elmagyarázza, hogy a HLS, dash és smooth streaming jegyzékei hogyan vannak elrendezve az időzített metaadat-jelekkel, valamint azzal, hogy hogyan kerül a sávon keresztül, amikor a tartalom CMAF (MP4 töredékek) vagy a Transport Stream (TS) szegmensek használatával kerül továbbításra a HLS számára. 

Az időzített metaadatok gyakori használati esetesetei a következők:

 - SCTE-35 hirdetési jelek, amelyek hirdetéstöréseket indítanak el egy élő eseményben vagy lineáris adásban
 - Egyéni ID3-metaadatok, amelyek eseményeket indíthatnak el egy ügyfélalkalmazásban (böngésző, iOS vagy Android)
 - Egyénileg definiált JSON-, bináris- vagy XML-metaadatok az ügyfélalkalmazások eseményeinek aktiválásához
 - Telemetria élő kódolóból, IP-kamerából vagy drónból
 - Események egy IP kamera, mint a mozgás, arcfelismerés, stb
 - Az akciókamera, a drón vagy a mozgó eszköz földrajzi helyzetadatai
 - Dalszöveg
 - Programhatárok lineáris élő hírcsatornán
 - Élő hírfolyamban megjelenítendő képek vagy bővített metaadatok
 - Sporteredmények vagy játékóra-információk
 - A videó mellett a böngészőben megjelenítendő interaktív hirdetési csomagok
 - Kvízek vagy szavazások
  
Az Azure Media Services Élő események és a Packager képes fogadni ezeket az időzített metaadat-jeleket, és azokat olyan metaadatfolyammá alakítja, amely a szabványokon alapuló protokollok, például a HLS és a DASH protokollok használatával elérheti az ügyfélalkalmazásokat.


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP időzített metaadatok

Az [RTMP] protokoll lehetővé teszi az időzített metaadat-jelek küldését különböző esetekben, beleértve az egyéni metaadatokat és az SCTE-35 hirdetési jeleket. 

A hirdetési jelek (cue üzenetek) [AMF0] dákóüzenetként kerülnek elküldésre az [RTMP] adatfolyamba ágyazva. Előfordulhat, hogy a dákó üzeneteket valamikor a tényleges esemény előtt küldik el, vagy [SCTE35] hirdetéssplice jelet kell betartania. A forgatókönyv támogatásához az esemény tényleges megjelenítési időbélyege a cue üzenetben kerül elküldésre. További információ: [AMF0].

Az Azure Media Services rtmp-betöltéséhez a következő [AMF0] parancsokat támogatja:

- **onUserDataEvent** - egyéni metaadatokhoz vagy [ID3v2] időzített metaadatokhoz használható
- **onAdCue** - elsősorban a reklámelhelyezési lehetőség jelzésére szolgál az élő közvetítésben. A dákó két formája támogatott, egy egyszerű és egy "SCTE-35" mód. 
- **onCuePoint** - bizonyos helyszíni hardverkódolók, például az Elemental Live kódoló által támogatott, az [SCTE35] üzenetek jelzésére. 
  

Az alábbi táblázat az AMF-üzenet hasznos adatának azon formátumát ismerteti, amelyet a Media Services az "egyszerű" és az [SCTE35] üzenetmódokban is befog tölteni.

Az [AMF0] üzenet neve több azonos típusú eseményadatfolyam megkülönböztetésére használható.  Mind az [SCTE-35] üzenetek, mind az "egyszerű" mód esetében az AMF-üzenet nevének az [Adobe-Primetime] specifikációban előírtak szerint "onAdCue"-nak kell lennie.  Az alább nem felsorolt mezőket az Azure Media Services betöltéskor figyelmen kívül hagyja.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP egyéni metaadatokkal az "onUserDataEvent" használatával

Ha egyéni metaadat-hírcsatornákat szeretne biztosítani a felsőáramú kódolóból, AZ IP-kamerából, a droneból vagy az RTMP protokollt használó eszközről, használja az "onUserDataEvent" [AMF0] adatüzenet parancstípusát.

Az **"onUserDataEvent"** adatüzenet-parancsnak egy üzenethasznos adatot kell hordoznia a media services által rögzítendő és a sávon túli fájlformátumba csomagolandó üzenetekkel, valamint a HLS, dash és smooth streaming jegyzékeivel.
Javasoljuk, hogy az időzített metaadat-üzeneteket legfeljebb 0,5 másodpercenként (500 ms) küldjék el, vagy az élő közvetítés stabilitási problémái léphetnek fel. Ha keretszintű metaadatokat kell megadnia, minden üzenet több képkockából származó metaadatokat összesíthet. Ha többsávszélességű adatfolyamot küld, ajánlott a metaadatokat egyetlen bitrátán is megadni a sávszélesség csökkentése és a video-/hangfeldolgozással való interferencia elkerülése érdekében. 

Az **"onUserDataEvent"** hasznos adatának [MPEGDASH] EventStream XML formátumú üzenetnek kell lennie. Ez megkönnyíti az egyénidefiniált sémákban való átadást, amelyek a HLS- vagy DASH-protokollokon keresztül szállított CMAF [MPEGCMAF] tartalom sávon keresztül ily módon hordozható tartalomban hordozhatók. Minden DASH eseményfolyam-üzenet tartalmaz egy schemeIdUri-t, amely URN-üzenetséma azonosítóként működik, és meghatározza az üzenet hasznos adatát. Egyes sémákat, mint például a "https://aomedia.org/emsg/ID3" az [ID3v2] vagy az **urna:scte:scte35:2013:bin** az [SCTE-35] esetében, az együttműködési képesség relevancia ipari konzorciumai szabványosítják. Bármely alkalmazásszolgáltató saját egyéni sémát definiálhat az általa (saját tartomány) lévő URL-cím használatával, és adott módon megadhatja az adott URL-címet, ha úgy dönt. Ha egy játékosnak van kezelője a meghatározott sémához, akkor ez az egyetlen összetevő, amelynek meg kell értenie a hasznos terhet és a protokollt.

Az [MPEG-DASH] EventStream XML tartalom sémája a következőképpen határozható meg (részlet a DASH ISO-IEC-23009-1-3rd Edition kiadásból). Vegye figyelembe, hogy az "EventStream"-enként jelenleg csak egy "EventType" támogatott. Csak az első **Esemény** elem lesz feldolgozva, ha az **EventStream**több eseményt is megad.

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


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Példa ID3-as sémaazonosítóval és base64 kódolású adattartalommal rendelkező XML-eseménystreamre.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Példa eseményfolyamra egyéni sémaazonosítóval és base64 kódolású bináris adatokkal  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Példa eseményfolyamra egyéni sémaazonosítóval és egyéni JSON-azonosítóval  
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

### <a name="built-in-supported-scheme-id-uris"></a>Beépített támogatott rendszerazonosító URI-k
| Rendszerazonosító URI                 | Leírás                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | Ez a témakör azt ismerteti, hogy az [ID3v2] metaadatok hogyan vihetők át időzített metaadatként egy METAF-kompatibilis [MPEGCMAF] töredezett MP4-ben. További információt az [Időzített metaadatok a Common Media Application Format (CMAF) formátumban talál.](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Eseményfeldolgozás és jegyzékfájl jelzése

Érvényes **"onUserDataEvent"** esemény beérkezésekor az Azure Media Services olyan érvényes XML-tartalomra fog keresni, amely megegyezik az EventStreamType (a [MPEGDASH] nyelven definiálva), elemzi az XML-tartalom, és átalakítja azt egy [MPEGCMAF] MP4 fragmentum 'emsg' 1.   A Packager észleli az "emsg" mezőt az élő közvetítésben, és:

- a) "dinamikusan csomagolja" ts szegmensekbe a HLS-ügyfeleknek történő szállításhoz a HLS időzített metaadat-specifikációjának [HLS-TMD] megfelelően;
- b) továbbítja azt CMAF-töredékekben hls-en vagy DASH-en keresztül történő szállításra, vagy 
- (c) alakítsa át ritka vágányjellé a Smooth Streaming [MS-SSTR] keresztültörténő kézbesítéshez.

A SÁVOn át eső CMAF vagy TS PES csomagok on kívül a DASH (MPD) és a Smooth Streaming jegyzékei a sávon kívüli eseményfolyamokra (más néven a Smooth Streaming ritka streamsávjára) hivatkoznak. 

Az egyes események vagy adatrakományok NEM kimenetek közvetlenül a HLS, DASH vagy Smooth jegyzékekben. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>További információs korlátozások és alapértelmezett értékek az onUserDataEvent eseményekhez

- Ha az időskála nincs beállítva az EventStream elemben, a rendszer alapértelmezés szerint az RTMP 1 kHz-es időskálát használja.
- Az onUserDataEvent üzenet kézbesítése legfeljebb 500 ms-onként egyszer érhető el. Ha gyakrabban küld eseményeket, az hatással lehet az élő hírcsatorna sávszélességére és stabilitására

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 RTMP hirdetésjelző "onAdCue" jelzéssel

Az Azure Media Services több [AMF0] üzenettípust is figyelhet és válaszolhat, amelyek segítségével különböző valós idejű szinkronizált metaadatokat jelezhet az élő adatfolyamban.  Az [Adobe-Primetime] specifikáció két dákótípust határoz meg, amelyeket "egyszerű" és "SCTE-35" módnak neveznek. Az "egyszerű" módban a Media Services egyetlen AMF-üzenetben , az "onAdCue"-ban támogatja az "onAdCue" nevű tartalom használatát, amely megfelel az alábbi táblázatnak, amelyet az "Egyszerű mód" jelhez határoznak meg.  

A következő szakasz az RTMP "egyszerű" módhasznos terhelését mutatja be, amely egy alapvető "spliceOut" hirdetési jel jelzésére használható, amelyet a HLS, a DASH és a Microsoft Smooth Streaming ügyféljegyzékbe visznek át. Ez nagyon hasznos olyan esetekben, amikor az ügyfél nem rendelkezik összetett SCTE-35 alapú hirdetésjelző központi telepítési vagy beillesztési rendszerrel, és egy alapvető helyszíni kódolót használ a dákó üzenet api-n keresztül történő elküldéséhez. A helyszíni kódoló általában támogatja a REST-alapú API-t a jel aktiválásához, amely a videostreamet is "összeilleszti" egy IDR-keret behelyezésével a videóba, és új gop-ot indít.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 RTMP hirdetésjelző "onAdCue" - Egyszerű mód

| Mezőnév | Mező típusa | Kötelező? | Leírások                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | Sztring     | Kötelező  | Az esemény üzenet.  Kell "SpliceOut" kijelölni egy egyszerű mód splice.                                                                                                                                                                                                         |
| id         | Sztring     | Kötelező  | A kötést vagy a szegmenst leíró egyedi azonosító. Az üzenet ezen példányát azonosítja                                                                                                                                                                                       |
| duration   | Szám     | Kötelező  | Az időtartam a splice. Az egységek tört másodpercek.                                                                                                                                                                                                                           |
| elapsed    | Szám     | Optional  | Amikor a jel megismétlődik a hangolás támogatása érdekében, ez a mező a splice kezdete óta eltelt megjelenítési idő. Az egységek tört másodpercek. Egyszerű mód használata esetén ez az érték nem haladhatja meg a kötés eredeti időtartamát. |
| time       | Szám     | Kötelező  | A megjelenési idő nek kell lennie. Az egységek tört másodpercek.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Példa MPEG DASH jegyzékfájl kimenetére az Adobe RTMP egyszerű mód használatakor

Lásd a [3.3.2.1 MPEG DASH .mpd EventStream példát az Adobe egyszerű módhasználatával](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Lásd a [3.3.3.1 DASH-jegyzékfájl egypontú és adobe egyszerű módú fájljait](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Példa HLS manifest kimenet re adobe RTMP egyszerű mód használataesetén

Lásd a [3.2.2 HLS manifest példát az Adobe egyszerű móddal és az EXT-X-CUE címkével](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 RTMP hirdetésjelző "onAdCue" - SCTE-35 móddal

Ha olyan fejlettebb szórásos termelési munkafolyamattal dolgozik, amely megköveteli, hogy a teljes SCTE-35 tartalomüzenet átkerüljön a HLS- vagy DASH-jegyzékbe, a legjobb, ha az [Adobe-Primetime] specifikáció "SCTE-35 mode"-ját használja.  Ez a mód támogatja a sávon keresztüli SCTE-35 jelek közvetlenül a helyszíni élő kódolóba, amely ezután az [Adobe-Primetime] specifikációban megadott "SCTE-35 mód" használatával kódolja a jeleket az RTMP-adatfolyamba. 

Általában SCTE-35 üzenetek jelenhetnek meg csak az MPEG-2 átviteli stream (TS) bemenetek egy helyszíni kódoló. Az SCTE-35-öt tartalmazó átviteli adatfolyam-betöltés konfigurálásáról érdeklődjön a kódoló gyártójánál, és hogy miként engedélyezheti az RTMP-nek való áthaladást Adobe SCTE-35 módban.

Ebben a forgatókönyvben a következő hasznos adat kell küldeni a helyszíni kódoló az **"onAdCue"** [AMF0] üzenet típusa használatával kell küldeni.

| Mezőnév | Mező típusa | Kötelező? | Leírások                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Végszó        | Sztring     | Kötelező  | Az esemény üzenet.  Az [SCTE-35] üzenetek esetében ennek a hls, smooth és dash ügyfeleknek küldött üzenetek hez a base64 kódolású [RFC4648] bináris splice_info_section() kell lennie.                                                                                                                                                                                                                               |
| type       | Sztring     | Kötelező  | Az üzenetsémét azonosító URL-cím vagy URL-cím. Az [SCTE-35] üzenetek esetében ennek **"scte35"-nek** **kell** lennie ahhoz, hogy az üzeneteket a HLS, Smooth és Dash ügyfeleknek küldhessék az [Adobe-Primetime] követelményeknek megfelelően. Opcionálisan az "urn:scte:scte35:2013:bin" URL-cím is használható az [SCTE-35] üzenet jelzésére.                                                                                                        |
| id         | Sztring     | Kötelező  | A kötést vagy a szegmenst leíró egyedi azonosító. Az üzenet ezen példányát azonosítja.  Az azonos szemantikával rendelkező üzenetek értéke megegyezik.                                                                                                                                                                                                                                                       |
| duration   | Szám     | Kötelező  | Az esemény vagy a hirdetés-splice-szegmens időtartama, ha ismert. Ha ismeretlen, akkor az érték **0 legyen.**                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Szám     | Optional  | Amikor az [SCTE-35] hirdetésjelzés torkodonsolása közben megismétlődik, akkor ez a mező a splice kezdete óta eltelt megjelenítési idő. Az egységek tört másodpercek. [SCTE-35] módban ez az érték meghaladhatja a kötés vagy szegmens eredeti megadott időtartamát.                                                                                                                   |
| time       | Szám     | Kötelező  | Az esemény vagy hirdetés megjelenési ideje.  A bemutatási **időnek** és időtartamnak igazodnia kell az I. mellékletben meghatározott 1- vagy 2-es típusú streamhozzáférési pontokhoz (SAP). A HLS-kimenő forgalom esetében az időnek és az **időtartamnak** igazodnia kell a szegmenshatárokhoz. Az ugyanazon eseményfolyamon belüli különböző eseményüzenetek megjelenítési ideje és időtartama nem fedheti át egymást. Az egységek tört másodpercek. |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>Példa MPEG DASH .mpd jegyzékfájl SCTE-35 móddal
Lásd [a 3.3.3.2.](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Példa HLS manifest .m3u8 SCTE-35 módú jellel
Lásd [a 3.2.1.1.](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RTMP hirdetés jelzés "onCuePoint" az Elemental Live

Az Elemental Live helyszíni kódoló támogatja az RTMP-jel hirdetésjelölőit. Az Azure Media Services jelenleg csak az RTMP "onCuePoint" hirdetésjelölő-típusát támogatja.  Ez az Elemental Media Live kódoló beállításaiban vagy AZ API-ban található Adobe RTMP csoportbeállításokban engedélyezhető a **"ad_markers**" "onCuePoint" értékre állításával.  A részleteket az Elemental Live dokumentációjában találja. Ha engedélyezi ezt a funkciót az RTMP-csoportban, az SCTE-35 jeleket továbbítja az Azure Media Services által feldolgozandó Adobe RTMP-kimenetek számára.

Az "onCuePoint" üzenettípus az [Adobe-Flash-AS] fájlban van definiálva, és az Elemental Live RTMP kimenetről küldött tartalomstruktúra a következő.


| Tulajdonság   | Leírás                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| név       | Az Elemental Live névnek '**scte35**' -nak kell lennie.                                                                                                                                                                              |
| time       | Az az idő másodpercben, amikor a pont a videofájlban az idővonal során történt                                                                                                                                           |
| type       | A cue pont típusát "**esemény**" típusúra kell állítani.                                                                                                                                                                             |
| paraméterek | Név-érték pár karakterláncok asszociatív tömbje, amely az SCTE-35 üzenetből származó adatokat tartalmazza, beleértve az azonosítót és az időtartamot is. Ezeket az értékeket az Azure Media Services elemzi, és tartalmazza a jegyzékfájl dekorációs címke. |


Ha ezt a hirdetésjelölő módot használja, a HLS jegyzékfájl kimenete hasonló az Adobe "Simple" módjához.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Példa MPEG DASH MPD, egypont, Adobe Simple mode jelek

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
~~~

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Példa HLS lejátszási lista, Adobe Simple mode jelek ext-x-CUE címkével (csonkolt "..." a rövidségért)

A következő példa a Media Services dinamikus csomagzatának kimenetét mutatja be egy RTMP-adatfolyamhoz az Adobe "egyszerű" módjelek használatával, valamint az örökölt [Adobe-Primetime] EXT-X-CUE címkét.  

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...

~~~

### <a name="216-cancellation-and-updates"></a>2.1.6 Lemondás és frissítések

Az üzenetek megszakíthatók vagy frissíthetők több, azonos megjelenítési idővel és azonosítóval ellátott üzenet elküldésével. A bemutató ideje és az azonosító egyedileg azonosítja az eseményt, és az utolsó üzenet, amely egy adott bemutatóidejére érkezett, és amely megfelel a görgetés előtti megkötéseknek, az az üzenet, amelyet a rendszer a rendszer a megszólítás előtt fogad el. A frissített esemény felülírja a korábban fogadott üzeneteket. Az előgörgetési megkötés négy másodperc. A bemutató időpontja előtt legalább négy másodperccel fogadott üzeneteket a rendszer a bemutató időpontja előtt fogja megküldeni.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Töredezett MP4 lenyelés (Smooth Streaming)

Az élő közvetítés betöltésére vonatkozó követelményekről az [MS-SSTR-Betöltés] című dokumentumban tájékozódhat. A következő szakaszok az időzített bemutató metaadatainak betöltésével kapcsolatos részleteket ismertetik.  Az időzített bemutató metaadatait ritka sávként kell betartani, amelyet a Live Server manifest box (lásd MS-SSTR) és a Movie Box ("moov") is definiál.  

Minden ritka töredék egy filmtöredékdobozból ("moof") és a Media Data Boxból ("mdat") áll, ahol a "mdat" mező a bináris üzenet.

A hirdetések képkockapontos beillesztésének elérése érdekében a kódolónak fel kell osztania a töredéket a bemutató időpontjában, amikor a dákót be kell illeszteni.  Létre kell hozni egy új töredéket, amely az I. mellékletben meghatározott újonnan létrehozott IDR-kerettel vagy 1 vagy 2 típusú streamhozzáférési pontokkal (SAP) kezdődik. Ez lehetővé teszi, hogy az Azure Media Packager megfelelően hozzon létre egy HLS-jegyzékfájl és egy DASH többidőszakos jegyzékfájl, ahol az új időszak kezdődik a keret-pontos splice feltételként prezentációs idő.

### <a name="221-live-server-manifest-box"></a>2.2.1 Élő kiszolgálójegyzék-mező

A ritka sávot a Live Server Manifest mezőben **kell** deklarálni ** \<egy\> szövegfolyam-bejegyzéssel,** és a következő attribútumokkal **kell** rendelkeznie:

| **Attribútum neve** | **Mező típusa** | **Szükséges?** | **Leírás**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Szám         | Kötelező      | "0" **értékkel kell** rendelkeznie, amely ismeretlen, változó bitrátával rendelkező sávot jelez.                                                                                                                                                          |
| parentTrackName    | Sztring         | Kötelező      | A szülőpálya neve **kell,** hogy legyen, amelyhez a ritka pályaidőkódok időskála-igazítást tartalmaznak. A szülőzene nem lehet ritka szám.                                                                             |
| manifestOutput     | Logikai        | Kötelező      | **Kell** "igaz", jelezve, hogy a ritka pálya lesz ágyazva a Smooth ügyfél jegyzékfájl.                                                                                                                        |
| Altípus            | Sztring         | Kötelező      | **A** négykarakterű "DATA" kódnak kell lennie.                                                                                                                                                                                  |
| Séma             | Sztring         | Kötelező      | **Az** üzenetsémát azonosító URN-nek vagy URL-nek kell lennie. Az [SCTE-35] üzenetek esetében ennek "urn:scte:scte35:2013:bin" **kell** lennie ahhoz, hogy az üzeneteket az [SCTE-35] követelményeknek megfelelően küldje el a HLS, Smooth és Dash ügyfeleknek. |
| trackName          | Sztring         | Kötelező      | **A** ritka pálya neve kell, hogy legyen. A trackName több eseményfolyam megkülönböztetésére is használható ugyanazzal a sémával. Minden egyedi **eseményfolyamnak** egyedi műsorszám-névvel kell rendelkeznie.                                |
| Időskála          | Szám         | Optional      | **A** szülőszám időskálájának kell lennie.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Filmdoboz

A Movie Box ('moov') a Live Server Manifest Box-ot követi a stream fejlécének részeként egy ritka számhoz.

A "moov" mezőnek **tartalmaznia kell** az [ISO-14496-12] mezőben meghatározott **TrackHeaderBox ('tkhd')** mezőt, a következő megkötésekkel:

| **Mező neve** | **Mező típusa**          | **Szükséges?** | **Leírás**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | 64 bites, aláíratlan egész szám | Kötelező      | **0-nak kell** lennie, mivel a sávdobozban nulla minta van, és a minták teljes időtartama a vágánydobozban 0. |

---

A "moov" mezőnek **tartalmaznia kell** az [ISO-14496-12] -ben meghatározott **HandlerBox ("hdlr")** mezőt, a következő korlátozásokkal:

| **Mező neve** | **Mező típusa**          | **Szükséges?** | **Leírás**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32 bites, aláíratlan egész szám | Kötelező      | **Kell** "meta". |

---

Az "stsd" mezőnek tartalmaznia **kell** egy MetaDataSampleEntry mezőt, amelynek kódolási neve az [ISO-14496-12] mezőben meghatározott.  Az SCTE-35 üzenetek esetében például a **kódnévnek** "scte"-nek kell lennie.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Filmtöredék doboz és médiaadat-doboz

A ritka pályatöredékek egy filmtöredékdobozból ("moof") és egy médiaadat-dobozból ("mdat") állnak.

> [!NOTE]
> A hirdetések képkockapontos beillesztésének elérése érdekében a kódolónak fel kell osztania a töredéket a bemutató időpontjában, amikor a dákót be kell illeszteni.  Létre kell hozni egy új töredéket, amely az [I. mellékletben] meghatározott újonnan létrehozott IDR-kerettel vagy 1- vagy 2-es típusú streamhozzáférési pontokkal (SAP) kezdődik.
> 

A MovieFragmentBox ('moof') mezőnek tartalmaznia **kell** az [MS-SSTR] mezőben meghatározott **TrackFragmentExtendedHeaderBox ('uuid')** mezőt a következő mezőkkel:

| **Mező neve**         | **Mező típusa**          | **Szükséges?** | **Leírás**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64 bites, aláíratlan egész szám | Kötelező      | **Az** esemény érkezési ideje kell, hogy legyen. Ez az érték az üzenetet a szülősávhoz igazítja.           |
| fragment_duration      | 64 bites, aláíratlan egész szám | Kötelező      | **Az** esemény időtartama kell, hogy legyen. Az időtartam nulla lehet, jelezve, hogy az időtartam ismeretlen. |

---


A MediaDataBox ('mdat') mezőnek a következő formátummal **kell** rendelkeznie:

| **Mező neve**          | **Mező típusa**                   | **Szükséges?** | **Leírás**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | 32 bites, aláíratlan egész szám (uimsbf) | Kötelező      | A "mdat" mező tartalmának formátumát határozza meg. Az ismeretlen verziókat a rendszer figyelmen kívül hagyja. Jelenleg az egyetlen támogatott verzió 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32 bites, aláíratlan egész szám (uimsbf) | Kötelező      | Az üzenet ezen példányát azonosítja. Az azonos szemantikával rendelkező üzenetek értéke megegyezik; ez azt, hogy elegendő bármely eseményüzenet-mező feldolgozása ugyanazzal az azonosítóval.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32 bites, aláíratlan egész szám (uimsbf) | Kötelező      | A TrackFragmentExtendedHeaderBox mezőben megadott fragment_absolute_time összege és a presentation_time_delta az esemény megjelenítési ideje **kell,** hogy legyen. A bemutatási **időnek** és időtartamnak igazodnia kell az I. mellékletben meghatározott 1- vagy 2-es típusú streamhozzáférési pontokhoz (SAP). A HLS-kimenő forgalom esetében az időnek és az **időtartamnak** igazodnia kell a szegmenshatárokhoz. Az ugyanazon eseményfolyamon belüli különböző eseményüzenetek megjelenítési ideje és időtartama nem **fedheti** át egymást. |
| message                 | bájttömb                       | Kötelező      | Az esemény üzenet. Az [SCTE-35] üzenetek esetében az üzenet a bináris splice_info_section(). Az [SCTE-35] üzenetek esetében ennek **kell** lennie a splice_info_section() annak érdekében, hogy az [SCTE-35] követelményeknek megfelelően a HLS, Smooth és Dash ügyfeleknek küldjék az üzeneteket. Az [SCTE-35] üzenetek esetében a bináris splice_info_section() a "mdat" mező hasznos terhelése, és **NEM** base64 kódolású.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Lemondás és frissítések

Az üzenetek megszakíthatók vagy frissíthetők több, azonos megjelenítési idővel és azonosítóval ellátott üzenet elküldésével.  A bemutató ideje és azonosítója egyedileg azonosítja az eseményt. Az utolsó üzenet, amely egy adott bemutatóidejére érkezett, és amely megfelel az előgörgetési korlátozásoknak, az az üzenet, amelyre reagál. A frissített üzenet felülírja a korábban fogadott üzeneteket.  Az előgörgetési megkötés négy másodperc. A bemutató időpontja előtt legalább négy másodperccel fogadott üzeneteket a rendszer a bemutató időpontja előtt fogja megküldeni. 


## <a name="3-timed-metadata-delivery"></a>3 időzített metaadat-kézbesítés

Az eseményfolyam-adatok átlátszatlanok a Media Services számára. A Media Services csupán három információt ad át a betöltési végpont és az ügyfél végpontja között. A következő tulajdonságokat az [SCTE-35] és/vagy az ügyfél streamelési protokolljának megfelelően kézbesítik az ügyfélnek:

1.  Séma – az üzenet sémát azonosító URN vagy URL.
2.  Bemutató ideje – az esemény bemutatási ideje a média idővonalán.
3.  Időtartam – az esemény időtartama.
4.  Azonosító – az esemény opcionális egyedi azonosítója.
5.  Üzenet – az esemény adatokat.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft Smooth Streaming Manifest  

A ritka üzenetsávok formázásáról a ritka vágánykezelés [MS-SSTR] című dokumentumban talál részleteket. Az [SCTE35] üzenetek esetén a Smooth Streaming a base64 kódolású splice_info_section() egy ritka töredékét adja ki.
A StreamIndex **altípusának** "DATA" altípussal kell rendelkeznie, és a CustomAttributes **attribútumnak** name="Séma" és Value="urn:scte:scte35:2013:bin" attribútumot kell tartalmaznia.

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Simított ügyféljegyzékpélda a base64 kódolású [SCTE35] splice_info_section()
~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS manifest dekoráció

Az Azure Media Services a következő HLS-jegyzékcímkéket támogatja a hirdetési tartalmakkal kapcsolatos információk jelzésére egy élő vagy igény szerinti esemény során. 

- EXT-X-DATERANGE az Apple HLS meghatározása szerint [RFC8216]
- EXT-X-CUE az [Adobe-Primetime] meghatározása szerint - ez a mód "örököltnek" minősül. Az ügyfeleknek lehetőség szerint el kell fogadniuk az EXT-X-DATERANGE címkét.

Az egyes címkék adatkimenete a használt betöltési jelmódtól függően változik. Az RTMP Adobe Simple móddal történő betöltéspéldául nem tartalmazza a teljes SCTE-35 base64 kódolású hasznos terhet.

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1 Apple HLS EXT-X-DATERANGE -val (ajánlott)

Az Apple HTTP Live Streaming [RFC8216] specifikáció lehetővé teszi az [SCTE-35] üzenetek jelzését. Az üzenetek a szegmens lejátszási listába egy EXT-X-DATERANGE címkében kerülnek be az [RFC8216] szakaszonként, "SCTE-35 hozzárendelése EXT-X-DATERANGE-be" című szakaszban.  Az ügyfélalkalmazási réteg elemezheti az M3U lejátszási listát, és feldolgozhatja az M3U címkéket, vagy fogadhatja az eseményeket az Apple player keretrendszeren keresztül.  

Az Azure Media Services (3-as verziójú API) **ajánlott** megközelítése az [RFC8216] követése, és a jegyzékben az [SCTE35] hirdetési szolgáltatás kihasználtságának EXT-X_DATERANGE címkéjének használata.

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 Példa HLS jegyzékfájl .m3u8 az SCTE-35 EXT-X-DATERANGE jelzésével

A következő példa hls jegyzékfájl kimeneta a Media Services dinamikus csomagzó jat a használata az EXT-X-DATERANGE címke [RFC8216] jelző SCTE-35 események az adatfolyamban. Ezenkívül ez az adatfolyam tartalmazza az [Adobe-Primetime] "örökölt" EXT-X-CUE címkéjét is.

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS Adobe Primetime EXT-X-CUE (örökölt)

Van egy "örökölt" implementáció is az Azure Media Servicesben (2-es és 3-as verziójú API), amely az [Adobe-Primetime] "SCTE-35 mode" -ban meghatározott EXT-X-CUE címkét használja. Ebben a módban az Azure Media Services base64 kódolású [SCTE-35] splice_info_section() lesz beágyazva az EXT-X-CUE címkébe.  

Az "örökölt" EXT-X-CUE címke az alábbiakban meghatározott, és normatív hivatkozással is szerepelhet az [Adobe-Primetime] specifikációban. Ezt csak az örökölt SCTE35 jelzéshez szabad használni, ahol szükséges, ellenkező esetben az ajánlott címkét az [RFC8216] EXT-X-DATERANGE-ként definiálja. 

| **Attribútum neve** | **Típus**                      | **Szükséges?**                             | **Leírás**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Végszó                | idézett karakterlánc                 | Kötelező                                  | Az [RFC4648] című közlemény base64 kódolású karakterláncként van kódolva. Az [SCTE-35] üzenetek esetében ez a base64 kódolású splice_info_section().                                                                                                                                      |
| TÍPUS               | idézett karakterlánc                 | Kötelező                                  | Az üzenetsémét azonosító URL-cím vagy URL-cím. Az [SCTE-35] üzenetek esetében a típus az "scte35" speciális értéket veszi fel.                                                                                                                                                                          |
| ID (Azonosító)                 | idézett karakterlánc                 | Kötelező                                  | Az esemény egyedi azonosítója. Ha az azonosító nincs megadva, amikor az üzenet betöltése, az Azure Media Services létrehoz egy egyedi azonosítót.                                                                                                                                              |
| Időtartam           | decimális lebegőpontos szám | Kötelező                                  | Az esemény időtartama. Ha ismeretlen, akkor az érték **0 legyen.** Az egységek tényszerű másodpercek.                                                                                                                                                                                           |
| Eltelt            | decimális lebegőpontos szám | Nem kötelező, de a csúszóablakhoz szükséges | Ha a jel ismétlődik, hogy támogassa a csúszó bemutató ablak, ez a mező **kell** az összeget a bemutató idő telt el, mivel az esemény kezdődött. Az egységek tört másodpercek. Ez az érték meghaladhatja a kötés vagy a szegmens eredeti megadott időtartamát. |
| TIME               | decimális lebegőpontos szám | Kötelező                                  | Az esemény bemutatóidőpontja. Az egységek tört másodpercek.                                                                                                                                                                                                                        |


A HLS-lejátszó alkalmazásrétege a TÍPUS segítségével azonosítja az üzenet formátumát, dekódolja az üzenetet, alkalmazza a szükséges időkonverziókat, és feldolgozza az eseményt.  Az események időszinkronizálása a szülőzene szegmens lejátszási listájában történik az esemény időbélyegének megfelelően.  Ezeket a legközelebbi szegmens (#EXTINF címke) elé helyezi.

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS .m3u8 jegyzékfájl példa a "Legacy" Adobe Primetime EXT-X-CUE használatával

A következő példa a HLS jegyzékbe vételét mutatja be az Adobe Primetime EXT-X-CUE címkével.  A "CUE" paraméter csak a TYPE és az Duration tulajdonságokat tartalmazza, ami azt jelenti, hogy ez egy RTMP-forrás volt, amely az Adobe "egyszerű" módjelzést használ.  Ha ez egy SCTE-35 módú jel, a címke tartalmazza a base64 kódolt bináris SCTE-35 hasznos terhet a [3.2.1.1 példában](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)láthatómódon.

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 HLS-üzenetkezelés a "Legacy" Adobe Primetime EXT-X-CUE-hoz

Az eseményeket az egyes video- és hangsávok szegmens lejátszási listája jelzi. Az EXT-X-CUE címke pozíciójának mindig közvetlenül az első HLS-szegmens előtt **kell** lennie (a kiillesztés vagy a szegmens indítása előtt), vagy közvetlenül az utolsó HLS-szegmens után (a splice-ben vagy a szegmens végén), amelyre az IDŐ és az IDŐTARTAM attribútumai vonatkoznak, az [Adobe-Primetime] által megkövetelt módon.

Ha egy csúszó bemutatóablak engedélyezve van, az EXT-X-CUE címkét elég gyakran **meg kell** ismételni ahhoz, hogy a splice vagy a szegmens mindig teljes mértékben le legyen írva a szegmens lejátszási listában, és az ELAPSED attribútumot **kell** használni annak jelzésére, hogy a splice vagy a szegmens mennyi ideig volt aktív, ahogy azt az [Adobe-Primetime] előírja.

Ha egy csúszó bemutatóablak engedélyezve van, az EXT-X-CUE címkék törlődnek a szegmens lejátszási listáról, amikor az általuk hivatkozott médiaidő kigördült a csúszó bemutatóablakból.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 DASH jegyzékbe való kitüntetés (MPD)

Az [MPEGDASH] három féleképpen jelezheti az eseményeket:

1.  Az MPD EventStream ben jelzett események
2.  Sávon keresztül jelzett események az Esemény üzenetpanellel ("emsg")
3.  Az 1 és a 2 kombinációja

Az MPD EventStream ben jelzett események hasznosak a VOD-streameléshez, mivel az ügyfelek az MPD letöltésekor azonnal hozzáférhetnek az összes eseményhez. Az SSAI-jelzéshez is hasznos, ahol a lejjebb lévő SSAI-szállítónak elemeznie kell a többidőszaki MPD-jegyzékből származó jeleket, és dinamikusan kell beszúrnia a hirdetéstartalmat.  A sávon ('emsg')megoldás akkor hasznos, ha az ügyfeleknek nem kell újra letölteniük az MPD-t, vagy nincs SSAI-jegyzékfájl-manipuláció az ügyfél és az eredet között. 

Az Azure Media Services alapértelmezett viselkedése a DASH számára az, hogy jelezzen mind az MPD EventStream, mind a sávon keresztül az Event Message Box ('emsg') használatával.

Az [RTMP] vagy az [MS-SSTR-Ingt] felett bevitt cue-üzenetek dash-eseményekbe vannak leképezve a sávon belüli 'emsg' mezők és/vagy az MPD-n belüli EventStreams használatával. 

A DASH sávon keresztüli SCTE-35 jelzése az [SCTE-214-3] és a [DASH-IF-IOP] 13.12.2 szakasz ("SCTE35 Események") című szakaszban meghatározott meghatározást és követelményeket követi. 

Sávon keresztül [SCTE-35] kocsi esetén az Eseményüzenet mező ('emsg') a schemeId = "urn:scte:scte35:2013:bin" azonosítót használja. MpD jegyzékjegyzék-dekoráció esetén az EventStream schemeId az "urn:scte:scte35:2014:xml+bin" függvényt használja.  Ez a formátum az esemény XML-ábrázolása, amely a teljes SCTE-35 üzenet betöltési üzenetének bináris base64 kódolású kimenetét tartalmazza. 

Az [SCTE-35] dákó üzenetek dash-ben történő szállításának normatív hivatkozási definíciói az [SCTE-214-1] 6.7.4 (MPD) és [SCTE-214-3] 7.3.2(SCTE 35 dákóüzenetek szállítása) között érhetők el.

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH (MPD) EventStream jelzés

Az események jegyzékfájl (MPD) díszítése az MPD-ben az EventStream elem használatával jelenik meg, amely a Period elemen belül jelenik meg. A használt schemeId a következő: "urn:scte:scte35:2014:xml+bin".

> [!NOTE]
> Rövidség céljából [SCTE-35] lehetővé teszi a signal.binary elem base64 kódolású szakaszának használatát (a Signal.SpliceInfoSection elem helyett) a teljesen elemizált végszó üzenet szállításának alternatívájaként.
> Az Azure Media Services ezt az "xml+bin" módszert használja az MPD-jegyzékben való jelzéshez.
> Ez a [DASH-IF-IOP] által használt ajánlott módszer is - lásd [a DASH IF IOP iránymutatás "Ad insertion event streams" című szakaszát.](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

Az EventStream elem attribútumai a következőtulajdonságokkal rendelkeznek:

| **Attribútum neve** | **Típus**                | **Szükséges?** | **Leírás**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | sztring                  | Kötelező      | Az üzenet sémát azonosítja. A séma értéke a Séma attribútum értéke a Live Server Manifest mezőben. Az **értéknek** az üzenetsémát azonosító URN-nek vagy URL-nek kell lennie; A támogatott kimeneti mintazonosítónak az "urn:scte35:2014:xml+bin" (SCTE-214-1] mp 6.7.4 (MPD) szerint kell lennie az "urn:scte:scte35:2014:xml+bin" (SCTE-214-1] mp 6.7.4 (MPD) szerint, mivel a szolgáltatás jelenleg csak az "xml+bin" szót támogatja az MPD rövidsége érdekében. |
| érték              | sztring                  | Optional      | A séma tulajdonosai által az üzenet szemantikájának testreszabásához használt további karakterlánc-érték. Annak érdekében, hogy több eseményfolyamot különböztetjen meg ugyanazzal a sémával, az értéket az eseményfolyam nevére **kell** beállítani (trackName for [MS-SSTR-Ingt] vagy AMF üzenet neve az [RTMP] betöltéséhez).                                                                         |
| Időskála          | 32 bites, aláíratlan egész szám | Kötelező      | Az időskála, kullancsper másodpercben.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 Példa eseménystreamek az MPEG DASH-hez

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 Példa MPEG DASH .mpd nyilvánvaló jelzése RTMP streaming adobe egyszerű módban

A következő példa egy eseményrészletet mutat be a Media Services dinamikus csomagcsomagolójától egy RTMP-adatfolyamhoz az Adobe "egyszerű" módjelzéssel.

~~~ xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
~~~

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 Példa MPEG DASH .mpd rtmp adatfolyam jegyzékfájljelzésére Adobe SCTE-35 módban

A következő példa egy eseményrészletet mutat be a Media Services dinamikus csomagcsomagolójától egy RTMP-adatfolyamhoz az Adobe SCTE-35 módú jelzéshasználatával.

~~~ xml
<!-- Example EventStream element using xml+bin style signaling per [SCTE-214-1] -->

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
~~~

> [!IMPORTANT]
> Vegye figyelembe, hogy a presentationTime az [SCTE-35] esemény megjelenítési ideje, amelyet az időszak kezdési időpontjához viszonyítva, nem pedig az üzenet érkezési idejére fordítanak le.
> A[MPEGDASH] a Event@presentationTime következőképpen definiálja a következőt: "Az esemény megjelenítési idejét adja meg az időszak kezdetéhez viszonyítva.
> A megjelenítési idő másodpercekben megadva értéke az attribútum értékének EventStream@timescale és az attribútum értékének felosztása.
> Ha nincs jelen, a megjelenítési idő értéke 0.

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 Példa MPEG DASH jegyzékfájl (MPD) egypontos, EventStream, Adobe egyszerű módú jelek használatával

A következő példa a Media Services dinamikus csomagolójának kimenetét mutatja be egy forrás RTMP-adatfolyamhoz az Adobe "egyszerű" módú hirdetési jelmódszerével. A kimenet egy egyidőszakos jegyzékfájl, amely egy EventStream-et jelenít meg az "urn:com:adobe:dpi:simple:2015" és az "simplesignal" értékű tulajdonság ot használó SchemeId Uri használatával.
Minden egyszerű jel egy eseményelemben @presentationTime @durationtalálható, @id a , és a bejövő egyszerű jelek alapján felszámított tulajdonságok.

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 Példa MPEG DASH-jegyzékfájl (MPD) többpontos EventStream alkalmazással, Adobe SCTE35 módú jelzéssel

A következő példa a Media Services dinamikus csomagolójának kimenetét mutatja be egy forrás RTMP-adatfolyamhoz az Adobe SCTE35 módú jelzés használatával.
Ebben az esetben a kimeneti jegyzékfájl egy többidőszakos DASH .mpd, egy EventStream elemmel, és @schemeIdUri az "urn:scte:scte35:2014:xml+bin" tulajdonság, valamint az @value "scte35" tulajdonság. Az EventStream minden eseményeleme tartalmazza a teljes base64 kódolású bináris SCTE35 jelet 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                    
                    <!--> ... aduio segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH Sávon keresztül imessage box jelzés

A sávon ként idorozott eseményfolyamhoz az MPD-nek inbandEventStream elemmel kell rendelkeznie az Adaptációs készlet szintjén.  Ez az elem rendelkezik egy kötelező schemeIdUri attribútummal és opcionális időskála attribútummal, amely az Eseményüzenet-mezőben ("emsg") is megjelenik.  Az MPD-ben nem definiált sémaazonosítókkal rendelkező eseményüzenetek nem **lehetnek** jelen.

Sávon keresztül [SCTE-35] kocsi esetén a **jeleknek** a schemeId = "urn:scte:scte35:2013:bin" szót kell használniuk.
Az [SCTE-35] sávon kénti üzenetek szállításának normatív definícióit az [SCTE-214-3] 7.3.2 (SCTE 35 dákóüzenetek szállítása) határozza meg.

A következő részletek ismertetik azokat az értékeket, amelyekre az ügyfélnek számítania kell az "emsg"-ben az [SCTE-214-3] szerint:

| **Mező neve**          | **Mező típusa**          | **Szükséges?** | **Leírás**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | sztring                  | Kötelező      | Az üzenet sémát azonosítja. A séma értéke a Séma attribútum értéke a Live Server Manifest mezőben. Az **értéknek** az üzenetsémát azonosító URN-nek kell lennie. Az [SCTE-35] üzenetek esetében **ennek** az [SCTE-214-3] megfelelő "urn:scte:scte35:2013:bin" (urna:scte35:2013:bin) kell, hogy legyen.          |
| Érték                   | sztring                  | Kötelező      | A séma tulajdonosai által az üzenet szemantikájának testreszabásához használt további karakterlánc-érték. Annak érdekében, hogy több eseményfolyamot különböztetjen meg ugyanazzal a sémával, az érték az eseményfolyam nevére lesz állítva (trackName for Smooth betöltés vagy AMF-üzenet neve rtmp betöltéshez). |
| Időskála               | 32 bites, aláíratlan egész szám | Kötelező      | Az "emsg" mezőben lévő idő- és időtartammezők időskálája másodpercenként.                                                                                                                                                                                                            |
| Presentation_time_delta | 32 bites, aláíratlan egész szám | Kötelező      | Az esemény megjelenítési idejének és a szegmens legkorábbi megjelenítési idejének a médiamegjelenítési idő különbözete. A bemutatási **időnek** és időtartamnak igazodnia kell az I. mellékletben meghatározott 1- vagy 2-es típusú streamhozzáférési pontokhoz (SAP).                                  |
| event_duration          | 32 bites, aláíratlan egész szám | Kötelező      | Az esemény időtartama, vagy 0xFFFFFFFF ismeretlen időtartamjelzésére.                                                                                                                                                                                                                              |
| Azonosító                      | 32 bites, aláíratlan egész szám | Kötelező      | Az üzenet ezen példányát azonosítja. Az azonos szemantikával rendelkező üzenetek értéke megegyezik. Ha az azonosító nincs megadva, amikor az üzenet betöltése, az Azure Media Services létrehoz egy egyedi azonosítót.                                                                                        |
| Message_data            | bájttömb              | Kötelező      | Az esemény üzenet. Az [SCTE-35] üzenetek esetében az üzenetadatok a bináris splice_info_section() az [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Példa InBandEvenStream entitásra az Adobe Simple módhoz
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 DASH-üzenetek kezelése

Az eseményeket sávon belül, az "emsg" mezőben jelzik mind a videó, mind a hangsáv számára.  A jelzés minden olyan szegmenskérelemnél megtörténik, amelynek a presentation_time_delta legrövidebb ideje. 

Ha egy csúszó bemutatóablak engedélyezve van, az eseményüzenetek törlődnek az MPD-ből, ha az eseményüzenet ideje és időtartama kisebb, mint a jegyzékben lévő médiaadatok ideje.  Más szóval az eseményüzenetek törlődnek a jegyzékfájlból, amikor az adathordozó-idő, amelyre hivatkoznak, kigördült a csúszó bemutatóablakból.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 Betöltés megvalósítási útmutató kódoló gyártók

Az alábbi irányelvek olyan gyakori problémák, amelyek hatással lehetnek a kódoló szállítói nak a specifikáció megvalósítására.  Az alábbi irányelveket valós partnerek visszajelzései alapján gyűjtöttük össze, hogy mások számára könnyebblegyen ezt a specifikációt alkalmazni. 

Az [SCTE-35] üzenetek bináris formátumban kerülnek betöltésre az **"urn:scte35:2013:bin"** (MS-SSTR-Ingest) és az [RTMP] betöltése esetén az **"scte35"** típus használatával. Az [SCTE-35] időzítés átalakításának megkönnyítése érdekében, amely az MPEG-2 átviteli adatfolyam-bemutatási időbélyegein (PTS) alapul, a PTS (pts_time + a splice_time()) pts_adjustment és a média idővonala közötti leképezést az esemény megjelenítési ideje biztosítja ( a sima betöltés fragment_absolute_time mezője és az RTMP betöltésének időmezője). A leképezésre azért van szükség, mert a 33 bites PTS-érték körülbelül 26,5 óránként gördül át.

A sima streamelési betöltés [MS-SSTR-Int] megköveteli, hogy **MUST** a médiaadat-mező ("mdat") tartalmazza az [SCTE-35] -ben meghatározott **splice_info_section()** . 

RTMP-betöltés esetén az AMF-üzenet cue attribútuma az [SCTE-35] mezőben meghatározott base64-kódolású **splice_info_section()** értékre van állítva.  

Ha az üzenetek formátuma fent leírta, a rendszer a hls, smooth és DASH ügyfeleknek küldi el őket a fent meghatározottak szerint.  

Amikor az Azure Media Services platformon teszteli a megvalósítást, először kezdje el a tesztelést egy "átmenő" LiveEvent-el, mielőtt egy encoding LiveEvent en tesztelésre lépne.

---

## <a name="change-history"></a>Korábbi módosítások

| Dátum     | Változások                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Felülvizsgált RTMP betöltés részére SCTE35 támogat, hozzáadott RTMP " onCuePoint" részére ElemI Él                                  |
| 08/22/19 | Frissítve, hogy hozzáadja az OnUserDataEvent-et az RTMP-hez az egyéni metaadatokhoz                                                          |
| 1/08/20  | Javítottuk az RTMP Simple és RTMP SCTE35 módban történt hibát. Az "onCuePoint"-ról "onAdCue"-ra változott. Frissítve: Egyszerű mód táblázat. |

## <a name="next-steps"></a>További lépések
A Media Services tanulási útvonalainak megtekintése.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
