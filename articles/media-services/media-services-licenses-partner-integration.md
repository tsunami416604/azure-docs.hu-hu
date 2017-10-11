---
title: "Widevine-licencek kézbesíthet Azure Media Services használatával a partnerek |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan használható az Azure Media Services (AMS) által a PlayReady vagy a Widevine DRMs AMS dinamikusan titkosított adatfolyam továbbítására. A PlayReady-licenc Media Services PlayReady licenckiszolgáló származik, és Widevine-licenc castLabs licenckiszolgáló hozta."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 5bcad5a4-c0bb-4871-9cce-808a913c53e6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 6867e4f910970121df3858516c6bab3114c3c6f9
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>Partnerek használata a Widevine licencek kézbesítéséhez az Azure Media Services szolgáltatásba
## <a name="overview"></a>Áttekintés
A Microsoft Azure Media Services lehetővé teszi, hogy MPEG-DASH védelme a Widevine DRM-Védelemmel, amely titkosítása a Common Encryption (CENC) megadását.

A Media Services .NET SDK verzió 3.5.2 verziótól kezdődően a Media Services lehetővé teszi a Widevine-licencsablon konfigurálása és Widevine-licencek. A Widevine-licencek továbbításának támogatásához a következő AMS-partnereket is használhatja: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) vagy [castLabs](http://castlabs.com/company/partners/azure/).

## <a name="castlabs"></a>castLabs
Használhat [castLabs](http://castlabs.com/company/partners/azure/) a Widevine-licencek. További információkért lásd: [Azure Media Services használatával castLabs képes biztosítani a DRM licencek](media-services-castlabs-integration.md)

## <a name="axinom"></a>Axinom
Használhat [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) a Widevine-licencek. További információkért lásd: [Axinom használatával képes biztosítani a DRM licencek Azure Media Services szolgáltatáshoz](media-services-axinom-integration.md)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[A PlayReady és/vagy Widevine Dynamic Common Encryption titkosítás használata](media-services-protect-with-drm.md)

[Mingfei's blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

