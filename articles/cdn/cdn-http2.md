---
title: "HTTP/2-támogatás az Azure CDN |} Microsoft Docs"
description: "Ismerje meg a HTTP/2 és a CDN támogatása."
services: cdn
documentationcenter: 
author: lichard
manager: erikre
editor: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: rli
ms.openlocfilehash: 4f8dd685c3ae89535217d7a17a01c5129ca7e6e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="http2-support-in-azure-cdn"></a>Az Azure CDN HTTP/2-támogatás

A HTTP/2 HTTP/1.1\ nagyobb javítása. Biztosít gyorsabban webes teljesítmény, a csökkentett válaszidő és a jobb felhasználói élmény, a megszokott HTTP-metódus állapotkódok és szemantikáját megőrzésével. Bár a HTTP/2 célja, hogy a HTTP és HTTPS használata, számos ügyfél webböngészőben csak támogatja a HTTP/2 TLS feletti.

###<a name="http2-benefits"></a>A HTTP/2 előnyei

A HTTP/2 előnyei a következők:

*   **Multiplexáló és feldolgozási**

    HTTP 1.1 több és több erőforrás-kérelmek több TCP-kapcsolatot igényel, és minden egyes kapcsolathoz teljesítményigény társítva van. HTTP/2 lehetővé teszi, hogy több erőforrás vonatkozó egyetlen TCP-kapcsolatot.

*   **Fejléc tömörítése**

    A HTTP-fejlécek szolgálatban erőforrások tömörítésével vezetéken lévő idő jelentősen csökkent.

*   **Adatfolyam-függőségek**

    Adatfolyam-függőségek engedélyezi az ügyfél megjelenítésével jelzi a kiszolgáló, amely erőforrások prioritással rendelkezik.


##<a name="http2-browser-support"></a>A HTTP/2 webböngésző támogatása

Az összes ismertebb böngésző alkalmaztunk, hogy HTTP/2-támogatás az aktuális verzióban. Nem támogatott böngészők lesz automatikusan tartalék HTTP/1.1.

|Böngésző|Minimális verzió|
|-------------|------------|
|A Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

##<a name="enabling-http2-support-in-azure-cdn"></a>Az Azure CDN HTTP/2-támogatás engedélyezése

Jelenleg aktív a HTTP/2 támogatási **Akamai Azure CDN** és **Azure CDN Verizon** profilok. Az ügyfelek semmilyen további műveletet kell biztosítania.

##<a name="next-steps"></a>Következő lépések

A HTTP/2 előnyeit működés közben, olvassa el [ebben a bemutatóban a Akamai](https://http2.akamai.com/demo).

A HTTP/2 kapcsolatos további információkért látogasson el a következőket:

*   [A HTTP/2 specification-kezdőlap](https://http2.github.io/)
*   [Hivatalos HTTP/2 – gyakori kérdések](https://http2.github.io/faq/)
*   [Akamai HTTP/2-információk](https://http2.akamai.com/)

Azure CDN elérhető funkciókkal kapcsolatos további tudnivalókért tekintse meg a [Azure CDN áttekintése](https://azure.microsoft.com/documentation/articles/cdn-overview/).