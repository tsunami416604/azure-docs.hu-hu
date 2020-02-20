---
title: Oktatóanyag – Terraform alapsablon létrehozása az Azure-ban a Yeoman használatával
description: Megtudhatja, hogyan hozhat létre Terraform alapszintű sablont az Azure-ban a Yeoman használatával.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 82c3f5e640789547abb716b55959e1821a61e6d0
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472145"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Oktatóanyag: Terraform-alapú alapsablon létrehozása az Azure-ban a Yeoman használatával

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a [Terraform](/azure/terraform/) és a [Yeoman](https://yeoman.io/)kombinációját. A Terraform egy eszköz az Azure-beli infrastruktúra létrehozásához. A Yeoman megkönnyíti a Terraform-modulok létrehozását.

Ebből a cikkből megtudhatja, hogyan hajthatja végre a következő feladatokat:
> [!div class="checklist"]
> * Hozzon létre egy alapszintű Terraform-sablont a Yeoman modul Generator használatával.
> * Tesztelje a Terraform-sablont két különböző módszer használatával.
> * Futtassa a Terraform modult egy Docker-fájl használatával.
> * Futtassa a Terraform modult natív módon a Azure Cloud Shellban.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
- **Visual Studio Code**: töltse le a platformhoz készült [Visual Studio Code](https://code.visualstudio.com/download) -ot.
- **Terraform**: [telepítse a Terraform](terraform-install-configure.md) -t a Yeoman által létrehozott modul futtatásához.
- **Docker**: [telepítse a Docker](https://www.docker.com/get-started) -t a Yeoman-generátor által létrehozott modul futtatásához.
- **Go programozási nyelv**: [telepítse a go](https://golang.org/) -t Yeoman által generált tesztelési esetekben a go nyelv használatával.

>[!NOTE]
>Az oktatóanyagban szereplő eljárások többsége magában foglalja a parancssori felületet. Az ismertetett lépések az összes operációs rendszerre és parancssori eszközre érvényesek. A példákban a PowerShell a helyi környezethez és a git bash a Cloud Shell-környezethez lett kiválasztva.

## <a name="prepare-your-environment"></a>A környezet előkészítése

### <a name="install-nodejs"></a>Node.js telepítése

A Terraform Cloud Shellben való használatához [telepítenie kell a Node.js](https://nodejs.org/en/download/) 6.0-s vagy újabb verzióját.

>[!NOTE]
>A Node.js telepítésének ellenőrzéséhez nyisson meg egy terminálablakot, és írja be a `node --version` parancsot.

### <a name="install-yeoman"></a>A Yeoman telepítése

Futtassa az alábbi parancsot:

```bash
npm install -g yo
```

![A Yeoman telepítése](media/terraform-vscode-module-generator/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>A Terraform-modulhoz készült Yeoman-sablon telepítése

Futtassa az alábbi parancsot:

```bash
npm install -g generator-az-terra-module
```

![A generator-az-terra-module projekt telepítése](media/terraform-vscode-module-generator/ymg-pm-install-generator-module.png)

A Yeoman telepítésének ellenőrzéséhez futtassa a következő parancsot:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Könyvtár létrehozása a Yeoman által generált modulhoz

A Yeoman sablon az aktuális könyvtárban hozza létre a fájlokat. Ehhez Önnek kell létrehoznia egy könyvtárat.

Ezt az üres könyvtárat a $GOPATH/src alatt kell elhelyeznie. Az elérési úttal kapcsolatos további információkért tekintse meg a [GOPATH beállítását](https://github.com/golang/go/wiki/SettingGOPATH)ismertető cikket.

1. Navigáljon arra a szülő könyvtárra, amelyből létre szeretne hozni egy új könyvtárat.

1. Futtassa a következő parancsot a helyőrző cseréjével. Ebben a példában a rendszer egy `GeneratorDocSample` könyvtár nevét használja.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Navigáljon az új könyvtárra:

    ```bash
    cd <new-directory-name>
    ```

    ![Belépés az új könyvtárba](media/terraform-vscode-module-generator/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>Alapmodul-sablon létrehozása

1. Futtassa az alábbi parancsot:

    ```bash
    yo az-terra-module
    ```

1. Adja meg az alábbi információkat a képernyőn megjelenő utasítások alapján:

    - **Terraform-modul projekt neve** – a példában a `doc-sample-module` érték szerepel.

        ![Projektnév](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Kívánja a Docker-lemezképfájl belefoglalását?** – Adja meg a `y`. Ha a `n`lehetőséget választja, a generált modul kódja csak a natív módban történő futtatást fogja támogatni.

        ![Docker-lemezképfájl belefoglalása?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. A könyvtár tartalmának listázása a létrehozott fájlok megtekintéséhez:

    ```bash
    ls
    ```

    ![A létrehozott fájlok listázása](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>A generált modul kódjának vizsgálata

1. Indítsa el a Visual Studio Code-ot

1. A menüsávon kattintson a **Fájl > Mappa megnyitása** elemre, majd válassza ki a létrehozott könyvtárat.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

A Yeoman modul létrehozója a következő fájlokat hozta létre. További információ ezekről a fájlokról és azok használatáról: [Terratest a Terraform-modulokban.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster)

- `main.tf` – egy `random-shuffle`nevű modult határoz meg. A bemenet egy `string_list`. Kimenete a permutációk száma.
- `variables.tf` – meghatározza a modul által használt bemeneti és kimeneti változókat.
- `outputs.tf` – meghatározza a modul kimeneteit. Itt a `random_shuffle`által visszaadott érték, amely egy beépített, Terraform modul.
- `Rakefile` – meghatározza a létrehozás lépéseit. Ilyen lépések többek között:
    - `build` – ellenőrzi a main.tf fájl formátumát.
    - `unit` – a generált modul csontváza nem tartalmaz kódot az egység teszteléséhez. Ha egységtesztet is meg kíván adni, akkor ahhoz itt írhatja be a kódot.
    - `e2e` – a modul végpontok közötti tesztelését futtatja.
- `test`
    - A tesztesetek Go nyelven vannak megírva.
    - A tesztkódok mindegyike teljes körű teszt.
    - A végpontok közötti tesztek megkísérlik kiépíteni a `fixture`alatt definiált összes elemet. A `template_output.go` fájlban lévő eredményeket a rendszer az előre definiált várt értékekkel hasonlítja össze.
    - `Gopkg.lock` és `Gopkg.toml`: meghatározza a függőségeket. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Az új Terraform-modul tesztelése Docker-fájl használatával

Ez a szakasz bemutatja, hogyan tesztelheti a Terraform modult egy Docker-fájl használatával.

>[!NOTE]
>Ez a példa helyileg futtatja a modult; nem az Azure-ban.

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

    ![Sikeres buildet jelző üzenet](media/terraform-vscode-module-generator/ymg-successfully-built.png)

1. A parancssorban adja meg a `docker image ls` a létrehozott modul `terra-mod-example` listájának megjelenítéséhez.

    ![Az új modult tartalmazó lista](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Írja be a `docker run -it terra-mod-example /bin/sh` (igen) kifejezést. A `docker run` parancs futtatása után a Docker-környezetben van. Ezen a ponton a `ls` parancs használatával derítheti fel a fájlt.

    ![Fájlok listája a Docker-ben](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>A modul felépítése

1. Futtassa az alábbi parancsot:

    ```bash
    bundle install
    ```

1. Futtassa az alábbi parancsot:

    ```bash
    rake build
    ```

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>A végpontok közötti teszt futtatása

1. Futtassa az alábbi parancsot:

    ```bash
    rake e2e
    ```

1. Rövid idő múlva megjelenik a **PASS** üzenet.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Adja meg `exit` a teszt befejezéséhez és a Docker-környezetből való kilépéshez.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Modul létrehozása és tesztelése a Yeoman Generator használatával Cloud Shell

Ebben a szakaszban a Yeoman-generátor használatával lehet modulokat létrehozni és tesztelni Cloud Shell. A Docker-fájl használata helyett a Cloud Shell használata jelentősen leegyszerűsíti a folyamatot. A Cloud Shell használatával a következő termékek mindegyike előre telepítve van:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Cloud Shell-munkamenet elindítása

1. Azure Cloud Shell munkamenet elindítása a [Azure Portal](https://portal.azure.com/), a [shell.azure.com](https://shell.azure.com)vagy a [Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/)használatával.

1. Megnyílik **az üdvözli Azure Cloud Shell** lap. Válassza a **bash (Linux)** lehetőséget.

    ![Az Azure Cloud Shell üdvözlőképernyője](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Ha még nem állította be az Azure-tárfiókot, a következő képernyő jelenik meg. Kattintson a **Create storage** (Tároló létrehozása) gombra.

    ![Nincs csatlakoztatott tároló](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Az Azure Cloud Shell az előzőleg kiválasztott rendszerhéjban indul el, és megjeleníti az imént létrehozott felhőalapú meghajtó adatait.

    ![A felhőalapú meghajtó létrejött](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Készítse elő a Terraform modult tároló könyvtárat

1. Ezen a ponton a Cloud Shell már konfigurálta az GOPATH a környezeti változókban. Az elérési út megtekintéséhez írja be a `go env`.

1. Hozza létre a $GOPATH könyvtárat, ha még nem létezik: írja be a `mkdir ~/go`.

1. Hozzon létre egy könyvtárat a $GOPATH könyvtárán belül. Ez a könyvtár az ebben a példában létrehozott különböző Project-címtárak tárolására szolgál. 

    ```bash
    mkdir ~/go/src
    ```

1. Hozzon létre egy könyvtárat, amely a helyőrző helyett a Terraform modult fogja tárolni. Ebben a példában a rendszer egy `my-module-name` könyvtár nevét használja.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Navigáljon a modul könyvtárába: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Terraform-modul létrehozása és tesztelése

1. Futtassa az alábbi parancsot, és kövesse az utasításokat. Ha a rendszer megkérdezi, hogy szeretné-e létrehozni a Docker-fájlokat, adja meg `N`.

    ```bash
    yo az-terra-module
    ```

1. A függőségek telepítéséhez futtassa a következő parancsot:

    ```bash
    bundle install
    ```

1. Futtassa a következő parancsot a modul összeállításához:

    ```bash
    rake build
    ```

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. Futtassa a következő parancsot a szöveg futtatásához:

    ```bash
    rake e2e
    ```

    ![Tesztek eredményeinek továbbítása](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Telepítse és használja az Azure Terraform Visual Studio Code bővítményt](/azure/terraform/terraform-vscode-extension).