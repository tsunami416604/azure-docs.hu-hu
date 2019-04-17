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
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 532701eb2c5e92e5443f69c464b561d6fa242598
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617631"
---
# <a name="encoding-with-media-services"></a>Kódolás a Media Services használatával

Az Azure Media Services lehetővé teszi, hogy az kiváló minőségű médiafájlt kódolandó adaptív sávszélességű MP4-fájlokat, így a tartalom a böngészők és eszközök széles lejátszhatók. Egy sikeres Media Services kódolási feladat létrehoz egy kimeneti objektumot az adaptív sávszélességű MP4-és adatfolyam-konfigurációs fájlok. A konfigurációs fájlok közé tartoznak a .ism, .ismc, .mpi és egyéb fájlokat, amelyek nem szabad módosítani. Ha a kódolási feladat elkészült, akkor kihasználhatja [dinamikus csomagolási](dynamic-packaging-overview.md) és streamelésének megkezdéséhez.

Győződjön meg arról, videók, a kimenetben az eszköz érhető el az ügyfelek számára a lejátszás, létre kell hoznia egy **Streamelési lokátor** és létrehozása a streamelési URL-címek. Ezt követően a jegyzékfájlban megadott formátumnak megfelelően, az ügyfelek az adatfolyamban kaphatja a protokoll választotta.

Az alábbi ábrán látható, a dinamikus csomagolás munkafolyamat igényalapú streameléshez.

![A dinamikus csomagolás](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Ez a témakör nyújt útmutatást tartalmait a Media Services v3 kódolással.

## <a name="transforms-and-jobs"></a>Átalakítások és feladatok

Kódolás a Media Services v3-as, létre kell egy [átalakítása](https://docs.microsoft.com/rest/api/media/transforms) és a egy [feladat](https://docs.microsoft.com/rest/api/media/jobs). Egy-egy átalakítási határozza meg a recept, a kódolási beállítások és kimenetek, és a feladat a recept egy példányát. További információkért lásd: [átalakítások és feladatok](transforms-jobs-concept.md)

A Media Services encoding, amikor a szolgáltatás használatával ossza meg a kódoló a bemeneti fájlok feldolgozásának módja. Például megadhatja a videó felbontást és/vagy a kívánt hang csatornák száma kódolt tartalmában. 

Ismerkedhet meg gyorsan az ágazatban kialakult bevált gyakorlaton alapuló ajánlott a beépített beállítások egyikét, vagy dönthet úgy, amelyekre az adott forgatókönyv vagy eszközkövetelmények beállított egyéni. További információkért lásd: [Egyéni átalakító Encode](customize-encoder-presets-how-to.md). 

Január 2019, kezdve kódolás a Media Encoder Standard MP4-fájl létrehozására, ha .mpi új fájl jön létre, és hozzáadódik a kimeneti adategység. A MPI-fájl az célja, hogy a teljesítmény javítása [dinamikus csomagolási](dynamic-packaging-overview.md) és adatfolyam-forgatókönyvekhez.

> [!NOTE]
> Ne módosítsa vagy távolítsa el az MPI-fájlt, és minden olyan függőséget is a létezik-e a service-ben (vagy sem) egy souboru.

## <a name="built-in-presets"></a>Beépített készletek

A Media Services jelenleg a következő beépített kódolási beállításokat támogatja:  

### <a name="builtinstandardencoderpreset-preset"></a>BuiltInStandardEncoderPreset preset

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

### <a name="standardencoderpreset-preset"></a>StandardEncoderPreset előbeállítás

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) kódolás a bemeneti videó a standard szintű Encoder használt beállításokat ismerteti. Ezzel a készlet, átalakító készletek testreszabásához. 

#### <a name="considerations"></a>Megfontolandó szempontok

Egyéni készletek létrehozásakor a következő szempontokat kell figyelembe venni:

- Minden értékét magasságát és szélességét AVC tartalom kezelésére kell költeni 4 többszörösének kell lennie.
- Az Azure Media Services v3-as a kódolási bitsebességre való átkódolása összes bit / másodperc. Ez különbözik a készletek a v2 API-kkal használt egységet kilobit/másodperc. Például ha az átviteli sebesség a v2-ben van megadva, 128 (kilobit/másodperc), a v3-as volna meg akkor 128000 (bit/másodperc).

#### <a name="examples"></a>Példák

A Media Services teljes körűen támogatja az adott kódolási igények és követelmények készletek található értékek némelyike testreszabása. Azt mutatják be, hogyan szabhatja testre a kódoló készletek példákért lásd:

- [.NET-tel készletek testreszabása](customize-encoder-presets-how-to.md)
- [CLI-vel beállításkészletek testreszabása](custom-preset-cli-howto.md)
- [REST-beállításkészletek testreszabása](custom-preset-rest-howto.md)

## <a name="scaling-encoding-in-v3"></a>V3 a kódolás méretezése

Méretezhető médiafeldolgozás, lásd: [CLI-vel méretezési](media-reserved-units-cli-how-to.md).

## <a name="next-steps"></a>További lépések

* [Kódolás a beépített készlet használatával HTTPS URL-címet](job-input-from-http-how-to.md)
* [Beépített készlet használatával egy helyi fájl kódolása](job-input-from-local-file-how-to.md)
* [Az egyedi, amelyekre az adott forgatókönyv vagy eszközkövetelmények készletek létrehozása](customize-encoder-presets-how-to.md)
* [Feltöltése, kódolása és streamelése a Media Services használatával](stream-files-tutorial-with-api.md)
