---
title: Azure bejárati ajtó konfigurációs problémáinak elhárítása
description: Ebből az oktatóanyagból megtudhatja, hogyan végezheti el a bejárati ajtóhoz kapcsolódó gyakori problémák némelyikét.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: dbce9019e33c07dd4faa91ffd490eba4d313c675
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630610"
---
# <a name="troubleshooting-common-routing-issues"></a>Gyakori útválasztási problémák elhárítása

Ez a cikk azt ismerteti, hogyan lehet elhárítani az Azure-beli előtérbeni konfigurációval kapcsolatos gyakori útválasztási problémák némelyikét.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 válasz a bejárati ajtóról néhány másodperc múlva

### <a name="symptom"></a>Hibajelenség

* A háttérbe való beküldést követő rendszeres kérések nem lesznek sikeresek, de a bejárati ajtón keresztül 503-as hiba történik.
* A bejárati ajtó meghibásodása néhány másodperc elteltével (általában 30 másodperc után) jelenik meg

### <a name="cause"></a>Ok

A probléma oka a következő két dolog egyike lehet:
 
* A háttérrendszer az időtúllépésnél hosszabb időt vesz igénybe (az alapértelmezett érték 30 másodperc), hogy megkapja a kérést az előtérben.
* A beérkező kérésre adott válasz küldéséhez szükséges idő az időtúllépési értéknél hosszabb időt vesz igénybe. 

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

* Küldje el a kérést a háttérbe közvetlenül (anélkül, hogy belépjen a bejárati ajtón), és megtudhatja, mi a szokásos idő a háttérrendszer megválaszolására.
* Küldje el a kérést a bejárati ajtón keresztül, és ellenőrizze, hogy van-e 503-válasz. Ha nem, akkor előfordulhat, hogy a probléma nem időtúllépési hiba. Vegye fel a kapcsolatot az ügyfélszolgálattal.
* Ha az első ajtón halad át, a rendszer 503-as hibakódot eredményez, majd konfigurálja a `sendReceiveTimeout` bejárati ajtó mezőjét. Az alapértelmezett időtúllépés akár 4 perc is kiterjeszthető (240 másodperc). A beállítás a és a `backendPoolSettings` néven van meghívva `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Az egyéni tartományba küldött kérések 400 állapotkódot adnak vissza.

### <a name="symptom"></a>Hibajelenség

* Létrehozott egy bejárati ajtót, de a tartományra vagy a előtér-gazdagépre irányuló kérelem HTTP 400-as állapotkódot ad vissza.
* Létrehozta az egyéni tartományhoz tartozó DNS-leképezést a konfigurált előtér-gazdagéphez. A kérésnek az egyéni tartományba való küldése azonban HTTP 400-állapotkódot ad vissza. Ez a beállítás nem jelenik meg a konfigurált háttérre való átirányításhoz.

### <a name="cause"></a>Ok

A probléma akkor fordul elő, ha nem konfigurált útválasztási szabályt a előtér-gazdagépként hozzáadott egyéni tartományhoz. Explicit módon hozzá kell adni egy útválasztási szabályt az adott előtér-gazdagéphez. Még akkor is, ha már konfigurálva van a előtér-gazdagéphez a bejárati ajtó altartományában (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

Adja hozzá az egyéni tartomány útválasztási szabályát a kiválasztott háttérbeli készletre irányuló közvetlen forgalomhoz.

## <a name="front-door-doesnt-redirect-http-to-https"></a>A bejárati ajtó nem irányítja át a HTTP PROTOKOLLT

### <a name="symptom"></a>Hibajelenség

A bejárati ajtó rendelkezik egy útválasztási szabállyal, amely szerint a HTTP-t HTTPS-re irányítja, de a tartományhoz való hozzáférés továbbra is a HTTP protokollt használja.

### <a name="cause"></a>Ok

Ez a viselkedés akkor fordulhat elő, ha nem megfelelően konfigurálta az útválasztási szabályokat az előtérben. Az aktuális konfiguráció alapvetően nem specifikus, és lehetnek ütköző szabályok.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>A előtér-gazdagépre irányuló kérelem a 404 állapotkódot adja vissza.

### <a name="symptom"></a>Hibajelenség

 A bejárati ajtót úgy hozta létre, hogy egy előtér-gazdagépet, egy háttér-készletet és legalább egy háttér-készletet konfigurál, valamint egy útválasztási szabályt, amely összekapcsolja a frontend-gazdagépet a háttér-készlettel. A tartalom nem érhető el, ha a konfigurált előtér-gazdagépre irányuló kérést küld, ezért a rendszer HTTP 404 állapotkódot ad vissza.

### <a name="cause"></a>Ok

A tünet több lehetséges oka is lehet:

* A háttér nem egy nyilvános háttérrendszer, és nem látható a bejárati ajtón.
* Helytelenül van konfigurálva a háttérrendszer, így a bejárati ajtó nem a megfelelő kérést küldi el. Más szóval a háttérrendszer csak a HTTP-t fogadja el, és a HTTPS engedélyezése nem engedélyezett. Így a bejárati ajtó a HTTPS-kérelmek továbbítására tesz kísérletet.
* A háttérrendszer elutasítja a háttérben a kérelemmel továbbított állomásfejléc-fejlécet.
* A háttérrendszer konfigurációja még nincs teljesen telepítve.

### <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

1. Üzembe helyezés ideje
   * Győződjön meg arról, hogy a konfiguráció üzembe helyezése körülbelül 10 percet várt.

2. A háttér-beállítások keresése
    * Navigáljon arra a háttér-készletre, amelyhez a kérésnek útválasztási műveletnek kell lennie (attól függ, hogy az útválasztási szabály hogyan van konfigurálva). Ellenőrizze, hogy helyesek-e a *háttérbeli gazdagép típusa* és a háttérbeli gazdagép neve. Ha a háttérrendszer egy egyéni gazdagép, győződjön meg arról, hogy helyesen írta-e be. 

    * Keresse meg a HTTP-és HTTPS-portokat. A legtöbb esetben a 80-es és a 443-as (ill.) érték helyes, és nincs szükség módosításra. Van azonban esély arra, hogy a háttérrendszer ne legyen így konfigurálva, és egy másik portot figyel.

        * Győződjön meg arról, hogy _a háttérrendszer_ azon hátteréhez van konfigurálva, amelyhez a előtér-gazdagépnek útválasztási művelettel kell rendelkeznie. A legtöbb esetben ez a fejléc nem egyezhet meg a háttér- *gazdagép nevével*. A helytelen érték azonban különféle HTTP-4xx okozhat, ha a háttérrendszer más értéket vár el. Ha a háttér IP-címét adja meg, lehetséges, hogy a háttérbeli *állomásfejléc-fejlécet* be kell állítania a háttér állomásneve számára.

3. Keresse meg az útválasztási szabály beállításait:
    * Navigáljon ahhoz az útválasztási szabályhoz, amely a szóban forgó előtér-gazdagépről egy háttér-készletre irányítja. Győződjön meg arról, hogy az elfogadott protokollok megfelelően vannak konfigurálva a kérelem továbbításakor. Az *elfogadott protokollok* mező határozza meg, hogy a befelé irányuló kérések mely kérelmeket fogadják el. A *továbbítási protokoll* határozza meg, hogy milyen protokoll-előtérben kell használni a kérést a háttérbe való továbbításhoz.
         * Ha például a háttérrendszer csak a HTTP-kérelmeket fogadja el, a következő konfigurációk érvényesek lesznek:
            * Az *elfogadott protokollok* a http és a HTTPS. A *továbbítási protokoll* http. A kérelem egyeztetése nem fog működni, mivel a HTTPS egy engedélyezett protokoll, és ha egy kérelem HTTPS-ként jelentkezett be, a bejárati ajtó HTTPS-kapcsolaton keresztül próbálkozik a továbbítással.

            * Az *elfogadott protokollok* a http. A *továbbítási protokoll* vagy egyeztetési kérelem vagy http.

    - Az *URL-cím újraírása* alapértelmezés szerint le van tiltva. Ez a mező csak akkor használható, ha le szeretné szűkíteni az elérhetővé tenni kívánt háttérrendszer-erőforrások hatókörét. Ha le van tiltva, a bejárati ajtó továbbítja ugyanazt a kérési útvonalat, amelyet a rendszer kap. Ezt a mezőt helytelenül lehet beállítani. Tehát amikor a bevezető ajtó olyan erőforrást kér a háttérből, amely nem érhető el, egy HTTP 404 állapotkódot ad vissza.
