---
title: Hibaelhárítás – az Azure bejárati ajtó szolgáltatás konfigurációjával kapcsolatos problémák elhárítása | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a bejárati ajtóhoz kapcsolódó gyakori problémák némelyikét.
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
ms.openlocfilehash: 420d7afe0d825da9149f2cb2ae1540a2805b357c
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335883"
---
# <a name="troubleshooting-common-routing-issues"></a>Gyakori útválasztási problémák elhárítása
Ez a cikk azt ismerteti, hogyan lehet elhárítani az Azure bejárati szolgáltatásának konfigurációjában esetlegesen előforduló gyakori útválasztási problémákat. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>A gazdagép nem irányítja át a háttérbe, és 400 állapotkódot ad vissza.


### <a name="symptom"></a>Jelenség
- Létrehozott egy bejárati ajtót, de a előtér-gazdagépre irányuló kérelem HTTP 400-as állapotkódot ad vissza.

  - Létrehozott egy DNS-hozzárendelést egy egyéni tartományból a konfigurált előtér-gazdagépre. Az egyéni tartományhoz tartozó állomásnév küldése azonban egy HTTP 400 állapotkódot ad vissza, és nem úgy tűnik, hogy a konfigurált háttér (ek) ra irányítja az útvonalat.

### <a name="cause"></a>Ok
- Ez a jelenség akkor fordulhat elő, ha nem konfigurált útválasztási szabályt a előtér-gazdagépként hozzáadott egyéni tartományhoz. Az útválasztási szabályt explicit módon hozzá kell adni az adott előtér-gazdagéphez, még akkor is, ha az egyik már be van állítva az előtér-gazdagéphez a bejárati ajtó altartományában (*. azurefd.net), amelyhez az egyéni tartományhoz tartozik egy DNS-hozzárendelés.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
- Adjon hozzá egy útválasztási szabályt az egyéni tartományból a kívánt háttér-készlethez.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>A előtér-gazdagépre irányuló kérelem a 404 állapotkódot adja vissza.

### <a name="symptom"></a>Jelenség
- Létrehozta a bejárati ajtót, és konfigurált egy előtér-gazdagépet, egy háttér-készletet legalább egy háttérrel, valamint egy útválasztási szabályt, amely összekapcsolja a frontend-gazdagépet a háttér-készlettel. Úgy tűnik, hogy a tartalom nem érhető el a konfigurált előtér-gazdagépre irányuló kérelem küldésekor, mert a rendszer HTTP 404 állapotkódot ad vissza.

### <a name="cause"></a>Ok
A tünet több lehetséges oka is lehet:
 - A háttérrendszer nem nyilvános háttérrendszer, és nem látható a bejárati ajtó szolgáltatás számára.

- A háttér-konfiguráció hibásan van konfigurálva, ami azt okozza, hogy a bejárati ajtó szolgáltatás nem megfelelő kérést küld (azaz a háttérrendszer csak HTTP-t fogad el, de nem jelölte be a HTTPS-t, így a bejárati ajtó a HTTPS-kérelmek továbbítását kísérli meg).
- A háttérrendszer elutasítja a háttérben a kérelemmel továbbított állomásfejléc-fejlécet.
- A háttérrendszer konfigurációja még nincs teljesen telepítve.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
1. Üzembe helyezés ideje
    - Győződjön meg arról, hogy a konfiguráció üzembe helyezése körülbelül 10 percet várt.

2. A háttér-beállítások keresése
   - Navigáljon arra a háttér-készletre, amelyhez a kérésnek útválasztási műveletnek kell lennie (attól függ, hogyan konfigurálta az útválasztási szabályt), és ellenőrizze, hogy helyesek-e a _háttérbeli gazdagép típusa_ és a háttérbeli gazdagép neve. Ha a háttérrendszer egy egyéni gazdagép, győződjön meg arról, hogy helyesen írta-e be. 

   - Keresse meg a HTTP-és HTTPS-portokat. A legtöbb esetben a 80-es és a 443-as (ill.) érték helyes, és nincs szükség módosításra. Van azonban esély arra, hogy a háttérrendszer ne legyen így konfigurálva, és egy másik portot figyel.

     - Győződjön meg arról, hogy a háttérrendszer azon hátteréhez _van konfigurálva_ , amelyhez a előtér-gazdagépnek útválasztási művelettel kell rendelkeznie. A legtöbb esetben ez a fejléc nem egyezhet meg a háttér- _gazdagép nevével_. A helytelen érték azonban különféle HTTP-4xx okozhat, ha a háttérrendszer más értéket vár el. Ha a háttér IP-címét adja meg, lehetséges, hogy a háttérbeli _állomásfejléc_ -fejlécet be kell állítania a háttér állomásneve számára.


3. Az útválasztási szabály beállításainak megtekintése
     - Navigáljon ahhoz az útválasztási szabályhoz, amely a szóban forgó előtér-gazdagépről egy háttér-készletre irányítja. Győződjön meg arról, hogy az elfogadott protokollok megfelelően vannak konfigurálva, vagy ha nem, győződjön meg arról, hogy a protokoll bejárati ajtaja a kérelem továbbításakor fog működni. Az _elfogadott protokollok_ határozzák meg, hogy a bejárati ajtó mely kéréseket fogadja el, és a _továbbítási protokoll_ határozza meg, hogy melyik protokollt kell használnia a kérésnek a háttérbe küldéséhez
          - Ha például a háttérrendszer csak a HTTP-kérelmeket fogadja el, a következő konfigurációk érvényesek lesznek:
               - Az _elfogadott protokollok_ a http és a HTTPS. A _továbbítási protokoll_ http. Az egyeztetési kérelem nem fog működni, mivel a HTTPS egy engedélyezett protokoll, és ha egy kérelem HTTPS-ként jött létre, a bejárati ajtó HTTPS-kapcsolaton keresztül próbálkozik a továbbítással.

               - Az _elfogadott protokollok_ a http. A _továbbítási protokoll_ vagy egyeztetési kérelem vagy HTTPS.

   - Az _URL-cím_ újraírása alapértelmezés szerint le van tiltva, és csak akkor használja ezt a mezőt, ha le szeretné szűkíteni az elérhetővé tenni kívánt háttérbeli erőforrások hatókörét. Ha le van tiltva, a bejárati ajtó továbbítja ugyanazt a kérési útvonalat, amelyet a rendszer kap. Lehetséges, hogy ez a mező helytelenül van konfigurálva, és a bejárati ajtó olyan erőforrást kér a háttérből, amely nem érhető el, így HTTP 404 állapotkódot ad vissza.

