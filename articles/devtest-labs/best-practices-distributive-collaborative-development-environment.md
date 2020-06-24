---
title: Azure DevTest Labs erőforrások elosztott együttműködési fejlesztése
description: A DevTest Labs erőforrásainak fejlesztéséhez ajánlott eljárásokat biztosít egy elosztott és együttműködő fejlesztési környezet beállításához.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9469591b1945adaffca973828d619d5d06655262
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84898730"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Ajánlott eljárások az Azure DevTest Labs-erőforrások elosztott és együttműködő fejlesztéséhez
Az elosztott együttműködési fejlesztés lehetővé teszi a különböző csapatok vagy személyek számára, hogy alapszintű kódot fejlesszenek és őrizzenek meg. Ahhoz, hogy sikeres legyen, a fejlesztési folyamat az információk létrehozására, megosztására és integrálására való képességtől függ. Ez a kulcs-fejlesztési elv a Azure DevTest Labson belül használható. A laborban többféle típusú erőforrás van, amelyeket általában a különböző laborok osztanak szét egy vállalaton belül. A különböző típusú erőforrások két területre vannak koncentrálva:

- A laboron belül belsőan tárolt erőforrások (Lab-alapú)
- A laborhoz (code Repository-alapú) [kapcsolódó külső adattárakban](devtest-lab-add-artifact-repo.md) tárolt erőforrások. 

Ez a dokumentum ismerteti azokat az ajánlott eljárásokat, amelyek lehetővé teszik az együttműködés és a terjesztést több csapat között, miközben minden szinten biztosítja a testreszabást és a minőséget.

## <a name="lab-based-resources"></a>Tesztkörnyezet-alapú erőforrások

### <a name="custom-virtual-machine-images"></a>Egyéni virtuálisgép-rendszerképek
Az Egyéni rendszerképek közös forrásaként is elvégezheti a laborok éjszakai üzembe helyezését. Részletes információ: a [rendszerkép-előállító](image-factory-create.md).    

### <a name="formulas"></a>Képletek
A [képletek](devtest-lab-manage-formulas.md) labor-specifikusak, és nincs terjesztési mechanizmusuk. A labor tagjai a képletek fejlesztését végzik. 

## <a name="code-repository-based-resources"></a>Adattár-alapú erőforrások
Két különböző funkció található a kódokon, az összetevőkön és a környezeteken alapulva. Ez a cikk a funkciók és a leghatékonyabban a Tárházak és a munkafolyamatok beállítását mutatja be, így lehetővé teszi az elérhető összetevők és környezetek testreszabását a szervezet szintjén vagy a csapat szintjén.  Ez a munkafolyamat a szabványos [forráskód-vezérlési elágazási stratégián](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)alapul. 

### <a name="key-concepts"></a>Fő fogalmak
Az összetevőkre vonatkozó információforrás metaadatokat, parancsfájlokat tartalmaz. A környezetekhez tartozó információforrás metaadatokat és Resource Manager-sablonokat tartalmaz minden olyan támogató fájllal, mint például a PowerShell-parancsfájlok, a DSC-parancsfájlok, a zip-fájlok stb.  

### <a name="repository-structure"></a>Tárház szerkezete  
A forráskód-vezérlés (SCC) leggyakoribb konfigurációja egy többrétegű struktúra beállítása a laborokban használt programkódok (Resource Manager-sablonok, metaadatok és parancsfájlok) tárolására. Konkrétan hozzon létre különböző adattárakat a vállalat különböző szintjei által felügyelt erőforrások tárolásához:   

- Vállalati szintű erőforrások.
- Üzleti egység/részleg – széles körű erőforrások
- A csoportra jellemző erőforrások.

Ezen szintek mindegyike egy másik adattárra mutat, ahol a fő ág az éles minőséghez szükséges. Az egyes adattárokban lévő [ágak](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) az adott erőforrások (összetevők vagy sablonok) fejlesztéséhez szükségesek. Ez a struktúra jól illeszkedik a DevTest Labs szolgáltatáshoz, mivel egyszerre több tárház és több ág összekapcsolására is lehetőség van a szervezet laborjában. Az adattár nevét a felhasználói felület (UI) tartalmazza, hogy elkerülje a félreértéseket, ha ugyanazok a nevek, a leírások és a közzétevők.
     
Az alábbi ábrán két tárház látható: az IT-részleg által karbantartott vállalati tárház, valamint az R&D divízió által karbantartott divízió-tárház.

![Példaként szolgáló terjesztési és együttműködési fejlesztési környezet](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Ez a rétegzett struktúra lehetővé teszi a fejlesztést, miközben magasabb szintű minőséget tart fenn a fő ág esetében, miközben a laborhoz több tárház csatlakozik, így nagyobb rugalmasságot biztosít.

## <a name="next-steps"></a>További lépések    
Lásd az alábbi cikkeket:

- Adattár hozzáadása laborhoz a [Azure Portal](devtest-lab-add-artifact-repo.md) vagy az [Azure Resource Management-sablon](add-artifact-repository.md) használatával
- [DevTest Labs-összetevők](devtest-lab-artifact-author.md)
- [DevTest Labs-környezetek](devtest-lab-create-environment-from-arm.md).
