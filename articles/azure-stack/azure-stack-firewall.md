---
title: "Azure verem tervezése Azure verem tűzfal integrált rendszerek |} Microsoft Docs"
description: "Több csomópontos Azure verem Azure csatlakozó rendszerekhez Azure verem tűzfal szempontokat ismerteti."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 737df48a24d8e077f898d00526c7138db69dbb93
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="azure-stack-firewall-integration"></a>Azure verem tűzfal-integráció
Javasoljuk, hogy a tűzfal eszközt használ-e biztonságos Azure verem segítségével. Bár tűzfalak segítségére lehetnek többek között elosztott-szolgáltatásmegtagadásos (DDOS-) támadások, a behatolás-észlelés és a tartalomvizsgálat a, a teljesítmény szűk keresztmetszetek az Azure storage szolgáltatások, például a blobot, táblát és üzenetsort is válnának is.

Azure Active Directory (Azure AD) vagy a Windows Server Active Directory összevonási szolgáltatások (AD FS) identitás minta alapján, szükséges lehet az AD FS végpont közzététele. Ha a kapcsolat nélküli telepítési módban használja, az AD FS végpont közzé kell tennie. További információkért lásd: a [datacenter integrációs identitás cikk](azure-stack-integrate-identity.md).

Az Azure Resource Manager (rendszergazda), a felügyeleti portál és a Key Vault (rendszergazda) végpontok nincs feltétlenül szükség külső közzététel. Például egy szolgáltató érdemes támadási felületét, és csak felügyelheti az Azure-vermet, a hálózaton belül, és nem az internetről.

A vállalati szervezetek számára a külső hálózati lehet a meglévő vállalati hálózathoz. Ilyen esetben közzé kell tennie ezekre a végpontokra Azure verem működéséhez a vállalati hálózatról.

### <a name="network-address-translation"></a>Hálózati címfordítás
Hálózati címfordítás (NAT) a központi telepítés virtuális gép (DVM) a külső erőforrások és a telepítés során az internet eléréséhez, valamint a sürgős helyreállítási konzol (ERCS) virtuális gépeket, vagy a kiemelt a végpont (EGP) a során a javasolt módja a regisztráció és a hibaelhárítás.

NAT nyilvános IP-címek a külső hálózaton vagy a nyilvános virtuális IP-címek alternatív megoldás is lehet. Azonban nem ajánlott, mert azt korlátozza a bérlői felhasználói élményt, és növeli a összetettségét. A két lehetőség lenne a 1:1 NAT, amelyhez egy nyilvános IP-cím felhasználói IP-készlet vagy egy NAT-szabály, amely tartalmazza a társítást minden port a felhasználó használhatja a felhasználó VIP / igénylő NAT több: 1 továbbra is szükséges.

A NAT nyilvános VIP-címhez használatával downsides vannak:
- NAT többletterheléssel tűzfalszabályok kezeléséhez, mert a felhasználók a saját végpontok és a saját közzétételi szabályokat a szoftveralapú hálózatkezelési (SDN) verem ellenőrzése. Felhasználók kapcsolatba kell lépnie az Azure-verem üzemeltető a virtuális IP-címek közzé, illetve port listáját.
- NAT használata korlátozza a felhasználói élmény, amíg biztosít teljes hozzáférés a operátornak keresztül közzétételi kérelmeket.
- Hibrid felhős rendszerekben az Azure-ral vegye figyelembe, hogy az Azure nem támogatja a hálózati címfordítást. használja a végpont VPN-alagúton beállítása

### <a name="ssl-decryption"></a>SSL-visszafejtés
Jelenleg az ajánlott megoldás a SSL visszafejtés után. tiltsa le az összes Azure verem forgalmat, akkor a későbbiekben lesz nyújtunk segítséget történő SSL visszafejtési Azure verem.

## <a name="edge-firewall-scenario"></a>Peremhálózati tűzfal forgatókönyv
Az él központi telepítése Azure verem közvetlenül a peremhálózati útválasztó vagy a tűzfal mögött van telepítve. Ezekben az esetekben, támogatott a tűzfalon a szegély vagy a szegély eszközként működő, ha az támogatja-e azonos költségű több útvonalas (ECMP) vagy a BGP, vagy a statikus útválasztás.

Általában nyilvános elérhető IP-címet adott meg a külső hálózati nyilvános virtuális IP-címkészletet a központi telepítéskor. Az edge esetben nem ajánlott nyilvános irányítható IP-címek használata bármely más hálózati biztonsági okokból. Ebben a forgatókönyvben lehetővé teszi, hogy a felhasználót, hogy a teljes önálló ellenőrzött felhő élményt, például az Azure nyilvános felhő mint tapasztalhat.  

![Az Azure verem peremhálózati tűzfal – példa](.\media\azure-stack-firewall\edge-firewall-scenario.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>A vállalati intranet vagy peremhálózati tűzfal forgatókönyve
A vállalati intranet vagy szegélyhálózat üzembe helyezés Azure-verem telepítve vannak multi-zónájában tűzfal vagy a peremhálózati tűzfalon és a belső, a vállalati hálózat tűzfalán Between. A forgalom majd elosztása a biztonságos, szegélyhálózaton (vagy DMZ) között, és nem biztonságos zónák, az alábbiakban:

- **Biztonságos zóna**: Ez az a belső hálózat által használt belső vagy a vállalati elérhető IP-címet. A biztonságos hálózati osztható, a tűzfalon keresztül NAT kimenő internet-hozzáféréssel rendelkeznek, és elérhető-e általában bárhol az adatközpontban, a belső hálózaton keresztül belül. Az összes Azure verem hálózatot a biztonságos zónában, kivéve a külső hálózati nyilvános virtuális IP-címkészletet kell tárolni.
- **Szegélyhálózati zóna**. A peremhálózaton, de ha külső alkalmazások, például a webkiszolgálók általában telepített internetes. A rendszer általában például DDoS és a behatolás (támadásoknak) miközben továbbra is lehetővé teszi a megadott bejövő forgalom az internetről érkező támadásokat elkerülése érdekében tűzfal figyeli. Csak a külső hálózati nyilvános virtuális IP-címkészletet Azure verem a DMZ zónában kell lennie.
- **Nem biztonságos zóna**. Ez egy, a külső hálózathoz, az internethez. Az **nem** célszerű telepíteni a nem biztonságos zónában Azure verem.

![Az Azure verem szegélyhálózati hálózati – példa](.\media\azure-stack-firewall\perimeter-network-scenario.png)


## <a name="next-steps"></a>További lépések
További információ [Azure verem végpontok által használt portokról és protokollokról](azure-stack-integrate-endpoints.md)

