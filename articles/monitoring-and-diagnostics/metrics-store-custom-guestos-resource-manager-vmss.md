---
title: Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolásához, egy Windows virtuálisgép-méretezési csoportot a Resource Manager-sablon használatával
description: Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolásához, egy Windows virtuálisgép-méretezési csoportot a Resource Manager-sablon használatával
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: d896cb01c7dc2cd4ed028db418f838809c7ce25c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986999"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolásához, egy Windows virtuálisgép-méretezési csoportot a Resource Manager-sablon használatával

Az Azure Monitor [Windows Azure Diagnostics bővítmény](azure-diagnostics.md) (WAD) lehetővé teszi, hogy a vendég operációs rendszer (vendég operációs rendszer) futó virtuális gépek, Cloud Service vagy Service Fabric-fürt részeként metrikák és naplók gyűjtését.  A bővítmény telemetriát küldhessen a korábban hivatkozott cikk felsorolt számos különböző helyeken.  

Ez a cikk ismerteti a folyamat küldési vendég operációs rendszer teljesítménymetrikák méretezési csoportokhoz tartozó Windows virtuális gép az Azure Monitor-adattárba. WAD 1.11-es verzió kezdve írhatja metrikák közvetlenül az Azure Monitor metrikák tároló, ahol már standard platform metrikákat gyűjt. Ezen a helyen tárolja őket lehetővé teszi tartozó platform metrikák elérhető ugyanazokat a műveleteket.  Műveletek közé tartozik a közel valós idejű riasztások, diagramkészítési, útválasztás, REST API-t és egyéb elérését.  A múltban a WAD-bővítmény írt Azure Storage, de nem az Azure Monitor adattár.  

Ha most ismerkedik a Resource Manager-sablonokat, további információ [sablon-üzembehelyezések](../azure-resource-manager/resource-group-overview.md), és azok szerkezetét, és a szintaxis.  

## <a name="pre-requisites"></a>Előfeltételek

- Az előfizetés regisztrálva kell lenniük [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Rendelkeznie kell [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) telepítve, vagy használhat [az Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Állítsa be az Azure Monitor fogadására 
Az Azure Diagnostics bővítmény nevű, "data fogadók" útvonal metrikák és naplók különböző helyekre szolgáltatást használja.  A következő lépések bemutatják, hogyan a új "Az Azure Monitor"-adatokat gyűjtő virtuális gép üzembe helyezése Resource Manager-sablon és a PowerShell használatával. 

## <a name="author-resource-manager-template"></a>Szerző Resource Manager-sablon 
Ebben a példában egy nyilvánosan elérhető mintasablon is használhatja. A kiindulási sablonok vannak https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale  

- **Azuredeploy.JSON** egy előre konfigurált Resource Manager-sablon egy virtuálisgép-méretezési csoportot üzembe helyezéséhez

- **Azuredeploy.Parameters.JSON** paraméterfájl, amely tárolja az információkat, például milyen felhasználónevet és jelszót szeretne-e beállítva a virtuális gép van. A telepítés során a Resource Manager-sablon paraméterei a beállítása az ezt a fájlt. 

Töltse le és mentse mindkét fájlt helyileg. 

###  <a name="modify-azuredeployparametersjson"></a>Azuredeploy.parameters.json módosítása
Nyissa meg a *azuredeploy.parameters.json* fájl 

- Adjon meg egy **vmSKU** szeretné üzembe helyezése (javasoljuk, hogy standard D2 v3) 
- Adjon meg egy **windowsOSVersion** szeretné a virtuális gép méretezési csoporthoz (javasoljuk, hogy 2016 Datacenter) 
- Erőforrás telepíthető a használata a virtuálisgép-méretezési csoport nevét egy **vmssName** tulajdonság. Ha például *VMSS-WAD-TEST*.    
- Adja meg, amelyet szeretne futnak a virtuálisgép-méretezési csoportot a virtuális gépek számát a **instanceCount** tulajdonság
- Adjon meg értéket a **adminUsername** és **adminPassword** a virtuálisgép-méretezési csoport. Ezeket a paramétereket távoli hozzáférést a méretezési csoportban lévő virtuális gépek használhatók. Ezek a paraméterek használhatók a virtuális gép távoli elérését. Ne használja a sablon azokat a virtuális gép eltérített ne kelljen. A robotok vizsgálati felhasználónevei és jelszavai a nyilvános Github-tárházakban az internethez. Valószínűleg ezeket az alapértelmezett értékeket a virtuális gépek tesztelését. 


###  <a name="modify-azuredeployjson"></a>Azuredeploy.json módosítása
Nyissa meg a *azuredeploy.json* fájl 

Adjon hozzá egy változót, a storage-fiók adatait tartsa a Resource Manager-sablonban. Továbbra is kell adnia egy Tárfiókot a diagnosztikai bővítmény telepítésének részeként. Naplók és/vagy a diagnosztika pluginconfig.JSON fájlban megadott teljesítményszámlálók írja felül az Azure Monitor metrika tároló küld a megadott tárfiók. 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
 ```
 
Keresse meg a virtuálisgép-méretezési definíció az erőforrások szakaszban, és adja hozzá a konfigurációhoz az "identity" szakasz. Ez biztosítja, hogy az Azure hozzárendeli azt egy rendszer-azonosító. Ez a lépés biztosítja, hogy a méretezési csoportban lévő virtuális gépek el tudná küldeni az Azure monitornak magukról a Vendég mérőszámok.  

```json
  { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
 ```

A virtuálisgép-méretezési csoportot erőforrás, keresse meg a **virtualMachineProfile** szakaszban. Adjon hozzá egy új profilt nevű **extensionsProfile** bővítmények kezeléséhez.  


Az a **extensionProfile**, új-bővítmény hozzáadása a sablonhoz, ahogy azt a **VMSS-WAD-kiterjesztésben**.  Ez a szakasz a Felügyeltszolgáltatás-identitás (MSI) bővítmény, amely biztosítja, hogy a metrikák alatt kibocsátott fogadja el az Azure Monitor. A **neve** mező tartalmazhat bármilyen nevet. 

Az alábbi kódot az MSI-bővítmény alatt is hozzáadja a diagnosztikai bővítmény és a konfiguráció-bővítmény erőforrásként a virtuálisgép-méretezési készlet erőforrás. Nyugodtan igény szerint teljesítményszámlálók hozzáadása/eltávolítása. 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed service identity   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Adja hozzá a dependsOn annak érdekében, hogy a megfelelő sorrendben létrehozása a tárfiókhoz. 
```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
 ```

Hozzon létre egy tárfiókot, ha egy még nem lett létrehozva a sablonban.  
```json
"resources": [
  // add this code    
  {
     "type": "Microsoft.Storage/storageAccounts",
     "name": "[variables('storageAccountName')]",
     "apiVersion": "2015-05-01-preview",
     "location": "[resourceGroup().location]",
     "properties": {
       "accountType": "Standard_LRS"
      }
  },
  // end added code
  { 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Mentse és zárja be mindkét fájlt. 

## <a name="deploy-the-resource-manager-template"></a>A Resource Manager-sablon üzembe helyezése 

> [!NOTE]
> Az Azure Diagnostics bővítmény 1.5-ös verzióját kell futtatnia, vagy magasabb, és rendelkezik a "autoUpgradeMinorVersion": tulajdonság *igaz* a Resource Manager-sablonban.  Az Azure majd a rendszer betölti a megfelelő bővítményt a virtuális gép indításakor. Ha ezek a beállítások a sablonban nem rendelkezik, módosítsa őket, és a sablon újbóli telepítése. 


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
    New-AzureRmResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Ne felejtse el használni egy Azure-régióban, ahol engedélyezve van az egyéni metrikákat. Lásd: 
 
1. Hajtsa végre az alábbi parancsokat a virtuális gép üzembe helyezése a  
   > [!NOTE] 
   > Ha szeretne frissíteni egy már létező méretezési csoporthoz, egyszerűen adja hozzá *-mód növekményes* a következő parancs végéhez. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Miután az üzembe helyezés sikeres, a virtuálisgép-méretezési csoportba az Azure Portalon található képesnek kell lennie, és azt kell kibocsátó mérőszámok az Azure Monitor használatával. 

   > [!NOTE] 
   > Előfordulhat, hogy hibákba ütközik a kiválasztott vmSkuSize körül. Ha ez történik, lépjen vissza az azuredeploy.json fájlt, és frissítse a vmSkuSize paraméter alapértelmezett értékét. Ebben az esetben azt javasoljuk, próbáljon "Standard_DS1_v2"). 


## <a name="chart-your-metrics"></a>A metrikák mutató részletes diagram 

1. Jelentkezzen be az Azure Portalra 

1. A bal oldali menüben kattintson a **figyelője** 

1. A figyelő lapon kattintson a **metrikák**. 

   ![Metrikák lap](./media/metrics-store-custom-rest-api/metrics.png) 

1. Az összesítési időszak módosításához **elmúlt 30 percben**.  

1. Az erőforrás listából válassza ki az imént létrehozott virtuálisgép-méretezési.  

1. Válassza ki a legördülő névterek **azure.vm.windows.guest** 

1. A mérőszámok a legördülő menüben válassza ki **memória\%előjegyzett memória kihasználtsága**.  

Ezután választhatja a dimenziók használatára Ez a mérőszám a diagram – Ez a mérőszám a méretezési csoportban egy adott virtuális gép számára, illetve jeleníti meg a méretezési csoportba tartozó egyes virtuális Gépeket. 



## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](metrics-custom-overview.md).

