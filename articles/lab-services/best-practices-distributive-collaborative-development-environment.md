---
title: Az Azure DevTest Labs erőforrásainak elosztott együttműködésen alapuló fejlesztése
description: Gyakorlati tanácsok egy elosztott és együttműködésen alapuló fejlesztői környezet devtest labs erőforrások fejlesztéséhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170118"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Gyakorlati tanácsok az Azure DevTest Labs-erőforrások elosztott és együttműködésen alapuló fejlesztéséhez
Az elosztott együttműködésen alapuló fejlesztés lehetővé teszi a különböző csapatok vagy személyek számára a kódbázis fejlesztését és karbantartását. Ahhoz, hogy sikeres legyen, a fejlesztési folyamat attól függ, hogy képes létrehozni, megosztani és integrálni az információkat. Ez a kulcsfontosságú fejlesztési elv az Azure DevTest Labs-en belül használható. A tesztkörnyezeten belül többféle erőforrás létezik, amelyek általában egy vállalaton belül különböző laborok között oszlanak meg. A különböző típusú erőforrások két területre összpontosítanak:

- A laboron belül tárolt erőforrások (laboralapú)
- A laborhoz (kódtár-alapú) [kapcsolódó külső adattárakban](devtest-lab-add-artifact-repo.md) tárolt erőforrások. 

Ez a dokumentum néhány ajánlott eljárás, amely lehetővé teszi az együttműködés és a több csapat közötti terjesztés, miközben biztosítja a testreszabásés a minőség minden szinten.

## <a name="lab-based-resources"></a>Laboralapú erőforrások

### <a name="custom-virtual-machine-images"></a>Egyéni virtuálisgép-lemezképek
A laborok által éjszakánként üzembe helyezett egyéni lemezképek közös forrása is lehet. Részletes információt a [Képgyár című témakörben](image-factory-create.md)talál.    

### <a name="formulas"></a>Képletek
[A képletek laborspecifikusak,](devtest-lab-manage-formulas.md) és nem rendelkeznek elosztási mechanizmussal. A labor tagjai dolgoznak ki minden képletet. 

## <a name="code-repository-based-resources"></a>Kódtáron alapuló erőforrások
Két különböző szolgáltatás létezik, amelyek kódtárolókon, összetevőkön és környezeteken alapulnak. Ez a cikk bemutatja a szolgáltatásokat, és hogyan lehet a leghatékonyabban beállítani adattárak és munkafolyamatok, amelyek lehetővé teszik a rendelkezésre álló összetevők és környezetek testreszabása a szervezet vagy a csapat szintjén.  Ez a munkafolyamat a [forráskód-vezérlő elágazási stratégiáján](/azure/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops)alapul. 

### <a name="key-concepts"></a>Fő fogalmak
Az összetevők forrásadatai metaadatokat, parancsfájlokat tartalmaznak. A környezetek forrásinformációi metaadatokat és Erőforrás-kezelő sablonokat tartalmaznak olyan támogató fájlokkal, mint a PowerShell-parancsfájlok, a DSC-parancsfájlok, a Zip-fájlok és így tovább.  

### <a name="repository-structure"></a>Adattár szerkezete  
A forráskód-vezérlés (SCC) leggyakoribb konfigurációja egy többrétegű struktúra beállítása a kódfájlok (Erőforrás-kezelő sablonok, metaadatok és parancsfájlok) tárolására, amelyeket a laborokban használnak. Pontosabban hozzon létre különböző adattárakat a vállalkozás különböző szintjei által kezelt erőforrások tárolására:   

- Az egész vállalatra kiterjedő erőforrások.
- Részleg/divíziószintű erőforrások
- Csapatspecifikus erőforrások.

Ezek a szintek mindegyike egy másik tárházhoz kapcsolódik, ahol a főágnak termelési minőségűnek kell lennie. Az egyes adattárak ban lévő [ágak](/azure/devops/repos/git/git-branching-guidance?view=azure-devops) az adott erőforrások (összetevők vagy sablonok) fejlesztésére szolgálnának. Ez a struktúra jól illeszkedik a DevTest Labs-hez, mivel egyszerre több tárházat és több ágat egyszerűen csatlakoztathat a szervezet laborjaihoz. A tárház neve szerepel a felhasználói felületen (UI), hogy elkerülje a félreértéseket, ha azonos nevek, leírás és közzétevők vannak.
     
A következő ábrán két adattár látható: egy, az informatikai részleg által karbantartott vállalati adattár, és egy divízió-tárház, amelyet az R&D részleg tart fenn.

![Mintaelosztó és együttműködésen alapuló fejlesztési környezet](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Ez a réteges struktúra lehetővé teszi a fejlesztést, miközben a főág ban magasabb szintű minőséget tart fenn, miközben több adattár csatlakozik egy tesztkörnyezethez, nagyobb rugalmasságot tesz lehetővé.

## <a name="next-steps"></a>További lépések    
Lásd az alábbi cikkeket:

- Tárház hozzáadása egy laborhoz az [Azure Portalon](devtest-lab-add-artifact-repo.md) vagy az [Azure Erőforrás-kezelési sablonon](add-artifact-repository.md) keresztül
- [DevTest Labs-összetevők](devtest-lab-artifact-author.md)
- [DevTest Labs környezetek](devtest-lab-create-environment-from-arm.md).
