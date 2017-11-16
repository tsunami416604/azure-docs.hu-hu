---
title: "További tudnivalók az Azure Media Services által ajánlott kódolók |} Microsoft Docs"
description: "További információk a media services által ajánlott kódolók"
services: media-services
keywords: "kódolás; kódolók; adathordozó"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d0c5536d2339470eac058250cc14e1f250b86d90
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="recommended-on-premises-encoders"></a>Ajánlott a helyszíni kódolókkal
Amikor élő adatfolyam-az Azure Media Services, megadhatja, hogyan történjen a csatorna a bemeneti adatfolyam fogadására. Ha egy helyszíni kódoló egy élő kódolás Channel használatát választja, a kódoló leküldéses egy kiváló minőségű egyféle sávszélességű adatfolyamokat output típusúként. Ha a csatorna továbbítása helyszíni kódoló használatát választja, a kódoló leküldéses többféle sávszélességűvé minden kívánt kimeneti minőségű kimenetként. További információkért lásd: [élő Stream továbbítása helyszíni kódolókkal](media-services-live-streaming-with-onprem-encoders.md).

Az Azure Media Services azt javasolja, hogy a következő élő kódolók kimenetként RTMP rendelkező egyikének használatával:
- Az Adobe Flash Media élő kódoló 3.2.
- Haivision Makito X HEVC
- Telestream Wirecast 8.1
- Teradek szelet 756
- TriCaster 8000
- Tricaster Mini HD-4

Az Azure Media Services azt javasolja, hogy a következő élő kódolók képesek többféle sávszélességű Smooth Streaming kimenetként rendelkező egyikének használatával:
- Élő TITAN Ateme
- Cisco digitális Media Encoder 2200
- Élő elemi
- Envivo 4Caster C4 generációs III.
- Képzelje el kommunikációs Selenio MCP3
- Media Excel Hero élő

> [!NOTE]
> Az élő kódolók egy egyféle sávszélességű adatfolyamot küldeni a csatorna továbbítása, de ez a konfiguráció nem ajánlott, mert nem teszi lehetővé az adaptív sávszélességű streamelés az ügyfélnek.

## <a name="how-to-become-an-on-prem-encoder-partner"></a>Hogyan válhat egy helyszíni kódolót partner
Partnerként Azure Media Services helyszíni kódolót a Media Services a termék elősegíti a vállalati ügyfelek a kódoló ajánlja. Egy helyszíni kódolót partner válnak, ellenőriznie kell a helyszíni kódolót kompatibilisek a Media Services. Ehhez hajtsa végre a következő ellenőrzések:

Csatorna ellenőrzési továbbítása
1. Hozzon létre, vagy keresse fel az Azure Media Services-fiók
2. Hozzon létre, és indítsa el a **áteresztő** csatorna
3. Konfigurálja a kódoló leküldéses egy többszörös sávszélességű élő streamet.
4. Közzétett élő esemény létrehozása
5. Futtassa az élő kódoló körülbelül 10 percig
6. Állítsa le az élő esemény
7. Jegyezze fel az eszköz azonosítója közzé a streamelési URL-cím, az élő archívum, és a beállítások és az élő kódoló a használt verzió
8. A csatorna visszaállítása után minden egyes minta létrehozása
9. Ismételje meg a 3-8 az összes konfiguráció támogatja a kódoló (rendelkező és anélküli ad jelzés/feliratok/különböző sebességű kódolás)

Élő kódolás csatorna ellenőrzése
1. Hozzon létre, vagy keresse fel az Azure Media Services-fiók
2. Hozzon létre, és indítsa el a **élő kódolás** csatorna
3. Konfigurálja a kódoló leküldéses egy egyszeres sávszélességű élő streamet.
4. Közzétett élő esemény létrehozása
5. Futtassa az élő kódoló körülbelül 10 percig
6. Állítsa le az élő esemény
7. Jegyezze fel az eszköz azonosítója közzé a streamelési URL-cím, az élő archívum, és a beállítások és az élő kódoló a használt verzió
8. A csatorna visszaállítása után minden egyes minta létrehozása
9. Ismételje meg a 3-8 az összes konfiguráció támogatja a kódoló (rendelkező és anélküli ad jelzés/feliratok/különböző sebességű kódolás)

Az érvényességi ideje ellenőrzése
1. Hozzon létre, vagy keresse fel az Azure Media Services-fiók
2. Hozzon létre, és indítsa el a **áteresztő** csatorna
3. Konfigurálja a kódoló leküldéses egy többszörös sávszélességű élő streamet.
4. Közzétett élő esemény létrehozása
5. Futtassa az élő kódoló egy hét vagy hosszabb ideig
6. Állítsa le az élő esemény
7. Jegyezze fel az eszköz azonosítója közzé a streamelési URL-cím, az élő archívum, és a beállítások és az élő kódoló a használt verzió

Végül a rögzített beállítások küldése és archív paraméterek Media Services segítségével élő amsstreaming@microsoft.com. Fogadásakor a Media Services az élő kódoló a minták ellenőrző teszteket hajt végre. Ez a folyamat kapcsolatos kérdéseivel a Media Services felveheti a kapcsolatot.