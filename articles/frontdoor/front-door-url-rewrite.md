---
title: Azure bejárati ajtó - URL újraírása | Microsoft dokumentumok
description: Ez a cikk segít megérteni, hogy az Azure Bejárati ajtó hogyan írja át az url-eket az útvonalakhoz, ha konfigurálva van.
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
ms.openlocfilehash: 1e5bd565be7a1cabf08ddf33c65eb12b5294249f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471472"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL-újraírás (egyéni továbbítási útvonal)
Az Azure Front Door támogatja az URL-újraírást azáltal, hogy lehetővé teszi egy opcionális **egyéni továbbítási útvonal** konfigurálását a háttérrendszernek való továbbításra vonatkozó kérés összeállításakor. Ha nincs megadva egyéni továbbítási útvonal, a Front Door alapértelmezés szerint bemásolja a bejövő URL-útvonalat a továbbított kérelemben használt URL-címbe. A továbbított kérelemben használt állomásfejléc megegyezik a kiválasztott háttérrendszer számára konfigurált állomásfejléccel. Olvassa el [a háttér-állomásfejlécet,](front-door-backend-pool.md#hostheader) hogy megtudja, mit csinál, és hogyan konfigurálhatja azt.

Az URL-átírás erőteljes része az egyéni továbbítási útvonal használatával az, hogy a bejövő útvonal bármely olyan részét másolja, amely megegyezik a továbbított elérési úthelyettesítő elérési útjával (ezek az elérési útszegmensek az alábbi példában a **zöld** szegmensek):
</br>
![Az Azure bejárati ajtajának URL-címe újraírása][1]

## <a name="url-rewrite-example"></a>Példa URL-újraírására
Fontolja meg a következő előtér-állomásokkal és elérési utakkal rendelkező útválasztási szabályt:

| Hosts      | Elérési utak       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

Az alábbi táblázat első oszlopa példákat mutat be a bejövő kérelmekre, a második pedig azt, hogy mi lenne a "legspecifikusabb" egyező útvonal,' "Path".  A táblázat első sorának harmadik és későbbi oszlopai példák a konfigurált **egyéni továbbítási útvonalakra,** és az oszlopok többi sora példákat mutat be arra, hogy mi lenne a továbbított kérelem elérési útja, ha az megfelelne az adott sorban lévő kérésnek.

Ha például a második sorban olvasunk, azt mondjuk, `www.contoso.com/sub`hogy a bejövő kérésesetén, ha az egyéni továbbítási útvonal volt `/`, akkor a továbbított elérési út lesz. `/sub` Ha az egyéni továbbítási útvonal `/fwd/`a , `/fwd/sub`akkor a továbbított elérési út lesz. És így tovább, a többi oszlop. Az alábbi görbék **kijelölt** részei a helyettesítő karakter egyezés részét képező részeket jelölik.


| Bejövő kérelem       | A legspecifikusabb egyezési útvonala | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**Al**   | /fwd/**al**   | /foo/**al**   | /foo/bar/**al**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bár** | /foo/\*                  | /**Bár**   | /fwd/**sáv**   | /foo/**sáv**   | /foo/bar/**bár**   |


## <a name="optional-settings"></a>Választható beállítások
Az adott útválasztási szabály beállításaihoz további választható beállításokat is megadhat:

* **Gyorsítótár-konfiguráció** – Ha le van tiltva vagy nincs megadva, akkor az útválasztási szabálynak megfelelő kérelmek nem kísérlik meg használni a gyorsítótárazott tartalmat, és ehelyett mindig a háttérrendszerből fognak lekérni. Tudjon meg többet a [Caching a Bejárati ajtó](front-door-caching.md).



## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg