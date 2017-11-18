---
title: "Az Azure Machine Learning a munkaterületet üzemeltető projekt Git-tárház használata |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure Machine Learning munkaterület projektek Git-tárház együtt használni."
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: c91eadd69eaf16b2496f4d7247e5b0121904e172
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Az Azure Machine Learning-munkaterület projektek Git-tárház használata
Ez a dokumentum bemutatja, hogyan Azure Machine Learning-munkaterület használja az adatok tudományos kísérletben reprodukálhatóság biztosítására Git. Rendelje hozzá a projekt Git-tárház felhő útmutatást is biztosítja.

## <a name="introduction"></a>Bevezetés
Az Azure Machine Learning-munkaterület célja az Git integráció az alapoktól fel. Új projekt létrehozásakor a projektmappa, automatikusan "Git-inicializálása" egy helyi Git-tárház (tárház) míg egy második rejtett helyi Git-tárház is létrejön, melynek egy ágat nevű _AzureMLHistory / < project_GUID >_ számára nyomon követjük, hogy minden egyes végrehajtása a projekt módosításait. 

Az Azure ML projekt társít egy Git-tárház, a Visual Studio Team szolgáltatás (VSTS) project központja üzemelteti lehetővé teszi, hogy automatikus verziókezelést helyileg és távolról. Ez a társítás lehetővé teszi, hogy birtokában bárki hozzáférést a távoli tárház töltse le a legfrissebb forráskód (központi) egy másik számítógépre.  

> [!NOTE]
> VSTS rendelkezik saját Azure Machine Learning kísérletezhet szolgáltatás független hozzáférés-vezérlési listája. Felhasználói hozzáférés Git-tárház és az Azure ML munkaterület vagy projekt változhat, és előfordulhat, hogy kell kezelni. Ezért ha meg szeretné osztani egy csapattag számára, beleértve a kódot a hozzáférési szint, kívül csak az Azure ML projekt megosztásához a munkaterület kell explicit módon hozzáférést biztosíthat neki megfelelő a VSTS Git-tárház. 

A Git, akkor is kezelheti a verziókövetés explicit módon a _fő_ fiókirodai vagy az adattárban lévő többi fiók közben létrehozása. Használhatja a helyi Git-tárház, és akkor is is leküldése a távoli Git-tárház Ha kiépítve.

Ez az ábra egy VSTS Git-tárház és az Azure ML projektek közötti kapcsolatot ábrázolja:

![AML Git](media/using-git-ml-project/aml_git.png)

Egy távoli Git-tárház használatának megkezdéséhez kövesse az alábbi alapvető utasításokat.

> [!NOTE]
> Jelenleg Azure Machine Learning csak támogatja a Git tárházak VSTS-fiókok. Általános Git repók (például a Githubon és stb.) támogatása a jövőben tervezett.

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>1. lépés Az Azure ML kísérletezhet-fiók létrehozása
Ha nem már, hozzon létre egy Azure ML kísérletezhet fiókot és az Azure ML munkaterület alkalmazás telepítéséhez. A további részleteket a [telepítse, és hozzon létre a gyors üzembe helyezés](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>2. lépés A csapatprojekt létrehozni, vagy egy meglévő csapatprojekt
A [Azure-portálon](https://portal.azure.com/), hozzon létre egy új **Csapatprojekt**.
1. Kattintson a**+**
2. Keresse meg **"Vonja össze a projekt"**
3. Adja meg a szükséges adatokat.
    - Name: A csoport nevét.
    - Verziókövetés: **Git**
    - Előfizetés: A egy olyan Azure Machine Learning kísérletezhet fiókkal.
    - Hely: Ideális esetben olyan régióhoz, amelynek mérete megközelítőleg az Azure Machine Learning kísérlet erőforrások maradnak.
4. Kattintson a **Create** (Létrehozás) gombra. 

![Hozzon létre egy Csapatprojekt Azure-portálon](media/using-git-ml-project/create_vsts_team.png)


> [!TIP]
> Ügyeljen arra, hogy jelentkezzen be az Azure Machine Learning-munkaterület eléréséhez használt Azure Active Directory (AAD) fiók. Ellenkező esetben az új csapatprojektbe előfordulhat, hogy vége felfelé alatt a megfelelő Bérlőazonosító és Azure Machine Learning előfordulhat, hogy nem található. Ebben az esetben meg kellene a parancssori felületen, és adja meg a VSTS-token.

A Csapatprojekt létrehozása után készen áll a következő lépésének.

A közvetlenül az imént létrehozott Csapatprojekt lépjen, az URL-cím: `https://<team_project_name>.visualstudio.com`.

> [!NOTE]
> Azure Machine Learning jelenleg csak üres Git repók rendelkező nincs főághoz támogatja. A parancssori felületről, használhatja a – force argumentum először törli a főágba. 

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>3. lépés Egy távoli Git-tárház egy új Azure ML-projekt létrehozása
Indítsa el az Azure ML munkaterületet, és hozzon létre egy új projektet. A Git-tárház mezőbe beírja a 2. lépésben kapott VSTS Git-tárház URL. Általában néz ki:`http://<vsts_account_name>.visualstudio.com/_git/<project_name>`

![Azure ML-projekt Git-tárház létrehozása](media/using-git-ml-project/create_project_with_git_rep.png)

Most egy új Azure ML-projekt létrehozása távoli Git-tárház integrációs engedélyezett és készen áll. A projektmappa Git-inicializálása egy helyi Git-tárház mindig. És a Git _távoli_ véglegesítések továbbíthatja azokat a távoli Git-tárház, a távoli VSTS Git-tárház beállítása.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>3/a. lépés. Rendelje hozzá egy meglévő Azure ML-projektet egy VSTS Git-tárház
Szükség esetén is az Azure ML projekt nélkül a VSTS Git-tárház létrehozása, és csak a helyi Git-tárház futtatási előzményei a pillanatképek támaszkodnak. És társíthatja egy VSTS Git-tárház később a meglévő Azure ML-projekt, a következő parancsot:

```azurecli
# make sure you are in the project path so CLI has context of your current project
$ az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name>
```

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>4. lépés A Git-tárház projekt pillanatkép rögzítése
Most a projekt végre lehet hajtani néhány fut, ellenőrizze-e egyes módosítások között a futtatják. Ehhez az asztali alkalmazást, vagy a parancssori felület használatával `az ml experiment submit` parancsot. További információkért kövesse a [zárolásának Iris oktatóanyag](tutorial-classifying-iris-part-1.md). Minden egyes futtatásához, ha bármi is módosul a projekt mappában található fájlokat a végrehajtott teljes projektmappában pillanatképet véglegesítve lett, és azokat a távoli Git-tárház nevű ág a leküldött `AzureMLHistory/<Project_GUID>`. A elágazásokat, és véglegesíti a VSTS Git-tárház URL-cím tallózással tekintheti meg és a fiókirodákban található. 

![futtatási előzményei ág](media/using-git-ml-project/run_history_branch.png)

Vegye figyelembe, hogy jobb működés nem lehetséges az előzmények ág magát. Ennek során lehet, hogy mess a futtatási előzményei. Főághoz használja, vagy a saját Git műveletek inkább hozzon létre a többi fiók közben.

## <a name="step-5-restore-a-previous-project-snapshot"></a>5. lépés Visszaállítás egy korábbi pillanatképből projekt 
Állapotba való visszaállításához a teljes projekt mappát az előző futtatási előzményei projekt állapot pillanatkép, az Azure ML munkaterület:
1. Kattintson a **futtatása** sáv (üveghatású órás ikon) tevékenységet.
2. Az a **futtatása listában** megtekintéséhez kattintson a visszaállítani kívánt futtatáskor.
3. Az a **futtatása részletes** megtekintéséhez kattintson a **visszaállítása**.

![futtatási előzményei ág](media/using-git-ml-project/restore_project.png)

Másik lehetőségként használhatja a következő parancsot az Azure ML munkaterület parancssori ablakból.

```azurecli
# discover the run I want to restore snapshot from:
$ az ml history list -o table

# restore the snapshot from a particular run
$ az ml project restore --run-id <run_id>
```

Ez a parancs végrehajtásával teljes projektmappában felülírja azt a pillanatkép készítésekor rendszert adott futtató lett kezdődött el. De az aktuális ág a projekt megvalósításában. Ez azt jelenti, hogy a rendszer **összes módosítás elvész.** a jelenlegi projekt mappában. Úgy adja legyen nagyon óvatos Ez a parancs futtatásakor.

## <a name="step-6-use-the-master-branch"></a>6. lépés A főágba használata
Egy véletlenül elkerülése érdekében a jelenlegi projekt állapot elvesztése módja a projekt véglegesítenie kell a főágba (vagy bármely készítésű ág) a Git-tárház. Git közvetlenül használható, ha a parancssor (vagy a más kedvenc Git ügyfél tetszőleges eszköz általi) az a főágba való működésre. Példa:

```
# make sure you are on the master branch (or branch of your choice)
$ git checkout master

# stage all changes
$ git add -A

# commit all changes locally on the master branch
$ git commit -m 'this is my updates so far'

# push changes into the remote VSTS Git repo master branch.
$ git push origin master
```

Most már biztonságosan projekt visszaállíthatja egy korábbi pillanatkép alábbi 5. lépés, hogy tudnák, hogy Ön is bármikor visszatérhet a véglegesítés, csak végrehajtott fő ág.

## <a name="authentication"></a>Authentication
Ha csak az Azure ml futtatási előzményei funkciók projekt pillanatképek készítése és visszaállítását, nem kell aggódnia a Git-tárház hitelesítés. A kísérleti szolgáltatási réteg van végrehajtott fontos.

Azonban ha a saját Git eszközök használatával felügyeli a verziókövetés, akkor megfelelően kezelje a távoli Git-tárház VSTS a hitelesítést. Ez azt jelenti, hogy szüksége lesz ahhoz, hogy távoli Git-tárház elleni Git-parancsokat adhat ki a helyi számítógépen a Git-tárház hitelesítés beállítása. 

Az ehhez a legkönnyebben hozzon létre egy SSH-kulcspárral és a nyilvános kulcs része feltölti a Git-tárház biztonsági beállításait.

### <a name="generate-ssh-key"></a>SSH-kulcs létrehozása 
Először hozzuk létre az SSH-kulcsok a számítógép két.

#### <a name="on-windows"></a>A Windows esetén:
Indítsa el a Git grafikus felhasználói felület egy asztali alkalmazás Windows és a _súgó_ menüben kattintson a _megjelenítése SSH-kulcsának_.

![SSH kulcs](media/using-git-ml-project/git_gui.png)

SSH másolja a vágólapra.

#### <a name="on-macos"></a>A macOS:
Gyorsműveletek parancs-rendszerhéjból:
```
# generate the SSH key
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# start the SSH agent in the background
$ eval "$(ssh-agent -s)"

# add newly generated SSH key to the SSH agent
$ ssh-add -K ~/.ssh/id_rsa

# display the public key so you can copy it.
$ more ~/.ssh/id_rsa.pub
```
Itt talál további részleteket lépéseket [GitHub cikkben](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).

### <a name="upload-public-key-to-git-repo"></a>Töltse fel a nyilvános kulcsot a Git-tárház
Nyissa meg a Visual Studio-fiók kezdőlapon: https://<vsts_account_name>.visualstudio.com és a bejelentkezéshez, majd kattintson a biztonsági csoportban a profilképet.

Adja meg az SSH nyilvános kulcsát, illessze be a nyilvános SSH-kulcsot, az előző lépésben kapott, és adjon neki egy nevet. Itt több kulcs is hozzáadhat.

Most helyileg szemben a remove-tárház Git-parancsokat adhat ki további explicit hitelesítés szükséges.

### <a name="read-more"></a>További információk
Kövesse az alábbi két cikkek (mindkét megközelítés együttműködhet) további részleteket a VSTS távoli Git-tárház helyi hitelesítés engedélyezése.
- [SSH hitelesítés használata](https://www.visualstudio.com/en-us/docs/git/use-ssh-keys-to-authenticate)
- [Git hitelesítőadat-kezelő használata](https://www.visualstudio.com/en-us/docs/git/set-up-credential-managers)


## <a name="next-steps"></a>Következő lépések
A projekt struktúra rendszerezése, olvassa el az Team tudományos folyamat használata [szerkezeti TDSP a projekthez](how-to-use-tdsp-in-azure-ml.md)
