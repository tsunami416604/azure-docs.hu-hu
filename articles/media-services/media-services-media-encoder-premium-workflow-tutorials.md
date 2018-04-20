---
title: Avanced Media Encoder prémium munkafolyamat oktatóprogramok
description: Ez a dokumentum tartalmaz forgatókönyvek, amelyek megjelenítik a Media Encoder prémium munkafolyamat speciális feladatok elvégzéséhez, és a munkafolyamat-tervezővel bonyolult munkafolyamatok létrehozásához.
services: media-services
documentationcenter: ''
author: xstof
manager: cfowler
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: 8b714fcf001a6830cffe4df8c152dab40834c7c4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Speciális Media Encoder prémium munkafolyamat oktatóprogramok
## <a name="overview"></a>Áttekintés
Ez a dokumentum tartalmaz, amelyek megjelenítik a munkafolyamatok testreszabása forgatókönyvek **munkafolyamat-Tervező**. A tényleges munkafolyamatfájlokat található [Itt](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>TARTALOMJEGYZÉK
A következő témaköröket:

* [Az egyszeres sávszélességű MP4 kódolási MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Új munkafolyamat indítása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [A Media fájl bemeneti használatával](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Tanulmányozza az adatfolyamok](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Az egy videókódoló hozzáadása. MP4-fájl létrehozása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Kódolás a hangadatfolyam](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Audió és videó-adatfolyamokat multiplexáló egy MP4-tárolóba](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [A MP4-fájl írása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [A kimeneti fájl létrehozása a Media Services eszközök](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [A befejezett munkafolyamat helyi tesztelése](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Kódolás MXF multibitrate MP4 - a dinamikus becsomagolás engedélyezve](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Egy vagy több további MP4 kimenetek hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [A kimeneti fájl nevének konfigurálása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Egy külön lejátszása hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [A "ISM" SMIL fájl hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kódolási MXF multibitrate MP4 - továbbfejlesztett tervezetének be](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * [Javítása érdekében a munkafolyamat áttekintése](#workflow-overview-to-enhance)
  * [A fájlok elnevezési konvenciók](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [A munkafolyamat legfelső szintű alakzatot közzétételi összetevő tulajdonságai](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Kimeneti fájl nevét közzétett tulajdonságértékek támaszkodnak hozta létre.](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Miniatűrök multibitrate MP4 kimenet hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * [Miniatűrök hozzáadandó munkafolyamat áttekintése](#workflow-overview-to-add-thumbnails-to)
  * [JPG kódolás hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [A szín terület átalakítás foglalkozó](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [A miniatűrök írása](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [A munkafolyamat hibáinak észleléséhez](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Befejezett munkafolyamat](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Tisztítás időalapú multibitrate MP4 kimenet](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Hozzáadni a tisztítás, a munkafolyamat áttekintése](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Az adatfolyam vágó használatával](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Befejezett munkafolyamat](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [A parancsprogram-alapú összetevők bemutatása](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [A munkafolyamaton belül Scripting: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Keret-alapú levágási multibitrate MP4 kimenet](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Tisztítás való felvételéhez tervezetének áttekintése](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [Az XML klip listáról](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [Egy parancsprogram összetevő klip listájának módosítása](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Egy ClippingEnabled kényelmi tulajdonság hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Az egyszeres sávszélességű MP4 kódolási MXF
Ez a szakasz bemutatja, hogyan hozzon létre egy egyszeres sávszélességű. MP4-fájlokat a AAC-HE kódolású hangja egy. MXF bemeneti fájl.

### <a id="MXF_to_MP4_start_new"></a>Új munkafolyamat indítása
Nyissa meg a munkafolyamat-tervezőben, és válassza ki a fájl > Új munkaterület > átkódolására tervezetének

Az új munkafolyamat három elemeit tartalmazza:

* Elsődleges forrásfájl
* XML klip listázása
* Kimeneti fájl vagy eszköz  

![Új kódolási munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Új kódolási munkafolyamat*

### <a id="MXF_to_MP4_with_file_input"></a>A Media fájl bemeneti használatával
Ahhoz, hogy a bemeneti médiafájl elfogadásához induljon el adja hozzá az adathordozó fájl bemeneti összetevőt. Vegyen fel összetevőt a munkafolyamathoz, keresse meg azt a tárház keresési mezőbe, és a kívánt bejegyzés húzza a Tervező ablak. Ismételje meg a műveletet a Media fájl bemeneti, és csatlakoztassa az elsődleges forrásfájl összetevőt a fájlnév bemeneti PIN-kód az adathordozó fájl bemeneti.

![Csatlakoztatott médiafájl bemeneti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Csatlakoztatott médiafájl bemeneti*

Egy megfelelő mintafájl egy egyéni munkafolyamat tervezése során használandó kezdetben azonosításához. Ehhez kattintson a Tervező ablak háttérben, és keresse meg a elsődleges forrásfájl tulajdonság, a jobb oldali tulajdonság panelen. A mappa ikonra, és válassza ki a kívánt fájlt, a munkafolyamat teszteléshez. A Media fájl bemeneti összetevő megvizsgálja a fájlt, és tölti fel a kimeneti PIN-kód megfelelően az ellenőrzött mintafájl részleteit.

![Ki van töltve médiafájl bemeneti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Ki van töltve médiafájl bemeneti*

A bemeneti fel van töltve, a következő lépéssel fogja kódolási beállítások; kimeneti beállítása. A hasonló elsődleges forrásfájl konfigurálása után konfigurálhatja a kimeneti mappa változóját megadó tulajdonság csak alá.

![Konfigurált bemeneti és kimeneti tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Konfigurált bemeneti és kimeneti tulajdonságai*

### <a id="MXF_to_MP4_streams"></a>Tanulmányozza az adatfolyamok
Gyakran az rendelkezik szeretné tudni, hogyan az adatfolyam a következőképpen néz azt tranzakciós a munkafolyamaton keresztül. Vizsgálja meg a munkafolyamat bármely pontján adatfolyam, kattintson egy kimeneti vagy bemeneti PIN-kódjának az összetevők. Ebben az esetben próbálkozzon a tömörítetlen videó kimeneti PIN-kódot, a Media fájl bemeneti kattint. A párbeszédpanel megnyílik, amely lehetővé teszi a kimenő videó vizsgálata.

![Tanulmányozza a tömörítetlen videó kimeneti PIN-kód](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Tanulmányozza a tömörítetlen videó kimeneti PIN-kód*

Ebben az esetben azt mutatja, hogy a videó tartalmaz egy 1920 x 1080 ráfordítás 24 képkockák másodpercenkénti 4:2:2 mintavételi szinte 2 perces videót.

### <a id="MXF_to_MP4_file_generation"></a>Az egy videókódoló hozzáadása. MP4-fájl létrehozása
Most egy tömörítetlen Video és PIN-kódok érhetők el több tömörítetlen hang kimeneti használja a Media fájl bemeneti. Ahhoz, hogy a bejövő videó kódolása egy kódolási összetevő hozzá kell adni a munkafolyamat - ebben az esetben előállítása érdekében. MP4-fájlokat.

A video-adatfolyamot H.264 kódolására, adja hozzá a AVC Videókódoló összetevőt a Tervező felületére. Ez az összetevő egy Kibontás video-adatfolyamot bemenetként veszi, és kézbesíti az AVC tömörített video-adatfolyamot a kimeneti PIN-kód a.

![Frissíthető AVC kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Frissíthető AVC kódoló*

A tulajdonság határozza meg, hogyan történik a kódolás pontosan. Most rá egy pillantást, néhány fontosabb beállítás:

* Kimeneti szélességének és magasságának kimeneti: meghatározza, hogy a kódolt videó felbontása. Ebben az esetben 640 x 360 a helyes beállítást.
* Képkockasebessége: Ha beállítása csatlakoztatott fog csak fogadja el a forrás képkockasebessége, akkor lehet, ha felülbírálja ezt. Ilyen képkockasebességhez átalakítás nem mozgásérzékelő – a kompenzációt.
* Profil és szint: meghatározza, hogy a AVC profil és a szintnek. További információ a különböző szintű és profilokkal kényelmesen beszerzéséhez kattintson a kérdőjel ikon AVC videó kódoló összetevőjének, és a súgó lapon megjelenik az egyes szintjének további információkra. Például használhatja a fő profilt 3.2 (alapértelmezett) szinten.
* Értékelje a mód és átviteli sebesség (KB/s): Ebben a forgatókönyvben választhat egy állandó átviteli sebesség (CBR) kimeneti, 1200-as kbit/s
* Videó formátum: a VUI (videó használhatóság adatokat), amely lekérdezi a H.264 adatfolyamba való mentésre (ügyféloldali vonatkozó információkat a dekóder javítása érdekében a megjelenítési által használt, de nem feltétlenül megfelelő dekódolása) írt kapcsolatos információkat tartalmazza:
* NTSC (Egyesült Államok vagy japán, 30 fps használatával a jellemző)
* PAL (Európa, 25 fps használatával a jellemző)
* GOP méretezési módját: GOP rögzített méretű beállítása a lezárt GOPs 2 másodperc kulcs időközt a célokra. 2 másodpercben a beállítást választja, a dinamikus becsomagolás Azure Media Services-kompatibilitási mód biztosítja.

Az AVC kódoló hírcsatornát, csatlakozzon a tömörítetlen videó kimeneti PIN-kódot az adathordozó fájl bemeneti összetevőtől a tömörítetlen videó bemeneti PIN-kódot az AVC kódoló.

![Csatlakoztatott AVC kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Csatlakoztatott AVC fő kódoló*

### <a id="MXF_to_MP4_audio"></a>Kódolás a hangadatfolyam
Ezen a ponton az eredeti tömörítetlen hangadatfolyam továbbra is meg kell tömöríthetők. A hangadatfolyam tömörítési AAC kódoló (Dolby) összetevő hozzáadása a munkafolyamathoz.

![Frissíthető AVC kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Frissíthető AAC kódoló*

Most nincs inkompatibilitás: nincs csak a egyetlen tömörítetlen hang bemeneti PIN-kódot a AAC kódoló, miközben valószínűleg több, mint az adathordozó fájl bemeneti két különböző tömörítetlen hang adatfolyamok érhető el: egy a bal oldali hang csatorna, egy, a jobb. (Ha között legyen hang foglalkozó, ez hat csatornák.) Így nincs lehetőség a közvetlenül kapcsolódnak a hang a Media fájl bemeneti forrás a AAC hang kódoló. A AAC összetevő vár az úgynevezett "kihagyásos" hangadatfolyam: egyetlen adatfolyam, amely rendelkezik a bal és a jobb oldali csatornák időosztásos egymással. Egyszer azt tudja megállapítani a forrás-adathordozó fájlból, zeneszámok milyen helyzetben a forráshelyen, az azt hozhat létre a bal és jobb megfelelően hozzárendelt hangalapú pozíciójának ilyen kihagyásos hangadatfolyam.

Először egy szeretne használatával hozzon létre egy kihagyásos adatfolyam a szükséges forrás hang csatornák. A hang adatfolyam Interleaver összetevő kezeli, ez az USA. Adja hozzá a munkafolyamat, és csatlakozzon a hang kimenetek Media fájl bemeneti bele.

![Csatlakoztatott hangadatfolyam Interleaver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Csatlakoztatott hangadatfolyam Interleaver*

Most, hogy egy kihagyásos hangadatfolyam, azt még nem helyének megadása a bal vagy jobb hangalapú pozíciók való hozzárendelése. Ahhoz, hogy ezek megadása után a hangalapú pozíció Assigner használhatják azt.

![Egy hangalapú pozíció Assigner hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Egy hangalapú pozíció Assigner hozzáadása*

A hangalapú pozíció Assigner használatra konfigurálja sztereó bemeneti adatfolyam keresztül egy kódoló beállított szűrő az "Egyéni", "(L, R) 2.0." a csatorna az adott néven beállítás neve (Ez hozzárendeli a bal oldali hangalapú állapot csatornához 1 és jobb oldali hangalapú csatorna 2.)

Csatlakoztassa a hangalapú pozíció Assigner kimenetét a AAC kódoló a bemeneti. Ezt követően adja a AAC kódoló használható a "2.0 (L, R)" csatorna-készletet, így az tudni fogja a bemeneti sztereó hang kezelésére.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Audió és videó-adatfolyamokat multiplexáló egy MP4-tárolóba
Az AVC megadott kódolt video-adatfolyamot és a AAC kódolású hangadatfolyam, azt is rögzítheti, mindkettő egy. MP4-tároló. A különböző adatfolyamokba keverési be egyetlen egy folyamathoz "multiplexáló" (vagy a "muxing"). Ebben az esetben azt még kihagyásos a hang- és a video-adatfolyamot összefüggő egyetlen. MP4-csomag. A koordináló Ez az összetevő egy. MP4-tárolónak a neve a ISO MPEG-4 Multiplexer. Vegyen fel egyet a Tervező felületére, és csatlakozzon a AVC videó kódoló, mind a AAC kódoló a bemeneti adatok.

![Csatlakoztatott MPEG4 Multiplexer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Csatlakoztatott MPEG4 Multiplexer*

### <a id="MXF_to_MP4_writing_mp4"></a>A MP4-fájl írása
Kimeneti fájl írásakor, a kimeneti fájl összetevő szolgál. Nem lehet csatlakozni ez kimenetét a ISO MPEG-4 Multiplexer, hogy a kimeneti lekérdezi írt lemezre. Ehhez az szükséges, csatlakoztassa a tároló (MPEG-4) kimeneti PIN-kód írási bemeneti PIN-kódja a kimeneti fájl.

![Kimeneti fájl csatlakoztatva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Kimeneti fájl csatlakoztatva*

A használt fájlnevet a fájl tulajdonság határozza meg. Tulajdonság lehetnek szoftveresen kötött egy adott értékre, nagy valószínűséggel szeretne inkább beállítása kifejezésből.

A munkafolyamatot, automatikusan határozza meg a kimeneti névtulajdonság kifejezésből fájlt, kattintson a gombra a fájl neve (ikon a mappa) mellett. A legördülő menüből válassza ki "Kifejezése." Ekkor megjelenik a kifejezés-szerkesztőt. Először törölje a tartalmát a szerkesztőbe.

![Üres kifejezés-szerkesztő](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Üres kifejezés-szerkesztő*

A kifejezés szerkesztő segítségével adhat meg szövegkonstans értéket, egy vagy több változót. Változók dollárjelet kezdődik. Elérte a $ kulcs, mert a szerkesztő változók közül választhat a legördülő listában jeleníti meg. Abban az esetben, ha a kimeneti könyvtár változó és a bemeneti alap fájlnév változó fogjuk használni:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Kitöltött kimenő kifejezés-szerkesztő](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Kitöltött kimenő kifejezés-szerkesztő*

> [!NOTE]
> Ahhoz, hogy az Azure-ban a kódolási feladat kimeneti fájl megtekintéséhez meg kell adnia egy értéket a kifejezés-szerkesztőben.
>
>

Ha úgy, hogy elérte-e az OK gombra a kifejezés győződjön meg arról, a Tulajdonságok ablak előnézetét jeleníti meg a fájl tulajdonság oldja fel a rendszer ezen a ponton a időben történő értéket.

![Fájl kifejezés kimeneti dir oldja fel.](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Fájl kifejezés kimeneti dir oldja fel.*

### <a id="MXF_to_MP4_asset_from_output"></a>A kimeneti fájl létrehozása a Media Services eszközök
Azt írt kimeneti MP4-fájlokat, amíg azt továbbra is meg kell adnia, hogy a fájl tartozik a kimeneti adategységen, mely media services állít elő, a munkafolyamat végrehajtása miatt. Ennek érdekében a kimeneti fájl/eszköz csomópont a munkafolyamat vásznon szolgál. Ez a csomópont minden bejövő fájlok ellenőrizze az eredményül kapott Azure Media Services-eszközt.

A kimeneti fájl összetevő csatlakozni a kimeneti fájl/eszköz összetevő a munkafolyamat befejezéséhez.

![Befejezett munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Befejezett munkafolyamat*

### <a id="MXF_to_MP4_test"></a>A befejezett munkafolyamat helyi tesztelése
Helyileg a munkafolyamat tesztelése, kattintson a lejátszás gombra a felső eszköztáron. Befejezésekor a munkafolyamat végrehajtása, tekintse meg a kimenetet, a megadott kimeneti mappában jön létre. Látni fogja, hogy a MXF bemeneti forrásfájlból kódolt végzett MP4 kimeneti fájl.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>Kódolás MXF MP4 - multibitrate a dinamikus becsomagolás engedélyezve
Ez a forgatókönyv hoz létre a több sávszélességű MP4-fájlokat kódolású AAC egyetlen hang. MXF bemeneti fájl.

Ha egy többszörös sávszélességű eszköz kimeneti szükséges együttesen használják az Azure Media Services szolgáltatásban több GOP igazított MP4-fájlok az egyes egy másik átviteli sebesség és a feloldási generálása kell a dinamikus becsomagolás szolgáltatásaira. Ehhez a [kódolás MXF be egy egyféle sávszélességű MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) útmutatóul szolgál, az jó kiindulási pont.

![Munkafolyamat indítása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Munkafolyamat indítása*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Egy vagy több további MP4 kimenetek hozzáadása
Minden MP4-fájlokat az eredményül kapott Azure Media Services eszközt egy másik átviteli sebesség és a megoldás támogatja. Adjuk hozzá egy vagy több MP4 kimeneti fájlt a munkafolyamathoz.

Győződjön meg arról, hogy a videó kódolók ugyanazokkal a beállításokkal létrehozott kell, hogy célszerű a leginkább a már meglévő AVC Videókódoló duplikált, és egy másik kombinációja megoldás és (adjuk hozzá 960 x 540 egyikét: 25 képkockák másodpercenkénti 2,5 Mbit/s átviteli sebesség konfigurálása ). A meglévő kódoló duplikálásához másolási illessze be a Tervező felületére.

Az új AVC összetevőt a tömörítetlen videó kimeneti PIN-kódot, a Media fájl bemeneti kapcsolódnak.

![Második AVC kódoló csatlakoztatva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Második AVC kódoló csatlakoztatva*

Most alkalmazkodnak az új AVC kódoló 960 x 540 2,5 Mbit/s kimeneti konfigurációját. (Használható tulajdonságok "kimeneti szélességének", "Kimeneti magasság" és "Átviteli sebesség (KB/s)" a ez.)

Megadott szeretnénk az eredményül kapott eszköz Azure Media Services dinamikus becsomagolást együtt használja, a streamvégpontján kell lennie, pontosan igazított egymáshoz oly módon, hogy az ügyfelek, amelyek között különböző bitrates vált le a egy egyetlen zökkenőmentes folyamatos videó és a hang élmény HLS/Fragmented MP4/DASH-töredék MP4 fájlokat képesek. Ellenőrizze, hogy fordulhat elő, győződjön meg arról, hogy mindkét AVC kódolók a GOP ("csoport képek") tulajdonságainak mindkét MP4-fájlok mérete értéke 2 másodperc, amely végezhető el kell:

* a GOP mérete mód beállítása rögzített GOP méretével és
* a kulcs keret időköz két másodpercre.
* Állítson be úgy az összes GOPs biztosításához lezárt GOP GOP IDR vezérlő vannak állandó nélkül saját függőségekkel

Ellenőrizze a munkafolyamat könnyebben érthetőek legyenek, nevezze át az első AVC kódoló a "AVC videó kódoló 640 x 360 1200-as kbit/s" és a második AVC kódoló "AVC videó kódoló 960 x 540 2500 kbps."

Ezután adja hozzá a második ISO MPEG-4 Multiplexer és egy második fájl kimenet. Az új AVC kódoló a multiplexer csatlakozik, és győződjön meg arról, annak kimenetét a kimeneti fájl be van átirányítva. Ezután is csatlakoznak a AAC hang kódolás kimenete az új multiplexer tartozó bemeneti. A kimeneti fájl pedig majd csatlakozhat veheti fel a Media Services eszköz, amely létrehozza a kimeneti fájl/eszköz csomópontra.

![Második Muxer és a kimeneti fájl csatlakoztatva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Második Muxer és a kimeneti fájl csatlakoztatva*

Az Azure Media Services dinamikus becsomagolást is kompatibilisek a multiplexer adatrészlet módnál GOP count vagy időtartama, és állítsa 1-adattömbök száma GOPs. (Ez az alapértelmezett kell.)

![Muxer adatrészlet módok](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer adatrészlet módok*

Megjegyzés: Előfordulhat, hogy szeretné bármely más átviteli sebesség és a feloldási kombináció hozzá kell adni az eszköz kimeneti kívánt ismételje meg ezt a folyamatot.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>A kimeneti fájl nevének konfigurálása
A kimeneti adategységen hozzá egynél több egyetlen fájl van. Ez lehetővé teszi a érdekében gondoskodjon arról, hogy a kimeneti fájlok minden egyes fájlneveket egymástól különböző, és lehet, hogy még érvényes a fájl-elnevezési konvenció így világossá válik, a fájl neve a most foglalkozó.

Kimeneti fájlelnevezésnél szabályozható kifejezések a tervezőben. Nyissa meg a kimeneti fájl összetevői a tulajdonság ablaktáblában, és nyissa meg a fájl tulajdonság kifejezés-szerkesztőt. Az első kimeneti fájl a következő kifejezés használatával lett konfigurálva (lásd az útmutató át [az egyszeres sávszélességű MP4 kimeneti MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Ez azt jelenti, hogy a fájlnév határozza meg két változót: írni kimeneti könyvtár és a forrás-fájlnevet. A korábbi tesz elérhetővé a munkafolyamat legfelső szintű tulajdonságaként, és a bejövő fájl alapján. A kimeneti könyvtár nem helyi tesztelési; használja Ez a tulajdonság felülbírálja a munkafolyamat-motor a munkafolyamat a felhő alapú adathordozót feldolgozó Azure Media Services eljárás végrehajtásakor.
A kimeneti fájlok elnevezési konzisztens kimeneti, biztosítva kifejezés első fájlelnevezési módosítása:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

és a másik pedig:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Hajtsa végre egy köztes teszt futtatásával győződjön meg arról, hogy mindkét MP4 kimeneti fájlok megfelelően jönnek létre.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Egy külön lejátszása hozzáadása
Megtanulhatja, később azt egy .ism-fájlt az MP4 kimeneti fájlok nal generálásakor, mivel azt is igényel csak MP4-fájlokat, a hang nyomon követése az adaptív streameléshez. A fájl létrehozásához, egy további muxer hozzá a munkafolyamathoz (Multiplexer ISO-MPEG-4), és csatlakozzon a AAC kódoló kimenő pin a bemeneti PIN-kód követése 1.

![Hang Muxer hozzáadva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Hang Muxer hozzáadva*

Hozzon létre egy harmadik kimeneti fájl összetevő a kimenő adatfolyam a muxer a kimeneti, és konfigurálja a fájlelnevezési kifejezés:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Hang Muxer kimeneti fájl létrehozása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Hang Muxer kimeneti fájl létrehozása*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Hozzáadás a. ISM SMIL fájl
A dinamikus csomagolás működjön együtt MP4 fájlokat (és a csak MP4) a Media Services eszközt is el kell jegyzékfájlt (más néven "SMIL" fájlba: multimédia integrációs nyelvi szinkronizálva). Ez a fájl meghatározza, hogy az Azure Media Services milyen MP4-fájlok dinamikus becsomagolás és azokat figyelembe kell venni a hang folyamatos közül melyik érhető el. Egy tipikus jegyzékfájl állítja be a MP4 meg egyetlen hangadatfolyam a következőképpen néz ki:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

A .ism-fájlt egy switch utasításban, egy hivatkozást az egyes mellett ezeket is egy (vagy több) hangfájl hivatkozik egy MP4 csak tartalmaz a hang és az egyes videokártya MP4-fájlokat tartalmazza.

A jegyzékfájl MP4 meg az adott vonatkozóan létrehozása végezhető el a "AMS jegyzékfájl írta." nevű összetevő A használatához húzza azt a felületet, és kapcsolódjon a három kimeneti fájl összetevői az "Írás kész" kimeneti PIN-kód AMS Manifest író bemenetet. Ezután ellenőrizze, hogy csatlakozzon az AMS-Manifest író kimenetét a kimeneti fájl vagy eszköz számára.

A többi fájl kimeneti összetevők konfigurálását a .ism kimeneti fájlnév kifejezés:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

A befejezett munkafolyamat néz az alábbi:

![Befejezett MXF multibitrate MP4-munkafolyamathoz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Befejezett MXF multibitrate MP4-munkafolyamathoz*

## <a id="MXF_to__multibitrate_MP4"></a>Kódolási MXF multibitrate MP4 - továbbfejlesztett tervezetének be
Az a [előző munkafolyamat forgatókönyv](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) azt láthatta, hogyan MXF egyetlen bemeneti eszköz létrehozható egy kimeneti eszköz többszörös sávszélességű MP4-fájlok, a csak MP4-fájlokat és a jegyzékfájlt, az Azure Media együtt használható Services dinamikus becsomagolást.

Ez a bemutató ismerteti, hogyan néhány azokat a vonatkozásait melyek fejlesztése és kényelmesebb végzett.

### <a id="MXF_to_multibitrate_MP4_overview"></a>Javítása érdekében a munkafolyamat áttekintése
![Multibitrate MP4 munkafolyamat javítása érdekében](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Multibitrate MP4 munkafolyamat javítása érdekében*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>A fájlok elnevezési konvenciók
Az előző munkafolyamatban kimeneti fájl nevének létrehozásának alapjául szolgáló azt meg egyetlen egyszerű kifejezésbe. Néhány duplikálva lettek-e, ha van: az egyes kimeneti fájl összetevőket megadott ilyen kifejezés.

Például a fájl kimeneti összetevőjének az első videofájl ebben a kifejezésben van konfigurálva:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

A második kimeneti a videót, például a kifejezés vezetünk be:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Lenne kevésbé vannak téve, és kényelmesebbé teszik hiba tisztító Ha igazolnia sikerült távolítson el néhányat a duplikálva lettek-e, és dolgot több konfigurálható helyette? Szerencsére alábbiakat tehetjük: a Tervező kifejezés képességek képes létrehozni az egyéni tulajdonságok a munkafolyamat legfelső szintű együtt fog kényelmi hozzáadott rétegeket biztosítson.

Tegyük fel, azt fogja az egyes MP4-fájlok bitrates a fájlnév konfigurációs meghajtó. Ezek bitrates igyekszünk fog konfigurálni egy központi helyen vannak (a legfelső szintű a grafikon), ahol azok lesz hozzáférhető konfigurálása és a meghajtó fájlnév létrehozása. Ehhez először a sávszélességű tulajdonság mindkét AVC kódolók a, a munkafolyamat legfelső szintű közzétételével, hogy maga az AVC kódolók mindkét a legfelső szintű is elérhető lesz. (Még akkor is, ha két különböző tesztüzeméhez jelenik meg, nincs csak egy alapul szolgáló érték.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>A munkafolyamat legfelső szintű alakzatot közzétételi összetevő tulajdonságai
Nyissa meg az első AVC kódoló nyissa meg az átviteli sebesség (KB/s) tulajdonság és a legördülő listából válassza ki a közzététel.

![Az átviteli sebesség tulajdonság közzététele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Az átviteli sebesség tulajdonság közzététele*

A közzététel a, a munkafolyamat gráf legfelső szintű közzététele párbeszédpanelen állítson be egy közzétett neve "video1bitrate" és "Videó 1 sávszélességű" olvasható megjelenítendő neve. Konfigurálja az egyéni csoport neve "Streaming Bitrates" néven, majd nyomja le a közzététel.

![Az átviteli sebesség tulajdonság közzététele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Átviteli sebesség tulajdonság közzétételi párbeszédpanel*

Ismételje meg a ugyanaz a második AVC kódoló sávszélességű tulajdonságához, és nevezze el az azonos egyéni csoport "Streaming Bitrates" megjelenítendő nevű "Videó 2 sávszélességű", "video2bitrate".

Ha azt most vizsgálhatja meg a munkafolyamat legfelső szintű tulajdonságait, megtanulhatja az egyéni csoportban található, a két közzétett tulajdonság jelenik meg. Mindkét van tükrözve a megfelelő AVC kódoló sávszélességű értékét.

![A munkafolyamat legfelső szintű közzétett sávszélességű tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Amikor azt szeretné elérni ezeket a tulajdonságokat kódot vagy egy kifejezés, azt is megteheti ehhez hasonló:

* a beágyazott kód egy összetevő a gyökér alatti jobb oldali: node.getPropertyAsString('.. / video1bitrate ", null)
* kifejezésben: ${ROOT_video1bitrate}

A "Folyamatos átviteli Bitrates" csoport befejezéseként közzététele a zenei sávszélességű rajta is. A AAC kódoló tulajdonságain belül keresse meg a sávszélességű beállítást, és látható a legördülő listából válassza ki a közzététel. A gyökér neve "audio1bitrate" a gráf közzététele, és megjelenítendő név "Hang 1 sávszélességű" a "Folyamatos átviteli Bitrates" egyéni csoporton belül.

![A hang sávszélességű közzétételi párbeszédpanel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*A hang sávszélességű közzétételi párbeszédpanel*

![A gyökérszintű eredményül kapott video- és tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*A gyökérszintű eredményül kapott video- és tulajdonságai*

Ezen három megváltoztatásával értékeket is Átkonfigurálás, és megváltoztatja az értékeket a megfelelő összetevők vannak összekapcsolva (és egyes esetekben közzétett).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Kimeneti fájl nevét közzétett tulajdonságértékek támaszkodnak hozta létre.
Hardcoding helyett a létrehozott fájl nevének azt mostantól megváltoztatható a fájlnév kifejezést a kimeneti fájl összetevői használják a azt a gráf legfelső szintű közzétett sávszélességű tulajdonságait. Az első kimeneti fájl verziótól kezdődően található a fájl tulajdonság, és a Szerkesztés ehhez hasonló:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

A különböző paraméterek ebben a kifejezésben érhető el, és adja meg a kifejezés ablakban a billentyűzeten a dollárjel elérte-e. A rendelkezésre álló paraméterek egyike fel azt a korábban közzétett video1bitrate tulajdonság.

![Paraméterek kifejezésben elérése](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Paraméterek kifejezésben elérése*

Tegye meg ugyanezt a fájl kimenetét a második videót:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

és csak hang-fájl kimenetének:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Most módosítjuk az átviteli sebesség a video- vagy fájlokat, ha a megfelelő kódoló újrakonfigurálni, és az átviteli sebesség alapú fájl neve egyezmény szerződéses kötelezettségeket összes automatikus.

## <a id="thumbnails_to__multibitrate_MP4"></a>Miniatűrök multibitrate MP4 kimenet hozzáadása
Állít elő, munkafolyamat-től kezdődő [egy multibitrate MP4 kimenete egy bemeneti MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), azt most kell keresése a miniatűrök hozzáadása a kimenetet.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Miniatűrök hozzáadandó munkafolyamat áttekintése
![Kiinduló Multibitrate MP4-munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Kiinduló Multibitrate MP4-munkafolyamat*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>JPG kódolás hozzáadása
A miniatűr generációs lelke fogja tudni kimeneti JPG fájlok JPG kódoló összetevő.

![JPG kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG Encoder*

Nem azonban közvetlenül kapcsolódni a tömörítetlen Video-adatfolyamot Media fájl bemeneti azokat a JPG kódoló. Ehelyett várhatóan egyes keretek kell átadni. Ez a videó keret kapu összetevő segítségével végezzük.

![A keret kapu csatlakozik a JPG kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*A keret kapu csatlakozik a JPG kódoló*

A keret kapu egyszer sok másodpercenként vagy keretek lehetővé teszi, hogy képkockát adja át. Az időköz, amely Ez akkor fordul elő időeltolódás pedig tulajdonságaiban konfigurálható.

![Videó keret kapu tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Videó keret kapu tulajdonságai*

Hozzon létre egy miniatűr percenként idő (másodperc) és az időtartamot 60 a mód beállításával.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>A szín terület átalakítás foglalkozó
Logikai mindkét tömörítetlen videó PIN-kódok a keret kapu és a Media fájl bemeneti most csatlakozhat tűnik, amíg azt visszajelzést kap a figyelmeztetést, ha azt szeretné ehhez.

![Bemeneti szín helyének hibája](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Bemeneti szín helyének hibája*

Ennek oka az, mely színnel információk jelennek meg az eredeti nyers tömörítetlen video-adatfolyammá alakítja, a MXF érkező módja eltér a Mi a JPG kódoló által várt paraméterekkel. Pontosabban egy úgynevezett "szín lemezterület" a "RGB" vagy "Szürkeárnyalatos" várt rendszer. Ez azt jelenti, hogy a videó keret kapu bejövő video-adatfolyamot alkalmazza a szín terület vonatkozó először konverzió rendelkeznie kell.

Húzza a munkafolyamat a szín terület konverter - Intel, és csatlakoztassa a keret kapu.

![Csatlakozás egy szín terület konverter](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Csatlakozás egy szín terület konverter*

A Tulajdonságok ablakban válassza ki a BGR 24 bejegyzést, az előre definiált listából.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>A miniatűrök írása
A MP4 videó eltér a JPG kódoló összetevő kimenete egynél több fájlt. Ahhoz, hogy megmaradjon, használhatja a leképezni kívánt jelenetben keresési JPG fájl író összetevője: a bejövő JPG miniatűrök vesz igénybe, és írja őket, minden egyes eltérő számú által éppen utótaggal fájlnév. (A azonosítószámát általában a másodperc/egységek számát, amely a miniatűr megrajzolása az adatfolyamban.)

![A leképezni kívánt jelenetben keresési JPG fájl író bemutatása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*A leképezni kívánt jelenetben keresési JPG fájl író bemutatása*

A kimeneti mappa elérési útja tulajdonság beállítása a következő kifejezéssel: ${ROOT_outputWriteDirectory}

és a fájlnév előtag tulajdonság:

    ${ROOT_sourceFileBaseName}_thumb_

Az előtag határozza meg, hogyan a miniatűr fájlok neve alatt. Azok a görgetőgomb pozíciót az adatfolyamban azonosítószámát a rendszer utótaggal.

![Megjelenítés keresési JPG fájl író tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Megjelenítés keresési JPG fájl író tulajdonságai*

A kimeneti fájl/eszköz csomópont a leképezni kívánt jelenetben keresési JPG fájl író csatlakozni.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>A munkafolyamat hibáinak észleléséhez
A szín terület konverter a bemenetben csatlakozni a nyers tömörítetlen videokimenetéhez. Egy tesztcélú helyi futtatásához a munkafolyamat végrehajtható. A munkafolyamat hirtelen leállítja a végrehajtása és a vizsgált összetevő hibába ütközött egy piros vázlatot jelzi esély van:

![Szín terület konverter hiba](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Szín terület konverter hiba*

Kattintson a jobb felső sarokban, mi az az oka a kódolási kísérlet megtekintéséhez a szín terület konverter összetevő sarkában sikertelen kevés piros "E" ikonra.

![Szín terület konverter hiba-párbeszédpanelen.](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Szín terület konverter hiba-párbeszédpanelen.*

Változik, ahogy látja, hogy rendelkezik-e a bejövő szín terület szabványt állít fel a szín terület konverter kell lennie a kért átalakításához YUV RGB-rec601. Az adatfolyam látszólag nem jelzi a rec601. (Jav. 601 a digitális videót formában váltakozó analóg videó jelek kódolási szabványos. Azt adja meg az aktív terület 720 fénysűrűség mintákat és 360 chrominance minták soronként. A rendszer kódolás szín YCbCr 4 néven: 2:2.)

A javítás érdekében azt fogja jelzi az adatfolyam, amely jelenleg éppen foglalkozó rec601 tartalom metaadatainak. Ehhez a videó adatok típusa Frissítőjének összetevő, amelyeket igazolnia kell a Between a nyers forrás- és a szín terület átalakítás összetevőjének fogjuk használni. Ezen adatok típusa frissítőjének lehetővé teszi, hogy az egyes videokártya adatok manuális frissítése típus tulajdonságai. Jelzi a szín terület szabványos a "Rec 601" konfigurálja. Ennek hatására a videó adatok típusa megtekinti a címkézésére "Rec 601" szín területtel rendelkező adatfolyam, ha nincs meghatározva szín terület történt. (Ez nem bírálja felül a meglévő metaadatokat, hacsak a felülbírálás jelölőnégyzetet, de.)

![Szín terület Standard megtekinti a adatok típusa a frissítése](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Szín terület Standard megtekinti a adatok típusa a frissítése*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>Befejezett munkafolyamat
Most, hogy a munkafolyamat befejeződött, hajtsa végre egy másik teszt haladnak megjelenítéséhez futtassa.

![Befejezett munkafolyamat vázlattal multi-mp4-kimenet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Befejezett munkafolyamat vázlattal multi-mp4-kimenet*

## <a id="time_based_trim"></a>Tisztítás időalapú multibitrate MP4 kimenet
Amely hoz létre egy munkafolyamat-től kezdődő [egy multibitrate MP4 kimenete egy bemeneti MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), azt most kell keresése a forrás videó időbélyegeket alapján díszítésre be.

### <a id="time_based_trim_start"></a>Hozzáadni a tisztítás, a munkafolyamat áttekintése
![Adja hozzá a tisztítás kezdő munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Adja hozzá a tisztítás kezdő munkafolyamat*

### <a id="time_based_trim_use_stream_trimmer"></a>Az adatfolyam vágó használatával
Az adatfolyam vágó összetevő vághatja a kezdő és egy alap mérve az időt (másodpercben, perc,...) adatokat a bemeneti adatfolyam befejezése teszi lehetővé. A vágó nem támogatja a keret-alapú tisztítás.

![Az adatfolyam vágó](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Az adatfolyam vágó*

Helyett, ha a AVC kódolók és hangalapú pozíció assigner Media fájl bemeneti közvetlenül, azt fogja put Between azokat az adatfolyam vágó. (Egy a videó jel, egy másik pedig a kihagyásos hang jelet.)

![Az adatfolyam vágó helyezze a kettő között](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Az adatfolyam vágó helyezze a kettő között*

A vágó pedig konfiguráljuk az, hogy a videó és hang 15 másodperc és a videó 60 másodperc között csak azt fogja feldolgozni.

Nyissa meg a Video-adatfolyamot vágó tulajdonságait, és mindkét kezdete konfigurálása (15 s) és befejezési időpontja (60 s) tulajdonságait. Annak biztosításához, mind a hang- és vágó mindig vannak konfigurálva a megegyező kezdő és záró értékek, közzétesszük azokat a legfelső szintű a munkafolyamat.

![A kezdési idő tulajdonságot adatfolyam vágó közzététele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*A kezdési idő tulajdonságot adatfolyam vágó közzététele*

![A kezdő időpont tulajdonság párbeszédpanel közzététele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*A kezdő időpont tulajdonság párbeszédpanel közzététele*

![Tulajdonság párbeszédpanel közzé a befejezési időpontja](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Tulajdonság párbeszédpanel közzé a befejezési időpontja*

Azt a munkafolyamat gyökérmappájában most vizsgálja meg, ha mindkét tulajdonságok egyszerű jelennek meg és konfigurálható onnan.

![A gyökérszintű elérhető közzétett tulajdonságok](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*A gyökérszintű elérhető közzétett tulajdonságok*

Most nyissa meg a tisztítás tulajdonságokat a hang vágó, és konfigurálja a kezdő és záró időpontjának a közzétett tulajdonságai a munkafolyamat legfelső szintű hivatkozó kifejezést.

A hang levágási kezdési idő:

    ${ROOT_TrimmingStartTime}

és a befejezési ideje:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>Befejezett munkafolyamat
![Befejezett munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Befejezett munkafolyamat*

## <a id="scripting"></a>A parancsprogram-alapú összetevők bemutatása
Parancsprogram-alapú összetevők tetszőleges parancsfájlokat futtathat a munkafolyamat végrehajtásának fázisában. Négy különböző parancsprogramok hajt végre, az adott jellemzőit és a saját helyét, a munkafolyamat-életciklusának:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

A dokumentáció a parancsprogram-összetevő kerül részletesebben minden, a fentiek közül. A [a következő szakasz](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), a **realizeScript** parancsfájl-kezelési összetevő összeállításához egy cliplist xml menet közben a munkafolyamat indításakor szolgál. A parancsprogram neve csak egyszer történik, életciklus összetevő telepítése során.

### <a id="scripting_hello_world"></a>A munkafolyamaton belül Scripting: hello world
Egy parancsprogram összetevő húzza a Tervező felületére, és adjon neki (például "SetClipListXML").

![A parancsfájlalapú összetevő hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*A parancsfájlalapú összetevő hozzáadása*

A parancsprogram összetevő tulajdonságainak vizsgálja meg, ha a különböző parancsfájl négyféle lesz látható, minden egyes konfigurálható különböző parancsfájlba.

![A parancsfájlalapú összetevő tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*A parancsfájlalapú összetevő tulajdonságai*

Törölje a processInputScript, és nyissa meg a realizeScript szerkesztője. Most azt még beállítás és felkészülés a parancsfájlkezelés megkezdéséhez.

Parancsfájlok Groovy, egy dinamikusan lefordított programozási nyelv a Java platform, amely megőrzi a kompatibilitást a Java nyelven íródtak. A legtöbb Java-kóddal ténylegesen, érvényes Groovy kód.

Ideje lefuttatni egy egyszerű hello world groovy parancsfájl a realizeScript környezetében. A szerkesztőben írja be a következőket:

    node.log("hello world");

Egy tesztcélú helyi Futtatás most végrehajtani. Ehhez a futtató után vizsgálja meg (a parancsprogram-összetevőjének rendszer lap) a naplók tulajdonság.

![Hello world kimenet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hello world kimenet*

A csomópont-objektum, a napló metódus hívása az aktuális "csomópont" vagy az összetevőt, azt hogy scripting belül hivatkozik. Minden összetevő használja, így képes a kimeneti naplózási adatokat, a rendszer lapon keresztül érhető el. Ebben az esetben azt kimeneti a szövegkonstans "hello world". Fontos tudni, hogy ez hasznos információt hibakeresési eszközt, a parancsfájl ténylegesen tevékenységétől ismereteket elérhető bizonyítja ez.

A belül a parancsfájl-kezelési környezet, azt is hozzáférhetnek a tulajdonságok más összetevőket. Próbálkozzon a következővel:

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

A napló ablakban a következő látható:

![Napló kimeneti elérési útjának eléréséhez](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Napló kimeneti elérési útjának eléréséhez*

## <a id="frame_based_trim"></a>Keret-alapú levágási multibitrate MP4 kimenet
Amely hoz létre egy munkafolyamat-től kezdődő [egy multibitrate MP4 kimenete egy bemeneti MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), azt most kell keresése a keret érintett alapján forrás videó díszítésre be.

### <a id="frame_based_trim_start"></a>Tisztítás való felvételéhez tervezetének áttekintése
![Hozzáadni a tisztítás, a munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Hozzáadni a tisztítás, a munkafolyamat*

### <a id="frame_based_trim_clip_list"></a>Az XML klip listáról
Az összes korábbi munkafolyamat oktatóanyag a bemeneti videoforrást az adathordozó fájl bemeneti összetevő használja azt. Ebben a forgatókönyvben azonban fogjuk használni a klip forráslista összetevő helyette. Ne legyen az előnyben részesített módja működő; csak akkor alkalmazza a klip forráslista, ha egy valódi ok arra, hogy ehhez (a következő esetében, ahol azt hajt végre, például a klip lista levágási képességek használatát).

Ha az adathordozó fájl bemenet a klip forráslista a, a klip forráslista összetevő húzza a tervezési felülethez, és a klip lista XML PIN-kód csatlakozzon a munkafolyamat-Tervező klip lista XML-csomópont. Ez a klip forráslista a kimeneti PIN-kód, a bemeneti videóhoz megfelelően tölti fel. Most csatlakoztassa a tömörítetlen videó és tömörítetlen hang PIN-kódok a klip lista forrásból származó megfelelő AVC kódolók és hang adatfolyam Interleaver. A Media fájl bemeneti eltávolítása.

![A Media fájl bemeneti cseréli a klip forráslista](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*A Media fájl bemeneti cseréli a klip forráslista*

A klip forráslista összetevő fogadja a bemeneti adatként "Klip listáját XML." A forrásfájl helyileg tesztelni kiválasztásakor a a klip lista XML-kódja, automatikusan feltölti az Ön.

![Automatikus feltöltve klip lista XML-tulajdonság](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automatikus feltöltve klip lista XML-tulajdonság*

Keresése egy kicsit közelebb az XML-fájl, ez az hogyan hasonlóan néz ki:

![Szerkesztése klip lista párbeszédpanel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Szerkesztése klip lista párbeszédpanel*

Ez azonban nem tükrözi a klip lista xml képességeit. Egy lehetőség van egy vegyen fel egy "Vágás" mindkét a video- és a forrás területen ehhez hasonló:

![A vágás elem felvétele a klip listájára](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*A vágás elem felvétele a klip listájára*

Ha módosítja a klip lista xml ilyen felett, és helyi ellenőrzéséhez futtassa, láthatja a videó megfelelően lett levágja a videó 10 és 20 másodperc között.

Mi történik, ha a helyi Futtatás tegye, ellentétesen az azonos cliplist xml nem kellene ugyanaz az eredmény az Azure Media Services futó munkafolyamatot alkalmazásakor. Azure prémium szintű kódolás indításakor az cliplist XML-fájl jön létre minden alkalommal, amikor újra, a kódolási feladat adtak meg a bemeneti fájl alapján. Ez azt jelenti, hogy a módosításokat az XML-végezzük volna sajnos bírálható felül.

A számláló az egy kódolási feladat indításakor adatainak törlése cliplist xml, azt is ismét menet közben a munkafolyamat elindítása után. Ilyen egyéni műveletek is lehet képernyőfelvételt készíteni, keresztül úgynevezett "Parancsfájlalapú Component." További információkért lásd: [vezet be, a parancsprogram összetevő](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Húzzon egy parancsprogram-összetevő a Tervező felületére, és nevezze át a "SetClipListXML."

![A parancsfájlalapú összetevő hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*A parancsfájlalapú összetevő hozzáadása*

A parancsprogram összetevő tulajdonságainak vizsgálja meg, amikor a négy különböző parancsfájl típusok a következők látható, minden egyes konfigurálható különböző parancsfájlba.

![A parancsfájlalapú összetevő tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*A parancsfájlalapú összetevő tulajdonságai*

### <a id="frame_based_trim_modify_clip_list"></a>Egy parancsprogram összetevő klip listájának módosítása
Azt írni a munkafolyamat indítása során létrehozott cliplist xml, igazolnia kell kell a cliplist XML-tulajdonság és a tartalmak eléréséhez. Ehhez hasonló azt is megteheti:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![A naplózott bejövő klip listája](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*A naplózott bejövő klip listája*

Először tudja határozni, hogy mely pontról amikor csak lehet levágni a videó szeretnénk kell. Legyen Ez kényelmes kevésbé-technikai felhasználóját, hogy a munkafolyamat, tegye közzé a gráf legfelső szintű két tulajdonság. Ehhez kattintson a jobb gombbal a Tervező felületére, és válassza ki a "Tulajdonság hozzáadása":

* Első tulajdonság: "ClippingTimeStart" típusú: "IDŐKÓD"
* A második tulajdonság: "ClippingTimeEnd" típusú: "IDŐKÓD"

![Hozzáadása tulajdonsághoz párbeszédpanel Kivágás kezdési ideje](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Hozzáadása tulajdonsághoz párbeszédpanel Kivágás kezdési ideje*

![Közzétett a munkafolyamat legfelső szintű idő tulajdonságai Kivágás](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Közzétett a munkafolyamat legfelső szintű idő tulajdonságai Kivágás*

Konfigurálja a megfelelő értéket tulajdonságot is:

![Konfigurálása a Kivágás kezdő és befejező tulajdonságok](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurálása a Kivágás kezdő és befejező tulajdonságok*

Most a belül a parancsfájl azt férhetnek hozzá mindkét tulajdonságok ehhez hasonló:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Kezdő és záró a Kivágás tartalmazó napló ablak](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Kezdő és záró a Kivágás tartalmazó napló ablak*

Most értelmezhető a időkód karakterláncok egy kényelmesebb egyszerű reguláris kifejezést használ, az űrlapot használhatja:

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![Napló ablakban az elemzett időkód kimenete](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Napló ablakban az elemzett időkód kimenete*

Az információ az elvégzendő azt mostantól módosíthatják a kezdő és záró időpontjának a film a kívánt keret pontos Kivágás megfelelően cliplist xml.

![Parancsfájlkód vágás elemek hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Parancsfájlkód vágás elemek hozzáadása*

Ez normál karakterlánc fájlkezelési műveleteket keresztül végezhető el. Az eredményül kapott módosított klip lista xml van visszaírását a clipListXML tulajdonság a munkafolyamat legfelső szintű "setProperty" metódusával. A napló ablak egy másik teszt futtatása után szeretné jeleníti meg a következő us:

![Naplózási a klip listájában](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Naplózási a klip listájában*

Teheti meg, hogy hogyan lettek a video- és adatfolyamok levágva vizsgálat. Módon teheti meg egynél több vizsgálat levágási pontok különböző értékekkel, láthatja, hogy a rendszer nem figyelembe kell venni azonban! Ennek oka az, hogy a Tervező, az Azure futásidejű eltérően nem bírálja felül a cliplist xml minden futtatás. Ez azt jelenti, hogy csak az első alkalommal van beállítva, az a és pontokon, ki, akkor az XML-átalakítási és minden egyéb esetben a őr záradék (Ha (clipListXML.indexOf ("<trim>") == -1)) megakadályozza, hogy a munkafolyamat egy másik vágás elem felvétele esetén már létezik egy.

Ahhoz, hogy a munkafolyamat tesztelése helyileg kényelmes, a Microsoft ajánlott adja hozzá néhány house-karbantartási kódot, amely megvizsgálja, ha a vágás elem már található. Ha igen, azt is távolítsa el az új értékekkel xml módosításával a folytatás előtt. Ahelyett, hogy az egyszerű karakterlánc-feldolgozás, akkor valószínűleg biztonságosabb ehhez keresztül valós xml elemzése.

Ahhoz azonban vehessen fel ilyen kód, kell adni az importálási utasításban a parancsfájl elején először:

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

Ezt követően azt is vegye fel a szükséges tisztítási kódot:

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

Ez a kód kerül a pont, ahol azt a vágás elemek hozzáadása a cliplist xml felett.

Ezen a ponton azt futtathatja és módosíthatja a munkafolyamat, mint azt szeretnénk, ha során legalább egyszer a módosított rendelkező idő.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Egy ClippingEnabled kényelmi tulajdonság hozzáadása
Nem minden esetben érdemes díszítésre megtörténjen-e, mivel most Befejezés ki a munkafolyamat egy kényelmes logikai jelző, amely azt jelzi-e azt engedélyezni szeretné a díszítésre / Kivágás hozzáadásával.

Mint korábban tegye közzé a legfelső szintű a munkafolyamat "ClippingEnabled" nevű új tulajdonsággal "Logikai" típusúnak.

![Közzétett egy tulajdonság Kivágás engedélyezése](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Közzétett egy tulajdonság Kivágás engedélyezése*

Az az alábbi egyszerű őr záradék, azt is ellenőrizze, hogy a tisztítás szükség és döntse el, ha klip listáját használja, így kell vagy nem módosítható.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a id="code"></a>Teljes kód

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>Lásd még:
[Prémium szintű kódolás az Azure Media Services bemutatása](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Prémium szintű kódolás az Azure Media Services használatával](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Az Azure Media Services kódolási igény tartalom](media-services-encode-asset.md#media-encoder-premium-workflow)

[A Media Encoder Premium munkafolyamat formátumai és kodekei](media-services-premium-workflow-encoder-formats.md)

[Minta munkafolyamat-fájlok](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Explorer eszköz](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
