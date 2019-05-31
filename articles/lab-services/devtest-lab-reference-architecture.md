---
title: Vállalati referenciaarchitektúra az Azure DevTest Labs szolgáltatásban
description: Ez a cikk a referencia architektúra-útmutató az Azure DevTest Labs a vállalaton belül.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1bfd1b5b4b7febd98499e338fcb62e339867aef4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244720"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Az Azure DevTest Labs referenciaarchitektúra nagyvállalatok számára
Ez a cikk segítséget nyújt az Azure DevTest Labs alapján egy vállalati megoldás üzembe helyezése a referencia-architektúra. A következő adatokat tartalmazza:
- Helyszíni kapcsolatok az Azure ExpressRoute használatával
- A távoli asztali átjáró, hogy távolról jelentkezzen be a virtuális gépek
- Privát összetevők egy összetevő-adattárban való kapcsolódás
- Egyéb PaaS-szolgáltatások, amelyek használják a Labs szolgáltatásban

![Architekturális diagramja](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektúra
A referenciaarchitektúra a fő elemei a következők:

- **Az Azure Active Directory (Azure AD)** : DevTest Labs használja a [Identitáskezelés az Azure AD szolgáltatás](../active-directory/fundamentals/active-directory-whatis.md). Ezek két fő szempontokat vegye figyelembe, amikor engedélyezi a felhasználók elérését a DevTest Labs-környezetben:
    - **Az erőforrás-kezelés**: Erőforrások kezelése az Azure Portalon való hozzáférést biztosít (virtuális gépek létrehozása; környezeteket hozhat létre; indítása, leállítása, újraindítása, törléséhez és összetevők; a alkalmazni és stb). Az erőforrás-kezelés az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával történik. Szerepkörök hozzárendelése a felhasználókhoz, és állítsa be az erőforrás- és hozzáférés-szintű engedélyekkel.
    - **Virtuális gépek (hálózati szintű)** : Az alapértelmezett beállítás a virtuális gépek egy helyi rendszergazdai fiókot használja. Ha van egy tartományhoz ([Azure AD tartományi szolgáltatások](../active-directory-domain-services/overview.md), egy a helyszíni tartomány vagy egy felhőalapú tartomány), gépek tartományhoz kell csatlakoznia. Felhasználók tartományalapú identitásuk felhasználhatja a virtuális géphez való kapcsolódásra.
- **Helyszíni kapcsolatok**: Az architektúra diagramja az [ExpressRoute](../expressroute/expressroute-introduction.md) szolgál. Is használhatja, de egy [site-to-site VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Bár az ExpressRoute nem szükséges a DevTest Labs, gyakran használják a vállalatok számára. Az ExpressRoute kötelező, csak akkor, ha a vállalati erőforrásokhoz való hozzáférésre van szüksége. Gyakori forgatókönyvek a következők:
    - Nem lehet áthelyezni a felhőbe helyszíni adatokkal rendelkezik.
    - Inkább a labor virtuális gépek csatlakoztatása a helyszíni tartománnyal.
    - Az összes hálózati forgalom, a felhőalapú környezet biztonsági/megfelelőségi egy a helyszíni tűzfalon kívüli kényszeríteni kívánja.
- **Hálózati biztonsági csoportok**: Gyakori módja irányuló forgalom korlátozásához, a felhőalapú környezetét (vagy a felhőalapú környezet) alapján a forrás és cél IP-címek használatára egy [hálózati biztonsági csoport](../virtual-network/security-overview.md). Például azt szeretné, hogy csak a labor hálózatokba a vállalati hálózat származó forgalom.
- **A távoli asztali átjáró**: Vállalatok általában a vállalati tűzfalon a kimenő távoli asztali kapcsolatok letiltása. A DevTest Labs-környezetben, beleértve a felhőalapú környezetre való kapcsolódás engedélyezéséhez több lehetőség áll rendelkezésre:
  - Használja a [távoli asztali átjáró](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture), és engedélyezze a statikus IP-címet az átjáró terheléselosztó.
  - [Az összes bejövő RDP-forgalom közvetlen](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) az ExpressRoute és a helyek közötti VPN-kapcsolaton keresztül. Ez a funkció akkor közös veszi figyelembe, ha a vállalatok DevTest Labs-telepítés megtervezése.
- **Hálózati szolgáltatások (virtuális hálózatok, alhálózatok)** : A [az Azure-hálózatok](../networking/networking-overview.md) topológia egy másik fő elemet a DevTest Labs-architektúra. Azt szabályozza, e erőforrások a labor létrehozása a kommunikációhoz, és hozzáférhetnek a helyszíni és az interneten. Az architektúra ábráját, hogy az ügyfelek DevTest Labs használatát a leggyakrabban használt módszereket vezetett be: -N keresztül csatlakozik az összes labs [virtuális hálózatok közötti társviszony](../virtual-network/virtual-network-peering-overview.md) használatával egy [küllős modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) ExpressRoute és a helyek közötti VPN-kapcsolat a helyszíni. De a DevTest Labs szolgáltatásban az Azure Virtual Network közvetlenül, így nem vonatkoznak korlátozások meg, hogyan állíthatja be a hálózati infrastruktúra.
- **DevTest Labs**:  DevTest Labs az általános architektúrát kulcsfontosságú része. A szolgáltatással kapcsolatos további tudnivalókért lásd: [DevTest Labs](devtest-lab-overview.md).
- **Virtuális gépek és más erőforrások (SaaS, PaaS, IaaS)** :  Virtuális gépek, a kulcsfontosságú munkaterhelési DevTest Labs támogató egyéb Azure-erőforrások együtt. A DevTest Labs szolgáltatással gyors és egyszerű (beleértve a virtuális gépek és egyéb Azure-erőforrások) Azure-erőforrásokhoz való hozzáférést biztosítani a vállalati. További információ az Azure access [fejlesztők](devtest-lab-developer-lab.md) és [tesztelőknek](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Méretezési szempontok
DevTest Labs nem rendelkezik beépített kvótákat vagy korlátozásokat, bár egyéb Azure-erőforrások a labor létrehozása a tipikus műveletben használt rendelkeznek [előfizetési kvóták](../azure-subscription-service-limits.md). Tehát jellemző nagyvállalati környezetben kell ahhoz, hogy biztosítsák a DevTest Labs nagy üzembe helyezése több Azure-előfizetéssel. A kvóták elérő vállalatok leggyakrabban a következők:

- **Erőforráscsoportok**: Alapértelmezés szerint a DevTest Labs hoz létre egy erőforráscsoportot, minden új virtuális gép vagy a felhasználó létrehoz egy környezetet a szolgáltatás használatával. Előfizetések tartalmazhat [akár 980 erőforráscsoportok](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). Tehát ez a virtuális gépek és a egy adott előfizetés környezetek korlátját. Nincsenek két egyéb konfigurációk, figyelembe kell venni:
    - **[Nyissa meg az összes virtuális gép ugyanabban az erőforráscsoportban](resource-group-control.md)** : Bár ez a beállítás a csoport erőforráskorlátot betartásában is segítséget, hatással van az erőforrás-típus-per-resource-group korlátot.
    - **Nyilvános IP-címek használatával megosztott**: Az azonos méretű és régió összes virtuális gép ugyanabban az erőforráscsoportban lépnek. Erre a konfigurációra egy "közel földön" csoport erőforráskvóták és erőforrás-típus-per-resource-group kvóták, között, ha a virtuális gép nyilvános IP-címeket használhat.
- **Erőforrások erőforrásonként csoportosítás erőforrástípus**: Az alapértelmezett korlát esetében [erőforrások erőforrás-típusonként erőforráscsoportra 800](../azure-subscription-service-limits.md#resource-group-limits).  Használatakor a *minden virtuális gép ugyanabban az erőforráscsoportban Ugrás* konfigurációját, a felhasználók találat ehhez az előfizetéshez sok korábban, korlátozza, különösen akkor, ha a virtuális gépek rendelkeznek számos további lemezeket.
- **Storage-fiókok**: DevTest Labs szolgáltatásban létrehozott tesztkörnyezet egy tárfiókot tartalmaz. Az Azure-kvótája [storage-fiókok régiónként és előfizetésenként száma: 250](../azure-subscription-service-limits.md#storage-limits). DevTest Labs ugyanabban a régióban legfeljebb 250 is.
- **Szerepkör-hozzárendelések**: Szerepkör-hozzárendelés, hogy egy felhasználó vagy hozzáférést adhat egy erőforrást (tulajdonos, erőforrás, jogosultsági szint). Az Azure-ban van olyan [korlát előfizetésenként 2000 szerepkör-hozzárendelések](../azure-subscription-service-limits.md#role-based-access-control-limits). A DevTest Labs szolgáltatás alapértelmezés szerint létrehoz egy erőforráscsoportot az egyes virtuális Gépekhez. A tulajdonosa kapnak *tulajdonosa* engedéllyel a DevTest Labs virtuális gép és *olvasó* engedély az erőforráscsoporthoz. Ily módon minden létrehozott új virtuális Gépre felül a hozzárendeléseket, engedélyezheti a felhasználók számára a laborhoz használt két szerepkör-hozzárendelések használ.
- **API-olvasási/írási műveleteket**: Automatizálhatja az Azure-ban és a DevTest Labs, beleértve a REST API-k, PowerShell, az Azure parancssori felület és Azure SDK-val különböző módja van. Az automatizálás előfordulhat, hogy eléri egy másik API-kérések korlátozva: Minden egyes előfizetés lehetővé teszi, hogy akár [12 000 olvasási kérelmek és 1200 írási kérelmek száma óránként](../azure-resource-manager/resource-manager-request-limits.md). Vegye figyelembe ezt a korlátot, amikor, automatizálhatja a DevTest Labs szolgáltatásban.

## <a name="manageability-considerations"></a>Felügyeleti szempontok
DevTest Labs-egyetlen labor használata a nagyszerű rendszergazdai felhasználói felülettel rendelkezik. Azonban egy vállalati, akkor valószínűleg több Azure-előfizetéssel, és számos labs. Parancsfájl-kezelési/automatizálási módosítása következetesen minden a gyakorlatot igényel. Íme, néhány példa és felügyeleti gyakorlatok DevTest Labs központi telepítés:

- **Labor beállításai módosításának**: Gyakran előfordul, hogy egy adott labor-beállítás frissítése a központi telepítésben lévő összes labs között. Például egy új Virtuálisgép-példány mérete érhető el, és minden labs frissíteni kell, hogy engedélyezi-e. Célszerű ezeket a módosításokat automatizálása a PowerShell-parancsprogramok, a parancssori felület vagy a REST API-k használatával.  
- **Összetevő-adattárban személyes hozzáférési tokent**:  Általában a személyes hozzáférési jogkivonatok Git-tárházhoz 90 napon, egy, vagy két év múlva lejár. Folytonosságának biztosításához fontos hosszabbítsa meg a személyes hozzáférési jogkivonat, vagy hozzon létre egy újat. Automation segítségével az új személyes hozzáférési tokent minden labs vonatkoznak.
- **Módosítások korlátozása a laboratóriumban**: Egy adott beállítást gyakran korlátozott (például a marketplace-rendszerképek használatának engedélyezése) kell lennie. Az Azure Policy segítségével erőforrástípust módosításának megakadályozása. Vagy hozzon létre egy egyéni szerepkört, és biztosítson a felhasználó számára, hogy a szerepkör helyett a *tulajdonosa* szerepkörnek a labor létrehozása. Ezt megteheti a legtöbb beállítását a laborban (belső támogató, labor közlemény, engedélyezett Virtuálisgép-méretek és így tovább).
- **Virtuális gépek egy elnevezési konvenciója megkövetelése**: Kezelők gyakran szeretne könnyen azonosíthatja azokat a felhőalapú fejlesztési és tesztelési környezetet részét képező virtuális gépeket. Ezt megteheti használatával is [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Fontos megjegyezni, hogy a DevTest Labs alapul szolgáló Azure-ugyanúgy felügyelt erőforrást használ: hálózatkezelés, lemezek, számítási és így tovább. Például az Azure Policy tesztkörnyezetben létrehozott virtuális gépek vonatkozik. Az Azure Security Center VM-megfelelőségi jelentést is. És az Azure Backup szolgáltatás rendszeres biztonsági mentések megadhatja a virtuális gépek a tesztkörnyezetben.

## <a name="security-considerations"></a>Biztonsági szempontok
Az Azure DevTest Labs meglévő erőforrást használ az Azure-ban (számítási, hálózati és így tovább). Ezért azt automatikusan számos előnyt biztosít az a platformba beépített biztonsági szolgáltatások. Például oly módon csak a vállalati hálózathoz, hogy a bejövő távoli asztali kapcsolatokat igényelnek, egyszerűen adjon hozzá egy hálózati biztonsági csoportot a virtuális hálózathoz, a távoli asztali átjárón. A csak további biztonsági szempont, hogy szintű engedélyeket az engedélyt a csapattagok számára, aki a tesztkörnyezetek használata napi alapon. A leggyakoribb engedélyek a következők [ *tulajdonosa* és *felhasználói*](devtest-lab-add-devtest-user.md). Ezek a szerepkörök kapcsolatos további információkért lásd: [tulajdonosai és felhasználók hozzáadása az Azure DevTest Labs szolgáltatásban](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>További lépések
Ez a sorozat következő cikkében talál: [Vertikális felskálázás az Azure DevTest Labs-infrastruktúra](devtest-lab-guidance-scale.md).
