---
title: A Standard Encoder használja az Azure Media Services használatával egy automatikusan létrehozott sávszélességű létra videók kódolásához |} Microsoft Docs
description: Ez a témakör bemutatja a adás használata a Media Services bemeneti kódolni egy automatikusan létrehozott sávszélességű létra, a bemeneti feloldási és átviteli sebesség alapján a videó. A bemeneti megoldás és sávszélességű soha nem túllépése. Például ha a bemeneti 720p, 3 MB/s, kimeneti lesz 720p legjobb maradnak, és alacsonyabb, mint 3 MB/s sebesség időpontban fog elindulni.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 6e447c04f4a94f2fb534ecb0605595a90816431e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638296"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Az egy automatikusan létrehozott sávszélességű létra kódolása

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti, hogyan a adás a Media Services egy bemeneti videó egy automatikusan létrehozott sávszélességű létra (sávszélességű felbontású párok) a bemeneti feloldási és átviteli sebesség alapján történő kódolásához. Ez a beállítás beépített kódoló vagy előre definiált, nem haladhatja meg a bemeneti megoldás és átviteli sebesség. Például ha a bemeneti 720p, 3 MB/s, kimeneti 720p legjobb marad, és alacsonyabb, mint 3 MB/s sebesség időpontban fog elindulni.

### <a name="encoding-for-streaming"></a>Az adatfolyam-kódolás

Használatakor a **AdaptiveStreaming** az előre definiált **átalakítási**, megfelelő kézbesítési keresztül protokollokkal HLS és kötőjel streaming kimenetnek kap. Ha ezt a beállítást, a szolgáltatás intelligens módon meghatározza, hogy hány videó rétegek létrehozásához, és milyen átviteli sebesség és a megoldás. A kimeneti ahol AAC kódolású audió és videó H.264 kódolású nem időosztásos MP4-fájlokat tartalmazza.

Például hogyan használja fel a készletet, olvassa el [adatfolyam-fájl](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Kimenet

Ez a szakasz bemutatja a Media Services kódoló miatt kódolás kimenete videó rétegek három példái a **AdaptiveStreaming** előre. Minden esetben a kimeneti 128 kb/s, kódolású sztereó hanggal csak MP4-fájlokat tartalmazza.

### <a name="example-1"></a>1. példa
A magasság "1080" és "29.970" képkockasebességhez forrás 6 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Átviteli sebesség (KB/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>2. példa
A magasság "720" és "23.970" képkockasebességhez forrás 5 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Átviteli sebesség (KB/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>3. példa
A magasság "360" és "29.970" képkockasebességhez forrás 3 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Átviteli sebesség (KB/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fájl streamelése](stream-files-dotnet-quickstart.md)
