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
ms.openlocfilehash: 6085eb2b520217c4e678a75032e8a1cb4b9343ec
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261329"
---
# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>A felügyelt lemezek az Azure Resource Manager-sablonok használatával

Ez a dokumentum végigvezeti Önt az Azure Resource Manager-sablonok használata a virtuális gépek üzembe helyezése felügyelt és nem felügyelt lemezek közötti különbségeket. A példák segítenek a felügyelt lemezeket nem felügyelt lemezeket használó meglévő sablonok frissítése. Referenciaként használjuk a [101-es vm-egyszerű – windows-](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) sablon alapján. Láthatja, hogy a sablon használatával is [felügyelt lemezek](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) és a egy korábbi verzióját használja [nem felügyelt lemezek](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) Ha szeretne közvetlenül összehasonlítani őket.

## <a name="unmanaged-disks-template-formatting"></a>Nem felügyelt lemezek sablon formázása

Első lépésként hozzunk hajtsa végre a megfelelő egy pillantást hogyan nem felügyelt lemezek vannak üzembe helyezve. Nem felügyelt lemezek létrehozásakor kell egy tárfiókot a VHD-fájlok tárolására. Hozzon létre egy új tárfiókot, vagy használjon egy meglévőt. Ez a cikk bemutatja, hogyan hozhat létre egy új tárfiókot. Hozzon létre egy tárfiók típusú erőforrást az erőforrások blokk alább látható módon.

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

A virtuális gép objektumon belül adja hozzá a függőség a storage-fiók segítségével, hogy a virtuális gép előtt létrehozott. Belül a `storageProfile` szakaszban adja meg a teljes URI-ját a VHD-helyen, amely hivatkozik a tárfiók és az operációsrendszer-lemez és bármely adatlemez van szükség.

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

## <a name="managed-disks-template-formatting"></a>Felügyelt lemezek sablon formázása

Az Azure Managed Disks használatával a a lemez legfelsőbb szintű erőforrással válik, és a egy storage-fiókot kell létrehoznia a felhasználó már nem igényel. A felügyelt lemezek először lett közzétéve a `2016-04-30-preview` API-verzió, azok minden későbbi API-verziókban elérhető és a már a alapértelmezett lemeztípus. A következő szakaszok végigvezetik az alapértelmezett beállításokat, és bemutatják, hogyan további testreszabási a lemezek.

> [!NOTE]
> Javasoljuk, hogy az API-verziót használja későbbi, mint `2016-04-30-preview` módon történt használhatatlanná tévő változás között `2016-04-30-preview` és `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Alapértelmezett beállítások a felügyelt lemez

A felügyelt lemezekkel rendelkező virtuális Gépet létrehozni, már nem szeretne létrehozni a storage erőforrás-fiók és a következőképpen frissítheti a virtuális gép típusú erőforrást. Kifejezetten vegye figyelembe, hogy a `apiVersion` tükrözi `2017-03-30` és a `osDisk` és `dataDisks` már nem hivatkozik egy adott URI-t a virtuális merevlemez. További tulajdonságok megadása nélkül telepíti, ha a lemez egy tárolási típust, a virtuális gép mérete alapján fogja használni. Például ha a prémium szintű képes a virtuális gép méretét ("s" a neve például Standard_D2s_v3 méretek) használ majd rendszer Premium_LRS tárolást alkalmaznak. A lemez a termékváltozat-beállítás használatával adja meg a tárolás típusát. Ha nem ad meg nevet, akkor formátuma `<VMName>_OsDisk_1_<randomstring>` az operációsrendszer-lemez és `<VMName>_disk<#>_<randomstring>` az egyes adatlemezek. Alapértelmezés szerint le van tiltva az Azure disk encryption; írási/olvasási gyorsítótárazás az operációsrendszer-lemez és adatlemezek sem. Az alábbi példában Észreveheti, van még egy storage-fióktól függ, de ez csak a tároló, diagnosztika, és nem szükséges lemezes tárolás.

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

### <a name="using-a-top-level-managed-disk-resource"></a>A legfelső szintű felügyelt lemez használata

Alternatív megoldásként a lemezkonfigurációt megadásáról a virtuálisgép-objektumot legfelső szintű lemez erőforrás létrehozása, és csatolja azt a virtuális gép létrehozásának részeként. Például olyan lemezerőforrást adatlemezként használni kívánt módon is létrehozhat.

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

A virtuális gép objektumon belül hivatkozhat a lemez objektum van csatlakoztatva. Adja meg a létrehozott felügyelt lemez az erőforrás-Azonosítóját a `managedDisk` tulajdonság lehetővé teszi, hogy a lemez a mellékletet, a virtuális gép létrehozása. A `apiVersion` a virtuális gép erőforrás értéke `2017-03-30`. Sikeresen létrejön a virtuális gép létrehozása előtt ellenőrizze a lemez erőforrás függőségi kerül. 

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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>A felügyelt lemezeket használó virtuális gépek felügyelt rendelkezésre állási csoportok létrehozása

Felügyelt rendelkezésre állási csoportok a virtuális gépek felügyelt lemezeket használ, adja hozzá a `sku` objektum, melyet a rendelkezésre állási erőforrás, és állítsa be a `name` tulajdonságot `Aligned`. Ez a tulajdonság biztosítja, hogy az egyes virtuális gépek lemezei kellőképpen különítve egymástól a kritikus hibapontok elkerülése érdekében. Azt is vegye figyelembe, hogy a `apiVersion` esetében a rendelkezésre állási csoport erőforrás értéke `2017-03-30`.

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

### <a name="standard-ssd-disks"></a>Standard SSD-lemez

Az alábbiakban Standard SSD-lemez létrehozása a Resource Manager-sablon a szükséges paraméterek:

* *API-verzió* a Microsoft.Compute kell beállítani: `2018-04-01` (vagy újabb)
* Adja meg *managedDisk.storageAccountType* , `StandardSSD_LRS`

A következő példa bemutatja a *properties.storageProfile.osDisk* Standard SSD-lemezeket használó virtuális gép a következő szakaszban:

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

Standard SSD-lemez létrehozása sablon alapján, a teljes sablont példa: [egy Windows-rendszerképből Standard SSD-Adatlemezekkel rendelkező virtuális gép létrehozása](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/).

### <a name="additional-scenarios-and-customizations"></a>További szituációk és testreszabás

A REST API-leírások teljes információk megkereséséhez tekintse át a [hozzon létre egy felügyelt lemezt REST API-dokumentáció](/rest/api/manageddisks/disks/disks-create-or-update). További forgatókönyvek, valamint az alapértelmezett és az elfogadható értékek, amelyek küldheti el a sablon-üzembehelyezések API találja. 

## <a name="next-steps"></a>További lépések

* Látogasson el a következő Azure rövid útmutató tárház hivatkozások teljes, felügyelt lemezeket használó sablonokat.
    * [Windows virtuális gép felügyelt lemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux rendszerű virtuális gép felügyelt lemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
    * [Felügyelt lemez sablonok teljes listája](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md)
* Látogasson el a [Azure Managed Disks – áttekintés](../articles/virtual-machines/windows/managed-disks-overview.md) dokumentum további információk a felügyelt lemezekről.
* Tekintse át a sablon referenciadokumentációjából virtuálisgép-erőforrások meglátogatják a [Microsoft.Compute/virtualMachines sablonreferenciája](/azure/templates/microsoft.compute/virtualmachines) dokumentumot.
* Tekintse át a sablon referenciadokumentációjából lemez erőforrások meglátogatják a [Microsoft.Compute/disks sablonreferenciája](/azure/templates/microsoft.compute/disks) dokumentumot.
* A felügyelt lemezek használata az Azure-beli virtuálisgép-méretezési csoportok további információért látogasson el a [adatlemezek használata méretezési csoportokkal](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) dokumentumot.
