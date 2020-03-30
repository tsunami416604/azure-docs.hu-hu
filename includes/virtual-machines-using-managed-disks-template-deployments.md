---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: 126b488d2bb59e2904bee646301240efe6fe71a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76038145"
---
Ez a dokumentum bemutatja a felügyelt és nem felügyelt lemezek közötti különbségeket, amikor az Azure Resource Manager-sablonok at használja a virtuális gépek kiépítése. A példák segítségével frissítheti a nem felügyelt lemezeket használó meglévő sablonokat a felügyelt lemezekre. Referenciaként a [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) sablont használjuk útmutatóként. Ha közvetlenül szeretné összehasonlítani a [sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) a felügyelt lemezek és a [nem felügyelt lemezek](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) et használó korábbi verzió használatával is megtekintheti.

## <a name="unmanaged-disks-template-formatting"></a>Nem felügyelt lemezek sablonformázása

Először is vessünk egy pillantást a nem felügyelt lemezek üzembe helyezésére. Nem felügyelt lemezek létrehozásakor a Virtuális merevlemez-fájlok tárolásához tárfiókra van szükség. Létrehozhat egy új tárfiókot, vagy használhatja a már létezőt. Ez a cikk bemutatja, hogyan hozhat létre egy új tárfiókot. Hozzon létre egy tárfiók-erőforrást az erőforrásblokkban az alábbiak szerint.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

A virtuálisgép-objektumon belül adjon hozzá egy függőséget a tárfiókhoz annak érdekében, hogy a virtuális gép előtt létrejön. A `storageProfile` szakaszon belül adja meg a virtuális merevlemez helyének teljes URI-ját, amely a tárfiókra hivatkozik, és szükséges az operációs rendszer lemezéhez és az adatlemezekhez.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Felügyelt lemezek sablonjának formázása

Az Azure felügyelt lemezekkel a lemez felső szintű erőforrássá válik, és már nem igényel tárfiókot a felhasználó által létrehozandó. A felügyelt lemezek először `2016-04-30-preview` az API-verzióban voltak elérhetők, minden későbbi API-verzióban elérhetők, és mostantól az alapértelmezett lemeztípus. A következő szakaszok végigvezetik az alapértelmezett beállításokon, és részletezik a lemezek további testreszabását.

> [!NOTE]
> Javasoljuk, hogy az API-verziót később használja, mint `2016-04-30-preview` mivel a között és `2016-04-30-preview` `2017-03-30`a között megszakadt a változás.
>
>

### <a name="default-managed-disk-settings"></a>Alapértelmezett felügyelt lemezbeállítások

Ha felügyelt lemezekkel szeretne virtuális géphez dolgozni, többé nem kell létrehoznia a tárfiók-erőforrást. Hivatkozva a sablon alábbi példa, van néhány különbség a korábbi nem manged lemez példák at megjegyezni:

- Az `apiVersion` olyan verzió, amely támogatja a felügyelt lemezeket.
- `osDisk`és `dataDisks` már nem hivatkozhat a virtuális merevlemez adott URI-jára.
- Ha további tulajdonságok megadása nélkül telepíti, a lemez a virtuális gép mérete alapján használja a tárolási típust. Ha például olyan virtuális gépméretet használ, amely támogatja a prémium szintű tárhelyet (a nevükben "s" méretű méretek, például Standard_D2s_v3), akkor a prémium szintű lemezek alapértelmezés szerint konfigurálva lesznek. Ezt úgy módosíthatja, hogy a lemez termékváltozat-beállításával megad egy tárolótípust.
- Ha nincs megadva a lemez neve, akkor `<VMName>_OsDisk_1_<randomstring>` az operációs `<VMName>_disk<#>_<randomstring>` rendszer lemezének és minden adatlemeznek a formátumát veszi igénybe.
  - Ha egy virtuális gép jön létre egy egyéni lemezkép, majd az alapértelmezett beállításokat a tárfiók típusa és a lemez neve beolvasni az egyéni lemezerőforrásban meghatározott lemez tulajdonságokat. Ezek felülbírálhatók, ha a sablonban megadják ezek értékeit.
- Alapértelmezés szerint az Azure lemeztitkosítás le van tiltva.
- Alapértelmezés szerint a lemezgyorsítótárazás az operációs rendszer lemezének olvasása/írása, adatlemezek esetén nincs.
- Az alábbi példában továbbra is van egy tárfiók-függőség, bár ez csak a diagnosztika tárolására szolgál, és nincs szükség a lemezes tároláshoz.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Felső szintű felügyelt lemezerőforrás használata

A virtuálisgép-objektum lemezkonfigurációjának megadásának alternatívájaként létrehozhat egy legfelső szintű lemezerőforrást, és csatolhatja azt a virtuális gép létrehozásához. Létrehozhat például egy lemezerőforrást az adatlemezként való használatra.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

A virtuális gép objektumon belül hivatkozzon a csatolandó lemezobjektumra. A `managedDisk` tulajdonságban létrehozott felügyelt lemez erőforrás-azonosítójának megadása lehetővé teszi a lemez csatolását a virtuális gép létrehozásakor. A `apiVersion` virtuális gép erőforrás a. `2017-03-30` A rendszer hozzáadja a lemezerőforrás-függőséget annak érdekében, hogy a virtuális gép létrehozása előtt sikeresen létrejön. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Felügyelt rendelkezésre állási készletek létrehozása felügyelt lemezekkel rendelkező virtuális gépekkel

Ha felügyelt lemezekkel szeretne felügyelt rendelkezésre állási csoportokat létrehozni, adja hozzá az objektumot a `sku` rendelkezésre állási készlet erőforrásához, és állítsa a `name` tulajdonságot a számára. `Aligned` Ez a tulajdonság biztosítja, hogy az egyes virtuális gépek lemezei eléggé elkülönüljenek egymástól az egyes meghibásodási pontok elkerülése érdekében. Azt is `apiVersion` vegye figyelembe, hogy a `2018-10-01`rendelkezésre állási készlet erőforrás a.

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>Szabványos SSD-lemezek

Az alábbiakban az Erőforrás-kezelő sablonban a szabványos SSD-lemezek létrehozásához szükséges paramétereket talál:

* a Microsoft.Compute *apiVersion* verzióját `2018-04-01` (vagy újabb) kell beállítani.
* *ManagedDisk.storageAccountType* megadása`StandardSSD_LRS`

A következő példa a *properties.storageProfile.osDisk* szakaszt mutatja be egy szabványos SSD-lemezeket használó virtuális géphez:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

A szabványos SSD-lemez sablonnal történő létrehozásáról a [Virtuális gép létrehozása szabványos SSD-adatlemezekkel rendelkező Windows lemezről](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)című témakörben olvashat.

### <a name="additional-scenarios-and-customizations"></a>További forgatókönyvek és testreszabások

A REST API specifikációival kapcsolatos teljes körű információkért tekintse át a [FELÜGYELT lemez létrehozása REST API dokumentációját.](/rest/api/manageddisks/disks/disks-create-or-update) További forgatókönyveket, valamint alapértelmezett és elfogadható értékeket talál, amelyek sablontelepítéseken keresztül elküldhetők az API-ba. 

## <a name="next-steps"></a>További lépések

* A felügyelt lemezeket használó teljes sablonokat az alábbi Azure-gyorsindítási tárlaphivatkozásokra látogathatja.
    * [Windows virtuális gép felügyelt lemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux virtuális gép felügyelt lemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* A felügyelt lemezekről az [Azure Felügyelt lemezek áttekintése](../articles/virtual-machines/windows/managed-disks-overview.md) című dokumentumban olvashat bővebben.
* Tekintse át a virtuálisgép-erőforrások sablonreferencia-dokumentációját a [Microsoft.Compute/virtualMachines sablon](/azure/templates/microsoft.compute/virtualmachines) referenciadokumentumában.
* Tekintse át a lemezerőforrások sablonhivatkozási dokumentációját a [Microsoft.Compute/disks sablon](/azure/templates/microsoft.compute/disks) referenciadokumentumát.
* A felügyelt lemezek Azure virtuálisgép-méretezési készletek ben való használatáról az [Adatlemezek használata méretezési készletekkel](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) című dokumentumban talál további információt.
