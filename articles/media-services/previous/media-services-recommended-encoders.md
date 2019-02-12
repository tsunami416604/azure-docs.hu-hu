---
title: További tudnivalók az Azure Media Services által javasolt kódolók |} A Microsoft Docs
description: További tudnivalók a media services által ajánlott kódolók
services: media-services
keywords: kódolás; kódolók; adathordozó
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 02/09/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f07b2d128bd770854b7c240fd3c964d880029d3c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999011"
---
# <a name="recommended-on-premises-encoders"></a>Ajánlott helyszíni kódolók
Amikor az élő adások online közvetítése az Azure Media Services, megadhatja, hogyan történjen a csatorna a bemeneti streamet. Ha egy helyszíni kódolót használata élő kódolás csatornát választja, a kódoló leküldéses egy kiváló minőségű egyszeres átviteli sebességű streamet kimenetként. Ha egy helyszíni kódolót használata a pass-csatornán keresztül, a kódoló leküldéses egy többszörös sávszélességű streamet az összes kívánt kimeneti minőség kimenetként. További információkért lásd: [élő adások online közvetítése helyszíni kódolókkal](media-services-live-streaming-with-onprem-encoders.md).

Az Azure Media Services javasolja, hogy valamelyik a következő élő kódolók képesek kimenetként RTMP rendelkezik:
- Az Adobe Flash Media Live Encoder 3.2-es verzióját
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Teradek szelet 756
- A 8000-es TriCaster
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

Az Azure Media Services a következő élő kódolók többféle sávszélességű töredezett-MP4 (Smooth Streaming) kimenetként rendelkező egyikének használatát javasolja:
- Media Excel élő Hero és a Hero 4 KB-os (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 csomag általános III.
- Az Imagine Communications Selenio MCP3

> [!NOTE]
> Az élő kódolók olyan egy egyféle sávszélességű adatfolyamot küldeni a csatorna továbbítása, de ez a konfiguráció nem ajánlott, mert nem teszi lehetővé az adaptív sávszélességű streamelés az ügyfélnek.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Hogyan válhat egy helyszíni kódolót partner
Egy Azure Media Services – helyszíni kódolót partnerként a Media Services a termék elősegíti a vállalati ügyfelek számára a kódoló javaslatot. Egy helyszíni kódolót partner szeretne lenni, ellenőriznie kell a helyszíni kódolót kompatibilitását a Media Services használatával. Ehhez hajtsa végre a következő ellenőrzések:

Csatorna ellenőrzési továbbítása
1. Hozzon létre, vagy keresse fel az Azure Media Services-fiók
2. Hozzon létre, és indítsa el a **átmenő** csatorna
3. Konfigurálja a kódoló leküldése egy többszörös sávszélességű élő streamet.
4. A közzétett élő esemény létrehozása
5. Futtassa az élő kódoló körülbelül 10 percre
6. Állítsa le az élő esemény
7. Hozzon létre, a Streaming endpoint start, például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az adott lejátszási ne legyen az összes minőségi szinthez nem látható hipervizorgazdákat archivált objektumot (vagy másik megoldásként nézze meg, és ellenőrizze az előnézeti URL-CÍMEN keresztül a 6. lépés előtt élő munkamenetben)
8. Jegyezze fel az eszköz azonosítója, közzétett streamelési URL-cím, az élő archívumot, és a beállítások és az élő kódoló a használt verziója.
9. Mindegyik minta létrehozása után a csatorna állapot alaphelyzetbe állítása
10. Ismételje meg a 3 – 9 a konfigurációk a kódoló (rendelkező és anélküli ad jelzés/akadálymentes feliratok/különböző sebességek kódolás) által támogatott

Élő csatorna kódolási ellenőrzése
1. Hozzon létre, vagy keresse fel az Azure Media Services-fiók
2. Hozzon létre, és indítsa el a **valós idejű kódolás** csatorna
3. Konfigurálja a kódoló leküldése egy egyféle sávszélességű élő streamet.
4. A közzétett élő esemény létrehozása
5. Futtassa az élő kódoló körülbelül 10 percre
6. Állítsa le az élő esemény
7. Hozzon létre, a Streaming endpoint start, például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az adott lejátszási ne legyen az összes minőségi szinthez nem látható hipervizorgazdákat archivált objektumot (vagy másik megoldásként nézze meg, és ellenőrizze az előnézeti URL-CÍMEN keresztül a 6. lépés előtt élő munkamenetben)
8. Jegyezze fel az eszköz azonosítója, közzétett streamelési URL-cím, az élő archívumot, és a beállítások és az élő kódoló a használt verziója.
9. Mindegyik minta létrehozása után a csatorna állapot alaphelyzetbe állítása
10. Ismételje meg a 3 – 9 a konfigurációk a kódoló (rendelkező és anélküli ad jelzés/akadálymentes feliratok/különböző sebességek kódolás) által támogatott

Az érvényességi ideje ellenőrzése
1. Hozzon létre, vagy keresse fel az Azure Media Services-fiók
2. Hozzon létre, és indítsa el a **átmenő** csatorna
3. Konfigurálja a kódoló leküldése egy többszörös sávszélességű élő streamet.
4. A közzétett élő esemény létrehozása
5. Futtassa az élő kódoló egy hét vagy hosszabb ideig
6. Például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az élő streamelés időpontból lejátszás ne legyen semmilyen látható hipervizorgazdákat idő (vagy archivált objektumot)
7. Állítsa le az élő esemény
8. Jegyezze fel az eszköz azonosítója, közzétett streamelési URL-cím, az élő archívumot, és a beállítások és az élő kódoló a használt verziója.

Végül a rögzített beállítások küldése és archív paraméterek a Media Services élő e-mail amsstreaming@microsoft.com. Kap a Media Services az élő kódoló a minták ellenőrző teszteket hajt végre. Ez a folyamat kapcsolatos kérdéseivel a Media Services fordulnia.
