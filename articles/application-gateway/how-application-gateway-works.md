---
title: Azure Application Gateway főbb jellemzői
description: Ez a cikk információt nyújt az Application Gateway Works
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: d37114fda7f442a5fa077c8dde9fd8aec3ac4378
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317101"
---
# <a name="how-application-gateway-works"></a>Az Application Gateway működése

Ez a cikk bemutatja, hogyan az application gateway elfogadja a bejövő kéréseket, és továbbítja a háttérrendszerhez.

![how-application-gateway-works](.\media\how-application-gateway-works\how-application-gateway-works.png)

## <a name="how-request-is-accepted"></a>Hogyan kérelem elfogadása

Egy ügyfél kérelmet küld az application gateway, mielőtt az application gateway tartománynév tartománynévrendszer (DNS) kiszolgáló használatával oldja fel. Mivel az application Gateway átjárók az azure.com tartomány szabályozza Azure-ban, a DNS-bejegyzést. Az Azure DNS az ügyfélnek, amely az IP-címet adja vissza a *előtérbeli IP-cím* az Application Gateway. Az application gateway fogadja a bejövő forgalmat egy vagy több *figyelői*. Egy figyelő olyan logikai entitás, amely ellenőrzi a kapcsolódási kérelmeket. Egy fronted IP-cím, protokoll és port számát, az application Gateway-kapcsolatokat az ügyfelektől van konfigurálva. Webalkalmazási tűzfal (WAF) engedélyezve van, ha az Application Gateway ellenőrzi a kérelemfejlécek és a szervezet (ha van ilyen) szemben a *WAF-szabályok* annak megállapításához, hogy a kérelem kérés - ebben az esetben azt a rendszer átirányítja a háttér – vagy biztonsági fenyegetéseket, amelyben le lesz tiltva a kérelem eset.  

Az Application gateway is használható az alkalmazás belső load balancerhez vagy egy internetkapcsolattal rendelkező application load balancert. Internetkapcsolattal rendelkező application gateway nyilvános IP-címmel rendelkezik. Internetkapcsolattal rendelkező application gateway DNS-neve nyilvánosan feloldható a nyilvános IP-címére. Ezért az internetkapcsolattal rendelkező application Gateway átjárókon is irányíthatja az ügyfelektől érkező kérelmek az interneten keresztül. Belső application Gateway átjárók csak magánhálózati IP-címmel rendelkezik. A belső application gateway DNS-neve nyilvánosan feloldható a magánhálózati IP-címére. Ezért belső terheléselosztók csak irányíthatja az application gateway számára a virtuális hálózathoz hozzáféréssel rendelkező ügyfelek kéréseit. Internetkapcsolattal rendelkező, mind belső, az Application Gateway átjárók a háttérkiszolgálók, privát IP-címek használatával irányíthatja a kérelmeket. A háttérkiszolgálók, ezért nem kell kéréseket fogadni egy belső és a egy internetkapcsolattal rendelkező Application Gateway nyilvános IP-címeket.

## <a name="how-request-is-routed"></a>Hogyan irányítja a kérelmeket

Ha a kérés, hogy érvényesek legyenek található (vagy ha a WAF nincs engedélyezve), a *kérelemirányítási szabály* társított a *figyelő* meghatározásához abban az esetben a *háttérkészlet* , amely a kérés át szeretné irányítani. A szabályok abban a sorrendben vannak feldolgozva, amelyben a portálon szerepelnek. Alapján a *kérelemirányítási szabály* konfigurálása az application gateway úgy dönt, hogy a kérelmek átirányítása a figyelőn egy adott háttérkészlethez, vagy irányíthatja őket a különböző háttérkészletek függően az URL-címet vagy a *kérelmek átirányítása a* egy másik portra vagy külső helyre

Egyszer egy *háttérrendszer* *készlet* van kiválasztva, az application gateway egy konfigurálva a készlethez, hogy kifogástalan-e a háttérkiszolgálók (y.y.y.y) elküldi a kérelmet. Határozza meg a kiszolgáló állapotát egy *állapotadat-mintavétel*. Ha a háttérkészlet több kiszolgálót tartalmaz, a az application gateway irányíthatja a kérelmeket a kifogástalan állapotú kiszolgálók között, így terheléselosztást a kérelmek a kiszolgálókon a ciklikus időszeletelés algoritmust használ.

A háttérkiszolgáló meghatározása után az application gateway a háttérkiszolgáló, a konfiguráció alapján a megnyílik egy új TCP-munkamenet az *HTTP-beállítások*. A *HTTP-beállítások* egy összetevő, amely meghatározza a protokoll, port és más útválasztás kapcsolatos nem beállítását, amelyre szükség az új munkamenetet a háttérkiszolgáló. A port és protokoll, a HTTP-beállítások az határozza meg, hogy az alkalmazás átjáró és a háttérkiszolgáló-kiszolgálók közötti forgalom titkosítva van, így teljes körű SSL megvalósítására, vagy titkosítás nélkül. Az eredeti kérést a háttérkiszolgálónak küldésekor az application gateway figyelembe veszi a HTTP-beállítások felülbírálása az állomásnév, elérési út, protokoll; kapcsolatos bármilyen egyéni konfigurációt fenntartása cookie-alapú munkamenet-affinitást,-kapcsolatának kiürítéséhez, kiválasztotta a megadott állomás nevét a háttérrendszer stb.

Belső Application Gateway csak magánhálózati IP-címmel rendelkezik. Egy belső Application Gateway DNS-nevét a belső oldható fel a magánhálózati IP-címére. Ezért belső terheléselosztók csak irányíthatja a virtuális hálózathoz hozzáféréssel rendelkező ügyfelek kéréseit az Application Gateway számára.

Vegye figyelembe, hogy az Internet felé néző mind a belső Application Gateway átjárók irányíthatja a kérelmeket a háttérkiszolgálók, privát IP-címek használatával. Ha a készlet háttérerőforrásra magánhálózati IP-cím, virtuális gép hálózati Adapteres konfiguráció vagy egy belső feloldható címet tartalmaz, és a egy nyilvános végpontot a háttérkészlet esetén az Application Gateway az előtérbeli nyilvános IP-címet használ, a kiszolgáló eléréséhez. Előtérbeli nyilvános IP-cím még nincs kiépítve, ha egy kimenő külső csatlakoztatásához van hozzárendelve.


## <a name="next-steps"></a>További lépések

Miután megismerkedett az application gateway működéséről, tekintse meg a [Application gateway összetevők](application-gateway-components.md) tudni, hogy annak összetevői részletesebben ismertetik.
