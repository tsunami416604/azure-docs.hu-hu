---
title: Egy rendszerkép-előállító létrehozása az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogy egy egyéni rendszerkép-előállító létrehozása az Azure DevTest Labs szolgáltatásban.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 8190c2043d7d3daae91c93fd3b66126d0941710b
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361849"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Egy egyéni rendszerkép-előállító létrehozása az Azure DevTest Labs szolgáltatásban
Ez a cikk ismerteti az adatmegőrzési beállítás, az előállító karbantartása és régi lemezképek kivonása az összes a többi DevTest Labs szolgáltatásban a szervezet. 

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy a folytatás előtt követte ezeket a cikkeket:

- [Rendszerkép-előállító létrehozása](image-factory-create.md)
- [Rendszerkép-előállító futtatása az Azure DevOpsból](image-factory-set-up-devops-lab.md)
- [Egyéni rendszerképek mentése és továbbítása több tesztkörnyezetbe](image-factory-save-distribute-custom-images.md)

A következő elemek már helyén kell lennie:

- A kép Factory Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet
- Egy vagy több cél Azure DevTest Labs szolgáltatásban, ahol az előállító hamisított Kerberos-lemezképek terjesztése lesz
- Az Azure DevOps-projekt, amelyekkel automatizálható a rendszerkép gyári.
- A parancsfájlok és a configuration (a példánkban a fent használt azonos DevOps-projekt) tartalmazó kód forráshely
- Az Azure Powershell-feladatok builddefiníció
 
## <a name="setting-the-retention-policy"></a>A megőrzési házirend beállítása
Mielőtt beállítaná a törölje lépéseket, adja meg a hány korábbi lemezképek meg kíván őrizni a DevTest Labs szolgáltatásban. Ha követte a [egy lemezkép-előállító Futtatás az Azure DevOps](image-factory-set-up-devops-lab.md) konfigurálva a cikkben különböző hozhat létre változókat. Az egyiket volt **ImageRetention**. Ezt a változót `1`, ami azt jelenti, hogy a DevTest Labs nem vezet a egyéni rendszerképek előzményeit. Csak a legújabb elosztott képeket is elérhető lesz. Ha módosítja ezt a változót `2`: a legújabb elosztott lemezképet, és továbbra is ugyanúgy működik a korábbiakat. Beállíthatja, hogy ezt az értéket a DevTest Labs szolgáltatásban létrehozott fenntartani kívánt történelmi lemezképek számának meghatározásához.

## <a name="cleaning-up-the-factory"></a>A feldolgozó megtisztítása
Az első lépés a feldolgozó megtisztítása, hogy távolítsa el az arany lemezkép virtuális gépeket a lemezkép gyári. Ez a feladat, csakúgy, mint a korábbi parancsfájlok parancsfájl van. Az első lépés az, hogy adjon hozzá egy másik **Azure PowerShell-lel** a builddefiníció feladatot az alábbi képen látható módon:

![PowerShell lépés](./media/set-retention-policy-cleanup/powershell-step.png)

Miután az új feladat a listában, válassza ki az elemet, és adja meg az összes az adatokat az alábbi képen látható módon:

![Régi kép PowerShell feladat törlése](./media/set-retention-policy-cleanup/configure-powershell-task.png)

A parancsfájl-paraméterek: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Régi képeket kivonni 
Ez a feladat eltávolítja az összes régi lemezképet, csak egy előzmények egyező tartja a **ImageRetention** hozhat létre a változót. Adjon hozzá egy további **Azure PowerShell-lel** készítése feladatot a builddefiníció. Miután hozzáadta, válassza ki a feladatot, és adja meg a részleteket a következő képen látható módon: 

![Régi kép PowerShell feladat kivonása](./media/set-retention-policy-cleanup/retire-old-image-task.png)

A parancsfájl-paraméterek a következők: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>A build várólista
Most, hogy végrehajtotta a builddefiníció, várólista, győződjön meg arról, hogy minden működik, új fordítást. Miután a létrehozás sikeresen befejeződik, az új egyéni lemezképek jelennek meg a cél tesztkörnyezetben, és ellenőrizheti, ha a kép gyári labor, nincs üzembe helyezett virtuális gépek megjelenik. Továbbá ha a várólistára fel további buildek jelenik meg a karbantartási feladatok kivonása régi egyéni rendszerképek meg a DevTest Labs a összhangban a build változók beállított megőrzési értéket.

> [!NOTE]
> Ha végrehajtott a buildelési folyamat utolsó a cikk végén található az a sorozat, manuálisan törölje a virtuális gépeket a lemezkép gyári lab-ben egy új létrehozást queuing előtt készült.  A manuális törléshez lépés csak akkor van szükség, amíg minden beállítása, és ellenőrizze, működik-e.



## <a name="summary"></a>Összegzés
Most már egy futó lemezkép-előállítót, amely hoz létre, és a tesztkörnyezetek igény szerinti egyéni lemezképek terjesztése. Ez a pont, annak megfelelően beállítva, a képek első annyit és a cél labs azonosítása. Az előző cikkben említettek szerint a **Labs.json** fájlt a **konfigurációs** mappát adja meg, melyik lemezképeket elérhetővé kell tenni az egyes, a cél labs. További DevTest Labs termelhessen szervezete számára, ahogy egyszerűen adjon hozzá egy bejegyzést a Labs.json a új labor a kell.

Új lemezkép hozzáadása a gyári egyben egyszerű. Ha szeretne felvenni egy új rendszerképet, nyissa meg az előállító a [az Azure portal](https://portal.azure.com), keresse meg a DevTest Labs gyári, válassza ki a virtuális gép hozzáadása gombra, és válassza ki a kívánt Piactéri lemezképet és az összetevők. Kiválasztása helyett a **létrehozás** gombra, jelölje be az új virtuális gép **megtekintése az Azure Resource Manager-sablon**", és mentheti a sablont egy .JSON kiterjesztésű fájlt valahol a **GoldenImages** mappát a tárházban. A kép factory a következő futtatásakor hoz létre az egyéni rendszerkép.


## <a name="next-steps"></a>További lépések
1. [A build és kiadás ütemezése](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) a lemezkép factory rendszeres időközönként futtatásához. A gyári által generált képek rendszeres időközönként frissíti azt.
2. Győződjön meg arról, további hamisított Kerberos-lemezképek az előállító. Emellett érdemes [összetevők létrehozása](devtest-lab-artifact-author.md) , további információt a Virtuálisgép-beállítási feladatok létrehozása és a gyári képeken közé tartozik az összetevők.
4. Hozzon létre egy [build és kiadás külön](/azure/devops/pipelines/overview.md?view=azure-devops-2019) futtatásához a **DistributeImages** parancsfájl külön-külön. Ezt a szkriptet futtathatja is, ha Labs.json módosítja, és másolja a cél tesztkörnyezetekhez nélkül hozza létre újra az összes rendszerkép képek bekérése.

