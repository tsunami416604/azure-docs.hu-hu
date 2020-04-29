---
title: A Azure CDN-Verizon Premium Rules motor feltételes kifejezései
description: A Verizon Premium Rules Engine Azure CDN a feltételekkel és szolgáltatásokkal kapcsolatos dokumentációja.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 25b3c1a011e39adea651a6daa1d4ab9aa3a9d186
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253509"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Azure CDN a Verizon Premium Rules Engine feltételes kifejezésekkel

Ez a cikk az Azure Content Delivery Network (CDN) [szabályok motorjának](cdn-verizon-premium-rules-engine.md)feltételes kifejezéseit ismerteti.

A szabály első része a feltételes kifejezés.

Feltételes kifejezés | Leírás
-----------------------|-------------
IF | Az IF kifejezés mindig a szabály első utasításának részét képezi. Az összes többi feltételes kifejezéshez hasonlóan ez az IF utasításnak egyezéssel kell társítania. Ha nincs megadva további feltételes kifejezés, akkor ez a megfeleltetés határozza meg azt a feltételt, amelynek teljesülnie kell ahhoz, hogy a szolgáltatások egy csoportját alkalmazni lehessen egy kérelemre.
ÉS HA | A és IF kifejezés csak a következő típusú feltételes kifejezések után vehető fel: IF, és IF. Azt jelzi, hogy egy másik feltételnek kell teljesülnie a kezdeti IF utasításhoz.
MÁS, HA| MÁS, ha a kifejezés olyan alternatív feltételt határoz meg, amelynek teljesülnie kell ahhoz, hogy az ehhez a MÁSIKhoz tartozó szolgáltatások készlete megtörténjen. Ha az utasítás az előző utasítás végét jelöli, akkor más, mint a. Az egyetlen feltételes kifejezés, amely akkor helyezhető el, ha egy másik, ha az utasítás más más, mint a utasítás. Ez azt jelenti, hogy egy másik, ha az utasítás csak egyetlen további feltételt tartalmazhat, amelynek teljesülnie kell.

**Példa**: ![CDN Match feltétel](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Egy későbbi szabály felülbírálhatja egy korábbi szabály által megadott műveleteket.
   > Példa: A catch-all szabály A jogkivonat-alapú hitelesítésen keresztül minden kérést biztosít. Egy másik szabály is létrehozható közvetlenül az alatt, hogy bizonyos típusú kérelmek esetében kivételt hozzon létre.

## <a name="next-steps"></a>További lépések

- [Azure CDN áttekintése](cdn-overview.md)
- [Szabályok motor referenciája](cdn-verizon-premium-rules-engine-reference.md)
- [Szabályok motorjának egyeztetési feltételei](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [A szabályok motorjának funkciói](cdn-verizon-premium-rules-engine-reference-features.md)
- [Az alapértelmezett HTTP-viselkedés felülbírálása a szabályok motor használatával](cdn-verizon-premium-rules-engine.md)