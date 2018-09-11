---
title: Barangolás és együttműködés az Azure Machine Learning Workbenchben |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be a barangolás és együttműködés a Machine Learning Workbench alkalmazásban.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 0abc5e34d2bfa1cf2a9fc0569831e21ed295891c
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296499"
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Barangolás és együttműködés az Azure Machine Learning Workbenchben
Ez a cikk bemutatja, hogyan használhatja az Azure Machine Learning Workbench a számítógépek között központi projektek és együttműködhet a csapat tagjai. 

Amikor létrehoz egy Azure Machine Learning-projektet, amely rendelkezik egy távoli Git-tárház (adattár) hivatkozást, a projekt metaadatok és a pillanatképekhez tárolódnak a felhőben. Használhatja a felhő hivatkozásra a projekthez egy másik számítógépről (központi) eléréséhez. Is együttműködhet a csapat tagjai által hozzáférést ad nekik a projekthez. 

## <a name="prerequisites"></a>Előfeltételek
1. A Machine Learning Workbench alkalmazás telepítéséhez. Győződjön meg arról, hogy az Azure Machine Learning-Kísérletezési fiókhoz való hozzáférés. További információkért lásd: a [telepítési útmutató](../service/quickstart-installation.md).

2. Hozzáférés [Azure DevOps](https://www.visualstudio.com) , majd hozzon létre olyan adattárat a projekthez, hogy mutató hivatkozást. További információkért lásd: [egy Machine Learning Workbench-projekt Git-tárház használatával](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Hozzon létre egy új Machine Learning-projektet
Nyissa meg a Machine Learning Workbench, és ezután hozzon létre egy új projektet (például egy iris nevű projekt). Az a **Visualstudio.com GIT-adattár URL-címe** adja meg egy érvényes URL-címet az Azure DevOps Git-adattárból. 

> [!IMPORTANT]
> Ha úgy dönt, az üres projekt sablon, a Git-tárház használt előfordulhat, hogy már a master ággal. Machine Learning egyszerűen csak klónozza helyileg a master ágról. A aml_config mappát és egyéb metaadatok soubory projektu hozzáadja a helyi projektmappában. 
>
> Ha úgy dönt, bármely más webesprojekt-sablon, a Git-tárház *nem* már rendelkezik a master ággal. Ha igen, hibaüzenet jelenik meg. Ez esetben használja a `az ml project create` parancsot a projekt létrehozása egy `--force` váltson. Ez törli a fájlokat az eredeti főágban, és lecseréli azokat az új fájlokat a sablonban, hogy az Ön által választott.

A projekt létrehozása után küldjön el néhány fut minden olyan parancsfájlok, amelyek a projektben. Ez a művelet a projekt állapota véglegesíti a távoli Git-tárház futtatási előzmények ágban. 

> [!NOTE] 
> Csak a parancsfájl a futtatási előzmények ágra eseményindító véglegesítéseket futtatja. Adat-előkészítési végrehajtását és a Notebook futtatások nem aktiválja a futtatási előzmények ágban projekt pillanatképeket.

Ha úgy állította be a Git-hitelesítést, is működhet a master ágban található. Vagy létrehozhat egy új ágat. 

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

## <a name="roaming"></a>Barangolás
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Nyissa meg a Machine Learning Workbench alkalmazásban egy másik számítógépen
Miután az Azure DevOps Git-adattárat a projekthez kapcsolódik, elérheti az iris-projektből bármely olyan számítógépről, amelyen telepítve van a Machine Learning Workbench alkalmazásban. 

Hozzáférhet az iris-projektből egy másik számítógépen, jelentkezzen be az alkalmazás által a projekt létrehozása során használt hitelesítő adatokkal. Is kell lenniük az ugyanazt a Machine Learning-Kísérletezési fiókot és a munkaterületen. A munkaterület más projektekkel betűrend szerint jelenik meg az iris-projektből. 

### <a name="download-the-project-on-a-second-computer"></a>Töltse le a projekthez egy másik számítógépen
Ha a munkaterületen nyissa meg a második számítógépre, az iris-projektből melletti ikon eltér a tipikus mappa ikont. A letöltési ikon azt jelzi, hogy a projekt tartalma a felhőben, és, hogy le kell az aktuális számítógép készen áll-e a projekt. 

![Projekt létrehozása](./media/roaming-and-collaboration/downloadable-project.png)

Válassza ki az iris-projektből a letöltés megkezdéséhez. A letöltés befejeződése után a projekt érhető el a második számítógép készen áll. 

Windows, a projekt nem található: C:\Users\\< felhasználónév\>\Documents\AzureML.

MacOS-gépeken, a projekt következő /home/ helyen található\<felhasználónév \> /dokumentumok/AzureML.

Egy későbbi kiadásban tervezzük kibővítik a funkciókat, így is jelöljön ki egy célmappát. 

> [!NOTE]
> Ha van olyan mappája, amely rendelkezik a pontos neve megegyezik a projekt Machine Learning a címtárban, a letöltés sikertelen lesz. A probléma megkerüléséhez ideiglenesen nevezze át a létező mappát.


### <a name="work-on-the-downloaded-project"></a>A letöltött projektből dolgozhat 
Az újonnan letöltött projektet, a legutóbbi futtatás a projektet a project állapotát tükrözi. A projekt állapotának pillanatkép elkötelezett automatikusan a futtatási előzményekben tekintheti át az Azure DevOps Git-adattár ágában minden alkalommal, amikor a Futtatás küld el. A pillanatkép, a legutóbbi futtatás társított segítségével hozza létre a projektet, a második számítógépre. 
 

## <a name="collaboration"></a>Együttműködés
Együttműködhet a csapat tagjai, amelyek kapcsolódnak az Azure DevOps Git-tárház projekteken. Engedélyeket rendelhet a felhasználók számára a Machine Learning-Kísérletezési fiók, a munkaterület és a projekt. Jelenleg az Azure CLI-vel az Azure Resource Manager-parancsok is végezhet. Is használhatja a [az Azure portal](https://portal.azure.com). További információkért lásd: [felhasználók hozzáadása az Azure portal használatával](#portal).    

### <a name="use-the-command-line-to-add-users"></a>Felhasználók hozzáadása a parancssor használatával
Tegyük fel a Alice az iris-projektből tulajdonosa. Alice szeretné osztani a projekthez való hozzáférés Bob. 

Alice kiválasztja a **fájl** menüben, majd kiválasztja a **parancssor** menüpontot. A parancssori ablak megnyílik az iris-projektből. Alice majd eldöntheti, milyen szintű hozzáférésre és szeretné Bob számára. Marcela engedélyt ad a következő parancsok végrehajtásával:  

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

A szerepkör hozzárendelése után közvetlenül vagy öröklés, Bob láthatja a projekt a Machine Learning Workbench-projekt listában. Bob a projekt megtekintéséhez az alkalmazás újraindítására lehet szükség. Bob ezután letöltheti a projekt leírtak szerint [Roaming](#roaming), és kezdje el Anna együttműködni. 

A futtatási előzmények az összes felhasználó számára, amelyek közösen dolgozzon a projekt számára fontos, hogy az egyazon távoli Git-adattárban. Alice futtató végrehajtja, amikor Bálint a Futtatás a projektre a Machine Learning Workbench alkalmazást a futtatási előzmények szakaszában tekintheti meg. Bob is helyreállíthatja a projekt bármely futtassa a következőt, beleértve az Alice elindított futtatások állapotát. 

Egy távoli Git-adattárat a projekthez tartozó megosztásával Alice, Bob és is együttműködhetnek a master ágban található. Szükség esetén azok személyes ágak létrehozása, és Git pull-kérelmek és összevonása használata működhet-e. 

### <a name="use-the-azure-portal-to-add-users"></a>Felhasználók hozzáadása az Azure portal használatával
<a name="portal"></a>

Machine Learning-Kísérletezési fiók munkaterületeket és projekteket az Azure Resource Manager-erőforrásokat. Ha szerepköröket, használhatja a **hozzáférés-vezérlés** hivatkozásra a [az Azure portal](https://portal.azure.com). 

Keresse meg az, hogy a felhasználók hozzáadása a kívánt erőforrást a **összes erőforrás** megtekintése. Válassza ki a **hozzáférés-vezérlés (IAM)** hivatkozásra, és válassza ki **felhasználók hozzáadása**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Együttműködés munkafolyamat-minta
Az együttműködés munkafolyamatot mutatja be, nézzük végig egy példa. Alice, Bob és Contoso az alkalmazottak szeretnék egy adatelemzési projektjéhez szerkesztésében a Machine Learning Workbench használatával. Identitásuk ugyanahhoz a Contoso Azure Active Directory (Azure AD) bérlőhöz tartozik. Alice, és igénybe Bob lépései a következők:

1. Alice létrehoz egy üres Git-tárházat az Azure DevOps-projekt. Az Azure DevOps-projekt jön létre a Contoso Azure AD-bérlőhöz Azure-előfizetést kell lennie. 

2. Alice létrehoz egy Machine Learning-kísérletezés-fiókot, a munkaterület és a egy Machine Learning Workbench-projekt számítógépén. A projekt létrehoz, ha beírja az Azure DevOps Git-adattár URL-CÍMÉT.

3. Alice elindul, és a projektben. Ő néhány parancsprogramot hoz létre, és végrehajt néhány futtatások. Minden egyes futtatásához a teljes projektmappáról pillanatképét automatikusan elküld a véglegesítések, egy az Azure DevOps Git-adattár, amely létrehozza a Machine Learning Workbench futtatási előzményeket ága.

4. Alice elégedett a megoldása folyamatban. Véglegesítse a változásokat a helyi főágban, és majd továbbítsa őket az Azure DevOps Git tárház fő ágát szeretné. Nyissa meg a projektet, és a Machine Learning Workbench marcela megnyitja a parancssori ablakot, és ezután beírja ezeket a parancsokat:
    
    ```sh
    # Verify that the Git remote is pointing to the Azure DevOps Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Azure DevOps.
    $ git push
    ```

5. Alice közreműködője Bob hozzáadja a munkaterületen. Ezt ennek az Azure Portalon, vagy a `az role assignment` parancsot, ahogyan korábban is. Alice is nyújt, Bob olvasási/írási engedélyek az Azure DevOps Git-adattárhoz.

6. Bob a számítógépen jelentkezik be Machine Learning Workbench alkalmazásban. Megjelenik a munkaterület, amely Alice osztott meg vele. Megjelenik az adott munkaterület alatt felsorolt iris-projektből. 

7. Bob választja ki a projekt nevét. A projekt letöltődik a számítógépre.
    * A letöltött projektből fájlok a legutóbbi futtatás, a futtatási előzmények rögzített a pillanatkép másolatát. Azok nem az utolsó véglegesítési a master ággal.
    * A helyi projektmappában a főágban, a nem előkészített módosítások van beállítva.

8. Bob megkeresheti az Anna által végrehajtott futtatások. Ő állíthatja vissza a korábbi futtatásokat pillanatképeket.

9. Bob szeretné a legutóbbi változtatásokat, hogy Ágnes leküldött lekérése, és indítsa el a egy másik ágban dolgozik. A Machine Learning Workbench Bob nyitja meg egy parancssori ablakot, és végrehajtja a következő parancsokat:

    ```sh
    # Verify that the Git remote is pointing to the Azure DevOps Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Azure DevOps Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob módosítja a projektet, és elküldi az új futtatások. A módosítások a bob ágon. Bob futtatások is láthatóvá Ágnes részére.

11. Bob készen áll a saját változásainak leküldése a távoli Git-tárház. A master ággal, ahol Alice működik, való ütközés elkerülése érdekében Bob leküldéses munkájával egy új fiókirodai, amelynek bob neve is.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Azure DevOps Git repo, and then push the changes.
    $ git push origin bob
    ```

12. A kódban a ritkán használt adatok új trükk kapcsolatos állapítható meg, hogy Ágnes, Bob lekéréses kérelem a távoli Git-adattárat a bob ágból a főágba hoz létre. Alice majd egyesítheti a pull-kérelmet a főágba.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egy Machine Learning Workbench-projekt Git-tárház használatával](using-git-ml-project.md).
