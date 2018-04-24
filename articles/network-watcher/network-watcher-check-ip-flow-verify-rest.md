---
title: Ellenőrizze a forgalom Azure hálózati figyelő IP folyamata ellenőrizze - REST-|} Microsoft Docs
description: Ez a cikk ismerteti, hogyan ellenőrizhető, ha a bejövő és kimenő forgalmat a virtuális gépek engedélyezett vagy megtagadott
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c3d38f776db63e777174b7dca8b09a0d19c387e8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Ellenőrizze, hogy a forgalom engedélyezett vagy megtagadott IP-folyamat az Azure hálózati figyelőt összetevője ellenőrzése

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Az Azure REST API-n](network-watcher-check-ip-flow-verify-rest.md)


IP-adatfolyam ellenőrizze, hogy egy funkciója, amely lehetővé teszi, hogy ellenőrizze, hogy ha a forgalom engedélyezve van-e, vagy a virtuális gép hálózati figyelőt. Az érvényesítési futtathatja a bejövő vagy kimenő forgalmat. Ebben a forgatókönyvben akkor hasznos, ha szeretné, hogy a virtuális gép kommunikálhat külső erőforrás- vagy háttéradatbázis aktuális állapotának. IP-adatfolyam győződjön meg arról is használható, ha a hálózati biztonsági csoport (NSG) szabályok konfigurációja megfelelő-e, és hibáinak elhárítása az NSG-szabályok által blokkolt adatfolyamok ellenőrzése. Egy másik oka IP folyamat, ellenőrizze annak biztosítása, amelyet a letiltott forgalmat blokkol megfelelően az NSG.

## <a name="before-you-begin"></a>Előkészületek

A PowerShell használatával REST API hívása ARMclient szolgál. ARMClient verziója van telepítve, chocolatey [a Chocolatey ARMClient](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt.

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben IP folyamat, ellenőrizze használatával győződjön meg arról, ha egy virtuális gép működik egy másik 443-as porton keresztül. Ha a forgalmat a rendszer megtagadja, a biztonsági szabály, amely megtagadja a forgalom adja vissza. IP-adatfolyam ellenőrizze kapcsolatos további információkért látogasson el a [IP folyamat ellenőrzése – áttekintés](network-watcher-ip-flow-verify-overview.md)

Ebben a forgatókönyvben azt:

* A virtuális gép beolvasása
* Hívás IP folyamat ellenőrzése
* Eredmények ellenőrzése

## <a name="log-in-with-armclient"></a>Jelentkezzen be ARMClient

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>A virtuális gép beolvasása

Futtassa a következő parancsfájl egy virtuális gép visszaállítására. A következő kódot a változók értékeit kell:

* **a subscriptionId** -az előfizetési azonosító használatával.
* **resourceGroupName** -virtuális gépeket tartalmazó erőforráscsoport nevét.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

A szükséges adatokat a rendszer a azonosítóját típus szerinti `Microsoft.Compute/virtualMachines`. Az eredményeket a következő példakód hasonló lesz:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>Hívás IP folyamat ellenőrzése

Az alábbi példakód létrehozza a forgalmat egy adott virtuális gép kérelmet. A válasz adja vissza, ha a forgalom engedélyezve van, vagy ha a forgalmat a rendszer megtagadja. Ha a forgalom megtagadva is adja vissza, milyen szabály blokkolja a forgalmat.

> [!NOTE]
> IP-adatfolyam ellenőrizze, hogy megköveteli, hogy a virtuális gép erőforrásához van lefoglalva.

A parancsfájl az erőforrás-azonosítót a virtuális gépek és a hálózati kártyát a virtuális gépen van szükség. Ezeket az értékeket az előző kimeneti által biztosított.

> [!Important]
> Az összes hálózati figyelő REST-hívást az erőforráscsoport neve a kérés URI, a hálózati figyelőt példányt tartalmazó, nem az erőforrásokat a diagnosztikai műveleteket hajt végre.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>Az eredmények ismertetése

A válasz vissza azt ismerteti, hogy a forgalom engedélyezett vagy megtagadott. A válasz a következőképpen néz egyet az alábbi példákat:

**Engedélyezett**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**Megtagadva**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>További lépések

Ha a forgalmat blokkol, és nem kell, lásd: [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md) további információt a hálózati biztonsági csoportok.












