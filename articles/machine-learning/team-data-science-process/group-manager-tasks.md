---
title: Csapat adatok adatelemzési folyamat Csoportkezelő feladatok – Azure |} A Microsoft Docs
description: A tevékenységek a data science csapatprojekt csoportvezető röviden ismerteti.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: ee10f17f76f7c3745cb32223c58b0d2d12f7d6b6
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446347"
---
# <a name="group-manager-tasks"></a>Csoportkezelő feladatok

Ez a témakör ismerteti a feladatokat, a csoport kezelőjének várhatóan végezze el a előzmény / saját data science szervezet. Együttműködésen alapuló csoport környezet, amely szabványosítja a célja a [csoportos adatelemzési folyamat](overview.md) (TDSP). Röviden ismerteti a személyzet szerepkörök és hozzájuk kapcsolódó részfeladatokat adatelemzési csapatával által végrehajtott ezt a folyamatot, a szabványosításával lásd [csoportos adatelemzési folyamat szerepkörök és feladatok](roles-tasks.md).

A **Csoportkezelő** a vállalaton belül, a teljes adatelemzési egység vezetője. Előfordulhat, hogy a data science egység több csapat, amelyek mindegyike több különböző üzleti referenciaegyenesen az adatelemzési projektek dolgozik. A csoport kezelőjének delegálhat egy helyettes a feladataikat, de a feladatokat a szerepkörhöz társított azonos. Nincsenek hat fő tevékenység a következő ábrán látható módon:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] Hogy szerkezeti egy TDSP csoport környezet az alábbi utasításokat az Azure DevOps-szolgáltatások segítségével beállításához szükséges lépéseket. Azt adja meg, hogyan végezheti el ezeket a feladatokat az Azure DevOps-szolgáltatásokkal, mivel az a Microsoft TDSP tanfolyamsorozat. Egy másik, a üzemeltetési platform kód a csoport használható, ha a feladatokat kell elvégezni a csoport által általában, ne módosítsa. Azonban ezeket a feladatokat a módja eltérő lesz.

1. Állítsa be a **Azure DevOps-szolgáltatásokkal** a csoporthoz.
2. Hozzon létre egy **projekt** az Azure DevOps-szolgáltatásokkal (Azure fejlesztési és üzemeltetési szolgáltatások számára)
3. Hozzon létre a **GroupProjectTemplate** tárház
4. Hozzon létre a **GroupUtilities** tárház
5. Kezdőérték a **GroupProjectTemplate** és **GroupUtilities** tárolóhelyekkel tartalommal a TDSP változásokat az Azure DevOps-szolgáltatásokkal.
6. Állítsa be a **biztonsági vezérlők** GroupProjectTemplate és GroupUtilities tárházakba eléréséhez a csapattagok számára.

Az egyes az előző lépéseket részletesen ismertetjük. De először azt révén megismerkedhet a rövidítések és beszélgetés a tárházak használatának előfeltételei.

### <a name="abbreviations-for-repositories-and-directories"></a>Az adattárak és a címtárak rövidítések

Ebben az oktatóanyagban rövidített adattárak és a könyvtárakat. Ezeknek a definícióknak könnyebben hajtsa végre a műveleteket az adattárak és a címtárak között. Ezen jelölés szerepel a következő szakaszokban:

- **A G1**: projekt sablontár fejlesztett ki és TDSP csapat, a Microsoft által felügyelt.
- **G2**: A segédprogramok tárház fejlesztett ki és TDSP csapat, a Microsoft által felügyelt.
- **R1**: a Git a GroupProjectTemplate tárház beállítása az Azure DevOps-csoport kiszolgálón.
- **R2**: a Git a GroupUtilities tárház beállítása az Azure DevOps-csoport kiszolgálón.
- **LG1** és **LG2**: A helyi címtárak klónozása rendre G1 és G2, a gépen.
- **LR1** és **LR2**: A helyi címtárak klónozása rendre R1 és R2, a gépen.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>A tárház klónozása és kód ellenőrzése és Előfeltételek
 
- Git a gépen telepítve kell lennie. Ha egy adatelemzési virtuális gép (DSVM) használ, Git előre telepítve van, és készen áll. Egyéb esetben a [platformok és eszközök függelék](platforms-and-tools.md#appendix).  
- Ha használ egy **Windows DSVM**, rendelkeznie kell [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a gépén. A README.md fájl görgessen le a **töltse le és telepítse** szakaszt, és kattintson a *legújabb*. Ez a lépés végigvezeti a telepítő legújabb lapra. Töltse le az .exe itt, és futtathatja. 
- Ha használ **Linuxos DSVM**, egy nyilvános SSH-kulcs létrehozása a dsvm-hez, és adja hozzá a csoport az Azure DevOps-szolgáltatásokkal. SSH kapcsolatos további információkért lásd: a **hozzon létre SSH nyilvános kulcs** című rész a [platformok és eszközök függelék](platforms-and-tools.md#appendix). 


## <a name="1-create-account-on-azure-devops-services"></a>1. Fiók létrehozása az Azure DevOps-szolgáltatásokkal

Az Azure DevOps-szolgáltatásokkal a következő tárházak futtatja:

- **közös tárházak csoport**: általános célú adattárak, amely több adatelemzési projektek a csoporton belül több csapat fogadja el. Ha például a *GroupProjectTemplate* és *GroupUtilities* tárházakban.
- **tárházak csapat**: Tárházak csoporton belüli adott csapatok számára. Az alábbi két adattár egy csapat kell az adott, és lehet több projektet csapat által végrehajtott által elfogadott, de nem általános elég data science csoporton belül több csapat számára hasznos lehet. 
- **a projekt tárházak**: Tárházakhoz elérhető az adott projektekhez. Előfordulhat, hogy az ilyen adattárak nem elég általános a csapat által elvégzett több projektet, és egy data science csoportban több csapat hasznos lehet.


### <a name="setting-up-the-azure-devops-services-sign-into-your-microsoft-account"></a>Az Azure fejlesztési és üzemeltetési szolgáltatások jelentkezzen be a Microsoft-fiókjával beállítása
    
Lépjen a [Visual Studio online](https://www.visualstudio.com/), kattintson a **jelentkezzen be a** a jobb felső sarokban, és jelentkezzen be Microsoft-fiókjával. 
    
![1](./media/group-manager-tasks/login.PNG)

Ha nem rendelkezik Microsoft-fiókot, kattintson a **regisztráció** a Microsoft-fiók létrehozásához, és jelentkezzen be ezzel a fiókkal. 

Ha a szervezet egy Visual Studio/MSDN-előfizetéssel rendelkezik, kattintson a zöld **jelentkezzen be munkahelyi vagy iskolai fiókjával** mezőbe, és jelentkezzen be az ehhez az előfizetéshez társított hitelesítő adatokat. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
Miután bejelentkezett, kattintson a **új fiók létrehozása** jobb felső sarokban az alábbi képen látható módon:
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Írja be az adatokat, amely a létrehozandó Azure-fejlesztési és üzemeltetési szolgáltatás a **-fiók létrehozása** varázsló a következő értékeket: 

- **Kiszolgáló URL-címe**: csere *mysamplegroup* saját *kiszolgálónév*. Annak a kiszolgálónak az URL-cím lesz: *https://\<servername\>. visualstudio.com*. 
- **Kód használata kezelheti:** kiválasztása  **_Git_**.
- **Projektnév:** Enter *GroupCommon*. 
- **Munkahelyi használatával rendszerezheti:** válasszon *Agile*.
- **A projektek, a gazdagép:** válasszon egy földrajzi helyet. Ebben a példában lehetőséget választjuk *USA déli középső Régiójában*. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Ha látja az alábbi felugró ablak kattintás után **hozzon létre új fiókot**, akkor kattintson a **változás részletei** részletezett az összes mező megjelenítéséhez.

![5](./media/group-manager-tasks/create-account-2.png)


Kattintson a **Folytatás** gombra. 

## <a name="2-groupcommon-project"></a>2. GroupCommon projekt

A **GroupCommon** lap (*https://\<servername\>.visualstudio.com/GroupCommon*) az Azure DevOps-szolgáltatásokkal létrehozása után megnyílik.
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Az (R2) GroupUtilities tárház létrehozása

Hozhat létre a **GroupUtilities** alatt az Azure DevOps-szolgáltatásokkal (R2) tárházat:

- Megnyitásához a **hozzon létre egy új adattár** varázslóban kattintson a **új adattár** a a **verziókövetés** fülre a projekt. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Válassza ki *Git* , a **típusa**, és adja meg *GroupUtilities* , a **neve**, és kattintson a **létrehozás**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Most meg kell jelennie a két Git-tárházak **GroupProjectTemplate** és **GroupUtilities** a bal oldali oszlopban, a **verziókövetés** oldalon: 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. A GroupProjectTemplate (R1) tárház létrehozása

A telepítő az Azure DevOps-csoport kiszolgáló-adattárak két tevékenységet tartalmaz:

- Nevezze át az alapértelmezett **GroupCommon** tárház***GroupProjectTemplate***.
- Hozzon létre a **GroupUtilities** tárházat az Azure DevOps-szolgáltatásokkal projekt keretében a **GroupCommon**. 

Elnevezési konvenciók megjegyzések vagy az adattárak és könyvtárak után ez a szakasz az első feladatra vonatkozó utasításokat tartalmazza. A következő szakasz 4. lépésnél a második feladat vonatkozó utasításokat tartalmazza.

### <a name="rename-the-default-groupcommon-repository"></a>Nevezze át az alapértelmezett GroupCommon tárház

Az alapértelmezett átnevezése **GroupCommon** tárház, *GroupProjectTemplate* (nevezett **R1** ebben az oktatóanyagban):
    
- Kattintson a **másokkal együttműködve a kódra** a a **GroupCommon** projekt oldal. Ekkor megjelenik az alapértelmezett Git-tárház lap a projekt **GroupCommon**. A Git-tárház jelenleg üres. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Kattintson a **GroupCommon** a bal felső sarokban (egy piros be az alábbi ábrán kiemelt) a Git adattárban oldalán **GroupCommon** válassza **tárházakkezelése**(és a egy zöld mezőt az alábbi ábrán kiemelt). Ez az eljárás kimenetei a **VEZÉRLŐPULT**. 
- Válassza ki a **verziókövetés** a projekt fülre. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Kattintson a **...**  jobb oldalán a **GroupCommon** tárházat a bal oldali panelen, és válassza a **átnevezése tárház**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- Az a **nevezze át a GroupCommon tárház** varázslót, amely a POP, adja meg *GroupProjectTemplate* a a **adattár neve** mezőbe, majd kattintson a **átnevezése** . 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-azure-devops-services"></a>5. Az Azure DevOps-szolgáltatásoknak R1 & R2 tárházak ültet be

Ebben a szakaszban az eljárás rendezze a *GroupProjectTemplate* (R1) és *GroupUtilities* (R2)-tárházakat, amelyek az előző szakaszban állítsa be. Az alábbi két adattár a rendszer áttöltésekor a ***ProjectTemplate*** (**G1**) és ***segédprogramok*** (**G2**) által kezelt tárházak A Microsoft számára a csoportos adatelemzési folyamat. Ha az a beültetés befejeződött:

- a R1 tárház ugyanazokat a könyvtárak és dokumentum-sablonok, amely a G1 lesz
- az R2 tárházban fogja a Microsoft által kifejlesztett data science segédprogramok készletét tartalmazzák.

A kiindulási eljárás a könyvtárak a helyi dsvm-hez a köztes átmeneti tárolási helyként használja. Az alábbiakban az ebben a szakaszban leírt lépéseket:

- A G1 & G2 - klónozni LG1 & LG2 ->
- R1 & R2 – klónozni LR1 & LR2 ->
- LG1 & LG2 - fájlokat átmásolhatja a LR1 & LR2 ->
- (Nem kötelező) LR1 & LR2 testreszabása
- LR1 & LR2 - tartalmak hozzáadása R1 & R2 ->


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Klónozza a helyi dsvm-hez G1 & G2 tárhelyek

Ebben a lépésben klónozza a csoportos adatelemzési folyamat (TDSP) ProjectTemplate tárházat (G1) és a parancssori segédeszközöket (G2) a TDSP github-adattárak mappák a LG1 és LG2 a helyi dsvm-hez:

- Hozzon létre egy könyvtárat a gyökérkönyvtár, az adattárak összes klónok üzemeltetésére szolgál. 
    -  A Windows dsvm-hez, hozzon létre egy könyvtárat *C:\GitRepos\TDSPCommon*. 
    -  A Linux-DSVM, hozzon létre egy könyvtárat *GitRepos\TDSPCommon* a kezdőkönyvtárban. 

- Futtassa a következő parancsokat a *GitRepos\TDSPCommon* könyvtár.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- A tárház rövidített nevét használja, ez az mi ezek a szkriptek el: 
    - -> LG1 G1 - be klónozása
    - -> LG2 G2 - be klónozása
- A Klónozás befejeződése után láthatja a két címtár kell _ProjectTemplate_ és _segédprogramok_alatt **GitRepos\TDSPCommon** könyvtár. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Klónozza a helyi dsvm-hez R1 & R2 tárhelyek

Ebben a lépésben klónozza a GroupProjectTemplate tárház (R1) és a GroupUtilities tárház (R2) a helyi címtárak (nevezett LR1 és LR2, illetve) alatt **GitRepos\GroupCommon** a a dsvm-hez.

- R1 és R2 adattárak az URL-címeket, keresse fel a **GroupCommon** kezdőlap meg az Azure DevOps-szolgáltatásokkal. Ez az URL-cím általában rendelkezik *https://\<Services neve az Azure DevOps\>.visualstudio.com/GroupCommon*. 
- Kattintson a **kód**. 
- Válassza ki a **GroupProjectTemplate** és **GroupUtilities** tárházakban. Másolja ki és mentse az URL-címeket (HTTPS for Windows; mindegyike Az SSH Linux rendszeren) az a **Clone URL-cím** eleme, kapcsolja be az alábbi parancsfájlok használható:  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Módosítsa a **GitRepos\GroupCommon** a Windows vagy Linux-DSVM és parancsok R1 és R2 klónozásához abba a könyvtárba, a következő eljárások futtatása egy könyvtárába.
        
A Windows és Linux-parancsprogramok a következők:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] Figyelmeztető üzeneteket kaphat, hogy LR1 és LR2 üres.    

- A tárház rövidített nevét használja, ez az mi ezek a szkriptek el: 
    - R1 - be klónozott LR1 ->
    - R2 - be klónozott LR2 ->   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Rendezze a GroupProjectTemplate (LR1) és GroupUtilities (LR2)

Ezután a helyi gépen átmásolni a tartalmat ProjectTemplate és segédprogramok könyvtár (kivéve a .git könyvtárak metaadatai) alatt GitRepos\TDSPCommon a GroupProjectTemplate és GroupUtilities könyvtárakat **GitRepos\ GroupCommon**. Az alábbiakban található ebben a lépésben két feladatot:

- Másolja a fájlokat a GitRepos\TDSPCommon\ProjectTemplate (**LG1**) való GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Másolja a fájlokat a GitRepos\TDSPCommon\Utilities (**LG2** való GitRepos\GroupCommon\Utilities (**LR2**). 

Ez a két feladat eléréséhez futtassa az alábbi parancsfájlok a PowerShell-konzol (Windows) vagy Shell script-konzol (Linux). Adjon meg teljes elérési útjára LG1, LR1, LG2 és LR2 kéri. Ellenőrzi a elérési utakat, adjon meg. Ha egy nem létező könyvtárat adjon meg, a rendszer megkéri, adja meg újra. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Most láthatja, hogy könyvtárak LG1 és LG1 (kivéve a .git könyvtárban található fájlok) lévő fájlok erőforrástármegosztásba megtörtént-e LR1 és LR2, jelölik.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
Most láthatja, hogy a két mappa (kivéve a .git könyvtárban található fájlok) a rendszer átmásolja a fájlokat GroupProjectTemplate és GroupUtilities jelölik.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- A tárház rövidített nevét használja, ez az mi ezek a szkriptek el:
    - LG1 – a fájlokat átmásolhatja LR1 ->
    - LG2 – a fájlokat átmásolhatja LR2 ->

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>A beállítás LR1 & LR2 tartalmának testreszabása
    
Ha testre szeretné szabni a LR1 és LR2 konkrét igényeinek a csoport tartalmát, akkor az eljárást, amennyiben ez megfelelő szakaszában. Módosítsa a sablon dokumentumok, módosítsa a könyvtárstruktúra, és adja hozzá a meglévő segédprogramok, amely a csoport fejlesztett ki, vagy, amelyek hasznosak a teljes csoport. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Adja hozzá a tartalom LR1 & LR2 R1 & R2 csoport kiszolgálón

Most hozzá kell a tartalmát a LR1 és LR2 tárházakba R1 és R2. Az alábbiakban a git bash-parancsok futtatása a Windows PowerShell vagy a Linux. 

Futtassa az alábbi parancsokat a GitRepos\GroupCommon\GroupProjectTemplate könyvtárból:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

Az -m beállítás lehetővé teszi a git commit üzenet beállítása.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Láthatja, hogy az a csoport az Azure DevOps-szolgáltatásokkal, a GroupProjectTemplate tárházban, a fájlok szinkronizálva lesznek azonnal.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Végül módosítsa az **GitRepos\GroupCommon\GroupUtilities** könyvtárba, és futtassa ugyanazokat a git bash-parancsokat:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Ha ez az első alkalommal egy Git-tárház véglegesítése után, szeretné-e a globális paraméterek konfigurálása *user.name* és *user.email* futtatása előtt a `git commit` parancsot. Futtassa az alábbi két parancsot:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Ha Ön a véglegesítést használatával több Git-tárházakat, használja ugyanazt a nevet és e-mail cím Ha hozzájuk véglegesítése. Ugyanazt a nevet és e-mail-cím használatával bizonyul kényelmes később több tárházakon a Git tevékenységek nyomon követésére a Power bi-irányítópultok készítése során.


- A tárház rövidített nevét használja, ez az mi ezek a szkriptek el:
    - LR1 - tartalom-> R1 hozzáadása
    - LR2 - tartalom hozzáadása az R2 ->

## <a name="6-add-group-members-to-the-group-server"></a>6. Adja hozzá a csoport tagjai a csoport-kiszolgálóhoz

A csoport az Azure DevOps szolgáltatás kezdőlapján kattintson a **fogaskerék ikont** mellett a jobb felső sarokban a felhasználónevére, majd válassza ki a **biztonsági** fülre. A csoport itt különböző engedélyekkel rendelkező tagokat adhat hozzá.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>További lépések

Az alábbiakban a hivatkozások a szerepkörök és feladatok határozzák meg a csoportos adatelemzési folyamat részletes ismertetését:

- [Adatelemzési csapatával csoport Manager-feladatok](group-manager-tasks.md)
- [Csoportos adatelemzési csapatával az érdeklődő feladatok](team-lead-tasks.md)
- [Érdeklődő tevékenységeket az adatelemzési csapatával](project-lead-tasks.md)
- [Projektben egyes közreműködők az adatelemzési csapatával](project-ic-tasks.md)