---
title: Azure-beli Application Gateway-ILB Microsoft Docs
description: Megtudhatja, hogyan végezhet hibakeresést egy Application Gateway használatával egy belső Load Balancer az Azure-ban App Service Environment
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
ms.openlocfilehash: 9c3216af283ebd9d84a5469d4d50d18c19f67534
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121954"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>A háttér-kiszolgálói tanúsítvány nem engedélyezett olyan Application Gateway számára, amely egy belső Load Balancert használ App Service Environment

Ez a cikk az alábbi problémát ismerteti: A tanúsítvány nem engedélyezett, ha egy belső Load Balancer (ILB) és egy App Service Environment (bevezető) használatával hoz létre egy olyan alkalmazás-átjárót, amely végpontok közötti SSL-t használ az Azure-ban.

## <a name="symptoms"></a>Probléma

Amikor egy ILB használatával hoz létre egy Application Gateway-t, a háttérrendszer a háttér-kiszolgálóval is megsérült. Ez a probléma akkor fordul elő, ha az Application Gateway hitelesítési tanúsítványa nem egyezik meg a háttér-kiszolgálón konfigurált tanúsítvánnyal. Példaként tekintse meg a következő forgatókönyvet:

**Application Gateway konfiguráció:**

- **Hallgató** Több hely
- **Port** 443
- **Állomásnév:** test.appgwtestase.com
- **SSL-tanúsítvány:** CN=test.appgwtestase.com
- **Háttér-készlet:** IP-cím vagy FQDN
- **IP-cím:** : 10.1.5.11
- **HTTP-beállítások:** HTTPS
- **Port:** : 443
- **Egyéni mintavétel:** Állomásnév – test.appgwtestase.com
- **Hitelesítési tanúsítvány:** . cer – test.appgwtestase.com
- **Háttér állapota:** Nem megfelelő – a háttér-kiszolgáló tanúsítványa nincs engedélyezve a Application Gateway.

**Bemutató konfiguráció:**

- **ILB IP-CÍM:** 10.1.5.11
- **Tartománynév:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **SSL-kötés:** SNI SSL – CN=test.appgwtestase.com

Az Application Gateway eléréséhez a következő hibaüzenet jelenik meg, mert a háttér-kiszolgáló állapota nem kifogástalan:

**502 – a webkiszolgáló érvénytelen választ kapott az átjáróként vagy proxykiszolgálóként való működés közben.**

## <a name="solution"></a>Megoldás

Ha nem használ állomásnévt egy HTTPS-webhely eléréséhez, a háttér-kiszolgáló az alapértelmezett webhelyen visszaadja a konfigurált tanúsítványt, abban az esetben, ha a SNI le van tiltva. Egy ILB-benyújtó esetében az alapértelmezett tanúsítvány az ILB-tanúsítványból származik. Ha nincsenek konfigurált tanúsítványok a ILB, a tanúsítvány a benyújtó alkalmazás-tanúsítványból származik.

Ha teljes tartománynevet (FQDN) használ a ILB eléréséhez, a háttér-kiszolgáló a HTTP-beállításokban feltöltött megfelelő tanúsítványt fogja visszaadni. Ha ez nem igaz, vegye figyelembe a következő lehetőségeket:

- Használja a teljes tartománynevet az Application Gateway háttér-készletében, hogy a ILB IP-címére mutasson. Ez a beállítás csak akkor működik, ha rendelkezik saját DNS-zónával vagy egyéni DNS-kiszolgálóval. Ellenkező esetben létre kell hoznia egy "A" rekordot a nyilvános DNS-hez.

- Használja a feltöltött tanúsítványt a ILB vagy az alapértelmezett tanúsítványon (ILB-tanúsítványon) a HTTP-beállítások között. Az Application Gateway lekéri a tanúsítványt, amikor hozzáfér a ILB IP-címéhez a mintavételhez.

- Használjon helyettesítő tanúsítványt a ILB és a háttér-kiszolgálón, hogy az összes webhelyhez a tanúsítvány gyakori legyen. Ez a megoldás azonban csak altartományok esetén lehetséges, és nem, ha az egyes webhelyekhez eltérő állomásnevek szükségesek.

- Ha a ILB IP-címét használja, törölje az **app Service-hez való használat** beállítását az Application Gateway esetében.

A terhelés csökkentése érdekében feltöltheti a ILB-tanúsítványt a HTTP-beállításokba a mintavételi útvonal működésének érdekében. (Ez a lépés csak a Whitelisting számára érhető el. Az SSL-kommunikációhoz nem használható.) A ILB-tanúsítvány lekéréséhez nyissa meg az IP-címét a ILB a HTTPS-en keresztül, majd exportálja az SSL-tanúsítványt egy Base-64 kódolású CER formátumba, és töltse fel a tanúsítványt a megfelelő HTTP-beállításokra.

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
