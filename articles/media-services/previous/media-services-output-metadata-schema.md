---
title: Az Azure Media Services kimeneti metaadatsémája | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure Media Services kimeneti metaadat-sémáról.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 3f0c6b60e2be625d1f869c3eda4acb9dfd3c6e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74886812"
---
# <a name="output-metadata"></a>Kimeneti metaadatok
## <a name="overview"></a>Áttekintés
A kódolási feladat olyan bemeneti eszközhöz (vagy eszközökhöz) van társítva, amelyen bizonyos kódolási feladatokat el szeretne végezni. Például kódolhat egy MP4-fájlt H.264 MP4 adaptív sávszélességű bitráta-készletekbe; miniatűr létrehozása; átfedéseket hozhat létre. A feladat befejezésekor egy kimeneti eszköz keletkezik.  A kimeneti eszköz tartalmaz videót, hangot, miniatűröket stb. A kimeneti eszköz is tartalmaz egy fájlt a kimeneti eszköz metaadatait. A metaadat-XML-fájl neve a &lt;következő&gt;formátumú: source_file_name _manifest.xml (például BigBuckBunny_manifest.xml).  

A Media Services nem szokta elő, hogy metaadatokat generáljon a bemeneti eszközökből. A bemeneti metaadatok csak összetevőként jönnek létre, ha egy bemeneti eszközt egy feladatban dolgoznak fel. Ezért ez a összetevő a kimeneti eszközbe van írva. Különböző eszközök segítségével jönnek létre metaadatok a bemeneti eszközök és a kimeneti eszközök. Ezért a bemeneti metaadatok egy kicsit eltérnek a kimeneti metaadatok.

Ha meg szeretné vizsgálni a metaadatfájlt, **SAS** létrehozhat egy SAS-lokátort, és letöltheti a fájlt a helyi számítógépre.  

Ez a cikk annak az XML-sémának az elemeit és&lt;típusait ismerteti, amelyeken a kimeneti metada ( source_file_name&gt;_manifest.xml) alapul. A bemeneti eszköz metaadatait tartalmazó fájlról a Bemeneti metaadatok című témakörben talál további információt.  

A teljes sémakódot és az XML-példát a cikk végén találja.  

## <a name="assetfiles-root-element"></a><a name="AssetFiles"></a>AssetFiles gyökérelem
AssetFile bejegyzések gyűjteménye a kódolási feladathoz.  

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **Eszközfájl**<br/><br/> minOccurs="0" maxOccurs="1" |Az AssetFiles gyűjtemény részét képezi AssetFile elem. |

## <a name="assetfile-element"></a><a name="AssetFile"></a>AssetFile elem
Megkeresheti az [XML-példákat.](#xml)  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Név**<br/><br/> Kötelező |**xs:karakterlánc** |A médiaeszköz fájlneve. |
| **Méret**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:hosszú** |Az eszközfájl mérete bájtban. |
| **Időtartam**<br/><br/> Kötelező |**xs:időtartam** |A tartalom lejátszási időtartama. |

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **Források** |Az AssetFile létrehozásához feldolgozott bemeneti/forrásmédia-fájlok gyűjteménye. További információ: Source element. |
| **VideoTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Minden fizikai AssetFile tartalmazhat benne nulla vagy több videó számok atleaved egy megfelelő tároló formátumban. További információ: VideoTracks element. |
| **Hangsávok**<br/><br/> minOccurs="0" maxOccurs="1" |Minden fizikai AssetFile tartalmazhat benne nulla vagy több hangsávot egy megfelelő tárolóformátumba. Ez a gyűjtemény az összes hangsávot. További információ: AudioTracks element. |

## <a name="sources-element"></a><a name="Sources"></a>Források elem
Az AssetFile létrehozásához feldolgozott bemeneti/forrásmédia-fájlok gyűjteménye.  

Megkeresheti az [XML-példákat.](#xml)  

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **Forrás**<br/><br/> minOccurs="1" maxOccurs="nem kötött" |Az eszköz létrehozásához használt bemeneti/forrásfájl. További információ: Source element. |

## <a name="source-element"></a><a name="Source"></a>Forráselem
Az eszköz létrehozásához használt bemeneti/forrásfájl.  

Megkeresheti az [XML-példákat.](#xml)  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Név**<br/><br/> Kötelező |**xs:karakterlánc** |Bemeneti forrásfájl neve. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a>VideoTracks elem
Minden fizikai AssetFile tartalmazhat benne nulla vagy több videó számok atleaved egy megfelelő tároló formátumban. A **VideoTracks** elem az összes videosáv gyűjteményét jelöli.  

Megkeresheti az [XML-példákat.](#xml)  

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="nem kötött" |Egy adott videósáv a szülő AssetFile-ban. További információ: VideoTrack element. |

## <a name="videotrack-element"></a><a name="VideoTrack"></a>VideoTrack elem
Egy adott videósáv a szülő AssetFile-ban.  

Megkeresheti az [XML-példákat.](#xml)  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:int** |A videósáv nulla alapú indexe. **Megjegyzés:**  Ez **az azonosító** nem feltétlenül az MP4-fájlban használt TrackID. |
| **Fourcc**<br/><br/> Kötelező |**xs:karakterlánc** |Video codec FourCC kód. |
| **Profil** |**xs:karakterlánc** |H264 profil (csak a H264 kodekre vonatkozik). |
| **Szint** |**xs:karakterlánc** |H264 szint (csak a H264 kodeket alkalmazható). |
| **Width**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:int** |Kódolt videószélesség képpontban. |
| **Height**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:int** |Kódolt videómagasság képpontban. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:dupla** |Videomegjelenítési képarány számlálója. |
| **DisplayAspectRatioDenevező**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:dupla** |A videó megjelenítési képarány-nevezője. |
| **Képkockasebesség**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:decimális** |Mért videó képkockasebesség .3f formátumban. |
| **Célkeretarány**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:decimális** |Előre beállított célvideoképkocka-sebesség .3f formátumban. |
| **Bitráta**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:int** |Átlagos videó bitsebesség kilobit/másodpercben, az AssetFile-ból számítva. Csak az elemi adatfolyam hasznos terhelését számolja, és nem tartalmazza a csomagolási többletterhelést. |
| **Célbitrátus**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:int** |Célátlag-bitráta ehhez a videosávhoz, a kódolási készleten keresztül kért módon, kilobit/másodpercben. |
| **MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |Maximális GOP átlagos bitráta ehhez a videó sávhoz, kilobit/másodpercben. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a>AudioTracks elem
Minden fizikai AssetFile tartalmazhat benne nulla vagy több hangsávot egy megfelelő tárolóformátumba. A **Hangsáv** elem az összes hangsáv gyűjteményét képviseli.  

Megkeresheti az [XML-példákat.](#xml)  

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **Hangsáv**<br/><br/> minOccurs="1" maxOccurs="nem kötött" |Adott hangsáv a szülő Eszközfájlban. További információ: AudioTrack element. |

## <a name="audiotrack-element"></a><a name="AudioTrack"></a>Hangsáv elem
Adott hangsáv a szülő Eszközfájlban.  

Megkeresheti az [XML-példákat.](#xml)  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:int** |A hangsáv nulla alapú indexe. **Megjegyzés:**  Ez nem feltétlenül a TrackID használt MP4 fájlt. |
| **Codec** |**xs:karakterlánc** |Hangsáv kodek karakterlánc. |
| **Kódolóverzió** |**xs:karakterlánc** |Opcionális kódoló verziókarakterlánc, az EAC3-hoz szükséges. |
| **Csatornák**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:int** |Az audiocsatornák száma. |
| **Mintavételi ráta**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:int** |Hangmintavételi sebesség minták/mp-ben vagy Hz-ben. |
| **Bitráta**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:int** |Átlagos hangátviteli sebesség bit/másodpercben, az Eszközfájlból számítva. Csak az elemi adatfolyam hasznos terhelését számolja, és nem tartalmazza a csomagolási többletterhelést. |
| **BitsPerSample**<br/><br/> minInclusive ="0"<br/><br/> Kötelező |**xs:int** |A wFormatTag formátum típusának mintánkénti bitjei. |

### <a name="child-elements"></a>Gyermek elemek
| Név | Leírás |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs="0" maxOccurs="1" |A hangosság mérésének eredményparaméterei. További információ: LoudnessMeteringResultParameters element. |

## <a name="loudnessmeteringresultparameters-element"></a><a name="LoudnessMeteringResultParameters"></a>LoudnessMeteringResultParameters elem
A hangosság mérésének eredményparaméterei.  

Megkeresheti az [XML-példákat.](#xml)  

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **DPLMVersionInformáció** |**xs:karakterlánc** |**Dolby** professzionális hangosság mérés fejlesztési készlet verzió. |
| **Párbeszédek normalizálása**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> Kötelező |**xs:int** |A DPLM-en keresztül generált DialogNormalization, amely a LoudnessMetering beállításakor szükséges |
| **Integrált hangosság**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> Kötelező |**xs:lebegtetés** |Integrált hangosság |
| **Integrált HangosságEgység**<br/><br/> Kötelező |**xs:karakterlánc** |Integrált hangosság egység. |
| **IntegratedLoudnessGatingMódszer**<br/><br/> Kötelező |**xs:karakterlánc** |Gating azonosító |
| **Integrált Hangosbeszédszázalék**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs:lebegtetés** |Beszédtartalom a program felett százalékban. |
| **Mintacsúcs**<br/><br/> Kötelező |**xs:lebegtetés** |Csúcs abszolút mintaérték, az alaphelyzetbe állítás vagy az utolsó törlés óta, csatornánként.  Az egységek dBFS. |
| **SamplePeakUnit**<br/><br/> fixed="dBFS"<br/><br/> Kötelező |**xs:anySimpleType** |Minta csúcsegysége. |
| **TruePeak között**<br/><br/> Kötelező |**xs:lebegtetés** |Az ITU-R BS.1770-2 érték szerinti maximális valós csúcsérték az alaphelyzetbe állítás vagy az utolsó törlés óta, csatornánként. Az egységek dBTP-k. |
| **TruePeakUnit egység**<br/><br/> fixed="dBTP"<br/><br/> Kötelező |**xs:anySimpleType** |Valódi csúcsegység. |

## <a name="schema-code"></a>Sémakód
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               elementFormDefault="qualified">  
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
                  <xs:element name="Sources">  
                    <xs:annotation>  
                      <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:attribute name="Name" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>input source file name</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
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
                            <xs:attribute name="FourCC" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video codec FourCC code</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Profile" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Level" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                            <xs:attribute name="Framerate" use="required">  
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
                            <xs:attribute name="TargetFramerate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:decimal">  
                                  <xs:minInclusive value="0"/>  
                                  <xs:fractionDigits value="3"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="TargetBitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:sequence>  
                              <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                                <xs:annotation>  
                                  <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                                </xs:annotation>  
                                <xs:complexType>  
                                  <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="DialogNormalization" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:int">  
                                        <xs:minInclusive value="-31"/>  
                                        <xs:maxInclusive value="-1"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudness" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation>Integrated loudness</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="-70"/>  
                                        <xs:maxInclusive value="10"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Gating identifier</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                    <xs:annotation>  
                                      <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="0"/>  
                                        <xs:maxInclusive value="100"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                                  </xs:attribute>  
                                </xs:complexType>  
                              </xs:element>  
                            </xs:sequence>  
                            <xs:attribute name="Id" use="required">  
                              <xs:annotation>  
                                <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Codec" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>audio track codec string</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="EncoderVersion" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="BitsPerSample" use="required">  
                              <xs:annotation>  
                                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
                <xs:attribute name="Duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:duration"/>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  



## <a name="xml-example"></a><a name="xml"></a>PÉLDA XML-re

A következő XML egy példa a Kimeneti metaadatfájlra.  

    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
                xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
      <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
