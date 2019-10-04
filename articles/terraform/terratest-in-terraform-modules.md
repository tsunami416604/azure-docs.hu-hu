---
title: Terraform-modulok tesztelése az Azure-ban a Terratest használatával
description: Ismerje meg, hogyan használható a Terratest a Terraform-modulok tesztelésére.
services: terraform
ms.service: azure
keywords: terraform, devops, tárfiók, azure, terratest, egységteszt, integrációs teszt
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 637bb01bff625989e392d5d711ebd5cdef5c0e09
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169630"
---
# <a name="test-terraform-modules-in-azure-by-using-terratest"></a>Terraform-modulok tesztelése az Azure-ban a Terratest használatával

> [!NOTE]
> A cikkben szereplő mintakód nem működik a 0,12-es (és újabb) verzióval.

Az Azure Terraform-modulokkal újrafelhasználható, készíthető és tesztelhető összetevőket hozhat létre. A Terraform-modulok beágyazást tartalmaznak, amely hasznos lehet az infrastruktúra programkód-folyamatként való megvalósításában.

Terraform-modulok létrehozásakor fontos a minőségbiztosítás megvalósítása. Sajnos korlátozott dokumentáció érhető el, amelyből megtudhatja, hogyan hozhat létre egység-és integrációs teszteket a Terraform-modulokban. Ez az oktatóanyag bemutatja a tesztelési infrastruktúrát és az ajánlott eljárásokat, amelyeket az [Azure Terraform-modulok](https://registry.terraform.io/browse?provider=azurerm)kiépítésekor elfogadottunk.

Megvizsgáltuk az összes legnépszerűbb tesztelési infrastruktúrát, és úgy döntöttünk, hogy [Terratest](https://github.com/gruntwork-io/terratest) a Terraform-modulok tesztelésére. A Terratest Go könyvtárként van megvalósítva. A Terratest segítő függvények és minták gyűjteményét kínálja a közös infrastruktúra-tesztelési feladatokhoz, például HTTP-kérések készítéséhez és SSH használatával egy adott virtuális géphez való hozzáféréshez. Az alábbi lista ismerteti a Terratest használatának főbb előnyeit:

- **Kényelmes segítséget nyújt az infrastruktúra vizsgálatához**. Ez a funkció akkor hasznos, ha a valódi környezetben szeretné ellenőrizni a valós infrastruktúrát.
- **A mappa szerkezete világosan van rendszerezve**. A tesztelési esetek egyértelműen vannak rendszerezve, és követik a [szabványos Terraform modult](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- Az **összes tesztelési eset a Go-ban van írva**. A Terraform használó fejlesztők többsége a go fejlesztői. Ha Ön go-fejlesztő, nem kell megtanulnia egy másik programozási nyelvet a Terratest használatához. Emellett az egyetlen olyan függőség, amely szükséges ahhoz, hogy tesztelési eseteket futtasson a Terratest-ben, a Go és a Terraform.
- **Az infrastruktúra nagyon bővíthető**. A Terratest felső részén további funkciókat is kiterjesztheti, beleértve az Azure-specifikus funkciókat is.

## <a name="prerequisites"></a>Előfeltételek

Ez a gyakorlati cikk a platformtól független. Az ebben a cikkben a Windows, Linux vagy MacOS rendszeren használt példákat is futtathatja. 

A Kezdés előtt telepítse a következő szoftvereket:

- **Go programozási nyelv**: A Terraform-tesztelési esetek a [Go](https://golang.org/dl/)-ban íródnak.
- **dep**: [dep](https://github.com/golang/dep#installation) a GO nyelvhez készült függőségkezelő eszköz.
- **Azure CLI**: Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) egy parancssori eszköz, amely az Azure-erőforrások kezelésére használható. (A Terraform támogatja az Azure-ban való hitelesítést az egyszerű szolgáltatásnév vagy [Az Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html)használatával.)
- **mágus**: A [mágus végrehajtható fájl](https://github.com/magefile/mage/releases) segítségével bemutatjuk, hogyan egyszerűsíthető a futó Terratest-esetek futtatása. 

## <a name="create-a-static-webpage-module"></a>Statikus weblap modul létrehozása

Ebben az oktatóanyagban egy Terraform-modult hoz létre, amely egy statikus weblapot tesz elérhetővé azáltal, hogy egyetlen HTML-fájlt tölt fel egy Azure Storage-blobba. Ez a modul lehetővé teszi a felhasználók számára, hogy a modul által visszaadott URL-címen keresztül hozzáférjenek a weboldalhoz.

> [!NOTE]
> Hozza létre az ebben a szakaszban leírt összes fájlt a [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) helye alatt.

Először hozzon létre egy nevű `staticwebpage` új mappát a GoPath `src` mappában. Az oktatóanyag általános mappastruktúrát a következő példában látható. A csillaggal `(*)` jelölt fájlok az elsődleges hangsúly ebben a szakaszban.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

A statikus weblap modul három bemenetet fogad el. A bemenetek deklarálva `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

Ahogy korábban is említettük a cikkben, ez a modul a következőben `./outputs.tf`deklarált URL-címet is kiírja:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

A modul fő logikája négy erőforrást foglal le:
- **erőforráscsoport**: Az erőforráscsoport neve a `website_name` által `-staging-rg`hozzáfűzött bemenet.
- **Storage-fiók**: A Storage-fiók neve a `website_name` által `data001`hozzáfűzött bemenet. A Storage-fiók nevének korlátozásához a modul eltávolítja az összes speciális karaktert, és kisbetűket használ a teljes Storage-fiók nevében.
- **rögzített név tárolója**: A tároló neve `wwwroot` , és létrejön a Storage-fiókban.
- **egyetlen HTML-fájl**: A rendszer beolvassa a HTML `html_path` -fájlt a bemenetből, és `wwwroot/index.html`feltölti a következőre:.

A statikus weblap modul logikát a `./main.tf` valósítja meg:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = "${var.location}"
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = "${azurerm_resource_group.main.name}"
  location                 = "${azurerm_resource_group.main.location}"
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = "${azurerm_resource_group.main.name}"
  storage_account_name  = "${azurerm_storage_account.main.name}"
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = "${azurerm_resource_group.main.name}"
  storage_account_name   = "${azurerm_storage_account.main.name}"
  storage_container_name = "${azurerm_storage_container.main.name}"
  source                 = "${var.html_path}"
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Egységteszt

A Terratest az integrációs tesztekhez készült. Erre a célra a Terratest valós erőforrásokkal rendelkezik valódi környezetekben. Előfordulhat, hogy az integrációs teszt feladatai kivételesen nagy méretűek lehetnek, különösen akkor, ha nagy számú erőforrást kell kiépíteni. Jó példa arra, hogy az előző szakaszban hivatkozott Storage-fiókok nevét konvertálja. 

Azonban nem igazán szükséges erőforrást kiépíteni. Csak azt szeretnénk, hogy helyesek legyenek az elnevezés átalakítási logikája. A Terratest rugalmasságának köszönhetően az egységes tesztek is használhatók. Az egységes tesztek helyi tesztelési esetekben működnek (bár az internet-hozzáférésre van szükség). A rendszer végrehajtja `terraform init` `terraform plan` az egység tesztelési eseteit, `terraform plan` valamint a kimenetének elemzéséhez szükséges parancsokat, és megkeresi az összehasonlítandó attribútumok értékeit.

A szakasz további része azt ismerteti, hogyan használjuk a Terratest az egység tesztelésének megvalósítására, hogy megbizonyosodjon arról, hogy a Storage-fiókok nevének konvertálásához használt logika helyes. Csak a csillaggal `(*)`jelölt fájlok érdeklik.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Először egy helyőrzőként használt üres HTML-fájlt `./test/fixtures/storage-account-name/empty.html` használunk.

A fájl `./test/fixtures/storage-account-name/main.tf` a tesztelési eset kerete. Egy bemenetet fogad el `website_name`, amely egyben az egység tesztek bemenete is. A logika itt látható:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = "${var.website_name}"
  html_path    = "empty.html"
}
```

A fő összetevő az egység tesztelésének `./test/storage_account_name_unit_test.go`megvalósítása a alkalmazásban.

A go Developers valószínűleg azt észleli, hogy az egység tesztelése megfelel a klasszikus go-teszt függvény aláírásának, ha elfogad `*testing.T`egy típusú argumentumot.

Az egység teszt törzsében összesen öt eset van meghatározva a változóban `testCases` (`key` bemenetként, és `value` a várt kimenetként). Az egyes egységekhez tartozó tesztelési esetekben először `terraform init` a test bevezetési mappáját`./test/fixtures/storage-account-name/`() futtatjuk. 

Ezt követően egy `terraform plan` adott tesztelési esetet használó parancs (a `website_name` definíciójának `tfOptions`áttekintése) menti az eredményt `./test/fixtures/storage-account-name/terraform.tfplan` (a mappa teljes struktúrájában nem szerepel).

Ez az eredményhalmaz egy kód által olvasható struktúrára lesz értelmezve a hivatalos Terraform terv-elemző használatával.

Most megkeresjük a kívánt attribútumokat (ebben az esetben a `name` `azurerm_storage_account`), és összehasonlítjuk az eredményeket a várt kimenettel:

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions.Vars, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Az egység tesztek futtatásához hajtsa végre a következő lépéseket a parancssorban:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

A hagyományos go-teszt eredménye körülbelül egy percet ad vissza.

### <a name="integration-test"></a>Integrációs teszt

Az egységbeli tesztek esetében az integrációs tesztek teljes körű perspektívát biztosítanak az erőforrások valódi környezethez való kiépítéséhez. A Terratest jó munkát végez az ilyen típusú feladatokkal. 

A Terraform-modulok ajánlott eljárásai közé tartozik `examples` a mappa telepítése. A `examples` mappa egy végpontok közötti mintákat tartalmaz. A valós adatértékek használatának elkerülése érdekében miért nem teszteli ezeket a mintákat integrációs tesztekként? Ebben a szakaszban a három olyan fájlra fogunk összpontosítani, amelyek csillaggal `(*)` vannak megjelölve a következő mappák struktúrájában:

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Kezdjük a mintákat. A rendszer létrehoz egy nevű `hello-world/` új minta mappát a `./examples/` mappában. Itt egy egyszerű, feltölthető HTML-oldalt adunk meg: `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

A Terraform minta `./examples/hello-world/main.tf` hasonló az egység tesztben láthatóhoz. Van egy jelentős különbség: a minta a feltöltött HTML URL-címét is kiírja a nevű `homepage`weblapként.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = "${var.website_name}"
}

output "homepage" {
  value = "${module.staticwebpage.homepage_url}"
}
```

A Terratest és a klasszikus go test functions funkciót újra használjuk az integrációs teszt fájljában `./test/hello_world_example_test.go`.

Az egységes tesztektól eltérően az integrációs tesztek tényleges erőforrásokat hoznak létre az Azure-ban. Ezért körültekintően kell eljárnia, hogy elkerülje az ütközések elnevezését. (Különös figyelmet fordít a globálisan egyedi nevekre, például a Storage-fiókok nevére.) Ezért a tesztelési logika első lépése a Terratest által biztosított `websiteName` `UniqueId()` függvény használatával véletlenszerűen generált. Ez a függvény olyan véletlenszerű nevet állít elő, amely kisbetűkből, nagybetűkből vagy számokból áll. `tfOptions`az összes olyan Terraform-parancsot végrehajtja, amely a `./examples/hello-world/` mappát célozza meg. Azt is ellenőrzi, hogy `website_name` az a `websiteName`randomizált értékre van-e állítva.

Utána `terraform init`, `terraform apply` és `terraform output` kerül végrehajtásra, sorban egymás után. Egy másik segítő függvényt `HttpGetWithCustomValidation()`használunk, amelyet a Terratest biztosít. A Helper függvény használatával győződjön meg arról, hogy a HTML-fájl fel van `homepage` töltve a által `terraform output`visszaadott kimeneti URL-címre. Összehasonlítjuk a HTTP Get állapotkódot, `200` és megkeresünk néhány kulcsszavakat a HTML-tartalomban. Végül az `terraform destroy` "ígéretet" kap a végrehajtásra a Go `defer` funkciójának kihasználásával.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

Az integrációs tesztek futtatásához hajtsa végre a következő lépéseket a parancssorban:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

A hagyományos go-teszt eredménye körülbelül két percet vesz igénybe. A következő parancsok végrehajtásával is futtathatja az egység teszteit és az integrációs teszteket:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Az integrációs tesztek sokkal hosszabb ideig tartanak, mint az egység-tesztek (két perc az egyes integrációs esetekhez, mint egy perc öt egység esetén). Azonban a döntése, hogy az egység-vagy integrációs teszteket egy adott forgatókönyvben használja-e. Általában a Terraform HCL függvények használatával érdemes az összetett logikához tartozó egység-teszteket használni. Általában az integrációs teszteket használjuk a felhasználók végpontok közötti perspektívájában.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Mage használata a Terratest esetek egyszerűbb futtatásához 

A Azure Cloud Shell tesztelési eseteinek futtatása nem egyszerű feladat. Különböző címtárakban kell eljárnia, és különböző parancsokat kell végrehajtania. A Cloud Shell használatának elkerülése érdekében bemutatjuk a Build rendszert a projektben. Ebben a szakaszban a feladatokhoz egy go Build-rendszerindítási rendszer van használatban.

A mágus által szükséges egyetlen dolog a `magefile.go` projekt gyökérkönyvtárában van (a következő példának `(+)` megfelelően megjelölve):

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Íme egy példa a `./magefile.go`következőre:. Ebben az összeállításban, a Go-ban írt Build parancsfájl öt fordítási lépést implementál:
- `Clean`: A lépés eltávolítja a tesztek végrehajtása során generált összes generált és ideiglenes fájlt.
- `Format`: A lépés lefut `terraform fmt` , `go fmt` és formázza a kód alapját.
- `Unit`: A lépés az összes Unit-tesztet futtatja (a Function `TestUT_*`Name konvenció használatával `./test/` ) a mappában.
- `Integration`: A lépés hasonló `Unit`, de az egységenkénti tesztek helyett az integrációs teszteket (`TestIT_*`) hajtja végre.
- `Full`: A lépés a `Clean`, a `Unit`, a `Integration` és a billentyűkombinációt is futtatja `Format`.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

A teljes tesztelési csomag végrehajtásához használja a következő parancsokat. A kód hasonló a korábbi szakaszban használt futtatási lépésekhez. 

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only required when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage$ mage
```

Az utolsó parancssort a mágus további lépéseivel cserélheti le. Használhatja `mage unit` például a következőt: vagy `mage clean`. Érdemes beágyazni `dep` a parancsokat és `az login` a magefile. Itt nem jelennek meg a kód. 

A mágus használatával a go Package rendszeren is megoszthatja a lépéseket. Ebben az esetben egyszerűbbé teheti a magefiles az összes modulban azáltal, hogy csak egy közös implementációra és deklarált függőségekre`mg.Deps()`() hivatkozik.

**Választható Egyszerű szolgáltatás környezeti változóinak beállítása az elfogadási tesztek futtatásához**
 
A tesztek végrehajtása `az login` helyett az Azure-hitelesítést az egyszerű szolgáltatásnév környezeti változóinak beállításával végezheti el. A Terraform közzéteszi a [környezeti változók neveinek listáját](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (A környezeti változók közül csak az első négy szükséges.) [A Terraform a környezeti változók értékének beszerzését](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)ismertető részletes utasításokat is közzétesz.

## <a name="next-steps"></a>További lépések

* A Terratest kapcsolatos további információkért tekintse meg a [Terratest GitHub oldalát](https://github.com/gruntwork-io/terratest).
* A Mágussal kapcsolatos információkért tekintse meg a [mágus GitHub oldalát](https://github.com/magefile/mage) és a [mágus webhelyét](https://magefile.org/).
