---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: b7bbf1fb8ffc1f57a53682d3baf288077b144572
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664583"
---
### <a name="virtual-networks"></a>Virtuális hálózatok

|  |  |
|---------|---------|
| [Engedélyezett Application Gateway termékváltozatok](../articles/azure-policy/scripts/allowed-app-gate-sku.md) | Ehhez az alkalmazásátjáróknak jóváhagyott termékváltozatot kell használniuk. Meg kell adnia egy jóváhagyott termékváltozatokat tartalmazó tömböt. |
| [Hálózati társviszony tiltása egy erőforráscsoportban lévő hálózatban](../articles/azure-policy/scripts/no-peering-er-net.md) | Megtiltja hálózati társviszony társítását egy megadott erőforráscsoportban lévő hálózattal. Segít meggátolni a központi felügyelt hálózati infrastruktúrához való kapcsolódást. Meg kell adnia annak az erőforráscsoportnak a nevét, amelyben meg kívánja gátolni a társítást. |
| [Felhasználó által megadott útválasztási táblázatok tiltása](../articles/azure-policy/scripts/no-user-def-route-table.md)  |Megtiltja a virtuális hálózatok felhasználó által megadott útválasztási táblázattal való üzembe helyezését. |
| [NSG X minden alhálózaton](../articles/azure-policy/scripts/nsg-on-subnet.md) | Előírja, hogy minden virtuális alhálózathoz egy adott hálózati biztonsági csoportot kell használni. A használandó hálózati biztonsági csoport azonosítóját Ön határozza meg. |
| [Jóváhagyott alhálózat használata a virtuális gépek hálózati adaptereihez](../articles/azure-policy/scripts/use-approved-subnet-vm-nics.md) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott alhálózatot kell használniuk. A jóváhagyott alhálózat azonosítóját Ön határozza meg. |
| [Jóváhagyott virtuális hálózat használata a virtuális gépek hálózati adaptereihez](../articles/azure-policy/scripts/use-approved-vnet-vm-nics.md) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott virtuális hálózatot kell használniuk. A jóváhagyott virtuális hálózat azonosítóját Ön határozza meg. |