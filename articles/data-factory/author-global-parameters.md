---
title: Globális paraméterek
description: Globális paraméterek beállítása az egyes Azure Data Factory környezetekhez
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/05/2020
ms.openlocfilehash: 052f502ed27db9ade0fd2916f91d6922c52a5a98
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854269"
---
# <a name="global-parameters-in-azure-data-factory"></a>Globális paraméterek a Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A globális paraméterek állandók egy olyan adatelőállítón keresztül, amelyet bármely kifejezésben felhasználhat egy folyamat. Akkor hasznosak, ha több, azonos paraméterekkel és értékekkel rendelkező folyamattal rendelkezik. Ha a folyamatos integrációs és üzembe helyezési folyamat (CI/CD) használatával népszerűsít egy adatelőállítót, felülbírálhatja ezeket a paramétereket az egyes környezetekben. 

## <a name="creating-global-parameters"></a>Globális paraméterek létrehozása

Globális paraméter létrehozásához nyissa meg a *kezelés* szakasz *globális paraméterek* lapját. Válassza az **új** lehetőséget a létrehozási oldal megnyitásához – NAV.

![Globális paraméterek létrehozása](media/author-global-parameters/create-global-parameter-1.png)

A Side-NAV lapon adjon meg egy nevet, válasszon ki egy adattípust, és adja meg a paraméter értékét.

![Globális paraméterek létrehozása](media/author-global-parameters/create-global-parameter-2.png)

A globális paraméter létrehozása után a paraméter nevére kattintva szerkesztheti. Ha egyszerre több paramétert szeretne módosítani, válassza az **összes szerkesztése**lehetőséget.

![Globális paraméterek létrehozása](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Globális paraméterek használata egy folyamaton belül

A globális paraméterek bármelyik [folyamat kifejezésében](control-flow-expression-language-functions.md)használhatók. Ha egy folyamat egy másik erőforrásra hivatkozik, például egy adatkészletre vagy adatfolyamatra, a globális paraméter értékét átadhatja az erőforrás paramétereinek használatával. A globális paraméterek a következőképpen vannak hivatkozva: `pipeline().globalParameters.<parameterName>` .

![Globális paraméterek használata](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a>Globális paraméterek a CI/CD-ben

A globális paraméterek egyedi CI/CD-folyamattal rendelkeznek a Azure Data Factory többi entitásához képest. Ha egy gyárat tesz közzé, vagy globális paraméterekkel exportál egy ARM-sablont, a *globalParameters* nevű mappát egyyour-factory-name_GlobalParameters.jsnevű fájllal hozza létre *a*rendszer. Ez a fájl egy JSON-objektum, amely a közzétett gyár összes globális paraméter-típusát és értékét tartalmazza.

![Globális paraméterek közzététele](media/author-global-parameters/global-parameters-adf-publish.png)

Ha egy új környezetbe (például a TEST vagy a PROD) végzi az üzembe helyezést, az ajánlott a globális Parameters fájl másolatának létrehozása és a megfelelő környezet-specifikus értékek felülírása. Az eredeti globális paraméterek ismételt közzétételekor a rendszer felülírja a fájlt, de a másik környezet másolata érintetlen marad.

Ha például egy "ADF-DEV" nevű gyárat és egy "Environment" nevű, karakterlánc típusú globális paramétert ad meg a "dev" értékkel, akkor a rendszer a következő nevű fájlt teszi közzé: *ADF-DEV_GlobalParameters.js* . Ha egy "ADF_TEST" nevű tesztelési gyárba helyezi üzembe, hozzon létre egy másolatot a JSON-fájlról (például: ADF-TEST_GlobalParameters.js), és cserélje le a paramétereket a környezeti specifikus értékekre. A "környezet" paraméternek lehet "test" értéke. 

![Globális paraméterek üzembe helyezése](media/author-global-parameters/powershell-task.png)

Az alábbi PowerShell-szkripttel előléptetheti a globális paramétereket a további környezetekhez. Vegyen fel egy Azure PowerShell DevOps feladatot az ARM-Template deployment előtt. Az DevOps feladatban meg kell adnia az új Parameters fájl, a célként megadott erőforráscsoport és a cél adat-előállító helyét.

> [!NOTE]
> Globális paraméterek a PowerShell használatával történő telepítéséhez az az modul legalább 4.4.0 kell használnia.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Következő lépések

* A Azure Data Factory [folyamatos integrációs és üzembe helyezési folyamatának](continuous-integration-deployment.md) megismerése
* Megtudhatja, hogyan használhatja a [vezérlési folyamat kifejezésének nyelvét](control-flow-expression-language-functions.md)