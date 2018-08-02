---
title: Az Azure Traffic Manager működése |} A Microsoft Docs
description: Ez a cikk segít megérteni, hogyan Traffic Manager átirányítja a forgalmat a magas teljesítmény és a webalkalmazások rendelkezésre állásának
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2018
ms.author: kumud
ms.openlocfilehash: 97ce7fb9d5a99f70e5dd6c81d8c8dd1af343117e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39400370"
---
# <a name="how-traffic-manager-works"></a>Traffic Manager működése

Az Azure Traffic Manager lehetővé teszi, hogy az alkalmazás végpontok közötti forgalom elosztását. A végpont bármely internethez csatlakoztatott szolgáltatás belül, vagy az Azure-on kívül üzemeltetett.

A TRAFFIC Manager két fő előnyöket nyújtja:

- Több egyikére forgalom elosztását [forgalom-útválasztási módszerek](traffic-manager-routing-methods.md)
- [Végpont állapotának folyamatos figyelése](traffic-manager-monitoring.md) és az Automatikus feladatátvétel, ha a végpontok sikertelen

Amikor egy ügyfél megpróbál kapcsolódni egy szolgáltatáshoz, a szolgáltatás DNS-nevét először azt kell feloldani egy IP-címet. Az ügyfél ezután csatlakozik a szolgáltatáshoz való hozzáféréshez IP-címet.

**A leginkább fontos megérteni, hogy, hogy a Traffic Manager a DNS szintjén működik.**  A TRAFFIC Manager DNS-ügyfelek számára, hogy a szabályok a forgalom-útválasztási módszer alapján meghatározott Szolgáltatásvégpontok közvetlen használja. Az ügyfelek kapcsolódni a kiválasztott végpont **közvetlenül**. A TRAFFIC Manager nem a proxy- vagy egy átjárót. A TRAFFIC Manager nem látja a forgalom az ügyfél és a szolgáltatás között.

## <a name="traffic-manager-example"></a>A TRAFFIC Manager-példa

Contoso vállalat kidolgoztunk egy új partner portálra. A portál URL-címe https://partners.contoso.com/login.aspx. Az alkalmazás az Azure három régióban üzemel. Globális teljesítmény maximalizálása és javítható a rendelkezésre állás, a Traffic Manager a legközelebbi elérhető végpontot a fürtforgalom elosztására használata.

Ez a konfiguráció eléréséhez, kövesse az alábbi lépéseket:

1. A szolgáltatás három példány üzemeltethető. A DNS-nevek központi "contoso-us.cloudapp .net", "contoso-eu.cloudapp .net" és "contoso-asia.cloudapp .net".
2. Nevű, "contoso.trafficmanager.net", Traffic Manager-profil létrehozása és konfigurálása, hogy a "Teljesítmény" forgalom-útválasztási módszert használja a három végpontok között.
* Állítsa be a saját személyes tartomány nevét, a "partners.contoso.com", "contoso.trafficmanager.net", egy DNS CNAME rekord használatával átirányítása.

![A TRAFFIC Manager DNS-konfiguráció][1]

> [!NOTE]
> Egy személyes tartomány az Azure Traffic Manager használata esetén a személyes tartománynév átirányítása a Traffic Manager szolgáltatásbeli tartománynévre egy olyan CNAME REKORDOT kell használnia. DNS-szabványok nem teszik lehetővé, hogy hozzon létre egy CNAME rekord, egy tartományhoz "rekordnevet" (vagy főtanúsítvány). Így nem hozható létre egy CNAME REKORDOT a "contoso.com" (más néven "csupasz" tartományhoz). Csak a "contoso.com", például "www.contoso.com" tartomány egy olyan CNAME REKORDOT hozhat létre. A probléma megoldásához, javasoljuk hogy egy egyszerű HTTP-átirányítás küldött közvetlen kérelmeket, egy alternatív nevet, például "www.contoso.com" a "contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Hogy az ügyfelek hogyan kapcsolódnak a Traffic Manager használatával

Folytatás az előző példából, amikor egy ügyfél igényel az oldal https://partners.contoso.com/login.aspx, az ügyfél feloldani a DNS-nevet, és kapcsolatot létesíteni a következő lépéseket hajtja végre:

![Kapcsolat létrehozása a Traffic Manager használatával][2]

1. Az ügyfél egy DNS-lekérdezést küld a konfigurált rekurzív DNS szolgáltatás feloldani a "partners.contoso.com" nevet. Egy rekurzív DNS-szolgáltatás, néha "helyi DNS" szolgáltatás, nem tartalmaz közvetlenül DNS-tartományokat. Ehelyett az ügyfél off-loads a munkát, vegye fel a kapcsolatot a különböző mérvadó DNS-szolgáltatások az interneten egy DNS-név feloldása szükséges.
2. Feloldani a DNS-nevet, a rekurzív DNS szolgáltatás megkeresi a "contoso.com" tartomány névkiszolgálókat. Ezután csatlakozik a ezeket a névkiszolgálókat kérése a "partners.contoso.com" DNS-rekord. A contoso.com DNS-kiszolgálókat a contoso.trafficmanager.net mutató CNAME-rekord visszaadása.
3. Ezután a rekurzív DNS szolgáltatás a "trafficmanager.net" tartományhoz, amely az Azure Traffic Manager szolgáltatás által biztosított megkeresi a névkiszolgálókat. Majd ezeket a DNS-kiszolgálók a "contoso.trafficmanager.net" DNS-rekordra vonatkozó kérelmet küld.
4. A Traffic Manager névkiszolgálóit a kérés fogadásához. Ezek alapján végpont kiválasztása:

    - Minden végpont konfigurált állapotát (a letiltott végpontok nem adja vissza)
    - Minden végpont a Traffic Manager állapot-ellenőrzések határoz meg az aktuális állapotát. További információkért lásd: [Traffic Manager végpont figyelése](traffic-manager-monitoring.md).
    - A kiválasztott forgalom-útválasztási módszer. További információkért lásd: [Traffic Manager útválasztási módszereit](traffic-manager-routing-methods.md).

5. A kiválasztott végpontot egy másik DNS CNAME rekord formájában adja vissza. Ebben az esetben jelezze nekünk tegyük fel, hogy contoso-us.cloudapp.net adja vissza.
6. Ezután a rekurzív DNS szolgáltatás a "cloudapp.net" tartomány megkeresi a névkiszolgálókat. Amelyhez csatlakozik, hogy ezeket a contoso-us.cloudapp .net kéréséhez névkiszolgálókat a DNS-rekordot. Egy "A" DNS-rekordot, amely az Egyesült Államokbeli székhelyű service-végpont IP-címét adja vissza.
7. A rekurzív DNS szolgáltatás összesíti az eredményeket, és az ügyfél egy egyetlen DNS-választ adja vissza.
8. Az ügyfél megkapja a DNS-találatokat, és csatlakozik a megadott IP-cím. Az ügyfél csatlakozik a service-végpont, közvetlenül nem Traffic Manageren keresztül. HTTPS-végpontokat, mert az ügyfél elvégzi a szükséges SSL/TLS-kézfogás, és majd lehetővé teszi egy HTTP GET kérés a "/ login.aspx" lapot.

A rekurzív DNS szolgáltatás gyorsítótárazza a DNS-válaszok kap. A DNS-feloldási az ügyféleszközön is gyorsítótáraz az eredményt. Gyorsítótárazás lehetővé teszi, hogy további DNS-lekérdezések gyorsabban vet fel más névkiszolgálók lekérdezése helyett a gyorsítótárból származó adatok felhasználásával. A gyorsítótár időtartamát az egyes DNS-rekordok a "time-to-live" (TTL) tulajdonság határozza meg. Rövidebb értékek hatására gyorsabb gyorsítótár lejárati és így több adatváltásra az a Traffic Manager-kiszolgálók neveihez. Hosszabb értékek jelenti azt, hogy is telhet, mire hosszabb távolabbi sikertelen endpoint forgalomnak. A TRAFFIC Manager lehetővé teszi az élettartam a Traffic Manager DNS-válaszok kérelemegységszámot 0 másodperc és magas, mint 2 147 483 647 másodperc volt használatos konfigurálását (a megfelelő tartomány maximuma [– az RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)), lehetővé téve az értéket, amely a legjobban elosztja a az alkalmazás igényeinek megfelelően.


## <a name="next-steps"></a>További lépések

További tudnivalók a Traffic Manager [végpont ellenőrzési és automatikus feladatátvételi](traffic-manager-monitoring.md).

További tudnivalók a Traffic Manager [forgalom-útválasztási módszerek](traffic-manager-routing-methods.md).

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

