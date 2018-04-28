---
title: A Git-tárház használata az Azure Machine Learning-munkaterület projektek |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan együtt egy Git-tárházat az Azure Machine Learning-munkaterület-projekt esetében.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 6d244e990552ac4899361360efb44be140b06fe8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>A Git-tárház használata a Machine Learning-munkaterület projekt
Ismerje meg, hogyan Azure Machine Learning-munkaterület segítségével Git verziókezelést, és a tudományos adatok kísérletbe reprodukálhatósági biztosítja. Megtudhatja, hogyan rendelje hozzá a projekt Git-tárház (tárház) felhő.

Machine Learning-munkaterület a Git integráció szolgál. Új projekt létrehozása a projektmappa esetén automatikusan "Git-inicializálása" egy helyi Git-tárház. Egy második, rejtett helyi Git-tárház is létrejön, a fiókiroda AzureMLHistory nevű /\<GUID projekt\>. A fiókirodai nyomon követi az egyes végrehajtásra projekt módosításait. 

Az Azure Machine Learning projekt társít egy Git-tárház lehetővé teszi, hogy automatikus verziókezelést, helyileg és távolról. A Git-tárház egy, a Visual Studio Team Services (Team Services). Mivel a Machine Learning projekt Git-tárház társítva, birtokában bárki, aki hozzáfér a távoli tárház töltse le a legfrissebb forráskód (központi) egy másik számítógépre.  

> [!NOTE]
> Team Services rendelkezik saját hozzáférés-vezérlési lista (ACL), amely független az Azure Machine Learning kísérletezhet szolgáltatás. Felhasználói hozzáférés Git-tárház és a Machine Learning-munkaterület vagy projekt között változhat. Szükség lehet a hozzáférés kezelése. 
> 
> E kíván adni a csoport tagjainak kód szintű hozzáférés a Machine Learning projekthez vagy egyszerűen csak megoszthatja a munkaterület, kell biztosítania a felhasználó a megfelelő engedélyekkel a Team Services Git-tárház eléréséhez. 

Kezelheti a Git verziókezelést, használja a főágba, vagy hozzon létre a többi fiók közben a tárházban. A helyi Git-tárház használjon, és ha ki van építve a távoli Git-tárház leküldéssel is.

Ez az ábra a kapcsolat egy Team Services Git-tárház és a gépi tanulás projekt ábrázol:

![Az Azure Machine Learning Git](media/using-git-ml-project/aml_git.png)

Egy távoli Git-tárház használatának megkezdéséhez hajtsa végre a lépéseket, amelyek a következő szakaszok ismertetik.

> [!NOTE]
> Azure Machine Learning jelenleg csak az Team Services-fiókot támogatja a Git adattárak. Általános Git repók, például a github webhelyen, támogatása tervezünk-e a jövőben.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>1. lépés A Machine Learning kísérletezhet-fiók létrehozása
Machine Learning kísérletezhet fiók létrehozásához, és telepítse az Azure Machine Learning-munkaterület alkalmazást. További információkért lásd: [telepítse, és hozzon létre a gyors üzembe helyezés](../service/quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>2. lépés A csapatprojekt létrehozni, vagy egy meglévő csapatprojekt
Az a [Azure-portálon](https://portal.azure.com/), hozzon létre egy új csapatprojektbe:
1. Válassza ki **+**.
2. Keresse meg **projekt csapat**.
3. Adja meg a szükséges adatokat:
    - **Név**: A csoport nevét.
    - **Verziókövetés**: válasszon **Git**.
    - **Előfizetés**: Válasszon egy előfizetést, amely a Machine Learning kísérletezhet fiókkal rendelkezik.
    - **Hely**: ideális esetben válasszon egy régiót, amelynek mérete megközelítőleg a Machine Learning kísérletezhet erőforrásokhoz.
4. Kattintson a **Létrehozás** gombra. 

![Csoport-projekt létrehozása az Azure-portálon](media/using-git-ml-project/create_vsts_team.png)

Győződjön meg arról, hogy használatával írja alá Machine Learning-munkaterület eléréséhez használt azonos Azure Active Directory (Azure AD-) fiók. Ellenkező esetben a rendszer nem érhető el Machine Learning-munkaterület az Azure AD hitelesítő adataival. Kivétel, ha a parancs segítségével a Machine Learning-projekt létrehozása, és adja meg a Git-tárház eléréséhez személyes hozzáférési jogkivonat. Ez a cikk későbbi részében részletesebben azt tárgyalják.

Ugrás a közvetlenül a csapatprojekt létrehozott, használja az URL-cím https://\<csapatprojekt nevét\>. visualstudio.com webhelyre.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>3. lépés A Machine Learning projektet és a Git-tárház beállítása

A Machine Learning projekt beállításához két lehetőség közül választhat:
- Egy távoli Git-tárház rendelkezik Machine Learning-projekt létrehozása
- Rendelje hozzá egy meglévő Machine Learning-projektet egy Team Services Git-tárház

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Egy távoli Git-tárház rendelkezik Machine Learning-projekt létrehozása
Nyissa meg a Machine Learning-munkaterület, és hozzon létre egy új projektet. Az a **Git-tárház** mezőbe írja be a Team Services Git-tárház URL-cím a 2. lépésben. Általában néz ki: https://\<Team Services-fiók neve\>.visualstudio.com/_git/\<projekt neve\>

![A Git-tárház rendelkezik Machine Learning-projekt létrehozása](media/using-git-ml-project/create_project_with_git_rep.png)

A projekt az Azure parancssori eszköz (Azure CLI) használatával is létrehozhat. Lehetősége nyílik személyes hozzáférési jogkivonat megadásáról. Gépi tanulás a token használatával is hozzáférhetnek a Git-tárház helyett az Azure AD hitelesítő adatait:

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Team Services personal access token>
```

> [!IMPORTANT]
> Ha úgy dönt, hogy az üres projektsablon, a Git-tárház használt valószínűleg már rendelkezik egy főághoz. Gépi tanulás egyszerűen klónokat helyileg a főágba. Hozzáadja a aml_config mappa és egyéb metaadatokat projektfájlokat a helyi projekt mappába. 
>
> Ha úgy dönt más projekt sablonokat, a Git-tárház *nem* már rendelkezik egy főághoz. Ha igen, hibaüzenet jelenik meg. Ez esetben használja a `az ml project create` parancsot a projekt létrehozásához egy `--force` váltani. Ez törli a fájlokat az eredeti fő ág és azok az Ön által a sablont az új fájlokat.

Egy új Machine Learning projekt létrehozása a Git-tárház távoli integráció engedélyezésével. A projektmappa Git-inicializálása egy helyi Git-tárház mindig. A távoli Git beállítása a távoli Team Services Git-tárház, véglegesítések leküldése a távoli Git-tárház.

### <a name="associate-an-existing-machine-learning-project-with-a-team-services-git-repo"></a>Rendelje hozzá egy meglévő Machine Learning-projektet egy Team Services Git-tárház
A Team Services Git-tárház nélkül Machine Learning-projekt létrehozása, és futtatási előzményei a pillanatképek helyi Git-tárház támaszkodnak. Később társíthatja egy Team Services Git-tárház a meglévő Machine Learning-projekt a következő paranccsal:

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Team Services account name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> A frissítés-tárház művelet csak a Machine Learning-projekt, amely nincs társítva egy Git-tárház végezheti el. Emellett a Git-tárház társítja a Machine Learning, miután nem távolítható el.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>4. lépés A Git-tárházban projekt pillanatkép rögzítése
Végrehajtási néhány parancsfájl futtatása a projekt és az fut Between néhány módosítást. Ehhez az asztali alkalmazásban vagy az Azure parancssori felület használatával a `az ml experiment submit` parancsot. További információkért lásd: a [zárolásának Iris oktatóanyag](tutorial-classifying-iris-part-1.md). Minden egyes futtatásához, ha bármilyen változás történik, a projekt mappában található minden fájl teljes projektmappában pillanatképe véglegesítve lett, és leküldeni a távoli Git-tárház AzureMLHistory nevű ág a /\<GUID projekt\>. Az ágakkal rendelkező és véglegesíti, beleértve a AzureMLHistory megtekintése /\<GUID projekt\> fiókirodai, keresse fel a Team Services Git-tárház URL-cím. 

> [!NOTE] 
> A pillanatkép csak egy parancsprogram végrehajtása előtt véglegesítése. Jelenleg egy adatok prep végrehajtása vagy a Notebook cella végrehajtási nem indítható el, a pillanatkép.

![futtatási előzményei ág](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Célszerű, ha nem működik az előzmények ág Git-parancsok használatával. Akkor lehet, hogy zavarják a futtatási előzményeit. Ehelyett a főágba használja, vagy hozzon létre a többi fiók közben a saját Git műveletekhez.

## <a name="step-5-restore-a-previous-project-snapshot"></a>5. lépés Visszaállítás egy korábbi pillanatképből projekt 
Teljes projektmappában visszaállítása egy előző futtatási előzményei, készítsen pillanatképet a Machine Learning-munkaterület állapotát:
1. A tevékenység (homokóra ikon) látható, válassza ki **futtatása**.
2. Az a **futtatása listában** megtekintéséhez válassza ki a visszaállítani kívánt Futtatás.
3. Az a **futtatása részletes** nézetben jelölje ki **visszaállítása**.

![futtatási előzményei ág](media/using-git-ml-project/restore_project.png)

Másik lehetőségként használhatja a következő parancsokat a Machine Learning-munkaterület Azure CLI ablakban:

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Legyen óvatos, ez a parancs futtatásakor. A parancs végrehajtása a teljes projektmappa felülírja a pillanatkép készült, ha a megadott futtató lett kezdődött el. A projekt az aktuális ág marad. Ez azt jelenti, hogy Ön **összes módosítás elvész.** a jelenlegi projekt mappában.  

Előfordulhat, hogy szeretné véglegesíteni a módosításokat az aktuális ág, ez a művelet végrehajtása előtt a Git segítségével.

## <a name="step-6-use-the-master-branch"></a>6. lépés A főágba használata
Egy véletlenül a a jelenlegi projekt állapota elvesztésének elkerülése érdekében módja a projekt véglegesíti a Git-tárház főágába (vagy bármely olyan fiókja, saját maga hozott létre). Segítségével Git a parancssorból vagy a kedvenc Git ügyfél eszközből a főágba működik. Példa:

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Team Services Git repo master branch.
$ git push origin master
```

Most már biztonságosan visszaállíthatja a projekt egy korábbi pillanatkép 5. lépés végrehajtásával. Akkor bármikor visszatérhet, amelyeket az imént készített a a főágba.

## <a name="authentication"></a>Hitelesítés
Ha használ, csak a futtatási előzményei funkciókat a Machine Learning projekt pillanatfelvételeket, és állítsa vissza őket, nem kell foglalkoznia a Git-tárház hitelesítés. Hitelesítési kezeli a Machine Learning kísérletezhet szolgáltatásrétegben.

Azonban ha a saját Git eszközök használatával felügyeli a verziókövetés, szeretné kezelni a távoli Git-tárház Team Services a hitelesítést. A gépi tanulásban a távoli Git-tárház bekerül a helyi tárház távoli Git mappaként a HTTPS protokoll használatával. Ez azt jelenti, hogy a távoli Git-parancsok (például a lekérés és küldés) elküldésekor meg kell adnia a felhasználónevet és jelszót vagy személyes hozzáférési jogkivonat. A Team Services Git-tárház személyes hozzáférési jogkivonat létrehozásához kövesse a [személyes hozzáférési jogkivonat segítségével hitelesítheti a](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate).

## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan [az Team tudományos folyamat segítségével a projekt struktúra](how-to-use-tdsp-in-azure-ml.md).
