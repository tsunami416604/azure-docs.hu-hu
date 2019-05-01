---
title: Egy application gateway működése
description: Ez a cikk ismerteti egy application gateway működése
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: a16421182f533f5aa2ad4bcc2e58e910cc7e8ca6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702415"
---
# <a name="how-an-application-gateway-works"></a>Egy application gateway működése

Ez a cikk bemutatja, hogyan egy application gateway elfogadja a bejövő kérelmeket, és továbbítja a háttérrendszert.

![Hogyan egy application gateway elfogadja a kérelmet](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Hogyan egy application gateway elfogadja a kérelmet

1. Egy ügyfél kérést küld az application gateway, mielőtt a tartománynév, az application Gateway feloldja a tartománynévrendszer (DNS) kiszolgáló használatával. Az Azure DNS-bejegyzés szabályozza, mivel az összes application Gateway átjárók az azure.com tartomány.

2. Az Azure DNS az IP-címet az ügyfélnek, amely az előtérbeli IP-cím, az application Gateway adja vissza.

3. Az application gateway egy vagy több figyelői a bejövő forgalmat fogad el. Egy figyelő olyan logikai entitás, amely ellenőrzi a kapcsolódási kérelmeket. Egy előtérbeli IP-cím, protokoll és port számát, az application Gateway-kapcsolatokat az ügyfelektől van konfigurálva.

4. Webalkalmazási tűzfal (WAF) használatban van, ha az application gateway ellenőrzi a kérelemfejléc és a szervezet, ha van ilyen, WAF-szabályok alapján. Ez a művelet meghatározza, hogy a kérelem kérés vagy a biztonsági fenyegetéseket. Ha a kérés érvényes, a háttérrendszer lesz irányítva. A kérés nem érvényes, ha azt le van tiltva, a biztonsági fenyegetéseket.

Az Azure Application Gateway is használható, az alkalmazás belső load balancerhez, vagy egy internetkapcsolattal rendelkező application load balancert. Internetkapcsolattal rendelkező application gateway nyilvános IP-címet használ. Internetkapcsolattal rendelkező application gateway DNS-neve nyilvánosan feloldható a nyilvános IP-címére. Ennek eredményeképpen az internetkapcsolattal rendelkező application Gateway átjárókon is irányítania az ügyfélkérelmeket az internethez.

Belső application Gateway átjárók csak magánhálózati IP-címek használata. A belső application gateway DNS-neve nyilvánosan feloldható a magánhálózati IP-címére. Ezért belső terheléselosztók csak irányíthatja az application gateway egy virtuális hálózathoz hozzáféréssel rendelkező ügyfelek kéréseit.

Internetkapcsolattal rendelkező, mind a belső alkalmazás átjárók háttérkiszolgálók magánhálózati IP-címek használatával irányíthatja a kérelmeket. Háttérkiszolgálók fogadásához egy belső érkező kérelmeket, vagy egy internetkapcsolattal rendelkező application gateway nyilvános IP-címek nem szükséges.

## <a name="how-an-application-gateway-routes-a-request"></a>Hogyan irányítja az application gateway a kérelem

Ha egy kérés érvényességét, vagy egy WAF nem használt, az application gateway a kérés útválasztási szabályt, a figyelő társított értékeli ki. Ez a művelet irányítsa át a kérést, hogy melyik háttérkészlet határozza meg.

Szabályok felsorolás a portálon a rendelés feldolgozása. A kérelem-útválasztási szabály alapján, az application gateway határozza meg, útvonal kéri, hogy különböző háttérkészletek alapján a következő URL-cím vagy egy másik portra kérelmek átirányítása vagy külső a figyelő egy adott háttérkészlethez, az összes kérelem átirányítása hely.

Amikor az application gateway háttérkészlet választja, azt a kérést küld a készletben (y.y.y.y) megfelelően működő háttér-kiszolgálók valamelyikével. Az állapotfigyelő mintavételező határozza meg a kiszolgáló állapotát. Ha a háttérkészlet több kiszolgálót tartalmaz, az application gateway Ciklikus időszeleteléses algoritmust használ irányíthatja a kérelmeket, kifogástalan állapotú kiszolgálók között. Ez elosztja a kérelmeket a kiszolgálókon.

Az application gateway határozza meg a háttérkiszolgáló, megnyílik egy új TCP-munkamenet a HTTP-beállításai alapján a háttér-kiszolgálóval. HTTP-beállítások megadása a protokoll, port és más útválasztás kapcsolatos beállításokat, hozzon létre egy új munkamenetet a háttérkiszolgáló szükséges.

A port és protokoll, a HTTP-beállítások határozza meg, hogy az alkalmazás átjáró és a háttérkiszolgáló-kiszolgálók közötti forgalom titkosítva van (tehát ennek a végpontok közötti SSL) vagy titkosítatlan.

Egy application gateway és a háttérkiszolgáló az eredeti kérést küld, ha a HTTP-beállítások felülbírálása a gazdagépnév, az elérési út és a protokoll kapcsolatos bármilyen egyéni konfigurációt figyelembe veszi. Ez a művelet fenntartja a cookie-alapú munkamenet-affinitást, a kapcsolat kiürítési, állomásnév-kijelölés a háttér, és így tovább.

Belső application gateway csak magánhálózati IP-címeket használ. Belső application gateway DNS neve nem oldható fel a magánhálózati IP-címére. Ennek eredményeképpen belső terheléselosztók is csak irányíthatja a kérelmeket a hozzáféréssel rendelkező ügyfelek számára a virtuális hálózatot az application gateway számára.

 >[!NOTE]
 >Mindkét internetkapcsolattal rendelkező és a belső application Gateway átjárók irányíthatja a kérelmeket háttérkiszolgálók magánhálózati IP-címek használatával. Ez a művelet történik, ha a a készlet háttérerőforrásra magánhálózati IP-cím, virtuális gép hálózati Adapteres konfiguráció vagy egy belső feloldható címet tartalmaz. Ha a háttérkiszolgáló-készlet:
> - **Van egy nyilvános végpontot**, az application gateway az előtérbeli nyilvános IP-címet használ a kiszolgáló eléréséhez. Ha nincs előtérbeli nyilvános IP-cím, egy kimenő külső csatlakoztatásához van hozzárendelve.
> - **Egy belső feloldható teljes tartománynévnek, vagy egy magánhálózati IP-címet tartalmaz**, az application gateway a kérelmet irányítja a háttérkiszolgálón a példány magánhálózati IP-címek használatával.
> - **Külső végpont és a egy külsőleg feloldható teljes tartalmaz**, az application gateway a kérelmet irányítja a háttérkiszolgáló az előtérbeli nyilvános IP-cím használatával. A DNS-feloldás alapján egy saját DNS-zóna vagy egyéni DNS-kiszolgálót, ha konfigurálva, vagy használja az alapértelmezett Azure által biztosított DNS. Ha nincs előtérbeli nyilvános IP-cím, egy kimenő külső csatlakoztatásához van hozzárendelve.

### <a name="modifications-to-the-request"></a>A kérelem módosításai

Egy application gateway összes kérésre négy további fejlécek beszúrása, továbbítja a kéréseket a háttér előtt. Ezek a fejlécek: x – továbbított – a, x továbbított proto, x-továbbított-port és x-eredeti-gazdagép. Az x-továbbított – a fejléc formátuma IP:port vesszővel tagolt listája.

Az x továbbított proto érvényes értékei a következők: HTTP vagy HTTPS PROTOKOLLT. X továbbított port meghatározza azt a portot, amelyen a kérelem elérte-e az application gateway. X eredeti állomásfejlécet tartalmaz az eredeti állomásfejlécet, amelyhez a kérés érkezett. Ezt a fejlécet hasznos az Azure-webhelyen integráció, ahol a bejövő állomásfejléc módosította-e előtt az adatforgalmat, a háttérkiszolgáló. Ha a beállítás engedélyezve van a munkamenet-affinitást, majd hozzáadja egy átjáró által kezelt affinitási cookie-k.

Konfigurálhatja az Alkalmazásátjáró fejlécek módosítása használatával [Újraírási HTTP-fejlécek](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) vagy az URI elérési út módosítása egy elérési út-felülírási beállítás használatával. Azonban, ha úgy konfigurálva, az összes beérkező webszolgáltatásokhoz használják proxyként a háttérrendszer.

## <a name="next-steps"></a>További lépések

[Application gateway összetevőinek ismertetése](application-gateway-components.md)
