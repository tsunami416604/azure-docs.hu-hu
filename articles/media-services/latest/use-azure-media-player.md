---
title: Lejátszása az Azure Media Player – Azure |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt az Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/03/2018
ms.author: juliako
ms.openlocfilehash: 4677fc9a56db3190073b3f310a51632055510bc2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028553"
---
# <a name="play-back-with-azure-media-player"></a>Az Azure Media Player lejátszása

[Az Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) webes videolejátszót médiatartalmak lejátszására a Microsoft Azure Media Services számos különböző böngészők és eszközök is. Az Azure Media Player iparági szabványok – például HTML5, Media forrás Extensions (MSE) és az Encrypted Media Extensions (EME) adjon meg egy streamelési megoldást használja. Ha ezek a szabványok nem érhetők el, az eszközön vagy böngészőben, az Azure Media Player használja Flashre és Silverlightra tartalék technológia. A fejlesztők a lejátszási technológiától függetlenül egy egységes JavaScript-felület API-k elérése kell. Ez lehetővé teszi az Azure Media Services által kiszolgált tartalom wide-számos eszköz és bármely extra erőfeszítést böngészőkben lejátszhatók legyenek.

A Microsoft Azure Media Services lehetővé teszi, hogy a tartalom kiszolgálása HLS, DASH, Smooth Streaming formátumban vissza tartalom lejátszása. Az Azure Media Player a különféle formátumokat figyelembe veszi, és automatikusan játszik a legjobb hivatkozás, a platformjától és böngészői képességeitől alapján. A Media Services emellett lehetővé teszi, hogy a dinamikus titkosítást, PlayReady-titkosítást az eszközök, vagy a 128 bites AES-boríték-titkosítást. Az Azure Media Player lehetővé teszi a PlayReady-titkosítás visszafejtésére és 128 bites AES-titkosított tartalmat, ha megfelelően konfigurálva. 

[Az ingyenes próbaverzió használatának megkezdéséhez](http://azure.microsoft.com/en-us/pricing/free-trial/)

## <a name="monitor-diagnostics-of-a-video-stream"></a>A video-adatfolyamot diagnosztika figyelése

Használhatja a [Azure Media Player demó lap](http://aka.ms/amp) , video-adatfolyamot diagnosztika figyelése. 

! [Az azure Media Player diagnostics] [amp_diagnostics]

## <a name="next-steps"></a>További lépések

- [Maradjon naprakész a legújabb, az Azure Media Player regisztráljon](http://azuremediaplayerdemo.azurewebsites.net/amp_signup.html)
- [Az Azure Media Player dokumentációja](https://aka.ms/ampdocs)
- [Azure Media Player-minták](https://aka.ms/ampsamples)
