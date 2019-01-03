---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: d1416e7a6cad079be94b0a9b48d7c7c71a598cc2
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977362"
---
Fluentd egy nyílt forráskódú adatgyűjtő egyesített naplózási. A `Fluentd` beállítások kezelése, a tároló kapcsolati egy [Fluentd](https://www.fluentd.org) kiszolgáló. A tároló tartalmazza a Fluentd naplózási szolgáltató, amely lehetővé teszi, hogy a tároló is naplóznak, és opcionálisan metrikaadatok Fluentd kiszolgálóhoz.

A következő táblázat ismerteti a támogatott konfigurációs beállításait a `Fluentd` szakaszban.

| Name (Név) | Adattípus | Leírás |
|------|-----------|-------------|
| `Host` | Karakterlánc | Az IP-cím vagy a Fluentd kiszolgáló DNS-állomásneve. |
| `Port` | Egész szám | A port, a Fluentd kiszolgáló.<br/> Az alapértelmezett érték: 24224. |
| `HeartbeatMs` | Egész szám | A szívverési időköz ezredmásodpercben. Ha esemény forgalmat a rendszer elküldte, ez az időtartam lejárta előtt, szívverést küld a Fluentd kiszolgálóra. Az alapértelmezett érték: 60000 ezredmásodperc (1 perc). |
| `SendBufferSize` | Egész szám | A hálózati pufferterületének, műveletek számára lefoglalt (bájt). Az alapértelmezett érték: 32768 bájtok (32 kilobájt). |
| `TlsConnectionEstablishmentTimeoutMs` | Egész szám | Az időkorlát ezredmásodpercben, a Fluentd kiszolgálóval SSL/TLS kapcsolatot létesíteni. Az alapértelmezett érték: 10000 ezredmásodperc (10 másodperc).<br/> Ha `UseTLS` értéke hamis értékre, ezt az értéket figyelmen kívül hagyja. |
| `UseTLS` | Logikai | Azt jelzi, hogy a tároló kell használnia az SSL/TLS a Fluentd kiszolgálóval való kommunikáció során. Az alapértelmezett értéke FALSE (hamis). |