---
title: Vonja össze az adatokat tudományos folyamat Team vezethet feladatok – Azure |} Microsoft Docs
description: A feladatokat egy csoport röviden a tudományos adatok team projekt vezethet.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: bradsev;
ms.openlocfilehash: 995ad557eb06e545b1813e1f4631e243a98830b3
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="team-lead-tasks"></a>Az érdeklődési feladatainak

Ez a témakör vázol fel, amely egy feladatok végezze el az adatok tudományos csoport nem várt. Hoz létre, amely egységesíti az együttműködést team környezetet a [Team adatok tudományos folyamat](overview.md) (TDSP). TDSP egy gyors, fokozatos adatok tudományos módszertana prediktív elemzési megoldások és intelligens alkalmazások hatékonyan továbbítani. Együttműködés és csapata tanulási javítása érdekében tervezték. A folyamat egy lepárlása az ajánlott eljárásokat, és a Microsoft vagy az iparág, az adatok tudományos kezdeményezések segítségével a vállalatok teljesen sikeres végrehajtásához szükséges struktúrák a analytics programok a előnyök megvalósításához. A személyzet szerepkörök és a kapcsolódó feladatok, egy data tudományos csapat által kezelt vázlat szabványosítása a folyamattal, lásd: [Team adatok tudományos folyamat szerepköröket és feladatokat](roles-tasks.md).

A **Team vezethet** vállalati adatok tudományos egységben csapata kezeli. Egy csoport több adatszakértőkön áll. Adatok tudományos egység csak egy kis mennyiségű adatszakértőkön a **csoport kezelőjének** és a **Team vezethet** előfordulhat, hogy ugyanaz a személy, vagy azokat a sikertelen feladat egy helyettesítő feladatuknak. De a feladatok ne módosítsa. A munkafolyamat által team vezet a környezet beállításához elvégzendő feladatok az alábbi ábrán ezek ábrázolva:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] 1. és 2. ábra blokkokban feladatok ha platform üzemeltető kódú Visual Studio Team Services (VSTS) használ, és szeretne használni egy külön csapat-projektjét, amely a saját csoport van szükség. Ezek a feladatok elvégzése után a csoport összes adattárak ezzel a csapatprojekttel alapján hozhatók létre. 

Után a következő szakaszban meghatározott feladatok teljesülnek a csoport-kezelő számos előfeltételt, nincsenek az öt egyszerű feladatokat (egyes nem kötelező), amely az oktatóanyag befejezése. Ezeket a feladatokat az elsődleges, ez a témakör szakaszai számozott felel meg:

1. Hozzon létre egy **csapatprojekt** VSTS-kiszolgálón a csoport a csoport és a projekt két team tárházak:
    - **ProjectTemplate tárház** 
    - **TeamUtilities tárház**
2. Feltöltheti a csapat **ProjectTemplate** a tárházat a **GroupProjectTemplate** tárház, amely szerint a csoport kezelőjének beállítása. 
3. Hozza létre a team adatok és analitikák erőforrások:
    - Adja hozzá a munkacsoport-specifikus segédprogramokat, ha a **TeamUtilities** tárházba. 
    - (Választható) Hozzon létre egy **az Azure file storage** adategységeiről, amelyeket a teljes csoport számára hasznos lehet tárolására használható. 
4. (Választható) Az az Azure file storage való csatlakoztatása a **adatok tudományos virtuális gép** (DSVM) a csapat vezethet, és adja hozzá az adategységek rajta.
5. Állítsa be a **biztonsági ellenőrzést** a csoport tagjainak hozzáadása és konfigurálása a jogosultságait.

>[AZURE.NOTE] A Microsoft szerkezeti VSTS használatát az alábbi utasítások alapján TDSP csoportos környezetben beállításához szükséges lépéseket. Azt adja meg, hogyan hajthat végre ezeket a feladatokat a VSTS, mivel az a Microsoft TDSP megvalósítása módját. Egy másik kódot platform üzemeltető csoport használata esetén a feladatokat, amelyeket teljesíteni a csapat az érdeklődési általában ne módosítsa. De ezek a feladatok úgy a különböző lesz.

## <a name="repositories-and-directories"></a>Tárolóhelyekkel és könyvtárak

Ez a témakör a tárolóhelyekkel és könyvtárak rövidített csoportneveket használja. Ezek a nevek könnyebben hajtsa végre a műveletek a tárolóhelyekkel és a címtárak között. Ez a notation (**R** a Git-tárházak és **D** a DSVM a helyi könyvtárak) a következő szakaszokban szolgál:

- **R1**: A **GroupProjectTemplate** tárházat a Git, amely a csoport kezelőjének beállítása a VSTS csoport kiszolgálón.
- **R3**: A csapat **ProjectTemplate** tárházából beállította a Git.
- **R4**: A **TeamUtilities** tárházából beállította a Git.
- **D1**: A helyi címtárszolgáltatásban R1 alapján klónozták, és D3 másolva.
- **D3**: A helyi címtárszolgáltatásban R3 alapján klónozták, testreszabása és visszakerül R3.
- **D4**: A helyi címtárszolgáltatásban R4 alapján klónozták, testreszabása és visszakerül R4.

Ebben az oktatóanyagban a tárolóhelyekkel és a könyvtárak a megadott nevek vannak-e megadva feltételezve, hogy az a célja egy külön csapat-projektjét, amely a saját csoport nagyobb adatok tudományos csoporton belül. De lehetőség áll rendelkezésre más meg van nyitva, mint team átfutási:

- A teljes csoport választhat egy csoportot projekt létrehozásához. Majd a összes adatok tudományos csapat összes projektek kell a egyetlen ezzel a csapatprojekttel. Ennek érdekében a git rendszergazdák is képesek egy csoportot-projekt létrehozása a következő lépések követésével is kijelölhet. Lehet, hogy ez a forgatókönyv érvényes, például:
    -  Nincs több adat tudományos csapat kisméretű tudományos csoportja 
    -  a nagyobb adatok tudományos csoportban található adatok tudományos több csapat, hogy optimalizálja a tevékenységeket, például a csoport szintű sprint tervezési közötti együttműködést azonban próbál. 
- Csoportok beállíthatja úgy a csoport-specifikus projektsablonjai vagy team-specifikus segédprogramok a teljes csoport egyetlen csapatprojekt alatt. Ebben az esetben a csapat érdeklődők hozzon létre team projekt sablon adattárak és/vagy team segédprogramok adattárak azonos csapatprojekt alatt. A tárolóhelyekkel name *< TeamName\>ProjectTemplate* és *< TeamName\>segédprogramok*, például *TeamJohnProjectTemplate*és *TeamJohnUtilities*. 

Minden esetben team érdeklődők kell ahhoz, hogy a csoport tagjai tudja, melyik sablont és segédprogramok tárhelyek beállításához és a projekt és segédprogramok adattárak Klónozás elfogadják. Projekt érdeklődők kell követnie a [tudományos adatok csoport feladatainak projekt vezethet](project-lead-tasks.md) projekt adattárak létrehozásához, hogy külön csapatprojektek, illetve egyetlen csapatprojekt. 


## <a name="0-prerequisites"></a>0. Előfeltételek

Az előfeltételek teljesülnek, a csoport kezelőjének leírt rendelt feladatok végrehajtásával [csoport kezelőjének feladatokat tudományos adatok csoport](group-manager-tasks.md). Itt összefoglalva, az alábbi követelményeknek kell felel meg a csoport az érdeklődési feladatok megkezdése előtt: 

- A **csoport VSTS server** (vagy a platform futtató egyéb kód csoportfiók) be van állítva a csoport kezelőjének által.
- A **GroupProjectTemplate tárház** (R1) be van állítva a csoport fiókját a csoport-kezelő a használni kívánt platform üzemeltető kódba.
- Rendelkezik-e **engedélyezett** csoport fiókja tárolóhelyekkel a csoport létrehozásához.
- Git telepítenie kell a számítógépre. Ha egy tudományos virtuális gép (DSVM) használ, Git előre telepítve van, és visszaigazolásához. Ellenkező esetben tekintse meg a [platformok és az eszközök függelék](platforms-and-tools.md#appendix).  
- Ha használ egy **Windows DSVM**, telepíteni kell [Git hitelesítőadat-kezelő (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) van telepítve a számítógépre. A README.md fájl görgessen le a **töltse le és telepítse** szakaszt, és kattintson a *installer legújabb*. Ezzel megnyitná a legfrissebb telepítési oldal. Töltse le a .exe-telepítő címről, és futtassa. 
- Ha használ **Linux DSVM**, a DSVM a nyilvános SSH-kulcs létrehozása, és adja hozzá a csoporthoz VSTS-kiszolgáló. SSH kapcsolatos további információkért tekintse meg a **létrehozása SSH nyilvános kulcs** szakasz a [platformok és az eszközök függelék](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-team-project-and-repositories"></a>1. Hozzon létre egy csapatprojekt és a tárhelyekhez

E lépés elvégzése után VSTS meg a platform versioning és együttműködés üzemeltető használatakor. Ez a szakasz hozhat létre három összetevők a VSTS-kiszolgálón, a csoport rendelkezik:

- **MyTeam** projektre a VSTS
- **MyProjectTemplate** tárház (**R3**) a Git
- **MyTeamUtilities** tárház (**R4**) a Git

### <a name="create-the-myteam-project"></a>A MyTeam projekt létrehozása

- Lépjen a csoport VSTS server kezdőlap URL-címen `https://<VSTS Server Name\>.visualstudio.com`. 
- Kattintson a **új** team projekt létrehozásához. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- A Create team projekt ablakról arra kéri, hogy a projekt nevét (**MyTeam** ebben a példában). Győződjön meg arról, hogy kiválassza **Agile** , a **folyamatsablon** és **Git** , a **verziókezelést**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Kattintson a **Create project**. A csapatprojekt **MyTeam** kevesebb mint 1 perces jön létre. 

- A csapatprojekt után **MyTeam** van létrehozni, kattintson **és projekt lép** gombra, a csapatprojekt kezdőlapján irányíthatja. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Ha megjelenik egy **Gratulálunk!** előugró ablak, kattintson a **kódot** (vörös téglalappal gombra). Ellenkező esetben kattintson a **kód** (a sárga mezőben). Ez irányítja a csapatprojekt Git-tárház oldalán. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>A Git MyProjectTemplate összetevőtárházat (R3) létrehozása

- A csapatprojekt Git-tárház oldalán kattintson a tárház neve melletti lefelé mutató nyílra **MyTeam**, és válassza ki **adattárak kezelése...** .

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- A a **verziókezelést** lapon a Vezérlőpult a csapat projekt, kattintson a **MyTeam**, majd válassza **átnevezése tárház...** . 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Adjon meg egy új nevet a tárházba a **nevezze át a MyTeam tárház** ablak. Ebben a példában *MyTeamProjectTemplate*. Dönthet úgy hasonlót *< a csoport neve\>ProjectTemplate*. Kattintson a **átnevezése** folytatja.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>A Git MyTeamUtilities összetevőtárházat (R4) létrehozása

- Új adattár létrehozása *< a csoport neve\>segédprogramok* csoportban a csapatprojekt **új tárház...**  a a **verziókezelést** a csapatprojekt-Vezérlőpult lapján.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- Az a **hozzon létre egy új tárház** ablakban, adja meg a tárház nevét. Ebben a példában azt adjon neki nevet, *MyTeamUtilities*, amely **R4** a jelöléssel. Válassza ki a következőhöz hasonlóan *< a csoport neve\>segédprogramok*. Győződjön meg arról, hogy kiválassza **Git** a **típus**. Kattintson a **létrehozása** folytatja.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Ellenőrizze, hogy látható-e a csapatprojekt létrehozott két új Git tárházak **MyTeam**. Ebben a példában: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-team-projecttemplate-and-teamutilities-repositories"></a>2. A csapat ProjectTemplate és TeamUtilities adattárak magtípusú leképezést

Az index-összehangolási eljárás használja a könyvtárak a helyi DSVM a köztes átmeneti helyek. Ha testre szeretné szabni a **ProjectTemplate** és **TeamUtilities** tárhelyek megfelelnek bizonyos megadott csapatának van szüksége, azt megteheti az alábbi eljárás az utolsó előtti lépésben. A tartalom rendezésére használható lépések összegzése a **MyTeamProjectTemplate** és **MyTeamUtilities** tudományos adatok csoport tárolóhelyekkel. Az egyes lépéseket felelnek meg az index-összehangolási eljárás alszakaszokat:

- Klónozás csoport tárház helyi könyvtárba: vonja össze az R1 --> helyi D1 klónozása
- A csapat adattárak klónozza a helyi könyvtárak: vonja össze az R3 & R4 --> helyi D3 & D4 klónozása
- A csoport projekt sablon tartalmának másolása a helyi csoport mappába: D1 - másolva D3 -> tartalma
- (Választható) helyi D3 & D4 testreszabása
- Helyi könyvtár tartalmának leküldése team adattárak: D3 & D4 - tartalmak hozzáadása -> vonja össze az R3 & R4


### <a name="initialize-the-team-repositories"></a>A csapat adattárak inicializálása

Ebben a lépésben a csapat projekt sablon tárház a csoport projekt sablon tárházból inicializálása:

- **MyTeamProjectTemplate** tárház (**R3**) az a **GroupProjectTemplate** (**R1**) tárház


### <a name="clone-group-repositories-into-local-directories"></a>Klónozás csoport tárházak találhatók, a helyi könyvtárak

Ez az eljárás megkezdéséhez:

- Könyvtárak létrehozása a helyi számítógépen:
    - A **Windows**: **C:\GitRepos\GroupCommon** és **C:\GitRepos\MyTeam**
    - A **Linux**: **GitRepos\GroupCommon** és **GitRepos\MyTeam** meg a kezdőkönyvtár 
- Váltson **GitRepos\GroupCommon**.
- A következő parancsot, a helyi számítógép operációs rendszerének megfelelő módon.

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Ezek a parancsok klónozza a **GroupProjectTemplate** (R1) tárház a csoport VSTS kiszolgálón a helyi címtárban lévő **GitRepos\GroupCommon** a helyi számítógépen. Klónozás directory után **GroupProjectTemplate** (D1) könyvtárban jön létre **GitRepos\GroupCommon**. Itt azt feltételezzük, hogy a csoport kezelőjének létrehozása egy csapatprojekt **GroupCommon**, és a **GroupProjectTemplate** tárház ezzel a csapatprojekttel alatt áll. 


### <a name="clone-your-team-repositories-into-local-directories"></a>A csapat adattárak klónozza a helyi könyvtárak

Ezek a parancsok klónozza a **MyTeamProjectTemplate** (R3) és **MyTeamUtilities** (R4) adattárak alatt a csapatprojekt **MyTeam** VSTS csoport kiszolgálóján a  **MyTeamProjectTemplate** (D3) és **MyTeamUtilities** (D4) könyvtárainak **GitRepos\MyTeam** a helyi számítógépen. 

- Váltson **GitRepos\MyTeam**
- A következő parancsokat, szükség esetén a helyi számítógép operációs rendszerére. 

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Klónozás, a két címtár után **MyTeamProjectTemplate** (D3) és **MyTeamUtilities** (D4) directory jönnek létre **GitRepos\MyTeam**. Igazolnia kell feltételezve, hogy itt nevű a csapatprojekt sablon és segédprogramok adattárak **MyTeamProjectTemplate** és **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-team-project-template-directory"></a>A csoport projekt sablon tartalmat a helyi csoport projektkönyvtár sablon másolása

Másolja a tartalmat a helyi **GroupProjectTemplate** (D1) mappát a helyi **MyTeamProjectTemplate** (D3), futtassa a következő rendszerhéj-parancsfájlok egyikét: 

#### <a name="from-the-powershell-command-line-for-windows"></a>A parancssori Windows Powershellből       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>A Linux-rendszerhéjból a **Linux DSVM**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

A parancsfájlok kizárja a .git könyvtár tartalmát. A parancsfájlok kéri a **fejezze be az elérési utak** a forráskönyvtár D1 és a célkönyvtárat D3.
        

### <a name="customize-your-team-project-template-or-team-utilities-optional"></a>Testre szabhatja a csapat projektsablon vagy team segédprogramok (nem kötelező)

Testre szabhatja a **MyTeamProjectTemplate** (D3) és **MyTeamUtilities** (D4), ha szükséges, a telepítési folyamat ezen a ponton. 

- Ha szeretné testre szabni a csapat a konkrét igényeinek D3 tartalmát, a sablon dokumentumok, vagy módosítsa a könyvtárstruktúra.

- Ha meg szeretné osztani a teljes csapattal néhány segédprogram a csapat fejlesztett ki, másolja be ezeket a segédprogramokat D4 könyvtárba. 


### <a name="push-local-directory-content-to-team-repositories"></a>Helyi könyvtár tartalmának leküldése team adattárak

Tartalma (ha szükséges testreszabott) helyi könyvtárak D3 és D4 hozzáadása a csapat adattárak R3 és R4, futtassa a következő git bash parancsokat egy Windows PowerShell-konzolon vagy a Linux-rendszerhéjból. Futtassa a parancsokat a **GitRepos\MyTeam\MyTeamProjectTemplate** könyvtár.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

A csoport VSTS-kiszolgáló a MyTeamProjectTemplate tárházban található fájlok szinte azonnal szinkronizált a parancsfájl futása során.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Most futtassa a ugyanazokat a négy, a git-parancsok a **GitRepos\MyTeam\MyTeamUtilities** könyvtár. 

> [AZURE.NOTE]Ha az első alkalommal véglegesíti a Git-tárházba, szeretné-e globális paraméterek konfigurálása *user.name* és *user.email* futtatása előtt a `git commit` parancsot. Futtassa az alábbi két parancsot:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Ha több Git adattárak a véglegesítés használja ugyanazt a nevet és e-mail cím Ha hozzájuk véglegesíti. Ugyanazzal a névvel és e-mail címmel bizonyítja kényelmes később a Power bi-irányítópultok nyomon követheti a Git tevékenységek több adattárak összeállításakor.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Hozzon létre a team adatok és analitikák erőforrások (nem kötelező)

Adatok és analitikák erőforrások megosztása a teljes csoport rendelkezik teljesítményének és költséghatékonyságának előnyei: csoport tagjai a projektjeikhez végrehajtja a megosztott erőforrások, költségvetések mentheti, és hatékonyabb együttműködést. Ebben a részben azt vonatkozó utasítások az Azure file storage létrehozásához. A következő szakaszban azt szolgálnak utasítás csatlakoztatási Azure file Storage a helyi számítógépre. További információk megosztását más erőforrások, például az Azure Data tudományos virtuális gépek, Azure HDInsight Spark-fürtjei: [platformok és az eszközök](platforms-and-tools.md). Ez a témakör útmutatást, tudományos szempontjából az igényeinek megfelelő erőforrások kiválasztása, és a termék lapok és más releváns és hasznos oktatóprogramot kínál, amelyek a Microsoft közzétett hivatkozásokat tartalmaz.

>[AZURE.NOTE] Az adatok átvitele az alhálózatok közötti adatközpontokban, amelyek lassú és költséges lehet, elkerülése érdekében győződjön meg arról, hogy az erőforráscsoport, a tárfiók és az Azure virtuális Géphez (pl. DSVM) Azure adatközpontba. 

Futtassa a következő parancsprogramokat az Azure file storage a csapat számára. Az Azure file storage a csapat számára, amelyek hasznosak a teljes csoport adategységeket tárolására használható. A parancsfájlok kéri az Azure fiókja és -előfizetése információkat, így ezeket a hitelesítő adatokat adjon meg készen kell. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Az Azure file storage létrehozása a Windows PowerShell használatával

Futtassa a parancsfájlt a PowerShell parancssori:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Jelentkezzen be a Microsoft Azure-fiókjához, amikor a rendszer kéri:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Válassza ki a használni kívánt Azure-előfizetést:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Válassza ki a használt tárfiók, vagy hozzon létre egy újat a kijelölt előfizetésben:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Adja meg a létrehozása az Azure file storage nevét. Csak kisebb eset karaktereket, számokat és - elfogadottak:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

Csatlakoztatása és megosztása a létrehozásukat követően elősegítése érdekében mentse a az Azure file storage adatokat egy szövegfájlba, és jegyezze fel az elérési út helyére. Különösen a fájl a az Azure file storage a következő szakaszban az Azure virtuális gépeken való csatlakoztatásához szükséges. 

Ajánlott a szöveges fájl történő a csapat ProjectTemplate tárházba. Azt javasoljuk, hogy a címtárban **Docs\DataDictionaries**. Ezért az adategységet elérhetők, a csoport összes projektet. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Az Azure file storage hozzon létre egy Linux-parancsfájl

Ez a parancsfájl futtatása a Linux-rendszerhéjból:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Jelentkezzen be a Microsoft Azure-fiók ezen a képernyőn megjelenő utasításokat követve:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Válassza ki a használni kívánt Azure-előfizetést:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Válassza ki a használt tárfiók, vagy hozzon létre egy újat a kijelölt előfizetésben:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Adja meg a nevét, az Azure file storage létrehozásához, csak kisbetűket, számokat és - elfogadottak:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

Lehetővé teszi, hogy ez a tároló elérése létrehozása után, mentse a az Azure file storage adatokat egy szövegfájlba, és jegyezze fel az elérési út helyére. Különösen a fájl a az Azure file storage a következő szakaszban az Azure virtuális gépeken való csatlakoztatásához szükséges.

Ajánlott a szöveges fájl történő a csapat ProjectTemplate tárházba. Azt javasoljuk, hogy a címtárban **Docs\DataDictionaries**. Ezért az adategységet elérhetők, a csoport összes projektet. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Csatlakoztatási Azure fájltároló (nem kötelező)

Az Azure file storage sikeres létrehozása után azt a helyi gépére, használja az alábbi PowerShell- vagy Linux-parancsfájlok közül lehet csatlakoztatni.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Csatlakoztatási Azure fájltároló a Windows PowerShell használatával

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
A rendszer felkéri jelentkezzen be az első, ha nincs bejelentkezve. 

Kattintson a **Enter** vagy **y** folytatja, amikor rendszer rákérdez, hogy van-e egy fájl Azure tárolással kapcsolatos fájlt, és majd adjon meg a ***fejezze be a elérési útja és neve** hoz létre a fájl előző lépésben. Az információk csatlakoztatása egy az Azure file storage van közvetlenül olvassák be, hogy fájlt, és készen áll a következő lépéssel.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Ha nem rendelkezik az Azure file storage információkat tartalmazó fájl, a lépéseket a felhasználótól, a billentyűzetről adatokat arra szolgálnak, ez a szakasz végén.

Ezután meg kell adnia adja meg a meghajtót, fel kell venni a virtuális gép nevét. Meglévő meghajtó neveinek listáját a képernyőn megjelenő nyomtatása. Meg kell adnia a meghajtó neve, amely nem szerepel a listában.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Győződjön meg arról, hogy egy új F meghajtó sikeresen csatlakoztatva lett a gépre.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Adja meg manuálisan az Azure file storage adatokat hogyan:** nem rendelkezik az Azure file storage adatait egy szövegfájlt, ha akkor is kövesse az utasításokat a következő képernyőn írja be a szükséges előfizetés, a tárfiók és az Azure fájl tárolással kapcsolatos:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Írja be az Azure-előfizetés nevét, jelölje be a tárolási fiók, ahol az az Azure file storage jön létre, és írja be az Azure storage fájlnév:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>A Linux-parancsfájllal csatlakoztatási Azure fájltároló

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

A rendszer felkéri jelentkezzen be az első, ha nincs bejelentkezve. 

Kattintson a **Enter** vagy **y** folytatja, amikor rendszer rákérdez, hogy van-e egy fájl Azure tárolással kapcsolatos fájlt, és majd adjon meg a ***fejezze be a elérési útja és neve** hoz létre a fájl előző lépésben. Az információk csatlakoztatása egy az Azure file storage van közvetlenül olvassák be, hogy fájlt, és készen áll a következő lépéssel.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Ezután meg kell adnia adja meg a meghajtót, fel kell venni a virtuális gép nevét. Meglévő meghajtó neveinek listáját a képernyőn megjelenő nyomtatása. Meg kell adnia a meghajtó neve, amely nem szerepel a listában.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Győződjön meg arról, hogy egy új F meghajtó sikeresen csatlakoztatva lett a gépre.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Adja meg manuálisan az Azure file storage adatokat hogyan:** nem rendelkezik az Azure file storage adatait egy szövegfájlt, ha akkor is kövesse az utasításokat a következő képernyőn írja be a szükséges előfizetés, a tárfiók és az Azure fájl tárolással kapcsolatos:

- Bemeneti **n**.
- Válassza ki az előfizetés nevét, ahol az az Azure file storage az előző lépésben jött létre az indexet:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Jelölje ki a tárfiók előfizetését és típus szerint az Azure storage fájlnév:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Adja meg a nevét, a meghajtó fel kell venni a gépet, mely a már meglévőket eltérőnek kell lennie:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Biztonsági vezérlési házirend beállítása 

A csoport VSTS server kezdőlapján kattintson a **fogaskerék ikonra** mellett jobb felső sarokban a felhasználónevére, majd válassza ki a **biztonsági** fülre. A csoport itt különböző engedélyekkel tagokat adhat hozzá.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>További lépések

Az alábbiakban a szerepkörök és az adatok tudományos eljárással meghatározott feladatok részletes leírását mutató hivatkozásokat:

- [Tudományos adatok csoport csoport Manager-feladatok](group-manager-tasks.md)
- [Az érdeklődési feladatainak egy adatok tudományos csoport](team-lead-tasks.md)
- [Az érdeklődési tevékenységeket a tudományos adatok csoport](project-lead-tasks.md)
- [Project egyes közreműködők a tudományos adatok csoport](project-ic-tasks.md)