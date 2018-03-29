---
title: Team tudományos folyamat csoport adatkezelő feladatok – Azure |} Microsoft Docs
description: A csoport kezelőjének a tudományos adatok team projekt kapcsolatos feladatokat Vázlat.
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
ms.author: bradsev
ms.openlocfilehash: 3c39ae865ec26567f70706b042dab2aa8e4939d3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="group-manager-tasks"></a>Csoport kezelőjének feladatok

Ez a témakör vázol fel, a csoport kezelőjének várhatóan feladatok elvégzését előzmény / saját adatok tudományos szervezet. Hoz létre, amely egységesíti az együttműködési csoport környezetet a [Team adatok tudományos folyamat](overview.md) (TDSP). A személyzet szerepkörök és a kapcsolódó feladatok, egy data tudományos csapat által kezelt vázlat szabványosítása a folyamattal, lásd: [Team adatok tudományos folyamat szerepköröket és feladatokat](roles-tasks.md).

A **csoport kezelőjének** a teljes tudományos adategység vállalati felettese. Egy adategység tudományos rendelkezhet több csapat dolgozik, amelyek mindegyike különböző üzleti referenciaegyenesen az adatok tudományos több projektet. A csoport kezelőjének delegálhat egy helyettesítő a feladatok, de a feladatokat a szerepkörhöz társított változatlan marad. Nincsenek hat fő feladatok a következő ábrán látható módon:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] A Microsoft helyzeteit vázolják fel, az alábbi utasításokat a VSTS alkalmazásával TDSP csoport környezet beállítása szükséges lépéseket. Azt adja meg, hogyan hajthat végre ezeket a feladatokat a VSTS, mivel az a Microsoft TDSP megvalósítása módját. Egy másik kódot platform üzemeltető csoport használata esetén a feladatokat, amelyeket teljesíteni a csoport kezelőjének általában ne módosítsa. De ezek a feladatok úgy a különböző lesz.

1. Állítson be **Visual Studio Team Services (VSTS) kiszolgáló** a csoporthoz.
2. Hozzon létre egy **csoport csapatprojekt** a Visual Studio Team Services-kiszolgálón (VSTS számára)
3. Hozzon létre a **GroupProjectTemplate** tárház
4. Hozzon létre a **GroupUtilities** tárház
5. Kezdőérték a **GroupProjectTemplate** és **GroupUtilities** tárolóhelyekkel a tartalmat a TDSP tárházak VSTS-kiszolgálót.
6. Állítsa be a **biztonsági vezérlők** csoport tagjai a GroupProjectTemplate és GroupUtilities tárolóhelyekkel való hozzáférést.

A az előző lépések részletes leírása. Azonban először azt megismerkedhet a rövidítések a és tárolóhelyekkel való munkához szükséges előfeltételek ismertetik.

### <a name="abbreviations-for-repositories-and-directories"></a>Rövidítések adattárak és könyvtárak

Ez az oktatóanyag adattárak és könyvtárak rövidített nevét használja. Ezek a definíciók könnyebben hajtsa végre a műveletek a tárolóhelyekkel és könyvtárak között. Ez a notation szolgál a következő szakaszokban:

- **A G1**: A projekt sablon tárház fejlett és TDSP csapat a Microsoft felügyeli.
- **G2**: A segédprogramok tárház fejlett és TDSP csapat a Microsoft által felügyelt.
- **R1**: Git a GroupProjectTemplate tárházából beállította a VSTS csoport kiszolgálón.
- **R2**: Git a GroupUtilities tárházából beállította a VSTS csoport kiszolgálón.
- **LG1** és **LG2**: A helyi könyvtárak klónozni rendre G1 és G2, a számítógépen.
- **LR1** és **LR2**: A helyi könyvtárak klónozni rendre R1 és R2-ről, a számítógépen.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Szükséges előfeltételek adattárak klónozásának és a kód bejövő és kimenő adatforgalma ellenőrzése
 
- Git telepítenie kell a számítógépre. Ha egy tudományos virtuális gép (DSVM) használ, Git előre telepítve van, és visszaigazolásához. Ellenkező esetben tekintse meg a [platformok és az eszközök függelék](platforms-and-tools.md#appendix).  
- Ha használ egy **Windows DSVM**, telepíteni kell [Git hitelesítőadat-kezelő (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) van telepítve a számítógépre. A README.md fájl görgessen le a **töltse le és telepítse** szakaszt, és kattintson a *installer legújabb*. Ebben a lépésben megnyitná a legfrissebb telepítési oldal. Töltse le a .exe-telepítő címről, és futtassa. 
- Ha használ **Linux DSVM**, a DSVM a nyilvános SSH-kulcs létrehozása, és adja hozzá a csoporthoz VSTS-kiszolgáló. SSH kapcsolatos további információkért tekintse meg a **létrehozása SSH nyilvános kulcs** szakasz a [platformok és az eszközök függelék](platforms-and-tools.md#appendix). 


## <a name="1-create-account-on-vsts-server"></a>1. Fiók létrehozása a VSTS-kiszolgálón

A VSTS-kiszolgálón a következő tárházak találhatók:

- **közös adattárak csoport**: általános célú tárházak találhatók, amely több adat tudományos projektek csoporton belül több csapat fogadja el. Például a *GroupProjectTemplate* és *GroupUtilities* tárházak találhatók.
- **vonja össze az adattárak**: tárolóhelyekkel adott csapatoknak csoporton belül. A tárolóhelyekkel egy csapat igényeknek, és lehet több csapat által végrehajtott projekt által elfogadott, de nem általános elég tudományos adatok csoporton belül több csapat számára hasznos lehet. 
- **a projekt adattárak**: adattárak adott projektekhez érhető el. Ilyen tárházak nem lehet általános elég hasznosnak, több csapat által elvégzett projektet, és több csapat tudományos csoportokban.


### <a name="setting-up-the-vsts-server-sign-into-your-microsoft-account"></a>A VSTS kiszolgálón jelentkezzen be a Microsoft-fiók beállítása
    
Ugrás a [Visual Studio online-hoz](https://www.visualstudio.com/), kattintson a **jelentkezzen be a** a jobb felső sarokban, és jelentkezzen be a Microsoft-fiókjával. 
    
![1](./media/group-manager-tasks/login.PNG)

Ha nincs Microsoft-fiókot, kattintson a **feliratkozás most** gombra a Microsoft-fiók létrehozása, majd jelentkezzen be a fiókjával. 

Ha a szervezete egy Visual Studio/MSDN-előfizetés, kattintson a zöld **jelentkezzen be munkahelyi vagy iskolai fiókjával** mezőbe, és jelentkezzen be az ehhez az előfizetéshez társított hitelesítő adatokat. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
Miután bejelentkezik, kattintson a **új fiók létrehozása** a jobb felső sarkában található az alábbi ábrán látható:
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Töltse ki a létrehozni kívánt VSTS-kiszolgáló az információkat a **létrehozni a fiókját** varázsló a következő értékekkel: 

- **URL-címe**: cserélje le *mysamplegroup* a saját *kiszolgálónév*. A kiszolgáló az URL-cím lesz: *https://\<kiszolgálónév\>. visualstudio.com webhelyre*. 
- **Kezelheti a kód használatával:** válasszon  **_Git_**.
- **Projektnév:** Enter *GroupCommon*. 
- **Munkahelyi használatával rendezése:** válasszon *Agile*.
- **A projektek üzemeltetésére:** földrajzi helyét. Ebben a példában választjuk *déli középső Régiójában*. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Ha kattintás után a következő megjelenő ablakban látható **hozzon létre új fiókot**, majd kattintson **adatainak módosítása** részletezett összes mezők megjelenítése.

![5](./media/group-manager-tasks/create-account-2.png)


Kattintson a **továbbra is**. 

## <a name="2-groupcommon-team-project"></a>2. GroupCommon Csapatprojekt

A **GroupCommon** lap (*https://\<kiszolgálónév\>.visualstudio.com/GroupCommon*) megnyitja a VSTS-kiszolgáló létrehozása után.
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Az (R2) GroupUtilities tárház létrehozása

Létrehozásához a **GroupUtilities** VSTS-kiszolgáló (R2)-tárház:

- Megnyitásához a **hozzon létre egy új tárház** varázsló, kattintson a **új tárház** a a **verziókezelést** a csapatprojekt lapján. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Válassza ki *Git* , a **típus**, és írja be *GroupUtilities* , a **neve**, és kattintson a **létrehozása**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Most látnia kell a két Git adattárak **GroupProjectTemplate** és **GroupUtilities** a bal oldali oszlopban található a **verziókezelést** oldalon: 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. A GroupProjectTemplate (R1) tárház létrehozása

A telepítő a VSTS-csoport kiszolgáló adattárak két tevékenységből áll:

- Nevezze át az alapértelmezett **GroupCommon** tárház***GroupProjectTemplate***.
- Hozzon létre a **GroupUtilities** a VSTS-kiszolgálón a csapatprojekt tárház **GroupCommon**. 

Az első feladat vonatkozó utasításokat ebben a szakaszban után elnevezési konvenciókat megjegyzések vagy a tárolóhelyekkel és a könyvtárak tartalmazza. A következő szakasz 4. lépés második feladata vonatkozó utasításokat tartalmazza.

### <a name="rename-the-default-groupcommon-repository"></a>Az alapértelmezett GroupCommon tárház átnevezése

Az alapértelmezett átnevezése **GroupCommon** , tárház *GroupProjectTemplate* (néven **R1** ebben az oktatóanyagban):
    
- Kattintson a **kódja együttműködés** a a **GroupCommon** csapat projekt lap. Ezzel megnyitná a csapatprojekt alapértelmezett Git tárház oldalán **GroupCommon**. A Git-tárház jelenleg üres. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Kattintson a **GroupCommon** a bal felső sarokban (a vörös téglalappal az alábbi ábrán kiemelt) Git tárház oldalán **GroupCommon** válassza **adattárakkezelése**(egy zöld be az alábbi ábrán kiemelt). Ez az eljárás megjelenik a **VEZÉRLŐPULT**. 
- Válassza ki a **verziókezelést** a csapatprojekt lapján. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Kattintson a **...**  jobb oldalán a **GroupCommon** a bal oldali panelen, majd válassza a tárház **átnevezése tárház**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- Az a **nevezze át a GroupCommon tárház** varázslót, amely a POP, adja meg *GroupProjectTemplate* a a **tárház nevét** gombra, majd **átnevezése** . 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-vsts-server"></a>5. A R1 kezdőérték & R2 tárházak találhatók, a VSTS-kiszolgálón

Ebben a szakaszban az eljárás feltöltheti a *GroupProjectTemplate* (R1) és *GroupUtilities* (R2) tárházak találhatók, amely az előző szakaszban állítsa be. A tárolóhelyekkel rendelkező vannak rendezés a ***ProjectTemplate*** (**G1**) és ***segédprogramok*** (**G2**) által kezelt adattárak A Microsoft az adatok tudományos feldolgozáshoz. Ha a összehangolása befejeződött:

- a R1 tárház ugyanazokat a könyvtárak és dokumentum-sablonok, amelyet a G1 lesz
- az R2 tárház lesz a Microsoft által kifejlesztett adatok tudományos segédprogramok tartalmaz.

Az index-összehangolási eljárás használja a könyvtárak a helyi DSVM a köztes átmeneti helyek. Az alábbiakban az ebben a szakaszban leírt lépéseket:

- A G1 & G2 --> LG1 & LG2 klónozása
- R1 & R2 --> LR1 & LR2 klónozása
- LG1 & LG2 - másolta, a fájl -> LR1 & LR2
- (Választható) LR1 & LR2 testreszabása
- LR1 & LR2 - tartalmak R1 & R2 -> hozzáadása


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>A helyi DSVM G1 & G2 tárhelyek klónozása

Ebben a lépésben klónozását a csapat adatok tudományos folyamat (TDSP) ProjectTemplate tárház (G1) és segédprogramjai (G2) a TDSP github-adattárak mappákhoz az a helyi DSVM LG1 és LG2:

- Hozzon létre egy könyvtárat a gyökérkönyvtár adattárak valamennyi a klónok futtatására szolgál. 
    -  A Windows DSVM hozzon létre egy könyvtárat *C:\GitRepos\TDSPCommon*. 
    -  A Linux DSVM hozzon létre egy könyvtárat *GitRepos\TDSPCommon* a kezdőkönyvtárban. 

- Futtassa a következő parancsokat a *GitRepos\TDSPCommon* könyvtár.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- A rövidített tárház nevét használja, ez az parancsfájlokat értek: 
    - G1 - a klónozott LG1 ->
    - G2 - a klónozott LG2 ->
- A Klónozás befejezése után kell látni a két címtár _ProjectTemplate_ és _segédprogramok_a **GitRepos\TDSPCommon** könyvtár. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>A helyi DSVM R1 & R2 tárhelyek klónozása

Ebben a lépésben klónozza a GroupProjectTemplate tárház (R1) és GroupUtilities tárházat (R2) a helyi könyvtárakat (néven LR1 és LR2, illetve) **GitRepos\GroupCommon** a DSVM meg.

- Az URL-címek R1 és R2 adattárak, keresse fel a **GroupCommon** kezdőlapja a VSTS. Ez általában az URL-cím van *https://\<a VSTS-kiszolgálónév\>.visualstudio.com/GroupCommon*. 
- Kattintson a **kód**. 
- Válassza ki a **GroupProjectTemplate** és **GroupUtilities** tárházak találhatók. Másolja ki és mentse az egyes URL-címek (HTTPS for Windows; SSH Linux rendszeren) az a **klón URL-cím** elem viszont használat esetén a következő:  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Váltson át a **GitRepos\GroupCommon** a Windows vagy Linux DSVM és parancsokat R1 és R2 klónozza a könyvtárba, amely a következő eljárások futtatása egy könyvtárába.
        
Az alábbiakban a Windows és Linux parancsfájlokat:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] Várt LR1 és LR2 üresek figyelmeztető üzeneteket fogadni.    

- A rövidített tárház nevét használja, ez az parancsfájlokat értek: 
    - R1 - a klónozott LR1 ->
    - R2 - be klónozott LR2 ->   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Feltöltheti a GroupProjectTemplate (LR1) és a GroupUtilities (LR2)

Ezt követően a helyi gépen átmásolni a tartalmat (kivéve a metaadatok .git könyvtárak) ProjectTemplate és segédprogramok könyvtárak GitRepos\TDSPCommon alatt a GroupProjectTemplate és GroupUtilities könyvtárakat **GitRepos\ GroupCommon**. Az alábbiakban a két feladatot ezt a lépést:

- Másolja a fájlokat a GitRepos\TDSPCommon\ProjectTemplate (**LG1**) való GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Másolja a fájlokat a GitRepos\TDSPCommon\Utilities (**LG2** való GitRepos\GroupCommon\Utilities (**LR2**). 

Ez a két feladat eléréséhez futtassa az alábbi parancsfájlok PowerShell-konzolon (Windows) vagy a rendszerhéj parancsfájl konzol (Linux). Adjon meg a teljes elérési útjait LG1, LR1, LG2 és LR2 kéri. A megadott elérési utak érvényesíti. Ha a megadott könyvtár nem létezik, a felhasználótól, hogy újra kell adnia. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Most már láthatja, hogy könyvtárak LG1 és LG1 (kivéve a .git könyvtárban található fájlok) a fájlok másolása LR1 és LR2, illetve.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
Most láthatja, hogy a két mappákban (kivéve a .git könyvtárban található fájlok) lévő fájlok kerülnek GroupProjectTemplate és GroupUtilities kulcsattribútumokkal.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- A rövidített tárház nevét használja, ez az parancsfájlokat értek:
    - LG1 - a fájlok átmásolja az LR1 ->
    - LG2 - a fájlok átmásolja az LR2 ->

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>A beállítással LR1 & LR2 tartalmát testreszabása
    
Ha szeretné testre szabni a csoport a konkrét igényeinek megfelelő LR1 és LR2 tartalmát, ez az eljárás, ahol, amely megfelel a szakaszában az. A sablon dokumentumok, módosítsa a könyvtárstruktúra, és adja hozzá a meglévő segédprogramok fejlesztett ki, a csoport vagy a teljes csoport hasznos. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Adja hozzá a tartalom LR1 & LR2 R1 & R2 csoport kiszolgálón

Most kell adja hozzá a tartalom LR1 és LR2 R1 és R2 tárházak találhatók. Az alábbiakban a git bash parancsokat futtathat Windows PowerShell vagy a Linux. 

A következő parancsokat a GitRepos\GroupCommon\GroupProjectTemplate könyvtárból:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

Az -m beállítással egy üzenetet a git commit megadhat.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Láthatja, hogy a csoport VSTS Server, a GroupProjectTemplate tárházban, a fájlok szinkronizált azonnal.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Végül módosítsa a **GitRepos\GroupCommon\GroupUtilities** directory és a Futtatás ugyanazokat a git bash parancsokat:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Ha az első alkalommal véglegesíti a Git-tárházba, szeretné-e globális paraméterek konfigurálása *user.name* és *user.email* futtatása előtt a `git commit` parancsot. Futtassa az alábbi két parancsot:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Ha több Git adattárak a véglegesítés használja ugyanazt a nevet és e-mail cím Ha hozzájuk véglegesíti. Ugyanazzal a névvel és e-mail címmel bizonyítja kényelmes később a Power bi-irányítópultok nyomon követheti a Git tevékenységek több adattárak összeállításakor.


- A rövidített tárház nevét használja, ez az parancsfájlokat értek:
    - LR1 - tartalom hozzáadása R1 ->
    - LR2 - tartalom hozzáadása az R2 ->

## <a name="6-add-group-members-to-the-group-server"></a>6. A csoport kiszolgálói csoport tagjainak hozzáadása

A csoport VSTS server kezdőlapján kattintson a **fogaskerék ikonra** mellett jobb felső sarokban a felhasználónevére, majd válassza ki a **biztonsági** fülre. A csoport itt különböző engedélyekkel tagokat adhat hozzá.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>További lépések

Az alábbiakban a szerepkörök és az adatok tudományos eljárással meghatározott feladatok részletes leírását mutató hivatkozásokat:

- [Tudományos adatok csoport csoport Manager-feladatok](group-manager-tasks.md)
- [Az érdeklődési feladatainak egy adatok tudományos csoport](team-lead-tasks.md)
- [Az érdeklődési tevékenységeket a tudományos adatok csoport](project-lead-tasks.md)
- [Project egyes közreműködők a tudományos adatok csoport](project-ic-tasks.md)