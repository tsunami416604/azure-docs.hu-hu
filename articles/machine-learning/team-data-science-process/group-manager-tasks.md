---
title: Csapatadat-elemzési folyamat csoportkezelői feladatai
description: Kövesse a csoportmenedzser által egy adatelemzési csoportprojekten elvégzett feladatok részletes forgatókönyvét.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ec7f4242e5046e90fdf0eb8c6c0579f402e4f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721353"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Csapatadat-elemzési folyamat csoportkezelői feladatai

Ez a cikk azokat a feladatokat ismerteti, amelyeket egy *csoportkezelő* végez egy adatelemzési szervezetnél. A csoportvezető egy vállalat teljes adatelemzési egységét kezeli. Egy adatelemzési egység több csapat, amelyek mindegyike dolgozik számos adatelemzési projektek különböző üzleti vertikális. A csoportvezető célja egy együttműködési csoportkörnyezet létrehozása, amely szabványosítja a [csapatadat-elemzési folyamat](overview.md) (TDSP). A TDSP-re szabványosító adatelemzési csoport által kezelt személyzeti szerepkörök és kapcsolódó feladatok vázlatát a [Csapatadat-elemzési folyamat szerepkörei és feladatai című témakörben tetszésben tetszésszerint olvassa](roles-tasks.md)el.

Az alábbi ábra a csoportkezelő hat fő beállítási feladatát mutatja be. A csoportvezetők átruházhatják feladataikat helyettesítőkre, de a szerepkörhöz társított feladatok nem változnak.

![Csoportkezelői feladatok](./media/group-manager-tasks/tdsp-group-manager.png)

1. Hozzon létre egy **Azure DevOps-szervezetet** a csoporthoz.
2. Hozza létre az alapértelmezett **GroupCommon projektet** az Azure DevOps-szervezetben.
3. Hozza létre a **GroupProjectTemplate** tárházat az Azure Repos-ban.
4. Hozza létre a **GroupUtilities** tárház az Azure Repos.
5. Importálja a Microsoft TDSP-csapat **ProjectTemplate** and **Utilities** adattárainak tartalmát a közös tárházakba.
6. Állítson be **tagságot** és **engedélyeket** a csoporttagok számára a csoport eléréséhez.

A következő oktatóanyag részletesen végighalad a lépéseken. 

> [!NOTE] 
> Ez a cikk az Azure DevOps segítségével tdsp-csoportkörnyezetet állít be, mert így valósíthatja meg a TDSP-t a Microsoftnál. Ha a csoport más kódüzemeltetési vagy fejlesztési platformokat használ, a csoportkezelő feladatai megegyeznek, de a befejezésük módja eltérő lehet.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Szervezet és projekt létrehozása az Azure DevOps-ban

1. Nyissa meg [visualstudio.microsoft.com,](https://visualstudio.microsoft.com)válassza a Bejelentkezés a jobb felső **sarokban** lehetőséget, és jelentkezzen be A Microsoft-fiókjába. 
   
   ![Bejelentkezés Microsoft-fiókjába](./media/group-manager-tasks/signinvs.png)
   
   Ha nem rendelkezik Microsoft-fiókkal, válassza **a Regisztráció most**lehetőséget, hozzon létre egy Microsoft-fiókot, és jelentkezzen be ezzel a fiókkal. Ha a szervezet rendelkezik Visual Studio-előfizetéssel, jelentkezzen be az adott előfizetés hez szükséges hitelesítő adatokkal.
   
1. Miután bejelentkezett, az Azure DevOps-lap jobb felső részén válassza az **Új szervezet létrehozása lehetőséget.**
   
   ![Új szervezet létrehozása](./media/group-manager-tasks/create-organization.png)
   
1. Ha a rendszer kéri, hogy fogadja el a Szolgáltatási feltételeket, az adatvédelmi nyilatkozatot és a magatartási kódexet, válassza a **Folytatás**gombot.
   
1. A regisztrációs párbeszédpanelen nevezze el az Azure DevOps-szervezetet, és fogadja el a gazdarégió-hozzárendelést, vagy válasszon le egy másik régiót. Ezután válassza a **Folytatás** elemet. 

1. A **Kezdéshez projekt létrehozása**csoportban írja be a *GroupCommon*( Csoport – csoport ) lehetőséget, és válassza **a Projekt létrehozása lehetőséget.** 
   
   ![Projekt létrehozása](./media/group-manager-tasks/create-project.png)

Megnyílik **a GroupCommon** projekt **összegzése** lap. A lap URL-címe *\//\<https:\<kiszolgálónév>/ szervezetnév>/GroupCommon*.

![Projekt összefoglaló lapja](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>A közös tárolók csoport beállítása

Az Azure Repos a következő típusú adattárak at a csoport:

- **Közös adattárak csoportosítása:** Általános célú adattárak, amelyeket egy adatelemzési egységen belül több csapat is elfogadhat számos adatelemzési projekthez. 
- **Csapatadattárak:** Adattárak egy adatelemzési egységen belül adott csapatok számára. Ezek a tárházak egy csapat igényeinek megfelelően vannak, és az adott csoporton belüli több projekthez is használhatók, de nem elég általánosak ahhoz, hogy egy adatelemzési egységen belül több csapaton keresztül használhatók.
- **Projektadattárak**: Konkrét projektek adattárai. Az ilyen adattárak nem lehet elég általános a csoporton belüli több projekt, vagy más csapatok egy adatelemzési egység.

A közös csoporttárolók beállításához tegye a következőket: 
- Az alapértelmezett **GroupCommon** tárház átnevezése **GroupProjectTemplate-re**
- Új **GroupUtilities-tárház** létrehozása

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Az alapértelmezett projekttár átnevezése GroupProjectTemplate-re

Az alapértelmezett **GroupCommon** projekttár átnevezése **GroupProjectTemplate értékre**:

1. A **GroupCommon** projekt **összegzése** lapon válassza **a Repos**lehetőséget. Ez a művelet a GroupCommon projekt alapértelmezett **GroupCommon** tárházához vezet, amely jelenleg üres.
   
1. A lap tetején a GroupCommon melletti nyílra bontsa le a **legördülő** menüt, és válassza **az Adattárak kezelése**lehetőséget.
   
   ![Adattárak kezelése](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. A **Projekt beállítások** lapján válassza a **...** elemet a **GroupCommon**, majd a **Tárház átnevezése**lehetőség mellett. 
   
   ![Válassza ki... majd válassza a Tárház átnevezése lehetőséget](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. A **GroupCommon tárház átnevezése** előugró ablakban írja be a *GroupProjectTemplate*( Csoportsablon ) mezőbe, majd válassza **az Átnevezés lehetőséget.** 
   
   ![Adattár átnevezése](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>A GroupUtilities tárház létrehozása

A **GroupUtilities** tárház létrehozása:

1. A **GroupCommon** projekt **összegzése** lapon válassza **a Repos**lehetőséget. 
   
1. A lap tetején a **GroupProjectTemplate** melletti nyílra bontsa le a mutatót, és válassza az **Új tárház**lehetőséget.
   
   ![Új tárház kiválasztása](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. Az **Új tárház létrehozása** párbeszédpanelen válassza a **Git** **típusként**lehetőséget, írja be a *GroupUtilities* értéket **adattárnévként,** majd válassza a **Create (Létrehozás) lehetőséget.**
   
   ![GroupUtilities-tárház létrehozása](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. A **Projektbeállítások** lapon válassza a Bal oldali navigációs sáv **Adattárak** csoportjában a **Tárházak** lehetőséget a csoport két tárházának megtekintéséhez: **GroupProjectTemplate** és **GroupUtilities**.
   
   ![Két csoport adattárak](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>A Microsoft TDSP-csoportadattárak importálása

Az oktatóanyag ezen részében importálja a Microsoft TDSP-csapat által kezelt **ProjectTemplate** and **Utilities** adattárak tartalmát a **GroupProjectTemplate** és **GroupUtilities** adattárakba. 

A TDSP-csapattárházak importálása:

1. A **GroupCommon** projekt kezdőlapján válassza a **Repók** lehetőséget a bal oldali navigációs sávon. Megnyílik az alapértelmezett **GroupProjectTemplate** tártár. 
   
1. A **GroupProjectTemplate üres** lapon válassza az **Importálás**lehetőséget. 
   
   ![Importálás kiválasztása](./media/group-manager-tasks/import-repo.png)
   
1. A **Git-tárház importálása** párbeszédpanelen válassza a **Git** **forrástípust,** majd írja be a *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* parancsot a **klónozó URL-címhez.** Ezután válassza **az Importálás**lehetőséget. A Microsoft TDSP csapat ProjectTemplate tárházának tartalma a GroupProjectTemplate tárházba kerül. 
   
   ![Microsoft TDSP csapattár importálása](./media/group-manager-tasks/import-repo-2.png)
   
1. A **Repos** oldal tetején legördülő menüben válassza ki a **GroupUtilities** adattárat.
   
1. Ismételje meg az importálási folyamatot a Microsoft TDSP team **Utilities** adattár *tartalmának\/importálásához https: /github.com/Azure/Azure-TDSP-Utilities.git*, a **GroupUtilities** tárházba. 
   
A két csoporttár mostmár tartalmazza az összes fájlt, kivéve a *.git* könyvtárban lévőket, a Microsoft TDSP csapat megfelelő tárházából. 

## <a name="customize-the-contents-of-the-group-repositories"></a>A csoportadattárak tartalmának testreszabása

Ha a csoporttárházainak tartalmát a csoport egyedi igényeinek megfelelően szeretné testreszabni, most megteheti. Módosíthatja a fájlokat, módosíthatja a könyvtárszerkezetet, vagy hozzáadhat olyan fájlokat, amelyeket a csoport fejlesztett ki, vagy amelyek hasznosak a csoport számára.

### <a name="make-changes-in-azure-repos"></a>Módosítások végrehajtása az Azure-repókban

A tárház tartalmának testreszabása:

1. A **GroupCommon** projekt **összegzése** lapon válassza **a Repos**lehetőséget. 
   
1. A lap tetején válassza ki a testre szabni kívánt tárházat.

1. A tártárstruktúrában keresse meg a módosítani kívánt mappát vagy fájlt. 
   
   - Új mappák vagy fájlok létrehozásához jelölje ki az **Új**gomb melletti nyilat. 
     
     ![Új fájl létrehozása](./media/group-manager-tasks/new-file.png)
     
   - Fájlok feltöltéséhez válassza **a Fájl(ok) feltöltése lehetőséget.** 
     
     ![Fájlok feltöltése](./media/group-manager-tasks/upload-files.png)
     
   - Meglévő fájlok szerkesztéséhez keresse meg a fájlt, és válassza a **Szerkesztés**lehetőséget. 
     
     ![Fájl szerkesztése](./media/group-manager-tasks/edit-file.png)
     
1. A fájlok hozzáadása vagy szerkesztése után válassza a **Véglegesítés**lehetőséget.
   
   ![Módosítások véglegesítése](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Módosítások végrehajtása a helyi számítógép vagy a DSVM használatával

Ha a helyi számítógép vagy a DSVM használatával szeretne módosításokat végrehajtani, és a módosításokat a csoporttárolókba szeretné átvinni, győződjön meg arról, hogy rendelkezik a Git- és DSVM-ek használatának előfeltételeivel:

- Egy Azure-előfizetés, ha dsvm-et szeretne létrehozni.
- Git telepítve van a gépre. Ha DSVM-et használ, a Git előre telepítve van. Ellenkező esetben tekintse meg a [Platformok és eszközök függeléket.](platforms-and-tools.md#appendix)
- Ha DSVM-et szeretne használni, az Azure-ban létrehozott és konfigurált Windows vagy Linux DSVM. További információt és utasításokat az [Adatelemzési virtuálisgép dokumentációjában talál.](/azure/machine-learning/data-science-virtual-machine/)
- Windows DSVM esetén a [Git credential manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) telepítve van a számítógépen. A *README.md* fájlban görgessen le a **Letöltés és telepítés** szakaszhoz, és válassza ki a legújabb **telepítőt**. Töltse le az *.exe* telepítőt a telepítő lapról, és futtassa azt. 
- Linuxos DSVM esetén a DSVM-en beállított és az Azure DevOps-ban hozzáadott SSH nyilvános kulcs. További információt és utasításokat a Platformok és eszközök függelék **SSH-kulcs létrehozása** [című szakaszában talál.](platforms-and-tools.md#appendix) 

Először másolja vagy *klónozza* a tárházat a helyi számítógépre. 
   
1. A **GroupCommon** projekt **összegzése** lapon válassza a **Repók**lehetőséget, és a lap tetején válassza ki a klónozni kívánt tárházat.
   
1. A tárház lapon válassza a **Klónozás** lehetőséget a jobb felső sarokban.
   
1. A **Klónozótár** párbeszédpanelen válassza a **HTTPS lehetőséget** egy HTTP-kapcsolathoz, vagy **az SSH-kapcsolatot,** és másolja a klón URL-t a **parancssorból** a vágólapra.
   
   ![Klón tárhét](./media/group-manager-tasks/clone.png)
   
1. A helyi számítógépen hozza létre a következő könyvtárakat:
   
   - Windows rendszerre: **C:\GitRepos\GroupCommon**
   - Linux esetén **$/GitRepos/GroupCommon** a kezdőkönyvtárban 
   
1. Váltás a létrehozott könyvtárra.
   
1. A Git Bash alkalmazásban futtassa a parancsot`git clone <clone URL>.`
   
   Például az alábbi parancsok valamelyike klónozza a **GroupUtilities** tárházat a helyi számítógép *GroupCommon* könyvtárába. 
   
   **HTTPS-kapcsolat:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH kapcsolat:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Miután bármilyen módosítást végzett a tárház helyi klónjában, leküldéses a módosításokat a megosztott csoport közös tárolóiba. 

Futtassa a következő Git Bash parancsokat a helyi **GroupProjectTemplate** vagy **GroupUtilities** könyvtárból.

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

## <a name="add-group-members-and-configure-permissions"></a>Csoporttagok hozzáadása és engedélyek konfigurálása

Tagok hozzáadása a csoporthoz:

1. Az Azure DevOps-ban a **GroupCommon** projekt kezdőlapján válassza a **Projekt beállításai t** a bal oldali navigációs sávon. 
   
1. A **Projektbeállítások** bal oldali navigációs sávon válassza a **Csapatok**lehetőséget, majd a **Csapatok** lapon válassza a **GroupCommon Team**lehetőséget. 
   
   ![Csapatok konfigurálása](./media/group-manager-tasks/teams.png)
   
1. A **Csapatprofil** lapon válassza a **Hozzáadás gombot.**
   
   ![Hozzáadás a csoportközös csapathoz](./media/group-manager-tasks/add-to-team.png)
   
1. A **Felhasználók és csoportok hozzáadása** párbeszédpanelen keresse meg és jelölje ki a csoporthoz hozzáadni kívánt tagokat, majd válassza a **Módosítások mentése lehetőséget.** 
   
   ![Felhasználók és csoportok hozzáadása](./media/group-manager-tasks/add-users.png)
   

A tagok engedélyeinek konfigurálása:

1. A **Projekt beállításai** navigációs sávon válassza az **Engedélyek**lehetőséget. 
   
1. Az **Engedélyek** lapon jelölje ki azt a csoportot, amelyhez tagokat szeretne hozzáadni. 
   
1. A csoport lapján válassza a **Tagok**lehetőséget, majd a **Hozzáadás**lehetőséget. 
   
1. A **Tagok meghívása** előugró ablakban keresse meg és jelölje ki a csoporthoz felvenni kívánt tagokat, majd válassza a **Mentés gombot.** 
   
   ![Engedélyek megadása a tagoknak](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>További lépések

Az alábbiakban a csapatadat-elemzési folyamat egyéb szerepköreinek és feladatainak részletes leírására mutató hivatkozásokat olvashat:

- [Csapatvezető-feladatok egy adatelemzési csapatszámára](team-lead-tasks.md)
- [Projektvezető-tevékenységek adatelemzési csoportszámára](project-lead-tasks.md)
- [Egyéni közreműködői feladatok projektje adatelemzési csoport számára](project-ic-tasks.md)
