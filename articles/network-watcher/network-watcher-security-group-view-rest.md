---
title: Hálózati biztonság elemzése – Biztonsági csoportnézet – Azure REST API
titleSuffix: Azure Network Watcher
description: Ez a cikk ismerteti, hogyan használhatja a PowerShell t a virtuális gépek biztonságának elemzésére a Security Group View használatával.
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
ms.openlocfilehash: c9c76e9c06d4c45a096cff79dac82bb80ebe25d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840740"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>A virtuális gép biztonságának elemzése a Biztonsági csoport nézetsegítségével a REST API használatával

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

A biztonsági csoport nézet a virtuális gépekre alkalmazott konfigurált és hatékony hálózati biztonsági szabályokat adja vissza. Ez a funkció a hálózati biztonsági csoportok és a virtuális gépeken konfigurált szabályok naplózásához és diagnosztizálásához hasznos, amelyek biztosítják a forgalom megfelelő engedélyezik vagy megtagadják. Ebben a cikkben bemutatjuk, hogyan lehet lekérni a hatékony és alkalmazott biztonsági szabályokat egy virtuális gépre a REST API használatával


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Előkészületek

Ebben a forgatókönyvben hívja meg a Network Watcher Rest API-t, hogy egy virtuális gép biztonsági csoport nézetét. Az ARMclient a REST API-t a PowerShell használatával hívja meg. ARMClient található csokoládés [armclient a Chocolatey](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv feltételezi, hogy már követte a [Hálózatfigyelő létrehozása](network-watcher-create.md) a Hálózatfigyelő létrehozásához című lépéseit. A forgatókönyv azt is feltételezi, hogy egy érvényes virtuális géppel rendelkező erőforráscsoport használható.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv ebben a cikkben lekéri a hatékony és alkalmazott biztonsági szabályok egy adott virtuális gép.

## <a name="log-in-with-armclient"></a>Bejelentkezés armclient nal

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Virtuális gép beolvasása

Futtassa a következő parancsfájlt egy virtuális gép visszaadásáraA következő kódhoz változókra van szükség:

- **subscriptionId** – Az előfizetés-azonosító a **Get-AzSubscription** parancsmaggal is lehívható.
- **resourceGroupName** - A virtuális gépeket tartalmazó erőforráscsoport neve.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

A szükséges információ a válaszként megadott `Microsoft.Compute/virtualMachines` típus szerinti **azonosító,** ahogy az a következő példában látható:

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

## <a name="get-security-group-view-for-virtual-machine"></a>Biztonsági csoport nézet beszereznie a virtuális géphez

A következő példa egy megcélzott virtuális gép biztonságicsoport-nézetét kéri. Az eredmények ebben a példában lehet összehasonlítani a szabályok és a biztonság által meghatározott kezdeményezése a konfigurációs eltolódás.

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

## <a name="view-the-response"></a>A válasz megtekintése

A következő minta az előző parancsból visszaadott válasz. Az eredmények a virtuális gépen érvényes és alkalmazott biztonsági szabályokat mutatják **a NetworkInterfaceSecurityRules**, **DefaultSecurityRules**és **EffectiveSecurityRules**csoportokra bontva.

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

Látogasson el [a Network Security Groups (NSG)](network-watcher-security-group-view-powershell.md) és a Network Watcher elemre, ahol megtudhatja, hogyan automatizálhatja a hálózati biztonsági csoportok érvényesítését.


