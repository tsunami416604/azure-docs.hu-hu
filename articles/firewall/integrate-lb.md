---
title: Az Azure Firewall integrálása az Azure Standard Load Balancerrel
description: Az Azure-tűzfal integrálható egy virtuális hálózatba egy Azure standard terheléselosztó (nyilvános vagy belső).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/28/2020
ms.author: victorh
ms.openlocfilehash: ab9a500d9535b55702b8baff15f8cc47e6ac2c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196709"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Az Azure Firewall integrálása az Azure Standard Load Balancerrel

Az Azure-tűzfal integrálható egy virtuális hálózatba egy Azure standard terheléselosztó (nyilvános vagy belső). 

Az előnyben részesített kialakítás egy belső terheléselosztó integrálása az Azure tűzfallal, mivel ez egy sokkal egyszerűbb kialakítás. Használhatja a nyilvános terheléselosztó, ha már van egy telepítve, és szeretné tartani a helyén. Azonban tisztában kell lennie egy aszimmetrikus útválasztási probléma, amely megtörheti a funkciót a nyilvános terheléselosztó forgatókönyv.

Az Azure Load Balancer szolgáltatásról a [Mi az Azure Load Balancer című](../load-balancer/load-balancer-overview.md) témakörben talál további információt?

## <a name="public-load-balancer"></a>Nyilvános terheléselosztó

Nyilvános terheléselosztóval a terheléselosztó nyilvános előtér-IP-címmel van telepítve.

### <a name="asymmetric-routing"></a>Aszimmetrikus útválasztás

Az aszimmetrikus útválasztás az, ahol a csomag az egyik útvonalat a célhoz viszi, és egy másik útvonalat vesz igénybe, amikor visszatér a forráshoz. Ez a probléma akkor fordul elő, ha egy alhálózat alapértelmezett útvonala a tűzfal privát IP-címéhez megy, és nyilvános terheléselosztót használ. Ebben az esetben a bejövő terheléselosztó forgalma a nyilvános IP-címén keresztül érkezik, de a visszatérési útvonal a tűzfal privát IP-címén keresztül történik. Mivel a tűzfal állapotalapú, eldobja a visszatérő csomagot, mert a tűzfal nem tud ilyen munkamenetről.

### <a name="fix-the-routing-issue"></a>Az útválasztási probléma megoldása

Amikor egy Azure-tűzfalat telepít egy alhálózatba, egy lépésaz, hogy hozzon létre egy alapértelmezett útvonalat az alhálózat irányítja a csomagokat a tűzfal privát IP-cím található az AzureFirewallSubnet. További információ: [Oktatóanyag: Az Azure Tűzfal telepítése és konfigurálása az Azure Portalhasználatával.](tutorial-firewall-deploy-portal.md#create-a-default-route)

Amikor bevezeti a tűzfalat a terheléselosztó forgatókönyvbe, azt szeretné, hogy az internetes forgalom a tűzfal nyilvános IP-címén keresztül érkezjön meg. Innen a tűzfal tűzfalszabályokat alkalmaz, és nats a csomagokat a terheléselosztó nyilvános IP-címét. Ez az, ahol a probléma jelentkezik. A csomagok a tűzfal nyilvános IP-címére érkeznek, de a privát IP-címen keresztül (az alapértelmezett útvonalon) térnek vissza a tűzfalra.
A probléma elkerülése érdekében hozzon létre egy további állomásútvonalat a tűzfal nyilvános IP-címéhez. A tűzfal nyilvános IP-címére irányuló csomagok az interneten keresztül kerülnek átirányításra. Így elkerülhető, hogy az alapértelmezett útvonalat a tűzfal privát IP-címére irányítsa.

![Aszimmetrikus útválasztás](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Példa útvonaltáblára

A következő útvonalak például a 20.185.97.136 nyilvános IP-címen és a 10.0.1.4 privát IP-címen lévő tűzfalhoz tartoznak.

> [!div class="mx-imgBorder"]
> ![Útvonaltábla](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Példa NAT-szabályra

A következő példában a NAT-szabály a 20.185.97.136-os RDP-forgalmat a 20.42.98.220-as terheléselosztóra fordítja:

> [!div class="mx-imgBorder"]
> ![NAT-szabály](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Állapotminták

Ne feledje, hogy egy webszolgáltatás fut a gazdagépek a terheléselosztó készlet, ha a TCP-állapotmintavételek port 80, vagy HTTP/HTTPS mintavételek.

## <a name="internal-load-balancer"></a>Belső terheléselosztó

Egy belső terheléselosztó, a terheléselosztó egy privát előtér IP-címmel van telepítve.

Ehhez a forgatókönyvhöz nincs aszimmetrikus útválasztási probléma. A bejövő csomagok megérkeznek a tűzfal nyilvános IP-címére, lefordítják a terheléselosztó privát IP-címére, majd visszatérnek a tűzfal privát IP-címére ugyanazzal a visszatérési útvonallal.

Így telepítheti ezt a forgatókönyvet hasonló a nyilvános terheléselosztó forgatókönyv, de anélkül, hogy a tűzfal nyilvános IP-cím gazdaútvonal.

## <a name="additional-security"></a>További biztonság

A terheléselosztásos forgatókönyv biztonságának további növelése érdekében használhatja a hálózati biztonsági csoportokat(NSG)

Létrehozhat például egy NSG-t a háttér-alhálózaton, ahol a terheléselosztásos virtuális gépek találhatók. A tűzfal IP-címéről/portjáról érkező bejövő forgalom engedélyezése.

![Hálózati biztonsági csoport](media/integrate-lb/nsg-01.png)

Az NSG-kről további információt a Biztonsági csoportok című témakörben [talál.](../virtual-network/security-overview.md)

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [telepítheti és konfigurálhatja az Azure-tűzfalat.](tutorial-firewall-deploy-portal.md)