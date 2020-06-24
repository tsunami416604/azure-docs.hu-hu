---
title: ARM-sablon tesztelési eszközkészlete
description: Útmutatás a ARM-sablon tesztelési eszközkészletének futtatásához a sablonban. Az eszközkészlet segítségével megtekintheti, hogy végrehajtotta-e a javasolt eljárásokat.
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7b88096dfdd1c7fb3e2671a369132e75a8885b8d
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85255946"
---
# <a name="use-arm-template-test-toolkit"></a>ARM-sablon tesztelési eszközkészletének használata

Az [ARM-sablon tesztelési eszközkészlete](https://aka.ms/arm-ttk) ellenőrzi, hogy a sablon ajánlott eljárásokat használ-e. Ha a sablon nem felel meg az ajánlott eljárásoknak, a figyelmeztetések listáját adja vissza a javasolt módosításokkal. A test Toolkit használatával megtudhatja, hogyan kerülheti el a sablonok fejlesztésével kapcsolatos gyakori problémákat.

A test Toolkit az [alapértelmezett tesztek készletét](test-cases.md)tartalmazza. Ezek a tesztek javaslatok, de nem szükségesek. Eldöntheti, hogy mely tesztek relevánsak a céljaihoz, és hogy milyen tesztek futnak.

Ez a cikk bemutatja, hogyan futtathatja a tesztelési eszközkészletet, és hogyan adhat hozzá vagy távolíthat el teszteket. Az alapértelmezett tesztek leírását lásd: [eszközkészlet-tesztelési esetek](test-cases.md).

Az eszközkészlet PowerShell-parancsfájlok egy készlete, amely a PowerShell vagy a parancssori felület egyik parancsának használatával futtatható.

## <a name="download-test-toolkit"></a>Tesztelési eszközkészlet letöltése

A tesztelési eszközkészlet használatához elágazással és klónozással a parancsfájlokat tartalmazó [tárházat](https://aka.ms/arm-ttk) vagy a [legújabb. zip fájlt töltheti le](https://aka.ms/arm-ttk-latest).

Annak a számítógépnek a végrehajtási házirendjétől függően, amelyen futtatja a parancsfájlt, hibaüzenetet kaphat arról, hogyan futtathat parancsfájlokat az internetről. Módosítania kell a [végrehajtási házirendet](/powershell/module/microsoft.powershell.core/about/about_execution_policies) , vagy fel kell [oldania a parancsfájlok feloldását](/powershell/module/microsoft.powershell.utility/unblock-file).

## <a name="run-on-powershell"></a>Futtatás a PowerShell-lel

A tesztek futtatása előtt importálja a modult.

```powershell
Import-Module .\arm-ttk.psd1 # from the same directory as .\arm-ttk.psd1
```

A tesztek **PowerShellben**való futtatásához használja a következő parancsot:

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

## <a name="run-on-linux"></a>Futtatás Linuxon

A tesztek futtatása előtt telepítse a [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux)-t.

A **Linux** rendszerű, bash-alapú tesztek futtatásához használja a következő parancsot:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

A tesztet pwsh.exe is futtathatja.

## <a name="run-on-macos"></a>Futtatás macOS rendszeren

A tesztek futtatása előtt telepítse a [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-macos)-t. 

`coreutils` telepítése:

```bash
brew install coreutils
```

A **MacOS**rendszeren futtatott tesztek futtatásához használja a következő parancsot:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

## <a name="result-format"></a>Eredmény formátuma

Az átadott tesztek **zöld** színnel és a **[+]** előtaggal jelennek meg.

A sikertelen tesztek **piros színnel** jelennek meg, és a **[-]** előtaggal vannak ellátva.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="teszt eredményeinek megtekintése":::

A szöveges eredmények a következők:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Paraméterek tesztelése

Ha megadja a **-TemplatePath** paramétert, az eszközkészlet az azuredeploy.json vagy maintemplate.jsnevű sablonban megjelenő mappában található. Először a sablont teszteli, majd az összes többi sablont teszteli a mappában és annak almappáiban. A többi sablon csatolt sablonként van tesztelve. Ha az elérési út tartalmaz egy [CreateUiDefinition.js](../managed-applications/create-uidefinition-overview.md)nevű fájlt, akkor a felhasználói felületi definícióhoz kapcsolódó teszteket futtat.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

A mappában található egy fájl teszteléséhez adja hozzá a **-file** paramétert. A mappának azonban továbbra is rendelkeznie kell egy azuredeploy.jsvagy maintemplate.jsnevű fő sablonnal.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Alapértelmezés szerint az összes teszt le van futtatva. Az egyedi tesztek futtatásához használja a **-test** paramétert. Adja meg a teszt nevét. A nevekért lásd: az [eszközkészlet tesztelési esetei](test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Tesztek testreszabása

ARM-sablonok esetében az eszközkészlet a **\arm-ttk\testcases\deploymentTemplate**mappában található összes tesztet futtatja. Ha végleg el szeretné távolítani a tesztet, törölje a fájlt a mappából.

A [CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) -fájlok esetében a **\arm-ttk\testcases\CreateUiDefinition**mappában futtatja az összes tesztet.

Saját teszt hozzáadásához hozzon létre egy fájlt a következő elnevezési konvencióval: **Your-Custom-Test-Name.test.ps1**.

A teszt a sablont objektum-paraméterként vagy karakterlánc-paraméterként kérdezheti le. Általában az egyiket vagy a másikat kell használnia, de mindkettőt használhatja.

Akkor használja az Object paramétert, ha a sablon egy szakaszát be kell szereznie, és meg kell ismételni a tulajdonságait. Az Object paramétert használó teszt formátuma a következő:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

A sablon objektum a következő tulajdonságokkal rendelkezik:

* $schema
* contentVersion
* paraméterek
* változók
* resources
* kimenetek

Például lekérheti a paraméterek gyűjteményét a következővel: `$TemplateObject.parameters` .

Ha a teljes sablonon sztring műveletet kell végrehajtania, használja a string paramétert. A string paramétert használó teszt formátuma a következő:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Futtathat például egy reguláris kifejezést a string paraméterrel, hogy megtudja, van-e egy adott szintaxis használata.

Ha többet szeretne megtudni a teszt végrehajtásáról, tekintse meg a mappában található többi tesztet.

## <a name="integrate-with-azure-pipelines"></a>Integrálás az Azure-folyamatokkal

A tesztelési eszközkészletet hozzáadhatja az Azure-folyamathoz. Egy folyamattal a sablon minden frissítésekor futtathatja a tesztet, vagy futtathatja azt a telepítési folyamat részeként.

A tesztelési eszközkészletnek a folyamathoz való hozzáadásának legegyszerűbb módja a harmadik féltől származó bővítmények használata. A következő két bővítmény érhető el:

* [ARM TTK tesztek futtatása](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [ARM-sablon tesztelése](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

Emellett saját feladatokat is megvalósíthat. Az alábbi példa bemutatja, hogyan töltheti le a tesztelési eszközkészletet.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

A következő példa bemutatja, hogyan futtathatja a teszteket.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>További lépések

Az alapértelmezett tesztek megismeréséhez tekintse meg az [eszközkészlet tesztelési eseteivel foglalkozó](test-cases.md)témakört.
