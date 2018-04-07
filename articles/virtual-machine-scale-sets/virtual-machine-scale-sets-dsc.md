---
title: Használatával Célállapotkonfiguráció a virtuálisgép-méretezési csoportok |} Microsoft Docs
description: Az Azure DSC-bővítményt a virtuálisgép-méretezési használatával beállítása
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
ms.openlocfilehash: a68a5f31952d636c054b66dc0bb6ec0579cd7192
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Az Azure DSC-bővítményt a virtuálisgép-méretezési használatával beállítása
[Virtuálisgép-méretezési csoportok](virtual-machine-scale-sets-overview.md) együtt a [Azure kívánt állapot konfigurációs szolgáltatása (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) bővítmény kezelő. Virtuálisgép-méretezési csoportok nyújtanak olyan telepíthetnek és kezelhetnek olyan nagy számú virtuális gépet, és rugalmasan méretezhető és betölteni válaszul. A DSC ismét online elérhető, a termelési szoftvert futtatnak, mert a virtuális gépek konfigurálására szolgál.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Központi telepítése virtuális gépek és virtuálisgép-méretezési csoportok közötti különbségek
Az alapul szolgáló sablon-szerkezet egy virtuálisgép-méretezési csoport egy egy virtuális némileg eltérő. Egy virtuális pontosabban, a "virtuális gép" csomópontban bővítmények telepíti. Nincs "kiterjesztésekkel" típusú bejegyzés ahol DSC hozzáadódik a sablon

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

A virtuális gép méretezési készlet csomópont "Tulajdonságok" szakasza az a "VirtualMachineProfile", "extensionProfile" attribútum. A DSC hozzáadása az "kiterjesztésekkel"

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport működése
A virtuálisgép-méretezési csoport viselkedés megegyezik a működése egy virtuális. Amikor egy új virtuális Gépet hoz létre, akkor a DSC-bővítményt automatikusan ki van építve. A WMF újabb verziója szükséges a bővítmény által, ha a virtuális gép újraindul, mielőtt online állapotba kerüljön. Online állapotban, ha a DSC-konfiguráció .zip tölti le, és építse ki azt a virtuális Gépen. További részletek találhatók [az Azure DSC-bővítmény áttekintése](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>További lépések
Vizsgálja meg a [Azure Resource Manager sablon a DSC-bővítmény](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ismerje meg, hogy a [DSC-bővítményt biztonságosan kezeli a hitelesítő adatok](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Az Azure DSC-bővítmény kezelő további információkért lásd: [bemutatása az Azure célállapot-konfiguráció bővítmény kezelő](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

További információ a PowerShell DSC [látogasson el a PowerShell dokumentációs központban](https://msdn.microsoft.com/powershell/dsc/overview). 

