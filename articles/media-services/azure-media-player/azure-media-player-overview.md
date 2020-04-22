---
title: Az Azure Media Player áttekintése
description: További információ az Azure Media Playerről.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: b6d30aebd4de272ba98fce87f23701b129eacb02
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726521"
---
# <a name="azure-media-player-overview"></a>Az Azure Media Player áttekintése #

Az Azure Media Player egy webes videólejátszó, amely a [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) médiatartalmainak lelejátszására készült a legkülönbözőbb böngészőkben és eszközökön. Az Azure Media Player az iparági szabványokat, például a HTML5-öt, a Media Source Extensions (MSE) és a titkosított médiabővítményeket (EME) használja a bővített adaptív streamelési élmény biztosításához.  Ha ezek a szabványok nem érhetők el egy eszközön vagy böngészőben, az Azure Media Player a Flash és a Silverlight-ot használja tartalék technológiaként. Függetlenül attól, hogy a lejátszási technológia használt, a fejlesztők egy egységes JavaScript felület eléréséhez API-k.  Ez lehetővé teszi, hogy az Azure Media Services által kiszolgált tartalmakat az eszközök és böngészők széles körében, további erőfeszítés nélkül lelehessen játszani.

A Microsoft Azure Media Services lehetővé teszi, hogy a tartalom DASH, Smooth Streaming és HLS streamelési formátumokkal kerüljön ki, hogy lejátszhassa a tartalmakat. Az Azure Media Player figyelembe veszi ezeket a különböző formátumokat, és automatikusan lejátssza a legjobb hivatkozást a platform/böngésző képességei alapján. A Microsoft Azure Media Services lehetővé teszi a közös titkosítással (PlayReady vagy Widevine) vagy AES-128 bites borítéktitkosítással rendelkező eszközök dinamikus titkosítását is. Az Azure Media Player lehetővé teszi a PlayReady és az AES-128 bites titkosított tartalom visszafejtését, ha megfelelően konfigurálva van.  Ennek konfigurálása a [Védett tartalom](azure-media-player-protected-content.md) című szakaszban található.

Új funkciók igényléséhez adjon ötleteket vagy visszajelzést, kérjük, küldje el [a UserVoice for Azure Media Player alkalmazásnak.](https://aka.ms/ampuservoice) Ha van, és konkrét kérdések, kérdések vagy talál ampinfo@microsoft.comhibákat, csepp nekünk egy sort .

[Regisztráljon,](https://aka.ms/ampsignup) hogy soha ne maradjon le egy kiadásról, és naprakész maradjon az Azure Media Player által kínált legújabb ajánlatokkal.

> [!NOTE]
> Kérjük, vegye figyelembe, hogy az Azure Media Player csak az Azure Media Services-ből származó médiaadatfolyamokat támogatja.

## <a name="license"></a>Licenc ##

Az Azure Media Player licencelt, és az Azure Media Player microsoftszoftverlicenc-szerződésében meghatározott feltételek nek megfelelően érhető el. A [licencfájl ban](azure-media-player-license.md) a teljes feltételeket lásd. További információt az [Adatvédelmi nyilatkozatban](https://www.microsoft.com/en-us/privacystatement/default.aspx) talál.

Szerzői jog 2015 Microsoft Corporation.

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)