---
title: Helyszíni kódolók konfigurálása a többszörös sávszélességű adatfolyamok létrehozásához Azure Media Services használatakor | Microsoft Docs
description: Ez a témakör felsorolja azokat a helyszíni élő kódolókat, amelyek segítségével rögzítheti az élő eseményeket, és egyetlen sávszélességű élő streamet küldhet az AMS-csatornákra (amelyek az élő kódolás engedélyezve van) további feldolgozás céljából. A témakör a felsorolt kódolók konfigurálását bemutató oktatóanyagokra mutató hivatkozásokat tartalmaz.
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
ms.openlocfilehash: 71a31228602ef161158eaa05c80d50f65de98a50
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133271"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Helyszíni kódolók konfigurálása a többszörös átviteli sebességű streamek létrehozásához Azure Media Services használatakor
Ez a témakör felsorolja azokat a helyszíni élő kódolókat, amelyek segítségével rögzítheti az élő eseményeket, és egyetlen sávszélességű élő streamet küldhet az AMS-csatornákra (amelyek az élő kódolás engedélyezve van) további feldolgozás céljából. A témakör emellett olyan oktatóanyagokra is hivatkozik, amelyek bemutatják a felsorolt kódolók konfigurálásának módját.

> [!NOTE]
> Amikor RTMP-vel streamel, ellenőrizze a tűzfal és/vagy a proxy beállításaiban, hogy az 1935-ös és az 1936-os kimenő TCP-portok nyitva vannak-e.

## <a name="haivision-kb-encoder"></a>Haivision KB-kódoló
További információ arról, hogyan konfigurálhatja a [HAIVISION kb kódoló](https://www.haivision.com/products/kb-series/) kódolót egyetlen sávszélességű élő adatfolyam küldésére egy AMS-csatornába: [Haivision kb-kódoló konfigurálása](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Wirecast
További információ arról, hogyan konfigurálhatja a [Wirecast](https://www.telestream.net/wirecast/overview.htm) -kódolót úgy, hogy egyetlen sávszélességű élő streamet küldjön egy AMS-csatornába: a [Wirecast konfigurálása](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NewTek TriCaster
További információ arról, hogyan konfigurálhatja a [Tricaster](https://newtek.com/products/tricaster-40.html) -kódolót úgy, hogy egyetlen sávszélességű élő streamet küldjön egy AMS-csatornába: [Tricaster konfigurálása](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
További információ: [Elemental Live](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések

[Élő közvetítés a Azure Media Services használatával többszörös átviteli sebességű streamek létrehozásához](media-services-manage-live-encoder-enabled-channels.md).

