---
title: Az Azure Resource Manager-sablonokban a virtuális gépek |} A Microsoft Azure
description: További tudnivalók hogyan Azure Resource Manager-sablonokban a virtuális gép erőforrás van definiálva.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: davidmu
ms.openlocfilehash: 43cd6322bb03b5c781a890c3280247cbb2d118f6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonokban a virtuális gépek

Ez a cikk ismerteti az Azure Resource Manager-sablonokban a virtuális gép aspektusait. Ez a cikk nem alkalmazható a teljes sablont hoz létre virtuális gépet; az adott erőforrás-definíciókban storage-fiókok, a hálózati adapterek, a nyilvános IP-címek és a virtuális hálózatok kell. Hogyan ezekkel az erőforrásokkal együtt definiálható kapcsolatos további információkért tekintse meg a [Resource Manager sablonokhoz](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Nincsenek a sok [a tárban lévő sablonok](https://azure.microsoft.com/documentation/templates/?term=VM) , amelyek tartalmazzák a virtuális gép erőforrásához. Nem minden sablonként szereplő összetevőit itt.

Ez a példa bemutatja a jellemző erőforrás szakaszában megadott számú virtuális gépek létrehozására szolgáló sablont:

```json
"resources": [
  { 
    "apiVersion": "2016-04-30-preview", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[concat('myVM', copyindex())]", 
    "location": "[resourceGroup().location]",
    "copy": {
      "name": "virtualMachineLoop", 
      "count": "[parameters('numberOfInstances')]"
    },
    "dependsOn": [
      "[concat('Microsoft.Network/networkInterfaces/myNIC', copyindex())]" 
    ], 
    "properties": { 
      "hardwareProfile": { 
        "vmSize": "Standard_DS1" 
      }, 
      "osProfile": { 
        "computername": "[concat('myVM', copyindex())]", 
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
          "name": "[concat('myOSDisk', copyindex())]",
          "caching": "ReadWrite", 
          "createOption": "FromImage" 
        },
        "dataDisks": [
          {
            "name": "[concat('myDataDisk', copyindex())]",
            "diskSizeGB": "100",
            "lun": 0,
            "createOption": "Empty"
          }
        ] 
      }, 
      "networkProfile": { 
        "networkInterfaces": [ 
          { 
            "id": "[resourceId('Microsoft.Network/networkInterfaces',
              concat('myNIC', copyindex()))]" 
          } 
        ] 
      },
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('https://', variables('storageName'), '.blob.core.windows.net')]"
        }
      } 
    },
    "resources": [ 
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
      {
        "name": "MyCustomScriptExtension",
        "type": "extensions",
        "apiVersion": "2016-03-30",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
        ],
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.7",
          "autoUpgradeMinorVersion": true,
          "settings": {
            "fileUris": [
              "[concat('https://', variables('storageName'),
                '.blob.core.windows.net/customscripts/start.ps1')]" 
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
          }
        }
      } 
    ]
  } 
]
``` 

> [!NOTE] 
>Ez a példa egy korábban létrehozott tárfiókot támaszkodik. Ehhez a sablon alapján létrehozhatja a tárfiók. A példa is egy hálózati adapter és a tőle függő erőforrások, akkor lehet a sablonban definiált támaszkodik. A példa nem mutatja be ezeket az erőforrásokat.
>
>

## <a name="api-version"></a>API-verzió

Sablon használatával erőforrások telepítésekor akkor adja meg a használandó API verzióját. A példa bemutatja a virtuálisgép-erőforrás a apiVersion elem használatával:

```
"apiVersion": "2016-04-30-preview",
```

Az API-t ad meg a sablon a verziószáma mely tulajdonságok meghatározhatja a sablonban. A legújabb API verziót általában ki kell választania sablonok létrehozásakor. A meglévő sablonok eldöntheti, hogy továbbra is az korábbi API-verziót használja, vagy a sablont az új szolgáltatások előnyeinek legújabb verziójának frissítése.

Ezek a lehetőségek lekérhesse a legújabb API-verziók használata:

- REST API - [összes erőforrás-szolgáltatók felsorolása](https://docs.microsoft.com/rest/api/resources/providers#Providers_List)
- PowerShell - [Get-AzureRmResourceProvider](/powershell/module/azurerm.resources/get-azurermresourceprovider)
- Az Azure CLI 2.0 - [az szolgáltató megjelenítése](https://docs.microsoft.com/cli/azure/provider#az_provider_show)

## <a name="parameters-and-variables"></a>Paraméterek és változók

[Paraméterek](../../resource-group-authoring-templates.md) megkönnyíti, hogy a sablon értékeket megadni, ha. A Paraméterek szakaszban szerepel a példában:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

A példa sablon telepítésekor adhatja értékek a nevet és jelszót a rendszergazdai fiók létrehozása minden virtuális gép és a virtuális gépek számát. Lehetősége van a paraméterértékek meghatározásáról sablonnal felügyelt külön fájlban, vagy megadása az értékek, amikor a rendszer kéri.

[Változók](../../resource-group-authoring-templates.md) könnyen használt ismételten egész, vagy idővel megváltozhat, hogy a sablonban szereplő értékek beállítása. A változók szakaszban szerepel a példában:

```
"variables": { 
  "storageName": "mystore1",
  "accountid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name,
  '/providers/','Microsoft.Storage/storageAccounts/', variables('storageName'))]", 
  "wadlogs": "<WadCfg> 
    <DiagnosticMonitorConfiguration overallQuotaInMB=\"4096\" xmlns=\"http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration\"> 
      <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter=\"Error\"/> 
      <WindowsEventLog scheduledTransferPeriod=\"PT1M\" > 
        <DataSource name=\"Application!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"Security!*[System[(Level = 1 or Level = 2)]]\" /> 
        <DataSource name=\"System!*[System[(Level = 1 or Level = 2)]]\" />
      </WindowsEventLog>", 
  "wadperfcounters": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\">
      <PerformanceCounterConfiguration counterSpecifier=\"\\Process(_Total)\\Thread Count\" sampleRate=\"PT15S\" unit=\"Count\">
        <annotation displayName=\"Threads\" locale=\"en-us\"/>
      </PerformanceCounterConfiguration>
    </PerformanceCounters>", 
  "wadcfgxstart": "[concat(variables('wadlogs'), variables('wadperfcounters'), 
    '<Metrics resourceId=\"')]", 
  "wadmetricsresourceid": "[concat('/subscriptions/', subscription().subscriptionId, 
    '/resourceGroups/', resourceGroup().name , 
    '/providers/', 'Microsoft.Compute/virtualMachines/')]", 
  "wadcfgxend": "\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/>
    <MetricAggregation scheduledTransferPeriod=\"PT1M\"/>
    </Metrics></DiagnosticMonitorConfiguration>
    </WadCfg>"
}, 
```

A példa sablon telepítésekor változó a neve és azonosítója a korábban létrehozott tárfiókot használ. Adja meg a beállításokat a diagnosztikai bővítmény változók is használhatók. Használja a [ajánlott eljárások Azure Resource Manager-sablonok létrehozásához](../../resource-manager-template-best-practices.md) segítségével eldöntheti, hogy a paraméterek és változók alkalmazása a sablon felépítésének módját.

## <a name="resource-loops"></a>Erőforrás hurkok

Ha egynél több virtuális gép van szükség az alkalmazás, egy sablon egy másolás elem is használhatja. A választható elem végighalad a virtuális gépek paraméterként megadott számát létrehozása:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

A példa is, láthatja, hogy ciklusindex használja, ha néhány értéket az erőforrás. Például három példányszám ad meg, ha az operációs rendszer lemezén a következők myOSDisk1, myOSDisk2, és myOSDisk3:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>A példa felügyelt lemezt a virtuális gépekhez.
>
>

Ne feledje, hogy egy erőforrás hurkot létrehozása a sablonban előfordulhat, hogy a hurok létrehozásakor, vagy más erőforrások eléréséhez használja. Például több virtuális gép nem használható hálózati adaptert, ha a sablon végighalad három virtuális gépek létrehozása azt kell is ismétlése három hálózati adapterek létrehozása. A hálózati adaptert egy virtuális géphez hozzárendelésekor ciklusindex alapján határozza meg azt:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Függőségek

A legtöbb erőforrást megfelelő működéséhez más erőforrások függenek. Virtuális gépek rendelve, a virtuális hálózat és a teendő, hogy kell-e a hálózati adaptert kell lennie. A [dependsOn](../../resource-group-define-dependencies.md) elem segítségével győződjön meg arról, hogy a virtuális gépek létrehozása előtt használható készen áll-e hálózati kapcsolat:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Erőforrás-kezelő párhuzamosan telepíti, amelyek nem függenek más erőforrás telepített erőforrásokat. Ügyeljen arra, hogy függőségek beállításakor, mert a szükségtelen függőségek megadásával véletlenül lelassíthatja a központi telepítés. Függőségek is láncolt több forrásanyagok segítségével. Például a hálózati illesztő a nyilvános IP-cím és a virtuális hálózati erőforrások függ.

Hogyan tudja, hogy szükség-e egy függőséget? Tekintse meg a sablonban beállított értéket. Ha a virtuális gép erőforrás definition mutat, amely ugyanazt a sablont telepítve van egy másik erőforrás eleme, egy függőségi kell. A Példa virtuális gép például egy hálózati profil határozza meg:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Ez a tulajdonság beállításához a hálózati illesztő léteznie kell. Ezért függőség van szüksége. Szükség függőség beállítása, amikor egy erőforrást (gyermek) egy másik erőforrás (szülő) van meghatározva. Például a diagnosztikai beállításokat és egyéni parancsfájl-kiterjesztés is definiálhatók a virtuális gép gyermek erőforrásként. Amíg nem létezik a virtuális gép nem hozhatók létre. Ezért mindkét erőforrás meg van jelölve, a virtuális gép függ.

## <a name="profiles"></a>Profilok

Több profil elemek használt virtuálisgép-erőforrás definiálásakor. Néhány szükséges és választható. Például a hardwareProfile, osProfile, storageProfile és networkProfile elemek szükségesek, de a diagnosticsProfile nem kötelező megadni. Ezeket a profilokat, mint beállításainak megadása:
   
- [size](sizes.md)
- [név](/architecture/best-practices/naming-conventions) és hitelesítő adatait.
- lemez és [operációsrendszer-beállításokat](cli-ps-findimage.md)
- [Hálózati adapter](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- Rendszerindítási diagnosztika

## <a name="disks-and-images"></a>A lemezek és lemezképek
   
Az Azure, a vhd-fájlok jelenthet [lemezek vagy képeket](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ha egy vhd-fájlt az operációs rendszernek kell lennie egy adott VM kifejezetten, azt nevezzük lemezt. Ha a vhd-fájlt az operációs rendszer általánosított sok virtuális gép létrehozásához használt, azt nevezzük lemezkép.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Hozzon létre új virtuális gépek és az új lemezt, a platformlemezkép

Amikor létrehoz egy virtuális Gépet, határozza meg, milyen operációs rendszer használatához. Az imageReference elem egy új virtuális gép operációs rendszerének azonosítására szolgál. A példa bemutatja, a következő definícióját: a Windows Server operációs rendszert:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Ha szeretne létrehozni a Linux operációs rendszert, ez a definíció használhatja:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Az operációsrendszer-lemez konfigurációs beállításait az osDisk elemhez rendelt. A példa egy új felügyelt lemezes meghatározása a gyorsítótár módban **ReadWrite** , és hogy a lemez létrehozása folyamatban van a egy [platformlemezképet](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Új virtuális gépek létrehozása a meglévő felügyelt lemezekből

Ha azt szeretné, a meglévő lemezt a virtuális gépek létrehozásához, távolítsa el az imageReference, és a osProfile elemek, és a következő lemez beállítások megadása:

```
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Hozzon létre új virtuális gépek egy felügyelt lemezképből

Ha egy felügyelt képre egy virtuális gép létrehozása, módosítsa az imageReference elem, és a következő lemez beállítások megadása:

```
"storageProfile": { 
  "imageReference": {
    "id": "[resourceId('Microsoft.Compute/images', 'myImage')]"
  },
  "osDisk": { 
    "name": "[concat('myOSDisk', copyindex())]",
    "osType": "Windows",
    "caching": "ReadWrite", 
    "createOption": "FromImage" 
  }
},
```

### <a name="attach-data-disks"></a>Adatlemez csatolása

Az adatlemezek opcionálisan a virtuális gépeket adhat hozzá. A [lemezek száma](sizes.md) használt operációsrendszer-lemez méretétől függ. A virtuális gépek Standard_DS1_v2 beállítása méretű a számukra fel adatlemezek maximális számának két. A példában egy felügyelt adatlemezt ad hozzá minden egyes virtuális gép:

```
"dataDisks": [
  {
    "name": "[concat('myDataDisk', copyindex())]",
    "diskSizeGB": "100",
    "lun": 0, 
    "caching": "ReadWrite",
    "createOption": "Empty"
  }
],
```

## <a name="extensions"></a>Bővítmények

Bár a [bővítmények](extensions-features.md) külön forrást, szorosan a virtuális gépek vannak társítva. Bővítmények gyermek erőforrásként a virtuális gép vagy egy külön erőforrásként lehet hozzáadni. A példa azt mutatja meg a [diagnosztika bővítmény](extensions-diagnostics-template.md) kíván hozzáadni a virtuális gépek:

```
{ 
  "name": "Microsoft.Insights.VMDiagnosticsSettings", 
  "type": "extensions", 
  "location": "[resourceGroup().location]", 
  "apiVersion": "2016-03-30", 
  "dependsOn": [ 
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
  ], 
  "properties": { 
    "publisher": "Microsoft.Azure.Diagnostics", 
    "type": "IaaSDiagnostics", 
    "typeHandlerVersion": "1.5", 
    "autoUpgradeMinorVersion": true, 
    "settings": { 
      "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
      variables('wadmetricsresourceid'), 
      concat('myVM', copyindex()),
      variables('wadcfgxend')))]", 
      "storageAccount": "[variables('storageName')]" 
    }, 
    "protectedSettings": { 
      "storageAccountName": "[variables('storageName')]", 
      "storageAccountKey": "[listkeys(variables('accountid'), 
        '2015-06-15').key1]", 
      "storageAccountEndPoint": "https://core.windows.net" 
    } 
  } 
},
```

A bővítmény erőforrás storageName változó és a diagnosztikai változók használatával adjon meg értékeket. Ha azt szeretné, a bővítmény által gyűjtött adatokat, adhat hozzá további teljesítményszámlálók a wadperfcounters változó. Sikerült meg szeretne adni a diagnosztika adatokat során eltérő tárfiók, mint a Virtuálisgép-lemezek tárolására.

Sok kiterjesztések, a virtuális gép telepíthető, de a leghasznosabb valószínűleg a [egyéni parancsprogramok futtatására szolgáló bővítmény](extensions-customscript.md). A példában egy start.ps1 nevű PowerShell-parancsfájl futó minden virtuális gép első indításakor:

```
{
  "name": "MyCustomScriptExtension",
  "type": "extensions",
  "apiVersion": "2016-03-30",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]"
  ],
  "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.7",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "[concat('https://', variables('storageName'),
          '.blob.core.windows.net/customscripts/start.ps1')]" 
      ],
      "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
    }
  }
}
```

A start.ps1 parancsfájl számos konfigurációs feladat érhető el. A példában a virtuális gépek által hozzáadott adatlemezek például nincs inicializálva; Egyéni parancsfájl segítségével azokat. Ha több indítási feladatok elvégzéséhez, a start.ps1 fájl segítségével más PowerShell-parancsfájlok az Azure storage-hívás. A példában PowerShell, de bármely a parancsfájl-kezelési módszer, amely az Ön által használt operációs rendszeren érhető el.

A telepített bővítmények bővítmények beállításai közül a portálon állapota látható:

![Bővítmény állapotának beolvasása](./media/template-description/virtual-machines-show-extensions.png)

Is kaphat a sémakiterjesztési adatok használatával a **Get-AzureRmVMExtension** PowerShell-parancsot a **virtuálisgép-bővítmény get** Azure CLI 2.0 parancsot, vagy a **sémakiterjesztésiadatokbeolvasása** REST API-T.

## <a name="deployments"></a>Központi telepítés

Amikor telepít egy sablont, az Azure erőforrások csoportként telepíteni, és automatikusan hozzárendel egy nevet a központilag telepített csoportban követi nyomon. A központi telepítés neve megegyezik a sablon nevét.

Ha a központi telepítésben lévő erőforrások állapotával fejezetét, használhatja az erőforráscsoport panelről az Azure-portálon:

![Telepítési információk](./media/template-description/virtual-machines-deployment-info.png)
    
Azt nem ugyanazt a sablont használni erőforrásokat létrehozni vagy frissíteni a meglévő erőforrásokat probléma. Parancsok használatával történő telepítése a sablonok, lehetősége van a tegyük fel például, amely [mód](../../resource-group-template-deploy.md) szeretne használni. A módot is megadni **Complete** vagy **növekményes**. Az alapértelmezett érték a növekményes frissítések végrehajtásához. Használata esetén ügyeljen a **Complete** módban, mert előfordulhat, hogy véletlenül törli az erőforrásokat. Ha a mód beállítása legyen **Complete**, erőforrás-kezelő törlése az erőforráscsoporthoz tartozik, amelyek nincsenek a sablonban lévő erőforrásokat.

## <a name="next-steps"></a>További lépések

- Hozzon létre egy saját sablon használatával [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md).
- A létrehozott sablon üzembe helyezése [Windows virtuális gép létrehozása egy Resource Manager sablonnal](ps-template.md).
- Megtudhatja, hogyan kezelheti a virtuális gépek által létrehozott [létrehozása és kezelése Windows virtuális gépek az Azure PowerShell modulra](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
