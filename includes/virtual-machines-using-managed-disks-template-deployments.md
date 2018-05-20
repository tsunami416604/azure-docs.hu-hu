# <a name="using-managed-disks-in-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok lemezek felügyelt

Ez a dokumentum végigvezeti a virtuális gépeket Azure Resource Manager-sablonok használatával kezelt és nem kezelt lemezek közötti különbségeket. Ez segít frissíteni a felügyelt lemezek nem felügyelt lemezt használ, meglévő sablonok. Referenciaként használjuk a [101-vm-egyszerű-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) útmutatóként sablont. Láthatja, hogy a sablon használatával is [által kezelt lemezeken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) egy korábbi verzióját használja, és [lemezek nem felügyelt](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) Ha szeretne közvetlenül hasonlítsa össze azokat.

## <a name="unmanaged-disks-template-formatting"></a>Nem felügyelt lemezek sablon formázás

Első lépésként azt tekintse meg, hogyan nem felügyelt lemezek vannak telepítve. Nem felügyelt lemezek létrehozásakor a VHD-fájlok tárolásához storage-fiók szükséges. Hozzon létre egy új tárfiókot, vagy használjon már meglévő. Ez a cikk bemutatja, hogyan hozzon létre egy új tárfiókot. Ehhez szüksége van a tárolási fiók erőforrás az erőforrások blokkban alább látható módon.

```
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

A virtuális gép objektumon belül igazolnia kell a storage-fiók segítségével győződjön meg arról, hogy a virtuális gép előtt létrehozott függ. Belül a `storageProfile` szakaszban azt adja meg a teljes URI-azonosítója a VHD helye, amely hivatkozik a tárfiók, és az operációsrendszer-lemezképet és az adatok lemezzel van szükség. 

```
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

```
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

Másik megoldásként a lemezkonfiguráció megadása a virtuálisgép-objektumot hozzon létre egy legfelső szintű lemezerőforrás, és csatlakoztassa a virtuális gép létrehozása során nem. Olyan lemezerőforrást az alábbiak szerint használandó adatlemezt például létrehozhatunk olyan.

```
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

A Virtuálisgép-objektum belül a lemez objektum csatolni kell majd azt is hivatkozhatnak. Adja meg a felügyelt lemezes, a létrehozott erőforrás-azonosítója a `managedDisk` tulajdonság lehetővé teszi a mellékletet, a lemez számára, a virtuális gép létrehozása. Vegye figyelembe, hogy a `apiVersion` a virtuális gép erőforrás értéke `2017-03-30`. Ne feledje, hogy létrehoztunk Önnek függősége a lemezerőforrástól sikeres létrehozása előtt a Virtuálisgép-létrehozási biztosításához. 

```
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

Felügyelt létrehozásához rendelkezésre állási készletek virtuális gépek felügyelt lemezt használ, adja hozzá a `sku` objektum a következő rendelkezésre állási erőforrás beállításához, állítsa be a `name` tulajdonságot `Aligned`. Ez biztosítja, hogy az egyes virtuális gépek a lemezek megfelelően különítve egymástól elkerülése érdekében a hibaérzékeny pontokat. Ne feledje, hogy a `apiVersion` esetében a rendelkezésre állási erőforrás értéke `2017-03-30`.

```
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
* Az Azure VM skálázási készletekben felügyelt lemezek használatáról további információért látogasson el a [adatlemezek használata méretezési csoportok](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md) dokumentum.
