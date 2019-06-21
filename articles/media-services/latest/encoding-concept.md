---
title: Kódolás a felhőben, a Media Services – Azure |} A Microsoft Docs
description: Ez a témakör ismerteti a kódolási folyamatot az Azure Media Services használata közben
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b0a71e8b3ffff822521a23aafd6764bcce9bd4d4
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303933"
---
# <a name="encoding-with-media-services"></a>Kódolás a Media Services használatával

A folyamat digitális videót és/vagy egy másik (a) a fájlok méretének csökkentésével, és/vagy (b) kompatibilis formátumban előállító kirovását szabványos formátumba hangot tartalmazó fájlok átalakítása kifejezés kódolás a Media Services vonatkozik egy eszközök és alkalmazások széles skáláját. Ez a folyamat is nevezik videó tömörítést vagy átkódolása. Tekintse meg a [az adattömörítés](https://en.wikipedia.org/wiki/Data_compression) és a [kódolás és átkódolása mi?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) további vitafórum a fogalmakat.

Videók az eszközök és alkalmazások által általában érkezzenek [progresszív letöltés](https://en.wikipedia.org/wiki/Progressive_download) vagy [adaptív sávszélességű streamelés](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). 

* Továbbítás letöltésen keresztül fokozatos, használhatja az Azure Media Services (mezzanine) digitális média fájl konvertálása be egy [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) fájl, amely tartalmazza a videóra,-kódolású a [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) kodeket, és hang, amely a kódolású a [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) kodek. A tárfiókban található egy Eszköz a MP4-fájl írása. Használhatja az Azure Storage API-k és SDK-kkal (például [Storage REST API-val](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), vagy [.NET SDK-val](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) közvetlenül a fájl letöltéséhez. Ha a kimeneti hozta létre a tároló egy adott tároló nevű eszköz használ erre a helyre. Ellenkező esetben használhatja a Media Services [eszköz tároló url-Címek listája](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Tartalom előkészítése kézbesítési adaptív bitsebességű folyamatos átvitel, a mezzanine-fájlt kell kódolni, több bitsebességre való átkódolása (magastól alacsonyig). Az átviteli sebesség arányában minőségű, biztonságos átállás érdekében így egyedül a videó felbontása. Ennek eredményeként egy úgynevezett kódolási létra – felbontásra és bitsebességre való átkódolása tábláját (lásd: [adaptív sávszélességű automatikusan létrehozott létra](autogen-bitrate-ladder.md)). A Media Services segítségével több bitsebességre való – ennek során átkódolása, a "mezzanine" formátumú fájlok, MP4-fájlokat, és a kapcsolódó streamelési konfigurációs fájlokat az eszköz a tárfiókban lévő írt fog kapni. Ezután a [dinamikus csomagolási](dynamic-packaging-overview.md) , hogy a videó streamelési protokollok, mint például a Media Services képesség [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) és [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Ehhez szükséges, hogy hozzon létre egy [Streamelési Lokátor](streaming-locators-concept.md) és létrehozása a streamelési url-Címek, a támogatott protokollok, amelyek majd átadható az eszközök és alkalmazások képességeikkel alapján megfelelő.

Az alábbi ábrán látható, a munkafolyamat igény szerinti Encoding, a dinamikus csomagolás használatával.

![A dinamikus csomagolás](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Ez a témakör nyújt útmutatást tartalmait a Media Services v3 kódolással.

## <a name="transforms-and-jobs"></a>Átalakítások és feladatok

Kódolás a Media Services v3-as, létre kell egy [átalakítása](https://docs.microsoft.com/rest/api/media/transforms) és a egy [feladat](https://docs.microsoft.com/rest/api/media/jobs). Az átalakítás határozza meg a recept, a kódolási beállítások és kimenetek; a feladat egy példányát a recept. További információkért lásd: [átalakítások és feladatok](transforms-jobs-concept.md)

A Media Services encoding, amikor a szolgáltatás használatával ossza meg a kódoló a bemeneti fájlok feldolgozásának módja. Például megadhatja a videó felbontást és/vagy a kívánt hang csatornák száma kódolt tartalmában. 

Ismerkedhet meg gyorsan az ágazatban kialakult bevált gyakorlaton alapuló ajánlott a beépített beállítások egyikét, vagy dönthet úgy, amelyekre az adott forgatókönyv vagy eszközkövetelmények beállított egyéni. További információkért lásd: [Egyéni átalakító Encode](customize-encoder-presets-how-to.md). 

Január 2019, kezdve kódolás a Media Encoder Standard MP4-fájl létrehozására, ha .mpi új fájl jön létre, és hozzáadódik a kimeneti adategység. A MPI-fájl az célja, hogy a teljesítmény javítása [dinamikus csomagolási](dynamic-packaging-overview.md) és adatfolyam-forgatókönyvekhez.

> [!NOTE]
> Ne módosítsa vagy távolítsa el az MPI-fájlt, és minden olyan függőséget is a létezik-e a service-ben (vagy sem) egy souboru.

### <a name="creating-job-input-from-an-https-url"></a>Feladatbemenet HTTPS URL-cím létrehozása

A videók feldolgozásához feladatok elküldésekor meg kell mondanunk a Media Services, hogy hol található a bemeneti videó. A beállításokat egyik HTTPS URL-címet adja meg a bemeneti feladatként. A Media Services v3 jelenleg nem támogatja a darabolásos átvitel kódolási HTTPS URL-címek keresztül. 

#### <a name="examples"></a>Példák

* [Kódolás a HTTPS URL-címet a .NET használatával](stream-files-dotnet-quickstart.md)
* [Kódolás a HTTPS URL-címet a REST segítségével](stream-files-tutorial-with-rest.md)
* [Kódolás a HTTPS URL-címet a CLI használatával](stream-files-cli-quickstart.md)
* [Kódolás a HTTPS URL-címet a node.js használatával](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Létrehozás a feladat bemenete egy helyi fájlból

A bemeneti videó tárolható Media Service eszközként, ebben az esetben létrehozhat egy bemeneti objektuma a (helyi vagy az Azure Blob storage-ban tárolt) fájl alapján. 

#### <a name="examples"></a>Példák

[Beépített készlet használatával egy helyi fájl kódolása](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Feladatbemenet részklipkészítés létrehozása

Amikor a videó kódolása, megadhatja, is trim vagy a forrásfájl grafikus és kimenetet, amely rendelkezik a bemeneti videó csak egy kívánt része. Ez a funkció együttműködik bármely [átalakítása](https://docs.microsoft.com/rest/api/media/transforms) épül fel, amelyek segítségével a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) készleteket, vagy a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) készletek. 

Hozzon létre is megadhat egy [feladat](https://docs.microsoft.com/rest/api/media/jobs/create) az egyetlen klip egy videó igény szerinti vagy élő archívum (rögzített esemény). A feladat bemenetére lehet egy eszköz vagy a HTTPS URL-címet.

> [!TIP]
> Ha azt szeretné, anélkül, hogy a videó reencoding egy sublip a videó továbbításához, érdemes [dinamikus Packager az alkalmazásjegyzékeket előre szűrés](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Példák

Példa látható:

* [Részklip egy videót, a .NET használatával](subclip-video-dotnet-howto.md)
* [Részklip egy videót, a REST segítségével](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Beépített készletek

A Media Services jelenleg a következő beépített kódolási beállításokat támogatja:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) a bemeneti videó a standard szintű Encoder kódolási előbeállítást beépített beállítására szolgál. 

A következő készletek jelenleg támogatja:

- **EncoderNamedPreset.AACGoodQualityAudio** -kódolású 192 Kb/s, csak sztereó hang tartalmazó egyetlen MP4-fájl eredményez.
- **EncoderNamedPreset.AdaptiveStreaming** (ajánlott). További információkért lásd: [skála automatikus generálásához](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental** -elérhetővé teszi a tartalom-kompatibilis Encoding egy kísérleti készletet. A megadott bemeneti tartalom, a szolgáltatás megkísérli automatikusan meghatározni a rétegek, a megfelelő átviteli sebesség és a szállítási feloldási beállításaitól optimális számát által adaptív streameléshez. Az alapul szolgáló algoritmusok továbbra is verzióinformációk. A kimenet a videó és hang közbeékeléses MP4-fájlokat tartalmazza. További információkért lásd: [tartalom-kompatibilis kódolási előbeállítást kísérleti](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p** – 8 Képcsoporttal igazított MP4-fájlokat, és a 6000 KB/s 400 kb/s és sztereó AAC hang eredményez. Megoldás 1080p-nél kezdődik, és lefelé 360 p felbontású kerül.
- **EncoderNamedPreset.H264MultipleBitrate720p** -6 Képcsoporttal igazított MP4-fájlokat, és a 3400 kbps 400 kb/s és sztereó AAC hang eredményez. Feloldási 720 p elindul, és lefelé 360 p felbontású kerül.
- **EncoderNamedPreset.H264MultipleBitrateSD** – 5 Képcsoporttal igazított MP4-fájlokat, és a 1600 KB/s 400 kb/s és sztereó AAC hang eredményez. Megoldás 480p-nél kezdődik, és lefelé 360 p felbontású kerül.
- **EncoderNamedPreset.H264SingleBitrate1080p** -MP4-fájlokat, a videó kódolása H.264 kodekkel 6750 KB/s és a egy kép magassága 1080 képpont, és a sztereó hang kódolása 64 KB/s, AAC-LC kodekkel eredményez.
- **EncoderNamedPreset.H264SingleBitrate720p** -MP4-fájlokat, ahol az H.264 kodekkel 4500 KB/s és 720 képpont magasságú kép a videó kódolása és a sztereó hang kódolása 64 KB/s, AAC-LC kodekkel eredményez.
- **EncoderNamedPreset.H264SingleBitrateSD** -MP4-fájlokat, ahol az a videó kódolása H.264 kodekkel 2200 KB/s és a egy kép magassága képpontban 480 és a sztereó hang kódolása 64 KB/s, AAC-LC kodekkel eredményez.

A készletek legfrissebb lista megtekintéséhez lásd: [videók kódolásához használandó beépített készlet](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Ha szeretné látni, hogyan használja a készletek, tekintse meg [feltöltése, kódolása és fájlok folyamatos](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) kódolás a bemeneti videó a standard szintű Encoder használt beállításokat ismerteti. Ezzel a készlet, átalakító készletek testreszabásához. 

#### <a name="considerations"></a>Megfontolandó szempontok

Egyéni készletek létrehozásakor a következő szempontokat kell figyelembe venni:

- Minden értékét magasságát és szélességét AVC tartalom kezelésére kell költeni 4 többszörösének kell lennie.
- Az Azure Media Services v3-as a kódolási bitsebességre való átkódolása összes bit / másodperc. Ez különbözik a készletek a v2 API-kkal használt egységet kilobit/másodperc. Például ha az átviteli sebesség a v2-ben van megadva, 128 (kilobit/másodperc), a v3-as volna meg akkor 128000 (bit/másodperc).

### <a name="customizing-presets"></a>Beállításkészletek testreszabása

A Media Services teljes körűen támogatja az adott kódolási igények és követelmények készletek található értékek némelyike testreszabása. Azt mutatják be, hogyan szabhatja testre a kódoló készletek példákért lásd:

#### <a name="examples"></a>Példák

- [.NET-tel készletek testreszabása](customize-encoder-presets-how-to.md)
- [CLI-vel beállításkészletek testreszabása](custom-preset-cli-howto.md)
- [REST-beállításkészletek testreszabása](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Előre definiált séma

A Media Services v3 készletek típusos entitások az API-t. Ezek az objektumok a "schema" definícióját találja [nyílt API-specifikációnak (vagy a Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Is megtekintheti az előre megadott definíciók (például **StandardEncoderPreset**) az a [REST API-val](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK-val](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (vagy más Media Services v3 SDK-forrásdokumentáció).

## <a name="scaling-encoding-in-v3"></a>V3 a kódolás méretezése

Méretezhető médiafeldolgozás, lásd: [CLI-vel méretezési](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Tegyen fel kérdéseket, küldje el visszajelzését, frissítések beszerzése

Tekintse meg a [Azure Media Services-Közösség](media-services-community.md) kérdések, küldje el visszajelzését, és tudnivalók a Media Services-frissítések különböző módon olvashatja.

## <a name="next-steps"></a>További lépések

* [Feltöltése, kódolása és streamelése a Media Services használatával](stream-files-tutorial-with-api.md)
* [Kódolás a beépített készlet használatával HTTPS URL-címet](job-input-from-http-how-to.md)
* [Beépített készlet használatával egy helyi fájl kódolása](job-input-from-local-file-how-to.md)
* [Az egyedi, amelyekre az adott forgatókönyv vagy eszközkövetelmények készletek létrehozása](customize-encoder-presets-how-to.md)