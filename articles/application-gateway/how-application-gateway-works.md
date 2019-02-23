---
title: Azure Application Gateway főbb jellemzői
description: Ez a cikk információt nyújt az Application Gateway Works
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: d44bb4d3d0ff625d071c87fd39b3776915a6198f
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675807"
---
# <a name="how-application-gateway-works"></a>Az Application Gateway működése

Application Gateway-példány egy felhőalapú Alkalmazáskézbesítési vezérlőt az (HTTP) 7. rétegbeli terheléselosztási, amely lehetővé teszi a webes forgalom kezelése az összes kiszolgálón. Emellett a webes szolgáltatások a gyakori webes támadások és biztonsági rések központi védelmet biztosító webalkalmazási tűzfal (WAF) lehetőségeket is kínál.

Amikor egy ügyfél HTTP-kérést küld az Application Gateway, fordított proxyként funkcionál, és továbbítja a forgalmat a háttérkiszolgálók, a konfiguráció alapján. Az Application Gateway emellett is a háttérkiszolgálók állapotát figyeli, és biztosítja, hogy irányítja a forgalmat csak a kifogástalan állapotú háttérrendszereket.

![how-application-gateway-works](.\media\how-application-gateway-works\how-application-gateway-works.png)

## <a name="how-request-is-accepted"></a>Hogyan kérelem elfogadása

Egy ügyfél kérelmet küld az Application Gateway, mielőtt az Application Gateway tartománynév tartománynévrendszer (DNS) kiszolgáló használatával oldja fel. Mivel az Application Gateway átjárók az azure.com tartomány szabályozza Azure-ban, a DNS-bejegyzést. Az Azure DNS az ügyfélnek, amely az IP-címet adja vissza a *előtérbeli IP-cím* az Application Gateway. Az Application Gateway fogadja a bejövő forgalmat egy vagy több *figyelői*. Egy figyelő olyan folyamat, ellenőrzi a kapcsolódási kérelmeket. Egy fronted IP-cím, protokoll és port számát, az Application Gateway-kapcsolatokat az ügyfelektől van konfigurálva. Ha engedélyezve van a WAF, az Application Gateway ellenőrzi, a kérelem fejlécében és a szervezet (ha van ilyen) szemben a *WAF-szabályok* annak megállapításához, hogy a kérelem kérés - ebben az esetben lesz átirányítva a háttér - vagy egy biztonsági fenyegetés található a kérelem esetben le lesz tiltva.  

## <a name="how-request-is-routed"></a>Hogyan irányítja a kérelmeket

Ha a kérés, hogy érvényesek legyenek található (vagy ha a WAF nincs engedélyezve), a *kérelemirányítási szabály* társított a *figyelő* meghatározásához abban az esetben a *háttérkészlet* , amely a kérés át szeretné irányítani. A szabályok abban a sorrendben vannak feldolgozva, amelyben a portálon szerepelnek. Alapján a *kérelemirányítási szabály* konfigurálása az Application Gateway úgy dönt, hogy a kérelmek átirányítása a figyelőn egy adott háttérkészlethez, vagy irányíthatja őket a különböző háttérkészletek függően az URL-címet vagy a *kérelmek átirányítása a* egy másik portra vagy külső helyre

Egyszer egy *háttérrendszer* *készlet* van kiválasztva, az Application Gateway egy konfigurálva a készlethez, hogy kifogástalan-e a háttérkiszolgálók (y.y.y.y) elküldi a kérelmet. Határozza meg a kiszolgáló állapotát egy *állapotadat-mintavétel*. A háttérkészlet egynél több kiszolgálót tartalmaz, ha az Application Gateway irányíthatja a kérelmeket a kifogástalan állapotú kiszolgálók között, így terheléselosztást a kérelmek a kiszolgálókon a ciklikus időszeletelés algoritmust használja.

A háttérkiszolgáló meghatározása után az Application Gateway a háttérkiszolgáló, a konfiguráció alapján a megnyílik egy új TCP-munkamenet az *HTTP-beállítások*. A *HTTP-beállítások* egy összetevő, amely meghatározza a protokoll, port és más útválasztás kapcsolatos nem beállítását, amelyre szükség az új munkamenetet a háttérkiszolgáló. A port és protokoll, a HTTP-beállítások az határozza meg, hogy az Application Gateway és a háttérkiszolgálók közötti adatforgalom titkosítva van, így teljes körű SSL megvalósítására, vagy titkosítás nélkül. Az eredeti kérést a háttérkiszolgálónak küldésekor az Application Gateway figyelembe veszi a HTTP-beállítások felülbírálása az állomásnév, elérési út, protokoll; kapcsolatos bármilyen egyéni konfigurációt fenntartása cookie-alapú munkamenet-affinitást,-kapcsolatának kiürítéséhez, kiválasztotta a megadott állomás nevét a háttérrendszer stb.

Az Application gateway is szúr be a következő három alapértelmezett HTTP * fejlécek: x – továbbított – a x továbbított proto és x-továbbított-port, a háttérrendszere továbbítja a kérést.

Ha a háttérkiszolgáló feldolgozza a kérést, és elküldi az Application Gateway HTTP-válaszban az oldal tartalmát, az Application Gateway továbbítja a válasz az ügyfélhez, ahol a lapot a böngészőben megjelenik.

## <a name="application-load-balancing-type"></a>Alkalmazás terheléselosztó típusa

Az Application Gateway egy alkalmazás belső terheléselosztót vagy az Internet felé néző application load balancerhez is használhatja. Internetkapcsolattal rendelkező Application Gateway nyilvános IP-címmel rendelkezik. Internetkapcsolattal rendelkező Application Gateway DNS-neve nyilvánosan feloldható a nyilvános IP-címére. Ezért az internetkapcsolattal rendelkező Application Gateway átjárókon is irányíthatja az ügyfelektől érkező kérelmek az interneten keresztül.

Belső Application Gateway csak magánhálózati IP-címmel rendelkezik. A belső Application Gateway DNS-neve nyilvánosan feloldható a magánhálózati IP-címére. Ezért belső terheléselosztók csak irányíthatja a virtuális hálózathoz hozzáféréssel rendelkező ügyfelek kéréseit az Application Gateway számára.

Vegye figyelembe, hogy az Internet felé néző mind a belső Application Gateway átjárók irányíthatja a kérelmeket a háttérkiszolgálók, privát IP-címek használatával. A háttérkiszolgálók, ezért nem kell kéréseket fogadni egy belső és a egy internetkapcsolattal rendelkező Application Gateway nyilvános IP-címeket.

## <a name="next-steps"></a>További lépések

Miután megismerkedett az Application Gateway működésével, nyissa meg [Application Gateway összetevők](application-gateway-components.md) tudni, hogy annak összetevői részletesebben ismertetik.
