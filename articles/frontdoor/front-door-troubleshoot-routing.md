---
title: Az Azure Bejárati ajtajával kapcsolatos problémák elhárítása
description: Ebben az oktatóanyagban megtudhatja, hogyan háríthatja el a bejárati ajtóval kapcsolatos gyakori problémákat.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 962c884eb8adc05e5d50b6b254d5c3f0b18af556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471506"
---
# <a name="troubleshooting-common-routing-issues"></a>Gyakori útválasztási problémák elhárítása

Ez a cikk ismerteti, hogyan hárítsa el az Azure bejárati ajtajának konfigurációjához esetleg előforduló gyakori útválasztási problémákat.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 válasz a Bejárati ajtónéhány másodperc múlva

### <a name="symptom"></a>Hibajelenség

- A háttérrendszernek küldött rendszeres kérések, amelyek nem mennek át a Bejárati ajtón, sikeresek, de a Bejárati ajtón keresztül 503 hibaválaszt eredményez.

- A hiba a Bejárati ajtó mutatja után néhány másodperc (általában körülbelül 30 másodperc után)

### <a name="cause"></a>Ok

Ez a jelenség akkor fordul elő, ha a háttérrendszer túllépi az időtúllépési konfigurációt (az alapértelmezett érték 30 másodperc), hogy megkapja a kérést a Bejárati ajtótól, vagy ha az időtúllépési értéken túl ravandál a bejárati ajtótól érkező kérésre. 

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

- Küldje el a kérelmet a háttér-közvetlenül (anélkül, hogy a bejárati ajtón keresztül), és mi a szokásos időt vesz igénybe a háttérben, hogy válaszoljon.
- Küldje el a kérést a Bejárati ajtón keresztül, és nézze meg, hogy lát-e 503-as válaszokat. Ha nem, akkor ez nem lehet időtúltöltési probléma. Kérjük, forduljon az ügyfélszolgálathoz.
- Ha a Bejárati ajtón keresztül 503-as hibaválasz-kódot eredményez, konfigurálja a sendReceiveTimeout mezőt a bejárati ajtóhoz, hogy az alapértelmezett időtúllépést legfeljebb 4 percre (240 másodpercre) hosszabbítsa meg. A beállítás a `backendPoolSettings` és `sendRecvTimeoutSeconds`a neve alatt van. 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Az egyéni tartományba küldött kérelmek 400 állapotkódot adnak vissza

### <a name="symptom"></a>Hibajelenség

- Létrehozott egy bejárati ajtót, de a tartományvagy előtér-állomás kérése HTTP 400 állapotkódot ad vissza.

- Létrehozott egy DNS-hozzárendelést egy egyéni tartományból a beállított előtér-állomáshoz. Az egyéni tartományállomásnévre küldött kérelem azonban http 400-as állapotkódot ad vissza, és úgy tűnik, hogy nem irányítja a beállított háttérrendszer(ek)et.

### <a name="cause"></a>Ok

Ez a jelenség akkor fordulhat elő, ha nem konfigurált útválasztási szabályt az előtér-állomásként hozzáadott egyéni tartományhoz. Az előtér-állomáshoz explicit módon hozzá kell adni egy útválasztási szabályt, még akkor is, ha az előtér-állomáshoz már konfigurálva van a bejárati ajtó altartomány (*.azurefd.net) alatt, amelyhez az egyéni tartomány rendelkezik DNS-hozzárendeléssel.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Adjon hozzá egy útválasztási szabályt az egyéni tartományból a kívánt háttérkészlethez.

## <a name="front-door-is-not-redirecting-http-to-https"></a>A bejárati ajtó nem irányítja át a HTTP protokollt HTTPS-re

### <a name="symptom"></a>Hibajelenség

A bejárati ajtó rendelkezik egy útválasztási szabály, amely azt mondja, átirányítja a HTTP HTTPS, de a tartomány elérése továbbra is fenntartja a HTTP protokollként.

### <a name="cause"></a>Ok

Ez a viselkedés akkor fordulhat elő, ha nem megfelelően konfigurálta a bejárati ajtó útválasztási szabályait. Alapvetően a jelenlegi konfiguráció nem specifikus, és ütköző szabályokkal rendelkezhetnek.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Az előtér-állomásnévre irányuló kérés 404-es állapotkódot ad vissza

### <a name="symptom"></a>Hibajelenség

- Létrehozott egy bejárati ajtót, és konfigurált egy előtér-állomást, egy háttérkészletet, amelyben legalább egy háttérkiszolgáló van, és egy útválasztási szabályt, amely összeköti az előtér-állomást a háttérkészlettel. Úgy tűnik, hogy a tartalom nem érhető el, amikor kérést küld a konfigurált előtér-állomásnak, mert http 404-es állapotkódot ad vissza.

### <a name="cause"></a>Ok

Ennek a tünetnek számos oka lehet:

- A háttérterület nem nyilvános háttérterület, és nem látható a bejárati ajtó számára.
- A háttérrendszer helytelenül van konfigurálva, ami miatt a bejárati ajtó rossz kérést küld (azaz a háttérrendszer csak http-t fogad el, de nem jelölte be a HTTPS engedélyezését, így a Bejárati ajtó HTTPS-kérelmeket próbál továbbítani).
- A háttérrendszer elutasítja a gazdagép fejlécét, amely továbbította a kérést a háttérrendszernek.
- A háttérrendszer konfigurációja még nincs teljesen telepítve.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

1. Telepítési idő
   - Győződjön meg arról, hogy ~10 percet várt a konfiguráció telepítésére.

2. A háttérbeállítások ellenőrzése
    - Keresse meg azt a háttérkészletet, amelyhez a kérelmet el kell irányítania (attól függ, hogyan van konfigurálva az útválasztási szabály), és ellenőrizze, hogy a _háttérállomás típusa_ és a háttérállomás neve helyes-e. Ha a háttérrendszer egyéni állomás, győződjön meg arról, hogy helyesen írta be. 

    - Ellenőrizze a HTTP- és HTTPS-portokat. A legtöbb esetben a 80, illetve a 443 (illetve) helyes, és nem lesz szükség változtatásra. Azonban van esély arra, hogy a háttér-háttér-van beállítva így, és figyel egy másik porton.

        - Ellenőrizze a _háttér-állomás fejléckonfigurálva_ a háttérrendszereket, amelyek az előtér-állomás kell útválasztás. A legtöbb esetben ennek a fejlécnek meg kell egyeznie a _háttérállomás nevével._ A helytelen érték azonban különböző HTTP 4xx állapotkódokat okozhat, ha a háttérrendszer valami mást vár. Ha adja meg a háttércím IP-címét, előfordulhat, hogy be kell állítania a _háttér-gazdagép fejlécét_ a háttérszámítógép gazdagépnevére.


3. Az útválasztási szabály beállításainak ellenőrzése
    - Keresse meg azt az útválasztási szabályt, amelynek a kérdéses előtér-állomásnévről egy háttérkészletbe kell átirányítania. Győződjön meg arról, hogy az elfogadott protokollok megfelelően vannak konfigurálva, vagy ha nem, győződjön meg arról, hogy a protokoll bejárati ajtaját a kérelem továbbításakor helyesen fogja használni. Az _elfogadott protokollok_ mező határozza meg, hogy a Bejárati ajtó nak mely kéréseket kell elfogadnia, és a _továbbítási protokoll_ határozza meg, hogy a Bejárati ajtó milyen protokollt használjon a kérelem háttér-irányítónak történő továbbításához.
         - Ha például a háttérrendszer csak HTTP-kéréseket fogad, a következő konfigurációk érvényesek:
            - _Az elfogadott protokollok_ a HTTP és a HTTPS. _A továbbítási protokoll_ HTTP. Az egyezési kérelem nem fog működni, mivel a HTTPS egy engedélyezett protokoll, és ha egy kérelem HTTPS-ként érkezett, a Bejárati ajtó https-használatával próbálja továbbítani.

            - _Az elfogadott protokollok_ HTTP-k. _A továbbítási protokoll_ egyezési kérelem vagy HTTPS.

    - _Az URL-újraírás_ alapértelmezés szerint le van tiltva, és csak akkor használja ezt a mezőt, ha szűkíteni szeretné a háttérrendszer által üzemeltetett erőforrások hatókörét, amelyeket elérhetővé szeretne tenni. Ha le van tiltva, a Bejárati ajtó ugyanazt a kérési útvonalat továbbítja, amelyet kap. Lehetséges, hogy ez a mező helytelenül van konfigurálva, és a Bejárati ajtó olyan erőforrást kér a háttérrendszertől, amely nem érhető el, így http 404 állapotkódot ad vissza.

