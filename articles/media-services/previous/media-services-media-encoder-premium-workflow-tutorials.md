---
title: Avanced Media Encoder Premium munkafolyamat-oktatóanyagok
description: Ez a dokumentum azt mutatják be, hogyan hajthat végre a Media Encoder Premium munkafolyamat speciális feladatokat, és hogyan hozhat létre komplex munkafolyamatok a munkafolyamat-tervezővel útmutatókat tartalmazza.
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
ms.date: 10/30/2018
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: 79b2aa22307e4742d00d9422535d52c80485e2db
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249034"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>Media Encoder Premium munkafolyamat speciális oktatóanyagok
## <a name="overview"></a>Áttekintés
Ez a dokumentum tartalmaz, amelyek bemutatják, hogyan szabhatja testre a munkafolyamatok forgatókönyvek **munkafolyamat-tervezővel**. A tényleges munkafolyamatfájlokat annak [Itt](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

## <a name="toc"></a>TARTALOMJEGYZÉK
A következő témákat tárgyalja:

* [Kódolási MXF egy egyféle sávszélességű MP4-be](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [Új munkamenet indítása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [A Media bemeneti fájllal](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [Adatfolyamok vizsgálata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [Egy videó kódolót hozzáadása. MP4-fájl létrehozása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [Az audio-adatfolyam kódolása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [Hang és videó-adatfolyamokat multiplexálási egy MP4-tárolóba](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [Az MP4-fájl írása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [A kimeneti fájlt hoz létre egy Media Services-eszköz](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [A befejezett munkafolyamat helyi tesztelése](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [Több MP4 - fájlnak az MXF kódolás engedélyezve van a dinamikus csomagolás](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [Egy vagy több további MP4 kimenetek hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [A fájl kimeneti név beállítása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [Egy külön lejátszása hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * [A "ISM" SMIL fájl hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [Kódolási MXF, több MP4 - továbbfejlesztett tervezet](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * [A munkafolyamat áttekintése növelése érdekében](#workflow-overview-to-enhance)
  * [A fájlok elnevezési konvenciói](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [A munkafolyamat legfelső szintű alakzatot közzétételi összetevő tulajdonságai](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [Kimeneti fájl nevek támaszkodjon közzétett tulajdonságértékek hoztak létre.](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [A miniatűrök ad hozzá több MP4-kimenetre](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * [Miniatűrök hozzáadása a munkafolyamat áttekintése](#workflow-overview-to-add-thumbnails-to)
  * [Hozzáadás a JPG-kódolás](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [Szín terület átalakítás többé vesződnie a sérült](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [A miniatűrök írása](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [Hibák észlelése, a munkafolyamat](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [A munkafolyamat befejeződött](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [Időalapú levágási több MP4-kimenetre](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [Kezdje el hozzáadni a tisztítás, a munkafolyamat áttekintése](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [A Stream vágó használatával](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [A munkafolyamat befejeződött](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [A parancsfájlalapú összetevő bemutatása](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [Egy munkafolyamaton belül Scripting: hello world](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [Keret-alapú levágási több MP4-kimenetre](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [Kezdje el hozzáadni a tisztítás a tervezet áttekintése](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [A videoklip lista XML használata](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [A parancsfájlalapú összetevőtől klip listájának módosítása](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [Kényelmi ClippingEnabled tulajdonság hozzáadása](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>Kódolási MXF egy egyféle sávszélességű MP4-be
Ez a szakasz bemutatja, hogyan hozhat létre egy egyféle sávszélességű. Az AAC-HE MP4-fájl kódolású hangjának egy. MXF bemeneti fájl.

### <a id="MXF_to_MP4_start_new"></a>Új munkamenet indítása
Nyissa meg a munkafolyamat-Tervező, és válassza ki a fájl > Új munkaterület > átkódolása tervezet

Az új munkafolyamat három elemet jeleníti meg:

* Elsődleges forrásfájl
* Klip lista XML
* Kimeneti fájl vagy eszköz  

![Új kódolási munkafolyamatokat.](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Új kódolási munkafolyamatokat.*

### <a id="MXF_to_MP4_with_file_input"></a>A Media bemeneti fájllal
Annak érdekében, hogy fogadja a bemeneti fájlt, egy kiindulópontja egy adathordozó fájl bemeneti összetevő hozzáadása. Összetevő hozzáadása a munkafolyamat, a tárház keresőmezőbe keresése, és a kívánt bejegyzést a Tervező ablaktáblán húzzon. A művelet az adathordozó fájl bemeneti esetében, és csatlakozhat az elsődleges forrásfájl összetevőt a fájlnév bemeneti PIN-kód az adathordozó fájl bemeneti.

![Csatlakoztatott médiakiszolgálók a bemeneti fájl](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Csatlakoztatott médiakiszolgálók a bemeneti fájl*

Egy egyéni munkafolyamat tervezése során használandó megfelelő mintafájl kezdetben azonosításához. Ehhez kattintson a Tervező ablak háttérben, és keresse meg a elsődleges forrásfájl tulajdonság a jobb oldali tulajdonság panelen. A mappa ikonra, és válassza ki a kívánt fájlt a munkafolyamat tesztelése. A Media bemeneti fájllal összetevő megvizsgálja a fájlt, és tölti fel a kimeneti PIN-kódok, hogy a mintafájlt, ellenőrzött részleteit.

![Feltöltött médiafájl-bemenet](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Feltöltött médiafájl-bemenet*

Most, hogy a bemeneti adatok feltöltése a következő lépés az kódolási beállítások kimeneti beállítása. Hasonló a elsődleges forrásfájl lett konfigurálva, mostantól beállíthatja a kimeneti mappa változó tulajdonságot használja, ez alatt.

![Konfigurált bemeneti és kimeneti tulajdonságok](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Konfigurált bemeneti és kimeneti tulajdonságok*

### <a id="MXF_to_MP4_streams"></a>Adatfolyamok vizsgálata
Gyakran ez rendelkezik a kívánt tudni, hogy hogyan a stream néz ki a munkafolyamat áthaladó. Vizsgálja meg a stream a munkafolyamat bármely pontján, kattintson egy kimeneti vagy valamelyik összetevő a bemeneti PIN-kódot. Ebben az esetben próbálkozzon a tömörítetlen videó kimeneti PIN-kódot, a Media fájl bemeneti kattint. Egy párbeszédpanel megnyílik, amely lehetővé teszi, hogy a kimenő videó vizsgálhatja meg.

![A videó tömörítetlen kimeneti PIN-kód vizsgálatával](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*A videó tömörítetlen kimeneti PIN-kód vizsgálatával*

Ebben az esetben azt mutatja, hogy a videó tartalmaz egy 1920 × 1080 képpont ráfordítás 24 képkockák másodpercenkénti a 4:2:2 mintavételi majdnem 2 perces videót.

### <a id="MXF_to_MP4_file_generation"></a>Egy videó kódolót hozzáadása. MP4-fájl létrehozása
Most egy tömörítetlen videó és a PIN-kódok érhetők el a több tömörítetlen hang kimeneti használja a Media bemeneti fájllal. Annak érdekében, hogy a bejövő videó kódolása, egy kódolási összetevő hozzá kell adnia a munkafolyamat - ebben az esetben előállítása érdekében. MP4-fájlokat.

A video-adatfolyamot H.264 kódolásához, adja hozzá a AVC Videókódoló a Tervező felületére. Ez az összetevő-Kibontás video-adatfolyamot bemenetként fogadja, és biztosít egy AVC tömörített video-adatfolyamot a kimenet a PIN-kódját.

![Csatlakoztatott AVC kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Csatlakoztatott AVC kódoló*

A tulajdonságait határozza meg, hogyan történik a kódolás pontosan. Nézzük meg, néhány fontosabb beállítás található:

* Kimeneti szélességének és magasságának kimeneti: azt határozza meg, a kódolt videó felbontása. 640 x 360 ebben az esetben a megfelelő beállítást.
* Képkockasebesség: Ha passthrough értéke lesz csak fogadja el a forrás képkockasebessége, lehetőség azonban felülbírálja ezt. Az ilyen képkockasebesség átalakítás nem mozgásban lévő adatoknak egyaránt-kompenzációt.
* Profil és a szintje: AVC profil és szint határozza meg. Kényelmesen részletes tájékoztatást nyújt a különböző szintek és a profilok, kattintson a AVC videó kódoló összetevő található kérdőjel ikonra, és részletesen szintjének jelennek meg a következő súgóoldalt. Ebben a példában használja a központi profil 3.2-es (alapértelmezett) szintjén.
* Ellenőrzési mód és az átviteli sebesség (KB/s): Ebben a forgatókönyvben egy állandó kimeneti, 1200-as KB/s sávszélességű (CBR) optimalizált
* Videó formátum: (ügyféloldali információkat, amelyek lehetnek a dekóder javíthatják a megjelenített használja, de nem elengedhetetlen megfelelően dekódolandó) H.264 adatfolyamba írt lekérdezi VUI (videó a használhatósági információk) kapcsolatos információkat tartalmazza:
* NTSC (jellemző, USA-beli vagy japán használatával 30 képkocka/s)
* PAL (Európa, 25 fps használatával jellemző)
* Képcsoporttal mérete mód: a jelen a lezárt GOPs 2 másodperc kulcs időközt Képcsoporttal rögzített méretű beállítása. A beállítás 2 másodperces biztosítja, hogy a dinamikus csomagolás révén az Azure Media Services kompatibilitást biztosít.

Hírcsatorna a AVC kódoló, csatlakozzon a tömörítetlen videó kimeneti PIN-kód az adathordozó fájl bemeneti összetevőtől a tömörítetlen videó bemeneti PIN-kódot a AVC kódoló.

![Csatlakoztatott AVC kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Csatlakoztatott AVC fő kódoló*

### <a id="MXF_to_MP4_audio"></a>Az audio-adatfolyam kódolása
Ezen a ponton az eredeti tömörítetlen hang stream továbbra is tömörítendő van szüksége. A hang Stream tömörítési AAC Encoder (Dolby) összetevő hozzáadása a munkafolyamatnak.

![Csatlakoztatott AVC kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Csatlakoztatott AAC-kódoló*

Most már van egy kompatibilitási: van csak egyetlen tömörítetlen hang bemeneti PIN-kódot az AAC-kódoló, miközben valószínűleg több, mint az adathordozó fájl bemeneti lesz két különböző tömörítetlen audiostreamek lejátszásával érhető el: egy, a bal oldali hang csatorna, egy pedig a jobb oldalon. (Ha legyen hang foglalkozó, amely a hat csatornák.) Így nincs lehetőség közvetlenül kapcsolódni a hanganyag a forrás-adathordozó bemeneti fájllal a AAC hang kódoló. Az AAC-összetevő vár egy úgynevezett "kihagyásos" audio-adatfolyam: egyetlen streammel, amely rendelkezik a bal és a megfelelő csatornák közbeékeléses egymással. Egyszer tudjuk, hogy a forrás-adathordozó fájlból, hogy hangsáv-e a milyen helyzetben a forrás, hogy hozhat létre ilyen összefűzéses audio-adatfolyam a megfelelően hozzárendelt speaker a beosztások bal és jobb.

Először egy szeretné, hozzon létre egy kihagyásos stream a szükséges forrás hang csatornákat. A hang Stream Interleaver összetevő kezeli ez velünk a kapcsolatot. Adja hozzá a munkafolyamatot, és a hang kimenetek csatlakoztatása a Media fájl bemeneti bele.

![Hang Stream Interleaver csatlakoztatva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Hang Stream Interleaver csatlakoztatva*

Most, hogy egy kihagyásos audio-adatfolyam, hogy még nem adott meg, a bal vagy jobb speaker pozíciók rendelhet. Adja meg, hogy kiaknázhatja a beosztás Speaker hozzárendelő.

![A Speaker pozíció hozzárendelő hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*A Speaker pozíció hozzárendelő hozzáadása*

A Speaker pozíció hozzárendelő használatra konfigurálja egy sztereó bemeneti Stream egy kódoló beállított szűrő az "Egyéni" és a csatorna Előbeállítást nevű, "(L, R) 2.0." (Ez hozzárendeli a bal oldali speaker állapot, a channel 1 és jobb speaker csatornán 2.)

A beosztás Speaker hozzárendelő kimenete csatlakozhat a AAC kódoló a bemeneti. Ezután ossza meg a 2.0-val egy"(L, R)" működéséhez AAC kódoló csatorna készletet, így az tudni fogja sztereó hang bemeneteként foglalkozni.

### <a id="MXF_to_MP4_audio_and_fideo"></a>Hang és videó-adatfolyamokat multiplexálási egy MP4-tárolóba
A AVC megadott kódolt video-adatfolyamot, és az AAC kódolású audio-adatfolyam, be is rögzíthetők egy. MP4-tároló. A különböző Streamek keverési be egyetlen egy folyamat neve "multiplexálási" (vagy a "muxing"). Ebben az esetben mi vagyunk kihagyásos a hang- és a video-adatfolyamokat egyetlen egységes. MP4-csomag. Az összetevőt, ez a koordináló egy. MP4-tároló neve Multiplexer ISO MPEG-4. Vegyen fel egy, a Tervező felületére, és csatlakozzon a AVC videó kódoló és a az AAC-kódoló bemenete.

![Csatlakoztatott MPEG4 Multiplexer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*Csatlakoztatott MPEG4 Multiplexer*

### <a id="MXF_to_MP4_writing_mp4"></a>Az MP4-fájl írása
Kimeneti fájl írásakor, a kimeneti fájl összetevő szolgál. Csatlakozhassunk ezt az ISO MPEG-4 Multiplexer kimenetét, hogy a kimeneti írt lekérdezi a lemezre. Ehhez a tároló (MPEG-4) kimeneti PIN-kód csatlakozhat a kimeneti fájl bemeneti írási PIN kódját.

![Kimeneti fájl csatlakoztatva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Kimeneti fájl csatlakoztatva*

A használt fájlnevet a fájl tulajdonság határozza meg. A tulajdonság a megadott érték nem változtatható lehetnek, nagy valószínűséggel szeretne helyette állítsa kifejezésből.

Szeretné, hogy a munkafolyamat automatikusan meghatározni a kimeneti fájlt a name tulajdonság egy kifejezésből, kattintson a gombra (mappa ikon) mellett látható a fájl neve melletti. A legördülő menüből válassza ki "Kifejezés." Ekkor megjelenik a kifejezésszerkesztőbe. Először törölje a szerkesztőben a tartalmát.

![Üres Kifejezésszerkesztőbe](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Üres Kifejezésszerkesztőbe*

A kifejezésszerkesztőbe lehetővé teszi, hogy adjon meg egy szöveges értéket, egy vagy több változót. Változók dollárjelet kezdődik. Eléri a $ kulcsot, mert a szerkesztő megjeleníti az elérhető változók közül választhat a legördülő listából. Ebben az esetben használjuk a kimeneti könyvtár változót és az alap, a bemeneti fájl neve változó:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![A Kifejezésszerkesztőbe ki kitöltve](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*A Kifejezésszerkesztőbe ki kitöltve*

> [!NOTE]
> Annak érdekében, hogy az Azure-ban a kódolási feladat kimeneti fájl megtekintéséhez meg kell adnia egy értéket a kifejezésszerkesztőbe.
>
>

Lenyomásával az OK gombra a kifejezés győződjön meg arról, ha a tulajdonságának ablakban előnézetét jeleníti meg a fájl tulajdonság feloldása ezen a ponton a időben történő értéket.

![Fájl kifejezés, oldja fel a dir parancs kimenete](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*Fájl kifejezés, oldja fel a dir parancs kimenete*

### <a id="MXF_to_MP4_asset_from_output"></a>A kimeneti fájlt hoz létre egy Media Services-eszköz
Bár a Microsoft írásos engedélye szükséges. a kimeneti fájl MP4, továbbra is jelzi, hogy ez a fájl tartozik-e a kimeneti adategységen, mely a media services állít elő, ez a munkafolyamat célpéldányának kell. Ennek érdekében a kimeneti fájl vagy eszköz csomópont a munkafolyamat vásznon szolgál. Ez a csomópont minden bejövő fájlok győződjön meg az eredményül kapott Azure Media Services-objektum részét.

A kimeneti fájl összetevő csatlakozhat a kimeneti fájl vagy eszköz összetevő, a munkafolyamat befejezéséhez.

![A munkafolyamat befejeződött](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*A munkafolyamat befejeződött*

### <a id="MXF_to_MP4_test"></a>A befejezett munkafolyamat helyi tesztelése
A munkafolyamatot a helyi teszteléséhez nyomja le a lejátszás gombra az eszköztáron az oldal tetején. Végeztével a munkafolyamat végrehajtása, tekintse meg a kimenetet, a megadott kimeneti mappában jön létre. Látni fogja a kész MP4 kimeneti fájl, amely a bemeneti forrásfájl MXF kódolt.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>A dinamikus csomagolás engedélyezve az MP4 - több MXF kódolás
Ez a forgatókönyv hoz létre egy bitsebességű MP4-fájlok készlete kódolású AAC hang egyetlen. MXF bemeneti fájl.

Ha egy eszköz és többféle sávszélességű kimeneti van szükség együttesen használják az Azure Media Services szolgáltatásban több Képcsoporttal igazított MP4-fájlok az egyes egy másik átviteli sebesség és a megoldási kell létrehozni a dinamikus csomagolási funkcióit. Ehhez a [kódolás MXF be egy egyféle sávszélességű MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) forgatókönyv biztosítja számunkra az jó kiindulási pont.

![A munkafolyamat indítása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*A munkafolyamat indítása*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Egy vagy több további MP4 kimenetek hozzáadása
Az eredményül kapott Azure Media Services-adategység minden MP4-fájl egy másik átviteli sebesség és a megoldás támogatja. Adjunk hozzá egy vagy több MP4 kimeneti fájlok a munkafolyamatnak.

Ahhoz, hogy rendelkezünk a videó kódolók létrehozott ugyanazokkal a beállításokkal, célszerű a leginkább a már meglévő AVC Videókódoló ismétlődő, és egy másik megoldás és a (adjunk hozzá egy 960 × 540: 25 képkockamásodperc 2,5 Mbit/s sávszélességű kombinációja konfigurálása ). A meglévő kódoló duplikálásához másolási illessze be a Tervező felületére.

A Media bemeneti fájllal tömörítetlen videó kimeneti PIN-kód az új AVC összetevő csatlakoztathatnak.

![Csatlakoztatott második AVC kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Csatlakoztatott második AVC kódoló*

Most már alkalmazkodik a kimenetben 960 × 540 2,5 MB/s, az új AVC encoder konfigurációját. (Használja a Tulajdonságok "kimeneti szélesség", "Kimeneti magasság" és "Sávszélességű (kbps)" ennek.)

Adott szeretnénk az eredményül kapott eszköz az Azure Media Services dinamikus becsomagolást együtt használja, a streamvégpontnak kell lennie az érintett MP4-fájlok HLS/Fragmented MP4/DASH töredék pontosan igazított egymáshoz módon képesek, ügyfelek, amelyek különböző bitsebességre való átkódolása közötti váltás, a zökkenőmentes folyamatos video- és egyesített beolvasása. Ellenőrizze, hogy történik, győződjön meg arról, hogy mindkét AVC kódolók a Képcsoporttal ("csoport képek") tulajdonságainak mindkét MP4-fájlok mérete értéke 2 másodperc, amely hajtható végre kell:

* az Képcsoporttal mérete mód beállítása rögzített Képcsoporttal méretre és
* a kulcs keret időköz két másodperc.
* is beállíthatja az Képcsoporttal IDR vezérlő Képcsoporttal lezárt összes GOPs biztosításához rendelkezésére állnak a saját nélkül függőségek

Ahhoz, hogy ez a munkafolyamat megérteni, nevezze át az első AVC kódolót "AVC videó kódoló 640 x 360 1200-as KB/s" és a második AVC kódoló "AVC videó kódoló 960 × 540 2 500 kbps."

Adjon hozzá egy második ISO MPEG-4 Multiplexer és a egy második fájl kimeneti. Az új AVC kódoló a multiplexer csatlakozik, és ellenőrizze, hogy a kimenetét a kimeneti fájl be van irányítva. Ezután is csatlakoztathat a AAC hang kódoló kimeneti az új multiplexer a bemeneti. A kimeneti fájl viszont majd csatlakoztathatók, adja hozzá a Media Services eszköz, amely létrehozza a kimeneti fájl vagy eszköz csomópont.

![Második Muxer és a kimeneti fájl csatlakoztatva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Második Muxer és a kimeneti fájl csatlakoztatva*

Kompatibilitás érdekében az Azure Media Services dinamikus csomagolást Képcsoporttal száma vagy időtartama a multiplexer adattömbök mód konfigurálása, és az adattömbök száma GOPs állítsa 1-re. (Ez legyen az alapértelmezett.)

![Muxer adattömbök módok](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer adattömbök módok*

Megjegyzés: Előfordulhat, hogy szeretné bármely más átviteli sebesség és a megoldási kombináció hozzá kell adni az eszközintelligencia kimeneti szeretné ismételje meg ezt a folyamatot.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>A fájl kimeneti név beállítása
A kimeneti objektum egynél több egyetlen fájl van. Ez biztosítja, hogy ellenőrizze, hogy a kimeneti fájlok mindegyike a fájlnevek különböznek egymástól, és lehet, ezért törölje a fájl neve a használt foglalkozó is alkalmazhat egy fájl-elnevezési konvenciót kell.

A tervezőben kifejezések kimeneti fájlelnevezésnél szabályozható. A kimeneti fájl egyik tulajdonság panel megnyitásához, és nyissa meg a kifejezésszerkesztőbe fájl tulajdonság. Az első kimeneti fájl lett konfigurálva, a következő kifejezés használatával (lásd a következő oktatóanyagban azzal [egy egyféle sávszélességű MP4-kimenetre a MXF](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Ez azt jelenti, hogy a fájlnév határozza meg két változót: írni a kimeneti könyvtárba, és a forrásfájl alap neve. Az előbbi ki van téve a munkafolyamat legfelső szintű tulajdonságot, és a bejövő fájl által meghatározott. A kimeneti könyvtárba való helyi tesztelést; használata Ez a tulajdonság felülírja a rendszer a munkafolyamat-motor a munkafolyamat által a felhőalapú médiaprocesszorral Azure Media Services végrehajtásakor.
Egy kimeneti egységes elnevezési adni a kimeneti fájlokat, az elnevezési kifejezés első fájl módosítása:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

a másik pedig:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Hajtsa végre egy köztes teszt futtatásával ellenőrizze, hogy mindkét MP4 kimeneti fájlok megfelelően jönnek létre.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Egy külön lejátszása hozzáadása
A későbbiekben látni fogjuk Ha az általunk létrehozott .ism-fájlt, a kimeneti MP4-fájlokat a go, is szükséges lesz egy csak MP4-fájlt a hangsávot, az adaptív streameléshez. A fájl létrehozásához, egy további muxer hozzá a munkafolyamathoz (ISO-MPEG-4 Multiplexer), és nyomon követése 1 az AAC-kódoló kimenő pin összekapcsolása a bemeneti PIN-kódját.

![Hang Muxer hozzáadva](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Hang Muxer hozzáadva*

Hozzon létre egy harmadik kimeneti fájl összetevő kimeneti a muxer kimenő streamjét, és konfigurálhatja a fájlelnevezési kifejezés:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Hang Muxer kimeneti fájl létrehozása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Hang Muxer kimeneti fájl létrehozása*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Hozzáadás a. ISM SMIL fájl
A dinamikus becsomagolást is MP4 fájlokat (és a csak hangfájlt tartalmazó MP4) a Media Services eszközben dolgozhat együtt, a is kell a jegyzékfájlt (más néven "SMIL" fájlba: multimédiás integrációs nyelvi szinkronizálva). Ez a fájl azt jelzi, hogy az Azure Media Services milyen MP4-fájlokat a dinamikus csomagolás és melyiket érdemes figyelembe venni a hang streameléshez érhetők el. Egy tipikus Alkalmazásjegyzék-fájl egy egyetlen hang Stream MP4 a készletének így néz ki:

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

A .ism-fájlt egy switch utasításban, minden egyes az egyes videó MP4-fájlokat, és ezeket is egy (vagy több) hangfájl hivatkozik egy MP4 csak a hangot tartalmazó mellett egy hivatkozást tartalmaz.

A csoporton MP4 a jegyzékfájl létrehozása végezhető el a "AMS jegyzékfájl író." nevű összetevő A használatához húzza át a felületet, és az "Írás teljes" kimeneti PIN-kód az AMS-jegyzékfájl író bemeneti három fájl kimeneti összetevők csatlakozni. Ezután ellenőrizze, hogy az AMS-jegyzékfájl író kimenetét a kimeneti fájl vagy eszköz a csatlakozáshoz.

Mivel a más összetevőkkel fájl kimeneti, adja meg a kifejezés .ism fájlt kimeneti elé:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

A befejezett munkafolyamat néz ki az alábbi:

![Befejezett MXF több MP4-munkafolyamathoz](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Befejezett MXF több MP4-munkafolyamathoz*

## <a id="MXF_to__multibitrate_MP4"></a>Kódolási MXF, több MP4 - továbbfejlesztett tervezet
Az a [a munkafolyamat előző bemutatón](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) megtudtuk, hogyan MXF egyetlen bemeneti eszköz létrehozható egy kimeneti objektum többszörös sávszélességű MP4-fájlokat, egy csak MP4-fájlt és a egy jegyzékfájl használható együtt az Azure Media A dinamikus csomagolás szolgáltatásokat.

Ez az útmutató bemutatja, hogyan bizonyos szempontjait fejlesztése és kényelmesebb végzett.

### <a id="MXF_to_multibitrate_MP4_overview"></a>A munkafolyamat áttekintése növelése érdekében
![Több MP4 javíthatja a munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Több MP4 javíthatja a munkafolyamat*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>A fájlok elnevezési konvenciói
Az előző munkafolyamatban a egyetlen egyszerű kifejezésbe generálása a kimeneti fájl nevét alapjaként beállítottunk. Azonban van néhány duplikáció: minden egyes kimeneti fájl összetevője megadott ilyen kifejezés.

Például a fájl kimeneti összetevő esetében az első videó fájl van konfigurálva a következő kifejezést:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

A videó második kimenet van például egy kifejezést:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Hát nem lenne, letisztultabb, kevésbé hiba rejt magában, és kényelmesebb Ha tudjuk sikerült távolítson el néhányat a duplikációt tartalmaz, és dolgokat több konfigurálható inkább? Szerencsére is: a Tervező kifejezés képességek lehetővé teszi az egyéni tulajdonságok létrehozása a munkafolyamat legfelső szintű együtt biztosít egy réteggel is kényelmi célokat szolgál.

Tegyük fel, azt fogja a bitsebességre való átkódolása az egyes MP4-fájlokká a filename konfigurációs meghajtó. Ezek bitsebességre való átkódolása igyekszünk fogja konfigurálni egy központi helyen (gyökere a graph), ahol azok lesz elérhető konfigurálása és a meghajtó fájlnév létrehozása. Ehhez, azt először tegye közzé a sávszélességű tulajdonság mindkét AVC kódolók a munkafolyamat, a legfelső szintű, hogy az elérhető mind a legfelső szintű, valamint a AVC kódolók időszakhoz. (Akkor is, ha két különböző kritikus pontok elkerülése érdekében jelenik meg, nincs mögöttes csak egy érték.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>A munkafolyamat legfelső szintű alakzatot közzétételi összetevő tulajdonságai
Nyissa meg az első AVC kódoló, nyissa meg az átviteli sebesség (kbps) tulajdonságot, és a legördülő listából válassza a közzététel.

![Az átviteli sebesség tulajdonság közzététele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*Az átviteli sebesség tulajdonság közzététele*

Állítsa be a közzétételi párbeszédpanelre, közzététel a munkafolyamat grafikon, a legfelső szintű egy közzétett neve "video1bitrate" és "A videó 1 sávszélességű" olvasható megjelenítendő nevét. Konfigurálja az egyéni "Streaming bitsebességre való átkódolása" nevű csoport nevét, és kattintson a közzététel.

![Az átviteli sebesség tulajdonság közzététele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Közzététel párbeszédpanelen sávszélességű tulajdonság*

Ismételje meg ugyanaz a második AVC kódoló sávszélességű tulajdonságához, és adja neki a ugyanazon egyéni csoport "Streaming bitsebességre való átkódolása" megjelenített névvel "A videó 2 sávszélességű", "video2bitrate".

Ha azt most már megvizsgálhatja a munkafolyamat legfelső szintű tulajdonságait, láthatjuk, az egyéni csoportot a két közzétett tulajdonságai jelennek meg. Mindkét van tükrözve azok megfelelő AVC kódoló sávszélességű értékét.

![A munkafolyamat legfelső szintű közzétett sávszélességű Kellékek](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Amikor azt szeretné elérni ezeket a tulajdonságokat kódot vagy egy kifejezés, azt is megteheti ehhez hasonló:

* a beágyazott kódot egy összetevő jobb gyökere alatt: node.getPropertyAsString('.. / video1bitrate ", NULL értékű)
* kifejezésben: ${ROOT_video1bitrate}

Tegye közzé a hangsávra sávszélességű, valamint a "Streaming bitsebességre való átkódolása" csoport befejezéséhez. A tulajdonságok a AAC kódoló keresse meg a sávszélességű beállítást, majd válassza a közzététel mellett a legördülő listából. A gráf neve "audio1bitrate" gyökerében közzététele, és a megjelenített név "Audio 1 sávszélességű" a "Streaming bitsebességre való átkódolása" egyéni csoporton belül.

![Számára audiotartalmak sávszélességű közzététel párbeszédpanel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Számára audiotartalmak sávszélességű közzététel párbeszédpanel*

![A legfelső szintű eredményül kapott video- és Kellékek](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*A legfelső szintű eredményül kapott video- és Kellékek*

Ezen három bármelyikét módosítása értékeket is Átkonfigurálás, és megváltoztatja az értékeket a megfelelő összetevőinek vannak összekapcsolva (és ha közzétett).

### <a id="MXF_to__multibitrate_MP4_output_files"></a>Kimeneti fájl nevek támaszkodjon közzétett tulajdonságértékek hoztak létre.
Hardcoding helyett a generált fájlneveket, most már átalakíthatjuk az egyes számíthat a Microsoft graph legfelső szintű közzétett sávszélességű tulajdonságait a kimeneti fájl összetevőt a fájlnév kifejezés. Az első kimeneti fájl kezdve található fájl tulajdonsága, és ehhez hasonló kifejezés szerkesztése:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

Ebben a kifejezésben a különböző paraméterek érheti el és a billentyűzeten a kifejezés ablakban dollárjel lenyomásával megadott. A rendelkezésre álló paraméterek egyike az, hogy a korábban közzétett video1bitrate tulajdonság.

![Paraméterek kifejezésben elérése](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Paraméterek kifejezésben elérése*

Használja ugyanazt a fájlt kimenetét a második videó:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

és a csak hangfájlt tartalmazó fájl kimeneti:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Ha most módosítjuk az átviteli sebesség a video- vagy fájlokat, a megfelelő kódoló újra kell konfigurálni, és az MP4-alapú fájl neve egyezmény lesz lesz érvényes az összes automatikus.

## <a id="thumbnails_to__multibitrate_MP4"></a>A miniatűrök ad hozzá több MP4-kimenetre
Egy munkafolyamatot, amely állít elő, kezdve [az MXF, adjon meg egy több MP4 kimenete](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), azt most keresse az miniatűrök ad hozzá a kimenetet.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>Miniatűrök hozzáadása a munkafolyamat áttekintése
![Indítsa el a munkafolyamatot több MP4](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Indítsa el a munkafolyamatot több MP4*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Hozzáadás a JPG-kódolás
A miniatűrkészítés lelke a JPG-kódoló összetevő, a kimeneti fájlok JPG tudni lesz.

![JPG-kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG Encoder*

Nem sikerült azonban közvetlenül csatlakozni a tömörítetlen Video-adatfolyamot a Media fájl bemeneti a JPG-kódoló be. Ehelyett várhatóan át kell adni egyes keretek. Ez a videó keret kapu összetevő keresztül elvégezhető.

![A keret kapu csatlakozik a JPG-kódoló](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*A keret kapu csatlakozik a JPG-kódoló*

A keret kapu sok másodpercenként vagy keretek lehetővé teszi, hogy egy videó keret át. Az időközt, és amely ez történik az időeltolódás tulajdonságaiban konfigurálható.

![Videó keret kapu tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Videó keret kapu tulajdonságai*

Hozzunk létre egy miniatűr percenként idő (másodperc) és 60 intervallum a mód beállításával.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>Szín terület átalakítás többé vesződnie a sérült
Logikai mindkét tömörítetlen videó PIN-kódok a keret kapu és az adathordozó bemeneti fájllal most már csatlakoztatható tűnik, miközben tartozna figyelmeztetés, ha szeretné tesszük.

![Bemeneti szín helyének hibája](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Bemeneti szín helyének hibája*

Ennek oka az, megjelenítésének színe az adatokat az eredeti nyers tömörítetlen video-adatfolyamot, a érkező, az MXF, különbözik a Mi a JPG-kódoló feltételez. Pontosabban egy úgynevezett "szín terület", "RGB" vagy "Szürkeárnyalatos" várhatóan a flow a. Ez azt jelenti, hogy a videó keret kapu bejövő video-adatfolyamot kell rendelkeznie a szín terület kapcsolatban először alkalmazza az átalakításhoz.

Húzza a munkafolyamat a szín terület Converter - Intel, és csatlakoztathatja azt a keret kapu.

![Csatlakozás egy szín terület konverter](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*Csatlakozás egy szín terület konverter*

A Tulajdonságok ablakban válassza ki a készlet listából a BGR 24 bejegyzést.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>A miniatűrök írása
A MP4 videó-től eltérően a JPG-kódoló összetevő jelenít meg több fájlt. Annak érdekében, hogy ez foglalkozik, egy jelenet keresési JPG fájl író összetevővel is használható: azt a bejövő JPG miniatűrök fogadja, és minden folyamatban van egy másik számmal suffixed filename ír ki, azokat. (A általában az a streamben, amely a miniatűr megrajzolása másodperc/egységek számát jelző szám.)

![A jelenet keresési JPG-fájl író bemutatása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*A jelenet keresési JPG-fájl író bemutatása*

A kimeneti mappa elérési útja tulajdonság beállítása a kifejezésben: ${ROOT_outputWriteDirectory}

és a fájlnév előtag tulajdonságot:

    ${ROOT_sourceFileBaseName}_thumb_

Az előtag határozza meg, hogyan a miniatűr fájlok neve alatt. Ezek a streambeli pozíció a bütykének jelző szám vannak utótaggal.

![Jelenet JPG-fájl író keresési tulajdonságok](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Jelenet JPG-fájl író keresési tulajdonságok*

A kimeneti fájl vagy eszköz a csomóponthoz csatlakozás a jelenet keresési JPG-fájl író.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>Hibák észlelése, a munkafolyamat
A bemeneti a szín terület konverter csatlakozhat a nyers tömörítetlen videokimenetet. Most egy helyi tesztet végrehajtani a munkafolyamat futtatásához. A munkafolyamat hirtelen leáll a végrehajtása, és egy piros vázlatot, az a komponens, amely a hibát jelezze esély van:

![Szín terület konvertálási hiba](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Szín terület konvertálási hiba*

Kattintson a jobb sarkában a szín terület Converter összetevő megtekintéséhez, mi az az oka, a kódolási kísérlet nem sikerült felső kis red "E" ikont.

![Szín terület Converter hiba-párbeszédpanelen.](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Szín terület Converter hiba-párbeszédpanelen.*

Azt tapasztaltuk, ahogy láthatjuk, hogy rendelkezik-e a bejövő szín terület a szín terület átalakító szabvány a kért átalakításához meg YUV RGB-rec601 kell. A stream látszólag nem jelzi a rec601. (Jav. 601 egy digitális videót formában váltakozó analóg videó jelek kódolási szabvány. Azt adja meg egy aktív régió 720 fényességének minták és a egy sorban 360 chrominance minták. A rendszer kódolás szín YCbCr 4 néven: 2:2.)

A probléma megoldásához, jeleztük fogja meg a stream, azt még foglalkozó rec601 tartalmak metaadatait. Ehhez egy videó adatok típusa Updater összetevőt, amely a nyers forrás- és a szín terület átalakítás összetevő között elhelyezzük fogjuk használni. Ezen adatok típusa updater lehetővé teszi, hogy az egyes videó adatok manuális frissítés tulajdonságait. Konfigurálja egy szín terület Standard, "Rec 601" jelzi. Ezáltal a videó adatok típusa Updater címkézése a streamet, a "Rec 601" szín területtel rendelkező, ha nem volt nincs még definiált színt hely. (Nem felülírja a meglévő metaadatokat, hacsak a felülbírálás jelölőnégyzetet, de.)

![Szín terület Standard megtekinti a adatok típusa a frissítése](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Szín terület Standard megtekinti a adatok típusa a frissítése*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>A munkafolyamat befejeződött
Most, hogy a munkafolyamat befejeződött, hajtsa végre egy másik Futtatás megtekintéséhez adja át.

![Miniatűrökkel multi-mp4-kimenetre a befejezett munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Miniatűrökkel multi-mp4-kimenetre a befejezett munkafolyamat*

## <a id="time_based_trim"></a>Időalapú levágási több MP4-kimenetre
Egy munkafolyamatot, amely állít elő, kezdve [az MXF, adjon meg egy több MP4 kimenete](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), azt most keresse a forrás-videó-időbélyegek alapján vágást be.

### <a id="time_based_trim_start"></a>Kezdje el hozzáadni a tisztítás, a munkafolyamat áttekintése
![Adja hozzá a tisztítás kezdő munkafolyamat](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Adja hozzá a tisztítás kezdő munkafolyamat*

### <a id="time_based_trim_use_stream_trimmer"></a>A Stream vágó használatával
A Stream vágó összetevő lehetővé teszi, hogy a tároló visszaigénylésének elején és a egy alap túllépik az információk (másodperc, perc,...) a bemeneti Stream befejezési. A vágó nem támogatja a keret-alapú tisztítás.

![Stream vágó](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Stream vágó*

Helyett, ha a AVC kódolók és beszélőfelismerési pozíció hozzárendelő az adathordozó bemeneti fájllal közvetlenül, elhelyezzük között azokat a stream vágó. (Egy a videó jel, valamint a kihagyásos hang jel esetében.)

![Stream vágó helyezze a kettő között](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Stream vágó helyezze a kettő között*

Így a videó és hang 15 másodperc és a videóban 60 másodperc közötti csak azt fogja feldolgozni, konfiguráljuk a vágó.

Nyissa meg a videó Stream vágó tulajdonságait, és konfigurálása mindkét Kezdés időpontja (15 s) és befejezési időpontja (60 s) tulajdonságait. Ahhoz, hogy mind az audio- és vágó mindig megtörténik az megegyező kezdő és záró értéket, hogy közzé azokat, a munkafolyamat legfelső szintű.

![A kezdési idő tulajdonságot Stream vágó közzététele](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*A kezdési idő tulajdonságot Stream vágó közzététele*

![Közzététel tulajdonság párbeszédpanel kezdési ideje](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Közzététel tulajdonság párbeszédpanel kezdési ideje*

![Közzététel tulajdonság párbeszédpanel a befejezési idő](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Közzététel tulajdonság párbeszédpanel a befejezési idő*

Azt a munkafolyamat a legfelső szintű most vizsgálja meg, mindkét tulajdonság-e eligazíthatja megjelenített és konfigurálható onnan.

![Elérhető a legfelső szintű közzétett tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Elérhető a legfelső szintű közzétett tulajdonságai*

Most nyissa meg a tisztítás tulajdonságait a hang vágó, és a közzétett tulajdonságai a munkafolyamat legfelső szintű hivatkozó kifejezést is kezdési és befejezési idejének konfigurálása.

A csonkolás Audio kezdő időpont:

    ${ROOT_TrimmingStartTime}

és a Befejezés időpontja:

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>A munkafolyamat befejeződött
![A munkafolyamat befejeződött](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*A munkafolyamat befejeződött*

## <a id="scripting"></a>A parancsfájlalapú összetevő bemutatása
Parancsfájlalapú összetevők parancsfájlok hajthat végre, a munkafolyamat-végrehajtási fázisaiban. Négy különböző parancsprogramok is végrehajtható, egyenként egyedi jellemzőit, és saját helyen, a munkafolyamat-életciklusának:

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

A dokumentáció a parancsfájlalapú összetevő kerül részletesen a fenti minden. A [az alábbi szakasz](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), a **realizeScript** parancsfájl-kezelési összetevő létrehozására egy cliplist XML-t működés közben, amikor elindítja a munkafolyamat használható. Ez a szkript neve, amely csak egyszer történik, annak életciklusát összetevő telepítésekor.

### <a id="scripting_hello_world"></a>Egy munkafolyamaton belül Scripting: hello world
Húzzon egy parancsprogrammal létrehozva összetevő a tervezőfelületére, és nevezze (például "SetClipListXML").

![A parancsfájlalapú összetevő hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*A parancsfájlalapú összetevő hozzáadása*

A parancsfájlalapú összetevő tulajdonságainak nézze meg, a másik parancsprogramot négyféle lesz látható, minden állítható be egy másik szkriptet.

![Parancsfájlalapú összetevő tulajdonságai párbeszédpanel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Parancsfájlalapú összetevő tulajdonságai párbeszédpanel*

Törölje a jelet a processInputScript, és nyissa meg a realizeScript szerkesztője. Most már beállított és készen áll a parancsfájlkezelés megkezdéséhez.

Parancsfájlok Groovy, egy dinamikusan lefordított programozási nyelv, a Java platform, amely megtartja a kompatibilitás a Java nyelven íródtak. Valójában a legtöbb Java-kódok érvényes Groovy kódot.

A realizeScript kontextusában ideje lefuttatni egy egyszerű hello world groovy parancsfájlt. A szerkesztőben írja be a következőket:

    node.log("hello world");

Most már végre egy tesztcélú helyi Futtatás. A Futtatás után vizsgálja meg (keresztül a rendszer a parancsfájlalapú összetevő lapján) a naplók tulajdonság.

![Hello world napló kimenetét](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hello world napló kimenetét*

A csomópont-objektum a napló metódus nevezzük, az aktuális "csomópont" vagy az összetevőt, azt hogy parancsfájl-kezelési belül hivatkozik. Minden összetevő ilyen képes kimeneti naplózási adatokat, a rendszer fülre keresztül érhető el. Ebben az esetben azt kimeneti a szövegkonstans "hello world." Fontos tudni, itt látható, hogy ez felbecsülhetetlen értékű hibakeresési eszközt, hogy milyen a parancsfájl ténylegesen módon adatforgalmáról bizonyíthatja.

A parancsfájl-kezelési környezetből. azt is hozzáféréssel rendelkezik a többi összetevő. Megpróbálhatja a következőt:

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

A napló ablak megmutatja a következőket:

![Kimenet elérési útjának eléréséhez](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Kimenet elérési útjának eléréséhez*

## <a id="frame_based_trim"></a>Keret-alapú levágási több MP4-kimenetre
Egy munkafolyamatot, amely állít elő, kezdve [az MXF, adjon meg egy több MP4 kimenete](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), azt most keresse a forrásvideókat, keret érintett alapján vágást be.

### <a id="frame_based_trim_start"></a>Kezdje el hozzáadni a tisztítás a tervezet áttekintése
![A munkafolyamat elindításához a tisztítás hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*A munkafolyamat elindításához a tisztítás hozzáadása*

### <a id="frame_based_trim_clip_list"></a>A videoklip lista XML használata
Az összes korábbi munkafolyamat oktatóanyagok a Media fájl bemeneti összetevő, a videó bemeneti forrás használtuk. Ebben a forgatókönyvben, fogjuk használni a klipek lista forrása összetevő helyette. Ne legyen működő; az előnyben részesített módja csak akkor használja a klipek lista forrása, ha ehhez valós indok van (például a következő esetben, ahol végzünk klip lista levágási funkcióinak használatát).

Ha az adathordozó bemeneti fájllal, a klipek lista forrása a, a klipek lista forrása összetevő húzza a tervezőfelületére, és csatlakozzon a klipek lista XML PIN-kód a munkafolyamat-tervezővel klip lista XML-csomópont. Ez a klip lista forrása a kimeneti PIN-kód, a bemeneti videó alapján tölti fel. Mostantól csatlakozhat a tömörítetlen videó és hang tömörítetlen PIN-kódok a klipek lista forrásból a megfelelő AVC kódolók és hang Stream Interleaver. Most már távolítsa el a Media bemeneti fájllal.

![A Media bemeneti fájllal cseréli a klipek lista forrása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*A Media bemeneti fájllal cseréli a klipek lista forrása*

A lista forrása klip összetevő felvesz bemenetként egy "klip XML." A forrás-fájl a helyi teszteléshez kiválasztásakor a klipek lista xml-automatikusan kitölti az Ön számára kódja.

![Klip lista XML-tulajdonság automatikusan kitöltve](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Klip lista XML-tulajdonság automatikusan kitöltve*

Szeretne egy kicsit közelebb az XML-fájl, ez hogyan is néz ki:

![Klip tartalmazó párbeszédpanelen szerkesztése](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Klip tartalmazó párbeszédpanelen szerkesztése*

Ez azonban nem tükrözi a klipek lista xml képességeit. Az egyik lehetőség van, hogy adjon hozzá egy "Vágás" elemet mindkét video- és forrás alatt, ehhez hasonló:

![Egy vágás elem felvétele a klipek listához](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Egy vágás elem felvétele a klipek listához*

Ha ilyen fent klip lista xml módosítja, és hajtson végre egy helyi tesztet futtatni, látni fogja a videó megfelelően lett vágott a videóban 10 és 20 másodperc között.

Mi történik, ha azonban mégis egy helyi futtatás, ellentétesen az azonos cliplist xml nem kell a ugyanaz a hatása, ha egy munkafolyamatot, amely futtatja az Azure Media Services a alkalmazni. Azure prémium szintű kódoló indításakor az cliplist XML-fájl jön létre minden alkalommal újra, a kódolási feladat a megadott bemeneti fájl alapján. Ez azt jelenti, hogy változtatásokat végzünk az XML sajnos bírálható felül lenne.

A számláló a cliplist xml-kódolási feladat indításakor törlődnek, azt újból létrehozhatja azt menet közben csak a munkafolyamat megkezdése után. Az ilyen egyéni műveletek elvégezhet keresztül az úgynevezett "Parancsfájlalapú Component." További információkért lásd: [bemutatása a parancsfájlalapú összetevő](media-services-media-encoder-premium-workflow-tutorials.md#scripting).

Húzzon egy parancsprogrammal létrehozva összetevő a Tervező felületére, és nevezze át a "SetClipListXML."

![A parancsfájlalapú összetevő hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*A parancsfájlalapú összetevő hozzáadása*

A parancsfájlalapú összetevő tulajdonságainak nézze meg, amikor a négy különböző parancsfájl-típusok a következők látható, minden állítható be egy másik szkriptet.

![Parancsfájlalapú összetevő tulajdonságai párbeszédpanel](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Parancsfájlalapú összetevő tulajdonságai párbeszédpanel*

### <a id="frame_based_trim_modify_clip_list"></a>A parancsfájlalapú összetevőtől klip listájának módosítása
Azt is újraírási cliplist XML-kód jön létre a munkafolyamat-indítás közben, mielőtt azt kell hozzáféréssel rendelkezik a cliplist XML-tulajdonság és a tartalom. Ehhez hasonló azt is megteheti:

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![A naplózott bejövő klip listája](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*A naplózott bejövő klip listája*

Először szükségünk meg tudja határozni, hogy melyik pontból melyik pont csak a videó vágása szeretnénk. Ahhoz, hogy ez kényelmes kevésbé-technikai felhasználóját, hogy a munkafolyamat, tegye közzé a grafikon a legfelső szintű két tulajdonság. Ehhez kattintson a jobb gombbal a Tervező felületére, és válassza ki a "Tulajdonság hozzáadása":

* Első tulajdonság: "ClippingTimeStart" típusú: "IDŐKÓD"
* A második tulajdonság: "ClippingTimeEnd" típusú: "IDŐKÓD"

![Adja hozzá a tulajdonság párbeszédpanel kapcsolást kezdési ideje](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Adja hozzá a tulajdonság párbeszédpanel kapcsolást kezdési ideje*

![A munkafolyamat legfelső szintű idő Kellékek levágott közzétett](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*A munkafolyamat legfelső szintű idő Kellékek levágott közzétett*

Adja meg mindkét tulajdonság értékét:

![Konfigurálása a Kivágás kezdő és záró tulajdonságai](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Konfigurálása a Kivágás kezdő és záró tulajdonságai*

Most a belül a parancsfájl elérheti az mindkét tulajdonság, ehhez hasonló:

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![Napló ablak, rajta az elejéről és végéről, Kivágás](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Napló ablak, rajta az elejéről és végéről, Kivágás*

Nézzük elemezni a időkód karakterláncokat az űrlap használatával egy egyszerű reguláris kifejezés használatát egy kényelmesebbé:

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

![Elemzett időkód kimenetét a napló ablak](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Elemzett időkód kimenetét a napló ablak*

Ezen információk csak olyan mélységben is most úgy módosítjuk, hogy a kívánt keret pontos Kivágás a film kezdési és befejezési időpontjai cliplist xml.

![Szkriptkódot, hogy a vágás elemek hozzáadása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Szkriptkódot, hogy a vágás elemek hozzáadása*

Ez azért volt szükség keresztül normál karakterlánc fájlkezelési műveleteket. Az eredményül kapott módosított klip lista xml írt vissza a clipListXML tulajdonság a munkafolyamat legfelső szintű a "setProperty" metódus segítségével. Egy másik teszt futtatása után a napló ablak jelenik meg velünk a kapcsolatot a következők:

![Az eredményül kapott klip listát naplózása](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Az eredményül kapott klip listát naplózása*

Hajtsa végre a megtekintéséhez, hogyan lett a videó- és audio-adatfolyamokat levágva vizsgálat. Egynél több teszt futtatása a tisztítás pontok eltérő értékek teheti meg, ahogy azt láthatja, hogy a rendszer nem figyelembe kell venni azonban! Ennek oka az, hogy a Tervező, eltérően az Azure-futtatókörnyezetben nem bírálja felül az cliplist XML-fájl minden egyes futtatáskor. Ez azt jelenti, hogy csak az első alkalommal állította a is és pontokon, ki okoz az XML-átalakítás, minden más esetben az alkalmazásőr záradék (Ha (clipListXML.indexOf ("<trim>") == -1)) megakadályozza, hogy a munkafolyamat egy másik vágás elem felvétele esetén már egy.

Ahhoz, hogy a munkafolyamat tesztelése helyileg kényelmes, ajánlott hozzáadunk néhány vezetése ház kódot, amely megvizsgálja, ha a vágás elem már jelen volt. Ha igen, törölhetjük az XML-fájl az új értékekkel módosításával a folytatás előtt. Ahelyett, hogy használja az egyszerű karakterlánc-feldolgozás, az valószínűleg biztonságosabb ehhez keresztül valós xml-elemzés hálózatiobjektum-modellt.

Mielőtt azonban számos importálási utasításokat a parancsfájl elején először hozzá kell hozzá lehessen adni az ilyen kód:

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

Ezt követően hozzáadhatja a szükséges tisztítási kódot:

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

Ez a kód csak túllépik a pont, ahol a cliplist XML hozzáadjuk a vágás elemeket.

Ezen a ponton azt futtatható és módosítható munkafolyamatunkat a, akár mellett a módosításokat, minden eddiginél a alkalmazni kívánt időben.    

### <a id="frame_based_trim_clippingenabled_prop"></a>Kényelmi ClippingEnabled tulajdonság hozzáadása
Nem mindig szívesen vágást megtörténjen, mivel most befejezéséhez ki a munkafolyamat adjon hozzá egy kényelmes logikai jelzőt, amely azt jelzi-e szeretnénk vágást / levágott engedélyezése.

Mint korábban tegye közzé az új tulajdonság a legfelső szintű, az úgynevezett "ClippingEnabled" munkafolyamat "Logikai" típusú.

![Közzétett egy tulajdonság kapcsolást engedélyezése](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Közzétett egy tulajdonság kapcsolást engedélyezése*

Az az alábbi egyszerű guard záradék esetében lehet ellenőrizze, hogy a tisztítás megadása kötelező és döntse el, ha a klipek lista ilyen módosítani kell, vagy sem.

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

### <a id="code"></a>Teljes kódját

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
[Introducing Premium Encoding in Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Prémium szintű kódolás az Azure Media Services használatával](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Az Azure Media Services az igény szerinti Tartalomterjesztésről kódolás](media-services-encode-asset.md#media-encoder-premium-workflow)

[A Media Encoder Premium munkafolyamat formátumai és kodekei](media-services-premium-workflow-encoder-formats.md)

[Minta munkafolyamat-fájlok](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Az Azure Media Services Explorer eszköz](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
