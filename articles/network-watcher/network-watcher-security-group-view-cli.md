---
title: Hálózati biztonság Azure Network Watcher biztonsági csoport nézet - Azure CLI-vel elemzése |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan elemezheti a biztonsági csoport nézet a virtuális gépek biztonsági az Azure CLI használatával.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 2b96c82f92ea49fa5bcb35d180240eb939d29d04
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962895"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Azure CLI-vel a biztonsági csoport nézet a virtuális gép biztonsági elemzése

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Biztonsági csoport nézet konfigurált és érvényben lévő hálózati biztonsági szabályok a virtuális gépek alkalmazott adja vissza. Ez a funkció akkor hasznos, naplózás, és diagnosztizálhatja hálózati biztonsági csoportok és annak érdekében, hogy folyamatban van a forgalom egy virtuális gépen konfigurált szabályok megfelelően engedélyezi vagy megtagadja. Ebben a cikkben bemutatjuk, hogyan kérheti le a konfigurált és érvényben lévő biztonsági szabályokat a virtuális gépek Azure CLI használatával

Ebben a cikkben szereplő lépések végrehajtásához kell [telepítse az Azure parancssori felület Mac, Linux és Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv azt feltételezi, hogy már követte a lépéseket a [hozzon létre egy Network Watcher](network-watcher-create.md) egy Network Watcher létrehozásához.

## <a name="scenario"></a>Forgatókönyv

Az ebben a cikkben ismertetett forgatókönyvben egy adott virtuális géphez konfigurált és érvényben lévő biztonsági szabályok kérdezi le.

## <a name="get-a-vm"></a>A virtuális gép lekérése

A virtuális gépek futtatásához szükséges a `vm list` parancsmagot. A következő parancs felsorolja a virtuális gépek egy erőforráscsoportban:

```azurecli
az vm list -resource-group resourceGroupName
```

Ha már tudja, hogy a virtuális gép, használhatja a `vm show` -parancsmaggal beolvasható az erőforrás-azonosító:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Biztonsági csoport nézet beolvasása

A következő lépés, hogy a biztonsági csoport nézet eredmény beolvasása.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Az eredmények megtekintése

Az alábbi példa a visszaadott eredmények akkor használhatja rövidített választ. Az eredmények megjelenítése hatékony és alkalmazott biztonsági szabályok a virtuális gépen a csoportok szerinti bontásban **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, és  **EffectiveSecurityRules**.

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

Látogasson el [naplózás hálózati biztonsági csoportok (NSG) és a Network Watcher](network-watcher-nsg-auditing-powershell.md) megtudhatja, hogyan automatizálhatja a hálózati biztonsági csoportok érvényesítése.

További információ a hálózati erőforrásokhoz való funkcionáló alkalmazott biztonsági szabályok [biztonsági csoport nézet áttekintése](network-watcher-security-group-view-overview.md)
