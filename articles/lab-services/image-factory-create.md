---
title: Lemezkép-gyár létrehozása az Azure DevTest Labs ben | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan állíthat be egy egyéni lemezkép-gyár at a Git-tárházban (Azure DevTest Labs) elérhető mintaparancsfájlok használatával.
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
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759448"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Egyéni lemezkép-gyár létrehozása az Azure DevTest Labsben
Ez a cikk bemutatja, hogyan állíthat be egyéni lemezkép-gyárat a [Git-tárházban](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)elérhető mintaparancsfájlok használatával.

## <a name="whats-an-image-factory"></a>Mi az a képgyár?
A lemezképgyár egy kódkénti konfigurációs megoldás, amely automatikusan, a kívánt konfigurációkkal automatikusan létrehozza és terjeszti a képeket. A képgyárban lévő képek mindig naprakészek, és a folyamatos karbantartás szinte nulla, ha az egész folyamat automatizált. És mivel az összes szükséges konfigurációk már a lemezképben, időt takarít meg a rendszer manuális konfigurálása után a virtuális gép jött létre az alap operációs rendszer.

A devtest labs-ben a fejlesztői asztal kész állapotba hozásának jelentős gyorsítója egyéni lemezképeket használ. A hátránya az egyéni képek, hogy van valami extra fenntartani a laborban. Például a termékek próbaverziói idővel lejárnak (vagy) az újonnan kiadott biztonsági frissítések nem lesznek alkalmazva, ami arra kényszerít bennünket, hogy rendszeresen frissítsük az egyéni lemezképet. A lemezképgyárban a rendszerdefiníciója be van jelentkezve a forráskód-ellenőrzésbe, és automatikus folyamattal rendelkezik a definíción alapuló egyéni lemezképek létrehozásához.

A megoldás lehetővé teszi a virtuális gépek létrehozásának sebességét az egyéni lemezképekről, miközben kiküszöböli a további folyamatos karbantartási költségeket. Ezzel a megoldással automatikusan létrehozhat egyéni lemezképeket, terjesztheti őket más DevTest Labs, és kivonhatja a régi képeket. Az alábbi videóban megismerheti a képgyárat, és azt, hogyan valósítható meg a DevTest Labs.  Az Összes Azure Powershell-parancsfájl szabadon elérhető, [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)és itt található: .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>A megoldás magas szintű nézete
A megoldás lehetővé teszi a virtuális gépek létrehozásának sebességét az egyéni lemezképekről, miközben kiküszöböli a további folyamatos karbantartási költségeket. Ezzel a megoldással automatikusan létrehozhat egyéni lemezképeket, és terjesztheti azokat más DevTest Labs. Az Azure DevOps (korábbi nevén Visual Studio Team Services) vezénylési motorként használja a DevTest Labs összes műveletének automatizálásához.

![A megoldás magas szintű nézete](./media/create-image-factory/high-level-view-of-solution.png)

Van egy [VSTS-bővítmény a DevTest Labs számára,](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) amely lehetővé teszi az alábbi lépések végrehajtását:

- Egyéni kép létrehozása
- Virtuális gép létrehozása
- Virtuális gép törlése
- Környezet létrehozása
- Környezet törlése
- Feltöltési környezet

A DevTest Labs bővítmény használatával egyszerűen elindíthatja az egyéni lemezképek automatikus létrehozását a DevTest Labs-ben.

Van egy alternatív megvalósítás a PowerShell-parancsfájl használatával egy összetettebb forgatókönyv. A PowerShell használatával teljes mértékben automatizálhatja a DevTest Labs alapú lemezképgyárat, amely a folyamatos integráció és a folyamatos kézbesítés (CI/CD) eszközláncban használható. Az ebben az alternatív megoldásban követett elvek a következők:

- A gyakori frissítések nem igényelnek módosításokat a lemezkép-gyárban. (például új típusú egyéni lemezkép hozzáadása, a régi képek automatikus kibontása, egy új "végpont" DevTest Labs hozzáadása egyéni képek fogadásához, és így tovább.)
- A gyakori módosításokat a forráskód-ellenőrzés támogatja (infrastruktúra kódként)
- Előfordulhat, hogy az egyéni lemezképeket fogadó DevTest Labs nem ugyanabban az Azure-előfizetésben van (a laborok előfizetései)
- A PowerShell-parancsfájlok újrafelhasználhatónak kell lenniük, hogy szükség esetén további gyárakat is feltudjunk pörgetni

## <a name="next-steps"></a>További lépések
Lépjen tovább a szakasz következő cikkére: [Lemezkép-gyár futtatása az Azure DevOps-ból](image-factory-set-up-devops-lab.md)
