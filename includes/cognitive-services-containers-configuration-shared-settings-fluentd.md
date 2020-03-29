---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: b08516b35a864eae6d15c4c5c928f0550c64c239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67712501"
---
Fluentd egy nyílt forráskódú adatgyűjtő az egységes naplózás. A `Fluentd` beállítások kezelik a tároló kapcsolatát egy [fluentd](https://www.fluentd.org) kiszolgálóval. A tároló tartalmaz egy Fluentd naplózási szolgáltatót, amely lehetővé teszi a tároló naplók és adott esetben metrikaadatok írását egy Fluentd kiszolgálóra.

Az alábbi táblázat a `Fluentd` szakaszban támogatott konfigurációs beállításokat ismerteti.

| Név | Adattípus | Leírás |
|------|-----------|-------------|
| `Host` | Sztring | A Fluentd kiszolgáló IP-címe vagy DNS-állomásneve. |
| `Port` | Egész szám | A Fluentd szerver portja.<br/> Az alapértelmezett érték: 24224. |
| `HeartbeatMs` | Egész szám | A szívverés időköze, ezredmásodpercben. Ha az időköz lejárta előtt nem lett eseményforgalom, a rendszer szívverést küld a Fluentd kiszolgálónak. Az alapértelmezett érték 60000 ezredmásodperc (1 perc). |
| `SendBufferSize` | Egész szám | A küldési műveletekhez lefoglalt hálózati pufferterület bájtban. Az alapértelmezett érték 32768 bájt (32 kilobájt). |
| `TlsConnectionEstablishmentTimeoutMs` | Egész szám | Az időtúllépés ezredmásodpercben a Fluentd kiszolgálóval való SSL/TLS-kapcsolat létrehozásához. Az alapértelmezett érték 10000 ezredmásodperc (10 másodperc).<br/> Ha `UseTLS` hamis értékre van állítva, a rendszer figyelmen kívül hagyja ezt az értéket. |
| `UseTLS` | Logikai | Azt jelzi, hogy a tárolónak ssl/TLS-t kell-e használnia a Fluentd kiszolgálóval való kommunikációhoz. Az alapértelmezett érték a hamis. |