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
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: e1fc58db8f933ae122801f492fbbafdb905c7dda
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910410"
---
# <a name="encoding-with-media-services"></a>Kódolás a Media Services használatával

A Media Services kódolása a digitális videót és/vagy hangot tartalmazó fájlok egyik standard formátumból egy másikba való konvertálásának folyamatára vonatkozik, amelynek célja, hogy (a) csökkentse a fájlok méretét, és/vagy (b) olyan formátumot állít elő, amely kompatibilis a következővel: eszközök és alkalmazások széles köre. Ezt a folyamatot a videó tömörítésének vagy az átkódolásnak is nevezzük. Tekintse meg az [adattömörítést](https://en.wikipedia.org/wiki/Data_compression) és a [Mi a kódolás és az átkódolás?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) című témakört a fogalmak további megvitatására.

A videók általában [progresszív letöltéssel](https://en.wikipedia.org/wiki/Progressive_download) vagy [adaptív sávszélességű adatfolyam](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)használatával érkeznek meg az eszközökre és alkalmazásokra. 

* A progresszív letöltéssel történő továbbításhoz használhatja a Azure Media Servicest a digitális médiafájlok [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) -fájllá való átalakításához, amely tartalmazza a [H. 264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) kodekkel kódolt videókat, valamint az AAC-vel kódolt hanganyagot. [ ](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)kodek. Ez az MP4-fájl a Storage-fiókban lévő eszközre íródik. A fájl közvetlen letöltéséhez használhatja az Azure Storage API-jait vagy SDK-kat (például a [Storage REST API](../../storage/common/storage-rest-api-auth.md) vagy a [.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)-t). Ha a kimeneti eszközt egy adott tároló nevével hozta létre a tárolóban, használja ezt a helyet. Ellenkező esetben a Media Services használatával [listázhatja az objektum-tároló URL-címeit](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* A tartalom adaptív sávszélességű adatfolyamként történő kézbesítésének előkészítéséhez a köztes fájlt több bitrátán kell kódolni (magasról alacsonyra). A minőség kecses átállásának biztosítása érdekében, mivel a sávszélesség alacsonyabb, ezért a videó felbontása. Ez egy úgynevezett kódolási létrát eredményez, amely a felbontások és a bitráta (lásd: [automatikusan generált adaptív sávszélességű létrák](autogen-bitrate-ladder.md)) táblázata. Media Services használatával több bitrátán is kódolhatja a köztes fájlokat – ennek során az MP4-fájlok és a hozzájuk kapcsolódó, a Storage-fiókban lévő eszközre írt társított adatfolyam-konfigurációs fájlok készletét fogja kapni. Ezután a Media Services [dinamikus csomagolási](dynamic-packaging-overview.md) funkciójának használatával továbbíthatja a videót a streaming protokollok, például az [MPEG-Dash](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) és a [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)segítségével. Ehhez létre kell hoznia egy [adatfolyam-keresőt](streaming-locators-concept.md) , és a támogatott protokolloknak megfelelő streaming URL-címeket kell létrehoznia, amelyek az eszközökre és alkalmazásokra a képességeik alapján átadhatók.

Az alábbi ábrán a dinamikus csomagolással rendelkező igény szerinti kódoláshoz tartozó munkafolyamat látható.

![Dinamikus csomagolás](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Ez a témakör nyújt útmutatást tartalmait a Media Services v3 kódolással.

## <a name="transforms-and-jobs"></a>Átalakítások és feladatok

Media Services v3 kódoláshoz létre kell hoznia egy átalakítót és [](https://docs.microsoft.com/rest/api/media/transforms) egy [feladatot](https://docs.microsoft.com/rest/api/media/jobs). Az átalakítás egy receptet határoz meg a kódolási beállításokhoz és kimenetekhez; a feladattípus a recept egy példánya. További információkért lásd: [átalakítások és feladatok](transforms-jobs-concept.md)

A Media Services encoding, amikor a szolgáltatás használatával ossza meg a kódoló a bemeneti fájlok feldolgozásának módja. Például megadhatja a videó felbontást és/vagy a kívánt hang csatornák száma kódolt tartalmában. 

Ismerkedhet meg gyorsan az ágazatban kialakult bevált gyakorlaton alapuló ajánlott a beépített beállítások egyikét, vagy dönthet úgy, amelyekre az adott forgatókönyv vagy eszközkövetelmények beállított egyéni. További információkért lásd: [Egyéni átalakító Encode](customize-encoder-presets-how-to.md). 

Az MP4-fájl (ok) előállításakor a Media Encoder Standardtól kezdődően a (z) 2019-as és újabb, a kimeneti eszközhöz hozzáadott új. MPI-fájl jön létre. Ez az MPI-fájl a [dinamikus csomagolás](dynamic-packaging-overview.md) és a folyamatos átviteli forgatókönyvek teljesítményének növelésére szolgál.

> [!NOTE]
> Ne módosítsa vagy távolítsa el az MPI-fájlt, vagy az ilyen fájl létezésére (vagy nem) való függőséget a szolgáltatásban.

### <a name="creating-job-input-from-an-https-url"></a>Feladathoz tartozó bemenet létrehozása HTTPS URL-címről

Amikor feladatokat küld a videók feldolgozásához, meg kell tudnia Media Services hol található a bemeneti videó. Az egyik lehetőség egy HTTPS URL-cím megadása feladatként. A Media Services v3 jelenleg nem támogatja a HTTPS URL-címeken keresztüli darabolásos átvitel kódolását. 

#### <a name="examples"></a>Példák

* [Kódolás HTTPS-alapú URL-címről a .NET-tel](stream-files-dotnet-quickstart.md)
* [Kódolás HTTPS URL-címről a REST használatával](stream-files-tutorial-with-rest.md)
* [Kódolás HTTPS URL-címről a parancssori felületről](stream-files-cli-quickstart.md)
* [Kódolás HTTPS URL-címről a Node. js használatával](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Feladathoz tartozó bemenet létrehozása helyi fájlból

A bemeneti videó adathordozó-szolgáltatási eszközként tárolható, ebben az esetben egy bemeneti eszközt hoz létre egy fájl alapján (helyileg vagy az Azure Blob Storage-ban tárolva). 

#### <a name="examples"></a>Példák

[Helyi fájl kódolása beépített beállításkészletek használatával](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Feladathoz tartozó bemenet létrehozása kivágással

Videó kódolásakor megadhatja, hogy a forrásfájl kivágása vagy bevágása is megtörténjen, és olyan kimenetet hozzon létre, amely csak a bemeneti videó kívánt részével rendelkezik. Ez a funkció a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -előállítók vagy a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) -előállítók használatával létrehozott bármely [átalakítással](https://docs.microsoft.com/rest/api/media/transforms) működik. 

Megadhatja, hogy létrehoz egy [feladatot](https://docs.microsoft.com/rest/api/media/jobs/create) egyetlen videoklipet igény szerint vagy élő archívumban (rögzített esemény). A feladathoz tartozó bemenet lehet egy eszköz vagy egy HTTPS URL-cím is.

> [!TIP]
> Ha a videó újrakódolása nélkül szeretné továbbítani a videó sublip, érdemes lehet a [dinamikus csomagoló használatával előszűrési jegyzékeket](filters-dynamic-manifest-overview.md)használni.

#### <a name="examples"></a>Példák

Példák:

* [Videó kivágása a .NET-tel](subclip-video-dotnet-howto.md)
* [Videó kivágása a REST-tel](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Beépített készletek

A Media Services jelenleg a következő beépített kódolási beállításokat támogatja:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

A [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) egy beépített beállításkészlet beállítására szolgál, amely a bemeneti videót a szabványos kódolóval kódolja. 

A következő készletek jelenleg támogatja:

- **EncoderNamedPreset.AACGoodQualityAudio** -kódolású 192 Kb/s, csak sztereó hang tartalmazó egyetlen MP4-fájl eredményez.
- **EncoderNamedPreset.AdaptiveStreaming** (ajánlott). További információkért lásd: [skála automatikus generálásához](autogen-bitrate-ladder.md).
- **EncoderNamedPreset. ContentAwareEncodingExperimental** – kísérleti beállításkészletet tesz elérhetővé a Content-Aware kódoláshoz. A szolgáltatás bármilyen bemeneti tartalmat megpróbál automatikusan meghatározni a rétegek optimális számát, a megfelelő sávszélesség-és megoldási beállításokat az adaptív adatfolyam-továbbítással. Az alapul szolgáló algoritmusok az idő múlásával továbbra is fejlődnek lesznek. A kimenet video-és hangalapú MP4-fájlokat fog tartalmazni. További információ: [kísérleti beállításkészlet a Content-Aware kódoláshoz](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p** – 8 Képcsoporttal igazított MP4-fájlokat, és a 6000 KB/s 400 kb/s és sztereó AAC hang eredményez. Megoldás 1080p-nél kezdődik, és lefelé 360 p felbontású kerül.
- **EncoderNamedPreset.H264MultipleBitrate720p** -6 Képcsoporttal igazított MP4-fájlokat, és a 3400 kbps 400 kb/s és sztereó AAC hang eredményez. Feloldási 720 p elindul, és lefelé 360 p felbontású kerül.
- **EncoderNamedPreset.H264MultipleBitrateSD** – 5 Képcsoporttal igazított MP4-fájlokat, és a 1600 KB/s 400 kb/s és sztereó AAC hang eredményez. Megoldás 480p-nél kezdődik, és lefelé 360 p felbontású kerül.
- **EncoderNamedPreset. H264SingleBitrate1080p** – egy MP4-fájlt hoz létre, ahol a videó a H. 264 kodekkel van kódolva, 6750 kbps sebességgel, a kép magassága pedig 1080 képpont, a sztereó hang pedig AAC-LC kodekkel van kódolva, 64 kbps sebességgel.
- **EncoderNamedPreset. H264SingleBitrate720p** – egy MP4-fájlt hoz létre, ahol a videó a H. 264 kodekkel van kódolva, 4500 kbps sebességgel, a kép magassága pedig 720 képpont, a sztereó hang pedig AAC-LC kodekkel van kódolva, 64 kbps sebességgel.
- **EncoderNamedPreset. H264SingleBitrateSD** – egy MP4-fájlt hoz létre, ahol a videó a H. 264 kodekkel van kódolva, 2200 kbps sebességgel, a kép magassága pedig 480 képpont, a sztereó hang pedig AAC-LC kodekkel van kódolva, 64 kbps sebességgel.

A legfrissebb beállításkészletek listájának megtekintéséhez tekintse meg a [videók kódolásához használható beépített beállításkészleteket](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Ha szeretné megtekinteni az előzetes beállítások használatát, tekintse meg a [feltöltési, a kódolási és a streaming fájlokat](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

A [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) a bemeneti videó standard kódolóval való kódolásakor használandó beállításokat ismerteti. Ezzel a készlet, átalakító készletek testreszabásához. 

#### <a name="considerations"></a>Megfontolandó szempontok

Egyéni beállításkészletek létrehozásakor a következő szempontokat kell figyelembe venni:

- Az AVC-tartalom magasságának és szélességének összes értékének a 4-es többszörösének kell lennie.
- A Azure Media Services V3 esetében az összes kódolási bitrátát bit/másodpercben kell kiszámítani. Ez különbözik az előre beállított v2 API-kkal, amelyek az egységhez kilobit/másodpercet használnak. Ha például a v2-es bitrátát 128 (kilobit/másodperc) értékre adták, a v3-as verzióban a 128000 (BITS/Second) értéket adja meg.

### <a name="customizing-presets"></a>Előzetes beállítások testreszabása

A Media Services teljes körűen támogatja az adott kódolási igények és követelmények készletek található értékek némelyike testreszabása. A kódoló-beállításkészletek testreszabását bemutató példákat a következő témakörben talál:

#### <a name="examples"></a>Példák

- [Készletek testreszabása .NET-tel](customize-encoder-presets-how-to.md)
- [Beállításkészlet testreszabása a parancssori felülettel](custom-preset-cli-howto.md)
- [Beállítások testreszabása REST használatával](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Előre definiált séma

A Media Services v3-as verzióban az előzetes beállítások az API-ban erősen beírt entitások. Ezekhez az objektumokhoz a "séma" definíciója [nyílt API-specifikációban (vagy hencegés)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)található. A [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), a [.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) -ban (vagy más Media Services v3 SDK-dokumentációban) is megtekintheti az előre definiált definíciókat (például **StandardEncoderPreset**).

## <a name="scaling-encoding-in-v3"></a>V3 a kódolás méretezése

A médiafájlok feldolgozásának méretezéséhez lásd: [Méretezés a CLI-vel](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>További lépések

* [Feltöltése, kódolása és streamelése a Media Services használatával](stream-files-tutorial-with-api.md)
* [Kódolás HTTPS-URL-címről beépített beállításkészletek használatával](job-input-from-http-how-to.md)
* [Helyi fájl kódolása beépített beállításkészletek használatával](job-input-from-local-file-how-to.md)
* [Egyéni beállításkészlet kiépítése az adott forgatókönyv vagy eszköz követelményeinek megcélzásához](customize-encoder-presets-how-to.md)