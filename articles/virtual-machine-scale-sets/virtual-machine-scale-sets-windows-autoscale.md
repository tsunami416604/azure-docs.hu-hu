---
title: "Windows automatikus skálázási virtuálisgép-skálázási készletekben |} Microsoft Docs"
description: "Egy Windows virtuálisgép-méretezési csoportban az Azure PowerShell automatikus skálázás beállítása"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
ms.openlocfilehash: 91f731bec46c005221f4e66e95822994070d4c26
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="automatically-scale-machines-in-a-virtual-machine-scale-set"></a>Automatikus méretezése a gépet egy virtuálisgép-méretezési csoportban lévő
Virtuálisgép-méretezési csoportok könnyebben akkor helyezheti üzembe és felügyelheti az azonos virtuális gépek készletként. Méretezési csoportok hyperscale alkalmazások jól méretezhető és testre szabható számítási rétegét adja meg, és a Windows platform-lemezképek, Linux platformon képek, egyéni lemezképek és bővítmények támogatják. Méretezési csoportok kapcsolatos további információkért lásd: [virtuálisgép-skálázási készletekben](virtual-machine-scale-sets-overview.md).

Ez az oktatóanyag bemutatja, hogyan hozzon létre egy Windows virtuális gépek méretezési és automatikus méretezése a gépek készletében. A skála, állíthatók be skálázása az Azure Resource Manager-sablon létrehozása és telepítése az Azure PowerShell használatával hoz létre. A sablonokról további információkat az [Authoring Azure Resource Manager templates](../azure-resource-manager/resource-group-authoring-templates.md) (Azure Resource Manager-sablonok készítése) című témakörben talál. Méretezési készlet automatikus skálázás kapcsolatos további információkért lásd: [automatikus skálázás és virtuálisgép-skálázási készletekben](virtual-machine-scale-sets-autoscale-overview.md).

Ez a cikk telepít, a következő erőforrások és bővítmények:

* Microsoft.Storage/storageAccounts
* Microsoft.Network/virtualNetworks
* Microsoft.Network/publicIPAddresses
* Microsoft.Network/loadBalancers
* Microsoft.Network/networkInterfaces
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.Insights.VMDiagnosticsSettings
* Microsoft.Insights/autoscaleSettings

Erőforrás-kezelő erőforrásokra vonatkozó további információkért lásd: [Azure Resource Manager és klasszikus üzembe helyezési](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="step-1-install-azure-powershell"></a>1. lépés: Az Azure PowerShell telepítése
Lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) Azure PowerShell legfrissebb verziójának telepítésével kapcsolatos további információkért válassza ki az előfizetését, majd Azure próbál bejelentkezni.

## <a name="step-2-create-a-resource-group-and-a-storage-account"></a>2. lépés: Az erőforráscsoportot és a storage-fiók létrehozása
1. **Hozzon létre egy erőforráscsoportot** – összes erőforrás telepíteni kell egy erőforráscsoportot. Használjon [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx) nevű erőforráscsoport létrehozásához **vmsstestrg1**.
2. **Hozzon létre egy tárfiókot** – Ez a tárfiók, a sablon tárolására. Használjon [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) nevű tárfiók létrehozásához **vmsstestsa**.

## <a name="step-3-create-the-template"></a>3. lépés: A sablon létrehozása
Az Azure Resource Manager-sablon általi telepítéséhez és az Azure-erőforrások együtt kezelheti az erőforrásokat és a társított telepítési paraméterek JSON-leírásuk lehetővé teszi.

1. A kedvenc szerkesztőben C:\VMSSTemplate.json létrehozásához, és adja hozzá a sablon támogatásához a kezdeti JSON struktúrában.

    ```json
    {
      "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      },
      "variables": {
      },
      "resources": [
      ]
    }
    ```

2. Paraméterek nem mindig szükséges, de azok nyújtanak olyan értéket adjon meg a sablon telepítésekor. Adja hozzá ezeket a paramétereket a paraméterek szülőelem hozzáadott, a sablon alapján.

    ```json   
    "vmName": { "type": "string" },
    "vmSSName": { "type": "string" },
    "instanceCount": { "type": "string" },
    "adminUsername": { "type": "string" },
    "adminPassword": { "type": "securestring" },
    "resourcePrefix": { "type": "string" }
    ```
   
    * Állítsa be a külön, a méretezési gépek eléréséhez használt virtuális gép nevét.
    * A tárfiók, amelyen megtalálható a sablon neve.
    * Először hozzon létre a méretezési csoportban lévő virtuális gépek száma.
    * A nevet és jelszót a rendszergazdai fiók, a virtuális gépeken.
    * Az erőforrások, a méretezési támogatásához létrehozott előtagja.

3. Változók segítségével sablonban adja meg az értékeket, amelyeket gyakran változhat vagy értékeket, amelyeket a paraméterértékek kombinációjából kell létrehozni. Adja hozzá ezeket a változókat a változók szülőelem hozzáadott, a sablon alapján.

    ```json
    "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
    "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
    "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
    "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
    "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
    "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
    "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
    "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
      "wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" /></WindowsEventLog>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor(_Total)\\% Processor Time\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU utilization\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
    ```
   
    * A hálózati adapterek által használt DNS-neveket.

        * Az IP-címet nevének és a virtuális hálózati és-alhálózatok előtagokat.
        * A nevek és a virtuális hálózat azonosítók terheléselosztó és a hálózati adapterek betöltése.
        * Állítsa be a fiókok a skála gépén társított tárfiókok neve.
        * A diagnosztika bővítményt a virtuális gépeken telepített beállításait. A diagnosztika bővítmény kapcsolatos további információkért lásd: [Windows virtuális gép létrehozása a figyelési és -diagnosztika Azure Resource Manager-sablon](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

4. Adja hozzá a tárolási fiók erőforrások az erőforrások szülőelem hozzáadott, a sablon alapján. Ez a sablon használatával hurkot ajánlott öt tárfiókok létrehozása az operációs rendszer lemezek és diagnosztikai adatok tárolására. E fiókok csoportja, amelyek a jelenlegi maximális méretezési csoportban lévő támogathatja a legfeljebb 100 virtuális gépet. Minden tárfiók neve az, hogy a sablon a paraméterek meg előtaggal együtt változók definiált levél jelzésével.

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
      "apiVersion": "2015-06-15",
      "copy": {
        "name": "storageLoop",
        "count": 5
      },
      "location": "[resourceGroup().location]",
      "properties": { "accountType": "Standard_LRS" }
    },
    ```

5. Adja hozzá a virtuális hálózati erőforráshoz. További információkért lásd: [hálózati erőforrás-szolgáltató](../virtual-network/resource-groups-networking.md).

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
        "subnets": [
          {
            "name": "subnet1",
            "properties": { "addressPrefix": "10.0.0.0/24" }
          }
        ]
      }
    },
    ```

6. Adja hozzá a nyilvános IP-cím erőforrás a terheléselosztó és a hálózati adapter által használt.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP1')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName1')]"
        }
      }
    },
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP2')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName2')]"
        }
      }
    },
    ```

7. Adja hozzá a terheléselosztó erőforrást a méretezési készlet által használt. További információkért lásd: [Azure Resource Manager támogatása a terheléselosztó](../load-balancer/load-balancer-arm.md).

    ```json   
    {
      "apiVersion": "2015-06-15",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
              }
            }
          }
        ],
        "backendAddressPools": [ { "name": "bepool1" } ],
        "inboundNatPools": [
          {
            "name": "natpool1",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPortRangeStart": 50000,
              "frontendPortRangeEnd": 50500,
              "backendPort": 3389
            }
          }
        ]
      }
    },
    ```

8. Vegye fel a különálló virtuális gép által használt hálózati illesztő erőforrás. Méretezési csoportban lévő gépek nem nyilvános IP-címnek keresztül érhető el, mert egy különálló virtuális gép jön létre az azonos virtuális hálózatban való távoli hozzáférés a gépek.

    ```json  
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
              },
              "subnet": {
                "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
              }
            }
          }
        ]
      }
    },
    ```

9. Vegye fel a különálló virtuális gép ugyanazon a hálózaton, a méretezési készlet.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": { "vmSize": "Standard_A1" },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "[concat(parameters('resourcePrefix'), 'os1')]",
            "vhd": {
              "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"        
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    ```

10. A virtuálisgép-méretezési csoport erőforrás hozzáadása, és adja meg a diagnosztika-bővítménnyel, a méretezési csoportban lévő összes virtuális gép telepítve van. Az erőforrás beállításainak jelentős része hasonlóak a virtuálisgép-erőforrás. A fő különbség a kapacitás elem, amely meghatározza a virtuális gépek számát a méretezési és upgradePolicy, amely meghatározza, hogyan végrehajtott frissítések virtuális gépek. A méretezési készlet nem jön létre, amíg a storage-fiókok létrehozásához szükségesek a dependsOn elemmel megadottak szerint.

    ```json
    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "apiVersion": "2016-03-30",
      "name": "[parameters('vmSSName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "sku": {
        "name": "Standard_A1",
        "tier": "Standard",
        "capacity": "[parameters('instanceCount')]"
      },
      "properties": {
        "upgradePolicy": {
          "mode": "Manual"
        },
        "virtualMachineProfile": {
          "storageProfile": {
            "osDisk": {
              "vhdContainers": [
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3], '.blob.core.windows.net/vhds')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4], '.blob.core.windows.net/vhds')]"
              ],
              "name": "vmssosdisk",
              "caching": "ReadOnly",
              "createOption": "FromImage"
            },
            "imageReference": {
              "publisher": "MicrosoftWindowsServer",
              "offer": "WindowsServer",
              "sku": "2012-R2-Datacenter",
              "version": "latest"
            }
          },
          "osProfile": {
            "computerNamePrefix": "[parameters('vmSSName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "networkconfig1",
                "properties": {
                  "primary": "true",
                  "ipConfigurations": [
                    {
                      "name": "ip1",
                      "properties": {
                        "subnet": {
                          "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                          }
                        ]
                      }
                    }
                  ]
                }
              }
            ]
          },
          "extensionProfile": {
            "extensions": [
              {
                "name": "Microsoft.Insights.VMDiagnosticsSettings",
                "properties": {
                  "publisher": "Microsoft.Azure.Diagnostics",
                  "type": "IaaSDiagnostics",
                  "typeHandlerVersion": "1.5",
                  "autoUpgradeMinorVersion": true,
                  "settings": {
                    "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                    "storageAccount": "[variables('diagnosticsStorageAccountName')]"
                  },
                  "protectedSettings": {
                    "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
                    "storageAccountKey": "[listkeys(variables('accountid'), '2015-06-15').key1]",
                    "storageAccountEndPoint": "https://core.windows.net"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

11. Vegyen fel a autoscaleSettings erőforrást, amely meghatározza, hogyan a méretezési beállítja a processzor kihasználtsága a méretezési csoportban lévő gépeken alapján.

    ```json
    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Processor(_Total)\\% Processor Time",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 50.0
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      }
    }
    ```

    Ebben az oktatóanyagban fontosak a ezeket az értékeket:
    
    * **metricName**  
    Ez az érték megegyezik a wadperfcounter változóban meghatározott teljesítményszámláló. Használja ezt a változót, a diagnosztika bővítmény gyűjti a **processzor(_Total)\% processzoridő** számláló.
    
    * **metricResourceUri**  
    Ezt az értéket az erőforrás-azonosítót a virtuálisgép-méretezési csoport.
    
    * **időkeretben vannak**  
    Ez az érték a metrikák összegyűjtött granularitása. Ez a sablon az érték egy perc alatt.
    
    * **statisztika**  
    Ez az érték határozza meg, hogyan a metrikák egyesíti a rendszer megfeleljen az automatikus skálázási művelet. A lehetséges értékek: átlagos, Min, Max. A sablonban a virtuális gépek átlagos teljes processzorhasználatot gyűjti.
    
    * **Az időtartomány értékének**  
    Ez az érték, amely a, amelyben példány adatgyűjtés. 5 perc és 12 óra között kell lennie.
    
    * **timeAggregation**  
    az érték határozza meg, hogyan kell-e a gyűjtött adatok kombinált adott idő alatt. Alapértelmezett érték átlaga. A lehetséges értékek: átlagos, Minimum, Maximum, utolsó, összesen, száma.
    
    * **operátor**  
    Az értéke az operátort, amelynek segítségével összehasonlíthatja a metrikaadatokat és a küszöbértéket. A lehetséges értékek: egyenlő NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    
    * **küszöbérték**  
    Ezt az értéket az érték, amely elindítja a műveletet. Ez a sablon a gépeket adnak a méretezési készletben, ha a készlet gépek között az átlagos CPU-használat több mint 50 %.
    
    * **iránya**  
    Ez az érték határozza meg, amikor érhető el, a küszöbérték végrehajtandó műveletet. A lehetséges értékek a következők: növekedése vagy csökkenése. A sablonban a méretezési csoportban lévő virtuális gépek száma nő, amikor több mint 50 %-a meghatározott idő ablakban a küszöbértéket.
    
    * **típusa**  
    Ez az érték műveletet kell végrehajtani, és ChangeCount értékre kell állítani.
    
    * **érték**  
    Ez az érték hozzáadásakor vagy eltávolításakor a méretezési csoport a virtuális gépek száma. Ez az érték 1 vagy nagyobb lehet. Az alapértelmezett értéke 1. Ez a sablon a méretezési gépek számát be növekszik 1 amikor teljesül a küszöbértéket.
    
    * **cooldown**  
    Ez az érték a szükséges időt a legutóbbi méretezési művelet óta várni, mielőtt a következő művelet történik. Ez az érték egy percet, és egy hét között kell lennie.

12. A sablonfájl mentési.    

## <a name="step-4-upload-the-template-to-storage"></a>4. lépés: Töltse fel a sablon tárolási
A sablon mindaddig, amíg nevét és az 1. lépésben létrehozott tárfiók elsődleges kulcs ismeri fel kell tölteni.

1. A Microsoft Azure PowerShell-ablakot állítson be egy változó, amely megadja az 1. lépésben létrehozott storage-fiók nevét.
   
    ```powershell
    $storageAccountName = "vmstestsa"
    ```

2. Egy változó, amely meghatározza a tárfiók elsődleges kulcs beállítva.
   
    ```powershell
    $storageAccountKey = "<primary-account-key>"
    ```
   
   Ezt a kulcsot az Azure-portálon a tárolási fiók erőforrás megtekintésekor a kulcs ikonra kattintva kaphat.
3. A fiók helyi tárolóobjektum létrehozásához, amely ellenőrzi a storage-fiók műveleteket.
   
    ```powershell
    $ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    ```

4. Az a sablon tárolására alkalmas a tároló létrehozása.

    ```powershell
    $containerName = "templates"
    New-AzureStorageContainer -Name $containerName -Context $ctx  -Permission Blob
    ```

5. A sablon fájlt feltölteni az új tárolóhoz.

    ```powershell   
    $blobName = "VMSSTemplate.json"
    $fileName = "C:\" + $BlobName
    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob  $blobName -Context $ctx
    ```

## <a name="step-5-deploy-the-template"></a>5. lépés: A sablon üzembe helyezése
Most, hogy létrehozta a sablont, indítsa el az erőforrásokat üzembe helyezi. A folyamat elindításához használt a parancs:

```powershell
New-AzureRmResourceGroupDeployment -Name "vmsstestdp1" -ResourceGroupName "vmsstestrg1" -TemplateUri "https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json"
```

Amikor lenyomja az adja meg, adjon meg értékeket a változók hozzárendelt kéri. Adja meg ezeket az értékeket:

```powershell
vmName: vmsstestvm1
  vmSSName: vmsstest1
  instanceCount: 5
  adminUserName: vmadmin1
  adminPassword: VMpass1
  resourcePrefix: vmsstest
```

Gondolja, hogy a szolgáltatás sikeresen telepíthető az összes erőforrás mintegy 15 percre leáll.

> [!NOTE]
> A portál képességét segítségével is telepítheti az erőforrásokat. A hivatkozás: "https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>"
> 
> 

## <a name="step-6-monitor-resources"></a>6. lépés: A figyelő erőforrások
Virtuálisgép-méretezési csoportok ezekkel a módszerekkel kapcsolatos információkat kaphat:

* Az Azure portál – jelenleg kaphat a korlátozott mennyiségű információt a portál használatával.
* A [Azure erőforrás-kezelő](https://resources.azure.com/) -Ez az eszköz a legjobb felderítését lehetővé tevő a méretezési csoport jelenlegi állapotában. Hajtsa végre ezt az elérési utat, és a létrehozott méretezési csoport példányait tartalmazó nézetet kell megjelennie:
  
    előfizetések > {az előfizetés} > resourceGroups > vmsstestrg1 > szolgáltatók > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtuális gépek vannak

* Az Azure PowerShell - használja ezt a parancsot néhány adatra:

  ```powershell
  Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  ```
  
  Vagy
  
  ```powershell
  Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView
  ```

* A különálló virtuális gép csatlakozni, ugyanúgy, mint bármely más gép üzembe helyezése, és ezután távolról elérheti a virtuális gépek használata a méretezési készletben az egyes folyamatok figyelése.

> [!NOTE]
> A teljes REST API-t a méretezési készlet vonatkozó adatok találhatók [virtuálisgép-skálázási készletekben](https://msdn.microsoft.com/library/mt589023.aspx)

## <a name="step-7-remove-the-resources"></a>7. lépés: Távolítsa el az erőforrásokat
Mivel az Azure-ban használt erőforrásokhoz van szó, ajánlott mindig egy törli az erőforrást, amely már nem szükséges. Nem kell törölnie az egyes erőforrások külön-külön egy erőforráscsoportból. Az erőforráscsoport törlése, és az erőforrások automatikusan törlődnek.

  ```powershell
  Remove-AzureRmResourceGroup -Name vmsstestrg1
  ```

Ha meg szeretné tartani az erőforráscsoport, törölheti a méretezési készletben csak.

  ```powershell
  Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name"
  ```

## <a name="next-steps"></a>Következő lépések
* Az imént létrehozott található információk segítségével méretezési kezelése [egy virtuálisgép-méretezési csoportban lévő virtuális gépeket](virtual-machine-scale-sets-windows-manage.md).
* További információ a vertikális skálázásáról: [Vertikális automatikus méretezés a virtuálisgép-méretezési csoportokkal](virtual-machine-scale-sets-vertical-scale-reprovision.md)
* Példák figyelési szolgáltatásokat az Azure-figyelő található [Azure figyelő PowerShell quick start minták](../monitoring-and-diagnostics/insights-powershell-samples.md)
* További tudnivalók az értesítési szolgáltatások [automatikus skálázási műveletek segítségével e-mailek és a webhook riasztási értesítések küldése az Azure-figyelő](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
* Megtudhatja, hogyan [használata naplókat küldeni e-mailek és a webhook riasztási értesítések az Azure-figyelő](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)

