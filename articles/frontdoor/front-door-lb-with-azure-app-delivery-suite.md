---
title: Azure bejárati ajtó – terheléselosztás az Azure Application Delivery Suite szolgáltatással | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan javasolja az Azure a terheléselosztást az Application Delivery Suite használatával
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 685ee9feaf057e4f2fae3cfe016624806f1ad00c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399463"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Terheléselosztás az Azure alkalmazáskézbesítési csomagjával

## <a name="introduction"></a>Bevezetés
Microsoft Azure több globális és regionális szolgáltatást biztosít a hálózati forgalom elosztásának és terheléselosztásának kezeléséhez: Traffic Manager, bejárati ajtó, Application Gateway és Load Balancer.  Az Azure számos régiója és a többtényezős architektúrája mellett ezen szolgáltatások együttes használatával robusztus, méretezhető, nagy teljesítményű alkalmazásokat hozhat létre.

![Application Delivery Suite ][1]
 
Ezek a szolgáltatások két kategóriába sorolhatók:
1. A **globális terheléselosztási szolgáltatások** , mint például a Traffic Manager és a bejárati ajtó a végfelhasználók számára a regionális háttérrendszer, a felhők, vagy akár a hibrid helyszíni szolgáltatások közötti adatforgalom elosztását is lehetővé teszi. A globális terheléselosztás a legközelebbi szolgáltatási háttérre irányítja át a forgalmat, és a szolgáltatás megbízhatóságának és teljesítményének változásait a felhasználók folyamatos, maximális teljesítményének fenntartása érdekében hajtja végre. 
2. A **regionális terheléselosztási szolgáltatások** , például a standard Load Balancer vagy a Application Gateway lehetővé teszik a virtuális hálózatokon (virtuális hálózatok) belüli forgalom elosztását a virtuális gépeken (VM-EK) vagy a régión belüli zónák közötti végpontokon belül.

Az alkalmazás globális és regionális szolgáltatásainak kombinálásával teljes körű megbízhatósági, teljesítménybeli és biztonságos megoldást biztosítanak a felhasználók és a IaaS, a Pásti vagy a helyszíni szolgáltatások felé irányuló adatforgalom irányítására. A következő szakaszban leírjuk ezeket a szolgáltatásokat.

## <a name="global-load-balancing"></a>Globális terheléselosztás
A **Traffic Manager** globális DNS-terheléselosztást biztosít. A bejövő DNS-kérelmeket tekinti át, és kifogástalan háttérrel válaszol, az ügyfél által kiválasztott útválasztási házirendnek megfelelően. Az útválasztási módszerek beállításai a következők:
- A teljesítmény-útválasztás, hogy a kérelmezőt a legközelebbi háttérbe küldje a késés szempontjából.
- Prioritású útválasztás, amely az összes forgalmat egy háttérbe irányítja, más háttérrel pedig biztonsági másolatként.
- Súlyozott ciklikus időszeleteléses útválasztás, amely az egyes háttérekhez rendelt súlyozás alapján osztja el a forgalmat.
- Földrajzi útválasztás annak biztosítására, hogy az adott földrajzi régiókban található kérelmek az adott régióhoz rendelt háttérre legyenek irányítva (például a spanyolországi összes kérést a Franciaország középső régiójának Azure-régiójába kell átirányítani)
- Alhálózat-útválasztás, amely lehetővé teszi az IP-címtartományok megjelenítését a háttérrendszer számára, hogy az ezektől érkező kéréseket a rendszer a megadott háttérbe küldje (például a vállalati HQ IP-címéről csatlakozó összes felhasználó számára, hogy az általános felhasználóktól eltérő webes tartalmat kapjon)

Az ügyfél közvetlenül csatlakozik ehhez a háttérhöz. Az Azure Traffic Manager észleli, ha egy háttér állapota sérült, majd átirányítja az ügyfeleket egy másik, kifogástalan állapotú példányra. A szolgáltatással kapcsolatos további információkért tekintse meg az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) dokumentációját.

Az **Azure bejárati ajtó** dinamikus webhely-gyorsítást (DSA) biztosít, beleértve a globális http-terheléselosztást is.  A bejövő HTTP-kérések útvonalait a legközelebbi szolgáltatási háttér/régió felé irányítja a megadott állomásnév, URL-útvonal és konfigurált szabályok alapján.  
A bejárati ajtó leállítja a HTTP-kérelmeket a Microsoft hálózatának peremén, és aktívan ellenőrzi az alkalmazás vagy az infrastruktúra állapotának vagy késésének változásait.  A bejárati ajtó ekkor mindig a leggyorsabb és az elérhető (kifogástalan) háttér felé irányítja a forgalmat. A szolgáltatással kapcsolatos további információért tekintse meg a bejárati ajtó [útválasztási architektúrájának](front-door-routing-architecture.md) részleteit és a [forgalom útválasztási módszereit](front-door-routing-methods.md) .

## <a name="regional-load-balancing"></a>Regionális terheléselosztás
A Application Gateway az Application Delivery Controller (ADC) szolgáltatást nyújtja, amely különböző, 7. rétegbeli terheléselosztási funkciókat kínál az alkalmazáshoz. Lehetővé teszi az ügyfelek számára a webfarmok hatékonyságának optimalizálását azáltal, hogy kiszervezik a CPU-igényes TLS-lezárást az Application Gateway felé. Az egyéb 7. rétegbeli útválasztási képességek közé tartozik a bejövő forgalom ciklikus lekéréses eloszlása, a cookie-alapú munkamenet-affinitás, az URL-alapú útválasztás, valamint az egyetlen Application Gateway mögött több webhely üzemeltetése. Application Gateway konfigurálható internetre irányuló átjáróként, csak belső átjáróként, vagy mindkettő kombinációja. A Application Gateway teljes mértékben az Azure által felügyelt, méretezhető és magasan elérhető. Diagnosztikai és naplózási képességek széles skáláját biztosítja a jobb kezelhetőség érdekében.
Load Balancer az Azure SDN stack szerves része, amely nagy teljesítményű, kis késleltetésű 4. rétegbeli terheléselosztási szolgáltatásokat biztosít minden UDP-és TCP-protokollhoz. Felügyeli a bejövő és kimenő kapcsolatokat. Segítségével nyilvános és belső elosztott terhelésű végpontok konfigurálhatók, valamint szabályok definiálhatók, amelyek a bejövő kapcsolatokat a háttérbeli készletben található célokra irányítják TCP- és HTTP-állapotellenőrzési lehetőségek használatával, a szolgáltatás rendelkezésre állásának felügyeletéhez.


## <a name="choosing-a-global-load-balancer"></a>Globális Load Balancer kiválasztása
Ha globális terheléselosztást választ a Traffic Manager és az Azure között a globális útválasztáshoz, érdemes megfontolnia, hogy mi a hasonló, és miben különbözik a két szolgáltatástól.   Mindkét szolgáltatás biztosít
- **Többszörös földrajzi redundancia:** Ha az egyik régió leáll, a forgalom zökkenőmentesen irányítja a legközelebbi régiót, az alkalmazás tulajdonosának beavatkozása nélkül.
- **Legközelebbi régió útválasztása:** A rendszer automatikusan átirányítja a forgalmat a legközelebbi régióba

</br>Az alábbi táblázat a Traffic Manager és az Azure bejárati ajtó közötti különbségeket ismerteti:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Bármely protokoll:** Mivel Traffic Manager a DNS-rétegben működik, bármilyen típusú hálózati forgalmat átirányíthat; HTTP, TCP, UDP stb. | **Http-gyorsítás:** A bejárati ajtó forgalma a Microsoft hálózatának peremén található.  Ezért a HTTP (S) kérelmek a késés és az átviteli sebesség növelését mutatják a TLS-egyeztetés késésének csökkentése és a AFD és az alkalmazás közötti gyors kapcsolatok használatával.|
|Helyszíni **Útválasztás:** A DNS-réteg útválasztásával a forgalom mindig pontról pontra kerül.  A fiókirodából a helyszíni adatközpontba történő útválasztás közvetlen elérési utat is igénybe vehet. még a saját hálózatán is a Traffic Manager használatával. | **Független méretezhetőség:** Mivel a bevezető ajtó a HTTP-kérelemmel működik, a különböző URL-címekre irányuló kérések átirányíthatók különböző háttér-/regionális szolgáltatási készletekbe (a-szolgáltatásokra) a szabályok és az egyes Application Service-szolgáltatások állapota alapján.|
|**Számlázási formátum:** A DNS-alapú számlázás a felhasználók és a több felhasználóval rendelkező szolgáltatások számára is méretezhető, a magasabb szintű használat érdekében. |**Beágyazott biztonság:** A bejárati ajtó lehetővé teszi olyan szabályok használatát, mint például a ráta korlátozása és az IP ACL-nek, hogy a forgalom elérje az alkalmazást. 

</br>Mivel a teljesítmény, az működőképesség és a biztonsági előnyök a HTTP-alapú számítási feladatokhoz a bejárati ajtónál, javasoljuk, hogy az ügyfelek a saját HTTP-számítási feladataikat használják.    A Traffic Manager és a bejárati ajtó párhuzamosan használható az alkalmazás összes adatforgalmának kiszolgálására. 

## <a name="building-with-azures-application-delivery-suite"></a>Kiépítés az Azure Application Delivery Suite szolgáltatással 
Javasoljuk, hogy minden webhely, API és szolgáltatás földrajzilag redundáns legyen, és a lehető legközelebb (legalacsonyabb késésű) helyről továbbítsa a forgalmat a felhasználóknak.  A szolgáltatások Traffic Manager, a bejárati ajtó, a Application Gateway és a Load Balancer együttes használata lehetővé teszi a földrajzilag és zonally redundáns kiépítését a megbízhatóság, a skálázás és a teljesítmény maximalizálása érdekében.

Az alábbi ábrán egy olyan példát ismertetünk, amely az összes szolgáltatás kombinációját használja egy globális webszolgáltatás létrehozásához.   Ebben az esetben az építész arra használta a Traffic Manager, hogy a fájl-és objektum-kézbesítés globális hátterére irányítsa, miközben a bevezetőt használva globálisan átirányítja az URL-címeket, amelyek megfelelnek az áttelepített szolgáltatásnak a (App Service)/Store/.

A régióban, a IaaS szolgáltatás esetében az alkalmazás fejlesztői úgy döntöttek, hogy a/images/*-ként megegyező URL-címek a webfarm többi részétől eltérő virtuális gépek dedikált készletéből szolgálnak.

Emellett a dinamikus tartalmat kiszolgáló alapértelmezett virtuálisgép-készletnek a magas rendelkezésre állású fürtön üzemeltetett háttér-adatbázissal kell kommunikálnia. A teljes telepítés Azure Resource Manageron keresztül van beállítva.

Az alábbi ábrán a forgatókönyv architektúrája látható:

![Application Delivery Suite – részletes architektúra][2] 

> [!NOTE]
> Ez a példa az Azure által kínált terheléselosztási szolgáltatások számos lehetséges konfigurációjának egyike. A Traffic Manager, a bejárati ajtó, a Application Gateway és a Load Balancer összekeverhető, és a legjobban megfelel a terheléselosztási igényeknek. Ha például a TLS/SSL-kiszervezés vagy a 7. rétegbeli feldolgozás nem szükséges, Load Balancer Application Gateway helyett használható.


## <a name="next-steps"></a>Következő lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.

<!--Image references-->
[1]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png
[2]: ./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png
