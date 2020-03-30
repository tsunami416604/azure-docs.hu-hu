---
title: Azure Bejárati ajtó - háttérállapot-figyelés | Microsoft dokumentumok
description: Ez a cikk segít megérteni, hogy az Azure Front Door hogyan figyeli a háttérrendszerek állapotát
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471574"
---
# <a name="health-probes"></a>Állapotminták

Annak érdekében, hogy egy adott bejárati ajtó környezetből az egyes háttérrendszerek állapotát és közelségét határozza meg, minden bejárati ajtó környezet rendszeres időközönként szintetikus HTTP/HTTPS-kérelmet küld a konfigurált háttérrendszerek mindegyikének. A Front Door ezután a mintavételek válaszai alapján határozza meg, hogy melyek a „legjobb” háttérrendszerek, amelyekre továbbítania kell a valódi ügyfélkéréseket. 

> [!WARNING]
> Mivel a Bejárati ajtó világszerte számos peremhálózati környezettel rendelkezik, az állapotminta-kérelmek mennyisége a háttérrendszerekhez meglehetősen magas lehet – a percenkénti 25 kérelemtől a percenkénti 1200 kérelemig, a konfigurált állapotmintavételi gyakoriságtól függően. Az alapértelmezett mintavételi gyakoriság 30 másodperc, a mintavételi kötet a háttérrendszer körülbelül 200 kérelmek percenként.

## <a name="supported-protocols"></a>Támogatott protokollok

A Bejárati ajtó támogatja a mintavételek HTTP- vagy HTTPS protokollon keresztüli küldését. A rendszer a mintavételeket ugyanazokon az ügyfélkérelmek átirányítására konfigurált, nem felülbírálható TCP-portokon keresztül küldi el.

## <a name="supported-http-methods-for-health-probes"></a>Támogatott HTTP-módszerek az állapotmintásokhoz

A Bejárati ajtó a következő HTTP-módszereket támogatja az állapotmintaküldéshez:

1. **GET:** A GET metódus azt jelenti, hogy a Kérés-URI bármilyen információt lekér (entitás formájában).
2. **Fej:** A HEAD metódus megegyezik a GET-vel, azzal a kivételével, hogy a kiszolgáló nem ad vissza üzenettörzset a válaszban. Az új bejárati ajtó profilok esetében a mintavételi módszer alapértelmezés szerint HEAD-ként van beállítva.

> [!NOTE]
> Az alacsonyabb terhelés és a költségek a háttérrendszereken, bejárati ajtó azt javasolja, hogy head kérelmek et az egészségügyi szondák.

## <a name="health-probe-responses"></a>Egészségügyi mintavételi válaszok

| Válaszok  | Leírás | 
| ------------- | ------------- |
| Az egészség meghatározása  |  A 200 OK állapotkód azt jelzi, hogy a háttérterület kifogástalan állapotú. Minden más kudarcnak számít. Ha bármilyen okból (beleértve a hálózati hiba) érvényes HTTP-válasz nem érkezik meg a mintavételhez, a mintavétel hibaként számít.|
| Késés mérése  | A késleltetés a falióra idő, amelyet közvetlenül a szondakérés elküldése előtti pillanattól kezdve mérnek addig a pillanatig, amikor megkapjuk a válasz utolsó bájtot. Minden kéréshez új TCP-kapcsolatot használunk, így ez a mérés nem elfogult a meglévő meleg kapcsolatokkal rendelkező háttérrendszerek felé.  |

## <a name="how-front-door-determines-backend-health"></a>Hogyan határozza meg a Front Door a háttér-állapotot?

Az Azure Bejárati ajtaját használja ugyanazt a három lépéses folyamat ot az összes algoritmus állapotának meghatározásához.

1. A letiltott háttérrendszerek kizárása.

2. Az állapotmintahibákkal kapcsolatos háttérrendszerek kizárása:
    * Ez a kiválasztás az utolsó _n_ állapotminta-válaszok vizsgálatával történik. Ha legalább _x_ kifogástalan, a háttér-adék kifogástalannak minősül.

    * _n_ a SampleSize tulajdonság terheléselosztási beállításokban való módosításával van konfigurálva.

    * _X_ a SuccessfulSamplesRequired tulajdonság módosításával van konfigurálva a terheléselosztási beállításokban.

3. A háttérkészletből a bejárati ajtó emellett méri és fenntartja az egyes háttérrendszerek késését (oda-vissza idő).


## <a name="complete-health-probe-failure"></a>Teljes állapotminta hiba

Ha az állapotpróbák sikertelenek a háttérkészlet minden háttérrendszeréhez, majd a Bejárati ajtó az összes háttérrendszer kifogástalannak tekinti a forgalmat, és a forgalmat egy ciklikus multiplexelés-eloszlásban irányítja mindegyikben.

Ha bármelyik háttér-háttéradat kifogástalan állapotba kerül, majd a Bejárati ajtó folytatja a normál terheléselosztási algoritmust.

## <a name="disabling-health-probes"></a>Az állapotvizsgálók letiltása

Ha egyetlen háttér-tartaléka van a háttérkészletben, dönthet úgy, hogy letiltja az állapot-mintavételek csökkenti a terhelést az alkalmazás háttér-. Még akkor is, ha több háttérprogram a háttérkészletben, de csak egyikük engedélyezett állapotban van, letilthatja az állapotminta.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
