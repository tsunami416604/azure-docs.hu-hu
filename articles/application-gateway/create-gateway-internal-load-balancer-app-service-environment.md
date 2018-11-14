---
title: Azure-ban – ILB ASE környezetben lévő Application Gateway hibaelhárítása |} A Microsoft Docs
description: Kapcsolatos problémák elhárítása az application gateway az App Service Environment Azure egy belső Load Balancer használatával
services: vpn-gateway
documentationCenter: na
author: genlin
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 16cfe4c1db8fe9ba4c80f6451611237e3ee12c55
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617880"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Háttérkiszolgáló-tanúsítvány nem szerepel az engedélyezési listán az application Gateway egy belső terheléselosztó használata App Service-környezet

Ez a cikk hibaelhárítást végez a következő problémaleírást: A tanúsítvány nem szerepel az engedélyezési listán, egy application gateway használatával egy belső Load Balancer (ILB) egy App Service Environment (ASE) együtt a, a háttérben, ha a végpontok közötti SSL-lel az Azure-ban való létrehozásakor.

## <a name="symptoms"></a>Probléma

A háttérben, az ASE ILB segítségével létrehozott application gateway, a háttér-kiszolgáló nem megfelelő állapotú válhat. Ez a probléma akkor fordul elő, ha az application Gateway-hitelesítési tanúsítvány nem felel meg a konfigurált tanúsítvánnyal a háttér-kiszolgálón. Példaként tekintse meg az alábbi forgatókönyvet:

**Application Gateway-konfigurációt:**

- **Figyelő:** többhelyes
- **Port:** 443-as porton
- **Állomásnév:** test.appgwtestase.com
- **SSL-tanúsítvány:** CN=test.appgwtestase.com
- **Háttérkészlet:** IP-cím vagy FQDN
- **IP-cím:**: 10.1.5.11
- **HTTP-beállítások:** HTTPS
- **Port:**: 443
- **Egyéni mintavétel:** állomásnév – test.appgwtestase.com
- **Hitelesítési tanúsítvány:** test.appgwtestase.com, .cer
- **Háttérkiszolgáló állapotadatainak:** nem megfelelő – háttérrendszer kiszolgálótanúsítvány nem szerepel az engedélyezési listán az Application Gateway szolgáltatással.

**ASE konfigurálása:**

- **ILB IP-CÍME:** 10.1.5.11
- **Tartománynév:** appgwtestase.com
- **App Service-ben:** test.appgwtestase.com
- **SSL-kötés:** SNI SSL – CN=test.appgwtestase.com

Az application gateway használatakor jelenhet meg a következő hibaüzenetet kapja, mert a háttér-kiszolgáló nem megfelelő állapotban:

**502 – webkiszolgáló érvénytelen választ kapott-átjáróval vagy proxyval-kiszolgálóként.**

## <a name="solution"></a>Megoldás

Ha egy HTTPS-webhely eléréséhez állomásnév nem használja, a háttérkiszolgáló-visszaküldi a konfigurált tanúsítvánnyal az alapértelmezett webhelyen. Az ILB ASE környezetben az alapértelmezett tanúsítvány származik az ILB-tanúsítvány. Ha nincs konfigurált tanúsítványok az ILB-hez, a tanúsítványt az ASE App tanúsítvány származik.

Az ILB eléréséhez egy teljesen minősített tartománynevét (FQDN) használatakor a háttérkiszolgáló-a HTTP-beállítások a megfelelő tanúsítvány feltöltött fog visszaadása. Ebben az esetben fontolja meg a következő beállításokat:

- Az application Gateway háttérkészlet teljes tartománynév használatával az ilb IP-címre mutasson. Ez a beállítás csak akkor működik, ha privát DNS-zónák vagy egy egyéni DNS konfigurálva. Ellenkező esetben kell az "A" rekord létrehozása egy nyilvános DNS-ben.

- A feltöltött tanúsítvány használata az ILB vagy a HTTP-beállítások az alapértelmezett tanúsítvány. Az application gateway lekéri a tanúsítványt, az ILB IP a mintavétel elérésekor.

- Helyettesítő karakteres tanúsítványt használjon az ILB-és a háttér-kiszolgálón.

- Törölje a **használata az App service** lehetőség az application gateway számára.

Terhelés csökkentése érdekében, tölthet fel a HTTP-beállítások, hogy a mintavétel elérési útjának működnek az ILB-tanúsítvány. (Ezt a lépést, és az engedélyezés érték. Ez nem használható SSL-kommunikáció.) Az ILB IP-címének HTTPS-elérésével kérheti le az ILB-tanúsítvány, majd exportálása az SSL-tanúsítványt egy Base-64 kódolású CER formátumban és feltölteni a tanúsítványt a megfelelő HTTP-beállítások.

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
