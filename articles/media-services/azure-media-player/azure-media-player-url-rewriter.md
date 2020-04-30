---
title: Azure Media Player URL-újraíró
description: Azure Media Player egy adott URL-címet ír elő a Azure Media Servicesból a SMOOTH, DASH, HLS v3 és HLS v4 streamek biztosításához.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: f238a2a3c499cf1e36f5e7c40e087375b7db0a70
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726458"
---
# <a name="url-rewriter"></a>URL-átíró #

Alapértelmezés szerint a Azure Media Player egy adott URL-címet ír a Azure Media Servicesból a SMOOTH, DASH, HLS v3 és HLS v4 streamek biztosításához. Ha például a forrást a következőképpen adja meg, Azure Media Player biztosítja, hogy megpróbálja lejátszani a fenti protokollokat:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Ha azonban nem kívánja használni az URL-újraíró használatát, ezt a `disableUrlRewriter` tulajdonság paraméterrel való hozzáadásával teheti meg. Ez azt jelenti, hogy a forrásoknak átadott összes információ módosítása nélkül, közvetlenül a lejátszónak lesz átadva.  Íme egy példa arra, hogyan lehet két forrást hozzáadni a lejátszóhoz, KÖTŐJELen és egy SMOOTH Streamingen.

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

Ha szeretné, megadhatja azokat a folyamatos átviteli formátumokat, amelyeket Azure Media Player szeretne újraírni a `streamingFormats` (z) paraméter használatával. A lehetőségek `DASH`a `SMOOTH`következők `HLSv3`: `HLSv4`, `HLS`,,,. A HLS és a HLSv3 & v4 közötti különbség az, hogy a HLS formátuma támogatja a FairPlay-tartalmak lejátszását. a v3 és a v4 nem támogatja a FairPlay. Ez akkor hasznos, ha nincs elérhető kézbesítési házirend egy adott protokollhoz.  Íme egy példa arra, ha egy kötőjel protokoll nincs engedélyezve az adategységben.

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

A fenti kettő kombinálható egymással az adott eszközön alapuló több körülmény esetén.

> [!NOTE]
> A Widevine-védelmi információk csak a kötőjel protokollon maradnak meg.

## <a name="next-steps"></a>További lépések ##

- [Azure Media Player rövid útmutató](azure-media-player-quickstart.md)