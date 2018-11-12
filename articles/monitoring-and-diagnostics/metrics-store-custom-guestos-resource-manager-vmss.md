---
title: A vendég operációs rendszer mérőszámok küldése az Azure Monitor metrika áruház egy Windows virtuálisgép-méretezési csoportot az Azure Resource Manager-sablon használatával
description: A vendég operációs rendszer mérőszámok küldése az Azure Monitor metrika áruház egy Windows virtuálisgép-méretezési csoportot egy Resource Manager-sablon használatával
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 52d82e20b2156b503de3dc24ea6e01ecd7088d08
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037482"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>A vendég operációs rendszer mérőszámok küldése az Azure Monitor metrika áruház egy Windows virtuálisgép-méretezési csoportot az Azure Resource Manager-sablon használatával

Az Azure Monitor használatával [Windows Azure Diagnostics (WAD) bővítmény](azure-diagnostics.md), metrikákat és naplókat a vendég operációs rendszerből (vendég operációs rendszer) egy virtuális gép, a felhőalapú szolgáltatás vagy az Azure Service Fabric-fürt részeként futó gyűjtheti. A bővítmény telemetriát küldhessen a korábban hivatkozott cikk felsorolt számos különböző helyeken.  

Ez a cikk ismerteti a folyamat küldési vendég operációs rendszer teljesítménymetrikák méretezési csoportokhoz tartozó Windows virtuális gép az Azure Monitor-adattárba. Windows Azure Diagnostics 1.11-es verzió kezdve írhat mérőszámok közvetlenül az Azure Monitor-metrikák tárol, ahol már standard platform metrikákat gyűjt. Ezen a helyen tárolja őket, ugyanazokat a műveleteket tartozó platform metrikák elérhető érheti el. Műveletek közé tartoznak a közel valós idejű riasztások, diagramkészítési, útválasztás, eléréséhez a REST API és egyebek. Múltbeli időpont a Windows Azure Diagnostics bővítmény írt Azure Storage, de nem az Azure Monitor-adattárban.  

Ha most ismerkedik a Resource Manager-sablonokat, további információ [sablon-üzembehelyezések](../azure-resource-manager/resource-group-overview.md) és azok szerkezetét, és a szintaxis.  

## <a name="prerequisites"></a>Előfeltételek

- Az előfizetés regisztrálva kell lenniük [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal). 

- Rendelkeznie kell [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) telepítve, vagy használhat [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Állítsa be az Azure Monitor fogadására 
Az Azure Diagnostics bővítmény nevű szolgáltatást használja a **adatfogadókba** útvonal metrikák és naplók különböző helyekre. A következő lépések bemutatják, hogyan virtuális gép üzembe helyezése az Azure Monitor új adatfogadó használatával a Resource Manager-sablon és a PowerShell használatával. 

## <a name="author-a-resource-manager-template"></a>Szerző Resource Manager-sablonnal 
Ebben a példában használhatja egy nyilvánosan elérhető [mintasablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale):  

- **Azuredeploy.JSON** egy előre konfigurált Resource Manager-sablon egy virtuálisgép-méretezési csoportot üzembe helyezéséhez.

- **Azuredeploy.Parameters.JSON** paraméterfájl, amely tárolja az információkat, például milyen felhasználónevet és jelszót szeretne-e beállítva a virtuális gép van. A telepítés során a Resource Manager-sablon paraméterei a beállítása az ezt a fájlt. 

Töltse le és mentse mindkét fájlt helyileg. 

###  <a name="modify-azuredeployparametersjson"></a>Azuredeploy.parameters.json módosítása
Nyissa meg a **azuredeploy.parameters.json** fájlt:  
 
- Adjon meg egy **vmSKU** számára telepíteni kívánja. Standard D2 v3 javasoljuk. 
- Adjon meg egy **windowsOSVersion** a virtuálisgép-méretezési csoporthoz használni szeretne. Azt javasoljuk, hogy a 2016-Datacenter. 
- Neve a virtuális gép méretezési csoport üzembe helyezni az erőforrást egy **vmssName** tulajdonság. Például **VMSS-WAD-TEST**.    
- Adja meg a virtuálisgép-méretezési csoport használatával állítsa be a futtatni kívánt virtuális gépek számát a **instanceCount** tulajdonság.
- Adjon meg értéket a **adminUsername** és **adminPassword** a virtuálisgép-méretezési csoport. Ezeket a paramétereket távoli hozzáférést a méretezési csoportban lévő virtuális gépek használhatók. A virtuális gép eltérített, ne kelljen **nem** Ez a sablon a portokat használja. A robotok vizsgálati felhasználónevei és jelszavai a nyilvános GitHub-tárházakban az internethez. Azok, hogy találkozni ezeket az alapértelmezett értékeket a virtuális gépek tesztelését. 


###  <a name="modify-azuredeployjson"></a>Azuredeploy.json módosítása
Nyissa meg a **azuredeploy.json** fájlt. 

Adjon hozzá egy változót, a storage-fiók adatait tartsa a Resource Manager-sablonban. Bármely naplók vagy a diagnosztika pluginconfig.JSON fájlban megadott teljesítményszámlálók írt a metrika az Azure Monitor-tároló és a tárfiók, itt is: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Keresse meg a virtuálisgép-méretezési definíció beállítása az erőforrások szakaszban, és adja hozzá a **identitás** a konfigurációs szakaszban. A Hozzáadás biztosítja, hogy az Azure hozzárendeli azt egy rendszer-azonosító. Ez a lépés is biztosítja, hogy a méretezési csoportban lévő virtuális gépek el tudná küldeni az Azure monitornak magukról a Vendég mérőszámok:  

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


Az a **extensionProfile**, új-bővítmény hozzáadása a sablonhoz, ahogyan az a **VMSS-WAD-bővítmény** szakaszban.  Ez a szakasz az Azure-erőforrás-bővítmény, amely a metrikák alatt kibocsátott fogadja el az Azure Monitor biztosítja a felügyelt identitásokból. A **neve** mező tartalmazhat bármilyen nevet. 

A következő kódot az MSI-bővítményében is hozzáadja a diagnosztikai bővítmény és a konfiguráció-bővítmény erőforrásként a virtuálisgép-méretezési készlet erőforrás. Nyugodtan hozzáadni vagy eltávolítani a teljesítményszámlálókat, igény szerint: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identites for Azure resources   
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


Adjon hozzá egy **dependsOn** a tárfiók, annak érdekében, hogy a megfelelő sorrendben létrehozása: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Hozzon létre egy tárfiókot, ha egy nem hozott létre a sablonban: 

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
> Meg kell futnia az Azure Diagnostics bővítmény verziója 1.5-ös vagy újabb **és** rendelkezik a **autoUpgradeMinorVersion:** tulajdonság **igaz** a Resource Manager a sablon. Az Azure majd a rendszer betölti a megfelelő bővítményt a virtuális gép indításakor. Ha ezek a beállítások a sablonban nincs, módosítsa őket, és a sablon újbóli telepítése. 


Azure PowerShell használatával a Resource Manager-sablon üzembe helyezéséhez:  

1. Indítsa el a Powershellt. 
1. Jelentkezzen be Azure-bA `Login-AzureRmAccount`.
1. Az előfizetések listájának lekérése használatával `Get-AzureRmSubscription`.
1. Az előfizetés fog létrehozni, vagy frissítse a virtuális gép beállításához: 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Hozzon létre egy új erőforráscsoportot a virtuális gép üzembe helyezéséhez. Futtassa az alábbi parancsot: 

   ```PowerShell
    New-AzureRmResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Ne felejtse el használni egy Azure-régióban, ahol engedélyezve van az egyéni metrikákat. Ne felejtse el használni egy [Azure-régióban, amely engedélyezve van az egyéni metrikákat](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Futtassa az alábbi parancsokat a virtuális gép üzembe helyezéséhez:  

   > [!NOTE]  
   > Ha szeretne frissíteni egy meglévő méretezési csoportot, adjon hozzá **-mód növekményes** a parancs végéhez. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Miután az üzembe helyezés sikeres, keresse meg a virtuálisgép-méretezési csoportba az Azure Portalon. Azt kell kibocsátania mérőszámok az Azure Monitor használatával. 

   > [!NOTE]  
   > Előfordulhat, hogy hibákba ütközik a kiválasztott körül **vmSkuSize**. Ebben az esetben lépjen vissza a **azuredeploy.json** fájlt, és frissítse az alapértelmezett érték a **vmSkuSize** paraméter. Azt javasoljuk, hogy megpróbálja **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>A metrikák mutató részletes diagram 

1. Jelentkezzen be az Azure portálra. 

1. A bal oldali menüben válassza ki a **figyelő**. 

1. Az a **figyelő** lapon jelölje be **metrikák**. 

   ![Monitor - metrikák lap](media/metrics-store-custom-guestos-resource-manager-vmss/metrics.png) 

1. Az összesítési időszak módosításához **elmúlt 30 percben**.  

1. A resource legördülő menüben válassza ki a virtuális gép méretezési csoportot hozott létre.  

1. A névterek legördülő menüben válassza ki a **azure.vm.windows.guest**. 

1. A metrikák legördülő menüben válassza ki a **memória\%előjegyzett memória kihasználtsága**.  

Ezután választhatja a dimenziók használatára Ez a mérőszám a diagram azt egy adott virtuális gép számára, vagy jeleníti meg a méretezési csoportban lévő mindegyik virtuális gép. 



## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](metrics-custom-overview.md).

