---
title: Az Azure Media Services bementi metaadat-séma |} A Microsoft Docs
description: A témakör áttekintést nyújt az Azure Media Services bementi metaadat-séma.
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
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: 42e3464a190f296675b544e0087b664ff256f2fa
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003244"
---
# <a name="input-metadata-legacy"></a>Bemeneti metaadatok (örökölt)

A kódolási feladat vagy társítva egy bemeneti objektuma (eszközök) a amely kívánt kódolási feladatok elvégzésére.  A feladat befejeztével a kimeneti adategység jön létre.  A kimeneti objektum tartalmazza a videó, hang, a miniatűrök, jegyzékfájl, stb. A kimeneti objektum bemeneti objektuma metaadatait tartalmazó fájl is tartalmazza. A metaadatok XML-fájl neve formátuma a következő: &lt;asset_id&gt;_metadata.xml (például 41114ad3 eb5e – 4c - 57-8d 92-5354e2b7d4a4_metadata.xml), ahol &lt;asset_id&gt; AssetId értéke a bemeneti objektuma.  

A Media Services jegyértékesítésről nem vizsgálja a bemeneti eszközök metaadatainak létrehozásához. Bemeneti metaadatok csak, egy összetevő, amikor egy eszköz feldolgozása a feladat bemeneti jön létre. Ezért az összetevő íródik a kimeneti adategység. A különböző eszközök bemeneti eszközök létrehozása a metaadatokat, és a kimeneti eszközök segítségével. A bementi metaadat, ezért kimeneti metaadatok némileg különböző sémákkal rendelkeznek.

Ha meg szeretné vizsgálni a metaadatait tartalmazó fájl, létrehozhat egy **SAS** kereső és letöltése a fájlt a helyi számítógépen. Találhat egy példát, hogyan hozhat létre egy SAS-kereső, és töltse le a fájlt a [a Media Services .NET SDK-bővítmények használatával](media-services-dotnet-get-started.md).  

Ez a cikk ismerteti az elemek és az XML-séma típusát, amelyre a bemeneti metada (&lt;asset_id&gt;_metadata.xml) alapul.  A kimeneti adategység metaadatait tartalmazó fájl kapcsolatos információkért lásd: [kimeneti metaadatok](media-services-output-metadata-schema.md).  

Annak a [séma kódjának](media-services-input-metadata-schema.md#code) egy [XML-példa](media-services-input-metadata-schema.md#xml) Ez a cikk végén található.  
 

## <a name="AssetFiles"></a> AssetFiles elem (gyökérelem)
Gyűjteményét tartalmazza [AssetFile elem](media-services-input-metadata-schema.md#AssetFile)s a kódolási feladat.  

Ez a cikk végén található XML példaként lásd: [XML-példa](media-services-input-metadata-schema.md#xml).  

| Name (Név) | Leírás |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs = maxOccurs "1" = "korlátlan" |Egyetlen gyermekelemet. További információkért lásd: [AssetFile elem](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a> AssetFile elem
 Attribútumok és elemek, amelyek ismertetik egy adategység-fájlt tartalmaz.  

 Ez a cikk végén található XML példaként lásd: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Name (Név)**<br /><br /> Szükséges |**xs:string** |Az eszközintelligencia-fájl neve. |
| **Méret**<br /><br /> Szükséges |**xs:Long** |Mérete bájtban adategységfájlon. |
| **Időtartam**<br /><br /> Szükséges |**DURATION típusú** |Tartalom play vissza időtartama. Példa: Duration="PT25M37.757S". |
| **NumberOfStreams**<br /><br /> Szükséges |**xs:int** |Az eszközintelligencia fájlban adatfolyam száma. |
| **FormatNames**<br /><br /> Szükséges |**xs: karakterlánc** |Nevek formázása. |
| **FormatVerboseNames**<br /><br /> Szükséges |**xs: karakterlánc** |Részletes formátumnevek. |
| **StartTime** |**DURATION típusú** |Tartalom kezdete. Példa: StartTime="PT2.669S". |
| **OverallBitRate** |**xs: int** |Az eszköz fájl kbit/s átlagos átviteli sebesség. |

> [!NOTE]
> A következő négy gyermekelemek szerepelnie kell egy sorozatban.  
> 
> 

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Programok**<br /><br /> minOccurs="0" | |Az összes gyűjtemény [programok elem](media-services-input-metadata-schema.md#Programs) mikor az adategységfájlon MPEG-TS formátumban van. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Minden egyes fizikai eszköz fájl tartalmazhat nulla vagy egy megfelelő tárolót formátumba közbeékeléses további videók nyomon követi. Ez az elem tartalmazza az összes gyűjtemény [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) adategységfájlon részét. |
| **AudioTracks**<br /><br /> minOccurs="0" | |Minden egyes fizikai eszköz fájl tartalmazza a megfelelő tárolót formátumba közbeékeléses nulla vagy több hangsáv. Ez az elem tartalmazza az összes gyűjtemény [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) adategységfájlon részét. |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Az eszközintelligencia fájl metaadatai key\value karakterláncok-kiszolgálókként. Példa:<br /><br /> **&lt;Metadata key="language" value="eng" /&gt;** |

## <a name="TrackType"></a> TrackType
Ez a cikk végén található XML példaként lásd: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Azonosító**<br /><br /> Szükséges |**xs:int** |A hang- vagy követése nulla alapú indexét.<br /><br /> Ez nem feltétlenül, hogy a TrackID, mint az MP4-fájlokat használja. |
| **Kodek** |**xs:string** |Videó követése kodek karakterlánc. |
| **CodecLongName** |**xs: karakterlánc** |Hang- vagy követése kodek hosszú neve. |
| **TimeBase**<br /><br /> Szükséges |**xs:string** |Idő alapja. Példa: TimeBase="1/48000" |
| **NumberOfFrames** |**xs:int** |A keretek (videó nyomon követi a jelen) száma. |
| **StartTime** |**xs: időtartam** |Kezdési idő nyomon követése. Példa: StartTime="PT2.669S" |
| **Időtartam** |**DURATION típusú** |Nyomon követheti a időtartama. Példa: Duration="PTSampleFormat M37.757S". |

> [!NOTE]
> A következő két gyermekelemek szerepelnie kell egy sorozatban.  
> 
> 

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Törlése**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |(Például, hogy egy adott hangsávra a gyengén látó felhasználóknak) bemutató információkat tartalmaz. |
| **Metadata**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Általános kulcs/érték karakterláncok, amelyek különböző információk tárolására is használható. Ha például a kulcs = "nyelv", és érték = "hun". |

## <a name="AudioTrackType"></a> AudioTrackType (TrackType örököl)
 **AudioTrackType** globális összetett típus, amely örökli [TrackType](media-services-input-metadata-schema.md#TrackType).  

 A típus jelöli az eszközintelligencia-fájl egy adott hangsávra.  

 Ez a cikk végén található XML példaként lásd: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **SampleFormat** |**xs:string** |Minta formátumával. |
| **ChannelLayout** |**xs: karakterlánc** |Csatorna elrendezés. |
| **csatornák**<br /><br /> Szükséges |**xs:int** |(0 vagy több) hang csatornák száma. |
| **Érvénytelen a SamplingRate**<br /><br /> Szükséges |**xs:int** |Hang mintavételi ráta samples/mp vagy Hz. |
| **Átviteli sebesség** |**xs:int** |Átlagos átviteli sebességű bit / másodperc, számított az eszközintelligencia-fájlból. Ez a szám nem szerepel a csomagolási terhelését, és csak a elemi stream hasznos tranzakciónak. |
| **Bitspersample obsahuje neplatnou Hodnotu** |**xs:int** |Bit / minta wFormatTag formátumban írja be. |

## <a name="VideoTrackType"></a> VideoTrackType (TrackType örököl)
**VideoTrackType** globális összetett típus, amely örökli [TrackType](media-services-input-metadata-schema.md#TrackType).  

A típus jelöli egy adott videó nyomon követése az adategységfájlon.  

Ez a cikk végén található XML példaként lásd: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **FourCC**<br /><br /> Szükséges |**xs:string** |Videó kodek FourCC kódot. |
| **Profil** |**xs: karakterlánc** |Videó követése profil. |
| **Szint** |**xs: karakterlánc** |Videó követése szintje. |
| **PixelFormat** |**xs: karakterlánc** |Videó követése képpont formátuma. |
| **Szélesség**<br /><br /> Szükséges |**xs:int** |A kódolt videó szélességét (képpontban). |
| **Magasság**<br /><br /> Szükséges |**xs:int** |A kódolt videó magasságát (képpontban). |
| **DisplayAspectRatioNumerator**<br /><br /> Szükséges |**xs: dupla** |Oldalarány számlálójának képmegjelenítő. |
| **DisplayAspectRatioDenominator**<br /><br /> Szükséges |**xs:Double** |Oldalarány nevező képmegjelenítő. |
| **DisplayAspectRatioDenominator**<br /><br /> Szükséges |**xs: dupla** |Videó minta oldalarány osztandót. |
| **SampleAspectRatioNumerator** |**xs: dupla** |Videó minta oldalarány osztandót. |
| **SampleAspectRatioNumerator** |**xs:Double** |Videó minta oldalarány nevező. |
| **Képkockasebesség**<br /><br /> Szükséges |**xs:decimal** |Mért videó képkockasebessége .3f formátumban. |
| **Átviteli sebesség** |**xs:int** |Videó átlagos átviteli sebessége a kilobit / másodperc, számított az eszközintelligencia-fájlból. A csomagolási terhelését a lehetőség nem része, és csak a elemi stream hasznos tranzakciónak. |
| **MaxGOPBitrate** |**xs: int** |Maximális Képcsoporttal átlagos bitsebességű a a videó nyomon követése a kilobit / másodperc. |
| **HasBFrames** |**xs:int** |B keretek videó követése száma. |

## <a name="MetadataType"></a> MetadataType
**MetadataType** globális összetett típus, amely leírja egy adategység-fájl metaadatait a kulcs/érték karakterláncként. Ha például a kulcs = "nyelv", és érték = "hun".  

Ez a cikk végén található XML példaként lásd: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **key**<br /><br /> Szükséges |**xs:string** |A kulcs a kulcs/érték pár. |
| **value**<br /><br /> Szükséges |**xs:string** |A kulcs/érték párok értéke. |

## <a name="ProgramType"></a> ProgramType
**ProgramType** globális összetett típus, amely leírja a program.  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **ProgramId**<br /><br /> Szükséges |**xs:int** |Program azonosítója |
| **NumberOfPrograms**<br /><br /> Szükséges |**xs:int** |Programok száma. |
| **PmtPid**<br /><br /> Szükséges |**xs:int** |Program térkép táblák (PMTs) programok vonatkozó adatokat tartalmaznak.  További információkért lásd: [részlet](http://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Szükséges |**xs: int** |Dekódoló által használt. További információkért lásd: [PCR](http://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: hosszú** |Bemutatási időbélyege indítása. |
| **EndPTS** |**xs: hosszú** |A befejező bemutató időbélyeg. |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** globális összetett típus, amely leírja a streamet.  

Ez a cikk végén található XML példaként lásd: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Alapértelmezett**<br /><br /> Szükséges |**xs: int** |Ez az attribútum értéke 1 azt, hogy ez az alapértelmezett megjelenítése. |
| **Dub**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 azt, hogy ez a szinkronizált bemutatót. |
| **Original**<br /><br /> Szükséges |**xs: int** |Ez az attribútum értéke 1 azt, hogy ez az eredeti bemutató. |
| **Megjegyzés**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 a track tartalmaz mellékmondatokkal jelzi. |
| **Szöveg**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 a track tartalmaz szöveg jelzi. |
| **Karaokézom**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1, ez jelenti az karaokézom nyomon követése (háttérben zene, nincs énekhez) jelzi. |
| **Kényszerített**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 azt, hogy ez a kényszerített bemutatót. |
| **HearingImpaired**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1, a hallás, ronthatja az a szám azt, hogy. |
| **VisualImpaired**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 a gyengén látók számára ez követése jelzi. |
| **CleanEffects**<br /><br /> Szükséges |**xs: int** |Ez az attribútum értéke 1 a track rendelkezik tiszta hatások jelzi. |
| **AttachedPic**<br /><br /> Szükséges |**xs: int** |Ez az attribútum értéke 1 a track rendelkezik képek jelzi. |

## <a name="Programs"></a> Programok elem
Több tároló burkoló elem **Program** elemeket.  

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Az adategység-fájlok, MPEG-TS formátumú adategységfájlon programokkal kapcsolatos információkat tartalmazza. |

## <a name="VideoTracks"></a> VideoTracks elem
 Több tároló burkoló elem **VideoTrack** elemeket.  

 Ez a cikk végén található XML példaként lásd: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (TrackType örököl)](media-services-input-metadata-schema.md#VideoTrackType) |Videó nyomon követi az adategységfájlon kapcsolatos információt tartalmazza. |

## <a name="AudioTracks"></a> AudioTracks elem
 Több tároló burkoló elem **AudioTrack** elemeket.  

 Ez a cikk végén található XML példaként lásd: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (TrackType örököl)](media-services-input-metadata-schema.md#AudioTrackType) |Az eszközintelligencia fájlban hangsáv kapcsolatos információt tartalmazza. |

## <a name="code"></a> Séma kódjának
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               elementFormDefault="qualified">  

      <xs:complexType name="MetadataType">  
        <xs:attribute name="key"   type="xs:string" use="required"/>  
        <xs:attribute name="value" type="xs:string" use="required"/>  
      </xs:complexType>  

      <xs:complexType name="ProgramType">  
        <xs:attribute name="ProgramId" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Program Id</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Number of programs</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PmtPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pmt pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PcrPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pcr pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="StartPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>start pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="EndPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>end pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="StreamDispositionType">  
        <xs:attribute name="Default"          type="xs:int" use="required" />  
        <xs:attribute name="Dub"              type="xs:int" use="required" />  
        <xs:attribute name="Original"         type="xs:int" use="required" />  
        <xs:attribute name="Comment"          type="xs:int" use="required" />  
        <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
        <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
        <xs:attribute name="Forced"           type="xs:int" use="required" />  
        <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
        <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
        <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
        <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
      </xs:complexType>  

      <xs:complexType name="TrackType" abstract="true">  
        <xs:sequence>  
          <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
          <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
        </xs:sequence>  
        <xs:attribute name="Id" use="required">  
          <xs:annotation>  
            <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Codec" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec string</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="CodecLongName" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec long name</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="TimeBase"  type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfFrames">  
          <xs:annotation>  
            <xs:documentation>number of frames</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="StartTime" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Duration" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="VideoTrackType">  
        <xs:annotation>  
          <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="FourCC" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>video codec FourCC code</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Profile" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>profile</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Level" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>level</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="PixelFormat" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>Video track's pixel format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Width" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video width in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Height" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video height in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioNumerator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioDenominator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="FrameRate" use="required">  
              <xs:annotation>  
                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:decimal">  
                  <xs:minInclusive value="0"/>  
                  <xs:fractionDigits value="3"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="MaxGOPBitrate">  
              <xs:annotation>  
                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="HasBFrames" type="xs:int">  
              <xs:annotation>  
                <xs:documentation>video track number of B frames</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:complexType name="AudioTrackType">  
        <xs:annotation>  
          <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="SampleFormat"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>sample format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="ChannelLayout"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>channel layout</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Channels" use="required">  
              <xs:annotation>  
                <xs:documentation>number of audio channels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SamplingRate" use="required">  
              <xs:annotation>  
                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="BitsPerSample">  
              <xs:annotation>  
                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Programs" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" type="xs:duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
                  <xs:annotation>  
                    <xs:documentation>number of streams in asset file</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatNames" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format verbose names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="StartTime" type="xs:duration">  
                  <xs:annotation>  
                    <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="OverallBitRate">  
                  <xs:annotation>  
                    <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:int">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  


## <a name="xml"></a> XML-példa
Az alábbiakban látható egy példa a bemeneti metaadatait tartalmazó fájl.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
      <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
        <VideoTracks>  
          <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
          </VideoTrack>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
          </AudioTrack>  
        </AudioTracks>  
        <Metadata key="major_brand" value="mp42" />  
        <Metadata key="minor_version" value="0" />  
        <Metadata key="compatible_brands" value="mp42mp41" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
        <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

