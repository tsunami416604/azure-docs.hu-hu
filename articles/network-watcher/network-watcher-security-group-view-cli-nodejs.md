---
title: "Hálózati biztonság Azure hálózati figyelő biztonsági csoport láthassák - Azure CLI 1.0 elemzése |} Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan Azure CLI 1.0 használatával elemezheti a virtuális gépek biztonsági a biztonsági csoport megtekintése."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f27157129bea4e47a2e0e6cc1169b9e4887bdd78
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli-10"></a>A virtuális gép biztonsági a biztonsági csoport nézet segítségével az Azure CLI 1.0 elemzése

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Biztonsági csoport megtekintése konfigurált és hatékony hálózati biztonsági szabályok virtuális gép által használt adja vissza. Ez a funkció akkor hasznos, naplózási és diagnosztizálhatja a hálózati biztonsági csoportok és annak érdekében, hogy folyamatban van a forgalom egy virtuális gépen konfigurált szabályok megfelelően engedélyez vagy tilt. Ebben a cikkben megmutatjuk, hogyan lehet lekérni a konfigurált és hatékony biztonsági szabályokat egy virtuális gép az Azure parancssori felület használatával

Ebben a cikkben az Azure CLI 1.0 platformfüggetlen, elérhető a Windows, Mac és Linux. Hálózati figyelőt jelenleg használ Azure CLI 1.0 CLI támogatására.

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv a cikkben szereplő lekérdezi a konfigurált és hatékony biztonsági szabályok egy adott virtuális géphez.

## <a name="get-a-vm"></a>A virtuális gép beolvasása

A virtuális gépek futtatásához szükséges a `vm list` parancsmag. A következő parancsot egy erőforráscsoportban található virtuális machinese sorolja fel:

```azurecli
azure vm list -g resourceGroupName
```

Ha tudja, hogy a virtuális gépet, a `vm show` parancsmagot, hogy megkapja az erőforrás-azonosítót:

```azurecli
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Biztonsági csoport nézet beolvasása

A program a következő lépés a biztonsági csoport megtekintése eredménye. Hozzáadása a "--json" jelző formázza az eredmények json-ban.

```azurecli
azure network watcher security-group-view -g resourceGroupName -n networkWatcherName -t targetResourceId --json
```

## <a name="viewing-the-results"></a>Az eredmények megtekintése

A következő példa egy rövidített választ adott vissza eredményt. Az eredmények megjelenítése a hatékony és alkalmazott biztonsági szabályokat a virtuális gépen csoportok bontásban **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, és  **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic",
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testvm",
          "securityRules": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/test-nsg/securityRules/default-allow-rdp",
              "protocol": "TCP",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 1000,
              "direction": "Inbound",
              "provisioningState": "Succeeded",
              "name": "default-allow-rdp",
              "etag": "W/\"00000000-0000-0000-0000-000000000000\""
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/networkSecurityGroups//defaultSecurityRules/",
            "description": "Allow inbound traffic from all VMs in VNET",
            "protocol": "*",
            "sourcePortRange": "*",
            "destinationPortRange": "*",
            "sourceAddressPrefix": "VirtualNetwork",
            "destinationAddressPrefix": "VirtualNetwork",
            "access": "Allow",
            "priority": 65000,
            "direction": "Inbound",
            "provisioningState": "Succeeded",
            "name": "AllowVnetInBound"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

Látogasson el [naplózás hálózati biztonsági csoportok (NSG) rendelkező hálózati figyelőt](network-watcher-nsg-auditing-powershell.md) megtudhatja, hogyan automatizálhatja a hálózati biztonsági csoportok érvényesítése.

További információ a biztonsági szabályok látogasson el a hálózati erőforrások alkalmazott [biztonsági csoport megtekintése – áttekintés](network-watcher-security-group-view-overview.md)
