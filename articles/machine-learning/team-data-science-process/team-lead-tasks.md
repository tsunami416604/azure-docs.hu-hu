---
title: A csapatvezető feladatai a csapatadat-elemzési folyamatcsapatában
description: A csapatadat-elemzési folyamat csapatának feladatainak részletes forgatókönyve
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d099d7c233c3f4b5e65bfdb7d4b875a0e4098499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864281"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>A csapatvezető feladatai egy csapatadat-elemzési folyamat csapatában

Ez a cikk ismerteti azokat a feladatokat, amelyeket egy *csoport érdeklődő* jeget az adatelemzési csapat. A csapatvezető célja egy együttműködési csapatkörnyezet létrehozása, amely szabványosítja a [csapatadat-elemzési folyamatot](overview.md) (TDSP). A TDSP célja az együttműködés és a csapattanulás javítása. 

A TDSP egy agilis, iteratív adatelemzési módszer a prediktív elemzési megoldások és az intelligens alkalmazások hatékony biztosításához. A folyamat lepárolja a Microsoft és az iparág bevált gyakorlatait és struktúráit.  A cél az adatelemzési kezdeményezések sikeres megvalósítása és az elemzési programok előnyeinek teljes körű kihasználása. A TDSP-n szabványosító adatelemzési csoport személyzeti szerepköreinek és társított feladatainak vázlatát a [Csapatadat-elemzési folyamat szerepkörei és feladatai című témakörben tetszésben tetszésszerint olvassa](roles-tasks.md)el.

A csoportvezető egy olyan csapatot kezel, amely egy vállalat adatelemzési egységének több adattudósából áll. Az adatelemzési egység méretétől és szerkezetétől függően a [csoportvezető](group-manager-tasks.md) és a csoportvezető lehet ugyanaz a személy, vagy átruházhatják feladataikat helyettesítőkre. De maguk a feladatok nem változnak. 

Az alábbi ábra a csapatvezető által a csapatkörnyezet beállításához végzett feladatok munkafolyamatát mutatja be:

![Csapatvezető feladat munkafolyamata](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Hozzon létre egy **csapatprojektet** a csoport szervezetében az Azure DevOps-ban. 
  
1. Nevezze át az alapértelmezett csapattárházat **TeamUtilities névre.**
  
1. Hozzon létre egy új **TeamTemplate** tárházat a csapatprojektben. 
  
1. Importálja a csoport **GroupUtilities** és **GroupProjectTemplate** tárházainak tartalmát a **TeamUtilities** és **TeamTemplate** tárházakba. 
  
1. Állítsa be **a biztonsági vezérlést** a csapattagok hozzáadásával és az engedélyeik konfigurálásával.
  
1. Szükség esetén hozzon létre csapatadatokat és elemzési erőforrásokat:
   - Csapatspecifikus segédprogramok hozzáadása a **TeamUtilities** tárházhoz. 
   - Hozzon létre **Azure-fájltárolást** olyan adateszközök tárolására, amelyek az egész csapat számára hasznosak lehetnek. 
   - Csatlakoztassa az Azure-fájltárolót a **csoportérdeklődők adatelemzési virtuális gépéhez** (DSVM), és adja hozzá az adatelemeket.

A következő oktatóanyag részletesen végighalad a lépéseken.

> [!NOTE] 
> Ez a cikk az Azure DevOps és a DSVM segítségével tdsp-csapatkörnyezetet állít be, mert így valósíthatja meg a TDSP-t a Microsoftnál. Ha a csapat más kódüzemeltetési vagy fejlesztési platformokat használ, a csapatvezető feladatok azonosak, de a befejezésük módja eltérő lehet.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy a [csoportvezető](group-manager-tasks.md)a következő erőforrásokat és engedélyeket állította be:

- Az Azure **DevOps-szervezet** az adategységhez
- **GroupProjectTemplate** és **GroupUtilities** adattárak, amelyek a Microsoft TDSP csapat **ProjectTemplate** és **Utilities** adattárainak tartalmával vannak feltöltve
- A szervezeti fiók engedélyei a csapat projektjeinek és adattárainak létrehozásához

Ahhoz, hogy klónozhassa az adattárakat, és módosíthassa azok tartalmát a helyi számítógépen vagy a DSVM-en, vagy be állíthassa az Azure-fájltárolást, és csatlakoztathassa azt a DSVM-hez, a következőkre van szüksége:

- Azure-előfizetés.
- Git telepítve van a gépre. Ha DSVM-et használ, a Git előre telepítve van. Ellenkező esetben tekintse meg a [Platformok és eszközök függeléket.](platforms-and-tools.md#appendix)
- Ha DSVM-et szeretne használni, az Azure-ban létrehozott és konfigurált Windows vagy Linux DSVM. További információt és utasításokat az [Adatelemzési virtuálisgép dokumentációjában talál.](/azure/machine-learning/data-science-virtual-machine/)
- Windows DSVM esetén a [Git credential manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a számítógépen. A *README.md* fájlban görgessen le a **Letöltés és telepítés** szakaszhoz, és válassza ki a legújabb **telepítőt**. Töltse le az *.exe* telepítőt a telepítő lapról, és futtassa azt. 
- Linuxos DSVM esetén a DSVM-en beállított és az Azure DevOps-ban hozzáadott SSH nyilvános kulcs. További információt és utasításokat a Platformok és eszközök függelék **SSH-kulcs létrehozása** [című szakaszában talál.](platforms-and-tools.md#appendix) 

## <a name="create-a-team-project-and-repositories"></a>Csoportprojekt és adattárak létrehozása

Ebben a szakaszban a következő erőforrásokat hozza létre a csoport Azure DevOps-szervezetében:

- A **MyTeam** projekt az Azure DevOps-ban
- A **TeamTemplate** tárház
- A **TeamUtilities** adattár

Az oktatóanyagban az adattárakhoz és könyvtárakhoz megadott nevek feltételezik, hogy egy külön projektet szeretne létrehozni a saját csapata számára a nagyobb adatelemzési szervezeten belül. A teljes csoport azonban dönthet úgy, hogy a csoportvezető vagy a szervezet rendszergazdája által létrehozott egyetlen projekt alatt dolgozik. Ezután az összes adatelemzési csapat hozzon létre adattárak at ez az egyetlen projekt. Ez a forgatókönyv a következőkre érvényes lehet:
- Egy kis adatelemzési csoport, amely nem rendelkezik több adatelemzési csapat. 
- Egy nagyobb adatelemzési csoport több adatelemzési csapat, amely mindazonáltal szeretné optimalizálni a csapatközi együttműködés tevékenységek, például a csoportszintű sprint tervezés. 

Ha a csapatok úgy döntenek, hogy a csapatspecifikus adattárak at egy csoport projekt, a csapat vezet létre kell hoznia a tárolók nevei, mint a * \<TeamName>sablon* és * \<TeamName>Utilities*. Például: *TeamATemplate* és *TeamAUtilities*. 

Mindenesetre a csapatérdeklődőknek tudatniuk kell a csapattagjaikkal, hogy mely sablon- és segédprogram-adattárakat kell beállítani uk és klónozni. A projektérdeklődőknek követniük kell a [projektvezető feladatokat egy adatelemzési csoport számára,](project-lead-tasks.md) hogy projekttárakat hozzanak létre, akár külön projektek, akár egyetlen projekt keretében. 

### <a name="create-the-myteam-project"></a>A MyTeam projekt létrehozása

Külön projekt létrehozása a csapat számára:

1. A webböngészőben nyissa meg a csoport Azure DevOps-szervezetének kezdőlapját az URL-címen *https:\//\<kiszolgálónév>/\<szervezet neve>*, és válassza az Új **projekt**lehetőséget. 
   
   ![Új projekt kiválasztása](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. A **Projekt létrehozása** párbeszédpanelen írja be a csapat nevét(például *A Csapat*, **projektnév )**, majd válassza a **Speciális**lehetőséget. 
   
1. A **Verziókövetés**csoportban válassza a **Git**lehetőséget, majd a **Munkaelem folyamata csoportban**válassza az **Agilis**lehetőséget. Ezután válassza **a Létrehozás lehetőséget.** 
   
   ![Projekt létrehozása](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Megnyílik a csapatprojekt **összegzése** lap, amelynek URL-címe *https:\//\<kiszolgálónév>/\<szervezet neve>/\<csapatnév>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>A MyTeam alapértelmezett tárházának átnevezése TeamUtilities-re

1. A **MyTeam** projekt **összegzése** lap **Melyik szolgáltatással szeretne kezdeni?** **Repos** 
   
   ![Repók kiválasztása](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. A **MyTeam** repo lapon válassza ki a lap tetején található **MyTeam-tárházat,** majd válassza a Legördülő menü **Tárházak kezelése lehetőséget.** 
   
   ![Tárházak kezelése jelölőnégyzetet](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. A **Projektbeállítások** lapon válassza a **...** elemet a **MyTeam** tárház mellett, majd a **Tárház átnevezése**lehetőséget. 
   
   ![Adattár átnevezése jelölőnégyzet kiválasztása](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. A **MyTeam tárház átnevezése** előugró ablakban írja be a TeamUtilities ( *Csapat)* mezőbe, és válassza **az Átnevezés lehetőséget.** 

### <a name="create-the-teamtemplate-repository"></a>A TeamTemplate tárház létrehozása

1. A **Projekt beállítások** lapján válassza az **Új tárház lehetőséget.** 
   
   ![Új tárház kiválasztása](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Vagy válassza a **Tárházak** lehetőséget a **MyTeam** projekt **összegzése** lap bal oldali navigációs sávján, válasszon ki egy tárházat a lap tetején, majd válassza az **Új tárház** lehetőséget a legördülő menüből.
   
1. Az **Új tárház létrehozása** párbeszédpanelen győződjön meg arról, hogy a **Git** a **Típus**csoportban van kiválasztva. Írja be a *TeamTemplate* értéket **a Tárház neve mezőbe,** és válassza a **Létrehozás lehetőséget.**
   
   ![Tárház létrehozása](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Győződjön meg arról, hogy a két tárház **TeamUtilities** és **TeamTemplate** a projekt beállítások oldalon. 
   
   ![Két csapatadattár](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>A közös csoporttárolók tartalmának importálása

A csoportadattárak feltöltése a csoportkezelő által létrehozott közös tárházak tartalmával:

1. A **MyTeam** projekt kezdőlapján válassza a **Repók lehetőséget** a bal oldali navigációs sávon. Ha olyan üzenetet kap, hogy a **MyTeam** sablon nem található, jelölje ki a hivatkozást az **Egyébként területen, és keresse meg az alapértelmezett TeamTemplate tárházat.** 
   
   Megnyílik az alapértelmezett **TeamTemplate** tárház. 
   
1. A **TeamTemplate üres** lapon válassza az **Importálás**lehetőséget. 
   
   ![Importálás kiválasztása](./media/team-lead-tasks/import-repo.png)
   
1. A **Git-tárház importálása** párbeszédpanelen válassza a **Git** **forrástípust,** és írja be a csoport közös sablontárának URL-címét a **Klónozó URL csoportban.** Az URL-cím *\//\<https:\<kiszolgálónév>/\<szervezet neve>/_git/ tárház neve>*. Például: *\/https: /dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Kattintson az **Importálás** gombra. A csoportsablon-tárház tartalma a csoportsablon-tárházba kerül. 
   
   ![Importálási csoport közös sablontárja](./media/team-lead-tasks/import-repo-2.png)
   
1. A projekt **Repos** oldalának tetején legördülő menüben válassza ki a **TeamUtilities** tárházat.
   
1. Ismételje meg az importálási folyamatot a csoport közös segédprogram-tárházának ( például *GroupUtilities*) tartalmának a **TeamUtilities** tárházba történő importálásához. 
   
A két csapattármost már tartalmazza a fájlokat a megfelelő csoport közös tárház. 

### <a name="customize-the-contents-of-the-team-repositories"></a>A csoportadattárak tartalmának testreszabása

Ha a csapatadattárak tartalmát a csapat egyedi igényeinek megfelelően szeretné testreszabni, most megteheti. Módosíthatja a fájlokat, módosíthatja a könyvtárszerkezetet, vagy hozzáadhat fájlokat és mappákat.

Fájlok vagy mappák közvetlenül az Azure DevOps-ban történő módosítása, feltöltése vagy létrehozása:

1. A **MyTeam** projekt **összegzése** lapon válassza a **Repos**lehetőséget. 
   
1. A lap tetején válassza ki a testre szabni kívánt tárházat.

1. A tártárstruktúrában keresse meg a módosítani kívánt mappát vagy fájlt. 
   
   - Új mappák vagy fájlok létrehozásához jelölje ki az **Új**gomb melletti nyilat. 
     
     ![Új fájl létrehozása](./media/team-lead-tasks/new-file.png)
     
   - Fájlok feltöltéséhez válassza **a Fájl(ok) feltöltése lehetőséget.** 
     
     ![Fájlok feltöltése](./media/team-lead-tasks/upload-files.png)
     
   - Meglévő fájlok szerkesztéséhez keresse meg a fájlt, és válassza a **Szerkesztés**lehetőséget. 
     
     ![Fájl szerkesztése](./media/team-lead-tasks/edit-file.png)
     
1. A fájlok hozzáadása vagy szerkesztése után válassza a **Véglegesítés**lehetőséget.
   
   ![Módosítások véglegesítése](./media/team-lead-tasks/commit.png)

A helyi számítógépen vagy a DSVM-en lévő adattárakhoz először másolja vagy *klónozza* az adattárakat a helyi számítógépre, majd véglegesítse és tolja a módosításokat a megosztott csapattárolókba, 

Klónozó tárolókhoz:

1. A **MyTeam** projekt **összegzése** lapon válassza a **Repók**lehetőséget, és a lap tetején válassza ki a klónozni kívánt tárházat.
   
1. A tárház lapon válassza a **Klónozás** lehetőséget a jobb felső sarokban.
   
1. A **Klónozótár** párbeszédpanel **Parancssor**területén válassza a **HTTPS lehetőséget** egy HTTP-kapcsolathoz, vagy **az SSH-kapcsolatot SSH-kapcsolathoz,** és másolja a klón URL-címét a vágólapra.
   
   ![Klón URL-címének másolása](./media/team-lead-tasks/clone.png)
   
1. A helyi számítógépen hozza létre a következő könyvtárakat:
   
   - Windows rendszerre: **C:\GitRepos\MyTeam**
   - **Linuxra, $home/GitRepos/MyTeam** 
   
1. Váltás a létrehozott könyvtárra.
   
1. A Git Bash alkalmazásban `git clone <clone URL>`futtassa a parancsot, ahol a klón URL-cím> a \< **Klónozás** párbeszédpanelről másolt URL.In Git Bash, run the command , where clone URL> is the URL you copied from the Clone dialog.
   
   Például az alábbi parancsok egyikével klónozhatja a **TeamUtilities** tárházat a helyi számítógép *MyTeam* könyvtárába. 
   
   **HTTPS-kapcsolat:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH kapcsolat:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Miután elvégzett bármilyen módosítást a tárház helyi klónjában, véglegesítse és lenyomja a módosításokat a megosztott csapattárházakba. 

Futtassa a következő Git Bash parancsokat a helyi **GitRepos\MyTeam\TeamTemplate** vagy **GitRepos\MyTeam\TeamUtilities** könyvtárból.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Ha ez az első alkalom, hogy véglegesíti a Git-tárházban, előfordulhat, hogy a `git commit` parancs futtatása előtt be kell állítania a globális paramétereket *user.name* és a *user.email* fájlt. Futtassa a következő két parancsot:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Ha több Git-tárházban is elkötelezi magát, használja ugyanazt a nevet és e-mail címet mindegyikhez. Ugyanazzal a névvel és e-mail címmel kényelmes, ha Power BI-irányítópultokat hoz létre a Git-tevékenységek nyomon követéséhez több tárházban.

## <a name="add-team-members-and-configure-permissions"></a>Csapattagok hozzáadása és engedélyek konfigurálása

Tagok felvétele a csapatba:

1. Az Azure DevOps-ban a **MyTeam** projekt kezdőlapján válassza a **Projekt beállításai t** a bal oldali navigációs sávon. 
   
1. A **Projektbeállítások** bal oldali navigációs sávon válassza a **Csapatok**lehetőséget, majd a **Csapatok** lapon válassza a **Sajátcsapat-csapat lehetőséget.** 
   
   ![Csapatok konfigurálása](./media/team-lead-tasks/teams.png)
   
1. A **Csapatprofil** lapon válassza a **Hozzáadás gombot.**
   
   ![Hozzáadás a MyTeam csapathoz](./media/team-lead-tasks/add-to-team.png)
   
1. A **Felhasználók és csoportok hozzáadása** párbeszédpanelen keresse meg és jelölje ki a csoporthoz hozzáadni kívánt tagokat, majd válassza a **Módosítások mentése lehetőséget.** 
   
   ![Felhasználók és csoportok hozzáadása](./media/team-lead-tasks/add-users.png)
   

A csapattagok engedélyeinek konfigurálása:

1. A **Projekt beállításai** navigációs sávon válassza az **Engedélyek**lehetőséget. 
   
1. Az **Engedélyek** lapon jelölje ki azt a csoportot, amelyhez tagokat szeretne hozzáadni. 
   
1. A csoport lapján válassza a **Tagok**lehetőséget, majd a **Hozzáadás**lehetőséget. 
   
1. A **Tagok meghívása** előugró ablakban keresse meg és jelölje ki a csoporthoz felvenni kívánt tagokat, majd válassza a **Mentés gombot.** 
   
   ![Engedélyek megadása a tagoknak](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Csapatadatok és elemzési erőforrások létrehozása

Ez a lépés nem kötelező, de az adatok és elemzési erőforrások megosztása a teljes csapattal rendelkezik a teljesítmény és a költségelőnyök. A csapattagok a megosztott erőforrásokon hajthatják végre projektjeiket, takarékoskodhatnak a költségvetéssel, és hatékonyabban működhetnek együtt. Létrehozhat azure-beli fájltárolást, és csatlakoztathatja a DSVM-hez, hogy megoszthassa a csapattagokkal. 

Ha további erőforrásokat szeretne megosztani a csapatával, például az Azure HDInsight Spark-fürtökről, a [Platformok és eszközök](platforms-and-tools.md)című témakörben talál további információt. Ez a témakör adatelemzési szempontból nyújt útmutatást az igényeinek megfelelő erőforrások kiválasztásához, valamint a termékoldalakra és más releváns és hasznos oktatóanyagokra mutató hivatkozásokhoz.

>[!NOTE]
> Az adatközpontok közötti adatátvitel elkerülése érdekében, amely lassú és költséges lehet, győződjön meg arról, hogy az Azure-erőforráscsoport, a tárfiók és a DSVM ugyanabban az Azure-régióban található. 

### <a name="create-azure-file-storage"></a>Azure-fájltárolás létrehozása

1. Futtassa a következő parancsfájlt az Azure-fájltárolás létrehozásához a teljes csapat számára hasznos adateszközök számára. A parancsfájl kéri, hogy az Azure-előfizetési adatokat, így már, hogy készen áll a belépésre. 

   - Windows rendszerű számítógépen futtassa a parancsfájlt a PowerShell parancssorából:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Linux-gépen futtassa a parancsfájlt a Linux rendszerhéjból:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Jelentkezzen be a Microsoft Azure-fiókjába, amikor a rendszer kéri, és válassza ki a használni kívánt előfizetést.
   
1. Válassza ki a használni kívánt tárfiókot, vagy hozzon létre egy újat a kiválasztott előfizetés alatt. Az Azure-fájltároló nevéhez kisbetűket, számokat és kötőjeleket használhat.
   
1. A tárhely csatlakoztatásának és megosztásának megkönnyítése érdekében nyomja le az Enter billentyűt, vagy írja be az *Y* billentyűt az Azure-fájltárolási adatok nak az aktuális címtárban lévő szöveges fájlba való mentéséhez. Ezt a szövegfájlt beviheti a **TeamTemplate** tárházba, ideális esetben a **Docs\DataDictionaries**mappában, így a csoport minden projektje hozzáférhet. A következő szakaszban is szüksége van a fájladatokra az Azure-fájltárolás csatlakoztatásához az Azure DSVM-hez. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Az Azure-fájltárolás csatlakoztatása a helyi számítógépen vagy a DSVM-en

1. Az Azure-fájltároló helyi számítógéphez vagy DSVM-hez való csatlakoztatásához használja a következő parancsfájlt.
   
   - Windows rendszerű számítógépen futtassa a parancsfájlt a PowerShell parancssorából:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Linux-gépen futtassa a parancsfájlt a Linux rendszerhéjból:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Ha az előző lépésben mentett egy Azure-fájltárolási adatfájlt, nyomja le az Enter billentyűt, vagy írja be az *Y* billentyűt. Adja meg a létrehozott fájl teljes elérési útját és nevét. 
   
   Ha nem rendelkezik Azure-fájltárolási adatfájllal, írja be *az n*értéket, és kövesse az utasításokat az előfizetés, az Azure storage-fiók és az Azure-fájltárolási adatok megadásához.
   
1. Adja meg a fájlmegosztás csatlakoztatásához helyi vagy TDSP-meghajtó nevét. A képernyőn megjelenik a meglévő meghajtónevek listája. Adjon meg egy olyan meghajtónevet, amely még nem létezik.
   
1. Ellenőrizze, hogy az új meghajtó és tároló sikeresen csatlakoztatva van-e a készülékhez.

## <a name="next-steps"></a>További lépések

Az alábbiakban a csapatadat-elemzési folyamat által meghatározott egyéb szerepkörök és feladatok részletes leírására mutató hivatkozásokat olvashat:

- [Csoportkezelői feladatok adatelemzési csoportszámára](group-manager-tasks.md)
- [Projektvezető-tevékenységek adatelemzési csoportszámára](project-lead-tasks.md)
- [Egyéni közreműködői feladatok projektje adatelemzési csoport számára](project-ic-tasks.md)
