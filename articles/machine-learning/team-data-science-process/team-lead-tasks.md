---
title: A csapat feladatok vezethet a a csapat adatelemzési folyamat csapatával – Azure
description: A feladatok, amelyek egy Csapatvezető egy data science csoportprojektet a Vázlat várhatóan hajtsa végre az adatelemzési csapatával.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, "(previous author=deguhath, ms.author=deguhath)"
ms.openlocfilehash: 68fd0abf314eb4fde5ff335acc161d82ad78cd0c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106634"
---
# <a name="tasks-for-the-team-lead-in-the-team-data-science-process-team"></a>A csapat adatelemzési folyamat csapatával az vezethet a csapat feladatok

Ez a témakör ismerteti a feladatokat, amely egy Csapatvezető várható esetében az adatelemzési csapatával. Együttműködő csapat környezet, amely szabványosítja az a célja a [csoportos adatelemzési folyamat](overview.md) (TDSP). Tdsp-vel egy rugalmas és iteratív adatelemzési módszer, amelynek célja prediktív elemzési megoldások és intelligens alkalmazások hatékony. Együttműködés és a csapat learning növelésére tervezték. A folyamat egy szerkezetekből ajánlott eljárást, és mind a Microsofttól, valamint az iparág, hogy elősegítse a cégek teljes adatelemzési kezdeményezések sikeres megvalósítását szükséges struktúrák rejlő előnyöket a analytics programok. Röviden ismerteti a személyzet szerepkörök és hozzájuk kapcsolódó részfeladatokat adatelemzési csapatával által végrehajtott ezt a folyamatot, a szabványosításával lásd [csoportos adatelemzési folyamat szerepkörök és feladatok](roles-tasks.md).

A **csapata** egy csapatot, a data science egység a vállalat felügyeli. Egy csoport több adatszakértők áll. Data science egység csak kis számú adatszakértők az **Csoportkezelő** és a **csapata** lehet, hogy ugyanaz a személy vagy azok sikerült engedélyezheti – delegálja a feladatot egy helyettes. De a feladatok ne módosítsa. A munkafolyamat tölti ki a környezetet a csapatvezetők feladatok számára az alábbi ábrán vannak kitaláltak:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] A feladatokat az 1. és 2. ábra blokkok van szükség, ha az Azure DevOps használja a kód üzemeltetési platformot, és a egy külön Azure DevOps-projekt saját csapat számára legyen. Miután végrehajtotta ezeket a feladatokat a csoport minden tárház a projekt alatt hozható létre. 

Után a következő szakaszban megadott feladatokat teljesülnek, a csoport-kezelő számos előfeltételnek, nincsenek az öt egyszerű feladatokat (egyes nem kötelező), amely ebben az oktatóanyagban elvégezte. Ezek a feladatok felel meg a fő számozott Ez a témakör szakaszait:

1. Hozzon létre egy **projekt** meg a csoport az Azure DevOps-szolgáltatásokkal, a csoport és a projekt adattárait két csapat:
    - **ProjectTemplate tárház** 
    - **TeamUtilities tárház**
2. Rendezze a csapat **ProjectTemplate** adattárat a **GroupProjectTemplate** tárház, amely be lett állítva a csoport kezelője által. 
3. Hozza létre a team adatok és analitika erőforrások:
    - A csapat-specifikus segédprogramok használatával adja hozzá a **TeamUtilities** tárház. 
    - (Nem kötelező) Hozzon létre egy **az Azure file storage** azon adategységeiről, amelyeket a teljes csoport számára hasznos lehet tárolására használható. 
4. (Nem kötelező) Csatlakoztassa az Azure file storage-a **adatelemző virtuális gép** (DSVM) a csapat vezethet, és adja hozzá az adategységek rajta.
5. Állítsa be a **biztonsági ellenőrzést** a csoporttagok felvétele, és konfigurálja a jogosultságait.

>[AZURE.NOTE] Az Azure DevOps használatával a következő utasítások TDSP csoportos környezetben beállításához szükséges lépéseket szerkezeti azt. Azt adja meg, hogyan végezheti el ezeket a feladatokat az Azure DevOps, mivel az a Microsoft TDSP tanfolyamsorozat. Egy másik, a üzemeltetési platform kód a csoport használható, ha a feladatokat kell végrehajtani a csapatvezetők általában, ne módosítsa. Azonban ezeket a feladatokat a módja eltérő lesz.

## <a name="repositories-and-directories"></a>Adattárak és könyvtárak

Ez a témakör az adattárak és a címtárak rövidített használja. Ezeket a neveket könnyebben hajtsa végre a műveleteket az adattárak és a címtárak között. Ezen jelölés (**R** a Git-tárházak és **D** a dsvm-hez a helyi címtárak) szerepel a következő szakaszokban:

- **R1**: A **GroupProjectTemplate** , amely a csoport kezelőjének beállítása az Azure DevOps-csoport kiszolgálón Git a tárház.
- **R3**: A csapat **ProjectTemplate** beállítása Git a tárház.
- **R4**: A **TeamUtilities** beállítása Git a tárház.
- **A D1**: A helyi könyvtárban klónozható R1 a, és másolja a D3.
- **D3**: A helyi könyvtárban klónozható a R3, testreszabása és visszamásolja R3.
- **D4**: A helyi könyvtárban klónozható R4 a, testre szabhatja, és másolja vissza az R4.

Ebben az oktatóanyagban az adattárak és könyvtárakhoz megadott nevek feltételezve, hogy az a célja egy külön projektet a saját nagyobb data science csoporton belüli csoport létrehozása adtak meg. Azonban más lehetőség van, Csapatvezető megnyitása:

- A teljes csoport lehet váltani, egyetlen projekt létrehozása. Ezután minden-projektekhez az összes adat adattudománnyal foglalkozó csapatai Ez egyetlen projekt keretében lenne. Ennek érdekében megadhat egy git-rendszergazdát, hogy kövesse ezeket az utasításokat, egyetlen projekt létrehozásához. Lehet, hogy ez a forgatókönyv érvényes, például:
    -  egy kis méretű adatok adatelemzési csoport, amelynek nincs több adat adattudománnyal foglalkozó csapatai 
    -  nagyobb data science csoport több data science csapatok számára, amely mégis szeretne optimalizálja a tevékenységeket, például-szintű sprint tervezési közötti együttműködést. 
- Csapatok kiválaszthatják a csapat-specifikus projektsablonok vagy csapat-specifikus segédprogramok a a teljes csoport egyetlen projekt keretében. Ebben az esetben a csapatvezetők hozzon létre projekt sablon adattárak és/vagy csapat segédprogramok tárházak ugyanazon a projekten alatt. Nevezze el az alábbi két adattár *< TeamName\>ProjectTemplate* és *< TeamName\>segédprogramok*, például *TeamJohnProjectTemplate*és *TeamJohnUtilities*. 

Minden esetben csapatvezetők ahhoz, hogy a csapat tagjai tudja, melyik sablont és segédprogramok tárházak beállítása és a projekt és segédprogramok adattár klónozása alkalmaznia kell. Projekt érdeklődők kell követniük a [feladatok projekt vezethet az adatelemzési csapatával](project-lead-tasks.md) hozhat létre a project-adattárak, külön projektek alatt vagy egy projekt alatt. 


## <a name="0-prerequisites"></a>0. Előfeltételek

Az előfeltételek teljesülnek, a hozzárendelve a csoport kezelőjének ismertetett feladatok végrehajtásával [Csoportkezelő feladatokat egy adatelemzési csapatával](group-manager-tasks.md). Összefoglalva az itt, a következő követelményeknek kell megfelelni a csapat vezető feladatok megkezdése előtt: 

- A **csoportban az Azure DevOps-szolgáltatásokkal** (vagy egyéb üzemeltetési platform kód a fiók) be lett állítva a csoport kezelője által.
- A **GroupProjectTemplate tárház** (R1) be lett állítva a csoport-fiókjában a csoport-kezelő üzemeltetési platform használatát tervezi a kódba.
- Rendelkezik-e **jogosult** csoport fiókja csapata tárházak létrehozásához.
- Git a gépen telepítve kell lennie. Ha egy adatelemzési virtuális gép (DSVM) használ, Git előre telepítve van, és készen áll. Egyéb esetben a [platformok és eszközök függelék](platforms-and-tools.md#appendix).  
- Ha használ egy **Windows DSVM**, rendelkeznie kell [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a gépén. A README.md fájl görgessen le a **töltse le és telepítse** szakaszt, és kattintson a *legújabb*. Ezzel továbblép a telepítő legújabb lapra. Töltse le az .exe itt, és futtathatja. 
- Ha használ **Linuxos DSVM**, egy nyilvános SSH-kulcs létrehozása a dsvm-hez, és adja hozzá a csoport az Azure DevOps-szolgáltatásokkal. SSH kapcsolatos további információkért lásd: a **hozzon létre SSH nyilvános kulcs** című rész a [platformok és eszközök függelék](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-project-and-repositories"></a>1. Hozzon létre egy projektet és adattárak

Töltse ki ezt a lépést, ha használja az Azure DevOps, a kódot futtató verziókezelés és Együttműködési platform. Ez a szakasz rendelkezik, hozzon létre három összetevők a csoport az Azure DevOps-szolgáltatásokkal:

- **MyTeam** az Azure DevOps project
- **MyProjectTemplate** tárház (**R3**) a Git
- **MyTeamUtilities** tárház (**R4**) a Git

### <a name="create-the-myteam-project"></a>A MyTeam-projekt létrehozása

- Nyissa meg a csoport az Azure DevOps-szolgáltatásokkal kezdőlap URL-címen `https://<Azure DevOps Services Name\>.visualstudio.com`. 
- Kattintson a **új** projekt létrehozásához. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Projekt létrehozása ablak kéri, adjon meg a projekt neve (**MyTeam** ebben a példában). Győződjön meg arról, hogy kiválasztotta **Agile** , a **folyamatsablon** és **Git** , a **verziókövetés**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Kattintson a **Create project**. A projekt **MyTeam** kevesebb mint 1 percen belül létrejön. 

- A projekt **MyTeam** van hozta létre, kattintson a **nyissa meg a projekt** gombra, a rendszer a projekt kezdőlapjára. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Ha megjelenik egy **Gratulálunk!** előugró ablakban kattintson a **adja hozzá kódot** (vörös téglalappal gomb). Ellenkező esetben kattintson a **kód** (a sárga mezőben). Ez irányítja a projekt Git-tárház lapján. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>A Git a MyProjectTemplate tárház (R3) létrehozása

- A projekt Git-tárház lapján kattintson az adattár neve melletti lefelé mutató nyílra **MyTeam**, és válassza ki **tárházak kezelése...** .

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- Az a **verziókövetés** lapon a projekt a Vezérlőpulton kattintson **MyTeam**, majd **átnevezése tárház...** . 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Adjon meg egy új nevet a tárház a **nevezze át a MyTeam tárház** ablak. Ebben a példában *MyTeamProjectTemplate*. Kiválaszthatja az alábbihoz hasonló *< a Csapatnév\>ProjectTemplate*. Kattintson a **átnevezése** folytatásához.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>A Git a MyTeamUtilities tárház (R4) létrehozása

- Új adattár létrehozása *< a Csapatnév\>segédprogramok* az-projektben kattintson **új adattár...**  a a **verziókövetés** a projekt Vezérlőpult lapján.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- Az a **hozzon létre egy új adattár** , felugró ablakban adja meg a tárház nevét. Ebben a példában nevet használjuk, *MyTeamUtilities*, amely **R4** a jelöléssel. Válassza ki a következőhöz hasonló *< a Csapatnév\>segédprogramok*. Győződjön meg arról, hogy kiválasztotta **Git** a **típus**. Kattintson a **létrehozás** folytatásához.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Győződjön meg arról, hogy a két új Git-tárházakat a projekt alatt létrehozott **MyTeam**. Ebben a példában: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-projecttemplate-and-teamutilities-repositories"></a>2. Rendezze az ProjectTemplate és TeamUtilities tárházak

A kiindulási eljárás a könyvtárak a helyi dsvm-hez a köztes átmeneti tárolási helyként használja. Ha testre kell szabnia a **ProjectTemplate** és **TeamUtilities** megfelelnek bizonyos megadott-tárházakról igények kielégítéséhez csapat, így tesz, a következő eljárás az utolsó előtti lépésben. Itt található egy összefoglaló az tartalmának kezdőszámát használt lépéseket a **MyTeamProjectTemplate** és **MyTeamUtilities** tárolóhelyekkel adatelemzési csapatával. Az egyes lépések a struktúrát a kiindulási eljárásban felelnek meg:

- Klónozott csoportba tárház helyi könyvtárba: R1 – helyi D1 -> klónozni csapata
- Klónozza a csapat tárházak helyi címtárak: R3 & R4 – helyi D3 & D4 -> klónozni csapata
- Csoport projekt sablon tartalmának másolása a helyi csoport mappa: a D1 - tartalmát másolta a D3 ->
- Helyi D3 & D4 (nem kötelező) testreszabása
- Küldje le a helyi könyvtár tartalma csapat tárházakba: D3 & D4 - tartalom -> hozzá R3 & R4 csapata


### <a name="initialize-the-team-repositories"></a>A csapat tárházak inicializálása

Ebben a lépésben a projektadattárat a sablon a csoport projekt sablontár inicializálása:

- **MyTeamProjectTemplate** tárház (**R3**), a **GroupProjectTemplate** (**R1**) tárház


### <a name="clone-group-repositories-into-local-directories"></a>Klónozott csoportba tárházak be helyi címtárak

Ez az eljárás a kezdéshez:

- A helyi gépén hozzon létre a könyvtárak:
    - A **Windows**: **C:\GitRepos\GroupCommon** és **C:\GitRepos\MyTeam**
    - A **Linux**: **GitRepos\GroupCommon** és **GitRepos\MyTeam** meg a kezdőkönyvtár 
- A címtár módosítása **GitRepos\GroupCommon**.
- Futtassa a következő parancsot, szükség esetén a helyi számítógép operációs rendszeren.

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Ezek a parancsok klónozza a **GroupProjectTemplate** a csoport az Azure DevOps-szolgáltatásokkal a helyi könyvtárba (R1) tárházából **GitRepos\GroupCommon** a helyi gépen. A klónozás, directory után **GroupProjectTemplate** (D1) hoznak létre a könyvtár **GitRepos\GroupCommon**. Itt, feltételezzük, hogy a csoport kezelőjének létrehozott egy projektet **GroupCommon**, és a **GroupProjectTemplate** tárház a projekt alatt áll. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Klónozza a csapat tárházak helyi címtárak

Ezek a parancsok klónozza a **MyTeamProjectTemplate** (R3) és **MyTeamUtilities** (R4) tárházakat a projekt keretében **MyTeam** meg a csoport az Azure DevOps-szolgáltatásokkal, a **MyTeamProjectTemplate** (D3) és **MyTeamUtilities** (D4) könyvtárak **GitRepos\MyTeam** a helyi gépen. 

- A címtár módosítása **GitRepos\MyTeam**
- Futtassa a következő parancsokat, szükség esetén a helyi számítógép operációs rendszeren. 

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

A klónozás, két címtár után **MyTeamProjectTemplate** (D3) és **MyTeamUtilities** (D4) könyvtárban jönnek **GitRepos\MyTeam**. A Microsoft rendelkezik feltételezi Itt a projekt sablont és segédprogramok adattárak, nevű **MyTeamProjectTemplate** és **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-project-template-directory"></a>Csoport projekt sablon tartalmának másolása a helyi projektkönyvtárára sablon

Másolnia a tartalmat a helyi **GroupProjectTemplate** (D1) mappát a helyi **MyTeamProjectTemplate** (D3), futtassa az alábbi rendszerhéj-parancsfájlok egyikét: 

#### <a name="from-the-powershell-command-line-for-windows"></a>A parancssori a Windows PowerShellben       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>A Linux-shellből a **Linuxos dsvm-hez**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

A parancsfájlok kizárása a .git könyvtár tartalmát. A parancsfájl kérni fogja, hogy adja meg a **végezze el az elérési utak** a forráskönyvtár D1, és a célkönyvtárban duplikátum D3.
        

### <a name="customize-your-project-template-or-team-utilities-optional"></a>Testre szabhatja a projekt sablont vagy csapat segédprogramok (nem kötelező)

Testre szabhatja a **MyTeamProjectTemplate** (D3) és **MyTeamUtilities** (D4), ha szükséges, ezen a ponton a telepítési folyamat. 

- Ha azt szeretné, szabhatja testre a csapat igényeinek kielégítése érdekében D3 tartalmát, módosítsa a sablon dokumentumokat, vagy módosítsa a könyvtárstruktúra.

- Ha a csapata néhány segédprogram, amely teljes munkatársaival megosztani kívánt fejlesztett ki, másolja be ezeket a segédprogramokat könyvtárba: D4. 


### <a name="push-local-directory-content-to-team-repositories"></a>Küldje le a helyi könyvtár tartalma csapat tárházakba

D3 és D4 (igény szerint testre szabott) helyi címtárakban a tartalmat ad hozzá a csapat tárházak R3 és R4, futtassa az alábbi git bash-parancsok a Linux-shellből vagy a Windows PowerShell-konzolt. Futtassa a parancsokat a **GitRepos\MyTeam\MyTeamProjectTemplate** könyvtár.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

A fájlok a MyTeamProjectTemplate tárházban, a csoport az Azure DevOps-szolgáltatásokkal szinkronizált szinte azonnal a szkript futása során.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Most futtassa a ugyanazokat a négy, a git-parancsok a **GitRepos\MyTeam\MyTeamUtilities** könyvtár. 

> [AZURE.NOTE]Ha ez az első alkalommal egy Git-tárház véglegesítése után, szeretné-e a globális paraméterek konfigurálása *user.name* és *user.email* futtatása előtt a `git commit` parancsot. Futtassa az alábbi két parancsot:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Ha Ön a véglegesítést használatával több Git-tárházakat, használja ugyanazt a nevet és e-mail cím Ha hozzájuk véglegesítése. Ugyanazt a nevet és e-mail-cím használatával bizonyul kényelmes később több tárházakon a Git tevékenységek nyomon követésére a Power bi-irányítópultok készítése során.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Hozzon létre a team adatok és analitika erőforrások (nem kötelező)

Adatok és analitika erőforrások megosztása a teljes csoport teljesítménnyel és költségekkel előnye van: a csapattagok projektjeikhez hajtsa végre a megosztott erőforrások, költségvetések menti és hatékony együttműködés. Ebben a szakaszban a utasításokat az Azure file storage létrehozásával biztosítunk. A következő szakaszban a utasítást az Azure file storage csatlakoztatása a helyi gépre való biztosítunk. További információk megosztását más erőforrások, például a Azure Data Science Virtual Machines, Azure HDInsight Spark-fürtök: [platformok és eszközök](platforms-and-tools.md). Ez a témakör útmutatást nyújt az Ön data science szempontból kiválasztása az erőforrások, amelyek az igényeinek megfelelő, és termék lapok és egyéb fontos és hasznos oktatóanyagok, amely a Microsoft közzétette hivatkozásokat tartalmaz.

>[AZURE.NOTE] Adatokat küldő közötti adatközpontok, amely lassú és költséges lehet, elkerülése érdekében ügyeljen arra, hogy az erőforráscsoport, tárfiók és az Azure virtuális gép (pl. DSVM) az Azure adatközponton. 

Futtassa a következő szkripteket hozhat létre az Azure file storage a csapat számára. Az Azure file storage a csapat számára, amelyek hasznosak a teljes csoport adategységek tárolásához használható. A parancsfájl kérni fogja az Azure fiók és -előfizetés adatait, így ezeket a hitelesítő adatokat adja meg készen kell. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Az Azure file storage létrehozása a Windows PowerShell használatával

Futtassa a parancsfájlt a PowerShell parancssori:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Jelentkezzen be a Microsoft Azure-fiókot, amikor a rendszer kéri:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Válassza ki a használni kívánt Azure-előfizetést:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Válassza ki, melyik tárfiókot használja, vagy hozzon létre egy újat a kijelölt előfizetésben:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Adja meg a nevét, az Azure file Storage hozhat létre. Csak alsó használatieset-karaktereket, számokat és - fogadja:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

Csatlakoztatja, és ez a tároló megosztás létrehozása után elősegítése érdekében mentse egy szövegfájlba az Azure file storage információkat, és jegyezze fel az elérési út helyét. Különösen a csatlakoztatása az Azure virtuális gépek a következő szakaszban az Azure file storage-fájl szükséges. 

Tanácsos a szöveges fájl az ProjectTemplate adattárba. Azt javasoljuk, hogy a címtárban put **Docs\DataDictionaries**. Ezért ezt az adategységet hozzáférhet a csapat összes projektet. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Az Azure file storage egy Linux parancsfájl létrehozása

Futtassa a parancsfájlt a Linux felületen:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Jelentkezzen be a Microsoft Azure-fiók ezen a képernyőn megjelenő utasításokat követve:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Válassza ki a használni kívánt Azure-előfizetést:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Válassza ki, melyik tárfiókot használja, vagy hozzon létre egy újat a kijelölt előfizetésben:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Adja meg a nevét, a létrehozásához az Azure file storage, csak kisbetűs karaktereket, számokat és - fogadja:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

A tárolók elérése során, a létrehozást követően megkönnyítésére mentse egy szövegfájlba az Azure file storage információkat, és jegyezze fel az elérési út helyét. Különösen a csatlakoztatása az Azure virtuális gépek a következő szakaszban az Azure file storage-fájl szükséges.

Tanácsos a szöveges fájl az ProjectTemplate adattárba. Azt javasoljuk, hogy a címtárban put **Docs\DataDictionaries**. Ezért ezt az adategységet hozzáférhet a csapat összes projektet. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Az Azure file storage csatlakoztatása (nem kötelező)

Az Azure file storage sikeres létrehozása után azt a helyi gépre, az egyik az alábbi PowerShell- vagy Linux-parancsfájlok segítségével csatlakoztathatók.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Az Azure file storage csatlakoztatása a Windows PowerShell használatával

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
A rendszer felkéri jelentkezzen be az első, ha nincs bejelentkezve. 

Kattintson a **Enter** vagy **y** esetén a rendszer kéri, hogy ha rendelkezik egy Azure-beli tárolási adatainak fájlt, és ezután bemeneti is a ***fejezze be a elérési útja és neve** hoz létre a fájl előző lépésben. Beolvassa a információkat egy az Azure file storage csatlakoztatása közvetlenül az, hogy fájlt, és készen áll, folytassa a következő lépéssel.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Ha nem rendelkezik az Azure file storage információkat tartalmazó fájl, a lépések bemeneti billentyűzet adatait Ez a szakasz végén található állnak rendelkezésre.

Ezután rendszer adja meg a meghajtót kell hozzáadni a virtuális gép nevét. Egy meglévő meghajtó-nevek listája a képernyő van nyomtatva. Meg kell adnia a meghajtó neve, amely még nem létezik a listában.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Győződjön meg arról, hogy egy új F meghajtó sikeresen csatlakoztatva lett a gépre.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Adja meg manuálisan az Azure file storage adatokat hogyan:** Ha nem rendelkezik az Azure file storage adatait egy szövegfájlt, akkor is kövesse az utasításokat a következő képernyőn írja be az előfizetés szükséges, a storage-fiók és az Azure File storage információkat:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Írja be az Azure-előfizetés nevére, válassza ki a tárfiókot, ahol az Azure file storage jön létre, és írja be a az Azure file storage nevét:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Azure file storage csatlakoztatása egy Linux-parancsfájllal

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

A rendszer felkéri jelentkezzen be az első, ha nincs bejelentkezve. 

Kattintson a **Enter** vagy **y** esetén a rendszer kéri, hogy ha rendelkezik egy Azure-beli tárolási adatainak fájlt, és ezután bemeneti is a ***fejezze be a elérési útja és neve** hoz létre a fájl előző lépésben. Beolvassa a információkat egy az Azure file storage csatlakoztatása közvetlenül az, hogy fájlt, és készen áll, folytassa a következő lépéssel.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Ezután rendszer adja meg a meghajtót kell hozzáadni a virtuális gép nevét. Egy meglévő meghajtó-nevek listája a képernyő van nyomtatva. Meg kell adnia a meghajtó neve, amely még nem létezik a listában.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Győződjön meg arról, hogy egy új F meghajtó sikeresen csatlakoztatva lett a gépre.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Adja meg manuálisan az Azure file storage adatokat hogyan:** Ha nem rendelkezik az Azure file storage adatait egy szövegfájlt, akkor is kövesse az utasításokat a következő képernyőn írja be az előfizetés szükséges, a storage-fiók és az Azure File storage információkat:

- Bemeneti **n**.
- Válassza ki az előfizetés nevét, ahol az Azure file storage az előző lépésben jött létre index:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Válassza ki a tárfiókot az előfizetésben, és írja be az Azure file storage nevét:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Adja meg a gépén, amelyek azonosak minden meglévőket kell hozzáadni a meghajtó neve:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Állítsa be a biztonsági szabályok 

A csoport az Azure DevOps szolgáltatás kezdőlapján kattintson a **fogaskerék ikont** mellett a jobb felső sarokban a felhasználónevére, majd válassza ki a **biztonsági** fülre. A csapat itt különböző engedélyekkel rendelkező tagokat adhat hozzá.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>További lépések

Az alábbiakban a hivatkozások a szerepkörök és feladatok határozzák meg a csoportos adatelemzési folyamat részletes ismertetését:

- [Adatelemzési csapatával csoport Manager-feladatok](group-manager-tasks.md)
- [Csoportos adatelemzési csapatával az érdeklődő feladatok](team-lead-tasks.md)
- [Érdeklődő tevékenységeket az adatelemzési csapatával](project-lead-tasks.md)
- [Projektben egyes közreműködők az adatelemzési csapatával](project-ic-tasks.md)