---
title: Az Azure Stack-tűzfal tervezése az Azure Stack integrált rendszerek |} A Microsoft Docs
description: A több csomópontos, az Azure Stack Azure-kapcsolattal rendelkező üzemelő példányok az Azure Stack tűzfal szempontokat ismerteti.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 3759a9845d4ad1514fc5f0183c78b5eca2e31464
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960651"
---
# <a name="azure-stack-firewall-integration"></a>Az Azure Stack tűzfal integrációja
Javasoljuk, hogy egy tűzfal eszköz segítségével biztonságos Azure Stack segítségével. Bár tűzfalak többek között elosztott-szolgáltatásmegtagadásos (DDOS) támadások, a behatolásérzékelési és a tartalomvizsgálat segíthet, azokat is az Azure storage-szolgáltatásokhoz hasonlóan a blobok, táblák és üzenetsorok átviteli szűk keresztmetszetté válhat.

Azure Active Directory (Azure AD) vagy a Windows Server Active Directory összevonási szolgáltatások (AD FS) identitás mintán alapuló, szükséges lehet közzétenni az Active Directory összevonási szolgáltatások végpont. Ha a kapcsolat nélküli telepítési módban használja, az AD FS-végpont közzé kell tennie. További információkért lásd: a [adatközpont integrációja identitás cikk](azure-stack-integrate-identity.md).

Az Azure Resource Manager (rendszergazda), a felügyeleti portál és a Key Vault (rendszergazda) végpontok nem feltétlenül szükséges külső közzétételét. Például egy szolgáltató, érdemes a számítógép támadható felületét, és csak felügyelheti az Azure Stack, a hálózaton belül, és nem az internetről.

A vállalati szervezetek számára a külső hálózat lehet a meglévő vállalati hálózaton. Ilyen esetben közzé kell tennie azokat a végpontokat, Azure Stack működjön a vállalati hálózatról.

### <a name="network-address-translation"></a>Hálózati címfordítás
Hálózati címfordítás (NAT), hogy a központi telepítés a virtuális gép (DVM) a külső erőforrásokat és üzembe helyezése során az internet eléréséhez, valamint a válságkezelési helyreállítási konzol (ERCS) virtuális gépek vagy a kiemelt jogosultságú a végpontot (EGP) a során a javasolt módszer a regisztráció és a hibaelhárítás.

NAT is lehet a külső hálózaton vagy a nyilvános virtuális IP-cím nyilvános IP-címek helyett. Azonban nem ajánlott, mert azt korlátozza a bérlő felhasználói élményt, és növeli az összetettséget. A két lehetőség lenne, itt is szükség van egy nyilvános IP-cím / felhasználói IP-cím a készleten, vagy számos NAT 1:1:1 NAT igénylő egy NAT-szabályhoz / virtuális IP-cím, amely tartalmazza az összes port felhasználó a felhasználó használhatja.

A nyilvános virtuális IP-CÍMEK a NAT használatával több szempontból sem ajánlott a következők:
- NAT többletterheléssel tűzfalszabályok kezelésekor, mert a felhasználók saját végpontokat és a saját közzététel szabályai a szoftveralapú hálózatkezelési (SDN) verem szabályozhatja. Felhasználók kapcsolatba kell lépnie a VIP-k közzététele, és a port listájának frissítése az Azure Stack-operátorokról.
- NAT-használat korlátozza a felhasználói élmény, miközben nyújt teljes hozzáférés az operátor keresztül közzétételi kérelmeket.
- Hibrid felhős rendszerekben az Azure-ral fontolja meg, hogy az Azure nem támogatja a VPN-alagúton NAT használata a végpont beállítása

### <a name="ssl-decryption"></a>Az SSL visszafejtési
Jelenleg javasoljuk, hogy az összes Azure Stack-forgalom SSL visszafejtési letiltása. A jövőbeli frissítések támogatott, ha útmutatást nyújtanak az Azure Stack SSL visszafejtési engedélyezésével kapcsolatos.

## <a name="edge-firewall-scenario"></a>Peremhálózati tűzfal forgatókönyv
Az edge üzemelő példány, az Azure Stack közvetlenül a peremhálózati útválasztó vagy a tűzfal mögött van telepítve. Ezekben az esetekben támogatott meghaladta a szegély (1. forgatókönyv esetében), ahol támogatja mindkét aktív-aktív és az aktív-passzív Tűzfalkonfiguráció, vagy a szegély eszköz (2. forgatókönyv) átjáróként, csak a támogatott aktív-aktív tűzfal tűzfal függő a egyenlő költség több elérési út (ECMP) BGP vagy a statikus útválasztás feladatátvételi konfiguráció.

Általában nyilvános irányítható IP-címek vannak megadva a nyilvános VIP-készletet a külső hálózatról üzembe helyezéskor. Az edge a forgatókönyvben nem ajánlott bármilyen más hálózati biztonsági okokból nyilvános irányítható IP-címek használatát. Ebben a forgatókönyvben lehetővé teszi, hogy a felhasználó élmény, mint például az Azure nyilvános felhő mint a teljes körű önálló felügyelt felhőbeli élményt.  

![Azure Stack peremhálózati tűzfal – példa](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Vállalati intranet vagy peremhálózati tűzfal forgatókönyve
Vállalati intranetes vagy szegélyhálózat-alapú üzembe helyezés Azure Stack üzembe lesz helyezve a többszörös zónázva tűzfalon, vagy a peremhálózati tűzfalon, és a belső, vállalati hálózati tűzfal között. Ezután ossza a biztonságos, szegélyhálózaton (vagy a DMZ) között a forgalmat, és nem biztonságos zónák, az alábbiakban:

- **Biztonságos zóna**: Ez az, hogy a belső hálózat, amely a belső vagy vállalati irányítható IP-címet használ. A biztonságos hálózati oszthatók, a tűzfalon keresztül, NAT kimenő internet-hozzáféréssel rendelkezik, és általában érhető el, bárhol az adatközpontban, a belső hálózaton belül. Az összes Azure Stack-hálózatok a külső hálózati nyilvános VIP-címkészlet kivételével a biztonságos zóna kell lennie.
- **Szegélyhálózat-alapú zóna**. A szegélyhálózaton van, ha külső vagy internetkapcsolattal rendelkező alkalmazás, például webkiszolgálók általában vannak telepítve. Egy tűzfal, például DDoS és a behatolás (feltörés) miközben továbbra is lehetővé teszi a megadott bejövő forgalom az internetről érkező támadások elkerülése érdekében általában figyelhető. Csak a külső hálózati nyilvános VIP-készletet az Azure Stack kell lennie a DMZ zónában.
- **Nem biztonságos zóna**. Ez az a külső hálózat, az interneten. Ez **nem** javasoljuk, hogy a nem biztonságos zónában az Azure Stack üzembe helyezése.

![Azure Stack szegélyhálózat-alapú hálózat – példa](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>Részletek
Tudjon meg többet [Azure Stack-végpontok által használt portokról és protokollokról](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>További lépések
[Az Azure Stack nyilvános kulcsokra épülő infrastruktúra követelményei](azure-stack-pki-certs.md)

