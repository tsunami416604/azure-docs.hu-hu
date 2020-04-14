---
title: Feltételes kifejezések az Azure CDN – Verizon Premium szabályok motorhoz
description: Az Azure CDN referenciadokumentációja a Verizon Premium szabályok motorja megfelel a feltételeknek és funkcióknak.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 25b3c1a011e39adea651a6daa1d4ab9aa3a9d186
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253509"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN a Verizon Premium szabályok motor feltételes kifejezések

Ez a cikk az Azure Content Delivery Network (CDN) Rules Engine feltételes kifejezéseinek részletes [leírását sorolja fel.](cdn-verizon-premium-rules-engine.md)

A szabály első része a feltételes kifejezés.

Feltételes kifejezés | Leírás
-----------------------|-------------
IF | A HA kifejezés mindig a szabály első utasításának része. Az összes többi feltételes kifejezéshez hasonlóan ezt az IF utasítást is egyegyezéshez kell társmeccsre társcsinálni. Ha nincs megadva további feltételes kifejezés, ez az egyezés határozza meg azt a feltételt, amelynek teljesülnie kell ahhoz, hogy egy szolgáltatáskészletet egy kérelemre alkalmazzon.
ÉS HA | Az ÉS HA kifejezés csak a következő típusú feltételes kifejezések után adható hozzá:IF,ÉS HA. Azt jelzi, hogy van egy másik feltétel, amelyet teljesíteni kell az első IF utasításhoz.
ELLENKEZŐ| Az ELSE IF kifejezés egy alternatív feltételt határoz meg, amelynek teljesülnie kell, mielőtt az else if utasításra jellemző szolgáltatások készlete megtörténik. Az ELSE IF utasítás megléte az előző utasítás végét jelzi. Az else IF utasítás után csak egy másik ELSE IF utasítás helyezhető el feltételes kifejezés. Ez azt jelenti, hogy az ELSE IF utasítás csak egyetlen további feltétel megadására használható, amelynek teljesülnie kell.

**Példa** ![: CDN egyezési feltétel](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Egy későbbi szabály felülbírálhatja az előző szabály által meghatározott műveleteket.
   > Példa: A catch-all szabály biztosítja az összes kérelmet jogkivonat-alapú hitelesítéssel. Egy másik szabály közvetlenül alatta is létrehozható, hogy bizonyos típusú kérelmek esetében kivételt tegyen.

## <a name="next-steps"></a>További lépések

- [Az Azure CDN – áttekintés](cdn-overview.md)
- [Szabályok motorjának hivatkozása](cdn-verizon-premium-rules-engine-reference.md)
- [A szabálymotor megfelel a feltételeknek](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Szabályok motor jellemzői](cdn-verizon-premium-rules-engine-reference-features.md)
- [Az alapértelmezett HTTP-viselkedés felülbírálása a szabálymotor használatával](cdn-verizon-premium-rules-engine.md)