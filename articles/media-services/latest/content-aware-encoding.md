---
title: Tartalombarát kódolás készlete - Azure Media Services
description: Ez a cikk a Microsoft Azure Media Services 3-as verzióban található tartalomérzékeny kódolást ismerteti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 3ea6c4226a59ba020a477cc5811033ff3dc3c2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772097"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>A tartalomérzékeny kódolási készlet segítségével keresse meg egy adott felbontás optimális bitrátaértékét

Annak érdekében, hogy a tartalom [adaptív sávszélességű streameléssel](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)készüljön fel, a videót több átviteli sebességű (magastól alacsonyig) kódolásra kell kódolni. Ez biztosítja a minőség kecses romlását, mivel a bitráta csökken, így a videó felbontása is. Az ilyen többszörös átviteli sebességű kódolás egy úgynevezett kódolási létrát használ – egy felbontások és bitráták táblázatát, lásd a Media Services [beépített kódolási készleteit.](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)

Tisztában kell lennie a feldolgozás alatt álló tartalommal, és testre kell szabnia / be kell hangolnia a kódolási létrát az egyes videók összetettségére. Minden felbontásnál van egy bitráta, amelyen túl a minőség növekedése nem észlelhető – a kódoló ezen az optimális bitráta-értéken működik. Az optimalizálás következő szintje a felbontások kiválasztása a tartalom alapján – például egy PowerPoint-bemutató videója nem előnyös a 720p alá. Tovább haladva a kódoló feladata lehet, hogy optimalizálja a beállításokat minden lövés a videó. 

A Microsoft [Adaptive Streaming](autogen-bitrate-ladder.md) készlete részben megoldja a forrásvideók minőségének és felbontásának változékonyságának problémáját. Ügyfeleink különböző összetételű tartalom, néhány 1080p, mások 720p, és néhány SD és alacsonyabb állásfoglalások. Továbbá, nem minden forrás tartalom kiváló minőségű mezzanines a film-vagy TV-stúdiók. Az Adaptive Streaming készlet úgy oldja meg ezeket a problémákat, hogy biztosítja, hogy a bitráta létra soha ne lépje túl a bemeneti mezzanine felbontását vagy átlagos bitráját. Ez a készlet azonban nem vizsgálja meg a felbontáson és a bitrátonon kívül más forrástulajdonságokat.

## <a name="the-content-aware-encoding"></a>A tartalombarát kódolás 

A tartalomérzékeny kódolási készlet kiterjeszti az "adaptív sávszélességű streamelési" mechanizmust azáltal, hogy olyan egyéni logikát tartalmaz, amely lehetővé teszi a kódoló számára, hogy egy adott felbontásoptimális bitráta-értékét keresse, de anélkül, hogy kiterjedt számítási elemzést igényelne. Ez az előre beállított létrehoz egy sor GOP-igazított MP4s. Bármilyen bemeneti tartalom esetén a szolgáltatás elvégzi a bemeneti tartalom kezdeti könnyű elemzését, és az eredmények alapján határozza meg a rétegek optimális számát, a megfelelő sávszélesség-átviteli és felbontási beállításokat az adaptív streameléshez. Ez az előre beállított beállítás különösen hatékony az alacsony és közepes összetettségű videók esetében, ahol a kimeneti fájlok alacsonyabb bitsebességgel lesznek, mint az Adaptive Streaming készlet, de olyan minőségben, amely még mindig jó élményt nyújt a nézőknek. A kimenet MP4 fájlokat tartalmaz video- és audioátközi

Az alábbi mintagrafikonok az összehasonlítást olyan minőségi mutatók kal kitaszatot, mint a [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) és a [VMAF.](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion) A forrás jött létre összefűzése rövid klipek nagy komplexitás felvételek filmek és tv-műsorok, célja, hogy hangsúlyozzák a kódoló. Definíció szerint ez az előre beállított eredményeket hoz létre, amelyek tartalomról tartalomra változnak – ez azt is jelenti, hogy egyes tartalmak esetében előfordulhat, hogy a bitráta vagy a minőség javulása nem csökken jelentősen.

![Sebességtorzítás (RD) görbe PSNR használatával](media/content-aware-encoding/msrv1.png)

**1. ábra: Sebességtorzítási (RD) görbe PSNR-mérőszám használatával a nagy összetettségű forráshoz**

![Sebességtorzítás (RD) görbe vmaf használatával](media/content-aware-encoding/msrv2.png)

**2. ábra: Sebességtorzítási (RD) görbe VMAF-metrikával a nagy összetettségű forráshoz**

Az alábbiakban a forrástartalom egy másik kategóriájának eredményeit találjuk, ahol a kódoló meg tudta állapítani, hogy a bemenet gyenge minőségű volt (sok tömörítési összetevő az alacsony bitráta miatt). Ne feledje, hogy a tartalomérzékeny készlettel a kódoló úgy döntött, hogy csak egy kimeneti réteget hoz létre – elég alacsony bitsebességgel, hogy a legtöbb ügyfél az elakadás nélkül játszhassa le az adatfolyamot.

![Távoli asztali kapcsolat görbéje PSNR használatával](media/content-aware-encoding/msrv3.png)

**3. ábra: Távoli asztali görbe PSNR-t használ a gyenge minőségű bevitelhez (1080p-nél)**

![Távoli asztali görbe a VMAF használatával](media/content-aware-encoding/msrv4.png)

**4. ábra: Távoli asztali görbe VMAF használatával gyenge minőségű bevitelhez (1080p-nél)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>A tartalombarát kódolási készlet használata 

Létrehozhat olyan átalakításokat, amelyek ezt a készletet a következőképpen használják. 

> [!TIP]
> A transzformkimeneteket használó oktatóanyagokról a [Következő lépések](#next-steps) című szakaszban található. A kimeneti eszköz a Media Services streamelési végpontjaiból is kézbesíthető olyan protokollokban, mint az MPEG-DASH és a HLS (ahogy az oktatóanyagok ban látható).


```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Az `ContentAwareEncoding` előre beállított kódolási feladatok számlázása a kimeneti percek alapján kerül számlázásra. 

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Videók feltöltése, kódolása és streamelése a Media Services v3-as számával](stream-files-tutorial-with-api.md)
* [Oktatóanyag: Távoli fájl kódolása URL alapján és streamelés - REST](stream-files-tutorial-with-rest.md)
* [Oktatóanyag: Egy távoli fájl kódolása URL alapján és a videó streamelése - CLI](stream-files-cli-quickstart.md)
* [Oktatóanyag: Távoli fájl kódolása URL alapján és a videó streamelése - .NET](stream-files-dotnet-quickstart.md)
* [Oktatóanyag: Egy távoli fájl kódolása URL alapján és a videó streamelése - Node.js](stream-files-nodejs-quickstart.md)
