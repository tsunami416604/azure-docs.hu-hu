---
title: Speciális oktatóanyagok a Media Encoder Premium-munkafolyamathoz
description: Ez a dokumentum forgatókönyveket tartalmaz, amelyek bemutatják, hogyan hajthat végre speciális feladatokat a Media Encoder Premium Workflow segítségével, és hogyan hozhat létre összetett munkafolyamatokat a Munkafolyamat-tervezővel.
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
ms.openlocfilehash: 1ab70d56bd3def58d0e814035070cf027a88cd3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251010"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Speciális oktatóanyagok a Media Encoder Premium-munkafolyamathoz
## <a name="overview"></a>Áttekintés
Ez a dokumentum útmutatókat tartalmaz, amelyek bemutatják, hogyan szabhatja testre a munkafolyamatokat a **Munkafolyamat-tervezővel.** A tényleges munkafolyamat-fájlokat [itt](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples)találja.  

## <a name="toc"></a>Toc
A következő témaköröket a következő témakörök ben tárgyaltuk:

* [Az MXF kódolása egyetlen bitráta MP4-be](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Új munkafolyamat indítása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [A médiafájl bemenetének használata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Médiaadatfolyamok vizsgálata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Videokódoló hozzáadása a alkalmazáshoz. MP4 fájl generálása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Az audioadatfolyam kódolása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Hang- és videoadatfolyamok multiplexelése MP4-tárolóba](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Az MP4-fájl megírása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [Media Services-eszköz létrehozása a kimeneti fájlból](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [A befejezett munkafolyamat helyi tesztelése](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Az MXF kódolása többbitrátásos MP4-ekbe – dinamikus csomagolás engedélyezve](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Egy vagy több további MP4-kimenet hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [A fájlkimeneti nevek konfigurálása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Külön hangsáv hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [Az "ISM" SMIL fájl hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Az MXF kódolása többbitráta-mp4-be - továbbfejlesztett tervrajz](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * A munkafolyamat áttekintése a továbbfejlesztéséhez
  * [Fájlelnevezési konvenciók](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [Összetevő-tulajdonságok közzététele a munkafolyamat gyökérén](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [A létrehozott kimeneti fájlnevek közzétett tulajdonságértékeken alapulnak](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [Bélyegképek hozzáadása többbitráta-MP4 kimenethez](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * A munkafolyamat áttekintése a bélyegképek hozzáadásához
  * [JPG-kódolás hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [A színtér átalakításának kezelése](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [A bélyegképek írása](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Hibák észlelése egy munkafolyamatban](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [Munkafolyamat befejeződött](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Többbitráta-mp4 kimenet időalapú levágása](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Munkafolyamat áttekintése a vágás hozzáadásának megkezdéséhez](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [A Stream Trimmer használata](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [Munkafolyamat befejeződött](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [A parancsfájlalapú összetevő bemutatása](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Parancsfájlok futtatása egy munkafolyamaton belül: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Többbitráta MP4 kimenet keretalapú vágása](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [A tervezet áttekintése a vágás hozzáadásának megkezdéséhez](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [A Clip List XML használata](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [A kliplista módosítása parancsfájlalapú összetevőből](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [ClippingEnabled kényelmi tulajdonság hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a name="encoding-mxf-into-a-single-bitrate-mp4"></a><a id="MXF_to_MP4"></a>Az MXF kódolása egyetlen bitráta MP4-be
Ez a szakasz bemutatja, hogyan hozhat létre egyetlen bitrátát . MP4 fájl AAC-HE kódolt hang egy . MXF bemeneti fájl.

### <a name="starting-a-new-workflow"></a><a id="MXF_to_MP4_start_new"></a>Új munkafolyamat indítása
A Munkafolyamat-tervező megnyitása és a Fájl > új munkaterület > a Transcode Blueprint

Az új munkafolyamat három elemet mutat:

* Elsődleges forrásfájl
* Kliplista XML-fájlja
* Kimeneti fájl/eszköz  

![Új kódolási munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Új kódolási munkafolyamat*

### <a name="using-the-media-file-input"></a><a id="MXF_to_MP4_with_file_input"></a>A médiafájl bemenetének használata
A bemeneti médiafájl elfogadásához egy media file bemeneti összetevő hozzáadásával kell kezdeni. Ha összetevőt szeretne hozzáadni a munkafolyamathoz, keresse meg azt a Tárház keresőmezőjében, és húzza a kívánt bejegyzést a tervező ablaktáblájára. Ismételje meg a műveletet a médiafájl bemenetén, és csatlakoztassa az elsődleges forrásfájl-összetevőt a Médiafájl bemeneti fájlbemeneti fájlnév bemeneti pinjéhez.

![Csatlakoztatott médiafájl bemenete](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Csatlakoztatott médiafájl bemenete*

Kezdetben azonosítsa az egyéni munkafolyamat tervezésekor használandó megfelelő mintafájlt. Ehhez kattintson a tervező ablaktáblájának hátterére, és keresse meg az Elsődleges forrásfájl tulajdonságot a jobb oldali tulajdonságpanelen. Kattintson a mappa ikonra, és válassza ki a munkafolyamat teszteléséhez kívánt fájlt. A Media File Input összetevő megvizsgálja a fájlt, és feltölti a kimeneti csapokat, hogy azok tükrözzék a vizsgált mintafájl részleteit.

![Lakott médiafájl bemenete](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Lakott médiafájl bemenete*

Most, hogy a bemenet fel van töltve, a következő lépés a kimeneti kódolási beállítások beállítása. Hasonlóan az elsődleges forrásfájl konfigurálásához, most konfigurálja a Kimeneti mappa változó tulajdonságát, közvetlenül alatta.

![Konfigurált bemeneti és kimeneti tulajdonságok](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Konfigurált bemeneti és kimeneti tulajdonságok*

### <a name="inspecting-media-streams"></a><a id="MXF_to_MP4_streams"></a>Médiaadatfolyamok vizsgálata
Gyakran kívánatos tudni, hogy a stream hogyan néz ki, ahogy a munkafolyamaton keresztül folyik. Ha a munkafolyamat bármely pontján meg szeretne vizsgálni egy adatfolyamot, kattintson egy kimeneti vagy bemeneti tűre bármelyik összetevőn. Ebben az esetben kattintson a Media File Input tömörítetlen videokimeneti pinére. Megnyílik egy párbeszédablak, amely lehetővé teszi a kimenő videó vizsgálatát.

![A tömörítetlen videokimeneti csap vizsgálata](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*A tömörítetlen videokimeneti csap vizsgálata*

Ebben az esetben azt mutatja, hogy a videó tartalmaz egy 1920x1080 bemenet 24 képkocka / másodperc 4:2:2 mintavétel egy videó csaknem 2 perc.

### <a name="adding-a-video-encoder-for-mp4-file-generation"></a><a id="MXF_to_MP4_file_generation"></a>Videokódoló hozzáadása a alkalmazáshoz. MP4 fájl generálása
Most egy tömörítetlen videó és több tömörítetlen hangkimeneti tű használható a médiafájl bemenetén. A bejövő videó kódolásához hozzá kell adni egy kódolási összetevőt a munkafolyamathoz - ebben az esetben a létrehozásához. MP4 fájlokat.

Ha a videostreamet H.264-re szeretné kódolni, adja hozzá az AVC videokódoló összetevőt a tervezőfelülethez. Ez az összetevő bemenetként kibontja a videostreamet, és a kimeneti pinen a dip-tömörített videostreamet biztosít.

![Nem csatlakoztatott AVC kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Nem csatlakoztatott AVC kódoló*

Tulajdonságai határozzák meg, hogy a kódolás pontosan hogyan történik. Vessünk egy pillantást néhány, a fontosabb beállítások:

* Kimeneti szélesség és kimeneti magasság: meghatározza a kódolt videó felbontását. Ebben az esetben a 640x360 jó beállítás.
* Frame Rate: ha be van állítva, hogy átmenő ez csak elfogadja a forrás frame rate, lehetséges, hogy felülbírálja ezt mégis. Az ilyen framerate átalakítás nem mozgáskompenzált.
* Profil és szint: meghatározza az AVC profilt és a szintet. Ha kényelmesen szeretne többet megtudni a különböző szintekről és profilokról, kattintson az AVC Video Encoder összetevő kérdőjel ikonjára, és a súgóoldal részletesebben bemutatja az egyes szinteket. Ebben a példában használja a Fő profilt a 3.2 szinten (ez az alapértelmezett).
* Sebességvezérlési mód és bitráta (kbit/s): ebben a forgatókönyvben 1200 kbps-os állandó bitráta (CBR) kimenetet kell választania.
* Video formátum: információt nyújt a VUI-ról (Video Használhatósági információk), amely bekerül a H.264 adatfolyamba (oldalsó információk, amelyeket a dekóder a kijelző javítására használhat, de nem elengedhetetlen a megfelelő dekódoláshoz):
* NTSC (jellemző az Egyesült Államokban vagy Japánban, 30 fps használatával)
* PAL (jellemző Európára, 25 fps-t használva)
* GOP méret mód: állítsa fix GOP mérete a céljainkhoz egy 2 másodperces kulcsintervallummal zárt GOP-okkal. A 2 másodperces beállítás biztosítja az Azure Media Services dinamikus csomagolásával való kompatibilitást.

Az AVC kódoló adagolásához csatlakoztassa a Media File Input összetevő tömörítetlen videokimeneti csapját az AVC kódoló tömörítetlen videobemeneti tűjéhez.

![Csatlakoztatott AVC kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Csatlakoztatott AVC fő kódoló*

### <a name="encoding-the-audio-stream"></a><a id="MXF_to_MP4_audio"></a>Az audioadatfolyam kódolása
Ezen a ponton az eredeti tömörítetlen hangfolyamot még tömöríteni kell. Az audiostream tömörítéséhez adjon hozzá egy AAC kódoló (Dolby) összetevőt a munkafolyamathoz.

![Nem csatlakoztatott AVC kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Nem csatlakoztatott AAC kódoló*

Most van egy inkompatibilitás: csak egy tömörítetlen audio bemeneti pin az AAC Kódoló, míg több mint valószínű, a Media File Input lesz két különböző tömörítetlen audio stream áll rendelkezésre: az egyik a bal audio csatorna és egy a jobb oldalon. (Ha térhatású hanggal van dolgunk, az hat csatorna.) Így nem lehet közvetlenül csatlakoztatni a médiafájl bemeneti forrásából származó hangot az AAC audiokódolóhoz. Az AAC komponens egy úgynevezett "átegyenesedett" hangfolyamot vár: egyetlen adatfolyamot, amely mind a bal, mind a jobb csatornákat egymással összeláncította. Amint tudjuk, a mi forrás média fájlt, hogy a hangsávok milyen helyzetben van a forrás, tudunk generálni ilyen interleaved audio stream a helyesen hozzárendelt hangszóró pozíciók at bal és jobb.

Először is, az egyik akar generálni egy áthatárolt patak a szükséges forrás audio csatornák. Az Audio Stream Interleaver komponens kezeli ezt számunkra. Adja hozzá a munkafolyamathoz, és csatlakoztassa a médiafájl bemenetének hangkimeneteit.

![Csatlakoztatott audiostream interleaver](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Csatlakoztatott audiostream interleaver*

Most, hogy van egy átegyenesedett audio stream, még mindig nem adja meg, hogy hol kell rendelni a bal vagy jobb hangszóró pozíciókat. Ennek meghatározásához kihasználhatjuk a hangszóró pozíció-hozzárendelését.

![Hangszóró pozíció-hozzárendeléshozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Hangszóró pozíció-hozzárendeléshozzáadása*

Konfigurálja a hangszóró pozícióhozzáhelyezőjét sztereó bemeneti adatfolyamhoz az "Egyéni" kódoló készletszűrőn és a "2.0 (L,R) nevű csatornakészleten keresztül" (Ez a bal oldali hangszóró tazulit az 1-es csatornához, a jobb oldali hangszórót pedig a 2-es csatornához rendeli.)

Csatlakoztassa a hangszóró pozícióhozzárendelésének kimenetét az AAC kódoló bemenetéhez. Ezután mondja meg az AAC kódolónak, hogy működjön együtt egy "2.0 (L,R)" csatornakészlettel, így tudja, hogy a sztereó hang bemenetként kell kezelnie.

### <a name="multiplexing-audio-and-video-streams-into-an-mp4-container"></a><a id="MXF_to_MP4_audio_and_fideo"></a>Hang- és videoadatfolyamok multiplexelése MP4-tárolóba
Mivel a Mi AVC kódolt video stream és a Mi AAC kódolt audio stream, tudjuk elfog mind a . MP4 tároló. A folyamat keverése különböző patakok egyetlen az úgynevezett "multiplexing" (vagy "muxing"). Ebben az esetben, mi interleaving a hang-és video stream egyetlen koherens . MP4 csomag. Az összetevő, amely koordinálja ezt a . Mp4 konténer az úgynevezett ISO MPEG-4 Multiplexer. Adjon hozzá egyet a tervezőfelülethez, és csatlakoztassa mind az AVC videokódolót, mind az AAC kódolót a bemeneteihez.

![Csatlakoztatott MPEG4 multiplexer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Csatlakoztatott MPEG4 multiplexer*

### <a name="writing-the-mp4-file"></a><a id="MXF_to_MP4_writing_mp4"></a>Az MP4-fájl megírása
Kimeneti fájl írásakor a fájlkimeneti összetevő t használja a rendszer. Ezt az ISO MPEG-4 Multiplexer kimenetéhez tudjuk csatlakoztatni, hogy a kimenete lemezre kerüljön. Ehhez csatlakoztassa a Container (MPEG-4) kimeneti tűt a fájlkimenet írási bemeneti pinjéhez.

![Csatlakoztatott fájl kimenete](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Csatlakoztatott fájl kimenete*

A használt fájlnevet a Fájl tulajdonság határozza meg. Bár ez a tulajdonság egy adott értékre kódolható, valószínűleg egy kifejezésen keresztül szeretné beállítani.

Ha azt szeretné, hogy a munkafolyamat automatikusan meghatározza a kimeneti fájlnév tulajdonságot egy kifejezésből, kattintson a Fájlnév gombjára (a mappaikon mellett). A legördülő menüben válassza a "Kifejezés" lehetőséget. Ez felveti a kifejezésszerkesztőt. Először törölje a szerkesztő tartalmát.

![Üres kifejezésszerkesztő](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Üres kifejezésszerkesztő*

A kifejezésszerkesztő lehetővé teszi, hogy bármilyen literális értéket adjon meg, egy vagy több változóval keverve. A változók dollárjellel kezdődnek. Ahogy bejön a $ gombot, a szerkesztő egy legördülő dobozt jelenít meg a rendelkezésre álló változók választékával. A mi esetünkben a kimeneti könyvtár változó és az alap bemeneti fájlnév változó kombinációját használjuk:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Kitöltött kifejezésszerkesztő](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Kitöltött kifejezésszerkesztő*

> [!NOTE]
> Annak érdekében, hogy egy kimeneti fájlt a kódolási feladat az Azure-ban, meg kell adnia egy értéket a kifejezés szerkesztőben.
>
>

Ha megerősíti a kifejezést az OK gombra lenyomja, a tulajdonságablak előnézete azt tekinti meg, hogy a Fájl tulajdonság jelenleg milyen értéket old meg.

![A fájlkifejezés feloldja a kimeneti könyvtárat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*A fájlkifejezés feloldja a kimeneti könyvtárat*

### <a name="creating-a-media-services-asset-from-the-output-file"></a><a id="MXF_to_MP4_asset_from_output"></a>Media Services-eszköz létrehozása a kimeneti fájlból
Bár írtunk egy MP4 kimeneti fájlt, még mindig jeleznünk kell, hogy ez a fájl ahhoz a kimeneti eszközhöz tartozik, amelyet a médiaszolgáltatások a munkafolyamat végrehajtása során hoznak létre. Ebből a célból a munkafolyamat-vásznon lévő kimeneti fájl/eszközcsomópont használatos. Az ebbe a csomópontba érkező összes bejövő fájl az eredményül kapott Azure Media Services-eszköz részét képezi.

A munkafolyamat befejezéséhez csatlakoztassa a Fájlkimeneti összetevőt a kimeneti fájlhoz/eszközösszetevőhöz.

![Munkafolyamat befejeződött](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Munkafolyamat befejeződött*

### <a name="test-the-finished-workflow-locally"></a><a id="MXF_to_MP4_test"></a>A befejezett munkafolyamat helyi tesztelése
A munkafolyamat helyi teszteléséhez nyomja meg a lejátszás gombot a felső eszköztáron. Amikor a munkafolyamat végrehajtása befejeződött, vizsgálja meg a beállított kimeneti mappában létrehozott kimenetet. Látni fogja a kész MP4 kimeneti fájlt, amely az MXF bemeneti forrásfájlból lett kódolva.

## <a name="encoding-mxf-into-mp4---multibitrate-dynamic-packaging-enabled"></a><a id="MXF_to_MP4_with_dyn_packaging"></a>Az MXF kódolása MP4-re - multibitráta dinamikus csomagolás engedélyezve
Ez a forgatókönyv több bitráta MP4-fájlt hoz létre AAC kódolású hanggal egyetlen ből. MXF bemeneti fájl.

Ha egy többbitráta-eszköz kimenet használata az Azure Media Services által kínált dinamikus csomagolási funkciókkal együtt szükséges, több GOP-hoz igazított MP4-fájlra van szükség, amelyek mindegyike különböző bitrátát és felbontást hoz létre. Ehhez az [MXF kódolása egyetlen bitráta MP4 forgatókönyvbe](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) jó kiindulási pontot biztosít számunkra.

![Munkafolyamat indítása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Munkafolyamat indítása*

### <a name="adding-one-or-more-additional-mp4-outputs"></a><a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Egy vagy több további MP4-kimenet hozzáadása
Az így létrejövő Azure Media Services-eszközminden MP4-fájlja más bitrátát és felbontást támogat. Adjunk hozzá egy vagy több MP4 kimeneti fájlt a munkafolyamathoz.

Annak érdekében, hogy minden videó kódolónk azonos beállításokkal legyen létrehozva, a legkényelmesebb a már meglévő AVC Video Encoder másolása és a felbontás és a bitráta egy másik kombinációjának konfigurálása (adjunk hozzá egy 960 x 540-et 25 képkocka másodpercenként 2,5 Mbps sebességgel ). A meglévő kódoló másolásához másolja be a tervezőfelületre.

Csatlakoztassa a Media File Input tömörítetlen videokimeneti csapját az új AVC-összetevőhöz.

![Második AVC kódoló csatlakoztatva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Második AVC kódoló csatlakoztatva*

Most igazítsa az új AVC kódoló konfigurációját a 960x540 kimenethez 2,5 Mbps sebességgel. (Ehhez használja a "Kimeneti szélesség", "Kimeneti magasság" és "Bitráta (kbit/s)" tulajdonságokat.)

Tekintettel arra, hogy az így kapott eszközt az Azure Media Services dinamikus csomagolásával együtt szeretnénk használni, a streamelési végpontnak képesnek kell lennie az MP4-fájlokHLS/Fragmented MP4/DASH-töredékek generálására, amelyek pontosan egymáshoz igazodnak oly módon, hogy a különböző bitráták közötti váltást használó ügyfelek egyetlen egyenletes folyamatos video- és hangélményt kapjanak. Ahhoz, hogy ez megtörténjen, biztosítanunk kell, hogy mindkét AVC kódoló tulajdonságaiban a GOP ("képcsoport") mérete mindkét MP4 fájl esetében 2 másodpercre van állítva, ami a következőkkel történhet meg:

* a GOP size mode-ot rögzített GOP méretre és
* a kulcskeret időköze két másodpercig.
* a GOP IDR-ellenőrzést zárt gop-ra is állítsa annak biztosítása érdekében, hogy minden gop a saját, függőség nélkül álljon

A munkafolyamat könnyebb megértéséhez nevezze át az első AVC kódolót "AVC Video Encoder 640x360 1200 kbps" és a második AVC kódolóra: "AVC Video Encoder 960x540 2500 kbps".

Most adjunk hozzá egy második ISO MPEG-4 Multiplexer és egy második fájl kimenet. Csatlakoztassa a multiplexert az új AVC kódolóhoz, és győződjön meg arról, hogy a kimenet a fájlkimenetbe irányul. Ezután csatlakoztassa az AAC audio kódoló kimenetét az új multiplexer bemenetéhez. A fájlkimenet ezután csatlakoztatható a kimeneti fájlhoz/eszközcsomóponthoz, hogy hozzáadja a létrehozandó Media Services-eszközhöz.

![Második Muxer és fájlkimenet csatlakoztatva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Második Muxer és fájlkimenet csatlakoztatva*

Az Azure Media Services dinamikus csomagolásával való kompatibilitás érdekében konfigurálja a multiplexer adattömbüzemmódját gop-számra vagy időtartamra, és állítsa a gop-okat darabonként 1-re. (Ennek az alapértelmezettnek kell lennie.)

![Muxer akta üzemmódok](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer akta üzemmódok*

Megjegyzés: érdemes megismételni ezt a folyamatot bármely más bitráta és felbontás kombinációk szeretné hozzáadni az eszköz kimenet.

### <a name="configuring-the-file-output-names"></a><a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>A fájlkimeneti nevek konfigurálása
A kimeneti eszközhöz több fájl is hozzáadódik. Ez biztosítja, hogy győződjön meg arról, hogy a fájlnevek az egyes kimeneti fájlok eltérnek egymástól, és talán még alkalmazni fájlelnevezési konvenció, így világossá válik a fájl nevét, amit foglalkozik.

A fájlkimenet-elnevezés a tervezőben lévő kifejezéseken keresztül szabályozható. Nyissa meg az egyik Fájlkimenet összetevő tulajdonságablakát, és nyissa meg a Fájl tulajdonság kifejezésszerkesztőjét. Az első kimeneti fájl a következő kifejezésen keresztül lett konfigurálva (lásd az [MXF-ről egyetlen bitráta MP4 kimenetre](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)való áttekintési útmutatót):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Ez azt jelenti, hogy a fájlnevet két változó határozza meg: a beírandó kimeneti könyvtár és a forrásfájl alapneve. Az előbbi a munkafolyamat-gyökér tulajdonságaként van elérhető, és az utóbbit a bejövő fájl határozza meg. A kimeneti könyvtár, amit a helyi teszteléshez használ; ezt a tulajdonságot a munkafolyamat-motor felülbírálja, amikor a munkafolyamatot az Azure Media Services felhőalapú médiaprocesszora végrehajtja.
Ha mindkét kimeneti fájlunknak egységes kimeneti elnevezést szeretne adni, módosítsa az első fájlelnevezési kifejezést a következőre:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

a második pedig:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Közbenső tesztfuttatást hajtson végre, hogy mindkét MP4 kimeneti fájl megfelelően legyen létrehozva.

### <a name="adding-a-separate-audio-track"></a><a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Külön hangsáv hozzáadása
Amint azt később látni fogjuk, amikor létrehozunk egy .ism fájlt az MP4 kimeneti fájljainkhoz, egy csak hanggal rendelkező MP4 fájlt is meg kell követelnünk, mint az adaptív streaming hangsávját. A fájl létrehozásához adjon hozzá egy további muxert a munkafolyamathoz (ISO-MPEG-4 Multiplexer), és csatlakoztassa az AAC kódoló kimeneti csapját az 1-es sáv bemeneti csapjával.

![Audio muxer hozzáadva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Audio muxer hozzáadva*

Hozzon létre egy harmadik fájlkimeneti összetevőt a kimenő adatfolyam muxerből történő kimenetéhez, és konfigurálja a fájlelnevezési kifejezést a következőképpen:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Audio Muxer fájlkimenet létrehozása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Audio Muxer fájlkimenet létrehozása*

### <a name="adding-the-ism-smil-file"></a><a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>A hozzáadása a hoz ISM SMIL fájl
Ahhoz, hogy a dinamikus csomagolás az MP4 fájlokkal (és a csak hanggal rendelkező MP4-fájlokkal) kombinálva működjön a Media Services eszközben, szükségünk van egy jegyzékfájlra is (más néven "SMIL" fájlra: Szinkronizált multimédia integrációs nyelv). Ez a fájl jelzi az Azure Media Services számára, hogy milyen MP4-fájlok érhetők el a dinamikus csomagoláshoz, és melyek azok közül, amelyeket figyelembe kell venni a hangstreameléshez. Egy tipikus jegyzékfájl egy sor MP4 egyetlen audio stream így néz ki:

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

A .ism fájl tartalmaz egy kapcsoló nyilatkozatot, egy hivatkozást az egyes MP4 videofájlok és amellett, hogy azok is egy (vagy több) audio fájl hivatkozásokat egy MP4, amely csak tartalmazza a hang.

Az MP4-ek készletének jegyzékfájljának létrehozása az "AMS Manifest Writer" nevű összetevőn keresztül végezhető el. A használatához húzza a felületre, és csatlakoztassa a három fájlkimeneti összetevő "Complete írása" kimeneti tűit az AMS manifest writer bemenethez. Ezután győződjön meg arról, hogy az AMS manifest writer kimenetét a kimeneti fájlhoz/eszközhöz csatlakoztatja.

A többi fájlkimeneti összetevőhöz ugyanúgy konfigurálja az .ism fájlkimeneti nevet egy kifejezéssel:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

A kész munkafolyamat unk az alábbiakra hasonlít:

![Kész MXF a többbitrátás MP4 munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Kész MXF a többbitrátás MP4 munkafolyamat*

## <a name="encoding-mxf-into-multibitrate-mp4---enhanced-blueprint"></a><a id="MXF_to__multibitrate_MP4"></a>Az MXF kódolása többbitráta-mp4-be - továbbfejlesztett tervrajz
Az [előző munkafolyamat-forgatókönyvben](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) azt láttuk, hogy egyetlen MXF-bemeneti eszköz hogyan alakítható át többbitrájas MP4-fájlokkal, csak hanggal rendelkező MP4-fájllal és az Azure Media Services dinamikus csomagolásával együtt használható jegyzékfájllal.

Ez a forgatókönyv bemutatja, hogyan lehet néhány szempontot továbbjavítani és kényelmesebbé tenni.

### <a name="workflow-overview-to-enhance"></a><a id="MXF_to_multibitrate_MP4_overview"></a>A munkafolyamat áttekintése a továbbfejlesztéséhez
![Többbitorús MP4-munkafolyamat a](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Többbitorús MP4-munkafolyamat a*

### <a name="file-naming-conventions"></a><a id="MXF_to__multibitrate_MP4_file_naming"></a>Fájlelnevezési konvenciók
Az előző munkafolyamatban egy egyszerű kifejezést adtunk meg a kimeneti fájlnevek létrehozásának alapjaként. Van azonban némi ismétlődés: az összes egyes kimeneti fájl összetevő ily módon ilyen kifejezést adott meg.

Például az első videofájl fájlkimeneti összetevője ezzel a kifejezéssel van konfigurálva:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Míg a második kimeneti videó, van egy kifejezés, mint:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Nem lenne tisztább, kevésbé hibahajlamos, és sokkal kényelmesebb, ha tudnánk eltávolítani néhány ilyen párhuzamos, és a dolgok jobban konfigurálható helyett? Szerencsére tudunk: a tervező kifejezési képességei, valamint az egyéni tulajdonságok létrehozása a munkafolyamat gyökérén, további kényelmi réteget biztosítanak.

Tegyük fel, hogy az egyes MP4-fájlok bitsebességéből vezetjük a fájlnév konfigurációját. Ezeket a bitrátákat egy központi helyen (a grafikon gyökerénél) fogjuk konfigurálni, ahonnan a fájlnév generálásának konfigurálásához és meghajtójához hozzá fogjuk férni. Ehhez azzal kezdjük, hogy közzétesszük a bitráta tulajdonságot mindkét AVC kódolótól a munkafolyamat gyökeréig, hogy elérhetővé váljon mind a gyökérből, mind az AVC kódolókból. (Még ha két különböző helyen is megjelenik, csak egy mögöttes érték van.)

### <a name="publishing-component-properties-onto-the-workflow-root"></a><a id="MXF_to__multibitrate_MP4_publishing"></a>Összetevő-tulajdonságok közzététele a munkafolyamat gyökérén
Nyissa meg az első AVC kódolót, lépjen a Bitrate (kbps) tulajdonságra, és a legördülő menüben válassza a Közzététel lehetőséget.

![A bitráta tulajdonság közzététele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*A bitráta tulajdonság közzététele*

Konfigurálja úgy a közzétételi párbeszédpanelt, hogy a munkafolyamat-grafikon gyökerében tegyék közzé, a "video1bitrate" közzétett nevével és a "Video 1 bitráta" olvasható megjelenítendő nevével. Konfiguráljon egy "Streamelési bitátusok" nevű egyéni csoportnevet, és nyomja le a Közzététel lehetőséget.

![A bitráta tulajdonság közzététele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Közzétételi párbeszédpanel a bitráta tulajdonsághoz*

Ismételje meg ugyanezt a második AVC kódoló bitráta tulajdonságával, és nevezze el "video2bitrate" névnek "Video 2 Bitrate" megjelenítendő névvel, ugyanabban az egyéni csoportban, mint a "Streambitrate".

Ha most megvizsgáljuk a munkafolyamat gyökértulajdonságait, látni fogjuk az egyéni csoportot a két közzétett tulajdonsággal. Mindkettő a megfelelő AVC kódoló bitráta értékét tükrözi.

![Közzétett bitráta-kellékek a munkafolyamat gyökérén](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Amikor ezeket a tulajdonságokat kódból vagy kifejezésből szeretnénk elérni, ezt a következőket tehetjük:

* a legfelső szintű összetevőből származó inline kódból: node.getPropertyAsString('. /video1bitrate', null)
* kifejezésen belül: ${ROOT_video1bitrate}

Töltsük ki a "Streaming Bitrates" csoportot a hangsáv-bitrátánk közzétételével. Az AAC kódoló tulajdonságai között keresse meg a Bitrate beállítást, és válassza a Közzététel lehetőséget a mellette lévő legördülő menüből. Tegye közzé a diagram gyökerét az "audio1bitrate" névvel és az "Audio 1 Bitrate" megjelenítendő névvel a "Streaming Bitrates" egyéni csoportunkban.

![Közzétételi párbeszédpanel a hangbitráthoz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Közzétételi párbeszédpanel a hangbitráthoz*

![Eredő video-és audio kellékek a gyökér](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Eredő video-és audio kellékek a gyökér*

A három érték bármelyikének módosítása újrakonfigurálja és módosítja az adott összetevők értékeit, amelyekhez kapcsolódnak (és ahol közzéteszik őket).

### <a name="have-generated-output-file-names-rely-on-published-property-values"></a><a id="MXF_to__multibitrate_MP4_output_files"></a>A létrehozott kimeneti fájlnevek közzétett tulajdonságértékeken alapulnak
Ahelyett, hogy hardcoding a generált fájlnevek, most már módosíthatja a fájlnév kifejezés minden a File Output összetevők támaszkodni a bitráta tulajdonságait tettünk közzé a grafikon gyökér. Az első fájlkimenettől kezdve keresse meg a File tulajdonságot, és a következővel szerkesztse a kifejezést:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

A kifejezés különböző paraméterei úgy érhetők el és adhatók meg, hogy a kifejezésablakban a billentyűzeten a dollárjelet ütik. Az egyik rendelkezésre álló paraméterek a mi video1bitrate ingatlan, amit korábban közzétett.

![Paraméterek elérése egy kifejezésen belül](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Paraméterek elérése egy kifejezésen belül*

Tegye ugyanezt a fájl kimenet a második videó:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

és a csak hangfájl kimenetéhez:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Ha most megváltoztatjuk a video- vagy hangfájlok bitrátáját, a megfelelő kódoló újrakonfigurálható, és a bitráta-alapú fájlnév-konvenciót minden automatikusnak tiszteletben tartjuk.

## <a name="adding-thumbnails-to-multibitrate-mp4-output"></a><a id="thumbnails_to__multibitrate_MP4"></a>Bélyegképek hozzáadása többbitráta-MP4 kimenethez
Kezdve a munkafolyamat, amely létrehoz [egy multibitrate MP4 kimenet egy MXF bemenet](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), mi most vizsgálja hozzá miniatűrök a kimenethez.

### <a name="workflow-overview-to-add-thumbnails-to"></a><a id="thumbnails_to__multibitrate_MP4_overview"></a>A munkafolyamat áttekintése a bélyegképek hozzáadásához
![Többbitráta-mp4-munkafolyamat indítása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Többbitráta-mp4-munkafolyamat indítása*

### <a name="adding-jpg-encoding"></a><a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>JPG-kódolás hozzáadása
A szív a mi miniatűr generáció lesz a JPG Kódoló komponens, képes kimeneti JPG fájlokat.

![JPG kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG kódoló*

Nem tudjuk azonban közvetlenül csatlakoztatni a tömörítetlen video stream a Media File Input a JPG kódoló. Ehelyett arra számít, hogy kell adni az egyes keretek. Ezt meg tudjuk csinálni a Video Frame Gate komponens.

![Keretkapu csatlakoztatása a JPG kódolóhoz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*Keretkapu csatlakoztatása a JPG kódolóhoz*

A keret kapu egyszer minden olyan sok másodperc vagy képkocka lehetővé teszi a videó keret át. Az időköz és az időeltolás, amellyel ez történik, konfigurálható a tulajdonságokban.

![Videokeretkapu tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Videokeretkapu tulajdonságai*

Hozzunk létre egy miniatűr percenként beállításával a mód idő (másodperc) és az intervallum 60.

### <a name="dealing-with-color-space-conversion"></a><a id="thumbnails_to__multibitrate_MP4_color_space"></a>A színtér átalakításának kezelése
Bár logikusnak tűnik mind a keretkapu tömörítetlen videocsapjai, mind a Media File Input most már csatlakoztatható, figyelmeztetést kapnánk, ha ezt tennénk.

![Beviteli színtér hiba](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Beviteli színtér hiba*

Ez azért van, mert az a mód, ahogyan a színes információk képviselteti magát az eredeti nyers tömörítetlen video stream, érkező mi MXF, eltér attól, amit a JPG Kódoló vár. Pontosabban, az úgynevezett "színtér" az "RGB" vagy "szürkeárnyalatos" várhatóan áramlását. Ez azt jelenti, hogy a Video Frame Gate bejövő videostreamkell egy átalakítás tanusítsa a színtérben először.

Húzza rá a munkafolyamatra a Color Space Converter - Intel és csatlakoztassa a mi keret kapu.

![Színtér-konverter csatlakoztatása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Színtér-konverter csatlakoztatása*

A Tulajdonságok ablakban válassza ki a BGR 24 bejegyzést a Készlet listából.

### <a name="writing-the-thumbnails"></a><a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>A bélyegképek írása
Eltér a mi MP4 videó, a JPG kódoló komponens kimenetek több fájlt. Annak érdekében, hogy foglalkozzon ezzel, a Scene Search JPG File Writer komponens használható: úgy a bejövő JPG miniatűrök és írja ki őket, minden fájlnevet, hogy utótag egy másik számot. (A szám általában azt jelzi, hogy hány másodperc/egység van az adatfolyamban, amelyből a bélyegkép készült.)

![Bemutatkozik a Scene Search JPG File Writer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Bemutatkozik a Scene Search JPG File Writer*

A Kimeneti mappa elérési útja tulajdonság konfigurálása a következő kifejezéssel: ${ROOT_outputWriteDirectory}

és a Fájlnév előtag tulajdonság:

    ${ROOT_sourceFileBaseName}_thumb_

Az előtag határozza meg a miniatűrfájlok elnevezett nevét. Ezek egy számmal vannak ellátva, amely jelzi a hüvelykujj helyzetét a patakban.

![Jelenetkeresés JPG fájlíró tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Jelenetkeresés JPG fájlíró tulajdonságai*

Csatlakoztassa a Jelenetkeresés JPG fájlíróját a kimeneti fájlhoz/eszközcsomóponthoz.

### <a name="detecting-errors-in-a-workflow"></a><a id="thumbnails_to__multibitrate_MP4_errors"></a>Hibák észlelése egy munkafolyamatban
Csatlakoztassa a bemeneti a színtér konverter a nyers tömörítetlen video kimenet. Most hajtson végre egy helyi tesztfuttatást a munkafolyamathoz. Jó esély van arra, hogy a munkafolyamat hirtelen leáll, és piros körvonallal jelzi az összetevőt, amely hibát észlelt:

![Színtér-konverter hiba](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Színtér-konverter hiba*

Kattintson a kis piros "E" ikonra a térátalakító összetevő jobb felső sarkában, hogy lássa, mi az oka a kódolási kísérlet sikertelen.

![Színtér-konverter hibapárbeszédpanel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Színtér-konverter hibapárbeszédpanel*

Kiderül, mint látható, hogy a bejövő színtér szabvány a színtér átalakító kell rec601 a mi kért átalakítása YUV RGB. Úgy tűnik, a patak nem jelzi a rec601-et. (A Rec 601 szabvány az átlapolt analóg videojelek digitális videoformában történő kódolására. Olyan aktív régiót határoz meg, amely soronként 720 fénysűrűségmintát és 360 kromatikus mintát tartalmaz. A színkódrendszer ycbcr 4:2:2 néven ismert.)

Ennek megoldásához az adatfolyamunk metaadatai között jelezzük, hogy rec601 tartalommal van dolgunk. Ehhez egy Video Data Type Updater összetevőt fogunk használni, amelyet a nyers forrás és a színtérkonverziós összetevő között helyezünk el. Ez az adattípus-frissítő lehetővé teszi bizonyos videoadat-típustulajdonságok manuális frissítését. Állítsa be a "Rec 601" színtérszabvány jelzésére. Ennek hatására a Video Data Type Updater megjelöli az adatfolyamot a "Rec 601" színtérrel, ha még nem volt megadva színtér. (Nem bírálja felül a meglévő metaadatokat, kivéve, ha a Felülbírálás jelölőnégyzet be van jelölve.)

![A színtérszabvány frissítése az adattípus-frissítőn](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*A színtérszabvány frissítése az adattípus-frissítőn*

### <a name="finished-workflow"></a><a id="thumbnails_to__multibitrate_MP4_finish"></a>Munkafolyamat befejeződött
Most, hogy a munkafolyamat befejeződött, tegyen meg egy újabb próbafuttatást, hogy lássa, hogy halad.

![Folyamatkész munkafolyamat több mp4-es kimenethez miniatűrökkel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Folyamatkész munkafolyamat több mp4-es kimenethez miniatűrökkel*

## <a name="time-based-trimming-of-multibitrate-mp4-output"></a><a id="time_based_trim"></a>Többbitráta-mp4 kimenet időalapú levágása
Kezdve a munkafolyamat, amely létrehoz [egy multibitrate MP4 kimenet egy MXF bemenet](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), mi most vizsgálja a vágás a forrás videó alapján időbélyegek.

### <a name="workflow-overview-to-start-adding-trimming-to"></a><a id="time_based_trim_start"></a>Munkafolyamat áttekintése a vágás hozzáadásának megkezdéséhez
![Munkafolyamat indítása vágás hozzáadásához](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Munkafolyamat indítása vágás hozzáadásához*

### <a name="using-the-stream-trimmer"></a><a id="time_based_trim_use_stream_trimmer"></a>A Stream Trimmer használata
A Stream Trimmer összetevő lehetővé teszi a bemeneti adatfolyam-alap kezdetét és befejezését az időzítési adatokon (másodperc, perc, ...). A vágó nem támogatja a keretalapú vágást.

![Stream Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Stream Trimmer*

Ahelyett, hogy az AVC kódolókat és a hangszóró pozícióhozzárendeltt közvetlenül a Media File Inputhoz kapcsolnánk, a streamtrimmer között helyezzük el. (Az egyik a videojelhez, a egy pedig az áthatárolt hangjelhez.)

![Stream trimmer belehelyezheti a kettő közé](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Stream trimmer belehelyezheti a kettő közé*

Állítsuk be a szegélyvágót úgy, hogy csak 15 másodperc és 60 másodperc között dolgozzuk fel a videót és a hangot.

Nyissa meg a Video Stream Trimmer tulajdonságait, és konfigurálja a Kezdési időpont (15 s) és a Befejezési idő (60 s) tulajdonságát is. Annak érdekében, hogy mind a hang- és videovágónk mindig ugyanazokhoz a kezdő és záró értékekhez legyen beállítva, azokat a munkafolyamat gyökerében tesszük közzé.

![Kezdési időpont tulajdonság közzététele a Stream Trimmer ből](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Kezdési időpont tulajdonság közzététele a Stream Trimmer ből*

![Tulajdonság közzététele párbeszédpanel a kezdési időponthoz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Tulajdonság közzététele párbeszédpanel a kezdési időponthoz*

![Tulajdonság közzététele párbeszédpanel befejezési időponthoz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Tulajdonság közzététele párbeszédpanel befejezési időponthoz*

Ha most megvizsgáljuk a munkafolyamat gyökerét, mindkét tulajdonság szépen megjelenik és konfigurálható.

![A közzétett tulajdonságok a gyökéren érhetők el](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*A közzétett tulajdonságok a gyökéren érhetők el*

Most nyissa meg a vágási tulajdonságokat az audio trimmerből, és konfigurálja a kezdési és befejezési időpontokat egy olyan kifejezéssel, amely a munkafolyamat gyökerén közzétett tulajdonságokra hivatkozik.

A hangvágás kezdési időpontjához:

    ${ROOT_TrimmingStartTime}

és a végső időpontban:

    ${ROOT_TrimmingEndTime}

### <a name="finished-workflow"></a><a id="time_based_trim_finish"></a>Munkafolyamat befejeződött
![Munkafolyamat befejeződött](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Munkafolyamat befejeződött*

## <a name="introducing-the-scripted-component"></a><a id="scripting"></a>A parancsfájlalapú összetevő bemutatása
A parancsfájlalapú összetevők tetszőleges parancsfájlokat hajthatnak végre a munkafolyamat végrehajtási fázisaiban. Négy különböző parancsfájl hajtható végre, mindegyik egyedi jellemzőkkel rendelkezik, és saját helye van a munkafolyamat életciklusában:

* **commandScript**
* **megvalósításI**
* **processInputScript**
* **lifeCycleScript**

A parancsfájlalapú összetevő dokumentációja részletesebben ismerteti a fentiek mindegyikét. A [következő szakaszban a](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim) **realizeScript** parancsfájl-összetevő segítségével létre egy cliplist xml menet közben, amikor a munkafolyamat elindul. Ezt a parancsfájlt az összetevő beállítása során hívják meg, amely csak egyszer történik meg az életciklusában.

### <a name="scripting-within-a-workflow-hello-world"></a><a id="scripting_hello_world"></a>Parancsfájlok futtatása egy munkafolyamaton belül: hello world
Húzzon egy parancsfájlalapú összetevőt a tervezőfelületre, és nevezze át (például "SetClipListXML").

![Parancsfájlalapú összetevő hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Parancsfájlalapú összetevő hozzáadása*

A parancsfájlalapú összetevő tulajdonságainak vizsgálatakor a négy különböző parancsfájltípus jelenik meg, amelyek mindegyike más-más parancsfájlra konfigurálható.

![Parancsfájlalapú összetevő tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Parancsfájlalapú összetevő tulajdonságai*

Törölje a processInputScript-et, és nyissa meg a megvalósító parancsfájl szerkesztőjét. Most már készen állunk a parancsfájlok futtatására.

A szkriptek groovy nyelven íródott, egy dinamikusan összeállított script nyelv a Java platformhoz, amely megtartja a Java-val való kompatibilitást. Valójában, a legtöbb Java kód érvényes Groovy kódot.

Írjunk egy egyszerű hello világ klassz script keretében a mi realizeScript. Írja be a következőt a szerkesztőbe:

    node.log("hello world");

Most hajtson végre egy helyi tesztfutást. A futtatás után vizsgálja meg (a Parancsfájlalapú összetevő Rendszer lapján keresztül) a Naplók tulajdonságot.

![Hello világ log kimenet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hello világ log kimenet*

A csomópont objektum hívjuk a napló metódust, utal a jelenlegi "csomópont", vagy az összetevő vagyunk scripting belül. Minden összetevő, mint olyan, képes kimeneti naplózási adatok, elérhető a rendszer lapon. Ebben az esetben a "hello world" karakterláncot adjuk ki. Fontos megérteni itt az, hogy ez bizonyítani tudja, hogy felbecsülhetetlen hibakereső eszköz, amely az Ön számára betekintést, amit a szkript valójában csinál.

A parancsfájl-környezeten belül más összetevők tulajdonságaihoz is hozzáférhetünk. Próbálja ki a következőt:

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

Naplóablakunk a következőket mutatja:

![Naplókimenet a csomópontelérési utak eléréséhez](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Naplókimenet a csomópontelérési utak eléréséhez*

## <a name="frame-based-trimming-of-multibitrate-mp4-output"></a><a id="frame_based_trim"></a>Többbitráta MP4 kimenet keretalapú vágása
Kezdve a munkafolyamat, amely létrehoz [egy multibitrate MP4 kimenet egy MXF bemenet](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), mi most vizsgálja a vágás a forrás videó alapján frame counts.

### <a name="blueprint-overview-to-start-adding-trimming-to"></a><a id="frame_based_trim_start"></a>A tervezet áttekintése a vágás hozzáadásának megkezdéséhez
![Munkafolyamat a vágás hozzáadásának megkezdéséhez](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Munkafolyamat a vágás hozzáadásának megkezdéséhez*

### <a name="using-the-clip-list-xml"></a><a id="frame_based_trim_clip_list"></a>A Clip List XML használata
Az összes korábbi munkafolyamat-oktatóanyagban a Media File Input összetevőt használtuk videobemeneti forrásként. Ebben a konkrét esetben azonban a Clip List Source összetevőt fogjuk használni. Nem ez lehet az előnyben részesített munkamódszer; csak akkor használja a kliplista forrását, ha erre valódi oka van (például a következő esetben, amikor a kliplista vágási képességeit használjuk).

Ha át szeretne váltani a médiafájl-bemenetről a Kliplista forrására, húzza a Kliplista forrásának összetevőt a tervezőfelületre, és csatlakoztassa a Kliplista XML-pint a munkafolyamat-tervező Kliplista XML-csomópontjéhez. Ez feltölti a Clip List Source kimeneti csapok szerint a mi bemeneti videó. Most csatlakoztassa a tömörítetlen video- és tömörítetlen hangtűket a kliplista forrásából a megfelelő AVC kódolókhoz és audiostream interleaverhez. Most távolítsa el a médiafájl bemenetét.

![A médiafájl bemenetének lecserélése a kliplista forrására](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*A médiafájl bemenetének lecserélése a kliplista forrására*

A Kliplista forrása összetevő "Kliplista XML"-t vesz fel. Amikor kiválasztja a helyileg tesztelő forrásfájlt, a kliplista xml-je automatikusan ki van töltve.

![Automatikusan kitöltött Kliplista XML tulajdonsága](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Automatikusan kitöltött Kliplista XML tulajdonsága*

Keresi egy kicsit közelebb az xml, ez hogyan néz ki:

![Kliplista szerkesztése párbeszédpanel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Kliplista szerkesztése párbeszédpanel*

Ez azonban nem tükrözi a kliplista xml-jének képességeit. Az egyik lehetőség van, hogy adjunk egy "Trim" elem alatt mind a videó és audió forrás, mint ez:

![Vágási elem hozzáadása a kliplistához](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Vágási elem hozzáadása a kliplistához*

Ha a fentihez hasonlóan módosítja a kliplista xml-jét, és helyi tesztfuttatást hajt végre, a videó 10 és 20 másodperc között megfelelően lesz vágva a videóban.

Ellentétben azzal, hogy mi történik, ha egy helyi futtatást végez, ugyanez a cliplist xml nem lenne ugyanaz a hatása, ha az Azure Media Services-ben futó munkafolyamatban alkalmazza. Amikor az Azure Premium Encoder elindul, a cliplist xml jön létre minden alkalommal újra, a bemeneti fájl alapján a kódolási feladat kapott. Ez azt jelenti, hogy az xml-en végzett módosításokat sajnos felülbírálják.

A kódolási feladat indításakor törölt cliplist XML-fájl ellen menet közben regenerálhatjuk a munkafolyamat megkezdése után. Az ilyen egyéni műveleteket az úgynevezett "parancsfájlalapú összetevőn" keresztül lehet elvégezni. További információ: [A parancsfájlalapú összetevő bemutatása.](media-services-media-encoder-premium-workflow-tutorials.md#scripting)

Húzza a parancsfájlalapú összetevőt a tervezőfelületre, és nevezze át "SetClipListXML" névre.

![Parancsfájlalapú összetevő hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Parancsfájlalapú összetevő hozzáadása*

A parancsfájlalapú összetevő tulajdonságainak vizsgálatakor a négy különböző parancsfájltípus jelenik meg, amelyek mindegyike más-más parancsfájlra konfigurálható.

![Parancsfájlalapú összetevő tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Parancsfájlalapú összetevő tulajdonságai*

### <a name="modifying-the-clip-list-from-a-scripted-component"></a><a id="frame_based_trim_modify_clip_list"></a>A kliplista módosítása parancsfájlalapú összetevőből
Mielőtt átírnánk a munkafolyamat indításakor létrehozott cliplist XML-t, hozzá kell férnünk a cliplist XML tulajdonsághoz és tartalomhoz. Meg tudjuk csinálni, mint ez:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![A bejövő kliplista naplózása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*A bejövő kliplista naplózása*

Először is meg kell határozni, hogy melyik ponttól, amely pontig szeretnénk vágni a videót. Ahhoz, hogy ez kényelmes legyen a munkafolyamat kevésbé technikai jellegű felhasználója számára, tegyen közzé két tulajdonságot a diagram gyökerében. Ehhez kattintson a jobb gombbal a tervezőfelületre, és válassza a "Tulajdonság hozzáadása" parancsot:

* Első tulajdonság: "ClippingTimeStart" típusú: "TIMECODE"
* Második tulajdonság: "ClippingTimeEnd" típusú: "TIMECODE"

![Tulajdonság hozzáadása párbeszédpanel a vágás kezdési időpontjához](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Tulajdonság hozzáadása párbeszédpanel a vágás kezdési időpontjához*

![Közzétett vágási időkellékek a munkafolyamat gyökérén](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Közzétett vágási időkellékek a munkafolyamat gyökérén*

Mindkét tulajdonságkonfigurálása megfelelő értékre:

![A vágókivágás kezdő és záró tulajdonságainak konfigurálása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*A vágókivágás kezdő és záró tulajdonságainak konfigurálása*

A szkriptünkből hozzáférhetünk mindkét tulajdonsághoz, így:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![A vágás kezdetét és végét megjelenítő naplóablak](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*A vágás kezdetét és végét megjelenítő naplóablak*

Elemezze az időkódkarakterláncokat egy kényelmesebb űrlapba, egy egyszerű reguláris kifejezés használatával:

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

![Naplóablak az elemzéses időkód kimenetével](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Naplóablak az elemzéses időkód kimenetével*

Ezzel az információval most már módosíthatjuk a cliplist xml-t, hogy tükrözze a film kívánt képkocka-pontos vágásának kezdési és befejezési idejét.

![Trim elemek hozzáadásához szükséges parancsfájlkód](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Trim elemek hozzáadásához szükséges parancsfájlkód*

Ez történt a szokásos karakterlánc-manipulációs műveletek. Az eredményül kapott módosított clip list list xml visszakerül a munkafolyamat gyökérének clipListXML tulajdonságába a "setProperty" metóduson keresztül. A naplóablak egy másik próbafuttatás után a következőket mutatja:

![Az eredményül kapott kliplista naplózása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Az eredményül kapott kliplista naplózása*

Tesztelje, hogy hogyan lett levágva a video- és hangadatfolyamokat. Mivel akkor nem több, mint egy teszt-run különböző értékeket a vágási pontok, észre fogod venni, hogy ezek nem veszik figyelembe azonban! Ennek az az oka, hogy a tervező, ellentétben az Azure-futásidejű, nem bírálja felül a cliplist xml minden futtatáskor. Ez azt jelenti, hogy csak az első alkalommal állította be és ki a pontokat, hatására`clipListXML.indexOf("<trim>") == -1`az xml átalakítani, minden más alkalommal, a mi őr záradék (if( )) megakadályozza, hogy a munkafolyamat hozzá egy másik trim elem, ha már van egy jelen.

Annak érdekében, hogy a munkafolyamatunk kényelmes legyen a helyi teszteléshez, a legjobb, ha hozzáadunk néhány házőri kódot, amely ellenőrzi, hogy egy trim elem már jelen volt-e. Ha igen, eltávolíthatjuk, mielőtt folytatnánk az xml módosításával az új értékekkel. Ahelyett, hogy egyszerű karakterlánc-manipulációk, ez valószínűleg biztonságosabb, hogy ezt a valós XML objektum modell elemzése.

Mielőtt azonban hozzátudnánk adni egy ilyen kódot, először számos importálási utasítást kell hozzáadnunk a szkriptünk elején:

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

Ezt követően hozzáadhatjuk a szükséges tisztítási kódot:

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

Ez a kód éppen azon a pont felett megy, ahol hozzáadjuk a vágási elemeket a cliplist xml-hez.

Ezen a ponton, tudjuk futtatni és módosítani a munkafolyamatot, amennyit csak akar, miközben a változások at alkalmazott valaha alkalommal.    

### <a name="adding-a-clippingenabled-convenience-property"></a><a id="frame_based_trim_clippingenabled_prop"></a>ClippingEnabled kényelmi tulajdonság hozzáadása
Mivel lehet, hogy nem mindig szeretné, hogy a vágás megtörténjen, fejezzük be a munkafolyamatot egy kényelmes logikai jelző hozzáadásával, amely jelzi, hogy engedélyezni akarjuk-e a vágást / vágást.

Mint korábban, tegyen közzé egy új tulajdonságot a "CLIPPINGEnabled" típusú "ClippingEnabled" nevű munkafolyamat gyökerében.

![Közzétett egy tulajdonságot a vágás engedélyezéséhez](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Közzétett egy tulajdonságot a vágás engedélyezéséhez*

Az alábbi egyszerű őr záradékkal ellenőrizhetjük, hogy szükség van-e vágásra, és eldönthetjük, hogy a kliplistánkat módosítani kell-e vagy sem.

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
[Prémium kódolás bemutatása az Azure Media Services szolgáltatásban](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Prémium kódolás használata az Azure Media Servicesszolgáltatásban](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Igény szerinti tartalom kódolása az Azure Media Szolgáltatással](media-services-encode-asset.md#media-encoder-premium-workflow)

[A Media Encoder Premium munkafolyamat formátumai és kodekei](media-services-premium-workflow-encoder-formats.md)

[Munkafolyamat-fájlok mintaa](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services Explorer eszköz](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
