---
title: Egy kísérleti beállított tartalom-kompatibilis Encoding – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti a tartalom-kompatibilis kódolás az Azure Media Servicesben
services: media-services
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: sethm
ms.custom: ''
ms.openlocfilehash: 3c50502a8b873503ee937914fac5f2d92cb23a2b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288381"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Tartalom-kompatibilis kódolási előbeállítást kísérleti

Annak érdekében, hogy a tartalom előkészítése a kézbesítési [adaptív sávszélességű streamelés](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), videó kell kódolni, adaptív átviteli sebességű (alacsony, magas). Minőségű, biztonságos teljesítménycsökkenése biztosítása érdekében, az átviteli sebesség van-e csökkenteni a videó felbontása van így. Ennek eredményeként egy úgynevezett kódolási létra – egy táblát felbontásra és bitsebességre való átkódolása, láthatja az egyes rögzített a kódolási szolgáltatás, például [H264MultipleBitrate1080p](../previous/media-services-mes-preset-h264-multiple-bitrate-1080p.md).

## <a name="overview"></a>Áttekintés

Érdekli, túl egy egy-készlet-megfelelő – minden-videók megközelítés növelni a Netflix közzététele után a [blog](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) a 2015 decemberében. Azóta több megoldás tartalom-kompatibilis Encoding adtak ki a piactéren; Lásd: [Ez a cikk](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) áttekintését. A cél, hogy a tartalom – testreszabásához, vagy az egyéni videó összetettségétől, a kódolási létra hangolása vegye figyelembe. Minden egyes felbontású nincs egy sávszélességű, amelyek nem perceptive minőségének növelése – a kódoló működik, ez az optimális átviteli sebesség érték. A következő optimalizálás szintjét, hogy válassza ki a megoldások a tartalom alapján – például a PowerPoint-bemutató videó nem részesül folyamatos 720p alatt. Továbblép, a kódoló optimalizálni egyes képernyőkép-készítés belül a videó beállításait biztosítja a is. Netflix leírt [Ez a megközelítés](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) 2018-ban.

A korai 2017, Microsoft, amely a [adaptív Streamelés](autogen-bitrate-ladder.md) előre definiált, a probléma a minőségi változékonyságát és a forrásvideókat feloldása. Ügyfeleink különböző vegyesen tartalmat, néhány 1080 p, mások 720 p és SD és kisebb felbontások esetén néhány korábban. Emellett nem minden tartalmat nem jó minőségű mezzanines filmek és TV studios. Az adaptív ezeket a problémákat előre beállított címek Streamelés azáltal, hogy a skála soha nem meghaladja a felbontás vagy az átlagos átviteli sebesség, a bemeneti "mezzanine" formátumú.

A kísérleti tartalom-kompatibilis kódolási előbeállítás mechanizmus, kiterjeszti beépítése az egyéni logikát, amely lehetővé teszi, hogy az optimális átviteli sebesség értékét egy adott felbontás, de anélkül, hogy a széles körű számítási elemzési hledání kódoló. Az eredmény az, hogy az új készletet hoz létre, amely rendelkezik, mint az adaptív Streameléshez készletet, de jobb minőségben alacsonyabb sávszélességű kimenet. Tekintse meg a következő minta diagramok azt mutatják be, az összehasonlítást, például a minőségi mérőszámok segítségével [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) és [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). A forrás hozta létre, filmek a magas összetettséget helyességének rövid klipeket összetűzésének, és a kódoló hangsúlyozzák TV-műsorok tekinthető. Az előre beállított észszerűek eredmények, amelyek eltérőek lehetnek a tartalom a tartalom – definíció szerint azt is jelenti, hogy egyes tartalmak nem lehet sávszélességű jelentős csökkentése vagy a minőség fokozása.

![Árfolyam-torzulást (RD) görbe PSNR használatával](media/cae-experimental/msrv1.png)

**1. ábra: Árfolyam-torzulást (RD) görbe PSNR mérőszámmal magas összetettséget forrás**

![Árfolyam-torzulást (RD) görbe VMAF használatával](media/cae-experimental/msrv2.png)

**2. ábra: Árfolyam-torzulást (RD) görbe VMAF mérőszámmal magas összetettséget forrás**

A készlet jelenleg van ideálisak a nagy összetettségét, kiváló minőségű forrásvideókat (filmek, tévéműsorok). Folyamatban van nehezebbé válik az alacsony összetettséget tartalom (például PowerPoint-bemutatók), valamint rontja minőségű videókat. Az adaptív Streameléshez készletet a beállításkészlet is ugyanazokat a megoldását használja. A Microsoft dolgozik módszerek kiválasztásához a minimálisan szükséges a tartalom alapján megoldását. A következő olyan eredményei egy másik kategóriát a tartalmat, ahol a kódoló értéke meg tudja határozni, hogy a bemeneti gyenge minőségű (az alacsony sávszélességű miatt számos tömörítési összetevők) volt-e. Vegye figyelembe, hogy a kísérleti a készletet, a kódoló úgy döntött, hogy csak egyetlen kimeneti réteggel – elég alacsony sávszélességű előállításához, így a legtöbb ügyfél tudná a stream lejátszás leállása nélkül.

![A távoli asztali görbe PSNR használatával](media/cae-experimental/msrv3.png)

**3. ábra: A távoli asztali görbét PSNR gyenge minőségű bemeneti (a 1080p)**

![A távoli asztali görbe VMAF használatával](media/cae-experimental/msrv4.png)

**4. ábra: A távoli asztali görbét VMAF gyenge minőségű bemeneti (a 1080p)**

## <a name="use-the-experimental-preset"></a>A kísérleti készlet használata

Átalakítások, amely használja ezt a készletet a következőképpen hozhat létre. Ha oktatóprogram [a](stream-files-tutorial-with-api.md), módon frissítheti a kódot:

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> A "kísérleti" előtag itt szolgál, hogy jelezze, hogy az alapul szolgáló algoritmusok is változhat. Ott is, és módosítások sávszélességű létrák a cél az algoritmus, amely hatékony, és számos különböző bemeneti feltételek alkalmazkodik a beépül generálásához használt logikát az idő függvényében. Kódolási feladat használatával továbbra is előre beállított ezzel számlázott alapján kimeneti perc, és a kimeneti objektum is üzembe helyezhető, a protokollok, mint például a DASH vagy HLS streamelési végpontok.

## <a name="next-steps"></a>További lépések

Most, hogy az új lehetőség a videók olyan megismerkedett Felkérjük, hogy próbálja ki. Küldjön visszajelzést a cikk végén található hivatkozásokkal, vagy küldjön több közvetlenül léphet <amsved@microsoft.com>.
