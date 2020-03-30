---
title: Az Azure DevTest Labs vállalati referenciaarchitektúrája
description: Ez a cikk referencia architektúra útmutatást nyújt az Azure DevTest Labs egy nagyvállalati környezetben.
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
ms.openlocfilehash: 77e6ab588f74c8b810f211e069c1c24043155111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132850"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Azure DevTest Labs referenciaarchitektúra nagyvállalatoknak
Ez a cikk referenciaarchitektúrát tartalmaz, amely segítséget nyújt az Azure DevTest Labs vállalati alapú megoldásának üzembe helyezéséhez. Ez a következőket tartalmazza:
- Helyszíni kapcsolat az Azure ExpressRoute-on keresztül
- Távoli asztali átjáró a virtuális gépekre való távoli bejelentkezéshez
- Személyes műtermékek må±termã¡lã©k-tárolóhoz való kapcsolódás
- Más PaaS-szolgáltatások, amelyeket laborokban használnak

![Referenciaarchitektúra-diagram](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektúra
Ezek a referenciaarchitektúra legfontosabb elemei:

- **Azure Active Directory (Azure AD)**: A DevTest Labs az Azure AD szolgáltatást használja az [identitáskezeléshez.](../active-directory/fundamentals/active-directory-whatis.md) Vegye figyelembe ezt a két fő szempontot, amikor hozzáférést biztosít a felhasználóknak egy DevTest Labs alapú környezethez:
    - **Erőforrás-kezelés:** Hozzáférést biztosít az Azure Portalon az erőforrások kezeléséhez (virtuális gépek létrehozása; környezetek létrehozása; indítás, leállítás, újraindítás, törlés és összetevők alkalmazása; és így tovább). Az erőforrás-kezelés az Azure-ban a szerepköralapú hozzáférés-vezérlés (RBAC) használatával történik. Szerepköröket rendelhet a felhasználókhoz, és erőforrás- és hozzáférési szintű engedélyeket állíthat be.
    - **Virtuális gépek (hálózati szintű)**: Az alapértelmezett konfigurációban a virtuális gépek helyi rendszergazdai fiókot használnak. Ha van elérhető tartomány[(Azure AD tartományi szolgáltatások](../active-directory-domain-services/overview.md), helyszíni tartomány vagy felhőalapú tartomány), gépek csatlakozhatnak a tartományhoz. A felhasználók ezután a tartományalapú identitások segítségével csatlakozhatnak a virtuális gépekhez.
- **Helyszíni kapcsolat:** Az architektúradiagramban az [ExpressRoute](../expressroute/expressroute-introduction.md) használatos. De használhat egy [helyek közötti VPN-t](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)is. Bár expressroute nem szükséges a DevTest Labs, általánosan használt vállalatoknál. ExpressRoute csak akkor szükséges, ha hozzá kell férnem a vállalati erőforrásokhoz. Gyakori forgatókönyvek a következők:
    - A helyszíni adatok, amelyek nem helyezhetők át a felhőbe.
    - A tesztkörnyezet virtuális gépeihez szeretne csatlakozni a helyszíni tartományhoz.
    - A felhőalapú környezetbe be- és kikívánt összes hálózati forgalmat egy helyszíni tűzfalon keresztül szeretné kikényszeríteni a biztonság/megfelelőség érdekében.
- **Hálózati biztonsági csoportok**: A felhőalapú környezetbe (vagy a felhőkörnyezetbe) irányuló forgalom forrás- és célIP-címek alapján történő korlátozásának gyakori módja egy [hálózati biztonsági csoport](../virtual-network/security-overview.md)használata. Például csak a vállalati hálózatról származó forgalmat szeretné engedélyezni a labor hálózataiba.
- **Távoli asztali átjáró**: A vállalatok általában blokkolják a kimenő távoli asztali kapcsolatokat a vállalati tűzfalon. A DevTest Labs felhőalapú környezetéhez való kapcsolódás engedélyezésére számos lehetőség áll rendelkezésre, többek között a következők:
  - Használjon [távoli asztali átjárót](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture), és engedélyezze az átjáró terheléselosztójának statikus IP-címét.
  - [Irányítsa az összes bejövő RDP-forgalmat](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) az ExpressRoute/helyek közötti VPN-kapcsolaton keresztül. Ez a funkció gyakori szempont, amikor a vállalatok devtest labs központi telepítését tervezik.
- **Hálózati szolgáltatások (virtuális hálózatok, alhálózatok)**: Az [Azure hálózati](../networking/networking-overview.md) topológia egy másik kulcsfontosságú eleme a DevTest Labs architektúra. Azt szabályozza, hogy a labor ból származó erőforrások kommunikálhatnak-e, és hozzáférhetnek-e a helyszíni és az internethez. Architektúradiagramunk tartalmazza a DevTest Labs: Minden laborok virtuális [hálózati társviszony-létesítésen](../virtual-network/virtual-network-peering-overview.md) keresztül csatlakozik egy [hub-küllős modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) az ExpressRoute/a helyek közötti VPN-kapcsolat a helyszíni használatával a leggyakoribb módszereket. A DevTest Labs azonban közvetlenül használja az Azure virtuális hálózatot, így nincsenek korlátozások a hálózati infrastruktúra beállítására vonatkozóan.
- **DevTest Labs:** DevTest Labs kulcsfontosságú része a teljes architektúra. A szolgáltatásról a [DevTest Labs – ismertet](devtest-lab-overview.md)című témakörben olvashat bővebben.
- **Virtuális gépek és egyéb erőforrások (SaaS, PaaS, IaaS)**: A virtuális gépek olyan kulcsfontosságú számítási feladatok, amelyeket a DevTest Labs más Azure-erőforrásokkal együtt támogat. A DevTest Labs segítségével a nagyvállalatok egyszerűen és gyorsan hozzáférhetnek az Azure-erőforrásokhoz (beleértve a virtuális gépeket és más Azure-erőforrásokat is). További információ az Azure-hoz való hozzáférésről [fejlesztők](devtest-lab-developer-lab.md) és [tesztelők számára.](devtest-lab-test-env.md)

## <a name="scalability-considerations"></a>Méretezési szempontok
Bár a DevTest Labs nem rendelkezik beépített kvótákkal vagy korlátokkal, a tesztkörnyezet tipikus működéséhez használt egyéb [Azure-erőforrások előfizetési szintű kvótákkal rendelkeznek.](../azure-resource-manager/management/azure-subscription-service-limits.md) Így egy tipikus vállalati telepítés, több Azure-előfizetésre van szükség a DevTest Labs nagy üzembe helyezéséhez. A vállalatok által leggyakrabban elérett kvóták a következők:

- **Erőforráscsoportok**: Az alapértelmezett konfigurációban a DevTest Labs minden új virtuális géphez létrehoz egy erőforráscsoportot, vagy a felhasználó létrehoz egy környezetet a szolgáltatás használatával. Az előfizetések [legfeljebb 980 erőforráscsoportot](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits)tartalmazhatnak. Tehát ez a virtuális gépek és környezetek egy előfizetésben. Két másik konfigurációt is figyelembe kell vennie:
    - **[Az összes virtuális gép ugyanahhoz az erőforráscsoporthoz kerül:](resource-group-control.md)** Bár ez a beállítás segít az erőforráscsoport-korlát elérésében, hatással van az erőforrás-típus-erőforrás-csoportkorra.
    - **Megosztott nyilvános IP-k használata:** Minden azonos méretű és régiós virtuális gép ugyanabba az erőforráscsoportba kerül. Ez a konfiguráció egy "középút" az erőforráscsoport-kvóták és az erőforrás-típus-erőforrás-csoport kvóták között, ha a virtuális gépek nyilvános IP-címekkel rendelkeznek.
- **Erőforrás-csoportonként erőforrástípusonként**: Erőforrástípus : Erőforrástípus : Erőforrás-típus : [800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Ha az *összes virtuális gép ugyanahhoz az erőforráscsoport-konfigurációhoz megy, a* felhasználók sokkal hamarabb elérik ezt az előfizetési korlátot, különösen akkor, ha a virtuális gépek sok extra lemezzel rendelkeznek.
- **Tárfiókok:** A DevTest Labs laboregy tárfiókkal rendelkezik. Az Azure-kvóta a [tárfiókok száma régiónként előfizetésenként 250.](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) A DevTest Labs maximális száma ugyanabban a régióban is 250.
- **Szerepkör-hozzárendelések:** A szerepkör-hozzárendelés az, ahogyan egy felhasználónak vagy egyszerű hozzáférésnek ad egy erőforráshoz (tulajdonos, erőforrás, jogosultsági szint). Az Azure-ban [előfizetésenként legfeljebb 2000 szerepkör-hozzárendelés lehet.](../azure-resource-manager/management/azure-subscription-service-limits.md#role-based-access-control-limits) Alapértelmezés szerint a DevTest Labs szolgáltatás létrehoz egy erőforráscsoportot minden virtuális géphez. A tulajdonos *tulajdonosi* engedélyt kap a DevTest Labs virtuális gép és *olvasó* engedélyt az erőforráscsoport. Ily módon minden új virtuális gép, amely létrehozott két szerepkör-hozzárendelések mellett a hozzárendelések, amelyek a felhasználók engedélyének a laborban.
- **API-olvasási/írási:** Az Azure és a DevTest Labs automatizálásának számos módja van, beleértve a REST API-kat, a PowerShellt, az Azure CLI-t és az Azure SDK-t. Az automatizálás, előfordulhat, hogy egy másik korlátot api-kérelmek: Minden előfizetés lehetővé teszi, hogy legfeljebb [12 000 olvasási kérelmek és 1200 írási kérelmek óránként.](../azure-resource-manager/management/request-limits-and-throttling.md) Vegye figyelembe ezt a korlátot, amikor automatizálja a DevTest Labs.

## <a name="manageability-considerations"></a>Felügyeleti szempontok
A DevTest Labs nagyszerű felügyeleti felhasználói felülettel rendelkezik az egyetlen tesztkörnyezettel való munkához. De egy nagyvállalati, valószínűleg több Azure-előfizetések és számos labs. A módosítások következetes módosítása az összes laborban parancsfájl-készítést/automatizálást igényel. Íme néhány példa és ajánlott felügyeleti gyakorlat a DevTest Labs telepítéséhez:

- **A laborbeállítások módosítása:** Gyakori forgatókönyv egy adott tesztkörnyezet-beállítás frissítése a központi telepítés összes laborjában. Például egy új virtuálisgép-példány mérete érhető el, és minden labs frissíteni kell, hogy lehetővé tegye. A legjobb, ha ezeket a módosításokat powershell-parancsfájlok, a CLI vagy a REST API-k használatával automatizálhatja.  
- **Må±termÃ©k személyes hozzáférési jogkivonat:** Általában a Git-tárház személyes hozzáférési jogkivonatai 90 napon, egy évben vagy két évben lejárnak. A folytonosság biztosítása érdekében fontos a személyes hozzáférési jogkivonat kiterjesztése vagy egy új létrehozása. Ezután az automatizálás segítségével alkalmazza az új személyes hozzáférési jogkivonatot az összes laborok.
- **A tesztkörnyezet-beállítások módosításai korlátozása:** Gyakran egy adott beállítást korlátozni kell (például lehetővé kell tenni a marketplace-lemezképek használatát). Az Azure Policy használatával megakadályozhatja egy erőforrástípus módosítását. Vagy létrehozhat egy egyéni szerepkört, és adja meg a felhasználóknak ezt a szerepkört a *tesztkörnyezet tulajdonosi* szerepköre helyett. Ezt a laborban a legtöbb beállításnál megteheti (belső támogatás, laborbejelentése, engedélyezett virtuális gépméretek és így tovább).
- **A virtuális gépeknek egy elnevezési konvenciót kell követniük:** A kezelők általában könnyen szeretnék azonosítani a felhőalapú fejlesztési és tesztelési környezet részét vm-eket. Ezt az Azure [Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns)használatával teheti meg.

Fontos megjegyezni, hogy a DevTest Labs az alapul szolgáló Azure-erőforrásokat használja, amelyek et ugyanúgy kezelik: hálózatkezelés, lemezek, számítási és így tovább. Például az Azure-szabályzat egy tesztkörnyezetben létrehozott virtuális gépekre vonatkozik. Az Azure Security Center jelentést tehet a virtuális gép megfelelőségéről. És az Azure Backup szolgáltatás rendszeres biztonsági mentést biztosíthat a laborban lévő virtuális gépekről.

## <a name="security-considerations"></a>Biztonsági szempontok
Az Azure DevTest Labs az Azure meglévő erőforrásait (számítási, hálózati és így tovább) használja. Így automatikusan élvezheti a platformba beépített biztonsági funkciók előnyeit. Ha például a bejövő távoli asztali kapcsolatokcsak a vállalati hálózatról való kapcsolatra van szükség, egyszerűen adjon hozzá egy hálózati biztonsági csoportot a távoli asztali átjáró virtuális hálózatához. Az egyetlen további biztonsági szempont az engedélyek szintje, amelyet a laborokat napi rendszerességgel használó csapattagoknak ad meg. A leggyakoribb engedélyek a tulajdonos és a [ *owner* *felhasználó.*](devtest-lab-add-devtest-user.md) Ezekről a szerepkörökről további információt az [Azure DevTest Labs tulajdonosainak és felhasználóinak hozzáadása című témakörben talál.](devtest-lab-add-devtest-user.md)

## <a name="next-steps"></a>További lépések
Tekintse meg a sorozat következő cikkét: [Az Azure DevTest Labs infrastruktúrájának felskálázása.](devtest-lab-guidance-scale.md)
