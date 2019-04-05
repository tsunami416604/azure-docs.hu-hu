---
title: Hálózati biztonság Azure Network Watcher biztonsági csoport nézet - REST API-t elemzése |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan elemezheti a biztonsági csoport nézet a virtuális gépek biztonsági a PowerShell használatával.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: a2f418fe-f5d2-43ed-8dc3-df0ed2a4d4ac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: a2f55a65d88b499384fc961c3d6a479bac804ba8
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051529"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Biztonsági csoport nézet REST API-val a virtuális gép biztonsági elemzése

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Biztonsági csoport nézet konfigurált és érvényben lévő hálózati biztonsági szabályok a virtuális gépek alkalmazott adja vissza. Ez a funkció akkor hasznos, naplózás, és diagnosztizálhatja hálózati biztonsági csoportok és annak érdekében, hogy folyamatban van a forgalom egy virtuális gépen konfigurált szabályok megfelelően engedélyezi vagy megtagadja. Ebben a cikkben bemutatjuk, hogyan kérheti le a hatékony és alkalmazott biztonsági szabályok egy virtuális géphez, a REST API használatával


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ebben a forgatókönyvben a hálózati figyelő Rest API-t a biztonsági csoport nézet első virtuális gép hívható meg. ARMclient hívás a REST API, PowerShell-lel történik. ARMClient megtalálható a chocolatey [ARMClient a chocolatey-t](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv azt feltételezi, hogy már követte a lépéseket a [hozzon létre egy Network Watcher](network-watcher-create.md) egy Network Watcher létrehozásához. A forgatókönyv azt is feltételezi, hogy létezik-e egy érvényes virtuális gépet egy erőforráscsoportot használni.

## <a name="scenario"></a>Forgatókönyv

Az ebben a cikkben ismertetett forgatókönyvben a hatékony és alkalmazott biztonsági szabályok egy adott virtuális gép kérdezi le.

## <a name="log-in-with-armclient"></a>Jelentkezzen be ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Virtuális gép beolvasása

Futtassa a következő szkriptet egy virtuális machineThe vissza a következő kód változók van szüksége:

- **subscriptionId** – az előfizetés-azonosítót is lekérhető az a **Get-AzSubscription** parancsmagot.
- **resourceGroupName** -egy virtuális gépeket tartalmazó erőforráscsoport nevét.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

A szükséges adatokat a rendszer a **azonosító** típus alatt `Microsoft.Compute/virtualMachines` válaszként, az alábbi példában látható módon:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Virtuális gép biztonsági csoport nézet beolvasása

Az alábbi példában a biztonsági csoport nézet célként kijelölt virtuális gépek kérelmeket. Ez a példa az eredményeket hasonlítsa össze a szabályok és a konfigurációs csúszásokat a keresett feladójának által meghatározott biztonsági használható.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Tekintse meg a választ

Az alábbi minta az előző parancs által visszaadott válaszra. Az eredmények megjelenítése hatékony és alkalmazott biztonsági szabályok a virtuális gépen a csoportok szerinti bontásban **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, és  **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

Látogasson el [naplózás hálózati biztonsági csoportok (NSG) és a Network Watcher](network-watcher-security-group-view-powershell.md) megtudhatja, hogyan automatizálhatja a hálózati biztonsági csoportok érvényesítése.


