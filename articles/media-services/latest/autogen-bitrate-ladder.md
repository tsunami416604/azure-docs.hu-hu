---
title: Kódolás a Media Services – az Azure standard szintű Encoder videók |} A Microsoft Docs
description: Ez a témakör bemutatja a Media Services a standard szintű Encoder használatával kódolás a bemeneti videó és a egy automatikusan létrehozott skála, a bementi felbontás és átviteli sebesség alapján.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 976e1ea013c6a30615bdc742ed79e76ec35258a1
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132806"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Kódolás a egy automatikusan létrehozott skála

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan használhatja a standard szintű Encoder a Media Services egy bemeneti videó kódolandó egy automatikusan létrehozott skála (sávszélességű felbontású párok) a bementi felbontás és átviteli sebesség alapján. Ez a beállítás beépített kódoló vagy a készlet nem haladhatja meg a bementi felbontás és átviteli sebesség. Például ha a bemeneti 3 Mbps sebességnél 720p, kimeneti 720p legjobb marad, és elkezdi alacsonyabb, mint 3 MB/s díjakat.

### <a name="encoding-for-streaming"></a>Folyamatos átviteli kódolás

Használatakor a **AdaptiveStreaming** az előre beállított **átalakítása**, kap egy kimenetet, amely lehetővé teszi a streamelési protokollok, például a HLS és DASH-n keresztül történő továbbítását. Ez a beállítás használatakor a szolgáltatás intelligensen meghatározza, hogy hány videó rétegek létrehozásához, és milyen átviteli sebesség és a megoldás. A kimeneti tartalom MP4-fájlokat, ahol az AAC-kódolású audio- és videotartalmak H.264 kódolású nem időosztásos tartalmazza.

Egy példa a beállításkészlet módjáról, olvassa el [fájl Stream](stream-files-dotnet-quickstart.md).

## <a name="output"></a>Kimenet

Ez a szakasz bemutatja a Media Services encoder eredményeként kódolás által előállított kimeneti videót rétegek három példák a **AdaptiveStreaming** beállításkészletet. Minden esetben a kimeneti sztereó hang kódolása, 128 kb/s a csak hangfájlt tartalmazó MP4 fájlt tartalmaz.

### <a name="example-1"></a>1. példa
Forrás magasság "1080" és "29.970" képkockasebesség 6 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Átviteli sebesség (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>2. példa
Forrás magasság "720" és "23.970" képkockasebesség 5 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Átviteli sebesség (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>3. példa
Forrás magasság "360" és "29.970" képkockasebesség 3 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Átviteli sebesség (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fájl streamelése](stream-files-dotnet-quickstart.md)
