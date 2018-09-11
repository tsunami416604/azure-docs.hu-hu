---
title: Csoportos feladatok adatok adatelemzési folyamat projekt vezető – Azure |} A Microsoft Docs
description: A feladatok számára egy egy data science csoportprojektet a projektvezető röviden ismerteti.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 6a618efc6860371883bff7ebb953880293ad3120
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303881"
---
# <a name="project-lead-tasks"></a>Érdeklődő tevékenységeket

Ez az oktatóanyag ismerteti a feladatokat, amely a projektvezető marketingosztályhoz projektcsapat esetében várható. Együttműködő csapat környezet, amely szabványosítja az a célja a [csoportos adatelemzési folyamat](overview.md) (TDSP). A TDSP egy keretrendszer, amely hatékony felhőalapú, prediktív elemzési megoldások végrehajtásához tevékenységek strukturált sorrendje biztosít a Microsoft által kifejlesztett. Röviden ismerteti a személyzet szerepkörök és hozzájuk kapcsolódó részfeladatokat adatelemzési csapatával által végrehajtott ezt a folyamatot, a szabványosításával lásd [csoportos adatelemzési folyamat szerepkörök és feladatok](roles-tasks.md).

A **projekt vezető** kezeli a napi tevékenységek egyes adatszakértők, az egy adott adatelemzési projektjéhez. A projekt érdeklődők a környezet beállításához elvégzendő feladatok munkafolyamata az alábbi ábrán vannak kitaláltak:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Ez a témakör ismerteti a feladatok 1,2 és 6 projekt érdeklődők a munkafolyamat jelenleg.

>[AZURE.NOTE] Hogy szerkezeti TDSP csoportos környezetben az Azure DevOps használatával az alábbi utasításokat a projekt beállításához szükséges lépéseket. Azt adja meg, hogyan végezheti el ezeket a feladatokat az Azure DevOps, mivel az a Microsoft TDSP tanfolyamsorozat. Ha egy másik kódot üzemeltető platform szolgál a csoport, a feladatokat kell végrehajtani a csapatvezetők általában, ne módosítsa. Azonban ezeket a feladatokat a módja eltérő lesz.


## <a name="repositories-and-directories"></a>Adattárak és könyvtárak

Ebben az oktatóanyagban rövidített adattárak és a könyvtárakat. Ezeket a neveket könnyebben hajtsa végre a műveleteket az adattárak és a címtárak között. Ezen jelölés (Git-tárházakhoz készült R) és a dsvm-hez a helyi címtárak D szolgál a következő szakaszokban:

- **R3**: A csapat **ProjectTemplate** a Csapatvezető állított be Git a tárház.
- **R5**: A projektadattárat a Git, konfigurálja a projekthez.
- **D3**: A helyi könyvtárban klónozható R3 verzióját a.
- **D5**: A helyi könyvtárban klónozható R5 a.


## <a name="0-prerequisites"></a>0. Előfeltételek

Az előfeltételek teljesülnek, a hozzárendelve a csoport kezelőjének ismertetett feladatok végrehajtásával [Csoportkezelő feladatokat egy adatelemzési csapatával](group-manager-tasks.md) és a csapat vezető leírt [egy adatelemzési csapatávalcsapatvezetőfeladatai](team-lead-tasks.md). 

Összefoglalva az itt, a következő követelményeknek kell megfelelni a csapat vezető feladatok megkezdése előtt: 

- A **csoportban az Azure DevOps-szolgáltatásokkal** (vagy -csoportfiók kódot futtató más platformon) be lett állítva a csoport kezelője által.
- A **TeamProjectTemplate tárház** (R3) be lett állítva a fiók alatt a kódot futtató platform használatát tervezi a csapat vezethet.
- Rendelkezik-e **jogosult** által a Csapatvezető tárházak csapata csoport fiókja létrehozásához.
- Git a gépen telepítve kell lennie. Ha egy adatelemzési virtuális gép (DSVM) használ, Git előre telepítve van, és készen áll. Egyéb esetben a [platformok és eszközök függelék](platforms-and-tools.md#appendix).  
- Ha használ egy **Windows DSVM**, rendelkeznie kell [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a gépén. A README.md fájl görgessen le a **töltse le és telepítse** szakaszt, és kattintson a *legújabb*. Ezzel továbblép a telepítő legújabb lapra. Töltse le az .exe itt, és futtathatja. 
- Ha használ **Linuxos DSVM**, egy nyilvános SSH-kulcs létrehozása a dsvm-hez, és adja hozzá a csoport az Azure DevOps-szolgáltatásokkal. SSH kapcsolatos további információkért lásd: a **hozzon létre SSH nyilvános kulcs** című rész a [platformok és eszközök függelék](platforms-and-tools.md#appendix). 


## <a name="1-create-a-project-repository-r5"></a>1. Hozzon létre egy projektet adattár (R5)

- Jelentkezzen be a csoport az Azure DevOps-szolgáltatásokkal, *https://\<Azure fejlesztési és üzemeltetési szolgáltatások neve\>. visualstudio.com*. 
- A **legutóbbi projektek & csapatok**, kattintson a **Tallózás**. Egy felugró ablakban összes projekt az Azure fejlesztési és üzemeltetési szolgáltatások listája. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Kattintson a projekt nevét, amelyben a projektadattárat létrehozni kívánja. Ebben a példában kattintson **MyTeam**. 
- Kattintson a **Navigate** a rendszer a projekt kezdőlapjára **MyTeam**:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Kattintson a **másokkal együttműködve a kódra** a rendszer a projekt a git kezdőlapjára.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Kattintson a lefelé mutató nyílra a bal felső sarokban, majd válassza **+ új adattár**. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- Az a **hozzon létre egy új adattár** ablakban adjon egy nevet a projekt git-tárházhoz. Győződjön meg arról, hogy kiválasztotta **Git** a tárház típusaként. Ebben a példában nevet használjuk *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Hozhat létre a ***DSProject1*** git-tárház projektre, kattintson **létrehozás**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. A DSProject1 kezdőérték projektadattárat

Az itt feladata a Kezdőérték a **DSProject1** projektadattárat (R5) a projekt sablon adattárból (R3). A kiindulási eljárás a könyvtárak D3 és D5 használja a helyi dsvm-hez a köztes átmeneti tárolási helyként. Összefoglalva, a kiindulási elérési út: R3 -> D3 -> D5 R5 ->.

Ha testre kell szabnia a **DSProject1** projektadattárat megfelelni egy adott projekt igények kielégítéséhez, így tesz, a következő eljárás az utolsó előtti lépésben. Itt található egy összefoglaló az tartalmának kezdőszámát használt lépéseket a **DSProject1** projektadattárat. Az egyes lépések a struktúrát a kiindulási eljárásban felelnek meg:

- Klónozás projekt sablontár helyi könyvtárba: csapat R3 - -> helyi D3 klónozni.
- Klónozás DSProject1 tárház egy helyi könyvtárba: csapat R5 - -> helyi D5 klónozni.
- Másolja a klónozott projekt sablon tartalmat a DSProject1 adattár helyi klónjával: D3 - tartalom -> D5 másolva.
- (Nem kötelező) Testreszabás helyi D5.
- Helyi DSProject1 tartalom leküldése csapat tárházakba: D5 - csapat R5 -> hozzáadása tartalmát.


### <a name="clone-your-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Klónozza a projekt sablontár (R3) a helyi számítógép könyvtárba (D3).

A helyi gépén hozzon létre egy könyvtárat:

- *C:\GitRepos\MyTeamCommon* Windows esetében 
- *$home/GitRepos/MyTeamCommon* linuxhoz

Váltson arra a könyvtárra. Ezután futtassa a következő parancsot a projekt sablon adattár helyi számítógépre történő klónozásához. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Ha használja az Azure DevOps-platformként a kódot futtató általában a *HTTPS URL-címét a projekt sablontár* van:

 ***https://\<Azure fejlesztési és üzemeltetési szolgáltatások neve\>.visualstudio.com/\<a projekt neve\>/_git/\<a projekt sablon adattár neve\>***. 

Ebben a példában van:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Ha használja az Azure DevOps-platformként a kódot futtató általában a *SSH URL-címét a projekt sablontár* van:

***ssh: / /\<Azure fejlesztési és üzemeltetési szolgáltatások neve\>@\<Azure fejlesztési és üzemeltetési szolgáltatások neve\>.visualstudio.com:22/\<saját projekt neve > /_git/\<webesprojekt-sablon Adattár neve\>.*** 

Ebben a példában van:

***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>A helyi számítógép könyvtárba (D5) (R5) DSProject1-tárház klónozása

Módosítsa a könyvtárat **GitRepos**, és futtassa a következő parancsot a helyi gépen a projekt tárház klónozásához. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Ha használja az Azure DevOps-platformként a kódot futtató általában a _HTTPS URL-címét a projektadattárat_ van ***https://\<Azure fejlesztési és üzemeltetési szolgáltatások neve\>.visualstudio.com/\<Saját projekt neve > /_git/ < a tárház projektnév\>***. Ebben a példában van ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Ha használja az Azure DevOps-platformként a kódot futtató általában a _SSH URL-címét a projektadattárat_ _ssh van: / / < Azure fejlesztési és üzemeltetési szolgáltatások neve\>@< Azure fejlesztési és üzemeltetési szolgáltatások nevét\>. visualstudio.com:22/<Your Project Name>/\_git / < a tárház projektnév\>. Ebben a példában van ***ssh://mysamplegroup@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Másolja bele a D3 D5 csomag 

Most a helyi gépen kell átmásolni a tartalmat, a _D3_ való _D5_, kivéve a git-metaadatok .git könyvtárban. Az alábbi parancsfájlok elvégzi a feladatot. Ellenőrizze, hogy írja be a helyes és teljes elérési utat a címtárakhoz. Forrásmappa a csapata egy (_D3_); célmappa lesz a projekt számára (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Most láthatja _DSProject1_ mappában (kivéve a .git) minden fájl átmásolva _MyTeamProjectTemplate_.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Most láthatja _DSProject1_ mappában (kivéve a .git metaadatai) a fájlok átmásolva _MyTeamProjectTemplate_.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>D5 testreszabása, ha szeretné (nem kötelező)

Ha projektjéhez van szüksége, egyes dokumentumok vagy adott könyvtárakat, eltérő kap a projekt sablonból (a D5 könyvtárba az előző lépésben másolt), testre szabható D5 csomag tartalmának most. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-azure-devops-services"></a>Adja hozzá DSProject1 tartalmát a D5 R5 meg a csoport az Azure DevOps-szolgáltatásokkal

Most be kell küldje le a tartalmát **_DSProject1_** való _R5_ a projektben a csoport az Azure DevOps-szolgáltatásokkal a tárházban. 


- A címtár módosítása **D5**. 
- A következő git-parancsok használatával adja hozzá a tartalmakat **D5** való **R5**. A parancsok megegyeznek a Windows- és Linux rendszerekhez. 
    
    Adja hozzá a git állapotát a git.
    git commit -m "win dsvm-hez való leküldés" git push
    
- A változás- és leküldéses véglegesítése. 

>[AZURE.NOTE] Ha ez az első alkalommal egy Git-tárház véglegesítése után, szeretné-e a globális paraméterek konfigurálása *user.name* és *user.email* futtatása előtt a `git commit` parancsot. Futtassa az alábbi két parancsot:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Ha Ön a véglegesítést használatával több Git-tárházakat, használja ugyanazt a nevet és e-mail cím mindent átfogóan. Ugyanazt a nevet és e-mail-cím használatával bizonyul kényelmes később több tárházakon a Git tevékenységek nyomon követésére a Power bi-irányítópultok készítése során.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Létrehozhatja és csatlakoztathatja az Azure file storage projekt erőforrásként (nem kötelező)

Ha szeretne létrehozni az Azure file storage-adatok megosztása, például a projekt nyers adatok vagy a szolgáltatások, a projekt jön létre, hogy az összes projekt tag hozzáférhet az azonos adatkészletek a több Dsvm, kövesse a 3. és 4 [ Csoportos adatelemzési csapatával az érdeklődő feladatok](team-lead-tasks.md). 


## <a name="next-steps"></a>További lépések

Az alábbiakban a hivatkozások a szerepkörök és feladatok határozzák meg a csoportos adatelemzési folyamat részletes ismertetését:

- [Adatelemzési csapatával csoport Manager-feladatok](group-manager-tasks.md)
- [Csoportos adatelemzési csapatával az érdeklődő feladatok](team-lead-tasks.md)
- [Érdeklődő tevékenységeket az adatelemzési csapatával](project-lead-tasks.md)
- [Projektben egyes közreműködők az adatelemzési csapatával](project-ic-tasks.md)