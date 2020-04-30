---
title: Azure Media Player áttekintése
description: További információ a Azure Media Playerról.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: b6d30aebd4de272ba98fce87f23701b129eacb02
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726521"
---
# <a name="azure-media-player-overview"></a>Az Azure Media Player áttekintése #

A Azure Media Player egy webvideós lejátszó, amely a [Microsoft Azure Media Services](https://azure.microsoft.com/services/media-services/) különböző böngészők és eszközök által készített médiatartalom lejátszására készült. A Azure Media Player az iparági szabványokat, például a HTML5-t, a Media Source Extensions (MSE) és a titkosított adathordozó-bővítményeket (EME) használja a dúsított Adaptív átviteli élmény biztosításához.  Ha ezek a szabványok nem érhetők el egy eszközön vagy böngészőben, Azure Media Player a Flash és a Silverlight protokollt használja tartalék technológiaként. A használt lejátszási technológiától függetlenül a fejlesztőknek egységes JavaScript-felülettel kell rendelkezniük az API-k eléréséhez.  Ez lehetővé teszi, hogy a Azure Media Services által kiszolgált tartalmak a további erőfeszítések nélkül, széles körű eszközökön és böngészőkön keresztül legyenek játszhatók.

Microsoft Azure Media Services lehetővé teszi, hogy a tartalom a KÖTŐJELtel, a Smooth Streaming és a HLS adatfolyam-formátumokkal legyen kézbesítve a tartalmak lejátszásához. Azure Media Player a különböző formátumokat veszi figyelembe, és a platform/böngésző képességei alapján automatikusan a legjobb hivatkozást játssza le. Microsoft Azure Media Services a közös titkosítással (PlayReady vagy Widevine) vagy AES-128 bites boríték-titkosítással rendelkező eszközök dinamikus titkosítását is lehetővé teszi. A Azure Media Player lehetővé teszi a PlayReady és az AES-128 bites titkosított tartalom visszafejtését, ha az megfelelően van konfigurálva.  Ennek konfigurálásához tekintse meg a [védett tartalom](azure-media-player-protected-content.md) szakaszt.

Új szolgáltatások igényléséhez adjon meg ötleteket vagy visszajelzést, küldje el a [UserVoice a Azure Media Player](https://aka.ms/ampuservoice). Ha rendelkezik és adott problémákkal, kérdésekkel vagy hibákat talál, dobjon minket egy sorba ampinfo@microsoft.com.

[Regisztráljon](https://aka.ms/ampsignup) , hogy soha ne hagyjon ki egy kiadást, és naprakész maradjon a legújabb, Azure Media Player által kínált lehetőségek közül.

> [!NOTE]
> Vegye figyelembe, hogy a Azure Media Player csak a Azure Media Services-adatfolyamokat támogatja.

## <a name="license"></a>Licenc ##

A Azure Media Player licenccel rendelkezik, és a Azure Media Player Microsoft szoftverlicenc-feltételeiben ismertetett feltételekre vonatkozik. Tekintse meg a teljes használati feltételeket [tartalmazó licencfeltételeket](azure-media-player-license.md) . További információért tekintse meg az [adatvédelmi nyilatkozatot](https://www.microsoft.com/en-us/privacystatement/default.aspx) .

Copyright 2015 Microsoft Corporation.

## <a name="next-steps"></a>További lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)