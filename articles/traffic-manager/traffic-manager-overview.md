---
title: Mi az a Traffic Manager |} Microsoft Docs
description: Ez a cikk segít megérteni a Traffic Manager van, és hogy-e az alkalmazás megfelelő forgalom útválasztási kiválasztása
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: kumud
ms.openlocfilehash: 50d7f14d0d4234ee98d8a46e903b5f916cb02fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877054"
---
# <a name="overview-of-traffic-manager"></a>A Traffic Manager áttekintése

A Microsoft Azure Traffic Manager szolgáltatás végpontok különböző adatközpontokban felhasználói forgalom terjesztését teszi lehetővé. Traffic Manager által támogatott szolgáltatás végpontok közé tartoznak az Azure virtuális gépeken, Web Apps, és a felhőalapú szolgáltatások. A Traffic Manager külső, nem Azure-végpontokkal együtt is használható.

A TRAFFIC Manager használ a tartománynévrendszer (DNS) ügyfél kéréseiket legmegfelelőbb végpontra irányuló forgalom-útválasztási módszert és a végpontok állapotát. A TRAFFIC Manager biztosít számos különböző [forgalom-útválasztási módszert](traffic-manager-routing-methods.md) és [megfigyelési lehetőségek végpont](traffic-manager-monitoring.md) különböző alkalmazásnak és az Automatikus feladatátvétel modellek. A TRAFFIC Manager esetén is lehetséges legyen, beleértve a teljes Azure-régiót hibája.

## <a name="traffic-manager-benefits"></a>A TRAFFIC Manager előnyei

A TRAFFIC Manager segítségével:

* **Rendelkezésre állás, a kritikus alkalmazások fejlesztése**

    A TRAFFIC Manager biztosítja a magas rendelkezésre állás, az alkalmazások figyelése a végpontokat, és automatikus feladatátvétel megadásával, amikor a végpont leáll.

* **A nagy teljesítményű alkalmazások válaszképességét javítása**

    Azure felhőszolgáltatások és webhelyek futtatását a világ adatközpontokban teszi lehetővé. A TRAFFIC Manager úgy irányítja a forgalmat a végponthoz, a legkisebb hálózati késést az ügyfél javítja az alkalmazások válaszkészségét.

* **Szolgáltatás-karbantartás állásidő nélkül**

    A leállás nélküli alkalmazások tervezett karbantartási műveleteket végezheti el. A TRAFFIC Manager forgalom alternatív végpontokra irányítja, amíg folyamatban van a karbantartás.

* **A helyszíni és felhőalapú alkalmazások**

    A TRAFFIC Manager támogatja a külső, lehetővé téve a hibrid használni kívánt-Azure végpontok a felhőalapú és helyszíni környezetekhez, például a "kapacitásnövelés felhő," "áttelepítése felhő," és "feladatátvevő felhő" forgatókönyvek.

* **A forgalom nagy, összetett telepítések terjesztése**

    Használatával [Traffic Manager-profilok beágyazott](traffic-manager-nested-profiles.md), forgalom-útválasztási módszer kombinálható is kifinomult és rugalmas szabályok kielégítse az nagyobb, összetettebb központi telepítések létrehozását.

## <a name="how-traffic-manager-works"></a>A Traffic Manager működése

Az Azure Traffic Manager lehetővé teszi a forgalom eloszlása vezérelni az alkalmazási végpontokat. A végpont bármilyen internetre-szolgáltatott belül vagy kívül Azure.

A TRAFFIC Manager biztosít két fő előnnyel jár:

1. A forgalmat több valamelyikével [forgalom-útválasztási módszer](traffic-manager-routing-methods.md)
2. [Folyamatosan figyelje a végpont állapotfigyelő](traffic-manager-monitoring.md) és automatikus feladatátvétel, ha a végpontok sikertelen

Amikor egy ügyfél próbál kapcsolódni a szolgáltatáshoz, a szolgáltatás DNS-nevét először azt kell tartoznia IP-címet. Az ügyfél ezután kapcsolódik az IP-címet, a szolgáltatás eléréséhez.

**A legfontosabb pont megértéséhez, hogy a Traffic Manager DNS szintjén működik-e.**  A TRAFFIC Manager DNS-ügyfelek a forgalom-útválasztási módszer a szabályok adott Szolgáltatásvégpontok használja. A kijelölt végponthoz kapcsolódni az ügyfelek **közvetlenül**. A TRAFFIC Manager nincs olyan proxy vagy az átjáró. A TRAFFIC Manager nem látja az ügyfél és a szolgáltatás között áthaladó forgalmat.

### <a name="traffic-manager-example"></a>A TRAFFIC Manager – példa

Contoso vállalat egy új partner portál szert. A portál URL-je https://partners.contoso.com/login.aspx. Az alkalmazás Azure három régióiba tárolja. Rendelkezésre állás fejlesztése és globális teljesítmény maximalizálása érdekében, a Traffic Manager fürtforgalom elosztására a legközelebbi elérhető végpontnak használják.

Ez a konfiguráció eléréséhez, akkor kövesse az alábbi lépéseket:

1. A szolgáltatás három példányát telepítse. Két központi telepítését a DNS-nevek a következők: "contoso-us.cloudapp .net", "contoso-eu.cloudapp .net" és "contoso-asia.cloudapp .net".
2. Hozzon létre Traffic Manager-profil, "contoso.trafficmanager.net" nevű, és konfigurálja úgy, hogy a "Teljesítmény" forgalom-útválasztási módszert használja a három végpontok.
* Konfigurálja a kreatív tartománynevüket, "partners.contoso.com", "contoso.trafficmanager.net", egy DNS CNAME rekord használatával mutassanak.

![A TRAFFIC Manager DNS-konfiguráció][1]

> [!NOTE]
> Kreatív tartományhoz az Azure Traffic Manager használata esetén egy olyan CNAME REKORDOT kell használnia a kreatív tartománynév mutasson a Traffic Manager szolgáltatásbeli tartománynevére. DNS-szabványokból nem engedélyezi, hogy a "legfelső pontján" (vagy a legfelső szintű) tartomány egy CNAME rekordot kell létrehoznia. Így nem hozható létre egy CNAME REKORDOT a "contoso.com" (más néven "csupasz" tartományhoz). Csak a "contoso.com", például "www.contoso.com" tartomány egy olyan CNAME REKORDOT hozhat létre. A probléma megoldásához, azt javasoljuk, egy egyszerű HTTP-átirányítás való közvetlen kérelmeket egy másik névre, például "www.contoso.com" a "contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Hogy az ügyfelek hogyan kapcsolódnak a Traffic Managerrel

A Folytatás az előző példából, amikor egy ügyfél lekérdezi a lap https://partners.contoso.com/login.aspx az ügyfél a DNS-név, és létrehozza a kapcsolatot a következő lépéseket végzi el:

![A Traffic Manager használ a kapcsolat felépítése][2]

1. Az ügyfél a DNS-lekérdezést küld a konfigurált rekurzív DNS-szolgáltatás a "partners.contoso.com" név feloldásához. A rekurzív DNS-szolgáltatás, más néven a "helyi DNS" szolgáltatás nem fut közvetlenül DNS-tartományok. Ehelyett az ügyfél off-loads a munkát, lépjen kapcsolatba a különféle mérvadó DNS-szolgáltatásainak az interneten való hozzárendeléséhez egy DNS-nevet.
2. Feloldani a DNS-nevet, a rekurzív DNS-szolgáltatás a "contoso.com" tartomány megtalálja a névkiszolgálókat. Ezután csatlakozik a ezeket a "partners.contoso.com" DNS-rekord kéréséhez névkiszolgálók. A contoso.com DNS-kiszolgálók a contoso.trafficmanager.net mutató CNAME rekord visszaadása.
3. A rekurzív DNS-szolgáltatás a következő névkiszolgálókat megkeresi a "trafficmanager.net" tartomány, amely az Azure Traffic Manager szolgáltatás által biztosított. Majd a "contoso.trafficmanager.net" DNS-rekord kérelmet küld e DNS-kiszolgálók.
4. A Traffic Manager névkiszolgálók a kérés fogadásához. A végpont alapján választ:

    - Az egyes végpontok konfigurált állapotát (letiltott végpontok nem adott vissza)
    - Minden egyes végpont a Traffic Manager állapota alapján aktuális állapotát ellenőrzi. További információkért lásd: [Traffic Manager-végpont figyelés](traffic-manager-monitoring.md).
    - A kiválasztott forgalom-útválasztási módszerrel. További információkért lásd: [Traffic Manager útválasztási metódusok](traffic-manager-routing-methods.md).

5. A kiválasztott végpont egy másik DNS CNAME rekord adja vissza a rendszer. Ebben az esetben ossza meg velünk tegyük fel, hogy a contoso-us.cloudapp.net adja vissza.
6. A rekurzív DNS-szolgáltatás a következő névkiszolgálókat megkeresi a "cloudapp.net" tartomány. Amelyhez csatlakozik, hogy ezeket a contoso-us.cloudapp .net kéréséhez névkiszolgálók DNS-rekordot. Az Egyesült államokbeli szolgáltatás végpontjának IP-címét tartalmazó DNS "A" rekordot ad vissza.
7. A rekurzív DNS-szolgáltatás összesíti az eredményeket, és az ügyfél egy egyetlen DNS-választ ad vissza.
8. Az ügyfél megkapja a DNS-találatokat, és csatlakozik a megadott IP-cím. Az ügyfél kapcsolódik az alkalmazás szolgáltatási végpont közvetlenül, nem Traffic Manager használatával. Mivel a HTTPS-végpontnak, az ügyfél hajtja végre a szükséges SSL/TLS-kézfogás, és majd válik, mivel egy HTTP GET kérést a "/ login.aspx" lapon.

A rekurzív DNS-szolgáltatás gyorsítótárba helyezi azt a DNS-válaszok kap. A DNS-feloldási az ügyféleszközön is gyorsítótárba helyezi azt az eredményt. Gyorsítótárazás lehetővé teszi, hogy további DNS-lekérdezések gyorsabban válaszolhasson a gyorsítótárból adatokkal, nem pedig más névkiszolgálók lekérdezése. A gyorsítótár időtartamát határozza meg, az "idő TTL" (TTL) tulajdonsága minden DNS-rekord. Rövidebb értékek megnyilvánulhat gyorsabb gyorsítótár lejárati és így további üzenetváltások a Traffic Manager névkiszolgálók. Hosszabb értékek azt jelenti, hogy is telhet, mire hosszabb el egy hibás végpont közvetlen forgalmat. A TRAFFIC Manager lehetővé teszi az élettartam a Traffic Manager DNS-válaszok segítségével lehet akár csupán 0 másodperc és 2 147 483 647 másodpercig magas konfigurálását (a tartomány maximális megfelelő [RFC-1035 szabványnak megfelelően](https://www.ietf.org/rfc/rfc1035.txt)), kiválaszthatja az értéket, amelyet az alkalmazás igényeinek legjobban kiegyensúlyozza engedélyezése.

## <a name="pricing"></a>Díjszabás

Díjszabási információkért lásd: [Traffic Manager árazás](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faq"></a>GYIK

Lásd: gyakran ismételt kérdések a Traffic Manager [Traffic Manager – gyakori kérdések](traffic-manager-FAQs.md)

## <a name="next-steps"></a>Következő lépések

A Traffic Manager további [végpont figyelési és automatikus feladatátvételt](traffic-manager-monitoring.md).

A Traffic Manager további [forgalom-útválasztási módszerei](traffic-manager-routing-methods.md).

Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

