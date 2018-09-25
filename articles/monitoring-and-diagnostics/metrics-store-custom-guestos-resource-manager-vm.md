---
title: Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolja a Windows virtuális gépek használata a Resource Manager-sablonnal
description: Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolja a Windows virtuális gépek használata a Resource Manager-sablonnal
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 4ed911766a14dd35ea662326a5d50df11cf81698
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984071"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolja a Windows virtuális gépek használata a Resource Manager-sablonnal

Az Azure Monitor [Windows Azure Diagnostics bővítmény](azure-diagnostics.md) (WAD) lehetővé teszi, hogy a vendég operációs rendszer (vendég operációs rendszer) futó virtuális gépek, Cloud Service vagy Service Fabric-fürt részeként metrikák és naplók gyűjtését.  A bővítmény telemetriát küldhessen a korábban hivatkozott cikk felsorolt számos különböző helyeken.  

Ez a cikk ismerteti a folyamatot a vendég operációs rendszer teljesítmény-mérőszámok küldése egy Windows virtuális gép esetében az Azure Monitor-adattárba. WAD 1.11-es verzió kezdve írhatja metrikák közvetlenül az Azure Monitor metrikák tároló, ahol már standard platform metrikákat gyűjt. Ezen a helyen tárolja őket lehetővé teszi tartozó platform metrikák elérhető ugyanazokat a műveleteket.  Műveletek közé tartozik a közel valós idejű riasztások, diagramkészítési, útválasztás, REST API-t és egyéb elérését.  A múltban a WAD-bővítmény írt Azure Storage, de nem az Azure Monitor adattár.   

Ha most ismerkedik a Resource Manager-sablonokat, további információ [sablon-üzembehelyezések](../azure-resource-manager/resource-group-overview.md), és azok szerkezetét, és a szintaxis.  

## <a name="pre-requisites"></a>Előfeltételek

- Az előfizetés regisztrálva kell lenniük [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Rendelkeznie kell [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) telepítve, vagy használhat [az Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

 
## <a name="set-up-azure-monitor-as-a-data-sink"></a>Állítsa be az Azure Monitor fogadására 
Az Azure Diagnostics bővítmény nevű, "data fogadók" útvonal metrikák és naplók különböző helyekre szolgáltatást használja.  A következő lépések bemutatják, hogyan a új "Az Azure Monitor"-adatokat gyűjtő virtuális gép üzembe helyezése Resource Manager-sablon és a PowerShell használatával. 

## <a name="author-resource-manager-template"></a>Szerző Resource Manager-sablon 
Ebben a példában egy nyilvánosan elérhető mintasablon is használhatja. A kiindulási sablonok vannak https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows 

- **Azuredeploy.JSON** van egy előre konfigurált Resource Manager-sablon egy virtuális gép központi telepítésére vonatkozóan. 

- **Azuredeploy.Parameters.JSON** paraméterfájl, amely tárolja az információkat, például milyen felhasználónevet és jelszót szeretne-e beállítva a virtuális gép van. A telepítés során a Resource Manager-sablon paraméterei a beállítása az ezt a fájlt. 

Töltse le és mentse mindkét fájlt helyileg. 

###  <a name="modify-azuredeployparametersjson"></a>Azuredeploy.parameters.json módosítása
Nyissa meg a *azuredeploy.parameters.json* fájl 

1. Adjon meg értéket a *adminUsername* és *adminPassword* a virtuális gép számára. Ezek a paraméterek használhatók a virtuális gép távoli elérését. Ne használja a sablon azokat a virtuális gép eltérített ne kelljen. A robotok vizsgálati felhasználónevei és jelszavai a nyilvános Github-tárházakban az internethez. Valószínűleg ezeket az alapértelmezett értékeket a virtuális gépek tesztelését.  

1. Hozzon létre egy egyedi dnsname a virtuális gép számára.  

### <a name="modify-azuredeployjson"></a>Azuredeploy.json módosítása

Nyissa meg a *azuredeploy.json* fájl 

Egy tárfiók azonosítója, adja hozzá a **változók** szakasz bejegyzésére után a sablon **storageAccountName**.  

```json
// Find these lines 
"variables": { 
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]", 

// Add this line directly below.  
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]", 
```

A Felügyeltszolgáltatás-identitás (MSI) bővítmény hozzáadása a sablonhoz a "resources" szakasz elején.  A bővítmény biztosítja, hogy az Azure Monitor elfogadja a metrikák kibocsátott folyamatban van.  

```json
//Find this code 
"resources": [
// Add this code directly below
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

Adja hozzá a "identity" konfiguráció a VM-erőforrás Azure rendel az MSI-bővítmény egy rendszer identitás biztosítása érdekében. Ez a lépés biztosítja, hogy a virtuális gép kibocsátható Vendég mérőszámok saját magáról az Azure monitornak 

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

Adja hozzá a diagnosztikai bővítmény egy Windows virtuális gép engedélyezéséhez a következő konfigurációt.  Egy egyszerű Resource Manager-alapú virtuális gép esetén is hozzáadunk a bővítmény konfigurációja az erőforrások tömbhöz a virtuális gép. A következő sorban: "fogadóként": "AzMonSink", és a megfelelő "SinksConfig" a szakasz későbbi részében a bővítmény engedélyezése a közvetlenül az Azure Monitor metrikák kibocsátható. Nyugodtan igény szerint teljesítményszámlálók hozzáadása/eltávolítása.  


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
            "typeHandlerVersion": "1.4", 
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


Mentse és zárja be mindkét fájl 
 

## <a name="deploy-the-resource-manager-template"></a>A Resource Manager-sablon üzembe helyezése 

> [!NOTE]
> Az Azure Diagnostics bővítmény 1.5-ös verzióját kell futtatnia, vagy magasabb, és rendelkezik a "autoUpgradeMinorVersion": tulajdonság értéke "true", a Resource Manager-sablonban.  Az Azure majd a rendszer betölti a megfelelő bővítményt a virtuális gép indításakor. Ha ezek a beállítások a sablonban nem rendelkezik, módosítsa őket, és a sablon újbóli telepítése. 


A Resource Manager-sablon üzembe helyezéséhez használja a Microsoft Azure PowerShell-lel.  

1. A PowerShell indítása 
1. Jelentkezzen be Azure-ban `Login-AzureRmAccount`
1. A használatával előfizetések listájának beolvasása `Get-AzureRmSubscription`
1. Az előfizetés meg fogja kell létrehozása/frissítése a virtuális gép beállításához 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Hozzon létre egy új erőforráscsoportot a virtuális gép üzembe helyezéséhez, futtassa az alábbi parancs 

   ```PowerShell
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>" 
   ```
   > [!NOTE] 
   > Ne felejtse el [használata egy Azure-régióban, ahol engedélyezve van az egyéni metrikákat](metrics-custom-overview.md). 
 
1. Hajtsa végre az alábbi parancsokat a virtuális gép üzembe helyezése a  
   > [!NOTE] 
   > Ha szeretné frissíteni a meglévő virtuális gép, egyszerűen adja hozzá *-mód növekményes* a következő parancs végéhez. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>" 
   ```
  
1. Miután az üzembe helyezés sikeres, az Azure portálon található – a virtuális gép képesnek kell lennie, és azt kell kibocsátó mérőszámok az Azure Monitor használatával. 

   > [!NOTE] 
   > Előfordulhat, hogy hibákba ütközik a kiválasztott vmSkuSize körül. Ha ez történik, lépjen vissza az azuredeploy.json fájlt, és frissítse a vmSkuSize paraméter alapértelmezett értékét. Ebben az esetben azt javasoljuk, próbáljon "Standard_DS1_v2"). 

## <a name="chart-your-metrics"></a>A metrikák mutató részletes diagram 

1. Jelentkezzen be az Azure Portalon 

1. A bal oldali menüben kattintson a **figyelője** 

1. A figyelő lapján kattintson a **metrikák**. 

   ![Metrikák lap](./media/metrics-store-custom-rest-api/metrics.png) 

1. Az összesítési időszak módosításához **elmúlt 30 percben**.  

1. Az erőforrás listából válassza ki az imént létrehozott virtuális gép. Ha nem módosította a sablon nevét, kell lennie *SimpleWinVM2*.  

1. Válassza ki az áttelepítendő névterek legördülő **azure.vm.windows.guest** 

1. A mérőszámok a legördülő menüben válassza ki **memória\%előjegyzett memória kihasználtsága**.  
 

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](metrics-custom-overview.md).