---
title: Media Encoder Standard séma | Microsoft dokumentumok
description: Ez a cikk az XML-séma azon elemeit és típusait ismerteti, amelyeken a Media Encoder Standard készletei alapulnak.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 622f14beabb1f2f109dff5d28c1591ffdd5aa000
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901435"
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard-séma
Ez a cikk az XML-séma azon elemeit és típusait ismerteti, amelyeken a [Media Encoder Standard készletei](media-services-mes-presets-overview.md) alapulnak. A cikk ismerteti az elemeket és azok érvényes értékeit.  

## <a name="preset-root-element"></a><a name="Preset"></a>Készlet (gyökérelem)
Kódolási készletet határoz meg.  

### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **Kódolás** |[Kódolás](media-services-mes-schema.md#Encoding) |A gyökérelem azt jelzi, hogy a bemeneti forrásokat kódolni kell. |
| **Kimenetek** |[Kimenetek](media-services-mes-schema.md#Output) |A kívánt kimeneti fájlok gyűjteménye. |
| **StretchMode (Rugalmas mód)**<br/>minOccurs="0"<br/>default="Automatikus méret|xs:karakterlánc|A kimeneti videoképkocka-méret, a kitöltés, a képpont vagy a megjelenítési képarány szabályozása. **A StretchMode** a következő értékek egyike lehet: **Nincs**, **AutoSize** (alapértelmezett) vagy **Automatikus méretezés**.<br/><br/>**Nincs**: Szigorúan kövesse a kimeneti felbontást (például a **szélességet** és a **magasságot** az előre beállított készletben) a bemeneti videó képpontméretarányának vagy megjelenítési képarányának figyelembe nem venélkül. Olyan esetekben ajánlott, mint például a [vágás,](media-services-crop-video.md)ahol a kimeneti videó a bemenethez képest eltérő képaránnyal rendelkezik. <br/><br/>**AutoSize**: A kimeneti felbontás elfér az ablakon belül (Szélesség * Magasság) előre beállított. A kódoló azonban olyan kimeneti videót készít, amely négyzetes (1:1) képpontméretarányt biztosít. Ezért a kimeneti szélesség vagy a kimeneti magasság felülbírálható annak érdekében, hogy megfeleljen a bemenet megjelenítési képarányának, kitöltés nélkül. Ha például a bemenet 1920x1080, és a kódolási készlet 1280x1280 értéket kér, akkor a készlet Magasság értéke felülbírálódik, és a kimenet 1280x720 lesz, ami 16:9 bemeneti képarányt tart fenn. <br/><br/>**AutoFit**: Ha szükséges, pad a kimeneti videó (postafiókkal vagy pillarbox) a kívánt kimeneti felbontás, miközben biztosítja, hogy az aktív videó régió a kimeneti ugyanaz a képarány, mint a bemeneti. Tegyük fel például, hogy a bemenet 1920x1080, és a kódolási készlet 1280x1280-at kér. Ezután a kimeneti videó lesz 1280x1280, de ez tartalmaz egy belső 1280x720 téglalap "aktív videó" képarány 16:9, és postafiók régiók 280 pixel magas a felső és alsó. Egy másik példa, ha a bemenet 1440x1080, és a kódolási készlet 1280x720-at kér, akkor a kimenet 1280x720 lesz, amely 960x720 belső téglalapot tartalmaz 4:3 képarányban, és a pillérdoboz 160 képpont széles a bal és a jobb oldalon. 

### <a name="attributes"></a>Attribútumok

| Név | Típus | Leírás |
| --- | --- | --- |
| **Verzió**<br/><br/> Kötelező |**xs: tizedes** |Az előre beállított verzió. A következő korlátozások érvényesek: xs:fractionDigits value="1" és xs:minInclusive value="1" Például **version="1.0"**. |

## <a name="encoding"></a><a name="Encoding"></a>Kódolás
A következő elemek sorozatát tartalmazza:  

### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **H264Videó** |[H264Videó](media-services-mes-schema.md#H264Video) |A videó H.264 kódolásának beállításai. |
| **AACAudio között** |[AACAudio között](media-services-mes-schema.md#AACAudio) |A hang AAC kódolásának beállításai. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |A Bmp-kép beállításai. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Png-kép beállításai. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Jpg kép beállításai. |

## <a name="h264video"></a><a name="H264Video"></a>H264Videó
### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **TwoPass (Kéthágó)**<br/><br/> minOccurs="0" |**xs:logikai** |Jelenleg csak egymenetes kódolás támogatott. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs:idő** |Az IDR-keretek közötti rögzített térközt határozza meg másodpercegységekben. Más néven a GOP időtartama. Lásd: **SceneChangeDetection** annak szabályozásához, hogy a kódoló eltérhet-e ettől az értéktől. |
| **Jelenetmódosítás észlelése**<br/><br/> minOccurs="0"<br/><br/> default="false" |**xs: logikai** |Ha értéke igaz, a kódoló megpróbálja észlelni a jelenetváltozást a videóban, és idr-keretet szúr be. |
| **Összetettsége**<br/><br/> minOccurs="0"<br/><br/> default="Kiegyensúlyozott" |**xs:karakterlánc** |Szabályozza a kódolási sebesség és a videó minősége közötti kompromisszumot. A következő értékek egyike lehet: **Sebesség,** **Kiegyensúlyozott**vagy **Minőség**<br/><br/> Alapértelmezett: **Kiegyensúlyozott** |
| **Szinkronizálásmód**<br/><br/> minOccurs="0" | |A funkció egy későbbi kiadásban jelenik meg. |
| **H264Rétegek**<br/><br/> minOccurs="0" |[H264Rétegek](media-services-mes-schema.md#H264Layers) |Kimeneti videorétegek gyűjteménye. |

### <a name="attributes"></a>Attribútumok

| Név | Típus | Leírás |
| --- | --- | --- |
| **Állapot** |**xs:karakterlánc** | Ha a bemeneten nincs videó, érdemes lehet a kódolót monokróm videosáv behelyezésére kényszeríteni. Ehhez használja condition="InsertBlackIfNoVideoBottomLayerOnly" (csak a legalacsonyabb bitráta) vagy Condition="InsertBlackIfNoVideo" (beszúrni egy videót minden kimeneti bitráta). További információt [ebben a cikkben](media-services-advanced-encoding-with-mes.md#no_video) talál.|

## <a name="h264layers"></a><a name="H264Layers"></a>H264Rétegek

Alapértelmezés szerint, ha olyan bemenetet küld a kódolónak, amely csak hangot tartalmaz, és nem tartalmaz videót, a kimeneti eszköz csak hangadatokkal rendelkező fájlokat tartalmaz. Előfordulhat, hogy egyes játékosok nem tudják kezelni az ilyen kimeneti adatfolyamokat. A H264Video **InsertBlackIfNoVideo** attribútumbeállításával kényszerítheti a kódolót arra, hogy videosávot adjon a kimenethez ebben a forgatókönyvben. További információt [ebben a cikkben](media-services-advanced-encoding-with-mes.md#no_video) talál.
              
### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **H264Réteg**<br/><br/> minOccurs="0" maxOccurs="nem kötött" |[H264Réteg](media-services-mes-schema.md#H264Layer) |H264 rétegek gyűjteménye. |

## <a name="h264layer"></a><a name="H264Layer"></a>H264Réteg
> [!NOTE]
> A videokorlátok a [H264 Levels](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) táblázatban leírt értékeken alapulnak.  
> 
> 

### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0"<br/><br/> default="Automatikus" |**xs: karakterlánc** |A következő **x-ek** egyike lehet: karakterláncértékek: **Auto**, **Baseline**, **Main**, **Magas**. |
| **Szint**<br/><br/> minOccurs="0"<br/><br/> default="Automatikus" |**xs: karakterlánc** | |
| **Bitráta**<br/><br/> minOccurs="0" |**xs:int** |A videoréteghez használt, kbps-ben megadott bitráta. |
| **MaxBitrate érték**<br/><br/> minOccurs="0" |**xs: int** |A videoréteghez használt maximális bitráta, kbit/s-ban megadva. |
| **Pufferablak**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: idő** |A videopuffer hossza. |
| **Width**<br/><br/> minOccurs="0" |**xs: int** |A kimeneti videoképszélesség képpontban.<br/><br/> Jelenleg meg kell adnia a Szélesség és a Magasság értéket is. A Szélesség és magasság páros számnak kell lennie. |
| **Height**<br/><br/> minOccurs="0" |**xs:int** |A kimeneti videoképmagasság képpontban.<br/><br/> Jelenleg meg kell adnia a Szélesség és a Magasság értéket is. A Szélesség és magasság páros számnak kell lennie.|
| **Bképkockák**<br/><br/> minOccurs="0" |**xs: int** |A referenciakeretek közötti B keretek száma. |
| **Referenciakeretek**<br/><br/> minOccurs="0"<br/><br/> alapértelmezett="3" |**xs:int** |A gop-ban lévő referenciakeretek száma. |
| **EntrópiaMód**<br/><br/> minOccurs="0"<br/><br/> default="Cabac" |**xs: karakterlánc** |Lehet az alábbi értékek egyike: **Cabac** és **Cavlc**. |
| **Frameráta**<br/><br/> minOccurs="0" |racionális szám |A kimeneti videó képkockasebességét határozza meg. Az alapértelmezett "0/1" beállításával a kódoló nak ugyanazt a képkockasebességet kell használnia, mint a bemeneti videónak. Az engedélyezett értékek várhatóan gyakori videoképkocka-sebességek. Azonban minden érvényes racionális megengedett. Például az 1/1 1 fps és érvényes.<br/><br/> - 12/1 (12 fps)<br/><br/> - 15/1 (15 fps)<br/><br/> - 24/1 (24 fps)<br/><br/> - 24000/1001 (23.976 fps)<br/><br/> - 25/1 (25 fps)<br/><br/>  - 30/1 (30 fps)<br/><br/> - 30000/1001 (29,97 fps) <br/> <br/>**MEGJEGYZÉS** Ha egyéni készletet hoz létre a többszörös bitrátakódoláshoz, akkor a készlet minden rétegének ugyanazt az értéket **kell** használnia a FrameRate értékével.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: logikai** |Másolás az Azure médiakódolójából |
| **Szelet**<br/><br/> minOccurs="0"<br/><br/> alapértelmezett="0" |**xs:int** |Azt határozza meg, hogy egy keret hány szeletre van felosztva. Az alapértelmezett használata ajánlott. |

## <a name="aacaudio"></a><a name="AACAudio"></a>AACAudio között
 A következő elemek és csoportok sorozatát tartalmazza.  

 Az AAC-ről további információt az [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)című témakörben talál.  

### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **Profil**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: karakterlánc** |A következő értékek egyike lehet: **AACLC**, **HEAACV1**vagy **HEAACV2**. |

### <a name="attributes"></a>Attribútumok

| Név | Típus | Leírás |
| --- | --- | --- |
| **Állapot** |**xs: karakterlánc** |Ha azt szeretné, hogy a kódoló olyan eszközt állítson létre, amely néma hangsávot tartalmaz, ha a bemenet nem rendelkezik hanggal, adja meg az "InsertSilenceIfNoAudio" értéket.<br/><br/> Alapértelmezés szerint, ha olyan bemenetet küld a kódolónak, amely csak videót tartalmaz, és nem tartalmaz hangot, akkor a kimeneti eszköz olyan fájlokat tartalmaz, amelyek csak videoadatokat tartalmaznak. Előfordulhat, hogy egyes játékosok nem tudják kezelni az ilyen kimeneti adatfolyamokat. Ezzel a beállítással kényszerítheti a kódolót, hogy csendes hangsávot adjon a kimenethez ebben a forgatókönyvben. |

### <a name="groups"></a>Csoportok

| Referencia | Leírás |
| --- | --- |
| [Audiocsoport](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |Az [AudioGroup](media-services-mes-schema.md#AudioGroup) leírásából megismerheti az egyes profilokhoz beállítható csatornák, mintavételi sebesség és átviteli sebesség megfelelő számát. |

## <a name="audiogroup"></a><a name="AudioGroup"></a>Audiocsoport
Az egyes profilokra érvényes értékekről az alábbi "Audio kodek részletek" táblázatban talál részleteket.  

### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **Csatornák**<br/><br/> minOccurs="0" |**xs: int** |A kódolt hangcsatornák száma. A következő érvényes lehetőségek: 1, 2, 5, 6, 8.<br/><br/> Alapértelmezett: 2. |
| **Mintavételi ráta**<br/><br/> minOccurs="0" |**xs: int** |A Hz-ben megadott hangmintavételi sebesség. |
| **Bitráta**<br/><br/> minOccurs="0" |**xs: int** |A hang kódolásakor használt, kbit/s-ban megadott bitráta. |

### <a name="audio-codec-details"></a>Hang kodek részletei

Hang kodek|Részletek  
-----------------|---  
**AACLC** |1:<br/><br/> - 11025: &lt;8 &lt; = bitráta 16<br/><br/> - 12000: &lt;8 &lt; = bitráta 16<br/><br/> - 16000: &lt;8 &lt;= bitráta 32<br/><br/>- 22050: &lt;24 &lt; = bitráta 32<br/><br/> - 24000: &lt;24 &lt; = bitráta 32<br/><br/> - 32000: &lt;32 &lt;= bitráta = 192<br/><br/> - 44100: &lt;56 &lt;= bitráta = 288<br/><br/> - 48000: &lt;56 &lt;= bitráta = 288<br/><br/> - 88200: 128 &lt; &lt;= bitráta = 288<br/><br/> - 96000: 128 &lt; &lt;= bitráta = 288<br/><br/> 2:<br/><br/> - 11025: &lt;16 &lt; = bitráta 24<br/><br/> - 12000: &lt;16 &lt; = bitráta 24<br/><br/> - 16000: &lt;16 &lt; = bitráta 40<br/><br/> - 22050: &lt;32 &lt; = bitráta 40<br/><br/> - 24000 : &lt;32 &lt; = bitráta 40<br/><br/> - 32000: &lt;40 &lt;= bitráta = 384<br/><br/> - 44100: &lt;96 &lt;= bitráta = 576<br/><br/> - 48000: &lt;96 &lt;= bitráta = 576<br/><br/> - 88200: 256 &lt; &lt;= bitráta = 576<br/><br/> - 96000: 256 &lt; &lt;= bitráta = 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt; &lt;= bitráta = 896<br/><br/> - 44100: 240 &lt; &lt;= bitráta = 1024<br/><br/> - 48000: 240 &lt; &lt;= bitráta = 1024<br/><br/> - 88200: 640 &lt; &lt;= bitráta = 1024<br/><br/> - 96000: 640 &lt; &lt;= bitráta = 1024<br/><br/> 8:<br/><br/> - 32000: 224 &lt; &lt;= bitráta = 1024<br/><br/> - 44100: 384 &lt; &lt;= bitráta = 1024<br/><br/> - 48000: 384 &lt; &lt;= bitráta = 1024<br/><br/> - 88200: 896 &lt; &lt;= bitráta = 1024<br/><br/> - 96000: 896 &lt; &lt;= bitráta = 1024  
**HEAACV1** |1:<br/><br/> - 22050: bitráta = 8<br/><br/> - 24000: &lt;8 &lt;= bitráta = 10<br/><br/> - 32000: &lt;12 &lt;= bitráta = 64<br/><br/> - 44100: &lt;20 &lt;= bitráta = 64<br/><br/> - 48000: &lt;20 &lt;= bitráta = 64<br/><br/> - 88200: bitráta = 64<br/><br/> 2:<br/><br/> - 32000: &lt;16 &lt;= bitráta = 128<br/><br/> - 44100: &lt;16 &lt;= bitráta = 128<br/><br/> - 48000: &lt;16 &lt;= bitráta = 128<br/><br/> - 88200: &lt;96 &lt;= bitráta = 128<br/><br/> - 96000: &lt;96 &lt;= bitráta = 128<br/><br/> 5/6:<br/><br/> - 32000: &lt;64 &lt;= bitráta = 320<br/><br/> - 44100: &lt;64 &lt;= bitráta = 320<br/><br/> - 48000: &lt;64 &lt;= bitráta = 320<br/><br/> - 88200: 256 &lt; &lt;= bitráta = 320<br/><br/> - 96000: 256 &lt; &lt;= bitráta = 320<br/><br/> 8:<br/><br/> - 32000: &lt;96 &lt;= bitráta = 448<br/><br/> - 44100: &lt;96 &lt;= bitráta = 448<br/><br/> - 48000: &lt;96 &lt;= bitráta = 448<br/><br/> - 88200: 384 &lt; &lt;= bitráta = 448<br/><br/> - 96000: 384 &lt; &lt;= bitráta = 448  
**HEAACV2** |2:<br/><br/> - 22050: &lt;8 &lt;= bitráta = 10<br/><br/> - 24000: &lt;8 &lt;= bitráta = 10<br/><br/> - 32000: &lt;12 &lt;= bitráta = 64<br/><br/> - 44100: &lt;20 &lt;= bitráta = 64<br/><br/> - 48000: &lt;20 &lt;= bitráta = 64<br/><br/> - 88200: &lt;64 &lt;= bitráta = 64  
  
## <a name="clip"></a><a name="Clip"></a>Klip
### <a name="attributes"></a>Attribútumok

| Név | Típus | Leírás |
| --- | --- | --- |
| **StartTime** |**xs:időtartam** |A bemutató kezdési idejét adja meg. A StartTime értékének meg kell egyeznie a bemeneti videó abszolút időbélyegeivel. Ha például a bemeneti videó első képkockájának időbélyegzője 12:00:10.000, akkor a StartTime-nak legalább 12:00:10.000-nek kell lennie. |
| **Időtartam** |**xs:időtartam** |Megadja a bemutató időtartamát (például egy átfedés megjelenését a videóban). |

## <a name="output"></a><a name="Output"></a>Kimeneti
### <a name="attributes"></a>Attribútumok

| Név | Típus | Leírás |
| --- | --- | --- |
| **Fájlnév** |**xs:karakterlánc** |A kimeneti fájl neve.<br/><br/> Az alábbi táblázatban ismertetett makrók segítségével hozhatlétre létre a kimeneti fájlneveket. Példa:<br/><br/> **"Kimenetek": [ { "Fájlnév": "{Basename}*{Resolution}*{Bitrate}.mp4", "Format": { "Type": "MP4Format" } } ]** |

### <a name="macros"></a>Makrók

| Makró | Leírás |
| --- | --- |
| **{Alapnév}** |Ha VoD kódolást végez, a(z) {Basename} az elsődleges fájl AssetFile.Name tulajdonságának első 32 karaktere a bemeneti eszközben.<br/><br/> Ha a bemeneti eszköz egy élő archívum, akkor a(z) {Basename} a kiszolgálójegyzékben lévő trackName attribútumokból származik. Ha a TopBitrate használatával küld be egy segédklipet, mint\>például: "<\>VideoStream TopBitrate</VideoStream ", és a kimeneti fájl videót tartalmaz, akkor a {Basename} a legmagasabb bitrátával rendelkező videóréteg trackName -jének első 32 karaktere.<br/><br/> Ha ehelyett egy segédklip-feladatot küld be az összes bemeneti bitrátával, például a "<VideoStream\>*</VideoStream\>", és a kimeneti fájl videót tartalmaz, akkor a {Basename} a megfelelő videoréteg trackName című számának első 32 karaktere. |
| **{Kodek}** |Térképek a "H264" a videó és a "AAC" audio. |
| **{Bitráta}** |A célvideo bitráta, ha a kimeneti fájl video- és hangtartalmat tartalmaz, vagy a célhang bitrátája, ha a kimeneti fájl csak hangot tartalmaz. A használt érték a bitráta kbps-ben. |
| **{Csatorna}** |Audiocsatornák száma, ha a fájl hangot tartalmaz. |
| **{Szélesség}** |A videó szélessége képpontban, a kimeneti fájlban, ha a fájl videót tartalmaz. |
| **{Magasság}** |A videó magassága képpontban, a kimeneti fájlban, ha a fájl videót tartalmaz. |
| **{Kiterjesztés}** |A kimeneti fájl "Type" tulajdonságából öröklődik. A kimeneti fájl neve van egy kiterjesztés, amely az egyik: "mp4", "ts", "jpg", "png", vagy "bmp". |
| **{Index}** |Miniatűrhöz kötelező. Csak egyszer lehet jelen. |

## <a name="video-complex-type-inherits-from-codec"></a><a name="Video"></a>Videó (az összetett típus a Kodektől örököl)
### <a name="attributes"></a>Attribútumok

| Név | Típus | Leírás |
| --- | --- | --- |
| **Kezdés** |**xs:karakterlánc** | |
| **Lépés** |**xs:karakterlánc** | |
| **Tartomány** |**xs:karakterlánc** | |
| **Felbontás megőrzéseforgatásutánforgatás után** |**xs:logikai** |Részletes magyarázat: [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="preserveresolutionafterrotation"></a><a name="PreserveResolutionAfterRotation"></a>Felbontás megőrzéseforgatásutánforgatás után
A **PreserveResolutionAfterRotation** jelzőt ajánlott százalékban kifejezett felbontási értékekkel kombinálva használni (Szélesség="100%" , Magasság="100%").  

Alapértelmezés szerint a Media Encoder Standard (MES) készletek kódolási felbontási beállításai (Szélesség, Magasság) a 0 fokos elforgatású videókat célozzák meg. Ha például a bemeneti videó 1280x720, nulla fokos elforgatással, akkor az alapértelmezett készletek biztosítják, hogy a kimenet felbontása megegyezik.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

Ha a bemeneti videót nem nulla elforgatással rögzítették (például függőlegesen tartott okostelefonnal vagy táblagéppel), akkor a MES alapértelmezés szerint a kódolási felbontás beállításait (Szélesség, Magasság) alkalmazza a bemeneti videóra, majd kompenzálja az elforgatást. Lásd például a következő képet. A készlet szélesség = "100%", magasság = "100%" , amely MES értelmezi igénylő kimenet 1280 képpont széles és 720 képpont magas. A videó elforgatása után a kép összezsugorodik, hogy beleférjen az ablakba, ami a bal és a jobb oldali oszlopdoboz-területekhez vezet.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

Másik lehetőségként használhatja a **PreserveResolutionAfterRotation** jelzőt, és "true" értékre állíthatja (az alapértelmezett érték "hamis"). Tehát, ha az előre beállított szélesség = "100%", Magasság = "100%" és MegőrzéseResolutionAfterRotation beállítása "igaz", a bemeneti videó, amely 1280 pixel széles és 720 pixel magas, 90 fokos forgatás sal, nulla fokos elforgatással eredményez kimenetet, de 720 pixel széles és 1280 képpont magas. Lásd az alábbi képet:  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="formatgroup-group"></a><a name="FormatGroup"></a>FormatGroup (csoport)
### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormátum** |**PngFormátum** | |
| **Jpg Formátum** |**Jpg Formátum** | |

## <a name="bmplayer"></a><a name="BmpLayer"></a>BmpLayer között
### <a name="element"></a>Elem

| Név | Típus | Leírás |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attribútumok

| Név | Típus | Leírás |
| --- | --- | --- |
| **Állapot** |**xs:karakterlánc** | |

## <a name="pnglayer"></a><a name="PngLayer"></a>PngLayer között
### <a name="element"></a>Elem

| Név | Típus | Leírás |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>Attribútumok

| Név | Típus | Leírás |
| --- | --- | --- |
| **Állapot** |**xs:karakterlánc** | |

## <a name="jpglayer"></a><a name="JpgLayer"></a>JpgLayer között
### <a name="element"></a>Elem

| Név | Típus | Leírás |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |
| **Minőség**<br/><br/> minOccurs="0" |**xs:int** |Érvényes értékek: 1(legrosszabb)-100(legjobb) |

### <a name="attributes"></a>Attribútumok

| Név | Típus | Leírás |
| --- | --- | --- |
| **Állapot** |**xs:karakterlánc** | |

## <a name="pnglayers"></a><a name="PngLayers"></a>PngLayers (pngrétegek)
### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **PngLayer között**<br/><br/> minOccurs="0" maxOccurs="nem kötött" |[PngLayer között](media-services-mes-schema.md#PngLayer) | |

## <a name="bmplayers"></a><a name="BmpLayers"></a>BmpLayers
### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **BmpLayer között**<br/><br/> minOccurs="0" maxOccurs="nem kötött" |[BmpLayer között](media-services-mes-schema.md#BmpLayer) | |

## <a name="jpglayers"></a><a name="JpgLayers"></a>JpgRétegek
### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **JpgLayer között**<br/><br/> minOccurs="0" maxOccurs="nem kötött" |[JpgLayer között](media-services-mes-schema.md#JpgLayer) | |

## <a name="bmpimage-complex-type-inherits-from-video"></a><a name="BmpImage"></a>BmpImage (a videóból öröklődő komplex típus örökli)
### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **PngLayers (pngrétegek)**<br/><br/> minOccurs="0" |[PngLayers (pngrétegek)](media-services-mes-schema.md#PngLayers) |Png rétegek |

## <a name="jpgimage-complex-type-inherits-from-video"></a><a name="JpgImage"></a>JpgImage (az összetett szöveg a videóból örököl)
### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **PngLayers (pngrétegek)**<br/><br/> minOccurs="0" |[PngLayers (pngrétegek)](media-services-mes-schema.md#PngLayers) |Png rétegek |

## <a name="pngimage-complex-type-inherits-from-video"></a><a name="PngImage"></a>PngImage (az összetett típus örökli a videót)
### <a name="elements"></a>Elemek

| Név | Típus | Leírás |
| --- | --- | --- |
| **PngLayers (pngrétegek)**<br/><br/> minOccurs="0" |[PngLayers (pngrétegek)](media-services-mes-schema.md#PngLayers) |Png rétegek |

## <a name="examples"></a>Példák
Tekintse meg az ezen séma alapján készült XML-készletek példáit, [lásd: Feladatkészletek MES-hez (Media Encoder Standard)](media-services-mes-presets-overview.md)című témakörben.

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

