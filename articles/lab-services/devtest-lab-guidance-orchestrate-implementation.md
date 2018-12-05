---
title: Koordinálhatja a megvalósítását az Azure DevTest Labs szolgáltatásban
description: Ez a cikk nyújt útmutatást a szervezet az Azure DevTest Labs vezénylésével végrehajtásához.
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
ms.openlocfilehash: 61ec9d0639f4bee950be69ee951492974ac95c64
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52867474"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Koordinálhatja az Azure DevTest Labs megvalósítása
Ebben a cikkben javasolt módszert biztosít a gyors üzembe helyezési és az Azure DevTest Labs megvalósítását. Az alábbi képen területdiagram általános eljárásait, előírásszerű útmutató a különböző iparági követelményeknek és forgatókönyveknek támogató rugalmasságot szem előtt tartásával.

![Az Azure DevTest Labs végrehajtási lépések](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már működik a következő elemeket a DevTest Labs irányító implementálása előtt:

- **Azure-előfizetés**: A próbacsapat férhet hozzá az Azure-előfizetésben helyezi üzembe az erőforrásokat. Ha a számítási feladatok csak fejlesztési és tesztelési célra, azt javasoljuk, hogy válassza ki a vállalati DevTest-ajánlat elérhető további rendszerképeket és az alacsonyabb díjszabás, Windows virtuális gépeken.
- **Helyszíni hozzáférés**: Ha szükséges, a helyszíni hozzáférés már konfigurálva van. A helyszíni hozzáférés Express Route vagy helyek közötti VPN-kapcsolat keresztül valósítható meg. Express Route kapcsolatok általában hetet is igénybe vehet számos létesíteni, azt javasoljuk, hogy az Expressroute rendelkeznek a projekt indítása előtt.
- **Próbacsapat csapatok**: A kezdeti fejlesztéshez project nak DevTest Labs használó együtt a megfelelő fejlesztési és tesztelési tevékenységek lett azonosítva, és a követelmények/célok és célkitűzések ezeket csapatok létesíteni.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>1. fázisú: Létesíteni a kezdeti hálózati topológia és kialakítás
Az Azure DevTest Labs-megoldás üzembe helyezésekor fókusz első területéhez, hogy a virtuális gépek tervezett kapcsolatot. A szükséges eljárások lépései:

1. Definiálása **az első IP-címtartományok** az Azure DevTest Labs-előfizetéshez társított. Ez a lépés szükséges előrejelzés a várható használat, a virtuális gépek számát, így megadhatja egy elegendő nagyságú jövőbeli bővítés céljából.
2. Azonosítsa **kívánt hozzáférési módok** be a DevTest Labs (például a külső / belső hozzáférés). Ebben a lépésben a lényeg meghatározni, hogy rendelkeznek-e a virtuális gépek nyilvános IP-címek (azaz közvetlenül az interneten).
3. Azonosíthatja és létesíteni **módszer a hálózati kapcsolat** az Azure a rest-tel felhőbeli környezetben és a helyszíni. Ha a kényszerített az Express Route-útválasztást, valószínű, hogy a virtuális gépeket kell-e a megfelelő proxybeállításokkal gyermekelemeinek a vállalati tűzfalon.
4. Ha a virtuális gépek kell **tartományhoz csatlakoztatott**, határozza meg, hogy csatlakoznak egy felhőalapú tartományt (például AAD Directory Services) vagy egy helyszíni tartományban. A helyszíni határozza meg, melyik szervezeti egység (OU), amely csatlakoztatja a virtuális gépek active Directoryban. Emellett győződjön meg arról, hogy felhasználók férhetnek hozzá, csatlakozzon (vagy egy szolgáltatás a fiók, amely képes machine-rekordok létrehozása a tartomány létrehozása)

## <a name="milestone-2-deploy-the-pilot-lab"></a>2. fázisú: A próbaüzem labor üzembe helyezése
Miután a hálózati topológia van beállítva, az első/próbaüzem tesztlabor hozható létre végrehajtja a következő lépéseket:

1. Hozzon létre egy kezdeti DevTest Labs-környezetben (részletes útmutató [Itt](https://github.com/Azure/fta-devops/blob/master/devtest-labs/articles/devtest-labs-walkthrough-it.md))
2. Engedélyezett Virtuálisgép-rendszerképek és a tesztkörnyezet segítségével méretek határozzák meg. Döntse el, hogy egyéni rendszerképek is feltölthetők az Azure-bA a DevTest Labs segítségével.
3. Biztonságos hozzáférés a labor létrehozása a kezdeti szerepkör alap hozzáférés vezérlők (RBAC) a tesztkörnyezethez (labortulajdonosok és -labor felhasználók) létrehozásával. Azt javasoljuk, hogy használjon szinkronizált active directory-fiókokat az Azure Active Directory identity a DevTest Labs szolgáltatással.
4. DevTest Labs használandó ütemezéseket, a költség, felügyeleti, igényelhető virtuális gépek, egyéni rendszerképek és képletek például szabályzatok konfigurálása.
5. Az online tárházban például az Azure Adattárakkal/Git létesíteni.
6. Döntse el, a nyilvános vagy privát adattárak vagy mindkettőt. JSON-sablonok rendszerezésére központi telepítések és a hosszú távú sustainment.
7. Ha szükséges, egyéni összetevők létrehozása. Ez a lépés nem kötelező. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>3. fázisú: Dokumentáció, támogatás, ismerje meg, és javíthatja a
A kezdeti kísérleti csapatok első lépések részletes támogatást kérhet. Használjon személyre, vonatkozó dokumentáció és támogatás helyen az Azure DevTest Labs szolgáltatásban a folyamatos bevezetését.

1. A kísérleti csapat bevezetése új DevTest Labs erőforrásaik (bemutatók, dokumentáció)
2. Kísérleti csapat tapasztalatok alapján a terv és dokumentáció kézbesítése igény szerint
3. Tegye formálissá folyamat bevezetése új csapatok (létrehozásáról és konfigurálásáról a labs, biztosít hozzáférést, és így tovább)
4. A kezdeti felvételi alapján, győződjön meg arról az IP-címterület eredeti előrejelzés továbbra is ésszerű és pontos
5. Győződjön meg, hogy a megfelelő megfelelőségi és biztonsági felülvizsgálat elvégzése után

## <a name="next-steps"></a>További lépések
Ez a sorozat következő cikkében talál: [irányítás az Azure DevTest Labs-infrastruktúra](devtest-lab-guidance-governance-resources.md)
