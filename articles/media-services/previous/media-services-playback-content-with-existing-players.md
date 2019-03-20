---
title: Használja meglévő lejátszók lejátszás, a tartalom – Azure |} A Microsoft Docs
description: Ez a témakör felsorolja a meglévő lejátszók, hogy segítségével a lejátszás tartalmait.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 2083390c3d228e57151db8b165b876054d6c12cd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57876561"
---
# <a name="playing-your-content-with-existing-players"></a>A meglévő lejátszókkal tartalom lejátszása
Az Azure Media Services számos népszerű formátumban, például a Smooth Streaming, HTTP Live Streaming és MPEG-Dash támogatja. Ez a témakör az adatfolyamok teszteléséhez használhatja meglévő lejátszók mutat.

### <a name="the-azure-portal-media-services-content-player"></a>Az Azure portál Media Services content player
A **Azure** portálon talál egy tartalomlejátszót, amellyel tesztelheti a videót.

Kattintson arra a kívánt videóra (Ellenőrizze, hogy volt [közzétett](media-services-portal-publish.md)), és kattintson a **lejátszása** gombra a portál alján.

Vegye figyelembe a következőket:

* A **MEDIA SERVICES CONTENT PLAYER** (Media Services tartalomlejátszó) az alapértelmezett streamvégpontból játssza le a fájlokat. Ha egy nem alapértelmezett streamvégpontból szeretne lejátszani valamit, használjon másik lejátszót. Ha például [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player
Használat [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html) történő a tartalom (törölje vagy védett), a következő formátumok egyikében:

* Smooth Streaming
* MPEG DASH
* HLS
* A progresszív MP4

### <a name="flash-player"></a>Flash-lejátszó
#### <a name="aes-encrypted-with-token"></a>AES titkosítású jogkivonat
[https://aestoken.azurewebsites.net](https://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>A Silverlight-lejátszók

#### <a name="playready-with-token"></a>PlayReady-jogkivonat
[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH-lejátszók
[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Egyéb
HLS-URL-címeket is tesztelheti:

* **A Safari** iOS-eszközön vagy
* **HLS-lejátszó 3ivx** a Windows.

## <a name="developing-video-players"></a>Fejlesztés videolejátszók
A saját lejátszók fejlesztésével kapcsolatban további információkért lásd: [videolejátszók fejlesztése](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
