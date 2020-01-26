---
title: A Content-Aware kódoláshoz beállított beállításkészlet – Azure Media Services |
description: Ez a cikk az Microsoft Azure Media Services v3 tartalommal kompatibilis kódolását ismerteti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 34b4386d536a9845c8a48e7e54612548367b52bc
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759346"
---
# <a name="a-preset-for-content-aware-encoding"></a>A Content-Aware kódoláshoz beállított beállításkészlet

A tartalom [adaptív sávszélességű adatfolyamként](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)történő továbbításához a videót több átviteli sebességre kell kódolni (magasról alacsonyra). A minőség zavartalan romlásának biztosítása érdekében, mivel a bitrátát csökkenti, így a videó felbontása is megtörténik. Ez egy úgynevezett kódolási létrát eredményez, amely a felbontások és a bitráták táblázata. Tekintse meg a Media Services [beépített kódolási előkészleteket](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

## <a name="overview"></a>Áttekintés

Az egy előre definiált – az összes videós megközelítésre való áttérés iránti érdeklődés egyre nagyobb, miután a Netflix közzétette a [blogot](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) a 2015 decemberében. Azóta a piactéren több, Content-Aware kódolásra szolgáló megoldás jelent meg. Tekintse meg [ezt a cikket](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) . Ennek az az ötlete, hogy tisztában legyen a tartalommal – a kódolási létrát testreszabhatja vagy beállíthatja az egyes videók összetettsége érdekében. Minden egyes megoldásnál van egy bitráta, amelyen kívül a minőség növekedése nem érzékelhető – a kódoló ebben az optimális bitráta-értékben működik. A következő optimalizálási szint a tartalomon alapuló felbontások kiválasztására szolgál, például egy PowerPoint-bemutató videója nem éri el a 720p-t. Továbbra is a kódoló feladata, hogy optimalizálja a videón belüli egyes lövések beállításait. A Netflix [ezt a megközelítést](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) ismertette a 2018-es verzióban.

A 2017-es évek elején a Microsoft kiadta az [adaptív adatfolyam](autogen-bitrate-ladder.md) -készletet, amely a forrásként szolgáló videók minőségének és megoldásának változékonyságával kapcsolatos problémát orvosolja. Ügyfeleink többféle tartalommal rendelkeztek, néhányat 1080p-ra, másokat pedig 720p-ra, és néhányat SD és alacsonyabb felbontásban. Továbbá, nem minden forrás tartalma kiváló minőségű, film-vagy TV-stúdiókból készült. Az adaptív adatfolyam-készlet ezeket a problémákat úgy oldja meg, hogy a bitráta-létrán soha nem haladja meg a bemeneti időpontok felbontását vagy az átlagos bitrátát.

Az új, tartalmakat támogató kódolási beállításkészlet kiterjeszti ezt a mechanizmust olyan egyéni logika beépítésével, amely lehetővé teszi, hogy a kódoló egy adott megoldás optimális sebességét adja meg, azonban anélkül, hogy kiterjedt számítási elemzésre lenne szükség. Ez a beállításkészlet GOP-igazítású MP4 állít elő. A szolgáltatás a bemeneti tartalom kezdeti könnyű elemzését, valamint az eredmények használatával határozza meg a rétegek optimális számát, a megfelelő bitrátát és a megoldási beállításokat az adaptív adatfolyam-továbbítással. Ez a beállításkészlet különösen alacsony és közepes komplexitású videók esetében érvényes, ahol a kimeneti fájlok alacsonyabb sávszélességű lesznek, mint az adaptív adatfolyam-készlet, de olyan minőségben, amely továbbra is jó élményt nyújt a nézők számára. A kimenet video-és hangalapú MP4-fájlokat fog tartalmazni

Tekintse meg a következő, a minőségi mérőszámokkal (például [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) és [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)) való összehasonlítást bemutató ábrákat. A forrást úgy hozták létre, hogy összefűzött néhány rövid klipet a nagy komplexitású felvételektől a filmek és a TÉVÉMŰSORok alapján, hogy kihangsúlyozzák a kódolót. A definíció szerint ez a készlet olyan eredményeket hoz létre, amelyek a tartalomtól a tartalomtól függően változnak – ez azt is jelenti, hogy egyes tartalmak esetében nem lehet jelentős mértékben csökkenteni a bitrátát vagy javítani a minőséget.

![A torzítás (RD) görbéje a PSNR használatával](media/cae-experimental/msrv1.png)

**1. ábra: a ráta-torzítás (RD) görbe használata a PSNR metrikával a nagy komplexitású forráshoz**

![A torzítás (RD) görbéje a VMAF használatával](media/cae-experimental/msrv2.png)

**2. ábra: a ráta-torzítás (RD) görbe használata a VMAF metrikával a nagy komplexitású forráshoz**

Az alábbiakban láthatók a tartalomforrás egy másik kategóriájára vonatkozó eredmények, ahol a kódoló meghatározta, hogy a bemenet gyenge minőségű-e (az alacsony sávszélesség miatt sok tömörítési összetevő). Vegye figyelembe, hogy a "Content-Aware" készlettel a kódoló úgy döntött, hogy csak egy kimeneti réteget hoz létre – egy elég alacsony bitráta mellett, így a legtöbb ügyfél nem fog tudni lejátszani az adatfolyamot.

![Távoli asztali görbe a PSNR használatával](media/cae-experimental/msrv3.png)

**3. ábra: távoli asztali görbe a PSNR használatával alacsony minőségi bevitelhez (1080p)**

![Távoli asztali görbe a VMAF használatával](media/cae-experimental/msrv4.png)

**4. ábra: távoli asztali görbe a VMAF használatával alacsony minőségi bevitelhez (1080p)**

## <a name="use-the-experimental-preset"></a>A kísérleti beállításkészlet használata

A következő módon hozhat létre olyan átalakításokat, amelyek ezt az beállításkészletet használják. Ha [például](stream-files-tutorial-with-api.md)a következő oktatóanyagot használja, a kódot a következőképpen frissítheti:

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
> A mögöttes algoritmusok további fejlesztéseknek vannak kitéve. Az idő múlásával módosulhat a bitráta-létrák generálásához használt logika, amelynek célja, hogy egy robusztus algoritmust biztosítson, és alkalmazkodjon a különböző bemeneti feltételekhez. Az ezt az beállításkészletet használó kódolási feladatok továbbra is a kimeneti percek alapján lesznek kiszámlázva, és a kimeneti eszköz továbbítható az adatfolyam-végpontokról a protokollok, például a DASH és a HLS.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a videók optimalizálására szolgáló új lehetőséget, meghívjuk, hogy próbálja ki. A jelen cikk végén található hivatkozásokkal küldhet nekünk visszajelzést.
