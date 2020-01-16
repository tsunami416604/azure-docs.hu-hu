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
ms.openlocfilehash: 0e4b2cd208e11f7696e016d3fa4353b38f3060d8
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977526"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Folyamatos integráció és kézbesítés Azure Data Factory

## <a name="overview"></a>Áttekintés

A folyamatos integráció az a gyakorlat, amellyel a rendszer automatikusan és a lehető leghamarabb teszteli a kód egyes módosításait. A folyamatos teljesítés a folyamatos integráció során megjelenő tesztelést követi, és leküldi a módosításokat egy átmeneti vagy éles rendszerbe.

Azure Data Factory a folyamatos integráció és a szállítás (CI/CD) azt jelenti, hogy Data Factory folyamatokat az egyik környezetből (fejlesztési, tesztelési, éles) áthelyezi egy másikra. Data Factory UX-integrációt Azure Resource Manager-sablonokkal CI/CD-t használhat.

A Data Factory UX-ben létrehozhat egy Resource Manager-sablont az ARM- **sablon** legördülő menüjéből. Ha az **ARM-sablon exportálása**lehetőséget választja, a portál létrehozza a Resource Manager-sablont az adatok előállítója számára, valamint egy olyan konfigurációs fájlt, amely tartalmazza az összes kapcsolódási karakterláncot és egyéb paramétert. Ezután hozzon létre egy konfigurációs fájlt az egyes környezetekhez (fejlesztés, tesztelés, éles környezet). A Resource Manager-sablonfájl fő fájlja az összes környezet esetében azonos marad.

A funkció és a bemutató kilenc perces bemutatása esetén tekintse meg ezt a videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD-életciklus

Alább látható egy példa az Azure Repos git szolgáltatással konfigurált Azure-beli adatgyár CI/CD-életciklusának áttekintésére. A git-adattárak konfigurálásával kapcsolatos további információkért lásd: [a verziókövetés a Azure Data Factory](source-control.md).

1.  Egy fejlesztési célú adatelőállítót hoz létre és konfigurál az Azure Repos git szolgáltatással. Minden fejlesztőnek jogosultnak kell lennie Data Factory erőforrások, például folyamatok és adatkészletek létrehozásához.

1.  Ahogy a fejlesztők módosításokat hajtanak végre a szolgáltatási ágakban, hibakeresésük folyamata a legutóbbi módosításokkal együtt fut. A folyamatok futtatásának hibakeresésével kapcsolatos további információkért lásd: [iterációs fejlesztés és hibakeresés Azure Data Factory](iterative-development-debugging.md)használatával.

1.  Miután a fejlesztők elégedettek voltak a módosításaikkal, létrehozhatnak egy lekéréses kérelmet a szolgáltatási ágra a Master vagy az Collaboration ágban, hogy a társaik áttekintsék a módosításokat.

1.  A lekéréses kérelem jóváhagyása és a módosítások egyesítése a Master ág esetében a módosításokat közzéteheti a fejlesztői gyárban.

1.  Amikor a csapat készen áll a test Factory változásainak üzembe helyezésére, majd az üzemi gyárra, a csapat exportálja a Resource Manager-sablont a fő ágra.

1.  Az exportált Resource Manager-sablon különböző paraméterekkel van telepítve a test Factory és az üzemi gyár számára.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Resource Manager-sablon létrehozása az egyes környezetekhez

1. Az **ARM-sablon** listában válassza az **ARM-sablon exportálása** lehetőséget, hogy a fejlesztési környezetben exportálja a Resource Manager-sablont az adatgyárhoz.

   ![Resource Manager-sablon exportálása](media/continuous-integration-deployment/continuous-integration-image1.png)

1. A tesztelési és éles adatgyárak területen válassza az **ARM-sablon importálása**lehetőséget. Ez a művelet elvégzi a Azure Portal, ahol importálhatja az exportált sablont. Válassza a **saját sablon létrehozása lehetőséget a szerkesztőben** a Resource Manager-sablon szerkesztőjének megnyitásához.

   ![Saját sablon létrehozása](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Válassza a **fájl betöltése**lehetőséget, majd válassza ki a generált Resource Manager-sablont.

   ![Sablon szerkesztése](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. A beállítások szakaszban adja meg a konfigurációs értékeket, például a társított szolgáltatás hitelesítő adatait. Ha elkészült, válassza a **vásárlás** lehetőséget a Resource Manager-sablon telepítéséhez.

   ![Beállítások szakasz](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Kapcsolati sztringek

A kapcsolódási karakterláncok konfigurálásával kapcsolatos információkért tekintse meg az összekötő cikkét. Azure SQL Database esetében például az [adatok másolása Azure SQL Databaseba vagy a Azure Data Factory használatával](connector-azure-sql-database.md)című részből tájékozódhat. A kapcsolódási karakterlánc ellenőrzéséhez megnyithatja az erőforráshoz tartozó kódot a Data Factory UX-ben. A kód nézetben a rendszer eltávolítja a kapcsolatok karakterláncának jelszavát vagy a fiók kulcsát. A kód nézet megnyitásához válassza ki a Kiemelt ikont:

![A kapcsolódási karakterlánc megjelenítéséhez nyissa meg a kód nézetet](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Folyamatos integráció automatizálása az Azure-folyamatok kiadásaival

Az alábbi útmutató az Azure-folyamatok kiadásának beállításához nyújt útmutatást, amely automatizálja az adatelőállítók több környezetbe való telepítését.

![Az Azure-folyamatokkal való folyamatos integráció diagramja](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Követelmények

-   A Visual Studio Team Foundation Serverhoz vagy az [Azure Resource Manager szolgáltatási végpontot](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)használó Azure reposhez kapcsolódó Azure-előfizetés.

-   Az Azure Repos git-integrációja által konfigurált adatelőállító.

-   Egy [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , amely az egyes környezetek titkait tartalmazza.

### <a name="set-up-an-azure-pipelines-release"></a>Azure-folyamatok kiadásának beállítása

1.  Az [Azure DevOps](https://dev.azure.com/)nyissa meg az adatai-előállítóval konfigurált projektet.

1.  A lap bal oldalán válassza a **folyamatok**, majd a **kiadások**elemet.

    ![Folyamatok, kiadások kiválasztása](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Válassza az **új folyamat**lehetőséget, vagy ha meglévő folyamatokkal rendelkezik, válassza az **új** , majd az **új kiadási folyamat**lehetőséget.

1.  Válassza ki az **üres** sablont.

    ![Üres feladatok kiválasztása](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  A **szakasz neve** mezőben adja meg a környezet nevét.

1.  Válassza az összetevő **hozzáadása**lehetőséget, majd válassza ki az adattárakkal konfigurált tárházat. Válassza ki a **adf_publish** az **alapértelmezett ág**számára. Az **alapértelmezett verziónál**válassza a **legutóbbi lehetőséget az alapértelmezett ág**lehetőségnél.

    ![Összetevő hozzáadása](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager telepítési feladat hozzáadása:

    a.  A fázis nézetben válassza a **fázis-feladatok megtekintése**lehetőséget.

    ![Szakasz nézet](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Hozzon létre egy új feladatot. Keressen rá az **Azure erőforráscsoport-telepítésre**, majd válassza a **Hozzáadás**lehetőséget.

    c.  A központi telepítési feladat területen válassza ki az előfizetést, az erőforráscsoportot és a célhelyet a cél adat-előállító számára. Szükség esetén adja meg a hitelesítő adatokat.

    d.  A **művelet** listában válassza az **erőforráscsoport létrehozása vagy frissítése**lehetőséget.

    e.  Kattintson a három pont gombra ( **...** ) a **sablon** mező mellett. Keresse meg a jelen cikk [Resource Manager-sablon létrehozása az egyes környezetekhez](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) című részében, az **import ARM sablonnal** létrehozott Azure Resource Manager-sablont. Keresse meg ezt a fájlt a adf_publish ág <FactoryName> mappájában.

    f.  Válassza a **... lehetőséget.** a **sablon paramétereinek** mező mellett válassza ki a paramétereket tartalmazó fájlt. A kiválasztott fájl attól függ, hogy létrehozott-e egy másolatot, vagy az alapértelmezett fájlt, a ARMTemplateParametersForFactory. JSON-t használja.

    g.  Válassza a **... lehetőséget.** a **felülbírálási sablon paraméterei** mező mellett adja meg a cél adat-előállító adatait. Azure Key Vaultból származó hitelesítő adatok esetén adja meg a titkos kulcs nevét idézőjelek között. Ha például a titkos kód neve cred1, akkor adja meg a **"$ (cred1)"** értéket ehhez az értékhez.

    h. Válassza a **növekményes** lehetőséget a **központi telepítési módhoz**.

    > [!WARNING]
    > Ha a **Befejezés** lehetőséget választja a **központi telepítési módhoz**, a meglévő erőforrások törölhetők, beleértve a Resource Manager-sablonban nem definiált cél erőforráscsoport összes erőforrását.

    ![Gyártási Data Factory](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  A kiadási folyamat mentése.

1. A kiadás elindításához válassza a **kiadás létrehozása**lehetőséget.

   ![Válassza a kiadás létrehozása lehetőséget](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Titkok beolvasása Azure Key Vault

Ha Azure Resource Manager-sablonban titkos kulcsokkal rendelkezik, javasoljuk, hogy a Azure Key Vaultt az Azure-folyamatok kiadásával használja.

A titkokat kétféleképpen kezelheti:

1.  Adja hozzá a titkokat a parameters (paraméterek) fájlhoz. További információ: [a Azure Key Vault használata a biztonságos paraméterek értékének](../azure-resource-manager/templates/key-vault-parameter.md)átadására az üzembe helyezés során.

    Hozzon létre egy másolatot a közzétételi ágra feltöltött Parameters fájlról. Adja meg azoknak a paramétereknek az értékeit, amelyeket Key Vault szeretne kapni a következő formátum használatával:

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

    Ha ezt a módszert használja, a titkos kulcsot a rendszer automatikusan kihúzta a kulcstartóból.

    A paraméterek fájljának a közzétételi ágban is szerepelnie kell.

-  Az előző szakaszban ismertetett Azure Resource Manager központi telepítési feladat előtt adjon hozzá egy [Azure Key Vault feladatot](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) :

    1.  A **feladatok** lapon hozzon létre egy új feladatot. Keressen rá **Azure Key Vault** és vegye fel.

    1.  A Key Vault feladatban válassza ki azt az előfizetést, amelyben létrehozta a kulcstartót. Szükség esetén adja meg a hitelesítő adatokat, majd válassza ki a Key vaultot.

    ![Key Vault feladat hozzáadása](media/continuous-integration-deployment/continuous-integration-image8.png)

   #### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Engedélyek megadása az Azure-folyamatok ügynökének

   Ha a megfelelő engedélyek nincsenek beállítva, a Azure Key Vault feladat meghiúsulhat a hozzáférés-megtagadási hibával. Töltse le a kiadáshoz tartozó naplókat, és keresse meg azt a. ps1 fájlt, amely a parancsot tartalmazza az Azure-folyamatok ügynökének engedélyek megadásához. A parancsot közvetlenül is futtathatja. Vagy átmásolhatja a résztvevő AZONOSÍTÓját a fájlból, és manuálisan is hozzáadhatja a hozzáférési házirendet a Azure Portal. `Get` és `List` a minimálisan szükséges engedélyek.

### <a name="update-active-triggers"></a>Aktív eseményindítók frissítése

A központi telepítés meghiúsulhat, ha megpróbál frissíteni az aktív eseményindítókat. Az aktív eseményindítók frissítéséhez manuálisan kell leállítania azokat, majd újra kell indítania őket a telepítés után. Ezt egy Azure PowerShell feladat használatával teheti meg:

1.  A kiadás **feladatok** lapján adjon hozzá egy **Azure PowerShell** feladatot.

1.  Válassza a **Azure Resource Manager** lehetőséget a kapcsolattípus mezőben, majd válassza ki az előfizetését.

1.  A parancsfájl típusaként válassza a **beágyazott parancsfájl** lehetőséget, majd adja meg a kódot. A következő kód leállítja az eseményindítókat:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![Azure PowerShell feladat](media/continuous-integration-deployment/continuous-integration-image11.png)

A telepítés után az eseményindítók újraindításához hasonló lépések hajthatók végre (az `Start-AzDataFactoryV2Trigger` függvénnyel).

> [!IMPORTANT]
> A CI/CD-helyzetekben a különböző környezetekben lévő Integration Runtime (IR) típusnak azonosnak kell lennie. Ha például saját üzemeltetésű integrációs modult használ a fejlesztési környezetben, akkor ugyanazt az IR-t más környezetekben, például tesztelési és üzemi környezetben is önálló üzemeltetéssel kell eltárolni. Hasonlóképpen, ha több fázisban osztja meg az integrációs modulokat, az integrációs modulokat az összes környezetben, például a fejlesztés, a tesztelés és az éles környezetek szerint kell konfigurálni.

#### <a name="sample-pre--and-post-deployment-script"></a>Példa előtti és utáni parancsfájl

Az alábbi példa azt mutatja be, hogyan állíthatja le az eseményindítókat az üzembe helyezés előtt, majd később újraindíthatja őket. A parancsfájl emellett kódot is tartalmaz az eltávolított erőforrások törléséhez. A Azure PowerShell legújabb verziójának telepítéséhez tekintse [meg a Azure PowerShell telepítése Windows rendszerre a PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps)használatával című témakört.

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

    #Start active triggers - after cleanup efforts
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

Az alapértelmezett paraméterezés-sablon felülbírálásához a következő feltételek szerint hozzon létre egy ARM-template-Parameters-definition. JSON nevű fájlt az adattár gyökérkönyvtárában. Pontosan ezt a fájlnevet kell használnia. Data Factory beolvassa ezt a fájlt attól függően, hogy melyik ágban van jelenleg a Azure Data Factory portálon, nem csak az együttműködési ágban. A fájlt létrehozhatja vagy szerkesztheti egy privát ág alapján, ahol tesztelheti a módosításokat úgy, hogy kiválasztja az **ARM-sablon exportálása** lehetőséget a felhasználói felületen. Ezután egyesítheti a fájlt az együttműködési ágban. Ha nem található fájl, a rendszer az alapértelmezett sablont használja.

### <a name="syntax-of-a-custom-parameters-file"></a>Egyéni paraméterek fájljának szintaxisa

Az egyéni paraméterek fájljának létrehozásakor kövesse az alábbi irányelveket. A fájl az egyes entitások típusának egy szakaszát tartalmazza: trigger, folyamat, társított szolgáltatás, adatkészlet, integrációs futtatókörnyezet stb.
* Adja meg a tulajdonság elérési útját a megfelelő entitás típusa mezőben.
* A tulajdonságnév `*` értékre állítása azt jelzi, hogy az összes tulajdonságot meg szeretné parametrizálja (csak az első szintre, nem rekurzív módon). Kivételeket is megadhat ehhez a konfigurációhoz.
* Egy tulajdonság értékének karakterláncként való megadása azt jelzi, hogy meg kívánja parametrizálja a tulajdonságot. Használja a `<action>:<name>:<stype>`formátumot.
   *  `<action>` a következő karakterek egyike lehet:
      * `=` azt jelenti, hogy az aktuális értéket a paraméter alapértelmezett értékeként kell megőrizni.
      * `-` azt jelenti, hogy nem tartja meg a paraméter alapértelmezett értékét.
      * a `|` egy speciális eset a titkokhoz Azure Key Vault a kapcsolatok karakterláncai vagy kulcsai számára.
   * `<name>` a paraméter neve. Ha üres, akkor a tulajdonság nevét veszi fel. Ha az érték `-` karakterrel kezdődik, a név lerövidítve lesz. `AzureStorage1_properties_typeProperties_connectionString` például lerövidíti a `AzureStorage1_connectionString`.
   * `<stype>` a paraméter típusa. Ha `<stype>` üres, az alapértelmezett típus `string`. Támogatott értékek: `string`, `bool`, `number`, `object`és `securestring`.
* Egy tömb megadása a definíciós fájlban azt jelzi, hogy a sablonban szereplő egyező tulajdonság egy tömb. Data Factory a tömbben lévő összes objektumon megismétli a tömböt az Integration Runtime objektumában megadott definíció használatával. A második objektum, egy karakterlánc, a tulajdonság neve lesz, amely az egyes iterációk paraméterének neveként szerepel.
* Egy definíció nem lehet egy adott erőforrás-példányra jellemző. Bármely definíció az adott típusú összes erőforrásra vonatkozik.
* Alapértelmezés szerint az összes biztonságos karakterlánc, például a Key Vault titkos kódok, valamint a biztonságos karakterláncok, például a kapcsolati karakterláncok, kulcsok és tokenek paraméterei.
 
### <a name="sample-parameterization-template"></a>Minta paraméterezés-sablon

Az alábbi példa azt szemlélteti, hogy a paraméterezés-sablonok hogyan néznek ki:

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
Íme egy magyarázat arról, hogy az előző sablon hogyan épül fel, az erőforrástípus szerinti bontásban.

#### <a name="pipelines"></a>Adatcsatornák
    
* Az elérési út `activities/typeProperties/waitTimeInSeconds` egyik tulajdonsága paraméterrel van elfoglalva. A folyamat minden olyan tevékenysége, amely `waitTimeInSeconds` (például a `Wait` tevékenység) nevű programkód szintű tulajdonsággal rendelkezik, alapértelmezett névvel van paraméterként. A Resource Manager-sablonban azonban nem szerepel alapértelmezett érték. A Resource Manager üzembe helyezése során kötelezően megadandó adatok lesznek.
* Hasonlóképpen, egy `headers` nevű tulajdonság (például egy `Web` tevékenység) paraméterének típusa `object` (JObject). Alapértelmezett értékkel rendelkezik, amely megegyezik a forrás-előállítóval megegyező értékkel.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Az elérési út `typeProperties` összes tulajdonsága a megfelelő alapértelmezett értékekkel van ellátva. Például két tulajdonság van a `IntegrationRuntimes` típus tulajdonságainál: `computeProperties` és `ssisProperties`. Mindkét tulajdonság típusa a megfelelő alapértelmezett értékekkel és típusokkal (objektummal) jön létre.

#### <a name="triggers"></a>Eseményindítók

* A `typeProperties`alatt két tulajdonság paraméteres. Az első egy `maxConcurrency`, amely alapértelmezett értékkel rendelkezik, és`string`típusú. Az alapértelmezett paraméter neve `<entityName>_properties_typeProperties_maxConcurrency`.
* A `recurrence` tulajdonság is paraméteres. Ebben az esetben az adott szinten lévő összes tulajdonságot karakterláncként kell megadni, alapértelmezett értékekkel és paraméterek nevével. Kivételt képeznek a `interval` tulajdonság, amely `number`típusú paraméter. A paraméter neve a `<entityName>_properties_typeProperties_recurrence_triggerSuffix`utótaggal van ellátva. Hasonlóképpen, a `freq` tulajdonság egy karakterlánc, és karakterláncként van paraméterként. Az `freq` tulajdonság azonban alapértelmezett érték nélkül van paraméterben. A név rövidítve és utótaggal van elnevezve. Például: `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* A társított szolgáltatások egyediek. Mivel a társított szolgáltatások és adatkészletek sokféle típusúak, a típus-specifikus testreszabást is megadhatja. Ebben a példában a `AzureDataLakeStore`típusú társított szolgáltatások esetében egy adott sablon lesz alkalmazva. Minden más (`*`on keresztül) egy másik sablon lesz alkalmazva.
* A `connectionString` tulajdonság `securestring` értékként lesz paraméterként. Nem rendelkezik alapértelmezett értékkel. Egy rövidített paraméter neve lesz, amely a `connectionString`utótaggal van ellátva.
* A tulajdonság `secretAccessKey` történik `AzureKeyVaultSecret` (például egy Amazon S3 társított szolgáltatásban). Automatikusan Azure Key Vault titokként van konfigurálva, és a konfigurált kulcstartóból beolvasva. Saját maga is parametrizálja a kulcstartót.

#### <a name="datasets"></a>Adathalmazok

* Bár a típus-specifikus Testreszabás elérhető az adatkészletekhez, a konfigurációt explicit módon, \*szintű konfiguráció nélkül is megadhatja. Az előző példában a `typeProperties` alatti összes adatkészlet-tulajdonság paraméteres.

### <a name="default-parameterization-template"></a>Alapértelmezett paraméterezés-sablon

A következő az aktuális alapértelmezett paraméterezés sablon. Ha csak néhány paramétert kell felvennie, a sablon közvetlen szerkesztése jó ötlet lehet, mivel nem fogja elveszíteni a meglévő paraméterezés-struktúrát.

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

Az alábbi példa bemutatja, hogyan adhat hozzá egyetlen értéket az alapértelmezett paraméterezés-sablonhoz. Csak egy meglévő Azure Databricks interaktív Databricks szeretnénk hozzáadni egy társított szolgáltatáshoz a Parameters fájlhoz. Vegye figyelembe, hogy ez a fájl megegyezik az előző fájllal, kivéve `existingClusterId` hozzáadásával a `Microsoft.DataFactory/factories/linkedServices`tulajdonságok mezőjében.

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

Ha beállította a CI/CD-t az adatgyárai számára, akkor előfordulhat, hogy túllépi a Azure Resource Manager-sablon korlátait, mivel a gyár nagyobb mértékben növekszik. Például az egyik korlát az erőforrások maximális száma egy Resource Manager-sablonban. Ha a gyár teljes Resource Manager-sablonjának létrehozásakor nagy gyárakat szeretne kiszolgálni, Data Factory mostantól társított Resource Manager-sablonokat hoz létre. Ezzel a szolgáltatással a teljes gyári adattartalom több fájlra van bontva, így nem korlátozza a korlátokat.

Ha a git-t konfigurálta, a csatolt sablonok a adf_publish ág teljes Resource Manager-sablonjai mellett jönnek létre és menthetők egy új, linkedTemplates nevű mappában:

![Társított Resource Manager-sablonok mappája](media/continuous-integration-deployment/linked-resource-manager-templates.png)

A társított Resource Manager-sablonok általában egy fősablonból és egy, a főkiszolgálóhoz csatolt alárendelt sablonokból állnak. A fölérendelt sablon neve ArmTemplate_master. JSON, és a gyermek sablonok neve a következő mint ArmTemplate_0. JSON, ArmTemplate_1. JSON stb. 

Ha a teljes Resource Manager-sablon helyett csatolt sablonokat kíván használni, frissítse a CI/CD-feladatot úgy, hogy a ArmTemplateForFactory. JSON helyett a ArmTemplate_master. JSON fájlra mutasson (a teljes Resource Manager-sablon). A Resource Manager emellett azt is megköveteli, hogy a csatolt sablonokat egy Storage-fiókba töltse fel, hogy az Azure az üzembe helyezés során is hozzáférhessen. További információ: [a társított Resource Manager-sablonok üzembe helyezése a vsts](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Ne felejtse el hozzáadni az Data Factory szkripteket a CI/CD-folyamathoz az üzembe helyezési feladat előtt és után.

Ha nincs beállítva a git, a csatolt sablonokat az **ARM-sablonok** listájában, az **Exportálás ARM-sablon** használatával érheti el.

## <a name="hotfix-production-branch"></a>Gyorsjavítások üzemi ága

Ha a gyárat éles környezetben helyezi üzembe, és egy olyan hibát tapasztal, amelyet azonnal meg kell oldani, de nem tudja telepíteni az aktuális együttműködési ágat, előfordulhat, hogy telepítenie kell egy gyorsjavítást. Ez a megközelítés a Quick-Fix Engineering vagy a QFE.

1.  Az Azure DevOps nyissa meg az éles környezetben üzembe helyezett kiadást. A legutóbb telepített véglegesítő keresése.

2.  A véglegesítő üzenetből szerezze be az együttműködési ág véglegesítő AZONOSÍTÓját.

3.  Hozzon létre egy új gyorsjavítási ágat ebből a véglegesítve.

4.  Lépjen a Azure Data Factory UX-re, és váltson a gyorsjavítási ágra.

5.  A Azure Data Factory UX használatával javítsa ki a hibát. Tesztelje a módosításokat.

6.  A javítás ellenőrzése után válassza az **ARM-sablon exportálása** lehetőséget a gyorsjavítási erőforrás-kezelő sablon beszerzéséhez.

7.  Ezt a buildet manuálisan is bejelölheti az adf_publish ágra.

8.  Ha úgy állította be a kiadási folyamatot, hogy adf_publish-bejelentkezések alapján automatikusan aktiválódik, egy új kiadás automatikusan elindul. Ellenkező esetben manuálisan kell várólistát adni a kiadáshoz.

9.  Telepítse a gyorsjavítási kiadást a tesztelési és a termelési gyárakba. Ez a kiadás tartalmazza az előző éles adattartalmat, valamint az 5. lépésben elvégzett javítást.

10. Vegye fel a módosításokat a gyorsjavításból a fejlesztési ágra, hogy a későbbi kiadások ne tartalmazzanak ugyanazt a hibát.

## <a name="best-practices-for-cicd"></a>Ajánlott eljárások CI/CD-hez

Ha git-integrációt használ a saját adatgyárával, és rendelkezik egy CI/CD-folyamattal, amely a változásokat a fejlesztésből teszteli, majd éles környezetbe helyezi, javasoljuk az alábbi ajánlott eljárásokat:

-   **Git-integráció**. A git-integrációval csak a fejlesztői adatgyárat kell konfigurálnia. A tesztelés és a gyártás változásai a CI/CD-n keresztül települnek, és nincs szükség git-integrációra.

-   **Data Factory CI/CD-szkriptet**. A CI/CD-ben a Resource Manager üzembe helyezési lépése előtt végre kell hajtania bizonyos feladatokat, például az eseményindítók leállítását és újraindítását, valamint a tisztítást végző műveletet. Javasoljuk, hogy az üzembe helyezés előtt és után használjon PowerShell-parancsfájlokat. További információ: az [aktív eseményindítók frissítése](#update-active-triggers).

-   **Integrációs modulok és megosztás**. Az integrációs modulok nem változnak gyakran, és a CI/CD minden fázisában hasonlóak. Így Data Factory az integrációs modul azonos nevét és típusát várja a CI/CD minden szakaszában. Ha az integrációs modulokat minden fázisban meg szeretné osztani, érdemes lehet egy Ternáris-gyárat használni, amely csak a megosztott integrációs modulokat tartalmazza. Ezt a megosztott gyárat az összes környezetében használhatja társított Integration Runtime-típusként.

-   **Key Vault**. Ha Azure Key Vault alapján társított szolgáltatásokat használ, a különböző környezetekhez külön kulcstartók használatával kihasználhatja őket. Külön jogosultsági szinteket is beállíthat mindegyik kulcstartóhoz. Előfordulhat például, hogy nem szeretné, hogy a csapattagok rendelkezzenek a termelési titkokhoz szükséges engedélyekkel. Ha követi ezt a megközelítést, javasoljuk, hogy az összes fázisban ugyanazokat a titkos neveket tartsa meg. Ha megtartja ugyanazokat a neveket, nem kell megváltoztatnia a Resource Manager-sablonokat CI/CD-környezetekben, mert az egyetlen dolog, ami megváltoztatja a Key Vault nevét, amely a Resource Manager-sablon paramétereinek egyike.

## <a name="unsupported-features"></a>Nem támogatott funkciók

- A tervezés szerint a Data Factory nem teszi lehetővé az erőforrások elküldését vagy szelektív közzétételét. A közzététel magában foglalja az adatelőállítóban végrehajtott összes módosítást is.

    - A adatfeldolgozó-entitások egymástól függenek. Az eseményindítók például a folyamatoktól függenek, és az adatkészletek és az egyéb folyamatok függenek egymástól. Az erőforrások egy részhalmazának szelektív közzététele váratlan viselkedést és hibákat eredményezhet.
    - Ritka esetekben, amikor szelektív közzétételre van szüksége, érdemes lehet gyorsjavítást használni. További információ: [gyorsjavítások üzemi ága](#hotfix-production-branch).

-   Privát ágakból nem lehet közzétenni.

-   Jelenleg nem futtathat projekteket a bitbucket-on.
