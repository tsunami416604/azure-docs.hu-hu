---
title: A Standard Rules Engine referenciája Azure CDN | Microsoft Docs
description: Dokumentáció az Azure Content Delivery Network (Azure CDN) Standard Rules Engine-beli feltételeinek és műveleteinek egyeztetéséhez.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: cae0ec1daed8277d1e7e4f3c5cbc701965ac00b8
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661750"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>A Standard szabálymotor referenciája az Azure CDN-hez

Az Azure Content Delivery Network (Azure CDN) [szabványos szabályok motorjában](cdn-standard-rules-engine.md) a szabály egy vagy több egyeztetési feltételből és egy műveletből áll. Ez a cikk részletesen ismerteti a Azure CDN szabványos szabályok motorjában elérhető egyezési feltételeket és szolgáltatásokat.

A szabályok motor úgy lett kialakítva, hogy az adott típusú kérések standard Azure CDN általi feldolgozásának végső hatósága legyen.

**A szabályok gyakori felhasználási módjai**:

- Egyéni gyorsítótár-szabályzat felülbírálása vagy definiálása.
- Átirányítási kérelmek.
- A HTTP-kérés és a válasz fejlécének módosítása.

## <a name="terminology"></a>Terminológia

Ha szabályt szeretne definiálni a szabályok motorban, állítsa be a [megfelelő feltételeket](cdn-standard-rules-engine-match-conditions.md) és [műveleteket](cdn-standard-rules-engine-actions.md):

 ![Azure CDN szabályok szerkezete](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Mindegyik szabály legfeljebb tíz egyeztetési feltételt és öt műveletet tartalmazhat. Minden Azure CDN végpont legfeljebb 25 szabályt tartalmazhat. 

Ebben a korlátban egy alapértelmezett *globális szabály*szerepel. A globális szabály nem rendelkezik egyező feltételekkel; a globális szabályokban definiált műveletek mindig aktiválva lesznek.

## <a name="limits-and-pricing"></a>Korlátozások és díjszabás 

Minden Azure CDN végpont legfeljebb 25 szabályt tartalmazhat. Mindegyik szabály legfeljebb tíz egyeztetési feltételt és öt műveletet tartalmazhat. A Rules Engine díjszabása az alábbi dimenziókat követi: 
- Szabályok: $1/szabály/hó 
- Feldolgozott kérelmek: $0,60/millió requets
- Az első 5 szabály továbbra is ingyenes marad

## <a name="syntax"></a>Syntax

A speciális karakterek kezelése egy szabályban attól függően változik, hogy a különböző feltételek és műveletek milyen módon kezelik a szöveges értékeket. Az egyeztetési feltételnek vagy műveletnek a következő módszerek egyikével lehet szöveget értelmezni:

- [Literális értékek](#literal-values)
- [Helyettesítő karakteres értékek](#wildcard-values)


### <a name="literal-values"></a>Literális értékek

A literál értékként értelmezett szöveg az összes speciális karaktert kezeli a *(z)% szimbólum kivételével* az érték részeként, amelynek meg kell egyeznie egy szabályban. Például egy konstans egyezési feltétel `'*'` csak akkor teljesül, ha a pontos érték található `'*'` .

A százalékos aláírás az URL-kódolás (például:) jelzésére szolgál `%20` .

### <a name="wildcard-values"></a>Helyettesítő karakteres értékek

Jelenleg a standard szintű szabályok motorjában a **UrlPath Matching feltételben** szereplő helyettesítő karaktert támogatjuk. A \* karakter egy helyettesítő karakter, amely egy vagy több karaktert jelöl. 

## <a name="next-steps"></a>Következő lépések

- [Egyeztetési feltételek a standard szabályok motorban](cdn-standard-rules-engine-match-conditions.md)
- [Műveletek a standard szabályok motorban](cdn-standard-rules-engine-actions.md)
- [HTTPS kényszerítése a Standard szabálymotor használatával](cdn-standard-rules-engine.md)
- [Azure CDN áttekintése](cdn-overview.md)
