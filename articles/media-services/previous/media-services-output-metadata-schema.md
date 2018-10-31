---
title: Az Azure Media Services kimeneti metaadatok séma |} A Microsoft Docs
description: A témakör áttekintést nyújt az Azure Media Services kimeneti metaadatok séma.
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
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: e92bcd412071d1a991a0bd3ec7b28df9f509c54c
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50250886"
---
# <a name="output-metadata"></a>Kimeneti metaadatok
## <a name="overview"></a>Áttekintés
A kódolási feladat vagy társítva egy bemeneti objektuma (eszközök) a amely kívánt kódolási feladatok elvégzésére. Kódolása például MP4-fájlokat, a H.264 MP4 adaptív sávszélességű csoportok; Hozzon létre egy miniatűr; Hozzon létre lefedi. A feladat befejeztével a kimeneti adategység jön létre.  A kimeneti objektum tartalmazza a videót, hangot, a miniatűrök, stb. A kimeneti objektum a kimeneti adategység metaadatait tartalmazó fájl is tartalmazza. A metaadatok XML-fájl neve formátuma a következő: &lt;source_file_name&gt;_manifest.xml (például BigBuckBunny_manifest.xml).  

Ha meg szeretné vizsgálni a metaadatait tartalmazó fájl, létrehozhat egy **SAS** kereső és letöltése a fájlt a helyi számítógépen.  

Ez a cikk ismerteti az elemek és az XML-séma típusát, amelyre a kimeneti metada (&lt;source_file_name&gt;_manifest.xml) alapul. A bemeneti objektum metaadatait tartalmazó fájl kapcsolatos információkért lásd: [bementi metaadat](media-services-input-metadata-schema.md).  

> [!NOTE]
> A teljes séma kódjának és XML-példában ez a cikk végén találja.  
>
>

## <a name="AssetFiles "></a> AssetFiles gyökérelem
A kódolási feladat bejegyzéseket AssetFile gyűjteménye.  

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Leírás |
| --- | --- |
| **AssetFile**<br/><br/> minOccurs = maxOccurs "0" = "1" |Egy [AssetFile elem](media-services-output-metadata-schema.md) a AssetFiles gyűjtemény részét képezi. |

## <a name="AssetFile "></a> AssetFile elem
XML-példát talál [XML-példa](media-services-output-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Name (Név)**<br/><br/> Szükséges |**xs:string** |A média az eszközintelligencia-fájl neve. |
| **Méret**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:Long** |Mérete bájtban adategységfájlon. |
| **Időtartam**<br/><br/> Szükséges |**DURATION típusú** |Tartalom play vissza időtartama. |

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Leírás |
| --- | --- |
| **Adatforrások** |Gyűjtemény beviteli/forrás médiafájlok, annak érdekében, hogy ez AssetFile feldolgozott. További információkért lásd: [forrás elem](media-services-output-metadata-schema.md). |
| **VideoTracks**<br/><br/> minOccurs = maxOccurs "0" = "1" |Minden egyes fizikai AssetFile tartalmazhat, nulla vagy több videók egy megfelelő tárolót formátumba közbeékeléses nyomon követi. További információkért lásd: [VideoTracks elem](media-services-output-metadata-schema.md). |
| **AudioTracks**<br/><br/> minOccurs = maxOccurs "0" = "1" |Minden egyes fizikai AssetFile tartalmazhat, egy megfelelő tárolót formátumba közbeékeléses nulla vagy több hangsáv. Ez az összes ezeket hangsáv gyűjteménye. További információkért lásd: [AudioTracks elem](media-services-output-metadata-schema.md). |

## <a name="Sources "></a> Források elem
Gyűjtemény beviteli/forrás médiafájlok, annak érdekében, hogy ez AssetFile feldolgozott.  

XML-példát talál [XML-példa](media-services-output-metadata-schema.md#xml).  

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Leírás |
| --- | --- |
| **Forrás**<br/><br/> minOccurs = maxOccurs "1" = "korlátlan" |Egy bemeneti/forrásfájl az objektum létrehozásához használt. További információkért lásd: [forrás elem](media-services-output-metadata-schema.md). |

## <a name="Source "></a> Forrás elem
Egy bemeneti/forrásfájl az objektum létrehozásához használt.  

XML-példát talál [XML-példa](media-services-output-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Name (Név)**<br/><br/> Szükséges |**xs:string** |A bemeneti forrásfájl neve. |

## <a name="VideoTracks "></a> VideoTracks elem
Minden egyes fizikai AssetFile tartalmazhat, nulla vagy több videók egy megfelelő tárolót formátumba közbeékeléses nyomon követi. A **VideoTracks** elem a videó nyomon követi egy gyűjteményét képviseli.  

XML-példát talál [XML-példa](media-services-output-metadata-schema.md#xml).  

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Leírás |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs = maxOccurs "1" = "korlátlan" |Egy meghatározott videót a szülő AssetFile nyomon követése. További információkért lásd: [VideoTrack elem](media-services-output-metadata-schema.md#VideoTrack). |

## <a name="VideoTrack"></a> VideoTrack elem
Egy meghatározott videót a szülő AssetFile nyomon követése.  

XML-példát talál [XML-példa](media-services-output-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Azonosító**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:int** |A videó követése nulla alapú indexét. **Megjegyzés:** ez **azonosító** nem feltétlenül a TrackID a MP4-fájlokat. |
| **FourCC**<br/><br/> Szükséges |**xs:string** |Videó kodek FourCC kódot. |
| **Profil** |**xs:string** |H264 profil (H264 kodek csak érvényes). |
| **Szint** |**xs:string** |H264 szintje (H264 kodek csak érvényes). |
| **Szélesség**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:int** |A kódolt videó szélességét (képpontban). |
| **Magasság**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:int** |A kódolt videó magasságát (képpontban). |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:Double** |Oldalarány számlálójának képmegjelenítő. |
| **DisplayAspectRatioDenominator**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:Double** |Oldalarány nevező képmegjelenítő. |
| **Képkockasebesség**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:decimal** |Mért videó képkockasebessége .3f formátumban. |
| **TargetFramerate**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:decimal** |Az előre beállított céloldali videó képkockasebessége .3f formátumban. |
| **Átviteli sebesség**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:int** |Videó átlagos átviteli sebessége a kilobit / másodperc, a AssetFile a kiszámított módon. Csak a elemi stream hasznos számolja, és nem tartalmazza a csomagolási terhelését. |
| **TargetBitrate**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:int** |A videó nyomon követése, az átlagos átviteli TARGET kért keresztül a kódolási beállításkészletet, a kilobit / másodperc. |
| **MaxGOPBitrate**<br/><br/> minInclusive = "0" |**xs:int** |Maximális Képcsoporttal átlagos bitsebességű a a videó nyomon követése a kilobit / másodperc. |

## <a name="AudioTracks "></a> AudioTracks elem
Minden egyes fizikai AssetFile tartalmazhat, egy megfelelő tárolót formátumba közbeékeléses nulla vagy több hangsáv. A **AudioTracks** elem minden ezeket hangsáv egy gyűjteményét képviseli.  

XML-példát talál [XML-példa](media-services-output-metadata-schema.md#xml).  

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Leírás |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs = maxOccurs "1" = "korlátlan" |Egy adott hangsávra a szülő AssetFile. További információkért lásd: [AudioTrack elem](media-services-output-metadata-schema.md). |

## <a name="AudioTrack "></a> AudioTrack elem
Egy adott hangsávra a szülő AssetFile.  

XML-példát talál [XML-példa](media-services-output-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Azonosító**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:int** |Ez hangsávra nulla alapú indexét. **Megjegyzés:** ez nem feltétlenül a TrackID a MP4-fájlokat. |
| **Kodek** |**xs:string** |Hangsávra kodek karakterlánc. |
| **EncoderVersion** |**xs:string** |Nem kötelező kódoló verziószám-karakterlánc, EAC3 szükséges. |
| **csatornák**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:int** |Hang csatornák száma. |
| **Érvénytelen a SamplingRate**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:int** |Hang mintavételi ráta samples/mp vagy Hz. |
| **Átviteli sebesség**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:int** |Bit / másodperc, a AssetFile a kiszámított hang átlagos átviteli sebessége. Csak a elemi stream hasznos számolja, és nem tartalmazza a csomagolási terhelését. |
| **Bitspersample obsahuje neplatnou Hodnotu**<br/><br/> minInclusive = "0"<br/><br/> Szükséges |**xs:int** |Bit / minta wFormatTag formátumban írja be. |

### <a name="child-elements"></a>Gyermekelemek
| Name (Név) | Leírás |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs = maxOccurs "0" = "1" |Hangerő mérési eredmények paraméterek. További információkért lásd: [LoudnessMeteringResultParameters elem](media-services-output-metadata-schema.md). |

## <a name="LoudnessMeteringResultParameters "></a> LoudnessMeteringResultParameters elem
Hangerő mérési eredmények paraméterek.  

XML-példát talál [XML-példa](media-services-output-metadata-schema.md#xml).  

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **DPLMVersionInformation** |**xs:string** |**Dolby** szakmai hangerő mérési development kit verzió. |
| **DialogNormalization**<br/><br/> minInclusive "-31" maxInclusive = "1"<br/><br/> Szükséges |**xs:int** |Kötelező, ha a LoudnessMetering van beállítva, DPLM keresztül létrehozott DialogNormalization |
| **IntegratedLoudness**<br/><br/> minInclusive = "-70" maxInclusive "10" =<br/><br/> Szükséges |**xs:float** |Integrált hangerő |
| **IntegratedLoudnessUnit**<br/><br/> Szükséges |**xs:string** |Integrált hangerő egység. |
| **IntegratedLoudnessGatingMethod**<br/><br/> Szükséges |**xs:string** |Korhatáralapú hozzáférés-korlátozás azonosítója |
| **IntegratedLoudnessSpeechPercentage**<br/><br/> minInclusive = "0" maxInclusive "100" = |**xs:float** |A program százalékos keresztül beszédtartalomból. |
| **SamplePeak**<br/><br/> Szükséges |**xs:float** |Csúcsidőszak abszolút Mintaérték, alaphelyzetbe állítása óta, vagy utolsó óta nincs bejelölve, csatornánként.  Egységek dBFS. |
| **SamplePeakUnit**<br/><br/> rögzített = "dBFS"<br/><br/> Szükséges |**xs:anySimpleType** |Minta csúcs egység. |
| **TruePeak**<br/><br/> Szükséges |**xs:float** |Maximális valós Csúcsérték, megfelelően ITU-R BS.1770-2, alaphelyzetbe állítása óta, vagy utolsó óta nincs bejelölve, csatornánként. Egységek dBTP. |
| **TruePeakUnit**<br/><br/> rögzített = "dBTP"<br/><br/> Szükséges |**xs:anySimpleType** |Igaz csúcs egység. |

## <a name="schema-code"></a>Séma kódjának
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
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



## <a name="xml"></a> XML-példa

A következő XML-kódja egy példát a kimeneti metaadatait tartalmazó fájl.  

    <AssetFiles xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema"   
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
