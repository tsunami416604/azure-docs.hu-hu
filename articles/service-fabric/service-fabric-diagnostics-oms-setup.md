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
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: 61182668b2677f19edbb736505d4892150890fed
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="set-up-oms-log-analytics-for-a-cluster"></a>A fürt OMS Naplóelemzési beállítása

Az OMS-munkaterület Azure Resource Manageren keresztül vagy a Azure piactérről állíthat be. Az előző használja, ha meg szeretné tartani a sablon a telepítés későbbi használatra. Egyszerűbb, ha már rendelkezik egy fürt üzembe helyezéséhez engedélyezve diagnosztikai piactéren keresztül telepítése.

> [!NOTE]
> Diagnosztika engedélyezve van a fürt fürt megtekintéséhez szükséges / platform szintjén az eseményeket az OMS sikeresen beállíthatja a fürt figyelése.

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
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
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

5. A sablon telepítéséhez a fürt erőforrás-kezelő frissítéseként. Ebben az esetben használja a `New-AzureRmResourceGroupDeployment` API-nak a AzureRM PowerShell-modult. A példában a parancs a következő lesz:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Az Azure Resource Manager lesz képes észlelni, hogy ez a meglévő erőforrás frissítése. A módosítások a befolyásoló tényezők a meglévő központi telepítési sablont és az új sablon között csak fogja feldolgozni.

## <a name="deploying-oms-using-azure-marketplace"></a>Használja az Azure piactér OMS telepítése

Ha inkább az OMS-munkaterület hozzáadása a fürt telepítése után, látogasson el az Azure piactér (a portálon), és keressen *"Service Fabric Analytics"*.

1. Kattintson a **új** a bal oldali navigációs menü. 

2. Keresse meg *háló Analytics szolgáltatás*. Kattintson az erőforrás-be.

3. Kattintson a **létrehozása**

    ![A piactéren OMS ú elemzés](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. A Service Fabric Analytics létrehozása ablakában kattintson **munkaterület kiválasztása** a a *OMS-munkaterület* mezőben, majd **hozzon létre egy új munkaterületet**. Töltse ki a szükséges bejegyzések – a egyetlen követelménye, hogy az előfizetés, a Service Fabric-fürt és az OMS-munkaterület azonosnak kell lennie. Ha a bejegyzések érvényesítése az OMS-munkaterület központi telepítéséhez indul el. Ez csak kell eltarthat néhány percig.

5. Ha befejezte, kattintson a **létrehozása** újra a Service Fabric Analytics létrehozása ablak alján. Győződjön meg arról, hogy alatt megjelenik az új munkaterületet *OMS-munkaterület*. Ez hozzáadja a megoldás az imént létrehozott munkaterületet.

6. A munkaterület továbbra is meg kell kapcsolódnia kell a fürtből származó diagnosztikai adatokat. Nyissa meg a Service Fabric elemzési megoldások a létrehozott erőforráscsoportot. Megjelenik egy *ServiceFabric (\<nameOfOMSWorkspace\>)*. Kattintson a – áttekintés oldalra, ahol megoldás beállításokat, a beállítások munkaterületet, és keresse meg az OMS-portálon keresse meg a megoldást.

7. A bal oldali navigációs menüben kattintson a **tárfiókok naplók**a *munkaterület adatforrások*.

8. A a *a tárolási fiók naplófájljai* kattintson **hozzáadása** hozzáadása a fürt naplók a munkaterület tetején.

9. Kattintson a be **tárfiók** vegye fel a fürt létrehozása a megfelelő fiókot. Ha az alapértelmezett nevet, a tárfiók neve *sfdg\<resourceGroupName\>*. Azt is ellenőrizheti az Azure Resource Manager-sablon központi telepítéséhez a fürt által használt érték ellenőrzésével a `applicationDiagnosticsStorageAccountName`. Is lehet, hogy görgessen lefelé, és kattintson a **betöltése több** Ha a név nem jelenik meg. Kattintson a jobb tárfióknév fel rá kattintva jelölje ki.

10. A következő együtt kell megadni a *adattípus*, amely meg **Service Fabric események**.

11. A *forrás* automatikusan meg *WADServiceFabric\*címke*.

12. Kattintson a **OK** a munkaterület kapcsolódni a fürt naplókat.

    ![Adja hozzá a tárolási fiók naplófájljai az OMS-be](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A fiók kell megjelennek-e részeként a *a tárolási fiók naplófájljai* a munkaterület adatforrások.

A most hozzáadott a Service Fabric elemzési megoldás az OMS Naplóelemzési munkaterület, most már megfelelően csatlakozik-e a fürt platform és Alkalmazásnapló-tábla. Azonos módon adhat hozzá további források a munkaterületre.

## <a name="next-steps"></a>További lépések
* [Az OMS-ügynök telepítése](service-fabric-diagnostics-oms-agent.md) alakzatot a csomópontok teljesítményszámlálók gyűjtsön, és a docker statisztikák és a tárolók naplóinak gyűjtése
* Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési
* [Adatforrásnézet-tervezőből segítségével egyéni nézeteket hozhat létre a Naplóelemzési](../log-analytics/log-analytics-view-designer.md)
