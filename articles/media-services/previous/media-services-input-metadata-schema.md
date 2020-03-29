---
title: Az Azure Media Services bemeneti metaadatsémája | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure Media Services bemeneti metaadat-sémáról.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a81d6edfd887dc935a53742b7bc1492651c9bda5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887118"
---
# <a name="input-metadata"></a>Bemeneti metaadatok 

A kódolási feladat olyan bemeneti eszközhöz (vagy eszközökhöz) van társítva, amelyen bizonyos kódolási feladatokat el szeretne végezni.  A feladat befejezésekor egy kimeneti eszköz keletkezik.  A kimeneti eszköz tartalmaz videót, hangot, miniatűröket, jegyzéket stb. A kimeneti eszköz is tartalmaz egy fájlt a bemeneti eszköz metaadatait. A metaadat-XML-fájl neve a &lt;következő&gt;formátumú: asset_id _metadata.xml (például 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), ahol &lt;asset_id&gt; a bemeneti eszköz AssetId értéke.  

A Media Services nem szokta elő, hogy metaadatokat generáljon a bemeneti eszközökből. A bemeneti metaadatok csak összetevőként jönnek létre, ha egy bemeneti eszközt egy feladatban dolgoznak fel. Ezért ez a összetevő a kimeneti eszközbe van írva. Különböző eszközök segítségével jönnek létre metaadatok a bemeneti eszközök és a kimeneti eszközök. Ezért a bemeneti metaadatok egy kicsit eltérnek a kimeneti metaadatok.

Ha meg szeretné vizsgálni a metaadatfájlt, **SAS** létrehozhat egy SAS-lokátort, és letöltheti a fájlt a helyi számítógépre. A [Media Services .NET SDK Extensions segítségével](media-services-dotnet-get-started.md)példát találhat a SAS-lokátor létrehozására és a fájl letöltésére.  

Ez a cikk annak az XML-sémának az elemeit és&lt;típusait ismerteti, amelyeken a bemeneti metada ( asset_id&gt;_metadata.xml) alapul.  A kimeneti eszköz metaadatait tartalmazó fájlról a [Kimeneti metaadatok](media-services-output-metadata-schema.md)című témakörben talál további információt.  

A [sémakód](media-services-input-metadata-schema.md#code) [xml-példa](media-services-input-metadata-schema.md#xml) a cikk végén található.  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a>AssetFiles elem (gyökérelem)
A kódolási feladat hoz való [AssetFile elem](media-services-input-metadata-schema.md#AssetFile)s gyűjteményét tartalmazza.  

A cikk végén található XML-példa: [XML példa](media-services-input-metadata-schema.md#xml).  

| Név | Leírás |
| --- | --- |
| **Eszközfájl**<br /><br /> minOccurs="1" maxOccurs="nem kötött" |Egyetlen gyermekelem. További információ: [AssetFile element](media-services-input-metadata-schema.md#AssetFile). |

## <a name="assetfile-element"></a><a name="AssetFile"></a>AssetFile elem
 Olyan attribútumokat és elemeket tartalmaz, amelyek egy eszközfájlt írnak le.  

 A cikk végén található XML-példa: [XML példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Név**<br /><br /> Kötelező |**xs:karakterlánc** |Eszközfájl neve. |
| **Méret**<br /><br /> Kötelező |**xs:hosszú** |Az eszközfájl mérete bájtban. |
| **Időtartam**<br /><br /> Kötelező |**xs:időtartam** |A tartalom lejátszási időtartama. Példa: Duration="PT25M37.757s". |
| **NumberOfStreams**<br /><br /> Kötelező |**xs:int** |Az eszközfájlban lévő adatfolyamok száma. |
| **Formátumnevek**<br /><br /> Kötelező |**xs: karakterlánc** |Nevek formázása. |
| **FormatVerboseNames (FormátumVerboseNames)**<br /><br /> Kötelező |**xs: karakterlánc** |Részletes nevek formázása. |
| **StartTime** |**xs:időtartam** |Tartalom kezdési ideje. Példa: StartTime="PT2.669s". |
| **OverallBitRate** |**xs: int** |Az eszközfájl átlagos bitsebessége kbit/s-ban. |

> [!NOTE]
> A következő négy gyermekelemnek egy sorrendben kell megjelennie.  
> 
> 

### <a name="child-elements"></a>Gyermek elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **Programok**<br /><br /> minOccurs="0" | |Az összes [Program elem](media-services-input-metadata-schema.md#Programs) gyűjtése, ha az eszközfájl MPEG-TS formátumú. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Minden fizikai eszközfájl tartalmazhat nulla vagy több videót, amelyek megfelelő tárolóformátumba vannak átalakítva. Ez az elem az eszközfájl részét tartalmazó [összes VideoS-sáv](media-services-input-metadata-schema.md#VideoTracks) gyűjteményét tartalmazza. |
| **Hangsávok**<br /><br /> minOccurs="0" | |Minden fizikai eszközfájl tartalmazhat nulla vagy több hangsávot a megfelelő tárolóformátumba átlapolt hangsávban. Ez az elem az eszközfájl részét tartalmazó összes [hangsáv](media-services-input-metadata-schema.md#AudioTracks) gyűjteményét tartalmazza. |
| **Metaadatok**<br /><br /> minOccurs="0" maxOccurs="nem kötött" |[Metaadattípus](media-services-input-metadata-schema.md#MetadataType) |Az eszközfájl metaadatai kulcs\értékkarakterláncokként vannak jelölve. Példa:<br /><br /> **&lt;Metaadat-kulcs="nyelv" érték="eng" /&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a>TrackType típus
A cikk végén található XML-példa: [XML példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Id**<br /><br /> Kötelező |**xs:int** |A hang- vagy videosáv nulla alapú indexe.<br /><br /> Ez nem feltétlenül, hogy a TrackID használt MP4 fájlt. |
| **Codec** |**xs:karakterlánc** |Videopálya kodek karakterlánc. |
| **CodecLongName (KokkHosszúnév)** |**xs: karakterlánc** |Hang- vagy videosáv-kód hosszú név. |
| **TimeBase (Munkaidő-bázis)**<br /><br /> Kötelező |**xs:karakterlánc** |Időbázis. Példa: TimeBase="1/48000" |
| **Frame-ek száma** |**xs:int** |Képkockák száma (videoszámok esetén jelen van). |
| **StartTime** |**xs: időtartam** |A kezdési időpont nyomon követése. Példa: StartTime="PT2.669s" |
| **Időtartam** |**xs:időtartam** |A műsoridő. Példa: Duration="PTSampleformat M37.757s". |

> [!NOTE]
> A következő két gyermekelemnek egymás után kell megjelennie.  
> 
> 

### <a name="child-elements"></a>Gyermek elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **Hajlam**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType típus](media-services-input-metadata-schema.md#StreamDispositionType) |Bemutatóinformációkat tartalmaz (például azt, hogy egy adott hangsáv a gyengén látók számára készült-e). |
| **Metaadatok**<br /><br /> minOccurs="0" maxOccurs="nem kötött" |[Metaadattípus](media-services-input-metadata-schema.md#MetadataType) |Általános kulcs/érték karakterláncok, amelyek különböző információk tárolására használhatók. Például key="language", és value="eng". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a>AudioTrackType (a TrackType öröklődik)
 **Az AudioTrackType** egy globális komplextípus, amely a [TrackType](media-services-input-metadata-schema.md#TrackType)típustól örököl.  

 A típus egy adott hangsávot jelöl az eszközfájlban.  

 A cikk végén található XML-példa: [XML példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Mintaformátum** |**xs:karakterlánc** |Minta formátuma. |
| **ChannelLayout (Csatornaelrendezés)** |**xs: karakterlánc** |Csatorna elrendezés. |
| **Csatornák**<br /><br /> Kötelező |**xs:int** |A hangcsatornák száma (0 vagy több). |
| **Mintavételi ráta**<br /><br /> Kötelező |**xs:int** |Hangmintavételi sebesség minták/mp-ben vagy Hz-ben. |
| **Bitráta** |**xs:int** |Átlagos hangátviteli sebesség bit/másodpercben, az eszközfájlból számítva. Csak az elemi stream hasznos teher számít, és a csomagolási többletterhelés nem szerepel ebben a számlálóban. |
| **BitsPerSample** |**xs:int** |A wFormatTag formátum típusának mintánkénti bitjei. |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a>VideoTrackType (a TrackType öröklődik)
**A VideoTrackType** egy globális komplextípus, amely a [TrackType](media-services-input-metadata-schema.md#TrackType)típustól örököl.  

A típus egy adott videosávot jelöl az eszközfájlban.  

A cikk végén található XML-példa: [XML példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Fourcc**<br /><br /> Kötelező |**xs:karakterlánc** |Video codec FourCC kód. |
| **Profil** |**xs: karakterlánc** |A videósáv profilja. |
| **Szint** |**xs: karakterlánc** |A videósáv szintje. |
| **Képpontformátum** |**xs: karakterlánc** |A videósáv képpontformátuma. |
| **Width**<br /><br /> Kötelező |**xs:int** |Kódolt videószélesség képpontban. |
| **Height**<br /><br /> Kötelező |**xs:int** |Kódolt videómagasság képpontban. |
| **DisplayAspectRatioNumerator**<br /><br /> Kötelező |**xs: dupla** |Videomegjelenítési képarány számlálója. |
| **DisplayAspectRatioDenevező**<br /><br /> Kötelező |**xs:dupla** |A videó megjelenítési képarány-nevezője. |
| **DisplayAspectRatioDenevező**<br /><br /> Kötelező |**xs: dupla** |Videó minta képarányának számlálója. |
| **SampleAspectRatioNumerator** |**xs: dupla** |Videó minta képarányának számlálója. |
| **SampleAspectRatioNumerator** |**xs:dupla** |Videó minta képarányának nevezője. |
| **Frameráta**<br /><br /> Kötelező |**xs:decimális** |Mért videó képkockasebesség .3f formátumban. |
| **Bitráta** |**xs:int** |Átlagos videobitráta kilobit/másodpercben, az eszközfájlból számítva. Csak az elemi stream hasznos teher számít, és a csomagolási terhelés nem szerepel. |
| **MaxGOPBitrate** |**xs: int** |Maximális GOP átlagos bitráta ehhez a videó sávhoz, kilobit/másodpercben. |
| **HasBframes (HasBframes)** |**xs:int** |A Videó sáv száma B képkockák. |

## <a name="metadatatype"></a><a name="MetadataType"></a>Metaadattípus
**A MetadataType** egy globális komplextípus, amely az eszközfájl metaadatait kulcs/érték karakterláncként írja le. Például key="language", és value="eng".  

A cikk végén található XML-példa: [XML példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Kulcs**<br /><br /> Kötelező |**xs:karakterlánc** |A kulcs/érték pár kulcsa. |
| **value**<br /><br /> Kötelező |**xs:karakterlánc** |A kulcs/érték pár értéke. |

## <a name="programtype"></a><a name="ProgramType"></a>Programtype (Programtype)
**A ProgramType** egy globális komplextípus, amely egy programot ír le.  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Programazonosító**<br /><br /> Kötelező |**xs:int** |Programazonosító |
| **Programok száma**<br /><br /> Kötelező |**xs:int** |Programok száma. |
| **PmtPid között**<br /><br /> Kötelező |**xs:int** |A programtérkép-táblázatok (PMT-k) a programokkal kapcsolatos információkat tartalmaznak.  További információ: [PMt](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT). |
| **PcrPid**<br /><br /> Kötelező |**xs: int** |A dekóder használja. További információ: [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) |
| **StartPTS** |**xs: hosszú** |Bemutató időbélyegzőjának indítása. |
| **ENDPTS** |**xs: hosszú** |Bemutató időbélyegzőjának befejezése. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a>StreamDispositionType típus
**A StreamDispositionType** egy globális komplex típus, amely leírja az adatfolyamot.  

A cikk végén található XML-példa: [XML példa](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Alapértelmezett**<br /><br /> Kötelező |**xs: int** |Állítsa ezt az attribútumot 1-re, jelezve, hogy ez az alapértelmezett bemutató. |
| **Dub**<br /><br /> Kötelező |**xs:int** |Állítsa ezt az attribútumot 1-re, jelezve, hogy ez a szinkronizált bemutató. |
| **Eredeti**<br /><br /> Kötelező |**xs: int** |Állítsa ezt az attribútumot 1-re, jelezve, hogy ez az eredeti bemutató. |
| **Megjegyzés**<br /><br /> Kötelező |**xs:int** |Állítsa ezt az attribútumot 1-re, jelezve, hogy ez a szám kommentárt tartalmaz. |
| **Dalszövegei**<br /><br /> Kötelező |**xs:int** |Állítsa ezt az attribútumot 1-re, jelezve, hogy ez a szám dalszövegeket tartalmaz. |
| **Karaoke**<br /><br /> Kötelező |**xs:int** |Állítsa ezt az attribútumot 1-re, jelezve, hogy ez a karaoke számot jelöli (háttérzene, ének nélkül). |
| **Kényszerített**<br /><br /> Kötelező |**xs:int** |Állítsa ezt az attribútumot 1-re, jelezve, hogy ez a kényszerített bemutató. |
| **Halláskárosult**<br /><br /> Kötelező |**xs:int** |Állítsa ezt az attribútumot 1-re, jelezve, hogy ez a pálya a nagyothallók számára van. |
| **Látássérült**<br /><br /> Kötelező |**xs:int** |Állítsa ezt az attribútumot 1-re, jelezve, hogy ez a pálya a gyengén látókszámára van. |
| **CleanEffects (Tisztahatások)**<br /><br /> Kötelező |**xs: int** |Állítsa ezt az attribútumot 1-re, jelezve, hogy a sávnak tiszta hatásai vannak. |
| **Csatoltkép**<br /><br /> Kötelező |**xs: int** |Állítsa ezt az attribútumot 1-re, jelezve, hogy a sávnak vannak képei. |

## <a name="programs-element"></a><a name="Programs"></a>Programok elem
Több **programelemet** tartalmazó burkolóelem.  

### <a name="child-elements"></a>Gyermek elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **Program**<br /><br /> minOccurs="0" maxOccurs="nem kötött" |[Programtype (Programtype)](media-services-input-metadata-schema.md#ProgramType) |Az MPEG-TS formátumú eszközfájlok esetében az eszközfájlban lévő programokra vonatkozó információkat tartalmaz. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a>VideoTracks elem
 Több **VideoTrack-elemet** tartalmazó burkolóelem.  

 A cikk végén található XML-példa: [XML példa](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Gyermek elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="nem kötött" |[VideoTrackType (a TrackType öröklődik)](media-services-input-metadata-schema.md#VideoTrackType) |Az eszközfájlban lévő videosávokra vonatkozó információkat tartalmazza. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a>AudioTracks elem
 Burkolóelem, amely több **AudioTrack** elemet tartalmaz.  

 A cikk végén található XML-példa: [XML példa](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **Hangsáv**<br /><br /> minOccurs="0" maxOccurs="nem kötött" |[AudioTrackType (a TrackType öröklődik)](media-services-input-metadata-schema.md#AudioTrackType) |Az eszközfájlban lévő hangsávokkal kapcsolatos információkat tartalmazza. |

## <a name="schema-code"></a><a name="code"></a>Sémakód
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
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


## <a name="xml-example"></a><a name="xml"></a>PÉLDA XML-re
Az alábbi példa a bemeneti metaadat-fájlt mutat be.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
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

