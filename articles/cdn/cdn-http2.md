---
title: HTTP/2 támogatás az Azure CDN-ben | Microsoft dokumentumok
description: További információ a HTTP/2 és a CDN támogatásról.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: 703623e3f7f314d87417458f3f9a218dfdf45427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67849972"
---
# <a name="http2-support-in-azure-cdn"></a>HTTP/2 támogatás az Azure CDN-ben

A HTTP/2 a HTTP/1.1\protokoll egyik fő módosítása. Gyorsabb webes teljesítményt, csökkentett válaszidőt és jobb felhasználói élményt biztosít, miközben megőrzi a jól ismert HTTP-módszereket, állapotkódokat és szemantikát. Bár a HTTP/2 http-vel és HTTPS-kapcsolattal működik, sok ügyfélwebböngésző csak a HTTP/2-t támogatja a TLS-en keresztül.

### <a name="http2-benefits"></a>HTTP/2 előnyei

A HTTP/2 előnyei a következők:

*   **Multiplexelés és egyidejűség**

    A HTTP 1.1 használatával több erőforrás-kérelem esetén több TCP-kapcsolatra van szükség, és minden kapcsolathoz teljesítményterhelés jár. A HTTP/2 lehetővé teszi, hogy egyetlen TCP-kapcsolaton keresztül több erőforrást igényeljen.

*   **Fejléc tömörítése**

    A kiszolgált erőforrások HTTP-fejléceinek tömörítésével jelentősen csökken a vezetéken töltött idő.

*   **Streamfüggőségek**

    Az adatfolyam-függőségek lehetővé teszik az ügyfél számára, hogy jelezze a kiszolgálónak, hogy mely erőforrások élveznek prioritást.


## <a name="http2-browser-support"></a>HTTP/2 böngészőtámogatás

Az összes főbb böngészők végre HTTP / 2 támogatást a jelenlegi verziók. A nem támogatott böngészők automatikusan visszatérnek a HTTP/1.1-re.

|Böngésző|Minimális verzió|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>HTTP/2 támogatás engedélyezése az Azure CDN-ben

Jelenleg a HTTP/2-támogatás az összes Azure CDN-profilhoz aktív. Az ügyfelektől nincs szükség további lépésekre.

## <a name="next-steps"></a>Következő lépések

A HTTP/2 előnyeinek működés közbeni megtekintéséhez tekintse meg [az Akamai bemutatóját.](https://http2.akamai.com/demo)

Ha többet szeretne megtudni a HTTP/2-ről, látogasson el az alábbi forrásokra:

*   [HTTP/2 specifikáció honlapja](https://http2.github.io/)
*   [Hivatalos HTTP/2 GYIK](https://http2.github.io/faq/)
*   [Akamai HTTP/2 információ](https://http2.akamai.com/)

Ha többet szeretne megtudni az Azure CDN elérhető funkcióiról, olvassa el az [Azure CDN áttekintése című témakört.](https://azure.microsoft.com/documentation/articles/cdn-overview/)