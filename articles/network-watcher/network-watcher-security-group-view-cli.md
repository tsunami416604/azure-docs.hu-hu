---
title: Hálózati biztonság elemzése a Biztonsági csoport nézetben – Azure CLI
titleSuffix: Azure Network Watcher
description: Ez a cikk ismerteti, hogyan azure CLI segítségével elemezheti a virtuális gépek biztonságát a Security Group View használatával.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 73f1efc512bf031021791da8cc55bc4e7d98a812
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840775"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>A virtuális gép biztonságának elemzése a Biztonsági csoport nézetben az Azure CLI használatával

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

A biztonsági csoport nézet a virtuális gépekre alkalmazott konfigurált és hatékony hálózati biztonsági szabályokat adja vissza. Ez a funkció a hálózati biztonsági csoportok és a virtuális gépeken konfigurált szabályok naplózásához és diagnosztizálásához hasznos, amelyek biztosítják a forgalom megfelelő engedélyezik vagy megtagadják. Ebben a cikkben bemutatjuk, hogyan lehet lekérni a konfigurált és hatékony biztonsági szabályokat egy virtuális gépre az Azure CLI használatával

A cikkben ismertetett lépések végrehajtásához telepítenie kell [az Azure parancssori felületét Mac, Linux és Windows (CLI) rendszerhez.](/cli/azure/install-azure-cli)

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv feltételezi, hogy már követte a [Hálózatfigyelő létrehozása](network-watcher-create.md) a Hálózatfigyelő létrehozásához című lépéseit.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv ebben a cikkben lekéri a konfigurált és hatékony biztonsági szabályokat egy adott virtuális gép.

## <a name="get-a-vm"></a>Virtuális gép beszerezni

A `vm list` parancsmag futtatásához virtuális gép szükséges. A következő parancs egy erőforráscsoport ban lévő virtuális gépeket sorolja fel:

```azurecli
az vm list -resource-group resourceGroupName
```

Miután ismeri a virtuális gépet, `vm show` a parancsmag segítségével lekaphatja az erőforrás-azonosítóját:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Biztonsági csoport nézet beolvasása

A következő lépés a biztonsági csoport nézet eredményének beolvasása.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Az eredmények megtekintése

A következő példa a visszaadott eredmények rövidített válasza. Az eredmények a virtuális gépen érvényes és alkalmazott biztonsági szabályokat mutatják **a NetworkInterfaceSecurityRules**, **DefaultSecurityRules**és **EffectiveSecurityRules**csoportokra bontva.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

Látogasson el [a Network Security Groups (NSG)](network-watcher-nsg-auditing-powershell.md) és a Network Watcher elemre, ahol megtudhatja, hogyan automatizálhatja a hálózati biztonsági csoportok érvényesítését.

A hálózati erőforrásokra alkalmazott biztonsági szabályokról a [Biztonsági csoport nézet áttekintése](network-watcher-security-group-view-overview.md) című témakörben olvashat bővebben.
