---
title: Az Azure DevTest Labs használatának népszerű forgatókönyvei
description: Ez a cikk az Azure DevTest Labs használatának elsődleges forgatókönyveit és két általános elérési utat tartalmaz a szolgáltatás szervezetben való használatának megkezdéséhez.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60773808"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Az Azure DevTest Labs használatának népszerű forgatókönyvei
A vállalat igényeitől függően a DevTest Labs konfigurálható különböző követelményeknek.  Ez a cikk ismerteti a népszerű forgatókönyvek. Minden forgatókönyv a DevTest Labs és a forgatókönyvek megvalósításához használt erőforrások használatával járó előnyöket tartalmazza.  

- Fejlesztői asztalok
- Tesztkörnyezetek
- Képzések, gyakorlati laborok és hackathons
- Sandboxed vizsgálatok
- Osztályterem-tesztkörnyezetek

## <a name="developer-desktops"></a>Fejlesztői asztalok
A fejlesztők gyakran különböző követelményeknek támasztják be a különböző projektek fejlesztői gépeit. A DevTest Labs segítségével a fejlesztők hozzáférhetnek az igény szerinti virtuális gépekhez, amelyek a leggyakoribb forgatókönyvekhez vannak konfigurálva. A DevTest Labs a következő előnyöket nyújtja:

- A szervezetek közös fejlesztői gépeket biztosíthatnak a csapatok közötti konzisztenciát biztosítóan.
- A fejlesztők gyorsan kiépíthetik fejlesztői gépeiket igény szerint, vagy [igényelhetnek egy már előre konfigurált gépet.](devtest-lab-add-claimable-vm.md)
- A fejlesztők önkiszolgáló módon is kiépíthetnek erőforrásokat előfizetési szintű engedélyek nélkül.
- Az informatikai vagy rendszergazdák [előre meghatározhatják a hálózati topológiát,](devtest-lab-configure-vnet.md) és a fejlesztők közvetlenül használhatják azt egyszerű és intuitív módon anélkül, hogy speciális hozzáférést igényelne.
- A fejlesztők egyszerűen [testreszabhatják](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) fejlesztőgépeiket, ha szükséges.
- A rendszergazdák a következők biztosításával szabályozhatják a költségeket:
    - A fejlesztők [nem kaphatnak több virtuális gépet,](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) mint amennyi a fejlesztéshez szükséges
    - [A virtuális gépek leállnak,](devtest-lab-set-lab-policy.md#set-auto-shutdown) ha nincs használatban
    - Csak [a virtuálisgép-példányok méretének egy részhalmazának engedélyezése](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) az adott laborokhoz
    - Az egyes [tesztkörnyezetek költségcéljainak és értesítéseinek kezelése.](devtest-lab-configure-cost-management.md)

További olvasmány: [Azure DevTest Labs használata fejlesztőknek.](devtest-lab-developer-lab.md) 

## <a name="test-environments"></a>Tesztkörnyezetek
A tesztkörnyezetek létrehozása és kezelése a vállalaton belül jelentős erőfeszítést igényelhet. A DevTest Labs segítségével a tesztkörnyezetek könnyen létrehozhatók, frissíthetők vagy duplikálhatók. Lehetővé teszi a csapatok számára, hogy szükség esetén hozzáférjenek egy teljesen konfigurált környezethez. Ebben a forgatókönyvben a DevTest Labs a következő előnyöket biztosítja:

- A szervezetek közös tesztelési környezeteket biztosíthatnak a csapatok közötti konzisztenciát biztosító anonzidmát.
- A tesztelők tesztelhetik alkalmazásuk legújabb verzióját a Windows és Linux környezetek gyors kiépítésével az újrafelhasználható sablonok használatával.
- A rendszergazdák a labort az Azure DevOps-hoz csatlakoztathatják a DevOps-forgatókönyvek engedélyezéséhez
- A labortulajdonosok a következők biztosításával szabályozhatják a költségeket:
    - [A környezetekben lévő virtuális gépek leállnak,](devtest-lab-set-lab-policy.md#set-auto-shutdown) ha nincsenek használatban
    - Csak [a virtuálisgép-példányok méretének egy részhalmazának engedélyezése](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) az adott laborokhoz
    - Az egyes [tesztkörnyezetek költségcéljainak és értesítéseinek kezelése.](devtest-lab-configure-cost-management.md)

További információ: [Azure DevTest Labs használata virtuális géphez és PaaS-tesztkörnyezetek.](devtest-lab-test-env.md)

## <a name="sandboxed-investigations"></a>Sandboxed vizsgálatok
A fejlesztők gyakran vizsgálják a különböző technológiákat vagy infrastruktúra-tervezést. Alapértelmezés szerint a DevTest Labs-sel létrehozott összes környezet a saját erőforráscsoportban jön létre. A DevTest Labs-felhasználó csak olvasási hozzáférést kap ezekhez az erőforrásokhoz. Azonban a fejlesztők számára, akiknek több ellenőrzésre van szükségük, egy labor szintű beállítás frissíthető, hogy [közreműködői jogokat](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) adjon a fejlesztő DevTest Labs felhasználónak minden általuk létrehozott környezetben.  A DevTest Labs segítségével a fejlesztők automatikusan közreműködői engedélyt kaphatnak a laborban létrehozott környezetekben.  Ez a forgatókönyv lehetővé teszi a fejlesztők számára, hogy a fejlesztési vagy tesztelési környezetükhöz szükséges adatoknak köszönhetően azure-erőforrásokat adjanak hozzá és/vagy módosítsanak. A [költség erőforrás on-val](devtest-lab-configure-cost-management.md#view-cost-by-resource) lehetővé teszi a Labor-tulajdonosok nyomon követheti a vizsgálatokhoz használt egyes környezetek költségét.

További információt a [Hozzáférési jogok beállítása környezeterőforrás-csoporthoz](https://aka.ms/dtl-sandbox)című témakörben talál.

## <a name="trainings-hands-on-labs-and-hackathons"></a>Tréningek, gyakorlati laborok és hackathonok 
Az Azure DevTest Labs laborja nagyszerű tárolóként működik az olyan átmeneti tevékenységekhez, mint a műhelyek, gyakorlati laborok, tréningek vagy hackathonok.  A szolgáltatás lehetővé teszi, hogy hozzon létre egy labor, ahol egyéni sablonokat, hogy minden gyakornok használhatja, hogy azonos és elszigetelt környezetek képzés. Ebben a forgatókönyvben a DevTest Labs a következő előnyöket biztosítja:

- [A szabályzatok](devtest-lab-set-lab-policy.md) biztosítják, hogy a gyakornokok csak annyi erőforrást kaphassanak, mint például a virtuális gépek, amelyekre szükségük van.
- Az előre konfigurált és létrehozott gépeket a gyakornok egyetlen műveletével [igénylik.](devtest-lab-add-claimable-vm.md)
- A laborokat a tesztkörnyezet [URL-címének](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)elérésével osztják meg a gyakornokokkal.
- A virtuális gépek [lejárati dátumai](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) biztosítják, hogy a gépek törlése után már nincs rájuk szükség.
- A labor és az összes [kapcsolódó erőforrás](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) [törlése,](devtest-lab-delete-lab-vm.md#delete-a-lab) ha a képzés véget ért.

További információ: [Azure DevTest Labs oktatáshoz.](devtest-lab-training-lab.md)  

## <a name="proof-of-concept-vs-scaled-deployment"></a>A koncepció igazolása és a méretezett telepítés
Miután úgy döntött, hogy vizsgálja meg a DevTest Labs, két általános utak előre: Proof of Concept vs skálázott központi telepítés.  

A **méretezett üzembe helyezés** hetek/hónapok felülvizsgálatából és tervezéséből áll, azzal a szándékkal, hogy a DevTest Labs-t a több száz vagy több ezer fejlesztővel rendelkező teljes vállalatra telepítse.

A koncepció üzembe **helyezésének igazolása** egyetlen csapat koncentrált erőfeszítésére összpontosít a szervezeti érték létrehozása érdekében. Bár lehet csábító, hogy egy méretezett üzembe helyezés, a megközelítés általában nem gyakrabban, mint a koncepció igazolása opció. Ezért azt javasoljuk, hogy indítsa el a kicsi, tanulni az első csapat, ismételje meg ugyanazt a megközelítést két-három további csapat, majd tervezze meg a kibővített üzembe helyezés a megszerzett ismeretek alapján. A koncepció sikeres igazolása érdekében azt javasoljuk, hogy válasszon ki egy vagy két csapatot, és azonosítsa azok forgatókönyveit (fejlesztési környezet vs tesztkörnyezetek), dokumentálja az aktuális használati eseteiket, és telepítse a DevTest Labs-t.

## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [DevTest Labs-fogalmak](devtest-lab-concepts.md)
- [DevTest Labs – gyakori kérdések](devtest-lab-faq.md)

