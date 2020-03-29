---
title: A projektvezető feladatai a csapatadat-elemzési folyamatban
description: A projektérdeklődők tevékenységeinek részletes forgatókönyve egy csapatadat-elemzési folyamat csapatában
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a4d3a1480a852218e698862a509c4af45e49eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76714410"
---
# <a name="project-lead-tasks-in-the-team-data-science-process"></a>Projektvezető tevékenységek a csapatadat-elemzési folyamatban

Ez a cikk azokat a tevékenységeket ismerteti, amelyeket a *projektérdeklődők* befejeznek, hogy a csapatcsoportuk számára egy tárházat állítsanak be a [csapatadat-elemzési folyamatban](overview.md) (TDSP). A TDSP a Microsoft által kifejlesztett keretrendszer, amely strukturált tevékenységsorozatot biztosít a felhőalapú, prediktív elemzési megoldások hatékony végrehajtásához. A TDSP célja az együttműködés és a csapattanulás javítása. A TDSP-n szabványosító adatelemzési csoport személyzeti szerepköreinek és társított feladatainak vázlatát a [Csapatadat-elemzési folyamat szerepkörei és feladatai című témakörben tetszésben tetszésszerint olvassa](roles-tasks.md)el.

A projektvezető kezeli az egyes adatszakértők napi tevékenységeit egy adott adatelemzési projekten a TDSP-ben. Az alábbi ábra a projektvezető-tevékenységek munkafolyamatát mutatja be:

![Projektvezető tevékenység munkafolyamata](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Ez az oktatóanyag ismerteti az 1. 

3. lépés: Szolgáltatásmunkaelem létrehozása a projekthez, és 4. [Agile development of data science projects](agile-development.md)

5. lépés: Tárolási/elemzési eszközök létrehozása és testreszabása, és szükség esetén a [Csapatadatok és elemzési erőforrások létrehozása](team-lead-tasks.md#create-team-data-and-analytics-resources)című témakörben.

6. lépés: A projekttár háztár biztonsági vezérlésének beállítása a [Csapattagok hozzáadása és az engedélyek konfigurálása című témakörben](team-lead-tasks.md#add-team-members-and-configure-permissions)látható.

> [!NOTE] 
> Ez a cikk az Azure Repos-t használja egy TDSP-projekt beállításához, mert így valósíthatja meg a TDSP-t a Microsoftnál. Ha a csapat egy másik kódüzemeltetési platformot használ, a projektvezető-tevékenységek azonosak, de a befejezésük módja eltérő lehet.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy a [csoportvezető](group-manager-tasks.md) és a [csoportvezető](team-lead-tasks.md) a következő erőforrásokat és engedélyeket állította be:

- Az Azure **DevOps-szervezet** az adategységhez
- Csapatprojekt **project** az adatelemzési csapat számára
- Csapatsablon- és **segédprogram-adattárak**
- **A** projekt adattárai létrehozásához és szerkesztéséhez a szervezeti fiókhoz szükséges engedélyek

A klónozó adattárak és a tartalom módosítása a helyi gépen vagy adatelemzési virtuális gép (DSVM), vagy az Azure-fájltárolás beállítása és csatlakoztatása a DSVM, akkor is meg kell fontolnia ezt az ellenőrzőlistát:

- Azure-előfizetés.
- Git telepítve van a gépre. Ha DSVM-et használ, a Git előre telepítve van. Ellenkező esetben tekintse meg a [Platformok és eszközök függeléket.](platforms-and-tools.md#appendix)
- Ha DSVM-et szeretne használni, az Azure-ban létrehozott és konfigurált Windows vagy Linux DSVM. További információt és utasításokat az [Adatelemzési virtuálisgép dokumentációjában talál.](/azure/machine-learning/data-science-virtual-machine/)
- Windows DSVM esetén a [Git credential manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a számítógépen. A *README.md* fájlban görgessen le a **Letöltés és telepítés** szakaszhoz, és válassza ki a legújabb **telepítőt**. Töltse le az *.exe* telepítőt a telepítő lapról, és futtassa azt. 
- Linuxos DSVM esetén a DSVM-en beállított és az Azure DevOps-ban hozzáadott SSH nyilvános kulcs. További információt és utasításokat a Platformok és eszközök függelék **SSH-kulcs létrehozása** [című szakaszában talál.](platforms-and-tools.md#appendix) 

## <a name="create-a-project-repository-in-your-team-project"></a>Projekttár létrehozása a csapatprojektben

Projekttár létrehozása a csapat **MyTeam-projektjében:**

1. Nyissa meg a csapat **projektösszefoglaló** lapját *a https:\//\<kiszolgálónév>/\<szervezet neve>/\<csapatnév>*, például **https:\//dev.azure.com/DataScienceUnit/MyTeam**, és válassza a **Repók** lehetőséget a bal oldali navigációs sávon. 
   
1. Válassza ki a tárház nevét a lap tetején, majd válassza az **Új tárház** lehetőséget a legördülő menüből.
   
   ![Új tárház kiválasztása](./media/project-lead-tasks/project-leads-9-select-repos.png)
   
1. Az **Új tárház létrehozása** párbeszédpanelen győződjön meg arról, hogy a **Git** a **Típus**csoportban van kiválasztva. Írja be a *DSProject1 értéket* **a Tárház neve mezőbe,** majd válassza **a Létrehozás lehetőséget.**
   
   ![Tárház létrehozása](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)
   
1. Ellenőrizze, hogy láthatja-e az új **DSProject1** tárházat a projektbeállítások lapon. 
   
   ![Projekttár a Projektbeállítások párbeszédpanelen](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

## <a name="import-the-team-template-into-your-project-repository"></a>A csapatsablon importálása a projekttárházba

A projekttár feltöltése a csapatsablon-tárház tartalmával:

1. A csapat **projektösszegzés** lapján válassza a **Repók** lehetőséget a bal oldali navigációs sávon. 
   
1. Válassza ki a tárház nevét a lap tetején, és válassza a **DSProject1** elemet a legördülő menüből.
   
1. A **DSProject1 üres** lapon válassza az **Importálás**lehetőséget. 
   
   ![Importálás kiválasztása](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)
   
1. A **Git-tárház importálása** párbeszédpanelen válassza a **Git** **forrástípust,** és írja be a **TeamTemplate** tárház URL-címét a **Klónozó URL csoportban.** Az URL-cím *\//\<https:\<kiszolgálónév>/ szervezet neve\<>/ csapatnév>/_git/\<csapatsablon tárház neve>. * Például: **\/https: /dev.azure.com/DataScienceUnit/MyTeam/_git/TeamTemplate**. 
   
1. Kattintson az **Importálás** gombra. A csapatsablon-tárház tartalma a projekttárházba kerül. 
   
   ![Csapatsablon-tárház importálása](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

Ha a projekttár tartalmát a projekt egyedi igényeinek megfelelően testre kell szabnia, hozzáadhat, törölhet vagy módosíthat tárházfájlokat és -mappákat. Közvetlenül dolgozhat az Azure-tárházakban, vagy klónozhatja a tárházat a helyi számítógépre vagy a DSVM-re, módosításokat hajthat végre, és véglegesítheti és leküldéses a frissítéseket a megosztott projekttárházba. Kövesse a [csoportadattárak tartalmának testreszabása](team-lead-tasks.md#customize-the-contents-of-the-team-repositories)című útmutatót.

## <a name="next-steps"></a>További lépések

Az alábbiakban a csapatadat-elemzési folyamat által meghatározott egyéb szerepkörök és feladatok részletes leírására mutató hivatkozásokat olvashat:

- [Csoportkezelői feladatok adatelemzési csoportszámára](group-manager-tasks.md)
- [Csapatvezető-feladatok egy adatelemzési csapatszámára](team-lead-tasks.md)
- [Egyéni közreműködői feladatok egy adatelemzési csapatszámára](project-ic-tasks.md)
