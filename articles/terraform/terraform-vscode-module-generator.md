---
title: Az Azure Terraform Visual Studio Code modulgenerátor
description: Útmutató Terraform-alapsablon létrehozásához a Yeoman használatával.
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuális gép, azure, yeoman
author: v-mavick
manager: jeconnoc
ms.author: v-mavick
ms.topic: tutorial
ms.date: 09/12/2018
ms.openlocfilehash: 513b123c44cf2cd37cf81a91e0d2da9599eb1fcd
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396355"
---
# <a name="create-a-terraform-base-template-using-yeoman"></a>Alapszintű Terraform-sablon létrehozása a Yeoman használatával

A [Terraform](https://docs.microsoft.com/azure/terraform/
) egyszerű módot kínál infrastruktúra létrehozásához az Azure-ban. A [Yeoman](http://yeoman.io/) nagyban megkönnyíti a Terraform-modulokat készítő modulfejlesztő feladatát, ugyanakkor *ajánlott eljárások* kitűnő keretrendszerét is nyújtja.

Ebben a cikkben a Yeoman modulgenerátor alapszintű Terraform-sablon létrehozásához való használatával ismerkedhet meg.

## <a name="prerequisites"></a>Előfeltételek

- Windows 10, Linux vagy macOS 10.10+ rendszerű számítógép.
- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
- **Visual Studio Code**: A Yeoman generátor által létrehozott fájlokat a [Visual Studio Code](https://www.bing.com/search?q=visual+studio+code+download&form=EDGSPH&mkt=en-us&httpsmsn=1&refig=dffc817cbc4f4cb4b132a8e702cc19a3&sp=3&ghc=1&qs=LS&pq=visual+studio+code&sk=LS1&sc=8-18&cvid=dffc817cbc4f4cb4b132a8e702cc19a3&cc=US&setlang=en-US) használatával fogjuk megvizsgálni. Ön használhatja a saját maga által választott kódszerkesztőt is.
- **Terraform**: A Yeoman által létrehozott modul futtatásához szüksége lesz a [Terraform](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure ) egy telepített példányára.
- **Docker**: A [Dockert](https://www.docker.com/get-started) a Yeoman generátor által létrehozott modul futtatásához fogjuk használni. (Ha úgy kívánja, a Docker helyett a Rubyt is használhatja a mintamodul futtatásához.)
- **Go programozási nyelv**: Szüksége lesz a [Go](https://golang.org/) telepítésére, ugyanis a Yeoman által generált tesztesetek Go nyelven vannak megírva.

>[!NOTE]
>Ebben az oktatóanyagban az eljárások többsége parancssori bevitelt is igényel. Az itt leírt lépések minden operációs rendszerre és parancssori eszközre érvényesek. Példáinkban a PowerShell használata mellett döntöttünk. Ön azonban számos más eszköz, például a Git Bash, a Windows parancssor, és a Linux vagy a macOS parancssora közül választhat.

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

    >[!NOTE]
    >Az <új könyvtár neve> részt írja át az új könyvtár nevére. Ebben a példában az új könyvtárnak a `GeneratorDocSample` nevet adtuk.

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
        >Írja be a `y` (igen) kifejezést. Ha az **n** válaszlehetőséget választja, akkor a generált modul csak a natív módban való futtatást fogja támogatni.

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

- **build**: A main.tf fájl formázását ellenőrzi.
- **unit**: A modul generált váza nem tartalmaz egységteszthez alkalmas kódot. Ha egységtesztet is meg kíván adni, akkor ahhoz itt írhatja be a kódot.
- **e2e**: A modul teljes körű tesztelését futtatja le.

### <a name="test"></a>test

- A tesztesetek Go nyelven vannak megírva.
- A tesztkódok mindegyike teljes körű teszt.
- A teljes körű tesztek a **fixture** alatt definiált összes elemet megkísérlik létrehozni a Terraform használatával, majd összehasonlítják a **template_output.go** fájlban lévő kimenetet az előre megadott, elvárt értékekkel.
- **Gopkg.lock** és **Gopkg.toml**: Definiálja a függőségeket. 

## <a name="test-the-module-using-docker"></a>A modul tesztelése a Docker használatával

>[!NOTE]
>Példánkban a modult helyi modulként, az Azure tényleges érintése nélkül futtatjuk.

### <a name="confirm-docker-is-installed-and-running"></a>A Docker telepítésének és futásának ellenőrzése

Egy parancssorba írja be a következőt: `docker version`.

![docker version](media/terraform-vscode-module-generator/ymg-docker-version.png)

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

1. Írja be a `bundle install` (igen) kifejezést.

    Várja meg a **Bundle complete** üzenetet, majd térjen rá a következő lépésre.

1. Írja be a `rake build` (igen) kifejezést.

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="perform-the-end-to-end-test"></a>A teljes körű teszt végrehajtása

1. Írja be a `rake e2e` (igen) kifejezést.

1. Rövid idő múlva megjelenik a **PASS** üzenet.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. A teljes körű teszt befejezéséhez írja be az `exit` parancsot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Visual Studio Code Azure Terraform bővítményének telepítése és használata.](https://docs.microsoft.com/azure/terraform/terraform-vscode-extension)
