---
title: Az Azure tűzfal integrálása az Azure Standard Load Balancer
description: 'Útmutató: Azure tűzfal integrálása az Azure Standard Load Balancer'
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/1/2019
ms.author: victorh
ms.openlocfilehash: 7ee92a7508918635849caafab4632bbba81ee628
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784823"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Az Azure tűzfal integrálása az Azure Standard Load Balancer

Az Azure-tűzfalak integrálható az Azure Standard Load Balancerhez (nyilvános vagy belső) rendelkező virtuális hálózatban. 

Az előnyben részesített terv, hogy a belső terheléselosztó integrálása az Azure tűzfal, mivel ez egy sokkal egyszerűbb Tervező. Nyilvános load balancer is használhatja, ha már rendelkezik egy telepített, és tartsa helyen szeretne. Azonban szüksége, vegye figyelembe, hogy a nyilvános load balancer forgatókönyv funkciókat, azzal aszimmetrikus útválasztási problémát.

Az Azure Load Balancer kapcsolatos további információkért lásd: [Mi az Azure Load Balancer?](../load-balancer/load-balancer-overview.md)

## <a name="public-load-balancer"></a>Nyilvános load balancer

Nyilvános load balancer, az a load balancer nyilvános előtérbeli IP-címmel van telepítve.

### <a name="asymmetric-routing"></a>Aszimmetrikus útválasztás

Az aszimmetrikus útválasztás, ahol egy csomag vesz igénybe egy elérési útvonalat a cél és a egy másik elérési utat vesz igénybe, amikor visszatér a forrás. Ez a probléma akkor fordul elő, ha egy alhálózathoz van a tűzfal magánhálózati IP-címet fog az alapértelmezett útvonal és a egy nyilvános terheléselosztó használ. Ebben az esetben a load balancer bejövő forgalmat a nyilvános IP-címének keresztül érkezik, de a visszatérési elérési halad végig a tűzfal magánhálózati IP-címet. Mivel a tűzfal állapotalapú, a visszatérő csomag eldobja, mert a tűzfalon még nem ismeri az ilyen munkamenetet létesítenek.

### <a name="fix-the-routing-issue"></a>Az útválasztási problémát

Egy alhálózathoz, az Azure-tűzfalak telepítésekor egy lépéssel az alapértelmezett útvonal keresztül a tűzfal található a AzureFirewallSubnet a privát IP-alhálózat irányítható át csomagok létrehozása. További információkért lásd: [oktatóanyag: Telepítse és konfigurálja az Azure portal segítségével Azure tűzfal](tutorial-firewall-deploy-portal.md#create-a-default-route).

A tűzfal töltsünk a terheléselosztói forgatókönyv, ha azt szeretné, az internetes forgalmat eljussanak a tűzfal nyilvános IP-címet. Itt a tűzfalon a terheléselosztó nyilvános IP-címet a tűzfal-szabályok és NAT a csomagok vonatkozik. Ez az, ha a probléma akkor fordul elő. Csomagok nyilvános IP-címet a tűzfalon az ügyfélszámítógépekre érkeznek, de térjen vissza a tűzfalon keresztül a magánhálózati IP-cím (az alapértelmezett útvonal használatával).
A probléma elkerülése érdekében a tűzfala nyilvános IP-cím további gazdagépcsoport útvonal létrehozása. A tűzfal nyilvános IP-cím küldött csomagok az interneten keresztül továbbítódnak. Ezzel elkerülhető, hogy az alapértelmezett útvonal véve a tűzfal magánhálózati IP-címet.

![Aszimmetrikus útválasztás](media/integrate-lb/Firewall-LB-asymmetric.png)

Például a következő útvonalakat a nyilvános IP-cím 13.86.122.41 és magánhálózati IP-cím 10.3.1.4 tűzfalak vannak.

![Útválasztási táblázat](media/integrate-lb/route-table.png)

## <a name="internal-load-balancer"></a>Belső terheléselosztó

A belső terheléselosztót a terheléselosztó magánhálózati előtérbeli IP-címmel van telepítve.

Ebben a forgatókönyvben az aszimmetrikus útválasztás probléma van. A bejövő csomagok érkeznek a nyilvános IP-címet a tűzfal, első fordítja le a terheléselosztó magánhálózati IP-címet, és adja vissza a tűzfal magánhálózati IP-cím az ugyanazon visszatérési elérési út alapján.

Tehát ebben a forgatókönyvben a nyilvános terheléselosztói forgatókönyv hasonló telepíthet, de nincs szükség a tűzfal nyilvános IP-cím gazdaútvonalat.

## <a name="additional-security"></a>Fokozott biztonság

A fokozott biztonsága érdekében az elosztott terhelésű forgatókönyvben, használhatja a hálózati biztonsági csoportok (NSG-k).

Például létrehozhat egy NSG-t a háttérrendszer alhálózatát, ahol az elosztott terhelésű virtuális gépek találhatók meg. Lehetővé teszik a tűzfal IP-cím és port érkező bejövő forgalmat.

NSG-kkel kapcsolatos további információkért lásd: [biztonsági csoportok](../virtual-network/security-overview.md).

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [üzembe helyezése és konfigurálása az Azure-tűzfalak](tutorial-firewall-deploy-portal.md).