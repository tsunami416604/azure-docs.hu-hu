---
title: További információ az Azure Media Services által ajánlott kódolókról | Microsoft dokumentumok
description: Ez a cikk az Azure Media Services által ajánlott helyszíni kódolókat sorolja fel.
services: media-services
keywords: kódolás;kódolók;adathordozó
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 89b01a3fb066f181f5ec54b481b71feaa7a6ae08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131401"
---
# <a name="recommended-on-premises-encoders"></a>Ajánlott helyszíni kódolók

Amikor élő közvetítés tszeretne az Azure Media Services szolgáltatással, megadhatja, hogy a csatorna hogyan fogadja a bemeneti adatfolyamot. Ha úgy dönt, hogy egy helyszíni kódoló egy élő kódolási csatornával, a kódoló kell leküldéses egy kiváló minőségű egy-bitráta adatfolyam kimenetként. Ha úgy dönt, hogy egy helyszíni kódoló egy áthaladási csatornán keresztül, a kódoló kell leküldéses egy többátviteli adatfolyam kimenetként az összes kívánt kimeneti minőségekkel. További információ: [Élő közvetítés helyszíni kódolókkal.](media-services-live-streaming-with-onprem-encoders.md)

## <a name="encoder-requirements"></a>Kódoló követelmények

A kódolóknak HTTPS vagy RTMPS protokollok használata esetén támogatniuk kell a TLS 1.2-t.

## <a name="live-encoders-that-output-rtmp"></a>RTMP-t kibocsátó élő kódolók 

Az Azure Media Services a következő élő kódolók egyikének használatát javasolja, amelyek kimenetként RTMP-t használnak:

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast (13.0.2-es vagy újabb verzió a TLS 1.2 követelmény miatt)

  A kódolóknak támogatniuk kell a TLS 1.2 protokollt RTMPS protokollok használata esetén.
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Töredezett MP4-et kibocsátó élő kódolók 

Az Azure Media Services az alábbi élő kódolók egyikének használatát javasolja, amelyek többbitráta-töredezett MP4 (Smooth Streaming) kimenettel rendelkeznek:

- Media Excel Hero Live és Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (2.14.15-ös és újabb verzió a TLS 1.2 követelmény miatt)

  A kódolóknak támogatniuk kell a TLS 1.2 protokollt HTTPS protokollok használata esetén.
- Envivio 4Caster C4 Gen III
- Képzeld el, Kommunikáció Selenio MCP3

> [!NOTE]
> Az élő kódoló egy sávszélességű adatfolyamot küldhet egy áthaladási csatornára, de ez a konfiguráció nem ajánlott, mert nem teszi lehetővé az adaptív sávszélességű streamelést az ügyfélszámára.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Hogyan válhat egy helyszíni kódoló partner

Az Azure Media Services helyszíni kódoló partnereként a Media Services úgy népszerűsíti a terméket, hogy a kódolót a vállalati ügyfeleknek ajánlja. Ahhoz, hogy helyszíni kódoló partner ré váljon, ellenőriznie kell a helyszíni kódoló és a Media Services kompatibilitását. Ehhez végezze el a következő ellenőrzéseket:

Áthaladás a csatorna ellenőrzésén
1. Az Azure Media Services-fiók létrehozása vagy felkeresése
2. **Áthaladási** csatorna létrehozása és indítása
3. Állítsa be a kódolót úgy, hogy többbitrátásos élő közvetítést küldjön le.
4. Közzétett élő esemény létrehozása
5. Futtassa az élő kódolót körülbelül 10 percig
6. Az élő esemény leállítása
7. Hozzon létre, indítson el egy streamelési végpontot, használjon egy lejátszót, például az [Azure Media Playert](https://aka.ms/azuremediaplayer) az archivált eszköz megtekintéséhez annak érdekében, hogy a lejátszásnak ne legyenlátható hibája az összes minőségi szinten (Vagy pedig figyelje és érvényesítse az előnézeti URL-t az élő munkamenet során a 6. lépés előtt)
8. Az eszközazonosító, az élő archívum közzétett streamelési URL-címének, valamint az élő kódolóbeállításainak és verziójának rögzítése
9. A csatorna állapotának visszaállítása az egyes minták létrehozása után
10. Ismételje meg a 3–9.

Élő kódolási csatorna ellenőrzése
1. Az Azure Media Services-fiók létrehozása vagy felkeresése
2. **Élő kódolási** csatorna létrehozása és indítása
3. Állítsa be a kódolót úgy, hogy egy átviteli sebességű élő közvetítést továbbítson.
4. Közzétett élő esemény létrehozása
5. Futtassa az élő kódolót körülbelül 10 percig
6. Az élő esemény leállítása
7. Hozzon létre, indítson el egy streamelési végpontot, használjon egy lejátszót, például az [Azure Media Playert](https://aka.ms/azuremediaplayer) az archivált eszköz megtekintéséhez annak érdekében, hogy a lejátszásnak ne legyenlátható hibája az összes minőségi szinten (Vagy pedig figyelje és érvényesítse az előnézeti URL-t az élő munkamenet során a 6. lépés előtt)
8. Az eszközazonosító, az élő archívum közzétett streamelési URL-címének, valamint az élő kódolóbeállításainak és verziójának rögzítése
9. A csatorna állapotának visszaállítása az egyes minták létrehozása után
10. Ismételje meg a 3–9.

Hosszú élettartam ellenőrzése
1. Az Azure Media Services-fiók létrehozása vagy felkeresése
2. **Áthaladási** csatorna létrehozása és indítása
3. Állítsa be a kódolót úgy, hogy többbitrátásos élő közvetítést küldjön le.
4. Közzétett élő esemény létrehozása
5. Az élő kódoló futtatása egy hétig vagy tovább
6. Egy lejátszó, például az [Azure Media Player](https://aka.ms/azuremediaplayer) használatával időről időre megtekintheti az élő közvetítést (vagy archivált eszközt), hogy a lejátszásnak ne legyenlátható hibája
7. Az élő esemény leállítása
8. Az eszközazonosító, az élő archívum közzétett streamelési URL-címének, valamint az élő kódolóbeállításainak és verziójának rögzítése

Végül küldje el a rögzített beállításokat és az élő amsstreaming@microsoft.comarchiválási paramétereket a Media Services-nek e-mailben. A bevételezést követően a Media Services ellenőrző teszteket hajt végre az élő kódolóból vett mintákon. A folyamattal kapcsolatos kérdéseivel forduljon a Médiaszolgálathoz.
