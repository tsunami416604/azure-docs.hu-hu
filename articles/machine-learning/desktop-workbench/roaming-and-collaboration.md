---
title: Központi és az Azure Machine Learning munkaterület együttműködés |} Microsoft Docs
description: Megtudhatja, hogyan állíthat be központi és együttműködés a Machine Learning-munkaterület.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: b587f5dcc9558ec52b85e4b53dae0e31ad475a4e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Központi és az Azure Machine Learning munkaterület együttműködés
Ez a cikk azt ismerteti, hogyan használható Azure Machine Learning-munkaterület projektek a számítógépek közötti barangolás, és együttműködik a csoport tagjai. 

Az Azure Machine Learning-projekt, amely rendelkezik egy távoli Git-tárház (tárház) hivatkozás létrehozásakor a projekt metaadatok és a pillanatfelvételeket tárolják a felhőben. A felhő hivatkozás segítségével (központi) egy másik számítógépről nyissa meg a projektet. Is együttműködhet csoport tagjainak hozzáférést kaphatnak a projekt által. 

## <a name="prerequisites"></a>Előfeltételek
1. A gépi tanulási a munkaterületet üzemeltető alkalmazás telepítése. Győződjön meg arról, hogy rendelkezik-e egy olyan Azure Machine Learning kísérletezhet fiókot. További információkért lásd: a [a telepítési útmutató](../service/quickstart-installation.md).

2. Hozzáférés [Visual Studio Team Services](https://www.visualstudio.com) (Team Services), majd hozza létre a projektet a tárházban. További információkért lásd: [Git-tárház használata a Machine Learning-munkaterület projekt](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Gépi tanulás új projekt létrehozása
Nyissa meg a Machine Learning-munkaterület, és ezután hozzon létre egy új projektet (például egy iris nevű projekt). Az a **visualstudio.com webhelyre GIT-tárház URL-cím** adja meg egy érvényes URL-CÍMÉT a Team Services Git-tárház. 

> [!IMPORTANT]
> Ha úgy dönt, hogy az üres projektsablon, a Git-tárház használt valószínűleg már rendelkezik egy főághoz. Gépi tanulás egyszerűen klónokat helyileg a főágba. Hozzáadja a aml_config mappa és egyéb metaadatokat projektfájlokat a helyi projekt mappába. 
>
> Ha úgy dönt más projekt sablonokat, a Git-tárház *nem* már rendelkezik egy főághoz. Ha igen, hibaüzenet jelenik meg. Ez esetben használja a `az ml project create` parancsot a projekt létrehozásához egy `--force` váltani. Ez törli a fájlokat az eredeti fő ág és azok az Ön által a sablont az új fájlokat.

A projekt létrehozása után néhány futtatja a parancsfájlokat, amelyek a projekt elküldése Ez a művelet a projekt állapotát a távoli Git-tárház futtatási előzményei elágazási érvényesítése. 

> [!NOTE] 
> Csak a parancsfájl futtatásával eseményindító véglegesíti futtatási előzményei ágat. Adatok előkészítése és a Notebook fut nem indítható el a projekt pillanatképek futtatási előzményei ágában.

Git hitelesítési beállítását követően a főágba is akkor is működik. Másik lehetőségként létrehozhat egy új fiókirodai. 

Példa: 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>Központi
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Nyissa meg a Machine Learning-munkaterület egy másik számítógépen
Miután a Team Services Git-tárház a projektet, érheti el a iris projekt bármely olyan számítógépről, amely rendelkezik a Machine Learning munkaterület telepítve. 

Nyissa meg a iris projektet egy másik számítógépen, akkor be kell jelentkeznie a az alkalmazás által a projekt létrehozásához használt hitelesítő adatok használatával. Is kell lenniük a Machine Learning kísérletezhet ugyanazt a fiókot és a munkaterületen. A iris projekt betűrendben szerepel a más projektek a munkaterületen. 

### <a name="download-the-project-on-a-second-computer"></a>Töltse le a projekt egy másik számítógépen
Ha a munkaterületet meg nyitva, a második számítógép, a iris projekt melletti ikon eltér a szokásos mappára. Letöltési ikon jelzi, hogy a projekt tartalma a felhőben, és, hogy le kell az aktuális számítógép készen áll-e a projektet. 

![Projekt létrehozása](./media/roaming-and-collaboration/downloadable-project.png)

Válassza ki azt a iris a letöltés megkezdéséhez. A letöltést, akkor a projekt legyenek elérhetők a második számítógép készen áll. 

A Windows, a projekt itt található: C:\Users\\< felhasználónév\>\Documents\AzureML.

A macOS, a projekt itt található: /home/\<felhasználónév \> /dokumentumok/AzureML.

Egy későbbi kiadásban tervezzük kibővítik a funkciókat, így kiválaszthatja a célmappát. 

> [!NOTE]
> Ha egy mappa a pontos neve megegyezik a projekt Machine Learning könyvtárban található, a letöltés sikertelen lesz. A probléma megoldása érdekében ideiglenesen nevezze át a meglévő mappába.


### <a name="work-on-the-downloaded-project"></a>A letöltött projektből működik 
Az újonnan letöltött projektet, a legutóbbi futtatás a projekt projekt állapotát tükrözi. A projekt szerinti pillanatkép elkötelezett automatikusan a Team Services Git-tárházban futtatási előzményei ág minden alkalommal, amikor a kísérletek elküldését. A pillanatkép, a legutóbbi futtatás társított segítségével hozható létre a projektet, a második számítógépre. 
 

## <a name="collaboration"></a>Együttműködés
Csoport tagjai, amelyek kapcsolódnak a Team Services Git-tárház projektek együttműködhet. Engedélyeket rendelhet a felhasználók a Machine Learning kísérletezhet, a munkaterület és a projekt fiók. Jelenleg Azure Resource Manager parancsokat hajthat végre az Azure parancssori felület használatával. Használhatja a [Azure-portálon](https://portal.azure.com). További információkért lásd: [felhasználók hozzáadása az Azure portál segítségével](#portal).    

### <a name="use-the-command-line-to-add-users"></a>Felhasználók hozzáadása a parancssor használatával
Tegyük fel Ágnes a iris projekt tulajdonosa. Alice szeretné megosztani a projekthez való hozzáférés Bob. 

Alice kiválasztja a **fájl** menüben, majd kiválasztja a **parancssor** menüpont. A parancssori ablakban nyílik meg a iris projekt. Alice Ezután eldöntheti, milyen hozzáférési szintje szeretné adjon neki. Ezzel engedélyt ad a következő parancsok végrehajtásával:  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

Szerepkör-hozzárendelés után közvetlenül vagy öröklés, Bob tekintheti meg a projektet a Machine Learning-munkaterület projekt listában. Bálint az alkalmazás a projekt újraindítására lehet szükség. Bob ezután letöltheti a projekt a [központi](#roaming), és kezdje el Alice együttműködni. 

Összes felhasználó számára a projekt együttműködhet futtatási előzményeit elkötelezett az azonos távoli Git-tárház. Futtatás végrehajtásakor a Alice Bob a futtatáskor a Machine Learning-munkaterület-alkalmazás projekt futtatási előzményei szakaszában tekintheti meg. Bob is helyreállíthatja a projekt bármely futtassa, beleértve, fut-e Alice futtatása állapotát. 

Ossza meg a projekt egy távoli Git-tárház, Alice, Bob és is együttműködhetnek a a főágba. Ha szükséges, hogy is személyes fiókirodákat és Git-lekérési kérelmek és összevonása együttműködést. 

### <a name="use-the-azure-portal-to-add-users"></a>Felhasználók hozzáadása az Azure-portál használatával
<a name="portal"></a>

Számítógépfiókok tanulási kísérletezhet, munkaterületekkel és projektek az Azure Resource Manager erőforrásokat. Ha szerepköröket, használhatja a **hozzáférés-vezérlés** hivatkozásra a [Azure-portálon](https://portal.azure.com). 

Felhasználók hozzáadása a kívánt erőforrás található a **összes erőforrás** megtekintése. Válassza ki a **hozzáférés-vezérlés (IAM)** hivatkozásra, és válassza ki **felhasználók hozzáadása az**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>A minta együttműködés munkafolyamat
Az együttműködés munkafolyamat mutatja be, bemutatjuk, egy példán keresztül. Alice, Bob és a Contoso az alkalmazottak szeretnék a tudományos ülve a Machine Learning-munkaterület közötti együttműködés. Az identitásokat a Contoso Azure Active Directory (Azure AD) ugyanannak a bérlőnek tartozik. Alice és érvénybe Bob lépései a következők:

1. Alice létrehoz egy üres Git-tárház Team Services projektben. Azure-előfizetéssel, amely alatt a Contoso Azure AD-bérlő jön létre a Team Services projekt kell lennie. 

2. Alice létrehoz egy Machine Learning kísérletezhet fiókot, a munkaterület és a Machine Learning-munkaterület projekt számítógépén. A projekt létrehoz, ha beírja a Team Services Git-tárház URL-cím.

3. Alice megkezdi a munkát a projekthez. Ezzel létrehoz néhány parancsprogramot, és néhány futtatása végrehajtja. Minden egyes futtatásához a teljes projektmappa pillanatképe automatikusan kerül egy véglegesítést, a Machine Learning-munkaterület hozható létre Team Services Git-tárház futtatási előzményei ág.

4. Alice elégedett a megoldása folyamatban. Szeretné a helyi főágába a változtatások véglegesítése a határidő és majd küldje le őket a Team Services Git-tárház főágába. Nyissa meg a projektet, és a Machine Learning-munkaterület ő nyitja meg a parancsablakot, és majd megadja ezeket a parancsokat:
    
    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Team Services.
    $ git push
    ```

5. Alice közreműködőként Bob hozzáadja a munkaterületen. Ő ehhez az Azure portálon, vagy a `az role assignment` , ahogyan korábban parancsot. Alice Bob olvasási/írási engedéllyel a Team Services Git-tárház is nyújt.

6. Bob bejelentkezik Machine Learning-munkaterület a számítógépen. Azt látja, Ágnes megosztott neki a munkaterületen. A munkaterülethez tartozó iris projekt megjelenik. 

7. Bob választja ki a projekt nevét. A számítógép letölti a projektet.
    * A letöltött projektből fájlok a legutóbbi futtatás futtatási előzményeit rögzített pillanatképe másolatát. Nincsenek meg a főágba utolsó véglegesítése.
    * A projekt helyi mappa a főágba unstaged módosításainak van megadva.

8. Bob megkeresheti Alice által végrehajtott futtatása. Ezután is állítson vissza egy pillanatképet az összes korábbi futtatása.

9. Bob elérhetők legyenek a legutóbbi változtatások, amely Alice leküldött, és indítsa el a működik-e a másik fiókiroda szeretné. Machine Learning-munkaterület Bob megnyit egy parancssori ablakot, és végrehajtja a következő parancsokat:

    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Team Services Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob a projekt módosítja, és elküldi az új futtatása. A módosítások a bob ágat. Bob futtatása is Ágnes láthatóvá válnak.

11. Bob készen áll a saját változásainak leküldése a távoli Git-tárház. A főágba, ahol Alice dolgozik, az ütközés elkerülése érdekében Bob leküldi munkája új távoli ágán, amelynek bob neve is.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Team Services Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Adja a kódban a ritkán használt adatok új trükk Alice, Bob hoz egy lekérést a a távoli Git-tárház a bob ágat a főágba. Alice a főágba történő majd egyesítheti a lekérési kérelem.

## <a name="next-steps"></a>További lépések
- További információ [Git-tárház használata a Machine Learning-munkaterület projekt](using-git-ml-project.md).
