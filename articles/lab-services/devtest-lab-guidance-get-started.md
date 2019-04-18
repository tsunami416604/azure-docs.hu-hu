---
title: Az Azure DevTest Labs használatával gyakori felhasználási helyzetek
description: Ez a cikk az elsődleges forgatókönyvek az Azure DevTest Labs szolgáltatásban és a két általános elérési segítségével megkezdheti a szolgáltatás használatát a szervezetben.
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59272383"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Az Azure DevTest Labs használatával gyakori felhasználási helyzetek
A vállalat igényeitől függően DevTest Labs konfigurálható különböző követelményeinek.  Ez a cikk ismerteti a gyakori felhasználási helyzetek. Egyes forgatókönyvek előnyeit, illetve a forgatókönyvek megvalósításához használja a DevTest Labs és erőforrások használatával indított ismerteti.  

- Fejlesztői asztalok
- Tesztelési környezetek
- Képzési előadások, laborgyakorlatok és ötletbörzékhez
- Elkülönített vizsgálatokhoz
- Osztályterem-tesztkörnyezetek

## <a name="developer-desktops"></a>Fejlesztői asztalok
A fejlesztők gyakran rendelkeznek fejlesztői gépek különböző projektek különböző követelmények vonatkoznak. A DevTest Labs szolgáltatással a fejlesztők hozzáférhet a leggyakoribb forgatókönyvek igényeinek megfelelően konfigurált igény szerinti virtuális gépekhez. DevTest Labs a következő előnyöket nyújtja:

- Szervezetek számára biztosíthat a közös fejlesztési gépek különböző csapatokkal konzisztencia biztosítása.
- A fejlesztők gyorsan üzembe helyezhet igény szerinti fejlesztési gépeik vagy [előre konfigurált meglévő gépek jogcím](devtest-lab-add-claimable-vm.md).
- A fejlesztők helyezhet üzembe erőforrásokat önkiszolgáló módon anélkül, hogy előfizetés-szintű engedélyeit.
- Informatikai vagy rendszergazdák is [előre adja meg a hálózati topológia](devtest-lab-configure-vnet.md) és a fejlesztők közvetlenül használható egyszerű és intuitív módon semmilyen különleges hozzáférés nélkül.
- A fejlesztők könnyedén [testreszabása](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) fejlődésük gépek szükség szerint.
- A rendszergazdák biztosításával, hogy szabályozhatja költségeit:
    - A fejlesztők [nem olvasható be a további virtuális gépeket](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) fejlesztéshez szükséges, mint
    - [Virtuális gépek leállnak,](devtest-lab-set-lab-policy.md#set-auto-shutdown) amikor nincs használatban
    - Csak [lehetővé teszi egy Virtuálisgép-példányméretek részét](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) az adott laborral kapcsolatos
    - [Költség célozza meg, és értesítéseket kezelése](devtest-lab-configure-cost-management.md) egyes laborban.

További információ, lásd: [használata az Azure DevTest Labs-fejlesztőknek](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Tesztelési környezetek
Létrehozása és tesztelési környezetek kezelése a vállalaton belül akkor is egy jelentős erőfeszítésekre van szükség. A DevTest Labs szolgáltatással tesztelési környezetek könnyen létrehozhatják, frissítése, vagy ismétlődik. Lehetővé teszi a csapatok egy teljes körűen konfigurált környezet eléréséhez szükséges. Ebben a forgatókönyvben a DevTest Labs a következő előnyöket nyújtja:

- Szervezet konzisztencia biztosítása csapatok közös tesztelési környezetek is nyújt.
- Tesztelőknek újrahasznosítható sablonokkal Windows és Linux-környezetek gyors létrehozásával tesztelheti a legfrissebb verziójának alkalmazását.
- Rendszergazdák kapcsolódhatnak a labor létrehozása az Azure DevOps, DevOps-forgatókönyvekre engedélyezése
- Labortulajdonosok biztosításával, hogy szabályozhatja költségeit:
    - [Virtuális gépek környezetekben állítsa le az](devtest-lab-set-lab-policy.md#set-auto-shutdown) amikor nincs használatban
    - Csak [lehetővé teszi egy részét a Virtuálisgép-példányméretek](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) az adott labs
    - [Költség célozza meg, és értesítéseket kezelése](devtest-lab-configure-cost-management.md) egyes laborban.

További információkért lásd: [használata az Azure DevTest Labs virtuális gépek és PaaS-tesztelési környezetek](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Elkülönített vizsgálatokhoz
A fejlesztők gyakran vizsgálja meg a különböző technológiák vagy infrastruktúra-kialakítási. Alapértelmezés szerint a DevTest Labs szolgáltatással létrehozott összes környezetet jönnek létre a saját erőforráscsoport. A DevTest Labs-felhasználó lekérdezi ezeket az erőforrásokat csak olvasási hozzáférést. Azonban fejlesztőknek szól, akik nagyobb felügyeletet, a labor kiterjedő beállítás frissítheti biztosíthat [közreműködői jogokkal](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) a származó DevTest Labs-felhasználó számára az összes környezetben hoznak létre.  A DevTest Labs szolgáltatással a fejlesztők kaphatnak automatikusan hoznak létre a tesztkörnyezetben környezetek közreműködői engedélyt.  Ebben a forgatókönyvben lehetővé teszi a fejlesztők számára, hogy adjon hozzá és/vagy módosítani Azure-erőforrások, mert a fejlesztési-tesztelési környezetek szükséges. A [költségek erőforrás szerint](devtest-lab-configure-cost-management.md#view-cost-by-resource) lap lehetővé teszi, hogy a vizsgálatok során használt környezeteket költségeinek nyomon Labortulajdonosok.

További információkért lásd: [hozzáférési jogosultságok beállítása egy környezet erőforráscsoportot](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Betanítások, laborgyakorlatokkal és ötletbörzékhez 
Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet átmeneti tevékenységeket, például workshopokat, gyakorlati labormodulok, betanítások vagy ötletbörzékhez nagyszerű tárolójaként szolgál.  A szolgáltatás lehetővé teszi, hogy egy tesztkörnyezet, ahol megadhatja, hogy minden tanuló segítségével képzéshez azonosak, és elkülönített környezeteket hozhat létre egyéni sablonok létrehozása. Ebben a forgatókönyvben a DevTest Labs a következő előnyöket nyújtja:

- [Házirendek](devtest-lab-set-lab-policy.md) biztosítása a résztvevők csak a get-erőforrások, például a virtuális gépek számát.
- Előre konfigurált, és létrehozott gépek [igényelt](devtest-lab-add-claimable-vm.md) egyetlen művelettel a tanuló.
- Laborok megosztott résztvevők elérésével [URL-címet a tesztkörnyezethez](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Lejárati dátum](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) a virtuális gépek győződjön meg arról, hogy gépek törlése után már nem szükséges.
- Könnyen [tesztkörnyezet törlése](devtest-lab-delete-lab-vm.md#delete-a-lab) és az összes [kapcsolódó erőforrások](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) mikor a képzés felett van.

További információkért lásd: [használata az Azure DevTest Labs képzéshez](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>A koncepció igazolása és a méretezett üzembe helyezés
Ha úgy dönt, hogy ismerje meg a DevTest Labs, nincsenek két általános elérési utak előre: Koncepció vs méretezett telepítési megvalósíthatósági.  

A **üzembe helyezési horizontálisan** hét/hónap áttekintése és üzembe helyezése a DevTest Labs a teljes vállalat, amely rendelkezik, több száz vagy ezer főig megjelölésű tervezési áll.

A **megvalósíthatósági példa** telepítési szervezeti érték egy csoportot a koncentrált erőfeszítés összpontosít. Úgy gondolja, hogy méretezett központi telepítés csábító lehet, amíg a megközelítést általában többször a koncepció igazolása fogalom beállítás sikertelen. Ezért azt javasoljuk, hogy kezdhetik, ismerje meg az első csapat, ismételje meg a sémának két-három további csapatok számára, és majd szerzett ismeretek alapján vertikálisan telepítés megtervezését. Sikeres a koncepció igazolása javasoljuk, hogy válasszon ki egy vagy két csapat, és a saját forgatókönyveinek (a fejlesztési környezet és tesztelési környezetek), dokumentálni az aktuális használati esetek, és üzembe helyezése a DevTest Labs.

## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [DevTest Labs-fogalmak](devtest-lab-concepts.md)
- [DevTest Labs – gyakori kérdések](devtest-lab-faq.md)

