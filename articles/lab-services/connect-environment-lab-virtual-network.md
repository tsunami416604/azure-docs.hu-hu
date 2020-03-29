---
title: Környezetek csatlakoztatása egy tesztkörnyezet virtuális hálózatához az Azure DevTest Labsben | Microsoft dokumentumok
description: Megtudhatja, hogyan csatlakoztathat egy környezetet (például a Service Fabric-fürtöt) a labor virtuális hálózatához az Azure DevTest Labs ben
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: bb8b5f7d6578390fd0f48c3de154cfdb034ac6c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60777168"
---
# <a name="connect-an-environment-to-your-labs-virtual-network-in-azure-devtest-labs"></a>Környezet csatlakoztatása a labor virtuális hálózatához az Azure DevTest Labs ben
Az Azure DevTest Labs segítségével egyszerűen hozhat létre virtuális gépeket egy [laborban a beépített hálózatkezeléssel.](devtest-lab-configure-vnet.md) Ez egy nagy rugalmasságot azzal a képességgel, hogy [hozzon létre több VM környezetben](devtest-lab-test-env.md). Ez a cikk bemutatja, hogyan csatlakoztathat virtuális gépeket egy környezetben a tesztkörnyezet virtuális hálózatához. Az egyik forgatókönyv, ahol ezt a funkciót egy N-szintű alkalmazás beállítása egy SQL Server adatréteg, amely csatlakozik a labor virtuális hálózat, amely lehetővé teszi a teszt virtuális gépek a laborban való hozzáférés.  

## <a name="sample-environment-that-uses-lab-vnet"></a>Labor virtuális hálózatot használó mintakörnyezet
Az alábbiakban egy egyszerű környezeti sablont, amely összeköti a labor alhálózatát. Ebben a példában a `DTLSubnetId` paraméter annak az alhálózatnak az azonosítóját jelöli, amelyben a tesztkörnyezet létezik. A: hozzá van `$(LabSubnetId)`rendelve, amelyet a DevTest Labs automatikusan felold a labor alhálózatának azonosítójára. Ebben a definícióban a virtuális **gép hálózati adapterének** `DTLSubnetId` **alhálózati** tulajdonsága úgy van beállítva, hogy ugyanazt az alhálózatot csatlakozzon. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DTLEnvironVmStoretype": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
                "Standard_LRS",
                "Standard_ZRS",
                "Standard_GRS",
                "Standard_RAGRS",
                "Premium_LRS"
            ]
        },
        "DTLEnvironVmName": {
            "type": "string",
            "minLength": 1
        },
        "VmAdminUserName": {
            "type": "string",
            "minLength": 1
        },
        "VmAdminUserPassword": {
            "type": "securestring"
        },
        "DTLEnvironVmOsVersion": {
            "type": "string",
            "defaultValue": "2012-R2-Datacenter",
            "allowedValues": [
                "2008-R2-SP1",
                "2012-Datacenter",
                "2012-R2-Datacenter",
                "Windows-Server-Technical-Preview"
            ]
        },
        "DTLSubnetId": {
            "type": "string",
            "defaultValue": "$(LabSubnetId)"
        }
    },
    "variables": {
        "DTLEnvironStoreName": "[toLower([concat(parameters('DTLEnvironVmName'), 'storename')])]",
        "DTLEnvironVmImagePublisher": "MicrosoftWindowsServer",
        "DTLEnvironVmImageOffer": "WindowsServer",
        "DTLEnvironVmOSDiskName": "[concat(parameters('DTLEnvironVmName'), 'OSDisk')]",
        "DTLEnvironVmSize": "Standard_D2_v2",
        "DTLEnvironVmStorageAccountContainerName": "vhds",
        "DTLEnvironVmNicName": "[concat(parameters('DTLEnvironVmName'), 'NetworkInterface')]"
    },
    "resources": [{
            "name": "[variables('DTLEnvironStoreName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[resourceGroup().location]",
            "apiVersion": "2016-01-01",
            "sku": {
                "name": "[parameters('DTLEnvironVmStoretype')]"
            },
            "dependsOn": [],
            "tags": {
                "displayName": "[variables('DTLEnvironStoreName')]"
            },
            "kind": "Storage"
        },
        {
            "name": "[variables('DTLEnvironVmNicName')]",
            "type": "Microsoft.Network/networkInterfaces",
            "location": "southeastasia",
            "apiVersion": "2016-03-30",
            "dependsOn": [],
            "tags": {
                "displayName": "[variables('DTLEnvironVmNicName')]"
            },
            "properties": {
                "ipConfigurations": [{
                    "name": "ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "[parameters('DTLSubnetId')]"
                        }
                    }
                }]
            }
        },
        {
            "name": "[parameters('DTLEnvironVmName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "location": "[resourceGroup().location]",
            "apiVersion": "2015-06-15",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', variables('DTLEnvironStoreName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces', variables('DTLEnvironVmNicName'))]"
            ],
            "tags": {
                "displayName": "[parameters('DTLEnvironVmName')]"
            },
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('DTLEnvironVmSize')]"
                },
                "osProfile": {
                    "computerName": "[parameters('DTLEnvironVmName')]",
                    "adminUsername": "[parameters('VmAdminUserName')]",
                    "adminPassword": "[parameters('VmAdminUserPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "[variables('DTLEnvironVmImagePublisher')]",
                        "offer": "[variables('DTLEnvironVmImageOffer')]",
                        "sku": "[parameters('DTLEnvironVmOsVersion')]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "name": "[variables('DTLEnvironVmOSDiskName')]",
                        "vhd": {
                            "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('DTLEnvironStoreName')), '2016-01-01').primaryEndpoints.blob, variables('DTLEnvironVmStorageAccountContainerName'), '/', variables('DTLEnvironVmOSDiskName'), '.vhd')]"
                        },
                        "caching": "ReadWrite",
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [{
                        "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('DTLEnvironVmNicName'))]"
                    }]
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikket az Azure Portal használatával a következő műveletek: [Indítsa újra a virtuális gép.](devtest-lab-restart-vm.md)