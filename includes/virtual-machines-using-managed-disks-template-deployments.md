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
ms.openlocfilehash: b2561f4b1b5ef27f389114c85f0646b968f7765e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36269561"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok lemezek felügyelt

Ez a dokumentum végigvezeti a virtuális gépeket Azure Resource Manager-sablonok használatával kezelt és nem kezelt lemezek közötti különbségeket. A példák segítségével felügyelt lemezek nem felügyelt lemezt használ, meglévő sablonok frissítése. Referenciaként használjuk a [101-vm-egyszerű-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) útmutatóként sablont. Láthatja, hogy a sablon használatával is [által kezelt lemezeken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) egy korábbi verzióját használja, és [lemezek nem felügyelt](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) Ha szeretne közvetlenül hasonlítsa össze azokat.

## <a name="unmanaged-disks-template-formatting"></a>Nem felügyelt lemezek sablon formázás

Első lépésként most hajtsa végre a megfelelő hogyan nem felügyelt lemezek egy pillantást vannak telepítve. Nem felügyelt lemezek létrehozásakor a VHD-fájlok tárolásához storage-fiók szükséges. Hozzon létre egy új tárfiókot, vagy használjon már meglévő. Ez a cikk bemutatja, hogyan hozzon létre egy új tárfiókot. Hozzon létre egy tárolási fiók erőforrást az erőforrások blokkban alább látható módon.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2016-01-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

A virtuális gép objektumban hozzáadjon egy függőséget a tárfiók annak érdekében, hogy a virtual machine létrehozták. Belül a `storageProfile` szakaszban adja meg a teljes URI-azonosítója a VHD helye, amely hivatkozik a tárfiók, és az operációsrendszer-lemezképet és az adatok lemezzel van szükség.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines",
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

## <a name="managed-disks-template-formatting"></a>Felügyelt lemezek sablon formázás

Azure felügyelt lemezek a lemez egy legfelső szintű erőforrás lesz, és többé nem kell egy tárfiókot, a felhasználó által létrehozandó. Felügyelt lemezek először volt felfedett a `2016-04-30-preview` API-verziót, azok érhetők el az összes azt követő API-verziók és az alapértelmezett lemeztípus is. A következő szakaszok végezze el az alapértelmezett beállításokat, és részletesen további testreszabásával a lemezeket.

> [!NOTE]
> Javasoljuk, hogy az API-verziót használja későbbi, mint `2016-04-30-preview` mert jelentős változásokat közötti `2016-04-30-preview` és `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Alapértelmezett felügyelt lemezes beállításai

Felügyelt lemezzel rendelkező virtuális gép létrehozása, már nem szeretne létrehozni a tárolási erőforrás fiókot, és a következőképpen frissítheti a virtuálisgép-erőforrást. Kifejezetten vegye figyelembe, hogy a `apiVersion` tükrözi `2017-03-30` és a `osDisk` és `dataDisks` már nem hivatkozik egy adott URI-t a virtuális merevlemez. További tulajdonságok megadása nélkül való telepítésekor a fogja használni a lemez [Standard-LRS tárolási](../articles/storage/common/storage-redundancy.md). Ha nem ad meg nevet, tart formátuma `<VMName>_OsDisk_1_<randomstring>` az operációsrendszer-lemezképet a és `<VMName>_disk<#>_<randomstring>` adatok lemezek. Alapértelmezés szerint az Azure disk encryption le van tiltva; Olvasási/írási gyorsítótárazást az operációsrendszer-lemez és adatlemezek nincs. Az alábbi példában szereplő Észreveheti, továbbra is fennáll a tárolási fiók függőségi, bár ez csak a tárolási diagnosztikai és a lemezes tárolás esetén nem szükséges.

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
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

### <a name="using-a-top-level-managed-disk-resource"></a>Egy legfelső szintű felügyelt lemezes erőforrást használja

Másik megoldásként a lemezkonfiguráció megadása a virtuálisgép-objektumot hozzon létre egy legfelső szintű lemezerőforrás, és csatlakoztassa a virtuális gép létrehozása során nem. Például a következőképpen használandó adatlemezt lemezerőforrást is létrehozhat.

```json
{
    "type": "Microsoft.Compute/disks",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "apiVersion": "2017-03-30",
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

Belül a Virtuálisgép-objektum a lemez objektum csatolni kell hivatkoznia. Adja meg a létrehozott kezelt lemez az erőforrás-azonosítója a `managedDisk` tulajdonság lehetővé teszi a mellékletet, a lemez számára, a virtuális gép létrehozása. A `apiVersion` a virtuális gép erőforrás értéke `2017-03-30`. Győződjön meg arról, sikeres létrehozása előtt a virtuális gép létrehozása a lemezerőforrástól függőség kerül. 

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Felügyelt rendelkezésre állási csoportok felügyelt lemezek használata virtuális gépek létrehozása

Felügyelt létrehozásához rendelkezésre állási készletek virtuális gépek felügyelt lemezt használ, adja hozzá a `sku` objektum a következő rendelkezésre állási erőforrás beállításához, állítsa be a `name` tulajdonságot `Aligned`. Ez a tulajdonság biztosítja, hogy az egyes virtuális gépek a lemezek megfelelően különítve egymástól elkerülése érdekében a hibaérzékeny pontokat. Ne feledje, hogy a `apiVersion` esetében a rendelkezésre állási erőforrás értéke `2017-03-30`.

```json
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/availabilitySets",
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

### <a name="standard-ssd-disks"></a>Standard SSD-lemezek

Az alábbiakban a Standard SSD-lemezek létrehozásához a Resource Manager-sablon a szükséges paramétereket:

* *apiVersion* Microsoft.Compute kell beállítani, mint `2018-04-01` (vagy újabb)
* Adja meg *managedDisk.storageAccountType* , `StandardSSD_LRS`

Az alábbi példa azt mutatja meg a *properties.storageProfile.osDisk* szakasz a szabványos SSD-lemezeket használó virtuális gépek:

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

A teljes sablon példa bemutatja, hogyan sablonnal szabványos SSD lemez létrehozása, lásd: [virtuális gép létrehozása a Windows-lemezkép szabványos SSD Adatlemezekkel rendelkező](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>További forgatókönyvek és testreszabása

Teljes információ keresése a REST API előírásoknak, tekintse át a [felügyelt lemezes REST API-dokumentáció](/rest/api/manageddisks/disks/disks-create-or-update). További helyzeteket is, valamint az alapértelmezett és az elfogadható értékek, amelyek küldheti el a sablon központi telepítések keresztül API találja. 

## <a name="next-steps"></a>További lépések

* Látogasson el a következő Azure gyors üzembe helyezés tárház hivatkozások teljes kezelt lemezek használó sablonokat.
    * [A felügyelt lemezes Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [A felügyelt lemezes Linux virtuális gép](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Felügyelt lemezes sablonok teljes listája](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Látogasson el a [Azure felügyelt lemezekhez – áttekintés](../articles/virtual-machines/windows/managed-disks-overview.md) dokumentum felügyelt lemezek tájékozódhat.
* Tekintse át a virtuális gép erőforrásai sablon dokumentációját látogasson el a [Microsoft.Compute/virtualMachines sablonra való hivatkozást](/azure/templates/microsoft.compute/virtualmachines) dokumentum.
* Tekintse át a sablon referenciadokumentációt tartalmaz lemezerőforrásokat látogasson el a [Microsoft.Compute/disks sablonra való hivatkozást](/azure/templates/microsoft.compute/disks) dokumentum.
* Az Azure virtuálisgép-méretezési csoportok felügyelt lemezek használatáról további információért látogasson el a [adatlemezek használata méretezési csoportok](https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) dokumentum.
