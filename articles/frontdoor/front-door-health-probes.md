---
title: Az Azure bejárati ajtajának Service - háttérrendszer szolgáltatásállapot-figyelést |} A Microsoft Docs
description: Ez a cikk segít megérteni, hogyan Azure bejárati ajtajának szolgáltatás állapotát követi figyelemmel a háttérkiszolgálókon
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
ms.openlocfilehash: 59a3bac39437b91eeee3b005bd23476a34a308b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736581"
---
# <a name="health-probes"></a>Állapotminták

Annak érdekében, hogy meghatározni az egyes háttérrendszerek, bejárati ajtajának környezeteket rendszeres időközönként szintetikus HTTP/HTTPS kérelmet küld az egyes a konfigurált háttérrendszerekre. A Front Door ezután a mintavételek válaszai alapján határozza meg, hogy melyek a „legjobb” háttérrendszerek, amelyekre továbbítania kell a valódi ügyfélkéréseket.


## <a name="supported-protocols"></a>Támogatott protokollok

Bejárati ajtajának küldő mintavételek támogatja a HTTP vagy HTTPS protokollokon keresztül. A rendszer a mintavételeket ugyanazokon az ügyfélkérelmek átirányítására konfigurált, nem felülbírálható TCP-portokon keresztül küldi el.

## <a name="health-probe-responses"></a>Állapot-mintavételi válaszok

| Válaszok  | Leírás | 
| ------------- | ------------- |
| Állapot meghatározása  |  Egy 200 OK állapotkód: azt jelzi, hogy a háttérrendszer állapota kifogástalan. Minden más sikertelennek van tekintve. Ha bármilyen okból (beleértve a hálózati hiba) egy érvényes HTTP-válasz nem érkezik meg a mintavétel, a mintavételi hiba fog számítani.|
| Mérési késés  | Késés az az időpont-alapú idő, attól a pillanattól kezdve azonnal, mielőtt a pillanatban, amikor a válasz utolsó bájtját adatigénylési küldjük az állapotminta iránti kérelem mérni. Használjuk a új TCP-kapcsolat az egyes kérések, ez a mérték nem torzítatlan meglévő meleg kapcsolatok-alapú háttérkomponensek felé.  |

## <a name="how-front-door-determines-backend-health"></a>Hogyan határozza meg a bejárati ajtó a háttérkiszolgáló állapota

Az Azure bejárati ajtajának szolgáltatás alábbi háromlépcsős eljárást minden algoritmusok különböző állapotának meghatározására használja.

1. Letiltott háttérrendszerek kizárása.

2. Állapot-mintavételei hibákat tartalmazó háttérrendszerek kizárása:
    * Ezzel a választással végzi el az utolsó megtekintett _n_ állapot-mintavételi válaszok. Ha legalább _x_ kifogástalan állapotú, számít, hogy a háttérrendszer kifogástalan állapotú.

    * _n_ van konfigurálva, a terheléselosztási beállításokat SampleSize tulajdonság módosításával.

    * _x_ van konfigurálva, a terheléselosztási beállításokat SuccessfulSamplesRequired tulajdonság módosításával.

3. A háttérkészlet megfelelő háttérrendszereket készletét bejárati ajtajának emellett méri és fenntartja az egyes háttérrendszerek várakozási ideje (üzenetváltási idő).


## <a name="complete-health-probe-failure"></a>Kész állapot-mintavételi hibája

Ha állapotmintákat minden háttérkészlet háttérrendszer is sikertelen, bejárati ajtajának úgy ítéli meg, az összes háttérrendszerek kifogástalan állapotú, és mindent átfogóan irányítja a forgalmat egy Ciklikus időszeleteléses elosztását a.

Ha bármely háttérrendszerből ismét Kifogástalan állapotba, bejárati ajtajának folytatódik a normál terheléselosztási algoritmust.

## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
