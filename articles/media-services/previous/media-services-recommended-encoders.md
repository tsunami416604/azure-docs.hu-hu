---
title: A Azure Media Services által javasolt kódolók ismertetése | Microsoft Docs
description: A Media Services által ajánlott kódolók ismertetése
services: media-services
keywords: kódolás; kódolók; adathordozó
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: fc481129e652c6dacd15a5a6d039a9118393e8f1
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854244"
---
# <a name="recommended-on-premises-encoders"></a>Ajánlott helyszíni kódolók
Azure Media Services használatával folytatott élő közvetítésnél megadhatja, hogy a csatorna hogyan kapja meg a bemeneti adatfolyamot. Ha a helyszíni kódolót élő kódolási csatornával szeretné használni, a kódolónak kimenetként kell leküldenie egy kiváló minőségű, egysebességű adatfolyamot. Ha úgy dönt, hogy egy helyszíni kódolót használ egy átmenő csatornán keresztül, a kódolónak egy többszörös átviteli sebességű streamet kell leküldenie az összes kívánt kimeneti tulajdonsággal. További információ: [élő közvetítés helyszíni kódolókkal](media-services-live-streaming-with-onprem-encoders.md).

Azure Media Services javasolja a következő élő kódolók egyikének használatát, amelyeknek az RTMP as kimenete:
- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Telestream Wirecast 8.1+
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

Azure Media Services azt javasolja, hogy a következő élő kódolók egyikét használja kimenetként a többszörös sávszélességű töredezett MP4 (Smooth Streaming).
- Media Excel Hero Live és Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3

> [!NOTE]
> Egy élő kódoló egy átviteli sebességű streamet küldhet egy átmenő csatornára, ez a konfiguráció azonban nem ajánlott, mivel nem teszi lehetővé az adaptív sávszélességű adatfolyam-továbbítást az ügyfél számára.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Helyszíni kódoló partner létrehozása
Azure Media Services a helyszíni kódoló partnernek, Media Services népszerűsíti a terméket, ha a kódolót a nagyvállalati ügyfeleknek ajánlja. Ahhoz, hogy helyszíni kódoló partner legyen, ellenőriznie kell a helyszíni kódoló kompatibilitását Media Services használatával. Ehhez végezze el a következő ellenőrzéseket:

Csatorna ellenőrzésének továbbítása
1. Azure Media Services fiók létrehozása vagy meglátogatása
2. Áteresztő csatorna létrehozása **** és elindítása
3. Állítsa be a kódolót a többszörös sávszélességű élő stream küldéséhez.
4. Közzétett élő esemény létrehozása
5. Futtassa az élő kódolót körülbelül 10 percen keresztül
6. Az élő esemény leállítása
7. Hozzon létre, indítson el egy streaming-végpontot [](https://aka.ms/azuremediaplayer) , használjon például Azure Media Playert az archivált eszköz megtekintéséhez, és győződjön meg arról, hogy a lejátszás nem tartalmaz látható hibát az összes minőségi szinten (vagy az élő munkamenetben tekintse meg és ellenőrizze az előnézeti URL-címen keresztül. a 6. lépés előtt)
8. Jegyezze fel az eszköz AZONOSÍTÓját, a közzétett streaming URL-címet az élő archívumhoz, valamint az élő kódoló által használt beállításokat és verziót
9. A csatorna állapotának alaphelyzetbe állítása az egyes minták létrehozása után
10. Ismételje meg a 3 – 9. lépést a kódoló által támogatott összes konfiguráció esetében (ad-jelzési/feliratokkal vagy eltérő kódolási sebességgel)

Élő kódolási csatorna ellenőrzése
1. Azure Media Services fiók létrehozása vagy meglátogatása
2. **Élő kódolási** csatorna létrehozása és elindítása
3. Állítsa be a kódolót úgy, hogy egyetlen sávszélességű élő streamet helyezzen el.
4. Közzétett élő esemény létrehozása
5. Futtassa az élő kódolót körülbelül 10 percen keresztül
6. Az élő esemény leállítása
7. Hozzon létre, indítson el egy streaming-végpontot [](https://aka.ms/azuremediaplayer) , használjon például Azure Media Playert az archivált eszköz megtekintéséhez, és győződjön meg arról, hogy a lejátszás nem tartalmaz látható hibát az összes minőségi szinten (vagy az élő munkamenetben tekintse meg és ellenőrizze az előnézeti URL-címen keresztül. a 6. lépés előtt)
8. Jegyezze fel az eszköz AZONOSÍTÓját, a közzétett streaming URL-címet az élő archívumhoz, valamint az élő kódoló által használt beállításokat és verziót
9. A csatorna állapotának alaphelyzetbe állítása az egyes minták létrehozása után
10. Ismételje meg a 3 – 9. lépést a kódoló által támogatott összes konfiguráció esetében (ad-jelzések/feliratok/különböző kódolási sebességek nélkül)

Élettartam ellenőrzése
1. Azure Media Services fiók létrehozása vagy meglátogatása
2. Áteresztő csatorna létrehozása **** és elindítása
3. Állítsa be a kódolót a többszörös sávszélességű élő stream küldéséhez.
4. Közzétett élő esemény létrehozása
5. Élő kódoló futtatása egy hétig vagy tovább
6. Az élő adatfolyamok időről időre (vagy archivált eszközre) való megtekintésével ellenőrizheti, hogy a lejátszás nem rendelkezik-e látható hibákkal (például [Azure Media Player](https://aka.ms/azuremediaplayer) ).
7. Az élő esemény leállítása
8. Jegyezze fel az eszköz AZONOSÍTÓját, a közzétett streaming URL-címet az élő archívumhoz, valamint az élő kódoló által használt beállításokat és verziót

Végül küldje el a rögzített beállításokat és az élő archiválási paramétereket Media Services e-mailben amsstreaming@microsoft.com. A beérkezés után a Media Services ellenőrző teszteket hajt végre az élő kódoló mintáján. A folyamattal kapcsolatos bármilyen kérdéssel kapcsolatba léphet a Media Services.
