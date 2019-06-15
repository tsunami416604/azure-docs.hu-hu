---
title: HTTP/2-támogatás az Azure CDN-ben |} A Microsoft Docs
description: Ismerje meg a HTTP/2 és a CDN-támogatás.
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
ms.openlocfilehash: 2d27cd54486a08e18fe74c852af29d5cf6432023
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60737074"
---
# <a name="http2-support-in-azure-cdn"></a>HTTP/2-támogatás az Azure CDN-ben

HTTP/2 HTTP/1.1\ fő felülvizsgálatát. Biztosít gyorsabban webes teljesítmény, a csökkentett válaszidő és a felhasználói élmény, a jól ismert HTTP-metódusok, állapotkódok és szemantika megőrzése mellett. Bár a HTTP/2 a HTTP és HTTPS célja, sok ügyfél webböngészővel csak támogatja a HTTP/2 TLS protokollon keresztüli.

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

## <a name="enabling-http2-support-in-azure-cdn"></a>HTTP/2-támogatás az Azure CDN engedélyezése

HTTP/2-támogatás jelenleg aktív, az összes Azure CDN-profil számára. Semmilyen további műveletet nem kell az ügyfelektől származó.

## <a name="next-steps"></a>További lépések

A HTTP/2 előnyeit működés közben, olvassa el [a bemutatóhoz az Akamaitól](https://http2.akamai.com/demo).

HTTP/2 kapcsolatos további információkért látogasson el az alábbi forrásanyagokat:

*   [HTTP/2 specifikáció kezdőlapja](https://http2.github.io/)
*   [Hivatalos HTTP/2 – gyakori kérdések](https://http2.github.io/faq/)
*   [Akamai HTTP/2-információk](https://http2.akamai.com/)

Elérhető az Azure CDN-funkciókkal kapcsolatos további tudnivalókért tekintse meg a [Azure CDN áttekintése](https://azure.microsoft.com/documentation/articles/cdn-overview/).