---
title: Terraform-modulokkal tesztelése az Azure-ban Terratest használatával
description: Ismerje meg, hogyan használható a Terratest a Terraform-modulok tesztelésére.
services: terraform
ms.service: terraform
keywords: terraform, devops, tárfiók, azure, terratest, egységteszt, integrációs teszt
author: JunyiYi
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2018
ms.openlocfilehash: cff7d0dea27dd21ac4f7bb133e297e4f5928d2c2
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52680599"
---
# <a name="test-terraform-modules-in-azure-by-using-terratest"></a>Terraform-modulokkal tesztelése az Azure-ban Terratest használatával

Újrafelhasználható, algyűjteményeinek összefüggő létrehozása Terraform moduljait és testable összetevők is használhatja. Terraform-modulokkal építhet be, amely az infrastruktúra mint kód folyamatok megvalósítása során hasznos beágyazást.

Fontos, minőségbiztosítási megvalósításához, Terraform-modulokkal létrehozásakor. Sajnos az korlátozott dokumentáció azt ismertetik, hogyan hozhat létre az egységteszteket és a Terraform-modulokkal integrációs tesztek számára érhető el. Ez az oktatóanyag bemutatja egy tesztelési infrastruktúra és az ajánlott eljárás, hogy elfogadott, amikor is a [Azure Terraform moduljaival](https://registry.terraform.io/browse?provider=azurerm).

Megvizsgáltunk, minden a legnépszerűbb tesztelési infrastruktúra és a kiválasztott [Terratest](https://github.com/gruntwork-io/terratest) a Terraform-modulokkal teszteléshez használni kívánt. A Terratest Go könyvtárként van megvalósítva. Terratest segédfüggvények és mintázatok az alapvető infrastruktúra tesztelési feladatokat, mint a HTTP-kérelem indítására és a egy adott virtuális gép eléréséhez használt SSH gyűjteményét tartalmazza. Az alábbi listában néhány főbb előnyei a Terratest használatával mutatja be:

- **Kényelmes segítők infrastruktúra ellenőrzéséhez nyújt**. Ez a funkció akkor hasznos, ha a valódi környezetben szeretné ellenőrizni a valós infrastruktúrát.
- **A gyökérmappa-szerkezetében egyértelműen vannak rendszerezve**. A vizsgálati esetek jól vannak rendezve, és kövesse a [standard Terraform modul mappastruktúra](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Valamennyi vizsgálati eset a Go nyelven írták**. A legtöbb fejlesztő, aki a Terraform használata a Go fejlesztők számára. Ha Ön a Go fejlesztők, nem kell Terratest használt egy másik programozási nyelv tudnivalók. A csak szükséges függőségekkel, hogy a Terratest vizsgálati eset futtatása is a Go és a Terraform.
- **Az infrastruktúra képes legyen nagy mértékben bővíthetők**. Kibővítheti felett Terratest, beleértve az Azure-specifikus szolgáltatások további funkciók.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk gyakorlati platformfüggetlen. Ebben a cikkben használt hitelesítésikód-példák futtathatja Windows, Linux vagy MacOS rendszeren. 

Mielőtt elkezdené, a következő szoftverek telepítése:

- **Go programozási nyelv**: Terraform teszteseteket nyelven írták [Go](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) a GO nyelvhez készült függőségkezelő eszköz.
- **Az Azure CLI**: A [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) egy parancssori eszköz használatával Azure-erőforrások kezeléséhez. (A Terraform támogatja a hitelesítés az Azure-szolgáltatásnév keresztül vagy [az Azure CLI-n keresztül](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **obrázek**: használjuk a [végrehajtható mezkép](https://github.com/magefile/mage/releases) megmutatjuk, hogyan egyszerűsítheti a futó Terratest esetekben. 

## <a name="create-a-static-webpage-module"></a>Statikus weblap modul létrehozása

Ebben az oktatóanyagban a Terraform modul, amely egy statikus weblap kiosztja az Azure Storage-blobba egyetlen HTML-fájl feltöltésével hoz létre. Ez a modul biztosít a felhasználóknak a szerte a világon hozzáférést a weblapra egy URL-címmel, amely a modul adja vissza.

> [!NOTE]
> A jelen szakaszban ismertetett összes fájl létrehozása a [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) helyét.

Először hozzon létre egy új mappát `staticwebpage` alatt a GoPath `src` mappát. A teljes mappaszerkezetének ebben az oktatóanyagban az alábbi példában látható. Fájlok, egy csillaggal jelölve `(*)` referenciamodelljének ebben a szakaszban vannak.

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

A statikus weblap modul három bemenetek fogad el. A bemeneti adatok a deklarált `./variables.tf`:

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

Ahogy korábban említettük, a cikk korábbi részében, ez a modul is kimenete egy URL-címet a deklarált `./outputs.tf`:

```hcl
output "homepage_url" {
  value = "${azurerm_storage_blob.homepage.url}"
}
```

A modul a fő logika kiosztja az erőforrásokat négy:
- **Erőforráscsoport**: az erőforráscsoport neve a `website_name` hozzáfűzik bemeneti `-staging-rg`.
- **Storage-fiók**: a tárfiók neve a `website_name` hozzáfűzik bemeneti `data001`. Be kell tartaniuk a tárfiók neve vonatkozó korlátozások, a modul eltávolítja az összes speciális karakter és kisbetűk használata a teljes nevét használja.
- **tároló neve rögzített**: A tároló neve `wwwroot` és a storage-fiók jön létre.
- **egyetlen HTML-fájl**: A HTML-fájl olvasása az a `html_path` bemeneti és a feltöltött `wwwroot/index.html`.

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

Integráció az egységteszteket Terratest tervezték. Erre a célra Terratest látja el egy valós környezetben a valódi erőforrások. Egyes esetekben integrációs tesztelési feladatok válhat kivételesen nagy, különösen akkor, ha az erőforrások kiépítésének sok rendelkezik. A logika, amely átalakítja a tárfiókok nevének, hogy tekintse meg az előző szakaszban egy jó példa. 

De azt nem feltétlenül szükséges összes erőforrás kiépítéséhez. Csak szeretnénk győződjön meg arról, hogy helyesen szerepel-e az elnevezési átalakítás logikát. Köszönhetően Terratest rugalmasságát használhatjuk az egységteszteket. Egységteszteket is helyi vizsgálati eset futtatása (bár az internet-hozzáférésre szükség). Egység teszteseteket végrehajtása `terraform init` és `terraform plan` parancsok kimenetének elemzése `terraform plan` , és keressen az attribútumértékek összehasonlítására.

Ez a szakasz a többi ismerteti, hogyan használjuk az Terratest győződjön meg arról, hogy helyesen szerepel-e a tárfiókok nevének alakítani logikai egységet a vizsgálat végrehajtása érdekében. Jelenleg csak a csillaggal megjelölt fájlokat iránt `(*)`.

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

Először használjuk egy üres nevű HTML-fájlt `./test/fixtures/storage-account-name/empty.html` helyettesíti.

A fájl `./test/fixtures/storage-account-name/main.tf` a Teszteset keret van. Egy bemeneti fogad el `website_name`, ami egyben az egységteszteket bemenetével. A logikai itt jelenik meg:

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

A fő összetevője az egységteszteket a végrehajtásának `./test/storage_account_name_unit_test.go`.

Nyissa meg a fejlesztők valószínűleg megfigyelheti, hogy megfelel-e a egységtesztet Go klasszikus tesztfüggvény aláírását típusú argumentumot elfogadásával `*testing.T`.

A test jednotky törzse változóban meghatározott öt esetek összesen rendelkezünk `testCases` (`key` bemenetként, és `value` várt kimenetként). Minden egység vizsgálati eset, hogy először futtassa `terraform init` és a teszt készülék a célmappa (`./test/fixtures/storage-account-name/`). 

Ezután egy `terraform plan` parancs adott Teszteset bemeneti használó (vessen egy pillantást a `website_name` definíciójának `tfOptions`) menti az eredményt a `./test/fixtures/storage-account-name/terraform.tfplan` (a teljes nem szerepel).

Az eredményfájlt a hivatalos Terraform terv elemző használatával elemzi a kód olvasható struktúra.

Most áttekintjük az attribútumok érdekel (ebben az esetben a `name` , a `azurerm_storage_account`) és az eredményeket hasonlítsa össze a várt kimenet:

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

Ha szeretné futtatni az egységteszteket, a következő lépéseket a parancssorban:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestUT_StorageAccountName
```

A hagyományos Go vizsgálati eredmény körülbelül egy percig adja vissza.

### <a name="integration-test"></a>Integrációs teszt

Szakembereket egységteszteket integrációs tesztek engedélyeznie kell egy valós környezetben, egy teljes körű szempont erőforrásokat. Az ilyen típusú feladat jó feladatok Terratest hajtja végre. 

Ajánlott eljárások a Terraform modulok incude telepítéséhez a `examples` mappát. A `examples` mappa tartalmaz néhány teljes körű minta kipróbálásával. Adatok használatának elkerülése érdekében miért nem Tesztelje ezeket a mintákat, integrációs tesztek? Ebben a szakaszban fogunk összpontosítani, amely egy csillag három fájlokról `(*)` a következő gyökérmappa-szerkezetében lévő:

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

Kezdjük a mintákat. Minta nevű új mappa `hello-world/` jön létre a `./examples/` mappát. Itt egy egyszerű HTML-oldalt feltölteni kívánt kínálunk: `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample webpage to demostrate Terratest.</p>
</body>
</html>
```

A Terraform minta `./examples/hello-world/main.tf` hasonlít a egységtesztet a képen látható. Az egyik lényeges különbség van: a minta is kiírja ezt az URL-címét a feltöltött HTML nevű weblapként `homepage`.

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

Terratest használjuk, és a klasszikus Go tesztelési funkciók újra a-integráció tesztelése fájl `./test/hello_world_example_test.go`.

Egységtesztek, ellentétben a integrációs tesztek tényleges erőforrások létrehozása az Azure-ban. Ezért ügyeljen arra, hogy így elkerülhetők a névütközések kell. (Néhány globálisan egyedi nevet, például a tárfiókok nevének külön figyelmet szentelnie.) Az első lépés a tesztelési logika ezért egy véletlenszerű előállításához `websiteName` használatával a `UniqueId()` Terratest által biztosított függvény. Ez a függvény egy véletlenszerű névvel, amely rendelkezik a kisbetűk, nagybetűk és számok állít elő. `tfOptions` lehetővé teszi a Terraform összes parancs a cél a `./examples/hello-world/` mappát. Azt is gondoskodik róla, hogy `website_name` nem értékre állítva a véletlenszerű `websiteName`.

Utána `terraform init`, `terraform apply` és `terraform output` kerül végrehajtásra, sorban egymás után. Egy másik segédfüggvény használjuk `HttpGetWithCustomValidation()`, amely Terratest által biztosított. Győződjön meg arról, hogy HTML fel van töltve a kimenet a segédfüggvény használjuk `homepage` által visszaadott URL-cím `terraform output`. A HTTP GET állapotkód összehasonlítva `200` , és keresse meg bizonyos kulcsszavakat a HTML-tartalom. Végül az `terraform destroy` "ígéretet" kap a végrehajtásra a Go `defer` funkciójának kihasználásával.

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
            strings.Contains(content, "This is a sample web page to demostrate Terratest.")
    })
}
```

Az integrációs tesztek futtatásához a következő lépéseket a parancssorban:

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in test cases
GoPath/src/staticwebpage$ cd test
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage/test$ go test -run TestIT_HelloWorldExample
```

A hagyományos Go vizsgálati eredmény nagyjából két perc alatt adja vissza. Ezek a parancsok végrehajtásával egységteszteket és az integráció az egységteszteket is futtatásával:

```shell
GoPath/src/staticwebpage/test$ go fmt
GoPath/src/staticwebpage/test$ go test
```

Integráció az egységteszteket igénybe jóval több egységteszteket (egy integrációs esetekhez öt egység esetek perces képest két perc). Azonban a döntést az egységteszteket használja vagy integrációs tesztek forgatókönyvekben. Általában azt szeretné használni egységteszteket összetett logikát a Terraform HCL függvények használatával. Általában használjuk integrációs tesztek a felhasználó teljes körű szempontjából.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Mage használata a Terratest esetek egyszerűbb futtatásához 

Vizsgálati eset futtatása az Azure Cloud Shellben nem egyszerű feladat. Nyissa meg más könyvtárban, és hajtsa végre a különböző parancsokat kell. Elkerülése érdekében a Cloud Shellt használja, hogy a projekt az összeállítási rendszer vezetnek be. Ebben a szakaszban a Go buildelési rendszert, mezkép, a feladat használjuk.

Obrázek által igényelt egyedül van `magefile.go` a projekt gyökérkönyvtárában (jelölése `(+)` a következő példa):

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

Íme egy példa `./magefile.go`. A build-parancsfájl, Go, a megvalósítása öt létrehozási lépések:
- `Clean`: Ez a lépés eltávolítja az összes létrehozott ideiglenes fájlok és során végrehajtott teszt közül.
- `Format`: Ez a lépés fut `terraform fmt` és `go fmt` formázhatja a kódbázis.
- `Unit`: Ez a lépés fut az összes egységteszteket (a függvény neve konvenció szerint `TestUT_*`) alatt a `./test/` mappát.
- `Integration`: Ez a lépés hasonlít a `Unit`, de helyett egységteszteket, integrációs teszteket hajt végre (`TestIT_*`).
- `Full`: Ez a lépés fut `Clean`, `Format`, `Unit`, és `Integration` sorrendben.

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

A következő parancsok segítségével hajtható végre egy teljes tesztcsomag. A kód hasonlít a futó lépéseket egy korábbi szakaszában használtuk. 

```shell
$ cd [Your GoPath]/src/staticwebpage
GoPath/src/staticwebpage$ dep init    # Run only once for this folder
GoPath/src/staticwebpage$ dep ensure  # Required to run if you imported new packages in magefile or test cases
GoPath/src/staticwebpage$ go fmt      # Only requied when you change the magefile
GoPath/src/staticwebpage$ az login    # Required when no service principal environment variables are present
GoPath/src/staticwebpage$ mage
```

Az utolsó parancssorból lecserélheti mezkép további lépéseket. Használhatja például `mage unit` vagy `mage clean`. Jó ötlet ágyazhat be `dep` parancsokat és `az login` a magefile a. A kód Itt azt ne jelenjen meg. 

Obrázek, a is a Go-csomag rendszerrel tudta megosztani a lépéseket. Ebben az esetben, egyszerűsítheti magefiles közötti összes modul csak egy közös végrehajtási hivatkozó és függőségek deklaráló (`mg.Deps()`).

**Választható lehetőség: Szolgáltatás egyszerű környezeti változók beállítása minőségellenőrzési tesztelésben vesznek részt futtatása**
 
Végrehajtása helyett `az login` teszteket, mielőtt a szolgáltatás egyszerű környezeti változók beállításával az Azure hitelesítési hajthatja végre. A Terraform tesz közzé egy [Névlista környezeti változó](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (A környezeti változók közül csak az első négy szükséges.) A Terraform is közzéteszi a részletes útmutatásokat hogyan [szerezze be ezeket a környezeti változókat értékét](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>További lépések

* Terratest kapcsolatos további információkért lásd: a [Terratest GitHub-oldalon](https://github.com/gruntwork-io/terratest).
* Obrázek kapcsolatos információkért tekintse meg a [mezkép GitHub-oldalon](https://github.com/magefile/mage) és a [mezkép webhely](https://magefile.org/).
