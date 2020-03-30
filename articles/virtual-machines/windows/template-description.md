---
title: Virtuális gépek egy Azure Resource Manager-sablonban | Microsoft Azure
description: További információ avirtuálisba, hogyan definiálható a virtuálisgép-erőforrás egy Azure Resource Manager-sablonban.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: f63ab5cc-45b8-43aa-a4e7-69dc42adbb99
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/03/2019
ms.author: cynthn
ms.openlocfilehash: c9bf1cf0564655c932e066e5b74225382375e9c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235422"
---
# <a name="virtual-machines-in-an-azure-resource-manager-template"></a>Virtuális gépek egy Azure Resource Manager-sablonban

Ez a cikk egy Azure Resource Manager-sablon virtuális gépekre vonatkozó szempontjait ismerteti. Ez a cikk nem ismerteti a virtuális gépek létrehozásához szükséges teljes sablont; ahhoz, hogy szükség van erőforrás-definíciók tárfiókok, hálózati adapterek, nyilvános IP-címek és a virtuális hálózatok. Az erőforrások együttes definiálásáról az [Erőforrás-kezelő sablon forgatókönyve című témakörben olvashat bővebben.](../../azure-resource-manager/resource-manager-template-walkthrough.md)

A katalógusban számos [sablon található,](https://azure.microsoft.com/documentation/templates/?term=VM) amelyek tartalmazzák a virtuális gép erőforrást. A sablonban nem minden elem szerepel, hanem az itt található.

 

Ez a példa egy sablon tipikus erőforrásszakaszát mutatja be a megadott számú virtuális gép létrehozásához:

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
>Ez a példa egy korábban létrehozott tárfiókra támaszkodik. Létrehozhatja a tárfiókot a sablonból történő üzembe helyezésével. A példa egy hálózati adapterre és a sablonban definiált függő erőforrásokra is támaszkodik. Ezek az erőforrások nem jelennek meg a példában.
>
>

## <a name="api-version"></a>API-verzió

Amikor egy sablon használatával telepíti az erőforrásokat, meg kell adnia az API egy verzióját. A példa a virtuálisgép-erőforrást mutatja az apiVersion elem használatával:

```json
"apiVersion": "2016-04-30-preview",
```

A sablonban megadott API-verzió befolyásolja, hogy a sablonban definiálható tulajdonságok at. Általánosságban elmondható, hogy a legújabb API-verziót kell kiválasztania a sablonok létrehozásakor. Meglévő sablonok esetén eldöntheti, hogy egy korábbi API-verziót szeretne-e használni, vagy frissíti a sablont a legújabb verzióhoz az új funkciók előnyeinek kihasználásához.

Használja ezeket a lehetőségeket a legújabb API-verziók beszerzése:

- REST API – [Az összes erőforrás-szolgáltató listázása](https://docs.microsoft.com/rest/api/resources/providers)
- PowerShell – [Get-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/get-azresourceprovider)
- Azure CLI - [az szolgáltató show](https://docs.microsoft.com/cli/azure/provider)


## <a name="parameters-and-variables"></a>Paraméterek és változók

[A paraméterek](../../resource-group-authoring-templates.md) megkönnyítik a sablon értékeinek megadását a sablon futtatásakor. Ez a paraméterszakasz a példa:

```json
"parameters": {
  "adminUsername": { "type": "string" },
  "adminPassword": { "type": "securestring" },
  "numberOfInstances": { "type": "int" }
},
```

A példasablon telepítésekor adja meg a rendszergazdai fiók nevét és jelszavát az egyes virtuális gépeken, valamint a létrehozandó virtuális gépek számát. Lehetősége van paraméterértékek megadására egy külön fájlban, amelyet a sablonnal kezelnek, vagy amikor a rendszer kéri, értékeket adjon meg.

[A változók megkönnyítik](../../resource-group-authoring-templates.md) a sablonban ismétlődően használt vagy idővel változható értékek beállítását. Ez a változószakasz a példa:

```json
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

A példasablon telepítésekor a rendszer változóértékeket használ a korábban létrehozott tárfiók nevéhez és azonosítójához. Változók is használják, hogy a beállításokat a diagnosztikai bővítmény. Az [Azure Resource Manager-sablonok létrehozásához ajánlott eljárások](../../resource-manager-template-best-practices.md) segítségével eldöntheti, hogyan szeretné strukturálni a paramétereket és a változókat a sablonban.

## <a name="resource-loops"></a>Erőforráshurkok

Ha egynél több virtuális gépre van szüksége az alkalmazáshoz, használhat egy másolási elemet egy sablonban. Ez a választható elem a paraméterként megadott virtuális gépek számának létrehozásával ismétlődik:

```json
"copy": {
  "name": "virtualMachineLoop", 
  "count": "[parameters('numberOfInstances')]"
},
```

Is, figyelje meg a példában, hogy a hurok index az erőforrás egyes értékeinek megadásakor használatos. Ha például három példányszámot adott meg, az operációs rendszer lemezeinek neve a következő: myOSDisk1, myOSDisk2 és myOSDisk3:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
}
```

> [!NOTE] 
>Ez a példa felügyelt lemezeket használ a virtuális gépekhez.
>
>

Ne feledje, hogy a sablonban lévő egyik erőforráshoz szükség lehet a hurok használatára más erőforrások létrehozásakor vagy elérésekor. Például több virtuális gép nem használhatja ugyanazt a hálózati adaptert, így ha a sablon három virtuális gép létrehozásával hurkol, akkor három hálózati adapter ek on-val is meg kell ismétlődnie. Amikor hálózati adaptert rendel egy virtuális géphez, a ciklusindex azonosítására szolgál:

```json
"networkInterfaces": [ { 
  "id": "[resourceId('Microsoft.Network/networkInterfaces',
    concat('myNIC', copyindex()))]" 
} ]
```

## <a name="dependencies"></a>Függőségek

A legtöbb erőforrás más erőforrások megfelelő működésétől függ. A virtuális gépeket virtuális hálózathoz kell társozni, és ehhez hálózati adapterre van szükség. A [dependsOn](../../resource-group-define-dependencies.md) elem annak biztosításához szolgál, hogy a hálózati adapter készen álljon a virtuális gépek létrehozása előtt:

```json
"dependsOn": [
  "[concat('Microsoft.Network/networkInterfaces/', 'myNIC', copyindex())]" 
],
```

Az Erőforrás-kezelő párhuzamosan telepít idoben minden olyan erőforrást, amely nem függ egy másik erőforrás üzembe helyezésétől. Legyen óvatos a függőségek beállításakor, mert véletlenül lelassíthatja a telepítést a szükségtelen függőségek megadásával. A függőségek több erőforráson keresztül is láncolhatók. A hálózati adapter például a nyilvános IP-címtől és a virtuális hálózati erőforrásoktól függ.

Honnan tudja, hogy szükség van-e függőségre? Tekintse meg a sablonban beállított értékeket. Ha a virtuálisgép-erőforrás-definíció egy eleme egy másik erőforrásra mutat, amely ugyanabban a sablonban van telepítve, függőségre van szüksége. A példavirtuális gép például egy hálózati profilt határoz meg:

```json
"networkProfile": { 
  "networkInterfaces": [ { 
    "id": "[resourceId('Microsoft.Network/networkInterfaces',
      concat('myNIC', copyindex())]" 
  } ] 
},
```

A tulajdonság beállításához a hálózati adapternek léteznie kell. Ezért függőségre van szüksége. Akkor is be kell állítania egy függőséget, ha egy erőforrás (egy gyermek) egy másik erőforráson (szülőn) belül van definiálva. Például a diagnosztikai beállítások és az egyéni parancsfájl-bővítmények egyaránt a virtuális gép gyermekerőforrásaiként vannak definiálva. Nem hozhatók létre, amíg a virtuális gép nem létezik. Ezért mindkét erőforrás a virtuális géptől függőként van megjelölve.

## <a name="profiles"></a>Profilok

A virtuálisgép-erőforrások definiálásakor számos profilelem használatos. Néhány szükséges, és néhány nem kötelező. Például a hardwareProfile, osProfile, storageProfile és networkProfile elemek szükségesek, de a diagnosticsProfile nem kötelező. Ezek a profilok olyan beállításokat határoznak meg, mint például:
   
- [Méret](sizes.md)
- [név](/azure/architecture/best-practices/resource-naming) és hitelesítő adatok
- lemez- és [operációsrendszer-beállítások](cli-ps-findimage.md)
- [hálózati adapter](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md) 
- rendszerindítási diagnosztika

## <a name="disks-and-images"></a>Lemezek és képek
   
Az Azure-ban a vhd-fájlok [lemezeket vagy képeket](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)jelölhetnek. Ha az operációs rendszer egy vhd fájl ban van specializálva, hogy egy adott virtuális gép, ez a továbbiakban lemez. Ha az operációs rendszer egy vhd fájl általánosított létrehozásához használt sok virtuális gép, ez a továbbiakban egy lemezkép.   
    
### <a name="create-new-virtual-machines-and-new-disks-from-a-platform-image"></a>Új virtuális gépek és új lemezek létrehozása platformlemezről

Virtuális gép létrehozásakor el kell döntenie, hogy milyen operációs rendszert használjon. A imageReference elem egy új virtuális gép operációs rendszerének definiálására szolgál. A példa egy Windows Server operációs rendszer definícióját mutatja be:

```json
"imageReference": { 
  "publisher": "MicrosoftWindowsServer", 
  "offer": "WindowsServer", 
  "sku": "2012-R2-Datacenter", 
  "version": "latest" 
},
```

Ha Linux operációs rendszert szeretne létrehozni, a következő definíciót használhatja:

```json
"imageReference": {
  "publisher": "Canonical",
  "offer": "UbuntuServer",
  "sku": "14.04.2-LTS",
  "version": "latest"
},
```

Az operációs rendszer lemezének konfigurációs beállításai az osDisk-elemhez vannak rendelve. A példa egy új felügyelt lemezt határoz meg, amelynek gyorsítótárazási módja **ReadWrite,** és hogy a lemez egy [platformlemezről](cli-ps-findimage.md)jön létre:

```json
"osDisk": { 
  "name": "[concat('myOSDisk', copyindex())]",
  "caching": "ReadWrite", 
  "createOption": "FromImage" 
},
```

### <a name="create-new-virtual-machines-from-existing-managed-disks"></a>Új virtuális gépek létrehozása meglévő felügyelt lemezekről

Ha meglévő lemezekről szeretne virtuális gépeket létrehozni, távolítsa el a imageReference és az osProfile elemeket, és adja meg a következő lemezbeállításokat:

```json
"osDisk": { 
  "osType": "Windows",
  "managedDisk": { 
    "id": "[resourceId('Microsoft.Compute/disks', [concat('myOSDisk', copyindex())])]" 
  }, 
  "caching": "ReadWrite",
  "createOption": "Attach" 
},
```

### <a name="create-new-virtual-machines-from-a-managed-image"></a>Új virtuális gépek létrehozása felügyelt lemezképből

Ha felügyelt lemezképből szeretne virtuális gépet létrehozni, módosítsa a imageReference elemet, és adja meg a következő lemezbeállításokat:

```json
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

### <a name="attach-data-disks"></a>Adatlemezek csatolása

Igény szerint adatlemezeket adhat hozzá a virtuális gépekhez. A [lemezek száma](sizes.md) a használt operációsrendszer-lemez méretétől függ. A virtuális gépek mérete Standard_DS1_v2, a hozzájuk adható adatlemezek maximális száma kettő. A példában minden virtuális géphez egy felügyelt adatlemez kerül hozzáadásra:

```json
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

Bár [a bővítmények](extensions-features.md) egy külön erőforrás, szorosan kapcsolódnak a virtuális gépekhez. A bővítmények hozzáadhatók a virtuális gép gyermekerőforrásaként vagy külön erőforrásként. A példa azt [mutatja,](extensions-diagnostics-template.md) hogy a diagnosztikai bővítmény hozzáadódik a virtuális gépekhez:

```json
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

Ez a bővítmény erőforrás a storageName változót és a diagnosztikai változókat használja az értékek megadásához. Ha módosítani szeretné a bővítmény által gyűjtött adatokat, további teljesítményszámlálókat adhat hozzá a wadperfcounters változóhoz. Azt is választhatja, hogy helyezze el a diagnosztikai adatokat egy másik tárfiókba, mint ahol a virtuális gép lemezek találhatók.

Sok kiterjesztés, amely telepítheti a virtuális gép, de a leghasznosabb valószínűleg az [egyéni parancsfájl-bővítmény](extensions-customscript.md). A példában egy start.ps1 nevű PowerShell-parancsfájl minden virtuális számítógépen fut, amikor először elindul:

```json
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

A start.ps1 parancsfájl számos konfigurációs feladatot képes elvégezni. Például a példában a virtuális gépekhez hozzáadott adatlemezek nincsenek inicializálva; inicializálhatja őket. Ha több indítási feladatot kell elvégeznie, a start.ps1 fájl használatával más PowerShell-parancsfájlokat hívhat meg az Azure storage-ban. A példa a PowerShellt használja, de bármilyen parancsfájlkezelési módszert használhat, amely elérhető a használt operációs rendszeren.

A telepített bővítmények állapotát a portál bővítmények beállításai között láthatja:

![A bővítmény állapotának beírása](./media/template-description/virtual-machines-show-extensions.png)

A **Get-AzVMExtension** PowerShell paranccsal, a **vm-bővítmény beolvassa** az Azure CLI parancsot vagy a **bővítményinformáció-információk beszerezni** a REST API-t is kaphatja.

## <a name="deployments"></a>Központi telepítés

Sablon üzembe helyezésekor az Azure nyomon követi a csoportként üzembe helyezett erőforrásokat, és automatikusan hozzárendel egy nevet ehhez az üzembe helyezett csoporthoz. A központi telepítés neve megegyezik a sablon nevével.

Ha kíváncsi az erőforrások állapotára a központi telepítésben, tekintse meg az erőforráscsoportot az Azure Portalon:

![Telepítési információk beszerezni](./media/template-description/virtual-machines-deployment-info.png)
    
Nem probléma, hogy ugyanazt a sablont használja erőforrások létrehozásához vagy meglévő erőforrások frissítéséhez. Amikor parancsokat használ a sablonok telepítéséhez, lehetősége van megmondani, hogy melyik [módot](../../resource-group-template-deploy.md) szeretné használni. A mód teljes **vagy** **növekményes.** Az alapértelmezett beállítás a növekményes frissítések megcsinálása. Legyen óvatos a **Teljes** mód használatakor, mert véletlenül törölheti az erőforrásokat. Ha a módot **Befejezés**módra állítja, az Erőforrás-kezelő törli az erőforráscsoport azon erőforrásait, amelyek nem a sablonban vannak.

## <a name="next-steps"></a>Következő lépések

- Hozzon létre saját [sablont az Azure Resource Manager-sablonok](../../resource-group-authoring-templates.md)szerzői használatával.
- Telepítse a létrehozott sablont [a Windows létrehozása virtuális gép használatával egy Erőforrás-kezelő sablonnal](ps-template.md).
- Megtudhatja, hogyan kezelheti a létrehozott virtuális gépeket a [Windows virtuális gépek létrehozása és kezelése az Azure PowerShell-modullal című áttekintéssel.](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- A JSON szintaxisát és a sablonokban lévő erőforrástípusok tulajdonságait az [Azure Resource Manager sablonhivatkozás című témakörében olvashat.](/azure/templates/)
