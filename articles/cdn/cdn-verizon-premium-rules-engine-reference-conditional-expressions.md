---
title: A Verizon prémium szintű Azure CDN-szabályok szabálymotor feltételes kifejezései |} A Microsoft Docs
description: A Verizon prémium szintű Azure CDN dokumentációja szabályok szabálymotor egyezési feltételei és funkciókat.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: f790e37ae876c0640d55ebfb51abb43c6a705f04
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593222"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Az Azure CDN a Verizontól prémium szabályok szabálymotor feltételes kifejezései

Ez a cikk felsorolja az Azure Content Delivery Network (CDN), a feltételes kifejezések részletes leírását [Szabálymotorral](cdn-verizon-premium-rules-engine.md).

A szabály első része a feltételes kifejezéssel.

Feltételes kifejezéssel | Leírás
-----------------------|-------------
HA | Egy IF kifejezés mindig egy szabályban utasításnak elsőként egy részét képezi. Mint minden más feltételes kifejezés IF nyilatkozattal egyezést társítva kell lennie. Ha nincsenek további feltételes kifejezéseket vannak megadva, ez határozza meg azokat a feltételt, amely a szolgáltatások alkalmazandó kérelem előtt teljesülniük kell.
HA | ÉS a egy IF kifejezés csak a feltételes kifejezések: Ha, és ha a következő típusú után adható hozzá. Azt jelzi, hogy nincs-e egy másik feltétel, amelyeknek teljesülniük kell a kezdeti IF utasítás.
VAGY HA| Egy vagy ha egy másik feltétel, amelyeknek teljesülniük kell, vagy ha nyilatkozattal adott szolgáltatások előtt adja meg. VAGY ha utasítás jelzi az előző utasítást végén. Csak a feltételes kifejezés, amely lehet helyezni, vagy ha egy másik utasítás egy más IF utasítás után. Ez azt jelenti, hogy egy vagy ha utasítás csak akkor használható, amely rendelkezik, amelyeknek teljesülniük kell egyetlen további feltétel megadásához.

**Példa**: ![CDN egyezési feltételei](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > A következő szabály felülbírálhatja az előző szabályok által meghatározott műveleteket.
   > Példa: Egy kevésbé szabály összes kérelem keresztül jogkivonat-alapú hitelesítés védi. Egy másik szabály közvetlenül alá kivételt az egyes kérések hozható létre.

## <a name="next-steps"></a>További lépések

- [Az Azure CDN áttekintése](cdn-overview.md)
- [Szabálymotor-referencia](cdn-verizon-premium-rules-engine-reference.md)
- [Szabálymotor egyezési feltételei](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Szabálymotor funkciói](cdn-verizon-premium-rules-engine-reference-features.md)
- [A rules engine használatával a HTTP alapértelmezés felülbírálása](cdn-verizon-premium-rules-engine.md)