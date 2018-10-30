---
title: Terraform-modulok tesztelése Azure-ban Terratest használatával
description: Ismerje meg, hogyan használható a Terratest a Terraform-modulok tesztelésére.
services: terraform
ms.service: terraform
keywords: terraform, devops, tárfiók, azure, terratest, egységteszt, integrációs teszt
author: JunyiYi
manager: jeconnoc
ms.author: junyi
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: 7feee063c7b311934f7d157a9dff62d803a041b0
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638711"
---
# <a name="test-terraform-modules-in-azure-using-terratest"></a>Terraform-modulok tesztelése Azure-ban Terratest használatával

A Terraform-modulok segítségével újrahasznosítható, szabadon konstruálható és tesztelhető összetevők hozhatók létre. Beágyazást valósítanak meg a "infrastruktúra mint kód" világában.

Ahogy más szoftverösszetevőknél, úgy a Terraform-moduloknál is fontos szerepet játszik a minőségbiztosítás. Az egységtesztek készítéséről és a Terraform-modulokkal végezhető integrációs tesztekről sajnos csak kevés dokumentáció áll rendelkezésre. Ebben az oktatóanyagban bemutatjuk az [Azure Terraform moduljaink](https://registry.terraform.io/browse?provider=azurerm) készítése során általunk használt tesztelési infrastruktúrát és ajánlott eljárásokat.

A legnépszerűbb tesztelési infrastruktúrák mérlegelése után a [Terratestre](https://github.com/gruntwork-io/terratest) esett a választásunk. A Terratest Go könyvtárként van megvalósítva. Segédfüggvény és minta gyűjteményt biztosít az általános infrastruktúra tesztelési feladatokhoz, például HTTP-kéréseket és SSH-t egy adott virtuális géphez. A Terratest főbb előnyei a következők:

- **Kényelmes segítséget nyújt az infrastruktúra ellenőrzéséhez.** Ez a funkció akkor hasznos, ha a valódi környezetben szeretné ellenőrizni a valós infrastruktúrát.
- **Világosan szervezett mappa struktúra.** A vizsgálati esetek a [standard Terraform modul mappastruktúrát](https://www.terraform.io/docs/modules/create.html#standard-module-structure) követve jól szervezettek lesznek.
- **Minden teszteset Go nyelven van megírva.** Mivel a Terraform fejlesztők többsége már Go fejlesztő is, a Terratest használata szükségtelenné teszi újabb programozási nyelv megtanulását. A Terratestben a vizsgálati esetek futtatásához az egyetlen függőség ami szükséges, az a Go és a Terraform.
- **Ez az infrastruktúra nagy mértékben bővíthető.** A Terratestre ráépülve nem nehéz a bővítés további függvényekkel, például kifejezetten Azure-specifikus függvényekkel.

## <a name="prerequisites"></a>Előfeltételek

Ez a gyakorlati útmutató platformfüggetlen; futtatható Windows, Linux vagy MacOS rendszeren is. Továbblépés előtt telepítse az alábbi szoftvereket:

- **A Go programozási nyelvet**: A Terraform tesztesetek [Go](https://golang.org/dl/) nyelven íródtak.
- **dep**: [dep](https://github.com/golang/dep#installation) a GO nyelvhez készült függőségkezelő eszköz.
- **Azure CLI**: AZ [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) az Azure parancssori eszköze, az Azure-erőforrások kezelésére szolgál. (A Terraform támogatja az Azure-hitelesítést a szolgáltatásnéven keresztül és [az Azure CLI-n keresztül](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage**: A [mage végrehajtható](https://github.com/magefile/mage/releases) használatával fogjuk megtanulni, hogyan lehet egyszerűbbé tenni a Terratest esetek futtatását. 

## <a name="create-a-static-webpage-module"></a>Statikus weblap modul létrehozása

Ebben az oktatóanyagban létrehozhat egy Terraform-modult, amely egy statikus weblapot épít ki egyetlen HTML-fájlnak az Azure storage-blobba történő feltöltésével. A modul lehetővé teszi a weblap elérését a felhasználók számára a világ minden pontjáról a modul által visszaadott URL-címen keresztül.

> [!NOTE]
> A szakaszban leírt valamennyi fájlt a [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) alatt kell létrehozni.

Először hozzon létre egy új `staticwebpage` nevű mappát a GoPath `src` mappájában. Az oktatóanyagban használt teljes mappaszerkezet látható az alábbi ábrán. (A szakaszban főleg a `(*)` csillaggal jelölt fájlokkal foglalkozunk.)

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

A statikus weblap modul három bemenetet fogad, ezek deklarálása itt történik `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which all resources will be created."
}

variable "website_name" {
  description = "The website name which will be used to create a bunch of related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static homepage HTML in your local filesystem."
  default     = "index.html"
}
```

Ahogy korábban említettük, a modul kimenete lesz egy URL-cím is, amit itt deklarálunk `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

Ezzel elérkeztünk a modul a lényegéhez. Összességében négy erőforrást használ:
- Egy erőforráscsoport, amelynek neve a `website_name` bemenetből a `-staging-rg` hozzáfűzésével adódik.
- Egy tárfiók, amelynek neve a `website_name` bemenetből a `data001` hozzáfűzésével adódik. Azonban a tárfiók névkorlátozásainak betartásához a modul eltávolítja az összes speciális karaktert és kisbetűsre alakítja a teljes nevet.
- A fenti tárfiókban létrehozott rögzített nevű tároló `wwwroot`.
- A `html_path` bemenetről beolvasott és `wwwroot/index.html`-re feltöltött egyetlen HTML-fáj.

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

A Terratest hagyományosan integrációs tesztekhez tervezett eszköz – ami azt jelenti, hogy valós környezetben valós erőforrásokat használ. Az ilyen feladat néha különösen naggyá válhat, főleg, ha rengeteg erőforrást kell kiépíteni. Jó példa erre az előző szakaszban leírt tárfiók névátalakítási logika: igazából nem kell semmilyen erőforrást kiépítenünk, csak azt akarjuk biztosítani, hogy jó legyen a névátalakító logika.

A Terratest rugalmasságának köszönhetően ez könnyen megvalósítható az egységtesztek használatával. Az egységtesztek helyileg futó tesztesetek (bár továbbra is szükséges az internet-hozzáférés) mindössze a `terraform init` és a `terraform plan` parancsok végrehajtásával, az egység tesztesetek elemzik a `terraform plan` kimenetét és megkeresik az összehasonlítani szükséges attribútumok értékeit.

A szakasz további részében azt ismertetjük, hogyan használható a Terratest a tárfiók névátalakító logika helyességét ellenőrző egységteszt megvalósítására. Csak a csillaggal jelölt fájlokkal fogunk foglalkozni `(*)`.

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

Először is a `./test/fixtures/storage-account-name/empty.html` üres HTML-fájl csak egy helyőrző.

A `./test/fixtures/storage-account-name/main.tf` fájl a teszteset váza. Egy `website_name` bemenetet fogad, ami egyben az egységtesztek bemenete is. A logika itt látható:

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

Végül a legnagyobb rész az egységtesztek megvalósítása: `./test/storage_account_name_unit_test.go`

A Go fejlesztők fel fogják ismerni, hogy a `*testing.T` típusú argumentum fogadása révén a fej valójában megegyezik a klasszikus GO tesztfüggvénnyel.

Az egységteszt törzsében a `testCases` változóban összesen öt esetet definiáltunk (kulcs mint bemenet, érték mint várt kimenet). Mindegyik egységteszt esetre először következik a `terraform init` futtatása a teszt rögzített mappát (`./test/fixtures/storage-account-name/`) megcélozva. 

Utána, a `terraform plan` parancs a konkrét teszteset bemenettel (lásd a `website_name` definíciót, itt `tfOptions`) elmenti az eredményt `./test/fixtures/storage-account-name/terraform.tfplan`-be (ami a teljes mappaszerkezetben nincs feltüntetve).

Utána ezt az eredményfájlt kóddal olvasható szerkezet alapján elemezzük a hivatalos Terraform csomagelemző használatával.

Most már a számunkra érdekes attribútumokat keressük (ebben az esetben `name` - `azurerm_storage_account`) és összehasonlítjuk őket a várt kimenettel.

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
        // Specify test case folder and "-var" options
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

Az egységtesztek futtatásához a következő lépéseket kell elvégezni a parancssorban.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

A hagyományos Go teszt eredménye körülbelül egy perc után jelenik meg.

### <a name="integration-test"></a>Integrációs teszt

Az egységtesztekkel ellentétben az integrációs teszteknek végpont-végpont perspektívából valós környezetben ki kell építeniük az erőforrásokat. A Terratest jó munkát végez ilyen dolgokkal. Mivel a Terraform modul ajánlott eljárása is javasolja a néhány teljeskörű példát tartalmazó `examples` mappát, miért ne használhatnánk fel ezeket integrációs tesztnek? Ebben a szakaszban három fájlra fogunk figyelni, mindegyiket csillag jelöli `(*)`.

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

Először kezdjük a példákkal. Egy új `hello-world/` nevű példa mappa jön létre a `./examples/` mappában. Itt megadunk egy egyszerű feltöltendő HTML-oldalt `./examples/hello-world/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demostrate Terratest.</p>
</body>
</html>
```

A `./examples/hello-world/main.tf` Terraform példa hasonló az egységtesztben látotthoz, csak egy lényeges különbség van: a feltöltött HTML URL-címét is kinyomtatja `homepage` név alatt.

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

A Terratest és a klasszikus Go teszt funkció ismét megjelenik az integrációs teszt fájlban `./test/hello_world_example_test.go`.

Az egységtesztektől eltérően az integrációs tesztek tényleges erőforrásokat hoznak létre az Azure-ban, ezért kell figyelni a névütközések elkerülésére. (Különösen olyan globálisan egyedi nevekre figyeljen, mint a tárfiók név). Az első lépés tehát a tesztelési logikában a TerraTest által biztosított `UniqueId()` függvény használatával véletlenszerű `websiteName` generálása. Ez a függvény létrehoz egy véletlenszerű nevet, ami tartalmaz kisbetűt, nagybetűt és számot. A `tfOptions` minden Terraform parancs céljának beállítja a `./examples/hello-world/` mappát, és biztosítja azt is, hogy a `website_name` beállítása feltétlenül megtörténjen a véletlenszerű `websiteName` értékre.

Utána `terraform init`, `terraform apply` és `terraform output` kerül végrehajtásra, sorban egymás után. Egy másik Terratest által biztosított segédfüggvényt `HttpGetWithCustomValidation()` használtunk arra, hogy ellenőrizzük, a HTML valóban feltöltődött a `terraform output` által visszaadott kimeneti `homepage` URL-címre úgy, hogy összehasonlítjuk a HTTP Get állapotkódot a `200`-gyel és bizonyos kulcsszavakat keresünk a HTML tartalomban. Végül az `terraform destroy` "ígéretet" kap a végrehajtásra a Go `defer` funkciójának kihasználásával.

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

    // Generate a random website name to prevent naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

Az integrációs tesztek futtatásához a következő lépéseket kell elvégezni a parancssorban.

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

A hagyományos Go teszt eredménye körülbelül két perc után jelenik meg. Természetesen mind az egységteszteket, mind az integrációs teszteket futtathatná a következőképpen:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Amint látható, az integrációs tesztek sokkal tovább tartanak, mint az egységtesztek (két perc egy integrációs esetre, míg egy perc öt egység esetre). De továbbra is a saját döntése, hogy mikor használja az egységteszteket és mikor érdemes kihasználni az integrációs teszteket. Általában a bonyolultabb, Terraform HCL-függvényeket használó programoknál inkább az egységteszteket használjuk, és integrációs tesztet a felhasználó végpont-végpont alapú nézőpontjából.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Mage használata a Terratest esetek egyszerűbb futtatásához 

Amint láthatta, tesztesetek futtatása parancssorban nem könnyű feladat, mivel különböző könyvtárakba kell lépni és különböző parancsokat kell végrehajtani. Ez az oka, hogy a projektünkbe bevezetjük a build rendszert. Ebben a szakaszban a Go mage build rendszerét használjuk a feladatra.

A mage által igényelt egyetlen dolog a `magefile.go` a projekt gyökérkönyvtárában (`(+)` jelöli a következő ábrán).

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

Íme egy `./magefile.go` példa. Ebben a Go-ban írt build parancsfájlban öt lépést valósítottunk meg:
- `Clean`: ez a lépés eltávolít minden teszt végrehajtás közben létrehozott/ideiglenes fájlt.
- `Format`: ez a lépés a `terraform fmt` és a `go fmt` futtatásával formázni fogja a kódbázist.
- `Unit`: ez a lépés futtat minden egységtesztet (`TestUT_*` függvénynév konvenció használatával), ami a `./test/` mappában található.
- `Integration`: hasonló, mint a `Unit`, de egységteszt helyett integrációs tesztet hajt végre (`TestIT_*`).
- `Full`: ez a lépés sorban futtatja a `Clean`, `Format`, `Unit', and `integrációt.

```go
// +build mage

// Build script to format and run tests of a Terraform module project.
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// Default target when execute `mage` in shell
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

// A build step that removes temporary build/test files
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

Az előző futtatási lépésekhez hasonlóan, a következő parancsokkal futtathatja a teljes tesztcsomagot:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables present
GoPath/src/staticwebpage$ mage
```

Az utolsó parancssort nyugodtan lecserélheti bármelyik mage lépésre, például: `mage unit` vagy `mage clean`. Azt gondolhatná hogy még mindig sok parancssor van itt, és jó ötlet lehet a `dep`, valamint a `az login` parancsok beágyazása a mage fájlba. A kódot azonban nem mutatjuk meg itt. A mage használatának egy további lépése, hogy a lépések a Go csomagrendszer használatával megoszthatók. Így a mage fájl az összes modulban egyszerűsíthető azáltal, hogy csak a közös megvalósításra kell hivatkozni és a függőségeket kell deklarálni (`mg.Deps()`).

> [!NOTE]
> **. lehetőség: szolgáltatásnév környezeti változók beállítása tesztek futtatásához**
> 
> A `az login` végrehajtása helyett, az Azure hitelesítés megvalósítható a szolgáltatásnév környezeti változók beállításával. A Terraform közzéteszi [a környezeti változó nevének listáját](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (A környezeti változók közül csak az első négy szükséges.) A Terraform közzéteszi a részletes utasításokat arra vonatkozóan is, hogy hogyan lehet [megtudni ezeknek a környezeti változóknak az értékét.](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)

## <a name="next-steps"></a>További lépések

Bővebb információk találhatók a Terratesttel kapcsolatban [a GitHub-oldalán](https://github.com/gruntwork-io/terratest). Hasznos információk találhatók a mage-vel kapcsolatban a [GitHub-oldalán](https://github.com/magefile/mage) és a [honlapján](https://magefile.org/).
