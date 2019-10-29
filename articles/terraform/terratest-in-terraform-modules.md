---
title: Oktatóanyag – Terraform-modulok tesztelése az Azure-ban a Terratest használatával
description: Ismerje meg, hogyan használható a Terratest a Terraform-modulok tesztelésére.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: bdb76fe2f87806c02a861ea84361b61a3e94b554
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969221"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>Oktatóanyag: Terraform-modulok tesztelése az Azure-ban a Terratest használatával

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

- **Go programozási nyelv**: a Terraform-tesztelési esetek a [Go](https://golang.org/dl/)-ban íródnak.
- **dep**: [dep](https://github.com/golang/dep#installation) a GO nyelvhez készült függőségkezelő eszköz.
- **Azure CLI**: az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) egy parancssori eszköz, amely az Azure-erőforrások kezelésére használható. (A Terraform támogatja az Azure-ban való hitelesítést az egyszerű szolgáltatásnév vagy [Az Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html)használatával.)
- **mágus**: a [mágus végrehajtható fájl](https://github.com/magefile/mage/releases) használatával megmutatjuk, hogyan egyszerűsíthető a futó Terratest-esetek futtatása. 

## <a name="create-a-static-webpage-module"></a>Statikus weblap modul létrehozása

Ebben az oktatóanyagban egy Terraform-modult hoz létre, amely egy statikus weblapot tesz elérhetővé azáltal, hogy egyetlen HTML-fájlt tölt fel egy Azure Storage-blobba. Ez a modul lehetővé teszi a felhasználók számára, hogy a modul által visszaadott URL-címen keresztül hozzáférjenek a weboldalhoz.

> [!NOTE]
> Hozza létre az ebben a szakaszban leírt összes fájlt a [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) helye alatt.

Először hozzon létre egy `staticwebpage` nevű új mappát a GoPath `src` mappában. Az oktatóanyag általános mappastruktúrát a következő példában látható. A csillag `(*)` jelölt fájlok az elsődleges hangsúly ebben a szakaszban.

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

A statikus weblap modul három bemenetet fogad el. A bemenetek deklarálva vannak `./variables.tf`ban:

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

A cikkben korábban említettek szerint ez a modul a `./outputs.tf`ban deklarált URL-címet is megjeleníti:

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

A modul fő logikája négy erőforrást foglal le:
- **erőforráscsoport**: az erőforráscsoport neve a `-staging-rg`által hozzáfűzött `website_name` bemenet.
- **Storage-fiók**: a Storage-fiók neve a `data001`által hozzáfűzött `website_name` bemenet. A Storage-fiók nevének korlátozásához a modul eltávolítja az összes speciális karaktert, és kisbetűket használ a teljes Storage-fiók nevében.
- **rögzített név tároló**: a tároló neve `wwwroot`, és a Storage-fiókban jön létre.
- **egyetlen HTML-fájl**: a rendszer beolvassa a HTML-fájlt a `html_path` bemenetből, és feltölti `wwwroot/index.html`re.

A statikus weblap modul logikát a `./main.tf` valósítja meg:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = azurerm_resource_group.main.name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = azurerm_resource_group.main.name
  storage_account_name   = azurerm_storage_account.main.name
  storage_container_name = azurerm_storage_container.main.name
  source                 = var.html_path
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Egységteszt

A Terratest az integrációs tesztekhez készült. Erre a célra a Terratest valós erőforrásokkal rendelkezik valódi környezetekben. Előfordulhat, hogy az integrációs teszt feladatai kivételesen nagy méretűek lehetnek, különösen akkor, ha nagy számú erőforrást kell kiépíteni. Jó példa arra, hogy az előző szakaszban hivatkozott Storage-fiókok nevét konvertálja. 

Azonban nem igazán szükséges erőforrást kiépíteni. Csak azt szeretnénk, hogy helyesek legyenek az elnevezés átalakítási logikája. A Terratest rugalmasságának köszönhetően az egységes tesztek is használhatók. Az egységes tesztek helyi tesztelési esetekben működnek (bár az internet-hozzáférésre van szükség). Az egység tesztelési esetei `terraform init` és `terraform plan` parancsokat futtatnak a `terraform plan` kimenetének elemzéséhez, és megkeresik az összehasonlítandó attribútumok értékeit.

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

Először egy `./test/fixtures/storage-account-name/empty.html` nevű üres HTML-fájlt használunk helyőrzőként.

A `./test/fixtures/storage-account-name/main.tf` fájl a tesztelési eset kerete. Elfogad egy bemenetet, `website_name`, amely egyben az egység tesztek bemenete is. A logika itt látható:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = var.website_name
  html_path    = "empty.html"
}
```

A fő összetevő a `./test/storage_account_name_unit_test.go`egységben végzett tesztek megvalósítása.

A go Developers valószínűleg azt észleli, hogy az egység tesztelése megfelel a klasszikus go-teszt függvény aláírásának, ha elfogad egy `*testing.T`típusú argumentumot.

Az egység teszt törzsében összesen öt eset van megadva a változó `testCases` (`key` bemenetként, és `value` a várt kimenetként). Az egyes egységekhez tartozó tesztelési esetekben először a `terraform init` futtatjuk, és megcélozjuk a test rögzítő mappát (`./test/fixtures/storage-account-name/`). 

Következő lépésként egy `terraform plan` parancs, amely konkrét tesztelési eseteket használ (lásd: `tfOptions`) a `website_name` definíciójának megkeresése az eredményt `./test/fixtures/storage-account-name/terraform.tfplan` (nem a teljes mappa struktúrájában szerepel).

Ez az eredményhalmaz egy kód által olvasható struktúrára lesz értelmezve a hivatalos Terraform terv-elemző használatával.

Most megkeresjük azokat az attribútumokat, amelyeket érdekelünk (ebben az esetben a `azurerm_storage_account``name`), és összehasonlítjuk az eredményeket a várt kimenettel:

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
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions, "plan", "-out="+tfPlanOutput)...)

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

A Terraform-modulok ajánlott eljárásai közé tartozik a `examples` mappa telepítése. A `examples` mappa tartalmaz egy végpontok közötti mintákat. A valós adatértékek használatának elkerülése érdekében miért nem teszteli ezeket a mintákat integrációs tesztekként? Ebben a szakaszban arra a három fájlra fogunk összpontosítani, amely csillag `(*)` van megjelölve a következő mappastruktúrát:

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

Kezdjük a mintákat. A `./examples/` mappában létrejön egy `hello-world/` nevű új minta mappa. Itt egy olyan egyszerű HTML-oldalt adunk meg, amelyet fel kell tölteni: `./examples/hello-world/index.html`.

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

A Terraform minta `./examples/hello-world/main.tf` hasonló az egység tesztben láthatóhoz. Van egy jelentős különbség: a minta a feltöltött HTML URL-címét is kiírja `homepage`nevű weboldalként.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = var.website_name
}

output "homepage" {
  value = module.staticwebpage.homepage_url
}
```

A Terratest és a klasszikus go test functions funkciót ismét a `./test/hello_world_example_test.go`integrációs teszt fájljában használjuk.

Az egységes tesztektól eltérően az integrációs tesztek tényleges erőforrásokat hoznak létre az Azure-ban. Ezért körültekintően kell eljárnia, hogy elkerülje az ütközések elnevezését. (Különös figyelmet fordít a globálisan egyedi nevekre, például a Storage-fiókok nevére.) Ezért a tesztelési logika első lépése egy véletlenszerű `websiteName` létrehozása a Terratest által biztosított `UniqueId()` függvény használatával. Ez a függvény olyan véletlenszerű nevet állít elő, amely kisbetűkből, nagybetűkből vagy számokból áll. a `tfOptions` az összes olyan Terraform-parancsot végrehajtja, amely a `./examples/hello-world/` mappát célozza meg. Azt is biztosítja, hogy `website_name` a randomizált `websiteName`ra legyen beállítva.

Utána `terraform init`, `terraform apply` és `terraform output` kerül végrehajtásra, sorban egymás után. Egy másik segítő függvényt használunk, `HttpGetWithCustomValidation()`, amelyet a Terratest biztosít. A Helper függvény használatával gondoskodhat arról, hogy a rendszer feltöltse a HTML-kódot a `terraform output`által visszaadott kimeneti `homepage` URL-címre. Összehasonlítjuk a HTTP GET állapotkódot `200` és megkeresünk néhány kulcsszavakat a HTML-tartalomban. Végül az `terraform destroy` "ígéretet" kap a végrehajtásra a Go `defer` funkciójának kihasználásával.

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
A Azure Cloud Shell tesztelési eseteinek futtatásához különböző parancsokat kell végrehajtani különböző könyvtárakban. A folyamat hatékonyabbá tételéhez bevezetjük a Build rendszerét a projektben. Ebben a szakaszban a feladatokhoz egy go Build-rendszerindítási rendszer van használatban.

A mágus által igényelt egyetlen dolog `magefile.go` a projekt gyökérkönyvtárában (a következő példában `(+)` jelöléssel):

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

Íme egy példa a `./magefile.go`ra. Ebben az összeállításban, a Go-ban írt Build parancsfájl öt fordítási lépést implementál:
- `Clean`: a lépés eltávolítja a tesztek végrehajtása során generált összes generált és ideiglenes fájlt.
- `Format`: a lépés `terraform fmt` és `go fmt` futtatásával formázza meg a kód alapját.
- `Unit`: a lépés az összes Unit-tesztet futtatja (a Function Name Convention `TestUT_*`használatával) a `./test/` mappában.
- `Integration`: a lépés hasonló a `Unit`hoz, de az egységhez tartozó tesztek helyett az integrációs teszteket (`TestIT_*`) hajtja végre.
- `Full`: a lépés `Clean`, `Format`, `Unit`és `Integration` fut a sorozatban.

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

Az utolsó parancssort a mágus további lépéseivel cserélheti le. Használhatja például `mage unit` vagy `mage clean`. Érdemes beágyazni `dep` parancsokat és `az login`eket a magefile. Itt nem jelennek meg a kód. 

A mágus használatával a go Package rendszeren is megoszthatja a lépéseket. Ebben az esetben egyszerűbbé teheti a magefiles az összes modulban azáltal, hogy csak a közös megvalósítási és deklarált függőségekre hivatkozik (`mg.Deps()`).

**Nem kötelező: a szolgáltatás egyszerű környezeti változóinak beállítása az elfogadási tesztek futtatásához**
 
A tesztek előtt végzett `az login` végrehajtása helyett az Azure-hitelesítést az egyszerű szolgáltatás környezeti változóinak beállításával végezheti el. A Terraform közzéteszi a [környezeti változók neveinek listáját](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (A környezeti változók közül csak az első négy szükséges.) [A Terraform a környezeti változók értékének beszerzését](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)ismertető részletes utasításokat is közzétesz.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Terratest GitHub-oldal](https://github.com/gruntwork-io/terratest).