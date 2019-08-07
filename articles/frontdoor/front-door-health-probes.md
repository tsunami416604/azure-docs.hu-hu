---
title: Azure-beli bejárati szolgáltatás – háttérbeli állapot monitorozása | Microsoft Docs
description: Ez a cikk segít megérteni, hogy az Azure bejárati ajtó szolgáltatás hogyan figyeli a háttérrendszer állapotát
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
ms.openlocfilehash: 289b05a2c50a2b4af50eb2114515a49bb653cf1a
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742392"
---
# <a name="health-probes"></a>Állapotadat-mintavételek

Az egyes háttérrendszer állapotának meghatározásához minden egyes bejárati környezet időnként egy szintetikus HTTP/HTTPS-kérést küld minden konfigurált háttérrendszer számára. A Front Door ezután a mintavételek válaszai alapján határozza meg, hogy melyek a „legjobb” háttérrendszerek, amelyekre továbbítania kell a valódi ügyfélkéréseket. Vegye figyelembe, hogy mivel a bejárati ajtónak számos peremhálózati környezete van, globálisan, az állapot-mintavételi kérelmek kötete a háttérrendszer számára is magas lehet, mivel a másodpercenkénti kérelmek száma a beállított állapottól függ. 



## <a name="supported-protocols"></a>Támogatott protokollok

A bejárati ajtó támogatja a mintavételek HTTP-vagy HTTPS-protokollon keresztüli küldését. A rendszer a mintavételeket ugyanazokon az ügyfélkérelmek átirányítására konfigurált, nem felülbírálható TCP-portokon keresztül küldi el.

## <a name="health-probe-responses"></a>Állapot-mintavételi válaszok

| Responses  | Leírás | 
| ------------- | ------------- |
| Állapot meghatározása  |  A 200 OK állapotkód azt jelzi, hogy a háttér állapota Kifogástalan. Minden más hibát jelez. Ha bármilyen okból (a hálózati meghibásodást is beleértve) nem érkezik érvényes HTTP-válasz a mintavételhez, a mintavétel meghibásodásnak számít.|
| Mérési késés  | A késés az az idő, amelyet a rendszer közvetlenül a mintavételi kérelem elküldése előtt mért, amikor a válasz utolsó bájtját megkaptuk. Minden kérelemhez új TCP-kapcsolatot használunk, így ez a mérték nem torzítja a meglévő, meleg kapcsolatokkal rendelkező háttérrendszer irányába.  |

## <a name="how-front-door-determines-backend-health"></a>A háttérrendszer állapotának meghatározása

Az Azure bejárati szolgáltatása ugyanazt a három lépésből álló folyamatot használja az összes algoritmusban az állapot meghatározásához.

1. A letiltott háttérrendszer kizárása.

2. Állapot-mintavételi hibákkal rendelkező hátterek kizárása:
    * Ezt a kijelölést az utolsó _n_ Health mintavételi válasz alapján kell megtekinteni. Ha legalább _x_ állapota Kifogástalan, a rendszer kifogástalannak tekinti a hátteret.

    * _n_ úgy van konfigurálva, hogy megváltoztatja a SampleSize tulajdonságot a terheléselosztási beállításokban.

    * az _x_ a terheléselosztási beállítások SuccessfulSamplesRequired tulajdonságának módosításával állítható be.

3. A háttér-készletben lévő, kifogástalan állapotú háttérrendszer mellett a bejárati ajtó is méri a mértékeket, és fenntartja az egyes háttérrendszer késését (az oda-és visszaút időpontját).


## <a name="complete-health-probe-failure"></a>Állapot-mintavételi hiba befejezése

Ha az állapot-mintavétel egy háttér-készlet minden egyes munkafelületén meghiúsul, akkor a bevezető ajtó az összes háttérrendszer állapotát figyelembe veszi, és egy ciklikus multiplexelés eloszlásban irányítja át a forgalmat.

Ha bármelyik háttér Kifogástalan állapotba kerül, a bejárati ajtó folytatja a normál terheléselosztási algoritmust.

## <a name="next-steps"></a>További lépések

- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
