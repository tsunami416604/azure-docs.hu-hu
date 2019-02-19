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
ms.date: 02/17/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 52e7fdf6de25300d4f78ee9822aca4ad83f646e9
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408425"
---
# <a name="encoding-with-media-services"></a>Kódolás a Media Services használatával

Az Azure Media Services lehetővé teszi, hogy az kiváló minőségű médiafájlt kódolandó, hogy a böngészők és eszközök széles lejátszhatók. Például előfordulhat, hogy az Apple HLS vagy MPEG DASH formátumában szeretné streamelni a tartalmakat. Ez a témakör nyújt útmutatást tartalmait a Media Services v3 kódolással.

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

- **EncoderNamedPreset.AdaptiveStreaming** (ajánlott). További információkért lásd: [skála automatikus generálásához](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.AACGoodQualityAudio** -kódolású 192 Kb/s, csak sztereó hang tartalmazó egyetlen MP4-fájl eredményez.
- **EncoderNamedPreset.H264MultipleBitrate1080p** – 8 Képcsoporttal igazított MP4-fájlokat, és a 6000 KB/s 400 kb/s és sztereó AAC hang eredményez. Megoldás 1080p-nél kezdődik, és lefelé 360 p felbontású kerül.
- **EncoderNamedPreset.H264MultipleBitrate720p** -6 Képcsoporttal igazított MP4-fájlokat, és a 3400 kbps 400 kb/s és sztereó AAC hang eredményez. Feloldási 720 p elindul, és lefelé 360 p felbontású kerül.
- **EncoderNamedPreset.H264MultipleBitrateSD** – 5 Képcsoporttal igazított MP4-fájlokat, és a 1600 KB/s 400 kb/s és sztereó AAC hang eredményez. Megoldás 480p-nél kezdődik, és lefelé 360 p felbontású kerül.<br/><br/>További információkért lásd: [feltöltése, kódolása és fájlok folyamatos](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset-preset"></a>StandardEncoderPreset előbeállítás

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) kódolás a bemeneti videó a standard szintű Encoder használt beállításokat ismerteti. Ezzel a készlet, átalakító készletek testreszabásához. 

#### <a name="custom-presets"></a>Egyéni beállításkészletek

A Media Services teljes körűen támogatja az adott kódolási igények és követelmények készletek található értékek némelyike testreszabása. Használja a **StandardEncoderPreset** készlet, átalakító készletek testreszabásához. A részletes magyarázatokat és a példában: [kódoló beállításkészletek testreszabása](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>V3 a kódolás méretezése

Jelenleg a felhasználóknak kell az Azure portal vagy a Media Services v2 API-k használatával állítsa be a kérelemegység (leírtak szerint [médiafeldolgozás skálázás](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>További lépések

* [Átalakítások és feladatok](transforms-jobs-concept.md)
* [Feltöltése, kódolása és streamelése a Media Services használatával](stream-files-tutorial-with-api.md)
