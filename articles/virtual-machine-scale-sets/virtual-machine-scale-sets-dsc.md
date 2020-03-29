---
title: A kívánt állapotkonfiguráció használata virtuálisgép-méretezési készletekkel
description: Virtuálisgép-méretezési készletek használata az Azure kívánt állapotkonfigurációs bővítmény a virtuális gépek konfigurálásához.
author: zjalexander
tags: azure-service-management,azure-resource-manager
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: a93a8a9c27be5a1736a50e6c4c4b830980b7d974
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278078"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Virtuálisgép-méretezési készletek használata az Azure DSC-bővítménylel
[A virtuálisgép-méretezési készletek](virtual-machine-scale-sets-overview.md) az [Azure desired state configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) bővítménykezelővel használhatók. A virtuálisgép-méretezési készletek lekínálják a virtuális gépek nagy számának üzembe helyezését és kezelését, és rugalmasan skálázhatók be és ki a terhelésre válaszul. A DSC a virtuális gépek online állapotba kerülése során konfigurálható, így azok az éles szoftvert futtatják.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>A virtuális gépekre és a virtuálisgép-méretezési csoportokra való üzembe helyezés közötti különbségek
A virtuálisgép-méretezési csoport alapul szolgáló sablonszerkezete némileg eltér egyetlen virtuális géptől. Pontosabban egy virtuális gép telepíti a bővítményeket a "virtualMachines" csomópont alatt. Van egy "kiterjesztések" típusú bejegyzés, ahol a DSC hozzáadódik a sablonhoz

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

A virtuálisgép-méretezési csoport csomópontja rendelkezik egy "Properties" szakasz "VirtualMachineProfile", "extensionProfile" attribútummal. A DSC a "kiterjesztések" alá kerül

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport viselkedése
A virtuális gép méretezési csoport viselkedése megegyezik a viselkedését egy virtuális gép. Új virtuális gép létrehozásakor automatikusan kilesz építve a DSC-bővítmény. Ha a bővítmény a WMF újabb verziójára van szükség, a virtuális gép újraindul, mielőtt online állapotba lépne. Miután online állapotba került, letölti a DSC-konfigurációt .zip, és kiépíti a virtuális gépre. További részletek az [Azure DSC-bővítmény áttekintése című témakörben](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)találhatók.

## <a name="next-steps"></a>További lépések
Vizsgálja meg az [Azure Resource Manager sablont a DSC-bővítményhez.](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Ismerje meg, hogy a [DSC-bővítmény hogyan kezeli biztonságosan a hitelesítő adatokat.](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 

Az Azure DSC-bővítménykezelőről további információt [az Azure kívánt állapotkonfigurációs bővítménykezelőjének bemutatása](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)című témakörben talál. 

A PowerShell DSC-ről további információt [a PowerShell dokumentációs központjában talál.](/powershell/scripting/dsc/overview/overview) 

