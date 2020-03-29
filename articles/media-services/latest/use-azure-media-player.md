---
title: Lejátszás az Azure Media Playerrel – Azure | Microsoft dokumentumok
description: Az Azure Media Player egy webes videólejátszó, amely a Microsoft Azure Media Services médiatartalmainak lelejátszására készült a legkülönbözőbb böngészőkben és eszközökön.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: juliako
ms.openlocfilehash: d9e42e809443a2dd6cdeb989f692b96d63269f79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673577"
---
# <a name="azure-media-player-overview"></a>Az Azure Media Player áttekintése

Az Azure Media Player egy webes videólejátszó, amely a Microsoft Azure Media Services médiatartalmainak lelejátszására készült a legkülönbözőbb böngészőkben és eszközökön. Az Azure Media Player az iparági szabványokat, például a HTML5-öt, a Media Source Extensions (MSE) és a titkosított médiabővítményeket (EME) használja a bővített adaptív streamelési élmény biztosításához. Ha ezek a szabványok nem érhetők el egy eszközön vagy böngészőben, az Azure Media Player a Flash és a Silverlight-ot használja tartalék technológiaként. Függetlenül attól, hogy a lejátszási technológia használt, a fejlesztők egy egységes JavaScript felület eléréséhez API-k. Ez lehetővé teszi, hogy az Azure Media Services által kiszolgált tartalmakat az eszközök és böngészők széles körében, további erőfeszítés nélkül lelehessen játszani.

A Microsoft Azure Media Services lehetővé teszi, hogy a tartalom HLS, DASH és Smooth Streaming streamelési formátumokkal kerüljön kiszolgálásra a tartalom lejátszásához. Az Azure Media Player figyelembe veszi ezeket a különböző formátumokat, és automatikusan lejátssza a legjobb hivatkozást a platform/böngésző képességei alapján. A Media Services lehetővé teszi a PlayReady titkosítással vagy Az AES-128 bites borítéktitkosítással rendelkező eszközök dinamikus titkosítását is. Az Azure Media Player lehetővé teszi a PlayReady és az AES-128 bites titkosított tartalom visszafejtését, ha megfelelően konfigurálva van. 

> [!NOTE]
> A Widevine titkosított tartalomhoz HTTPS-lejátszás szükséges.

## <a name="use-azure-media-player-demo-page"></a>Az Azure Media Player bemutatóoldalának használata

### <a name="start-using"></a>Használat megkezdése

Az Azure [Media Player bemutatólapjával](https://aka.ms/azuremediaplayer) lejátszhatja az Azure Media Services-mintákat vagy a saját adatfolyamát.  

Új videó lejátszásához illesszen be egy másik URL-t, és nyomja le a **Frissítés**gombot.

A különböző lejátszási beállítások (például technikai, nyelvi vagy titkosítási) beállításához nyomja le a **Speciális beállítások gombot.**

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>Videoadatfolyam diagnosztikájának figyelése

Az Azure [Media Player bemutatólapjával](https://aka.ms/azuremediaplayer) figyelheti a videostream diagnosztikája. 

![Az Azure Media Player diagnosztikája](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>Az Azure Media Player beállítása a HTML-ben

Az Azure Media Player könnyen beállítható. Csak néhány percet vesz igénybe, hogy a Media Services-fiókból lejátssza a médiatartalmakat. Az Azure Media Player beállításával és konfigurálásával kapcsolatos részletekért tekintse meg az [Azure Media Player dokumentációját.](https://aka.ms/ampdocs) 

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="next-steps"></a>További lépések

- [Az Azure Media Player dokumentációja](https://aka.ms/ampdocs)
- [Az Azure Media Player mintái](https://aka.ms/ampsamples)
