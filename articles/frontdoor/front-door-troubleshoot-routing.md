---
title: Hibaelhárítás – az Azure bejárati ajtajának szolgáltatás konfigurációs hibáinak elhárítása |} A Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogyan önálló, amelyek számára a bejárati ajtajának fellépő leggyakoribb problémák elhárításához.
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
ms.openlocfilehash: 78370b004d18f70ae4d485f3ad7cfd910e6dd70a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047548"
---
# <a name="troubleshooting-common-routing-issues"></a>Útválasztási gyakori hibák elhárítása
Ez a cikk ismerteti az Azure bejárati ajtajának szolgáltatás konfigurációjában fellépő leggyakoribb útválasztási problémák elhárítása. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>Állomásnév nem útválasztás a háttérrendszer és az értéket ad vissza 400 állapotkód:


### <a name="symptom"></a>Jelenség
- Létrehozott egy bejárati ajtajának, de az előtér-állomás kérelem egy 400-as HTTP-állapotkódot adja vissza.

 - Létrehozott egy DNS egy egyéni tartomány leképezése a frontend gazdagép konfigurálta. Azonban az egyéni tartomány állomásneve kérést egy 400-as HTTP-állapotkódot adja vissza, és nem jelenik meg a backend(s) átirányítása a konfigurált.

### <a name="cause"></a>Ok
- A probléma akkor fordulhat elő, ha nem konfigurálta az egyéni tartomány előtér-gazdagépként hozzáadott útválasztási szabályainak. Útválasztási szabályt hozzá kell adnia kifejezetten adott előtérbeli gazdagép, még akkor is, ha egy már konfigurálva van a frontend gazdagép alatt a bejárati ajtajának altartomány (*. azurefd.net), hogy az egyéni tartomány rendelkezik egy, a DNS-hozzárendelést.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
- Az egyéni tartomány egy útválasztási szabály hozzáadása a kívánt háttérkészlethez.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Előtérbeli gazdagépnév 404 állapotkódot ad vissza kérése

### <a name="symptom"></a>Jelenség
- Létrehozott egy bejárati ajtajának és konfigurált egy előtér-gazdagép szerepel legalább egy háttérrendszerrel háttérkészlet és egy útválasztási szabályt, amely összeköti az előtér-állomás a háttérkészlethez. A tartalom úgy tűnik, nincs elérhető legyen, amikor kérést küld a konfigurált előtérbeli gazdagépre, mert egy HTTP 404-es állapotkódot adott vissza.

### <a name="cause"></a>Ok
Van a probléma lehetséges okai:
 - A háttérrendszer nem nyilvános irányuló háttérkiszolgálón, és nem látható a bejárati ajtajának szolgáltatáshoz.

- A háttérrendszer helytelenül van konfigurálva, a hibás kérelem elküldéséhez a bejárati ajtajának szolgáltatás okozó (azt jelenti, a háttérbeli csak HTTP fogad el, de még nem nincs bejelölve, így a HTTPS, így a bejárati ajtajának próbál továbbítja a HTTPS-kérelmek).
- A háttérrendszer visszautasítja az állomásfejlécet, továbbított, a háttérkiszolgáló a kérelemmel.
- A háttérrendszer konfigurációjának van még nem lett teljes mértékben telepítve.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
1. Üzembe helyezési idő
    - Győződjön meg arról, hogy Ön várakoztak ~ 10 percet a konfiguráció üzembe helyezni.

2. Ellenőrizze a háttér beállításokat
     - Keresse meg a háttérkészlet (függ, hogy rendelkezik a konfigurált útválasztási szabályt) útválasztás a kérelmet, és ellenőrizze, hogy a _háttérrendszer gazdagéptípusokkal_ és háttér-gazdagép neve helyes. Ha a háttérrendszer egy egyéni gazdagépet, győződjön meg arról, hogy Ön rendelkezik helyesen írta be azt. 

     - Ellenőrizze a HTTP és HTTPS-porton. A legtöbb esetben 80-as és 443-as (megfelelő) megfelelő és nem lesz szükség. Van azonban arra, hogy a háttérrendszer ezzel a módszerrel nincs konfigurálva, és a egy másik porton figyel.

    - Ellenőrizze a _háttérrendszer állomásfejléc_ a a háttérrendszerek, amely a Frontend gazdagép útválasztást kell konfigurálni. A legtöbb esetben ez a fejléc lehet ugyanaz, mint a _háttérrendszer állomásnév_. Helytelen értéket okozhat azonban különböző HTTP 4xx állapotkódú, ha a háttérrendszer más vár. A háttérbeli IP-címének adjon meg, ha szüksége lehet beállítani a _háttérrendszer állomásfejléc_ , a háttérrendszer állomásnevét.


3. Az útválasztási szabály beállításainak ellenőrzése
     - Keresse meg az útválasztási szabályt, amely a szóban forgó előtérbeli gazdagépnév a háttérkészlet kell átirányítani. Győződjön meg arról, hogy az elfogadott protokollok helyesen van-e konfigurálva, vagy ha nem, akkor győződjön meg arról, hogy bejárati ajtajának fogja használni, amikor a kérést a protokoll megfelelően van-e konfigurálva. A _protokollok elfogadott_ határozza meg, amely kéri a bejárati ajtajának el kell fogadniuk, és a _továbbítási protokoll_ alatt a _speciális_ lapon határozza meg, milyen bejárati ajtajának protokollt érdemes használnia továbbítja a kérést a háttérkiszolgálóra.
          - Például ha a háttérrendszer csak a HTTP-kéréseket fogad el a következő konfigurációk lenne érvényes:
               - _Elfogadott protokollok_ HTTP és HTTPS. _Továbbítási protokoll_ HTTP. Egyezés kérelem nem fog működni, mivel a HTTPS protokoll engedélyezett, egy kérést kapott HTTPS-t, ha bejárati ajtajának megpróbál-e a HTTPS használatával továbbküldi azokat.

               - _Elfogadott protokollok_ : HTTP. _Továbbítási protokoll_ vagy egyezik a kérelem- vagy HTTPS.

   - Kattintson a _speciális_ lap felső részén a útválasztási szabály konfigurációs panelen. _Az URL Rewrite_ alapértelmezés szerint le van tiltva, és csak használja ezt a mezőt, ha szeretné elérhetővé tenni kívánt erőforrások háttér-ban üzemeltetett a hatókörét leszűkítheti. Ha le van tiltva, bejárati ajtajának továbbítja kap ugyanazon kérelem elérési útját. Akkor lehet, hogy ez a mező helytelenül van konfigurálva, és bejárati ajtajának kér egy erőforrást, amely nem érhető el, a háttérbeli így visszaadása egy HTTP 404 állapotkódot.

