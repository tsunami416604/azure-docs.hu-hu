---
title: Irányítás az Azure DevTest Labs-infrastruktúra
description: Ez a cikk az Azure DevTest Labs-infrastruktúra cégirányítási útmutatást nyújt.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 8653056c5c4b0e5b6831d3cc2b0006e89ac01bdd
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48251090"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Az Azure DevTest Labs-infrastruktúra - alkalmazás áttelepítését és integrálását cégirányítási
A fejlesztési/tesztelési hálózati környezet létrehozása után kell gondolja át az alábbi kérdésekre: 

- Hogyan, kihasználhassák a környezet a projektcsapattal? 
- Hogyan gondoskodik, hogy hajtsa végre a szükséges szervezeti házirendeknek, és a rugalmasságot érték hozzáadása az alkalmazás karbantartása?

## <a name="azure-marketplace-images-vs-custom-images"></a>Egyéni rendszerképek és az Azure Marketplace-rendszerképek

### <a name="question"></a>Kérdés
Mikor célszerű használni és a saját egyéni szervezeti rendszerkép Azure Marketplace-rendszerképpel?

### <a name="answer"></a>Válasz
Az Azure Marketplace-en alapértelmezés szerint használandó, kivéve, ha bizonyos szempontból problematikus jelenségeket vagy a szervezeti követelményeknek. Néhány gyakori példa:;

- Összetett szoftver telepítése, amely az alaprendszerképet része egy alkalmazás szükséges.
- Telepítés és a egy alkalmazás telepítése eltarthat több órával, amelyek nem egy hatékonyabban használhatók fel az Azure Marketplace-rendszerképpel hozzáadandó számítási időért.
- Fejlesztőknek és tesztelőknek hozzáférést igényelnek a virtuális gépek gyors, és a egy új virtuális gép telepítési idő minimalizálása érdekében szeretné.
- Megfelelőségi vagy szabályozási feltételek (például biztonsági szabályzat), amely minden gép helyen kell lennie.

Egyéni rendszerképek használatával nem kell tekinteni kódjával. Alaplemezképek mögöttes VHD-fájlok kezelése most már rendelkezik további összetettséget vezetnek. Is kell rendszeresen javítani ezeket alaplemezképek a szoftverfrissítésekhez. Ezek a frissítések közé tartozik az új operációs rendszer (OS) frissítéseket, és a frissítések vagy a szoftvercsomag maga a szükséges konfigurációs módosításokat.

## <a name="formula-vs-custom-image"></a>Egyéni rendszerkép vagy képlet

### <a name="question"></a>Kérdés
Mikor érdemes használni az egyéni rendszerkép vagy képlet?

### <a name="answer"></a>Válasz
Ebben a forgatókönyvben a döntő tényező általában ez költséghatékony, és újra felhasználhatja.

Ha rendelkezik egy olyan forgatókönyvet, ahol sok felhasználó/labs megkövetelése a szoftver az alaprendszerképhez nagy kép, akkor csökkentheti a költségeket hozzon létre egy egyéni rendszerképet. Ez azt jelenti, hogy a lemezkép létrehozása után. Ez csökkenti a telepítési idő a virtuális gép és miatt a telepítő előfordulásakor futó virtuális gép költsége.

Egy további figyelembe vegye figyelembe azonban, a gyakori változásai a szoftvercsomag. Ha napi állít össze, és szükséges, hogy a felhasználók virtuális gépeket, a szoftver fontolja meg a képlet helyett egyéni rendszerkép.

## <a name="use-custom-organizational-images"></a>Egyéni szervezeti rendszerképek használatához

### <a name="question"></a>Kérdés
Hogyan állítható be egy egyszerű, megismételhető folyamattal ahhoz, hogy saját egyéni szervezeti lemezképek DevTest Labs-környezetben?

### <a name="answer"></a>Válasz
Lásd: [ezt a videót a Immage gyári minta](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Ebben a forgatókönyvben a speciális helyzetben, és a megadott szkriptek mintaszkriptek csak. Ha módosítások szükségesek, kezelésére és karbantartására a környezetében használt parancsfájlokra szüksége.

DevTest Labs használatával hozhat létre egy egyéni rendszerkép folyamatot a Visual Studio Team Services (VSTS):

- [Bevezetés: Felkészülés a virtuális gépek percek alatt beállításával egy kép factoryt az Azure DevTest Labs szolgáltatásban](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Kép gyári – 2. rész! Beállítása a vsts-ben és a gyári Lab hozhat létre virtuális gépeket](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Kép gyári – 3. rész: Save egyéni rendszerképek használata és terjesztése a több Tesztkörnyezetekhez](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Videó: Egyéni rendszerkép Factory az Azure DevTest Labs használatával](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Állítsa be a hálózati konfigurációs minták

### <a name="question"></a>Kérdés
Hogyan győződjön meg arról, hogy a fejlesztési és tesztelése a virtuális gépek azok nem a nyilvános interneten érhető el? Vannak-e bármely hálózati konfiguráció beállítása javasolt minták?

### <a name="answer"></a>Válasz
Igen. Két szempontot kell figyelembe venni – bejövő és kimenő forgalmat.

**Bejövő forgalom** – Ha a virtuális gép nem rendelkezik egy nyilvános IP-címet, akkor azt nem érik el az interneten. Általánosan használt megközelítése annak érdekében, hogy egy előfizetés-szintű szabályzat van beállítva, úgy, hogy a felhasználó nem tudja hozni egy nyilvános IP-cím nem.

**Kimenő forgalom** – Ha megakadályozzák a virtuális gépek közvetlenül a nyilvános interneten történik, és a vállalati tűzfalon, forgalom kényszerített majd irányíthatja a forgalmat a helyszíni express route-n keresztül, vagy VPN-, a kényszerített útválasztást.

> [!NOTE] 
> Ha rendelkezik egy proxykiszolgáló, amely blokkolja a forgalmat proxybeállítások nélkül, ne felejtse el kivételeket hozzáadni a lab artifact storage-fiók.

Hálózati biztonsági csoportok a virtuális gépek és alhálózatok is használhat. Ezt a lépést hozzáadja egy további rétegét védelem engedélyezése / letiltása a forgalmat.

## <a name="new-vs-existing-virtual-network"></a>Új vagy meglévő virtuális hálózat

### <a name="question"></a>Kérdés
Mikor kell új virtuális hálózat létrehozása a DevTest Labs környezet és a egy meglévő virtuális hálózattal?

### <a name="answer"></a>Válasz
Ha a virtuális gépek együttműködik a meglévő infrastruktúra kell, majd érdemes használni egy meglévő virtuális hálózaton belül a DevTest Labs-környezetben. Emellett az ExpressRoute használatakor érdemes virtuális hálózatok minimalizálására / alhálózatokat, hogy Ön nem darabolható az IP-címterületének, lekéri az előfizetések alatt használatra rendelt. Emellett érdemes a virtuális hálózatok közötti társviszony-létesítési minta használatával itt (Küllős modell). Ez a megközelítés lehetővé teszi a virtuális hálózat/alhálózat kommunikációt egy adott régión belül előfizetések között, bár a régiók közötti társviszony az Azure-hálózatok a felfelé-érkező szolgáltatása.

Ellenkező esetben minden egyes DevTest Labs-környezet lehet a saját virtuális hálózaton. Vegye azonban figyelembe, hogy nincsenek [korlátok](../azure-subscription-service-limits.md) virtuális hálózatok száma előfizetésenként száma. Az alapértelmezett értéke 50, bár ez a korlát 100-ra lehet megemelni.

## <a name="shared-public-or-private-ip"></a>Megosztott, nyilvános vagy magánhálózati IP

### <a name="question"></a>Kérdés
Mikor célszerű használni a magánhálózati IP-és nyilvános IP-cím és megosztott IP-cím?

### <a name="answer"></a>Válasz
Ha egy helyek közötti VPN vagy Express Route használja, fontolja meg magánhálózati IP-címek, a gépek a belső hálózaton elérhetők, és nem érhető el legyenek a nyilvános interneten keresztül. 

> [!NOTE] 
> Labortulajdonosok módosíthatja a alhálózat házirend annak érdekében, hogy senki véletlenül hozzon létre nyilvános IP-címek a virtuális gépek számára. Az előfizetés tulajdonosa hozzon létre egy előfizetési szabályzat meggátolja, hogy a nyilvános IP-címek létrehozása folyamatban.

Megosztott nyilvános IP-címek használata esetén a virtuális gépek tesztkörnyezetben megosztása a nyilvános IP-cím. Ez a megközelítés akkor lehet hasznos, amikor szüksége van egy adott előfizetéshez tartozó nyilvános IP-címek korlátait megszegéséhez elkerülése érdekében.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Felhasználó vagy a labor virtuális gépek számának korlátairól

### <a name="question"></a>Kérdés
Hány virtuális gépet tekintetében felhasználónként vagy labor kiszolgálónként kell beállítania szabály van?

### <a name="answer"></a>Válasz
Ha a mérlegeli felhasználónként vagy tesztlabor virtuális gépek számát, az alábbi három fő területtel kell foglalkoznunk:

- A **teljes költség** , amely a csapat a laborkörnyezetben található erőforrásokat fordítani. Több gép üzembe helyezése könnyebbé vált. Szabályozhatja a költségeket, egy mechanizmust, hogy a virtuális gépek száma felhasználónként és/vagy egy tesztkörnyezet
- A labor virtuális gépek teljes száma hatással van a [előfizetés szintje szerinti kvóták](../azure-subscription-service-limits.md) érhető el. A felső határértékek egyik 800 előfizetésenkénti erőforráscsoportok. DevTest Labs szolgáltatásban egy új erőforráscsoportot jelenleg minden virtuális géphez hoz létre (kivéve, ha a megosztott nyilvános IP-címek használata esetén). Ha egy adott előfizetés 10 labs, labs sikerült elfér KB. 79 virtuális gépek minden labor (800 felső korlátja – a 10 labs magukat a 10 erőforráscsoportok) = 79 tesztkörnyezeti virtuális gépet.
- Ha a labor (például) a helyszíni Express Route-n keresztül csatlakozik, nincsenek **meghatározott IP-címterek elérhető** a virtuális hálózat/alhálózat számára. Annak érdekében, hogy a labor virtuális gépek nem nem hozható létre (hiba: nem olvasható be az IP-cím), labortulajdonosok adhatja meg a maximális EK elérhető IP-címterület igazítva.

## <a name="use-resource-manager-templates"></a>Resource Manager-sablonok használata

### <a name="question"></a>Kérdés
Hogyan használhatok Resource Manager-sablonok a DevTest Labs Környezetemet?

### <a name="answer"></a>Válasz
A Resource Manager-sablonokat a DevTest Labs-környezetben szereplő lépések segítségével telepítheti a [környezetek funkció DevTest labs szolgáltatásban létrehozott](devtest-lab-test-env.md) cikk. Alapvetően a Resource Manager-sablonok tekintsen meg egy Git-tárház (Visual Studio Team Services vagy GitHub), és adjon hozzá egy [a sablonok privát tárház](devtest-lab-test-env.md) a tesztkörnyezethez.

Ebben a forgatókönyvben nem lehet hasznos, ha a DevTest Labs fejlesztési gazdagépeken használ, de előfordulhat, hogy lehet hasznos, ha egy átmeneti környezet, amely jellemző az éles környezetben hoz létre.

Emellett akkor is érdemes megjegyezni, hogy a labor időszakonkénti felhasználói beállítás, vagy a virtuális gépek száma csak korlátozza a laborkörnyezet, és nem bármely (Resource Manager-sablonok) környezetekben a natív módon készített gépek száma.

## <a name="next-steps"></a>További lépések
Lásd: [környezetek használata a DevTest Labs szolgáltatásban létrehozott](devtest-lab-test-env.md).