---
title: Meglévő játékosok használata a tartalom lejátszásához - Azure | Microsoft dokumentumok
description: Ez a cikk azokat a meglévő játékosokat sorolja fel, amelyek segítségével lejátszhatja a tartalmat.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 07537d3d67e41f7e1179a709ffa19f3d84aa4539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565825"
---
# <a name="playing-your-content-with-existing-players"></a>A tartalom lejátszása meglévő lejátszókkal
Az Azure Media Services számos népszerű streamelési formátumot támogat, például a sima streamelést, a HTTP Live Streaming és az MPEG-Dash formátumot. Ez a témakör a meglévő játékosokra mutat, akik segítségével tesztelheted az adatfolyamokat.

### <a name="the-azure-portal-media-services-content-player"></a>Az Azure Portal Media Services tartalomlejátszója
Az **Azure** Portal egy tartalomlejátszót biztosít, amely segítségével tesztelheti a videót.

Kattintson a kívánt videóra (győződjön meg róla, hogy [közzétették),](media-services-portal-publish.md)és kattintson a **lejátszás** gombra a portál alján.

Vegye figyelembe a következőket:

* A **MEDIA SERVICES CONTENT PLAYER** (Media Services tartalomlejátszó) az alapértelmezett streamvégpontból játssza le a fájlokat. Ha egy nem alapértelmezett streamvégpontból szeretne lejátszani valamit, használjon másik lejátszót. Például az [Azure Media Player](https://aka.ms/azuremediaplayer).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

Az [Azure Media Player](https://aka.ms/azuremediaplayer) segítségével lejátszhatja a tartalmat (tiszta vagy védett) az alábbi formátumok bármelyikében:

* Smooth Streaming
* MPEG DASH
* HLS
* Progresszív MP4

### <a name="flash-player"></a>Flash lejátszó

#### <a name="aes-encrypted-with-token"></a>AES-titkosított token

[https://aestoken.azurewebsites.net](https://aestoken.azurewebsites.net)

#### <a name="playready-with-token"></a>PlayReady és token

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH lejátszók

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Egyéb
A HLS URL-ek teszteléséhez a következőket is használhatja:

* **Safari** iOS-eszközön vagy
* **3ivx HLS Player** Windows rendszeren.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
