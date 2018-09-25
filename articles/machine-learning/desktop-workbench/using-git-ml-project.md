---
title: Az Azure Machine Learning Workbench-projekt Git-adattár használata |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogy egy Git-tárház együttes használata az Azure Machine Learning Workbench-projekt.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 16c102641321117f4776d761aba6c2148d15f1f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995642"
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>A Machine Learning Workbench-projekt Git-adattár használata

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Ismerje meg, hogyan Azure Machine Learning Workbench használja az Git a verziókövetéshez, és megismételhetőség az adatelemzési kísérlet az biztosítja. Ismerje meg, hogyan rendelje hozzá a projekthez egy felhőbeli Git-tárház (adattár).

Machine Learning Workbench alkalmazásban a Git-integrációval lett tervezve. Amikor egy új projektet hoz létre, a projektmappa fájllistájának, automatikusan "Git-inicializálása" helyi Git-adattár. Egy második, rejtett helyi Git-tárház is létrejön egy ágat AzureMLHistory nevű /\<GUID projekt\>. Az ág nyomon követi az egyes végrehajtási projekt módosításait. 

Automatikus verziókezelést, társítása az Azure Machine Learning-projektet egy Git-adattárral való lehetővé teszi a helyileg és távolról. A Git-adattár üzemel az Azure DevOps. Mivel a Machine Learning-projektet egy Git-adattár társítva, bárki, aki hozzáfér a távoli adattárat töltse le a legújabb forráskóddal (központi) egy másik számítógépre.  

> [!NOTE]
> Az Azure DevOps rendelkezik a saját hozzáférés-vezérlési lista (ACL), amely független az Azure Machine Learning-kísérletezés szolgáltatás. Felhasználói hozzáférés Git-adattár és a egy Machine Learning-munkaterületet, vagy a projekt között változhat. Szüksége lehet a hozzáférés kezelésére. 
> 
> Hogy egy csapattag biztosíthat kód szintű hozzáférést a Machine Learning-projektet vagy egyszerűen csak megoszthatja a munkaterülethez, kell biztosítania a felhasználó a megfelelő engedélyekkel ahhoz, hogy az Azure DevOps Git-adattár eléréséhez. 

Kezelheti a Git a verziókövetéshez, használja a master ággal, vagy további ág létrehozása a tárházban. Használja a helyi Git-adattár, és küldje le a távoli Git-tárház kiosztása esetén is.

Ez az ábra az Azure DevOps Git-adattár és a egy Machine Learning-projektet közötti kapcsolatot ábrázolja:

![Az Azure Machine Learning Git](media/using-git-ml-project/aml_git.png)

Első lépésként távoli Git-tárház használatával végezze el a következő szakaszban leírt lépéseket.

> [!NOTE]
> Az Azure Machine Learning jelenleg csak az Azure DevOps-szervezetek támogatja a Git-tárházakat.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>1. lépés Machine Learning-Kísérletezési fiók létrehozása
Hozzon létre egy Machine Learning-kísérletezés-fiókot, és az Azure Machine Learning Workbench alkalmazás telepítéséhez. További információkért lásd: [telepítése és létrehozási rövid útmutató](quickstart-installation.md).

## <a name="step-2-create-an-azure-devops-project-or-use-an-existing-project"></a>2. lépés Az Azure DevOps-projekt létrehozása, vagy használjon egy meglévő projekt
Az a [az Azure portal](https://portal.azure.com/), hozzon létre egy új projektet:
1. Válassza ki **+**.
2. Keresse meg **Team Project**.
3. Adja meg a szükséges adatokat:
    - **Név**: A csoport nevét.
    - **Verziókövetés**: válasszon **Git**.
    - **Előfizetés**: Válasszon ki egy előfizetést, amely rendelkezik egy Machine Learning-kísérletezés-fiókot.
    - **Hely**: ideális esetben válasszon egy közeli régiót, a Machine Learning-kísérletezés-erőforrásokat.
4. Kattintson a **Létrehozás** gombra. 

![Hozzon létre egy projektet az Azure Portalon](media/using-git-ml-project/create_vsts_team.png)

Győződjön meg arról, hogy, jelentkezzen be ugyanazzal a fiókkal az Azure Active Directory (Azure AD), amely a Machine Learning Workbench elérésére használja. Ellenkező esetben a rendszer nem fér hozzá Machine Learning Workbench használatával az Azure AD hitelesítő adatait. Kivétel, ha a parancssor használatával hozzon létre a Machine Learning-projektet, és megadja a személyes hozzáférési tokent a Git-adattár eléréséhez. Bemutatjuk, ez a cikk későbbi részében részletesebben.

Lépjen a létrehozott projekt, használja az URL-cím https://\<projektnév\>. visualstudio.com.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>3. lépés A Machine Learning-projektet és a Git-tárház beállítása

Állítsa be a Machine Learning-projektet, két lehetősége van:
- Hozzon létre egy Machine Learning-projektet, amelyen a távoli Git-adattár
- Rendelje hozzá egy meglévő Machine Learning-projektet az Azure DevOps Git-adattár

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Hozzon létre egy Machine Learning-projektet, amelyen a távoli Git-adattár
Nyissa meg a Machine Learning Workbench, és hozzon létre egy új projektet. Az a **Git-tárház** mezőbe írja be az Azure DevOps Git-adattár URL-CÍMÉT a 2. lépés. Általában néz ki: https://\<Azure DevOps-szervezet nevét\>.visualstudio.com/_git/\<projekt neve\>

![Hozzon létre egy Machine Learning-projektet, amelyen a Git-adattár](media/using-git-ml-project/create_project_with_git_rep.png)

A projekt az Azure parancssori felület (Azure CLI) használatával is létrehozhat. Lehetősége van a személyes hozzáférési tokent beírását. Machine Learning a jogkivonat használatával is hozzáférhetnek a Git-adattár helyett az Azure ad-ben használt hitelesítő adataival:

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Azure DevOps personal access token>
```

> [!IMPORTANT]
> Ha úgy dönt, az üres projekt sablon, a Git-tárház használt előfordulhat, hogy már a master ággal. Machine Learning egyszerűen csak klónozza helyileg a master ágról. A aml_config mappát és egyéb metaadatok soubory projektu hozzáadja a helyi projektmappában. 
>
> Ha úgy dönt, bármely más webesprojekt-sablon, a Git-tárház *nem* már rendelkezik a master ággal. Ha igen, hibaüzenet jelenik meg. Ez esetben használja a `az ml project create` parancsot a projekt létrehozása egy `--force` váltson. Ez törli a fájlokat az eredeti főágban, és lecseréli azokat az új fájlokat a sablonban, hogy az Ön által választott.

Új Machine Learning-projekt jön létre, a Git-tárház távoli integráció engedélyezve van. A projektmappa fájllistájának mindig, mint egy helyi Git-tárház Git-inicializálva. A távoli Git a távoli Azure-fejlesztési és üzemeltetési Git-adattár, így a távoli Git-tárház is véglegesítéseknél van beállítva.

### <a name="associate-an-existing-machine-learning-project-with-an-azure-devops-git-repo"></a>Rendelje hozzá egy meglévő Machine Learning-projektet az Azure DevOps Git-adattár
Hozzon létre egy Machine Learning-projektet az Azure DevOps Git-tárház nélkül, és a helyi Git-tárház futtatási előzmények pillanatképek támaszkodnak. Később is társíthat egy Azure-fejlesztési és üzemeltetési Git-adattár a meglévő Machine Learning-projektet a következő paranccsal:

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Azure DevOps organization name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> A frissítés-tárház műveletet csak a Machine Learning-projektet, amelyen nincs telepítve egy Git-adattár társítva is végezhet. Emellett a Machine Learning Git-adattár társítani, miután nem távolítható el.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>4. lépés A Git-adattárat a projekthez pillanatkép rögzítése
Hajtsa végre a néhány parancsprogramot futtatja a projektet, és hajtson végre néhány módosítást a futtatások között. Ezt megteheti is az asztali alkalmazás vagy Azure parancssori felület használatával a `az ml experiment submit` parancsot. További információkért lásd: a [Írisz osztályozása oktatóanyaghoz](tutorial-classifying-iris-part-1.md). Az egyes futtatások, ha bármilyen változás történik, az minden olyan fájlt a projektmappa fájllistájának a teljes projektmappáról pillanatképét véglegesített, és leküldte a távoli Git-tárház AzureMLHistory nevű ág alapján /\<GUID projekt\>. Az ágak és véglegesítéseket, beleértve a AzureMLHistory megtekintése /\<GUID projekt\> ág, nyissa meg az Azure DevOps Git-adattár URL-CÍMÉT. 

> [!NOTE] 
> A pillanatkép-parancsprogram végrehajtása előtt csak véglegesítése. Jelenleg egy adatelőkészítéshez végrehajtási vagy egy Jegyzetfüzet cella végrehajtás nem aktiválja a pillanatkép.

![Futtatási előzmények ág](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Emellett akkor ajánlott, ha nem működik az előzmények ág Git-parancsok használatával. A zavarhatják a futtatási előzményekben. Ehelyett a főágban vagy hozzon létre további ág számára a saját Git-műveletet.

## <a name="step-5-restore-a-previous-project-snapshot"></a>5. lépés Egy előző projekt-pillanatkép visszaállítása 
A teljes projektmappáról visszaállítása egy korábbi futtatási előzmények a Machine Learning Workbench a pillanatkép állapotát:
1. Sáv (homokóra ikon) a tevékenységben, válassza ki a **futtatások**.
2. Az a **futtatási lista** megtekinteni, válassza ki a visszaállítani kívánt Futtatás.
3. Az a **Futtatás részletei** nézetben válassza **visszaállítása**.

![Futtatási előzmények ág](media/using-git-ml-project/restore_project.png)

Szükség esetén használhatja ki az Azure parancssori felület ablakában, a Machine Learning Workbench a következő parancsokat:

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Legyen óvatos, ez a parancs futtatásakor. Ez a parancs végrehajtása felülírja a teljes projektmappáról pillanatképre, amikor adott futtató kezdődjön volt kerül. A projektet az aktuális ág marad. Ez azt jelenti, hogy Ön **módosítások elvesztése** az aktuális projektmappában.  

A Git használatával az aktuális ágra, a módosítások véglegesítéséhez, a művelet végrehajtása előtt érdemes.

## <a name="step-6-use-the-master-branch"></a>6. lépés A főágat
Egyik módja a jelenlegi projekt állapota véletlen elvesztésének elkerülése érdekében, hogy véglegesíti a projekt a Git-tárház főágába (vagy bármely ágra, amelyet saját maga hozott létre). Használhatja a Git a parancssorból vagy a kedvenc Git-ügyfél eszközről a master ággal a művelethez használandó. Példa:

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Azure DevOps Git repo master branch.
$ git push origin master
```

Most már nyugodtan visszaállíthatja a projekt egy korábbi pillanatkép 5. lépés végrehajtásával. Akkor bármikor visszatérhet, véglegesítésére épp most módosította a master ággal.

## <a name="authentication"></a>Hitelesítés
Ha használ, csak a futtatási előzmények funkciókat pillanatfelvételeket a projektet, és állíthatja vissza őket a Machine Learning, nem kell aggódnia a Git-tárház hitelesítést. Hitelesítés a Machine Learning-kísérletezés szolgáltatás réteg kezeli.

Azonban a saját Git-eszközök használatával verziókövetés kezelése, ha szeretné a távoli Git-adattárból az Azure DevOps ellen a hitelesítésnek a kezelésére. A Machine Learning a távoli Git-tárház bekerül a helyi tárháznak távoli Git-elemként a HTTPS protokoll használatával. Ez azt jelenti, hogy amikor a távoli Git-parancsok (például leküldéses és lekéréses), meg kell adnia a felhasználónevét és jelszavát, vagy a személyes hozzáférési tokent. A személyes hozzáférési tokent az Azure DevOps Git-tárház létrehozásához kövesse a [használják a hitelesítéshez a személyes hozzáférési tokent](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [a csoportos adatelemzési folyamat használata az projektstruktúra rendszerezéséhez](how-to-use-tdsp-in-azure-ml.md).
