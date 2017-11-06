---
title: "Media Encoder Standard séma |} Microsoft Docs"
description: "A témakör áttekintést nyújt a Media Encoder Standard séma."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 4c060062-8ef2-41d9-834e-e81e8eafcf2e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: juliako
ms.openlocfilehash: 0d034e2c3827b297173262d294a2e566a6b45fac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard séma
Ez a témakör egyes elemek és az XML-séma típusú amelyen [Media Encoder Standard készletek](media-services-mes-presets-overview.md) alapulnak. A témakör minden elemek és az érvényes értékekre ismertetése. A teljes séma közzé lesz téve egy későbbi időpontban.  

## <a name="Preset"></a>Előre definiált (legfelső szintű elem)
Meghatározza az egy kódolási beállításkészlet.  

### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **Kódolás** |[Kódolás](media-services-mes-schema.md#Encoding) |Legfelső szintű elem azt jelzi, hogy a bemeneti forrás a kódoláshoz. |
| **Kimenetek** |[Kimenetek](media-services-mes-schema.md#Output) |Kívánt kimeneti fájlok gyűjteménye. |

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Verzió**<br/><br/> Szükséges |**xs:decimal** |Az előre definiált verziót. A következő korlátozások vonatkoznak: xs:fractionDigits érték = "1" és a xs:minInclusive érték például = "1" **verzió = "1.0"**. |

## <a name="Encoding"></a>Kódolás
Egy a következő elemeket tartalmazza.  

### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |Videó H.264 kódolás beállításait. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |A hang kódolását AAC beállításait. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Bmp lemezképek beállításait. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Png-lemezképek beállításait. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Jpg lemezképek beállításait. |

## <a name="H264Video"></a>H264Video
### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs = "0" |**xs:Boolean** |Jelenleg csak egy pass kódolás esetén támogatott. |
| **KeyFrameInterval**<br/><br/> minOccurs = "0"<br/><br/> **alapértelmezett = "00: 00:02"** |**xs:time** |Meghatározza, hogy a IDR keretek mértékegységben másodperces rögzített térközét. Más néven a GOP időtartama. Lásd: **SceneChangeDetection** (lent) szabályozására, hogy a kódoló ezt az értéket is eltérnek. |
| **SceneChangeDetection**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "false" |**xs:Boolean** |Ha értéke igaz, kódoló megpróbálja észlelni a leképezni kívánt jelenetben módosítása a videó, és szúrja be egy IDR keret. |
| **Összetettsége**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "Kiegyensúlyozott" |**xs:String** |Meghatározza a kompromisszum közötti kódolja a sebesség és a videó minőségét. A következő értékek egyike lehet: **sebesség**, **kiegyensúlyozott**, vagy **minősége**<br/><br/> Alapértelmezett: **elosztott terhelésű** |
| **SyncMode**<br/><br/> minOccurs = "0" | |A szolgáltatás a későbbi kiadásokban megjelenik. |
| **H264Layers**<br/><br/> minOccurs = "0" |[H264Layers](media-services-mes-schema.md#H264Layers) |A réteggyűjteménynek kimeneti videó. |

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Az állapot** |**xs:String** | Ha a bemeneti adatok nem videó, előfordulhat, hogy kényszeríteni kívánja a kódoló beszúrása egy fekete-fehér videó nyomon követése. Ehhez használja a feltétel = "InsertBlackIfNoVideoBottomLayerOnly" (a beszúrást csak a legalacsonyabb sávszélességű videó) vagy a feltétel = "InsertBlackIfNoVideo" (videó minden beszúrása kimeneti bitrates). További információ [ebben](media-services-advanced-encoding-with-mes.md#no_video) a témakörben érhető el.|

## <a name="H264Layers"></a>H264Layers

Alapértelmezés szerint ha a kódoló csak hang, és a nem kép tartalmazó bemenete küldi el a kimeneti adategységen fogja tartalmazni fájlok, amelyek csak a hang adatokat. Előfordulhat, hogy néhány lejátszó nem kezeli az ilyen kimeneti adatfolyamokba. Használhatja a H264Video **InsertBlackIfNoVideo** beállítást, ha a kódoló videó nyomon hozzáadása a kimenetet a forgatókönyv a kényszerített attribútumot. További információ [ebben](media-services-advanced-encoding-with-mes.md#no_video) a témakörben érhető el.
              
### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs = "0" maxOccurs = "unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |A réteggyűjteménynek H264. |

## <a name="H264Layer"></a>H264Layer
> [!NOTE]
> Videó korlátai vannak értékek alapján a leírt a [H264 szintek](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) tábla.  
> 
> 

### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "Auto" |**xs:String** |A következők egyike lehet **xs:string** értékek: **automatikus**, **alapterv**, **fő**, **magas**. |
| **Szint**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "Auto" |**xs:String** | |
| **Átviteli sebesség**<br/><br/> minOccurs = "0" |**xs:INT** |Ez a videó réteg, Kbit/s megadott használt átviteli sebesség. |
| **MaxBitrate**<br/><br/> minOccurs = "0" |**xs:INT** |A maximális sávszélességű, ez a videó réteg, Kbit/s megadott használatos. |
| **BufferWindow**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "00: 00:05" |**xs:time** |A videó puffer hossza. |
| **Szélessége**<br/><br/> minOccurs = "0" |**xs:INT** |A kimeneti videó keret, képpontban szélességét.<br/><br/> Vegye figyelembe, hogy jelenleg, meg kell adnia a szélességét és magasságát. A szélességét és magasságát kell páros számra. |
| **Magassága**<br/><br/> minOccurs = "0" |**xs:INT** |A kimeneti videó keret, képpontban magasságát.<br/><br/> Vegye figyelembe, hogy jelenleg, meg kell adnia a szélességét és magasságát. A szélességét és magasságát kell páros számra.|
| **BFrames**<br/><br/> minOccurs = "0" |**xs:INT** |B keretek között hivatkozás keretek számát. |
| **ReferenceFrames**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "3" |**xs:INT** |Egy GOP hivatkozás keretek számát. |
| **EntropyMode**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "Cabac" |**xs:String** |A következő értékek egyike lehet: **Cabac** és **Cavlc**. |
| **Képkockasebességhez**<br/><br/> minOccurs = "0" |Racionális szám |Meghatározza, hogy a kimeneti videó sebessége. Szeretné, hogy a kódoló a bemeneti videóhoz használja az ugyanazon képkockasebessége használja a "0 vagy 1" alapértelmezett. Megengedett értékek várhatóan közös képkockák díjszabás alább látható módon. Azonban minden érvényes ésszerű engedélyezett. Például 1/1 1 fps és érvényes.<br/><br/> – 12/1 (12 fps)<br/><br/> -15/1 (15 fps)<br/><br/> -24/1 (24 fps)<br/><br/> 24000/1001 (23.976 fps)<br/><br/> -25/1 (25 fps)<br/><br/>  -30/1 (30 fps)<br/><br/> 30000/1001 (29,97 fps) <br/> <br/>**Megjegyzés:** létrehozásakor egy egyéni készletet a többszörös sávszélességű kódolási, majd a készlet összes rétegének **kell** képkockasebességhez azonos értéket.|
| **AdaptiveBFrame**<br/><br/> minOccurs = "0" |**xs:Boolean** |Az Azure media encoder másolása |
| **A szeletek**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "0" |**xs:INT** |Meghatározza, hogy hány szeletek keret van osztva. Javasoljuk, hogy használja az alapértelmezett. |

## <a name="AACAudio"></a>AACAudio
 A következő elemek és csoportok sorozatát tartalmazza.  

 AAC kapcsolatos további információkért lásd: [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "AACLC" |**xs:String** |A következő értékek egyike lehet: **AACLC**, **HEAACV1**, vagy **HEAACV2**. |

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Az állapot** |**xs:String** |Ha bemeneti hang nélkül csendes hang nyomon tartalmazó objektumot eredményezett a kódoló kényszerítéséhez "InsertSilenceIfNoAudio" értéket adja meg.<br/><br/> Alapértelmezés szerint ha küldi el a kódoló csak videó, és nincsenek hang tartalmazó bemenete ezután a kimeneti adategységen tartalmazza csak videó adatokat tartalmazó fájlt. Előfordulhat, hogy néhány lejátszó nem kezeli az ilyen kimeneti adatfolyamokba. Ez a beállítás segítségével csendes hang nyomon hozzáadása a kimenetet a forgatókönyv a kódoló kényszerítése. |

### <a name="groups"></a>Csoportok
| Referencia | Leírás |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs = "0" |Leírása az [AudioGroup](media-services-mes-schema.md#AudioGroup) tudni, hogy a megfelelő számú csatornák mintavételi ráta és beállíthatja az egyes profilok átviteli sebességet. |

## <a name="AudioGroup"></a>AudioGroup
Milyen értékek érvényesek az egyes profilok kapcsolatos részletekért lásd az alábbi "Hang kodek részletei" táblázat.  

### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **Csatornák**<br/><br/> minOccurs = "0" |**xs:INT** |A kódolt hang csatornák száma. Az érvényes beállítások a következők: 1, 2, 5, 6, 8.<br/><br/> Alapértelmezett: 2. |
| **Érvénytelen a SamplingRate**<br/><br/> minOccurs = "0" |**xs:INT** |A hang mintavételi ráta, Hz szerepel. |
| **Átviteli sebesség**<br/><br/> minOccurs = "0" |**xs:INT** |Az átviteli kódolás a hang-, megadva, a kbps használt. |

### <a name="audio-codec-details"></a>Hang kodek részletei
Hang kodek|Részletek  
-----------------|---  
**AACLC**|1:<br/><br/> -11025: 8 &lt;sávszélességű = &lt; 16<br/><br/> -12000: 8 &lt;sávszélességű = &lt; 16<br/><br/> -16000: 8 &lt;sávszélességű = &lt;32<br/><br/>-22050: 24 &lt;sávszélességű = &lt; 32<br/><br/> -24000: 24 &lt;sávszélességű = &lt; 32<br/><br/> -32000: 32 &lt;sávszélességű = &lt;= 192<br/><br/> -44100: 56 &lt;sávszélességű = &lt;= 288<br/><br/> -48000: 56 &lt;sávszélességű = &lt;= 288<br/><br/> -88200: 128 &lt;sávszélességű = &lt;= 288<br/><br/> -96000: 128 &lt;sávszélességű = &lt;= 288<br/><br/> 2:<br/><br/> -11025: 16 &lt;sávszélességű = &lt; 24<br/><br/> -12000: 16 &lt;sávszélességű = &lt; 24<br/><br/> -16000: 16 &lt;sávszélességű = &lt; 40<br/><br/> -22050: 32 &lt;sávszélességű = &lt; 40<br/><br/> -24000: 32 &lt;sávszélességű = &lt; 40<br/><br/> -32000: 40 &lt;sávszélességű = &lt;= 384<br/><br/> -44100: 96 &lt;sávszélességű = &lt;= 576<br/><br/> -48000: 96 &lt;sávszélességű = &lt;= 576<br/><br/> -88200: 256 &lt;sávszélességű = &lt;= 576<br/><br/> -96000: 256 &lt;sávszélességű = &lt;= 576<br/><br/> 5/6:<br/><br/> -32000: 160 &lt;sávszélességű = &lt;= 896<br/><br/> -44100: 240 &lt;sávszélességű = &lt;= 1024<br/><br/> -48000: 240 &lt;sávszélességű = &lt;= 1024<br/><br/> -88200: 640 &lt;sávszélességű = &lt;= 1024<br/><br/> -96000: 640 &lt;sávszélességű = &lt;= 1024<br/><br/> 8:<br/><br/> -32000: 224 &lt;sávszélességű = &lt;= 1024<br/><br/> -44100: 384 &lt;sávszélességű = &lt;= 1024<br/><br/> -48000: 384 &lt;sávszélességű = &lt;= 1024<br/><br/> -88200: 896 &lt;sávszélességű = &lt;= 1024<br/><br/> -96000: 896 &lt;sávszélességű = &lt;= 1024  
**HEAACV1**|1:<br/><br/> -22050: sávszélességű = 8<br/><br/> -24000: 8 &lt;sávszélességű = &lt;= 10<br/><br/> -32000: 12 &lt;sávszélességű = &lt;= 64<br/><br/> -44100: 20 &lt;sávszélességű = &lt;= 64<br/><br/> -48000: 20 &lt;sávszélességű = &lt;= 64<br/><br/> -88200: sávszélességű = 64<br/><br/> 2:<br/><br/> -32000: 16 &lt;sávszélességű = &lt;= 128<br/><br/> -44100: 16 &lt;sávszélességű = &lt;= 128<br/><br/> -48000: 16 &lt;sávszélességű = &lt;= 128<br/><br/> -88200: 96 &lt;sávszélességű = &lt;= 128<br/><br/> -96000: 96 &lt;sávszélességű = &lt;= 128<br/><br/> 5/6:<br/><br/> -32000: 64 &lt;sávszélességű = &lt;= 320<br/><br/> -44100: 64 &lt;sávszélességű = &lt;= 320<br/><br/> -48000: 64 &lt;sávszélességű = &lt;= 320<br/><br/> -88200: 256 &lt;sávszélességű = &lt;= 320<br/><br/> -96000: 256 &lt;sávszélességű = &lt;= 320<br/><br/> 8:<br/><br/> -32000: 96 &lt;sávszélességű = &lt;= 448<br/><br/> -44100: 96 &lt;sávszélességű = &lt;= 448<br/><br/> -48000: 96 &lt;sávszélességű = &lt;= 448<br/><br/> -88200: 384 &lt;sávszélességű = &lt;= 448<br/><br/> -96000: 384 &lt;sávszélességű = &lt;= 448  
**HEAACV2**|2:<br/><br/> -22050: 8 &lt;sávszélességű = &lt;= 10<br/><br/> -24000: 8 &lt;sávszélességű = &lt;= 10<br/><br/> -32000: 12 &lt;sávszélességű = &lt;= 64<br/><br/> -44100: 20 &lt;sávszélességű = &lt;= 64<br/><br/> -48000: 20 &lt;sávszélességű = &lt;= 64<br/><br/> -88200: 64 &lt;sávszélességű = &lt;= 64  
  

## <a name="Clip"></a>Klip
### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Kezdő időpont** |**DURATION típusú** |A bemutató kezdési idejét határozza meg. A StartTime értékének a bemeneti videó abszolút időbélyegeket egyezniük kell. Például, ha a bemeneti videó első keret 12:00:10.000 időbélyeggel rendelkezik, majd StartTime kell lennie, mint 12:00:10.000 vagy nagyobb. |
| **Időtartam** |**DURATION típusú** |A bemutató (például egy átmeneti területre a videó megjelenésének) időtartamát határozza meg. |

## <a name="Output"></a>Kimeneti
### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Fájlnév** |**xs:String** |A kimeneti fájl nevét.<br/><br/> Az alábbi táblázatban ismertetett makrók segítségével összeállítása a kimeneti fájl nevét. Példa:<br/><br/> **"Kimenetek": [{"Fájlnevet": "{Basename}*{feloldási}*{sávszélességű} .mp4", "Formátum": {"Type": "MP4Format"}}] ** |

### <a name="macros"></a>Makrók
| A makróban | Leírás |
| --- | --- |
| **{Basename}** |VoD kódolás végez, {Basename} esetén az elsődleges fájl a bemeneti eszköz AssetFile.Name tulajdonságának első 32 karakter.<br/><br/> Ha a bemeneti eszköz élő archívum létrehozása, majd a {Basename} származik a kiszolgáló jegyzékfájlt trackName attribútumokat. Ha egy subclip feladat a TopBitrate, mint a elküldése: "< VideoStream\>TopBitrate < / VideoStream\>", és a kimeneti fájl videót tartalmaz, akkor a {Basename} a videó réteg trackName az első 32 karakter az a legnagyobb átviteli sebesség.<br/><br/> Ha ehelyett küld egy subclip feladat az összes bemeneti bitrates, például a "< VideoStream\>* < / VideoStream\>", és a kimeneti fájl videót tartalmaz, akkor {Basename} az trackName első 32 karakterét a videó rétegét. |
| **{Kodek}** |Videó leképezések "H264" és "AAC" Audio. |
| **{Sávszélességű}** |A cél videó sávszélességű Ha a kimeneti fájl tartalmaz videó és a hang, vagy a cél hang sávszélességű, ha a kimeneti fájlt csak hang tartalmazza. A használt értéke az kbit/s átviteli sebesség. |
| **{Csatorna}** |Ha a fájl tartalmaz hangot hang csatorna száma. |
| **{Width}** |A videó képpont, a kimeneti fájl, ha a fájl tartalmaz videó szélességét. |
| **{Magasság}** |A videó képpont, a kimeneti fájl, ha a fájl tartalmaz videó magasságát. |
| **{Bővítmény}** |A "Type" tulajdonságot a kimeneti fájl örököl. A kimeneti fájl nevét egy bővítmény egyik lesz a: "mp4", "ts", "jpg", "png" vagy "bmp". |
| **{Index}** |Kötelező a miniatűr. Csak szerepelnie kell egyszer. |

## <a name="Video"></a>Videó (kodek örököl összetett típus)
### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Kezdés** |**xs:String** | |
| **Lépés** |**xs:String** | |
| **Tartomány** |**xs:String** | |
| **PreserveResolutionAfterRotation** |**xs:Boolean** |Részletes ismertetése a következő részben: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a>PreserveResolutionAfterRotation
Javasoljuk, hogy használni a PreserveResolutionAfterRotation jelző százalékos értékben kifejezve értékeket (Width = "100 %", magasság = "100 %").  

Alapértelmezés szerint a encode feloldási beállításaitól (szélesség, magasság) a Media Encoder Standard (MES) készletek a célzott 0 fokos Elforgatás videókat. Például ha a bemeneti videó 1280 x 720 a nulla fokos elforgatás, majd az alapértelmezett készletek győződjön meg arról, hogy a kimeneti rendelkezik-e az azonos megoldás. Lásd az alábbi képet.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Azonban ez azt jelenti, hogy ha a bemeneti videó rögzítésének nem nulla forgatási együtt (pl. egy okostelefonján vagy táblagépén tárolt függőleges), akkor MES alapértelmezés szerint a bemeneti videóhoz (szélesség, magasság) encode feloldási beállításokat alkalmazza, és majd kártalanítja elforgatási. Például tekintse meg az alábbi képen. A készletet használ Width = "100 %", magasság = "100 %", amely MES értelmezi, hogy a kimenet 1280 széles és 720 képpontok magas. A videó elforgatása, miután, majd zsugorítja a illeszthető be ezt az ablakot, ami pillar-box területek bal és jobb kép.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Ha a fenti nem a kívánt viselkedés, akkor a PreserveResolutionAfterRotation jelzőjének használja, és állítsa "true" (alapértelmezett érték a "false"). Ezért szélessége a készlet-e = "100 %", magasság = "100 %" PreserveResolutionAfterRotation értéke "true", egy bemeneti videó, amely 1280 képpontnál és 720 képpont magas a 90 fokos Elforgatás a művelet létrehoz egy kimeneti nulla fokos elforgatás, de széles és 1280 720 képpontban megadva magassága képpontban megadva. Tekintse meg az alábbi képen.  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a>FormatGroup (csoport)
### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a>BmpLayer
### <a name="element"></a>Elem
| Név | Típus | Leírás |
| --- | --- | --- |
| **Szélessége**<br/><br/> minOccurs = "0" |**xs:INT** | |
| **Magassága**<br/><br/> minOccurs = "0" |**xs:INT** | |

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Az állapot** |**xs:String** | |

## <a name="PngLayer"></a>PngLayer
### <a name="element"></a>Elem
| Név | Típus | Leírás |
| --- | --- | --- |
| **Szélessége**<br/><br/> minOccurs = "0" |**xs:INT** | |
| **Magassága**<br/><br/> minOccurs = "0" |**xs:INT** | |

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Az állapot** |**xs:String** | |

## <a name="JpgLayer"></a>JpgLayer
### <a name="element"></a>Elem
| Név | Típus | Leírás |
| --- | --- | --- |
| **Szélessége**<br/><br/> minOccurs = "0" |**xs:INT** | |
| **Magassága**<br/><br/> minOccurs = "0" |**xs:INT** | |
| **Minőségi**<br/><br/> minOccurs = "0" |**xs:INT** |Érvényes értékek: 1(worst)-100(best) |

### <a name="attributes"></a>Attribútumok
| Név | Típus | Leírás |
| --- | --- | --- |
| **Az állapot** |**xs:String** | |

## <a name="PngLayers"></a>PngLayers
### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs = "0" maxOccurs = "unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a>BmpLayers
### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs = "0" maxOccurs = "unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a>JpgLayers
### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs = "0" maxOccurs = "unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a>BmpImage (videó örököl összetett típus)
### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG rétegek |

## <a name="JpgImage"></a>JpgImage (videó örököl összetett típus)
### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG rétegek |

## <a name="PngImage"></a>PngImage (videó örököl összetett típus)
### <a name="elements"></a>Elemek
| Név | Típus | Leírás |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs = "0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG rétegek |

## <a name="examples"></a>Példák
XML-készletek, beépített példákat lásd: a séma alapján lásd [feladat készletet (Media Encoder Standard) MES](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

