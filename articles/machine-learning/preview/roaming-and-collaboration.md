---
title: "Központi és az Azure-ban együttműködés gép munkaterület tanulási |} Microsoft Docs"
description: "Ismert problémák listája és a hibaelhárítás elősegítése érdekében az útmutató"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 81954835185ebaa86c11a9498a85879e6985897a
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Központi és az Azure Machine Learning munkaterület együttműködés
A dokumentumból megtudhatja, hogyan segítik a Azure Machine Learning-munkaterület gépek, valamint a teammates együttműködve lehetővé teszik a projektek válasszanak. 

Egy távoli Git-tárház (tárház) hivatkozás az Azure Machine Learning projekt létrehozásakor, a projekt metaadatok és a pillanatfelvételeket tárolják a felhőben. A felhő hivatkozás lehetővé teszi egy másik számítógépről (központi) nyissa meg a projektet. Akkor is hozzáférést biztosíthat a munkatársai, így az együttműködés. 

## <a name="prerequisites"></a>Előfeltételek
Először telepítse az Azure Machine Learning-munkaterület kísérletezhet fiók hozzáférést. Kövesse a [a telepítési útmutató](quickstart-installation.md) további részleteket.

Második, hozzáférési [Visual Studio Team System](https://www.visualstudio.com) , és hozzon létre egy tárház a projektet. Git kapcsolatos részletes információkért lásd a [Git-tárház használata Azure Machine Learning munkaterület projekttel](using-git-ml-project.md) cikk.

## <a name="create-a-new-azure-machine-learning-project"></a>Új Azure Machine Learning-projekt létrehozása
Indítsa el az Azure Machine Learning-munkaterület, és hozzon létre egy új projektet (például _iris_). Töltse ki a **visualstudio.com webhelyre GIT-tárház URL-cím** egy érvényes VSTS Git-tárház URL-szövegmező. 

> [!IMPORTANT]
> Ha úgy dönt, hogy az üres projektsablon, a rendszer OK a Git-tárház már választja-e egy _fő_ ág. Az Azure ML egyszerűen klónokat a _fő_ fiókirodai helyileg, és adja hozzá a `aml_config` mappa és más projektfájlok metaadatok a helyi projekt mappába. De ha úgy dönt, hogy a többi projekt sablont, a Git-tárház kell már rendelkezik egy _fő_ fiókirodákban, vagy ha hibaüzenetet kap. Ez esetben használjon `az ml project create` a projekt létrehozásához, és adja meg a parancssori eszköz egy `--force` váltani. Ez törli a fájlokat az eredeti főágába, és cserélje le őket a sablon az új fájlokat.

A projekt létrehozása után küldje el a projekt parancsfájlokat néhány felhőkörnyezetben. Ez a művelet be a távoli Git-tárház futtatási előzményei fiókirodai projekt állapot érvényesítése. 

> [!NOTE] 
> Csak a parancsfájl futtatásával eseményindító véglegesíti futtatási előzményei ágat. Adatok előkészítése végrehajtása vagy a Notebook futtatása nem indítható el, a futtatási előzményei fiókirodai projekt pillanatfelvételeket.

Ha telepítő Git hitelesítési, kíván közvetlenül is működik, a főágba, vagy létrehozni egy új fiókirodai. 

Példa: 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>Központi
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Nyissa meg az Azure Machine Learning-munkaterület második gépen
Miután a VSTS Git-tárház kapcsolódik a projekthez, elérheti a _iris_ bármely olyan számítógépről, amelyen telepítette az Azure Machine Learning-munkaterület projekt. 

Nyissa meg a iris projektet egy másik számítógépen, kell bejelentkezni az alkalmazásba azokkal a a projekt létrehozása során használt hitelesítő adatokkal. Ezen felül kell kísérletezhet fiók és a munkaterületen nyissa meg. A _iris_ projekt betűrendben szerepel a más projektek belül a munkaterületen. 

### <a name="download-project-on-second-machine"></a>A második gépen projekt letöltése
Ha a munkaterületet meg van nyitva, a második számítógép, a szomszédos ikonra a _iris_ projekt eltér a szokásos mappára. Letöltési ikon jelzi, hogy a projekt a tartalom a felhőben, és az aktuális számítógép le kell tölteni. 

![projekt létrehozása](./media/roaming-and-collaboration/downloadable-project.png)

Kattintson a _iris_ projekt elindítja egy letöltési művelet. Egy rövid, közben, amikor a letöltés befejezése után a projekt legyenek elérhetők a második számítógép készen áll. 

Windows, a rendszer`C:\Users\<username>\Documents\AzureML`

A macOS akkor a következő helyen:`/home/<username>/Documents/AzureML`

Egy későbbi kiadásban tervezzük kibővítik a funkciókat, hogy jelöljön ki egy célmappát meg. 

> [!NOTE]
> Egy mappa a pontos neve megegyezik a projekt Azure ML könyvtárban található fordulhat elő, ha a letöltés sikertelen lesz. Jelenleg nevezze át a meglévő mappát ahhoz, hogy a probléma megoldásához szükséges.


### <a name="work-on-the-downloaded-project"></a>A letöltött projektből működik 
Az újonnan letöltött projektből a legutóbbi futtatás a projektben a projekt állapotot tükrözi. A projekt szerinti pillanatkép elkötelezett automatikusan a VSTS Git-tárházban futtatási előzményei ág minden alkalommal, amikor a kísérletek elküldését. A pillanatkép, a második számítógépre a projekt példányának létrehozásakor a legutóbbi futtatás társított használjuk. 
 

## <a name="collaboration"></a>Együttműködés
A projektek a VSTS Git-tárház kapcsolódó teammates együttműködhet. Engedélyeket rendelhet a felhasználókhoz a kísérleti fiók, a munkaterületet, és a projektet. Ekkor az Azure Resource Manager-parancsokat az Azure parancssori felület használatával végezheti el. Is [Azure-portálon](https://portal.azure.com). Lásd: [a következő szakasz](#portal).    

### <a name="using-command-line-to-add-users"></a>Felhasználók hozzáadása a parancssorból
Lehetővé teszi, hogy egy példát. Tegyük fel például, Ágnes th e_Iris_ projekt tulajdonosa, és szeretné Bob hozzáférési megosztása. 

Alice a hivatkozásra kattint a **fájl** menüt, és választják ki a **parancssor** menü a lehetőséggel elindíthatja a parancssorba, úgy konfigurálva, hogy a _iris_ projekt. Alice az Ezután eldöntheti, hogy milyen szintű fő hozzáféréssel, és szeretné adjon Bob a következő parancsok végrehajtásával.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the Workspace by inheritance. And he can invite or remove others.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace ARM ID>
```

A szerepkör-hozzárendelés után közvetlenül vagy öröklés, Bob tekintheti meg a projektet a munkaterületet üzemeltető projekt listában. Az alkalmazás újraindítás módosítania kell a projekt láthatók. Bob ezután letöltheti a projekt leírtak szerint a [szakasz központi](#roaming) és Alice együttműködni. 

Az azonos távoli Git-tárház elkötelezett az összes felhasználó számára, a projekt dolgoznak futtatási előzményeit. Ezért futtató végrehajtásakor a Alice Bob tekintheti meg a Futtatás a munkaterület-alkalmazás projekt futtatási előzményei szakaszában. Bob is helyreállíthatja a projekt bármely Alice indította futtatása beleértve futtatás állapotát. 

A projekt egy távoli Git-tárház megosztása lehetővé teszi, hogy Alice és Bob is közösen a főágba történő. Ha szükséges, hogy is személyes fiókirodákat és Git lekéréses-kérelmek és összevonása együttműködést. 

### <a name="using-azure-portal-to-add-users"></a>Felhasználók hozzáadása az Azure-portál használatával
<a name="portal"></a>

Az Azure Machine Learning kísérletezhet fiókok, munkaterületekkel és projektek az Azure Resource Manager erőforrásokat. A hozzáférés-vezérlés hivatkozásra is használhatja a [Azure-portálon](https://portal.azure.com) rendelhet szerepköröket. 

Adja hozzá a felhasználók számára az összes erőforrásból szeretne erőforrás található. Kattintson a hozzáférés-vezérlés a Page (IAM) hivatkozásra. Felhasználók hozzáadása 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>A minta együttműködés munkafolyamat
Az együttműködés folyamatot mutatja be, bemutatjuk, egy példán keresztül. Contoso alkalmazottak Alice, Bob és szeretné, hogy Azure ML munkaterület használatával adatok tudományos ülve dolgozhatnak. Az identitásukat a ugyanazt a Contoso Azure AD-bérlő tartozik.

1. Alice először létrehoz egy üres Git-tárház VSTS projektben. Ez a VSTS-projekt alatt a Contoso AAD-bérlőt hozott létre Azure-előfizetéssel kell létezniük. 

2. Alice ezután hoz létre egy Azure ML kísérletezhet fiókot, egy munkaterület és az Azure ML munkaterület projektek számítógépén. A projekt létrehozása ő megadja a Git-tárház URL-cím.

3. Alice megkezdi a munkát a projekthez. Ezzel létrehoz néhány parancsprogramot, és néhány futtatása végrehajtja. Minden egyes futtatásához a teljes projektmappa pillanatképe automatikusan leküldött azokat a VSTS Git-tárház hozta létre, egy véglegesítési munkaterület futtatási előzményei ág.

4. Alice már elégedett a befejezetlen. És szeretné véglegesíteni a helyi rendszerre változtatást _fő_ fiókirodai és leküldéses értesítések az azokat a VSTS Git-tárház _fő_ ág. Ehhez nyissa meg a projekt ő a parancssori ablakot, az Azure ML munkaterület elindul, és ki a következő parancsokat:
    
    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # stage all changes
    $ git add -A

    # commit changes with a comment
    $ git commit -m "this is a good milestone"

    # push the commit to the master branch of the remote Git repo in VSTS
    $ git push
    ```

5. Alice hozzáadja Bob a munkaterületre közreműködőként. Ő ehhez az Azure portálon, vagy használja a `az role assignment` parancs fent mutatja be. Azt is nyújt, Bob olvasási/írási hozzáférést a VSTS Git-tárház számára.

6. Bob most jelentkezik be az Azure ML-munkaterület a számítógépen. Azt látja, a vele megosztott munkaterület Alice, és a munkaterülethez tartozó projekt. 

7. Bob a projekt nevére kattint, és a számítógép letölti a projektet.
    
    a. A letöltött projektből fájlok futtatási előzményeit rögzíti a legutóbbi futtatás pillanatképe másolatát. Nincsenek meg a főágba utolsó véglegesítése.
    
    b. A projekt helyi mappa van megadva a _fő_ fiókirodai a fenti unstaged változtatásokat.

8. Bob tallózhatnak végrehajtja az Alice, és bármely korábbi futtatása visszaállítási pillanatképe futtatása.

9. Bob szeretne leküldött Alice által a legutóbbi változtatásokat, és indítsa el a másik fiókiroda működéséhez. Így ezután parancssori ablak megnyitása az Azure ML munkaterület, és végrehajtja a következő parancsokat:

    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # get the latest commit in VSTS Git master branch and overwrite current files
    $ git pull --force

    # create a new local branch named "bob" so Bob's work is done on the "bob" branch
    $ git checkout -b bob
    ```

10. Bob most módosítja a projekt, és küldje el az új futtatása. A módosításokat végzett a a _bob_ ág. És Bob futtatása megjelenítése Ágnes is.

11. Bob készen áll a küldje le a módosításokat a távoli Git-tárház be. Való ütközés elkerülése érdekében _fő_ fiókirodákban, ahol Alice működik-e, eldönti, hogy egy új távoli helyen, más néven a saját munkáját leküldéses _bob_.

    ```sh
    # verify that the current branch is "bob" and it has unstaged changes
    $ git status
    
    # stage all changes
    $ git add -A

    # commit them with a comment
    $ git commit -m "I found a cool new trick."

    # create a new branch on the remote VSTS Git repo, and push changes
    $ git push origin bob
    ```

12. Bob majd írhatja Alice az új ritkán használt adatok körben kapcsolatos a kódban, és a távoli Git-tárház hoz létre egy lekérési kérelmet a _bob_ a fiókirodai a _fő_ ág. Alice majd egyesítheti a lekérési kérelem be és _fő_ ág.

## <a name="next-steps"></a>Következő lépések
További információ a Git használatával az Azure ML munkaterület: [Azure Machine Learning-munkaterület projekttel használatával Git-tárház](using-git-ml-project.md)