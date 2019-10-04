---
title: Azure bejárati szolgáltatás – HTTP2-támogatás | Microsoft Docs
description: Ez a cikk segítséget nyújt az Azure bejárati szolgáltatásának HTTP/2-támogatásának megismeréséhez
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
ms.openlocfilehash: c3c1721454c0b3c96071c685a764f34d4fa540b9
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775266"
---
# <a name="http2-support-in-azure-front-door-service"></a>HTTP/2 támogatás az Azure bejárati ajtó szolgáltatásában

Jelenleg a HTTP/2 támogatás aktív az összes bejárati ajtó konfigurációjában. Az ügyfelektől nem szükséges további művelet.

A HTTP/2 a HTTP/1.1 fő verziója. Gyorsabb webes teljesítményt, kisebb válaszidőt és továbbfejlesztett felhasználói élményt biztosít, miközben megtartja az ismerős HTTP-metódusokat, az állapot-kódokat és a szemantikai funkciókat. Bár a HTTP/2 a HTTP-vel és a HTTPS-vel való együttműködésre lett tervezve, sok ügyfél-webböngésző csak a HTTP/2 protokollt támogatja Transport Layer Security (TLS) protokollon keresztül.

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

|Browser|Minimális verzió|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a HTTP/2-ről, látogasson el az alábbi forrásokra:

- [HTTP/2 specifikációs Kezdőlap](https://http2.github.io/)
- [Hivatalos HTTP/2 – gyakori kérdések](https://http2.github.io/faq/)
- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
