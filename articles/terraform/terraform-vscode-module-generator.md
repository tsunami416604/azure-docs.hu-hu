---
title: Oktatóanyag – Terraform alapsablon létrehozása az Azure-ban a Yeoman használatával
description: Ismerje meg, hogyan hozhat létre Terraform alapsablont az Azure-ban a Yeoman használatával.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 82c3f5e640789547abb716b55959e1821a61e6d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472145"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Oktatóanyag: Terraform alapsablon létrehozása az Azure-ban a Yeoman használatával

Ebben a bemutatóban megtudhatja, hogyan kell használni a [Terraform](/azure/terraform/) és a [Yeoman](https://yeoman.io/)kombinációját. A Terraform az Azure-beli infrastruktúra létrehozásának eszköze. A Yeoman megkönnyíti a Terraform modulok létrehozását.

Ebből a cikkből megtudhatja, hogyan kell elvégezni a következő feladatokat:
> [!div class="checklist"]
> * Hozzon létre egy alap Terraform sablont a Yeoman modulgenerátorral.
> * Tesztelje a Terraform sablont két különböző módszerrel.
> * Futtassa a Terraform modult egy Docker-fájl használatával.
> * Futtassa a Terraform modult natív módon az Azure Cloud Shellben.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
- **Visual Studio-kód:** [A Visual Studio-kód letöltése](https://code.visualstudio.com/download) a platformra.
- **Terraform**: [Telepítse a Terraform-ot](terraform-install-configure.md) a Yeoman által létrehozott modul futtatásához.
- **Docker**: Telepítse a [Docker-t](https://www.docker.com/get-started) a Yeoman generátor által létrehozott modul futtatásához.
- **Go programozási nyelv:** [Telepítse go](https://golang.org/) yeoman által generált teszt esetek kód segítségével a Go nyelvet.

>[!NOTE]
>Az oktatóanyag legtöbb eljárása a parancssori felületet érinti. A leírt lépések minden operációs rendszerre és parancssori eszközre vonatkoznak. A példák, PowerShell a helyi környezetben, és a Git Bash a felhőbeli rendszerhéj-környezetben.

## <a name="prepare-your-environment"></a>A környezet előkészítése

### <a name="install-nodejs"></a>A Node.js telepítése

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

A Yeoman telepítése után futtassa a következő parancsot:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Könyvtár létrehozása a Yeoman által létrehozott modulhoz

A Yeoman-sablon az aktuális könyvtárban generálja a fájlokat. Ehhez Önnek kell létrehoznia egy könyvtárat.

Ezt az üres könyvtárat a $GOPATH/src alatt kell elhelyeznie. Erről az elérési útról a [GOPATH beállítása](https://github.com/golang/go/wiki/SettingGOPATH)című témakörben olvashat bővebben.

1. Nyissa meg azt a szülőkönyvtárat, amelyből új könyvtárat szeretne létrehozni.

1. Futtassa a következő parancsot a helyőrző cseréje. Ebben a példában a `GeneratorDocSample` könyvtár nevét használja a lesz használva.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/terraform-vscode-module-generator/ymg-mkdir-GeneratorDocSample.png)

1. Keresse meg az új könyvtárat:

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

    - **Terraform modul projekt neve** `doc-sample-module` - A példa értéke használatos.

        ![Projektnév](media/terraform-vscode-module-generator/ymg-project-name.png)       


    - **Kívánja a Docker-lemezképfájl belefoglalását?** - `y`Írja be a be . Ha a `n`lehetőséget választja, a létrehozott modulkód csak natív módban fogja támogatni a futtatást.

        ![Docker-lemezképfájl belefoglalása?](media/terraform-vscode-module-generator/ymg-include-docker-image-file.png) 

1. A létrehozott fájlok megtekintéséhez sorolja fel a könyvtár tartalmát:

    ```bash
    ls
    ```

    ![A létrehozott fájlok listázása](media/terraform-vscode-module-generator/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>A generált modul kódjának vizsgálata

1. Indítsa el a Visual Studio Code-ot

1. A menüsávon kattintson a **Fájl > Mappa megnyitása** elemre, majd válassza ki a létrehozott könyvtárat.

    ![Visual Studio Code](media/terraform-vscode-module-generator/ymg-open-in-vscode.png)

A következő fájlokat hozta létre a Yeoman modul generátor. Ezekről a fájlokról és azok használatáról a [Terratest a Terraform modulokban című témakörben talál.](https://mseng.visualstudio.com/VSJava/_git/Terraform?path=%2FTerratest%20Introduction.md&version=GBmaster).

- `main.tf`- Meghatározza a `random-shuffle`modul neve . A bemenet `string_list`egy . Kimenete a permutációk száma.
- `variables.tf`- Meghatározza a modul által használt bemeneti és kimeneti változókat.
- `outputs.tf`- Meghatározza, hogy mit ad ki a modul. Itt ez az érték által `random_shuffle`visszaadott , amely egy beépített, Terraform modul.
- `Rakefile`- Meghatározza a build lépéseket. Ilyen lépések többek között:
    - `build`- Érvényesíti a main.tf fájl formázását.
    - `unit`- A generált modul csontváza nem tartalmazza az egységteszt kódját. Ha egységtesztet is meg kíván adni, akkor ahhoz itt írhatja be a kódot.
    - `e2e`- Végigfut a modulon.
- `test`
    - A tesztesetek Go nyelven vannak megírva.
    - A tesztkódok mindegyike teljes körű teszt.
    - A végpontok között végzett tesztek megkísérlik a `fixture`program ban meghatározott összes elem kiépítését. A fájlban `template_output.go` lévő eredményeket a rendszer összehasonlítja az előre meghatározott várt értékekkel.
    - `Gopkg.lock`és `Gopkg.toml`: Meghatározza a függőségeket. 

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Az új Terraform modul tesztelése Docker-fájlhasználatával

Ez a szakasz bemutatja, hogyan tesztelegy Terraform modult egy Docker-fájl használatával.

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

1. A parancssorból `docker image ls` írja be a `terra-mod-example` listában a létrehozott modul megtekintését.

    ![Az új modult tartalmazó lista](media/terraform-vscode-module-generator/ymg-repository-results.png)

1. Írja be a `docker run -it terra-mod-example /bin/sh` (igen) kifejezést. A `docker run` parancs futtatása után a Docker környezetben van. Ezen a ponton a parancs segítségével `ls` felfedezheti a fájlt.

    ![Fájllista a Dockerben](media/terraform-vscode-module-generator/ymg-list-docker-file.png)

### <a name="build-the-module"></a>A modul létrehozása

1. Futtassa az alábbi parancsot:

    ```bash
    bundle install
    ```

1. Futtassa az alábbi parancsot:

    ```bash
    rake build
    ```

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>A végpontok utáni vizsgálat futtatása

1. Futtassa az alábbi parancsot:

    ```bash
    rake e2e
    ```

1. Rövid idő múlva megjelenik a **PASS** üzenet.

    ![PASS](media/terraform-vscode-module-generator/ymg-pass.png)

1. Adja `exit` meg a teszt befejezéséhez, és lépjen ki a Docker-környezetből.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>A Yeoman generátor használata modul létrehozásához és teszteléséhez a Cloud Shellben

Ebben a szakaszban a Yeoman generátor segítségével hozzon létre és teszteljen egy modult a Cloud Shellben. A Cloud Shell használata a Docker-fájl használata helyett nagyban leegyszerűsíti a folyamatot. A Cloud Shell használatával a következő termékek mind előre telepítve vannak:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Cloud Shell-munkamenet indítása

1. Azure Cloud Shell-munkamenet indítása az [Azure Portalon,](https://portal.azure.com/) [shell.azure.com](https://shell.azure.com)vagy az [Azure mobilalkalmazáson](https://azure.microsoft.com/features/azure-portal/mobile-app/)keresztül.

1. **Megnyílik az Üdvözli az Azure Cloud Shell.** Válassza a **Bash (Linux) lehetőséget.**

    ![Az Azure Cloud Shell üdvözlőképernyője](media/terraform-vscode-module-generator/ymg-welcome-to-azure-cloud-shell.png)

1. Ha még nem állította be az Azure-tárfiókot, a következő képernyő jelenik meg. Kattintson a **Create storage** (Tároló létrehozása) gombra.

    ![Nincs csatlakoztatott tároló](media/terraform-vscode-module-generator/ymg-you-have-no-storage-mounted.png)

1. Az Azure Cloud Shell az előzőleg kiválasztott rendszerhéjban indul el, és megjeleníti az imént létrehozott felhőalapú meghajtó adatait.

    ![A felhőalapú meghajtó létrejött](media/terraform-vscode-module-generator/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Könyvtár előkészítése a Terraform modul tárolására

1. Ezen a ponton a Cloud Shell már konfigurálta a GOPATH-ot a környezeti változókban. Az elérési út `go env`megtekintéséhez írja be a be a című beírást.

1. Hozza létre a $GOPATH könyvtárat, ha `mkdir ~/go`még nem létezik: Írja be a t.

1. Hozzon létre egy könyvtárat a $GOPATH könyvtárban. Ez a könyvtár a példában létrehozott különböző projektkönyvtárak tárolására szolgál. 

    ```bash
    mkdir ~/go/src
    ```

1. Hozzon létre egy könyvtárat, hogy tartsa a Terraform modul helyett a helyőrző. Ebben a példában a `my-module-name` könyvtár nevét használja a lesz használva.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Keresse meg a modulkönyvtárat: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Terraform modul létrehozása és tesztelése

1. Futtassa a következő parancsot, és kövesse az utasításokat. Amikor a rendszer megkérdezi, hogy létre kívánja-e hozni a Docker-fájlokat, írja be a . `N`

    ```bash
    yo az-terra-module
    ```

1. A függőségek telepítéséhez futtassa a következő parancsot:

    ```bash
    bundle install
    ```

1. A modul létrehozásához futtassa a következő parancsot:

    ```bash
    rake build
    ```

    ![Rake build](media/terraform-vscode-module-generator/ymg-rake-build.png)

1. A szöveg futtatásához futtassa a következő parancsot:

    ```bash
    rake e2e
    ```

    ![Vizsgálati eredmények](media/terraform-vscode-module-generator/ymg-pass.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Telepítse és használja az Azure Terraform Visual Studio Kód bővítményt.](/azure/terraform/terraform-vscode-extension)