---
title: Folyamatos integráció és kézbesítés Azure Data Factory
description: Megtudhatja, hogyan használhatja a folyamatos integrációt és a szállítást, hogy Data Factory folyamatokat helyezzen át egy környezetből (fejlesztés, tesztelés, termelés) egy másikra.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 7c5c1e91e97087bf28b03629659e5194f67c22b3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680026"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Folyamatos integráció és szállítás (CI/CD) Azure Data Factory

## <a name="overview"></a>Áttekintés

A folyamatos integráció az a gyakorlat, amellyel a rendszer automatikusan és a lehető leghamarabb teszteli a kód minden módosítását. A folyamatos teljesítés a folyamatos integráció során megjelenő tesztelést követi, és leküldi a módosításokat egy átmeneti vagy éles rendszerbe.

Azure Data Factory a folyamatos integráció & a szállítás az Data Factory folyamatok egyik környezetből a másikba való áthelyezését jelenti (fejlesztési, tesztelési, éles). A folyamatos integráció & a továbbításhoz Data Factory UX-integrációt használhat Azure Resource Manager-sablonokkal. A Data Factory UX Resource Manager-sablont tud előállítani az **ARM-sablon** legördülő menüjéből. Ha az **ARM-sablon exportálása**lehetőséget választja, a portál létrehozza a Resource Manager-sablont az adatok előállítójának, valamint egy olyan konfigurációs fájlt, amely tartalmazza az összes kapcsolati karakterláncot és egyéb paramétert. Ezután létre kell hoznia egy konfigurációs fájlt az egyes környezetekhez (fejlesztés, tesztelés, éles környezet). A Resource Manager-sablonfájl fő fájlja az összes környezet esetében azonos marad.

A szolgáltatás kilenc perces bemutatása és bemutatása esetén tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>Folyamatos integráció életciklusa

Az alábbiakban áttekintheti az Azure Repos git szolgáltatással konfigurált Azure-beli adatelőállítók folyamatos integrációjának és továbbításának életciklusát. A git-adattárak konfigurálásával kapcsolatos további információkért lásd: [a verziókövetés a Azure Data Factory](source-control.md).

1.  A fejlesztői adat-előállító létrehozása és konfigurálása az Azure Repos git használatával történik, ahol minden fejlesztő jogosult a Data Factory erőforrások, például folyamatok és adatkészletek létrehozásához.

1.  Ahogy a fejlesztők módosításokat végeznek a szolgáltatási ágban, a legutóbbi módosításokkal a folyamatuk hibakeresését végzik. A folyamatok futtatásának hibakeresésével kapcsolatos további információkért lásd: [iterációs fejlesztés és hibakeresés Azure Data Factory](iterative-development-debugging.md)használatával.

1.  Miután a fejlesztők elégedettek lesznek a módosításaival, létrehozhatnak egy lekéréses kérelmet a szolgáltatási ágra a Master vagy az Collaboration ágban, hogy a társaik áttekintsék a módosításokat.

1.  A lekéréses kérelem jóváhagyása és a módosítások egyesítése a Master ágban történik, így közzétehető a fejlesztői gyárban.

1.  Amikor a csapat készen áll a tesztek telepítésére a test Factoryban, majd az üzemi gyárba, exportálják a Resource Manager-sablont a Master ág alapján.

1.  Az exportált Resource Manager-sablon különböző paraméterekkel lesz üzembe helyezve a test Factory és az üzemi gyár számára.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Resource Manager-sablon létrehozása az egyes környezetekhez

Az **ARM-sablon** legördülő menüben válassza az **ARM-sablon exportálása** lehetőséget, hogy exportálja a Resource Manager-sablont az adatok előállítójának a fejlesztési környezetben.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

A tesztelési és éles adatgyárak területen válassza az **ARM-sablon importálása**lehetőséget. Ez a művelet elvégzi a Azure Portal, ahol importálhatja az exportált sablont. Válassza a **saját sablon létrehozása lehetőséget a szerkesztőben** a Resource Manager-sablon szerkesztőjének megnyitásához.

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

Kattintson a **fájl betöltése** elemre, és válassza ki a generált Resource Manager-sablont.

![](media/continuous-integration-deployment/continuous-integration-image4.png)

A beállítások ablaktáblában adja meg a konfigurációs értékeket, például a társított szolgáltatás hitelesítő adatait. Ha elkészült, kattintson a **vásárlás** elemre a Resource Manager-sablon telepítéséhez.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Kapcsolati sztringek

A kapcsolódási karakterláncok konfigurálásával kapcsolatos információk az egyes összekötők cikkeiben találhatók. Azure SQL Database esetében például az [adatok másolása Azure SQL Databaseba vagy a Azure Data Factory használatával](connector-azure-sql-database.md)című részből tájékozódhat. A kapcsolódási karakterlánc ellenőrzéséhez megnyithatja az erőforráshoz tartozó kódot a Data Factory UX-ben. A kód nézetben a rendszer eltávolítja a kapcsolatok karakterláncának jelszavát vagy a fiók kulcsát. A kód nézet megnyitásához válassza ki az alábbi képernyőképen Kiemelt ikont.

![A kapcsolódási karakterlánc megjelenítéséhez nyissa meg a kód nézetet](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>A folyamatos integráció automatizálása az Azure-folyamatok kiadásaival

Az alábbi útmutató egy Azure-beli folyamatok kiadásának beállítására szolgál, amely automatizálja egy adatelőállító üzembe helyezését több környezetbe.

![Az Azure-folyamatokkal való folyamatos integráció diagramja](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Követelmények

-   Team Foundation Server-vagy Azure-adattárakhoz kapcsolódó Azure-előfizetés a [Azure Resource Manager szolgáltatási végpont](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)használatával.

-   Az Azure Repos git-integrációval konfigurált Data Factory.

-   Az egyes környezetekhez tartozó titkokat tartalmazó [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) .

### <a name="set-up-an-azure-pipelines-release"></a>Azure-folyamatok kiadásának beállítása

1.  Az [Azure DevOps felhasználói felületén](https://dev.azure.com/)nyissa meg a Data Factory konfigurált projektet.

1.  A lap bal oldalán kattintson a **folyamatok** , majd a **kiadások**elemre.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Válassza az **új folyamat** lehetőséget, vagy ha rendelkezik meglévő folyamatokkal, **új**, majd **új kiadási folyamattal**.

1.  Válassza ki az **üres** sablont.

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  A **szakasz neve** mezőben adja meg a környezet nevét.

1.  Válassza az **összetevő hozzáadása**lehetőséget, és válassza ki ugyanazt a tárházat, amelyet a Data Factory konfigurál. Válassza a `adf_publish` lehetőséget alapértelmezett ágként a legújabb alapértelmezett verzióval.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager telepítési feladat hozzáadása:

    a.  A fázis nézetben kattintson a **megtekintési szakasz feladatok** hivatkozásra.

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Hozzon létre egy új feladatot. Keresse meg az **Azure erőforráscsoport-telepítést**, és kattintson a **Hozzáadás**gombra.

    c.  A központi telepítési feladatban válassza ki az előfizetést, az erőforráscsoportot és a cél Data Factory helyét, és szükség esetén adja meg a hitelesítő adatokat.

    d.  A művelet legördülő menüben válassza az **erőforráscsoport létrehozása vagy frissítése**lehetőséget.

    e.  Válassza a **... lehetőséget.** a **sablon** mezőben. Az [egyes környezetekhez tartozó Resource Manager-sablon létrehozása](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment)lépéssel keresse meg a Azure Resource Manager-sablon létrehozása az **ARM-sablon** használatával című szakaszát. Keresse meg ezt a fájlt a `adf_publish` ág `<FactoryName>` mappában.

    f.  Válassza a **... lehetőséget.** a **sablon paraméterei mezőben.** a parameters (paraméterek) fájl kiválasztásához. Válassza ki a megfelelő fájlt attól függően, hogy létrehozott-e egy másolatot, vagy az alapértelmezett *ARMTemplateParametersForFactory. JSON*fájlt használja.

    g.  Válassza a **... lehetőséget.** a **felülbírálási sablon paraméterei** mező mellett adja meg a cél Data Factory információit. A Key vaultból érkező hitelesítő adatok esetében adja meg a titkos nevet idézőjelek között. Ha például a titok neve `cred1`, akkor adja meg a `"$(cred1)"`értékét.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. Válassza ki a **növekményes** központi telepítési módot.

    > [!WARNING]
    > Ha a **teljes** telepítési mód lehetőséget választja, előfordulhat, hogy a meglévő erőforrások törölve lesznek, beleértve a Resource Manager-sablonban nem definiált cél erőforráscsoport összes erőforrását.

1.  A kiadási folyamat mentése.

1. A kiadás elindításához kattintson a **kiadás létrehozása** lehetőségre.

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Titkok beolvasása Azure Key Vault

Ha a titkokat egy Azure Resource Manager-sablon átadására használja, javasoljuk, hogy a Azure Key Vault használatát az Azure-folyamatok kiadásával.

A titkokat kétféleképpen kezelheti:

1.  Adja hozzá a titkokat a parameters (paraméterek) fájlhoz. További információ: [a Azure Key Vault használata a biztonságos paraméterek értékének](../azure-resource-manager/resource-manager-keyvault-parameter.md)átadására az üzembe helyezés során.

    -   Hozzon létre egy másolatot a közzétételi ágra feltöltött Parameters fájlról, és állítsa be a Key vaultból beolvasni kívánt paraméterek értékeit a következő formátumban:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   Ha ezt a módszert használja, a titkos kulcsot a rendszer automatikusan kihúzta a kulcstartóból.

    -   A paraméterek fájljának a közzétételi ágban is szerepelnie kell.

1.  Vegyen fel egy [Azure Key Vault feladatot](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) az előző szakaszban ismertetett Azure Resource Manager üzembe helyezés előtt:

    -   Válassza a **feladatok** fület, hozzon létre egy új feladatot, keressen rá **Azure Key Vault** és vegye fel.

    -   A Key Vault feladatban válassza ki azt az előfizetést, amelyben létrehozta a kulcstartót, adja meg a hitelesítő adatokat, ha szükséges, majd válassza ki a kulcstartót.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Engedélyek megadása az Azure-folyamatok ügynökének

A Azure Key Vault feladat sikertelen lehet, ha a megfelelő engedélyek nincsenek jelen a hozzáférés-megtagadási hibával. Töltse le a kiadáshoz tartozó naplókat, és keresse meg az `.ps1` fájlt a paranccsal, hogy engedélyt adjon az Azure-folyamatok ügynökének. A parancsot közvetlenül is futtathatja, vagy átmásolhatja a résztvevő AZONOSÍTÓját a fájlból, és manuálisan is hozzáadhatja a hozzáférési házirendet a Azure Portal. A **Get** és a **List** a minimálisan szükséges engedélyek.

### <a name="update-active-triggers"></a>Aktív eseményindítók frissítése

A központi telepítés meghiúsulhat, ha megpróbál frissíteni az aktív eseményindítókat. Az aktív eseményindítók frissítéséhez manuálisan kell leállítania őket, és el kell indítania őket a telepítés után. Ezt egy Azure PowerShell-feladaton keresztül teheti meg.

1.  A kiadás feladatok lapján adjon hozzá egy **Azure PowerShell** -feladatot.

1.  Válassza a **Azure Resource Manager** lehetőséget a kapcsolattípus mezőben, majd válassza ki az előfizetését.

1.  A parancsfájl típusaként válassza a **beágyazott parancsfájl** lehetőséget, majd adja meg a kódot. A következő példa leállítja az eseményindítókat:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

A telepítés után a hasonló lépések (a `Start-AzDataFactoryV2Trigger` függvénnyel) követheti újra az eseményindítókat.

> [!IMPORTANT]
> A folyamatos integrációs és üzembe helyezési forgatókönyvekben a Integration Runtime típusnak azonosnak kell lennie a különböző környezetek között. Ha például a fejlesztői környezetben van egy *helyi Integration Runtime (* IR), akkor ugyanazt az IR-t más környezetekben, például tesztelési és üzemi környezetben is önálló üzemeltetéssel kell *működtetni* . Hasonlóképpen, ha több fázisban osztja meg az integrációs modulokat, az integrációs modulokat az összes környezetben, például a fejlesztés, a tesztelés és az éles környezetek *szerint kell* konfigurálni.

#### <a name="sample-prepostdeployment-script"></a>Minta előtti/postdeployment parancsfájl

Az alábbi példa egy parancsfájlt tartalmaz az eseményindítók leállításához az üzembe helyezés előtt és az eseményindítók újraindítását követően. A parancsfájl emellett kódot is tartalmaz az eltávolított erőforrások törléséhez. A Azure PowerShell legújabb verziójának telepítéséhez tekintse [meg a Azure PowerShell telepítése Windows rendszerre a PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps)használatával című témakört.

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Egyéni paraméterek használata a Resource Manager-sablonnal

Ha GIT módban van, felülbírálhatja a Resource Manager-sablonban található alapértelmezett tulajdonságokat a sablonban és a nehezen kódolt tulajdonságok által megadott tulajdonságok beállításához. A következő forgatókönyvekben érdemes lehet felülbírálni az alapértelmezett paraméterezés-sablont:

* Automatikus CI/CD-t használ, és módosítani szeretné néhány tulajdonságot a Resource Manager üzembe helyezése során, de a tulajdonságok alapértelmezés szerint nem paraméterek.
* A gyár olyan nagy méretű, hogy az alapértelmezett Resource Manager-sablon érvénytelen, mert több, mint a maximálisan megengedett paraméterek (256).

Az alapértelmezett paraméterezés-sablon felülbírálásához a következő feltételek szerint hozzon létre egy *ARM-template-Parameters-definition. JSON* nevű fájlt az adattár gyökérkönyvtárában. A fájl nevének pontosan egyeznie kell. Data Factory megpróbálja beolvasni ezt a fájlt attól függően, hogy melyik ágban van jelenleg a Azure Data Factory portálon, nem csak az együttműködési ágban. A fájlt létrehozhatja vagy szerkesztheti egy privát ág alapján, ahol a felhasználói felületen az **EXPORTÁLÁS ARM sablonnal** is tesztelheti a módosításokat. Ezután egyesítheti a fájlt az együttműködési ág használatával. Ha nem található fájl, a rendszer az alapértelmezett sablont használja.


### <a name="syntax-of-a-custom-parameters-file"></a>Egyéni paraméterek fájljának szintaxisa

Az egyéni paraméterek fájljának létrehozásakor az alábbi irányelvek használhatók. A fájl az egyes entitások típusának egy szakaszát tartalmazza: trigger, folyamat, társított szolgáltatás, adatkészlet, integrációs futtatókörnyezet stb.
* Adja meg a tulajdonság elérési útját a megfelelő entitás típusa mezőben.
* Ha a tulajdonság nevét "\*" értékre állítja, akkor azt jelzi, hogy az összes tulajdonságot le szeretné parametrizálja (csak az első szintre, nem rekurzív módon). Ezen kívül kivételeket is megadhat.
* Ha egy tulajdonság értékét karakterláncként állítja be, akkor azt jelzi, hogy a tulajdonságot parametrizálja kívánja. Használja a `<action>:<name>:<stype>`formátumot.
   *  `<action>` a következő karakterek egyike lehet:
      * `=` azt jelenti, hogy az aktuális értéket a paraméter alapértelmezett értékeként kell megőrizni.
      * `-` azt jelenti, hogy nem tartja meg a paraméter alapértelmezett értékét.
      * a `|` egy speciális eset a titkokhoz Azure Key Vault a kapcsolatok karakterláncai vagy kulcsai számára.
   * `<name>` a paraméter neve. Ha üres, akkor a tulajdonság nevét veszi fel. Ha az érték `-` karakterrel kezdődik, a név lerövidítve lesz. `AzureStorage1_properties_typeProperties_connectionString` például lerövidíti a `AzureStorage1_connectionString`.
   * `<stype>` a paraméter típusa. Ha `<stype>` üres, az alapértelmezett típus `string`. Támogatott értékek: `string`, `bool`, `number`, `object`és `securestring`.
* Amikor megad egy tömböt a definíciós fájlban, azt jelzi, hogy a sablonban szereplő egyező tulajdonság egy tömb. Data Factory a tömbben lévő összes objektumra vonatkozóan a tömb Integration Runtime objektumában megadott definíció használatával. A második objektum, egy karakterlánc, a tulajdonság neve lesz, amely az egyes iterációk paraméterének neveként szerepel.
* Az adott erőforrás-példányra vonatkozó definíció nem lehet. Bármely definíció az adott típusú összes erőforrásra vonatkozik.
* Alapértelmezés szerint az összes biztonságos karakterlánc, például a Key Vault titkos kódok és a biztonságos karakterláncok, például a kapcsolati karakterláncok, kulcsok és jogkivonatok paraméterei.
 
### <a name="sample-parameterization-template"></a>Minta paraméterezés-sablon

Az alábbiakban egy példát láthat arra, hogy a paraméterezés-sablonok hogyan néznek ki:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Alább látható a fenti sablon kialakításának magyarázata, erőforrás típusa szerint lebontva.

#### <a name="pipelines"></a>Folyamatok
    
* A Path tevékenységek/typeProperties/waitTimeInSeconds bármely tulajdonsága paraméteres. A folyamat minden olyan tevékenysége, amely `waitTimeInSeconds` (például a `Wait` tevékenység) nevű programkód szintű tulajdonsággal rendelkezik, alapértelmezett névvel van paraméterként. A Resource Manager-sablonban azonban nem szerepel alapértelmezett érték. A Resource Manager üzembe helyezése során kötelezően megadandó adatok lesznek.
* Hasonlóképpen, egy `headers` nevű tulajdonság (például egy `Web` tevékenység) paraméterének típusa `object` (JObject). Alapértelmezett értékkel rendelkezik, amely megegyezik a forrás-előállítóval megegyező értékkel.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Az elérési út `typeProperties` összes tulajdonsága a megfelelő alapértelmezett értékekkel van ellátva. Például két tulajdonság van a **IntegrationRuntimes** típus tulajdonságainál: `computeProperties` és `ssisProperties`. Mindkét tulajdonság típusa a megfelelő alapértelmezett értékekkel és típusokkal (objektummal) jön létre.

#### <a name="triggers"></a>Eseményindítók

* A `typeProperties`alatt két tulajdonság paraméteres. Az első egy `maxConcurrency`, amely alapértelmezett értékkel rendelkezik, és`string`típusú. Az alapértelmezett paraméter neve `<entityName>_properties_typeProperties_maxConcurrency`.
* A `recurrence` tulajdonság is paraméteres. Ebben az esetben az adott szinten lévő összes tulajdonságot karakterláncként kell megadni, alapértelmezett értékekkel és paraméterek nevével. Kivételt képeznek a `interval` tulajdonság, amely a szám típusaként van paraméterként, a paraméter neve pedig `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Hasonlóképpen, a `freq` tulajdonság egy karakterlánc, és karakterláncként van paraméterként. Az `freq` tulajdonság azonban alapértelmezett érték nélkül van paraméterben. A név rövidítve és utótaggal van elnevezve. Például: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* A társított szolgáltatások egyediek. Mivel a társított szolgáltatások és adatkészletek sokféle típusúak, a típus-specifikus testreszabást is megadhatja. Ebben a példában minden `AzureDataLakeStore`típusú társított szolgáltatás lesz alkalmazva, és minden más (\*-on keresztül) egy másik sablon lesz alkalmazva.
* A `connectionString` tulajdonság `securestring` értékként lesz paraméterként, nem lesz alapértelmezett értéke, és egy rövidített paraméter neve lesz, amely a `connectionString`utótaggal van ellátva.
* A tulajdonság `secretAccessKey` történik `AzureKeyVaultSecret` (például egy `AmazonS3` társított szolgáltatásban). Automatikusan Azure Key Vault titokként van konfigurálva, és a konfigurált kulcstartóból beolvasva. Saját maga is parametrizálja a kulcstartót.

#### <a name="datasets"></a>Adathalmazok

* Bár a típus-specifikus Testreszabás elérhető az adatkészletekhez, a konfigurációt a \*szintű konfiguráció explicit beállítása nélkül is megadhatja. A fenti példában a `typeProperties` alatti összes adatkészlet-tulajdonság paraméteres.

### <a name="default-parameterization-template"></a>Alapértelmezett paraméterezés-sablon

Alább látható az aktuális alapértelmezett paraméterezés-sablon. Ha csak egy vagy több paramétert kell felvennie, akkor a közvetlen szerkesztés hasznos lehet, mivel nem fogja elveszíteni a meglévő paraméterezés-struktúrát.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

Az alábbi példa bemutatja, hogyan adhat hozzá egyetlen értéket az alapértelmezett paraméterezés-sablonhoz. Csak a Databricks társított szolgáltatáshoz meglévő Databricks interaktív fürt-azonosítót szeretnénk hozzáadni a parameters (paraméterek) fájlhoz. Vegye figyelembe, hogy az alábbi fájl ugyanaz, mint a fenti fájl, kivéve `existingClusterId` a `Microsoft.DataFactory/factories/linkedServices`Properties (Tulajdonságok) mezőjében.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Társított Resource Manager-sablonok

Ha már beállította a folyamatos integrációt és üzembe helyezést (CI/CD) az adatelőállítók számára, akkor a Azure Resource Manager-sablonra korlátozhatja, mivel a gyár nagyobb mértékben növekszik. A határértékek például a Resource Manager-sablonokban található erőforrások maximális száma. A nagyméretű gyárak befogadásához, valamint a gyár teljes Resource Manager-sablonjának létrehozásához Data Factory a társított Resource Manager-sablonokat is létrehoz. Ezzel a szolgáltatással a teljes gyári adattartalom több fájlra van bontva, így a határértékek nem futnak.

Ha a git-t konfigurálta, a csatolt sablonok a `adf_publish` ág teljes Resource Manager-sablonjai mellett jönnek létre, és a `linkedTemplates`nevű új mappa alatt lesznek mentve.

![Társított Resource Manager-sablonok mappája](media/continuous-integration-deployment/linked-resource-manager-templates.png)

A társított Resource Manager-sablonok általában fősablonnal és a főkiszolgálóhoz csatolt alárendelt sablonokkal rendelkeznek. A fölérendelt sablon neve `ArmTemplate_master.json`, és a rendszer a (z) `ArmTemplate_0.json`, `ArmTemplate_1.json`stb. mintát tartalmazó alárendelt sablonokat nevezi el. Ha a teljes Resource Manager-sablon helyett csatolt sablonokat szeretne használni, frissítse a CI/CD-feladatot úgy, hogy `ArmTemplateForFactory.json` (a teljes Resource Manager-sablon) helyett `ArmTemplate_master.json` mutasson. A Resource Manager emellett azt is megköveteli, hogy a csatolt sablonokat egy Storage-fiókba töltse fel, hogy az Azure hozzáférhessen az üzembe helyezés során. További információ: [összekapcsolt ARM-sablonok üzembe helyezése a vsts](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Ne felejtse el hozzáadni az Data Factory szkripteket a CI/CD-folyamathoz az üzembe helyezési feladat előtt és után.

Ha nem rendelkezik a git-konfigurációval, a csatolt sablonok az **exportálási ARM-sablon** kézmozdulatán keresztül érhetők el.

## <a name="hot-fix-production-branch"></a>Éles üzemen kívüli ág javítása

Ha a gyárat éles környezetben helyezi üzembe, és egy olyan hibát tapasztal, amelyet azonnal meg kell oldani, de nem tudja telepíteni az aktuális együttműködési ágat, előfordulhat, hogy egy gyors javítást kell telepítenie. Ez a megközelítés a Quick-Fix Engineering vagy a QFE. 

1.  Az Azure DevOps nyissa meg az éles környezetben üzembe helyezett kiadást, és keresse meg a legutóbb telepített véglegesítet.

2.  A véglegesítő üzenetből szerezze be az együttműködési ág véglegesítő AZONOSÍTÓját.

3.  Hozzon létre egy új, gyors javítási ágat ebből a véglegesítve.

4.  Lépjen a Azure Data Factory UX-re, és váltson erre az ágra.

5.  A Azure Data Factory UX használatával javítsa ki a hibát. Tesztelje a módosításokat.

6.  Miután megtörtént a javítás ellenőrzése, kattintson az **ARM-sablon exportálása** elemre, hogy beolvassa a forró megoldás Resource Manager-sablonját.

7.  Ezt a buildet manuálisan is bejelölheti az adf_publish ág.

8.  Ha úgy állította be a kiadási folyamatot, hogy adf_publish-bejelentkezések alapján automatikusan aktiválódik, egy új kiadás automatikusan elindul. Ellenkező esetben manuálisan kell várólistát adni a kiadáshoz.

9.  Telepítse a gyors javítási kiadást a tesztelési és a termelési gyárakba. Ez a kiadás tartalmazza az előző éles adattartalmat, valamint az 5. lépésben végrehajtott javítást.

10. Adja hozzá a módosításokat a gyors javítás a fejlesztési ágra, hogy a későbbi kiadások ne fussanak ugyanabba a hibába.

## <a name="best-practices-for-cicd"></a>Ajánlott eljárások CI/CD-hez

Ha git-integrációt használ a saját adatgyárával, és rendelkezik egy CI/CD-folyamattal, amely a változásokat a fejlesztésből teszteli, majd éles környezetbe helyezi, javasoljuk az alábbi ajánlott eljárásokat:

-   **Git-integráció**. Csak a fejlesztői adatgyárat kell a git-integrációval konfigurálnia. A tesztelés és a gyártás változásai a CI/CD-n keresztül települnek, és nincs szükség git-integrációra.

-   **Data Factory CI/CD-szkriptet**. A CI/CD-ben a Resource Manager üzembe helyezési lépése előtt bizonyos feladatok szükségesek, például az eseményindítók leállítása/indítása és karbantartása. Javasoljuk, hogy az üzembe helyezés előtt és után PowerShell-parancsfájlokat használjon. További információ: az [aktív eseményindítók frissítése](#update-active-triggers). 

-   **Integrációs modulok és megosztás**. Az integrációs modulok nem változnak gyakran, és a CI/CD minden fázisában hasonlóak. Ennek eredményeképpen a Data Factory a CI/CD összes fázisában ugyanazt a nevet és azonos típusú integrációs modult várja. Ha az integrációs modulokat minden fázisban meg szeretné osztani, érdemes lehet egy Ternáris-gyárat használni, amely csak a megosztott integrációs modulokat tartalmazza. Ezt a megosztott gyárat az összes környezetében használhatja társított Integration Runtime-típusként.

-   **Key Vault**. Ha Azure Key Vault-alapú társított szolgáltatásokat használ, a különböző környezetekhez külön kulcstartók használatával további előnyöket is igénybe vehet. Mindegyikhez külön jogosultsági szint is konfigurálható. Előfordulhat például, hogy nem szeretné, hogy a csapattagok rendelkezzenek a termelési titkokhoz szükséges engedélyekkel. Ha ezt a módszert választja, akkor azt javasoljuk, hogy az összes fázisban ugyanazokat a titkos neveket tartsa meg. Ha megtartja ugyanazokat a neveket, nem kell módosítania a Resource Manager-sablonokat CI/CD-környezetekben, mivel az egyetlen dolog, ami megváltoztatja a Key Vault nevét, amely a Resource Manager-sablon paramétereinek egyike.

## <a name="unsupported-features"></a>Nem támogatott funkciók

- A tervezés szerint az ADF _nem_ engedélyezi a Cherry-picking vagy az erőforrások szelektív közzétételét. A közzététel magában foglalja az adatelőállítóban végrehajtott **összes** változást.

    - A adat-előállító entitások egymástól függenek, például az eseményindítók függnek a folyamattól, a folyamatok pedig az adatkészlettől és más folyamattól függenek. Az erőforrások egy részhalmazának szelektív közzététele _nem_ várt viselkedést és hibákat eredményezhet
    - Ritka esetekben, amikor szelektív közzétételre van szükség, érdemes lehet egy gyors javítást megfontolni. További információ: [gyors üzemi ág](#hot-fix-production-branch)

-   Nem tehet közzé privát ágakból

-   Mostantól nem futtathat projekteket a bitbucket-on
