---
title: Az alkalmazásátjáró működése
description: Ez a cikk arról nyújt tájékoztatást, hogy az alkalmazásátjáró hogyan fogadja a bejövő kérelmeket, és hogyan irányítja azokat a háttérrendszerhez.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: 84a7bdfb9f8f7c741140cbe2086149dff90db211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132974"
---
# <a name="how-an-application-gateway-works"></a>Az alkalmazásátjáró működése

Ez a cikk bemutatja, hogy az alkalmazásátjáró hogyan fogadja a bejövő kérelmeket, és hogyan irányítja őket a háttérrendszerhez.

![Hogyan fogad el egy alkalmazásátjáró egy kérést?](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Hogyan fogad el egy alkalmazásátjáró egy kérést?

1. Mielőtt az ügyfél kérelmet küldne egy alkalmazásátjárónak, az alkalmazásátjáró tartománynevét dns-kiszolgáló val oldja fel. Az Azure szabályozza a DNS-bejegyzést, mert az összes alkalmazásátjáró a azure.com tartományban van.

2. Az Azure DNS visszaadja az IP-címet az ügyfélnek, amely az alkalmazásátjáró előtér-IP-címe.

3. Az alkalmazásátjáró egy vagy több figyelő bejövő forgalmat fogad el. A figyelő egy logikai entitás, amely ellenőrzi a kapcsolatkérelmeket. Előtér-IP-címmel, protokoll- és portszámmal van konfigurálva az ügyfelek és az alkalmazásátjáró kapcsolataihoz.

4. Ha egy webalkalmazás-tűzfal (WAF) van használatban, az alkalmazásátjáró ellenőrzi a kérelem fejléceit és a törzset, ha van, a WAF-szabályokkal szemben. Ez a művelet határozza meg, hogy a kérelem érvényes-e, vagy biztonsági fenyegetés. Ha a kérelem érvényes, a háttérbe kerül. Ha a kérelem nem érvényes, és a WAF megelőzési módban van, biztonsági fenyegetésként le van tiltva. Ha észlelési módban van, a rendszer kiértékeli és naplózza a kérelmet, de továbbra is továbbítja a háttérkiszolgálóra.

Az Azure Application Gateway belső alkalmazásterhelés-elosztóként vagy internetfelé néző alkalmazásterhelés-elosztóként is használható. Az internetre néző alkalmazásátjáró nyilvános IP-címeket használ. Az internetre néző alkalmazásátjáró DNS-neve nyilvánosan feloldható a nyilvános IP-címére. Ennek eredményeképpen az internetre néző alkalmazásátjárók továbbíthatják az ügyfélkérelmeket az internetre.

A belső alkalmazásátjárók csak privát IP-címeket használnak. Egyéni vagy [magánhálózati DNS-zóna](https://docs.microsoft.com/azure/dns/private-dns-overview)használata esetén a tartománynévnek belsőleg feloldhatónak kell lennie az Application Gateway privát IP-címére. Ezért a belső terheléselosztók csak az alkalmazásátjáró virtuális hálózatához hozzáféréssel rendelkező ügyfelektől érkező kérelmeket irányíthatják.

## <a name="how-an-application-gateway-routes-a-request"></a>Hogyan irányítja egy alkalmazásátjáró a kéréseket?

Ha egy kérelem érvényes, és a WAF nem blokkolja, az alkalmazásátjáró kiértékeli a figyelőhöz társított kérelem-útválasztási szabályt. Ez a művelet határozza meg, hogy melyik háttérkészlethez irányítsa a kérelmet.

Az alkalmazás-útválasztási szabály alapján az alkalmazásátjáró határozza meg, hogy a figyelőn lévő összes kérelmet egy adott háttérkészlethez, az URL-cím elérési útja alapján különböző háttérkészletekhez irányítsa-e a kérelmeket, vagy átirányítja-e a kérelmeket egy másik portra vagy külső helyre.
>[!NOTE]
>A szabályok feldolgozása abban a sorrendben kerül feldolgozásra, ahogy azok szerepelnek a portálon a v1 Termékváltozat. 

When the application gateway selects the backend pool, it sends the request to one of the healthy backend servers in the pool (y.y.y.y). A kiszolgáló állapotát egy állapotminta határozza meg. Ha a háttérkészlet több kiszolgálót tartalmaz, az alkalmazásátjáró ciklikus multiplexelési algoritmust használ a kérelmek kifogástalan kiszolgálók közötti továbbításához. Ez a terhelés kiegyenlíti a kiszolgálókon lévő kérelmeket.

Miután az alkalmazásátjáró meghatározza a háttérkiszolgálót, http-beállítások alapján új TCP-munkamenetet nyit meg a háttérkiszolgálóval. A HTTP-beállítások határozzák meg azokat a protokoll-, port- és egyéb útválasztási beállításokat, amelyek szükségesek ahhoz, hogy új munkamenetet hozzon létre a háttérkiszolgálóval.

A HTTP-beállításokban használt port és protokoll határozza meg, hogy az alkalmazásátjáró és a háttérkiszolgálók közötti forgalom titkosítva van-e (így végpontok közötti TLS- t valósított meg), vagy titkosítatlan.The port and protocol used in HTTP settings determine whether the traffic between the application gateway and backend servers is encrypted (ezzel accomplishing end-to-end TLS) or is unencrypted.

Amikor egy alkalmazásátjáró elküldi az eredeti kérelmet a háttérkiszolgálónak, tiszteletben tartja az állomásnév, elérési út és protokoll felülbírálására vonatkozó HTTP-beállításokban végzett egyéni konfigurációkat. Ez a művelet fenntartja a cookie-alapú munkamenet-affinitást, a kapcsolat kiürítését, az állomásnév-kiválasztást a háttérrendszerből, és így tovább.

 >[!NOTE]
>Ha a háttérkészlet:
> - **Nyilvános végpont,** az alkalmazásátjáró az előtér nyilvános IP-címét használja a kiszolgáló eléréséhez. Ha nincs előtétrendszeri nyilvános IP-cím, a kimenő külső kapcsolathoz van hozzárendelve.
> - **Belsőleg feloldható teljes tartománynnt vagy privát IP-címet tartalmaz,** az alkalmazásátjáró a példány privát IP-címei vel irányítja a kérelmet a háttérkiszolgálóra.
> - **Külső végpontot vagy külsőleg feloldható teljes tartományhálózatot tartalmaz,** az alkalmazásátjáró a rendszerelőlap nyilvános IP-címével irányítja a kérelmet a háttérkiszolgálóra. A DNS-feloldás egy privát DNS-zónán vagy egyéni DNS-kiszolgálón alapul, ha konfigurálva van, vagy az alapértelmezett Azure által biztosított DNS-t használja. Ha nincs előtétrendszeri nyilvános IP-cím, a kimenő külső kapcsolathoz van hozzárendelve.

### <a name="modifications-to-the-request"></a>A kérelem módosítása

Az alkalmazásátjáró négy további fejlécet szúr be az összes kérelembe, mielőtt továbbítané a kérelmeket a háttérrendszernek. Ezek a fejlécek x-forwarded-for, x-forwarded-proto, x-forwarded-port, és x-original-host. Az x-forwarded-for header formátuma az IP:port vesszővel tagolt listája.

Az x-forwarded-proto érvényes értékei HTTP vagy HTTPS. Az X-forwarded-port azt a portot adja meg, ahol a kérelem elérte az alkalmazásátjárót. Az X-original-host fejléc azt az eredeti állomásfejlécet tartalmazza, amellyel a kérelem érkezett. Ez a fejléc hasznos az Azure-webhely-integráció, ahol a bejövő állomás fejléce módosul, mielőtt a forgalom a háttérrendszerre irányítják. Ha a munkamenet-affinitás engedélyezve van, majd hozzáad egy átjáró által felügyelt affinitáscookie-t.

Az alkalmazásátjáró konfigurálható a fejlécek [módosításához a HTTP-fejlécek újraírásával,](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) vagy az URI elérési út módosítása elérési út felülbírálási beállítással. Azonban, ha nem erre van beállítva, az összes bejövő kérelmek a háttérkapcsolati.

## <a name="next-steps"></a>További lépések

[Tudnivalók az alkalmazásátjáró-összetevőkről](application-gateway-components.md)
