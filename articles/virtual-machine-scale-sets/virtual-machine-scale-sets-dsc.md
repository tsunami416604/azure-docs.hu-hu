---
title: A Virtual Machine Scale Sets használatával Desired State Configuration |} A Microsoft Docs
description: Virtuális gép méretezési csoportokat használ az Azure DSC-bővítmény
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
ms.openlocfilehash: 24a37d352413ff9ac55ce8e189691988383950f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203992"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Virtuális gép méretezési csoportokat használ az Azure DSC-bővítmény
[Virtual Machine Scale Sets](virtual-machine-scale-sets-overview.md) együtt a [Azure Desired State Configuration (DSC)](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Bővítménykezelő. A Virtual machine scale sets kényelmesen üzembe helyezése és kezelése nagy számú virtuális gépet, és rugalmasan méretezheti és betölteni a válaszban. DSC konfigurálása a virtuális gépek online állapotba kerül, amikor futnak az üzemi célú szoftverek váló szolgál.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Virtuális gépek és a Virtual Machine Scale Sets telepítése közti különbséget
Egy virtuálisgép-méretezési csoportot az alapul szolgáló sablon struktúráját némileg eltér a egyetlen virtuális Gépet. Pontosabban egyetlen virtuális Gépet helyez üzembe bővítmények a "virtuális gép" csomópont alatt. Nincs "bővítmények" típusú bejegyzés ahol DSC bekerül a sablon

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

Egy virtuális gép méretezési készlet csomópont tartalmaz egy "Tulajdonságok" szakaszt a "VirtualMachineProfile", "extensionProfile" attribútum. DSC "bővítmények" területen hozzá lesz adva.

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

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Egy virtuálisgép-méretezési csoportot viselkedése
Egy virtuálisgép-méretezési csoportot a viselkedés megegyezik az egyetlen virtuális gép működése. Új virtuális gép létrehozásakor automatikusan kiépítve a DSC bővítménnyel. A bővítmény által a WMF egy újabb verziója szükséges, ha a virtuális gép újraindul, mielőtt online állapotba kerüljön. Online módban, ha letölti a DSC-konfiguráció .zip, és építse ki azt a virtuális gépen. További részletek találhatók [az Azure-DSC-bővítmény áttekintése](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>További lépések
Vizsgálja meg a [Azure Resource Manager-sablon a DSC-bővítmény](../virtual-machines/windows/extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ismerje meg, hogy a [DSC bővítmény biztonságosan kezeli a hitelesítő adatok](../virtual-machines/windows/extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Az Azure DSC bővítmény kezelő további információkért lásd: [bemutatása az Azure Desired State Configuration bővítmény kezelő](../virtual-machines/windows/extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

További információ a PowerShell DSC [a PowerShell dokumentációs központ felkeresése](https://msdn.microsoft.com/powershell/dsc/overview). 

