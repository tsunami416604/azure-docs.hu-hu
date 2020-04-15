---
title: Magas rendelkezésre állás és terheléselosztás – Azure AD alkalmazásproxy
description: Hogyan működik a forgalom elosztása az alkalmazásproxy központi telepítésével. Tippek az összekötőteljesítmény optimalizálásához és a háttérkiszolgálók terheléselosztásának használatához.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 992075378737552e890bd2d6fed3c519e6c62aa7
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312952"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Az alkalmazásproxy-csatlakozók és -alkalmazások magas rendelkezésre állása és terheléselosztása

Ez a cikk bemutatja, hogyan működik a forgalom elosztása az alkalmazásproxy központi telepítésével. Majd megbeszéljük:

- A forgalom elosztása a felhasználók és az összekötők között, valamint tippek az összekötők teljesítményének optimalizálásához

- Hogyan áramlik a forgalom az összekötők és a háttéralkalmazás-kiszolgálók között, több háttérkiszolgáló közötti terheléselosztásra vonatkozó javaslatokkal

## <a name="traffic-distribution-across-connectors"></a>Forgalomeloszlás az összekötők között

Az összekötők a magas rendelkezésre állás elvein alapuló kapcsolataikat hozzák létre. Nincs garancia arra, hogy a forgalom mindig egyenletesen oszlik el az összekötők között, és nincs munkamenet-affinitás. A használat azonban változó, és a kérelmeket véletlenszerűen küldi el az Alkalmazásproxy szolgáltatáspéldányai. Ennek eredményeképpen a forgalom általában szinte egyenletesen oszlik el az összekötők között. Az alábbi diagram és az alábbi lépések bemutatják, hogyan jönnek létre a felhasználók és az összekötők közötti kapcsolatok.

![Felhasználók és összekötők közötti kapcsolatokat bemutató diagram](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Az ügyféleszközön lévő felhasználó megpróbál hozzáférni egy alkalmazásproxyn keresztül közzétett helyszíni alkalmazáshoz.
2. A kérelem egy Azure Load Balancer-en keresztül történik, hogy meghatározza, melyik alkalmazásproxy-szolgáltatáspéldánynak kell megkérnie a kérelmet. Régiónként több tíz példány áll rendelkezésre a kérelem fogadásához. Ez a módszer segít a forgalom egyenletes elosztásában a szolgáltatáspéldányok között.
3. A rendszer elküldi a kérelmet a [Service Bus rendszernek.](https://docs.microsoft.com/azure/service-bus-messaging/)
4. A Service Bus egy elérhető csatlakozóhoz jelzi a szervizbuszt. Az összekötő ezután felveszi a kérelmet a Service Bus.
   - A 2. Ennek eredményeképpen az összekötőket szinte egyenletesen használják a csoporton belül.
5. Az összekötő továbbítja a kérelmet az alkalmazás háttérkiszolgálójára. Ezután az alkalmazás elküldi a választ az összekötőnek.
6. Az összekötő befejezi a választ egy kimenő kapcsolat megnyitásával a szolgáltatáspéldányhoz, ahonnan a kérelem érkezett. Ezután ez a kapcsolat azonnal megszakad. Alapértelmezés szerint minden összekötő legfeljebb 200 egyidejű kimenő kapcsolat.
7. A válasz ezután visszakerül az ügyfélnek a szolgáltatáspéldányból.
8. Az azonos kapcsolatból érkező további kérések megismétlik a fenti lépéseket.

Egy alkalmazás gyakran sok erőforrást, és megnyit több kapcsolatot, amikor bevan töltve. Minden kapcsolat végigmegy a fenti lépéseket, hogy lelegyen foglalva egy szolgáltatáspéldány, válasszon ki egy új elérhető összekötőt, ha a kapcsolat még nem párosított egy összekötőt.


## <a name="best-practices-for-high-availability-of-connectors"></a>Ajánlott eljárások a csatlakozók magas rendelkezésre állásával kapcsolatban

- A forgalom elosztása az összekötők között a magas rendelkezésre állás érdekében, elengedhetetlen, hogy mindig legalább két összekötők egy összekötőcsoportban. Három összekötők előnyben részesítik, hogy további puffer összekötők között. A szükséges összekötők helyes számának meghatározásához kövesse a kapacitástervezési dokumentációt.

- Egyetlen meghibásodási pont elkerülése érdekében helyezzen összekötőket különböző kimenő kapcsolatokra. Ha az összekötők ugyanazt a kimenő kapcsolatot használják, a kapcsolattal kapcsolatos hálózati probléma hatással lehet az azt használó összes összekötőre.

- Kerülje az összekötők újraindításának kényszerítését, amikor éles alkalmazásokhoz csatlakozik. Ez negatívan befolyásolhatja a forgalom elosztását az összekötők között. Az összekötők újraindítása miatt több összekötő nem érhető el, és a kapcsolatokat a fennmaradó rendelkezésre álló összekötőkhöz kényszeríti. Az eredmény kezdetben a csatlakozók egyenetlen használata.

- Kerülje a szövegközi ellenőrzés minden formáját az összekötők és az Azure közötti kimenő TLS-kommunikációsorán. Ez a fajta inline ellenőrzés a kommunikációs folyamat romlását okozza.

- Ügyeljen arra, hogy az automatikus frissítések továbbra is futjanak az összekötőkhöz. Ha az Alkalmazásproxy-összekötő frissítő szolgáltatás fut, az összekötők automatikusan frissülnek, és megkapják a legújabb frissítést. Ha nem látja az Összekötő frissítő szolgáltatást a kiszolgálón, újra kell telepítenie az összekötőt a frissítések lekéréséhez.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Az összekötők és a háttéralkalmazás-kiszolgálók közötti forgalom

Egy másik kulcsfontosságú terület, ahol a magas rendelkezésre állás tényező az összekötők és a háttérkiszolgálók közötti kapcsolat. Ha egy alkalmazás az Azure AD alkalmazásproxyn keresztül jelenik meg, a felhasználók tól az alkalmazásokfelé irányuló forgalom három ugráson keresztül áramlik:

1. A felhasználó csatlakozik az Azure AD alkalmazásproxy szolgáltatás nyilvános végponthoz az Azure-ban. A kapcsolat az ügyfél eredeti ügyfél IP-címe (nyilvános) és az alkalmazásproxy-végpont IP-címe között jön létre.
2. Az alkalmazásproxy-összekötő lekéri az ügyfél HTTP-kérelmét az alkalmazásproxy-szolgáltatásból.
3. Az alkalmazásproxy-összekötő csatlakozik a célalkalmazáshoz. Az összekötő saját IP-címét használja a kapcsolat létrehozásához.

![Alkalmazásproxyn keresztül iaalkalmazáshoz csatlakozó felhasználó diagramja](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-Forwarded-A fejléc mező szempontjai
Bizonyos helyzetekben (például naplózás, terheléselosztás stb.) a külső ügyfél eredeti IP-címének megosztása a helyszíni környezettel követelmény. A követelmény teljesítése érdekében az Azure AD alkalmazásproxy-összekötő hozzáadja az X-Forwarded-For fejlécmezőt az eredeti ügyfél IP-címével (nyilvános) a HTTP-kérelemhez. A megfelelő hálózati eszköz (terheléselosztó, tűzfal) vagy a webkiszolgáló vagy a háttéralkalmazás ezután eltudja olvasni és használhatja az adatokat.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Gyakorlati tanácsok a több alkalmazáskiszolgáló közötti terheléselosztáshoz
Ha az alkalmazásproxy-alkalmazáshoz rendelt összekötőcsoport két vagy több összekötővel rendelkezik, és a háttérszintű webalkalmazást több kiszolgálón (kiszolgálófarmon) futtatja, akkor jó terheléselosztási stratégiára van szükség. A jó stratégia biztosítja, hogy a kiszolgálók egyenletesen vegyék fel az ügyfélkérelmeket, és megakadályozza a kiszolgálófarm kiszolgálóinak túl- vagy alulkihasználtságát.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>1. eset: A háttéralkalmazás nem igényel munkamenet-megőrzést
A legegyszerűbb forgatókönyv az, ahol a háttér-webes alkalmazás nem igényel munkamenet stickiness (munkamenet-perzisztencia). A felhasználó bármely kérését a kiszolgálófarm bármely háttéralkalmazás-példánya eltudja intézni. Használhatja a 4. Néhány lehetőség a Microsoft Network Load Balancing and Azure Load Balancer vagy egy másik szállító terheléselosztója. Másik lehetőségként a ciklikus multiplexeléses DNS is konfigurálható.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>2. forgatókönyv: A háttéralkalmazás munkamenet-megőrzést igényel
Ebben a forgatókönyvben a háttér-webalkalmazás munkamenet stickiness (munkamenet-megőrzési) a hitelesített munkamenet során igényel munkamenet stickiness (munkamenet-megőrzési). A felhasználótól érkező összes kérést a kiszolgálófarm ugyanazon kiszolgálóján futó háttéralkalmazás-példánynak kell kezelnie.
Ez a forgatókönyv bonyolultabb lehet, mivel az ügyfél általában több kapcsolatot hoz létre az alkalmazásproxy szolgáltatással. A különböző kapcsolatokon keresztül érkező kérelmek a farm különböző összekötőihez és kiszolgálóihoz érkezhetnek. Mivel minden összekötő saját IP-címet használ ehhez a kommunikációhoz, a terheléselosztó nem tudja biztosítani a munkamenet-ragozást az összekötők IP-címe alapján. Forrás IP affinitás nem használható sem.
Íme néhány lehetőség a 2.

- 1. lehetőség: A munkamenet-megőrzést a terheléselosztó által beállított munkamenet-cookie-ra alapozza. Ez a beállítás azért ajánlott, mert lehetővé teszi, hogy a terhelés egyenletesebben oszlik el a háttérkiszolgálók között. Ez megköveteli a layer 7 terheléselosztó ezzel a képességgel, és amely képes kezelni a HTTP-forgalmat, és megszünteti a TLS-kapcsolat. Használhatja az Azure Application Gateway (Session Affinity) vagy egy terheléselosztó egy másik szállítótól.

- 2. lehetőség: A munkamenet-megőrzést az X-Forwarded-For fejlécmezőre alapozza. Ehhez a beállításhoz egy 7-es réteges terheléselosztóra van szükség ezzel a képességgel, amely képes kezelni a HTTP-forgalmat, és megszüntetni a TLS-kapcsolatot.  

- 3. lehetőség: Állítsa be úgy a háttéralkalmazást, hogy ne igényeljen munkamenet-megőrzést.

A háttéralkalmazás terheléselosztási követelményeinek megismeréséhez tekintse meg a szoftver gyártójának dokumentációját.

## <a name="next-steps"></a>További lépések

- [Alkalmazásproxy engedélyezése](application-proxy-add-on-premises-application.md)
- [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
- [Feltételes hozzáférés engedélyezése](application-proxy-integrate-with-sharepoint-server.md)
- [Az alkalmazásproxyval kapcsolatos problémák hibaelhárítása](application-proxy-troubleshoot.md)
- [Ismerje meg, hogy az Azure AD architektúra hogyan támogatja a magas rendelkezésre állást](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-architecture)
