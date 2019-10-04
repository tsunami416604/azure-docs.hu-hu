---
title: Helyszíni kódolók konfigurálása az Azure Media Services használatával és többféle sávszélességű adatfolyamok létrehozása közben |} A Microsoft Docs
description: Ez a témakör felsorolja a helyszíni élő kódolók képesek segítségével az élő események rögzítése és a egy egyféle sávszélességű élő adatfolyamot AMS csatornákra (élő kódolás engedélyezve) küldése további feldolgozás céljából. A témakör hivatkozások, amelyek bemutatják a felsorolt kódolók konfigurálása az oktatóanyagokat.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 8bd0014518dc93abb952114a1728f8fc1d3fb4a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708074"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Helyszíni kódolók konfigurálása, többszörös átviteli sebességű Streamek létrehozása az Azure Media Services használata esetén
Ez a témakör felsorolja a helyszíni élő kódolók képesek segítségével az élő események rögzítése és a egy egyféle sávszélességű élő adatfolyamot AMS csatornákra (élő kódolás engedélyezve) küldése további feldolgozás céljából. A témakör hivatkozásokat is tartalmaz, amelyek bemutatják a felsorolt kódolók konfigurálása az oktatóanyagokat.

> [!NOTE]
> Amikor RTMP-vel streamel, ellenőrizze a tűzfal és/vagy a proxy beállításaiban, hogy az 1935-ös és az 1936-os kimenő TCP-portok nyitva vannak-e.

## <a name="flash-media-live-encoder"></a>Flash Media Live kódoló
Információk konfigurálása a [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) encoder (FMLE) a egyféle sávszélességű élő adatfolyamot küldeni egy AMS-csatornát lásd [konfigurálása FMLE](media-services-configure-fmle-live-encoder.md).

## <a name="haivision-kb-encoder"></a>Haivision KB kódoló
Információk konfigurálása a [Haivision KB kódoló](https://www.haivision.com/products/kb-series/) kódoló egyféle sávszélességű élő adatfolyamot küldeni egy AMS-csatornát, lásd: [Haivision KB kódoló konfigurálása](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Információk konfigurálása a [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) kódoló egyféle sávszélességű élő adatfolyamot küldeni egy AMS-csatornát, lásd: [konfigurálása Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NewTek TriCaster
Információk konfigurálása a [Tricaster](https://newtek.com/products/tricaster-40.html) kódoló egyféle sávszélességű élő adatfolyamot küldeni egy AMS-csatornát, lásd: [konfigurálása Tricaster](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
További információkért lásd: [Elemental Live](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések

[Élő adatfolyam továbbítása az Azure Media Services használatával és többféle sávszélességű adatfolyamok létrehozása a](media-services-manage-live-encoder-enabled-channels.md).

