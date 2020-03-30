---
title: Windows-méretezési mutatóinak gyűjtése az Azure Monitorban sablonnal
description: Vendég operációsrendszer-metrikák küldése az Azure Monitor metrikatárolóba egy Erőforrás-kezelő sablon használatával egy Windows virtuálisgép-méretezési csoporthoz
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 24f83e4f6285d045e67bdaef431ebcff2345ef84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663892"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Vendég operációsrendszer-metrikák küldése az Azure Monitor metrikatárolóba egy Azure Resource Manager-sablon használatával egy Windows virtuálisgép-méretezési csoporthoz

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure Monitor [Windows Azure Diagnostics (WAD) bővítmény](diagnostics-extension-overview.md)használatával metrikákat és naplókat gyűjthet a vendég operációs rendszerből (vendég operációs rendszer), amely egy virtuális gép, felhőszolgáltatás vagy Az Azure Service Fabric-fürt részeként fut. A bővítmény telemetriai adatokat küldhet a korábban csatolt cikkben felsorolt számos különböző helyre.  

Ez a cikk ismerteti a folyamat vendég operációs rendszer teljesítménymetrikák küldése az Azure Monitor adattárba beállított Windows virtuálisgép-méretezési mutatók. A Windows Azure Diagnostics 1.11-es verziójától kezdve metrikákat írhat közvetlenül az Azure Monitor metrikák áruházába, ahol a szabványos platformmetrikák már összegyűjtöttek. Ha ezen a helyen tárolja őket, ugyanazokat a műveleteket érheti el, amelyek a platformmetrikákhoz elérhetők. A műveletek közé tartozik a közel valós idejű riasztás, a diagramkészítés, az útválasztás, a REST API-ból való hozzáférés és még sok más. A múltban a Windows Azure Diagnostics bővítmény írt az Azure Storage, de nem az Azure Monitor adattárba.  

Ha most ismerkedik az Erőforrás-kezelő sablonokkal, ismerje meg a [sablontelepítéseket,](../../azure-resource-manager/management/overview.md) azok szerkezetét és szintaxisát.  

## <a name="prerequisites"></a>Előfeltételek

- Előfizetését regisztrálni kell a [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Telepítve kell lennie az [Azure PowerShellnek,](/powershell/azure) vagy használhatja az [Azure Cloud Shellt.](https://docs.microsoft.com/azure/cloud-shell/overview) 

- A virtuálisgép-erőforrásnak [olyan régióban kell lennie, amely támogatja az egyéni metrikákat.](metrics-custom-overview.md#supported-regions)

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Az Azure Monitor beállítása adatgyűjtőként 
Az Azure Diagnostics bővítmény egy **adatgyűjtőnevű** szolgáltatást használ a metrikák és naplók különböző helyekre történő továbbításához. A következő lépések bemutatják, hogyan használhatja a Resource Manager-sablon és a PowerShell virtuális gép üzembe helyezéséhez az új Azure Monitor-adatgyűjtő használatával. 

## <a name="author-a-resource-manager-template"></a>Erőforrás-kezelő sablon készítése 
Ebben a példában nyilvánosan elérhető [mintasablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)használhat:  

- **Az Azuredeploy.json** egy előre konfigurált Erőforrás-kezelő sablon egy virtuálisgép-méretezési csoport üzembe helyezéséhez.

- **Az Azuredeploy.parameters.json** egy paraméterfájl, amely olyan információkat tárol, mint például, hogy milyen felhasználónevet és jelszót szeretne beállítani a virtuális géphez. A telepítés során az Erőforrás-kezelő sablon az ebben a fájlban beállított paramétereket használja. 

Töltse le és mentse mindkét fájlt helyileg. 

###  <a name="modify-azuredeployparametersjson"></a>Az azuredeploy.parameters.json módosítása
Nyissa meg az **azuredeploy.parameters.json** fájlt:  
 
- Adjon meg egy telepíteni kívánt **vmSKU-t.** Javasoljuk, Standard_D2_v3. 
- Adja meg a virtuálisgép-méretezési csoporthoz a kívánt **windowsOSVersion** verziót. Javasoljuk, hogy 2016-Datacenter. 
- Nevezze el a virtuálisgép méretezési csoport erőforrást kell telepíteni egy **vmssName** tulajdonság használatával. Ilyen például a **VMSS-WAD-TEST**.    
- Adja meg a virtuális gép méretezési csoportban futtatni kívánt virtuális gépek számát a **instanceCount** tulajdonság használatával.
- Adja meg az **adminUsername** és **adminPassword** értékeket a virtuálisgép-méretezési csoporthoz. Ezek a paraméterek a méretezési készletben lévő virtuális gépek távoli eléréséhez használatosak. Annak elkerülése érdekében, hogy a virtuális gép eltérített, **ne** használja a sablonban szereplő is. A botok a nyilvános GitHub-adattárakban keresik a felhasználóneveket és jelszavakat az interneten. Valószínű, hogy ezekkel az alapértelmezett beállításokkal tesztelik a virtuális gépeket. 


###  <a name="modify-azuredeployjson"></a>Azuredeploy.json módosítása
Nyissa meg az **azuredeploy.json** fájlt. 

Adjon hozzá egy változót a tárfiók adatainak tárolásához az Erőforrás-kezelő sablonban. A diagnosztikai konfigurációs fájlban megadott naplók vagy teljesítményszámlálók az Azure Monitor metrikatárolójába és az itt megadott tárfiókba is bekerülnek: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Keresse meg a virtuálisgép méretezési csoport definícióját az erőforrások szakaszban, és adja hozzá az **identitásszakaszt** a konfigurációhoz. Ez a kiegészítés biztosítja, hogy az Azure hozzárendeli a rendszeridentitást. Ez a lépés azt is biztosítja, hogy a méretezési készletben lévő virtuális gépek vendégmetrikákat bocsáthassanak ki magukról az Azure Monitorra:  

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

A virtuális gép méretezési készlet erőforrás, keresse meg a **virtualMachineProfile** szakaszban. Adjon hozzá egy új profilt, a **the extensionsProfile** nevű profilt a bővítmények kezeléséhez.  


Az **extensionProfile alkalmazásban**adjon hozzá egy új bővítményt a sablonhoz a **VMSS-WAD-bővítmény** szakaszban látható módon.  Ez a szakasz az Azure-erőforrások bővítmény felügyelt identitások, amely biztosítja, hogy a kibocsátott metrikák at az Azure Monitor elfogadja. A **névmező** bármilyen nevet tartalmazhat. 

Az MSI-bővítmény következő kódja is hozzáadja a diagnosztikai bővítményt és a konfigurációt bővítményerőforrásként a virtuálisgép méretezési csoport erőforrásához. Szükség esetén nyugodtan hozzáadhat vagy eltávolíthat teljesítményszámlálókat: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
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


Adjon hozzá egy **dependsOn-t** a tárfiókhoz, hogy megbizonyosodjon arról, hogy a megfelelő sorrendben jön létre: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Hozzon létre egy tárfiókot, ha még nincs létrehozva a sablonban: 

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

Mindkét fájl mentése és bezárása. 

## <a name="deploy-the-resource-manager-template"></a>Az Erőforrás-kezelő sablon telepítése 

> [!NOTE]  
> Az Azure Diagnostics bővítmény 1.5-ös vagy újabb verzióját kell futtatnia, **és** az **autoUpgradeMinorVersion:** tulajdonság értéke **true** az Erőforrás-kezelő sablonban. Az Azure majd betölti a megfelelő bővítményt, amikor elindítja a virtuális gép. Ha nem rendelkezik ezekkel a beállításokkal a sablonban, módosítsa őket, és telepítse újra a sablont. 


Az Erőforrás-kezelő sablon üzembe helyezéséhez használja az Azure PowerShellt:  

1. Indítsa el a PowerShellt. 
1. Jelentkezzen be az `Login-AzAccount`Azure-ba a használatával.
1. Az előfizetések listájának `Get-AzSubscription`beszerezhető a használatával.
1. Állítsa be a létrehozni kívánt előfizetést, vagy frissítse a virtuális gépet: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Hozzon létre egy új erőforráscsoportot a virtuális gép üzembe helyezése. Futtassa az alábbi parancsot: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Ne felejtsen el olyan Azure-régiót használni, amely egyéni metrikákhoz engedélyezett. Ne felejtsen el [olyan Azure-régiót használni, amely egyéni metrikákhoz engedélyezett.](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions)
 
1. Futtassa a következő parancsokat a virtuális gép telepítéséhez:  

   > [!NOTE]  
   > Ha meglévő méretezési készletet szeretne frissíteni, adja hozzá a **-Mode Incremental** parancsot a parancs végéhez. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Miután a központi telepítés sikeres, meg kell találnia a virtuális gép méretezési készlet az Azure Portalon. Metrikákat kell kibocsátanak az Azure Monitor. 

   > [!NOTE]  
   > Előfordulhat, hogy hibákba ütközik a kiválasztott **vmSkuSize**körül. Ebben az esetben lépjen vissza az **azuredeploy.json** fájlba, és frissítse a **vmSkuSize** paraméter alapértelmezett értékét. Javasoljuk, hogy próbálkozzon **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>A mutatók feltérképezése 

1. Jelentkezzen be az Azure portálra. 

1. A bal oldali menüben válassza a **Monitor**lehetőséget. 

1. A **Figyelő** lapon válassza a **Metrikák**lehetőséget. 

   ![Figyelő - Metrikák lap](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Módosítsa az összesítési időszakot **utolsó 30 percre.**  

1. Az erőforrás legördülő menüjében válassza ki a létrehozott virtuálisgép-méretezési készletet.  

1. A névterek legördülő menüben válassza az **azure.vm.windows.guest**lehetőséget. 

1. A metrikák legördülő menüben válassza a **Használatban lévő\%Memóriavéglegesített bájtok lehetőséget.**  

Ezután azt is választhatja, hogy használja a dimenziók ebben a metrikában, hogy egy adott virtuális gép, vagy a méretezési készlet egyes virtuális gépek ábrázolására. 



## <a name="next-steps"></a>További lépések
- További információ az [egyéni mutatókról.](metrics-custom-overview.md)


