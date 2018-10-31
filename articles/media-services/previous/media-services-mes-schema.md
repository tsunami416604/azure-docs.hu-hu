---
title: A Media Encoder Standard-séma |} A Microsoft Docs
description: A cikk áttekintést nyújt a Media Encoder Standard-séma.
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: 95f7d5cafa39daccccbd35c44510038d28601aed
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241752"
---
# <a name="media-encoder-standard-schema"></a>A Media Encoder Standard-séma
Ez a cikk azt ismerteti, egyes elemek és az XML-séma típusú, amelyen [Media Encoder Standard készletek](media-services-mes-presets-overview.md) alapulnak. A cikk nyújt elemek és érvényes értékekre ismertetése.  

## <a name="Preset"></a> Készlet (gyökérelem)
Határozza meg egy kódolási beállításkészletet.  

### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Kódolás** |[Kódolás](media-services-mes-schema.md#Encoding) |A gyökérelem, azt jelzi, hogy a bemeneti források értéket kódolni kell. |
| **Kimenetek** |[Kimenetek](media-services-mes-schema.md#Output) |A kívánt kimeneti fájlok gyűjtemény. |
| **StretchMode**<br/>minOccurs="0"<br/>alapértelmezett = "automatikus méretezése|xs:String|A kimeneti képkocka mérete, a kitöltési, képpont szabályozhatja, vagy hogy oldalarányának megőrzésével. **StretchMode** a következők egyike lehet: **nincs**, **AutoSize** (alapértelmezett), vagy **automatikus méretezés**.<br/><br/>**Nincs**: szigorúan hajtsa végre a kimeneti felbontás (például a **szélesség** és **magasság** a készletet a) a képpontos oldalarány vagy a megjelenítési oldalarányát meghatározza a bemeneti videó figyelembe vétele nélkül. Ajánlott forgatókönyvekben, például [vágása](media-services-crop-video.md), ahol a kimeneti videóhoz tartozik-e egy másik méretarányban a bemeneti képest. <br/><br/>**Automatikus méretezés**: kimeneti felbontás elfér a ablakon belül (szélesség * magasság) által előre megadott. A kódoló azonban egy kimeneti videót, amely rendelkezik a négyzetes (1:1) képpontos oldalarány hoz létre. Ezért vagy kimeneti szélessége vagy magassága kimeneti ahhoz, hogy megfeleljen a megjelenített oldalarányát meghatározza a bemeneti kitöltése nélkül felülbírálásokkal módosíthatók. Például ha a bemeneti 1920 × 1080 képpont, és a kódolási előbeállítás 1280 x 1280 kéri, majd a készlet magasság értéke legyen felülírva, és a kimeneti 1280 x 720 között, amely fenntartja a bemeneti oldalarány 16:9 lesz. <br/><br/>**Automatikus méretezés**: Ha szükséges, tartsa tiszteletben a kívánt kimeneti felbontást, biztosítva, hogy rendelkezik-e a kimenetben az aktív videó terület bemeneti méretaránya kitölti a kimeneti videót (a postaláda vagy pillarbox). Tegyük fel például, a bemeneti 1920 × 1080 képpont, és a kódolási előbeállítás 1280 x 1280 kéri. Ezután a videó kimenet 1280 x 1280, de tartalmazni fog egy belső "aktív videószolgáltatás" oldalarány 16:9, valamint postaláda régiók 280 képpont magasságú helyén, a felső és alsó téglalapjának 1280 x 720 között. Egy másik példa Ha a bemeneti 1440 x 1080 képpont, és a kódolási előbeállítás kéri a 1280 x 720 között, majd a kimenetet lesz 1280 x 720 között, amely tartalmaz egy belső téglalapjának 960 × 720 pillar box régió 160 képpont szélességű és a bal és jobb és 4:3 méretarány. 

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Verzió**<br/><br/> Szükséges |**xs: tizedes tört** |Az előre megadott verzió. Az alábbi korlátozások érvényesek: xs:fractionDigits érték = "1" és a xs:minInclusive érték például = "1" **verzió = "1.0"**. |

## <a name="Encoding"></a> Kódolás
Sorozata, a következő elemeket tartalmazza:  

### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |A videó kódolása H.264 beállításai. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |Az AAC hang kódolása beállításai. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Bmp lemezképek beállításait. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Png-kép beállításai. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Jpg lemezképek beállításait. |

## <a name="H264Video"></a> H264Video
### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs:boolean** |Jelenleg csak egy pass-kódolás használata támogatott. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:time** |Meghatározza, hogy másodperces egység IDR keretek közötti rögzített távolság. Más néven a Képcsoporttal időtartama. Lásd: **SceneChangeDetection** szabályozni, hogy a kódoló ezt az értéket is eltérnek. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> default=”false” |**xs: logikai** |Ha értéke igaz, kódoló megpróbálja észlelni a jelenet módosítása a videóban, és a egy IDR keret szúr be. |
| **Bonyolultsága**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:string** |Azt szabályozza, a kompromisszum közötti kódolás sebességének és videó minősége. A következő értékek egyike lehet: **sebesség**, **kiegyensúlyozott**, vagy **minősége**<br/><br/> Alapértelmezett: **elosztott terhelésű** |
| **SyncMode**<br/><br/> minOccurs="0" | |A szolgáltatás egy későbbi kiadásban lesz közzétéve. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |A réteggyűjteménynek kimeneti videót. |

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **a feltétel** |**xs:string** | Ha a bemeneti videó nem rendelkezik, előfordulhat, hogy kényszeríteni kívánja a kódoló beszúrása egy fekete-fehér videó nyomon követése. Ehhez használja a feltétel = "InsertBlackIfNoVideoBottomLayerOnly" (a videó jelenleg csak a legalacsonyabb sávszélességű beszúrása) vagy feltétel = "InsertBlackIfNoVideo" (a videó minden beszúrása bitsebességre való átkódolása kimenet). További információkért tekintse meg [ezt](media-services-advanced-encoding-with-mes.md#no_video) a cikket.|

## <a name="H264Layers"></a> H264Layers

Alapértelmezés szerint ha elküldi a kódolót, amely tartalmazza a csak hangot, és nincs videó bemenete a kimeneti objektum tartalmazza, fájlok, amelyek csak hang adatokat. Egyes lejátszók előfordulhat, hogy nem tudja kezelni az ilyen kimeneti adatfolyamokba. Használhatja a H264Video **InsertBlackIfNoVideo** beállítás hozzáadása egy videó nyomon követése a kimenetet a forgatókönyvhöz a kódoló kényszerített attribútumot. További információkért tekintse meg [ezt](media-services-advanced-encoding-with-mes.md#no_video) a cikket.
              
### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs = maxOccurs "0" = "korlátlan" |[H264Layer](media-services-mes-schema.md#H264Layer) |H264 rétegek gyűjteménye. |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> Videókorlát ismertetett értékek alapulnak a [H264 szintek](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) tábla.  
> 
> 

### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> alapértelmezett = "Auto" |**xs: karakterlánc** |A következők egyike lehet **xs: karakterlánc** értékek: **automatikus**, **alapkonfiguráció**, **fő**, **magas**. |
| **Szint**<br/><br/> minOccurs="0"<br/><br/> alapértelmezett = "Auto" |**xs: karakterlánc** | |
| **Átviteli sebesség**<br/><br/> minOccurs="0" |**xs:int** |A használt videó réteg, a megadott kbit/s sávszélességű. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |A maximális használt videó réteg, a megadott kbit/s sávszélességű. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: idő** |A videó puffer hossza. |
| **Szélesség**<br/><br/> minOccurs="0" |**xs: int** |A kimeneti képkocka, képpontban szélességét.<br/><br/> Jelenleg is szélességének és magasságának kell megadnia. A szélesség és magasság kell páros számra. |
| **Magasság**<br/><br/> minOccurs="0" |**xs:int** |A kimeneti képkocka, képpontban magassága.<br/><br/> Jelenleg is szélességének és magasságának kell megadnia. A szélesség és magasság kell páros számra.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |Hivatkozás keretek között a B keretek számát. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> default=”3” |**xs:int** |Referencia a keretek egy Képcsoporttal száma. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> default=”Cabac” |**xs: karakterlánc** |A következő értékek egyike lehet: **Cabac** és **Cavlc**. |
| **Képkockasebesség**<br/><br/> minOccurs="0" |racionális száma |Meghatározza, hogy a kimeneti videót keretének aránya. Használja a "0/1" alapértelmezett ahhoz, hogy a kódoló keret ugyanez a díjszabás a bemeneti videó használja. Megengedett értékek várt lehet közös videó képkockasebességet. Azonban bármely érvényes ésszerű használata engedélyezett. Ha például 1/1 lenne, 1 fps, és érvényes.<br/><br/> – 12/1 (12 fps)<br/><br/> – 15/1 (15 fps)<br/><br/> – 24/1 (24 fps)<br/><br/> - 24000/1001 (23.976 fps)<br/><br/> – 25/1 (25 fps)<br/><br/>  – 30/1 (30 képkocka/s)<br/><br/> 30000/1001 (29,97 fps) <br/> <br/>**Megjegyzés:** létrehozásakor egy egyéni előbeállítás a többszörös átviteli sebességű kódolás, majd a készlet összes rétegeit **kell** képkockasebesség, ugyanazt az értéket.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: logikai** |Az Azure media encoder másolása |
| **A szeletek**<br/><br/> minOccurs="0"<br/><br/> alapértelmezett = "0" |**xs:int** |Meghatározza, hogy hány szeletek keret oszlik. Javasoljuk, hogy alapértelmezett. |

## <a name="AACAudio"></a> AACAudio
 A következő elemek és csoportok sorozatát tartalmazza.  

 Az AAC kapcsolatos további információkért lásd: [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding).  

### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs = "0"<br/><br/> alapértelmezett = "AACLC" |**xs: karakterlánc** |A következő értékek egyike lehet: **AACLC**, **HEAACV1**, vagy **HEAACV2**. |

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **a feltétel** |**xs: karakterlánc** |A kódoló egy eszköz, amely tartalmazza a beavatkozás nélküli hangsávra, ha bemenet nincs hang előállításához kényszerítéséhez adja meg a "InsertSilenceIfNoAudio" értéket.<br/><br/> Alapértelmezés szerint ha elküldi egy bemenetet a kódolót, amely tartalmazza a csak a videó és hang nélkül, majd a kimeneti objektum tartalmaz csak videó adatokat tartalmazó fájlok. Egyes lejátszók előfordulhat, hogy nem tudja kezelni az ilyen kimeneti adatfolyamokba. Ez a beállítás használatával kényszerítheti a kódoló csendes hangsávra hozzá a kimenetet a forgatókönyvhöz. |

### <a name="groups"></a>Csoportok
| Leírások | Leírás |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Leírása, [AudioGroup](media-services-mes-schema.md#AudioGroup) tudni, hogy a megfelelő számú csatornák, mintavételi ráta és átviteli sebesség, amely az egyes profilok beállíthatók. |

## <a name="AudioGroup"></a> AudioGroup
Milyen értékek érvényesek az egyes profilok kapcsolatos részletekért lásd: a "Hang kodek details", az alábbi táblázat.  

### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **csatornák**<br/><br/> minOccurs="0" |**xs: int** |A kódolt hang csatornák száma. Az érvényes beállítások a következők: 1, 2, 5, 6, 8.<br/><br/> Alapértelmezett: 2. |
| **Érvénytelen a SamplingRate**<br/><br/> minOccurs="0" |**xs: int** |A hang mintavételi ráta, Hz megadott. |
| **Átviteli sebesség**<br/><br/> minOccurs="0" |**xs: int** |A használják, amikor a hang-, kódolási megadva kbit/s sávszélességű. |

### <a name="audio-codec-details"></a>Hang kodek részletei
Hang kodek|Részletek  
-----------------|---  
**AACLC**|1:<br/><br/> -11025: 8 &lt;sávszélességű = &lt; 16<br/><br/> -12000: 8 &lt;sávszélességű = &lt; 16<br/><br/> -16000: 8 &lt;sávszélességű = &lt;32<br/><br/>-22050: 24 &lt;sávszélességű = &lt; 32<br/><br/> -24000: 24 &lt;sávszélességű = &lt; 32<br/><br/> -32000: 32 &lt;sávszélességű = &lt;= 192<br/><br/> -44100: 56 &lt;sávszélességű = &lt;= 288<br/><br/> -48000: 56 &lt;sávszélességű = &lt;= 288<br/><br/> -88200: 128 &lt;sávszélességű = &lt;= 288<br/><br/> -96000: 128 &lt;sávszélességű = &lt;= 288<br/><br/> 2:<br/><br/> -11025: 16 &lt;sávszélességű = &lt; 24<br/><br/> -12000: 16 &lt;sávszélességű = &lt; 24<br/><br/> -16000: 16 &lt;sávszélességű = &lt; 40<br/><br/> -22050: 32 &lt;sávszélességű = &lt; 40<br/><br/> -24000: 32 &lt;sávszélességű = &lt; 40<br/><br/> -32000: 40 &lt;sávszélességű = &lt;= 384<br/><br/> -44100: 96 &lt;sávszélességű = &lt;= 576<br/><br/> -48000: 96 &lt;sávszélességű = &lt;= 576<br/><br/> -88200: 256 &lt;sávszélességű = &lt;= 576<br/><br/> -96000: 256 &lt;sávszélességű = &lt;= 576<br/><br/> 5/6:<br/><br/> -32000: 160 &lt;sávszélességű = &lt;= 896<br/><br/> -44100: 240 &lt;sávszélességű = &lt;= 1024<br/><br/> -48000: 240 &lt;sávszélességű = &lt;= 1024<br/><br/> -88200: 640 &lt;sávszélességű = &lt;= 1024<br/><br/> -96000: 640 &lt;sávszélességű = &lt;= 1024<br/><br/> 8:<br/><br/> -32000: 224 &lt;sávszélességű = &lt;= 1024<br/><br/> -44100: 384 &lt;sávszélességű = &lt;= 1024<br/><br/> -48000: 384 &lt;sávszélességű = &lt;= 1024<br/><br/> -88200: 896 &lt;sávszélességű = &lt;= 1024<br/><br/> -96000: 896 &lt;sávszélességű = &lt;= 1024  
**HEAACV1**|1:<br/><br/> -22050: sávszélességű = 8<br/><br/> -24000: 8 &lt;sávszélességű = &lt;= 10<br/><br/> -32000: 12 &lt;sávszélességű = &lt;= 64<br/><br/> -44100: 20 &lt;sávszélességű = &lt;= 64<br/><br/> -48000: 20 &lt;sávszélességű = &lt;= 64<br/><br/> -88200: sávszélességű = 64<br/><br/> 2:<br/><br/> -32000: 16 &lt;sávszélességű = &lt;= 128<br/><br/> -44100: 16 &lt;sávszélességű = &lt;= 128<br/><br/> -48000: 16 &lt;sávszélességű = &lt;= 128<br/><br/> -88200: 96 &lt;sávszélességű = &lt;= 128<br/><br/> -96000: 96 &lt;sávszélességű = &lt;= 128<br/><br/> 5/6:<br/><br/> -32000: 64 &lt;sávszélességű = &lt;= 320<br/><br/> -44100: 64 &lt;sávszélességű = &lt;= 320<br/><br/> -48000: 64 &lt;sávszélességű = &lt;= 320<br/><br/> -88200: 256 &lt;sávszélességű = &lt;= 320<br/><br/> -96000: 256 &lt;sávszélességű = &lt;= 320<br/><br/> 8:<br/><br/> -32000: 96 &lt;sávszélességű = &lt;= 448<br/><br/> -44100: 96 &lt;sávszélességű = &lt;= 448<br/><br/> -48000: 96 &lt;sávszélességű = &lt;= 448<br/><br/> -88200: 384 &lt;sávszélességű = &lt;= 448<br/><br/> -96000: 384 &lt;sávszélességű = &lt;= 448  
**HEAACV2**|2:<br/><br/> -22050: 8 &lt;sávszélességű = &lt;= 10<br/><br/> -24000: 8 &lt;sávszélességű = &lt;= 10<br/><br/> -32000: 12 &lt;sávszélességű = &lt;= 64<br/><br/> -44100: 20 &lt;sávszélességű = &lt;= 64<br/><br/> -48000: 20 &lt;sávszélességű = &lt;= 64<br/><br/> -88200: 64 &lt;sávszélességű = &lt;= 64  
  
## <a name="Clip"></a> Klip
### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **StartTime** |**DURATION típusú** |Kezdési idejét határozza meg a bemutató. StartTime értékét meg kell felelnie a bemeneti videó abszolút időbélyegei. Például, ha a bemeneti videó első keret 12:00:10.000 az időbélyeg, majd StartTime kell lennie, mint 12:00:10.000 vagy nagyobb. |
| **Időtartam** |**DURATION típusú** |Itt adható meg (például egy felirat a videóban megjelenésének) bemutató időtartamára. |

## <a name="Output"></a> Kimenet
### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **FileName** |**xs:string** |A kimeneti fájl neve.<br/><br/> Az alábbi táblázatban ismertetett makrók használatával hozhat létre a kimeneti fájl nevét. Példa:<br/><br/> **"Kimenetek": [{"Fájlnevet": "{Basename}*{feloldási}*{sávszélességű} .mp4", "Formátum": {"Type": "MP4Format"}}]** |

### <a name="macros"></a>Makrók
| Makra | Leírás |
| --- | --- |
| **{Basename}** |VoD-kódolás végez, a {Basename} esetén az első 32 karakter hosszú lehet az elsődleges fájlnak a bemeneti objektuma AssetFile.Name tulajdonságának.<br/><br/> Ha a bemeneti objektuma élő archívumot, majd {Basename} származik a trackName attribútumokat a kiszolgáló a jegyzékfájlban. Ha egy részklip feladat a TopBitrate, mint a küld be: "< VideoStream\>TopBitrate < / VideoStream\>", és a kimeneti fájl videót tartalmaz, akkor a {Basename} az első 32 karakter hosszú lehet, a videó réteg a trackName az a legnagyobb sávszélességű.<br/><br/> Ha ehelyett küld be egy részklip feladat összes a bemeneti bitsebességre való átkódolása, például a "< VideoStream\>* < / VideoStream\>", és a kimeneti fájl videót tartalmaz, akkor {Basename} az első 32 karakter hosszú lehet, a trackName, a videó rétegét. |
| **{Kodek}** |Térképek "H264" a videó és hang "AAC". |
| **{Bitrate}** |A cél videó sávszélességű Ha a kimeneti fájl tartalmaz videó és hang vagy cél hang sávszélességű, ha a kimeneti fájl csak hang tartalmazza. A használt értéke a kbit/s sávszélességű. |
| **{Channel}** |Ha a fájl tartalmaz hang hang csatorna száma. |
| **{Width}** |A videó (képpontban), a kimeneti fájl, ha a fájl tartalmazza a videó szélességét. |
| **{Height}** |A videó (képpontban), a kimeneti fájl, ha a fájl tartalmazza a videó magassága. |
| **{Extension}** |A kimeneti fájlt a "Type" tulajdonsága örökli. A kimeneti fájl nevét, amely egy kiterjesztéssel rendelkezik a: "mp4", "ts", "jpg", "png" vagy "bmp". |
| **{Index}** |Miniatűr megadása kötelező. Csak lehet jelen egyszer. |

## <a name="Video"></a> Videó (kodek örököl komplex típus)
### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Kezdés** |**xs:string** | |
| **Lépés** |**xs:string** | |
| **Címtartomány** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs:boolean** |Részletes ismertetését a következő szakaszt: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
Javasoljuk, hogy használja a **PreserveResolutionAfterRotation** jelző, százalékban kifejezett értékeket együtt (szélesség "100 %" Height = "100 %-os" =).  

Alapértelmezés szerint a encode feloldásának beállításai (szélesség, magasságát) a Media Encoder Standard (MES) készletek a videókat, amelyekhez 0 fokos Elforgatás célozzák meg. Például ha a bemeneti videó 1280 x 720 között, a nulla fokos elforgatás, majd az alapértelmezett készletek győződjön meg arról, hogy a kimenet rendelkezik-e meg a megoldást.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Ha a bemeneti videó rögzítésének befejeztével a nullától eltérő rotációja (például egy okostelefonján vagy táblagépén függőleges tárolt), majd MES alapértelmezés szerint érvényes a encode feloldásának beállításai (szélesség, magasság) a bemeneti videó és majd kompenzálja elforgatási. Például lásd a következő képen látható. A készletet használ Width = "100 %" Height = "100 %", amely MES értelmezi, hogy a kimenet 1280 képpont szélességű és 720 képpont magas. Elforgatás a videót, miután, majd csökken a illeszkedjenek ezt az ablakot, a bal és jobb pillar-box területek vezető a képen látható.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Azt is megteheti, akkor is győződjön meg arról, használja a **PreserveResolutionAfterRotation** jelzőt, és állítsa be "true" (alapértelmezett érték "hamis"). Tehát ha a készlet szélessége = "100 %", Height = "100 %", és állítsa be a "true", a bemeneti videó PreserveResolutionAfterRotation 1280 képpont szélességű és 720 képpont magas, a 90 fokos Elforgatás azaz eredményez a kimenetben nulla fokos elforgatás, de 720 képpont széles és 1280 képpont magas. Tekintse meg a következő képen látható:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a> FormatGroup (csoport)
### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>Elem
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Szélesség**<br/><br/> minOccurs="0" |**xs:int** | |
| **Magasság**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **a feltétel** |**xs:string** | |

## <a name="PngLayer"></a> PngLayer
### <a name="element"></a>Elem
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Szélesség**<br/><br/> minOccurs="0" |**xs:int** | |
| **Magasság**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **a feltétel** |**xs:string** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>Elem
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **Szélesség**<br/><br/> minOccurs="0" |**xs:int** | |
| **Magasság**<br/><br/> minOccurs="0" |**xs:int** | |
| **Minőség**<br/><br/> minOccurs="0" |**xs:int** |Érvényes értékek: 1(worst)-100(best) |

### <a name="attributes"></a>Attribútumok
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **a feltétel** |**xs:string** | |

## <a name="PngLayers"></a> PngLayers
### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs = maxOccurs "0" = "korlátlan" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs = maxOccurs "0" = "korlátlan" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs = maxOccurs "0" = "korlátlan" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a> BmpImage (komplex típus örökli a videó)
### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG-rétegek |

## <a name="JpgImage"></a> JpgImage (komplex típus örökli a videó)
### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG-rétegek |

## <a name="PngImage"></a> PngImage (komplex típus örökli a videó)
### <a name="elements"></a>Elemek
| Name (Név) | Típus | Leírás |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |PNG-rétegek |

## <a name="examples"></a>Példák
Beépített XML-készletek lásd a példát talál ezen séma alapján [feladat készletek (Media Encoder Standard) MES](media-services-mes-presets-overview.md).

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

