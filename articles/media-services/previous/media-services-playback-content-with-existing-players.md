---
title: Meglévő játékosok használata a tartalom lejátszásához – Azure | Microsoft Docs
description: Ez a cikk felsorolja azokat a meglévő játékosokat, amelyeket a tartalom lejátszásához használhat.
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
ms.openlocfilehash: fdcaa7d8eec0065ad9acd76df9918ed7bccc081b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895858"
---
# <a name="playing-your-content-with-existing-players"></a>Tartalom lejátszása meglévő lejátszókkal
A Azure Media Services számos népszerű streaming formátumot támogat, például a Smooth Streaming, a HTTP Live Streaming és az MPEG-Dash formátumot. Ez a témakör egy meglévő, a streamek tesztelésére használható játékost mutat be.

### <a name="the-azure-portal-media-services-content-player"></a>A tartalom lejátszó Azure Portal Media Services
Az **Azure** Portal egy tartalommal rendelkező lejátszót biztosít, amellyel tesztelheti a videót.

Kattintson a kívánt videóra (Győződjön meg róla, hogy [közzé lett téve](media-services-portal-publish.md)), majd kattintson a portál alján található **Lejátszás** gombra.

Vegye figyelembe a következőket:

* A **MEDIA SERVICES CONTENT PLAYER** (Media Services tartalomlejátszó) az alapértelmezett streamvégpontból játssza le a fájlokat. Ha egy nem alapértelmezett streamvégpontból szeretne lejátszani valamit, használjon másik lejátszót. Például [Azure Media Player](https://aka.ms/azuremediaplayer).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

A következő formátumok bármelyikében használhatja a [Azure Media Player](https://aka.ms/azuremediaplayer) a tartalom (tiszta vagy védett) lejátszásához:

* Smooth Streaming
* MPEG DASH
* HLS
* Progresszív MP4

### <a name="flash-player"></a>Flash-lejátszó

#### <a name="aes-encrypted-with-token"></a>AES-titkosítású Tokenrel

[https://aestoken.azurewebsites.net](https://aestoken.azurewebsites.net)

#### <a name="playready-with-token"></a>PlayReady Tokenrel

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH-lejátszók

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Egyéb
A HLS URL-címek teszteléséhez használhatja a következőket is:

* **Safari** iOS-eszközön vagy
* **3IVX HLS-lejátszó** Windows rendszeren.

## <a name="developing-video-players"></a>Videós lejátszók fejlesztése
További információ a saját játékosok fejlesztéséről: [videolejátszó fejlesztése](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
