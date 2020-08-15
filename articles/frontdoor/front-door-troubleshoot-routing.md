---
title: Azure bejárati ajtó konfigurációs problémáinak elhárítása
description: Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a bejárati ajtóhoz kapcsolódó gyakori problémák némelyikét.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 40809fae312401cb62fabb10140b9bb7f60e3715
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88234771"
---
# <a name="troubleshooting-common-routing-issues"></a>Gyakori útválasztási problémák elhárítása

Ez a cikk azt ismerteti, hogyan lehet elhárítani az Azure-beli előtérbeni konfigurációval kapcsolatos gyakori útválasztási problémák némelyikét.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 válasz a bejárati ajtóról néhány másodperc múlva

### <a name="symptom"></a>Hibajelenség

- A háttérbe való beküldést követő rendszeres kérések nem lesznek sikeresek, de a bejárati ajtón keresztül 503-as hiba történik.

- A bejárati ajtó meghibásodása néhány másodperc elteltével (általában 30 másodperc után) jelenik meg

### <a name="cause"></a>Ok

Ez a tünet akkor fordul elő, ha a háttérrendszer túllépi az időtúllépési konfigurációt (az alapértelmezett érték 30 másodperc), hogy megkapja a kérést a bejárati ajtótól, vagy ha az időtúllépési érték túllépi a kérést, a bejárati ajtótól választ küld. 

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

- Küldje el a kérést a háttérbe közvetlenül (anélkül, hogy belépjen a bejárati ajtón), és megtudhatja, mi a szokásos idő a háttérrendszer megválaszolására.
- Küldje el a kérést a bejárati ajtón keresztül, és ellenőrizze, hogy van-e 503-válasz. Ha nem, akkor ez nem lehet időtúllépési probléma. Forduljon az ügyfélszolgálathoz.
- Ha a bejárati ajtón keresztüli végrehajtás a 503-es hibakódot eredményezi, akkor a bejárati ajtó sendReceiveTimeout mezőjében adja meg az alapértelmezett időtúllépést akár 4 percre (240 másodpercre). A beállítás a és a `backendPoolSettings` néven van meghívva `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Az egyéni tartományba küldött kérések 400 állapotkódot adnak vissza.

### <a name="symptom"></a>Hibajelenség

- Létrehozott egy bejárati ajtót, de a tartományra vagy a előtér-gazdagépre irányuló kérelem HTTP 400-as állapotkódot ad vissza.

- Létrehozott egy DNS-hozzárendelést egy egyéni tartományból a konfigurált előtér-gazdagépre. Az egyéni tartományhoz tartozó állomásnév küldése azonban egy HTTP 400 állapotkódot ad vissza, és nem úgy tűnik, hogy a konfigurált háttér (ek) ra irányítja az útvonalat.

### <a name="cause"></a>Ok

Ez a jelenség akkor fordulhat elő, ha nem konfigurált útválasztási szabályt a előtér-gazdagépként hozzáadott egyéni tartományhoz. Az útválasztási szabályt explicit módon hozzá kell adni az adott előtér-gazdagéphez, még akkor is, ha az egyik már be van állítva az előtér-gazdagéphez a bejárati ajtó altartományában (*. azurefd.net), amelyhez az egyéni tartományhoz tartozik egy DNS-hozzárendelés.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Adjon hozzá egy útválasztási szabályt az egyéni tartományból a kívánt háttér-készlethez.

## <a name="front-door-is-not-redirecting-http-to-https"></a>A bejárati ajtó nem irányítja át a HTTP-t HTTPS-re

### <a name="symptom"></a>Hibajelenség

A bejárati ajtó rendelkezik egy útválasztási szabállyal, amely szerint a HTTP-t HTTPS-re irányítja, de a tartományhoz való hozzáférés továbbra is a HTTP protokollt használja.

### <a name="cause"></a>Ok

Ez a viselkedés akkor fordulhat elő, ha nem megfelelően konfigurálta az útválasztási szabályokat az előtérben. Az aktuális konfiguráció alapvetően nem specifikus, és lehetnek ütköző szabályok.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>A előtér-gazdagépre irányuló kérelem a 404 állapotkódot adja vissza.

### <a name="symptom"></a>Hibajelenség

- Létrehozta a bejárati ajtót, és konfigurált egy előtér-gazdagépet, egy háttér-készletet legalább egy háttérrel, valamint egy útválasztási szabályt, amely összekapcsolja a frontend-gazdagépet a háttér-készlettel. Úgy tűnik, hogy a tartalom nem érhető el a konfigurált előtér-gazdagépre irányuló kérelem küldésekor, mert a rendszer HTTP 404 állapotkódot ad vissza.

### <a name="cause"></a>Ok

A tünet több lehetséges oka is lehet:

- A háttérrendszer nem nyilvános háttérű, és nem látható a bejárati ajtón.
- A háttér-konfiguráció hibásan van konfigurálva, ami azt eredményezi, hogy a bejárati ajtó nem a megfelelő kérést küldi el (azaz a háttérrendszer csak HTTP-t fogad el, de nem jelölte be a HTTPS-t, így a bejárati ajtó a HTTPS-kérelmek továbbítását kísérli meg)
- A háttérrendszer elutasítja a háttérben a kérelemmel továbbított állomásfejléc-fejlécet.
- A háttérrendszer konfigurációja még nincs teljesen telepítve.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

1. Üzembe helyezés ideje
   - Győződjön meg arról, hogy a konfiguráció üzembe helyezése körülbelül 10 percet várt.

2. A háttér-beállítások keresése
    - Navigáljon arra a háttér-készletre, amelyhez a kérésnek útválasztási műveletnek kell lennie (attól függ, hogyan konfigurálta az útválasztási szabályt), és ellenőrizze, hogy helyesek-e a _háttérbeli gazdagép típusa_ és a háttérbeli gazdagép neve. Ha a háttérrendszer egy egyéni gazdagép, győződjön meg arról, hogy helyesen írta-e be. 

    - Keresse meg a HTTP-és HTTPS-portokat. A legtöbb esetben a 80-es és a 443-as (ill.) érték helyes, és nincs szükség módosításra. Van azonban esély arra, hogy a háttérrendszer ne legyen így konfigurálva, és egy másik portot figyel.

        - Győződjön meg arról, hogy _a háttérrendszer_ azon hátteréhez van konfigurálva, amelyhez a előtér-gazdagépnek útválasztási művelettel kell rendelkeznie. A legtöbb esetben ez a fejléc nem egyezhet meg a háttér- _gazdagép nevével_. A helytelen érték azonban különféle HTTP-4xx okozhat, ha a háttérrendszer más értéket vár el. Ha a háttér IP-címét adja meg, lehetséges, hogy a háttérbeli _állomásfejléc-fejlécet_ be kell állítania a háttér állomásneve számára.


3. Az útválasztási szabály beállításainak megtekintése
    - Navigáljon ahhoz az útválasztási szabályhoz, amely a szóban forgó előtér-gazdagépről egy háttér-készletre irányítja. Győződjön meg arról, hogy az elfogadott protokollok megfelelően vannak konfigurálva, vagy ha nem, győződjön meg arról, hogy a protokoll bejárati ajtaja a kérelem továbbításakor fog működni. Az _elfogadott protokollok_ mező határozza meg, hogy a befelé irányuló kérések mely kérelmeket fogadják el, és a _továbbítási protokoll_ határozza meg, hogy milyen protokoll-előtérben kell használni a kérést a háttér
         - Ha például a háttérrendszer csak a HTTP-kérelmeket fogadja el, a következő konfigurációk érvényesek lesznek:
            - Az _elfogadott protokollok_ a http és a HTTPS. A _továbbítási protokoll_ http. Az egyeztetési kérelem nem fog működni, mivel a HTTPS egy engedélyezett protokoll, és ha egy kérelem HTTPS-ként jött létre, a bejárati ajtó HTTPS-kapcsolaton keresztül próbálkozik a továbbítással.

            - Az _elfogadott protokollok_ a http. A _továbbítási protokoll_ vagy egyeztetési kérelem vagy http.

    - Az _URL-cím újraírása_ alapértelmezés szerint le van tiltva, és csak akkor használja ezt a mezőt, ha le szeretné szűkíteni az elérhetővé tenni kívánt háttérbeli erőforrások hatókörét. Ha le van tiltva, a bejárati ajtó továbbítja ugyanazt a kérési útvonalat, amelyet a rendszer kap. Lehetséges, hogy ez a mező helytelenül van konfigurálva, és a bejárati ajtó olyan erőforrást kér a háttérből, amely nem érhető el, így HTTP 404 állapotkódot ad vissza.

