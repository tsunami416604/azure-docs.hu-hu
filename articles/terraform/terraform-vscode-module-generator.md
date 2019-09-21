---
title: Terraform-alapú alapsablon létrehozása az Azure-ban a Yeoman használatával
description: Megtudhatja, hogyan hozhat létre Terraform alapszintű sablont az Azure-ban a Yeoman használatával.
services: terraform
ms.service: azure
keywords: terraform, devops, virtuális gép, azure, yeoman
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 7a628eb02170346a826cab19498d6fdf40cebddd
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173383"
---
# <a name="create-a-terraform-base-template-in-azure-using-yeoman"></a>Terraform-alapú alapsablon létrehozása az Azure-ban a Yeoman használatával

A [Terraform](https://docs.microsoft.com/azure/terraform/
) egyszerű módot kínál infrastruktúra létrehozásához az Azure-ban. A [Yeoman](https://yeoman.io/) nagyban megkönnyíti a Terraform-modulokat készítő modulfejlesztő feladatát, ugyanakkor *ajánlott eljárások* kitűnő keretrendszerét is nyújtja.

Ebben a cikkben a Yeoman modulgenerátor alapszintű Terraform-sablon létrehozásához való használatával ismerkedhet meg. Ezután megtudhatja, hogyan tesztelheti az új Terraform-sablont két különböző módszer használatával:

- Futtassa a Terraform modult egy, a cikkben létrehozott Docker-fájl használatával.
- A Terraform modult natív módon futtathatja Azure Cloud Shellban.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
- **Visual Studio Code**: A Yeoman-generátor által létrehozott fájlokat a [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) használatával vizsgáljuk meg. Ön használhatja a saját maga által választott kódszerkesztőt is.
- **Terraform**: A Yeoman által létrehozott modul futtatásához szüksége lesz a [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) telepítésére.
- **Docker**: A Yeoman-generátor [](https://www.docker.com/get-started) által létrehozott modul futtatásához a Docker-t fogjuk használni. (Ha úgy kívánja, a Docker helyett a Rubyt is használhatja a mintamodul futtatásához.)
- **Go programozási nyelv**: Telepítenie kell a [Go](https://golang.org/) -t, mert a Yeoman által generált tesztelési esetek a Go-ban íródnak.

>[!NOTE]
>Ebben az oktatóanyagban az eljárások többsége parancssori bevitelt is igényel. Az itt leírt lépések minden operációs rendszerre és parancssori eszközre érvényesek. A példákban a PowerShell használatát választotta a helyi környezetekhez és a git bash for Cloud Shell-környezethez.

## <a name="prepare-your-environment"></a>A környezet előkészítése

### <a name="install-nodejs"></a>A Node.js telepítése

A Terraform Cloud Shellben való használatához [telepítenie kell a Node.js](https://nodejs.org/en/download/) 6.0-s vagy újabb verzióját.

>[!NOTE]
>A Node.js telepítésének ellenőrzéséhez nyisson meg egy terminálablakot, és írja be a `node --version` parancsot.

### <a name="install-yeoman"></a>A Yeoman telepítése

Egy parancssorba írja be a következőt: `npm install -g yo`

![A Yeoman telepítése](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>A Terraform-modulhoz készült Yeoman-sablon telepítése

Egy parancssorba írja be a következőt: `npm install -g generator-az-terra-module`.

![A generator-az-terra-module projekt telepítése](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

>[!NOTE]
>A Yeoman telepítésének ellenőrzéséhez egy terminálablakba írja be a `yo --version` parancsot.

### <a name="create-an-empty-folder-to-hold-the-yeoman-generated-module"></a>Üres könyvtár létrehozása a Yeoman által generált modul tárolására

A Yeoman-sablon az **aktuális könyvtárban** generálja a fájlokat. Ehhez Önnek kell létrehoznia egy könyvtárat.

>[!Note]
>Ezt az üres könyvtárat a $GOPATH/src alatt kell elhelyeznie. Ennek végrehajtásához [itt](https://github.com/golang/go/wiki/SettingGOPATH) talál útmutatást.

Egy parancssorban hajtsa végre az alábbiakat:

1. Lépjen a létrehozni kívánt új, üres könyvtár tartalmazására szánt szülőkönyvtárba.
1. Írja be a `mkdir <new-directory-name>` (igen) kifejezést.

    > [!NOTE]
    > Cserélje `<new-directory-name>` le az nevet az új könyvtár nevére. Ebben a példában az új könyvtárnak a `GeneratorDocSample` nevet adtuk.

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Lépjen be az új könyvtárba a `cd <new directory's name>` parancs begépelésével, majd az **Enter** billentyű lenyomásával.

    ![Belépés az új könyvtárba](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

    >[!NOTE]
    >Arról, hogy a könyvtár üres, az `ls` parancs kiadásával győződhet meg. A parancs kimeneteként egy fájlnak sem szabad megjelennie.

## <a name="create-a-base-module-template"></a>Alapmodul-sablon létrehozása

Egy parancssorban hajtsa végre az alábbiakat:

1. Írja be a `yo az-terra-module` (igen) kifejezést.

1. Adja meg az alábbi információkat a képernyőn megjelenő utasítások alapján:

    - *Terraform-modul projekt neve*

        ![Projektnév](media/terraform-vscode-module-generator/ymg-project-name.png)       

        >[!NOTE]
        >Ebben a példában a `doc-sample-module` nevet adtuk meg.

    - *Kívánja a Docker-lemezképfájl belefoglalását?*

        ![Docker-lemezképfájl belefoglalása?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

        >[!NOTE]
        >Írja be a `y` (igen) kifejezést. Ha az **n**lehetőséget választja, a generált modul kódja csak a natív módban történő futtatást fogja támogatni.

3. Az eredményként keletkezett fájlok listáját az `ls` paranccsal tekintheti meg.

    ![A létrehozott fájlok listázása](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>A generált modul kódjának vizsgálata

1. Indítsa el a Visual Studio Code-ot

1. A menüsávon kattintson a **Fájl > Mappa megnyitása** elemre, majd válassza ki a létrehozott könyvtárat.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

Vizsgáljunk meg néhányat a Yeoman modulgenerátor által létrehozott fájlok közül.

>[!Note]
>Ebben a cikkben a main.tf, variables.tf és outputs.tf fájloknak a Yeoman modulgenerátor által létrehozott változatát fogjuk használni. Saját modulok létrehozásakor viszont a saját Terraform-moduljának funkcióihoz igazodva módosítani fogja ezeket a fájlokat. E fájlokat és használatukat a [Terratest Terraform-modulokban](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster) című cikk ismerteti részletesebben.

### <a name="maintf"></a>main.tf

Egy *random-shuffle* nevű modult definiál. A bemenete *string_list*. Kimenete a permutációk száma.

### <a name="variablestf"></a>variables.tf

A modul által használt kimeneti és bemeneti változókat definiálja.

### <a name="outputstf"></a>outputs.tf

A modul kimenetének tartalmát definiálja. Itt ez a beépített **random_shuffle** Terraform-modul által visszaadott érték.

### <a name="rakefile"></a>Rakefile

A fordítási lépéseket határozza meg. Ilyen lépések többek között:

- **Build**: Ellenőrzi a main.tf fájl formátumát.
- **egység**: A generált modul csontváza nem tartalmaz kódot az egység teszteléséhez. Ha egységtesztet is meg kíván adni, akkor ahhoz itt írhatja be a kódot.
- **E2E**: A modul végpontok közötti tesztelését futtatja.

### <a name="test"></a>test

- A tesztesetek Go nyelven vannak megírva.
- A tesztkódok mindegyike teljes körű teszt.
- A teljes körű tesztek a **fixture** alatt definiált összes elemet megkísérlik létrehozni a Terraform használatával, majd összehasonlítják a **template_output.go** fájlban lévő kimenetet az előre megadott, elvárt értékekkel.
- **Gopkg. Lock** és **Gopkg. toml**: Határozza meg a függőségeket. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Az új Terraform-modul tesztelése Docker-fájl használatával

>[!NOTE]
>Példánkban a modult helyi modulként, az Azure tényleges érintése nélkül futtatjuk.

### <a name="confirm-docker-is-installed-and-running"></a>A Docker telepítésének és futásának ellenőrzése

Egy parancssorba írja be a következőt: `docker version`.

![Docker-verzió](media/terraform-vscode-module-generator/ymg-docker-version.png)

A keletkező kimenet igazolja, hogy a Docker telepítve van.

Arról, hogy a Docker valóban fut, a `docker info` paranccsal győződhet meg.

![docker info](media/terraform-vscode-module-generator/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Docker-tároló beállítása

1. Egy parancssorba írja be a következőt:

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    Megjelenik a fordítás sikeres befejezését jelentő **Successfully built** üzenet.

    ![Successfully built](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. A parancssorba írja be a következőt: `docker image ls`.

    A listában megjelenik az újonnan létrehozott *terra-mod-example* modul.

    ![Adattár-eredmények](media/terraform-vscode-module-generator/ymg-repository-results.png)

    >[!NOTE]
    >A modulnak a fenti 1. lépésben kiadott parancsban adta meg a *terra-mod-example* nevet.

1. Írja be a `docker run -it terra-mod-example /bin/sh` (igen) kifejezést.

    Ettől kezdve a Dockerben futtat, és az `ls` paranccsal listázhatja a fájlt.

    ![Docker-fájllista](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>A modul felépítése

1. Írja be a `bundle install` (igen) kifejezést.

    Várja meg a **Bundle complete** üzenetet, majd térjen rá a következő lépésre.

1. Írja be a `rake build` (igen) kifejezést.

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>A végpontok közötti teszt futtatása

1. Írja be a `rake e2e` (igen) kifejezést.

1. Rövid idő múlva megjelenik a **PASS** üzenet.

    ![SIKERES](media/terraform-vscode-module-generator/ymg-pass.png)

1. A `exit` végpontok közötti teszt befejezéséhez és a Docker-környezetből való kilépéshez írja be a parancsot.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Modul létrehozása és tesztelése a Yeoman Generator használatával Cloud Shell

Az előző szakaszban megtanulta, hogyan tesztelheti a Terraform modult egy Docker-fájl használatával. Ebben a szakaszban a Yeoman generátort fogja használni a Cloud Shell-modul létrehozásához és teszteléséhez.

A Docker-fájl használata helyett a Cloud Shell használata jelentősen leegyszerűsíti a folyamatot. Cloud Shell használata:

- Nem kell telepítenie a Node. js-t
- Nem kell telepítenie a Yeoman
- Nem kell telepítenie a Terraform

Ezen elemek mindegyike előre telepítve van Cloud Shellban.

### <a name="start-a-cloud-shell-session"></a>Cloud Shell-munkamenet elindítása

1. Azure Cloud Shell munkamenet elindítása a [Azure Portal](https://portal.azure.com/), a [shell.azure.com](https://shell.azure.com)vagy a [Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/)használatával.

1. Megnyílik **az üdvözli Azure Cloud Shell** lap. Válassza a **bash (Linux)** lehetőséget. (A Power shell nem támogatott.)

    ![Az Azure Cloud Shell üdvözlőképernyője](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >Ebben a példában a Bash (Linux) lehetőséget választottuk.

1. Ha még nem állította be az Azure-tárfiókot, a következő képernyő jelenik meg. Kattintson a **Create storage** (Tároló létrehozása) gombra.

    ![Nincs csatlakoztatott tároló](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Az Azure Cloud Shell az előzőleg kiválasztott rendszerhéjban indul el, és megjeleníti az imént létrehozott felhőalapú meghajtó adatait.

    ![A felhőalapú meghajtó létrejött](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-folder-to-hold-your-terraform-module"></a>Készítse elő a Terraform modult tároló mappát

1. Ezen a ponton a Cloud Shell már konfigurálta az GOPATH a környezeti változókban. Az elérési út megtekintéséhez írja `go env`be a következőt:.

1. Hozzon létre egy $GOPATH mappát, ha még nem létezik: Írja be a `mkdir ~/go` (igen) kifejezést.

1. Hozzon létre egy mappát a $GOPATH mappában: Írja be a `mkdir ~/go/src` (igen) kifejezést. Ezt a mappát fogja használni a rendszer a különböző létrehozott Project-mappák tárolására és rendszerezésére, például `<your-module-name>` a következő lépésben létrehozandó mappára.

1. Hozzon létre egy mappát a Terraform modul tárolásához: Írja be a `mkdir ~/go/src/<your-module-name>` (igen) kifejezést.

    >[!NOTE]
    >Ebben a példában a Mappanév nevet `my-module-name` választottuk.

1. Navigáljon a modul mappájába: Be`cd ~/go/src/<your-module-name>`

### <a name="create-and-test-your-terraform-module"></a>Terraform-modul létrehozása és tesztelése

1. Adja `yo az-terra-module` meg a varázslót, és kövesse a varázsló utasításait.

    >[!NOTE]
    >Ha a rendszer megkérdezi, hogy szeretné-e létrehozni a Docker `N`-fájlokat, megadhatja a következőt:.

1. A `bundle install` függőségek telepítéséhez adja meg a következőt:.

    Várja meg a **Bundle complete** üzenetet, majd térjen rá a következő lépésre.

1. A `rake build` modul létrehozásához adja meg a következőt:.

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. A `rake e2e` végpontok közötti teszt futtatásához írja be a parancsot.

1. Rövid idő múlva megjelenik a **PASS** üzenet.

    ![SIKERES](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Telepítse és használja az Azure Terraform Visual Studio Code bővítményt.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
