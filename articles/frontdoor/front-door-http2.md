---
title: Azure bejárati ajtó – HTTP2-támogatás | Microsoft Docs
description: Ez a cikk segítséget nyújt a HTTP/2-támogatás az Azure bejárati ajtón való megismeréséhez
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 7cdcef80ef2433d34b32589d2539089fb9801eb1
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399429"
---
# <a name="http2-support-in-azure-front-door"></a>HTTP/2 támogatás az Azure-beli bejárati ajtón

Jelenleg a HTTP/2 támogatás aktív az összes Azure bejárati ajtó konfigurációjában. Az ügyfelektől nem szükséges további művelet.

A HTTP/2 a HTTP/1.1 fő verziója. Gyorsabb webes teljesítményt, kisebb válaszidőt és továbbfejlesztett felhasználói élményt biztosít, miközben megtartja az ismerős HTTP-metódusokat, az állapot-kódokat és a szemantikai funkciókat. Bár a HTTP/2 a HTTP-vel és a HTTPS-vel való együttműködésre lett tervezve, sok ügyfél-webböngésző csak a HTTP/2 protokollt támogatja Transport Layer Security (TLS) protokollon keresztül.

> [!NOTE]
> A HTTP/2 protokoll támogatása csak az ügyfelektől a bejáratig tartó kérelmek esetében érhető el. A háttérrendszer-végpontról a háttér felé irányuló kommunikáció a HTTP/1.1 protokollon keresztül történik. 

### <a name="http2-benefits"></a>HTTP/2 előnyök

A HTTP/2 előnyei a következők:

*   **Multiplex és Egyidejűség**

    A HTTP 1,1 használatával több erőforrás-kérelemhez több TCP-kapcsolat szükséges, és minden kapcsolathoz teljesítménybeli terhelés tartozik. A HTTP/2 lehetővé teszi több erőforrás kérelmezését egyetlen TCP-kapcsolatban.

*   **Fejléc tömörítése**

    Ha tömöríti a HTTP-fejléceket a kiszolgált erőforrásokhoz, a vezetékes idő jelentősen csökken.

*   **Stream-függőségek**

    A stream-függőségek lehetővé teszik az ügyfél számára, hogy jelezze a kiszolgálónak, hogy mely erőforrások rendelkeznek prioritással.


## <a name="http2-browser-support"></a>HTTP/2 böngésző-támogatás

Az összes fő böngésző implementálta a HTTP/2-támogatást a jelenlegi verzióiban. A nem támogatott böngészők automatikusan tartalék HTTP/1.1-re.

|Böngésző|Minimális verzió|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Operát| 32|
|Safari| 9|

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a HTTP/2-ről, látogasson el az alábbi forrásokra:

- [HTTP/2 specifikációs Kezdőlap](https://http2.github.io/)
- [Hivatalos HTTP/2 – gyakori kérdések](https://http2.github.io/faq/)
- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
