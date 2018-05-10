---
title: Az Azure Media Services bemeneti metaadatok séma |} Microsoft Docs
description: A témakör áttekintést nyújt az Azure Media Services bemeneti metaadatok séma.
author: Juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d72848e2-4b65-4c84-94bc-e2a90a6e7f47
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: 9edfdee82a289d3b966510281305dc45a451c35f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="input-metadata"></a>Bemeneti metaadatok
A kódolási feladat vagy társítva egy bemeneti eszköz (eszközök) a kívánja kódolási feladatok elvégzésére.  Létrehozása után egy feladatot kimeneti eszközként hozott létre.  A kimeneti adategységen tartalmazza a videó hang, miniatűröket, jegyzék, stb. A kimeneti adategységen is tartalmaz a bemeneti eszköz metaadatait tartalmazó fájl. A metaadatok XML-fájl nevének formátuma a következő: &lt;asset_id&gt;_metadata.xml (például 41114ad3 eb5e - 4 - c 57-8d 92-5354e2b7d4a4_metadata.xml), ahol &lt;asset_id&gt; AssetId értéke a bemeneti eszköz.  

Ha meg szeretne vizsgálni a metaadat-fájlt, akkor létrehozhat egy **SAS** lokátor és letöltés a fájlt a helyi számítógépen. Egy példa található SAS-kereső létrehozásával, és töltse le a fájlt [a Media Services .NET SDK-bővítmények használatával](media-services-dotnet-get-started.md).  

Ez a cikk ismerteti a elemek és az XML-séma típusú amikor a bemeneti metada (&lt;asset_id&gt;_metadata.xml) alapul.  A kimeneti adategységen metaadatait tartalmazó fájl kapcsolatos információkért lásd: [kimeneti metaadatok](media-services-output-metadata-schema.md).  

> [!NOTE]
> Megtalálhatja az [séma kód](media-services-input-metadata-schema.md#code) egy [XML-példa](media-services-input-metadata-schema.md#xml) Ez a cikk végén.  
> 
> 

## <a name="AssetFiles"></a> AssetFiles elem (legfelső szintű elem)
Gyűjteményét tartalmazza [AssetFile elem](media-services-input-metadata-schema.md#AssetFile)s a kódolási feladat.  

Ez a cikk végén XML példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

| Name (Név) | Leírás |
| --- | --- |
| **AssetFile**<br /><br /> minOccurs = "1" maxOccurs = "unbounded" |Egyetlen gyermekelemet. További információkért lásd: [AssetFile elem](media-services-input-metadata-schema.md#AssetFile). |

## <a name="AssetFile"></a> AssetFile elem
 Attribútumok és elemek leíró egy eszköz-fájl tartalmazza.  

 Ez a cikk végén XML példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Name (Név)**<br /><br /> Szükséges |**xs:string** |Eszköz neve. |
| **Méret**<br /><br /> Szükséges |**xs:Long** |Fájl mérete, az eszköz bájt. |
| **Időtartam**<br /><br /> Szükséges |**DURATION típusú** |Tartalom play hátsó időtartama. Példa: Duration = "PT25M37.757S". |
| **NumberOfStreams**<br /><br /> Szükséges |**xs:int** |Az eszköz fájlban adatfolyamok száma. |
| **FormatNames**<br /><br /> Szükséges |**xs: string** |Formátumnevek. |
| **FormatVerboseNames**<br /><br /> Szükséges |**xs: string** |Részletes formátumnevek. |
| **StartTime** |**DURATION típusú** |Tartalom kezdete. Példa: StartTime = "PT2.669S". |
| **OverallBitRate** |**xs: int** |Az eszköz fájl kbit/s átlagos átviteli sebesség. |

> [!NOTE]
> A következő négy gyermekelemek sorrendben kell szerepelnie.  
> 
> 

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Programok**<br /><br /> minOccurs="0" | |Az összes gyűjtemény [programok elem](media-services-input-metadata-schema.md#Programs) amikor az eszköz fájlformátum nem MPEG-TS. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Minden egyes fizikai eszköz fájl tartalmazhat nulla, vagy további videók nyomon követi a megfelelő tárolót formátumra időosztásos. Ez az elem tartalmazza az összes [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) , amely az eszköz fájl részét képezik. |
| **AudioTracks**<br /><br /> minOccurs="0" | |Minden egyes fizikai eszköz fájl tartalmazhat nulla vagy több zeneszámok egy megfelelő tárolót formátumra időosztásos. Ez az elem tartalmazza az összes [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) , amely az eszköz fájl részét képezik. |
| **Metaadatok**<br /><br /> minOccurs = "0" maxOccurs = "unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Eszköz fájl metaadat-ként key\value karakterláncok. Példa:<br /><br /> **&lt;A metaadat kulcsként = "nyelv" value = "hun" /&gt;** |

## <a name="TrackType"></a> TrackType
Ez a cikk végén XML példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Azonosító**<br /><br /> Szükséges |**xs:int** |A hang- vagy követése nulla alapú indexét.<br /><br /> Ez nem feltétlenül, mint TrackID használt MP4-fájlokat. |
| **Kodek** |**xs:string** |Videó követése kodek karakterlánc. |
| **CodecLongName** |**xs: string** |Hang- vagy követése kodek hosszú neve. |
| **TimeBase**<br /><br /> Szükséges |**xs:string** |Idejének alapja. Példa: TimeBase = "1/48000" |
| **NumberOfFrames** |**xs:int** |Keretek (videó nyomon követi a jelen) száma. |
| **StartTime** |**xs: duration** |Kezdési idő nyomon követése. Példa: StartTime = "PT2.669S" |
| **Időtartam** |**DURATION típusú** |Időtartam nyomon. Példa: Duration = "PTSampleFormat M37.757S". |

> [!NOTE]
> A következő két gyermekelemek sorrendben kell szerepelnie.  
> 
> 

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Törlése**<br /><br /> minOccurs = "0" maxOccurs = "1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Megjelenítési adatok (például, hogy egy adott hang nyomon követése a gyengén látó felhasználóknak) tartalmazza. |
| **Metaadatok**<br /><br /> minOccurs = "0" maxOccurs = "unbounded" |[MetadataType](media-services-input-metadata-schema.md#MetadataType) |Általános kulcs/érték karakterláncok, amelyek segítségével számos különféle információk tárolására. Például a kulcs = "nyelv", és az érték = "hun". |

## <a name="AudioTrackType"></a> AudioTrackType (TrackType örököl)
 **AudioTrackType** származó globális összetett típus [TrackType](media-services-input-metadata-schema.md#TrackType).  

 A típus jelképezi a eszköz fájlban meghatározott hang nyomon.  

 Ez a cikk végén XML példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **SampleFormat** |**xs:string** |A minta-formátum. |
| **ChannelLayout** |**xs: string** |Csatorna elrendezés. |
| **csatornák**<br /><br /> Szükséges |**xs:int** |(0 vagy több) hang csatornák száma. |
| **Érvénytelen a SamplingRate**<br /><br /> Szükséges |**xs:int** |Hang mintavételi ráta minták másodpercenkénti vagy Hz. |
| **Átviteli sebesség** |**xs:int** |Bit / másodperc, az eszköz fájlból kiszámított átlagos átviteli sebességet. Elemi adatfolyam tartalomban csak akkor számít, és a csomagolási terhelést nem szerepel a számláló értéke. |
| **A BitsPerSample** |**xs:int** |Bit / mintát eredményez, amely a wFormatTag formátumban írja be. |

## <a name="VideoTrackType"></a> VideoTrackType (TrackType örököl)
**VideoTrackType** származó globális összetett típus [TrackType](media-services-input-metadata-schema.md#TrackType).  

A típus jelképezi a eszköz fájlban meghatározott videó nyomon.  

Ez a cikk végén XML példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **FourCC**<br /><br /> Szükséges |**xs:string** |Videó kodek FourCC kódot. |
| **Profil** |**xs: string** |Videó követése profil. |
| **Szint** |**xs: string** |Videó követése szintje. |
| **PixelFormat** |**xs: string** |Videó követése képpontformátum. |
| **Szélessége**<br /><br /> Szükséges |**xs:int** |Kódolt videó szélessége képpontban megadva. |
| **Magassága**<br /><br /> Szükséges |**xs:int** |Videó magassága képpontban kódolva. |
| **DisplayAspectRatioNumerator**<br /><br /> Szükséges |**xs: double** |Képmegjelenítő oldalarányának számlálójának. |
| **DisplayAspectRatioDenominator**<br /><br /> Szükséges |**xs:Double** |Képmegjelenítő oldalarányának nevező. |
| **DisplayAspectRatioDenominator**<br /><br /> Szükséges |**xs: double** |Videó minta oldalarányának számlálójának. |
| **SampleAspectRatioNumerator** |**xs: double** |Videó minta oldalarányának számlálójának. |
| **SampleAspectRatioNumerator** |**xs:Double** |Videó minta oldalarányának nevező. |
| **Képkockasebességhez**<br /><br /> Szükséges |**xs:decimal** |Mért videó képkockasebessége .3f formátumban. |
| **Átviteli sebesség** |**xs:int** |Kilobit / másodperc, az eszköz fájlból kiszámított videó átlagos átviteli sebességet. Elemi adatfolyam tartalomban csak akkor számít, és nincs megadva a csomag terhelést. |
| **MaxGOPBitrate** |**xs: int** |Maximális GOP a videó nyomon követése, a kilobit / másodperc átlagos sávszélességű. |
| **HasBFrames** |**xs:int** |B keretek videó követése száma. |

## <a name="MetadataType"></a> MetadataType
**MetadataType** leíró metaadatok eszköz fájlok kulcs/érték karakterláncként globális összetett típus. Például a kulcs = "nyelv", és az érték = "hun".  

Ez a cikk végén XML példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **key**<br /><br /> Szükséges |**xs:string** |A kulcsot a kulcs/érték párok. |
| **value**<br /><br /> Szükséges |**xs:string** |A kulcs/érték pár értéke. |

## <a name="ProgramType"></a> ProgramType
**ProgramType** globális összetett típus, amely leírja a program.  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **ProgramId**<br /><br /> Szükséges |**xs:int** |Program azonosítója |
| **NumberOfPrograms**<br /><br /> Szükséges |**xs:int** |Programok száma. |
| **PmtPid**<br /><br /> Szükséges |**xs:int** |Program térkép táblák (PMTs) a programok információkat tartalmaznak.  További információkért lásd: [részlet](http://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Szükséges |**xs: int** |A dekódoló használják. További információkért lásd: [PCR](http://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: hosszú** |Kezdő bemutató időbélyegző. |
| **EndPTS** |**xs: hosszú** |Befejezési bemutató időbélyegző. |

## <a name="StreamDispositionType"></a> StreamDispositionType
**StreamDispositionType** , mely leírja az adatfolyam globális összetett típus.  

Ez a cikk végén XML példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Alapértelmezett**<br /><br /> Szükséges |**xs: int** |Ez az attribútum értéke 1 és jelzi, ez az alapértelmezett megjelenítése. |
| **Dub**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 és jelzi, ennek a szinkronizált bemutató. |
| **Eredeti**<br /><br /> Szükséges |**xs: int** |Ez az attribútum értéke 1 és jelzi, ez az eredeti bemutató. |
| **Megjegyzés**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 annak jelzésére, hogy a track magyarázatokkal tartalmazza. |
| **Szöveg**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 a track tartalmaz szöveg jelzi. |
| **Karaoke**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 annak jelzésére, hogy ez a karaoke nyomon követése (háttér zene, nincs énekhez) jelöli. |
| **A kényszerített**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 és jelzi, ez a kényszerített bemutató. |
| **HearingImpaired**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 és a sérült nyújtanak segítséget az a szám jelzi. |
| **VisualImpaired**<br /><br /> Szükséges |**xs:int** |Ez az attribútum értéke 1 és gyengén látó az a szám jelzi. |
| **CleanEffects**<br /><br /> Szükséges |**xs: int** |Ez az attribútum értéke 1 annak jelzésére, hogy a track tiszta hatásai vannak. |
| **AttachedPic**<br /><br /> Szükséges |**xs: int** |Ez az attribútum állítsa 1 jelzi a track képek. |

## <a name="Programs"></a> Programok elem
Több okozó burkolóelemet **Program** elemek.  

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs = "0" maxOccurs = "unbounded" |[ProgramType](media-services-input-metadata-schema.md#ProgramType) |Eszköz MPEG-TS formátumú fájlok esetében az adategységfájlon programokkal kapcsolatos információkat tartalmazza. |

## <a name="VideoTracks"></a> VideoTracks elem
 Több okozó burkolóelemet **VideoTrack** elemek.  

 Ez a cikk végén XML példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs = "0" maxOccurs = "unbounded" |[VideoTrackType (TrackType örököl)](media-services-input-metadata-schema.md#VideoTrackType) |Videó nyomon követi az eszköz fájl adatait tartalmazza. |

## <a name="AudioTracks"></a> AudioTracks elem
 Több okozó burkolóelemet **AudioTrack** elemek.  

 Ez a cikk végén XML példát: [XML-példa](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs = "0" maxOccurs = "unbounded" |[AudioTrackType (TrackType örököl)](media-services-input-metadata-schema.md#AudioTrackType) |Az eszköz fájlban zeneszámok adatait tartalmazza. |

## <a name="code"></a> Séma kódot
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
A bemeneti metaadatait tartalmazó fájl egy példát a következő:  

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

