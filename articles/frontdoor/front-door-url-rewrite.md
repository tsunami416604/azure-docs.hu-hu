---
title: Azure bejárati ajtó szolgáltatás - URL-Újraírási |} A Microsoft Docs
description: Ez a cikk segít megérteni, hogyan Azure bejárati ajtajának szolgáltatásnak nincs URL-újraíró az útvonalak, ha konfigurálva.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: dc2126276e3e8e0d35ce8ed1f835544386659eff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736181"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL-átírás (egyéni továbbítási útvonal)
Azure bejárati ajtajának szolgáltatás URL-újraírási támogatja azáltal, hogy egy nem kötelező konfigurálása **egyéni, továbbító elérési útjának** létrehozásakor a kérelem használatával továbbítsa a háttérrendszerhez. Ha nincs megadva egyéni továbbítási útvonal, a Front Door alapértelmezés szerint bemásolja a bejövő URL-útvonalat a továbbított kérelemben használt URL-címbe. A továbbított kérelemben használt állomásfejléc megegyezik a kiválasztott háttérrendszer számára konfigurált állomásfejléccel. Olvasási [háttérrendszer állomásfejléc](front-door-backend-pool.md#hostheader) művelet, és hogyan konfigurálhatja azt.

A hatékony URL-címet módosítsa úgy az egyéni, továbbító elérési út része, hogy, másolja a bejövő elérési útja, amely megfelel a helyettesítő karaktert tartalmazó elérési útját a továbbított elérési út bármely része (ezek elérési út szegmens a **zöld** szegmensek az alábbi példában):
</br>
![Az Azure bejárati ajtajának URL-átírás][1]

## <a name="url-rewrite-example"></a>URL rewrite példa
Fontolja meg egy útválasztási szabályt a következő előtér-gazdagépek és a konfigurált elérési utak:

| Hosts      | Elérési utak       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/sáv /\* |

Az első oszlop a tábla az alábbi példát láthat a bejövő kérelmeket, és második oszlopa jeleníti meg, hogy mi lenne a "legtöbb-specifikus" egyező útvonal "Elérési út".  A harmadik és az azt követő oszlopok a tábla első sorának példák a konfigurált **egyéni, továbbító elérési utak**, ezek az oszlopok jelölő, hogy mi a továbbított kérelem elérési útját lenne ha egyezést mutat példát a sorok a rest-tel a kérésre abban a sorban.

Például, hogy olvassa el a második sorban, ha azt van arról, hogy a bejövő kérelem `www.contoso.com/sub`, ha az egyéni, továbbító elérési út `/`, majd a továbbított útvonal `/sub`. Ha az egyéni, továbbító elérési út `/fwd/`, majd a továbbított útvonal `/fwd/sub`. És így tovább, a többi oszlop számára. A **kiemelését** részek az elérési utak jelölik azokat a részeit, amelyek részei a helyettesítő karaktert tartalmazó találatra.


| Bejövő kérelem       | A legtöbb-specifikus egyezés elérési útja | /          | /fwd/          | /foo/          | /foo/sáv /          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/sáv /          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/sáv /          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/sáv /          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |


## <a name="optional-settings"></a>Nem kötelező beállítások
Nincsenek további nem kötelező beállításokat is megadhat az adott útválasztási szabály beállítások:

* **Konfigurációs gyorsítótár** – Ha le van tiltva, vagy nincs megadva, majd a kérelmek, amelyek megfelelnek a útválasztási szabály nem próbálja meg használni a gyorsítótárazott tartalmat, és ehelyett mindig beolvassa a háttérbeli. Tudjon meg többet [bejárati ajtajának gyorsítótárazást](front-door-caching.md).



## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg