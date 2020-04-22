---
title: Az Azure Media Player URL-újraírója
description: Az Azure Media Player átírja az Azure Media Services adott URL-címét, hogy streameket biztosítson a SMOOTH, a DASH, a HLS v3 és a HLS v4 számára.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: f238a2a3c499cf1e36f5e7c40e087375b7db0a70
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726458"
---
# <a name="url-rewriter"></a>URL-író #

Alapértelmezés szerint az Azure Media Player átírja az Azure Media Services adott URL-címét, hogy streameket biztosítson a SMOOTH, a DASH, a HLS v3 és a HLS v4 számára. Ha például a forrás a következőképpen jelenik meg, az Azure Media Player biztosítja, hogy megkísérli lejátszani az összes fenti protokollt:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Ha azonban nem szeretné használni az URL-újraírót, ezt `disableUrlRewriter` úgy teheti meg, hogy hozzáadja a tulajdonságot a paraméterhez. Ez azt jelenti, hogy a forrásoknak átadott összes információt módosítás nélkül közvetlenül átadják a játékosnak.  Íme egy példa két forrás hozzáadására a lejátszóhoz, a DASH-hez és egy SMOOTH Streaminghez.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

vagy

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Ha szeretné, megadhatja azokat a streamelési formátumokat, amelyeket az `streamingFormats` Azure Media Player újraírna a paraméter használatával. A `DASH`lehetőségek `SMOOTH` `HLSv3`közé `HLSv4` `HLS`tartozik a , , , , . A HLS és a HLSv3 & v4 közötti különbség az, hogy a HLS formátum támogatja a FairPlay tartalom lejátszását. v3 és v4 nem támogatják fairplay. Ez akkor hasznos, ha nem rendelkezik egy adott protokollhoz elérhető kézbesítési szabályzattal.  Íme egy példa arra, ha egy DASH protokoll nincs engedélyezve az eszközzel.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

vagy

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

A fenti kettő egymással kombinálva használható az Adott eszköz alapján több körülmény esetén is.

> [!NOTE]
> A widevine védelmi információk csak a DASH protokollon maradnak meg.

## <a name="next-steps"></a>További lépések ##

- [Az Azure Media Player rövid útmutatója](azure-media-player-quickstart.md)