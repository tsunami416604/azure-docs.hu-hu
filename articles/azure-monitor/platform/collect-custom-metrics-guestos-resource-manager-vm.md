---
title: Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolja a Windows virtuális gép Resource Manager-sablon használatával
description: Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolja a Windows virtuális gép Resource Manager-sablon használatával
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 63b134ab9bfdac3617c845da7a14ee6b9234c84d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782020"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolja a Windows virtuális gép Resource Manager-sablon használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure Monitor használatával [diagnosztikai bővítmény](diagnostics-extension-overview.md), hogy tud gyűjteni a vendég operációs rendszerek (Guest OS), egy virtuális gép, a felhőalapú szolgáltatás vagy a Service Fabric-fürt részeként futó metrikák és naplók. A bővítmény küldhet telemetriát [számos különböző helyeken.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Ez a cikk azt ismerteti, hogyan Windows virtuális gép vendég operációs rendszer teljesítmény-mérőszámok küldése az Azure Monitor-adattárba. 1.11-es verzió diagnosztikai kezdve írhatja metrikák közvetlenül az Azure Monitor metrikák tárol, ahol már standard platform metrikákat gyűjt.

Ezen a helyen tárolja őket lehetővé teszi ugyanazokat a műveleteket tartozó platform metrikák elérését. Műveletek tartalmazzák a közel valós idejű riasztás, a diagram, Útválasztás és érheti el a REST API-t és egyéb. A múltban a diagnosztikai bővítmény írt, az Azure Storage, de nem az Azure Monitor adattár.

Ha most ismerkedik a Resource Manager-sablonokat, további információ [sablon-üzembehelyezések](../../azure-resource-manager/resource-group-overview.md) és azok szerkezetét, és a szintaxis.

## <a name="prerequisites"></a>Előfeltételek

- Az előfizetés regisztrálva kell lenniük [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Rendelkeznie kell [Azure PowerShell-lel](/powershell/azure) vagy [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) telepítve.


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Állítsa be az Azure Monitor fogadására
Az Azure Diagnostics bővítmény nevű, "data fogadók" útvonal metrikák és naplók különböző helyekre szolgáltatást használja. A következő lépések bemutatják, hogyan üzembe helyezése egy virtuális Gépet az új "Az Azure Monitor" adatfogadó használatával a Resource Manager-sablon és a PowerShell használatával.

## <a name="author-resource-manager-template"></a>Szerző Resource Manager-sablon
Ebben a példában egy nyilvánosan elérhető mintasablon is használhatja. A kiindulási sablonok vannak https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy.JSON** egy előre konfigurált Resource Manager-sablon egy virtuális gép központi telepítésére vonatkozóan.

- **Azuredeploy.Parameters.JSON** paraméterfájl, amely tárolja az adatokat, például milyen felhasználónevet és jelszót szeretne-e beállítva a virtuális gép van. A telepítés során a Resource Manager-sablon paraméterei a, amely ebben a fájlban vannak beállítva.

Töltse le és mentse mindkét fájlt helyileg.

### <a name="modify-azuredeployparametersjson"></a>Azuredeploy.parameters.json módosítása
Nyissa meg a *azuredeploy.parameters.json* fájl

1. Adjon meg értéket a **adminUsername** és **adminPassword** a virtuális gép számára. Ezek a paraméterek használhatók a virtuális gép távoli elérését. A virtuális gép eltérített kelljen, nem található értékek használatára Ez a sablon. A robotok vizsgálata az interneten a felhasználói neveket és jelszavakat a nyilvános GitHub-tárházakban. Valószínűleg ezeket az alapértelmezett értékeket a virtuális gépek tesztelését.

1. Hozzon létre egy egyedi dnsname a virtuális gép számára.

### <a name="modify-azuredeployjson"></a>Modify azuredeploy.json

Nyissa meg a *azuredeploy.json* fájl

Egy tárfiók azonosítója, adja hozzá a **változók** szakasz bejegyzésére után a sablon **storageAccountName.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

A Felügyeltszolgáltatás-identitás (MSI) bővítmény hozzáadása a sablonhoz felső részén a **erőforrások** szakaszban. A bővítmény biztosítja, hogy az Azure Monitor elfogadja, hogy a kibocsátott vannak folyamatban.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "WADExtensionSetup",
        "apiVersion": "2015-05-01-preview",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Adja hozzá a **identitás** konfigurációját, és győződjön meg arról, hogy az Azure hozzárendeli egy rendszer-identitás az MSI-bővítmény, a VM-erőforrás. Ez a lépés biztosítja, hogy a virtuális gép el tudná küldeni az Azure monitornak saját magáról a Vendég mérőszámok.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Adja hozzá a Windows virtuális gép a diagnosztikai bővítmény engedélyezése a következő konfigurációt. Egyszerű Resource Manager-alapú virtuális gép hogy a bővítmény konfigurációja a virtuális gép az erőforrások tömb is hozzáadhat. A következő sorban: "fogadóként"&mdash; "AzMonSink" és a megfelelő "SinksConfig" a szakasz későbbi részében&mdash;kibocsátható metrikák közvetlenül az Azure monitornak a bővítmény engedélyezéséhez. Nyugodtan hozzáadni vagy eltávolítani a teljesítményszámlálókat, igény szerint.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "extensions",
            "name": "Microsoft.Insights.VMDiagnosticsSettings",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Mentse és zárja be mindkét fájlt.


## <a name="deploy-the-resource-manager-template"></a>A Resource Manager-sablon üzembe helyezése

> [!NOTE]
> Az Azure Diagnostics bővítmény verziója fut az 1.5-ös vagy újabb kell és rendelkeznie kell a **autoUpgradeMinorVersion**: tulajdonság értéke "true", a Resource Manager-sablonban. Az Azure majd a rendszer betölti a megfelelő bővítményt a virtuális gép indításakor. Ha ezek a beállítások a sablonban nincs, módosítsa őket, és a sablon újbóli telepítése.


A Resource Manager-sablon üzembe helyezéséhez használhatja azt az Azure PowerShell.

1. Indítsa el a Powershellt.
1. Jelentkezzen be Azure-bA `Login-AzAccount`.
1. Az előfizetések listájának lekérése használatával `Get-AzSubscription`.
1. Állítsa be az előfizetést, amelyhez a virtuális gépet létrehozni vagy frissíteni használ:

   ```PowerShell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Hozzon létre egy új erőforráscsoportot, a telepítés alatt álló virtuális gép, futtassa a következő parancsot:

   ```PowerShell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Ne felejtse el [használata egy Azure-régióban, ahol engedélyezve van az egyéni metrikákat](metrics-custom-overview.md).

1. Futtassa az alábbi parancsokat a virtuális gép használata a Resource Manager-sablon üzembe helyezéséhez.
   > [!NOTE]
   > Ha szeretné frissíteni a meglévő virtuális gép, egyszerűen adja hozzá *-mód növekményes* a következő parancs végéhez.

   ```PowerShell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Miután az üzembe helyezés sikeres, a virtuális gép kell lennie az Azure Portalon kibocsátó mérőszámok az Azure Monitor használatával.

   > [!NOTE]
   > Előfordulhat, hogy hibákba ütközik a kiválasztott vmSkuSize körül. Ha ez történik, lépjen vissza az azuredeploy.json fájlt, és frissítse a vmSkuSize paraméter alapértelmezett értékét. Ebben az esetben azt javasoljuk, próbáljon "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>A metrikák mutató részletes diagram

1. Jelentkezzen be az Azure Portalra.

2. A bal oldali menüben válassza ki a **figyelő**.

3. A figyelő oldalon válassza ki a **metrikák**.

   ![Metrikák lap](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Az összesítési időszak módosításához **elmúlt 30 percben**.

5. A resource legördülő menüben válassza ki a létrehozott virtuális gép. Ha nem módosította a sablon nevét, kell lennie *SimpleWinVM2*.

6. A névterek legördülő menüben válassza ki a **azure.vm.windows.guest**

7. A metrikák legördülő menü, válassza ki a **memória\%előjegyzett memória kihasználtsága**.


## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](metrics-custom-overview.md).

