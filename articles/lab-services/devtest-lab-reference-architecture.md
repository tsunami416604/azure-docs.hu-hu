---
title: A vállalati Azure DevTest Labs a referencia-architektúra
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
ms.openlocfilehash: 61e76369a4d73bd171c9e5c2462b3f261681ba00
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551382"
---
# <a name="azure-devtest-labs---reference-architecture-for-an-enterprise"></a>Az Azure DevTest Labs - a vállalati a referencia-architektúra
A cikk ismerteti a referenciaarchitektúra üzembe helyezéséhez az Azure DevTest Labs a vállalaton belül épül. Ez magában foglalja az Express Route, távolról bejelentkezik a virtuális gépek távoli asztali átjáró, kapcsolat az összetevőtárban, privát összetevők és a egy tesztkörnyezetben használt egyéb PaaS-szolgáltatások helyszíni kapcsolatait.

![Referenciaarchitektúra](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektúra
A referenciaarchitektúrában található fontosabb elemei a következők:

- **Az Azure Active Directory (AAD)**: Az Azure DevTest Labs használja a [Identitáskezelés az Azure Active Directory szolgáltatás](../active-directory/fundamentals/active-directory-whatis.md). Két dolgot legfontosabb figyelembe kell venni egy környezetet, a felhasználók számára a DevTest Labs alapján való hozzáférés biztosítása:
    - **Az erőforrás-kezelés**:  Erőforrások kezelése az Azure Portalon való hozzáférést biztosít (virtuális gépek létrehozásához, létre környezetek, indítása/leállítása/újraindítás/törlés/applyartifacts és így tovább). Az Azure-ban Roble-alapú hozzáférés-vezérlés (RBAC) és a egy szerepkör-hozzárendelés a felhasználó, a beállítás erőforrás és a hozzáférési szint engedélyek alkalmazásával befejeződött.
    - **Virtuális gépek (hálózati szintű)**:  Az alapértelmezett beállítás a virtuális gépek egy helyi rendszergazdai fiókot használja.  Ha van egy tartományhoz ([AAD tartományi szolgáltatások](../active-directory-domain-services/active-directory-ds-overview.md), egy a helyszíni tartomány vagy egy felhőalapú tartomány), gépek tartományhoz kell csatlakoznia. Miután csatlakozott, a felhasználók használna tartományalapú identitásuk a virtuális géphez való kapcsolódásra.
- **Helyszíni kapcsolatok**: Az architektúra a fenti ábrán [Express Route](../expressroute/expressroute-introduction.md) i használt, de használhatja a [Site-to-Site VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Bár ez nem szükséges a DevTest Labs, gyakran láthatók a vállalatok számára. Ez szükséges csak van-e a vállalati erőforrásokhoz történő csatlakozáshoz okát. Leggyakoribb okai a következők: 
    - Nem lehet áthelyezni a felhőbe még a helyszíni adatok
    - A labor virtuális gépek csatlakoztatása a helyszíni tartomány szabályozó
    - Az összes hálózati forgalom, a felhőalapú környezet biztonsági vagy megfelelőségi okokból egy a helyszíni tűzfalon kívüli kényszerítése
- **Hálózati biztonsági csoportok**: Gyakori módja irányuló forgalom korlátozásához, a felhőalapú környezetét (vagy a felhőalapú környezet) alapján a forrás és cél IP-címek használatára egy [hálózati biztonsági csoport](../virtual-network/security-overview.md). Például így csak a hálózati forgalom származik az a vállalati hálózathoz a labor hálózatokba.
- **A távoli asztali átjáró**:  Vállalatok általában a vállalati tűzfalon a kimenő távoli asztali kapcsolatok letiltása. A DevTest Labs szolgáltatásban létrehozott felhőalapú környezetre való kapcsolódás engedélyezéséhez többféle módon használatával például egy [távoli asztali átjáró](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture) (engedélyezett átjárókiszolgáló terheléselosztó statikus IP-cím), vagy [irányítja az összes bejövő RDP-forgalmat](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) az expressz útvonal/Site-to-Site VPN-kapcsolaton keresztül. A DevTest Labs a vállalati üzembe helyezés megtervezésekor fontos közös veszi figyelembe.
- **Az Azure-hálózatok (virtuális hálózatok, alhálózatok)**:  A [az Azure-hálózatok](../networking/networking-overview.md) topológia egy másik kulcsfontosságú eleme az általános DevTest Labs-architektúrát. Lehetővé teszi az erőforrások labs kommunikációhoz (vagy nem), a helyszíni hozzáférés (vagy nem), és internet-hozzáférés (vagy nem). Architektúradiagram tartalmazza a DevTest Labs használják az ügyfelek leggyakoribb módja (keresztül kapcsolódik az összes labs [virtuális hálózatok közötti társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md) használatával egy [küllős modell](/architecture/reference-architectures/hybrid-networking/hub-spoke) expressz útvonal/Site-to-Site VPN-kapcsolat a helyszínen), de DevTest Labs óta használja az Azure-hálózatot közvetlenül nincsenek korlátozásait a hálózati infrastruktúra beállítása.
- **DevTest Labs**:  DevTest Labs az általános architektúrát kulcsfontosságú része. A szolgáltatással kapcsolatos tudnivalókért lásd: [DevTest Labs](devtest-lab-overview.md).
- **Virtuális gépek és más erőforrások (SaaS, PaaS, IaaS)**:  A DevTest Labs által támogatott fontosabb számítási feladatokhoz egyik olyan virtuális gépek és egyéb Azure-erőforrások.  DevTest Labs segítségével könnyen és gyorsan (beleértve a virtuális gépek és egyéb Azure-erőforrások) Azure-erőforrásokhoz való hozzáférés biztosítása a vállalati.  További információ az Azure access [fejlesztők](devtest-lab-developer-lab.md) és [tesztelőknek](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Méretezési szempontok
DevTest Labs nem rendelkezik minden olyan beépített kvótákat vagy korlátozásokat, bár egyéb Azure-erőforrások használatban vannak a tipikus labor műveleteinek rendelkeznek [előfizetési kvóták](../azure-subscription-service-limits.md). Ennek eredményeképpen egy jellemző nagyvállalati környezetben kell rendelkeznie ahhoz, hogy biztosítsák a DevTest Labs nagy üzembe helyezése több Azure-előfizetéssel. A vállalatok által leggyakrabban elért kvóták a következők:

- **Erőforráscsoportok**:  Az alapértelmezett beállítás a DevTest Labs minden virtuális gépet vagy egy olyan környezetet, amely a szolgáltatás használatával létrehoz egy erőforráscsoportot hoz létre. Előfizetések tartalmazhat egy [980 erőforráscsoportok maximális](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager), így ezt a határt a virtuális gépek és a egy adott előfizetés környezetek felső korlátja. Nincsenek két egyéb konfigurációk, vegye figyelembe:
    - **[Nyissa meg az összes virtuális gép ugyanabban az erőforráscsoportban](resource-group-control.md)**:  Bár ez nyújtanak segítséget az erőforrás-csoport korlátot, az erőforrás-típusú erőforrás-csoport korlát hatással van.
    - **Nyilvános IP-címek használatával megosztott**:  Összes virtuális gépet az azonos méretű és ugyanabban a régióban lépnek ugyanabban az erőforráscsoportban. Ha a virtuális gép nyilvános IP-címet a "közel földön" erőforráskvóták csoport és a egy csoport erőforráskvóták erőforrástípus között. 
- **Erőforrások erőforrásonként csoport típusa erőforrásonként**: Az alapértelmezett korlát esetében [erőforrások / típusú erőforrás-csoport erőforrásonként 800](../azure-subscription-service-limits.md#resource-group-limits).  Az összes virtuális gép nyissa meg az ugyanazon fürterőforrás-csoport konfigurálása, ha felhasználók eléri az előfizetésre vonatkozó korlát sokkal hamarabb, különösen akkor, ha a virtuális gépek számos további lemezekkel rendelkeznek.
- **Storage-fiókok**: DevTest Labs szolgáltatásban létrehozott tesztkörnyezet tartalmaz egy tárfiókot és az Azure-kvótája [storage-fiókok régiónként és előfizetésenként száma: 250](../azure-subscription-service-limits.md#storage-limits). Ez azt jelenti, hogy a DevTest Labs ugyanabban a régióban száma a felső határ is 250.
- **Szerepkör-hozzárendelések**: Szerepkör-hozzárendelés, hogyan biztosíthat hozzáférést egy felhasználó vagy egy erőforráshoz (tulajdonos, erőforrás, jogosultsági szint). Az Azure-ban van olyan [korlát előfizetésenként 2000 szerepkör-hozzárendelések](../azure-subscription-service-limits.md#role-based-access-control-limits). A DevTest Labs szolgáltatás (az alapértelmezett beállítás) minden virtuális géphez hoz létre egy erőforráscsoportot és a tulajdonosa kapnak a **tulajdonosa** engedéllyel a DevTest Labs virtuális gép és **olvasó** engedéllyel a erőforráscsoport.  Ezzel a módszerrel minden újonnan létrehozott virtuális gép szerepkör-hozzárendelések jön létre, amikor a felhasználók jogosultságot ad a laborhoz két további szerepkör-hozzárendelések használ.
- **API-olvasási/írási műveleteket**: Többféle módon is (REST API-k, PowerShell, CLI, Azure SDK-t és így tovább) automatizálható az Azure és a DevTest Labs szolgáltatásban, és az automatizálásnak, nyomja le az API-kérések egy másik korlátja lehetőség. Minden egyes előfizetés lehetővé teszi, hogy akár [12000 olvasási kérelmek és 1200-as írási kérelmek száma óránként](../azure-resource-manager/resource-manager-request-limits.md).  Kell ügyelnie, ha a DevTest Labs automatizálása korlátozva.

## <a name="manageability-considerations"></a>Felügyeleti szempontok
DevTest Labs nagyszerű rendszergazdai felhasználói felülettel rendelkezik, az egyetlen labor használatakor. Vállalati vannak valószínűleg több Azure-előfizetések és sok labs. Ez azt jelenti, hogy módosítaná az összes labs következetesen igényel/automatizálási szkriptek.  Íme, néhány példát, és a legjobb módja a DevTest Labs-központi telepítés kezelése:

- **Labor beállításai módosításának**: Gyakran előfordul, hogy egy adott labor-beállítás frissítése a központi telepítésben lévő összes labs között. Például egy új Virtuálisgép-példány mérete érhető el, és minden labs frissíteni kell, hogy engedélyezi-e.  A legcélszerűbb automatizálja ezeket a módosításokat az Azure PowerShell-parancsprogramok, az Azure CLI vagy REST API-k használatával.  
- **Összetevő-adattárban személyes hozzáférési tokent**:  Általában a személyes hozzáférési jogkivonatok a Git-adattár (90 nap, 1 (év), 2 yrs) lejár. Folytonosságának biztosításához fontos kiterjesztése a személyes hozzáférési jogkivonat, vagy hozzon létre egy új, és az automation használata a alkalmazni az új személyes hozzáférési tokent minden labs.
- **Módosítások korlátozása laboratóriumban**:  Ez a helyzet gyakran ahol van egy adott beállítást (például, ami lehetővé teszi Marketplace-rendszerképek használható), amely korlátozott kell lennie. Azt el lehet végezni az Azure policy (adott erőforrástípus módosításának megakadályozása) keresztül, vagy egy egyéni szerepkör létrehozása és biztosítása, hogy a szerepkör a labor a "tulajdonos" helyett. A legtöbb a beállításokat a laborban (belső támogató, labor közlemény, engedélyezett Virtuálisgép-méretek és így tovább) teheti meg
- **Igénylő virtuális gépek egy elnevezési konvenciója**: Könnyen azonosíthatja azokat a felhőalapú fejlesztési és tesztelési környezetet részét képező virtuális gépek közös kérést. Akkor [az Azure Policyvel](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Fontos megjegyezni, hogy a DevTest Labs mögöttes erőforrások az Azure-ban (Hálózatkezelés, lemezek, számítás, stb.) az Azure-ban ugyanúgy felügyelt használja.  Például az Azure policy egy lab-ben létrehozott virtuális gépekre vonatkozik. Az Azure Security center VM-megfelelőségi jelentést is. Az Azure Backup szolgáltatás rendszeres biztonsági mentést nyújt a virtuális gépek a tesztkörnyezetben, és így tovább. 

## <a name="security-considerations"></a>Biztonsági szempontok
Az Azure DevTest Labs meglévő erőforrást használ az Azure-ban (számítási, hálózati és így tovább), és így automatikusan számos előnyt biztosít az összes beépített nagyszerű biztonsági funkció, a platform. Például minden olyan bejövő távoli asztali kapcsolatokat csak oly módon, hogy a vállalati hálózat biztonságossá tételéhez, fontos szúrni egy hálózati biztonsági csoportot a virtuális hálózathoz, a távoli asztali átjárón. A csak további biztonsági szempont, az Azure DevTest Labs szolgáltatásban, a csapattagok számára, akik használni fogják a labs napi rendszerességgel megadott engedélyek szintjét.  A megadott közös engedélyek a következők ["Tulajdonos" és "DevTest Labs-felhasználó"](devtest-lab-add-devtest-user.md). Ezek a szerepkörök kapcsolatos további információkért lásd: [tulajdonosai és felhasználók hozzáadása az Azure DevTest Labs szolgáltatásban](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>További lépések
Ez a sorozat következő cikkében talál: [Vertikális felskálázás az Azure DevTest Labs-infrastruktúra](devtest-lab-guidance-scale.md)
