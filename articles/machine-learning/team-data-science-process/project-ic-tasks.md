---
title: Vonja össze az adatokat tudományos folyamat feladatok számára egy egyéni közreműködő - Azure |} Microsoft Docs
description: A feladatok a tudományos adatok team projekt egy egyéni közreműködő áttekintését.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 4951d596f64a17f0bd47100c1fc81397dc5febbd
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="individual-contributor-tasks"></a>Egyes közreműködői feladatok

Ez a témakör vázol fel, amely egy egyéni közreműködő feladatok befejezéséhez az adatok tudományos csoport nem várt. Hoz létre, amely egységesíti az együttműködést team környezetet a [Team adatok tudományos folyamat](overview.md) (TDSP). A személyzet szerepkörök és a kapcsolódó feladatok, egy data tudományos csapat által kezelt vázlat szabványosítása a folyamattal, lásd: [Team adatok tudományos folyamat szerepköröket és feladatokat](roles-tasks.md).

Project egyes közreműködő szerepkörrel rendelkező személyek (adatszakértőkön) a TDSP környezetet a projekt feladatainak ezek ábrázolva az alábbiak szerint: 

![1](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **GroupUtilities** tárolja a célszámítógépekre, hogy a csoport számára hasznos segédprogramok megosztani a teljes csoport karbantartása. 
- **TeamUtilities** tárolja a célszámítógépekre, amely a csapat karbantartása kifejezetten a csapat számára. 

Hogyan hajthat végre a TDSP tudományos adatok projektté, lásd: [végrehajtási az adatok tudományos projektek](project-execution.md). 

>[AZURE.NOTE] A Microsoft helyzeteit vázolják fel, az alábbi utasításokat a Visual Studio Team Services (VSTS) alkalmazásával TDSP team környezet beállítása szükséges lépéseket. Azt adja meg, hogyan hajthat végre ezeket a feladatokat a VSTS, mivel az a Microsoft TDSP megvalósítása módját. Ha egy másik kódot futtató platform csoport használata esetén a feladatokat, amelyeket teljesíteni a csapat az érdeklődési általában ne módosítsa. De ezek a feladatok úgy a különböző lesz.


## <a name="repositories-and-directories"></a>Tárolóhelyekkel és könyvtárak

Ez az oktatóanyag adattárak és könyvtárak rövidített nevét használja. Ezek a nevek könnyebben hajtsa végre a műveletek a tárolóhelyekkel és a címtárak között. Ez a notation (**R** a Git-tárházak és **D** a DSVM a helyi könyvtárak) a következő szakaszokban szolgál:

- **R2**: a csoport kezelőjének úgy van beállítva, a VSTS csoport kiszolgálón Git a GroupUtilities tárházából.
- **R4**: a csoport az érdeklődési beállított Git a TeamUtilities tárházából.
- **R5**: A projekt tárházat a Git, amely be van állítva a projekt vezethet.
- **D2**: R2 alapján klónozták a helyi címtárszolgáltatásban.
- **D4**: R4 alapján klónozták a helyi címtárszolgáltatásban.
- **D5**: R5 alapján klónozták a helyi címtárszolgáltatásban.


## <a name="step-0-prerequisites"></a>Lépés-0: Előfeltételek

Az előfeltételek teljesülnek, a csoport kezelőjének leírt rendelt feladatok végrehajtásával [csoport kezelőjének feladatokat tudományos adatok csoport](group-manager-tasks.md). Itt összefoglalva, az alábbi követelményeket kell teljesíteni a csapat az érdeklődési feladatok megkezdése előtt: 
- A csoport kezelőjének állította be a **GroupUtilities** tárház (ha van ilyen). 
- A csoport az érdeklődési állította be a **TeamUtilities** tárház (ha van ilyen).
- A projekt vezető beállította a projekt tárházba. 
- Ön érhetőek el a projekt tárház klónozandó és a projekt tárház vissza leküldése a jogosultságot a projekt vezethet.

A második **TeamUtilities** -tárházban, előfeltételként szükséges nem kötelező megadni, attól függően, hogy rendelkezik-e a csoport egy team-specifikus segédprogram-tárházat. Ha más három Előfeltételek bármelyike nem végzett, lépjen kapcsolatba a csapat segítséget, a projekt vezető vagy a delegáltak beállításához szükséges utasításokat követve [tudományos adatok csoport feladatainak Team vezethet](team-lead-tasks.md) vagy [ A projekt vezető feladatok tudományos adatok csoport](project-lead-tasks.md).

- Git telepítenie kell a számítógépre. Ha egy tudományos virtuális gép (DSVM) használ, Git előre telepítve van, és visszaigazolásához. Ellenkező esetben tekintse meg a [platformok és az eszközök függelék](platforms-and-tools.md#appendix).  
- Ha használ egy **Windows DSVM**, telepíteni kell [Git hitelesítőadat-kezelő (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) van telepítve a számítógépre. A README.md fájl görgessen le a **töltse le és telepítse** szakaszt, és kattintson a *installer legújabb*. Ezzel megnyitná a legfrissebb telepítési oldal. Töltse le a .exe-telepítő címről, és futtassa. 
- Ha használ **Linux DSVM**, a DSVM a nyilvános SSH-kulcs létrehozása, és adja hozzá a csoporthoz VSTS-kiszolgáló. SSH kapcsolatos további információkért tekintse meg a **létrehozása SSH nyilvános kulcs** szakasz a [platformok és az eszközök függelék](platforms-and-tools.md#appendix). 
- A csoport és/vagy a projekt vezető néhány, a DSVM csatlakoztatni kell az Azure file storage hozott létre, ha a fájl Azure tárolással kapcsolatos szerezheti be azokat. 

## <a name="step-1-3-clone-group-team-and-project-repositories-to-local-machine"></a>1 – 3. lépés: csoport, a csapat és a helyi számítógép projekt tárhelyek klónozása

Ez a szakasz a projekt egyedi közreműködők első három feladatok végrehajtásával kapcsolatos tudnivalókat tartalmazza: 

- Klónozott a **GroupUtilities** D2 R2 tárház
- Klónozott a **TeamUtilities** D4 R4 tárház 
- Klónozott a **projekt** D5 R5 tárház.

A helyi gépén, hozzon létre egy könyvtárat ***C:\GitRepos*** (a Windows) vagy ***$home/GitRepos*** (forLinux), majd állítsa át a könyvtárhoz. 

Klónozás megfelelően (az operációs rendszer) a következő parancsok egyikét futtassa a **GroupUtilities**, **TeamUtilities**, és **projekt** könyvtárak tárhelyek a helyi számítógép: 

**Windows**
    
    git clone <the URL of the GroupUtilities repository>
    git clone <the URL of the TeamUtilities repository>
    git clone <the URL of the Project repository>
    
![2](./media/project-ic-tasks/project-ic-2-clone-three-repo-to-ic.png)

Ellenőrizze, hogy látható-e a három mappák a projekt könyvtárában.

![3](./media/project-ic-tasks/project-ic-3-three-repo-cloned-to-ic.png)

**Linux**
    
    git clone <the SSH URL of the GroupUtilities repository>
    git clone <the SSH URL of the TeamUtilities repository>
    git clone <the SSH URL of the Project repository>

![4](./media/project-ic-tasks/project-ic-4-clone-three-repo-to_ic-linux.png)

Ellenőrizze, hogy látható-e a három mappák a projekt könyvtárában.

![5](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="step-4-5-mount-azure-file-storage-to-your-dsvm-optional"></a>4-5. lépés: a DSVM (nem kötelező) a csatlakoztatási Azure fájltároló

Csatlakoztatási Azure file Storage a DSVM számára, lásd a 4. szakaszban található utasítások a [Team átfutási feladatok tudományos adatok csoport](team-lead-tasks.md)

## <a name="next-steps"></a>További lépések

Az alábbiakban a szerepkörök és az adatok tudományos eljárással meghatározott feladatok részletes leírását mutató hivatkozásokat:

- [Tudományos adatok csoport csoport Manager-feladatok](group-manager-tasks.md)
- [Az érdeklődési feladatainak egy adatok tudományos csoport](team-lead-tasks.md)
- [Az érdeklődési tevékenységeket a tudományos adatok csoport](project-lead-tasks.md)
- [Project egyes közreműködők a tudományos adatok csoport](project-ic-tasks.md)

