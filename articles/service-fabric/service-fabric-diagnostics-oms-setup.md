---
title: "Az Azure Service Fabric - figyelés az OMS szolgáltatáshoz beállítása |} Microsoft Docs"
description: "Megtudhatja, hogyan állíthatja be az OMS megjelenítése és elemzése az Azure Service Fabric-fürtök megfigyelésére szolgáló események."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: dekapur
ms.openlocfilehash: 53b06c5a1395f34c96d4011366835a920d5c670b
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>A fürt OMS Naplóelemzési beállítása

Beállíthat egy OMS-munkaterület Azure Resource Manageren keresztül, PowerShell-lel, vagy az Azure piactéren keresztül. Ha meg vannak egy frissített Resource Manager-sablon a telepítés későbbi használatra használja ugyanazt a sablont az OMS-környezetet. Egyszerűbb, ha már van egy telepített, engedélyezett diagnosztika a fürt üzembe helyezése a piactéren keresztül. Abban az esetben, hogy nincs előfizetés szintű hozzáféréssel a fiók, amelyen OMS telepíti használhatja a PowerShell vagy a Resource Manager-sablon használatával telepítheti.

> [!NOTE]
> Diagnosztika engedélyezve van a fürt fürt megtekintéséhez szükséges / platform szintjén az eseményeket az OMS sikeresen beállíthatja a fürt figyelése.

## <a name="deploying-oms-using-azure-marketplace"></a>Használja az Azure piactér OMS telepítése

Ha inkább az OMS-munkaterület hozzáadása a fürt telepítése után, látogasson el az Azure piactér (a portálon), és keressen *"Service Fabric Analytics."*

1. Kattintson a **új** a bal oldali navigációs menü. 

2. Keresse meg *háló Analytics szolgáltatás*. Kattintson az erőforrás-be.

3. Kattintson a **létrehozása**

    ![A piactéren OMS ú elemzés](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. A Service Fabric Analytics létrehozása ablakában kattintson **munkaterület kiválasztása** a a *OMS-munkaterület* mezőben, majd **hozzon létre egy új munkaterületet**. Töltse ki a szükséges bejegyzések – a egyetlen követelménye, hogy az előfizetés, a Service Fabric-fürt és az OMS-munkaterület azonosnak kell lennie. Ha a bejegyzések érvényesítése az OMS-munkaterület indul telepítése. Ez csak kell eltarthat néhány percig.

5. Ha befejezte, kattintson a **létrehozása** újra a Service Fabric Analytics létrehozása ablak alján. Győződjön meg arról, hogy alatt megjelenik az új munkaterületet *OMS-munkaterület*. Ez biztosítja a megoldás az imént létrehozott munkaterületet.

Használatakor a Windows, folytassa a következő lépéseket a tárolási fiók OMS bekötése a fürthöz kapcsolódó események tároló. Ez a felület megfelelően a Linux-fürtök engedélyezése folyamatban van. Eközben a folytatáshoz adja hozzá az OMS-ügynököt a fürthöz.  

1. A munkaterület továbbra is meg kell kapcsolódnia kell a fürtből származó diagnosztikai adatokat. Nyissa meg a Service Fabric elemzési megoldások a létrehozott erőforráscsoportot. Megjelenik egy *ServiceFabric (\<nameOfOMSWorkspace\>)*. Kattintson a – áttekintés oldalra, ahol megoldás beállításokat, a beállítások munkaterületet, és keresse meg az OMS-portálon keresse meg a megoldást.

2. A bal oldali navigációs menüben kattintson a **tárfiókok naplók**a *munkaterület adatforrások*.

3. A a *a tárolási fiók naplófájljai* kattintson **hozzáadása** hozzáadása a fürt naplók a munkaterület tetején.

4. Kattintson a be **tárfiók** vegye fel a fürt létrehozása a megfelelő fiókot. Ha az alapértelmezett nevet, a tárfiók neve *sfdg\<resourceGroupName\>*. Azt is ellenőrizheti az Azure Resource Manager-sablon központi telepítéséhez a fürt által használt érték ellenőrzésével a `applicationDiagnosticsStorageAccountName`. Is lehet, hogy görgessen lefelé, és kattintson a **betöltése több** Ha a név nem jelenik meg. Kattintson a jobb tárfióknév fel rá kattintva jelölje ki.

5. A következő együtt kell megadni a *adattípus*, amely meg **Service Fabric események**.

6. A *forrás* automatikusan meg *WADServiceFabric\*címke*.

7. Kattintson a **OK** a munkaterület kapcsolódni a fürt naplókat.

    ![Adja hozzá a tárolási fiók naplófájljai az OMS-be](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A fiók kell megjelennek-e részeként a *a tárolási fiók naplófájljai* a munkaterület adatforrások.

A most hozzáadott a Service Fabric elemzési megoldás az OMS Naplóelemzési munkaterület, most már megfelelően csatlakozik-e a fürt platform és Alkalmazásnapló-tábla. Azonos módon adhat hozzá további források a munkaterületre.


## <a name="deploying-oms-using-a-resource-manager-template"></a>A Resource Manager sablonnal OMS telepítése

A Resource Manager-sablon a fürt telepítésekor a sablon hozzon létre egy új OMS-munkaterület felvétele a Service Fabric megoldás, és konfigurálja úgy, hogy a megfelelő tárolási táblákból származó adatokat olvasni.

[Itt](https://azure.microsoft.com/resources/templates/service-fabric-oms/) egy minta sablon használata, és módosítsa a követelményeknek. További sablonok, amelyek különböző beállításokról az OMS-munkaterület beállítása helyen találhatók [Service Fabric-és OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

A fő végrehajtott változtatásokat a következők:

1. Adja hozzá `omsWorkspaceName` és `omsRegion` a paramétereket. Ez azt jelenti, hogy a következő kódrészletet a definiált paraméterek hozzáadása a *template.json* fájlt. Nyugodtan módosíthatja az alapértelmezett értékeket, ahogyan szeretné. Is fel kell két új paramétereket tartalmaz a *parameters.json* megadhatók azok az erőforrások telepítése értékeit:
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    A `omsRegion` értéknek meg kell felelnie egy meghatározott értéket. Ki kell választania azt, amelyik a központi telepítés, a fürt legközelebb van.

2. Ha küldi minden alkalmazásnaplók az OMS Szolgáltatáshoz, ellenőrizze, hogy a `applicationDiagnosticsStorageAccountType` és `applicationDiagnosticsStorageAccountName` szerepel a sablonban szereplő paraméterekkel. Ha nem, adja hozzá a változók szakaszban így, és igény szerint szerkessze az értékekre. Is használható őket paramétert, ha azt szeretné, a fenti formátuma a következő.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Vegye fel a Service Fabric OMS-megoldás a sablon változók:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Adja hozzá az alábbiakat az erőforrások szakasz után, ahol a Service Fabric-fürt erőforrás van deklarálva.

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > Ha hozzáadta a `applicationDiagnosticsStorageAccountName` változóként, ügyeljen arra, hogy módosítsa úgy, hogy minden egyes hivatkozás `variables('applicationDiagnosticsStorageAccountName')` helyett `parameters('applicationDiagnosticsStorageAccountName')`.

5. A sablon telepítése a Resource Manager-frissítés a fürthöz. Ebben az esetben használja a `New-AzureRmResourceGroupDeployment` API-nak a AzureRM PowerShell-modult. A példában a parancs a következő lesz:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Az Azure Resource Manager lesz képes észlelni, hogy ez a meglévő erőforrás frissítése. A módosítások a befolyásoló tényezők a meglévő központi telepítési sablont és az új sablon között csak fogja feldolgozni.

## <a name="deploying-oms-using-azure-powershell"></a>Az Azure PowerShell OMS telepítése

Az OMS szolgáltatáshoz erőforrás Powershellen keresztül is telepíthet. Ehhez használja a `New-AzureRmOperationalInsightsWorkspace` parancsot. Ehhez az szükséges, győződjön meg arról, hogy telepítette [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Ezen parancsfájl segítségével hozzon létre egy új OMS Naplóelemzési munkaterületet, és a Service Fabric-megoldás felvétele: 

```ps

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Ha ez befejeződött, ha a fürt egy Windows-fürt, kövesse az OMS Naplóelemzési bekötése a megfelelő tárolási fiók a fenti szakaszban található lépéseket.

Más megoldások hozzáadása vagy más módosításokat az OMS-munkaterület PowerShell használatával is. További, lásd: [kezelése Log Analyticshez PowerShell használatával](../log-analytics/log-analytics-powershell-workspace-configuration.md)

## <a name="next-steps"></a>További lépések
* [Az OMS-ügynök telepítése](service-fabric-diagnostics-oms-agent.md) alakzatot a csomópontok teljesítményszámlálók gyűjtsön, és a docker statisztikák és a tárolók naplóinak gyűjtése
* Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési
* [Adatforrásnézet-tervezőből segítségével egyéni nézeteket hozhat létre a Naplóelemzési](../log-analytics/log-analytics-view-designer.md)
