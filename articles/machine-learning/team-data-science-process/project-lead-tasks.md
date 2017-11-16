---
title: "Vonja össze az adatokat tudományos folyamatban Project vezethet feladatok – Azure |} Microsoft Docs"
description: "Projekt feladatok röviden a tudományos adatok team projekt vezethet."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev;
ms.openlocfilehash: fe7cf1e6167de7d0dfaa5ed75c0b7f5ec23b18cf
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="project-lead-tasks"></a>Az érdeklődési tevékenységeket

Az oktatóanyag vázol fel, amely a projekt vezető feladatok várhatóan képes projektcsapat befejezéséhez. Hoz létre, amely egységesíti az együttműködést team környezetet a [Team adatok tudományos folyamat](overview.md) (TDSP). A TDSP egy felhőalapú, a prediktív elemzési megoldások hatékonyan végrehajtandó tevékenységek strukturált sorrendje biztosító Microsoft által kifejlesztett keretrendszert. A személyzet szerepkörök és a kapcsolódó feladatok, egy data tudományos csapat által kezelt vázlat szabványosítása a folyamattal, lásd: [Team adatok tudományos folyamat szerepköröket és feladatokat](roles-tasks.md).

A **projekt vezethet** kezeli az egyes adatszakértőkön egy adott adatok tudományos projekt napi tevékenységeit. A munkafolyamat által projekt vezet a környezet beállításához elvégzendő feladatok az alábbi ábrán ezek ábrázolva:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Ez a témakör ismerteti a feladatok 1,2 és 6 projekt érdeklődők a munkafolyamat jelenleg.

>[AZURE.NOTE] A Microsoft szerkezeti TDSP csoportos környezetben a Visual Studio Team Services (VSTS) használatával az alábbi utasításokat a projekt beállításához szükséges lépéseket. Azt adja meg, hogyan hajthat végre ezeket a feladatokat a VSTS, mivel az a Microsoft TDSP megvalósítása módját. Ha egy másik kódot futtató platform csoport használata esetén a feladatokat, amelyeket teljesíteni a csapat az érdeklődési általában ne módosítsa. De ezek a feladatok úgy a különböző lesz.


## <a name="repositories-and-directories"></a>Tárolóhelyekkel és könyvtárak

Ez az oktatóanyag adattárak és könyvtárak rövidített nevét használja. Ezek a nevek könnyebben hajtsa végre a műveletek a tárolóhelyekkel és a címtárak között. Ez a notation (Git tárházak R) és a helyi könyvtárak a DSVM D szolgál a következő szakaszokban:

- **R3**: A csapat **ProjectTemplate** tárházából beállította a csapat az érdeklődési Git.
- **R5**: A projekt tárházából Git telepítenie a projekthez.
- **D3**: R3 alapján klónozták a helyi címtárszolgáltatásban.
- **D5**: R5 alapján klónozták a helyi címtárszolgáltatásban.


## <a name="0-prerequisites"></a>0. Előfeltételek

Az előfeltételek teljesülnek, a csoport kezelőjének leírt rendelt feladatok végrehajtásával [csoport kezelőjének feladatokat tudományos adatok csoport](group-manager-tasks.md) és a leírt átfutási összevonva [tudományos adatok csoportTeamátfutásifeladatok](team-lead-tasks.md). 

Itt összefoglalva, az alábbi követelményeknek kell felel meg a csoport az érdeklődési feladatok megkezdése előtt: 

- A **csoport VSTS server** (vagy más kódot futtató platformon csoportfiók) be van állítva a csoport kezelőjének által.
- A **TeamProjectTemplate tárház** (R3) be van állítva a csoport fiók alatt is használni kívánja kódot futtató platformon a csapat vezethet.
- Rendelkezik-e **engedélyezett** a csapat vezethet a csapata csoport fiókja adattárak létrehozásához.
- Git telepítenie kell a számítógépre. Ha egy tudományos virtuális gép (DSVM) használ, Git előre telepítve van, és visszaigazolásához. Ellenkező esetben tekintse meg a [platformok és az eszközök függelék](platforms-and-tools.md#appendix).  
- Ha használ egy **Windows DSVM**, telepíteni kell [Git hitelesítőadat-kezelő (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) van telepítve a számítógépre. A README.md fájl görgessen le a **töltse le és telepítse** szakaszt, és kattintson a *installer legújabb*. Ezzel megnyitná a legfrissebb telepítési oldal. Töltse le a .exe-telepítő címről, és futtassa. 
- Ha használ **Linux DSVM**, a DSVM a nyilvános SSH-kulcs létrehozása, és adja hozzá a csoporthoz VSTS-kiszolgáló. SSH kapcsolatos további információkért tekintse meg a **létrehozása SSH nyilvános kulcs** szakasz a [platformok és az eszközök függelék](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. A projekt tárház (R5) létrehozása

- Jelentkezzen be a csoport VSTS kiszolgálóra *https://\<VSTS kiszolgálónév\>. visualstudio.com webhelyre*. 
- A **legutóbbi projektek & csapatok**, kattintson a **Tallózás**. Egy ablakban a VSTS-kiszolgálón az összes csapatprojektek sorolja fel. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Kattintson a csapatprojekt nevét, amelyben a projekt tárház létrehozni kívánja. A jelen példában kattintson **MyTeam**. 
- Kattintson a **Navigálás** csapatprojekt kezdőlapján irányíthatja **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Kattintson a **kódja együttműködés** a csapatprojekt git kezdőlapján irányíthatja.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Kattintson a lefelé mutató nyílra a bal felső sarokban, majd válassza **+ új tárház**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- Az a **hozzon létre egy új tárház** ablak, adjon meg a projekt git-tárház nevét. Győződjön meg arról, hogy kiválassza **Git** a tárház típusként. A jelen példában használjuk a neve *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Létrehozásához a ***DSProject1*** git-tárház projektre, kattintson **létrehozása**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. A DSProject1 kezdőérték projekt tárház

Az itt feladata a Kezdőérték a **DSProject1** projekt tárház (R5) a csapat projekt sablon tárházból (R3). Az index-összehangolási eljárás a könyvtárak D3 és D5 használja a helyi DSVM a köztes átmeneti helyek. Összefoglalva, az index-összehangolási elérési út: R3 -> D3 -> D5 R5 ->.

Ha testre szeretné szabni a **DSProject1** projekt tárház megfelelnek bizonyos megadott projekt igények, azt megteheti az alábbi eljárás az utolsó előtti lépésben. A tartalom rendezésére használható lépések összegzése a **DSProject1** projekt tárházba. Az egyes lépéseket felelnek meg az index-összehangolási eljárás alszakaszokat:

- Klónozás team projekt sablon tárház helyi könyvtárba: R3 --> helyi D3 klónozott vonja össze.
- Klónozás DSProject1 tárház egy helyi könyvtárba: R5 --> helyi D5 klónozott vonja össze.
- Másolja a klónozott team projekt sablon tartalmat a helyi klónozott tárház DSProject1: D3 - tartalom -> D5 másolva.
- (Választható) Testreszabási helyi D5.
- Helyi DSProject1 tartalom leküldéssel team adattárak: D5 - tartalmak hozzáadása team R5 ->.


### <a name="clone-your-team-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Klónozza a csapat projekt sablon tárházat (R3) a könyvtárba (D3) a helyi számítógépen.

A helyi gépén hozzon létre egy könyvtárat:

- *C:\GitRepos\MyTeamCommon* Windows 
- *$home/GitRepos/MyTeamCommon* Linux

Módosítsa a könyvtárhoz. Ezután futtassa a következő parancsot a helyi számítógép team projekt sablon tárház klónozása. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Ha használ VSTS platformként a kódot futtató, általában a *HTTPS URL-cím, a csapat projekt sablon tárház* van:

 ***https://\<VSTS kiszolgálónév\>.visualstudio.com/\<a csapatprojekt nevét\>/_git/\<a csapatprojekt sablon tárház nevét\>***. 

Ebben a példában vezetünk be:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Ha használ VSTS platformként a kódot futtató, általában a *SSH URL-CÍMÉT a team projekt sablon tárház* van:

***ssh: / /\<VSTS kiszolgálónév\>@\<VSTS kiszolgálónév\>.visualstudio.com:22/\<a Csapatprojekt nevét > /_git/\<a csapat projekt sablon tárház név\>.*** 

Ebben a példában vezetünk be:

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Klónozza DSProject1 tárházat (R5) a helyi számítógép könyvtárra (D5)

A Könyvtárváltás **GitRepos**, és futtassa a következő parancsot a helyi számítógép projekt tárház klónozása. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Használata VSTS platformként a kódot futtató, általában a _HTTPS URL-cím, a projekt tárház_ van ***https://\<VSTS-kiszolgálónév\>.visualstudio.com/\<saját csoport A projekt neve > /_git/ < a tárház projektnévre\>***. Ebben a példában tudunk ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Használata VSTS platformként a kódot futtató, általában a _SSH URL-címet a projekt tárház_ _ssh van: / / < VSTS kiszolgálónév\>@< VSTS kiszolgálónév\>.visualstudio.com:22/<Your Team Project Name> / \_git / < a tárház projektnévre\>. Ebben a példában tudunk  ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1*** .

### <a name="copy-contents-of-d3-to-d5"></a>D3 D5 másolja 

Most a helyi gépen, másolja a tartalmat a _D3_ való _D5_, kivéve a git-metaadatok .git könyvtárban. Az alábbi parancsfájlok fog tenni a feladatot. Ügyeljen arra, hogy adja meg a helyes és teljes elérési utat a címtárakba. Forrásmappa, a egy, a csapat számára (_D3_); célmappát, a egy, a projekthez (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Most azt láthatja _DSProject1_ mappában (kivéve a .git) összes fájlt másolja át _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Most azt láthatja _DSProject1_ mappában (kivéve .git metaadatai) a fájlokat másolja át _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>D5 testreszabása, ha szeretné (nem kötelező)

Ha a projekt egy adott könyvtárakat és a dokumentumokat, eltérő kap a csapat projektsablon (D5 címtárat az előző lépésben másolt), testre szabhatja D5 tartalmának most. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-vsts-server"></a>Hozzáadhat DSProject1 tartalmát D5 az R5 a VSTS-kiszolgálón

Tartalom leküldéses most kell  **_DSProject1_**  való _R5_ a csoport VSTS-kiszolgálón a csapatprojekt-tárház. 


- Váltson **D5**. 
- A következő git-parancsok segítségével adja hozzá a tartalom **D5** való **R5**. A parancsok megegyeznek a Windows és Linux rendszerek. 
    
    Adja hozzá a git állapot git.
    git commit -m "leküldéses a win DSVM" git push
    
- A változás- és leküldéses véglegesítése. 

>[AZURE.NOTE] Ha az első alkalommal véglegesíti a Git-tárházba, szeretné-e globális paraméterek konfigurálása *user.name* és *user.email* futtatása előtt a `git commit` parancsot. Futtassa az alábbi két parancsot:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Ha több Git adattárak a véglegesítés használja ugyanazt a nevet és e-mail címet az összes. Ugyanazzal a névvel és e-mail címmel bizonyítja kényelmes később a Power bi-irányítópultok nyomon követheti a Git tevékenységek több adattárak összeállításakor.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Hozzon létre, és csatlakoztassa az Azure file storage projekt erőforrásként (nem kötelező)

Ha szeretne létrehozni az Azure file storage adatmegosztásra, például a projekt nyers adatok vagy a szolgáltatások jön létre a projekthez, hogy az összes projektet hozzáférése van az azonos adatkészletek több DSVMs a kövesse az utasításokat a 3. és 4 [ Vonja össze az adatokat tudományos csoport átfutási feladatok](team-lead-tasks.md). 


## <a name="next-steps"></a>Következő lépések

Az alábbiakban a szerepkörök és az adatok tudományos eljárással meghatározott feladatok részletes leírását mutató hivatkozásokat:

- [Tudományos adatok csoport csoport Manager-feladatok](group-manager-tasks.md)
- [Az érdeklődési feladatainak egy adatok tudományos csoport](team-lead-tasks.md)
- [Az érdeklődési tevékenységeket a tudományos adatok csoport](project-lead-tasks.md)
- [Project egyes közreműködők a tudományos adatok csoport](project-ic-tasks.md)