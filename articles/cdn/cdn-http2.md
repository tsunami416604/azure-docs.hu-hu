---
title: HTTP/2-támogatás az Azure CDN |} Microsoft Docs
description: Ismerje meg a HTTP/2 és a CDN támogatása.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: rli
ms.openlocfilehash: b4751320af82a29fb13dc6012c1b197ebc2b1f9b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="http2-support-in-azure-cdn"></a>Az Azure CDN HTTP/2-támogatás

A HTTP/2 HTTP/1.1\ nagyobb javítása. Biztosít gyorsabban webes teljesítmény, a csökkentett válaszidő és a jobb felhasználói élmény, a megszokott HTTP-metódus állapotkódok és szemantikáját megőrzésével. Bár a HTTP/2 célja, hogy a HTTP és HTTPS használata, számos ügyfél webböngészőben csak támogatja a HTTP/2 TLS feletti.

### <a name="http2-benefits"></a>A HTTP/2 előnyei

A HTTP/2 előnyei a következők:

*   **Multiplexáló és feldolgozási**

    HTTP 1.1 több erőforrás kérést több TCP-kapcsolatot igényel, és minden egyes kapcsolathoz teljesítményigény társítva van. HTTP/2 lehetővé teszi, hogy több erőforrás vonatkozó egyetlen TCP-kapcsolatot.

*   **Fejléc tömörítése**

    A HTTP-fejlécek szolgálatban erőforrások tömörítésével vezetéken lévő idő jelentősen csökkent.

*   **Adatfolyam-függőségek**

    Adatfolyam-függőségek engedélyezi az ügyfél megjelenítésével jelzi a kiszolgáló, mely erőforrásokat prioritással rendelkezik.


## <a name="http2-browser-support"></a>A HTTP/2 webböngésző támogatása

Az összes ismertebb böngésző alkalmaztunk, hogy HTTP/2-támogatás az aktuális verzióban. Nem támogatott böngészők automatikusan tartalék HTTP/1.1.

|Böngésző|Minimális verzió|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Az Azure CDN HTTP/2-támogatás engedélyezése

A HTTP/2 támogatási jelenleg aktív, az összes Azure CDN-profil. Az ügyfelek semmilyen további műveletet kell biztosítania.

## <a name="next-steps"></a>További lépések

A HTTP/2 előnyeit működés közben, olvassa el [ebben a bemutatóban a Akamai](https://http2.akamai.com/demo).

A HTTP/2 kapcsolatos további információkért látogasson el a következőket:

*   [A HTTP/2 specification-kezdőlap](https://http2.github.io/)
*   [Hivatalos HTTP/2 – gyakori kérdések](https://http2.github.io/faq/)
*   [Akamai HTTP/2-információk](https://http2.akamai.com/)

Azure CDN elérhető funkciókkal kapcsolatos további tudnivalókért tekintse meg a [Azure CDN áttekintése](https://azure.microsoft.com/documentation/articles/cdn-overview/).