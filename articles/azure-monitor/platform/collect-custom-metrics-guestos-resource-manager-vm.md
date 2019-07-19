---
title: Vendég operációs rendszer metrikáinak küldése a Azure Monitor metrika-tárolóba egy Resource Manager-sablon használatával a Windows rendszerű virtuális gépekhez
description: Vendég operációs rendszer metrikáinak küldése a Azure Monitor metrika-tárolóba egy Resource Manager-sablon használatával a Windows rendszerű virtuális gépekhez
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 85f7395cbfa4ef2ba6ab448c9541b3f107eb0e96
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249815"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Vendég operációs rendszer metrikáinak küldése a Azure Monitor metrika-tárolóba egy Resource Manager-sablon használatával a Windows rendszerű virtuális gépekhez

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A Azure Monitor [Diagnostics bővítmény](diagnostics-extension-overview.md)használatával mérőszámokat és naplókat gyűjthet a virtuális gép, a felhőalapú szolgáltatás vagy Service Fabric fürt részeként futó vendég operációs rendszerből (vendég operációs rendszerből). A bővítmény [több különböző helyen](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json) is elküldheti a telemetria.

Ez a cikk a vendég operációs rendszer teljesítményének mérőszámait ismerteti a Windows rendszerű virtuális gépek Azure Monitor adattárba való küldésének folyamatán. A diagnosztika 1,11-es verziójától kezdődően a metrikák közvetlenül a Azure Monitor metrikák tárolójába írhatók, ahol a standard platform metrikái már begyűjtése megtörtént.

Az ezen a helyen való tárolás lehetővé teszi, hogy ugyanazokat a műveleteket elérje a platform metrikái esetében. A műveletek közé tartoznak a közel valós idejű riasztások, a diagramok, az Útválasztás és a hozzáférés egy REST API és egyebek között. Korábban a diagnosztikai bővítmény az Azure Storage-ba írt, de nem a Azure Monitor adattárba.

Ha most ismerkedik a Resource Manager-sablonokkal, ismerkedjen meg a [sablonok központi telepítésével](../../azure-resource-manager/resource-group-overview.md) , valamint azok struktúrájával és szintaxisával.

## <a name="prerequisites"></a>Előfeltételek

- Az előfizetést regisztrálni kell a [Microsoft.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)ininsights szolgáltatásban.

- [Azure PowerShell](/powershell/azure) vagy [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) telepítve kell lennie.


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor beállítása adatfogadóként
A Azure Diagnostics bővítmény az "adatnyelők" nevű szolgáltatást használja a metrikák és naplók különböző helyszínekre való átirányításához. A következő lépések bemutatják, hogyan használható a Resource Manager-sablon és a PowerShell egy virtuális gép üzembe helyezéséhez az új "Azure Monitor" adatfogadó használatával.

## <a name="author-resource-manager-template"></a>Resource Manager-sablon készítése
Ebben a példában egy nyilvánosan elérhető minta sablont használhat. A kezdő sablonok a következő https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows helyen találhatók:.

- A **Azuredeploy. JSON** egy előre konfigurált Resource Manager-sablon egy virtuális gép telepítéséhez.

- A **Azuredeploy. Parameters. JSON** egy olyan paraméter-fájl, amely olyan adatokat tárol, mint például a virtuális géphez beállítani kívánt Felhasználónév és jelszó. Az üzembe helyezés során a Resource Manager-sablon a fájlban beállított paramétereket használja.

Töltse le és mentse a fájlokat helyileg.

### <a name="modify-azuredeployparametersjson"></a>Módosítsa a azuredeploy. Parameters. JSON fájlt
Nyissa meg az *azuredeploy. Parameters. JSON fájlt.*

1. Adja meg a virtuális gép **adminUsername** és **adminPassword** értékeit. Ezek a paraméterek a virtuális gép távoli elérésére szolgálnak. Ha el szeretné kerülni, hogy a virtuális gép eltérítve legyen, ne használja a sablonban szereplő értékeket. A robotok a nyilvános GitHub-adattárakban lévő felhasználónevek és jelszavak számára ellenőrzik az interneten. Valószínűleg a virtuális gépeket tesztelik ezekkel az alapértékekkel.

1. Hozzon létre egy egyedi dnsname a virtuális géphez.

### <a name="modify-azuredeployjson"></a>Azuredeploy. JSON módosítása

A *azuredeploy. JSON* fájl megnyitása

A StorageAccountName bejegyzése után vegyen fel egy Storage-fiók AZONOSÍTÓját a sablon **változók** szakaszába **.**

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Adja hozzá ezt a Managed Service Identity (MSI) bővítményt az **erőforrások** szakasz tetején található sablonhoz. A bővítmény biztosítja, hogy a Azure Monitor elfogadja a kibocsátott metrikákat.

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

Adja hozzá az **identitás** konfigurációját a virtuálisgép-erőforráshoz, és győződjön meg arról, hogy az Azure rendszeridentitást RENDEL az MSI-bővítményhez. Ez a lépés biztosítja, hogy a virtuális gép saját maga is kibocsáthatja a Azure Monitor.

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

Adja hozzá a következő konfigurációt a diagnosztikai bővítmény Windowsos virtuális gépen való engedélyezéséhez. Egy egyszerű Resource Manager-alapú virtuális gép esetében hozzáadjuk a bővítmény konfigurációját a virtuális gép erőforrás-tömbhöz. A "mosogatók"&mdash; "AzMonSink" és a megfelelő "SinksConfig" vonal a szakasz későbbi&mdash;részében lehetővé teszi, hogy a bővítmény a metrikákat közvetlenül a Azure monitor. Szükség szerint a teljesítményszámlálók hozzáadására vagy eltávolítására van lehetőség.


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


Mentse és zárjunk be mindkét fájlt.


## <a name="deploy-the-resource-manager-template"></a>A Resource Manager-sablon üzembe helyezése

> [!NOTE]
> Az Azure Diagnostics bővítmény 1,5-es vagy újabb verzióját kell futtatnia, és a **autoUpgradeMinorVersion**: tulajdonság értéke "true" a Resource Manager-sablonban. Az Azure ezután betölti a megfelelő bővítményt, amikor elindítja a virtuális gépet. Ha nem rendelkezik ezekkel a beállításokkal a sablonban, módosítsa őket, és telepítse újra a sablont.


A Resource Manager-sablon üzembe helyezéséhez kihasználjuk a Azure PowerShell.

1. Indítsa el a PowerShellt.
1. Jelentkezzen be az Azure `Login-AzAccount`-ba a használatával.
1. Az előfizetések listájának lekérése a használatával `Get-AzSubscription`.
1. Állítsa be azt az előfizetést, amelyet a virtuális gép létrehozásához vagy frissítéséhez használ:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Ha új erőforráscsoportot szeretne létrehozni az üzembe helyezett virtuális géphez, futtassa a következő parancsot:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Ne felejtse el [használni az egyéni metrikák számára engedélyezett Azure](metrics-custom-overview.md)-régiót.

1. Futtassa a következő parancsokat a virtuális gép Resource Manager-sablonnal történő üzembe helyezéséhez.
   > [!NOTE]
   > Ha egy meglévő virtuális gépet szeretne frissíteni, egyszerűen adja hozzá a *-Mode növekményt* a következő parancs végéhez.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Miután az üzembe helyezés sikeres volt, a virtuális gépnek a Azure Portal kell lennie, és a metrikákat Azure Monitor kell kiadnia.

   > [!NOTE]
   > A kiválasztott vmSkuSize hibákba ütközhet. Ha ez történik, térjen vissza a azuredeploy. JSON fájlhoz, és frissítse a vmSkuSize paraméter alapértelmezett értékét. Ebben az esetben javasolt a "Standard_DS1_v2" kifejezés kipróbálása.

## <a name="chart-your-metrics"></a>A metrikák diagramja

1. Jelentkezzen be az Azure Portalra.

2. A bal oldali menüben válassza a **figyelő**lehetőséget.

3. A figyelés lapon válassza a **metrikák**lehetőséget.

   ![Metrikák lap](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Módosítsa az összesítési időszakot az **elmúlt 30 percre**.

5. Az erőforrás legördülő menüben válassza ki a létrehozott virtuális gépet. Ha nem módosította a nevet a sablonban, *SimpleWinVM2*kell lennie.

6. A névterek legördülő menüben válassza az **Azure. VM. Windows. Guest lehetőséget.**

7. A metrikák legördülő menüben válassza a **memória\%által véglegesített bájtok használatban**lehetőséget.


## <a name="next-steps"></a>További lépések
- További információ az [Egyéni metrikákkal](metrics-custom-overview.md)kapcsolatban.

