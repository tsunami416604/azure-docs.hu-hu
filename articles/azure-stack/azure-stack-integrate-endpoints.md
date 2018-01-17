---
title: "Azure datacenter integráció a verem - végpontok közzététele"
description: "Útmutató: Azure verem végpontok közzététele az adatközpontban található"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 01/16/2018
ms.author: victorh
keywords: 
ms.openlocfilehash: 1cc74cb2214918d6bfd0c0827cf5d9832b84f317
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure datacenter integráció a verem - végpontok közzététele

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Az Azure verem különböző végpontok (VIP - virtuális IP-címek) az infrastruktúra-szerepkörök beállítása. A virtuális IP-címek a nyilvános IP-címkészletből foglal le. A szoftver által meghatározott hálózati rétegben hozzáférés-vezérlési listaként (ACL) minden egyes VIP védi. Hozzáférés-vezérlési listákat a megoldás további támogatnia kell a is használhatók a fizikai kapcsolók (TORs és bmc-n) keresztül. A DNS-bejegyzés jön létre a külső DNS-zónában, a központi telepítéskor megadott végpontok.


A következő architekturális ábrán az látható, a különböző hálózati rétegek és a hozzáférés-vezérlési listák:

![Architekturális diagramja](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Portok és protokollok (bejövő)

A külső hálózatokat közzétételi Azure verem végpontok szükséges infrastruktúra VIP az alábbi táblázatban felsorolt. A listán látható minden egyes végpont, a szükséges portot és protokollt. További erőforrás-szolgáltató, például az erőforrás-szolgáltató SQL és más, a szükséges végpontok ismertetnek az adott erőforrás-szolgáltató telepítési dokumentációját.

Virtuális IP-címek nem jelennek meg, mert nem Azure közzétételi verem szükséges belső infrastruktúra.

> [!NOTE]
> Felhasználói VIP általában dinamikusan változnak maguk a felhasználók számára nem az Azure-verem üzemeltető határozzák meg.


|A végpont (VIP)|DNS host A record|Protokoll|Portok|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (rendszergazda)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Az Azure Resource Manager (rendszergazda)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portal (felhasználó)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Az Azure Resource Manager (felhasználó)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Graph|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Tanúsítvány-visszavonási listát|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP ÉS UDP|53|
|Key Vault (felhasználó)|*.vault.*&lt;region>.&lt;fqdn>*|TCP|443|
|Key Vault (rendszergazda)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|TCP|443|
|Tárolási üzenetsor|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tárolási tábla|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>Portok és URL-címek (kimenő)

Az Azure verem csak a transzparens proxy kiszolgálókat támogatja. Egy központi telepítésben lévő Amennyiben egy transzparens proxy kimenő kapcsolatot biztosítani a hagyományos proxykiszolgálót, engedélyeznie kell a következő portokat és URL-címek kimenő kommunikációra:


|Cél|URL-cím|Protokoll|Portok|
|---------|---------|---------|---------|
|Identitás|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|Piactér szindikálási|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Javítás & frissítése|https://&#42;.azureedge.net|HTTPS|443|
|Regisztráció|https://management.azure.com|HTTPS|443|
|Használat|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|

## <a name="firewall-publishing"></a>Tűzfal közzététele

Az előző szakaszban felsorolt portokon vonatkozik a bejövő kommunikáció Azure verem szolgáltatások egy meglévő tűzfalon keresztüli közzétételekor.

Azt javasoljuk, hogy használja-e tűzfal eszköz segítségével biztonságos Azure verem. Azonban nincs szigorú követelmény. Bár tűzfalak segítségére lehetnek többek között elosztott-szolgáltatásmegtagadásos (DDOS-) támadások és a tartalomvizsgálat a, a teljesítmény szűk keresztmetszetek az Azure storage szolgáltatások, például a blobot, táblát és üzenetsort is válnának is.

(Az Azure AD vagy AD FS) identitás minta alapján, lehetséges, hogy vagy az AD FS végpont közzétételéhez nincs szükség. Ha a kapcsolat nélküli telepítési módban használja, az AD FS végpont közzé kell tennie. (További információkért tekintse meg a Datacenter integrációs identitás témakört.)

Az Azure Resource Manager (rendszergazda), a felügyeleti portál és a Key Vault (rendszergazda) végpontok nincs feltétlenül szükség külső közzététel. Ez a forgatókönyv függ. Például egy szolgáltató, érdemes lehet támadási felületét, és csak az Azure-verem felügyeletéhez, a hálózaton belül, és nem az internetről.

A vállalaton belüli a külső hálózati lehet a meglévő vállalati hálózathoz. Ilyen esetben közzé kell tennie ezekre a végpontokra Azure verem működéséhez a vállalati hálózatról.

## <a name="edge-firewall-scenario"></a>Peremhálózati tűzfal forgatókönyv

Peremhálózati telepítések, Azure verem közvetlenül mögé telepítették a peremhálózati útválasztó (az Internetszolgáltató által biztosított) vagy egy tűzfal előtte nélkül.

![Az Azure-verem peremhálózati üzembe helyezésének architekturális diagramja](media/azure-stack-integrate-endpoints/Integrate-Endpoints-02.png)

Általában nyilvános elérhető IP-címet adott meg a nyilvános virtuális IP-címkészletet a központi telepítéskor edge-telepítés. Ebben a forgatókönyvben lehetővé teszi, hogy a felhasználót, hogy a teljes önálló ellenőrzött felhő élményt, például egy nyilvános felhő mint Azure tapasztalhat.

### <a name="using-nat"></a>NAT használata

A terhelés miatt nem ajánlott, de használhat hálózati címfordítás (NAT) közzétételi végpontok. Végpont a közzététel teljesen felhasználók által szabályozott ehhez a NAT-szabály, amely tartalmazza a felhasználó használhatja minden port VIP felhasználónként.

Meg kell vizsgálni, hogy Azure nem támogatja a végpont NAT használata hibrid felhő használata estén az Azure VPN-alagúton beállítását.

## <a name="enterpriseintranetperimeter-network-firewall-scenario"></a>Vállalati/intranetes/peremhálózati tűzfal forgatókönyv

Egy szervezet/intranetes/határain telepítési Azure verem második tűzfal, amely általában a szegélyhálózaton (más néven DMZ) része túl van telepítve.

![Az Azure verem tűzfal forgatókönyv](media/azure-stack-integrate-endpoints/Integrate-Endpoints-03.png)

Nyilvános elérhető IP-címet lett megadva a következő Azure verem nyilvános virtuális IP-címkészletet, ezeknél a címeknél logikailag tartozik a peremhálózaton, és szükséges az elsődleges tűzfalon közzétételi szabályokat.

### <a name="using-nat"></a>NAT használata

Ha nem nyilvános elérhető IP-címet használ az Azure-vermet nyilvános virtuális IP-címkészletet, NAT a másodlagos tűzfalon közzétételére szolgál Azure verem végpontok. Ebben a forgatókönyvben a közzétételi szabályok konfigurálása a elsődleges tűzfalon kívül a peremhálózaton, és a másodlagos tűzfalon kell. Ha szeretné használni a NAT, vegye figyelembe a következő szempontokat:

- NAT többletterheléssel tűzfalszabályok kezeléséhez, mert a felhasználók a saját végpontok és a saját közzétételi szabályokat a szoftveralapú hálózatkezelési (SDN) verem ellenőrzése. Felhasználók kapcsolatba kell lépnie az Azure-verem üzemeltető a virtuális IP-címek közzé, illetve port listáját.
- NAT használata korlátozza a felhasználói élmény, amíg biztosít teljes hozzáférés a operátornak keresztül közzétételi kérelmeket.
- Hibrid felhős rendszerekben az Azure-ral vegye figyelembe, hogy az Azure nem támogatja a hálózati címfordítást. használja a végpont VPN-alagúton beállítása


## <a name="next-steps"></a>További lépések

[Az Azure verem datacenter integrációs - biztonsági](azure-stack-integrate-security.md)