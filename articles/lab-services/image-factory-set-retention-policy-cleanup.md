---
title: Adatmegőrzési házirend beállítása az Azure DevTest Labsben | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhat adatmegőrzési szabályzatot, hogyan kell megtisztítani a gyárat, és kivonni a régi képeket a DevTest Labs-ből.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759414"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Adatmegőrzési szabályzat beállítása az Azure DevTest Labsben
Ez a cikk egy adatmegőrzési szabály beállítását, a gyár megtisztítását és a régi képek kivisszavonulását ismerteti a szervezet összes többi DevTest Labs-éből. 

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg arról, hogy követte ezeket a cikkeket, mielőtt továbbhaladna:

- [Rendszerkép-előállító létrehozása](image-factory-create.md)
- [Rendszerkép-előállító futtatása az Azure DevOpsból](image-factory-set-up-devops-lab.md)
- [Egyéni rendszerképek mentése és továbbítása több tesztkörnyezetbe](image-factory-save-distribute-custom-images.md)

A következő elemeknek már a helyükön kell lenniük:

- Az Azure DevTest Labs lemezképgyárának laborja
- Egy vagy több cél Azure DevTest Labs, ahol a gyár aranyszínű képeket fog terjeszteni
- Egy Azure DevOps-projekt a lemezképgyár automatizálására szolgál.
- A parancsfájlokat és a konfigurációt tartalmazó forráskód helye (példánkban a fent használt DevOps-projektben)
- Az Azure Powershell-feladatok vezényléséhez egy builddefiníció
 
## <a name="setting-the-retention-policy"></a>Az adatmegőrzési házirend beállítása
A clean up lépések konfigurálása előtt adja meg, hogy hány történelmi lemezképet szeretne megőrizni a DevTest Labs-ben. Ha követte a [lemezképgyár futtatása az Azure DevOps-cikkből,](image-factory-set-up-devops-lab.md) konfigurált a különböző build változók. Az egyik az **ImageRetention**volt. Ezt a változót a , `1`ami azt jelenti, hogy a DevTest Labs nem tartja karban az egyéni lemezképek előzményeit. Csak a legújabb elosztott képek lesznek elérhetők. Ha ezt a `2`változót a változóra módosítja, a legújabb elosztott kép és az előzőek is megmaradnak. Ezt az értéket beállíthatja a DevTest Labs ben karban tartani kívánt történelmi képek számának meghatározásához.

## <a name="cleaning-up-the-factory"></a>A gyár megtisztítása
A gyár tisztításának első lépése az arany képvirtuális gépek eltávolítása a lemezképgyárból. Van egy script, hogy ezt a feladatot, mint a korábbi szkriptek. Az első lépés egy másik **Azure Powershell-feladat** hozzáadása a builddefinícióhoz az alábbi képen látható módon:

![PowerShell-lépés](./media/set-retention-policy-cleanup/powershell-step.png)

Miután az új feladat a listában, jelölje ki az elemet, és töltse ki az összes részletet, ahogy az az alábbi képen látható:

![Régi képek karbantartása PowerShell-feladat](./media/set-retention-policy-cleanup/configure-powershell-task.png)

A parancsfájl paraméterei a következők: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Régi képek kivonása 
Ez a feladat eltávolítja a régi képeket, és csak az **ImageRetention** build változónak megfelelő előzményeket tart meg. Adjon hozzá egy további **Azure Powershell** buildelési feladatot a builddefinícióhoz. Miután hozzáadta, jelölje ki a feladatot, és töltse ki a részleteket az alábbi képen látható módon: 

![Régi képek kivonása PowerShell-feladat](./media/set-retention-policy-cleanup/retire-old-image-task.png)

A parancsfájl paraméterei a következők:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>A build várólistára állítása
Most, hogy befejezte a build definícióját, várólistára egy új buildet, hogy megbizonyosodjon arról, hogy minden működik. Miután a build sikeresen befejeződött, az új egyéni rendszerképek megjelennek a céllaborban, és ha ellenőrzi a rendszerkép gyári laborban, nem lát kiépített virtuális gépek. Továbbá, ha további buildek várólistára, láthatja a tisztítási feladatok kivonuló régi egyéni rendszerképeket a DevTest Labs a build változókban beállított megőrzési értéknek megfelelően.

> [!NOTE]
> Ha a sorozat utolsó cikkének végén végrehajtotta a buildfolyamatot, manuálisan törölje a lemezképgyári laborban létrehozott virtuális gépeket, mielőtt új buildet állítana fel.  A kézi razzia lépés csak akkor van szükség, amíg mi meg mindent, és ellenőrizze, hogy működik.



## <a name="summary"></a>Összefoglalás
Most már van egy futó lemezkép-gyár, amely képes generálni és terjeszteni az egyéni képeket a laborok igény szerint. Ezen a ponton, ez csak egy kérdés, hogy a képek megfelelően beállítva, és azonosítja a cél laborok. Az előző cikkben említettek szerint a **Konfigurációs** mappában található **Labs.json** fájl meghatározza, hogy mely képeket kell elérhetővé tenni az egyes céllaboratóriumokban. Ahogy más DevTest Labs a szervezethez, egyszerűen hozzá kell adnia egy bejegyzést a Labs.json az új labor.

Új kép hozzáadása a gyárhoz is egyszerű. Ha azt szeretné, hogy egy új lemezképet a gyárban megnyitja az [Azure Portalon,](https://portal.azure.com)keresse meg a gyári DevTest Labs, válassza ki a gombot, hogy adjunk egy virtuális gép, és válassza ki a kívánt piactéri rendszerkép és összetevők. Ahelyett, hogy a **Létrehozás** gombra kattintana az új virtuális gép létrehozásához, válassza az **Azure Resource Manager-sablon megtekintése**" lehetőséget, és mentse a sablont .json fájlként valahol a **GoldenImages** mappában a tárházban. A következő alkalommal, amikor futtatja a lemezképgyárat, létrehozza az egyéni lemezképet.


## <a name="next-steps"></a>További lépések
1. [Ütemezze a build/kiadást](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) a lemezképgyár rendszeres futtatásához. Rendszeresen frissíti a gyárilag generált képeket.
2. Készíts több arany képeket a gyár. Azt is megfontolhatja, hogy a virtuális gép beállítási feladatainak további részeinek parancsfájlfuttatásához [hozzon létre összetevőket,](devtest-lab-artifact-author.md) és a gyári rendszerképekben tartalmazza az összetevőket.
4. Hozzon létre egy [külön build/kiadás](/azure/devops/pipelines/overview?view=azure-devops-2019) a **DistributeImages** parancsfájl külön futtatásához. Ezt a parancsfájlt futtathatja, ha módosítja a Labs.json fájlját, és a képeket a céllaborokba másolja anélkül, hogy újra létre kellene hoznia az összes képet.

