---
title: Általános szabályok motorreferencia az Azure CDN - CDN | Microsoft dokumentumok
description: Az Azure Content Delivery Network (Azure CDN) szabványos szabályok motorjában az egyezési feltételekés műveletek referenciadokumentációja.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 6d4fa4451c3db3d6f2a506eabd5676d18b0219f4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259901"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>A Standard szabálymotor referenciája az Azure CDN-hez

Az Azure Content Delivery Network (Azure CDN) [standard szabályok motorjában](cdn-standard-rules-engine.md) egy szabály egy vagy több egyezési feltételből és egy műveletből áll. Ez a cikk részletes leírást ad az egyezési feltételekről és az Azure CDN standard szabálymotorjában elérhető funkciókról.

A szabálymotor célja, hogy a standard Azure CDN által feldolgozott adott típusú kérelmek végső jogosultságát.

**A szabályok gyakori felhasználási ideje:**

- Egyéni gyorsítótár-házirend felülbírálása vagy definiálása.
- Kérések átirányítása.
- Http-kérelem- és válaszfejlécek módosítása.

## <a name="terminology"></a>Terminológia

Ha szabálydefiniálása a szabálymotorban, állítsa be a [feltételeket](cdn-standard-rules-engine-match-conditions.md) és [műveleteket:](cdn-standard-rules-engine-actions.md)

 ![Az Azure CDN-szabályok struktúrája](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Minden szabálylegfeljebb négy egyezési feltételt és három műveletet használhat. Minden Egyes Azure CDN-végpont legfeljebb öt szabályt rendelkezhet. 

Az Azure CDN-végpontra vonatkozó jelenlegi ötszabályos korlát ban szerepel az alapértelmezett *globális szabály.* A globális szabály nem rendelkezik egyezési feltételekkel, és a globális szabályban definiált műveletek mindig aktiválódnak.

## <a name="syntax"></a>Szintaxis

A speciális karakterek szabályban való kezelésének módja attól függ, hogy a különböző egyezési feltételek és műveletek hogyan kezelik a szöveges értékeket. Az egyezési feltétel vagy művelet a következő módokon értelmezheti a szöveget:

- [Literális értékek](#literal-values)
- [Helyettesítő karakteres értékek](#wildcard-values)


### <a name="literal-values"></a>Literális értékek

A literális értékként értelmezett szöveg a *% szimbólum kivételével* minden speciális karaktert a szabályban egyeztetendő érték részeként kezel. Például egy literális egyezési `'*'` feltétel beállítása `'*'` csak akkor teljesül, ha a pontos érték található.

Az URL-kódolás (például `%20`) százalékos jellel jelzi az URL-kódolást.

### <a name="wildcard-values"></a>Helyettesítő karakteres értékek

A helyettesítő karakterként értelmezett szöveg további értelmet rendel a speciális karakterekhez. Az alábbi táblázat azt ismerteti, hogy a szabványos szabályok motorjában hogyan értelmeznek bizonyos speciális karaktereket:

Karakter | Leírás
----------|------------
\ | A fordított perjel a táblázatban megadott karakterek bármelyikének kikerülésére szolgál. A megkerülendő speciális karakter előtt közvetlenül meg kell adni egy fordított perjelet. A következő szintaxis például csillagot választ ki:`\*`
% | Az URL-kódolás (például `%20`) százalékos jellel jelzi az URL-kódolást.
\* | A csillag egy helyettesítő karakter, amely egy vagy több karaktert jelöl.
Hely | A szóköz karakter azt jelzi, hogy egy egyezési feltétel a megadott értékek vagy minták bármelyikével teljesíthető.
aknajelek | Az aposztrófnak nincs különleges jelentése. Az aposztrófok halmaza azonban azt jelzi, hogy egy értéket literális értékként kell kezelni. Az idézőjelek a következő módokon használhatók:<ul><li>Annak engedélyezése, hogy egy egyezési feltétel teljesüljen, ha a megadott érték megegyezik az összehasonlítási érték bármely részével.  Például `'ma'` az alábbi karakterláncok bármelyikének megfelelne: <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template. **ma**p</li></ul><li>Egy speciális karakter konstans karakterként való megadása. Megadhatja például a szó szerinti szóköz karaktert úgy, hogy egy`' '` `'<sample value>'`szóközt aknaidézőjelek ( vagy ) halmazába foglal.</li><li>Üres érték megadása. Adjon meg egy üres értéket az aposztrófok (**''**) készletének megadásával.</li></ul>**Fontos**:<br /><ul><li>Ha a megadott érték nem tartalmaz helyettesítő karaktert, az érték automatikusan literális értéknek minősül. Nem kell aknaidézőjelek készletét megadnia egy literális értékhez.</li><li>Ha a tábla egy másik karakterét nem használ fordított perjellel, a rendszer figyelmen kívül hagyja a fordított perjelet, ha egyszeres idézőjelek közé van állítva.</li><li>Egy másik módja annak, hogy egy speciális karaktert konstans karakterként adjon meg, az, ha fordított perjellel ( ).`\`</li></ul>

## <a name="next-steps"></a>További lépések

- [Feltételek egyeztetése a Standard rules motorban](cdn-standard-rules-engine-match-conditions.md)
- [Műveletek a Standard szabálymotorban](cdn-standard-rules-engine-actions.md)
- [HTTPS kényszerítése a Standard szabálymotor használatával](cdn-standard-rules-engine.md)
- [Az Azure CDN – áttekintés](cdn-overview.md)
