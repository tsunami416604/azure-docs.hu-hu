---
title: Helyszíni kódolók konfigurálása az Azure Media Services használatával és többféle sávszélességű adatfolyamok létrehozása közben |} A Microsoft Docs
description: Ez a témakör felsorolja a helyszíni élő kódolók képesek segítségével az élő események rögzítése és a egy egyféle sávszélességű élő adatfolyamot AMS csatornákra (élő kódolás engedélyezve) küldése további feldolgozás céljából. A témakör hivatkozások, amelyek bemutatják a felsorolt kódolók konfigurálása az oktatóanyagokat.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: juliako
ms.openlocfilehash: c0c7e2b8962cb757141b654c2956ed3ff5600c88
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53787647"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Helyszíni kódolók konfigurálása, többszörös átviteli sebességű Streamek létrehozása az Azure Media Services használata esetén
Ez a témakör felsorolja a helyszíni élő kódolók képesek segítségével az élő események rögzítése és a egy egyféle sávszélességű élő adatfolyamot AMS csatornákra (élő kódolás engedélyezve) küldése további feldolgozás céljából. A témakör hivatkozásokat is tartalmaz, amelyek bemutatják a felsorolt kódolók konfigurálása az oktatóanyagokat.

> [!NOTE]
> Streamelési RTMP-n keresztül, amikor ellenőrizze a tűzfallal és/vagy a proxy beállításait, győződjön meg arról, hogy 1935 és 1936 kimenő TCP-portok nyitva-e.

## <a name="flash-media-live-encoder"></a>Flash Media Live kódoló
Információk konfigurálása a [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) encoder (FMLE) a egyféle sávszélességű élő adatfolyamot küldeni egy AMS-csatornát lásd [konfigurálása FMLE](media-services-configure-fmle-live-encoder.md).

## <a name="haivision-kb-encoder"></a>Haivision KB kódoló
Információk konfigurálása a [Haivision KB kódoló](https://www.haivision.com/products/kb-series/) kódoló egyféle sávszélességű élő adatfolyamot küldeni egy AMS-csatornát, lásd: [Haivision KB kódoló konfigurálása](media-services-configure-kb-live-encoder.md).

## <a name="telestream-wirecast"></a>Telestream Wirecast
Információk konfigurálása a [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) kódoló egyféle sávszélességű élő adatfolyamot küldeni egy AMS-csatornát, lásd: [konfigurálása Wirecast](media-services-configure-wirecast-live-encoder.md).

## <a name="newtek-tricaster"></a>NewTek TriCaster
Információk konfigurálása a [Tricaster](http://newtek.com/products/tricaster-40.html) kódoló egyféle sávszélességű élő adatfolyamot küldeni egy AMS-csatornát, lásd: [konfigurálása Tricaster](media-services-configure-tricaster-live-encoder.md).

## <a name="elemental-live"></a>Elemental Live
További információkért lásd: [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések

[Élő adatfolyam továbbítása az Azure Media Services használatával és többféle sávszélességű adatfolyamok létrehozása a](media-services-manage-live-encoder-enabled-channels.md).

