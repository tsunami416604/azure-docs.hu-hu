---
title: Video- és hanganyagok kódolása a Media Services szolgáltatással
titleSuffix: Azure Media Services
description: Ez a cikk ismerteti a video- és hangfájlok Azure Media Services használatával történő kódolását.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6e1c9aa5c2e049d5fc1ebd8bf745417f56d232ec
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366579"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Video- és hanganyagok kódolása a Media Services szolgáltatással

A Media Services ben a kódolás kifejezés a digitális videót és/vagy hangot tartalmazó fájlok egyik szabványos formátumból a másikba történő konvertálására vonatkozik, azzal a céllal, hogy (a) csökkentse a fájlok méretét, és/vagy (b) olyan formátumot hoz létre, amely kompatibilis az eszközök és alkalmazások széles körével. Ezt a folyamatot videotömörítésnek vagy átkódolásnak is nevezik. A fogalmak további megvitatását lásd az [Adattömörítés](https://en.wikipedia.org/wiki/Data_compression) és a [Mi a kódolás és átkódolás?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)

A videókat általában [progresszív letöltéssel](https://en.wikipedia.org/wiki/Progressive_download) vagy [adaptív sávszélességű streameléssel](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)juttatják el az eszközökre és az alkalmazásokra.

> [!IMPORTANT]
> A Media Services nem számláz a törölt vagy hibás feladatokért. Például egy feladat, amely elérte az 50%-os előrehaladást, és megszakította a számlázás nem 50%-a feladat perc. Csak a kész feladatokért kell fizetnie.

* Progresszív letöltéssel történő kézbesítéshez az Azure Media Services segítségével konvertálhat [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) egy digitális médiafájlt (mezzanine) egy MP4-fájllá, amely a [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) kodekkel kódolt videót, valamint az [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) kodekkel kódolt hangot tartalmaz. Ez az MP4-fájl a tárfiókban lévő eszközhöz van írva. Az Azure Storage API-k vagy SDK-k (például [Storage REST API](../../storage/common/storage-rest-api-auth.md) vagy [.NET SDK)](../../storage/blobs/storage-quickstart-blobs-dotnet.md)segítségével közvetlenül letöltheti a fájlt. Ha a kimeneti eszközt egy adott tárolónévvel hozta létre a storage-ban, használja ezt a helyet. Ellenkező esetben a Media Services segítségével [felsorolhatja az eszköztároló URL-címeit.](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) 
* Az adaptív sávszélességű streamelési tartalom előkészítéséhez a mezzanine fájlt több bitsebességgel kell kódolni (magastól alacsonyig). A kecses minőségváltás biztosítása érdekében a videó felbontása csökken, ahogy a bitráta csökken. Ez azt eredményezi, hogy egy úgynevezett kódolási létra-táblázat a felbontások és bitráták (lásd [az automatikusan generált adaptív bitráta létra](autogen-bitrate-ladder.md)). A Media Services segítségével több bitsebességgel kódolhatja a félemeleti fájlokat. Ennek során kap egy sor MP4-fájlok at és a kapcsolódó streamelési konfigurációs fájlokat írt egy eszköz a tárfiókban. Ezután a Media Services [dinamikus csomagolási](dynamic-packaging-overview.md) funkciójával olyan streamelési protokollokon keresztül juttathatja el a videót, mint [az MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) és [a HLS.](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) Ehhez létre kell hoznia egy [streamelési lokátort,](streaming-locators-concept.md) és a támogatott protokolloknak megfelelő streamelési URL-címeket kell létrehoznia, amelyeket a képességek alapján át lehet adni az eszközöknek/alkalmazásoknak.

Az alábbi ábra az igény szerinti kódolás dinamikus csomagolással kapcsolatos munkafolyamatát mutatja be.

![Munkafolyamat az igény szerinti kódoláshoz dinamikus csomagolással](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Ez a témakör útmutatást nyújt a tartalom kódolásához a Media Services 3-as számmal.

## <a name="transforms-and-jobs"></a>Átalakítások és feladatok

A Media Services v3-as számával való kódoláshoz létre kell hoznia egy [átalakítást](https://docs.microsoft.com/rest/api/media/transforms) és egy [feladatot.](https://docs.microsoft.com/rest/api/media/jobs) Az átalakítás meghatározza a kódolási beállítások és kimenetek receptjét; a munka a recept egy példánya. További információt az [átalakításokkal és feladatokkal](transforms-jobs-concept.md) kapcsolatos cikkben olvashat.

A Media Services szolgáltatással történő kódolássorán a kódoló nak a bemeneti médiafájlok feldolgozásának módjára vonatkozó beállításokat kell használnia. A Media Services v3-as számban a Szabványos kódolót használja a fájlok kódolásához. Megadhatja például a videofelbontást és/vagy a kódolt tartalomban a kívánt hangcsatornák számát.

Az iparági bevált eljárások alapján az ajánlott beépített készletek egyikével gyorsan elkezdheti az első lépések, vagy egyéni készletet hozhat létre az adott forgatókönyv vagy eszközkövetelmények megcélzásához. További információt a [Kódolás egyéni átalakítással](customize-encoder-presets-how-to.md)című témakörben talál.

2019 januárjától kezdve, amikor a standard kódolóval MP4-fájl(ok) létrehozásához kódolást hoz létre, új .mpi fájl jön létre, és hozzáadódik a kimeneti eszközhöz. Ez az MPI fájl célja, hogy javítsa a teljesítményt a [dinamikus csomagolási](dynamic-packaging-overview.md) és streaming forgatókönyvek.

> [!NOTE]
> Ne módosítsa vagy távolítsa el az MPI-fájlt, és ne függjön a szolgáltatásban egy ilyen fájl létezésétől (vagy sem-től).

### <a name="creating-job-input-from-an-https-url"></a>Munkahelyi bevitel létrehozása HTTPS URL-címről

Amikor feladatokat küld be a videók feldolgozásához, meg kell mondania a Media Services-nek, hogy hol találja a bemeneti videót. Az egyik lehetőség egy HTTPS URL-cím megadása feladatbemenetként. Jelenleg a Media Services v3-as nem támogatja a HTTPS URL-címeken keresztüli darabolt átviteli kódolást.

#### <a name="examples"></a>Példák

* [Kódolás HTTPS-URL-címről a .NET használatával](stream-files-dotnet-quickstart.md)
* [Kódolás HTTPS-URL-címről REST-tel](stream-files-tutorial-with-rest.md)
* [Https URL-címből való kódolás CLI-vel](stream-files-cli-quickstart.md)
* [Https-URL-címről kódolva node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Munkahelyi bevitel létrehozása helyi fájlból

A bemeneti videó tárolható Media Service Asset, ebben az esetben hozzon létre egy bemeneti eszköz alapján egy fájlt (helyileg tárolt vagy az Azure Blob storage).

#### <a name="examples"></a>Példák

[Helyi fájl kódolása beépített készletekkel](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Feladatbevitel létrehozása alkivágással

Videó kódolásakor megadhatja, hogy a forrásfájl is levágjon vagy levágjon, és olyan kimenetet készítsen, amely csak a bemeneti videó nak csak egy kívánt részét tartalmazza. Ez a funkció minden [olyan átalakítással](https://docs.microsoft.com/rest/api/media/transforms) működik, amely a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) készletekkel vagy a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) készletekkel készült.

Megadhatja, hogy egy [feladat](https://docs.microsoft.com/rest/api/media/jobs/create) egyetlen klip egy videó on-demand vagy élő archívum (rögzített esemény). A feladat bemenete lehet egy eszköz vagy egy HTTPS URL-címet.

> [!TIP]
> Ha a videó egy allipjét a videó újrakódolása nélkül szeretné döngetni, fontolja meg az [Előszűrési jegyzékek](filters-dynamic-manifest-overview.md)használatát a Dynamic Packager segítségével.

#### <a name="examples"></a>Példák

Lásd a példákat:

* [Videoklip a .NET segítségével](subclip-video-dotnet-howto.md)
* [Videoklip rest-el](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Beépített készletek

A Media Services a következő beépített kódolási készleteket támogatja:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[A BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) egy beépített készlet beállítására szolgál a bemeneti videó szabványos kódolóval történő kódolásához.

A következő készletek jelenleg támogatottak:

- **EncoderNamedPreset.AACGoodQualityAudio**: egyetlen MP4 fájlt hoz létre, amely csak 192 kbps-re kódolt sztereó hangot tartalmaz.
- **EncoderNamedPreset.AdaptiveStreaming** (ajánlott): További információ: [bitráta-létra automatikus generálása](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental**: egy kísérleti készletet ad a tartalomérzékeny kódoláshoz. Bármilyen bemeneti tartalom esetén a szolgáltatás megpróbálja automatikusan meghatározni a rétegek optimális számát, valamint a megfelelő sávszélesség-átviteli és felbontási beállításokat az adaptív streamelési megjelenítéshez. Az alapul szolgáló algoritmusok idővel tovább fejlődnek. A kimenet mp4 fájlokat tartalmaz, amelyek video- és hanganyagokat tartalmaznak. További információ: [Kísérleti készlet tartalomérzékeny kódoláshoz.](content-aware-encoding.md)
- **EncoderNamedPreset.H264MultipleBitrate1080p**: nyolc GOP-hoz igazított MP4-fájlt állít elő, 6000 kbps-től 400 kbps-ig, valamint sztereó AAC hangot. Felbontás kezdődik 1080p és megy le 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p**: hat GOP-hoz igazított MP4-fájlt állít elő, 3400 kbps-től 400 kbps-ig, és sztereó AAC hangot. Felbontás kezdődik 720p és megy le 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD**: öt GOP-hoz igazított MP4-fájlt állít elő, 1600 kbps-től 400 kbps-ig, és sztereó AAC hangot. Felbontás kezdődik 480p és megy le 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p**: létrehoz egy MP4 fájlt, ahol a videó kódolt H.264 codec 6750 kbps és a kép magassága 1080 pixel, és a sztereó hang kódolva AAC-LC codec 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p**: létrehoz egy MP4 fájlt, ahol a videó h.264 kodekkel van kódolva 4500 kbps-en és 720 képpont képmagasságban, és a sztereó hang AAC-LC kodekmal van kódolva 64 kbps-on.
- **EncoderNamedPreset.H264SingleBitrateSD**: létrehoz egy MP4 fájlt, ahol a videó h.264 kodekmel van kódolva 2200 kbps és 480 képpont képmagasságban, és a sztereó hang AAC-LC kodekmal van kódolva 64 kbps-on.

A legfrissebb készletek listájának megtekintéséhez tekintse meg a [videók kódolásához használt beépített készleteket.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)

A készletek használatának módjáról a [Fájlok feltöltése, kódolása és streamelése című témakörben található.](stream-files-tutorial-with-api.md)

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[A StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) a bemeneti videó standard kódolóval történő kódolásakor használandó beállításokat írja le. Ezt a készletet az Átalakítási készletek testreszabásakor használja.

#### <a name="considerations"></a>Megfontolandó szempontok

Egyéni készletek létrehozásakor a következő szempontok érvényesek:

- Az AVC-tartalom magasságának és szélességének minden értékének a négy többszörösének kell lennie.
- Az Azure Media Services v3-as részében az összes kódolási bitsebesség bitper másodpercben van. Ez eltér a v2 API-kkal rendelkező készletektől, amelyek kilobit/második egységként használták a kilobitokat/ a második at. Ha például a v2-ben a bitráta 128 (kilobit/másodperc), akkor a v3-ban 128000 (bit/másodperc) lesz megadva.

### <a name="customizing-presets"></a>Készletek testreszabása

A Media Services teljes mértékben támogatja az összes érték beállításban való testreszabását, hogy megfeleljen az ön egyedi kódolási igényeinek és követelményeinek. A kódolókészletek testreszabását bemutató példákat az alábbi listában talál:

#### <a name="examples"></a>Példák

- [Készletek testreszabása a .NET segítségével](customize-encoder-presets-how-to.md)
- [Készletek testreszabása a CLI-vel](custom-preset-cli-howto.md)
- [Készletek testreszabása rest-el](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Előre beállított séma

A Media Services v3-ban a készletek erősen gépelt entitások az API-ban. Ezeknek az objektumoknak a "séma" definícióját az [Open API Specification (vagy Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)tartalmazza. Az előre beállított definíciók (például a **StandardEncoderPreset)** a [REST API,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [a .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (vagy más Media Services v3 SDK referenciadokumentáció) is megtekinthetők.

## <a name="scaling-encoding-in-v3"></a>Kódolás méretezése a v3-ban

Az adathordozó-feldolgozás méretezése a [Méretezés CLI-vel (Méretezés CLI-vel) (Méretezés CLI-vel)](media-reserved-units-cli-how-to.md)témakörben látható.

## <a name="billing"></a>Számlázás

A Media Services nem számláz a törölt vagy hibás feladatokért. Például egy feladat, amely elérte az 50%-os előrehaladást, és megszakította a számlázás nem 50%-a feladat perc. Csak a kész feladatokért kell fizetnie.

További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) tartalmaz.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

* [Feltöltés, kódolás és adatfolyam a Media Services használatával.](stream-files-tutorial-with-api.md)
* [Https-URL-címről kódolva beépített készletekhasználatával.](job-input-from-http-how-to.md)
* [Helyi fájl kódolása beépített készletekkel.](job-input-from-local-file-how-to.md)
* [Hozzon létre egy egyéni készletet az adott forgatókönyv vagy eszközkövetelmények célzásához.](customize-encoder-presets-how-to.md)
