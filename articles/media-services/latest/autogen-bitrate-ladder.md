---
title: Videók kódolása szabványos kódolóval a Media Services szolgáltatásban - Azure | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan használhatja a Media Services szabványos kódolóját egy bemeneti videó automatikusan generált bitráta létrával történő kódolásához a bemeneti felbontás és a bitráta alapján.
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
ms.date: 02/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c25c32f35adc1c017f0f4c012c82bd7e0af8d452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60733317"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Kódolható automatikusan generált bitráta létrával

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan lehet a Media Services szabványos kódolójával kódolni egy bemeneti videót egy automatikusan generált bitráta létrába (bitráta-felbontású párokba) a bemeneti felbontás és a bitráta alapján. Ez a beépített kódoló beállítás vagy készlet soha nem fogja meghaladni a bemeneti felbontást és a bitrátát. Ha például a bemenet 720p 3 Mbps sebességgel, a kimenet a legjobb esetben is 720p marad, és 3 Mbps-nál alacsonyabb sebességgel indul.

### <a name="encoding-for-streaming"></a>Kódolás streameléshez

Ha az **AdaptiveStreaming** készletet használja az **Átalakítás**ban, olyan kimenetet kap, amely alkalmas a streamelési protokollokon, például a HLS-en és a DASH-en keresztül történő kézbesítésre. A készlet használatakor a szolgáltatás intelligensen határozza meg, hogy hány videoréteget kell létrehozni, és milyen bitrátával és felbontással. A kimeneti tartalom MP4 fájlokat tartalmaz, ahol Az AAC kódolású hang és H.264 kódolású videó nincs interleaved.

A készlet használatának példáját a [Fájl streamelése](stream-files-dotnet-quickstart.md)című témakörben tetszésben található.

## <a name="output"></a>Kimenet

Ez a szakasz három példát mutat be a Media Services kódoló által az **AdaptiveStreaming-készlettel** való kódolás eredményeként létrehozott kimeneti videorétegekre. A kimenet minden esetben csak hanggal rendelkező MP4 fájlt tartalmaz, 128 kbps-os sztereó hanggal.

### <a name="example-1"></a>1. példa
Forrás magassága "1080" és framerate "29.970" termel 6 videó rétegek:

|Réteg|Height (Magasság)|Szélesség|Bitráta (kbit/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>2. példa
Forrás magassága "720" és framerate "23.970" termel 5 videó rétegek:

|Réteg|Height (Magasság)|Szélesség|Bitráta (kbit/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>3. példa
Forrás magassága "360" és framerate "29.970" termel 3 videó rétegek:

|Réteg|Height (Magasság)|Szélesség|Bitráta (kbit/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fájl streamelése](stream-files-dotnet-quickstart.md)
