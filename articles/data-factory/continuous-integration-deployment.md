---
title: Folyamatos integráció és kézbesítés az Azure Data Factoryban
description: Ismerje meg, hogyan használhatja a folyamatos integrációt és kézbesítést a Data Factory-folyamatok egyik környezetből (fejlesztés, tesztelés, termelés) való áthelyezéséhez.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 6aad01808ad155b745b614d8de6009386f0d2914
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687964"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Folyamatos integráció és kézbesítés az Azure Data Factoryban

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Áttekintés

A folyamatos integráció az a gyakorlat, hogy a kódbázison végrehajtott minden egyes módosítást automatikusan és a lehető leghamarabb tesztel.A folyamatos kézbesítés a folyamatos integráció során bekövetkező tesztelést követi, és leküldi a módosításokat egy átmeneti vagy éles rendszerbe.

Az Azure Data Factoryban a folyamatos integráció és kézbesítés (CI/CD) azt jelenti, hogy a Data Factory-folyamatok áthelyezése az egyik környezetből (fejlesztés, tesztelés, éles környezet) egy másikba. A Data Factory UX-integráció térhet el az Azure Resource Manager-sablonokkal ci/CD-vel.

A Data Factory ux-ben az **ARM template** legördülő menüből hozhat létre Erőforrás-kezelő sablont. Az **ARM-sablon exportálása**lehetőséget választva a portál létrehozza az adat-előállító Erőforrás-kezelő sablonját, valamint egy konfigurációs fájlt, amely tartalmazza az összes kapcsolati karakterláncot és egyéb paramétert. Ezután hozzon létre egy konfigurációs fájlt minden környezetben (fejlesztés, teszt, termelés). A fő Erőforrás-kezelő sablonfájl jatttsa az összes környezetben.

A funkció kilenc perces bemutatásához és bemutatóhoz tekintse meg ezt a videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD életciklus

Az alábbiakban egy minta áttekintést a CI/CD életciklusegy Azure-adat-előállító, amely konfigurálva az Azure Repos Git. A Git-tárház konfigurálásáról az [Azure Data Factory forrásvezérlése](source-control.md)című témakörben talál további információt.

1.  A fejlesztési adatok gyár jön létre, és konfigurálható az Azure Repos Git. Minden fejlesztőnek engedéllyel kell rendelkeznie a Data Factory-erőforrások, például a folyamatok és az adatkészletek szerzői engedélyezéséhez.

1.  Ahogy a fejlesztők módosításokat hajtanak végre a szolgáltatáságakban, hibakeresési folyamatuk a legutóbbi módosításokkal együtt fut. A folyamatfuttatások hibakereséséről további információt az [Iteratív fejlesztés és hibakeresés](iterative-development-debugging.md)az Azure Data Factory szolgáltatással című témakörben talál.

1.  Miután a fejlesztők elégedettek a módosításokkal, létrehoznak egy lekéréses kérelmet a szolgáltatáságból a fő vagy együttműködési ághoz, hogy a módosításokat a társak ellenőrizzék.

1.  A lekéréses kérelem jóváhagyása és a módosítások egyesítése után a főágban a módosítások közzétehetők a fejlesztési gyárban.

1.  Amikor a csapat készen áll a módosítások üzembe helyezésére a tesztgyárban, majd a termelési gyárban, a csapat exportálja az Erőforrás-kezelő sablont a főágból.

1.  Az exportált Erőforrás-kezelő sablon különböző paraméterfájlokkal van telepítve a tesztgyárba és a termelési gyárba.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Erőforrás-kezelő sablon létrehozása minden környezethez

1. Az **ARM-sablon** listában válassza az **ARM-sablon exportálása** lehetőséget az adatgyár Erőforrás-kezelő sablonjának exportálásához a fejlesztői környezetben.

   ![Erőforrás-kezelő sablon exportálása](media/continuous-integration-deployment/continuous-integration-image1.png)

1. A teszt- és termelési adatgyárakban válassza **az ARM-sablon importálása lehetőséget.** Ez a művelet az Azure Portalra viszi, ahol importálhatja az exportált sablont. Válassza a Saját sablon létrehozása lehetőséget **a szerkesztőben** az Erőforráskezelő sablonszerkesztő megnyitásához.

   ![Saját sablon létrehozása](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Válassza **a Fájl betöltése**lehetőséget, majd válassza ki a létrehozott Erőforrás-kezelő sablont. Ez az **arm_template 1.**

   ![Sablon szerkesztése](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. A beállítások szakaszban adja meg a konfigurációs értékeket, például a csatolt szolgáltatás hitelesítő adatait. Ha elkészült, válassza a **Vásárlás** lehetőséget az Erőforrás-kezelő sablon telepítéséhez.

   ![Beállítások szakasz](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Kapcsolati sztringek

A kapcsolati karakterláncok konfigurálásáról az összekötő cikkében olvashat. Az Azure SQL Database esetében például lásd: [Adatok másolása az Azure SQL Database-be vagy onnan az Azure Data Factory használatával.](connector-azure-sql-database.md) A kapcsolati karakterlánc ellenőrzéséhez nyissa meg az erőforrás kódnézetét a Data Factory UX-ben. Kódnézetben a kapcsolati karakterlánc jelszó- vagy fiókkulcs-része törlődik. A kódnézet megnyitásához jelölje ki az itt kiemelt ikont:

![Kódnézet megnyitása a kapcsolati karakterlánc megtekintéséhez](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>A folyamatos integráció automatizálása az Azure Pipelines-kiadások használatával

Az alábbiakban egy azure-folyamatkiadás beállításához, amely automatizálja az adatgyár üzembe helyezését több környezetben.

![Az Azure-folyamatokkal való folyamatos integráció diagramja](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Követelmények

-   A Visual Studio Team Foundation Server kiszolgálóhoz vagy az Azure-repókhoz kapcsolódó Azure-előfizetés, amely az [Azure Resource Manager szolgáltatásvégpontot](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)használja.

-   Az Azure Repos Git-integrációval konfigurált adatgyár.

-   Egy [Azure-kulcstartó,](https://azure.microsoft.com/services/key-vault/) amely tartalmazza az egyes környezetek titkos kulcsait.

### <a name="set-up-an-azure-pipelines-release"></a>Azure-folyamatok kiadásának beállítása

1.  Az [Azure DevOps](https://dev.azure.com/)alkalmazásban nyissa meg az adat-előállítóval konfigurált projektet.

1.  A lap bal oldalán válassza a **Folyamatok**lehetőséget, majd a **Felengedések**lehetőséget.

    ![Folyamatok, kiadások kiválasztása](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Válassza az **Új folyamat lehetőséget,** vagy ha már rendelkezik meglévő folyamatokkal, válassza az **Új,** majd **az Új kiadási folyamat lehetőséget.**

1.  Válassza az **Üres feladat** sablont.

    ![Üres feladat kiválasztása](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  A **Színpad neve** mezőbe írja be a környezet nevét.

1.  Válassza **a Műtermék hozzáadása**lehetőséget, majd válassza ki az adat-előállítóval konfigurált tárházat. Válassza ki az **alapértelmezett ág** **adf_publish.** Az **Alapértelmezett verzió**mezőben válassza a **Legújabb lehetőséget az alapértelmezett ágból**.

    ![Összetevő hozzáadása](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager telepítési feladat hozzáadása:

    a.  A színpadnézetben válassza a **Színpadi feladatok megtekintése**lehetőséget.

    ![Színpadnézet](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Hozzon létre egy új feladatot. Keressen az Azure Resource Group Deployment ( Azure Resource Group Deployment ) **elemre,** majd válassza **a Hozzáadás**lehetőséget.

    c.  A Központi telepítés feladatban válassza ki az előfizetést, az erőforráscsoportot és a céladat-előállító helyét. Szükség esetén adja meg a hitelesítő adatokat.

    d.  A **Művelet** listában válassza az **Erőforráscsoport létrehozása vagy frissítése**lehetőséget.

    e.  Válassza a három pont gombot (**...**) a **Sablon** mező mellett. Keresse meg az Azure Resource Manager sablont, amelyet az **ARM-sablon importálása parancathasznál** hozott létre a [Cikk minden környezetéhez az Erőforrás-kezelő sablon létrehozása](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) című szakaszban. Keresse meg ezt <FactoryName> a fájlt a adf_publish ág mappájában.

    f.  Válassza ki **a ...** a **Sablon paraméterek** mező mellett a paraméterfájl kiválasztásához. A választott fájl attól függ, hogy létrehozott-e másolatot, vagy az alapértelmezett ARMTemplateParametersForFactory.json fájlt használja.

    g.  Válassza ki **a ...** a **Sablon paramétereinek felülbírálása** mező mellett, és adja meg a céladat-előállító adatait. Az Azure Key Vaultból származó hitelesítő adatokért adja meg a titkos kulcs nevét a dupla idézőjelek között. Ha például a titkos adatik, akkor írja be a **"$(cred1)"** értéket ehhez az értékhez.

    h. Válassza a **Növekményes** lehetőséget a **központi telepítési módhoz.**

    > [!WARNING]
    > Ha a Telepítés módhoz a **Befejezés** lehetőséget **választja,** előfordulhat, hogy a meglévő erőforrások törlődnek, beleértve a célerőforrás-csoport minden olyan erőforrását, amely nincs definiálva az Erőforrás-kezelő sablonban.

    ![Adatgyári termékkönyvelés](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Mentse a kiadási folyamatot.

1. A kiadás aktiválásához válassza a **Kiadás létrehozása lehetőséget.**

   ![Válassza a Kiadás létrehozása lehetőséget.](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> CI/CD-forgatókönyvekben az integrációs futásidejű (IR) típusnak különböző környezetekben azonosnak kell lennie. Ha például a fejlesztői környezetben saját üzemeltetett infravörös rendszerrel rendelkezik, akkor ugyanaznak az infravörös rendszernek más környezetekben, például tesztelési és éles környezetekben is saját üzemeltetett típusúnak kell lennie. Hasonlóképpen, ha több szakaszban osztja meg az integrációs futásidőket, az integrációs futásidőket minden környezetben, például a fejlesztésben, a tesztelésben és az éles környezetben összekapcsolt önálló üzemeltetésként kell konfigurálnia.

### <a name="get-secrets-from-azure-key-vault"></a>Titkok beszerezése az Azure Key Vaultból

Ha egy Azure Resource Manager-sablonban titkos kulcsokat szeretne átadni, azt javasoljuk, hogy az Azure Key Vault ot használja az Azure Pipelines kiadásával.

A titkok kezelésének két módja van:

1.  Adja hozzá a titkos kulcsokat a paraméterfájlhoz. További információ: [Az Azure Key Vault használata a biztonságos paraméter értékének a telepítés során történő átadásához](../azure-resource-manager/templates/key-vault-parameter.md)című témakörben talál.

    Hozzon létre egy másolatot a paraméterek fájl, amely feltöltötte a közzétételi ágba. Állítsa be a Key Vaultból beszerezni kívánt paraméterek értékeit a következő formátummal:

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

    Ha ezt a módszert használja, a titkos kulcs automatikusan lekéri a key vault.

    A paraméterfájlnak a közzétételi ágban is meg kell lennie.

1. Adjon hozzá egy [Azure Key Vault-feladatot](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) az előző szakaszban ismertetett Azure Resource Manager-telepítési feladat előtt:

    1.  A **Feladatok** lapon hozzon létre egy új feladatot. Keresse meg az **Azure Key Vault ot,** és adja hozzá.

    1.  A Key Vault feladatválassza ki az előfizetést, amelyben létrehozta a key vault. Adja meg a hitelesítő adatokat, ha szükséges, majd válassza ki a key vault.

    ![Key Vault-feladat hozzáadása](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Engedélyek megadása az Azure Pipelines ügynöknek

Előfordulhat, hogy az Azure Key Vault-feladat sikertelen lehet egy Hozzáférés megtagadva hibával, ha a megfelelő engedélyek nincsenek beállítva. Töltse le a kiadások naplóit, és keresse meg a parancsot tartalmazó .ps1 fájlt, amely engedélyeket ad az Azure Pipelines ügynöknek. A parancs közvetlenül futtatható. Vagy másolhatja az egyszerű azonosítót a fájlból, és manuálisan hozzáadhatja a hozzáférési szabályzatot az Azure Portalon. `Get`és `List` a minimális anammindát a szükséges minimális engedélyeket.

### <a name="update-active-triggers"></a>Aktív eseményindítók frissítése

Az üzembe helyezés sikertelen lehet, ha megpróbálja frissíteni az aktív eseményindítókat. Az aktív eseményindítók frissítéséhez manuálisan le kell állítania őket, majd újra kell indítania őket a telepítés után. Ezt egy Azure PowerShell-feladat használatával teheti meg:

1.  A **kiadás Feladatok** lapján adjon hozzá egy **Azure PowerShell-feladatot.** Válassza a feladat 4-es verzióját.*. 

1.  Válassza ki azt az előfizetést, amelyben a gyár található.

1.  Parancsfájlelérési **út kiválasztása** parancsfájltípusként. Ehhez a PowerShell-parancsfájlt a tárházban kell mentenie. A következő PowerShell-parancsfájl használható az eseményindítók leállításához:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Hasonló lépéseket végezhet el `Start-AzDataFactoryV2Trigger` (a funkcióval) az eseményindítók üzembe helyezés után történő újraindításához.

### <a name="sample-pre--and-post-deployment-script"></a>A telepítés előtti és utáni parancsfájl minta

A következő mintaparancsfájl használható az eseményindítók leállításához az üzembe helyezés előtt, majd ezután újraindítva. A parancsfájl tartalmazza az eltávolított erőforrások törlésére vonatkozó kódot is. Mentse a parancsfájlt egy Azure DevOps git-tárházba, és hivatkozzon rá egy Azure PowerShell-feladaton keresztül a 4-es verzió használatával.*.

A központi telepítés előtti parancsfájl futtatásakor a **parancsfájl argumentumok** mezőben meg kell adnia a következő paraméterek egy változatát.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


A telepítés utáni parancsfájl futtatásakor a **parancsfájl argumentumok** mezőben meg kell adnia a következő paraméterek egy változatát.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure PowerShell-feladat](media/continuous-integration-deployment/continuous-integration-image11.png)

Itt van a szkript, amely használható az elő- és utótelepítés. A törölt erőforrásokat és erőforrás-hivatkozásokat figyelembe teszi.

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
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
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Egyéni paraméterek használata Resource Manager-sablonnal

Ha GIT módban van, felülbírálhatja az Erőforrás-kezelő sablon alapértelmezett tulajdonságait a sablonban paraméterezett és a nehezen kódolt tulajdonságok beállításához. A következő esetekben érdemes felülírni az alapértelmezett paraméterezési sablont:

* Automatikus CI/CD-t használ, és néhány tulajdonságot módosítani szeretne az Erőforrás-kezelő központi telepítése során, de a tulajdonságok alapértelmezés szerint nincsenek paraméterezve.
* A gyár olyan nagy, hogy az alapértelmezett Resource Manager-sablon érvénytelen, mert több, mint a maximálisan engedélyezett paraméterek (256).

Ilyen körülmények között az alapértelmezett paraméterezési sablon felülbírálásához hozzon létre egy **arm-template-parameters-definition.json** nevű fájlt az adatgyári git-integráció gyökérmappájaként megadott mappában. Pontosan ezt a fájlnevet kell használnia. A Data Factory ezt a fájlt az Azure Data Factory portálon jelenleg lévő ágból olvassa be, nem csak az együttműködési ágból. A fájlt létrehozhatja vagy szerkesztheti egy privát ágból, ahol a módosításokat a felhasználói felület **ARM-sablon exportálása** lehetőség kiválasztásával tesztelheti. Ezután egyesítheti a fájlt az együttműködési ágba. Ha nem található fájl, a rendszer az alapértelmezett sablont használja.

> [!NOTE]
> Az egyéni paraméterezési sablon nem módosítja az ARM sablon paraméterkori 256-os korlátját. Lehetővé teszi a paraméterezett tulajdonságok számának kiválasztását és csökkentését.

### <a name="syntax-of-a-custom-parameters-file"></a>Egyéni paraméterfájl szintaxisa

Az alábbiakban az egyéni paraméterfájl létrehozásakor követendő néhány irányelvet kell követnie. A fájl minden entitástípushoz tartalmaz egy szakaszt: eseményindító, folyamat, csatolt szolgáltatás, adatkészlet, integrációs futásidő és így tovább.
* Adja meg a tulajdonság elérési útját a megfelelő entitástípus alatt.
* Ha egy tulajdonságnevet állít be, `*` azt jelzi, hogy az alatta lévő összes tulajdonságot paraméterezni szeretné (csak az első szintig, nem rekurzívan). A konfiguráció alól kivételeket is megadhat.
* A tulajdonság értékének karakterláncként való beállítása azt jelzi, hogy paraméterezni szeretné a tulajdonságot. A formátum `<action>:<name>:<stype>`használata .
   *  `<action>` lehet az alábbi karakterek egyike:
      * `=` azt jelenti, hogy az aktuális értéket megtartja a paraméter alapértelmezett értékeként.
      * `-` azt jelenti, hogy ne tartsa meg a paraméter alapértelmezett értékét.
      * `|` egy különleges eset az Azure Key Vault ból származó kapcsolatok és kulcsok titkos titkait.
   * `<name>` a paraméter neve. Ha üres, akkor az ingatlan nevét veszi fel. Ha az érték `-` karakterrel kezdődik, a név lerövidül. Például `AzureStorage1_properties_typeProperties_connectionString` a . `AzureStorage1_connectionString`
   * `<stype>` a paraméter típusa. Ha `<stype>` üres, az alapértelmezett `string`típus a . `string`Támogatott értékek: `bool` `number`, `object`, `securestring`, , és .
* Ha egy tömböt ad meg a definíciós fájlban, az azt jelzi, hogy a sablonban lévő megfelelő tulajdonság tömb. A Data Factory a tömb összes objektumát végighalad a tömb integrációs futásidejű objektumában megadott definíció használatával. A második objektum, a karakterlánc lesz a tulajdonság neve, amely az egyes ismétlések paraméterének neveként használatos.
* A definíció nem lehet egy erőforrás-példányra jellemző. Minden meghatározás az adott típusú összes erőforrásra vonatkozik.
* Alapértelmezés szerint minden biztonságos karakterláncok, például a Key Vault titkos kulcsait, és a biztonságos karakterláncok, például a kapcsolati karakterláncok, kulcsok és jogkivonatok, paraméterezett.
 
### <a name="sample-parameterization-template"></a>Minta paraméterezési sablonja

Íme egy példa arra, hogyan nézhet ki egy paraméterezési sablon:

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
Íme egy magyarázat az előző sablon felépítésére, erőforrástípus szerinti bontásban.

#### <a name="pipelines"></a>Folyamatok
    
* Az elérési út `activities/typeProperties/waitTimeInSeconds` bármely tulajdonsága paraméterezett. A folyamat minden olyan tevékenysége, amelynek kódszintű tulajdonsága (például `waitTimeInSeconds` a `Wait` tevékenység) kódszintű tulajdonsággal van elnevezve, számként van paraméterezve, alapértelmezett névvel. De nem lesz alapértelmezett értéke az Erőforrás-kezelő sablonban. Kötelező bemenet lesz az Erőforrás-kezelő központi telepítése során.
* Hasonlóképpen a (például egy `headers` `Web` tevékenységben) megnevezett tulajdonság `object` ot a típus (JObject) paraméterezi. Alapértelmezett értéke megegyezik a forrásgyár értékével.

#### <a name="integrationruntimes"></a>Integrációs futtatási idők

* Az elérési út `typeProperties` alatti összes tulajdonság a megfelelő alapértelmezett értékekkel van paraméterezve. A típustulajdonságok alatt `IntegrationRuntimes` például két `computeProperties` `ssisProperties`tulajdonság található: és a . Mindkét tulajdonságtípus a saját alapértelmezett értékeivel és típusaival (Objektum) jön létre.

#### <a name="triggers"></a>Eseményindítók

* A `typeProperties`területen két tulajdonság paraméterezve. Az első `maxConcurrency`a , amely alapértelmezett értékkel rendelkezik`string`, és típusa van . Ez birtokol a `<entityName>_properties_typeProperties_maxConcurrency`hiba paraméter név .
* A `recurrence` tulajdonság is paraméterezett. Alatta az ezen a szinten található összes tulajdonság karakterláncként van paraméterezve, alapértelmezett értékekkel és paraméternevekkel. Kivételt képez `interval` a tulajdonság, amely `number`típusként van paraméterezve. A paraméter neve a `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Hasonlóképpen a `freq` tulajdonság egy karakterlánc, és karakterláncként van paraméterezve. A `freq` tulajdonság paraméterezése azonban alapértelmezett érték nélkül történik. A név rövidítésre és utótagra kerül. Például: `<entityName>_freq`.

#### <a name="linkedservices"></a>Csatolt szolgáltatások

* A csatolt szolgáltatások egyediek. Mivel a csatolt szolgáltatások és adatkészletek sokféle típussal rendelkeznek, típusspecifikus testreszabást is biztosíthat. Ebben a példában az összes `AzureDataLakeStore`kapcsolódó szolgáltatás típusára egy adott sablon kerül alkalmazásra. Az összes többi `*`(via) esetében egy másik sablon kerül alkalmazásra.
* A `connectionString` tulajdonság paraméterezése `securestring` értékként lesz megadva. Nem lesz alapértelmezett értéke. Rövidített paraméterneve lesz, amely a . `connectionString`
* A `secretAccessKey` tulajdonság történetesen egy `AzureKeyVaultSecret` (például egy Amazon S3 kapcsolt szolgáltatás). Automatikusan paraméterezi az Azure Key Vault titkos kulcsaként, és a konfigurált kulcstartóból lekéri. Azt is paraméterezheti a key vault is.

#### <a name="datasets"></a>Adathalmazok

* Bár az adatkészletek típusspecifikus testreszabása elérhető, a konfigurációt \*explicit szintű konfiguráció nélkül is biztosíthatja. Az előző példában az összes `typeProperties` adatkészlet-tulajdonságok alatt paraméterezett.

### <a name="default-parameterization-template"></a>Alapértelmezett paraméterezési sablon

A következőkben az aktuális alapértelmezett paraméterezési sablon látható. Ha csak néhány paramétert kell hozzáadnia, a sablon közvetlen szerkesztése jó ötlet lehet, mert nem veszíti el a meglévő paraméterezési struktúrát.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
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
                    "poolName": "=",
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

A következő példa bemutatja, hogyan adhat hozzá egyetlen értéket az alapértelmezett paraméterezési sablonhoz. Csak egy meglévő Azure Databricks interaktív fürtazonosítót szeretnénk hozzáadni egy Databricks csatolt szolgáltatáshoz a paraméterfájlhoz. Ne feledje, hogy ez a fájl megegyezik `existingClusterId` az előző `Microsoft.DataFactory/factories/linkedServices`fájllal, kivéve a tulajdonságmező ben való hozzáadását.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
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
            "poolName": "=",
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

## <a name="linked-resource-manager-templates"></a>Csatolt erőforrás-kezelő sablonok

Ha ci/CD-t állított be az adatgyárakhoz, előfordulhat, hogy túllépi az Azure Resource Manager sablonkorlátait, ahogy a gyár nagyobblesz. Az egyik korlát például az Erőforrás-kezelő sablonban lévő erőforrások maximális száma. A nagy gyárak befogadásához a gyár teljes Resource Manager-sablonjának létrehozása közben a Data Factory most összekapcsolt Erőforrás-kezelő sablonokat hoz létre. Ezzel a funkcióval a teljes gyári hasznos adat több fájlra van bontva, így nem korlátozzák a korlátok.

Ha konfigurálta a Git-et, a csatolt sablonok a adf_publish ágteljes Erőforrás-kezelő sablonjai mellett jönnek létre és kerülnek mentésre egy új mappában, amelynek neve linkedTemplates:

![Csatolt erőforrás-kezelő sablonok mappája](media/continuous-integration-deployment/linked-resource-manager-templates.png)

A csatolt Erőforrás-kezelő sablonok általában egy fősablonból és a mesteroldalhoz kapcsolódó gyermeksablonokból állnak. A szülősablon neve ArmTemplate_master.json, és a gyermeksablonok neve a ArmTemplate_0.json, ArmTemplate_1.json és így tovább mintával. 

Ha a teljes Erőforrás-kezelő sablon helyett csatolt sablonokat szeretne használni, frissítse a CI/CD-feladatot úgy, hogy az ArmTemplateForFactory.json (a teljes Erőforrás-kezelő sablon) helyett ArmTemplate_master.jsonra mutasson. Az Erőforrás-kezelő azt is megköveteli, hogy a csatolt sablonokat egy tárfiókba töltse fel, hogy az Azure a központi telepítés során elérhesse őket. További információ: [Csatolt Erőforrás-kezelő sablonok telepítése VSTS-sel.](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)

Ne felejtse el hozzáadni a Data Factory parancsfájlokat a CI/CD-folyamathoz a telepítési feladat előtt és után.

Ha nincs konfigurálva a Git, az **ARM-sablon exportálása** segítségével elérheti a csatolt sablonokat az **ARM sablon** listában.

## <a name="hotfix-production-branch"></a>Gyorsjavítás termelési ága

Ha üzembe helyez egy gyárat éles környezetben, és rájön, hogy van egy hiba, amelyet azonnal ki kell javítani, de nem tudja telepíteni az aktuális együttműködési ágat, előfordulhat, hogy telepítenie kell egy gyorsjavítást. Ez a megközelítés az úgynevezett gyorsjavítás-tervezés vagy QFE.

1.    Az Azure DevOps-ban nyissa meg az éles környezetben üzembe helyezett kiadást. Keresse meg az utoljára telepített véglegesítést.

2.    A véglegesítési üzenetből lekell szereznie az együttműködési ág véglegesítési azonosítóját.

3.    Hozzon létre egy új gyorsjavítás-ágat a véglegesítésből.

4.    Nyissa meg az Azure Data Factory felhasználói felületét, és váltson a gyorsjavítási ágra.

5.    Az Azure Data Factory UX használatával javítsa ki a hibát. Tesztelje a módosításokat.

6.    A javítás ellenőrzése után válassza az **ARM-sablon exportálása** lehetőséget a gyorsjavítás Erőforrás-kezelő sablonjának leigazolásához.

7.    Manuálisan ellenőrizze ezt a buildet a adf_publish ágba.

8.    Ha úgy állította be a kiadási folyamatot, hogy a bejelentkezések alapján automatikusan aktiválódjon adf_publish, automatikusan elindul egy új kiadás. Ellenkező esetben manuálisan sorakasztja fel a kiadást.

9.    Telepítse a gyorsjavítás kiadását a teszt- és termelési gyárakba. Ez a kiadás tartalmazza az előző termelési hasznos tartalmat, valamint az 5.

10.    Adja hozzá a gyorsjavítás módosításait a fejlesztői ághoz, hogy a későbbi kiadások ne tartalmazzák ugyanazt a hibát.

## <a name="best-practices-for-cicd"></a>Gyakorlati tanácsok a CI/CD-hez

Ha Git-integrációt használ az adatelőállítóval, és rendelkezik egy CI/CD-folyamattal, amely a módosításokat a fejlesztésről a tesztre, majd a termelésre helyezi át, az alábbi ajánlott eljárásokat javasoljuk:

-   **Git integráció**. Csak a fejlesztési adatgyárat kell konfigurálnia a Git-integrációval. A tesztelés és az éles környezet módosításai CI/CD-n keresztül kerülnek üzembe helyezésre, és nincs szükség Git-integrációra.

-   **Data Factory CI/CD parancsfájl**. Mielőtt az Erőforrás-kezelő üzembe helyezési lépés a CI/CD-ben, el kell végeznie bizonyos feladatokat, például az eseményindítók leállítását és újraindítását, valamint a karbantartást. Javasoljuk, hogy a PowerShell-parancsfájlok használata a központi telepítés előtt és után. További információ: [Aktív eseményindítók frissítése](#update-active-triggers).

-   **Integrációs futásidők és megosztás**. Az integrációs futásidők nem változnak gyakran, és hasonlóak a CI/CD minden szakaszában. Így a Data Factory elvárja, hogy ugyanazt a nevet és típusú integrációs futásidejű minden szakaszában a CI / CD. Ha szeretné megosztani az integrációs futtatási szakaszok minden szakaszában, fontolja meg egy háromsági gyár csak a megosztott integrációs futtatások. Ezt a megosztott gyárat az összes környezetben használhatja csatolt integrációs futásidejű típusként.

-   **Key Vault**. Ha az Azure Key Vault alapú összekapcsolt szolgáltatásokat használ, további kihasználása ként külön kulcstartók at különböző környezetekben. Az egyes kulcstartókhoz külön jogosultsági szinteket is konfigurálhat. Előfordulhat például, hogy nem szeretné, hogy a csapattagok éles titkokhoz engedélyeket adjanak. Ha ezt a módszert követi, azt javasoljuk, hogy tartsa meg ugyanazokat a titkos neveket az összes szakaszban. Ha megtartja ugyanazokat a neveket, nem kell módosítania az Erőforrás-kezelő sablonjait ci/CD-környezetekben, mert az egyetlen változás a kulcstartó neve, amely az Erőforrás-kezelő sablon paramétereinek egyike.

## <a name="unsupported-features"></a>Nem támogatott szolgáltatások

- A Data Factory nem teszi lehetővé a véglegesítések cseresznyeszedését vagy az erőforrások szelektív közzétételét. A közzétételi adatok tartalmazzák az adat-előállítóban végrehajtott összes módosítást.

    - Az adatfeldolgozó entitások egymástól függenek. Az eseményindítók például a folyamatoktól függenek, a folyamatok pedig az adatkészletektől és más folyamatoktól függenek. Az erőforrások egy részhalmazának szelektív közzététele váratlan viselkedésekhez és hibákhoz vezethet.
    - Ritkán, amikor szelektív közzétételre van szüksége, fontolja meg egy gyorsjavítás használatát. További információ: [Hotfix termelési ág](#hotfix-production-branch).

-   Privát fiókokból nem lehet közzétenni.

-   Jelenleg nem lehet projekteket üzemeltetni a Bitbucket-en.
