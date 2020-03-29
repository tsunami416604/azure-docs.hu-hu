---
title: Az Azure DevTest Labs megvalósításának koordinálása
description: Ez a cikk útmutatást nyújt az Azure DevTest Labs szervezeten belüli megvalósításának vezényléséhez.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e0ac09a68bda539fe7abd05fce1739d1a58a3c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "62127344"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Az Azure DevTest Labs megvalósításának koordinálása
Ez a cikk az Azure DevTest Labs gyors üzembe helyezéséhez és megvalósításához ajánlott megközelítést biztosít. Az alábbi kép az általános folyamatot előíró útmutatásként helyezi el, miközben megfigyeli a különböző iparági követelmények és forgatókönyvek támogatásának rugalmasságát.

![Az Azure DevTest Labs megvalósításának lépései](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Feltételezések
Ez a cikk feltételezi, hogy a DevTest Labs próbateszt végrehajtása előtt a következő elemek vannak érvényben:

- **Azure-előfizetés:** A kísérleti csapat hozzáférhet az erőforrások Azure-előfizetésbe való üzembe helyezéséhez. Ha a számítási feladatok csak fejlesztési és tesztelési, javasoljuk, hogy válassza ki az Enterprise DevTest ajánlat további elérhető lemezképek és alacsonyabb díjak a Windows virtuális gépeken.
- **Helyszíni hozzáférés:** Ha szükséges, a helyszíni hozzáférés már konfigurálva van. A helyszíni hozzáférés a helyek közötti VPN-kapcsolaton vagy az Expressz útvonalon keresztül valósítható meg. Az expressz útvonalon keresztüli kapcsolat létrehozása általában több hetet is igénybe vehet, ezért javasoljuk, hogy a projekt megkezdése előtt az expressz útvonal legyen a helyén.
- **Kísérleti csapatok:** A DevTest Labs-t használó kezdeti fejlesztési projektcsapat(ok) a megfelelő fejlesztési vagy tesztelési tevékenységekkel együtt lettek azonosítva, és követelményeket/célokat/célokat határoznak meg az adott csapatok számára.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>1. mérföldkő: Kezdeti hálózati topológia és tervezés létrehozása
Az Azure DevTest Labs-megoldás üzembe helyezésekor az első fókuszterület a virtuális gépek tervezett kapcsolatának létrehozása. A következő lépések ismertetik a szükséges eljárásokat:

1. Adja meg a DevTest Labs-előfizetéshez az Azure-ban hozzárendelt **kezdeti IP-címtartományokat.** Ez a lépés előre kell jelezve a várható használat a virtuális gépek száma, így elegendő nagy blokk ot biztosít a későbbi bővítéshez.
2. Azonosítsa a devtest labs-hez **való kívánt hozzáférés módszereit** (például külső / belső hozzáférést). Ennek a lépésnek a kulcspontja annak meghatározása, hogy a virtuális gépek rendelkeznek-e nyilvános IP-címekkel (azaz közvetlenül az internetről érhetők-e el).
3. Azonosítsa és hozza létre a **kapcsolódási módszereket** az Azure felhőkörnyezetének többi részével és a helyszíni környezettel. Ha a kényszerített útválasztás express route engedélyezve van, valószínű, hogy a virtuális gépek megfelelő proxykonfigurációk a vállalati tűzfalon való áthaladását.
4. Ha a virtuális gépek **tartományhoz**csatlakoznak, határozza meg, hogy felhőalapú tartományhoz (például AAD címtárszolgáltatások) vagy helyszíni tartományhoz csatlakoznak-e. A helyszíni, határozza meg, hogy melyik szervezeti egység (OU) az active directoryban, hogy a virtuális gépek csatlakozik. Ezenkívül ellenőrizze, hogy a felhasználók rendelkeznek-e a csatlakozáshoz való hozzáféréssel (vagy hozzon létre olyan szolgáltatásfiókot, amely képes gépi rekordokat létrehozni a tartományban)

## <a name="milestone-2-deploy-the-pilot-lab"></a>2. mérföldkő: A kísérleti labor telepítése
Miután a hálózati topológia a helyén van, az első/kísérleti labor a következő lépések végrehajtásával hozható létre:

1. Hozzon létre egy kezdeti DevTest Labs környezetet (lépésről-lépésre itt [található)](https://github.com/Azure/fta-devops/blob/master/devtest-labs/articles/devtest-labs-walkthrough-it.md)
2. Határozza meg a megengedett virtuális gép lemezképeit és méreteit a laborban való használatra. Döntse el, hogy az egyéni lemezképek feltölthetők-e az Azure-ba a DevTest Labs használatával való használatra.
3. Biztonságos hozzáférést biztosít a laborhoz a tesztkörnyezet (labortulajdonosok és tesztkörnyezet-felhasználók) kezdeti szerepkör-alap hozzáférés-vezérlésének (RBAC) létrehozásával. Azt javasoljuk, hogy az Azure Active Directoryval szinkronizált active directory fiókok at a DevTest Labs identitás használata.
4. Konfigurálja a DevTest Labs-t olyan házirendek használatára, mint az ütemezések, a költségkezelés, a követelhető virtuális gépek, az egyéni lemezképek vagy a képletek.
5. Hozzon létre egy online tárház, például az Azure Repos/Git.
6. Döntsön a nyilvános vagy magánadattárak használatáról vagy a kettő kombinációjáról. JSON-sablonok rendszerezése a telepítésekhez és a hosszú távú fenntartáshoz.
7. Szükség esetén hozzon létre egyéni összetevőket. Ez a lépés nem kötelező. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>3. mérföldkő: Dokumentáció, támogatás, tanulás és fejlesztés
A kezdeti kísérleti csapatok nak mélyreható támogatásra lehet szükségük az első lépésekhez. Az Azure DevTest Labs folyamatos bevezetéséhez használja a felhasználói élményt, és biztosítsa, hogy a megfelelő dokumentáció és támogatás legyen érvényben.

1. A kísérleti csapatok bevezetése az új DevTest Labs-erőforrásokhoz (bemutatók, dokumentációk)
2. A kísérleti csapatok tapasztalatai alapján szükség szerint tervezze meg és szállítsa ki a dokumentációt
3. Formalizálása folyamat onboarding új csapatok (létrehozása és konfigurálása labs, hozzáférés biztosítása, stb)
4. A kezdeti bevitel alapján ellenőrizze, hogy az IP-címterület eredeti előrejelzése még mindig ésszerű és pontos-e
5. A megfelelő megfelelőségi és biztonsági felülvizsgálatok elvégzésének biztosítása

## <a name="next-steps"></a>További lépések
Tekintse meg a sorozat következő cikkét: [Az Azure DevTest Labs infrastruktúrájának irányítása](devtest-lab-guidance-governance-resources.md)
