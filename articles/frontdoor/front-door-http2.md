---
title: Azure bejárati ajtajának szolgáltatás – a HTTP2 támogatása |} A Microsoft Docs
description: Ez a cikk segít a HTTP/2-támogatás az Azure bejárati ajtajának Service ismertetése
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 33e738f31be493d5890fc972ec71e7c6cd733bf4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736448"
---
# <a name="http2-support-in-azure-front-door-service"></a>Az Azure bejárati ajtajának Service HTTP/2-támogatás
HTTP/2 HTTP/1.1-es főbb felülvizsgálatát. Biztosít gyorsabban webes teljesítmény, a csökkentett válaszidő és a felhasználói élmény, a jól ismert HTTP-metódusok, állapotkódok és szemantika megőrzése mellett. Bár a HTTP/2 a HTTP és HTTPS célja, az számos ügyfél böngésző Transport Layer Security (TLS) keresztül támogatja csak HTTP/2.

### <a name="http2-benefits"></a>HTTP/2 előnyei

HTTP/2 előnyei a következők:

*   **Multiplexálási és egyidejűség**

    A HTTP 1.1 több erőforrás-kérelem indítására több TCP-kapcsolatot igényel, és minden egyes kapcsolat rendelkezik teljesítménybeli terhelést társítva. HTTP/2 lehetővé teszi, hogy több erőforrást vonatkozó egyetlen TCP-kapcsolatot.

*   **Fejléc tömörítése**

    A HTTP-fejlécek szolgálatban erőforrások tömörítésével az átviteli idő jelentősen csökkent.

*   **Stream-függőségek**

    Stream-függőségek lehetővé teszik az ügyfél számára, amelyek jelzik, hogy mely erőforrások prioritással a kiszolgáló.


## <a name="http2-browser-support"></a>HTTP/2 böngésző támogatása

Minden főbb böngésző a valósította meg a HTTP/2-támogatás a jelenlegi verzióban. Nem támogatott böngészők automatikusan tartalék HTTP/1.1.

|Böngésző|Minimális verziója|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-front-door-service"></a>Az Azure bejárati ajtajának Service HTTP/2-támogatás engedélyezése

HTTP/2-támogatás jelenleg aktív bejárati ajtajának konfigurációk. Semmilyen további műveletet nem kell az ügyfelektől származó.

## <a name="next-steps"></a>További lépések

HTTP/2 kapcsolatos további információkért látogasson el az alábbi forrásanyagokat:

- [HTTP/2 specifikáció kezdőlapja](https://http2.github.io/)
- [Hivatalos HTTP/2 – gyakori kérdések](https://http2.github.io/faq/)
- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
