---
title: Virtuális gépek az Azure Resource Manager-sablonnal |} A Microsoft Azure
description: További információ a virtuális gép erőforrást hogyan van definiálva egy Azure Resource Manager-sablon.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: d234e7f8a6005722a33a797f2b8ae6a1e1f4b98b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327765"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuális gépek az Azure Resource Manager-sablon

Ez a cikk ismerteti az Azure Resource Manager-sablon, amely a virtuális gép aspektusait. Ez a cikk egy teljes sablont hoz létre virtuális gépet; nem ismerteti. szüksége lesz a storage-fiókok, hálózati adapterek, nyilvános IP-címek és virtuális hálózatok erőforrás-definíciókban. Hogyan ezen erőforrások együtt definiálható kapcsolatos további információkért lásd: a [Resource Manager sablonokhoz](../../azure-resource-manager/resource-manager-template-walkthrough.md).

Számos [a tárban lévő sablonok](https://azure.microsoft.com/documentation/templates/?term=VM) , amely tartalmazza a VM-erőforrás. Nem minden elemek a sablonban szereplő ebben a témakörben találhatók.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Ez a példa bemutatja egy sablon a megadott számú virtuális gépek létrehozásához egy jellemző erőforrás szakaszába:

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
>Ebben a példában a korábban létrehozott tárfiók támaszkodik. A storage-fiókot létrehozhatja a sablonból telepítésével. A példában is támaszkodik egy hálózati adapter és a tőle függő erőforrások, amely akkor lesz definiálva a sablonban. A példában nem jelennek meg ezekhez az erőforrásokhoz.
>
>

## <a name="api-version"></a>API-verzió

Amikor erőforrások sablon használatával helyez üzembe, akkor adja meg a használandó API-verzió. A példa bemutatja a virtuális gép típusú erőforrást az API-verzió elem használatával:

```
"apiVersion": "2016-04-30-preview",
```

A sablonban megadott API-verzió melyik tulajdonságokat adhatja meg a sablon van hatással. Általánosságban elmondható akkor válassza a legújabb API-verzió sablonok létrehozásakor. A meglévő sablonok eldöntheti, hogy folytatja a műveletet egy régebbi API-verzió használatával, vagy a sablont az új szolgáltatások előnyeinek kihasználása érdekében a legújabb verzióra frissíteni.

Ezek a lehetőségek lekérhesse a legújabb API-verziók használata:

- REST API - [összes erőforrás-szolgáltató listázása](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell - [Get-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- Az Azure CLI - [az provider show](https://docs.microsoft.com/cli/azure/provider)


## <a name="parameters-and-variables"></a>Paraméterek és változók

[Paraméterek](../../resource-group-authoring-templates.md) megkönnyítik a futtatásakor, adja meg a sablon értékeit. A példában a Paraméterek szakaszban használja:

```        
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

A példában a sablon telepítésekor ad meg értékeket a nevét és a rendszergazdai fiók jelszavát minden virtuális gép és virtuális gépek létrehozása. Lehetősége van egy különálló fájlban történik a sablon a paraméterértékek meghatározásáról vagy megjelenésekor értékek megadásával.

[Változók](../../resource-group-authoring-templates.md) megkönnyítik a használt ismételten során, vagy idővel változhat, hogy a sablonban értékek beállítását. A változók szakaszban szerepel a példában:

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

A példában a sablon telepítésekor változó nevét és azonosítóját a korábban létrehozott tárfiók szolgálnak. Változók adja meg a beállításokat a diagnosztikai bővítmény is használhatók. Használja a [ajánlott eljárások az Azure Resource Manager-sablonok létrehozására](../../resource-manager-template-best-practices.md) eldöntheti, hogyan szeretné struktúra paramétereket és változókat a sablonban.

## <a name="resource-loops"></a>Erőforrás-hurok

Ha egynél több virtuális gép van szüksége az alkalmazás, egy másolási eleme a sablonokban is használhatja. A választható elem végighalad a paraméterként megadott virtuális gépek létrehozása:

```
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

A példa azt is Észreveheti, hogy ciklusindex használatos, amikor az egyes értékek az erőforrás. Ha például három példányszámot adott meg, ha az operációsrendszer-lemezek nevei: myOSDisk1, myOSDisk2 és myOSDisk3:

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>Ebben a példában a virtuális gépek felügyelt lemezeket használ.
>
>

Ne feledje, hogy a sablon egy erőforráshoz egy hurok létrehozása előfordulhat, hogy a hurok létrehozása vagy egyéb erőforrásokhoz való hozzáférés során használja. Például több virtuális gép nem használhatja ugyanazon hálózati adapterre, így ha a sablon végighalad a három virtuális gép létrehozása, kell is hurkot három hálózati adapter létrehozása. Amikor egy hálózati adaptert rendel egy virtuális Gépet, ciklusindex segítségével azonosítható:

```
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Függőségek

A legtöbb erőforrást megfelelő működéséhez más erőforrások függenek. Virtuális gépek hozzá kell rendelni, és a virtuális hálózat és a egy hálózati adaptert kell tennie. A [dependsOn](../../resource-group-define-dependencies.md) elem segítségével ellenőrizze, hogy a hálózati adapter készen áll a használatra, mielőtt a virtuális gépek jönnek létre:

```
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Resource Manager párhuzamosan telepíti az olyan erőforrások, amelyek nem függ egy másik erőforrás üzembe helyezve. Ügyeljen arra, hogy függőségek beállításakor, mivel a szükségtelen függőségek meghatározásával véletlenül lelassíthatja a központi telepítés. Függőségek láncolhatja össze a több-erőforrások használatával. Ha például a hálózati adaptert a nyilvános IP-cím és a virtuális hálózati erőforrások függ.

Hogyan tudta, hogy ha egy függőség szükség? Tekintse meg a sablonban megadott értékeket. Ha a virtuális gép erőforrás definíció mutat egy másik erőforrás üzembe helyezett ugyanazt a sablont egy eleme, egy függőségi kell. A példában a virtuális gép például egy hálózati profil határozza meg:

```
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

Ez a tulajdonság beállítása, a hálózati adapter léteznie kell. Ezért egy függőség van szüksége. Szükség is beállíthat egy függőséget, ha egy erőforrás (gyermek) egy másik erőforrás (szülő) van meghatározva. Ha például a diagnosztikai beállításokat és egyéni parancsfájl-kiterjesztés mindkét erőforrásként meghatározott gyermek a virtuális gép. Ezek nem hozható létre, amíg a virtuális gép valóban létezik. Ezért mindkét erőforrások lesznek megjelölve, függő, a virtuális gépen.

## <a name="profiles"></a>Profilok

Profil több elemet egy virtuálisgép-erőforrás definiálásakor szolgálnak. Néhány szükséges és választható. Például a hardwareProfile, osProfile, storageProfile és networkProfile elemek szükségesek, de a diagnosticsProfile nem kötelező. Ezek a profilok például-beállítások megadása:
   
- [size](sizes.md)
- [név](/azure/architecture/best-practices/naming-conventions) és a hitelesítő adatok
- lemez és [operációs rendszer beállításai](cli-ps-findimage.md)
- [Hálózati adapter](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- A rendszerindítási diagnosztika

## <a name="disks-and-images"></a>Lemezek és lemezképek
   
Az Azure-ban, a vhd-fájlok jelenthetik [lemezek vagy lemezképek](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Az operációs rendszer vhd-fájlban kifejezetten egy adott virtuális Gépet is, ha azt nevezzük egy lemezt. Amikor az operációs rendszer vhd-fájlban általánosítva van használható több virtuális gépet létrehozni, azt nevezzük kép.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Létrehozhat új virtuális gépek és új lemezeket platformlemezkép

Amikor létrehoz egy virtuális Gépet, határozza meg, milyen operációs rendszert kívánja használni. Az imageReference elemet egy új virtuális gép operációs rendszerének meghatározására szolgál. A példa bemutatja egy Windows Server operációs rendszer definícióját:

```
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Ha szeretne létrehozni a Linux operációs rendszer, ez a definíció használhatja:

```
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Az operációsrendszer-lemez konfigurációs beállításait az osDisk elemmel vannak hozzárendelve. A példában egy új felügyelt lemez meghatározása a gyorsítótárazási módban **ReadWrite** , és hogy a lemez létrehozása folyamatban van a egy [platformlemezkép](cli-ps-findimage.md):

```
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Hozzon létre új virtuális gépek a meglévő felügyelt lemezekre

Ha azt szeretné, meglévő lemezekből hozhat létre virtuális gépeket, az imageReference és osProfile elemek eltávolítása és a következő lemez-beállítások megadása:

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

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Új virtuális gép létrehozása felügyelt rendszerképből

Ha szeretne egy virtuális gép létrehozása felügyelt rendszerképből, módosítsa az imageReference elem, és a következő lemez-beállítások megadása:

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

Igény szerint hozzáadhat adatlemezeket a virtuális gépekhez. A [lemezek száma](sizes.md) használt operációsrendszer-lemez méretétől függ. A virtuális gépek, standard_ds1_v2 méret beállítása méretével a hozzájuk hozzáadható adatlemezek maximális száma két. A példában egy felügyelt adatlemezre vezetünk be az egyes virtuális gépek:

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

Bár a [bővítmények](extensions-features.md) külön erőforrást, az szorosan virtuális gépekre van kötve. Bővítmények hozzáadható egy gyermek-erőforrás a virtuális gép, vagy külön erőforrást. A példa bemutatja a [diagnosztikai bővítmény](extensions-diagnostics-template.md) a virtuális gépeket ad hozzá:

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

A bővítmény erőforrás használ a storageName változót és a diagnosztikai változók értékeit. Ha azt szeretné, ez a bővítmény által összegyűjtött adatokat, további teljesítményszámlálók a wadperfcounters változót is hozzáadhat. Dönthet a diagnosztikai adatok elhelyezi egy másik tárfiókot, mint a Virtuálisgép-lemezek tárolására is.

Számos olyan bővítmény, amelyet telepíthet a virtuális gép, de a leghasznosabb valószínűleg a [egyéni szkriptek futtatására szolgáló bővítmény](extensions-customscript.md). A példában egy start.ps1 nevű PowerShell-parancsfájlt futtatja az egyes virtuális Gépeken első elindításakor:

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

A start.ps1 parancsfájl számos konfigurációs feladatokat végezheti el. Például az adatlemezeket, a példában a virtuális gépek hozzáadott nem inicializált; Egyéni parancsfájl használatával inicializálja őket. Ha több indítási feladatok elvégzésére, a start.ps1 fájl segítségével más PowerShell-szkriptek meghívása az Azure storage-ban. A példa Powershellt használ, de bármilyen a parancsfájl-kezelési módszer, amely az operációs rendszer használata esetén érhető el.

A bővítmények beállítások a portálon a telepített bővítmények állapotát tekintheti meg:

![Bővítmény állapotának beolvasása](./media/template-description/virtual-machines-show-extensions.png)

Emellett információkat szerezhet a bővítmény használatával a **Get-AzVMExtension** PowerShell-parancsot a **vm-bővítmény get** Azure CLI-paranccsal, vagy a **sémakiterjesztésiadatoklekérése**REST API-t.

## <a name="deployments"></a>Központi telepítés

Amikor telepít egy sablont, az Azure az erőforrásokat, hogy telepített egy csoportot, és automatikusan hozzárendel egy nevet a központilag telepített csoportban követi nyomon. Az üzemelő példány neve megegyezik a sablon nevét.

Ha Ön inspirációkkal a központi telepítésben lévő erőforrások állapotáról, tekintse meg az erőforráscsoport az Azure Portalon:

![Získat informace o nasazení](./media/template-description/virtual-machines-deployment-info.png)
    
Akkor sem, használja ugyanazt a sablont hozhat létre erőforrásokat, vagy frissíteni a meglévő erőforrások probléma. Sablonok üzembe helyezése a parancsok használatakor lehetősége van például, amely [mód](../../resource-group-template-deploy.md) is használni szeretné. A mód is megadni **Complete** vagy **növekményes**. Alapértelmezés szerint a rendszer hajtsa végre a növekményes frissítéseket. Legyen óvatos a használatakor a **Complete** módban, mert előfordulhat, hogy véletlenül töröl erőforrásokat. Ha a mód beállítása legyen **Complete**, Resource Manager törli az erőforráscsoportban lévő erőforrásokat, amelyek nem szerepelnek a sablonban.

## <a name="next-steps"></a>További lépések

- Hozzon létre saját sablont [Azure Resource Manager-sablonok készítése](../../resource-group-authoring-templates.md).
- Az eszközzel létrehozott sablon üzembe helyezése [Windows virtuális gép létrehozása Resource Manager-sablonnal](ps-template.md).
- A használatával létrehozott virtuális gépek kezelése [létrehozása és a Windows virtuális gépek kezelése az Azure PowerShell modullal](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- A JSON-szintaxist és a sablonok erőforrástípusok tulajdonságainak: [Azure Resource Manager sablonreferenciája](/azure/templates/).
