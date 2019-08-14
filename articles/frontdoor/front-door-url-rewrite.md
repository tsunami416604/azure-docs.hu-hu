---
title: Azure bejárati ajtó szolgáltatás – URL-cím újraírása | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogy az Azure bejárati ajtó szolgáltatás hogyan írja le az útvonalak URL-címét, ha vannak ilyenek.
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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "60736181"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL-újraírás (egyéni továbbítási útvonal)
Az Azure bevezető ajtaja támogatja az URL-cím újraírását, mivel lehetővé teszi egy opcionális **Egyéni továbbítási útvonal** konfigurálását, amelyet a rendszer a háttérbe való továbbításra irányuló kérés összeállításakor használ. Ha nincs megadva egyéni továbbítási útvonal, a Front Door alapértelmezés szerint bemásolja a bejövő URL-útvonalat a továbbított kérelemben használt URL-címbe. A továbbított kérelemben használt állomásfejléc megegyezik a kiválasztott háttérrendszer számára konfigurált állomásfejléccel. Olvassa el a [háttérbeli állomásfejléc](front-door-backend-pool.md#hostheader) -fejlécet, hogy megtudja, mit csinál, és hogyan konfigurálhatja.

Az URL-cím az egyéni továbbítási útvonal használatával történő újraírásának nagy része az, hogy a bejövő elérési út bármely olyan részét átmásolja, amely megegyezik a továbbított útvonalhoz tartozó helyettesítő karakteres elérési úttal (ezek az elérésiút-szegmensek az alábbi példában szereplő **zöld** szegmensek):
</br>
![Azure bejárati ajtó URL-címének újraírása][1]

## <a name="url-rewrite-example"></a>URL-átírási példa
Vegye fontolóra egy útválasztási szabályt a következő előtér-gazdagépekkel és elérési utakkal:

| Gazdagépek      | Elérési utak       |
|------------|-------------|
| www\.contoso.com | /\*         |
|            | /foo        |
|            | foo\*     |
|            | /foo/bar/\* |

Az alábbi táblázat első oszlopa a beérkező kérelmekre mutat példákat, a második oszlop pedig azt mutatja be, hogy mi lenne a "legpontosabban egyező" útvonal.  A táblázat első sorában a harmadik és az azt követő oszlopok a konfigurált **Egyéni továbbítási útvonalakra**mutatnak, az oszlopok többi sora pedig példát mutat be arra, hogy a továbbított kérések elérési útja hogyan illeszkedik az adott kérelemhez. sor.

Ha például beolvasjuk a második sort, azt mondja, hogy a bejövő kérelem `www.contoso.com/sub`esetében, ha az egyéni továbbítási útvonal volt `/`, akkor a továbbított elérési út a következő lesz `/sub`:. Ha az egyéni továbbítási útvonal `/fwd/`volt, akkor a továbbított elérési út `/fwd/sub`a következő lesz:. És így tovább, a fennmaradó oszlopokhoz. Az alábbi elérési utak **Kiemelt** részei a helyettesítő karakteres egyezés részét képező részeket jelölik.


| Bejövő kérelem       | A legpontosabb egyezési elérési út | /          | /fwd/          | foo          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | foo          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**Sub**   | /fwd/**sub**   | /foo/**Sub**   | /foo/Bar/**Sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/Bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | foo          | /foo/bar/          |
| www\.contoso.com/foo/        | foo\*                  | /          | /fwd/          | foo          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | foo\*                  | /**Bár**   | /fwd/**bar**   | /foo/**sáv**   | /foo/Bar/**sáv**   |


## <a name="optional-settings"></a>Nem kötelező beállítások
További választható beállítások is megadhatók a megadott útválasztási szabályok beállításaihoz:

* **Gyorsítótár-konfiguráció** – ha le van tiltva vagy nincs megadva, akkor az ehhez az útválasztási szabályhoz illeszkedő kérelmek nem kísérlik meg a gyorsítótárazott tartalom használatát, hanem mindig a háttérből fognak beolvasni. További információ a [bejárati ajtók gyorsítótárazásáról](front-door-caching.md).



## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.

<!--Image references-->
[1]: ./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg