---
title: Speciális oktatóanyagok a Media Encoder Premium-munkafolyamathoz
description: Ez a dokumentum olyan bemutatókat tartalmaz, amelyek bemutatják, hogyan végezheti el a speciális feladatokat a Media Encoder Premium Workflow, valamint a Munkafolyamat-tervezővel összetett munkafolyamatok létrehozását is.
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc
ms.reviewer: xpouyat; juliako
ms.openlocfilehash: 67d3591a22ba68c0ddb5c4e2b467e133ef20102b
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057466"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Speciális oktatóanyagok a Media Encoder Premium-munkafolyamathoz
## <a name="overview"></a>Áttekintés
Ez a dokumentum olyan forgatókönyveket tartalmaz, amelyek bemutatják, hogyan lehet testreszabni a munkafolyamatokat a **Munkafolyamat-tervezővel** [Itt](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples)megtalálhatja az aktuális munkafolyamat-fájlokat.  

## <a name="toc"></a>Tartalomjegyzék
A következő témákat tárgyalja:

* [MXF kódolása egyetlen bitráta MP4-be](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Új munkafolyamat indítása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [A médiafájl bemenetének használata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Média streamek vizsgálata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Videó-kódoló hozzáadása a következőhöz:. MP4-fájl létrehozása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Az audió stream kódolása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Audio-és video-adatfolyamok egy MP4-tárolóba](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [MP4-fájl írása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Media Services-eszköz létrehozása a kimeneti fájlból](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [A befejezett munkafolyamat helyi tesztelése](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [MXF kódolása többszörös sávszélességű MP4 – dinamikus csomagolás engedélyezve](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Egy vagy több további MP4-kimenet hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [A fájlok kimeneti nevének konfigurálása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Külön hangfelvétel hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Az "ISM" SMIL-fájl hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [MXF kódolása többszörös sávszélességű MP4-ben – továbbfejlesztett terv](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * A munkafolyamat áttekintése a fejlesztéshez
  * [Fájl elnevezési konvenciói](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Összetevő tulajdonságainak közzététele a munkafolyamat gyökerén](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [A létrehozott kimeneti fájlnevek a közzétett tulajdonságértékek alapján vannak felhasználva](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Miniatűrök hozzáadása több sávszélességű MP4-kimenethez](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * A munkafolyamatok áttekintése a miniatűrök hozzáadásához
  * [JPG-kódolás hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [A színtér átalakításának kezelése](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [A miniatűrök írása](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Hibák észlelése egy munkafolyamatban](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Befejezett munkafolyamat](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [A többszörös sávszélességű MP4 kimenet időalapú kivágása](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [A munkafolyamat áttekintése a vágás hozzáadásának megkezdéséhez](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [Az adatfolyam-formázó használata](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Befejezett munkafolyamat](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [A megírt összetevő bemutatása](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Parancsfájlok futtatása a munkafolyamaton belül: Hello World](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Többsebességű MP4-kimenet frame-alapú körülvágása](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [A terv áttekintésével megkezdheti a vágás hozzáadását a következőhöz](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [A klipek listájának XML-fájljának használata](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [A klipek listájának módosítása egy parancsfájlból álló összetevőből](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [ClippingEnabled kényelmi tulajdonság hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a name="encoding-mxf-into-a-single-bitrate-mp4"></a><a id="MXF_to_MP4"></a>MXF kódolása egyetlen bitráta MP4-be
Ez a szakasz bemutatja, hogyan hozhat létre egyetlen sávszélességet. MP4-fájl AAC-kódolt hanggal az-ból. MXF bemeneti fájl.

### <a name="starting-a-new-workflow"></a><a id="MXF_to_MP4_start_new"></a>Új munkafolyamat indítása
Nyissa meg a munkafolyamat-tervezőt, és válassza a fájl > új munkaterület lehetőséget > transcode Blueprint

Az új munkafolyamat három elemet mutat be:

* Elsődleges forrásfájl
* Klipek XML-listája
* Kimeneti fájl/eszköz  

![Új kódolási munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Új kódolási munkafolyamat*

### <a name="using-the-media-file-input"></a><a id="MXF_to_MP4_with_file_input"></a>A médiafájl bemenetének használata
A bemeneti médiafájl elfogadásához az egyik a Media file input összetevő hozzáadásával kezdődik. Ha hozzá szeretne adni egy összetevőt a munkafolyamathoz, keresse meg az adattár keresési mezőjében, és húzza a kívánt bejegyzést a tervező ablaktáblára. Ismételje meg a médiafájl-bevitel műveletét, és az elsődleges forrásfájl-összetevőt a fájl bemenetén található fájlnév bemeneti PIN-kódjához kapcsolja.

![Csatlakoztatott médiafájl bemenete](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Csatlakoztatott médiafájl bemenete*

Először azonosítson egy, az egyéni munkafolyamatok tervezésekor használni kívánt mintát. Ehhez kattintson a tervező ablaktábla hátterére, és keresse meg az elsődleges forrásfájl tulajdonságot a jobb oldali Tulajdonságok ablaktáblán. Kattintson a mappa ikonra, és válassza ki a kívánt fájlt a munkafolyamat teszteléséhez. A médiafájl-beviteli összetevő megvizsgálja a fájlt, és feltölti a kimeneti PIN-kód tartalmát, hogy tükrözze az általa vizsgált fájl részleteit.

![Feltöltött médiafájlok bemenete](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Feltöltött médiafájlok bemenete*

Most, hogy a bemenet fel van töltve, a következő lépés a kimeneti kódolási beállítások beállítása. Hasonlóan az elsődleges forrásfájl konfigurálásához, most konfigurálja a kimeneti mappa változó tulajdonságát, amely közvetlenül alatta van.

![Konfigurált bemeneti és kimeneti tulajdonságok](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Konfigurált bemeneti és kimeneti tulajdonságok*

### <a name="inspecting-media-streams"></a><a id="MXF_to_MP4_streams"></a>Média streamek vizsgálata
Gyakran szeretné tudni, hogyan néz ki az adatfolyam a munkafolyamaton keresztül. Ha a munkafolyamat bármely pontján meg szeretné vizsgálni a streamet, csak kattintson egy kimenetre vagy egy bemeneti PIN-kódra bármelyik összetevőn. Ebben az esetben próbálja meg a fájl bemenetéről kibontott videó kimeneti PIN-kódra kattintani. Megnyílik egy párbeszédpanel, amely lehetővé teszi a kimenő videó vizsgálatát.

![A kibontott videó kimeneti PIN-kódjának vizsgálata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*A kibontott videó kimeneti PIN-kódjának vizsgálata*

Ebben az esetben ez azt mutatja, hogy a videó tartalmaz egy 1920 × 1080-bemenetet 24 képkocka/másodpercben, 4:2:2-es mintavételezéssel egy majdnem 2 percet tartalmazó videóhoz.

### <a name="adding-a-video-encoder-for-mp4-file-generation"></a><a id="MXF_to_MP4_file_generation"></a>Videó-kódoló hozzáadása a következőhöz:. MP4-fájl létrehozása
Most egy tömörítetlen videó és több tömörítetlen hangkimeneti PIN-kód is használható a médiafájlok bemenetén. A bejövő videó kódolásához fel kell venni egy kódolási összetevőt a munkafolyamatba – ebben az esetben a létrehozáshoz. MP4-fájlok.

Ha a videó streamet H. 264-re szeretné kódolni, adja hozzá az AVC-videó kódoló összetevőt a tervező felületéhez. Ez az összetevő bemenetként tömöríti a videó streamet, és egy AVC-tömörített videót továbbít a kimeneti PIN-kódjában.

![Nem csatlakoztatott AVC-kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Nem csatlakoztatott AVC-kódoló*

A tulajdonságai határozzák meg, hogy a kódolás pontosan megtörténjen-e. Tekintsük át a fontosabb beállításokat:

* Kimeneti szélesség és kimeneti magasság: meghatározza a kódolt videó feloldását. Ebben az esetben a 640 x 360 jó beállítás.
* Képkockasebesség: Ha az átadó értékre van állítva, akkor a rendszer csak a forrás képkockasebességet fogadja el, ez azonban felülbírálható. Az ilyen frameráta-konverzió nem kompenzált.
* Profil és szint: meghatározza az AVC-profilt és-szintet. Ha további információt szeretne kapni a különböző szintekről és profilokról, kattintson az AVC-videó kódoló összetevő kérdőjel ikonjára, és a Súgó lapon további információk jelennek meg az egyes szintekről. Ehhez a példához használja a fő profilt a 3,2 szinten (ez az alapértelmezett érték).
* Sebesség-vezérlési mód és bitráta (Kbit/s): ebben a forgatókönyvben az állandó bitráta (CBR) kimenetét kell választani, 1200 kbps
* Videó formátuma: a H. 264 streambe írt VUI (videó használhatósági információkkal) kapcsolatos információkat jeleníti meg (a dekóder által a kijelzőhöz felhasználható, de a megfelelő dekódolásra nem feltétlenül használt oldal):
* NTSC (jellemzően az USA-ban vagy Japánban, 30 fps használatával)
* PAL (jellemző Európa számára, 25 fps használatával)
* GOP-os méret mód: állítsa be a rögzített GOP-méretet a felhasználási célokra egy 2 másodperces, zárt Pallagi Péter rendelkező kulcs intervallumával. A 2 másodperces beállítás biztosítja a kompatibilitást a dinamikus csomagolással Azure Media Services biztosít.

Az AVC-kódoló megadásához kapcsolja össze a tömörített videó kimeneti PIN-kódját a médiafájl bemeneti összetevőjéből az AVC-kódolóból származó tömörítetlen video bemeneti PIN-kódra.

![Csatlakoztatott AVC-kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Csatlakoztatott AVC fő kódoló*

### <a name="encoding-the-audio-stream"></a><a id="MXF_to_MP4_audio"></a>Az audió stream kódolása
Ezen a ponton az eredeti tömörítetlen hangadatfolyamot továbbra is tömöríteni kell. Az audio stream tömörítéséhez adjon hozzá egy AAC-kódoló (Dolby) összetevőt a munkafolyamathoz.

![Nem csatlakoztatott AVC-kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Nem csatlakoztatott AAC-kódoló*

Most már van egy inkompatibilitás: az AAC-kódolóból csak egyetlen tömörítetlen hangbemeneti PIN-kód van, míg a médiafájlok bemenete több mint valószínű, két különböző tömörítetlen hangstream érhető el: egy a bal oldali hangcsatornához, egy pedig a jobbhoz. (Ha a surround hangzással dolgozik, ez hat csatorna.) Így nem lehet közvetlenül a médiafájl bemeneti forrásáról az AAC hangkódolóba kapcsolni a hangot. Az AAC-összetevő egy úgynevezett "Interleaved" hangadatfolyamot vár: egyetlen streamet, amely a bal oldali és a jobb oldali csatornák egymással való összekapcsolása mellett van. Ha már tudjuk, hogy a forrásként szolgáló médiafájlból a hangsávok a forráson belül találhatók, akkor az összefoglalt hangadatfolyamot a helyesen hozzárendelt, bal és jobb oldali hangszórókhoz is létrehozhatjuk.

Először is szeretne egy összekapcsolt streamet készíteni a szükséges forrásból származó Hangcsatornák közül. A hangadatfolyam-kapcsolati adatátviteli összetevő kezeli ezt a számunkra. Adja hozzá a munkafolyamathoz, és kapcsolja össze az audio kimeneteket a médiafájl bemenetével.

![Csatlakoztatott audio stream-kapcsolat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Csatlakoztatott audio stream-kapcsolat*

Most, hogy egy összekapcsolt hangadatfolyammal rendelkezünk, még mindig nem adta meg, hogy hová rendelje a bal vagy a jobb oldali hangfal pozícióját. A beállítás megadásához kihasználhatja a beszélő pozíciójának kiosztását.

![Beszélő pozíciós hozzárendelés hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Beszélő pozíciós hozzárendelés hozzáadása*

Állítsa be a hangszóró pozíciójának kiosztását a sztereó bemeneti streamhez a "Custom" kódoló előre beállított szűrője és a "2,0 (L, R)" nevű csatorna-beállításkészlet használatával. (Ez a bal oldali hangszóró pozícióját rendeli az 1. csatornához, a jobb oldali hangfal pozícióját pedig a 2. csatornához.)

Kapcsolja össze a beszélő pozíciójának kimenetét az AAC-kódoló bemenetével. Ezt követően mondja el az AAC-kódolónak, hogy működjön együtt egy "2,0 (L, R)" csatornás beállításkészlettel, így a sztereó hang bemenetként is kezelhető.

### <a name="multiplexing-audio-and-video-streams-into-an-mp4-container"></a><a id="MXF_to_MP4_audio_and_fideo"></a>Audio-és video-adatfolyamok egy MP4-tárolóba
Az AVC-kódolású videó stream és az AAC-kódolású hangstream esetében is rögzítheti a-t. MP4-tároló. A különböző streamek egyetlenre való keverésének folyamatát "Multiplexing" (vagy "mux") néven kell megnevezni. Ebben az esetben a hang és a videó streamek egyetlen koherens módon vannak elhagyva. MP4-csomag. Az a összetevő, amely a következőt koordinálja. Az MP4-tárolót ISO MPEG-4 Multiplexernek nevezzük. Vegyen fel egyet a tervező felületre, és kapcsolja össze az AVC-videó Kódolóját és az AAC-kódolót a bemenetekhez.

![Csatlakoztatott MPEG4 multiplexer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Csatlakoztatott MPEG4 multiplexer*

### <a name="writing-the-mp4-file"></a><a id="MXF_to_MP4_writing_mp4"></a>MP4-fájl írása
Kimeneti fájl írásakor a rendszer a fájl kimeneti összetevőjét használja. Ezt az ISO MPEG-4 multiplexer kimenetéhez lehet kapcsolni, hogy a kimenet lemezre legyen írva. Ehhez kapcsolja a tároló (MPEG-4) kimeneti PIN-kódot a fájl kimenetének írási bemeneti PIN-kódjához.

![Csatlakoztatott fájl kimenete](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Csatlakoztatott fájl kimenete*

A használt fájlnevet a fájl tulajdonság határozza meg. Habár ez a tulajdonság egy adott értékre is hardcoded, valószínűleg egy kifejezésen keresztül szeretné beállítani azt.

Ha azt szeretné, hogy a munkafolyamat automatikusan meghatározza a kimeneti fájl neve tulajdonságot egy kifejezésből, kattintson a fájlnév melletti gombra (a mappa ikon mellett). A legördülő menüben válassza a "kifejezés" lehetőséget. Ez a kifejezés-szerkesztőt hozza létre. Először törölje a szerkesztő tartalmát.

![Üres kifejezés-szerkesztő](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Üres kifejezés-szerkesztő*

A kifejezés-szerkesztő lehetővé teszi bármely literál érték megadását egy vagy több változóval együtt. A változók a dollár előjelével kezdődnek. A $ Key megnyomása után a szerkesztő egy legördülő listát jelenít meg a választható változók közül. Ebben az esetben a kimeneti könyvtár változó és az alap bemeneti fájl neve változó kombinációját fogjuk használni:

`${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4`

![Kitöltött kifejezés-szerkesztő](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Kitöltött kifejezés-szerkesztő*

> [!NOTE]
> Ha szeretné megtekinteni a kódolási feladatokhoz tartozó kimeneti fájlt az Azure-ban, meg kell adnia egy értéket a kifejezés-szerkesztőben.
>
>

Ha a kifejezést az OK gomb megnyomásával erősíti meg, a tulajdonság ablakban megtekintheti, hogy a fájl tulajdonság milyen értéket oldja fel ebben az időpontban.

![A fájl kifejezése feloldja a kimeneti dir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*A fájl kifejezése feloldja a kimeneti dir*

### <a name="creating-a-media-services-asset-from-the-output-file"></a><a id="MXF_to_MP4_asset_from_output"></a>Media Services-eszköz létrehozása a kimeneti fájlból
Az MP4 kimeneti fájl írásakor továbbra is jelezni kell, hogy ez a fájl ahhoz a kimeneti eszközhöz tartozik, amelyet a Media Services a munkafolyamat végrehajtásának eredményeképpen generál. Ennek érdekében a rendszer a munkafolyamat-vászon kimeneti fájl/eszköz csomópontját használja. Az ebben a csomópontban lévő összes bejövő fájl az eredményül kapott Azure Media Services eszköz részét képezi.

A munkafolyamat befejezéséhez kapcsolja a kimeneti fájl/eszköz összetevőhöz a fájl kimeneti összetevőjét.

![Befejezett munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Befejezett munkafolyamat*

### <a name="test-the-finished-workflow-locally"></a><a id="MXF_to_MP4_test"></a>A befejezett munkafolyamat helyi tesztelése
A munkafolyamat helyi teszteléséhez nyomja le a lejátszás gombot a felső eszköztáron. A munkafolyamat végrehajtása után ellenőrizze a konfigurált kimeneti mappában létrehozott kimenetet. Ekkor megjelenik a MXF bemeneti forrásfájl-fájlból kódolt, befejezett MP4 kimeneti fájl.

## <a name="encoding-mxf-into-mp4---multibitrate-dynamic-packaging-enabled"></a><a id="MXF_to_MP4_with_dyn_packaging"></a>MXF kódolása MP4-ben – Többszörös sávszélességű dinamikus csomagolás engedélyezve
Ez az útmutató több bitráta MP4-fájl egy készletét hozza létre AAC kódolású hanggal egyetlen fájlból. MXF bemeneti fájl.

Ha a többszörös sávszélességű eszköz kimenetét a Azure Media Services által kínált dinamikus csomagolási funkciókkal kívánja használni, akkor a rendszer a különböző sávszélességű és megoldású, több GOP-igazítású MP4-fájlokat is létre kell hozni. Ehhez a [kódolási MXF egyetlen BITRÁTA MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) -bemutatóba kerül, amely jó kiindulási pontot biztosít nekünk.

![Munkafolyamat indítása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Munkafolyamat indítása*

### <a name="adding-one-or-more-additional-mp4-outputs"></a><a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Egy vagy több további MP4-kimenet hozzáadása
Az eredményül kapott Azure Media Services objektum minden MP4-fájlja más bitrátát és megoldást támogat. Vegyünk fel egy vagy több MP4 kimeneti fájlt a munkafolyamatba.

Annak ellenőrzéséhez, hogy minden azonos beállításokkal létrehozott videó-kódolónk elérhető-e, a már meglévő AVC-videó-kódoló duplikálása, valamint a megoldás és a 2,5 540 960 bitráta egy másik kombinációjának konfigurálása A meglévő kódoló másolásához másolja a vágólapra a tervező felületét.

A médiafájl bemenetének tömörítetlen videó kimeneti PIN-kódjának összekötése az új AVC-összetevőbe.

![Második AVC-kódoló csatlakoztatva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Második AVC-kódoló csatlakoztatva*

Most módosítsa az új AVC-kódoló konfigurációját a kimeneti 960x540 2,5 Mbps-on. (Használja a "kimeneti szélesség", "output height" és "bitráta (kbps)" tulajdonságot ehhez a értékhez.)

Mivel az eredményül kapott eszközt a Azure Media Services dinamikus csomagolással együtt szeretnénk használni, az adatfolyam-végpontnak képesnek kell lennie az ilyen MP4-fájlokból származó HLS/darabolt MP4/DASH töredékek létrehozására, amelyek pontosan illeszkednek egymáshoz úgy, hogy a különböző bitráták közötti váltást lehetővé tévő ügyfelek egyetlen zökkenőmentes folyamatos videó-és hangélményt kapjanak. Ennek elvégzéséhez biztosítania kell, hogy mindkét AVC-kódoló tulajdonságaiban a GOP ("képek csoportja") mérete 2 másodpercre van beállítva, amelyet a következők végezhetnek el:

* a GOP méret mód beállítása rögzített GOP-méretre és
* a kulcs keretének intervalluma két másodpercre.
* Állítsa be a GOP IDR vezérlőt a GOP-ra, hogy az összes Pallagi Péter függőség nélkül álljon

Ahhoz, hogy ez a munkafolyamat könnyebben érthető legyen, nevezze át az első AVC-kódolót az "AVC Video Encoder 640 x 360 1200 kbps" értékre, a második AVC-kódoló "AVC Video Encoder 960x540 2500 kbps"-re.

Most adjon hozzá egy második ISO MPEG-4 multiplexert és egy második kimeneti fájlt. Kapcsolja össze a multiplexert az új AVC-kódolóval, és győződjön meg róla, hogy a kimenete a fájl kimenetére van irányítva. Ezután az AAC Hangkódoló kimenetét az új multiplexer bemenetéhez is kapcsolja. Ezután a kimeneti fájl/eszköz csomóponthoz csatlakoztathatja a fájl kimenetét, és hozzáadhatja azt a létrehozandó Media Services eszközhöz.

![Második muxer-fájl és kimenet csatlakoztatva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Második muxer-fájl és kimenet csatlakoztatva*

Azure Media Services dinamikus csomagolással való kompatibilitáshoz konfigurálja a multiplexerek darabolási módját GOP-szám vagy időtartam értékre, és állítsa az Pallagi Péter-t 1-re. (Ez az alapértelmezett érték.)

![Muxer-adathalmazok módjai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer-adathalmazok módjai*

Megjegyzés: érdemes megismételni ezt a folyamatot bármilyen más bitráta és feloldási kombináció esetében, amelyet hozzá szeretne adni az eszköz kimenetéhez.

### <a name="configuring-the-file-output-names"></a><a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>A fájlok kimeneti nevének konfigurálása
Egynél több fájl van hozzáadva a kimeneti eszközhöz. Ehhez meg kell győződnie arról, hogy az egyes kimeneti fájlok fájlneve eltér egymástól, és lehet, hogy egy fájl-elnevezési konvenciót is alkalmaz, így világossá válik a fájl nevéből, amit a szolgáltatással kapcsolatban használ.

A fájl kimenetének elnevezése a tervezőben kifejezésekkel vezérelhető. Nyissa meg a tulajdonság ablaktáblát a kimeneti összetevők egyikéhez, és nyissa meg a fájl tulajdonsághoz tartozó kifejezés-szerkesztőt. Az első kimeneti fájl a következő kifejezésen keresztül lett konfigurálva (lásd az oktatóanyagot, amely a [MXF-ról egyetlen sávszélességű MP4-kimenetre mutat](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4`

Ez azt jelenti, hogy a fájlnevet két változó határozza meg: a beírandó kimeneti könyvtár és a forrásfájl alapneve. Az előző a munkafolyamat gyökerének egyik tulajdonsága, az utóbbit pedig a bejövő fájl határozza meg. A kimeneti könyvtár a helyi teszteléshez használatos. ezt a tulajdonságot a munkafolyamat-motor felülbírálja, ha a munkafolyamatot az Azure Media Services felhőalapú adathordozó-processzora hajtja végre.
Ahhoz, hogy mindkét kimeneti fájl konzisztens kimeneti nevet adjon, módosítsa az első fájl elnevezési kifejezését a következőre:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4`

a második a következő:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4`

Hozzon létre egy köztes tesztet, és győződjön meg arról, hogy az MP4 kimeneti fájlok megfelelően lettek létrehozva.

### <a name="adding-a-separate-audio-track"></a><a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Külön hangfelvétel hozzáadása
Ahogy később látni fogjuk, amikor létrehozunk egy. ISM-fájlt, amely az MP4-kimeneti fájlokkal együtt fog megjelenni, az adaptív adatfolyam hangvételéhez az MP4-fájlt is meg kell adni. A fájl létrehozásához vegyen fel egy további muxer-t a munkafolyamatba (ISO-MPEG-4 multiplexer), és kapcsolja össze az AAC-kódoló kimeneti PIN-kódját az 1. számú bemeneti PIN-kóddal.

![Hang muxer hozzáadva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Hang muxer hozzáadva*

Hozzon létre egy harmadik kimeneti összetevőt a kimenő adatfolyam a muxer-ből való kimenetéhez, és konfigurálja a fájl elnevezési kifejezést a következőként:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4`

![Audio muxer fájl kimenetének létrehozása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Audio muxer fájl kimenetének létrehozása*

### <a name="adding-the-ism-smil-file"></a><a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>A hozzáadása. ISM SMIL-fájl
Ahhoz, hogy a dinamikus csomagolás egyszerre működjön együtt az MP4-fájlokkal (és a csak hangalapú MP4-sel), a Media Services-adategységben is szükség van egy manifest-fájlra (más néven "SMIL"-fájlra is): szinkronizált multimédia-integrációs nyelv). Ez a fájl azt jelzi, hogy Azure Media Services, milyen MP4-fájlok érhetők el a dinamikus csomagoláshoz, és hogy ezek közül melyeket érdemes figyelembe venni a hangadatfolyamok számára. Egyetlen hangstreamtel rendelkező MP4's egy tipikus jegyzékfájlja a következőképpen néz ki:

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
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

Az. ISM fájl egy switch utasításban található, amely az egyes MP4-videókra, valamint azokra a fájlokra is hivatkozik, amelyek egy MP4-re hivatkoznak, amely csak a hanganyagot tartalmazza.

A manifest-fájl létrehozása a MP4's az "AMS manifest Writer" nevű összetevőn keresztül végezhető el. A használatához húzza a felületet a felületre, és a három kimeneti összetevőből állítsa be a "Write Complete" kimeneti PIN-kódokat az AMS manifest-író bemenetbe. Ezután győződjön meg róla, hogy az AMS-író kimenetét a kimeneti fájlhoz/eszközhöz kapcsolja.

A többi kimeneti összetevőhöz hasonlóan a. ISM fájl kimeneti nevét is konfigurálja kifejezéssel:

`${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism`

A befejezett munkafolyamat az alábbihoz hasonlóan néz ki:

![A MXF befejeződött a több sávszélességű MP4-munkafolyamathoz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*A MXF befejeződött a több sávszélességű MP4-munkafolyamathoz*

## <a name="encoding-mxf-into-multibitrate-mp4---enhanced-blueprint"></a><a id="MXF_to__multibitrate_MP4"></a>MXF kódolása többszörös sávszélességű MP4-ben – továbbfejlesztett terv
Az [előző munkafolyamat](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) -bemutatóban láttuk, hogy egy MXF bemeneti eszköz Hogyan alakítható át a többszörös sávszélességű MP4-fájlokat tartalmazó kimeneti eszközre, a csak hangként használható MP4-fájlra, valamint a Azure Media Services dinamikus csomagolással együtt használandó jegyzékfájlra.

Ebből a bemutatóból megtudhatja, hogyan növelhető és kényelmesebb a különböző szempontok.

### <a name="workflow-overview-to-enhance"></a><a id="MXF_to_multibitrate_MP4_overview"></a>A munkafolyamat áttekintése a fejlesztéshez
![A többsebességű MP4-munkafolyamatok fejlesztése](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*A többsebességű MP4-munkafolyamatok fejlesztése*

### <a name="file-naming-conventions"></a><a id="MXF_to__multibitrate_MP4_file_naming"></a>Fájl elnevezési konvenciói
Az előző munkafolyamatban egy egyszerű kifejezést adtunk meg a kimeneti fájlnevek generálásának alapjaként. Van néhány duplikált fájl is: az összes, az adott kifejezést megadó kimeneti fájl-összetevő.

A fájl kimeneti összetevője például az első videofájl esetében a következő kifejezéssel van konfigurálva:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4`

A második kimeneti videóhoz hasonlóan a következő kifejezés is van:

`${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4`

Nem lenne tisztább, kevésbé gyakori hibák, és kényelmesebb, ha el lehetne távolítani néhány ilyen ismétlődést, és inkább konfigurálható a dolgok? Szerencsére az is lehetséges, hogy a tervező kifejezési képességei a munkafolyamatok gyökerében egyéni tulajdonságok létrehozására való képességgel bővülnek, és egy további kényelmi réteget is biztosítunk.

Tegyük fel, hogy a fájlnevek konfigurációját az egyes MP4-fájlok bitrátái alapján hajtjuk. Ezek a bitráták arra törekszünk, hogy egy központi helyen (a gráf gyökerén) legyenek konfigurálva, ahonnan a fájlnevek létrehozásához és eléréséhez fognak hozzáférni. Ehhez először tegye közzé a bitráta tulajdonságot az AVC-kódolók közül a munkafolyamat gyökerére, hogy az elérhető legyen a legfelső szintű és az AVC-kódolók között is. (Akkor is, ha két különböző helyen jelenik meg, csak egy mögöttes érték van.)

### <a name="publishing-component-properties-onto-the-workflow-root"></a><a id="MXF_to__multibitrate_MP4_publishing"></a>Összetevő tulajdonságainak közzététele a munkafolyamat gyökerén
Nyissa meg az első AVC-kódolót, lépjen a bitráta (kbps) tulajdonságra, és a legördülő menüből válassza a közzététel lehetőséget.

![A bitráta tulajdonság közzététele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*A bitráta tulajdonság közzététele*

Konfigurálja a közzétételi párbeszédpanelt a munkafolyamat-gráf gyökerére való közzétételhez, a "video1bitrate" közzétett nevével és a "video 1 bitráta" olvasható megjelenítendő névvel. Konfigurálja az egyéni csoportnév "streaming bitráta" nevű nevet, és kattintson a közzététel elemre.

![A bitráta tulajdonság közzététele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*A bitráta tulajdonság közzétételi párbeszédpanele*

Ismételje meg ugyanezt a második AVC-kódoló bitráta tulajdonságához, és nevezze el a "video2bitrate" nevet a "video 2 bitráta" névvel, ugyanabban az egyéni csoportban "streaming bitráta".

Ha most megvizsgáljuk a munkafolyamat gyökerének tulajdonságait, láthatjuk, hogy az egyéni csoport a két közzétett tulajdonsággal jelenik meg. Mindkettő tükrözi a megfelelő AVC-kódoló bitrátájának értékét.

![Közzétett bitráta-kellékek a munkafolyamat gyökerénél](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Ha ezeket a tulajdonságokat kód vagy kifejezés alapján szeretnénk elérni, ezt a következő módon teheti meg:

* a beágyazott kódból egy összetevőből közvetlenül a root: Node. getPropertyAsString ('. /video1bitrate ', null)
* egy kifejezésen belül: $ {ROOT_video1bitrate}

Fejezze be a "streaming bitráta" csoportot úgy, hogy közzéteszi a hangsávok bitrátáját is. Az AAC-kódoló tulajdonságai között keresse meg a bitráta beállítást, és válassza a közzététel elemet a mellette lévő legördülő menüben. Tegye közzé a "audio1bitrate" nevű gráf gyökerét és a "hang 1 bitrátát" megjelenítendő nevet az egyéni "streaming bitráta" csoporton belül.

![Az Audio bitráta közzétételi párbeszédpanele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Az Audio bitráta közzétételi párbeszédpanele*

![Eredményül kapott videók és hanganyagok a gyökéren](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Eredményül kapott videók és hanganyagok a gyökéren*

A három érték bármelyikének módosítása szintén újrakonfigurálja és módosítja a megfelelő összetevők értékeit, amelyek a következőhöz vannak társítva: (és ahol a közzétette).

### <a name="have-generated-output-file-names-rely-on-published-property-values"></a><a id="MXF_to__multibitrate_MP4_output_files"></a>A létrehozott kimeneti fájlnevek a közzétett tulajdonságértékek alapján vannak felhasználva
A generált fájlnevek rögzítjük helyett most már megváltoztathatjuk a fájl kimeneti összetevőinek fájlnevét, hogy a diagram gyökerében közzétett bitráta-tulajdonságokat használják. Az első kimeneti fájltól kezdve keresse meg a fájl tulajdonságot, és szerkessze a kifejezést:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4`

A kifejezésben szereplő különböző paraméterek elérhetők és megadhatók úgy, hogy a kifejezés ablakban a billentyűzeten a dollár-bejelentkezést is elérhetik. Az elérhető paraméterek egyike a korábban közzétett video1bitrate-tulajdonság.

![Paraméterek elérése egy kifejezésen belül](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Paraméterek elérése egy kifejezésen belül*

Végezze el ugyanezt a fájl kimenetét a második videóban:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4`

és a csak hangfájlok kimenetéhez:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4`

Ha most módosítjuk a videó-vagy hangfájlok sebességét, a rendszer újrakonfigurálja a megfelelő kódolót, és az összes automatikus átviteli sebesség-alapú fájl neve konvenciót tiszteletben tartja.

## <a name="adding-thumbnails-to-multibitrate-mp4-output"></a><a id="thumbnails_to__multibitrate_MP4"></a>Miniatűrök hozzáadása több sávszélességű MP4-kimenethez
Egy olyan munkafolyamattól kezdve, amely egy [MXF-bemenetből származó, több sávszélességű MP4-kimenetet](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)generál, most a bélyegképek hozzáadását fogjuk megtekinteni a kimenethez.

### <a name="workflow-overview-to-add-thumbnails-to"></a><a id="thumbnails_to__multibitrate_MP4_overview"></a>A munkafolyamatok áttekintése a miniatűrök hozzáadásához
![A több sávszélességű MP4-munkafolyamat kezdési](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*A több sávszélességű MP4-munkafolyamat kezdési*

### <a name="adding-jpg-encoding"></a><a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>JPG-kódolás hozzáadása
A miniatűr generációnk szíve a JPG kódoló összetevő lesz, amely képes a JPG-fájlok kimenetének exportálására.

![JPG-kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG-kódoló*

A tömörített videó streamet azonban nem lehet közvetlenül a JPG-kódolóba bevitt adathordozó-fájlból összekapcsolni. Ehelyett egyéni kereteket kell átadnia. Ezt a video frame Gate összetevőn keresztül teheti meg.

![Frame-kapu csatlakoztatása a JPG-kódolóhoz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Frame-kapu csatlakoztatása a JPG-kódolóhoz*

A keret kapuja ennyi másodperc vagy keret elteltével lehetővé teszi a videó keretének továbbítását. Az intervallum és az az időeltolódás, amellyel ez a beállítás a tulajdonságok között konfigurálható.

![A video frame Gate tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*A video frame Gate tulajdonságai*

Hozzon létre egy miniatűrt percenként úgy, hogy az időt (másodpercben), az intervallumot pedig a 60 értékre állítja.

### <a name="dealing-with-color-space-conversion"></a><a id="thumbnails_to__multibitrate_MP4_color_space"></a>A színtér átalakításának kezelése
Habár logikusnak tűnik a frame Gate kitömöríthető videós PIN-kódjai is, és a médiafájlok bemenete most már kapcsolódhat, egy figyelmeztetés jelenik meg, ha erre lenne szükség.

![Bemeneti színtér – hiba](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Bemeneti színtér – hiba*

Ennek az az oka, hogy az eredeti nyers, kitömörítetlen, MXF érkező színes információk is eltérnek a JPG-kódoló várható adataitól. Pontosabban, az "RGB" vagy a "szürkeárnyalatos" egy úgynevezett "színtér" a várt folyamat. Ez azt jelenti, hogy a video frame Gate bejövő videós adatfolyamának először a színtérre kell alkalmaznia.

Húzza a munkafolyamatra a színtér-átalakító-Intel elemet, és kapcsolja össze a frame Gate-vel.

![Színtér-átalakító csatlakoztatása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Színtér-átalakító csatlakoztatása*

A Properties (Tulajdonságok) ablakban válassza ki a BGR 24 bejegyzést az előre beállított listából.

### <a name="writing-the-thumbnails"></a><a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>A miniatűrök írása
Az MP4-videóktól eltérően a JPG kódoló összetevő több fájlt is kiküld. Ennek kezeléséhez a rendszer egy jelenetben keres egy JPG-fájl írója összetevőt: a bejövő JPG-miniatűröket veszi fel, és kiírja őket, és minden fájlnevet más számú utótagnak kell megadnia. (Ez a szám általában azt jelzi, hogy hány másodperc/egység szerepel az adatfolyamban, amelyet a miniatűr rajzolt.)

![A jelenet keresési JPG-fájljának írója](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*A jelenet keresési JPG-fájljának írója*

Konfigurálja a kimeneti mappa elérési útja tulajdonságát a kifejezéssel:`${ROOT_outputWriteDirectory}`

és a fájlnév előtag tulajdonsága a alábbiakkal:

`${ROOT_sourceFileBaseName}_thumb_`

Az előtag meghatározza, hogy a miniatűr fájlok hogyan legyenek elnevezve. Ezek utótagja egy szám, amely jelzi a hüvelykujj pozícióját a streamben.

![Jelenet keresése JPG-fájl írója tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Jelenet keresése JPG-fájl írója tulajdonságai*

A Scene Search JPG-fájl írója a kimeneti fájl/eszköz csomóponthoz csatlakoztatható.

### <a name="detecting-errors-in-a-workflow"></a><a id="thumbnails_to__multibitrate_MP4_errors"></a>Hibák észlelése egy munkafolyamatban
A színtér-átalakító bemenetének összekötése a nyers tömörítetlen videó kimenetével. Most futtasson helyi tesztelési tesztet a munkafolyamathoz. Jó esély van arra, hogy a munkafolyamat hirtelen leállítja a végrehajtást, és a hibát okozó összetevő piros körvonalát jelezze:

![Színtér-átalakító hiba](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Színtér-átalakító hiba*

Kattintson a kis piros "E" ikonra a színtér-átalakító összetevő jobb felső sarkában, hogy megtekintse, mi az oka, hogy a kódolási kísérlet sikertelen volt.

![Színtér-átalakító – hiba párbeszédpanel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Színtér-átalakító – hiba párbeszédpanel*

Kiderül, ahogy láthatja, hogy a Color Space converterhez tartozó bejövő színtér-szabványnak rec601 kell lennie a YUV és az RGB közötti átalakításhoz. Úgy tűnik, az adatfolyamunk nem jelzi a rec601. (A REC 601 egy szabvány, amely összefűzött analóg videojel-jeleket biztosít digitális videó formájában. Egy aktív régiót határoz meg, amely az 720-es fénysűrűség mintáit és a 360 Chrominance-mintákat tartalmazza. A színkódolási rendszer a YCbCr 4:2:2 néven ismert.)

Ennek kijavításához a rec601-tartalommal foglalkozó stream metaadatait fogjuk jelezni. Ehhez a videó adattípus-frissítési összetevőjét fogjuk használni, amelyet a rendszer a nyers forrás és a színtér átalakítási összetevője közé helyez. Ez az adattípus-frissítő lehetővé teszi bizonyos video adattípus-tulajdonságok manuális frissítését. Konfigurálja úgy, hogy jelezze a "REC 601" színtér-szabványt. Ennek hatására a videó adattípus-Updater a "REC 601" színterülettel címkézi a streamet, ha még nincs definiálva színtér. (A rendszer nem bírálja felül a meglévő metaadatokat, kivéve, ha a felülbírálás jelölőnégyzet be van jelölve.)

![A Color Space standard frissítése az adattípus-Frissítőn](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*A Color Space standard frissítése az adattípus-Frissítőn*

### <a name="finished-workflow"></a><a id="thumbnails_to__multibitrate_MP4_finish"></a>Befejezett munkafolyamat
Most, hogy befejezte a munkafolyamatot, hajtson végre egy újabb tesztet a megtekintéshez.

![Befejezett munkafolyamatok a többszörös MP4 kimenethez a miniatűrökkel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Befejezett munkafolyamatok a többszörös MP4 kimenethez a miniatűrökkel*

## <a name="time-based-trimming-of-multibitrate-mp4-output"></a><a id="time_based_trim"></a>A többszörös sávszélességű MP4 kimenet időalapú kivágása
Egy olyan munkafolyamattól kezdve, amely egy [MXF-bemenetből származó többszörös sávszélességű MP4-kimenetet](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)hoz létre, most a forrásként szolgáló videót a Time-Stamps alapján metszi.

### <a name="workflow-overview-to-start-adding-trimming-to"></a><a id="time_based_trim_start"></a>A munkafolyamat áttekintése a vágás hozzáadásának megkezdéséhez
![A munkafolyamat elindítása a vágás hozzáadásához](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*A munkafolyamat elindítása a vágás hozzáadásához*

### <a name="using-the-stream-trimmer"></a><a id="time_based_trim_use_stream_trimmer"></a>Az adatfolyam-formázó használata
Az adatfolyam-formázó összetevő lehetővé teszi, hogy az időzítési adatok (másodperc, perc,...) alapján levágja a bemeneti adatfolyam alapját és végét. A trimmer nem támogatja a frame-alapú vágást.

![Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Stream Trimmer*

Ahelyett, hogy közvetlenül összekapcsolja az AVC-kódolókat és a beszélő pozíciót a médiafájl-bemenethez, az adatfolyam-tárolóba kerül. (Az egyik a videojel, egy pedig az összekapcsolt hangjelzéshez.)

![A stream-trimmert a között kell elhelyezni](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*A stream-trimmert a között kell elhelyezni*

Konfigurálja a trimmert úgy, hogy a videó és a hang csak 15 másodperc és 60 másodperc között legyen feldolgozva a videóban.

Nyissa meg a videó stream Trimmer tulajdonságait, és konfigurálja a kezdési időt (15 s) és a Befejezés időpontja (60 s) tulajdonságokat. Annak érdekében, hogy a hang-és video-Trimmer mindig ugyanazokra a kezdő és záró értékekre legyen konfigurálva, ezeket a munkafolyamat gyökerére tesszük közzé.

![A kezdő időpont tulajdonság közzététele a stream Trimmerből](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*A kezdő időpont tulajdonság közzététele a stream Trimmerből*

![Tulajdonságok közzététele párbeszédpanel a kezdési időponthoz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Tulajdonságok közzététele párbeszédpanel a kezdési időponthoz*

![A közzétételi Tulajdonságok párbeszédpanel befejezési ideje](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*A közzétételi Tulajdonságok párbeszédpanel befejezési ideje*

Ha most megvizsgáljuk a munkafolyamatok gyökerét, mindkét tulajdonság jól látható és konfigurálható.

![A gyökéren elérhető közzétett tulajdonságok](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*A gyökéren elérhető közzétett tulajdonságok*

Most nyissa meg a vágási tulajdonságokat a hangformázó formátumból, és állítsa be a kezdő és a záró időpontot egy olyan kifejezéssel, amely a munkafolyamat gyökérkönyvtárának közzétett tulajdonságaira hivatkozik.

A Hangvágás indítási ideje:

`${ROOT_TrimmingStartTime}`

és a befejezési idejére:

`${ROOT_TrimmingEndTime}`

### <a name="finished-workflow"></a><a id="time_based_trim_finish"></a>Befejezett munkafolyamat
![Befejezett munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Befejezett munkafolyamat*

## <a name="introducing-the-scripted-component"></a><a id="scripting"></a>A megírt összetevő bemutatása
A megírt összetevők tetszőleges parancsfájlokat futtathatnak a munkafolyamat végrehajtási fázisaiban. Négy különböző szkriptet lehet végrehajtani, amelyek mindegyike konkrét tulajdonságokkal rendelkezik, és a munkafolyamatok életciklusában a saját helyük:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

A parancsfájlban szereplő összetevő dokumentációja részletesebben ismerteti a fentiek mindegyikét. A [következő szakaszban](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)a **realizeScript** Scripting összetevő használatával cliplist XML-t hozhat létre menet közben a munkafolyamat indításakor. Ezt a szkriptet az összetevő telepítésekor hívja meg a rendszer, amely csak egyszer fordul elő életciklusa során.

### <a name="scripting-within-a-workflow-hello-world"></a><a id="scripting_hello_world"></a>Parancsfájlok futtatása a munkafolyamaton belül: Hello World
Húzzon egy parancsfájlt tartalmazó összetevőt a tervező felületre, és nevezze át (például "SetClipListXML").

![Parancsfájlból álló összetevő hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Parancsfájlból álló összetevő hozzáadása*

Ha megvizsgálja a parancsfájlban szereplő összetevő tulajdonságait, a rendszer négy különböző parancsfájlt jelenít meg, amelyek mindegyike egy másik parancsfájlhoz konfigurálható.

![Parancsfájlozott összetevő tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Parancsfájlozott összetevő tulajdonságai*

Törölje a processInputScript, és nyissa meg a realizeScript szerkesztőjét. Most már be van állítva, és készen áll a parancsfájlok indítására.

A szkriptek a Java platformmal való kompatibilitást megtartó, egy dinamikusan lefordított parancsfájlkezelési nyelven íródtak. Valójában a legtöbb Java-kód érvényes sablonos kód.

Írj egy egyszerű Hello World-szkriptet a realizeScript kontextusában. Adja meg a következőt a szerkesztőben:

`node.log("hello world");`

Most futtasson egy helyi tesztet. A futtatást követően vizsgálja meg a naplók tulajdonságot a parancsfájlt tartalmazó összetevő rendszer lapján.

![Hello World log kimenet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hello World log kimenet*

A Node objektum, amelyben a log metódust hívjuk, a jelenlegi "csomópontra" vagy a parancsfájlban található összetevőre hivatkozik. Minden összetevő, amely lehetővé teszi a naplózási adatokat, a System lapon elérhetővé válik. Ebben az esetben a "Hello World" karakterláncot adja eredményül. Fontos megjegyezni, hogy ez egy felbecsülhetetlen hibakeresési eszköz lehet, amely a szkript tényleges futtatásának megismerését teszi lehetővé.

A parancsfájlkezelési környezetből más összetevők tulajdonságai is elérhetők. Próbálja meg a következőt:

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

A naplónk ablaka a következőket jeleníti meg:

![A csomópont elérési útjaihoz való hozzáférés naplózási kimenete](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*A csomópont elérési útjaihoz való hozzáférés naplózási kimenete*

## <a name="frame-based-trimming-of-multibitrate-mp4-output"></a><a id="frame_based_trim"></a>Többsebességű MP4-kimenet frame-alapú körülvágása
Egy olyan munkafolyamattól kezdve, amely egy több [sávszélességű MP4-kimenetet hoz létre egy MXF-bemenetből](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), most a forrásként szolgáló videót a frame Counts alapján metszi.

### <a name="blueprint-overview-to-start-adding-trimming-to"></a><a id="frame_based_trim_start"></a>A terv áttekintésével megkezdheti a vágás hozzáadását a következőhöz
![A kivágás hozzáadásának megkezdéséhez szükséges munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*A kivágás hozzáadásának megkezdéséhez szükséges munkafolyamat*

### <a name="using-the-clip-list-xml"></a><a id="frame_based_trim_clip_list"></a>A klipek listájának XML-fájljának használata
Az összes korábbi munkafolyamat-oktatóanyag esetében a Media file input összetevőt használtuk a videó bemeneti forrásaként. Ebben a konkrét forgatókönyvben azonban a klip lista forrás összetevőjét fogjuk használni. Ez nem lehet az előnyben részesített munkamódszer. csak akkor használja a klip-lista forrását, ha valóban erre van szükség (például a következő esetben, ha a klipek kivágási funkcióit használjuk).

Ha át szeretné váltani a médiafájl-bemenetről a klip listára, húzza a klip lista forrás összetevőjét a tervezési felületre, és a klip listához tartozó XML-PIN-kódot a Munkafolyamat-tervező klipek listája XML-csomópontjára kapcsolhatja. Ez a bemeneti videó alapján feltölti a klipek listáját a kimeneti PIN-kódokkal. Most csatlakoztassuk a kibontott videót és a kitömörítetlen hangpin-fájlokat a klip listáról a megfelelő AVC-kódolók és hangadatfolyam-összekötők között. Most távolítsa el a médiafájl bemenetét.

![A médiafájl-bevitel cseréje a klip listára – forrás](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*A médiafájl-bevitel cseréje a klip listára – forrás*

A legördülő lista forrásának összetevője a "klipek XML-kódjának" bevitelét írja be. Amikor kiválasztja a forrásfájlt a helyi teszteléshez, a lista XML-fájlja automatikusan ki lesz töltve.

![Automatikusan feltöltött klipek XML-tulajdonsága](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automatikusan feltöltött klipek XML-tulajdonsága*

Így néz ki egy kicsit közelebb az XML-hez, így néz ki:

![Klipek listájának szerkesztése párbeszédpanel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Klipek listájának szerkesztése párbeszédpanel*

Ez azonban nem tükrözi a klipek XML-listájának képességeit. Az egyik lehetőség, hogy egy "Trim" elemet adunk hozzá a videó-és hangforráshoz, például a következőhöz:

![Trim elem hozzáadása a klipek listájához](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Trim elem hozzáadása a klipek listájához*

Ha a fentiekben leírtak szerint módosítja a klipek listáját, és helyi tesztet hajt végre, akkor a videót a videóban megfelelően 10 és 20 másodperc között kell megtekinteni.

Ellentétben azzal, ami akkor fordul elő, ha helyi futtatást hajt végre, ugyanez a cliplist XML-fájl nem ugyanaz, mint a Azure Media Services-ban futó munkafolyamatokban. Ha elindul az Azure Premium Encoder, a rendszer minden alkalommal újra létrehozza a cliplist XML-kódot a kódolási feladatot kapott bemeneti fájl alapján. Ez azt jelenti, hogy az XML-ben végrehajtott módosítások sajnos felül lesznek bírálva.

A kódolási feladatok elindításakor a cliplist XML-fájl törléséhez a munkafolyamat elindítása után menet közben újra létrehozhatjuk. Ezek az egyéni műveletek a "megírt összetevő" néven is meghozhatók. További információ: [a megírt összetevő bemutatása](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Húzzon egy parancsfájlt tartalmazó összetevőt a tervező felületre, és nevezze át "SetClipListXML" értékre.

![Parancsfájlból álló összetevő hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Parancsfájlból álló összetevő hozzáadása*

A megírt összetevő tulajdonságainak vizsgálatakor a négy különböző parancsfájl-típus látható, amelyek mindegyike egy másik parancsfájlhoz konfigurálható.

![Parancsfájlozott összetevő tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Parancsfájlozott összetevő tulajdonságai*

### <a name="modifying-the-clip-list-from-a-scripted-component"></a><a id="frame_based_trim_modify_clip_list"></a>A klipek listájának módosítása egy parancsfájlból álló összetevőből
Ahhoz, hogy újra lehessen írni a munkafolyamat indításakor létrehozott cliplist XML-fájlt, hozzáféréssel kell rendelkeznie a cliplist XML-tulajdonsághoz és a tartalomhoz. Ezt a következőképpen teheti meg:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![Naplózott bejövő klipek listája](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Naplózott bejövő klipek listája*

Először is meg kell határozni, hogy melyik pontról kell kivágni a videót. Ahhoz, hogy ez a munkafolyamat kevésbé technikai felhasználói számára legyen elérhető, tegye közzé két tulajdonságot a gráf gyökerében. Ehhez kattintson a jobb gombbal a tervező felületre, és válassza a "tulajdonság hozzáadása" lehetőséget:

* Első tulajdonság: "ClippingTimeStart", típus: "IDŐKÓD"
* Második tulajdonság: "ClippingTimeEnd", típus: "IDŐKÓD"

![Tulajdonságok hozzáadása párbeszédpanel a kivágás kezdő időpontjához](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Tulajdonságok hozzáadása párbeszédpanel a kivágás kezdő időpontjához*

![Kivágási idő közzététele a munkafolyamat gyökerében](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Kivágási idő közzététele a munkafolyamat gyökerében*

Mindkét tulajdonságot egy megfelelő értékre állítsa be:

![A vágás kezdési és befejezési tulajdonságainak konfigurálása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*A vágás kezdési és befejezési tulajdonságainak konfigurálása*

Mostantól a szkriptből is elérheti mindkét tulajdonságot, a következőhöz hasonlóan:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![A levágás kezdetét és végét jelző napló ablak](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*A levágás kezdetét és végét jelző napló ablak*

Egy egyszerű reguláris kifejezéssel elemezzük, hogy az időkód-karakterláncok könnyebben használhatók legyenek az űrlaphoz:

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

![Napló ablak az elemzett időkód kimenetével](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Napló ablak az elemzett időkód kimenetével*

Ezzel az információval most már módosíthatjuk a cliplist XML-t, hogy az tükrözze a film kívánt keretének kezdési és befejezési időpontját.

![Parancsfájl-kód a Trim elemek hozzáadásához](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Parancsfájl-kód a Trim elemek hozzáadásához*

Ez a normál karakterlánc-manipulációs műveleteken keresztül történt. Az eredményül kapott módosított klipek listájának XML-fájlját a rendszer a "setProperty" metódus használatával visszaírja a munkafolyamat gyökerének clipListXML tulajdonságára. Egy másik teszt futtatása után a napló ablak a következőket jeleníti meg:

![Az eredményül kapott klipek listájának naplózása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Az eredményül kapott klipek listájának naplózása*

Futtasson egy tesztet, hogy megtekintse a videó-és hangadatfolyamok kivágásának módját. Mivel egynél több tesztet hajt végre különböző értékekkel a kivágási pontokhoz, megfigyelheti, hogy ezeket a rendszer nem veszi figyelembe. Ennek az az oka, hogy a tervező az Azure-futtatókörnyezettől eltérően nem bírálja felül a cliplist XML-t minden futtatáskor. Ez azt jelenti, hogy a be-és kimenő pontok első beállításakor az XML átalakítható, az összes többi alkalommal, a Guard záradék (IF ( `clipListXML.indexOf("<trim>") == -1` )) megakadályozza, hogy a munkafolyamat egy másik Trim elemet adjon hozzá, ha már van ilyen.

Ahhoz, hogy a munkafolyamatot helyileg tesztelni lehessen, a legjobb megoldás, ha egy Trim elem már szerepel. Ha igen, a folytatáshoz távolítsa el az XML-fájl új értékekkel való módosításával. Ahelyett, hogy egyszerű karakterlánc-manipulációkat használ, ez valószínűleg biztonságosabb, ha a valós XML-objektummodell elemzését végzi.

Mielőtt hozzá tudná adni ezt a kódot, hozzá kell adnia néhány importálási utasítást a szkript első elején:

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

Ezután felveheti a szükséges tisztítási kódot:

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

Ez a kód közvetlenül a pont fölé kerül, ahol a Trim elemeket hozzáadja a cliplist XML-hez.

Ezen a ponton futtathatjuk és módosíthatjuk a munkafolyamatot, amennyire csak szeretnénk, miközben a módosításokat még egyszer is alkalmazták.    

### <a name="adding-a-clippingenabled-convenience-property"></a><a id="frame_based_trim_clippingenabled_prop"></a>ClippingEnabled kényelmi tulajdonság hozzáadása
Mivel előfordulhat, hogy nem mindig szeretné kimetszeni a kivágást, a munkafolyamatot a megfelelő logikai jelző hozzáadásával fejezzük ki, amely azt jelzi, hogy szeretnénk-e engedélyezni a vágást vagy a nyírást.

Ahogy korábban is, tegyen közzé egy új tulajdonságot a "logikai" típusú "ClippingEnabled" nevű munkafolyamat gyökerében.

![Közzétett egy tulajdonságot a nyírás engedélyezéséhez](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Közzétett egy tulajdonságot a nyírás engedélyezéséhez*

Az alábbi egyszerű Guard záradékkal ellenőrizhető, hogy szükség van-e a vágásra, és eldöntse, hogy a klipek listáját módosítani kell-e, vagy sem.

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

### <a name="complete-code"></a><a id="code"></a>Teljes kód

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
[Prémium szintű kódolás bemutatása Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Prémium szintű kódolás használata a Azure Media Servicesban](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Igény szerinti tartalom kódolása az Azure Media Service szolgáltatással](media-services-encode-asset.md#media-encoder-premium-workflow)

[A Media Encoder Premium munkafolyamat formátumai és kodekei](media-services-premium-workflow-encoder-formats.md)

[Munkafolyamat-fájlok mintája](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Explorer eszköz](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
