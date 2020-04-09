---
title: Azure Bejárati ajtó - HTTP2 támogatás | Microsoft dokumentumok
description: Ez a cikk segítséget nyújt a HTTP/2 támogatásmegismeréséhez az Azure Bejárati ajtajában
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
ms.openlocfilehash: 8a3ae8065553b34a72528cb0f2681e327dc90097
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985184"
---
# <a name="http2-support-in-azure-front-door"></a>HTTP/2 támogatás az Azure bejárati ajtajában

Jelenleg a HTTP/2-támogatás aktív az összes Azure Bejárati ajtó konfigurációk. Az ügyfelektől nincs szükség további lépésekre.

A HTTP/2 a HTTP/1.1 egyik fő módosítása. Gyorsabb webes teljesítményt, csökkentett válaszidőt és jobb felhasználói élményt biztosít, miközben megőrzi a jól ismert HTTP-módszereket, állapotkódokat és szemantikát. Bár a HTTP/2 http-vel és HTTPS-lel való együttműködésre szolgál, sok ügyfélwebböngésző csak a HTTP/2-t támogatja a Transport Layer Security (TLS) protokollal szemben.

> [!NOTE]
> A HTTP/2 protokoll támogatása csak az ügyfelek től a Bejárati ajtóig érkező kérelmek esetén érhető el. The communication from Front Door to back ends in the back-end pool happens over HTTP/1.1. 

### <a name="http2-benefits"></a>HTTP/2 előnyei

A HTTP/2 előnyei a következők:

*   **Multiplexelés és egyidejűség**

    A HTTP 1.1 használatával több erőforrás-kérelem esetén több TCP-kapcsolatra van szükség, és minden kapcsolathoz teljesítményterhelés jár. A HTTP/2 lehetővé teszi, hogy egyetlen TCP-kapcsolaton keresztül több erőforrást igényeljen.

*   **Fejléc tömörítése**

    A kiszolgált erőforrások HTTP-fejléceinek tömörítésével jelentősen csökken a vezetéken töltött idő.

*   **Streamfüggőségek**

    Az adatfolyam-függőségek lehetővé teszik az ügyfél számára, hogy jelezze a kiszolgálónak, hogy mely erőforrások élveznek prioritást.


## <a name="http2-browser-support"></a>HTTP/2 böngésző támogatása

Az összes főbb böngészők végre HTTP / 2 támogatást a jelenlegi verziók. A nem támogatott böngészők automatikusan visszatérnek a HTTP/1.1-re.

|Böngésző|Minimális verzió|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a HTTP/2-ről, látogasson el az alábbi forrásokra:

- [HTTP/2 specifikáció honlapja](https://http2.github.io/)
- [Hivatalos HTTP/2 GYIK](https://http2.github.io/faq/)
- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
