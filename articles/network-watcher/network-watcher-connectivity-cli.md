---
title: Kapcsolatok – Azure CLI –
titleSuffix: Azure Network Watcher
description: Ismerje meg, hogyan használhatja az Azure Network Watcher kapcsolathibaelhárítási képességét az Azure CLI használatával.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 842e58de8dbc06d3f045b0e9d0dc6b99e6b1e2fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842886"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-cli"></a>Az Azure Network Watcherrel létesített kapcsolatok – az Azure CLI használatával – problémamegoldás

> [!div class="op_single_selector"]
> - [Powershell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Ismerje meg, hogyan használható a kapcsolathiba elhárítása annak ellenőrzésére, hogy létre lehet-e hozni egy közvetlen TCP-kapcsolatot egy virtuális gépről egy adott végpontra.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy a következő erőforrásokkal rendelkezik:

* A Hálózatfigyelő egy példánya abban a régióban, ahol el szeretné hárítani a kapcsolatot.
* Virtuális gépek a kapcsolatok elhárításához.

> [!IMPORTANT]
> A kapcsolathiba elhárításához szükség van `AzureNetworkWatcherExtension` arra, hogy a virtuális gép, amelyről hibaelhárítási, telepítve van a virtuális gép bővítménye. A bővítmény Windows virtuális gépen történő telepítéséhez látogasson el az [Azure Network Watcher Agent windowsos](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) és Linuxos virtuális gépbővítménybe, és látogasson el az [Azure Network Watcher Agent linuxos virtuálisgép-bővítménybe.](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) A bővítmény nem szükséges a célvégponton.

## <a name="check-connectivity-to-a-virtual-machine"></a>Virtuális gép csatlakoztatásának ellenőrzése

Ez a példa a 80-as porton keresztül ellenőrzi a célvirtuális géphez való kapcsolódást.

### <a name="example"></a>Példa

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-resource Database0 --dest-port 80
```

### <a name="response"></a>Válasz

A következő válasz az előző példából származik.  Ebben a válaszban az `ConnectionStatus` **elérhetetlen**. Láthatja, hogy az összes elküldött szonda meghibásodott. A kapcsolat nem sikerült a virtuális készüléken, mert a felhasználó által konfigurált `NetworkSecurityRule` **UserRule_Port80**nevű , úgy van beállítva, hogy blokkolja a bejövő forgalmat a 80-as porton. Ez az információ használható a kapcsolódási problémák kutatására.

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "bb01d336-d881-4808-9fbc-72f091974d68",
      "issues": [],
      "nextHopIds": [
        "f8b074e9-9980-496b-a35e-619f9bcbf648"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "10.1.2.4",
      "id": "f8b074e9-9980-496b-a35e-619f9bcbf648",
      "issues": [],
      "nextHopIds": [
        "8a5857f3-6ab8-4b11-b9bf-a046d66b8696"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/fw
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.3.4",
      "id": "8a5857f3-6ab8-4b11-b9bf-a046d66b8696",
      "issues": [
        {
          "context": [
            {
              "key": "RuleName",
              "value": "UserRule_Port80"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "NetworkSecurityRule"
        }
      ],
      "nextHopIds": [
        "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/au
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.4.4",
      "id": "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/db
Nic0/ipConfigurations/ipconfig1",
      "type": "VnetLocal"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="validate-routing-issues"></a>Útválasztási problémák ellenőrzése

Ez a példa ellenőrzi a virtuális gép és egy távoli végpont közötti kapcsolatot.

### <a name="example"></a>Példa

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address 13.107.21.200 --dest-port 80
```

### <a name="response"></a>Válasz

A következő példában `connectionStatus` az **elérhetetlenként**jelenik meg. A `hops` részletekben látható, `issues` hogy a forgalom blokkolva `UserDefinedRoute`volt a .

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "f2cb1868-2049-4839-b8ed-57a480d06f95",
      "issues": [
        {
          "context": [
            {
              "key": "RouteType",
              "value": "User"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "UserDefinedRoute"
        }
      ],
      "nextHopIds": [
        "da4022db-0ab0-48c4-a507-dd4c03561ca5"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "13.107.21.200",
      "id": "da4022db-0ab0-48c4-a507-dd4c03561ca5",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Unknown",
      "type": "Destination"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="check-website-latency"></a>Webhely késése

A következő példa ellenőrzi a kapcsolatot egy webhelyhez.

### <a name="example"></a>Példa

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://bing.com --dest-port 80
```

### <a name="response"></a>Válasz

A következő válaszban a `connectionStatus` műsorok **at elérhetőként láthatja.** Ha a kapcsolat sikeres, késési értékeket ad meg.

```json
{
  "avgLatencyInMs": 2,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "639c2d19-e163-4dfd-8737-5018dd1168ae",
      "issues": [],
      "nextHopIds": [
        "fd43a6e7-c758-4f48-90aa-8db99105a4a3"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "204.79.197.200",
      "id": "fd43a6e7-c758-4f48-90aa-8db99105a4a3",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>Tárolóvégpont-kapcsolat ellenőrzése

A következő példa ellenőrzi a virtuális gép és a blogtárfiók közötti kapcsolatot.

### <a name="example"></a>Példa

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://contosoexamplesa.blob.core.windows.net/
```

### <a name="response"></a>Válasz

A következő json az előző parancsmag futtatásából származó példaválasz. A csekk sikerességének `connectionStatus` sikerességéhez a tulajdonság **elérhetőként jelenik meg.**  A storage blob és a késés eléréséhez szükséges ugrások számát illetően.

```json
{
  "avgLatencyInMs": 1,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "5136acff-bf26-4c93-9966-4edb7dd40353",
      "issues": [],
      "nextHopIds": [
        "f8d958b7-3636-4d63-9441-602c1eb2fd56"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "1.2.3.4",
      "id": "f8d958b7-3636-4d63-9441-602c1eb2fd56",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan automatizálhatja a csomagrögzítéseket a virtuálisgép-riasztások segítségével a [Riasztás aktivált csomagrögzítésének létrehozásával](network-watcher-alert-triggered-packet-capture.md)

Annak megállapítása, hogy bizonyos forgalom engedélyezett-e a virtuális gépbe vagy a virtuális gépből, az [IP-folyamat ellenőrzése című ellátogat.Find](diagnose-vm-network-traffic-filtering-problem.md) if certain traffic is allowed in or out in your VM by check IP flow verify
