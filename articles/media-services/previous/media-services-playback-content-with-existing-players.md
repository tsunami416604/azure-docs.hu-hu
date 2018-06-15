---
title: Meglévő játékosok lejátszásához használjon a tartalom - Azure |} Microsoft Docs
description: Ez a témakör felsorolja a meglévő játékosok használható lejátszásához a tartalmat.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: juliako
ms.openlocfilehash: a64d32747371aab2bd927e53c5d43e30de9108f9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788479"
---
# <a name="playing-your-content-with-existing-players"></a>A meglévő játékosokkal tartalom lejátszása
Az Azure Media Services számos népszerű formátumban, például a Smooth Streaming, HTTP Live Streaming és MPEG-Dash támogatja. Ez a témakör mutat meglévő játékosok fogadhassák teszteléséhez használható.

### <a name="the-azure-portal-media-services-content-player"></a>Az Azure portál Media Services content player
A **Azure** portálon talál egy tartalomlejátszót, amelyek segítségével tesztelheti a videót.

Kattintson a kívánt videóra (Győződjön meg arról, hogy volt [közzétett](media-services-portal-publish.md)), és kattintson a **lejátszása** gomb a portál alján.

Vegye figyelembe a következőket:

* A **MEDIA SERVICES CONTENT PLAYER** (Media Services tartalomlejátszó) az alapértelmezett streamvégpontból játssza le a fájlokat. Ha egy nem alapértelmezett streamvégpontból szeretne lejátszani valamit, használjon másik lejátszót. Például [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player
Használjon [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) lejátszásához a tartalom (egyszerű vagy védett) a következő formátumban:

* Smooth Streaming
* MPEG DASH
* HLS
* Fokozatos MP4

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>AES által titkosított jogkivonatok
[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>A Silverlight-lejátszó
#### <a name="monitoring"></a>Figyelés
[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### <a name="playready-with-token"></a>PlayReady-tokenhez
[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>KÖTŐJEL lejátszó
[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>Egyéb
Tesztelése HLS URL-címeket is használhatja:

* **Safari** iOS-eszközön vagy
* **3ivx HLS Player** Windows rendszeren.

## <a name="developing-video-players"></a>Videó játékosok fejlesztése
A saját játékosok fejlesztésével kapcsolatos további információkért lásd: [videó játékosok fejlesztése](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
