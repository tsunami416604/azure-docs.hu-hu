---
title: Egyéni közreműködő feladatai a csapatadat-elemzési folyamatban
description: A tevékenységek részletes forgatókönyve egy adatelemzési csapat projekt egyéni közreműködője számára.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d9942c31b63de77196b8b51b88376cb8ef74b990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721251"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Egyéni közreműködő feladatai a csapatadat-elemzési folyamatban

Ez a témakör ismerteti azokat a tevékenységeket, amelyeket az *egyes közreműködők* végeznek a projekt beállítása a [csapat adatelemzési folyamat](overview.md) (TDSP). A cél az, hogy a munka egy együttműködési csapat környezetben, amely egységesíti a TDSP. A TDSP célja az együttműködés és a csapattanulás javítása. A TDSP-re szabványosító adatelemzési csapat által kezelt személyzeti szerepkörök és a hozzájuk kapcsolódó feladatok vázlatát a [Csapatadat-elemzési folyamat szerepkörei és feladatai című témakörben található.](roles-tasks.md)

Az alábbi ábra azokat a tevékenységeket mutatja be, amelyeket az egyes közreműködők (adatszakértők) a csapatkörnyezet beállításához végeznek. A TDSP-n alapuló adatelemzési projektek végrehajtásáról az [adatelemzési projektek végrehajtása című](project-execution.md)témakörben talál útmutatást. 

![Egyéni közreműködői feladatok](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **A ProjectRepository** az a tárház, amelyet a projektcsapat a projektsablonok és -eszközök megosztásához tart fenn.
- **A TeamUtilities** az a segédprogram-tárház, amelyet a csapata kifejezetten a csapata számára tart fenn. 
- **A GroupUtilities** az a tárház, amelyet a csoport a teljes csoport hasznos segédprogramjainak megosztására tart fenn. 

> [!NOTE] 
> Ez a cikk az Azure Repos és a Data Science Virtual Machine (DSVM) segítségével tdsp-környezetet állít be, mert így valósíthatja meg a TDSP-t a Microsoftnál. Ha a csapat más kódüzemeltetési vagy fejlesztési platformokat használ, az egyes közreműködői feladatok azonosak, de a befejezésük módja eltérő lehet.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy a [csoportvezető,](group-manager-tasks.md)a [csoportvezető](team-lead-tasks.md)és a [projektvezető](project-lead-tasks.md)a következő erőforrásokat és engedélyeket állította be:

- Az Azure **DevOps-szervezet** az adatelemzési egységhez
- A projekt által létrehozott **projekttár** a projektsablonok és -eszközök megosztására vezet
- **GroupUtilities** és **TeamUtilities** adattárak által létrehozott csoport vezetője és a csoport vezetője, ha van ilyen
- A csoport vagy a projekt megosztott eszközeihez beállított **Azure-fájltárolás,** ha van ilyen
- **Engedélyek** a projekttárból való klónozáshoz és a projekttárba való visszalökéshez 

A klónozó adattárak és a tartalom módosítása a helyi számítógépen vagy DSVM, vagy csatlakoztatni az Azure-fájltároló a DSVM, meg kell vizsgálni a következő ellenőrzőlistát:

- Azure-előfizetés.
- Git telepítve van a gépre. Ha DSVM-et használ, a Git előre telepítve van. Ellenkező esetben tekintse meg a [Platformok és eszközök függeléket.](platforms-and-tools.md#appendix)
- Ha DSVM-et szeretne használni, az Azure-ban létrehozott és konfigurált Windows vagy Linux DSVM. További információt és utasításokat az [Adatelemzési virtuálisgép dokumentációjában talál.](/azure/machine-learning/data-science-virtual-machine/)
- Windows DSVM esetén a [Git credential manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a számítógépen. A *README.md* fájlban görgessen le a **Letöltés és telepítés** szakaszhoz, és válassza ki a legújabb **telepítőt**. Töltse le az *.exe* telepítőt a telepítő lapról, és futtassa azt. 
- Linuxos DSVM esetén a DSVM-en beállított és az Azure DevOps-ban hozzáadott SSH nyilvános kulcs. További információt és utasításokat a Platformok és eszközök függelék **SSH-kulcs létrehozása** [című szakaszában talál.](platforms-and-tools.md#appendix) 
- Az Azure-fájltárolási adatok minden Olyan Azure-fájltároláshoz, amelyet a DSVM-hez kell csatlakoztatnia. 

## <a name="clone-repositories"></a>Klónozó adattárak

Ha helyileg szeretne dolgozni az adattárakkal, és a módosításokat a megosztott csapat- és projekttárakba szeretné átvinni, először másolja vagy *klónozza* a tárolókat a helyi számítógépre. 

1. Az Azure DevOps-ban nyissa meg a csapat projektösszefoglaló lapját *\//\<https: kiszolgálónév>/\<szervezetnév\<>/ csapatnév>*, például **https:\//dev.azure.com/DataScienceUnit/MyTeam**.
   
1. Válassza **a Fedárak** lehetőséget a bal oldali navigációs sávon, és a lap tetején válassza ki a klónozni kívánt tárházat.
   
1. A tárház lapon válassza a **Klónozás** lehetőséget a jobb felső sarokban.
   
1. A **Klónozótár** párbeszédpanelen válassza a **HTTPS lehetőséget** egy HTTP-kapcsolathoz, vagy **az SSH-kapcsolatot,** és másolja a klón URL-t a **parancssorból** a vágólapra.
   
   ![Klón tárhét](./media/project-ic-tasks/clone.png)
   
1. A helyi számítógépen vagy a DSVM-en hozza létre a következő könyvtárakat:
   
   - Windows rendszerre: **C:\GitRepos**
   - Linuxra: **$home/GitRepos**
   
1. Váltás a létrehozott könyvtárra.
   
1. A Git Bash alkalmazásban `git clone <clone URL>` futtassa a klónozni kívánt tárházak parancsát. 
   
   Például a következő parancs klónozza a **TeamUtilities** tárházat a helyi számítógép *MyTeam* könyvtárába. 
   
   **HTTPS-kapcsolat:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH kapcsolat:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Ellenőrizze, hogy a klónozott tárolók mappái láthatók-e a helyi projektkönyvtárban.
   
   ![Három helyi tárházmappa](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Az Azure-fájltárolás csatlakoztatása a DSVM-hez

Ha a csapat vagy a projekt megosztott eszközök kel az Azure-fájltárolóban, csatlakoztassa a fájltárolót a helyi géphez vagy a DSVM-hez. Kövesse a Mount Azure fájltárolásra vonatkozó utasításokat [a helyi számítógépen vagy a DSVM-en.](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm)

## <a name="next-steps"></a>További lépések

Az alábbiakban a csapatadat-elemzési folyamat által meghatározott egyéb szerepkörök és feladatok részletes leírására mutató hivatkozásokat olvashat:

- [Csoportkezelői feladatok adatelemzési csoportszámára](group-manager-tasks.md)
- [Csapatvezető-feladatok egy adatelemzési csapatszámára](team-lead-tasks.md)
- [Projektvezető-tevékenységek adatelemzési csoportszámára](project-lead-tasks.md)

