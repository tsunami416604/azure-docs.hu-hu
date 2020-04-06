---
title: Alkalmazásátjáró – ILB ASE – alkalmazásátjáró hibáinak elhárítása | Microsoft dokumentumok
description: Megtudhatja, hogyan háríthatja el az alkalmazásátjárót belső terheléselosztó használatával az Azure-beli App Service-környezettel
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 4edeea749ba22bef173c15f3a0855679b784ce33
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668564"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>A háttérkiszolgálói tanúsítvány nem szerepel az alkalmazásátjáró engedélyezési listáján, amely egy alkalmazásszolgáltatás-környezettel rendelkező belső terheléselosztót használ

Ez a cikk a következő problémát hárítja el: A tanúsítvány nem szerepel a listán, amikor belső terheléselosztó (ILB) használatával hoz létre egy alkalmazásátjárót egy app service-környezettel (ASE) együtt az Azure-beli végpontok közötti TLS használatával.

## <a name="symptoms"></a>Probléma

Ha egy alkalmazásátjáró t a háttérrendszer egy ASE-vel egy ASE használatával hoz létre, a háttérkiszolgáló nem megfelelő állapotúvá válhat. Ez a probléma akkor fordul elő, ha az alkalmazásátjáró hitelesítési tanúsítványa nem egyezik meg a háttérkiszolgálón konfigurált tanúsítvánnyal. Példaként a következő forgatókönyv:

**Alkalmazásátjáró konfigurációja:**

- **Hallgató:** Többoldalas
- **Kikötő:** 443
- **Állomásnév:** test.appgwtestase.com
- **SSL-tanúsítvány:** CN=test.appgwtestase.com
- **Háttérkészlet:** IP-cím vagy teljes tartományna
- **IP-cím:**: 10.1.5.11
- **HTTP-beállítások:** HTTPS
- **Kikötő:** 443
- **Egyéni mintavétel:** Hostname – test.appgwtestase.com
- **Hitelesítési tanúsítvány:** test.appgwtestase.com .cer
- **Háttérállapot:** Nem kifogástalan – a háttérkiszolgáló tanúsítványa nem szerepel az Application Gateway listáival.

**ASE konfiguráció:**

- **ILB IP cím:** 10.1.5.11
- **Domain név:** appgwtestase.com
- **Alkalmazásszolgáltatás:** test.appgwtestase.com
- **SSL kötés:** SNI SSL – CN=test.appgwtestase.com

Az alkalmazásátjáró elérésekor a következő hibaüzenet jelenik meg, mert a háttérkiszolgáló nem kifogástalan:

**502 – A webkiszolgáló érvénytelen választ kapott, miközben átjáróként vagy proxykiszolgálóként működt.**

## <a name="solution"></a>Megoldás

Ha nem használ állomásnevet egy HTTPS-webhely eléréséhez, a háttérkiszolgáló visszaadja a konfigurált tanúsítványt az alapértelmezett webhelyen, ha az SNI le van tiltva. Az ILB ASE esetében az alapértelmezett tanúsítvány az ILB-tanúsítványból származik. Ha az ILB-hez nincsenek konfigurált tanúsítványok, a tanúsítvány az ASE alkalmazástanúsítványból származik.

Ha teljesen minősített tartománynevet (FQDN) használ az ILB eléréséhez, a háttérkiszolgáló a HTTP-beállításokban feltöltött megfelelő tanúsítványt adja vissza. Ellenkező esetben vegye figyelembe a következő lehetőségeket:

- Az alkalmazásátjáró háttérkészletében használja az FQDN-t az ILB IP-címére való rámutatáshoz. Ez a beállítás csak akkor működik, ha saját DNS-zónával vagy egyéni DNS-sel van konfigurálva. Ellenkező esetben létre kell hoznia egy "A" rekordot egy nyilvános DNS-hez.

- Használja a feltöltött tanúsítványt az ILB-n vagy az alapértelmezett tanúsítványon (ILB-tanúsítvány) a HTTP-beállításokban. Az alkalmazásátjáró lekéri a tanúsítványt, amikor hozzáfér az ILB IP-a mintavétel.

- Használjon helyettesítő tanúsítványt az ILB-n és a háttérkiszolgálón, hogy az összes webhely esetében a tanúsítvány közös. Ez a megoldás azonban csak aldomainek esetén lehetséges, és nem akkor, ha mindegyik webhely különböző állomásneveket igényel.

- Törölje a törlést az **alkalmazásátjáró alkalmazásszolgáltatáshoz** beállításának jelölőnégyzetből arra az esetre, ha az ILB IP-címét használná.

A terhelés csökkentése érdekében feltöltheti az ILB-tanúsítványt a HTTP-beállításokban, hogy a mintavételi útvonal működjön. (Ez a lépés csak az engedélyezési listára. Nem lesz használva a TLS kommunikációhoz.) Az ILB-tanúsítványt úgy töltheti le, hogy az ILB-t az IP-címével együtt https-alapú böngészőből éri el, majd a TLS/SSL tanúsítványt Base-64 kódolású CER formátumban exportálja, és feltölti a tanúsítványt a megfelelő HTTP-beállításokra.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
