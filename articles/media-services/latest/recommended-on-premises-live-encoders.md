---
title: Ismerje meg az élő adatfolyam-továbbítási helyszíni kódolókkal történő továbbítását a Media Services – Azure által ajánlott |} A Microsoft Docs
description: További tudnivalók a Media Services által ajánlott élő streamelési helyszíni kódolók
services: media-services
keywords: kódolás; kódolók; adathordozó
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 12/14/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d1110669bd0ca8c0ba0caf34ef41861c500bdd33
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790028"
---
# <a name="recommended-live-streaming-encoders"></a>Ajánlott élő streamelési kódolók

A Media Services szolgáltatásban egy [videókhoz](https://docs.microsoft.com/rest/api/media/liveevents) (csatorna) egy olyan folyamatot jelent feldolgozása live-streaming-tartalmat. A videókhoz a bemeneti élő Streamek kap a két módszer egyikével:

* Egy helyszíni élő kódoló küld, a videókhoz, amely nincs engedélyezve a valós idejű kódolás a Media Services adatfolyam egy többszörös sávszélességű RTMP vagy Smooth Streaming (darabolt MP4). A feldolgozott adatfolyamok további feldolgozás nélkül LiveEvents továbbítása. Ezt a metódust meghívják **átmenő**. Az élő kódolók olyan egy egyféle sávszélességű adatfolyamot küldeni a csatorna továbbítása, de ez a konfiguráció nem ajánlott, mert nem teszi lehetővé az adaptív sávszélességű streamelés az ügyfélnek.

  > [!NOTE]
  > Valamely áteresztő módszer használata a leggazdaságosabb élő közvetítést végezni.

* A helyszíni élő kódoló egy egyféle sávszélességű adatfolyamot küld a videókhoz, amelyen engedélyezve van a valós idejű kódolás a Media Services a következő formátumok egyikében: RTMP vagy Smooth Streaming (darabolt MP4). A videókhoz végez a bejövő egyszeres átviteli sebességű streamet, és többféle sávszélességű (adaptív) video-adatfolyamot élő kódolás.

Valós idejű kódolás a Media Services használatával kapcsolatos részletes információkért lásd: [élő adások online közvetítése a Media Services v3](live-streaming-overview.md).

## <a name="live-encoders-that-output-rtmp"></a>Élő kódolók képesek RTMP-kimenetre

A Media Services RTMP kimenetként rendelkező következő élő kódolók egyikének használatát javasolja. A támogatott URL-sémák a `rtmp://` vagy `rtmps://`.

> [!NOTE]
 > Streamelési RTMP-n keresztül, amikor ellenőrizze a tűzfallal és/vagy a proxy beállításait, győződjön meg arról, hogy 1935 és 1936 kimenő TCP-portok nyitva-e.<br/>
 Streamelési RTMPS keresztül, amikor ellenőrizze a tűzfallal és/vagy a proxy beállításait, győződjön meg arról, hogy 2935 és 2936 kimenő TCP-portok nyitva-e.

- Az Adobe Flash Media Live Encoder 3.2-es verzióját
- Haivision KB
- Haivision Makito X HEVC
- OBS-Studio
- Környezetválasztó Studio (iOS)
- Telestream Wirecast 8.1 +
- Telestream Wirecast S
- Teradek szelet 756
- A 8000-es TriCaster
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>Élő kódolók képesek kimeneti töredékes MP4

A Media Services a következő élő kódolók többféle sávszélességű Smooth Streaming (töredékes-MP4) kimenetként rendelkező egyikének használatát javasolja. A támogatott URL-sémák a `rtmp://` vagy `rtmps://`.

- Élő TITAN Ateme
- Cisco digitális Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 csomag általános III.
- Az Imagine Communications Selenio MCP3
- Media Excel élő Hero és a Hero 4 KB-os (UHD/HEVC)

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Hogyan válhat egy helyszíni kódolót partner

Egy Azure Media Services a helyszíni kódolót partnerként a Media Services a termék elősegíti a vállalati ügyfelek számára a kódoló javaslatot. Egy helyszíni kódolót partner szeretne lenni, ellenőriznie kell a helyszíni kódolót kompatibilitását a Media Services használatával. Ehhez hajtsa végre a következő ellenőrzések:

### <a name="pass-through-liveevent-verification"></a>Az átmenő videókhoz ellenőrzése

1. Hozzon létre, vagy keresse fel az Azure Media Services-fiók.
2. Hozzon létre, és indítsa el a **átmenő** videókhoz.
3. Konfigurálja a kódoló leküldése egy többszörös sávszélességű élő streamet.
4. Hozzon létre egy közzétett élő esemény.
5. Futtassa az élő kódoló körülbelül 10 percre.
6. Leállítja az élő eseményt.
7. Hozzon létre, a Streaming endpoint start, például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az adott lejátszási ne legyen az összes minőségi szinthez nem látható hipervizorgazdákat archivált objektumot (vagy másik megoldásként nézze meg, és ellenőrizze az előnézeti URL-CÍMEN keresztül az élő munkamenet során 6. lépés előtt).
8. Jegyezze fel az eszköz azonosítója, közzétett streamelési URL-cím, az élő archívumot, és a beállítások és az élő kódoló a használt verzió.
9. Mindegyik minta létrehozása után videókhoz állapot alaphelyzetbe állítása.
10. Ismételje meg a 3 – 9 a konfigurációk a kódoló (rendelkező és anélküli ad jelzés/akadálymentes feliratok/különböző sebességek kódolás) által támogatott.

### <a name="live-encoding-liveevent-verification"></a>Valós idejű kódolás videókhoz ellenőrzése

1. Hozzon létre, vagy keresse fel az Azure Media Services-fiók.
2. Hozzon létre, és indítsa el a **valós idejű kódolás** videókhoz.
3. Konfigurálja a kódoló leküldése egy egyféle sávszélességű élő streamet.
4. Hozzon létre egy közzétett élő esemény.
5. Futtassa az élő kódoló körülbelül 10 percre.
6. Leállítja az élő eseményt.
7. Hozzon létre, a Streaming endpoint start, például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az adott lejátszási ne legyen az összes minőségi szinthez nem látható hipervizorgazdákat archivált objektumot (vagy másik megoldásként nézze meg, és ellenőrizze az előnézeti URL-CÍMEN keresztül az élő munkamenet során 6. lépés előtt).
8. Jegyezze fel az eszköz azonosítója, közzétett streamelési URL-cím, az élő archívumot, és a beállítások és az élő kódoló a használt verzió.
9. Mindegyik minta létrehozása után videókhoz állapot alaphelyzetbe állítása.
10. Ismételje meg a 3 – 9 a konfigurációk a kódoló (rendelkező és anélküli ad jelzés/akadálymentes feliratok/különböző sebességek kódolás) által támogatott.

### <a name="longevity-verification"></a>Az érvényességi ideje ellenőrzése

1. Hozzon létre, vagy keresse fel az Azure Media Services-fiók.
2. Hozzon létre, és indítsa el a **átmenő** csatorna.
3. Konfigurálja a kódoló leküldése egy többszörös sávszélességű élő streamet.
4. Hozzon létre egy közzétett élő esemény.
5. Az élő kódoló egy hét vagy hosszabb ideig fut.
6. Például egy lejátszóval [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) tekintse meg az élő streamelés időpontból lejátszás ne legyen semmilyen látható hipervizorgazdákat idő (vagy archivált objektumot).
7. Leállítja az élő eseményt.
8. Jegyezze fel az eszköz azonosítója, közzétett streamelési URL-cím, az élő archívumot, és a beállítások és az élő kódoló a használt verzió.

Végül, az e-mail a rögzített beállítások és archív paramétereket, az Azure Media Services élő amsstreaming@microsoft.com , egy értesítés, hogy az összes önkiszolgáló ellenőrzési ellenőrzések. Is bármely kövesse UPS a kapcsolattartási adatokat. Ez a folyamat kapcsolatos kérdéseivel Azure Media Services csapat fordulnia.

## <a name="next-steps"></a>További lépések

[Élő adások online közvetítése a Media Services v3](live-streaming-overview.md)
