---
title: A csoportos adatelemzési folyamat a egy egyedi közreműködői feladatok
description: A tevékenységek a data science csapatprojekt egy egyéni közreműködőjének röviden ismerteti.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a52907fa6c0e2483479031fbb3d1ad68a121d95
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475558"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>A csoportos adatelemzési folyamat a egy egyedi közreműködői feladatok

Ez a témakör ismerteti a feladatokat, amely egy egyéni közreműködő várható esetében az adatelemzési csapatával. Együttműködő csapat környezet, amely szabványosítja az a célja a [csoportos adatelemzési folyamat](overview.md) (TDSP). Röviden ismerteti a személyzet szerepkörök és hozzájuk kapcsolódó részfeladatokat adatelemzési csapatával által végrehajtott ezt a folyamatot, a szabványosításával lásd [csoportos adatelemzési folyamat szerepkörök és feladatok](roles-tasks.md).

A feladatokat a projektben egyes közreműködők (adatszakértők) a TDSP környezetet a projekt következő vannak kitaláltak: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** az adattár, amely a csoport a hasznos segédprogramok megosztása a teljes csoport karbantartása. 
- **TeamUtilities** az adattár, amely a csapat karbantartása kifejezetten a csapat számára. 

Hogyan hajtható végre a TDSP adatelemzési projektjéhez kapcsolatos utasításokért lásd: [végrehajtási az adatelemzési projektek](project-execution.md). 

>[AZURE.NOTE] Az Azure DevOps használatával a következő utasítások TDSP csoportos környezetben beállításához szükséges lépéseket szerkezeti azt. Azt adja meg, hogyan végezheti el ezeket a feladatokat az Azure DevOps, mivel az a Microsoft TDSP tanfolyamsorozat. Ha egy másik kódot üzemeltető platform szolgál a csoport, a feladatokat kell végrehajtani a csapatvezetők általában, ne módosítsa. Azonban ezeket a feladatokat a módja eltérő lesz.


## <a name="repositories-and-directories"></a>Adattárak és könyvtárak

Ebben az oktatóanyagban rövidített adattárak és a könyvtárakat. Ezeket a neveket könnyebben hajtsa végre a műveleteket az adattárak és a címtárak között. Ezen jelölés (**R** a Git-tárházak és **D** a dsvm-hez a helyi címtárak) szerepel a következő szakaszokban:

- **R2**: A Git, a csoport kezelőjének úgy van beállítva, az Azure DevOps-csoport kiszolgálón GroupUtilities tárház.
- **R4**: A Git, a csapat vezető TeamUtilities tárházából állított be.
- **R5**: A projekt tárházat, a Git, amely be lett állítva a projektvezető által.
- **D2**: A helyi könyvtárban klónozása R2-ről.
- **D4**: A helyi könyvtárban klónozható R4 a.
- **D5**: A helyi könyvtárban klónozható R5 a.


## <a name="step-0-prerequisites"></a>. Lépés – 0: Előfeltételek

Az előfeltételek teljesülnek, a hozzárendelve a csoport kezelőjének ismertetett feladatok végrehajtásával [Csoportkezelő feladatokat egy adatelemzési csapatával](group-manager-tasks.md). Összefoglalva az itt, az alábbi követelményeknek kell teljesülniük a csapat vezető feladatok megkezdése előtt: 
- A csoport kezelőjének úgy állította be a **GroupUtilities** tárház (ha van). 
- A Csapatvezető úgy állította be a **TeamUtilities** tárház (ha van).
- A projektvezető beállította a projektadattárat. 
- Felvették a projekt tárházra a jogosultság a klónozza, és küldhet vissza a projektadattárat a projektvezető által.

A második **TeamUtilities** előfeltétel-adattárban nem kötelező, attól függően, a csapat rendelkezik-e egy csapat-specifikus segédprogram-adattárhoz. Ha más három Előfeltételek bármelyike nem fejeződött, lépjen kapcsolatba a csapatvezető, a projektvezető vagy saját delegáltakat vonatkozó utasításokat követve be [feladatok csapata egy adatelemzési csapatával](team-lead-tasks.md) vagy [ A projekt vezető feladatok egy adatelemzési csapatával](project-lead-tasks.md).

- Git a gépen telepítve kell lennie. Ha egy adatelemzési virtuális gép (DSVM) használ, Git előre telepítve van, és készen áll. Egyéb esetben a [platformok és eszközök függelék](platforms-and-tools.md#appendix).  
- Ha használ egy **Windows DSVM**, rendelkeznie kell [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a gépén. A README.md fájl görgessen le a **töltse le és telepítse** szakaszt, és kattintson a *legújabb*. Ezzel továbblép a telepítő legújabb lapra. Töltse le az .exe itt, és futtathatja. 
- Ha használ **Linuxos DSVM**, egy nyilvános SSH-kulcs létrehozása a dsvm-hez, és adja hozzá a csoport az Azure DevOps-szolgáltatásokkal. SSH kapcsolatos további információkért lásd: a **hozzon létre SSH nyilvános kulcs** című rész a [platformok és eszközök függelék](platforms-and-tools.md#appendix). 
- Ha a csoport és/vagy érdeklődő néhány, a dsvm-hez csatlakoztatni kell az Azure file storage hozott létre, szerezheti be a az Azure file storage információkat belőlük. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>1 – 3. lépés: Klónozott csoportba, a csapat és a projekt tárház helyi számítógépre

Ez a szakasz útmutatást első három projektben egyes munkatársak feladatainak elvégzését: 

- Klónozás a **GroupUtilities** D2 R2-adattár
- Klónozás a **TeamUtilities** D4 R4-adattár 
- Klónozás a **projekt** D5 R5 adattárat.

A helyi gépén hozzon létre egy könyvtárat ***C:\GitRepos*** (a Windows) vagy ***$home/GitRepos*** (forLinux), majd módosítsa a könyvtárhoz. 

Megfelelően (az operációs rendszer) a következő parancsok egyikét futtatásával klónozza a **GroupUtilities**, **TeamUtilities**, és **projekt** könyvtáraihoz tárházakat a helyi számítógép: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Győződjön meg arról, hogy megjelenik-e a három mappát a projekt könyvtárában.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Győződjön meg arról, hogy megjelenik-e a három mappát a projekt könyvtárában.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>4 – 5. lépés: Az Azure file storage csatlakoztatása t a dsvm-hez (nem kötelező)

Az Azure file storage csatlakoztatása t a dsvm-hez, lásd 4. szakasz utasításait a [csapat vezető feladatok egy adatelemzési csapatával](team-lead-tasks.md)

## <a name="next-steps"></a>További lépések

Az alábbiakban a hivatkozások a szerepkörök és feladatok határozzák meg a csoportos adatelemzési folyamat részletes ismertetését:

- [Adatelemzési csapatával csoport Manager-feladatok](group-manager-tasks.md)
- [Csoportos adatelemzési csapatával az érdeklődő feladatok](team-lead-tasks.md)
- [Érdeklődő tevékenységeket az adatelemzési csapatával](project-lead-tasks.md)
- [Projektben egyes közreműködők az adatelemzési csapatával](project-ic-tasks.md)

