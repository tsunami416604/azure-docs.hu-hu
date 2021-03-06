---
title: Videók kódolása szabványos kódolóval a Media Services-Azure-ban | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használható a standard kódoló a Media Servicesban egy automatikusan generált bitráta-létrával rendelkező bemeneti videó kódolásához a bemeneti felbontás és a bitráta alapján.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 05accd69f1868b8b0e0f6dbd4fb5c21ee843ec5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297718"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Kódolás automatikusan generált bitráta-létrával

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Áttekintés

Ez a cikk azt ismerteti, hogyan használható a standard kódoló a Media Servicesban egy bemeneti videó egy automatikusan generált bitráta-ladderbe (bitráta-feloldási párok) való kódolására a bemeneti felbontás és a bitráta alapján. Ez a beépített kódoló-beállítás vagy az előre definiált érték soha nem lépi túl a bemeneti felbontást és a bitrátát. Ha például a bemenet 720p 3 Mbps-nél, a kimenet 720p marad a legjobb esetben, és 3 Mbps-nál kisebb arányban fog kezdődni.

### <a name="encoding-for-streaming"></a>Kódolás a folyamatos átvitelhez

Ha az **átalakítás**során a **AdaptiveStreaming** -készletet használja, olyan kimenetet kap, amely alkalmas továbbítási protokollok, például a HLS és a Dash protokollon keresztül történő kézbesítésre. Ha ezt az beállításkészletet használja, a szolgáltatás intelligensen határozza meg, hogy hány videó-réteget kell előállítani, és milyen bitrátát és megoldást kell használni. A kimeneti tartalom MP4-fájlokat tartalmaz, amelyekben az AAC-kódolású hang-és a H. 264 kódolású videó nincs átfedésben.

Ha szeretné megtekinteni a készlet használatának módját, tekintse meg [a fájl továbbítása](stream-files-dotnet-quickstart.md)című témakört.

## <a name="output"></a>Kimenet

Ez a szakasz három példát mutat be a Media Services kódoló által előállított kimeneti videó rétegekre, a **AdaptiveStreaming** -készlettel való kódolás eredményeképpen. A kimenet minden esetben tartalmaz egy csak hangot tartalmazó MP4-fájlt, amely 128 kbps-nál kódolt sztereó hanggal rendelkezik.

### <a name="example-1"></a>1\. példa
A "1080" magasságú és "29,970" framerátát tartalmazó forrás 6 videó réteget hoz létre:

|Réteg|Magasság|Szélesség|Bitráta (Kbit/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>2\. példa
A "720" magasságú és "23,970" framerátát tartalmazó forrás 5 videó réteget állít elő:

|Réteg|Magasság|Szélesség|Bitráta (Kbit/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>3\. példa
A "360" magasságú és "29,970" framerátát tartalmazó forrás 3 videó réteget hoz létre:

|Réteg|Magasság|Szélesség|Bitráta (Kbit/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Fájl streamelése](stream-files-dotnet-quickstart.md)
