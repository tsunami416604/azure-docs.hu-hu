---
title: Windows virtuális gép metrikáinak gyűjtése az Azure Monitorban sablonnal
description: Vendég operációsrendszer-metrikák küldése az Azure Monitor metrikatárolóba egy Windows virtuális gép Erőforrás-kezelő sablonja segítségével
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e747ca89912c36538bfb9d02986629fe57c5adcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657367"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Vendég operációsrendszer-mérőszámok küldése az Azure Monitor metrikatárolóba egy Windows virtuális gép Erőforrás-kezelő sablonja segítségével

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure Monitor [diagnosztikai bővítmény](diagnostics-extension-overview.md)használatával metrikákat és naplókat gyűjthet a vendég operációs rendszerből (Vendég operációs rendszer), amely egy virtuális gép, felhőszolgáltatás vagy a Service Fabric-fürt részeként fut. A bővítmény telemetriai adatokat küldhet [számos különböző helyre.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Ez a cikk ismerteti a folyamat a vendég operációs rendszer teljesítménymetrikák küldése egy Windows virtuális gép az Azure Monitor adattárba. Az 1.11-es diagnosztikai verzióval kezdve metrikákat írhat közvetlenül az Azure Monitor metrikatárolóba, ahol a standard platformmetrikák már összegyűjtöttek.

Ezen a helyen való tárolásuk lehetővé teszi, hogy ugyanazokat a műveleteket érje el a platformmérő számokhoz. A műveletek közé tartozik a közel valós idejű riasztás, a diagramkészítés, az útválasztás és a REST API-ból való hozzáférés és egyebek. A múltban a Diagnosztika bővítmény írt az Azure Storage, de nem az Azure Monitor adattárba.

Ha most ismerkedik az Erőforrás-kezelő sablonokkal, ismerje meg a [sablontelepítéseket,](../../azure-resource-manager/management/overview.md) azok szerkezetét és szintaxisát.

## <a name="prerequisites"></a>Előfeltételek

- Előfizetését regisztrálni kell a [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Az [Azure PowerShellt](/powershell/azure) vagy az [Azure Cloud Shellt](https://docs.microsoft.com/azure/cloud-shell/overview) telepítenie kell.

- A virtuálisgép-erőforrásnak [olyan régióban kell lennie, amely támogatja az egyéni metrikákat.](metrics-custom-overview.md#supported-regions) 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Az Azure Monitor beállítása adatgyűjtőként
Az Azure Diagnostics bővítmény egy "adatgyűjtők" nevű szolgáltatást használ a metrikák és naplók különböző helyekre történő továbbításához. A következő lépések bemutatják, hogyan használhatja a Resource Manager-sablon és a PowerShell virtuális gép üzembe helyezéséhez az új "Azure Monitor" adatgyűjtő használatával.

## <a name="author-resource-manager-template"></a>Szerző erőforrás-kezelő sablon
Ebben a példában nyilvánosan elérhető mintasablont is használhat. A kezdő sablonok https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windowsa helyen találhatók.

- **Az Azuredeploy.json** egy előre konfigurált Erőforrás-kezelő sablon egy virtuális gép üzembe helyezéséhez.

- **Az Azuredeploy.parameters.json** egy paraméterfájl, amely olyan információkat tárol, például hogy milyen felhasználónevet és jelszót szeretne beállítani a virtuális géphez. A telepítés során az Erőforrás-kezelő sablon a fájlban beállított paramétereket használja.

Töltse le és mentse mindkét fájlt helyileg.

### <a name="modify-azuredeployparametersjson"></a>Az azuredeploy.parameters.json módosítása
Nyissa meg az *azuredeploy.parameters.json* fájlt

1. Adja meg az **adminUsername** és **adminPassword** értékét a virtuális géphez. Ezek a paraméterek a virtuális gép távoli eléréséhez használatosak. Annak elkerülése érdekében, hogy a virtuális gép eltérített, NE használja a sablonban szereplő értékeket. A botok a nyilvános GitHub-adattárakban keresik a felhasználóneveket és jelszavakat az interneten. Ezek valószínűleg tesztelik a virtuális gépeket ezekkel az alapértelmezett beállításokkal.

1. Hozzon létre egy egyedi dns-nevet a virtuális gép.

### <a name="modify-azuredeployjson"></a>Azuredeploy.json módosítása

Az *azuredeploy.json* fájl megnyitása

Tárfiók-azonosító hozzáadása a sablon **változók** szakaszához a **storageAccountName** bejegyzés után.

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Adja hozzá ezt a felügyelt szolgáltatásidentitás(MSI) bővítményt az **erőforrások** szakasz tetején lévő sablonhoz. A bővítmény biztosítja, hogy az Azure Monitor elfogadja a kibocsátott metrikákat.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
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

Adja hozzá az **identitáskonfigurációt** a virtuális gép erőforrásához annak érdekében, hogy az Azure hozzárendelje a rendszeridentitást az MSI-bővítményhez. Ez a lépés biztosítja, hogy a virtuális gép vendégmetrikákat bocsáthat ki magáról az Azure Monitorba.

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

Adja hozzá a következő konfigurációt a Diagnosztika bővítmény Windows virtuális gépen való engedélyezéséhez. Egy egyszerű Erőforrás-kezelő-alapú virtuális gép, felveheti a bővítmény konfigurációját az erőforrások tömb a virtuális gép. A "sinks"&mdash; "AzMonSink" és a megfelelő "SinksConfig" vonal a szakasz&mdash;későbbi részében lehetővé teszi, hogy a bővítmény metrikákat közvetlenül az Azure Monitorra bocsát. Nyugodtan adjon hozzá vagy távolítson el teljesítményszámlálókat, ha szükséges.


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
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
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


Mindkét fájl mentése és bezárása.


## <a name="deploy-the-resource-manager-template"></a>Az Erőforrás-kezelő sablon telepítése

> [!NOTE]
> Az Azure Diagnostics bővítmény 1.5-ös vagy újabb verzióját kell futtatnia, és az **autoUpgradeMinorVersion**: tulajdonság értéke "true" az Erőforrás-kezelő sablonban. Az Azure majd betölti a megfelelő bővítményt, amikor elindítja a virtuális gép. Ha nem rendelkezik ezekkel a beállításokkal a sablonban, módosítsa őket, és telepítse újra a sablont.


A Resource Manager-sablon üzembe helyezéséhez kihasználjuk az Azure PowerShellt.

1. Indítsa el a PowerShellt.
1. Jelentkezzen be az `Login-AzAccount`Azure-ba a használatával.
1. Az előfizetések listájának `Get-AzSubscription`beszerezhető a használatával.
1. Állítsa be a virtuális gép létrehozásához/frissítéséhez használt előfizetést:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Ha új erőforráscsoportot szeretne létrehozni az üzembe helyezett virtuális géphez, futtassa a következő parancsot:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Ne [felejtsen el olyan Azure-régiót használni, amely egyéni metrikákhoz engedélyezett.](metrics-custom-overview.md)

1. Futtassa a következő parancsokat a virtuális gép erőforrás-kezelő sablon használatával történő központi telepítéséhez.
   > [!NOTE]
   > Ha egy meglévő virtuális gép frissítését szeretné frissíteni, egyszerűen adja hozzá a *-Mode Incremental* parancsot a következő parancs végéhez.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Miután a központi telepítés sikeres, a virtuális gép az Azure Portalon kell lennie, metrikákat bocsát ki az Azure Monitor.

   > [!NOTE]
   > Előfordulhat, hogy hibákba ütközik a kiválasztott vmSkuSize körül. Ha ez történik, lépjen vissza az azuredeploy.json fájlba, és frissítse a vmSkuSize paraméter alapértelmezett értékét. Ebben az esetben azt javasoljuk, hogy próbálja ki a "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>A mutatók feltérképezése

1. Jelentkezzen be az Azure Portalra.

2. A bal oldali menüben válassza a **Monitor**lehetőséget.

3. A Figyelő lapon válassza a **Metrikák**lehetőséget.

   ![Mérőszámok lap](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Módosítsa az összesítési időszakot **utolsó 30 percre.**

5. Az erőforrás legördülő menüben válassza ki a létrehozott virtuális gép. Ha nem módosította a sablonnevét, akkor *a SimpleWinVM2*nevet kell tennie.

6. A névterek legördülő menüben válassza az **azure.vm.windows.guest lehetőséget.**

7. A metrikák legördülő menüben válassza a **Használatban lévő\%Memóriavéglegesített bájtok lehetőséget.**


## <a name="next-steps"></a>További lépések
- További információ az [egyéni mutatókról.](metrics-custom-overview.md)

