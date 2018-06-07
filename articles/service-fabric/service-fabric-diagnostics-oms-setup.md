---
title: A Naplóelemzési figyelés beállítása az Azure Service Fabric - |} Microsoft Docs
description: Megtudhatja, hogyan állíthatja be Naplóelemzési megjelenítése és események elemzése az Azure Service Fabric-fürtök figyelése.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/03/2018
ms.author: srrengar
ms.openlocfilehash: 25db5075e2099dee354c4c5ef999b26c8e0c50c9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34642665"
---
# <a name="set-up-log-analytics-for-a-cluster"></a>A Naplóelemzési beállítása egy fürtben

A Naplóelemzési az ajánlott megoldás a fürt szolgáltatásiszint-események figyelésére. A Naplóelemzési munkaterület Azure Resource Manager, a PowerShell vagy az Azure piactér állíthat be. Ha a telepítés későbbi használat érdekében egy frissített Resource Manager-sablon, ugyanazt a sablont segítségével állítsa be a Naplóelemzési környezetet. Üzembe helyezés piactér egyszerűbb, ha már rendelkezik egy fürt engedélyezve diagnosztikai telepítik. Ha a fiók, amely telepíti az előfizetés szintű hozzáférés nem rendelkezik, a PowerShell vagy a Resource Manager-sablon telepíthet.

> [!NOTE]
> Naplóelemzési be, figyelheti a fürtöt, meg kell rendelkeznie a fürt- vagy platform-szintjén eseményeinek engedélyezve diagnosztikai. Tekintse meg [diagnosztika a Windows-fürtök beállítása](service-fabric-diagnostics-event-aggregation-wad.md) és [diagnosztika a Linux-fürtök beállítása](service-fabric-diagnostics-event-aggregation-lad.md) további

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>A Naplóelemzési munkaterület telepítését az Azure piactéren

Ha szeretne hozzáadni a Naplóelemzési munkaterület fürt telepítése után, nyissa meg a portálon az Azure piactéren, és keressen **Service Fabric Analytics**. Ez a Service Fabric központi telepítések egy egyéni megoldás, amely a Service Fabric vonatkozó adatokat tartalmaz. A folyamat a megoldás (az irányítópult megtekintéséhez insights) és a munkaterület (fürt adatok összesítésének) hoz létre.

1. Válassza ki **új** a bal oldali navigációs menü. 

2. Keresse meg **háló Analytics szolgáltatás**. Válassza ki az erőforrást, amely akkor jelenik meg.

3. Kattintson a **Létrehozás** gombra.

    ![A piactéren OMS ú elemzés](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Válassza a Service Fabric Analytics létrehozása ablak **munkaterület kiválasztása** a a **OMS-munkaterület** mezőben, majd **hozzon létre egy új munkaterületet**. Töltse ki a szükséges bejegyzéseket. Az egyetlen követelménye, hogy az előfizetés, a Service Fabric-fürt és a munkaterület megegyezik. Ha a bejegyzések érvényesítése a munkaterület központi telepítése elindul. A telepítés csak néhány percet vesz igénybe.

5. Ha elkészült, válassza ki a **létrehozása** újra a Service Fabric Analytics létrehozása ablak alján. Győződjön meg arról, hogy alatt megjelenik az új munkaterületet **OMS-munkaterület**. Ez a művelet hozzáadja a megoldás a munkaterületet.

Ha Windows használ, folytassa a OMS Szolgáltatáshoz való kapcsolódáshoz a tárfiók a fürthöz kapcsolódó események tároló az alábbi lépéseket. 

>[!NOTE]
>Ez a felület Linux-fürtök engedélyezése még nem érhető el. 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>A Naplóelemzési munkaterület csatlakozzon a fürthöz 

1. A munkaterület kell kapcsolódnia kell a fürtből származó diagnosztikai adatokat. Nyissa meg az erőforráscsoporthoz, amelyben létrehozta a Service Fabric elemzési megoldások. Válassza ki **ServiceFabric\<nameOfWorkspace\>**  és Ugrás a – áttekintés oldalra. Ott módosíthatja a megoldás beállításai, munkaterület beállítások és hozzáférés az OMS-munkaterület.

2. A bal oldali navigációs menü alatti **munkaterület adatforrások**, jelölje be **tárfiókok naplók**.

3. A a **a tárolási fiók naplófájljai** lapon jelölje be **Hozzáadás** hozzáadása a fürt naplók a munkaterület tetején.

4. Válassza ki **tárfiók** vegye fel a fürt létrehozása a megfelelő fiókot. Ha az alapértelmezett nevet, van-e a tárfiók **sfdg\<resourceGroupName\>**. Azt is ellenőrizheti az Azure Resource Manager sablon által használt érték a fürt telepítéséhez használandó **applicationDiagnosticsStorageAccountName**. Ha a név nem jelenik meg, görgessen lefelé, és válassza ki **betöltése több**. Válassza ki a tárfiók nevét.

5. Adja meg az adatok típusát. Állítsa az értékét **Service Fabric események**.

6. Győződjön meg arról, hogy a forrás értéke automatikusan **WADServiceFabric\*címke**.

7. Válassza ki **OK** a munkaterület kapcsolódni a fürt naplókat.

    ![Adja hozzá a tárolási fiók naplófájljai az OMS-be](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A fiók most megjelenik, a munkaterület adatforrások bejelentkezik a tárfiók részét.

A Service Fabric elemzési megoldás az OMS Naplóelemzési munkaterület, most már megfelelően csatlakozik-e a fürt platform és Alkalmazásnapló-tábla jelentek meg. Azonos módon adhat hozzá további források a munkaterületre.


## <a name="deploy-oms-by-using-a-resource-manager-template"></a>A Resource Manager-sablon használatával OMS telepítése

A Resource Manager-sablon használatával egy fürt üzembe helyezésekor a sablont hoz létre egy új OMS-munkaterület, a Service Fabric-megoldás hozzáadja a munkaterület, és konfigurálja úgy, hogy a megfelelő tárolási táblákból származó adatokat olvasni.

Használ, és módosítsa [minta sablon](https://github.com/krnese/azure-quickstart-templates/tree/master/service-fabric-oms) az igényeknek.

A következő módosításokat:
1. Adja hozzá `omsWorkspaceName` és `omsRegion` a paramétereire az alábbi kódrészletet a definiált paraméterek hozzáadásával a *template.json* fájlt. Nyugodtan módosíthatja az alapértelmezett értékeket, ahogyan szeretné. Továbbá adja hozzá a két új paramétereket tartalmaz a *parameters.json* megadhatók azok az erőforrások telepítése értékeinek fájlt:
    
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

    A `omsRegion` értéknek meg kell felelnie egy meghatározott értéket. Ki kell választania azt a központi telepítés, a fürt legközelebb van.

2. Ha bármely alkalmazásnaplók küldi el az OMS Szolgáltatáshoz, először győződjön meg arról, hogy a `applicationDiagnosticsStorageAccountType` és `applicationDiagnosticsStorageAccountName` szerepel a sablonban szereplő paraméterekkel. Ha nem szerepel, a változók szakaszban adja hozzá, és igény szerint szerkessze az értékekre. Is használható őket paraméterekként következő előző formátuma.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Vegye fel a Service Fabric OMS-megoldás a sablon változók:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Adja hozzá a következő, az erőforrások szakasz végén után, ahol a Service Fabric-fürt erőforrás van deklarálva:

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

5. A sablon üzembe helyezése a Resource Manager-frissítés a fürthöz, segítségével a `New-AzureRmResourceGroupDeployment` API-nak a AzureRM PowerShell-modult. A példában a parancs a következő lesz:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Az Azure Resource Manager azt észleli, hogy ez a parancs egy frissítést adunk ki a meglévő erőforrás. Csak feldolgozza a módosításokat a befolyásoló tényezők a meglévő központi telepítési sablont és az új sablon között.

## <a name="deploy-oms-by-using-azure-powershell"></a>OMS telepítését az Azure PowerShell használatával

Az OMS szolgáltatáshoz erőforrás PowerShell használatával is telepítheti a `New-AzureRmOperationalInsightsWorkspace` parancsot. Ezt a módszert használja, győződjön meg arról, hogy telepítette [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Ezen parancsfájl segítségével hozzon létre egy új OMS Naplóelemzési munkaterületet, és a Service Fabric-megoldás felvétele: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
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

Amikor elkészült, kövesse az előző szakaszban a megfelelő tárfiók OMS szolgáltatáshoz kapcsolódni.

Más megoldások hozzáadása vagy más módosításokat az OMS-munkaterület PowerShell használatával is. További tudnivalókért lásd: [PowerShell-lel kezelése Naplóelemzési](../log-analytics/log-analytics-powershell-workspace-configuration.md).

## <a name="next-steps"></a>További lépések
* [Az OMS-ügynök telepítése](service-fabric-diagnostics-oms-agent.md) alakzatot a csomópontok teljesítményszámlálók gyűjtsön, és a docker statisztikák és a tárolók naplóinak gyűjtése
* Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési
* [Adatforrásnézet-tervezőből segítségével egyéni nézeteket hozhat létre a Naplóelemzési](../log-analytics/log-analytics-view-designer.md)
