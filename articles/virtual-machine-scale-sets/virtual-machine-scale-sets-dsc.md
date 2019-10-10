---
title: A kívánt állapot konfigurációjának használata a Virtual Machine Scale Sets használatával | Microsoft Docs
description: Virtual Machine Scale Sets használata az Azure DSC bővítménnyel
services: virtual-machine-scale-sets
documentationcenter: ''
author: zjalexander
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
keywords: ''
ms.assetid: c8f047b5-0e6c-4ef3-8a47-f1b284d32942
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 04/05/2017
ms.author: zachal
ms.openlocfilehash: f3da1ed5eabd3a35fe382471314084258b20213b
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166161"
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

## <a name="next-steps"></a>Következő lépések
Vizsgálja [meg a DSC-bővítmény Azure Resource Manager sablonját](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ismerje meg, hogyan [kezeli a DSC-bővítmény a hitelesítő adatok biztonságos kezelését](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Az Azure DSC bővítmény kezelőjével kapcsolatos további információkért lásd: [Bevezetés az Azure kívánt állapotának konfigurációs bővítmény-kezelőjébe](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

A PowerShell DSC-vel kapcsolatos további információkért [látogasson el a PowerShell dokumentációs központba](/powershell/scripting/dsc/overview/overview). 

