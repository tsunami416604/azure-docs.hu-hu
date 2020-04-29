---
title: A Standard Rules Engine referenciája Azure CDN | Microsoft Docs
description: Dokumentáció az Azure Content Delivery Network (Azure CDN) Standard Rules Engine-beli feltételeinek és műveleteinek egyeztetéséhez.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 6d4fa4451c3db3d6f2a506eabd5676d18b0219f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81259901"
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

Mindegyik szabály legfeljebb négy egyeztetési feltételt és három műveletet tartalmazhat. Minden Azure CDN végpont legfeljebb öt szabályt tartalmazhat. 

Egy Azure CDN végpont aktuális öt szabályának korlátja egy alapértelmezett *globális szabály*. A globális szabálynak nincs megfelelő feltételei, és egy globális szabályban definiált műveletek mindig aktiválva lesznek.

## <a name="syntax"></a>Szintaxis

A speciális karakterek kezelése egy szabályban attól függően változik, hogy a különböző feltételek és műveletek milyen módon kezelik a szöveges értékeket. Az egyeztetési feltételnek vagy műveletnek a következő módszerek egyikével lehet szöveget értelmezni:

- [Literális értékek](#literal-values)
- [Helyettesítő karakteres értékek](#wildcard-values)


### <a name="literal-values"></a>Literális értékek

A literál értékként értelmezett szöveg az összes speciális karaktert kezeli a *(z)% szimbólum kivételével* az érték részeként, amelynek meg kell egyeznie egy szabályban. Például egy konstans egyezési feltétel `'*'` csak akkor teljesül, ha a pontos érték `'*'` található.

A százalékos aláírás az URL-kódolás (például: `%20`) jelzésére szolgál.

### <a name="wildcard-values"></a>Helyettesítő karakteres értékek

A helyettesítő karakterként értelmezett szöveg a speciális karakterek további jelentését rendeli hozzá. A következő táblázat ismerteti, hogyan történik a speciális karakterek értelmezése a szabványos szabályok motorban:

Karakter | Leírás
----------|------------
\ | A program fordított perjelet használ a táblázatban megadott karakterek bármelyikének megmeneküléséhez. Meg kell adni egy fordított perjelet közvetlenül a kikerülő különleges karakter előtt. Az alábbi szintaxis például megmenekül egy csillaggal:`\*`
% | A százalékos aláírás az URL-kódolás (például: `%20`) jelzésére szolgál.
\* | A csillag egy helyettesítő karakter, amely egy vagy több karaktert jelöl.
lemezterület | A szóköz karakter azt jelzi, hogy az egyeztetési feltételt a megadott értékek vagy minták valamelyikével lehet kielégíteni.
szimpla idézőjelek | Egyetlen idézőjel nem rendelkezik speciális jelentéssel. Az egyszeres idézőjelek halmaza azonban azt jelzi, hogy egy értéket literál értékként kell kezelni. Az aposztrófok az alábbi módokon használhatók:<ul><li>Annak engedélyezése, hogy az egyeztetési feltétel teljesüljon, ha a megadott érték megegyezik az összehasonlítási érték bármely részével.  Például `'ma'` a következő sztringek bármelyike megfelel: <ul><li>/Business/**ma**rathon/Asset.htm</li><li>**ma**p. gif</li><li>/business/template. **ma**p</li></ul><li>Egy speciális karakter literál karakterként való megadásának engedélyezése. Megadhat például egy konstans térközt úgy, hogy szóközzel látja el az aposztrófokat (`' '` vagy `'<sample value>'`).</li><li>Üres érték megadásának engedélyezése. Adja meg az üres értéket egy szimpla idézőjelek ("**"**) készletének megadásával.</li></ul>**Fontos**:<br /><ul><li>Ha a megadott érték nem tartalmaz helyettesítő karaktert, az érték automatikusan literál értéknek tekintendő. Nem kell megadnia egyetlen idézőjelek készletét egy literál értékhez.</li><li>Ha egy fordított perjel nem használ másik karaktert a táblázatban, a rendszer figyelmen kívül hagyja a fordított perjelet, ha egyszeres idézőjelek készletében van megadva.</li><li>A speciális karakter literál karakterként való megadásának másik módja egy fordított perjel (`\`) használatával történő Escape-érték.</li></ul>

## <a name="next-steps"></a>További lépések

- [Egyeztetési feltételek a standard szabályok motorban](cdn-standard-rules-engine-match-conditions.md)
- [Műveletek a standard szabályok motorban](cdn-standard-rules-engine-actions.md)
- [HTTPS kényszerítése a Standard szabálymotor használatával](cdn-standard-rules-engine.md)
- [Azure CDN áttekintése](cdn-overview.md)
