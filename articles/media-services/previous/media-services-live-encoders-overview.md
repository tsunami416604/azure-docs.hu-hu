---
title: Helyszíni kódolók konfigurálása az Azure Media Services használatával többbitrátású adatfolyamok létrehozásához | Microsoft dokumentumok
description: Ez a témakör azokat a helyszíni élő kódolókat sorolja fel, amelyek segítségével rögzítheti az élő eseményeket, és egyetlen átviteli sebességű élő közvetítést küldhet az AMS-csatornáknak (amelyek élő kódolásengedélyezve vannak) további feldolgozáscéljából. A témakör olyan oktatóanyagokra mutat, amelyek bemutatják a felsorolt kódolók konfigurálását.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77133271"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>A helyszíni kódolók konfigurálása az Azure Media Services használatával többbitrátású adatfolyamok létrehozásakor
Ez a témakör azokat a helyszíni élő kódolókat sorolja fel, amelyek segítségével rögzítheti az élő eseményeket, és egyetlen átviteli sebességű élő közvetítést küldhet az AMS-csatornáknak (amelyek élő kódolásengedélyezve vannak) további feldolgozáscéljából. A témakör olyan oktatóanyagokra mutató hivatkozásokra is hivatkozik, amelyek bemutatják a felsorolt kódolók konfigurálását.

> [!NOTE]
> Amikor RTMP-vel streamel, ellenőrizze a tűzfal és/vagy a proxy beállításaiban, hogy az 1935-ös és az 1936-os kimenő TCP-portok nyitva vannak-e.

## <a name="haivision-kb-encoder"></a>Haivision KB kódoló
A [Haivision KB kódoló](https://www.haivision.com/products/kb-series/) kódolójának egyetlen átviteli sebesség ű átviteli adatszolgáltatása AMS-csatornára történő küldéséről a [Haivision KB kódoló konfigurálása](media-services-configure-kb-live-encoder.md)című témakörben talál további információt.

## <a name="telestream-wirecast"></a>Telestream Wirecast
Arról, hogy miként konfigurálható úgy a [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) kódoló, hogy egyetlen bitráta-átviteli adatfolyamot küldjön egy AMS-csatornára, a [Wirecast konfigurálása című](media-services-configure-wirecast-live-encoder.md)témakörben talál további információt.

## <a name="newtek-tricaster"></a>NewTek TriCaster
ARról, hogy miként konfigurálható úgy a [Tricaster](https://newtek.com/products/tricaster-40.html) kódoló, hogy egyetlen bitráta-élő közvetítést küldjön egy AMS-csatornára, a [Tricaster konfigurálása](media-services-configure-tricaster-live-encoder.md)című témakörben talál további információt.

## <a name="elemental-live"></a>Elemental Live
További információ: [Elemental Live](https://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések

[Élő közvetítés az Azure Media Services használatával többbites adatfolyamok létrehozásához.](media-services-manage-live-encoder-enabled-channels.md)

