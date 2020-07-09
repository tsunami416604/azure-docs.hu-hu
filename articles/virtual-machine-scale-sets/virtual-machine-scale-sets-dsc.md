---
title: A kívánt állapot konfigurációjának használata Virtual Machine Scale Sets
description: A virtuális gépek konfigurálásához használja a Virtual Machine Scale Setst az Azure desired State Configuration bővítménnyel.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 6939339ce8d0f324fbd9e14ba31d23a6febcfcbe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85373897"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Virtual Machine Scale Sets használata az Azure DSC bővítménnyel
[Virtual Machine Scale sets](virtual-machine-scale-sets-overview.md) használható az [Azure desired State Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) bővítmény kezelőjével. A virtuálisgép-méretezési csoportok nagy számú virtuális gép üzembe helyezését és felügyeletét teszik lehetővé, és rugalmasan méretezhetik be és ki a terhelésre válaszul. A DSC használatával a virtuális gépek úgy konfigurálhatók, ahogy online állapotba kerülnek, így az éles szoftvert futtatják.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>A Virtual Machines és Virtual Machine Scale Sets üzembe helyezése közötti különbségek
A virtuálisgép-méretezési csoport mögöttes sablon szerkezete némileg eltér egyetlen virtuális gépről. Pontosabban, egyetlen virtuális gép telepíti a bővítményeket a "virtualMachines" csomópont alatt. Létezik egy "Extensions" típusú bejegyzés, amelyben a DSC hozzá van adva a sablonhoz

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

A virtuálisgép-méretezési csoport csomópontjának "Properties" szakasza "VirtualMachineProfile", "extensionProfile" attribútummal rendelkezik. A DSC a "Extensions" alatt lett hozzáadva

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
A virtuálisgép-méretezési csoport viselkedése azonos egyetlen virtuális gép viselkedésével. Új virtuális gép létrehozásakor a rendszer automatikusan kiépíti a DSC-bővítményt. Ha a bővítmény a WMF újabb verzióját igényli, a virtuális gép újraindul, mielőtt online állapotba kerül. Ha online állapotban van, letölti a DSC Configuration. zip fájlt, és kiépíti a virtuális gépre. További részleteket [Az Azure DSC bővítmény áttekintésében](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)találhat.

## <a name="next-steps"></a>További lépések
Vizsgálja [meg a DSC-bővítmény Azure Resource Manager sablonját](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ismerje meg, hogyan [kezeli a DSC-bővítmény a hitelesítő adatok biztonságos kezelését](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Az Azure DSC bővítmény kezelőjével kapcsolatos további információkért lásd: [Bevezetés az Azure kívánt állapotának konfigurációs bővítmény-kezelőjébe](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

A PowerShell DSC-vel kapcsolatos további információkért [látogasson el a PowerShell dokumentációs központba](/powershell/scripting/dsc/overview/overview). 

