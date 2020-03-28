---
title: Oktatóanyag – Terraform-modulok tesztelése az Azure-ban a Terratest használatával
description: Ismerje meg, hogyan használható a Terratest a Terraform-modulok tesztelésére.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 687a793af2b9b75efe463b042d121c32f18974d6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79370797"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>Oktatóanyag: Terraform-modulok tesztelése az Azure-ban a Terratest használatával

> [!NOTE]
> A cikkben szereplő mintakód nem működik a 0.12-es (és újabb) verzióval.

Az Azure Terraform-modulok segítségével újrafelhasználható, komponálható és tesztelhető összetevőket hozhat létre. A Terraform modulok olyan beágyazást tartalmaznak, amely hasznos az infrastruktúra kódfolyamatként történő megvalósításában.

A Terraform modulok létrehozásakor fontos a minőségbiztosítás megvalósítása. Sajnos korlátozott dokumentáció áll rendelkezésre a Terraform modulok egységtesztjeinek és integrációs tesztjeinek szerkesztéséhez. Ez az oktatóanyag bemutatja az [Azure Terraform-modulok](https://registry.terraform.io/browse?provider=azurerm)készítésekekekének során elfogadott tesztelési infrastruktúrát és bevált gyakorlatokat.

Megnéztük a legnépszerűbb tesztelési infrastruktúrákat, és a [Terratest-et](https://github.com/gruntwork-io/terratest) választottuk a Terraform modulok teszteléséhez. A Terratest Go könyvtárként van megvalósítva. Terratest segítő funkciók és minták gyűjteménye a gyakori infrastruktúra tesztelési feladatok, például a HTTP-kérelmek és az SSH használatával egy adott virtuális gép eléréséhez. Az alábbi lista a Terratest használatának néhány fő előnyét ismerteti:

- **Kényelmes segítők**számára biztosítja az infrastruktúra ellenőrzését. Ez a funkció akkor hasznos, ha a valódi környezetben szeretné ellenőrizni a valós infrastruktúrát.
- **A mappastruktúra egyértelműen szervezett**. A tesztesetek világosan vannak rendszerezve, és a [Terraform modul mappastruktúráját követik.](https://www.terraform.io/docs/modules/create.html#standard-module-structure)
- **Minden teszteset a Go .** A Terraformot használó legtöbb fejlesztő Go-fejlesztő. Ha Go-fejlesztő vagy, nem kell másik programozási nyelvet tanulnod a Terratest használatához. Emellett a Terratest tesztesetei futtatásához csak a Go és a Terraform függőségek szükségesek.
- **Az infrastruktúra rendkívül bővíthető**. További funkciókat is kiterjeszthet a Terratest en, beleértve az Azure-specifikus funkciókat is.

## <a name="prerequisites"></a>Előfeltételek

Ez a gyakorlati cikk platformfüggetlen. Futtathatja a cikkben windowsos, Linux os vagy MacOS rendszeren használt kódpéldákat. 

Mielőtt elkezdené, telepítse a következő szoftvert:

- **Go programozási nyelv:** Terraform teszt esetekben vannak írva [Go](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) a GO nyelvhez készült függőségkezelő eszköz.
- **Azure CLI:** Az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) egy parancssori eszköz, amellyel kezelheti az Azure-erőforrásokat. (A Terraform támogatja az Azure-ba való hitelesítést egy egyszerű szolgáltatáson keresztül vagy [az Azure CLI-n keresztül.)](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html)
- **mage**: A [mage futtatható,](https://github.com/magefile/mage/releases) hogy mutassa meg, hogyan lehet egyszerűsíteni futó Terratest esetekben. 

## <a name="create-a-static-webpage-module"></a>Statikus weblap modul létrehozása

Ebben az oktatóanyagban egy Terraform-modult hoz létre, amely egy statikus weblapot biztosít egyetlen HTML-fájl feltöltésével egy Azure Storage-blobba. Ez a modul a felhasználók számára a világ minden tájáról hozzáférést biztosít a weboldalhoz egy URL-en keresztül, amelyet a modul visszaad.

> [!NOTE]
> Hozza létre az ebben a szakaszban ismertetett összes fájlt a [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) helye alatt.

Először hozzon létre `staticwebpage` egy új `src` mappát a GoPath mappában. Az oktatóanyag teljes mappastruktúrája a következő példában látható. Ebben a szakaszban `(*)` a csillaggal jelölt fájlok az elsődleges fókusz.

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

A statikus weblapmodul három bemenetet fogad el. A bemenetek a `./variables.tf`következő helyen vannak bejelentve:

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

Ahogy korábban említettük a cikkben, ez a modul egy `./outputs.tf`URL-t is kiad, amely a következő ben deklarált:

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

A modul fő logikája négy forrást tartalmaz:
- **erőforráscsoport**: Az erőforráscsoport neve `website_name` a. `-staging-rg`által hozzáfűzött bemenet.
- **tárfiók**: A tárfiók neve `website_name` a. `data001`által hozzáfűzte a bemenetet. A tárfiók névkorlátainak betartása érdekében a modul eltávolítja az összes speciális karaktert, és kisbetűket használ a tárfiók teljes nevében.
- **rögzített névtároló:** A `wwwroot` tároló neve, és a tárfiókban jön létre.
- **egyetlen HTML-fájl:** A HTML-fájlt a program beolvassa a `html_path` bemenetről, és feltölti a rendszerbe. `wwwroot/index.html`

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

A Terratest integrációs tesztekhez készült. Ebből a célból a Terratest valódi erőforrásokat szolgál fel valós környezetben. Néha az integrációs tesztfeladatok kivételesen nagyok lehetnek, különösen akkor, ha nagy számú erőforrást kell kiépítenie. A logika, amely átalakítja a tárfiók nevét, hogy az előző szakaszban egy jó példa. 

De nem igazán kell erőforrásokat kiépíteni. Csak azt szeretnénk, hogy megbizonyosodjon arról, hogy az elnevezési konverziós logika helyes. A Terratest rugalmasságának köszönhetően használhatunk egységteszteket. Az egységtesztek helyi futó tesztesetek (bár internet-hozzáférés szükséges). Egység teszt `terraform init` esetek `terraform plan` végrehajtása és parancsok at `terraform plan` elemezni a kimenetet, és keresse meg az attribútum értékeket összehasonlítani.

A szakasz további része azt ismerteti, hogyan használjuk a Terratest-et egy egységteszt megvalósításához, hogy megbizonyosodjunk arról, hogy a tárfióknevek konvertálásához használt logika helyes. Mi érdekli csak a fájlokat jelölt `(*)`csillaggal .

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

Először is egy üres `./test/fixtures/storage-account-name/empty.html` HTML-fájlt használunk helyőrzőként.

A `./test/fixtures/storage-account-name/main.tf` fájl a teszteset kerete. Egy bemenetet fogad `website_name`el, amely egyben az egységtesztek bemenete is. A logika itt látható:

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

A fő összetevő az egységvizsgálatok `./test/storage_account_name_unit_test.go`végrehajtása a rendszerben.

Go fejlesztők valószínűleg észrefogja venni, hogy az egység teszt megegyezik az `*testing.T`aláírás egy klasszikus Go teszt funkció elfogadásával egy érv et .

Az egységteszt törzsében összesen öt olyan eset van, amely `testCases` `key` változóban van `value` meghatározva ( bemenetként és a várt kimenetként). Minden egység tesztesetében először `terraform init` futtatjuk és megcélozzuk a tesztszerelvény mappát (`./test/fixtures/storage-account-name/`). 

Ezután `terraform plan` egy adott teszteset-bemenetet használó parancs `website_name` (tekintse meg a definíciót `tfOptions`) menti az eredményt `./test/fixtures/storage-account-name/terraform.tfplan` (nem szerepel a teljes mappastruktúrában).

Ez az eredményfájl a hivatalos Terraform tervelemző használatával kódolvasható szerkezetre van elemezve.

Most megkeressük azokat az attribútumokat, amelyek érdekelnek `azurerm_storage_account`minket (ebben az esetben a) `name` és összehasonlítjuk az eredményeket a várt kimenettel:

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

Az egységtesztek futtatásához hajtsa végre a következő lépéseket a parancssorban:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestUT_StorageAccountName
```

A hagyományos Go teszt eredmény körülbelül egy perc alatt visszatér.

### <a name="integration-test"></a>Integrációs teszt

Az egységtesztekkel ellentétben az integrációs teszteknek erőforrásokat kell kiépíteniük egy valós környezetbe a végpontok közötti perspektíva érdekében. Terratest jó munkát végez az ilyen jellegű feladat. 

A Terraform modulokkal kapcsolatos gyakorlati `examples` tanácsok közé tartozik a mappa telepítése. A `examples` mappa tartalmaz néhány végponttól végpontig tartó mintát. A valós adatokkal való munka elkerülése érdekében miért nem teszteli ezeket a mintákat integrációs tesztként? Ebben a szakaszban a következő mappastruktúrában csillaggal `(*)` jelölt három fájlra összpontosítunk:

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

Kezdjük a mintákkal. A `./examples/` mappában `hello-world/` létrejön egy új mintamappa. Itt egy egyszerű HTML oldalt töltünk `./examples/hello-world/index.html`fel: .

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

A Terraform `./examples/hello-world/main.tf` minta hasonló az egységvizsgálatban láthatóhoz. Van egy jelentős különbség: a minta is kinyomtatja az URL-t `homepage`a feltöltött HTML, mint egy weboldal nevű .

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

A Terratest és a klasszikus Go tesztfunkciókat ismét az integrációs tesztfájlban használjuk. `./test/hello_world_example_test.go`

Az egységtesztekkel ellentétben az integrációs tesztek tényleges erőforrásokat hoznak létre az Azure-ban. Ezért kell óvatosnak lennie, hogy elkerülje az elnevezési ütközéseket. (Fordítson különös figyelmet néhány globálisan egyedi névre, például a tárfióknevekre.) Ezért a tesztelési logika első lépése a `websiteName` terratest `UniqueId()` által biztosított függvény használatával véletlenszerűvé tett létrehozás. Ez a függvény kisbetűkből, nagybetűkből vagy számokból származó véletlenszerű nevet hoz létre. `tfOptions`leteszi a `./examples/hello-world/` mappát célzó összes Terraform parancsot. Azt is gondoskodik arról, `website_name` `websiteName`hogy van beállítva, hogy a randomizált .

Utána `terraform init`, `terraform apply` és `terraform output` kerül végrehajtásra, sorban egymás után. Egy másik segítő funkciót `HttpGetWithCustomValidation()`használunk, amelyet a Terratest biztosít. A segítő funkciót használjuk annak érdekében, hogy `homepage` a HTML-t `terraform output`feltöltsük a kimeneti URL-re, amelyet a . Összehasonlítjuk a HTTP GET `200` állapotkódot, és megkeressük a HTML-tartalom néhány kulcsszavát. Végül az `terraform destroy` "ígéretet" kap a végrehajtásra a Go `defer` funkciójának kihasználásával.

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

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestIT_HelloWorldExample
```

A hagyományos Go teszt eredménye körülbelül két perc múlva tér vissza. Az egység- és integrációs teszteket is futtathatja a következő parancsok végrehajtásával:

```shell
go fmt
go test
```

Az integrációs tesztek sokkal hosszabb időt vesznek igénybe, mint az egységtesztek (két perc egy integrációs eset esetén, míg öt egységesetében egy perc). De ez a döntés, hogy használja egység tesztek vagy integrációs tesztek egy forgatókönyvben. Általában a Terraform HCL függvények használatával az összetett logika egységtesztjeit használjuk. Általában integrációs teszteket használunk a felhasználó végpontok közötti perspektívájának.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Mage használata a Terratest esetek egyszerűbb futtatásához 
Az Azure Cloud Shellben futó tesztesetek különböző könyvtárakban különböző parancsokat igényel. Ennek a folyamatnak a hatékonyabbá tétele érdekében bevezetjük a buildrendszert a projektünkbe. Ebben a szakaszban egy Go build rendszert használunk, a mage-t a feladathoz.

A mage csak a `magefile.go` projekt gyökérkönyvtárában van megkövetelve (a következő példában jelölve): `(+)`

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

Íme egy példa `./magefile.go`a . Ebben a Go-ban írt buildszkriptben öt buildlépést valósítunk meg:
- `Clean`: A lépés eltávolítja az összes létrehozott és ideiglenes fájlokat, amelyek során generált teszt végrehajtások.
- `Format`: A `terraform fmt` lépés `go fmt` fut, és formázza a kód bázis.
- `Unit`: A lépés a mappa alatt futtatja `TestUT_*`az `./test/` összes egységtesztet (a függvénynév-konvenció használatával).
- `Integration`: A lépés `Unit`hasonló a hoz, de az egységtesztek`TestIT_*`helyett integrációs teszteket hajt végre ( ).
- `Full`: A `Clean`lépés `Format` `Unit`fut `Integration` , , és sorrendben.

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

A következő parancsokkal hajthatja végre a teljes tesztcsomagot. A kód hasonló a korábbi szakaszban használt futó lépésekhez. 

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in magefile or test cases
go fmt      # Only required when you change the magefile
mage
```

Az utolsó parancssort további mage lépésekkel is lecserélheti. Használhatja `mage unit` például a `mage clean`vagyat. Célszerű parancsokat beágyazni `dep` a `az login` magefile-be. Itt nem mutatjuk a kódot. 

A mage segítségével a Lépéseket a Go csomagrendszer használatával is megoszthatja. Ebben az esetben egyszerűsítheti a magefiles-t az összes modulban, ha csak`mg.Deps()`egy közös implementációra hivatkozik, és deklarálja a függőségeket ( ).

**Nem kötelező: Egyszerű szolgáltatáskörnyezeti változók beállítása elfogadási tesztek futtatásához**
 
A tesztek `az login` végrehajtása helyett az Azure-hitelesítést a szolgáltatásegyszerű környezeti változók beállításával végezheti el. A Terraform közzéteszi [a környezeti változók nevének listáját.](https://www.terraform.io/docs/providers/azurerm/index.html#testing) (Csak az első négy ilyen környezeti változóra van szükség.) A Terraform részletes utasításokat is közzétesz, amelyek ismertetik, hogyan [lehet megszerezni ezeknek a környezeti változóknak az értékét.](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Terratest GitHub oldal](https://github.com/gruntwork-io/terratest).