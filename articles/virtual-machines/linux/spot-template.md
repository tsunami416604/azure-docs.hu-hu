---
title: Sablon használata az Azure Spot virtuális gépek üzembe helyezéséhez (előzetes verzió)
description: Ismerje meg, hogyan használhat sablont a direkt virtuális gépek üzembe helyezéséhez a költségek csökkentése érdekében.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 0e635fe7ce9b442a9cc8f0fdf614feef5a3a756a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082795"
---
# <a name="deploy-spot-vms-using-a-resource-manager-template"></a>Azonnali virtuális gépek telepítése Erőforrás-kezelő sablon használatával

A [spot virtuális gépek](spot-vms.md) használatával jelentős költségmegtakarítást eredményezhet a kihasználatlan kapacitás kihasználása. Bármikor, amikor az Azure-nak szüksége van a kapacitás vissza, az Azure-infrastruktúra kilakoltatja spot virtuális gépek. Ezért a direkt virtuális gépek kiválóan szolgálnak olyan számítási feladatokhoz, amelyek kezelni tudják a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási számítási feladatokat és egyebeket.

A direkt virtuális gépek díjszabása változó, a régió és a termékváltozat alapján. További információ: VM-díjszabás [Linuxra](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windowsra.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)

Lehetősége van arra, hogy állítsa be a maximális árat, amelyet hajlandó fizetni, óránként, a virtuális gép. A direktvirtuális gép maximális ára usa dollárban (USD) állítható be, legfeljebb 5 tizedesjegy használatával. Az érték `0.98765`például óránként $0,98765 USD max ár. Ha a maximális árat `-1`állítja be, a virtuális gép nem lesz kizárva az ár alapján. A virtuális gép ára a Direktség aktuális ára vagy egy szabványos virtuális gép ára lesz, amely valaha is kevesebb, mindaddig, amíg van kapacitás és kvóta. A maximális ár beállításáról további információt a Azonnali virtuális gépek – árképzés című témakörben [talál.](spot-vms.md#pricing)

> [!IMPORTANT]
> A direktpéldányok jelenleg nyilvános előzetes verzióban vannak.
> Ez az előzetes verzió éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>

## <a name="use-a-template"></a>Sablon használata

Direktszínsablon-telepítések esetén használja`"apiVersion": "2019-03-01"` vagy később. Adja `priority`hozzá `evictionPolicy` `billingProfile` a és a tulajdonságokat a sablonban:

```json
"priority": "Spot",
"evictionPolicy": "Deallocate",
"billingProfile": {
    "maxPrice": -1
}
```

Az alábbiakban egy mintasablon a hozzáadott tulajdonságok egy direkt virtuális gép. Cserélje le az erőforrás `<password>` neveket a saját és egy jelszót a helyi rendszergazdai fiók a virtuális gép.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2019-03-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "vnetId": "/subscriptions/ec9fcd04-e188-48b9-abfc-abcd515f1836/resourceGroups/spotVM/providers/Microsoft.Network/virtualNetworks/spotVM",
        "subnetName": "default",
        "networkInterfaceName": "spotVMNIC",
        "publicIpAddressName": "spotVM-ip",
        "publicIpAddressType": "Dynamic",
        "publicIpAddressSku": "Basic",
        "virtualMachineName": "spotVM",
        "osDiskType": "Premium_LRS",
        "virtualMachineSize": "Standard_D2s_v3",
        "adminUsername": "azureuser",
        "adminPassword": "<password>",
        "diagnosticsStorageAccountName": "diagstoragespot2019",
        "diagnosticsStorageAccountId": "Microsoft.Storage/storageAccounts/diagstoragespot2019",
        "diagnosticsStorageAccountType": "Standard_LRS",
        "diagnosticsStorageAccountKind": "Storage",
        "subnetRef": "[concat(variables('vnetId'), '/subnets/', variables('subnetName'))]"
    },
    "resources": [
        {
            "name": "spotVM",
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId(resourceGroup().name, 'Microsoft.Network/publicIpAddresses', variables('publicIpAddressName'))]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "name": "[variables('publicIpAddressName')]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2019-02-01",
            "location": "eastus",
            "properties": {
                "publicIpAllocationMethod": "[variables('publicIpAddressType')]"
            },
            "sku": {
                "name": "[variables('publicIpAddressSku')]"
            }
        },
        {
            "name": "[variables('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', variables('networkInterfaceName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[variables('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[variables('virtualMachineName')]",
                    "adminUsername": "[variables('adminUsername')]",
                    "adminPassword": "[variables('adminPassword')]"
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('https://', variables('diagnosticsStorageAccountName'), '.blob.core.windows.net/')]"
                    }
                },
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            }
        },
        {
            "name": "[variables('diagnosticsStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "location": "eastus",
            "properties": {},
            "kind": "[variables('diagnosticsStorageAccountKind')]",
            "sku": {
                "name": "[variables('diagnosticsStorageAccountType')]"
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[variables('adminUsername')]"
        }
    }
}
```

## <a name="next-steps"></a>További lépések

Direkt virtuális gép az [Azure PowerShell](../windows/spot-powershell.md) vagy az [Azure CLI](spot-cli.md)használatával is létrehozhat.

Ha hibát észlel, olvassa el a [Hibakódok című témakört.](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)