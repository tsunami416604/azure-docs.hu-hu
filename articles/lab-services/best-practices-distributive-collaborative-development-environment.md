---
title: Az elosztott együttműködési fejlesztés az Azure DevTest Labs-erőforrások |} A Microsoft Docs
description: Ajánlott eljárásokat biztosít olyan DevTest Labs-erőforrások egy elosztott és együttműködő fejlesztési környezet beállítása.
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
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: d8892b2d00008c9d67f8bc28d1abb7d562dfd95c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079885"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Elosztott és együttműködő fejlesztés az Azure DevTest Labs-erőforrások ajánlott eljárásai
Az elosztott együttműködési fejlesztési lehetővé teszi a különböző csapatok vagy személyek fejlesztéséhez és karbantartásához alap egy kódot. Sikeres legyen, a fejlesztési folyamatot hozhat létre, megoszthatja és integrálhatja az adatokat képes függ. A legfontosabb fejlesztési elve szerint az Azure DevTest Labs is használható. Nincsenek számos különböző típusú erőforrás kulcsok tesztkörnyezetben általában egy vállalaton belül különböző labs között elosztott. A különböző típusú erőforrások fióktevékenységekkel két területekre:

- A tesztkörnyezet (tesztkörnyezet-alapú) belül belső tárolt erőforrások
- A tárolt erőforrások [a labor csatlakoztatott külső tárházak](devtest-lab-add-artifact-repo.md) (kód a tárház-alapú). 

Ez a dokumentum ismerteti az ajánlott eljárásokat, amelyek lehetővé teszik együttműködés és a terjesztési több csapatok fenntartásával, testreszabási és minőségi minden szinten.

## <a name="lab-based-resources"></a>Tesztlabor-alapú erőforrások

### <a name="custom-virtual-machine-images"></a>Egyéni virtuálisgép-lemezképek
Egy gyakori forrása labs éjszakai történik az üzembe helyezett egyéni rendszerképek is rendelkezhet. Részletes információkért lásd: [kép gyári](image-factory-create.md).    

### <a name="formulas"></a>Képletek
[A képletek](devtest-lab-manage-formulas.md) labor-specifikusak, és nem kell egy terjesztési mechanizmus. A lab tagok képletek fejlesztésének teheti meg. 

## <a name="code-repository-based-resources"></a>Tárház-alapú erőforrások kód
Nincsenek kódtárházakhoz, összetevők és környezet alapján két különböző funkciókkal. Ez a cikk az funkciók és a leghatékonyabban beállítva az adattárak és a munkafolyamat engedélyezése az elérhető összetevők és a környezetek a szervezet vagy csapat szintjén testre szabhatók az keresztül haladnak.  Ez a munkafolyamat szabványon alapul [forráskód szabályozhatja elágazási stratégia](/devops/repos/tfvc/branching-strategies-with-tfvc?view=azure-devops). 

### <a name="key-concepts"></a>Fő fogalmak
Az adatforrás adatai összetevők metaadatokat, parancsfájlok tartalmaz. Információk a környezetek szolgáltatás metaadatok és a Resource Manager-sablonok és minden kiegészítő fájlt, mint például a PowerShell-parancsfájlok, DSC-parancsfájlok, Zip-fájlokat és így tovább.  

### <a name="repository-structure"></a>Tárház szerkezete  
A leggyakrabban használt konfiguráció verziókövetési (SCC), hogy állítsa be a fájlok tárolására szolgáló kódot (Resource Manager-sablonok, metaadatokat és parancsfájlok), amely segítségével a labs szolgáltatásban egy többrétegű struktúrát. Pontosabban hozzon létre különböző adattárak a vállalat különböző szintjei által kezelt erőforrások tárolására:   

- Céges szintű erőforrás.
- Üzleti egység/osztás-szintű erőforrások
- Csapat-specifikus erőforrásokat.

Ezeket a szinteket mindegyike egy másik adattár, ahol a főágban van szükség, a gyártási minőségű mutató hivatkozás. A [ágak](/devops/repos/git/git-branching-guidance?view=azure-devops) minden tárház lenne megadott erőforrások (összetevők és sablonok) fejlesztését. Ez a struktúra igazítja a DevTest Labs segítségével könnyedén csatlakoztathatók a tárházak több és több ágat, a szervezet labs egy időben. Az adattár neve szerepel a felhasználói felület (UI) nincsenek azonos nevek kialakuló bizonytalanság elkerülése érdekében, a leírást és a közzétevő.
     
Az alábbi ábrán látható két tárházak: egy vállalati-tárházat az informatikai részleg által kezelt, és a egy R & D hányadosának osztási tárakba.

![A minta elosztó és együttműködő fejlesztési környezet](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
A réteges felépítés közben csatlakozik a labor több tárházak kellene a nagyobb rugalmasság érdekében lehetővé teszi, hogy a magasabb minőségű megtartásával, a főágba fejlesztést tesz lehetővé.

## <a name="next-steps"></a>További lépések    
Lásd az alábbi cikkeket:

- Egy adattár hozzáadása egy laborhoz segítségével a [az Azure portal](devtest-lab-add-artifact-repo.md) vagy keresztül [Azure Resource Management-sablonnal](add-artifact-repository.md)
- [DevTest Labs-összetevők](devtest-lab-artifact-author.md)
- [DevTest Labs-környezetben](devtest-lab-create-environment-from-arm.md).
